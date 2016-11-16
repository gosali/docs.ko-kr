---
title: "제네릭 컬렉션 사용 기준"
description: "제네릭 컬렉션 사용 기준"
keywords: .NET, .NET Core
author: mairaw
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 971e08bd-b63f-4832-9e61-9f65cbedd352
translationtype: Human Translation
ms.sourcegitcommit: b828bb1d6c8fb750ad9ef34f8a7a1b7d2574f4c6
ms.openlocfilehash: c7e89a2139ca217a52be0fb9796772879c64e355

---

# <a name="when-to-use-generic-collections"></a>제네릭 컬렉션 사용 기준

제네릭 컬렉션을 사용하면 기본 컬렉션 형식에서 파생하고 형식별 멤버를 구현하지 않아도 형식 보안 이점이 즉시 제공되므로 일반적으로는 제네릭 컬렉션을 사용하는 것이 좋습니다. 또한 컬렉션 요소가 값 형식일 때는 대개 제네릭 컬렉션 형식이 해당하는 제네릭이 아닌 컬렉션 형식 및 제네릭이 아닌 기본 컬렉션 형식에서 파행되는 형식에 비해 성능도 뛰어납니다. 제네릭을 사용하는 경우에는 요소를 boxing할 필요가 없기 때문입니다. 

여러 스레드가 컬렉션에서 동시에 항목을 추가하거나 제거하는 경우 [System.Collections.Concurrent](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent) 네임스페이스에서 제네릭 컬렉션 클래스를 사용해야 합니다.

기존 컬렉션 형식에 해당하는 제네릭 형식은 다음과 같습니다. 

*   [List&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.List-1)는 [ArrayList](https://docs.microsoft.com/dotnet/core/api/System.Collections.ArrayList)에 해당하는 제네릭 클래스입니다.

*   [Dictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.Dictionary-2) 및 [ConcurrentDictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentDictionary-2)는 [Hashtable](https://docs.microsoft.com/dotnet/core/api/System.Collections.Hashtable)에 해당하는 제네릭 클래스입니다. 

*   [Collection&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.ObjectModel.Collection-1)는 [CollectionBase](https://docs.microsoft.com/dotnet/core/api/System.Collections.CollectionBase)에 해당하는 제네릭 클래스입니다. `Collection<T>`는 기본 클래스로 사용할 수 있지만 `CollectionBase`와는 달리 추상 클래스가 아니므로 훨씬 쉽게 사용할 수 있습니다.

*   [ReadOnlyCollection&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.ObjectModel.ReadOnlyCollection-1)는 [ReadOnlyCollectionBase](https://docs.microsoft.com/dotnet/core/api/System.Collections.ReadOnlyCollectionBase)에 해당하는 제네릭 클래스입니다. `ReadOnlyCollection<T>`은 추상 클래스가 아니며 기존 [List&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.List-1)를 읽기 전용 컬렉션으로 쉽게 노출할 수 있는 생성자를 포함합니다.

*   [Queue&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.Queue-1), [ConcurrentQueue&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentQueue-1), [Stack&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.Stack-1), [ConcurrentStack&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentStack-1) 및 [SortedList&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.SortedList-2) 제네릭 클래스는 각각 동일한 이름의 제네릭이 아닌 클래스에 해당합니다.

## <a name="additional-types"></a>추가 형식

다수의 제네릭 컬렉션 형식에는 그에 해당하는 제네릭이 아닌 형식이 없습니다. 이러한 형식은 다음과 같습니다. 

*   [LinkedList&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.LinkedList-1)는 O(1) 삽입 및 제거 작업을 제공하는 범용 연결된 목록입니다.

*   [SortedDictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.SortedDictionary-2)는 O(로그 n) 삽입 및 검색 작업을 제공하는 정렬된 사전으로, [SortedList&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.SortedList-2) 대신 사용하면 유용합니다. 

*   [KeyedCollection&lt;TKey, TItem&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.ObjectModel.KeyedCollection-2)은 자체 키를 포함하는 개체를 저장할 수 있도록 하는 목록과 사전 간의 하이브리드 형식입니다.

*   [BlockingCollection&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.BlockingCollection-1)은 경계 및 차단 기능을 포함하는 컬렉션 클래스를 구현합니다.

*   [ConcurrentBag&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentBag-1)은 순서가 지정되지 않은 요소의 빠른 삽입 및 제거 기능을 제공합니다.

## <a name="linq-to-objects"></a>LINQ to Objects

LINQ to Objects 기능을 사용하면 개체 형식이 [System.Collections.IEnumerable](https://docs.microsoft.com/dotnet/core/api/System.Collections.IEnumerable) 또는 [System.Collections.Generic.IEnumerable&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.IEnumerable-1) 인터페이스를 구현하는 경우 LINQ 쿼리를 통해 메모리 내 개체에 액세스할 수 있습니다. LINQ 쿼리는 데이터 액세스를 위한 일반 패턴을 제공하고, 표준 `foreach` 루프에 비해 간결하고 쉽게 읽을 수 있으며, 필터링, 순서 지정 및 그룹화 기능을 제공합니다. 또한 LINQ 쿼리를 통해 성능을 향상시킬 수도 있습니다.

## <a name="additional-functionality"></a>추가 기능

일부 제네릭 형식은 제네릭이 아닌 컬렉션 형식에는 없는 기능을 포함합니다. 예를 들어 제네릭이 아닌 [ArrayList](https://docs.microsoft.com/dotnet/core/api/System.Collections.ArrayList) 클래스에 해당하는 [List&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.List-1) 클래스에는 제네릭 대리자를 허용하는 여러 메서드가 포함되어 있습니다. 이러한 대리자로는 목록 검색을 위한 메서드를 지정할 수 있도록 하는 [Predicate&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Predicate-1) 대리자, 목록의 각 요소에 대해 작동하는 메서드를 나타내는 [Action&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Action-1) 대리자가 있습니다.

[List&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.List-1) 클래스를 사용하면 목록 검색 및 정렬을 위해 [IComparer&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.IComparer-1) 제네릭 인터페이스 구현을 직접 지정할 수 있습니다. [SortedDictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.SortedDictionary-2) 및 [SortedList&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.SortedList-2) 클래스에도 이 기능이 있습니다. 또한 이러한 클래스를 사용하는 경우 컬렉션을 만들 때 비교자를 지정할 수 있습니다. 마찬가지로 [Dictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.Dictionary-2) 및 [KeyedCollection&lt;TKey, TItem&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.ObjectModel.KeyedCollection-2) 클래스를 통해서도 고유한 같음 비교자를 지정할 수 있습니다.

## <a name="see-also"></a>참고 항목

[컬렉션 및 데이터 구조](index.md) 

[일반적으로 사용되는 컬렉션 형식](commonly-used-collection-types.md)



<!--HONumber=Nov16_HO3-->


