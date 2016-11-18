---
title: "일반적으로 사용되는 컬렉션 형식"
description: "일반적으로 사용되는 컬렉션 형식"
keywords: .NET, .NET Core
author: mairaw
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 55861611-1e40-4cc2-9ec5-0b2df4ba6c0c
translationtype: Human Translation
ms.sourcegitcommit: d4e7ef84480aa9f735fb8d1ff03c9e8a61127c83
ms.openlocfilehash: 6cadcd91810f08900b58e402240e8a469fad2018

---

# <a name="commonly-used-collection-types"></a>일반적으로 사용되는 컬렉션 형식

컬렉션 형식은 해시 테이블, 큐, 스택, 모음, 사전 및 목록과 같은 데이터 컬렉션의 일반적인 변형입니다.

컬렉션은 [`ICollection`](https://docs.microsoft.com/dotnet/core/api/System.Collections.ICollection), [`IList`](https://docs.microsoft.com/dotnet/core/api/System.Collections.IList), [`IDictionary`](https://docs.microsoft.com/dotnet/core/api/System.Collections.IDictionary) 인터페이스 또는 그에 해당하는 제네릭 대응 항목을 기반으로 합니다. `IList` 인터페이스와 `IDictionary` 인터페이스는 모두 `ICollection` 인터페이스에서 파생되므로 모든 컬렉션은 직접 또는 간접적으로 `ICollection` 인터페이스를 기반으로 합니다. `IList` 인터페이스를 기반으로 하는 컬렉션(예: [`Array`](https://docs.microsoft.com/dotnet/core/api/System.Array), [`ArrayList`](https://docs.microsoft.com/dotnet/core/api/System.Collections.ArrayList) 또는 [`List<T>)`](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.List-1) 또는 직접 `ICollection` 인터페이스를 기반으로 하는 컬렉션(예: [`Queue`](https://docs.microsoft.com/dotnet/core/api/System.Collections.Queue), [`ConcurrentQueue<T>`](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentQueue-1), [`Stack`](https://docs.microsoft.com/dotnet/core/api/System.Collections.Stack), [`ConcurrentStack<T>`](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentStack-1) 또는 [`LinkedList<T>`](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.LinkedList-1))의 모든 요소에는 값만 포함됩니다. `IDictionary` 인터페이스를 기반으로 하는 컬렉션(예: [`Hashtable`](https://docs.microsoft.com/dotnet/core/api/System.Collections.Hashtable) 및 [`SortedList`](https://docs.microsoft.com/dotnet/core/api/System.Collections.SortedList) 클래스, [`Dictionary<TKey, TValue>`](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.Dictionary-2) 및 [`SortedList<TKey, TValue>`](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.SortedList-2) 제네릭 클래스) 또는 [`ConcurrentDictionary<TKey, TValue>`](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentDictionary-2) 클래스의 모든 요소에는 키와 값이 모두 포함됩니다. [`KeyedCollection<TKey, TItem>`](https://docs.microsoft.com/dotnet/core/api/System.Collections.ObjectModel.KeyedCollection-2) 클래스는 값과 해당 값에 포함된 키의 목록이며 이에 따라 목록과 사전처럼 동작하기 때문에 고유합니다.

강력한 형식을 지정하려면 제네릭 컬렉션을 사용하는 것이 가장 좋습니다. 그러나 사용 중인 언어에서 제네릭을 지원하지 않는 경우에는 강력한 형식이 지정되는 컬렉션 클래스를 만들기 위해 확장 가능한 [`CollectionBase`](https://docs.microsoft.com/dotnet/core/api/System.Collections.CollectionBase), [`ReadOnlyCollectionBase`](https://docs.microsoft.com/dotnet/core/api/System.Collections.ReadOnlyCollectionBase), [`DictionaryBase`](https://docs.microsoft.com/dotnet/core/api/System.Collections.DictionaryBase) 등의 추상 기본 컬렉션이 [`System.Collections`](https://docs.microsoft.com/dotnet/core/api/System.Collections) 네임스페이스에 포함되어 있습니다. 다중 스레드 컬렉션에 효율적으로 액세스하려면 [`System.Collections.Concurrent`](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent) 네임 스페이스에서 제네릭 컬렉션을 사용해야 합니다.

컬렉션은 요소 저장/정렬 방식과 검색/비교 수행 방식에 따라 달라질 수 있습니다. [`Queue`](https://docs.microsoft.com/dotnet/core/api/System.Collections.Queue) 클래스와 [`Queue<T>`](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.Queue-1) 제네릭 클래스는 FIFO(선입 선출) 목록을 제공하는 반면 [`Stack`](https://docs.microsoft.com/dotnet/core/api/System.Collections.Stack) 클래스와 [`Stack<T>`](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.Stack-1) 제네릭 클래스는 LIFO(후입 선출) 목록을 제공합니다. [`SortedList`](https://docs.microsoft.com/dotnet/core/api/System.Collections.SortedList) 클래스와 [`SortedList<TKey, TValue>`](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.SortedList-2) 제네릭 클래스는 정렬된 버전의 [`Hashtable`](https://docs.microsoft.com/dotnet/core/api/System.Collections.Hashtable) 클래스와 [`Dictionary<TKey, TValue>`](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.Dictionary-2) 제네릭 클래스를 제공합니다. `Hashtable` 또는 `Dictionary<TKey, TValue>` 요소는 요소 키를 통해 액세스할 수 있지만 `SortedList` 또는 [`KeyedCollection<TKey, TItem>`](https://docs.microsoft.com/dotnet/core/api/System.Collections.ObjectModel.KeyedCollection-2) 요소는 요소 키나 인덱스를 통해 액세스할 수 있습니다. 컬렉션의 인덱스는 모두 0부터 시작됩니다(0부터 시작하지 않는 배열을 허용하는 [`Array`](https://docs.microsoft.com/dotnet/core/api/System.Array) 제외).

개체 형식이 [`IEnumerable`](https://docs.microsoft.com/dotnet/core/api/System.Collections.IEnumerable) 또는 [`IEnumerable<T>`](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.IEnumerable-1)를 구현하는 경우에는 LINQ to Objects 기능을 사용하여 LINQ 쿼리를 통해 메모리 내 개체에 액세스할 수 있습니다. LINQ 쿼리는 데이터 액세스를 위한 일반 패턴을 제공하고, 일반적으로 표준 foreach 루프에 비해 간결하고 쉽게 읽을 수 있으며, 필터링, 순서 지정 및 그룹화 기능을 제공합니다. 또한 LINQ 쿼리를 통해 성능을 향상시킬 수도 있습니다.

## <a name="related-topics"></a>관련 항목

제목 | 설명
----- | -----------
[`Collections and Data Structures`](index.md) | 스택, 큐, 목록, 배열 및 사전을 포함하여 .NET Framework에서 사용 가능한 다양한 컬렉션 형식을 설명합니다.
[`Hashtable and Dictionary Collection Types`](hashtable-and-dictionary-collection-types.md) | 제네릭 및 제네릭이 아닌 해시 기반 사전 형식의 기능을 설명합니다.
[`Sorted Collection Types`](sorted-collection-types.md) | 정렬된 컬렉션의 성능과 특징에 대해 설명합니다.

## <a name="reference"></a>참조

[`System.Collections`](https://docs.microsoft.com/dotnet/core/api/System.Collections)

[`System.Collections.Generic`](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic)

[`System.Collections.ICollection`](https://docs.microsoft.com/dotnet/core/api/System.Collections.ICollection)

[`System.Collections.Generic.ICollection<T>`](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.ICollection-1)

[`System.Collections.IList`](https://docs.microsoft.com/dotnet/core/api/System.Collections.IList)

[`System.Collections.Generic.IList<T>`](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.IList-1)

[`System.Collections.IDictionary`](https://docs.microsoft.com/dotnet/core/api/System.Collections.IDictionary)

[`System.Collections.Generic.IDictionary<TKey, TValue>`](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.IDictionary-2)

[`System.Linq`](https://docs.microsoft.com/dotnet/core/api/System.Linq)



<!--HONumber=Nov16_HO3-->


