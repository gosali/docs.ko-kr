---
title: "비동기에 대한 자세한 설명"
description: ".NET에서 비동기 코드가 작동하는 방식에 대한 자세한 설명"
keywords: ".NET, .NET Core, .NET 표준"
author: cartermp
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 1e38f9d9-8f84-46ee-a15f-199aec4f2e34
translationtype: Human Translation
ms.sourcegitcommit: de0dab146fc811e895dc32f98f877db5e757f82b
ms.openlocfilehash: 6536a608a4ee1bb10f41907a28114193a300a52c

---

# <a name="async-in-depth"></a>비동기에 대한 자세한 설명

.NET 태스크 기반 비동기 모델을 사용하면 I/O 및 CPU 바인딩된 비동기 코드를 간단하게 작성할 수 있습니다. 모델은 `Task` 및 `Task<T>` 형식과 `async` 및 `await` 언어 키워드로 표시됩니다. 이 문서에서는 .NET 비동기를 사용하는 방법을 설명하고 백그라운드에서 사용되는 비동기 프레임워크에 대한 통찰을 제공합니다.

## <a name="task-and-tasklttgt"></a>Task 및 Task&lt;T&gt;

태스크는 [동시성 약속 모델](https://en.wikipedia.org/wiki/Futures_and_promises)을 구현하는 데 사용되는 구문입니다.  간단히 말해서, 나중에 작업이 완료될 것이라는 "약속"을 제공하여 클린 API로 약속을 조정할 수 있게 합니다.

*   `Task` - 값을 반환하지 않는 작업 하나를 나타냅니다.
*   `Task<T>` - `T` 형식의 값을 반환하는 작업 하나를 나타냅니다.

스레딩에 대한 추상화가 *아니라* 비동기적으로 수행되는 작업의 추상화로 태스크에 대해 추론하는 것이 중요합니다. 기본적으로 태스크는 현재 스레드에 대해 실행되며 해당하는 경우 운영 체제에 작업을 위임합니다. 필요에 따라 `Task.Run` API를 통해 별도 스레드에서 실행되도록 태스크를 명시적으로 요청할 수 있습니다.

태스크는 태스크의 결과 값(`Task<T>`의 경우)을 모니터링, 대기 및 액세스하기 위한 API 프로토콜을 표시합니다. `await` 키워드가 있는 언어 통합에서는 태스크 사용을 위한 상위 수준 추상화를 제공합니다. 

`await`를 사용하면 태스크가 완료될 때까지 해당 호출자에게 제어가 양도되므로 태스크가 실행되는 동안 응용 프로그램 또는 서비스에서 유용한 작업을 수행할 수 있습니다. 태스크가 완료된 후에는 코드에서 콜백 또는 이벤트를 사용하여 실행을 계속할 필요가 없습니다. 언어 및 태스크 API 통합에서 해당 작업을 자동으로 수행합니다. `Task<T>`를 사용하는 경우 `await` 키워드는 태스크가 완료될 때 반환되는 값을 추가로 "래핑 해제"합니다.  작동 방식에 대한 자세한 내용은 아래에서 자세히 설명합니다.

[TAP(태스크 기반 비동기 패턴) 문서](https://msdn.microsoft.com/library/hh873175.aspx)에서 태스크 및 태스크를 조작하는 다양한 방법에 대해 자세히 알아볼 수 있습니다.

## <a name="deeper-dive-into-tasks-for-an-iobound-operation"></a>I/O 바인딩된 작업에 대한 태스크 심층 분석

다음 섹션에서는 일반적인 비동기 I/O 호출에서 발생하는 결과에 대해 대략적으로 설명합니다. 몇 가지 예부터 살펴보겠습니다.

첫 번째 예제에서는 비동기 메서드를 호출하고 아직 완료되지 않았을 가능성이 큰 활성 태스크를 반환합니다.

```csharp
public Task<string> GetHtmlAsync()
{
    // Execution is synchronous here
    var client = new HttpClient();
    
    return client.GetStringAsync("http://www.dotnetfoundation.org");
}
```

두 번째 예제에서는 태스크에 적용할 `async` 및 `await` 키워드 사용을 추가합니다.

```csharp
public async Task<string> GetFirstCharactersCountAsync(string url, int count)
{
    // Execution is synchronous here
    var client = new HttpClient();
    
    // Execution of GetFirstCharactersCountAsync() is yielded to the caller here
    // GetStringAsync returns a Task<string>, which is *awaited*
    var page = await client.GetStringAsync("http://www.dotnetfoundation.org");
    
    // Execution resumes when the client.GetStringAsync task completes,
    // becoming synchronous again.
    
    if (count > page.Length)
    {
        return page;
    }
    else
    {
        return page.Substring(0, count);
    }
}
```

`GetStringAsync()` 호출은 네이티브 네트워킹 라이브러리에 대한 P/Invoke interop 호출에 도달할 때까지 하위 수준 .NET 라이브러리를 호출(다른 비동기 메서드 호출)합니다. 이후에 네이티브 라이브러리는 시스템 API 호출(예: Linux의 소켓에 대한 `write()`)을 호출할 수 있습니다. [TaskCompletionSource](xref:System.Threading.Tasks.TaskCompletionSource%601.SetResult(%600))를 사용하여 네이티브/관리 경계에 태스크 개체가 생성됩니다. 태스크 개체가 계층을 통해 위로 전달되며, 초기 호출자에게 반환될 때까지 작업되거나 바로 반환될 수 있습니다. 

위의 두 번째 예제에서는 `Task<T>` 개체가 `GetStringAsync`에서 반환됩니다. `await` 키워드를 사용하면 메서드가 새로 만든 태스크 개체를 반환합니다. `GetFirstCharactersCountAsync` 메서드의 이 위치에서 호출자에게 제어가 반환됩니다. [Task&lt;T&gt;](xref:System.Threading.Tasks.Task%601) 개체의 메서드 및 속성을 사용하면 GetFirstCharactersCountAsync의 나머지 코드가 실행될 때 완료되는 태스크의 진행률을 호출자가 모니터링할 수 있습니다.

시스템 API 호출 후에 요청은 이제 커널 공간에 있으며, OS의 네트워킹 하위 시스템(예: Linux 커널의 `/net`)로 진행합니다.  여기서 OS는 네트워킹 요청을 *비동기적으로* 처리합니다.  세부 정보는 사용하는 OS에 따라 다를 수 있지만(장치 드라이버 호출을 런타임으로 다시 전송되는 신호로 예약하거나 장치 드라이버를 호출한 *다음* 신호가 다시 전송될 수 있음) 결국 런타임에서 네트워킹 요청이 진행 중이라는 알림을 받습니다.  이번에는 장치 드라이버에 대한 작업이 예약되거나, 진행 중이거나, 이미 완료(요청이 이미 "네트워크"를 통해 전송됨)되었지만 이 모든 작업이 비동기적으로 수행되므로 장치 드라이버에서 다른 작업을 즉시 처리할 수 있습니다.

예를 들어 Windows에서 OS 스레드는 네트워크 장치 드라이버를 호출하고 작업을 나타내는 IRP(인터럽트 요청 패킷)를 통해 네트워킹 작업을 수행하도록 요청합니다.  장치 드라이버는 IRP를 수신하고 네트워크를 호출한 다음 IRP를 "보류 중"으로 표시하고 OS에 다시 반환합니다.  이제 OS 스레드에서 IRP가 "보류 중"임을 알고 있으므로 이 작업에 대해 수행할 작업이 없으며 다른 작업을 수행하는 데 사용될 수 있도록 다시 "반환"됩니다.

요청이 수행되고 장치 드라이버를 통해 데이터가 반환되면 인터럽트를 통해 수신된 새 데이터를 CPU에 알려줍니다.  이 인터럽트의 처리 방법은 OS에 따라 다르지만 결국 데이터가 시스템 interop 호출에 도달할 때까지 OS를 통해 전달됩니다. 예를 들어 Linux에서는 인터럽트 처리기가 OS를 통해 비동기적으로 데이터를 위로 전달하기 위해 IRQ의 아래쪽 절반을 예약합니다.  이 작업도 *역시* 비동기적으로 수행됩니다.  다음 사용 가능한 스레드가 비동기 메서드를 실행하고 완료된 태스크의 결과를 "래핑 해제"할 수 있을 때까지 결과가 큐에 유지됩니다.

이 전체 프로세스의 요점은 **태스크 실행 전용 스레드가 없다**는 것입니다.  일부 컨텍스트에서 작업이 실행되기는 하지만(즉, OS에서 데이터를 장치 드라이버로 전달하고 인터럽트에 응답해야 함) 요청에서 데이터가 반환될 때까지 *대기*하는 전용 스레드는 없습니다.  이렇게 하면 시스템에서 일부 I/O 호출이 완료될 때까지 대기하는 것보다 훨씬 더 많은 작업량을 처리할 수 있습니다.

위의 프로세스를 위해 수행할 작업이 많아 보일 수도 있지만 벽시계 시간으로 측정할 때 실제 I/O 작업을 수행하는 데 걸리는 시간보다 훨씬 짧습니다. 정확하지는 않지만 이러한 호출의 잠재적인 타임라인은 다음과 같습니다.

0-1————————————————————————————————————————————————–2-3

*   `0` 지점에서 `1` 지점 사이의 소요 시간은 비동기 메서드가 호출자에 제어를 양도할 때까지 걸리는 시간입니다.
*   `1` 지점에서 `2` 지점 사이의 소요 시간은 CPU 비용 없이 I/O에 걸리는 시간입니다.
*   마지막으로, `2` 지점에서 `3` 지점 사이의 소요 시간은 제어(및 잠재적으로 값)가 비동기 메서드로 다시 전달되는 시간으로, 이때 메서드가 다시 실행됩니다.

### <a name="what-does-this-mean-for-a-server-scenario"></a>서버 시나리오에서는 어떤 의미가 있을까요?

이 모델은 일반적인 서버 시나리오 작업에도 잘 맞습니다.  완료되지 않은 태스크를 차단하는 전용 스레드가 없기 때문에 서버 스레드 풀이 훨씬 더 많은 웹 요청을 처리할 수 있습니다.

비동기 코드를 실행하는 서버와 비동기 코드를 실행하지 않는 서버가 있다고 가정해 보세요.  이 예제를 위해 각 서버에서 서비스 요청에 사용할 수 있는 스레드가 5개뿐이라고 가정해봅시다.  이러한 개수는 비현실적으로 적은 개수이며 데모 컨텍스트에서만 사용됩니다.

두 서버가 모두 6개의 동시 요청을 받는다고 가정해봅시다. 각 요청에서 I/O 작업을 수행합니다.  비동기 코드가 *없는* 서버는 5개의 스레드 중 하나가 I/O 바인딩된 작업을 완료하고 응답을 쓸 때까지 6번째 요청을 큐에 유지해야 합니다. 20번째 요청이 도달할 때쯤에는 큐가 너무 길어져서 서버 속도가 저하되기 시작할 수 있습니다.

비동기 코드가 실행되고 *있는* 서버도 여섯 번째 요청을 큐에 유지하지만 `async` 및 `await`를 사용하기 때문에 I/O 바인딩된 작업이 완료될 때가 아니라 시작될 때 해당 스레드가 각각 해제됩니다.  20번째 요청이 도달할 때쯤에는 들어오는 요청의 큐가 훨씬 더 적으며(큐에 요청이 있는 경우에도) 서버 속도가 저하되지 않습니다.

가상의 예제이지만 실제 환경에서도 매우 유사한 방식으로 작동합니다.  실제로 서버가 `async` 및 `await`를 사용할 경우 받는 각 요청에 전용 스레드를 사용하는 경우에 비해 훨씬 더 많은 요청을 처리할 것을 예상할 수 있습니다.

### <a name="what-does-this-mean-for-client-scenario"></a>클라이언트 시나리오에서는 어떤 의미가 있을까요?

클라이언트 앱에 `async` 및 `await`를 사용할 경우의 가장 큰 이점은 응답성 증가입니다.  스레드를 수동으로 생성하여 앱의 응답성을 개선할 수도 있지만 스레드 생성 작업은 `async` 및 `await`를 사용하는 것보다 비용이 많이 듭니다.  특히 모바일 게임 등의 경우 I/O와 관련된 UI 스레드에 대한 영향을 최소화하는 것이 중요합니다.

무엇보다도, I/O 바인딩된 작업은 CPU 시간이 거의 필요하지 않으므로 유용하지 않은 작업에 전체 CPU 스레드를 전용으로 지정할 경우 리소스를 잘못 사용하는 것입니다.

또한 UI 스레드에 작업을 디스패치하는 경우(예: UI 업데이트) `async` 메서드를 사용하면 간단하며 추가 작업(예: 스레드로부터 안전한 대리자 호출)이 필요하지 않습니다.

## <a name="deeper-dive-into-task-and-taskt-for-a-cpubound-operation"></a>CPU 바인딩된 작업에 대한 Task 및 Task<T> 심층 분석

CPU 바인딩된 `async` 코드는 I/O 바인딩된 `async` 코드와 약간 다릅니다.  CPU에서 작업이 수행되기 때문에 스레드를 계산 전용으로 지정할 방법이 없습니다.  `async` 및 `await`를 사용하면 깔끔하게 백그라운드 스레드를 조작하고 비동기 메서드 호출자를 응답 가능한 상태로 유지할 수 있습니다.  이 경우 공유 데이터는 보호되지 않습니다.  공유 데이터를 사용하는 경우 적절한 동기화 전략을 적용해야 합니다.

CPU 바인딩된 비동기 호출에 대한 개략적인 보기입니다.

```csharp
public async Task<int> CalculateResult(InputData data)
{
    // This queues up the work on the threadpool.
    var expensiveResultTask = Task.Run(() => DoExpensiveCalculation(data));
    
    // Note that at this point, you can do some other work concurrently,
    // as CalculateResult() is still executing!
    
    // Execution of CalculateResult is yielded here!
    var result = await expensiveResultTask;
    
    return result;
}
```

`CalculateResult()`가 호출된 스레드에서 실행됩니다.  `Task.Run`을 호출할 때 비용이 많이 드는 CPU 바인딩된 작업 `DoExpensiveCalculation()`을 스레드 풀의 큐에 넣고 `Task<int>` 핸들을 받습니다.  `DoExpensiveCalculation()`이 결국 다른 CPU 코어에 있을 가능성이 큰 다음 사용 가능한 스레드에서 동시에 실행됩니다.  `CalculateResult()`를 호출한 스레드가 여전히 실행되고 있으므로 `DoExpensiveCalculation()`이 다른 스레드에서 진행 중인 동안 동시 작업을 수행할 수 있습니다.

`await`가 발생하면 `CalculateResult()` 실행이 해당 호출자에게 양도되어 `DoExpensiveCalculation()`이 결과를 생성하는 동안 현재 스레드에서 다른 작업을 수행할 수 있습니다.  완료되면 결과가 주 스레드에서 실행하기 위해 큐에 배치됩니다.  결국 주 스레드가 `CalculateResult()` 실행으로 돌아가고, 이때 `DoExpensiveCalculation()` 결과를 갖게 됩니다.

### <a name="why-does-async-help-here"></a>이때 비동기가 왜 도움이 될까요?

`async` 및 `await`는 응답성이 필요할 때 CPU 바인딩된 작업을 관리하는 모범 사례입니다. CPU 바인딩된 작업에 비동기를 사용하는 여러 가지 패턴이 있습니다. 비동기 사용 시 작은 비용이 발생하며 타이트 루프에는 권장되지 않습니다.  이 새로운 기능과 관련된 코드 작성 방법은 사용자가 결정할 사항입니다.


<!--HONumber=Nov16_HO1-->


