---
title: "스레드로부터 안전한 컬렉션 사용 시기"
description: "스레드로부터 안전한 컬렉션 사용 시기"
keywords: .NET, .NET Core
author: mairaw
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: a2a42d44-f6a5-4f16-9000-026221d66349
translationtype: Human Translation
ms.sourcegitcommit: e07788926a995b41571be276379ad9285747951d
ms.openlocfilehash: 74f421a5badd9f2c7bf10fa1dfdf98161bba2ce8

---

# <a name="when-to-use-a-threadsafe-collection"></a>스레드로부터 안전한 컬렉션 사용 시기

`ConcurrentQueue`, `ConcurrentStack`, `ConcurrentDictionary`, `ConcurrentBag`, 및 `BlockingCollection` 컬렉션 형식은 다중 스레드 추가 및 제거 작업을 지원하도록 특별히 디자인되었습니다. 이러한 새 형식은 스레드로부터의 안전성을 달성하기 위해 다양한 종류의 효율적인 잠금 및 잠금 해제 동기화 메커니즘을 사용합니다. 동기화로 인해 작업에 오버헤드가 더해집니다. 사용되는 동기화의 종류, 수행되는 작업의 종류 및 컬렉션에 동시에 액세스하려는 스레드의 수와 같은 기타 요인에 따라 오버헤드의 양이 달라집니다.

일부 시나리오에서는 동기화 오버헤드가 거의 없고 다중 스레드 형식을 외부 잠금으로 보호하는 경우 스레드로부터 안전하지 않은 형식보다 훨씬 더 빠르게 수행하고 크기를 조정할 수 있습니다. 다른 시나리오에서는 오버헤드로 인해 스레드로부터 안전한 형식이 외부에서 잠근 스레드로부터 안전하지 않은 버전의 형식보다 동일하거나 훨씬 더 느리게 수행하고 크기를 조정하게 될 수 있습니다.

다음 섹션에서는 읽기 및 쓰기 작업에 사용자가 제공한 잠금이 있는 스레드로부터 안전한 컬렉션 및 스레드로부터 안전하지 않은 컬렉션을 사용하는 경우에 대한 일반적인 지침을 제공합니다. 성능이 여러 요인에 따라 달라질 수 있기 때문에 지침은 구체적이지 않고 모든 상황에서 반드시 유효하지 않습니다. 성능이 매우 중요한 경우 사용할 컬렉션 형식을 결정하는 가장 좋은 방법은 대표적인 컴퓨터 구성 및 부하에 따라 성능을 측정하는 것입니다. 이 문서에서는 다음과 같은 용어를 사용합니다.

*순수 생산자-소비자 시나리오:* 지정된 모든 스레드가 요소를 추가하거나 제거하는 작업 중 하나만 수행합니다.

*혼합 생산자-소비자 시나리오:* 지정된 모든 스레드가 요소를 추가하고 제거하는 작업을 모두 수행합니다.

*속도 향상:* 동일한 시나리오에서 다른 형식에 비해 더 빠른 알고리즘 성능입니다.

*확장성:* 컴퓨터의 코어 수에 비례하는 성능의 증가입니다. 배율이 두 개의 코어를 사용하는 경우보다 8개의 코어를 사용하는 경우 더 빠르게 수행하는 알고리즘입니다.

## <a name="concurrentqueuelttgt-vs-queuelttgt"></a>ConcurrentQueue&lt;T&gt; 및 Queue&lt;T&gt;

각 요소에 대한 처리 시간이 매우 작은 순수 생산자-소비자 시나리오(몇 가지 지침)에서 [System.Collections.Concurrent.ConcurrentQueue&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentQueue-1)는 외부 잠금이 있는 [System.Collections.Generic.Queue&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.Queue-1)를 통해 어느 정도의 성능 이점을 제공할 수 있습니다. 이 시나리오에서 `ConcurrentQueue<T>`은 하나의 전용 스레드가 큐에 대기 중이고 다른 하나의 전용 스레드가 큐에서 해제되는 경우 최고의 성능을 발휘합니다. 이 규칙을 적용하지 않는 경우 `Queue<T>`은 다중 코어인 컴퓨터의 `ConcurrentQueue<T>`보다 약간 더 빠르게 수행할 수 있습니다. 

처리 시간이 500FLOPS(부동 소수점 연산) 이상인 경우 매우 우수한 확장성을 가진 두 개의 스레드 규칙은 `ConcurrentQueue<T>`에 적용되지 않습니다. `Queue<T>`는 이 시나리오에서 잘 확장되지 않습니다.

혼합 생산자-소비자 시나리오에서 처리 시간이 매우 작은 경우 `Queue<T>`의 외부 잠금 배율은 `ConcurrentQueue<T>`에 비해 우수합니다. 그러나 처리 시간이 500FLOPS 이상인 경우 `ConcurrentQueue<T>`의 확장성이 더 우수합니다.

## <a name="concurrentstack-vs-stack"></a>ConcurrentStack 및 스택

처리 시간이 매우 짧으면 순수 생산자-소비자 시나리오에서 외부 잠금이 있는 [System.Collections.Concurrent.ConcurrentStack&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentStack-1) 및 [System.Collections.Generic.Stack&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.Stack-1)은 하나의 전용 푸싱 스레드 및 하나의 전용 팝업 스레드에 대해 거의 동일하게 작동할 수 있습니다. 그러나 스레드가 증가하면 경합 증가로 인해 두 유형의 성능이 모두 저하되고 `Stack<T>`이 `ConcurrentStack<T>`보다 우수한 결과를 수행할 수 있습니다. 처리 시간이 500FLOPS 이상인 경우 두 형식은 거의 동일한 속도로 확장합니다. 

혼합 생산자-소비자 시나리오에서는 `ConcurrentStack<T>`이 소규모 및 대규모 워크로드를 빠르게 처리합니다.

`PushRange` 및 `TryPopRange`를 사용하면 액세스 시간을 크게 절약할 수 있습니다.

## <a name="concurrentdictionary-vs-dictionary"></a>ConcurrentDictionary 및 사전

일반적으로 여러 스레드에서 동시에 키 또는 값을 추가하고 업데이트 모든 시나리오에서 [System.Collections.Concurrent.ConcurrentDictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentDictionary-2)를 사용합니다. 잦은 업데이트 및 비교적 적은 읽기를 포함하는 시나리오에서 `ConcurrentDictionary<TKey, TValue>`은 일반적으로 어느 정도 이점을 제공합니다. 많은 읽기 및 업데이트를 포함하는 시나리오에서 `ConcurrentDictionary<TKey, TValue>`은 일반적으로 코어를 가진 컴퓨터에서 훨씬 빠릅니다.

잦은 업데이트를 포함하는 시나리오에서 `ConcurrentDictionary<TKey, TValue>`의 동시성 수준을 증가시키고 더 많은 코어가 있는 컴퓨터에서 성능이 향상되는지를 측정할 수 있습니다. 동시성 수준을 변경하면 가능한 한 글로벌 운영을 하지 마십시오.

사전이 모든 스레드에서 수정되지 않은 경우 동기화가 필요하지 않기 때문에 키 또는 값을 읽기만 할 때는 [System.Collections.Generic.Dictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.Dictionary-2)가 더 빠릅니다.

## <a name="concurrentbag"></a>ConcurrentBag

순수 생산자-소비자 시나리오에서 [System.Collections.Concurrent.ConcurrentBag&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentBag-1)은 다른 동시 컬렉션 형식보다 더 느리게 작동할 수 있습니다.

혼합 생산자-소비자 시나리오에서 `ConcurrentBag<T>`은 일반적으로 크고 작은 작업에 대한 다른 동시 컬렉션 형식보다 훨씬 빠르고 확장성이 큽니다.

## <a name="blockingcollection"></a>BlockingCollection

경계 및 차단 구문이 필요한 경우 [System.Collections.Concurrent.BlockingCollection&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.BlockingCollection-1)은 사용자 지정 구현을 보다 빠르게 수행할 수 있습니다. 또한 여러 가지 취소, 열거 및 예외 처리를 지원합니다.

## <a name="see-also"></a>참고 항목

[System.Collections.Concurrent](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent)

[스레드로부터 안전한 컬렉션](index.md)



<!--HONumber=Nov16_HO1-->


