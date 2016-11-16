---
title: "심층 공용 형식 시스템"
description: "심층 공용 형식 시스템"
keywords: .NET, .NET Core
author: stevehoag
ms.author: shoag
manager: wpickett
ms.date: 07/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: b5482a1d-7bdc-40fe-aa45-10df930ceb5b
translationtype: Human Translation
ms.sourcegitcommit: b20713600d7c3ddc31be5885733a1e8910ede8c6
ms.openlocfilehash: 7d7f869b07d7cf00ffa69da117aa199d1b6e8f20

---

# <a name="common-type-system-in-depth"></a>심층 공용 형식 시스템

이 항목은 공용 형식 시스템을 자세히 설명하는 다음 섹션으로 이루어져 있습니다.

* [.NET의 형식](#types-in-net)

* [형식 정의](#type-definitions)

* [형식 멤버](#type-members)

* [형식 멤버의 특성](#characteristics-of-type-members)


## <a name="types-in-net"></a>.NET의 형식

.NET의 모든 형식은 값 형식 또는 참조 형식입니다. 

값 형식은 해당 형식의 개체가 개체의 실제 값으로 나타나는 데이터 형식입니다. 값 형식의 인스턴스가 변수에 할당되면 해당 변수에는 값의 새 사본이 부여됩니다.

참조 형식은 해당 형식의 개체가 개체의 실제 값에 대한 참조(포인터와 유사)로 나타나는 데이터 형식입니다. 참조 형식이 변수에 할당되면 해당 변수는 원래 값을 참조하거나 가리키며 복사는 수행되지 않습니다. 

.NET의 공용 형식 시스템에서는 다음과 같은 다섯 가지 범주의 형식을 지원합니다.

* [클래스](#classes)

* [구조체](#structures)

* [열거형](#enumerations)

* [인터페이스](#interfaces)

* [대리자](#delegates)

### <a name="classes"></a>클래스

클래스는 다른 클래스에서 직접 파생될 수 있거나 [System.Object](xref:System.Object)에서 암시적으로 파생되는 참조 형식입니다. 클래스는 개체(클래스의 인스턴스)가 수행할 수 있는 작업(메서드, 이벤트 또는 속성)과 개체에 포함된 데이터(필드)를 정의합니다. 구현 없이 정의만이 포함된 인터페이스와는 달리, 클래스에는 대개 정의와 구현이 모두 포함되어 있지만 구현이 없는 멤버가 하나 이상 있을 수도 있습니다.

다음 표에는 클래스가 가질 수 있는 일부 특성에 대한 설명이 나와 있습니다. 런타임을 지원하는 각 언어에는 클래스나 클래스 멤버가 이들 특성 중 하나 이상을 갖고 있음을 표시하는 방식이 있습니다. 그러나 .NET을 대상으로 하는 개별 프로그래밍 언어에서 이 특성들을 모두 사용할 수 있는 것은 아닙니다.

특성 | 설명
-------------- | -----------
sealed | 이 형식에서 다른 클래스를 파생할 수 없도록 지정합니다.
구현 | 인터페이스 멤버의 구현을 제공하여 클래스에서 하나 이상의 인터페이스를 사용할 수 있음을 나타냅니다.
abstract | 클래스를 인스턴스화할 수 없음을 나타냅니다. 이러한 클래스를 사용하려면 다른 클래스를 파생해야 합니다.
상속 | 기본 클래스를 지정한 곳에서 클래스의 인스턴스를 사용할 수 있음을 나타냅니다. 기본 클래스에서 상속된 파생 클래스에서는 기본 클래스에서 제공하는 공용 멤버의 구현을 사용할 수도 있고, 파생 클래스의 고유한 구현을 사용하여 공용 멤버의 구현을 재정의할 수도 있습니다.
exported 또는 not exported | 클래스를 정의한 어셈블리 밖에서 클래스를 볼 수 있는지의 여부를 지정하며 이 특성은 중첩 클래스에는 적용되지 않고 최상위 클래스에만 적용됩니다.

> [!NOTE]
> 클래스는 부모 클래스 또는 구조체 내에 중첩될 수도 있습니다. 중첩된 클래스에도 멤버 특성이 있습니다. 자세한 내용은 [중첩 형식](#nested-types)을 참조하세요.

구현이 없는 클래스 멤버는 추상 멤버입니다. 하나 이상의 멤버가 있는 클래스는 그 자체가 추상적이기 때문에 이 클래스의 새 인스턴스를 만들 수 없습니다. 런타임을 대상으로 하는 일부 언어에서는 추상 멤버가 없는 클래스를 추상으로 표시할 수 있습니다. 파생 클래스에서 상속하거나 재정의할 수 있는 기본 기능의 집합을 캡슐화하려는 경우 상황에 따라 추상 클래스를 사용할 수 있습니다. 추상이 아닌 클래스를 구체적인 클래스라고 합니다.

클래스는 인터페이스를 여러 개 구현할 수 있지만 모든 클래스가 암시적으로 상속받는 [System.Object](xref:System.Object) 외에 하나의 기본 클래스에서만 상속받을 수 있습니다. 모든 클래스에는 클래스의 새 인스턴스를 초기화하는 생성자가 최소한 하나는 있어야 합니다. 생성자를 명시적으로 정의하지 않으면 대부분의 컴파일러는 자동으로 매개 변수가 없는 기본 생성자를 제공합니다.

### <a name="structures"></a>구조체

구조체는 [System.Object](xref:System.Object)에서 파생된 [System.ValueType](xref:System.ValueType)에서 다시 암시적으로 파생되는 값 형식입니다. 구조체는 적은 메모리를 요구하는 값을 나타낼 때 유용할 뿐 아니라 강력한 형식의 매개 변수를 갖는 메서드에 값으로 전달되는 매개 변수로 값을 전달할 때도 유용합니다. .NET의 모든 기본 데이터 형식([Boolean](xref:System.Boolean), [Byte](xref:System.Byte), [Char](xref:System.Char), [DateTime](xref:System.DateTime), [Decimal](xref:System.Decimal), [Double](xref:System.Double), [Int16](xref:System.Int16), [Int32](xref:System.Int32), [Int64](xref:System.Int64), [SByte](xref:System.SByte), [Single](xref:System.Single), [UInt16](xref:System.UInt16), [UInt32](xref:System.UInt32) 및 [UInt64](xref:System.UInt64))은 구조체로 정의됩니다.

클래스와 마찬가지로 구조체는 데이터(구조체의 필드)와 해당 데이터에 대해 수행할 수 있는 작업(구조체의 메서드)을 정의합니다. 이는 [System.Object](xref:System.Object) 및 [System.ValueType](xref:System.ValueType) 클래스에 정의된 가상 메서드와 값 형식 자체에 정의된 메서드를 비롯하여 구조체에서 메서드를 호출할 수 있음을 의미합니다. 다시 말하면 구조체에는 필드, 속성, 이벤트뿐 아니라 정적 및 비정적 메서드가 포함될 수 있습니다. 구조체의 인스턴스를 만들어 매개 변수로 전달하고 지역 변수로 저장하거나 다른 값 형식 또는 참조 형식의 필드에 저장할 수 있습니다. 구조체는 인터페이스를 구현할 수도 있습니다.

또한 값 형식이 몇 가지 측면에서 클래스와 다릅니다. 먼저, 값 형식은 [System.ValueType](xref:System.ValueType)에서 암시적으로 상속되긴 하지만 형식에서 직접 상속받을 수는 없습니다. 마찬가지로 모든 값 형식은 봉인되어 있으므로 이 형식에서 다른 형식을 파생할 수 없습니다. 값 형식에는 생성자도 필요하지 않습니다.

공용 언어 런타임에서는 각 값 형식에 대해 해당 boxed 형식 클래스를 제공합니다. 이 클래스는 값 형식과 동일한 상태 및 동작을 가집니다. 값 형식의 인스턴스가 [System.Object](xref:System.Object) 형식의 매개 변수를 허용하는 메서드에 전달될 때는 boxing됩니다. 하지만 참조로 전달되는 매개 변수로 값 형식을 허용하는 메서드 호출에서 컨트롤이 반환될 때는 이 인스턴스가 unboxing됩니다. 즉, 클래스의 인스턴스에서 값 형식의 인스턴스로 다시 변환됩니다. 일부 언어에서는 boxed 형식이 필요할 때 별도의 구문을 사용해야 하지만 그 밖의 언어에서는 필요할 때 자동으로 boxed 형식을 사용합니다. 값 형식을 정의하면 boxed 형식과 unboxed 형식이 모두 정의됩니다.

### <a name="enumerations"></a>열거형

열거형(enum)은 [System.Enum](xref:System.Enum)에서 직접 상속받고 내부 기본 형식의 값에 대한 대체 이름을 제공하는 값 형식입니다. 열거형 형식은 이름, 내부 형식 및 필드 집합으로 구성됩니다. 내부 형식은 기본으로 제공되는 부호 있는 정수나 부호 없는 정수 형식(예: [Byte](xref:System.Byte), [Int32](xref:System.Int32) 또는 [UInt64](xref:System.UInt64)) 중 하나여야 합니다. 각 필드는 상수를 나타내는 정적 리터럴 필드입니다. 여러 필드에 동일한 값을 할당할 수 있습니다. 이 경우 리플렉션 및 문자열 변환을 위해 값 중 하나를 기본 열거형 값으로 표시해야 합니다.

내부 형식의 값을 열거형에 할당하거나 반대로도 할당할 수 있으며 이때 런타임에서 캐스팅할 필요가 없습니다. 열거형의 인스턴스를 만들고 열거형의 내부 형식에 정의된 메서드뿐 아니라 [System.Enum](xref:System.Enum)의 메서드도 호출할 수 있습니다. 그러나 일부 언어에서는 내부 형식의 인스턴스가 필요한 경우 열거형을 매개 변수로 전달할 수 없고 그 반대로도 전달할 수 없습니다.

다음은 열거형에 적용되는 추가 제약 조건입니다. 

* 고유의 메서드를 정의할 수 없습니다.

* 인터페이스를 구현할 수 없습니다.

* 속성 또는 이벤트를 정의할 수 없습니다.

* 열거형은 제네릭 형식 내에 중첩되어 있으므로 단독 제네릭이 아니면 제네릭이 될 수 없습니다. 즉, 열거형에는 자체의 형식 매개 변수를 사용할 수 없습니다. 

> [!NOTE]
> C#으로 만든 중첩 형식(열거형 포함)은 모든 바깥쪽 제네릭 형식의 형식 매개 변수를 포함하므로 자체의 형식 매개 변수가 없더라도 제네릭입니다. 자세한 내용은 [Type.MakeGenericType](xref:System.Type.MakeGenericType(System.Type[])) 참조 항목을 참조하세요. 

[FlagsAttribute](xref:System.FlagsAttribute) 특성은 비트 필드라는 특수한 열거형을 나타냅니다. 런타임 자체에서는 기존의 열거형과 비트 필드를 구분하지 않지만 언어에 따라서는 이를 구분할 수도 있습니다. 이를 구분할 경우 열거형이 아니라 비트 필드에 비트 연산자를 적용하여 명명되지 않은 값을 생성할 수 있습니다. 일반적으로 열거형은 요일, 국가 또는 지역 이름 등과 같은 고유한 요소의 목록에 사용하고, 비트 필드는 `Red And Big And Fast` 같은 조합에서 나타날 수 있는 특성 및 수량 목록에 사용합니다.

다음 예제에서는 비트 필드와 기존의 열거형을 둘 다 사용하는 방법을 보여 줍니다.

```csharp
using System;
using System.Collections.Generic;

// A traditional enumeration of some root vegetables.
public enum SomeRootVegetables
{
    HorseRadish,
    Radish,
    Turnip
}

// A bit field or flag enumeration of harvesting seasons.
[Flags]
public enum Seasons
{
    None = 0,
    Summer = 1,
    Autumn = 2,
    Winter = 4,
    Spring = 8,
    All = Summer | Autumn | Winter | Spring
}

public class Example
{
   public static void Main()
   {
       // Hash table of when vegetables are available.
       Dictionary<SomeRootVegetables, Seasons> AvailableIn = new Dictionary<SomeRootVegetables, Seasons>();

       AvailableIn[SomeRootVegetables.HorseRadish] = Seasons.All;
       AvailableIn[SomeRootVegetables.Radish] = Seasons.Spring;
       AvailableIn[SomeRootVegetables.Turnip] = Seasons.Spring | 
            Seasons.Autumn;

       // Array of the seasons, using the enumeration.
       Seasons[] theSeasons = new Seasons[] { Seasons.Summer, Seasons.Autumn, 
            Seasons.Winter, Seasons.Spring };

       // Print information of what vegetables are available each season.
       foreach (Seasons season in theSeasons)
       {
          Console.Write(String.Format(
              "The following root vegetables are harvested in {0}:\n", 
              season.ToString("G")));
          foreach (KeyValuePair<SomeRootVegetables, Seasons> item in AvailableIn)
          {
             // A bitwise comparison.
             if (((Seasons)item.Value & season) > 0)
                 Console.Write(String.Format("  {0:G}\n", 
                      (SomeRootVegetables)item.Key));
          }
       }
   }
}
// The example displays the following output:
//    The following root vegetables are harvested in Summer:
//      HorseRadish
//    The following root vegetables are harvested in Autumn:
//      Turnip
//      HorseRadish
//    The following root vegetables are harvested in Winter:
//      HorseRadish
//    The following root vegetables are harvested in Spring:
//      Turnip
//      Radish
//      HorseRadish
```

```vb
Imports System.Collections.Generic

' A traditional enumeration of some root vegetables.
Public Enum SomeRootVegetables
   HorseRadish
   Radish
   Turnip
End Enum 

' A bit field or flag enumeration of harvesting seasons.
<Flags()> Public Enum Seasons
   None = 0
   Summer = 1
   Autumn = 2
   Winter = 4
   Spring = 8
   All = Summer Or Autumn Or Winter Or Spring
End Enum 

' Entry point.
Public Class Example
   Public Shared Sub Main()
      ' Hash table of when vegetables are available.
      Dim AvailableIn As New Dictionary(Of SomeRootVegetables, Seasons)()

      AvailableIn(SomeRootVegetables.HorseRadish) = Seasons.All
      AvailableIn(SomeRootVegetables.Radish) = Seasons.Spring
      AvailableIn(SomeRootVegetables.Turnip) = Seasons.Spring Or _
                                               Seasons.Autumn

      ' Array of the seasons, using the enumeration.
      Dim theSeasons() As Seasons = {Seasons.Summer, Seasons.Autumn, _
                                     Seasons.Winter, Seasons.Spring}

      ' Print information of what vegetables are available each season.
      For Each season As Seasons In theSeasons
         Console.WriteLine(String.Format( _
              "The following root vegetables are harvested in {0}:", _
              season.ToString("G")))
         For Each item As KeyValuePair(Of SomeRootVegetables, Seasons) In AvailableIn
            ' A bitwise comparison.
            If(CType(item.Value, Seasons) And season) > 0 Then
               Console.WriteLine("  " + _
                     CType(item.Key, SomeRootVegetables).ToString("G"))
            End If
         Next
      Next
   End Sub 
End Class 
' The example displays the following output:
'    The following root vegetables are harvested in Summer:
'      HorseRadish
'    The following root vegetables are harvested in Autumn:
'      Turnip
'      HorseRadish
'    The following root vegetables are harvested in Winter:
'      HorseRadish
'    The following root vegetables are harvested in Spring:
'      Turnip
'      Radish
'      HorseRadish
```

### <a name="interfaces"></a>인터페이스

인터페이스는 "실행 가능" 관계나 "소유" 관계를 지정하는 계약을 정의합니다. 인터페이스는 비교 및 정렬([IComparable](xref:System.IComparable) and [IComparable&lt;T&gt;](xref:System.IComparable%601) 인터페이스), 같은지 테스트([IEquatable&lt;T&gt;](xref:System.IEquatable%601) 인터페이스) 또는 컬렉션의 항목 열거([IEnumerable](xref:System.Collections.IEnumerable) 및 [IEnumerable&lt;T&gt;](xref:System.Collections.Generic.IEnumerable%601) 인터페이스)와 같은 기능을 구현하는 데 주로 사용됩니다. 인터페이스에는 속성, 메서드 및 이벤트가 있을 수 있으며 이러한 멤버는 모두 추상 멤버입니다. 즉, 인터페이스는 멤버와 시그니처를 정의하지만 각 인터페이스 멤버의 기능은 인터페이스를 구현하는 형식에서 정의해야 합니다. 따라서 인터페이스를 구현하는 모든 클래스나 구조체는 인터페이스에 선언된 추상 멤버에 대한 정의를 제공해야 합니다. 인터페이스를 구현하는 클래스나 구조체에서 하나 이상의 다른 인터페이스를 함께 구현해야 할 수도 있습니다.

인터페이스에는 다음과 같은 제약 조건이 따릅니다. 

* 인터페이스의 액세스 가능성은 원하는 대로 선언할 수 있지만 모든 인터페이스 멤버는 공용 액세스 가능성을 가져야 합니다.

* 인터페이스는 생성자를 정의할 수 없습니다.

* 인터페이스는 필드를 정의할 수 없습니다.

* 인터페이스에서는 인스턴스 멤버만을 정의할 수 있습니다. 정적 멤버는 정의할 수 없습니다.

둘 이상의 인터페이스에서 동일한 시그니처의 멤버를 선언할 수 있고 이들 멤버가 별도의 구현을 가질 수 있으므로 각 언어에서는 멤버를 요구하는 인터페이스에 구현을 매핑하기 위한 규칙을 제공해야 합니다.

### <a name="delegates"></a>대리자

대리자는 C++의 함수 포인터와 비슷한 목적으로 사용되는 참조 형식입니다. 대리자는 .NET의 이벤트 처리기와 콜백 함수에 사용됩니다. 함수 포인터와 달리 대리자는 안전하고, 확인할 수 있으며, 형식이 안전합니다. 대리자 형식은 호환되는 시그니처가 포함된 정적 메서드 또는 인스턴스 메서드를 나타낼 수 있습니다. 

대리자 매개 변수의 형식이 메서드 매개 변수의 형식보다 제한적인 경우 대리자의 매개 변수는 메서드의 해당 매개 변수와 호환됩니다. 이 경우 대리자로 전달된 인수를 안전하게 메서드로 전달할 수 있습니다.

마찬가지로 메서드의 반환 형식이 대리자의 반환 형식보다 제한적인 경우 대리자의 반환 형식은 메서드의 반환 형식과 호환됩니다. 이 경우 메서드의 반환 값을 안전하게 대리자의 반환 형식으로 캐스팅할 수 있습니다.

예를 들어 [IEnumerable](xref:System.Collections.IEnumerable) 형식의 매개 변수가 있고 반환 형식이 [Object](xref:System.Object)인 대리자는 [Object](xref:System.Object) 형식의 매개 변수가 있고 반환 값이 [IEnumerable](xref:System.Collections.IEnumerable) 형식인 메서드를 나타낼 수 있습니다. 

 대리자는 이 대리자가 나타내는 메서드에 바인딩됩니다. 대리자는 메서드에 바인딩될 뿐 아니라 개체에도 바인딩될 수 있습니다. 개체는 메서드의 첫 번째 매개 변수를 나타내며 대리자가 호출될 때마다 메서드에 전달됩니다. 메서드가 인스턴스 메서드이면 바인딩된 개체가 암시적 `this` 매개 변수(Visual Basic의 경우 `Me`)로 전달됩니다. 메서드가 정적이면 개체는 메서드의 첫 번째 정식 매개 변수로 전달되며 대리자 시그니처가 나머지 매개 변수와 일치해야 합니다. 
 
 모든 대리자는 [System.Delegate](xref:System.Delegate)에서 상속받는 [System.MulticastDelegate](xref:System.MulticastDelegate)에서 다시 상속받습니다. C# 및 Visual Basic 언어에서는 이러한 형식에서 상속받는 것을 허용하지 않으며, 대신 대리자를 선언하기 위한 키워드를 제공합니다.
 
 대리자는 [MulticastDelegate](xref:System.MulticastDelegate)에서 상속받기 때문에 대리자에 호출 목록이 있습니다. 이 목록에는 대리자가 나타내는 메서드와 대리자가 호출될 때 실행되는 메서드가 표시됩니다. 이 목록의 모든 메서드는 대리자가 호출될 때 제공되는 인수를 받습니다.

> [!NOTE]
> 호출 목록에 하나 이상의 메서드가 있는 대리자에 대해서는 반환 형식을 가지고 있더라도 반환 값이 정의되지 않습니다.

대부분의 경우 콜백 메서드와 마찬가지로 대리자는 하나의 메서드만 나타내며, 대리자를 만들고 호출하는 것 외에는 필요한 작업이 없습니다. 

.NET에서는 여러 메서드를 나타내는 대리자에 대해 [Delegate](xref:System.Delegate) 및 [MulticastDelegate](xref:System.MulticastDelegate) 대리자 클래스의 메서드를 제공하여 대리자 호출 목록에 메서드 추가([Delegate.Combine](xref:System.Delegate.Combine(System.Delegate,System.Delegate)) 메서드), 메서드 제거([Delegate.Remove](xref:System.Delegate.Remove(System.Delegate,System.Delegate)) 메서드), 호출 목록 가져오기([Delegate.GetInvocationList](xref:System.Delegate.GetInvocationList) 메서드) 등의 작업을 지원합니다.

> [!NOTE]
> C# 또는 Visual Basic에서는 이벤트 처리기 대리자에 대해 이러한 메서드를 사용할 필요가 없습니다. 해당 언어에서 이벤트 처리기를 추가하고 제거하기 위한 구문을 제공하기 때문입니다.

## <a name="type-definitions"></a>형식 정의입니다.

형식 정의에는 다음과 같은 요소가 포함됩니다. 

* 형식에 정의되어 있는 특성

* 형식의 액세스 가능성(표시 여부)

* 형식의 이름

* 형식의 기본 형식

* 형식에서 구현하는 인터페이스

* 형식의 멤버 각각에 대한 정의

### <a name="attributes"></a>특성

특성에서는 추가적인 사용자 정의 메타데이터를 제공합니다. 특성은 어셈블리의 형식에 대한 추가 정보를 저장하거나 디자인 타임 또는 런타임 환경에서 형식 멤버의 동작을 수정하는 데 주로 사용됩니다. 

특성은 그 자체가 [System.Attribute](xref:System.Attribute)에서 상속받는 클래스입니다. 특성 사용을 지원하는 언어 각각에는 특성을 언어 요소에 적용하기 위한 자체 구문이 있습니다. 특성은 거의 대부분의 언어 요소에 적용될 수 있으며, 특성이 적용될 수 있는 특정 요소는 해당 특성 클래스에 적용된 [AttributeUsageAttribute](xref:System.AttributeUsageAttribute)에서 정의됩니다.

### <a name="type-accessibility"></a>형식 액세스 가능성

모든 형식에는 다른 형식에서 액세스할 수 있는지를 제어하는 한정자가 있습니다. 다음 표에서는 런타임에서 지원하는 형식 액세스 가능성에 대해 설명합니다.

액세스 가능성 | 설명
------------- | -----------
public | 모든 어셈블리에서 액세스할 수 있는 형식입니다.
어셈블리 | 해당 어셈블리 안에서만 액세스할 수 있는 형식입니다.

중첩된 형식의 액세스 가능성은 액세스 가능 도메인에 따라 다릅니다. 액세스 가능 도메인은 멤버에 대해 선언된 액세스 가능성 및 한 수준 위 형식의 액세스 가능 도메인에 의해 결정됩니다. 그러나 중첩 형식의 액세스 가능 도메인은 포함하는 형식의 액세스 가능 도메인을 벗어날 수는 없습니다.

`P` 프로그램 내의 `T` 형식에서 선언된 중첩된 멤버 `M`의 접근성 도메인은 다음과 같이 정의됩니다. `M` 자체가 형식일 수도 있습니다. 

* `M`에 대해 선언된 액세스 가능성이 `public`인 경우 `M`의 액세스 가능 도메인은 `T`의 액세스 가능 도메인입니다.

* `M`에 대해 선언된 액세스 가능성이 `protected internal`인 경우 `M`의 액세스 가능 도메인은 `T`의 프로그램 텍스트를 가진 `P`의 액세스 가능 도메인과 `T` 외부에 선언된 `P`에서 파생된 모든 형식의 프로그램 텍스트 사이의 교집합 부분입니다.

* `M`에 대해 선언된 액세스 가능성이 `protected`인 경우 `M`의 액세스 가능 도메인은 `T`의 프로그램 텍스트를 가진 `T`의 액세스 가능 도메인과 `T`에서 파생된 모든 형식 사이의 교집합 부분입니다.

* `M`의 선언된 접근성이 `internal`인 경우 `M`의 접근성 도메인은 `P`의 접근성 도메인과 프로그램 텍스트 `T`의 교집합 부분입니다.

* `M`에 대해 선언된 액세스 가능성이 `private`인 경우 `M`의 액세스 가능 도메인은 `T`의 프로그램 텍스트입니다.

### <a name="type-names"></a>형식 이름

공용 형식 시스템에서 형식 이름에는 두 가지 제약 조건이 있습니다. 

* 모든 이름은 유니코드(16비트) 문자의 문자열로 인코딩됩니다.

* 이름에 값 0x0000(16비트)을 포함할 수 없습니다.

그러나 대부분의 언어에서는 형식 이름에 대해 추가적인 제한을 적용합니다. 비교 연산은 바이트 단위로 수행되므로 대/소문자를 구분하며 로캘에 종속되지 않습니다.

형식은 다른 모듈 및 어셈블리의 형식을 참조할 수 있지만 하나의 .NET 모듈 내에서 완전히 정의되어야 합니다. 컴파일러 지원에 따라 차이가 있지만 형식은 여러 소스 코드 파일로 분리될 수 있습니다. 형식 이름은 네임스페이스 내에서만 고유하면 됩니다. 형식을 완전하게 식별하려면 형식의 구현을 포함하는 네임스페이스를 통해 형식 이름을 정규화해야 합니다.

### <a name="base-types-and-interfaces"></a>기본 형식 및 인터페이스

형식은 다른 형식에서 값과 동작을 상속할 수 있습니다. 공용 형식 시스템에서는 둘 이상의 기본 형식에서 형식을 상속할 수 없습니다.

형식에서 구현할 수 있는 인터페이스의 수는 제한이 없습니다. 인터페이스를 구현하려면 형식에서 해당 인터페이스의 모든 가상 멤버를 구현해야 합니다. 가상 메서드는 파생된 형식에서 구현할 수 있으며 정적으로 또는 동적으로 호출할 수 있습니다.

## <a name="type-members"></a>형식 멤버

런타임을 사용하면 형식의 동작과 상태를 지정하는 형식의 멤버를 정의할 수 있습니다. 다음과 같은 형식 멤버가 있습니다.

* [필드](#fields)

* [속성](#properties)

* [메서드](#methods)

* [생성자](#constructors)

* [이벤트](#events)

* [중첩 형식](#nested-types)

### <a name="fields"></a>필드

필드는 형식의 상태를 설명하고 상태의 일부를 포함합니다. 필드는 런타임에서 지원하는 모든 형식일 수 있습니다. 필드는 대개 `private` 또는 `protected`이므로 클래스 내부나 파생 클래스에서만 액세스할 수 있습니다. 필드의 값을 형식 외부에서 수정할 수 있는 경우 일반적으로 속성 집합 접근자가 사용됩니다. 공개적으로 노출된 필드는 대개 읽기 전용이며 다음 두 형식 중 하나일 수 있습니다. 

* 상수. 해당 값은 디자인 타임에 할당됩니다. 상수는 `static`(Visual Basic의 경우 `Shared`) 키워드를 통해 정의되지는 않지만 클래스의 정적 멤버입니다.

* 읽기 전용 변수. 해당 값은 클래스 생성자에서만 할당될 수 있습니다.

다음 예제에서는 이 두 가지 읽기 전용 필드를 사용하는 방법을 보여 줍니다.

```csharp
using System;

public class Constants
{
   public const double Pi = 3.1416;
   public readonly DateTime BirthDate;

   public Constants(DateTime birthDate)
   {
      this.BirthDate = birthDate;
   }
}

public class Example
{
   public static void Main()
   {
      Constants con = new Constants(new DateTime(1974, 8, 18));
      Console.Write(Constants.Pi + "\n");
      Console.Write(con.BirthDate.ToString("d") + "\n");
   }
}
// The example displays the following output if run on a system whose current
// culture is en-US:
//    3.1417
//    8/18/1974
```

```vb
Public Class Constants
   Public Const Pi As Double = 3.1416
   Public ReadOnly BirthDate As Date

   Public Sub New(birthDate As Date)
      Me.BirthDate = birthDate
   End Sub
End Class

Public Module Example
   Public Sub Main()
      Dim con As New Constants(#8/18/1974#)
      Console.WriteLine(Constants.Pi.ToString())
      Console.WriteLine(con.BirthDate.ToString("d"))
   End Sub
End Module
' The example displays the following output if run on a system whose current
' culture is en-US:
'    3.1417
'    8/18/1974
```

### <a name="properties"></a>속성

속성은 형식의 값 또는 상태에 이름을 지정하고 속성의 값을 가져오거나 설정하는 데 사용하는 메서드를 정의합니다. 속성은 기본 형식, 기본 형식의 컬렉션, 사용자 정의 형식 또는 사용자 정의 형식의 컬렉션일 수 있습니다. 속성은 주로 형식의 공용 인터페이스를 형식의 실제 표시와 무관하게 유지하기 위해 사용합니다. 따라서 속성은 클래스에 직접 저장되지 않은 값을 반영하거나(예를 들어 속성이 계산된 값을 반환하는 경우) 값이 전용 필드에 할당되기 전에 유효성 검사를 수행할 수 있습니다. 다음 예제에서는 후자의 패턴을 보여 줍니다.

```csharp
using System;

public class Person
{
   private int m_Age;

   public int Age
   { 
      get { return m_Age; }
      set {
         if (value < 0 || value > 125)
         {
            throw new ArgumentOutOfRangeException("The value of the Age property must be between 0 and 125.");
         }
         else
         {
            m_Age = value;
         }         
      }
   }
}
```

```vb
Public Class Person
   Private m_Age As Integer

   Public Property Age As Integer
      Get
         Return m_Age
      End Get
      Set
         If value < 0 Or value > 125 Then
            Throw New ArgumentOutOfRangeException("The value of the Age property must be between 0 and 125.")
         Else
            m_Age = value
         End If
      End Set
   End Property
End Class
```

읽기 가능한 속성이 포함된 형식의 MSIL(Microsoft Intermediate Language)에는 속성 자체뿐만 아니라 `get`*_propertyname* 메서드도 포함되어 있으며 쓰기 가능한 속성이 포함된 형식의 MSIL에는 `set`*_propertyname* 메서드가 포함되어 있습니다.

### <a name="methods"></a>메서드

메서드는 형식에 대해 수행할 수 있는 작업을 설명합니다. 메서드의 시그니처에서는 모든 매개 변수 및 반환 값에 허용되는 형식을 지정합니다.

대부분의 메서드는 메서드 호출에 필요한 매개 변수 개수를 정확하게 정의하지만, 일부 메서드는 일정하지 않은 수의 매개 변수를 지원합니다. 이러한 메서드에 대해 선언된 최종 매개 변수는 [ParamArrayAttribute](xref:System.ParamArrayAttribute) 특성으로 표시됩니다. 일반적으로 언어 컴파일러는 C#의 `params` 및 Visual Basic의 `ParamArray` 등과 같이 [ParamArrayAttribute](xref:System.ParamArrayAttribute)를 명시적으로 사용할 필요가 없도록 하는 키워드를 제공합니다.

### <a name="constructors"></a>생성자

생성자는 클래스 또는 구조체의 새 인스턴스를 만드는 특수한 메서드입니다. 다른 메서드와 마찬가지로 생성자는 매개 변수를 포함할 수 있지만 반환 값은 가지지 않습니다. 즉, `void`를 반환합니다. 

클래스의 소스 코드에서 생성자를 명시적으로 정의하지 않은 경우 컴파일러는 매개 변수가 없는 기본 생성자를 포함합니다. 그러나 클래스의 소스 코드에서 매개 변수가 있는 생성자만 정의하는 경우 C# 컴파일러는 매개 변수가 없는 생성자를 생성하지 않습니다.

구조의 소스 코드에서 생성자를 정의하는 경우 해당 생성자에는 매개 변수가 있어야 합니다. 구조에서는 기본 생성자(매개 변수가 없는 생성자)를 정의할 수 없으며 컴파일러는 구조 또는 다른 값 형식에 대해 매개 변수가 없는 생성자를 생성하지 않습니다. 모든 값 형식에는 암시적 기본 생성자가 없습니다. 이 생성자는 공용 언어 런타임에서 구현되며 구조의 모든 필드를 기본값으로 초기화합니다. 

### <a name="events"></a>이벤트

이벤트는 응답할 수 있는 인시던트를 정의하고 이벤트를 구독, 구독 취소 및 발생시키기 위한 메서드를 정의합니다. 이벤트는 주로 다른 형식에 상태 변경 내용을 알려주는 데 사용합니다.

### <a name="nested-types"></a>중첩 형식

중첩 형식은 다른 형식의 멤버인 형식을 나타냅니다. 중첩 형식은 포함하는 형식과 밀접하게 결합되어야 하고 일반 용도의 형식으로 사용하면 안 됩니다. 중첩 형식은 해당 선언 형식에서 중첩 형식의 인스턴스를 만들고 사용하며 중첩 형식의 사용이 공용 멤버에게 노출되지 않는 경우에 유용합니다.

중첩 형식은 일부 개발자에게 혼란을 줄 수 있으므로 불가피한 경우를 제외하고는 공개적으로 표시하지 않아야 합니다. 잘 디자인된 라이브러리에서 개발자는 개체를 인스턴스화하거나 변수를 선언할 때 중첩 형식을 거의 사용하지 않아야 합니다.

## <a name="characteristics-of-type-members"></a>형식 멤버의 특성

공용 형식 시스템에서 형식 멤버는 다양한 특성을 가질 수 있지만 언어에서 이런 특성을 모두 지원할 필요는 없습니다. 다음 표에서는 멤버 특성에 대해 설명합니다.

특성 | 적용 대상 | 설명
-------------- | ------------ | -----------
abstract | 메서드, 속성 및 이벤트 | 형식에서 메서드 구현을 제공하지 않습니다. 추상 메서드를 구현하거나 상속하는 형식에서 메서드에 대한 구현을 제공해야 합니다. 그러나 파생된 형식 자체가 추상 형식인 경우만은 예외입니다. 모든 추상 메서드는 가상 메서드입니다.
private | 모두 | 멤버와 동일한 형식 또는 중첩된 형식 내에서만 액세스할 수 있습니다.
family | 모두 | 멤버와 동일한 형식 내에서, 그리고 그 멤버에서 상속된 파생된 형식에서 액세스할 수 있습니다.
assemby | 모두 | 형식이 정의된 어셈블리에서만 액세스할 수 있습니다.
family and assembly | 모두 | 패밀리와 어셈블리 모두에 대한 액세스 자격이 있는 형식에서만 액세스할 수 있습니다.
family or assemby | 모두 | 패밀리 또는 어셈블리에 대한 액세스 자격이 있는 형식에서만 액세스할 수 있습니다.
public | 모두 | 모든 형식에서 액세스할 수 있습니다.
final | 메서드, 속성 및 이벤트 | 가상 메서드는 파생된 형식에서 재정의할 수 없습니다.
initialize-only | 필드 | 값을 초기화할 수만 있고 초기화 이후에는 작성할 수 없습니다.
인스턴스 | 필드, 메서드, 속성 및 이벤트 | `static`(C#), `Shared`(Visual Basic), `virtual`(C#) 또는 `Overridable`(Visual Basic)로 표시되지 않은 멤버는 instance 키워드가 없는 인스턴스 멤버입니다. 멤버를 사용하는 개체 수만큼의 멤버 복사본이 메모리에 있습니다.
리터럴 | 필드 | 필드에 할당되는 값은 컴파일 타임에 알려지는 기본 제공 값 형식의 고정 값입니다. 리터럴 필드를 때로는 상수라고도 합니다.
newslot 또는 override | 모두 | 멤버가 시그니처가 같은 상속된 멤버와 상호 작용하는 방법을 정의합니다. `newslot`은 시그니처가 같은 상속된 멤버를 숨기고, `override`는 상속된 가상 메서드의 정의를 바꿉니다. 기본값은 새 슬롯입니다.
정적 | 필드, 메서드, 속성 및 이벤트 | 멤버는 형식의 특정 인스턴스가 아니라 멤버가 정의된 형식에 속합니다. 멤버는 형식의 인스턴스가 작성되지 않은 경우에도 존재하며 형식의 모든 인스턴스 간에 공유됩니다.
virtual | 메서드, 속성 및 이벤트 | 메서드는 파생된 형식에서 구현할 수 있으며 정적으로 또는 동적으로 호출할 수 있습니다. 동적 호출을 사용하는 경우, 메서드의 어떤 구현을 호출할지 결정하는 것은 컴파일 타임에 알려진 형식이 아니라 런타임에 호출을 수행하는 인스턴스의 형식입니다. 가상 메서드를 정적으로 호출하려면 원하는 버전의 메서드를 사용하는 형식으로 변수를 캐스팅해야 합니다.

### <a name="overloading"></a>오버로딩

각 형식 멤버에는 고유의 시그니처가 있습니다. 메서드 시그니처는 메서드 이름과 매개 변수 목록(메서드 인수의 순서 및 형식)으로 구성됩니다. 형식 내에서 시그니처를 다르게 하여 이름이 같은 여러 개의 메서드를 정의할 수 있습니다. 이름이 같은 둘 이상의 메서드를 정의하면 메서드가 오버로드됩니다. 예를 들어 [System.Char](xref:System.Char)에서는 `IsDigit` 메서드가 오버로드됩니다. 한 메서드는 [Char](xref:System.Char)을 사용하고, 다른 메서드는 [String](xref:System.String) 및 [Int32](xref:System.Int32)를 사용합니다. 

> [!NOTE]
> 반환 형식은 메서드 시그니처의 일부로 간주되지 않습니다. 즉, 메서드는 반환 형식만 다를 경우에는 오버로드될 수 없습니다.

### <a name="inheriting-overriding-and-hiding-members"></a>멤버 상속, 재정의 및 숨기기

파생된 형식에서는 기본 형식의 멤버를 모두 상속합니다. 즉, 파생된 형식에서 이러한 멤버를 정의하고 사용할 수 있습니다. 상속된 멤버의 동작 또는 품질을 다음과 같은 두 가지 방법으로 수정할 수 있습니다. 

* 파생된 형식에서 동일한 시그니처를 가진 새 멤버를 정의하여 상속 멤버를 숨길 수 있습니다. 이는 이전의 공용 멤버를 전용 멤버로 변경하거나 `final`로 표시된 상속된 메서드에 대해 새로운 동작을 정의하기 위해 수행될 수 있습니다.

* 상속된 가상 메서드를 파생된 형식에서 재정의할 수 있습니다. 메서드를 재정의하면 컴파일 타임에 알려진 변수의 형식이 아니라 런타임에 값의 형식을 기반으로 호출되는 메서드에 대한 새 정의가 제공됩니다. 가상 메서드가 `final`로 표시되어 있지 않고 새 메서드에서 최소한 가상 메서드와 동일한 수준의 액세스 가능성을 갖는 경우에만 가상 메서드를 재정의할 수 있습니다.

## <a name="see-also"></a>참고 항목

[.NET Framework의 형식 변환](type-conversion.md)


<!--HONumber=Nov16_HO1-->


