---
title: "스레드로부터 안전한 컬렉션"
description: "스레드로부터 안전한 컬렉션"
keywords: .NET, .NET Core
author: mairaw
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 92d5515d-f5d6-4a09-8bbb-31865d678643
translationtype: Human Translation
ms.sourcegitcommit: cfe65fcba1b3fdc09ffcac704a760d8ce29ea60b
ms.openlocfilehash: 421d46585b5d83f5772fa6596ad581c8c6acbf71

---

# <a name="threadsafe-collections"></a>스레드로부터 안전한 컬렉션

[System.Collections.Concurrent](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent) 네임스페이스는 스레드로부터 안전하면서 확장 가능한 몇 가지 컬렉션 클래스를 포함합니다. 여러 스레드는 사용자 코드에서 추가로 동기화할 필요없이 이러한 컬렉션으로부터 안전하고 효율적으로 항목을 추가하거나 제거할 수 있습니다. 새 코드를 작성하는 경우 컬렉션이 여러 스레드를 동시에 작성할 때마다 동시 컬렉션 클래스를 사용합니다. 공유 컬렉션에서 읽기만 하는 경우에 [System.Collections.Generic](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic) 네임스페이스에서 클래스를 사용할 수 있습니다. .NET Framework 1.1 또는 이전 런타임을 대상으로 해야 하는 경우가 아니면 [System.Collections](https://docs.microsoft.com/dotnet/core/api/System.Collections) 컬렉션 클래스를 사용하지 않는 것이 좋습니다.

## <a name="finegrained-locking-and-lockfree-mechanisms"></a>세부적인 잠금 및 잠금 해제 메커니즘

동시 컬렉션 형식의 일부에서는 [SpinLock](https://docs.microsoft.com/dotnet/core/api/System.Threading.SpinLock), [SpinWait](https://docs.microsoft.com/dotnet/core/api/System.Threading.SpinWait), [SemaphoreSlim](https://docs.microsoft.com/dotnet/core/api/System.Threading.SemaphoreSlim) 및 [CountdownEvent](https://docs.microsoft.com/dotnet/core/api/System.Threading.CountdownEvent)와 같은 간단한 동기화 메커니즘을 사용합니다. 실제로 이러한 동기화 형식이 스레드를 `Wait` 상태로 전환하기 전 짧은 기간 동안에는 일반적으로 사용 중인 회전을 사용합니다. 대기 시간이 매우 짧을 경우 회전은 비용이 많이 드는 커널 전환을 포함하는 대기보다 훨씬 계산 비용이 적습니다. 회전을 사용하는 컬렉션 클래스의 경우 이 효율성 덕분에 여러 스레드가 매우 빠른 속도로 항목을 추가하고 제거할 수 있습니다.

[ConcurrentQueue&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentQueue-1) 및 [ConcurrentStack&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentStack-1) 클래스는 잠금을 사용하지 않습니다. 대신, 스레드로부터의 안전성을 달성하기 위해 연동 작업을 사용합니다.

> [!NOTE]
> 동시 컬렉션 클래스가 [ICollection](https://docs.microsoft.com/dotnet/core/api/System.Collections.ICollection)를 지원하기 때문에 이러한 속성이 관련되지 않은 경우에도 `IsSynchronized` 및 `SyncRoot` 속성에 대한 구현을 제공합니다. `IsSynchronized`는 항상 `false`를 반환하고 `SyncRoot`는 항상 null입니다.

다음 테이블에서는 [System.Collections.Concurrent](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent) 네임스페이스의 컬렉션 형식을 나열합니다.

형식 | 설명
---- | -----------
[BlockingCollection&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.BlockingCollection-1) | [IProducerConsumerCollection&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.IProducerConsumerCollection-1)을 구현하는 모든 형식에 대해 경계 및 차단 기능을 제공합니다. 자세한 내용은 [BlockingCollection 개요](blockingcollection-overview.md)를 참조하세요.
[ConcurrentBag&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentBag-1) | 요소의 순서 없는 컬렉션을 스레드로부터 안전하게 구현합니다.
[ConcurrentDictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentDictionary-2) | 키-값 쌍의 사전을 스레드로부터 안전하게 구현합니다.
[ConcurrentQueue&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentQueue-1) | FIFO(선입선출) 큐를 스레드로부터 안전하게 구현합니다.
[ConcurrentStack&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentStack-1) | LIFO(후입선출) 스택을 스레드로부터 안전하게 구현합니다.
[IProducerConsumerCollection&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.IProducerConsumerCollection-1) | 형식이 `BlockingCollection`에 사용하도록 구현해야 하는 인터페이스입니다.

## <a name="thread-synchronization-in-the-net-framework-version-10-and-20-collections"></a>.NET Framework 버전 1.0 및 2.0 컬렉션에서 스레드 동기화

.NET Framework 버전 1.0에서 처음 도입된 컬렉션은 [System.Collections](https://docs.microsoft.com/dotnet/core/api/System.Collections) 네임스페이스에 위치합니다. 일반적으로 사용되는 [ArrayList](https://docs.microsoft.com/dotnet/core/api/System.Collections.ArrayList) 및 [Hashtable](https://docs.microsoft.com/dotnet/core/api/System.Collections.Hashtable)를 포함하는 이러한 컬렉션은 컬렉션 주변에서 스레드로부터 안전한 래퍼를 반환하는 `Synchronized` 속성을 통해 어느 정도 스레드로부터의 안전성을 제공합니다. 래퍼는 모든 추가 또는 제거 작업에서 전체 컬렉션을 잠그는 방식으로 작동합니다. 따라서 컬렉션에 액세스하려고 하는 각 스레드는 잠금을 사용하기 위해 차례를 기다려야 합니다. 이것은 확장할 수 없고 큰 컬렉션에 상당한 성능 저하가 발생할 수 있습니다. 또한 디자인은 경합 조건에서 완전히 보호되지 않습니다. 

.NET Framework 버전 2.0에서 처음 도입된 컬렉션 클래스는 [System.Collections.Generic](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic) 네임스페이스에 위치합니다. 여기에는 [List&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.List-1), [Dictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.Dictionary-2) 등이 포함됩니다. 이러한 클래스는 `System.Collections` 클래스에 비해 형식 안정성 및 성능을 향상시킵니다. 그러나 `System.Collections.Generic` 컬렉션 클래스는 스레드 동기화를 제공하지 않습니다. 여러 스레드에서 동시에 항목이 추가되거나 제거되는 경우 사용자 코드에서는 모든 동기화를 제공해야 합니다.

`System.Collections.Concurrent` 컬렉션 클래스가 `System.Collections.Generic` 컬렉션 클래스의 형식 안전성을 제공할 뿐만 아니라 `System.Collections` 컬렉션이 제공하는 스레드로부터의 안전성보다 효율적이고 완전하기 때문에 해당 컬렉션 클래스를 사용하는 것이 좋습니다.

## <a name="related-topics"></a>관련 항목

제목 | 설명
----- | -----------
[BlockingCollection 개요](blockingcollection-overview.md) | `BlockingCollection<T>` 형식에서 제공하는 기능에 대해 설명합니다.
[스레드로부터 안전한 컬렉션 사용 시기](when-to-use-a-thread-safe-collection.md) | 스레드로부터 안전한 컬렉션을 사용하는 적절한 시기에 대해 설명합니다.
[방법: ConcurrentDictionary에서 항목 추가 및 제거](how-to-add-and-remove-items.md) | `ConcurrentDictionary<TKey, TValue>`에서 요소를 추가하고 제거하는 방법에 대해 설명합니다.
[방법: BlockingCollection에서 개별적으로 항목 추가 및 가져오기](how-to-add-and-take-items.md) | 읽기 전용 열거자를 사용하지 않고 차단 컬렉션에서 항목을 추가하고 검색하는 방법에 대해 설명합니다.
[방법: 컬렉션에 경계 및 차단 기능 추가](how-to-add-bounding-and-blocking.md ) | 컬렉션 클래스를 `IProducerConsumerCollection<T>;` 컬렉션에 대한 기본 저장소 메커니즘으로 사용하는 방법에 대해 설명합니다.
[방법: ForEach를 사용하여 BlockingCollection의 항목 제거](how-to-use-foreach-to-remove.md ) | `foreach`을 사용하여 차단 컬렉션에서 모든 항목을 제거하는 방법에 대해 설명합니다.
[방법: 파이프라인에서 차단 컬렉션 배열 사용](how-to-use-arrays-of-blockingcollections.md) | 파이프라인을 구현하는 동시에 여러 차단 컬렉션을 사용하는 방법에 대해 설명합니다.
[방법: ConcurrentBag을 사용하여 개체 풀 만들기](how-to-create-an-object-pool.md) | 개체를 끊임없이 새로 만드는 대신 다시 사용할 수 있는 시나리오에서 동시 모음을 사용하고 성능을 향상시키는 방법을 보여 줍니다.

## <a name="reference"></a>참조

[System.Collections.Concurrent](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent)






 





<!--HONumber=Nov16_HO3-->


