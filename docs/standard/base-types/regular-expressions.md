---
title: ".NET에서의 정규식"
description: ".NET에서의 정규식"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 07/26/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: d1a640cf-09ca-48f7-800c-a627a6d549c9
translationtype: Human Translation
ms.sourcegitcommit: fb00da6505c9edb6a49d2003ae9bcb8e74c11d6c
ms.openlocfilehash: 1fc1edd64c330fe579f389750432665ed982976e

---

# <a name="regular-expressions-in-net"></a>.NET에서의 정규식

정규식은 텍스트를 처리하는 강력하고 유연하며 효율적인 방법을 제공합니다. 정규식의 광범위한 패턴 일치 표기법을 사용하여 많은 양의 텍스트를 신속하게 구문 분석함으로써 특정 문자 패턴을 찾고, 텍스트의 유효성을 검사하여 해당 텍스트가 미리 정의된 패턴(예: 전자 메일 주소)과 일치하는지 확인하며, 텍스트 부분 문자열을 추출, 편집, 바꾸기 또는 삭제하고, 추출된 문자열을 컬렉션에 추가하여 보고서를 생성할 수 있습니다. 문자열을 처리하거나 텍스트의 큰 블록을 구문 분석하는 많은 응용 프로그램의 경우 정규식은 필수적인 도구입니다.

## <a name="how-regular-expressions-work"></a>정규식의 작동 방식

정규식을 사용하여 텍스트를 처리하는 중심에는 .NET에서 [System.Text.RegularExpressions.Regex](xref:System.Text.RegularExpressions.Regex) 개체로 표현되는 정규식 엔진이 있습니다. 정규식을 사용하여 텍스트를 처리하려면 최소한 정규식 엔진이 다음과 같은 두 가지 정보 항목과 함께 제공되어야 합니다.

* 텍스트에서 식별할 정규식 패턴 
  
  .NET에서 정규식 패턴은 특수 구문 또는 언어로 정의되며 Perl 5 정규식과 호환되고 오른쪽에서 왼쪽으로 일치와 같은 몇 가지 기능을 더 추가합니다. 자세한 내용은 [정규식 언어 - 빠른 참조](quick-ref.md)를 참조하세요.
  
* 정규식 패턴에 대해 구문 분석할 텍스트

[Regex](xref:System.Text.RegularExpressions.Regex) 클래스의 메서드를 사용하여 다음과 같은 작업을 수행할 수 있습니다.

* [Regex.IsMatch](xref:System.Text.RegularExpressions.Regex.IsMatch(System.String)) 메서드를 호출하여 입력 텍스트에서 정규식 패턴이 발생하는지를 확인합니다. 텍스트 유효성 검사에 [IsMatch](xref:System.Text.RegularExpressions.Regex.IsMatch(System.String)) 메서드를 사용하는 예제에 대해서는 [방법: 문자열이 올바른 전자 메일 서식인지 확인](verify-format.md)을 참조하세요.

* [Regex.Match](xref:System.Text.RegularExpressions.Regex.Match(System.String)) 또는 [Regex.Matches](xref:System.Text.RegularExpressions.Regex.Matches(System.String)) 메서드를 호출하여 정규식 패턴과 일치하는 텍스트를 하나 또는 모두 검색합니다. 전자 메서드는 일치하는 텍스트에 대한 정보를 제공하는 [System.Text.RegularExpressions.Match](xref:System.Text.RegularExpressions.Match) 개체를 반환합니다. 후자는 구문 분석된 텍스트에서 찾은 각 일치 항목의 [System.Text.RegularExpressions.Match](xref:System.Text.RegularExpressions.Match) 개체 하나를 포함하는 [MatchCollection](xref:System.Text.RegularExpressions.MatchCollection) 개체를 반환합니다. 

* [Regex.Replace](xref:System.Text.RegularExpressions.Regex.Replace(System.String,System.String)) 메서드를 호출하여 정규식 패턴과 일치하는 텍스트를 바꿉니다. [Replace](xref:System.Text.RegularExpressions.Regex.Replace(System.String,System.String)) 메서드를 사용하여 날짜 서식을 변경하고 문자열에서 잘못된 문자를 제거하는 예제는 [방법: 문자열에서 유효하지 않은 문자 제거](strip-characters.md) 및 [정규식 예제: 날짜 서식 변경](changing-formats.md)을 참조하세요.

정규식 개체 모델의 개요는 [정규식 개체 모델](object-model.md)을 참조하세요.

정규식 언어에 대한 자세한 내용은 [정규식 언어 - 빠른 참조](quick-ref.md)를 참조하세요.

## <a name="regular-expression-examples"></a>정규식 예제

[String](xref:System.String) 클래스에는 큰 문자열에서 리터럴 문자열을 찾을 때 사용할 수 있는 다양한 문자열 검색 및 바꾸기 메서드가 포함되어 있습니다. 정규식은 다음 예제에서 보여 주는 것처럼 큰 문자열에서 여러 부분 문자열 중 하나를 찾거나 문자열에서 패턴을 식별할 때 가장 유용합니다. 

### <a name="example-1-replacing-substrings"></a>예제 1: 부분 문자열 바꾸기

메일 그룹에 경우에 따라 호칭(Mr., Mrs., Miss 또는 Ms.)이 이름 및 성과 함께 포함되어 있는 이름이 들어 있다고 가정합니다. 메일 그룹에서 봉투 레이블을 생성할 때 호칭을 포함하지 않으려는 경우 다음 예제에서 보여 주는 것처럼 정규식을 사용하여 호칭을 제거할 수 있습니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = "(Mr\\.? |Mrs\\.? |Miss |Ms\\.? )";
      string[] names = { "Mr. Henry Hunt", "Ms. Sara Samuels", 
                         "Abraham Adams", "Ms. Nicole Norris" };
      foreach (string name in names)
         Console.WriteLine(Regex.Replace(name, pattern, String.Empty));
   }
}
// The example displays the following output:
//    Henry Hunt
//    Sara Samuels
//    Abraham Adams
//    Nicole Norris
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "(Mr\.? |Mrs\.? |Miss |Ms\.? )"
      Dim names() As String = { "Mr. Henry Hunt", "Ms. Sara Samuels", _
                                "Abraham Adams", "Ms. Nicole Norris" }
      For Each name As String In names
         Console.WriteLine(Regex.Replace(name, pattern, String.Empty))
      Next                                
   End Sub
End Module
' The example displays the following output:
'    Henry Hunt
'    Sara Samuels
'    Abraham Adams
'    Nicole Norris
```

정규식 패턴 `(Mr\.? |Mrs\.? |Miss |Ms\.? )`는 모든 "Mr", "Mr.", "Mrs", "Mrs.", "Miss", "Ms" 또는 "Ms."가 발생하는 것과 일치합니다. [Regex.Replace](xref:System.Text.RegularExpressions.Regex.Replace(System.String,System.String)) 메서드에 대한 호출은 일치하는 문자열을 [String.Empty](xref:System.String.Empty)로 바꿉니다. 즉, 원래 문자열에서 일치하는 문자열을 제거합니다.

### <a name="example-2-identifying-duplicated-words"></a>예제 2: 중복된 단어 식별

실수로 단어를 중복하는 것은 작성자가 흔히 하는 실수입니다. 다음 예제에서 보여 주는 것처럼 정규식을 사용하여 중복된 단어를 식별할 수 있습니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Class1
{
   public static void Main()
   {
      string pattern = @"\b(\w+?)\s\1\b";
      string input = "This this is a nice day. What about this? This tastes good. I saw a a dog.";
      foreach (Match match in Regex.Matches(input, pattern, RegexOptions.IgnoreCase))
         Console.WriteLine("{0} (duplicates '{1}') at position {2}", 
                           match.Value, match.Groups[1].Value, match.Index);
   }
}
// The example displays the following output:
//       This this (duplicates 'This)' at position 0
//       a a (duplicates 'a)' at position 66
```

```vb
Imports System.Text.RegularExpressions

Module modMain
   Public Sub Main()
      Dim pattern As String = "\b(\w+?)\s\1\b"
      Dim input As String = "This this is a nice day. What about this? This tastes good. I saw a a dog."
      For Each match As Match In Regex.Matches(input, pattern, RegexOptions.IgnoreCase)
         Console.WriteLine("{0} (duplicates '{1}') at position {2}", _
                           match.Value, match.Groups(1).Value, match.Index)
      Next
   End Sub
End Module
' The example displays the following output:
'       This this (duplicates 'This)' at position 0
'       a a (duplicates 'a)' at position 66
```

정규식 패턴 `\b(\w+?)\s\1\b`는 다음과 같이 해석될 수 있습니다.

구문 | 의미
------ | -------
`\b` | 단어 경계를 시작합니다.
`(\w+?)` | 하나 이상의 단어 문자(가능한 한 적은 문자)를 찾습니다. 이러한 단어 문자는 함께 `\1`이라고 할 수 있는 그룹을 형성합니다.
`\s` | 공백 문자를 찾습니다.
`\1` | `\1`이라는 그룹과 같은 부분 문자열을 찾습니다.
`\b` | 단어 경계를 찾습니다.

[Regex.Matches](xref:System.Text.RegularExpressions.Regex.Matches(System.String,System.String,System.Text.RegularExpressions.RegexOptions)) 메서드는 [RegexOptions.IgnoreCase](xref:System.Text.RegularExpressions.RegexOptions.IgnoreCase)로 설정된 정규식 옵션을 사용하여 호출됩니다. 따라서 찾기 작업은 대/소문자를 구분하지 않으며, 이 예제에서는 부분 문자열 "This this"를 중복으로 식별합니다.

입력 문자열에 부분 문자열 "this? This"가 포함되어 있습니다. 그러나 문장 부호가 중간에 있어 이는 중복으로 식별되지 않습니다.

### <a name="example-3-dynamically-building-a-culturesensitive-regular-expression"></a>예제 3: 동적으로 문화권 구분 정규식 작성

다음 예제에서는 정규식이 .NET의 전역화 기능에서 제공하는 유연성과 결합되었을 때의 성능을 설명합니다. 이 예제에서는 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 개체를 사용하여 시스템의 현재 문화권의 통화 값 서식을 확인합니다. 그런 다음 해당 정보를 사용하여 텍스트에서 통화 값을 추출하는 정규식을 동적으로 구성합니다. 각 일치 항목에 대해 숫자 문자열만 포함된 하위 그룹을 추출하여 [Decimal](xref:System.Decimal) 값으로 변환하고 누계를 계산합니다. 

```csharp
using System;
using System.Collections.Generic;
using System.Globalization;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      // Define text to be parsed.
      string input = "Office expenses on 2/13/2008:\n" + 
                     "Paper (500 sheets)                      $3.95\n" + 
                     "Pencils (box of 10)                     $1.00\n" + 
                     "Pens (box of 10)                        $4.49\n" + 
                     "Erasers                                 $2.19\n" + 
                     "Ink jet printer                        $69.95\n\n" + 
                     "Total Expenses                        $ 81.58\n"; 

      // Get current culture's NumberFormatInfo object.
      NumberFormatInfo nfi = CultureInfo.CurrentCulture.NumberFormat;
      // Assign needed property values to variables.
      string currencySymbol = nfi.CurrencySymbol;
      bool symbolPrecedesIfPositive = nfi.CurrencyPositivePattern % 2 == 0;
      string groupSeparator = nfi.CurrencyGroupSeparator;
      string decimalSeparator = nfi.CurrencyDecimalSeparator;

      // Form regular expression pattern.
      string pattern = Regex.Escape( symbolPrecedesIfPositive ? currencySymbol : "") + 
                       @"\s*[-+]?" + "([0-9]{0,3}(" + groupSeparator + "[0-9]{3})*(" + 
                       Regex.Escape(decimalSeparator) + "[0-9]+)?)" + 
                       (! symbolPrecedesIfPositive ? currencySymbol : ""); 
      Console.WriteLine( "The regular expression pattern is:");
      Console.WriteLine("   " + pattern);      

      // Get text that matches regular expression pattern.
      MatchCollection matches = Regex.Matches(input, pattern, 
                                              RegexOptions.IgnorePatternWhitespace);               
      Console.WriteLine("Found {0} matches.", matches.Count); 

      // Get numeric string, convert it to a value, and add it to List object.
      List<decimal> expenses = new List<Decimal>();

      foreach (Match match in matches)
         expenses.Add(Decimal.Parse(match.Groups[1].Value));      

      // Determine whether total is present and if present, whether it is correct.
      decimal total = 0;
      foreach (decimal value in expenses)
         total += value;

      if (total / 2 == expenses[expenses.Count - 1]) 
         Console.WriteLine("The expenses total {0:C2}.", expenses[expenses.Count - 1]);
      else
         Console.WriteLine("The expenses total {0:C2}.", total);
   }  
}
// The example displays the following output:
//       The regular expression pattern is:
//          \$\s*[-+]?([0-9]{0,3}(,[0-9]{3})*(\.[0-9]+)?)
//       Found 6 matches.
//       The expenses total $81.58.
```

```vb
Imports System.Collections.Generic
Imports System.Globalization
Imports System.Text.RegularExpressions

Public Module Example
   Public Sub Main()
      ' Define text to be parsed.
      Dim input As String = "Office expenses on 2/13/2008:" + vbCrLf + _
                            "Paper (500 sheets)                      $3.95" + vbCrLf + _
                            "Pencils (box of 10)                     $1.00" + vbCrLf + _
                            "Pens (box of 10)                        $4.49" + vbCrLf + _
                            "Erasers                                 $2.19" + vbCrLf + _
                            "Ink jet printer                        $69.95" + vbCrLf + vbCrLf + _
                            "Total Expenses                        $ 81.58" + vbCrLf
      ' Get current culture's NumberFormatInfo object.
      Dim nfi As NumberFormatInfo = CultureInfo.CurrentCulture.NumberFormat
      ' Assign needed property values to variables.
      Dim currencySymbol As String = nfi.CurrencySymbol
      Dim symbolPrecedesIfPositive As Boolean = CBool(nfi.CurrencyPositivePattern Mod 2 = 0)
      Dim groupSeparator As String = nfi.CurrencyGroupSeparator
      Dim decimalSeparator As String = nfi.CurrencyDecimalSeparator

      ' Form regular expression pattern.
      Dim pattern As String = Regex.Escape(CStr(IIf(symbolPrecedesIfPositive, currencySymbol, ""))) + _
                              "\s*[-+]?" + "([0-9]{0,3}(" + groupSeparator + "[0-9]{3})*(" + _
                              Regex.Escape(decimalSeparator) + "[0-9]+)?)" + _
                              CStr(IIf(Not symbolPrecedesIfPositive, currencySymbol, "")) 
      Console.WriteLine("The regular expression pattern is: ")
      Console.WriteLine("   " + pattern)      

      ' Get text that matches regular expression pattern.
      Dim matches As MatchCollection = Regex.Matches(input, pattern, RegexOptions.IgnorePatternWhitespace)               
      Console.WriteLine("Found {0} matches. ", matches.Count)

      ' Get numeric string, convert it to a value, and add it to List object.
      Dim expenses As New List(Of Decimal)

      For Each match As Match In matches
         expenses.Add(Decimal.Parse(match.Groups.Item(1).Value))      
      Next

      ' Determine whether total is present and if present, whether it is correct.
      Dim total As Decimal
      For Each value As Decimal In expenses
         total += value
      Next

      If total / 2 = expenses(expenses.Count - 1) Then
         Console.WriteLine("The expenses total {0:C2}.", expenses(expenses.Count - 1))
      Else
         Console.WriteLine("The expenses total {0:C2}.", total)
      End If   
   End Sub
End Module
' The example displays the following output:
'       The regular expression pattern is:
'          \$\s*[-+]?([0-9]{0,3}(,[0-9]{3})*(\.[0-9]+)?)
'       Found 6 matches.
'       The expenses total $81.58.
```

현재 문화권이 영어 - 미국(en-US)인 컴퓨터에서 이 예제를 실행할 경우 정규식 `\$\s*[-+]?([0-9]{0,3}(,[0-9]{3})*(\.[0-9]+)?)`가 동적으로 작성됩니다. 이 정규식 패턴은 다음과 같이 해석될 수 있습니다.

구문 | 의미
------ | -------
`\$` | 입력 문자열에서 단일 달러 기호($)를 찾습니다. 정규식 패턴 문자열에는 백슬래시가 포함되어 달러 기호가 정규식 앵커로 해석되는 것이 아니라 리터럴로 해석될 것임을 나타냅니다. ($ 기호 단독으로는 정규식 엔진이 문자열의 끝 부분에서 찾기를 시작하려고 해야 한다는 사실을 나타냅니다.) 현재 문화권의 통화 기호가 정규식 기호로 잘못 해석되지 않도록 하기 위해 이 예제에서는 [Escape](xref:System.Text.RegularExpressions.Regex.Escape(System.String)) 메서드를 호출하여 문자를 이스케이프합니다.
`\s*` | 0개 이상의 공백 문자를 찾습니다.
`[-+]?` | 0개 이상의 더하기 기호 또는 빼기 기호를 찾습니다.
`([0-9]{0,3}(,[0-9]{3})*(\.[0-9]+)?)` | 이 식을 둘러싼 바깥쪽 괄호는 이 식을 캡처링 그룹 또는 하위 식으로 정의합니다. 일치 항목을 찾은 경우 일치하는 문자열의 이 부분에 대한 정보는 [Match.Groups](xref:System.Text.RegularExpressions.Match.Groups) 속성에서 반환하는 [GroupCollection](xref:System.Text.RegularExpressions.GroupCollection) 개체의 두 번째 [Group](xref:System.Text.RegularExpressions.Group) 개체에서 검색할 수 있습니다. (컬렉션의 첫 번째 요소는 전체 일치를 나타냅니다.)
`[0-9]{0,3}` | 10진수 0-9를 0~3개 찾습니다.
`(,[0-9]{3})*` | 그룹 구분 기호 하나 다음에 세 개의 10진수가 있는 0개 이상의 일치 항목을 찾습니다.
`\.` | 단일 소수 구분 기호를 찾습니다.
`[0-9]+` | 하나 이상의 10진수를 찾습니다.
`(\.[0-9]+)?` | 소수 구분 기호 다음에 하나 이상의 10진수가 있는 0개 이상의 일치 항목을 찾습니다.

## <a name="related-topics"></a>관련 항목

제목 | 설명
----- | -----------
[정규식 언어 - 빠른 참조](quick-ref.md) | 정규식을 정의하는 데 사용할 수 있는 문자, 연산자 및 생성자 집합에 대한 정보를 제공합니다.
[정규식 개체 모델](object-model.md) | 정규식 클래스를 사용하는 방법을 보여 주는 코드 예제 및 정보를 제공합니다.
[정규식 동작 정보](regex-behavior.md) | .NET 정규식의 기능 및 동작에 대한 정보를 제공합니다.
[정규식 예제](regex-examples.md) | 정규식의 일반적인 사용을 보여 주는 코드 예제를 제공합니다.


## <a name="reference"></a>참조

[System.Text.RegularExpressions](xref:System.Text.RegularExpressions)

[System.Text.RegularExpressions.Regex](xref:System.Text.RegularExpressions.Regex)




<!--HONumber=Nov16_HO3-->


