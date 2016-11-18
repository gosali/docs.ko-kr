---
title: "Collection 클래스 선택"
description: "Collection 클래스 선택"
keywords: .NET, .NET Core
author: mairaw
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 0a60fca7-e082-48d4-9dda-30b0d3e67ec7
translationtype: Human Translation
ms.sourcegitcommit: cfe65fcba1b3fdc09ffcac704a760d8ce29ea60b
ms.openlocfilehash: 38f5a970738103bd96c9570f4d6e8ee540af6ee1

---

# <a name="selecting-a-collection-class"></a>Collection 클래스 선택

컬렉션 클래스를 신중하게 선택해야 합니다. 잘못된 형식을 사용하면 컬렉션 사용이 제한될 수 있습니다. 형식 안전성이 더 크고 기타 향상된 기능이 있는 제네릭 버전과 동시 버전의 컬렉션을 사용하는 것이 좋습니다. 일반적으로, .NET Framework 버전 1.1을 대상으로 하지 않는 한 System.Collections 네임스페이스의 형식을 사용하지 마세요. 

다음 질문을 살펴보세요.

* 값을 검색한 후 요소가 일반적으로 삭제되는 순차적 목록이 필요한가요? 

    * 예인 경우 FIFO(선입선출) 동작이 필요하면 [System.Collections.Generic.Queue&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.Queue-1) 제네릭 클래스를 사용합니다. LIFO(후입선출) 동작이 필요하면 [System.Collections.Generic.Stack&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.Stack-1) 제네릭 클래스를 사용합니다. 여러 스레드에서 안전하게 액세스하려면 동시 버전 [System.Collections.Concurrent.ConcurrentQueue&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentQueue-1) 및 [System.Collections.Concurrent.ConcurrentStack&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentStack-1)을 사용합니다.
    
    * 그러지 않은 경우 다른 컬렉션을 사용합니다.
    
* FIFO, LIFO, 무작위와 같은 특정 순서대로 요소에 액세스해야 하나요?

    * [System.Collections.Generic.Queue&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.Queue-1) 또는 [System.Collections.Concurrent.ConcurrentQueue&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentQueue-1) 제네릭 클래스는 FIFO 액세스를 제공합니다. 자세한 내용은 [스레드로부터 안전한 컬렉션 사용 시기](threadsafe/when-to-use-a-thread-safe-collection.md)를 참조하세요.
    
    * [System.Collections.Generic.Stack&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.Stack-1) 또는 [System.Collections.Concurrent.ConcurrentStack&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentStack-1) 제네릭 클래스는 LIFO 액세스를 제공합니다. 자세한 내용은 [스레드로부터 안전한 컬렉션 사용 시기](threadsafe/when-to-use-a-thread-safe-collection.md)를 참조하세요.
    
    * [System.Collections.Generic.LinkedList&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.LinkedList-1) 제네릭 클래스는 헤드에서 테일로 또는 테일에서 헤드로 순차적 액세스를 허용합니다.
    
* 인덱스를 기준으로 각 요소에 액세스해야 하나요? 

    * [System.Collections.Specialized.StringCollection](https://docs.microsoft.com/dotnet/core/api/System.Collections.Specialized.StringCollection) 클래스 및 [System.Collections.Generic.List&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.List-1) 제네릭 클래스는 요소의 0 기반 인덱스를 기준으로 해당 요소에 액세스를 제공합니다. 
    
    * [System.Collections.Specialized.ListDictionary](https://docs.microsoft.com/dotnet/core/api/System.Collections.Specialized.ListDictionary) 및 [System.Collections.Specialized.StringDictionary](https://docs.microsoft.com/dotnet/core/api/System.Collections.Specialized.StringDictionary) 클래스와 [System.Collections.Generic.Dictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.Dictionary-2) 및 [System.Collections.Generic.SortedDictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.SortedDictionary-2) 제네릭 클래스는 요소의 키를 기준으로 해당 요소에 대한 액세스를 제공합니다.
    
    * [System.Collections.Specialized.NameObjectCollectionBase](https://docs.microsoft.com/dotnet/core/api/System.Collections.Specialized.NameObjectCollectionBase) 및 [System.Collections.Specialized.NameValueCollection](https://docs.microsoft.com/dotnet/core/api/System.Collections.Specialized.NameValueCollection) 클래스와 [System.Collections.ObjectModel.KeyedCollection&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.ObjectModel.KeyedCollection-2) 및 [System.Collections.Generic.SortedList&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.SortedList-2) 제네릭 클래스는 0 기반 인덱스 또는 요소의 키를 기준으로 해당 요소에 대한 액세스를 제공합니다.
    
* 각 요소에 값 한 개, 키 한 개와 값 한 개의 조합 또는 키 한 개와 여러 값의 조합이 포함되나요? 

    * 값 한 개: [System.Collections.Generic.IList&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.IList-1) 제네릭 인터페이스를 기반으로 컬렉션을 사용합니다.
    
    * 키 한 개와 값 한 개: [System.Collections.Generic.IDictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.IDictionary-2) 제네릭 인터페이스를 기반으로 컬렉션을 사용합니다.
    
    * 포함된 키가 있는 값 한 개: [System.Collections.ObjectModel.KeyedCollection&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.ObjectModel.KeyedCollection-2) 제네릭 클래스를 사용합니다.
    
    * 키 한 개와 다중 값: [System.Collections.Specialized.NameValueCollection](https://docs.microsoft.com/dotnet/core/api/System.Collections.Specialized.NameValueCollection) 클래스를 사용입니다.
    
* 입력된 순서와 다르게 요소를 정렬해야 하나요? 

    * [System.Collections.Hashtable](https://docs.microsoft.com/dotnet/core/api/System.Collections.Hashtable) 클래스는 해시 코드를 기준으로 해당 요소를 정렬합니다.
    
    * [System.Collections.Generic.SortedDictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.SortedDictionary-2) 및 [System.Collections.Generic.SortedList&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.SortedList-2) 제네릭 클래스는 [System.Collections.IComparer](https://docs.microsoft.com/dotnet/core/api/System.Collections.IComparer) 인터페이스 및 [System.Collections.Generic.IComparer&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.IComparer-1) 제네릭 인터페이스의 구현을 기반으로 키에서 해당 요소를 정렬합니다.
    
    * [System.Collections.Generic.List&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.List-1) 제네릭 클래스는 `IComparer<T>` 제네릭 인터페이스의 구현을 매개 변수로 사용하는 `Sort` 메서드를 제공합니다.
    
* 문자열만 수락하는 컬렉션이 필요한가요? 

    * [StringCollection](https://docs.microsoft.com/dotnet/core/api/System.Collections.Specialized.StringCollection)([System.Collections.IList](https://docs.microsoft.com/dotnet/core/api/System.Collections.IList) 기반) 및 [StringDictionary](https://docs.microsoft.com/dotnet/core/api/System.Collections.Specialized.StringDictionary)([System.Collections.IDictionary](https://docs.microsoft.com/dotnet/core/api/System.Collections.IDictionary) 기반)는 [System.Collections.Specialized](https://docs.microsoft.com/dotnet/core/api/System.Collections.Specialized) 네임스페이스에 위치합니다. 
    
    * 또한 제네릭 형식 인수에 대해 `String` 클래스를 지정하여 [System.Collections.Generic](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic) 네임스페이스의 제네릭 컬렉션 클래스 중 하나를 강력한 형식의 문자열 컬렉션으로 사용할 수 있습니다.
    
## <a name="linq-to-objects"></a>LINQ to Objects

개발자가 LINQ to Objects를 사용하면 개체 형식이 [System.Collections.IEnumerable](https://docs.microsoft.com/dotnet/core/api/System.Collections.IEnumerable) 또는 [System.Collections.Generic.IEnumerable&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.IEnumerable-1)을 구현하는 경우 LINQ 쿼리를 통해 메모리 내 개체에 액세스할 수 있습니다. LINQ 쿼리는 데이터 액세스를 위한 일반 패턴을 제공하고, 일반적으로 표준 foreach 루프에 비해 간결하고 쉽게 읽을 수 있으며, 필터링, 순서 지정 및 그룹화 기능을 제공합니다. 자세한 내용은 [LINQ(언어 통합 쿼리)](../../csharp/linq.md)를 참조하세요.

## <a name="see-also"></a>참고 항목

[System.Collections](https://docs.microsoft.com/dotnet/core/api/System.Collections)

[System.Collections.Specialized](https://docs.microsoft.com/dotnet/core/api/System.Collections.Specialized)

[System.Collections.Generic](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic)

[스레드로부터 안전한 컬렉션](threadsafe/index.md)



<!--HONumber=Nov16_HO3-->


