---
title: "Hashtable 및 Dictionary 컬렉션 형식"
description: "Hashtable 및 Dictionary 컬렉션 형식"
keywords: .NET, .NET Core
author: mairaw
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 0f18fac7-fd0d-4f25-a046-1d3d51de062e
translationtype: Human Translation
ms.sourcegitcommit: e07788926a995b41571be276379ad9285747951d
ms.openlocfilehash: 373948733acc883a3fdc04d8dfc34f66435362af

---

# <a name="hashtable-and-dictionary-collection-types"></a>Hashtable 및 Dictionary 컬렉션 형식

[System.Collections.Hashtable](https://docs.microsoft.com/dotnet/core/api/System.Collections.Hashtable) 클래스와 [System.Collections.Generic.Dictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.Dictionary-2) 및 [System.Collections.Concurrent.ConcurrentDictionary<T>](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentDictionary-2) 제네릭 클래스는 [System.Collections.IDictionary](https://docs.microsoft.com/dotnet/core/api/System.Collections.IDictionary) 인터페이스를 구현합니다. 또한 `Dictionary<T>` 제네릭 클래스는 [IDictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.IDictionary-2) 제네릭 인터페이스를 구현합니다. 따라서 이러한 컬렉션의 각 요소는 한 쌍의 키-값입니다.

`Hashtable` 개체는 컬렉션 요소를 포함하는 버킷으로 구성됩니다. 버킷은 `Hashtable` 내 요소의 가상 하위 그룹으로, 대부분의 컬렉션보다 더 쉽고 빠르게 검색하고 가져올 수 있게 해줍니다. 각 버킷은 해시 함수를 사용하여 생성되고 요소의 키를 기반으로 하는 해시 코드와 연결됩니다.

제네릭 [HashSet&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.HashSet-1) 클래스는 고유 요소를 포함하는 순서가 지정되지 않은 컬렉션입니다. 

해시 함수는 키를 기준으로 숫자 해시 코드를 반환하는 알고리즘입니다. 키는 저장되는 개체의 일부 속성 값입니다. 해시 함수는 동일한 키에 대해 항상 동일한 해시 코드를 반환해야 합니다. 해시 함수는 두 개의 서로 다른 키에 대해 동일한 해시 코드를 생성할 수 있지만 각 고유 키에 대해 고유한 해시 코드를 생성하는 해시 함수를 통해 해시 테이블에서 요소를 검색할 때 성능이 더 낫습니다.

`Hashtable`에서 요소로 사용되는 각 개체는 `GetHashCode` 메서드 구현을 사용하여 자체 해시 코드를 생성할 수 있어야 합니다. 

개체가 `Hashtable`에 추가된 경우 개체의 해시 코드와 일치하는 해시 코드와 연결된 버킷에 저장됩니다. `Hashtable`에서 값을 검색하는 경우 해당 값에 대한 해시 코드가 생성되고 해당 해시 코드와 연결된 버킷이 검색됩니다.

예를 들어 문자열에 대한 해시 함수는 문자열에 포함된 각 문자의 ASCII 코드를 받은 다음 함께 결합하여 해시 코드를 생성합니다. 문자열 "picnic"은 문자열 "basket"의 해시 코드와 다른 해시 코드를 가지므로 문자열 "picnic"과 "basket"은 다른 버킷에 있습니다. 반면, "stressed"와 "desserts"는 동일한 해시 코드를 가지므로 동일한 버킷에 있습니다.

`Dictionary<T>` 및 `ConcurrentDictionary<T>`클래스는 `Hashtable` 클래스와 동일한 기능을 갖습니다. 특정 형식(`Object` 이외)의 `Dictionary<T>`는 값 형식의 `Hashtable`보다 더 나은 성능을 제공합니다. 이는 `Hashtable`의 요소가 `Object` 형식이기 때문입니다. 따라서 boxing 및 unboxing은 일반적으로 값 형식을 저장하거나 검색할 때 발생합니다. `ConcurrentDictionary<T>`클래스는 여러 스레드가 컬렉션에 동시에 액세스할 수 있는 경우에 사용해야 합니다.

## <a name="see-also"></a>참고 항목

[Hashtable](https://docs.microsoft.com/dotnet/core/api/System.Collections.Hashtable)

[IDictionary](https://docs.microsoft.com/dotnet/core/api/System.Collections.IDictionary)

[Dictionary](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.Dictionary-2)

[System.Collections.Generic.IDictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.IDictionary-2)

[System.Collections.Concurrent.ConcurrentDictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentDictionary-2)

[일반적으로 사용되는 컬렉션 형식](commonly-used-collection-types.md)




<!--HONumber=Nov16_HO3-->


