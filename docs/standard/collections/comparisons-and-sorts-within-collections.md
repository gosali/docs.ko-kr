---
title: "컬렉션 내에서 비교 및 정렬"
description: "컬렉션 내에서 비교 및 정렬"
keywords: .NET, .NET Core
author: mairaw
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: c7b7c005-628d-427a-91ad-af0c3958c00e
translationtype: Human Translation
ms.sourcegitcommit: e07788926a995b41571be276379ad9285747951d
ms.openlocfilehash: bad7fd4e9cda1976a31f287d7c95d81d113a30fa

---

# <a name="comparisons-and-sorts-within-collections"></a>컬렉션 내에서 비교 및 정렬

[System.Collections](https://docs.microsoft.com/dotnet/core/api/System.Collections) 클래스는 제거할 요소 검색, 키-값 쌍의 값 반환 등 컬렉션 관리와 관련된 거의 모든 프로세스에서 비교 연산을 수행합니다.

컬렉션은 일반적으로 같음 비교자 및/또는 순서 비교자를 사용합니다. 비교에는 두 가지 구문이 사용됩니다. 

## <a name="checking-for-equality"></a>같음 확인

`Contains`, `IndexOf`, `LastIndexOf`, `Remove` 등의 메서드는 컬렉션 요소에 대해 같음 비교자를 사용합니다. 제네릭 컬렉션의 경우 다음 지침에 따라 동일한 항목인지를 비교합니다.

*   T 형식이 [IEquatable&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.IEquatable-1) 제네릭 인터페이스를 구현하는 경우 같음 비교자는 해당 인터페이스의 `Equals` 메서드입니다.

*   T 형식이 `IEquatable<T>`을 구현하지 않는 경우에는 `Object.Equals`가 사용됩니다.

또한 사전 컬렉션의 일부 생성자 오버로드는 같은 키인지를 비교하는 데 사용되는 [IEqualityComparer&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.IEqualityComparer-1) 구현을 허용합니다.

## <a name="determining-sort-order"></a>정렬 순서 결정

`BinarySearch` 및 `Sort`와 같은 메서드는 컬렉션 요소에 대해 순서 비교자를 사용합니다. 컬렉션의 요소를 비교할 수도 있고 특정 요소와 지정된 값을 비교할 수도 있습니다. 개체를 비교하는 경우 기본 비교자 및 명시적 비교자의 개념이 적용됩니다. 

기본 비교자는 비교 대상 개체 중 하나 이상을 사용하여 `IComparable` 인터페이스를 구현합니다. 사용되는 모든 클래스에서 목록 컬렉션의 값 또는 사전 컬렉션의 키로서 `IComparable`을 구현하는 것이 좋습니다. 제네릭 컬렉션의 경우 다음 기준에 따라 같음 비교를 결정합니다.

*   [System.IComparable&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.IComparable-1) 제네릭 인터페이스를 구현하는 T 형식인 경우 기본 비교자는 해당 인터페이스의 `CompareTo(T)` 메서드입니다.

*   제네릭이 아닌 [System.IComparable](https://docs.microsoft.com/dotnet/core/api/System.IComparable) 인터페이스를 구현하는 T 형식인 경우 기본 비교자는 해당 인터페이스의 `CompareTo`(개체) 메서드입니다.

*   T 형식이 두 인터페이스를 모두 구현하지 않는 경우에는 기본 비교자가 없으며 비교자 또는 비교 대리자를 명시적으로 제공해야 합니다.

일부 메서드에는 명시적 비교를 제공하기 위해 매개 변수로서 `IComparer` 구현이 허용됩니다. 예를 들어는 `List<T>.Sort` 메서드에 [System.Collections.Generic.IComparer&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.IComparer-1) 구현이 허용됩니다. 

## <a name="equality-and-sort-example"></a>같음 및 정렬 예제

다음 코드에서는 [IEquatable&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.IEquatable-1) 및[IComparable&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.IComparable-1)를 간단한 비즈니스 개체에 구현합니다. 또한 개체를 목록에 저장하고 정렬할 때 `Sort()` 메서드를 호출하면 'Part' 형식의 기본 비교자가 사용되며, 무명 메서드를 사용하여 `Sort(Comparison<T>)` 메서드가 구현됩니다.

C#

```csharp
using System;
using System.Collections.Generic;
// Simple business object. A PartId is used to identify the type of part 
// but the part name can change. 
public class Part : IEquatable<Part> , IComparable<Part>
{
    public string PartName { get; set; }

    public int PartId { get; set; }

    public override string ToString()
    {
        return "ID: " + PartId + "   Name: " + PartName;
    }
    public override bool Equals(object obj)
    {
        if (obj == null) return false;
        Part objAsPart = obj as Part;
        if (objAsPart == null) return false;
        else return Equals(objAsPart);
    }
    public int SortByNameAscending(string name1, string name2)
    {

        return name1.CompareTo(name2);
    }

    // Default comparer for Part type.
    public int CompareTo(Part comparePart)
    {
          // A null value means that this object is greater.
        if (comparePart == null)
            return 1;

        else
            return this.PartId.CompareTo(comparePart.PartId);
    }
    public override int GetHashCode()
    {
        return PartId;
    }
    public bool Equals(Part other)
    {
        if (other == null) return false;
        return (this.PartId.Equals(other.PartId));
    }
    // Should also override == and != operators.

}
public class Example
{
    public static void Main()
    {
        // Create a list of parts.
        List<Part> parts = new List<Part>();

        // Add parts to the list.
        parts.Add(new Part() { PartName = "regular seat", PartId = 1434 });
        parts.Add(new Part() { PartName= "crank arm", PartId = 1234 });
        parts.Add(new Part() { PartName = "shift lever", PartId = 1634 }); ;
        // Name intentionally left null.
        parts.Add(new Part() {  PartId = 1334 });
        parts.Add(new Part() { PartName = "banana seat", PartId = 1444 });
        parts.Add(new Part() { PartName = "cassette", PartId = 1534 });


        // Write out the parts in the list. This will call the overridden 
        // ToString method in the Part class.
        Console.WriteLine("\nBefore sort:");
        foreach (Part aPart in parts)
        {
            Console.WriteLine(aPart);
        }


        // Call Sort on the list. This will use the 
        // default comparer, which is the Compare method 
        // implemented on Part.
        parts.Sort();


        Console.WriteLine("\nAfter sort by part number:");
        foreach (Part aPart in parts)
        {
            Console.WriteLine(aPart);
        }

        // This shows calling the Sort(Comparison(T) overload using 
        // an anonymous method for the Comparison delegate. 
        // This method treats null as the lesser of two values.
        parts.Sort(delegate(Part x, Part y)
        {
            if (x.PartName == null && y.PartName == null) return 0;
            else if (x.PartName == null) return -1;
            else if (y.PartName == null) return 1;
            else return x.PartName.CompareTo(y.PartName);
        });

        Console.WriteLine("\nAfter sort by name:");
        foreach (Part aPart in parts)
        {
            Console.WriteLine(aPart);
        }

        /*

            Before sort:
        ID: 1434   Name: regular seat
        ID: 1234   Name: crank arm
        ID: 1634   Name: shift lever
        ID: 1334   Name:
        ID: 1444   Name: banana seat
        ID: 1534   Name: cassette

        After sort by part number:
        ID: 1234   Name: crank arm
        ID: 1334   Name:
        ID: 1434   Name: regular seat
        ID: 1444   Name: banana seat
        ID: 1534   Name: cassette
        ID: 1634   Name: shift lever

        After sort by name:
        ID: 1334   Name:
        ID: 1444   Name: banana seat
        ID: 1534   Name: cassette
        ID: 1234   Name: crank arm
        ID: 1434   Name: regular seat
        ID: 1634   Name: shift lever

         */

    }
}
```

## <a name="see-also"></a>참고 항목

[IComparer](https://docs.microsoft.com/dotnet/core/api/System.Collections.IComparer)

[IEquatable&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.IEquatable-1)

[IComparer&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.IComparer-1)

[IComparable](https://docs.microsoft.com/dotnet/core/api/System.IComparable)

[IComparable&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.IComparable-1)



<!--HONumber=Nov16_HO3-->


