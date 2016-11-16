---
title: "대/소문자 바꾸기"
description: "대/소문자 바꾸기"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 07/26/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 646c5afd-8aec-4393-9c00-f68ad2580c68
translationtype: Human Translation
ms.sourcegitcommit: fb00da6505c9edb6a49d2003ae9bcb8e74c11d6c
ms.openlocfilehash: a6f6f21daae79f752cfac0a70558778ae98d1a5d

---

# <a name="changing-case"></a>대/소문자 바꾸기

사용자 입력을 수락하는 응용 프로그램을 작성하는 경우 데이터를 입력할 때 사용하는 대/소문자를 확신할 수 없습니다. 특히 사용자 인터페이스에 표시하는 경우 문자열의 대/소문자를 일관되게 표시하려는 경우가 많습니다. 다음 표에서는 두 가지 대/소문자 변경 메서드를 설명합니다.

메서드 이름 | 기능
----------- | ---
[String.ToUpper](xref:System.String.ToUpper) | 문자열의 모든 문자를 대문자로 변환합니다.
[String.ToLower](xref:System.String.ToLower) | 문자열의 모든 문자를 소문자로 변환합니다.

> [!WARNING]  
> 문자열을 비교하거나 같은지 테스트하기 위해 `String.ToUpper` 및 `String.ToLower` 메서드를 사용하여 문자열을 변환하면 안 됩니다. 

## <a name="comparing-strings-of-mixed-case"></a>대/소문자가 혼합된 문자열 비교

대/소문자가 혼합된 문자열을 비교하여 같은지 확인하려면 [String](xref:System) `Equals` 메서드의 오버로드 중 하나를 *comparisonType* 매개 변수와 함께 호출하고 *comparisonType* 인수에 대해 [StringComparison.CurrentCultureIgnoreCase](xref:System.StringComparison.CurrentCultureIgnoreCase) 또는 [StringComparison.OrdinalIgnoreCase](xref:System.StringComparison.OrdinalIgnoreCase) 값을 제공합니다. 

자세한 내용은 [문자열 사용에 대한 모범 사례](best-practices.md)를 참조하세요. 

## <a name="toupper"></a>ToUpper

[String.ToUpper](xref:System.String.ToUpper) 메서드는 문자열의 모든 문자를 대문자로 변경합니다. 다음 예제에서는 "Hello World!" 문자열을 대/소문자 혼합에서 대문자로 변환합니다.

```csharp
string properString = "Hello World!";
Console.WriteLine(properString.ToUpper());
// This example displays the following output:
//       HELLO WORLD!
```

```vb
Dim MyString As String = "Hello World!"
Console.WriteLine(MyString.ToUpper())
' This example displays the following output:
'       HELLO WORLD!
```

## <a name="tolower"></a>ToLower

[String.ToLower](xref:System.String.ToLower) 메서드는 이전 메서드와 비슷하지만 대신 문자열의 모든 문자를 소문자로 변환합니다. 다음 예제에서는 "Hello World!" 문자열을 소문자로 변환합니다.

```csharp
string properString = "Hello World!";
Console.WriteLine(properString.ToLower());
// This example displays the following output:
//       hello world!
```

```vb
Dim MyString As String = "Hello World!"
Console.WriteLine(MyString.ToLower())
' This example displays the following output:
'       hello world!
```

## <a name="see-also"></a>참고 항목

[기본적인 문자열 작업](basic-string-operations.md)



<!--HONumber=Nov16_HO1-->


