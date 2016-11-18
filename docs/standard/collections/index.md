---
title: "컬렉션 및 데이터 구조"
description: "컬렉션 및 데이터 구조"
keywords: .NET, .NET Core
author: mairaw
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 9e70255a-c02a-4046-86b7-10c84bab2d38
translationtype: Human Translation
ms.sourcegitcommit: cfe65fcba1b3fdc09ffcac704a760d8ce29ea60b
ms.openlocfilehash: a6ebc7bfcd42b93505a2e5c2c460f4f2cbe618a0

---

# <a name="collections-and-data-structures"></a>컬렉션 및 데이터 구조

비슷한 데이터는 컬렉션으로 저장 및 조작하면 보다 효율적으로 처리할 수 있는 경우가 많습니다. [System.Collections](https://docs.microsoft.com/dotnet/core/api/System.Collections), [System.Collections.Generic](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic) 또는 [System.Collections.Concurrent](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent) 네임스페이스에서 [System.Array](https://docs.microsoft.com/dotnet/core/api/System.Array) 클래스를 사용하여 개별 요소나 컬렉션의 요소 범위를 추가, 제거 및 수정할 수 있습니다.

컬렉션에는 제네릭 컬렉션과 제네릭이 아닌 컬렉션의 두 가지 기본 유형이 있습니다. 제네릭 컬렉션은 컴파일 타임 시 형식이 안전합니다. 이로 인해 제네릭 컬렉션은 일반적으로 성능이 더 뛰어납니다. 제네릭 컬렉션은 생성 시 형식 매개 변수를 허용하며, 컬렉션에서 항목을 추가하거나 제거할 때 [Object](https://docs.microsoft.com/dotnet/core/api/System.Object) 형식 간에 캐스팅을 수행하지 않아도 됩니다. 제네릭이 아닌 컬렉션은 [Object](https://docs.microsoft.com/dotnet/core/api/System.Object)로 항목을 저장하고 캐스팅을 수행해야 합니다. 이전 코드에는 제네릭이 아닌 컬렉션이 포함되어 있는 경우도 있습니다.

[System.Collections.Concurrent](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent) 네임스페이스의 컬렉션은 여러 스레드에서 컬렉션 항목에 액세스하기 위한 효율적이고 스레드로부터 안전한 작업을 제공합니다.

## <a name="common-collection-features"></a>일반 컬렉션 기능

모든 컬렉션은 컬렉션의 항목 추가, 제거 또는 찾기를 위한 방법을 제공합니다. 또한 직접/간접적으로 [ICollection](https://docs.microsoft.com/dotnet/core/api/System.Collections.ICollection) 인터페이스 또는 [ICollection&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.ICollection-1) 인터페이스 공유를 구현하는 모든 컬렉션은 다음 기능을 공유합니다. 

* **컬렉션을 열거하는 기능**

   .NET Framework 컬렉션은 [System.Collections.IEnumerable](https://docs.microsoft.com/dotnet/core/api/System.Collections.IEnumerable) 또는 [System.Collections.Generic.IEnumerable&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.IEnumerable-1)를 구현하여 컬렉션을 반복할 수 있습니다. 열거자는 컬렉션의 모든 요소에 대한 이동 가능 포인터라고 할 수 있습니다. `foreach, in` 문(C#)은 `GetEnumerator` 메서드가 노출하는 열거자를 사용하며 해당 열거자를 조작하는 복잡한 작업을 숨깁니다. 또한 [System.Collections.Generic.IEnumerable&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.IEnumerable-1)을 구현하는 모든 컬렉션은 쿼리 가능 형식으로 간주할 수 있으며 LINQ를 사용하여 쿼리할 수 있습니다. LINQ 쿼리는 데이터 액세스를 위한 일반 패턴을 제공합니다. 이러한 쿼리는 대개 루프 각각의 표준보다 간결하고 읽기 쉬우며 필터링, 순서 지정 및 그룹화 기능을 제공합니다. 또한 LINQ 쿼리를 통해 성능을 향상시킬 수도 있습니다.
    
* **컬렉션의 내용을 배열에 복사하는 기능**

   `CopyTo` 메서드를 사용하면 모든 컬렉션을 배열에 복사할 수 있습니다. 그러나 새 배열의 요소 순서는 열거자가 요소를 반환하는 시퀀스를 기준으로 합니다. 결과 배열은 항상 1차원이며 하한은 0입니다.
    
또한 다수의 컬렉션 클래스에는 다음 기능도 포함되어 있습니다.

* **Capacity 및 Count 속성**

   컬렉션의 용량은 컬렉션에 포함할 수 있는 요소의 수이고, 컬렉션의 카운트는 컬렉션에 실제로 포함되어 있는 요소의 수입니다. 용량이나 카운트 중 하나 또는 둘 다를 숨기는 컬렉션도 있습니다.
    
   대부분의 컬렉션은 현재 용량에 도달하면 자동으로 용량을 확장합니다. 이때 메모리가 다시 할당되며 요소는 이전 컬렉션에서 새 컬렉션으로 복사됩니다. 따라서 컬렉션을 사용하는 데 필요한 코드는 감소하지만 컬렉션 성능이 저하될 수 있습니다. 예를 들어 [List&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.List-1)의 경우 `Count`가 `Capacity`보다 작으면 항목을 추가하는 것은 O(1) 작업이 됩니다. 새 요소를 포함할 수 있도록 용량을 늘려야 하는 경우 항목을 추가하는 것은 O(n) 작업이 됩니다. 여기서 n은 `Count`입니다. 메모리가 여러 번 다시 할당되어 성능이 저하되는 현상을 방지하는 가장 좋은 방법은 초기 용량을 컬렉션의 예상 크기로 설정하는 것입니다. 
    
   [BitArray](https://docs.microsoft.com/dotnet/core/api/System.Collections.BitArray)는 해당 용량과 길이 및 카운트가 모두 같은 특수한 경우입니다.
    
*   **일관된 하한**

   컬렉션의 하한은 첫 번째 요소의 인덱스입니다. [System.Collections](https://docs.microsoft.com/dotnet/core/api/System.Collections) 네임스페이스의 모든 인덱싱된 컬렉션은 하한이 0입니다. 즉, 0부터 인덱싱됩니다. 기본적으로 [Array](https://docs.microsoft.com/dotnet/core/api/System.Array)의 하한은 0이지만 `Array.CreateInstance`를 사용하여 `Array` 클래스의 인스턴스를 만들 때 다른 하한을 정의할 수 있습니다.

*   **여러 스레드로부터의 액세스를 위한 동기화**([System.Collections](https://docs.microsoft.com/dotnet/core/api/System.Collections) 클래스에만 해당됨).

   [System.Collections](https://docs.microsoft.com/dotnet/core/api/System.Collections) 네임스페이스의 제네릭이 아닌 컬렉션 형식은 동기화와 관련하여 어느 정도의 스레드 보안을 제공합니다(일반적으로 `SyncRoot` 및 `IsSynchronized` 구성원을 통해 노출됨). 이러한 컬렉션은 기본적으로 스레드로부터 안전하지 않습니다. 확장 가능하며 효율적인 다중 스레드 방식으로 컬렉션에 액세스해야 하는 경우에는 [System.Collections.Concurrent](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent) 네임스페이스의 클래스 중 하나를 사용하거나 변경할 수 없는 컬렉션을 사용하는 것이 좋습니다. 자세한 내용은 [스레드로부터 안전한 컬렉션](threadsafe/index.md)을 참조하세요.    
    
## <a name="choosing-a-collection"></a>컬렉션 선택 

일반적으로는 제네릭 컬렉션을 사용해야 합니다. 다음 표에는 몇 가지 일반적인 컬렉션 시나리오와 이러한 시나리오에서 사용할 수 있는 컬렉션 클래스에 대해 설명합니다. 제네릭 컬렉션을 처음 사용하는 경우 이 표의 내용을 참조하여 작업에 가장 적합한 제네릭 컬렉션을 선택할 수 있습니다.

수행할 작업 | 제네릭 컬렉션 옵션 | 제네릭이 아닌 컬렉션 옵션
---------- | ---------------------------- | --------------------------------
키별로 빠르게 조회할 수 있도록 항목을 키/값 쌍으로 저장 | [System.Collections.Generic.Dictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.Dictionary-2) | [Hashtable](https://docs.microsoft.com/dotnet/core/api/System.Collections.Hashtable)
인덱스별로 항목 액세스 | [System.Collections.Generic.List&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.List-1) | [System.Array](https://docs.microsoft.com/dotnet/core/api/System.Array), [System.Collections.ArrayList](https://docs.microsoft.com/dotnet/core/api/System.Collections.ArrayList)
FIFO(선입 선출) 방식으로 항목 사용 | [System.Collections.Generic.Queue&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.Queue-1) | [System.Collections.Queue](https://docs.microsoft.com/dotnet/core/api/System.Collections.Queue)
LIFO(후입 선출) 방식으로 데이터 사용 | [System.Collections.Generic.Stack&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.Stack-1) | [System.Collections.Stack](https://docs.microsoft.com/dotnet/core/api/System.Collections.Stack)
순서대로 항목 액세스 | [System.Collections.Generic.LinkedList&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.LinkedList-1) | 권장 사항 없음
항목을 컬렉션에 추가하거나 컬렉션에서 제거할 때 알림이 표시됩니다. (implements [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/core/api/System.ComponentModel.INotifyPropertyChanged) and [INotifyCollectionChanged](https://docs.microsoft.com/dotnet/core/api/System.Collections.Specialized.INotifyCollectionChanged)) | [System.Collections.ObjectModel.ObservableCollection&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.ObjectModel.ObservableCollection-1) | 권장 사항 없음
정렬된 컬렉션 사용 | [System.Collections.Generic.SortedList&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.SortedList-2) | [System.Collections.SortedList](https://docs.microsoft.com/dotnet/core/api/System.Collections.SortedList)
고유한 요소의 효율적인 저장 및 액세스 관리 | [System.Collections.Generic.HashSet&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.HashSet-1), [System.Collections.Generic.SortedSet&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.SortedSet-1) | 권장 사항 없음

## <a name="related-topics"></a>관련 항목

제목 | 설명
----- | -----------
[Collection 클래스 선택](selecting-a-collection-class.md) | 다양한 컬렉션에 대해 설명하고 시나리오에 맞는 컬렉션을 선택하는 데 도움이 되는 정보를 제공합니다.
[일반적으로 사용되는 컬렉션 형식](commonly-used-collection-types.md) | [System.Array](https://docs.microsoft.com/dotnet/core/api/System.Array), [System.Collections.Generic.List&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.List-1) 및 [System.Collections.Generic.Dictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.Dictionary-2)와 같이 일반적으로 사용되는 제네릭 및 제네릭이 아닌 컬렉션 형식을 설명합니다. 
[제네릭 컬렉션 사용 기준](when-to-use-generic-collections.md) | 제네릭 컬렉션 형식의 사용을 설명합니다.
[컬렉션 내에서 비교 및 정렬](comparisons-and-sorts-within-collections.md) | 컬렉션에서 같음 비교 및 정렬 비교를 사용하는 방법을 설명합니다.
[Sorted 컬렉션 형식](sorted-collection-types.md) | 정렬된 컬렉션의 성능 및 특징에 대해 설명합니다.
[Hashtable 및 Dictionary 컬렉션 형식](hashtable-and-dictionary-collection-types.md) | 제네릭 및 제네릭이 아닌 해시 기반 사전 형식의 기능을 설명합니다.
[스레드로부터 안전한 컬렉션](threadsafe/index.md) | 여러 스레드에서 안전하고 효율적인 동시 액세스를 지원하는 [System.Collections.Concurrent.BlockingCollection&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.BlockingCollection-1) 및 [System.Collections.Concurrent.ConcurrentBag&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentBag-1)과 같은 컬렉션 형식을 설명합니다.

## <a name="reference"></a>참조

[System.Array](https://docs.microsoft.com/dotnet/core/api/System.Array)

[System.Collections](https://docs.microsoft.com/dotnet/core/api/System.Collections)

[System.Collections.Concurrent](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent)

[System.Collections.Generic](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic)

[System.Collections.Specialized](https://docs.microsoft.com/dotnet/core/api/System.Collections.Specialized)

[System.Linq](https://docs.microsoft.com/dotnet/core/api/System.Linq)
  



<!--HONumber=Nov16_HO3-->


