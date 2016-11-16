---
title: "Sorted 컬렉션 형식"
description: "Sorted 컬렉션 형식"
keywords: .NET, .NET Core
author: mairaw
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: bdc9c13e-e56a-433b-a293-c92364f6e9cb
translationtype: Human Translation
ms.sourcegitcommit: 149086110d7470d97e1ab3e5969269626290b523
ms.openlocfilehash: a5f6e2ef7f765dccf1fee0e2de60dea8aec003b9

---

# <a name="sorted-collection-types"></a>Sorted 컬렉션 형식  
 
 [System.Collections.SortedList](https://docs.microsoft.com/dotnet/core/api/System.Collections.SortedList) 클래스, [System.Collections.Generic.SortedList&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.SortedList-2) 제네릭 클래스 및 [System.Collections.Generic.SortedDictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.SortedDictionary-2) 제네릭 클래스는 [Hashtable](https://docs.microsoft.com/dotnet/core/api/System.Collections.Hashtable) 클래스 및 [Dictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.Dictionary-2) 제네릭 클래스와 유사합니다. 해당 항목은 여기서 [IDictionary](https://docs.microsoft.com/dotnet/core/api/System.Collections.IDictionary) 인터페이스를 구현하지만 키를 기준으로 한 정렬 순서로 해당 요소를 유지 관리하고O(1) 삽입 및 해시 테이블의 검색 특성을 갖지 않습니다. 세 가지 클래스에는 공통적으로 다음과 같은 몇 가지 기능이 있습니다.  

 *   세 클래스는 모두 [System.Collections.IDictionary](https://docs.microsoft.com/dotnet/core/api/System.Collections.IDictionary) 인터페이스를 구현합니다. 또한 두 개의 제네릭 클래스는 [System.Collections.Generic.IDictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.IDictionary-2) 제네릭 인터페이스를 구현합니다.  
 
 *   각 요소는 열거형에 사용할 키/값 쌍입니다.   
  
> [!NOTE]  
> 제네릭 클래스가 아닌 [SortedList](https://docs.microsoft.com/dotnet/core/api/System.Collections.SortedList) 클래스는 열거될 때 [DictionaryEntry](https://docs.microsoft.com/dotnet/core/api/System.Collections.DictionaryEntry) 개체를 반환하지만 두 개의 제네릭 형식은 [KeyValuePair&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.KeyValuePair-2) 개체를 반환합니다.  
   
*   요소는 [System.Collections.IComparer](https://docs.microsoft.com/dotnet/core/api/System.Collections.IComparer) 구현(제네릭 클래스가 아닌 `SortedList`의 경우) 또는 [System.Collections.Generic.IComparer&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.IComparer-1) 구현(두 개의 제네릭 클래스의 경우)에 따라 정렬됩니다.  
   
 *   각 클래스는 키만 포함하거나 값만 포함하는 컬렉션을 반환하는 속성을 제공합니다.  
   
다음 테이블에서는 두 개의 정렬된 목록 클래스 [SortedDictionary<TKey, TValue>](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.SortedDictionary-2) 클래스 간의 차이점을 나열합니다.  
   
 `SortedList` 제네릭이 아닌 클래스 및 `SortedList<TKey, TValue>` 제네릭 클래스 | `SortedDictionary<TKey, TValue>` 제네릭 클래스  
 --------------------------------------------------------------------------------- | ------------------------------  
 키와 값을 반환하는 속성은 인덱스되어 인덱싱된 효율적인 검색을 허용합니다. | 인덱스된 검색이 없습니다.  
 검색은 O(로그 n)입니다. | 검색은 O(로그 n)입니다.  
 삽입 및 제거는 일반적으로 O(n)이지만 정렬 순서로 나열된 데이터의 경우 삽입은 O(1)이므로 각 요소가 목록 끝에 추가됩니다. (여기서는 크기 조정이 필요하지 않다고 가정합니다.) | 삽입과 제거는 O(로그 n)입니다.  
 `SortedDictionary<TKey, TValue>`보다 적은 메모리를 사용합니다. | `SortedList` 제네릭이 아닌 클래스와 `SortedList<TKey, TValue>` 제네릭 클래스보다 더 많은 메모리를 사용합니다.  
  
 여러 스레드에서 동시에 액세스할 수 있어야 하는 정렬된 목록 또는 사전의 경우 정렬 논리를 [ConcurrentDictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentDictionary-2)에서 파생된 클래스에 추가할 수 있습니다.  
  
 > [!NOTE]  
 > 고유한 키를 포함하는 값(예: 직원 ID 번호를 포함하는 직원 레코드)의 경우 [KeyedCollection&lt;TKey, TItem&gt;]() 제네릭 클래스에서 파생하여 목록의 일부 특성 및 사전의 일부 특성을 가진 키가 지정된 컬렉션을 만들 수 있습니다.  
   
 [SortedSet&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.SortedSet-1) 클래스는 삽입, 삭제 및 검색 후에 정렬된 순서에 따라 데이터를 유지 관리하는 자체 균형 조정 트리를 제공합니다. 이 클래스와 [HashSet&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.HashSet-1) 클래스는 [ISet&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.ISet-1) 인터페이스를 구현합니다.  
   
## <a name="see-also"></a>참고 항목  
  
[System.Collections.IDictionary](https://docs.microsoft.com/dotnet/core/api/System.Collections.IDictionary)  
   
[System.Collections.Generic.IDictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.IDictionary-2)  
   
[ConcurrentDictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentDictionary-2)  
 
[일반적으로 사용되는 컬렉션 형식](commonly-used-collection-types.md) 



<!--HONumber=Nov16_HO1-->


