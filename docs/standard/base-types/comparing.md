---
title: "문자열 비교"
description: "문자열 비교"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 07/26/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 920ee5e8-3d61-4941-b5af-fc50eaee427c
translationtype: Human Translation
ms.sourcegitcommit: fb00da6505c9edb6a49d2003ae9bcb8e74c11d6c
ms.openlocfilehash: 10c9ea3ebb50e8d8075d8a4e4ce007ddc29ff412

---

# <a name="comparing-strings"></a>문자열 비교

.NET에서는 문자열의 값을 비교하는 여러 가지 메서드를 제공합니다. 다음 표에서는 값 비교 메서드를 나열하고 설명합니다.

메서드 이름 | 기능
----------- | ---
[String.Compare](xref:System.String.Compare(System.String,System.Int32,System.String,System.Int32,System.Int32)) | 두 문자열의 값을 비교합니다. 정수 값을 반환합니다.
[String.CompareOrdinal](xref:System.String.CompareOrdinal(System.String,System.Int32,System.String,System.Int32,System.Int32)) | 로컬 문화권에 관계없이 두 문자열을 비교합니다. 정수 값을 반환합니다.
[String.CompareTo](xref:System.String.CompareTo(System.String)) | 현재 문자열 개체를 다른 문자열과 비교합니다. 정수 값을 반환합니다.
[String.StartsWith](xref:System.String.StartsWith(System.String)) | 문자열이 전달된 문자열로 시작하는지 여부를 확인합니다. 부울 값을 반환합니다.
[String.EndsWith](xref:System.String.CompareTo(System.String)) | 문자열이 전달된 문자열로 끝나는지 여부를 확인합니다. 부울 값을 반환합니다.
[String.Equals](xref:System.String.CompareTo(System.String)) | 두 문자열이 같은지 여부를 확인합니다. 부울 값을 반환합니다.
[String.IndexOf](xref:System.String.IndexOf(System.Char)) | 검사 중인 문자열의 처음부터 시작하여 문자 또는 문자열의 인덱스 위치를 반환합니다. 정수 값을 반환합니다.
[String.LastIndexOf](xref:System.String.LastIndexOf(System.Char)) | 검사 중인 문자열의 끝부터 시작하여 문자 또는 문자열의 인덱스 위치를 반환합니다. 정수 값을 반환합니다.

## <a name="compare"></a>비교

정적 [String.Compare](xref:System.String.Compare(System.String,System.Int32,System.String,System.Int32,System.Int32)) 메서드는 두 문자열을 비교하는 철저한 방법을 제공합니다. 이 메서드는 문화권을 인식합니다. 이 함수를 사용하여 두 문자열 또는 두 문자열의 부분 문자열을 비교할 수 있습니다. 또한 대/소문자와 문화권 차이를 반영하거나 무시하는 오버로드가 제공됩니다. 다음 표에서는 이 메서드가 반환할 수 있는 세 가지 정수 값을 보여 줍니다. 

반환 값 | 조건
------------ | ---------
음의 정수 | 정렬 순서에서 첫 번째 문자열이 두 번째 문자열 앞에 오거나 첫 번째 문자열이 `null`입니다.
0 | 첫 번째 문자열과 두 번째 문자열이 같거나 두 문자열이 모두 `null`입니다.
양의 정수 또는 1 | 정렬 순서에서 첫 번째 문자열이 두 번째 문자열 뒤에 오거나 두 번째 문자열이 null입니다.
 
> [!IMPORTANT]
> [String.Compare](xref:System.String.Compare(System.String,System.Int32,System.String,System.Int32,System.Int32)) 메서드는 주로 문자열의 순서를 지정하거나 정렬할 때 사용됩니다. [String.Compare](xref:System.String.Compare(System.String,System.Int32,System.String,System.Int32,System.Int32)) 메서드를 통해 같은지 여부를 테스트하면 안 됩니다(즉, 한 문자열이 다른 문자열보다 작거나 큰지에 관계없이 반환 값 0을 명시적으로 찾기 위해 사용하면 안 됨). 대신, 두 문자열이 같은지를 확인하려면 [String.Equals(String, String, StringComparison)](xref:System.String.Equals(System.String,System.String,System.StringComparison)) 메서드를 사용합니다.

다음 예제에서는 [String.Compare](xref:System.String.Compare(System.String,System.Int32,System.String,System.Int32,System.Int32)) 메서드를 사용하여 두 문자열의 상대 값을 확인합니다.

```csharp
string string1 = "Hello World!";
Console.WriteLine(String.Compare(string1, "Hello World?"));
```

```vb
Dim string1 As String = "Hello World!"
Console.WriteLine(String.Compare(string1, "Hello World?"))
```

이 예제에서는 콘솔에 `-1`을 표시합니다.

## <a name="compareordinal"></a>CompareOrdinal

[String.CompareOrdinal](xref:System.String.CompareOrdinal(System.String,System.Int32,System.String,System.Int32,System.Int32)) 메서드는 로컬 문화권을 고려하지 않고 두 문자열 개체를 비교합니다. 이 메서드의 반환 값은 앞의 표에서 `Compare` 메서드가 반환하는 값과 같습니다.

> [!IMPORTANT]
> [String.CompareOrdinal](xref:System.String.CompareOrdinal(System.String,System.Int32,System.String,System.Int32,System.Int32)) 메서드는 주로 문자열의 순서를 지정하거나 정렬할 때 사용됩니다. [String.CompareOrdinal](xref:System.String.CompareOrdinal(System.String,System.Int32,System.String,System.Int32,System.Int32)) 메서드를 통해 문자열이 같은지를 테스트하면 안 됩니다(즉, 한 문자열이 다른 문자열보다 작거나 큰지에 관계없이 반환 값 0을 명시적으로 찾기 위해 사용하면 안 됨). 대신, 두 문자열이 같은지를 확인하려면 [String.Equals(String, String, StringComparison)](xref:System.String.Equals(System.String,System.String,System.StringComparison)) 메서드를 사용합니다.

다음 예제에서는 `CompareOrdinal` 메서드를 사용하여 두 문자열의 값을 비교합니다.

```csharp
string string1 = "Hello World!";
Console.WriteLine(String.CompareOrdinal(string1, "hello world!"));
```

```vb
Dim string1 As String = "Hello World!"
Console.WriteLine(String.CompareOrdinal(string1, "hello world!"))
```

이 예제에서는 콘솔에 `-32`을 표시합니다.

## <a name="compareto"></a>CompareTo

[String.CompareTo](xref:System.String.CompareTo(System.String)) 메서드는 현재 문자열 개체가 캡슐화하는 문자열을 다른 문자열 또는 개체와 비교합니다. 이 메서드의 반환 값은 앞의 표에서 `String.Compare` 메서드가 반환하는 값과 같습니다.

> [!IMPORTANT]
> [String.CompareTo](xref:System.String.CompareTo(System.String)) 메서드는 주로 문자열의 순서를 지정하거나 정렬할 때 사용됩니다. [String.CompareTo](xref:System.String.CompareTo(System.String)) 메서드를 통해 같은지 여부를 테스트하면 안 됩니다(즉, 한 문자열이 다른 문자열보다 작거나 큰지에 관계없이 반환 값 0을 명시적으로 찾기 위해 사용하면 안 됨). 대신, 두 문자열이 같은지를 확인하려면 [String.Equals(String, String, StringComparison)](xref:System.String.Equals(System.String,System.String,System.StringComparison)) 메서드를 사용합니다.

다음 예제에서는 `String.CompareTo` 메서드를 사용하여 `string1` 개체를 `string2` 개체와 비교합니다.

```csharp
string string1 = "Hello World";
string string2 = "Hello World!";
int MyInt = string1.CompareTo(string2);
Console.WriteLine( MyInt );
```

```vb
Dim string1 As String = "Hello World"
Dim string2 As String = "Hello World!"
Dim MyInt As Integer = string1.CompareTo(string2)
Console.WriteLine(MyInt)
```

이 예제에서는 콘솔에 `-1`을 표시합니다.

## <a name="equals"></a>같음

[String.Equals](xref:System.String.CompareTo(System.String)) 메서드는 두 문자열이 같은지 여부를 쉽게 확인할 수 있습니다. 대/소문자 구분 메서드는 `true` 또는 `false` 부울 값을 반환합니다. 다음 예제와 같이 기존 클래스에서 사용할 수 있습니다. 다음 예제에서는 `Equals` 메서드를 사용하여 문자열 개체에 "Hello World"라는 구가 포함되어 있는지 여부를 확인합니다.

```csharp
string string1 = "Hello World";
Console.WriteLine(string1.Equals("Hello World"));
```

```vb
Dim string1 As String = "Hello World"
Console.WriteLine(string1.Equals("Hello World"))
```

이 예제에서는 콘솔에 `true`을 표시합니다.

이 메서드는 정적 메서드로 사용될 수도 있습니다. 다음 예제에서는 정적 메서드를 사용하여 두 문자열 개체를 비교합니다.

```csharp
string string1 = "Hello World";
string string2 = "Hello World";
Console.WriteLine(String.Equals(string1, string2));
```

```vb
Dim string1 As String = "Hello World"
Dim string2 As String = "Hello World"
Console.WriteLine(String.Equals(string1, string2))
```

이 예제에서는 콘솔에 `true`을 표시합니다.

## <a name="startswith-and-endswith"></a>StartsWith 및 EndsWith

[String.StartsWith](xref:System.String.StartsWith(System.String)) 메서드를 사용하여 문자열 개체가 다른 문자열을 포함하는 동일한 문자로 시작하는지 여부를 확인합니다. 이 대/소문자 구분 메서드는 현재 문자열 개체가 전달된 문자열로 시작하는 경우 `true`를 반환하고, 전달된 문자열로 시작하지 않는 경우 `false`를 반환합니다. 다음 예제에서는 이 메서드를 사용하여 문자열 개체가 "Hello"로 시작하는지 여부를 확인합니다.

```csharp
string string1 = "Hello World";
Console.WriteLine(string1.StartsWith("Hello"));
```

```vb
Dim string1 As String = "Hello World!"
Console.WriteLine(string1.StartsWith("Hello"))
```

이 예제에서는 콘솔에 `true`을 표시합니다.

[String.EndsWith](xref:System.String.CompareTo(System.String)) 메서드는 전달된 문자열을 현재 문자열 개체의 끝에 있는 문자와 비교합니다. 역시 부울 값을 반환합니다. 다음 예제에서는 `EndsWith` 메서드를 사용하여 문자열의 끝을 검사합니다.

```csharp
string string1 = "Hello World";
Console.WriteLine(string1.EndsWith("Hello"));
```

```vb
Dim string1 As String = "Hello World!"
Console.WriteLine(string1.EndsWith("Hello"))
```

이 예제에서는 콘솔에 `false`을 표시합니다.

## <a name="indexof-and-lastindexof"></a>IndexOf 및 LastIndexOf

[String.IndexOf](xref:System.String.IndexOf(System.Char)) 메서드를 사용하여 문자열 내에서 특정 문자의 첫 번째 발생 위치를 확인합니다. 이 대/소문자 구분 메서드는 문자열의 시작 부분에서 계산을 시작하고 0부터 시작하는 인덱스를 사용하여 전달된 문자의 위치를 반환합니다. 문자를 찾을 수 없는 경우 -1 값이 반환됩니다.

다음 예제에서는 `IndexOf` 메서드를 사용하여 문자열에서 '`l`' 문자의 첫 번째 발생을 검색합니다.

```csharp
string string1 = "Hello World";
Console.WriteLine(string1.IndexOf('l'));
```

```vb
Dim string1 As String = "Hello World!"
Console.WriteLine(string1.IndexOf("l"))
```

이 예제에서는 콘솔에 `2`을 표시합니다.

[String.LastIndexOf](xref:System.String.LastIndexOf(System.Char)) 메서드는 문자열 내에서 특정 문자의 마지막 발생 위치를 반환한다는 점을 제외하고 `String.IndexOf` 메서드와 비슷합니다. 대/소문자를 구분하며 0부터 시작하는 인덱스를 사용합니다. 

다음 예제에서는 `LastIndexOf` 메서드를 사용하여 문자열에서 '`l`' 문자의 마지막 발생을 검색합니다.

```csharp
string string1 = "Hello World";
Console.WriteLine(string1.LastIndexOf('l'));
```

```vb
Dim string1 As String = "Hello World!"
Console.WriteLine(string1.LastIndexOf("l"))
```

이 예제에서는 콘솔에 `9`을 표시합니다.

두 메서드는 모두 [String.Remove](xref:System.String.Remove(System.Int32)) 메서드와 함께 사용할 때 유용합니다. 문자 또는 해당 문자로 시작하는 단어를 제거하기 위해 `IndexOf` 또는 `LastIndexOf` 메서드 중 하나를 사용하여 문자의 위치를 검색한 다음 해당 위치를 `Remove method` 메서드에 제공할 수 있습니다.

## <a name="see-also"></a>참고 항목

[기본적인 문자열 작업](basic-string-operations.md)















<!--HONumber=Nov16_HO1-->


