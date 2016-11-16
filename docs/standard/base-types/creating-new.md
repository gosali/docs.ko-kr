---
title: "새 문자열 만들기"
description: "새 문자열 만들기"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 07/26/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 639397c7-e694-43e0-845b-1681c62bd9fd
translationtype: Human Translation
ms.sourcegitcommit: fb00da6505c9edb6a49d2003ae9bcb8e74c11d6c
ms.openlocfilehash: 43d6194afd948aeccbf051365e059794c74d2646

---

# <a name="creating-new-strings"></a>새 문자열 만들기

.NET에서는 단순한 할당을 사용하여 문자열을 만들 수 있으며 다양한 매개 변수를 사용한 문자열 생성을 지원하기 위해 클래스 생성자도 오버로드됩니다. 또한 .NET은 여러 문자열, 문자열 배열 또는 개체를 결합하여 새 문자열 개체를 만드는 여러 메서드를 [System.String](xref:System.String) 클래스에서 제공합니다. 

## <a name="creating-strings-using-assignment"></a>할당을 사용하여 문자열 만들기

새 [String](xref:System.String) 개체를 만드는 가장 쉬운 방법은 [String](xref:System.String) 개체에 문자열 리터럴을 할당하는 것입니다. 

## <a name="creating-strings-using-a-class-constructor"></a>클래스 생성자를 사용하여 문자열 만들기

[String](xref:System.String) 클래스 생성자의 오버로드를 사용하여 문자 배열에서 문자열을 만들 수 있습니다. 특정 문자를 지정된 횟수만큼 복제하여 새 문자열을 만들 수도 있습니다. 

## <a name="methods-that-return-strings"></a>문자열을 반환하는 메서드

다음 표에서는 새 문자열 개체를 반환하는 여러 유용한 메서드를 보여 줍니다.

메서드 이름 | 기능
----------- | ---
[String.Format](xref:System.String.Format(System.String,System.Object)) | 입력 개체 집합에서 형식이 지정된 문자열을 작성합니다.
[String.Concat](xref:System.String.Concat(System.String,System.String)) | 둘 이상의 문자열에서 문자열을 작성합니다.
[String.Join](xref:System.String.Join(System.String,System.String[])) |문자열 배열을 결합하여 새 문자열을 작성합니다.
[String.Insert](xref:System.String.Insert(System.Int32,System.String)) | 기존 문자열의 지정된 인덱스에 문자열을 삽입하여 새 문자열을 작성합니다.
[String.CopyTo](xref:System.String.CopyTo(System.Int32,System.Char[],System.Int32,System.Int32)) | 문자열의 지정된 문자를 문자 배열의 지정된 위치에 복사합니다.

### <a name="format"></a>서식

`String.Format` 메서드를 사용하여 형식이 지정된 문자열을 만들고 여러 개체를 나타내는 문자열을 연결할 수 있습니다. 이 메서드는 전달된 모든 개체를 문자열로 자동으로 변환합니다. 예를 들어 응용 프로그램이 [Int32](xref:System.Int32) 값과 [DateTime](xref:System.DateTime) 값을 사용자에게 표시해야 하는 경우 `Format` 메서드를 사용하여 이러한 값을 나타내는 문자열을 쉽게 생성할 수 있습니다. 이 메서드에서 사용되는 형식 지정 규칙에 대한 자세한 내용은 [복합 형식 지정](composite-format.md) 섹션을 참조하세요.

다음 예제에서는 `Format` 메서드를 통해 정수 변수를 사용하는 문자열을 만듭니다.

```csharp
int numberOfFleas = 12;
string miscInfo = String.Format("Your dog has {0} fleas. " +
                                "It is time to get a flea collar. " + 
                                "The current universal date is: {1:u}.", 
                                numberOfFleas, DateTime.Now);
Console.WriteLine(miscInfo);
// The example displays the following output:
//       Your dog has 12 fleas. It is time to get a flea collar. 
//       The current universal date is: 2008-03-28 13:31:40Z.
```

```vb
Dim numberOfFleas As Integer = 12
Dim miscInfo As String = String.Format("Your dog has {0} fleas. " & _
                                       "It is time to get a flea collar. " & _ 
                                       "The current universal date is: {1:u}.", _ 
                                       numberOfFleas, Date.Now)
Console.WriteLine(miscInfo)
' The example displays the following output:
'       Your dog has 12 fleas. It is time to get a flea collar. 
'       The current universal date is: 2008-03-28 13:31:40Z.
```

이 예제에서 [DateTime.Now](xref:System.DateTime.Now)는 현재 스레드와 연결된 문화권에서 지정된 방식으로 현재 날짜와 시간을 표시합니다.

### <a name="concat"></a>Concat

`String.Concat` 메서드를 사용하여 둘 이상의 기존 개체에서 새 문자열 개체를 쉽게 만들 수 있습니다. 문자열을 연결하는 언어 독립적인 방법을 제공합니다. 이 메서드는 `System.Object`에서 파생된 모든 클래스를 허용합니다. 다음 예제에서는 두 개의 기존 문자열 개체와 구분 문자에서 문자열을 만듭니다.

```csharp
string helloString1 = "Hello";
string helloString2 = "World!";
Console.WriteLine(String.Concat(helloString1, ' ', helloString2));
// The example displays the following output:
//      Hello World!
```

```vb
Dim helloString1 As String = "Hello"
Dim helloString2 As String = "World!"
Console.WriteLine(String.Concat(helloString1, " "c, helloString2))
' The example displays the following output:
'      Hello World!
```

### <a name="join"></a>Join

`String.Join` 메서드는 문자열 배열과 구분 기호 문자열에서 새 문자열을 만듭니다. 이 메서드는 여러 문자열을 함께 연결하여 쉼표 등으로 구분된 목록을 만들려는 경우에 유용합니다.

다음 예제에서는 공백을 사용하여 문자열 배열을 바인딩합니다.

```csharp
string[] words = {"Hello", "and", "welcome", "to", "my" , "world!"};
Console.WriteLine(String.Join(" ", words));
// The example displays the following output:
//      Hello and welcome to my world!
```

```vb
Dim words() As String = {"Hello", "and", "welcome", "to", "my" , "world!"}
Console.WriteLine(String.Join(" ", words))
' The example displays the following output:
'      Hello and welcome to my world!
```

### <a name="insert"></a>Insert

`String.Insert` 메서드는 다른 문자열의 지정된 위치에 문자열을 삽입하여 새 문자열을 만듭니다. 이 메서드는 0부터 시작하는 인덱스를 사용합니다. 다음 예제에서는 `MyString`의 다섯 번째 인덱스 위치에 문자열을 삽입하고 이 값으로 새 문자열을 만듭니다.

```csharp
string sentence = "Once a time.";   
 Console.WriteLine(sentence.Insert(4, " upon"));
 // The example displays the following output:
 //      Once upon a time.
```

```vb
Dim sentence As String = "Once a time."   
 Console.WriteLine(sentence.Insert(4, " upon"))
 ' The example displays the 
```

### <a name="copyto"></a>CopyTo

`String.CopyTo` 메서드는 문자열의 일부를 문자 배열에 복사합니다. 기존 문자열의 시작 인덱스와 복사할 문자 수를 둘 다 지정할 수 있습니다. 이 메서드는 소스 인덱스, 문자 배열, 대상 인덱스 및 복사할 문자 수를 사용합니다. 모든 인덱스는 0부터 시작합니다.

다음 예제에서는 `CopyTo` 메서드를 사용하여 문자열 개체에서 "Hello" 단어의 문자를 문자 배열의 첫 번째 인덱스 위치에 복사합니다.

```csharp
string greeting = "Hello World!";
char[] charArray = {'W','h','e','r','e'};
Console.WriteLine("The original character array: {0}", new string(charArray));
greeting.CopyTo(0, charArray,0 ,5);
Console.WriteLine("The new character array: {0}", new string(charArray));
// The example displays the following output:
//       The original character array: Where
//       The new character array: Hello
```

```vb
Dim greeting As String = "Hello World!"
Dim charArray() As Char = {"W"c, "h"c, "e"c, "r"c, "e"c}
Console.WriteLine("The original character array: {0}", New String(charArray))
greeting.CopyTo(0, charArray,0 ,5)
Console.WriteLine("The new character array: {0}", New String(charArray))
' The example displays the following output:
'       The original character array: Where
'       The new character array: Hello
```

## <a name="see-also"></a>참고 항목

[기본적인 문자열 작업](basic-string-operations.md)

[복합 서식 지정](composite-format.md)




<!--HONumber=Nov16_HO1-->


