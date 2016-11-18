---
title: ".NET에서 기타 문자열 구문 분석"
description: ".NET에서 기타 문자열 구문 분석"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 07/29/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 67670b10-3df4-45ea-8908-5ba3f056887c
translationtype: Human Translation
ms.sourcegitcommit: fb00da6505c9edb6a49d2003ae9bcb8e74c11d6c
ms.openlocfilehash: bf3fc05428ed491e7658951f002a9af17dcba5df

---

# <a name="parsing-other-strings-in-net"></a>.NET에서 기타 문자열 구문 분석

숫자 및 [DateTime](xref:System.DateTime) 문자열 외에도 [Char](xref:System.Char), [부울](xref:System.Boolean), 및 [Enum](xref:System.Enum) 형식을 나타내는 문자열을 데이터 형식으로 구문 분석할 수 있습니다.

## <a name="char"></a>Char

[Char](xref:System.Char) 데이터 형식과 연결된 고정 구문 분석 메서드는 단일 문자를 포함하는 문자열을 해당 유니코드 값으로 변환하는 데 유용합니다. 다음 코드 예제에서는 문자열을 유니코드 문자로 구문 분석합니다.

```csharp
string MyString1 = "A";
char MyChar = Char.Parse(MyString1);
// MyChar now contains a Unicode "A" character.
```

```vb
Dim MyString1 As String = "A"
Dim MyChar As Char = Char.Parse(MyString1)
' MyChar now contains a Unicode "A" character.
```

## <a name="boolean"></a>부울

[부울](xref:System.Boolean) 데이터 형식은 `Boolean` 값을 나타내는 문자열을 실제 `Boolean` 유형으로 변환하는 데 사용할 수 있는 [Parse](xref:System.Boolean.Parse(System.String)) 메서드를 포함합니다. 이 메서드는 대/소문자를 구분하지 않으며 "True" 또는 "False"를 포함하는 문자열을 성공적으로 구문 분석할 수 있습니다. `Boolean` 형식과 연결된 `Parse` 메서드는 공백으로 둘러싸인 문자열을 구문 분석할 수도 있습니다. 다른 문자열을 전달하면 [FormatException](xref:System.FormatException)이 throw됩니다.

다음 코드 예제에서는 문자열을 `Boolean` 값으로 변환하는 `Parse` 메서드를 사용합니다.

```csharp
string MyString2 = "True";
bool MyBool = bool.Parse(MyString2);
// MyBool now contains a True Boolean value.
```

```vb
Dim MyString1 As String = "A"
Dim MyChar As Char = Char.Parse(MyString1)
' MyChar now contains a Unicode "A" character.
```

## <a name="enumeration"></a>열거형

고정 [Parse](xref:System.Enum.Parse(System.Type,System.String)) 메서드를 사용하여 문자열 값에 대한 열거형 형식을 초기화할 수 있습니다. 이 메서드는 구문 분석하는 열거형 형식, 구문 분석할 문자열 및 구문 분석이 대/소문자를 구분하는지를 나타내는 선택적 `Boolean` 플래그를 허용합니다. 구문 분석하는 문자열은 쉼표로 구분된 여러 값을 포함할 수 있으며 앞이나 뒤에는 하나 이상의 빈 공간(공백이라고도 함)이 있을 수 있습니다. 문자열에 여러 값이 포함된 경우 반환된 개체의 값은 비트 OR 연산과 함께 결합된 모든 지정된 값과 같습니다.

다음 예제에서는 문자열 표현을 열거형 값으로 변환하는 `Parse` 메서드를 사용합니다. [DayOfWeek](xref:System.DayOfWeek) 열거형은 문자열에서 목요일로 초기화됩니다.

```csharp
string MyString3 = "Thursday";
DayOfWeek MyDays = (DayOfWeek)Enum.Parse(typeof(DayOfWeek), MyString3);
Console.WriteLine(MyDays);
// The result is Thursday.
```

```vb
Dim MyString3 As String = "Thursday"
Dim MyDays As DayOfWeek = CType([Enum].Parse(GetType(DayOfWeek), MyString3), DayOfWeek)
Console.WriteLine("{0:G}", MyDays)
' The result is Thursday.
```

## <a name="see-also"></a>참고 항목

[.NET에서 문자열 구문 분석](parsing-strings.md)

[.NET의 서식 지정 형식](formatting-types.md)

[.NET에서 형식 변환](type-conversion.md)




<!--HONumber=Nov16_HO3-->


