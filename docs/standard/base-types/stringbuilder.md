---
title: "StringBuilder 클래스 사용"
description: "StringBuilder 클래스 사용"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 07/26/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: f4f5d1c7-d84d-4867-810f-2708cd6de0da
translationtype: Human Translation
ms.sourcegitcommit: fb00da6505c9edb6a49d2003ae9bcb8e74c11d6c
ms.openlocfilehash: 2c42a4ac5fcd889eedea27b54b249f48c4848c88

---

# <a name="using-the-stringbuilder-class"></a>StringBuilder 클래스 사용

[String](xref:System.String) 개체는 변경할 수 없습니다. [System.String](xref:System.String) 클래스에서 메서드 중 하나를 사용할 때마다 메모리에서 새 문자열 개체가 만들어지므로, 해당 새 개체에 대한 공간이 새로 할당됩니다. 문자열을 반복적으로 수정해야 하는 경우 새 [String](xref:System.String) 개체 만들기와 연결된 오버헤드로 인해 비용이 증가할 수 있습니다. 새 개체를 만들지 않고 문자열을 수정하려는 경우 [System.Text.StringBuilder](xref:System.Text.StringBuilder) 클래스를 사용할 수 있습니다. 예를 들어 [StringBuilder](xref:System.Text.StringBuilder) 클래스를 사용하면 루프에서 많은 문자열을 연결할 때 성능이 향상될 수 있습니다.

## <a name="importing-the-systemtext-namespace"></a>System.Text 네임스페이스 가져오기

[StringBuilder](xref:System.Text.StringBuilder) 클래스는 [System.Text](xref:System.Text) 네임스페이스에서 발견됩니다. 코드에서 정규화된 형식 이름을 제공할 필요가 없도록 하려면 [System.Text](xref:System.Text) 네임스페이스를 가져올 수 있습니다. 

```csharp
using System;
using System.Text;
```

```vb
Imports System.Text
```

## <a name="instantiating-a-stringbuilder-object"></a>StringBuilder 개체 인스턴스화

다음 예제와 같이 오버로드된 생성자 메서드 중 하나로 변수를 초기화하여 [StringBuilder](xref:System.Text.StringBuilder) 클래스의 새 인스턴스를 만들 수 있습니다.

```csharp
StringBuilder MyStringBuilder = new StringBuilder("Hello World!");
```

```vb
Dim MyStringBuilder As New StringBuilder("Hello World!")
```

## <a name="setting-the-capacity-and-length"></a>용량 및 길이 설정

[StringBuilder](xref:System.Text.StringBuilder)는 캡슐화되는 문자열에서 문자 수를 확장할 수 있는 동적 개체이지만, 보관할 수 있는 최대 문자 수 값을 지정할 수 있습니다. 이 값을 개체의 용량이라고 하며, 현재 [StringBuilder](xref:System.Text.StringBuilder)에 보관된 문자열의 길이와 혼동하지 마세요. 예를 들어 길이가 5인 "Hello" 문자열을 사용하여 [StringBuilder](xref:System.Text.StringBuilder) 클래스의 새 인스턴스를 만들고 개체의 최대 용량을 25로 지정할 수 있습니다. [StringBuilder](xref:System.Text.StringBuilder)를 수정할 경우 용량에 도달할 때까지 자체 크기를 다시 할당할 수 없습니다. 이 경우 새 공간이 자동으로 할당되고 용량이 두 배로 증가합니다. 오버로드된 생성자 중 하나를 사용하여 [StringBuilder](xref:System.Text.StringBuilder) 클래스의 용량을 지정할 수 있습니다. 다음 예에서는 `MyStringBuilder` 개체를 최대 25개 공간으로 확장할 수 있도록 지정합니다.

```csharp
StringBuilder MyStringBuilder = new StringBuilder("Hello World!", 25);
```

```vb
Dim MyStringBuilder As New StringBuilder("Hello World!", 25) 
```

또한 읽기/쓰기 [Capacity](xref:System.Text.StringBuilder.Capacity) 속성을 사용하여 개체의 최대 길이를 설정할 수 있습니다. 다음 예에서는 [Capacity](xref:System.Text.StringBuilder.Capacity) 속성을 사용하여 최대 개체 길이를 정의합니다.

```csharp
MyStringBuilder.Capacity = 25;
```

```vb
MyStringBuilder.Capacity = 25
```

[EnsureCapacity](xref:System.Text.StringBuilder.EnsureCapacity(System.Int32)) 메서드를 사용하여 현재 [StringBuilder](xref:System.Text.StringBuilder)의 용량을 확인할 수 있습니다. 용량이 전달된 값보다 크면 변경되지 않고, 용량이 전달된 값보다 작으면 현재 용량이 전달된 값과 일치하도록 변경됩니다.

[Length](xref:System.Text.StringBuilder.Length) 속성을 보거나 설정할 수도 있습니다. [Length](xref:System.Text.StringBuilder.Length) 속성을 [Capacity](xref:System.Text.StringBuilder.Capacity) 속성보다 큰 값으로 설정하면 [Capacity](xref:System.Text.StringBuilder.Capacity) 속성이 [Length](xref:System.Text.StringBuilder.Length) 속성과 동일한 값으로 자동으로 변경됩니다. [Length](xref:System.Text.StringBuilder.Length) 속성을 현재 [StringBuilder](xref:System.Text.StringBuilder) 내의 문자열 길이보다 작은 값으로 설정하면 문자열이 단축됩니다.

## <a name="modifying-the-stringbuilder-string"></a>StringBuilder 문자열 수정

다음 표에서는 [StringBuilder](xref:System.Text.StringBuilder)의 내용을 수정하는 데 사용할 수 있는 메서드를 보여 줍니다.

메서드 이름 | 기능
----------- | ---
[StringBuilder.Append](xref:System.Text.StringBuilder.Append(System.Char)) | 현재 [StringBuilder](xref:System.Text.StringBuilder)의 끝에 정보를 추가합니다.
[StringBuilder.AppendFormat](xref:System.Text.StringBuilder.AppendFormat(System.IFormatProvider,System.String,System.Object)) | 문자열에 전달된 서식 지정자를 서식 있는 텍스트로 바꿉니다.
[StringBuilder.Insert](xref:System.Text.StringBuilder.Insert(System.Int32,System.Char)) | 현재 [StringBuilder](xref:System.Text.StringBuilder)의 지정된 인덱스에 문자열 또는 개체를 삽입합니다.
[StringBuilder.Remove](xref:System.Text.StringBuilder.Remove(System.Int32,System.Int32)) | 현재 [StringBuilder](xref:System.Text.StringBuilder)에서 지정된 수의 문자를 제거합니다.
[StringBuilder.Replace](xref:System.Text.StringBuilder.Replace(System.Char,System.Char)) | 지정된 인덱스에서 지정된 문자를 바꿉니다.

### <a name="append"></a>추가

[StringBuilder.Append](xref:System.Text.StringBuilder.Append(System.Char)) 메서드를 사용하여 현재 [StringBuilder](xref:System.Text.StringBuilder)에 표시되는 문자열의 끝에 개체의 문자열 표현이나 텍스트를 추가할 수 있습니다. 다음 예에서는 [StringBuilder](xref:System.Text.StringBuilder)를 "Hello World"로 초기화한 다음 개체의 끝에 일부 텍스트를 추가합니다. 필요한 경우 공간이 자동으로 할당됩니다.

```csharp
StringBuilder MyStringBuilder = new StringBuilder("Hello World!");
MyStringBuilder.Append(" What a beautiful day.");
Console.WriteLine(MyStringBuilder);
// The example displays the following output:
//       Hello World! What a beautiful day.
```

```vb
Dim MyStringBuilder As New StringBuilder("Hello World!")
MyStringBuilder.Append(" What a beautiful day.")
Console.WriteLine(MyStringBuilder)
' The example displays the following output:
'       Hello World! What a beautiful day.
```

### <a name="appendformat"></a>AppendFormat

[StringBuilder.AppendFormat](xref:System.Text.StringBuilder.AppendFormat(System.IFormatProvider,System.String,System.Object)) 메서드는 [StringBuilder](xref:System.Text.StringBuilder) 개체의 끝에 텍스트를 추가합니다. 서식 지정할 개체의 [IFormattable](xref:System.IFormattable) 구현을 호출하여 복합 서식 지정 기능을 지원합니다. 자세한 내용은 [복합 서식 지정](composite-format.md)을 참조하세요. 따라서 숫자, 날짜 및 시간, 열거형 값에 대한 표준 서식 문자열, 숫자, 날짜 및 시간 값에 대한 사용자 지정 서식 문자열, 사용자 지정 형식에 대해 정의된 서식 문자열을 허용합니다. 서식 지정에 대한 자세한 내용은 [서식 지정 형식](formatting-types.md)을 참조하세요. 이 메서드를 사용하여 변수의 서식을 사용자 지정하고 해당 값을 [StringBuilder](xref:System.Text.StringBuilder)에 추가할 수 있습니다. 다음 예에서는 [AppendFormat] 메서드를 사용하여 통화 값으로 서식 지정된 정수 값을 [StringBuilder](xref:System.Text.StringBuilder) 개체의 끝에 배치합니다.

```csharp
int MyInt = 25; 
StringBuilder MyStringBuilder = new StringBuilder("Your total is ");
MyStringBuilder.AppendFormat("{0:C} ", MyInt);
Console.WriteLine(MyStringBuilder);
// The example displays the following output:
//       Your total is $25.00
```

```vb
Dim MyInt As Integer = 25
Dim MyStringBuilder As New StringBuilder("Your total is ")
MyStringBuilder.AppendFormat("{0:C} ", MyInt)
Console.WriteLine(MyStringBuilder)
' The example displays the following output:
'     Your total is $25.00 
```

### <a name="insert"></a>Insert

[StringBuilder.Insert](xref:System.Text.StringBuilder.Insert(System.Int32,System.Char)) 메서드는 현재 [StringBuilder](xref:System.Text.StringBuilder) 개체의 지정된 위치에 문자열 또는 개체를 추가합니다. 다음 예에서는 이 메서드를 사용하여 [StringBuilder](xref:System.Text.StringBuilder) 개체의 여섯 번째 위치에 단어를 삽입합니다.

```csharp
StringBuilder MyStringBuilder = new StringBuilder("Hello World!");
MyStringBuilder.Insert(6,"Beautiful ");
Console.WriteLine(MyStringBuilder);
// The example displays the following output:
//       Hello Beautiful World!
```

```vb
Dim MyStringBuilder As New StringBuilder("Hello World!")
MyStringBuilder.Insert(6, "Beautiful ")
Console.WriteLine(MyStringBuilder)
' The example displays the following output:
'      Hello Beautiful World!
```

### <a name="remove"></a>제거

[StringBuilder.Remove](xref:System.Text.StringBuilder.Remove(System.Int32,System.Int32)) 메서드를 사용하여 지정된 0부터 시작하는 인덱스를 기준으로 현재 [StringBuilder](xref:System.Text.StringBuilder) 개체에서 지정된 수의 문자를 제거할 수 있습니다. 다음 예에서는 [Remove](xref:System.Text.StringBuilder.Remove(System.Int32,System.Int32)) 메서드를 사용하여 [StringBuilder](xref:System.Text.StringBuilder) 개체를 단축합니다.

```csharp
StringBuilder MyStringBuilder = new StringBuilder("Hello World!");
MyStringBuilder.Remove(5,7);
Console.WriteLine(MyStringBuilder);
// The example displays the following output:
//       Hello
```

```vb
Dim MyStringBuilder As New StringBuilder("Hello World!")
MyStringBuilder.Remove(5, 7)
Console.WriteLine(MyStringBuilder)
' The example displays the following output:
'       Hello
```

### <a name="replace"></a>바꾸기

[StringBuilder.Replace](xref:System.Text.StringBuilder.Replace(System.Char,System.Char)) | 지정된 인덱스에서 지정된 문자를 바꿉니다.
메서드는 [StringBuilder](xref:System.Text.StringBuilder) 개체 내의 문자를 다른 지정된 문자로 바꾸는 데 사용할 수 있습니다. 다음 예제에서는 [Replace](xref:System.Text.StringBuilder.Replace(System.Char,System.Char))를 사용합니다. | 지정된 인덱스에서 지정된 문자를 바꿉니다.
메서드는 [StringBuilder](xref:System.Text.StringBuilder) 개체에서 느낌표 문자(!)의 모든 인스턴스를 검색한 다음 물음표 문자(?)로 바꿉니다.
 
 ```csharp
 StringBuilder MyStringBuilder = new StringBuilder("Hello World!");
MyStringBuilder.Replace('!', '?');
Console.WriteLine(MyStringBuilder);
// The example displays the following output:
//       Hello World?
```

```vb
Dim MyStringBuilder As New StringBuilder("Hello World!")
MyStringBuilder.Replace("!"c, "?"c)
Console.WriteLine(MyStringBuilder)
' The example displays the following output:
'       Hello World?
```

## <a name="converting-a-stringbuilder-object-to-a-string"></a>StringBuilder 개체를 문자열로 변환

[StringBuilder](xref:System.Text.StringBuilder) 개체에서 표시하는 문자열을 [String](xref:System.String) 매개 변수를 가진 메서드에 전달하거나 사용자 인터페이스에 표시하려면 [StringBuilder](xref:System.Text.StringBuilder) 개체를 [String](xref:System.String) 개체로 변환해야 합니다. [StringBuilder.ToString](xref:System.Text.StringBuilder.ToString) 메서드를 호출하여 이 변환을 수행합니다. 다음 예에서는 다양한 [StringBuilder](xref:System.Text.StringBuilder) 메서드를 호출한 다음 [StringBuilder.ToString](xref:System.Text.StringBuilder.ToString) 메서드를 호출하여 문자열을 표시합니다.

```csharp
using System;
using System.Text;

public class Example
{
   public static void Main()
   {
      StringBuilder sb = new StringBuilder();
      bool flag = true;
      string[] spellings = { "recieve", "receeve", "receive" };
      sb.AppendFormat("Which of the following spellings is {0}:", flag);
      sb.AppendLine();
      for (int ctr = 0; ctr <= spellings.GetUpperBound(0); ctr++) {
         sb.AppendFormat("   {0}. {1}", ctr, spellings[ctr]);
         sb.AppendLine();
      }
      sb.AppendLine();
      Console.WriteLine(sb.ToString());
   }
}
// The example displays the following output:
//       Which of the following spellings is True:
//          0. recieve
//          1. receeve
//          2. receive
```

```vb
Imports System.Text

Module Example
   Public Sub Main()
      Dim sb As New StringBuilder()
      Dim flag As Boolean = True
      Dim spellings() As String = { "recieve", "receeve", "receive" }
      sb.AppendFormat("Which of the following spellings is {0}:", flag)
      sb.AppendLine()
      For ctr As Integer = 0 To spellings.GetUpperBound(0)
         sb.AppendFormat("   {0}. {1}", ctr, spellings(ctr))
         sb.AppendLine()
      Next
      sb.AppendLine()
      Console.WriteLine(sb.ToString())
   End Sub
End Module
' The example displays the following output:
'       Which of the following spellings is True:
'          0. recieve
'          1. receeve
'          2. receive
```

## <a name="see-also"></a>참고 항목

[System.Text.StringBuilder](xref:System.Text.StringBuilder)

[기본적인 문자열 작업](basic-string-operations.md)

[형식 서식 지정](formatting-types.md)



<!--HONumber=Nov16_HO3-->


