---
title: "열거형 형식 문자열"
description: "열거형 형식 문자열"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 07/25/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 4d581898-99bc-42c3-816c-d8238f45096f
translationtype: Human Translation
ms.sourcegitcommit: fb00da6505c9edb6a49d2003ae9bcb8e74c11d6c
ms.openlocfilehash: 9f5b60a75bf4b92b88d249be2f95f312ddb6909c

---

# <a name="enumeration-format-strings"></a>열거형 형식 문자열

[Enum.ToString](xref:System.Enum.ToString) 메서드를 사용하여 열거형 멤버의 숫자, 16진수 또는 문자열 값을 나타내는 새 문자열 개체를 만들 수 있습니다. 이 메서드는 열거형 형식 문자열 중 하나를 사용하여 반환할 값을 지정합니다.

다음 섹션에서는 열거형 형식 문자열과 반환되는 값을 보여 줍니다. 이러한 형식 지정자는 대/소문자를 구분하지 않습니다.

## <a name="the-g-or-g-format-strings"></a>G 또는 g 형식 문자열

G 또는 g 형식 문자열은 가능한 경우 열거형 항목을 문자열 값으로 표시하고, 가능하지 않은 경우 현재 인스턴스의 정수 값을 표시합니다. `Flags` 특성을 설정하여 열거형을 정의한 경우 유효한 각 항목의 문자열 값이 쉼표로 구분되어 서로 연결됩니다. `Flags` 특성을 설정하지 않은 경우 잘못된 값이 숫자 항목으로 표시됩니다. 다음 예제에서는 G 형식 지정자를 보여 줍니다.

```csharp
Console.WriteLine(ConsoleColor.Red.ToString("G"));         // Displays Red
FileAttributes attributes = FileAttributes.Hidden |
                            FileAttributes.Archive;
Console.WriteLine(attributes.ToString("G"));   // Displays Hidden, Archive
```

```vb
Console.WriteLine(ConsoleColor.Red.ToString("G"))           ' Displays Red
Dim attributes As FileAttributes = FileAttributes.Hidden Or _
                                   FileAttributes.Archive
Console.WriteLine(attributes.ToString("G"))     ' Displays Hidden, Archive
```

## <a name="the-f-or-f-format-strings"></a>F 또는 f 형식 문자열

F 또는 f 형식 문자열은 열거형 항목을 문자열 값으로 표시합니다(가능한 경우). `Flags` 특성이 없어도 열거형 항목의 총합으로 값을 완전히 표시할 수 있는 경우 유효한 각 항목의 문자열 값이 쉼표로 구분되어 서로 연결됩니다. 열거형 항목으로 값을 완전히 확인할 수 없는 경우에는 값의 형식이 정수 값으로 지정됩니다. 다음 예제에서는 F 형식 지정자를 보여 줍니다.

```csharp
Console.WriteLine(ConsoleColor.Blue.ToString("F"));       // Displays Blue
FileAttributes attributes = FileAttributes.Hidden | 
                            FileAttributes.Archive;
Console.WriteLine(attributes.ToString("F"));   // Displays Hidden, Archive
```

```vb
Console.WriteLine(ConsoleColor.Blue.ToString("F"))         ' Displays Blue
Dim attributes As FileAttributes = FileAttributes.Hidden Or _
                                   FileAttributes.Archive
Console.WriteLine(attributes.ToString("F"))     ' Displays Hidden, Archive
```

## <a name="the-d-or-d-format-strings"></a>D 또는 d 형식 문자열

D 또는 d 형식 문자열은 열거형 항목을 가능한 가장 짧은 표현의 정수 값으로 표시합니다. 다음 예제에서는 D 형식 지정자를 보여 줍니다.

```csharp
Console.WriteLine(ConsoleColor.Cyan.ToString("D"));         // Displays 11
FileAttributes attributes = FileAttributes.Hidden |
                            FileAttributes.Archive;
Console.WriteLine(attributes.ToString("D"));                // Displays 34
````

```vb
Console.WriteLine(ConsoleColor.Cyan.ToString("D"))           ' Displays 11
Dim attributes As FileAttributes = FileAttributes.Hidden Or _
                                   FileAttributes.Archive
Console.WriteLine(attributes.ToString("D"))                  ' Displays 34 
```

## <a name="the-x-or-x-format-strings"></a>X 또는 x 형식 문자열

X 또는 x 형식 문자열은 열거형 항목을 16진수 값으로 표시합니다. 필요에 따라 값 앞에 0을 추가하여 값의 길이가 최소 8자리가 되도록 합니다. 다음 예제에서는 X 형식 지정자를 보여 줍니다.

```csharp
Console.WriteLine(ConsoleColor.Cyan.ToString("X"));   // Displays 0000000B
FileAttributes attributes = FileAttributes.Hidden |
                            FileAttributes.Archive;
Console.WriteLine(attributes.ToString("X"));          // Displays 00000022
```

```vb
Console.WriteLine(ConsoleColor.Cyan.ToString("X"))     ' Displays 0000000B
Dim attributes As FileAttributes = FileAttributes.Hidden Or _
                                   FileAttributes.Archive
Console.WriteLine(attributes.ToString("X"))            ' Displays 00000022 
```

## <a name="example"></a>예제

다음 예제에서는 세 개의 항목 `Red`, `Blue` 및 `Green`으로 구성된 `Colors`라는 열거형을 정의합니다.

 ```csharp
 public enum Color {Red = 1, Blue = 2, Green = 3}
```

```vb
Public Enum Color
   Red = 1
   Blue = 2
   Green = 3
End Enum
```

열거형이 정의되면 다음과 같이 인스턴스를 선언할 수 있습니다.

```csharp
Color myColor = Color.Green;
```

```vb
Dim myColor As Color = Color.Green
```

그런 다음 `Color.ToString(System.String)` 메서드를 사용하여 전달된 형식 지정자에 따라 열거형 값을 다양한 방식으로 표시할 수 있습니다.

```csharp
Console.WriteLine("The value of myColor is {0}.", 
                  myColor.ToString("G"));
Console.WriteLine("The value of myColor is {0}.", 
                  myColor.ToString("F"));
Console.WriteLine("The value of myColor is {0}.", 
                  myColor.ToString("D"));
Console.WriteLine("The value of myColor is 0x{0}.", 
                  myColor.ToString("X"));
// The example displays the following output to the console:
//       The value of myColor is Green.
//       The value of myColor is Green.
//       The value of myColor is 3.
//       The value of myColor is 0x00000003.
```

```vb
Console.WriteLine("The value of myColor is {0}.", _
                  myColor.ToString("G"))
Console.WriteLine("The value of myColor is {0}.", _
                  myColor.ToString("F"))
Console.WriteLine("The value of myColor is {0}.", _
                  myColor.ToString("D"))
Console.WriteLine("The value of myColor is 0x{0}.", _
                  myColor.ToString("X"))
' The example displays the following output to the console:
'       The value of myColor is Green.
'       The value of myColor is Green.
'       The value of myColor is 3.
'       The value of myColor is 0x00000003. 
```

## <a name="see-also"></a>참고 항목

[형식 서식 지정](formatting-types.md)




<!--HONumber=Nov16_HO3-->


