---
title: "BlockingCollection 개요"
description: "BlockingCollection 개요"
keywords: .NET, .NET Core
author: mairaw
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: a1a867de-53c2-49ca-9a1a-e5770a942724
translationtype: Human Translation
ms.sourcegitcommit: e07788926a995b41571be276379ad9285747951d
ms.openlocfilehash: 991b4b8389cc967881c2c39d29c387687047489f

---

# <a name="blockingcollection-overview"></a>BlockingCollection 개요

[BlockingCollection&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.BlockingCollection-1)는 스레드로부터 안전한 컬렉션 클래스이며 제공하는 기능은 다음과 같습니다.

*   공급자-소비자 패턴 구현

*   스레드로부터 안전한 컬렉션 항목 추가 및 제거

*   선택적 최대 용량

*   컬렉션이 비어 있거나 가득 찬 경우 차단할 작업 삽입 및 제거

*   지정된 시간까지 차단하지 않거나 차단할 “시도” 작업 삽입 및 제거

*   [IProducerConsumerCollection&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.IProducerConsumerCollection-1)를 구현하는 모든 컬렉션 형식의 캡슐화

*   취소 토큰으로 취소

*   `foreach`를 사용하는 다음 두 가지 유형의 열거 

    1. 읽기 전용 열거
    
    2. 열거된 항목을 제거하는 열거
    
## <a name="bounding-and-blocking-support"></a>한계 지정 및 차단 지원 

[BlockingCollection&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.BlockingCollection-1)는 한계 지정 및 차단을 지원합니다. 한계 지정이란 컬렉션의 최대 용량을 설정하는 것을 의미합니다. 메모리에서 컬렉션의 최대 크기를 제어할 수 있고 공급자 스레드가 소비자 스레드와 보조를 맞춰 실행되도록 할 수 있기 때문에 특정 시나리오에서 한계 지정은 중요한 의미를 가집니다.

다중 스레드 또는 작업은 동시에 항목을 컬렉션에 추가할 수 있습니다. 컬렉션이 지정된 최대 용량에 도달하는 경우 항목 하나를 제거할 때까지 공급자 스레드를 차단합니다. 여러 소비자에서 항목을 동시에 제거할 수 있습니다. 컬렉션이 비어 있는 경우 공급자가 항목 하나를 추가할 때까지 소비자 스레드를 차단합니다. 공급자 스레드는 `CompleteAdding`을 호출하여 더 이상 추가할 항목이 없음을 나타낼 수 있습니다. 소비자 스레드는 컬렉션이 비어 있고 항목이 더 이상 추가되지 않는 시점을 파악하기 위해 `IsCompleted` 속성을 모니터링합니다. 다음 예제에서는 한계 용량이 100인 간단한 `BlockingCollection`을 보여 줍니다. 공급자 작업에서는 일부 외부 조건이 true이면 항목을 컬렉션에 추가한 다음 `CompleteAdding`을 호출합니다. 소비자 작업에서는 `IsCompleted` 속성이 true일 때까지 항목을 가져옵니다.

```csharp
// A bounded collection. It can hold no more 
// than 100 items at once.
BlockingCollection<Data> dataItems = new BlockingCollection<Data>(100);


// A simple blocking consumer with no cancellation.
Task.Run(() => 
{
    while (!dataItems.IsCompleted)
    {

        Data data = null;
        // Blocks if number.Count == 0
        // IOE means that Take() was called on a completed collection.
        // Some other thread can call CompleteAdding after we pass the
        // IsCompleted check but before we call Take. 
        // In this example, we can simply catch the exception since the 
        // loop will break on the next iteration.
        try
        {
            data = dataItems.Take();
        }
        catch (InvalidOperationException) { }

        if (data != null)
        {
            Process(data);
        }
    }
    Console.WriteLine("\r\nNo more items to take.");
});

// A simple blocking producer with no cancellation.
Task.Run(() =>
{
    while (moreItemsToAdd)
    {
        Data data = GetData();
        // Blocks if numbers.Count == dataItems.BoundedCapacity
        dataItems.Add(data);
    }
    // Let consumer know we are done.
    dataItems.CompleteAdding();
});
```

완전한 예제는 [방법: BlockingCollection에서 개별적으로 항목 추가 및 가져오기](how-to-add-and-take-items.md)를 참조하십시오.

## <a name="timed-blocking-operations"></a>시간 지정된 차단 작업

한계 지정된 컬렉션의 시간 지정된 `TryAdd` 및 `TryTake` 차단 작업에서 메서드는 항목을 추가하거나 가져오려고 합니다. 사용 가능한 항목이 있으면 이 항목은 참조로 전달된 변수에 저장되고 메서드에서 `true`를 반환합니다. 지정된 제한 시간이 경과한 후에 검색되는 항목이 없으면 메서드에서 `false`를 반환합니다. 그러면 스레드에서 유용한 다른 작업을 수행한 후에 컬렉션에 대한 액세스를 다시 시도할 수 있습니다. 시간 지정된 차단 액세스에 대한 예제는 [방법: BlockingCollection에서 개별적으로 항목 추가 및 가져오기](how-to-add-and-take-items.md)에 있는 두 번째 예제를 참조하십시오.

## <a name="cancelling-add-and-take-operations"></a>추가 및 가져오기 작업 취소

추가 및 가져오기 작업은 일반적으로 루프에서 수행됩니다. `TryAdd` 또는 `TryTake` 메서드에 `CancellationToken`을 전달한 다음, 각 반복마다 이 토큰의 `IsCancellationRequested` 속성 값을 확인하여 루프를 취소할 수 있습니다. 이 속성 값이 `true`이면 사용자가 모든 리소스를 정리하고 루프를 종료하여 취소 요청에 응답해야 합니다. 다음 예제에서는 취소 토큰을 사용하는 `TryAdd`의 오버로드 및 이 오버로드를 사용하는 코드를 보여 줍니다.

```csharp
BlockingCollection<string> bc = new BlockingCollection<string>(new ConcurrentBag<string>(), 1000 );
```

## <a name="specifying-the-collection-type"></a>컬렉션 형식 지정

`BlockingCollection<T>;`를 만들 때는 한계 지정되는 용량뿐 아니라 사용할 컬렉션의 형식도 지정할 수 있습니다. 예를 들어 FIFO(선입 선출) 동작에 [ConcurrentQueue&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentQueue-1)를 지정하거나 LIFO(후입 선출) 동작에 [ConcurrentStack&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentStack-1)를 지정할 수 있습니다. [IProducerConsumerCollection&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.IProducerConsumerCollection-1) 인터페이스를 구현하는 컬렉션 클래스를 모두 사용할 수 있습니다. `BlockingCollection<T>`의 기본 컬렉션 형식은 `ConcurrentQueue<T>`입니다. 다음 코드 예제에서는 용량이 1000이고 [ConcurrentBag&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentBag-1)를 사용하는 `BlockingCollection<T>` 문자열을 만드는 방법을 보여 줍니다.

```csharp
BlockingCollection<string> bc = new BlockingCollection<string>(new ConcurrentBag<string>(), 1000 );
```

## <a name="ienumerable-support"></a>IEnumerable 지원

`BlockingCollection<T>`에서는 컬렉션이 완료될 때까지, 즉 컬렉션이 비어 있고 항목이 더 이상 추가되지 않을 때까지 소비자가 `foreach` 문을 사용하여 항목을 제거할 수 있도록 하는 `GetConsumingEnumerable` 메서드를 제공합니다. 자세한 내용은 [방법: ForEach를 사용하여 BlockingCollection 항목 제거](how-to-use-foreach-to-remove.md)를 참조하십시오.

## <a name="using-many-blockingcollections-as-one"></a>여러 BlockingCollection을 하나로 사용

소비자에서 여러 컬렉션의 항목을 동시에 가져와야 하는 시나리오의 경우 `BlockingCollection<T>` 배열을 만들고, 배열 내의 컬렉션에 항목을 추가하거나 이 컬렉션으로부터 항목을 가져올 `AddToAny` 및 `TakeFromAny`와 같은 정적 메서드를 사용할 수 있습니다. 특정 컬렉션이 차단되는 즉시 메서드에서 작업을 수행할 수 있는 컬렉션을 찾을 때까지 다른 컬렉션에 대한 액세스를 시도합니다. 자세한 내용은 [방법: 파이프라인에서 차단 컬렉션 배열 사용](how-to-use-arrays-of-blockingcollections.md)을 참조하십시오.

## <a name="see-also"></a>참고 항목

[System.Collections.Concurrent](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent)

[컬렉션 및 데이터 구조](../index.md)

[스레드로부터 안전한 컬렉션](index.md)




<!--HONumber=Nov16_HO1-->


