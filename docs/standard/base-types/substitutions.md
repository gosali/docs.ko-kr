---
title: "정규식의 대체"
description: "정규식의 대체"
keywords: .NET, .NET Core
author: stevehoag
ms.author: shoag
manager: wpickett
ms.date: 07/29/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 0fded615-1021-4468-a644-b491814305c6
translationtype: Human Translation
ms.sourcegitcommit: b20713600d7c3ddc31be5885733a1e8910ede8c6
ms.openlocfilehash: 4b2b547b6edd67590ad75851b8b287e55dc7d70c

---

# <a name="substitutions-in-regular-expressions"></a>정규식의 대체

대체는 바꾸기 패턴에서만 인식되는 언어 요소입니다. 정규식 패턴을 사용하여 입력 문자열에서 일치하는 텍스트를 바꿀 텍스트의 전부 또는 일부를 정의합니다. 바꾸기 패턴은 리터럴 문자와 하나 이상의 대체로 구성될 수 있습니다. *replacement* 매개 변수가 포함된 [Regex.Replace](xref:System.Text.RegularExpressions.Regex.Replace(System.String,System.String,System.String,System.Text.RegularExpressions.RegexOptions)) 메서드의 오버로드와 [Match.Result](xref:System.Text.RegularExpressions.Match.Result(System.String)) 메서드에 대해 바꾸기 패턴이 제공됩니다. 해당 메서드는 일치하는 패턴을 *replacement* 매개 변수에 정의된 패턴으로 바꿉니다.

.NET은 다음 테이블에 나와 있는 대체 요소를 정의합니다.

대체 | 설명
------------ | ----------- 
**$**_number_ | *number*(여기서 *number*는 10진수 값)로 식별되는 캡처 그룹과 일치하는 마지막 부분 문자열을 바꾸기 문자열에 포함합니다. 자세한 내용은 [번호가 매겨진 그룹 대체](#substituting-a-numbered-group)를 참조하세요.
**${**_name_**}** | **(?<**_name_**>)**에서 지정한 명명된 그룹과 일치하는 마지막 부분 문자열을 바꾸기 문자열에 포함합니다. 자세한 내용은 [명명된 그룹 대체](#substituting-a-named-group)를 참조하세요.
**$$** | 대체 문자열에 한 개의 "$" 리터럴을 포함합니다. 자세한 내용은 ["$" 문자 대체](#substituting-a--character)를 참조하세요.
**$&** | 대체 문자열에 일치하는 전체 문자열의 복사본을 포함합니다. 자세한 내용은 [일치하는 전체 문자열 대체](#substituting-the-entire-match)를 참조하세요.
**$`** | 대체 문자열에서 일치하는 문자열 앞에 입력 문자열의 모든 텍스트를 포함합니다. 자세한 내용은 [일치하는 문자열 앞에 있는 텍스트 대체](#substituting-the-text-before-the-match)를 참조하세요.
**$'** | 대체 문자열에서 일치하는 문자열 뒤에 입력 문자열의 모든 텍스트를 포함합니다. 자세한 내용은 [일치하는 문자열 뒤에 있는 텍스트 대체](#substituting-the-text-after-the-match)를 참조하세요. 
**$+** | 대체 문자열에 캡처된 마지막 그룹을 포함합니다. 자세한 내용은 [캡처된 마지막 그룹 대체](#substituting-the-last-captured-group)를 참조하세요.
**$_** | 대체 문자열에 전체 입력 문자열을 포함합니다. 자세한 내용은 [전체 입력 문자열 대체](#substituting-the-entire-input-string)를 참조하세요.
 
## <a name="substitution-elements-and-replacement-patterns"></a>대체 요소 및 바꾸기 패턴

대체는 바꾸기 패턴에서 인식되는 유일한 특수 구문입니다. 임의의 문자를 찾는 문자 이스케이프 및 마침표(**.**)를 비롯한 다른 정규식 언어 요소는 지원되지 않습니다. 마찬가지로, 대체 언어 요소는 바꾸기 패턴에서만 인식되고 정규식 패턴에는 사용할 수 없습니다. 

컨텍스트마다 다른 의미를 가지지만 정규식 패턴이나 대체에 표시할 수 있는 유일한 문자는 **$** 문자입니다. 정규식 패턴에서 **$**는 문자열의 끝 부분을 찾는 앵커입니다. 바꾸기 패턴에서 **$**는 대체 시작을 나타냅니다. 

> [!NOTE]
> 정규식의 바꾸기 패턴과 유사한 기능으로, 역참조를 사용합니다. 역참조에 대한 자세한 내용은 [역참조 구문](backreference.md)을 참조하세요.
 
## <a name="substituting-a-numbered-group"></a>번호가 매겨진 그룹 대체

**$**_number_ 언어 요소는 바꾸기 문자열에서 number 캡처 그룹과 일치하는 마지막 부분 문자열을 포함합니다. 여기서 *number*는 캡처 그룹의 인덱스입니다. 예를 들어 바꾸기 패턴 `$1`은 일치하는 부분 문자열이 처음 캡처한 그룹으로 대체됨을 나타냅니다. 번호가 매겨진 캡처 그룹에 대한 자세한 내용은[정규식의 그룹화 구문](grouping.md)을 참조하세요.

**$** 다음에 나오는 모든 숫자는 number 그룹에 속한 것으로 해석됩니다. 이런 것을 의도한 것이 아니라면, 그 대신 명명된 그룹을 대체할 수 있습니다. 예를 들어, **$11** 대신 **${1}1** 대체 문자열을 사용하여 대체 문자열을 숫자 "1"과 함께 처음으로 캡처된 그룹의 값으로 정의할 수 있습니다. 자세한 내용은 [명명된 그룹 대체](#substituting-a-named-group)를 참조하세요. 

**(?<**_name-**>)** 구문을 사용하여 명시적으로 이름을 할당하지 않은 그룹을 캡처할 때는 1부터 시작하여 왼쪽에서 오른쪽으로 번호를 매깁니다. 명명된 그룹도 명명되지 않은 마지막 그룹의 인덱스보다 큰 1부터 시작하여 왼쪽에서 오른쪽으로 번호가 매겨집니다. 예를 들어 정규식 `(\w)(?<digit>\d)`에서 `digit` 명명된 그룹의 인덱스는 2입니다.

*number*가 정규식 패턴에 정의된 올바른 캡처 그룹을 지정하지 않으면 **$**_number_는 일치하는 문자열을 바꾸는 데 사용되는 리터럴 문자 시퀀스로 해석됩니다.

다음 예제에서는 **$**_number_ 대체를 사용하여 10진수 값에서 통화 기호를 삭제합니다. 통화 값의 시작 부분이나 끝 부분에 있는 통화 기호를 제거하고 두 개의 가장 일반적인 소수 구분 기호("." 및 ",")를 인식합니다. 

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"\p{Sc}*(\s?\d+[.,]?\d*)\p{Sc}*";
      string replacement = "$1";
      string input = "$16.32 12.19 £16.29 €18.29  €18,29";
      string result = Regex.Replace(input, pattern, replacement);
      Console.WriteLine(result);
   }
}
// The example displays the following output:
//       16.32 12.19 16.29 18.29  18,29
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "\p{Sc}*(\s?\d+[.,]?\d*)\p{Sc}*"
      Dim replacement As String = "$1"
      Dim input As String = "$16.32 12.19 £16.29 €18.29  €18,29"
      Dim result As String = Regex.Replace(input, pattern, replacement)
      Console.WriteLine(result)
   End Sub
End Module
' The example displays the following output:
'       16.32 12.19 16.29 18.29  18,29
```

정규식 패턴 `\p{Sc}*(\s?\d+[.,]?\d*)\p{Sc}*`는 다음 테이블과 같이 정의됩니다.

패턴 | 설명
------- | -----------
`\p{Sc}*` | 통화 기호 문자를 0개 이상 찾습니다.
`\s?` | 0회 이상 나오는 공백 문자를 찾습니다.
`\d+` | 하나 이상의 10진수 숫자가 일치하는지 확인합니다.
`[.,]?` | 0-1개의 마침표 또는 쉼표를 찾습니다.
`\d*` | 0번 이상 나오는 10진수를 찾습니다.
`(\s?\d+[.,]?\d*)` | 공백, 하나 이상의 10진수 숫자, 0-1개의 마침표나 쉼표, 0-1개의 10진수 숫자가 차례로 표시된 문자열을 찾습니다. 이 그룹은 첫 번째 캡처링 그룹입니다. 바꾸기 패턴이 `$1`이기 때문에 [Regex.Replace](xref:System.Text.RegularExpressions.Regex.Replace(System.String,System.String,System.String,System.Text.RegularExpressions.RegexOptions)) 메서드를 호출하면 일치하는 전체 부분 문자열이 이 캡처된 그룹으로 바뀝니다. 
 
## <a name="substituting-a-named-group"></a>명명된 그룹 대체

**${**_name_**}** 언어 요소는 *name* 캡처 그룹과 일치하는 마지막 부분 문자열을 대체합니다. 여기서 *name*은 **(?<**_name_**>)** 언어 요소가 정의한 캡처 그룹의 이름입니다. 명명된 캡처 그룹에 대한 자세한 내용은 [정규식의 그룹화 구문](grouping.md)을 참조하세요.

*name*이 정규식 패턴에 정의된 명명된 유효한 캡처 그룹을 지정하지 않지만 숫자로 구성된 경우, **${**_name_**}**은 번호가 매겨진 그룹으로 해석됩니다. 

*name*이 명명된 유효한 캡처 그룹도, 정규식 패턴에 정의되어 번호가 매겨진 유효한 캡처 그룹도 지정하지 않는 경우, **${**_name_**}**은 각 일치 항목을 바꾸는 데 사용되는 리터럴 문자 시퀀스로 해석됩니다.

다음 예제에서는 **${**_name_**}** 대체를 사용하여 10진수 값에서 통화 기호를 삭제합니다. 통화 값의 시작 부분이나 끝 부분에 있는 통화 기호를 제거하고 두 개의 가장 일반적인 소수 구분 기호("." 및 ",")를 인식합니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"\p{Sc}*(?<amount>\s?\d+[.,]?\d*)\p{Sc}*";
      string replacement = "${amount}";
      string input = "$16.32 12.19 £16.29 €18.29  €18,29";
      string result = Regex.Replace(input, pattern, replacement);
      Console.WriteLine(result);
   }
}
// The example displays the following output:
//       16.32 12.19 16.29 18.29  18,29
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "\p{Sc}*(?<amount>\s?\d+[.,]?\d*)\p{Sc}*"
      Dim replacement As String = "${amount}"
      Dim input As String = "$16.32 12.19 £16.29 €18.29  €18,29"
      Dim result As String = Regex.Replace(input, pattern, replacement)
      Console.WriteLine(result)
   End Sub
End Module
' The example displays the following output:
'       16.32 12.19 16.29 18.29  18,29
```

정규식 패턴 `\p{Sc}*(?<amount>\s?\d[.,]?\d*)\p{Sc}*`는 다음 테이블과 같이 정의됩니다. 

패턴 | 설명
------- | -----------
`\p{Sc}*` | 통화 기호 문자를 0개 이상 찾습니다.
`\s?` | 0회 이상 나오는 공백 문자를 찾습니다.
`\d+` | 하나 이상의 10진수 숫자가 일치하는지 확인합니다.
`[.,]?` | 0-1개의 마침표 또는 쉼표를 찾습니다.
`\d*` | 0번 이상 나오는 10진수를 찾습니다.
`(?<amount>\s?\d[.,]?\d*)` | 공백, 하나 이상의 10진수 숫자, 0-1개의 마침표나 쉼표, 0-1개의 10진수 숫자가 차례로 표시된 문자열을 찾습니다. 이는 캡처링 그룹이 명명한 양입니다. 바꾸기 패턴이 `${amount}`이기 때문에 [Regex.Replace](xref:System.Text.RegularExpressions.Regex.Replace(System.String,System.String,System.String,System.Text.RegularExpressions.RegexOptions)) 메서드를 호출하면 일치하는 전체 부분 문자열이 이 캡처된 그룹으로 바뀝니다. 
 
## <a name="substituting-a-character"></a>$ 문자 대체

**$$** 대체는 바꾼 문자열에 리터럴 "$" 문자를 삽입합니다. 

다음 예제에서는 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 개체를 사용하여 통화 문자열에서 현재 문화권의 통화 기호 및 배치를 확인합니다. 그런 다음 정규식 패턴과 바꾸기 패턴을 동적으로 작성합니다. 현재 문화권이 en-US인 컴퓨터에서 예제가 실행될 경우, 정규식 패턴 `\b(\d+)(\.(\d+))?` 및 바꾸기 패턴 `$$ $1$2`를 생성합니다. 바꾸기 패턴은 일치하는 텍스트를 통화 기호와 공백, 첫 번째 및 두 번째 캡처된 그룹이 차례로 표시된 문자열로 바꿉니다.

```csharp
using System;
using System.Globalization;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      // Define array of decimal values.
      string[] values= { "16.35", "19.72", "1234", "0.99"};
      // Determine whether currency precedes (True) or follows (False) number.
      bool precedes = NumberFormatInfo.CurrentInfo.CurrencyPositivePattern % 2 == 0;
      // Get decimal separator.
      string cSeparator = NumberFormatInfo.CurrentInfo.CurrencyDecimalSeparator;
      // Get currency symbol.
      string symbol = NumberFormatInfo.CurrentInfo.CurrencySymbol;
      // If symbol is a "$", add an extra "$".
      if (symbol == "$") symbol = "$$";

      // Define regular expression pattern and replacement string.
      string pattern = @"\b(\d+)(" + cSeparator + @"(\d+))?"; 
      string replacement = "$1$2";
      replacement = precedes ? symbol + " " + replacement : replacement + " " + symbol;
      foreach (string value in values)
         Console.WriteLine("{0} --> {1}", value, Regex.Replace(value, pattern, replacement));
   }
}
// The example displays the following output:
//       16.35 --> $ 16.35
//       19.72 --> $ 19.72
//       1234 --> $ 1234
//       0.99 --> $ 0.99
```

```vb
Imports System.Globalization
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      ' Define array of decimal values.
      Dim values() As String = { "16.35", "19.72", "1234", "0.99"}
      ' Determine whether currency precedes (True) or follows (False) number.
      Dim precedes As Boolean = (NumberFormatInfo.CurrentInfo.CurrencyPositivePattern Mod 2 = 0)
      ' Get decimal separator.
      Dim cSeparator As String = NumberFormatInfo.CurrentInfo.CurrencyDecimalSeparator
      ' Get currency symbol.
      Dim symbol As String = NumberFormatInfo.CurrentInfo.CurrencySymbol
      ' If symbol is a "$", add an extra "$".
      If symbol = "$" Then symbol = "$$"

      ' Define regular expression pattern and replacement string.
      Dim pattern As String = "\b(\d+)(" + cSeparator + "(\d+))?" 
      Dim replacement As String = "$1$2"
      replacement = If(precedes, symbol + " " + replacement, replacement + " " + symbol)
      For Each value In values
         Console.WriteLine("{0} --> {1}", value, Regex.Replace(value, pattern, replacement))
      Next
   End Sub
End Module
' The example displays the following output:
'       16.35 --> $ 16.35
'       19.72 --> $ 19.72
'       1234 --> $ 1234
'       0.99 --> $ 0.99
```

정규식 패턴 `\b(\d+)(\.(\d+))?`는 다음 테이블과 같이 정의됩니다.

패턴 | 설명
------- | -----------
`\b` | 단어 경계 시작 부분에서 찾기를 시작합니다.
`(\d+)` | 하나 이상의 10진수 숫자가 일치하는지 확인합니다. 이 그룹은 첫 번째 캡처링 그룹입니다.
`\.` | 마침표(소수 구분 기호)를 찾습니다.
`(\d+)` | 하나 이상의 10진수 숫자가 일치하는지 확인합니다. 이 그룹은 세 번째 캡처링 그룹입니다.
`(\.(\d+))?` | 한 개 이상의 숫자가 뒤에 오는 마침표를 0개 또는 1개 찾습니다. 이 그룹은 두 번째 캡처링 그룹입니다.
 
## <a name="substituting-the-entire-match"></a>일치하는 전체 문자열 대체

**$&** 대체는 바꾸기 문자열에 일치하는 전체 문자열을 포함합니다. 일치하는 문자열의 시작 부분이나 끝 부분에 부분 문자열을 추가하는 데 자주 사용됩니다. 예를 들어 `($&)` 바꾸기 패턴은 각 일치 항목의 시작과 끝에 괄호를 추가합니다. 일치하는 부분이 없으면 **$&** 대체가 아무 효과가 없습니다.

다음 예제에서는 **$&** 대체를 사용하여 문자열 배열에 저장된 책 제목의 시작 부분과 끝 부분에 따옴표를 추가합니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"^(\w+\s?)+$";
      string[] titles = { "A Tale of Two Cities", 
                          "The Hound of the Baskervilles", 
                          "The Protestant Ethic and the Spirit of Capitalism", 
                          "The Origin of Species" };
      string replacement = "\"$&\"";
      foreach (string title in titles)
         Console.WriteLine(Regex.Replace(title, pattern, replacement));
   }
}
// The example displays the following output:
//       "A Tale of Two Cities"
//       "The Hound of the Baskervilles"
//       "The Protestant Ethic and the Spirit of Capitalism"
//       "The Origin of Species"
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "^(\w+\s?)+$"
      Dim titles() As String = { "A Tale of Two Cities", _
                                 "The Hound of the Baskervilles", _
                                 "The Protestant Ethic and the Spirit of Capitalism", _
                                 "The Origin of Species" }
      Dim replacement As String = """$&"""
      For Each title As String In titles
         Console.WriteLine(Regex.Replace(title, pattern, replacement))
      Next  
   End Sub
End Module
' The example displays the following output:
'       "A Tale of Two Cities"
'       "The Hound of the Baskervilles"
'       "The Protestant Ethic and the Spirit of Capitalism"
'       "The Origin of Species"
```

정규식 패턴 `^(\w+\s?)+$`는 다음 테이블과 같이 정의됩니다.

패턴 | 설명
------- | -----------
`^` | 입력 문자열의 시작 부분에서 일치 항목 찾기를 시작합니다.
`(\w+\s?)+` | 하나 이상의 단어 문자 다음에 0개 또는 1개의 공백 문자가 한 번 이상 나타나는 패턴을 찾습니다. 
`$` | 입력 문자열의 끝 부분을 찾습니다.

`"$&"` 바꾸기 패턴은 일치하는 문자열의 시작 부분과 끝 부분에 리터럴 따옴표를 추가합니다.

## <a name="substituting-the-text-before-the-match"></a>일치하는 문자열 앞에 있는 텍스트 대체

**$`** substitution replaces the matched string with the entire input string before the match. That is, it duplicates the input string up to the match while removing the matched text. Any text that follows the matched text is unchanged in the result string. If there are multiple matches in an input string, the replacement text is derived from the original input string, rather than from the string in which text has been replaced by earlier matches. (The example provides an illustration.) If there is no match, the **$`** 대체는 아무 효과가 없습니다.

다음 예제에서는 `\d+` 정규식 패턴을 사용하여 입력 문자열에서 하나 이상의 숫자가 연속적으로 표시된 부분을 찾습니다. 바꾸기 문자열 **$`**는 이 숫자를 일치하는 문자열 앞의 텍스트로 바꿉니다. 

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string input = "aa1bb2cc3dd4ee5";
      string pattern = @"\d+";
      string substitution = "$`";
      Console.WriteLine("Matches:");
      foreach (Match match in Regex.Matches(input, pattern))
         Console.WriteLine("   {0} at position {1}", match.Value, match.Index);

      Console.WriteLine("Input string:  {0}", input);
      Console.WriteLine("Output string: " + 
                        Regex.Replace(input, pattern, substitution));
   }
}
// The example displays the following output:
//    Matches:
//       1 at position 2
//       2 at position 5
//       3 at position 8
//       4 at position 11
//       5 at position 14
//    Input string:  aa1bb2cc3dd4ee5
//    Output string: aaaabbaa1bbccaa1bb2ccddaa1bb2cc3ddeeaa1bb2cc3dd4ee
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim input As String = "aa1bb2cc3dd4ee5"
      Dim pattern As String = "\d+"
      Dim substitution As String = "$`"
      Console.WriteLine("Matches:")
      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine("   {0} at position {1}", match.Value, match.Index)
      Next   
      Console.WriteLine("Input string:  {0}", input)
      Console.WriteLine("Output string: " + _
                        Regex.Replace(input, pattern, substitution))
   End Sub
End Module
' The example displays the following output:
'    Matches:
'       1 at position 2
'       2 at position 5
'       3 at position 8
'       4 at position 11
'       5 at position 14
'    Input string:  aa1bb2cc3dd4ee5
'    Output string: aaaabbaa1bbccaa1bb2ccddaa1bb2cc3ddeeaa1bb2cc3dd4ee
```

이 예제에서는 입력 문자열 `"aa1bb2cc3dd4ee5"`에 다섯 개의 일치 항목이 포함되어 있습니다. 다음 테이블에서는 $` 대체를 사용할 경우 정규식 엔진이 입력 문자열에서 일치하는 부분을 어떻게 바꾸는지 보여줍니다. 삽입된 텍스트는 [결과] 문자열에서 굵게 표시됩니다.

일치 | 위치 | 일치하는 문자열 앞에 있는 문자열 | 결과 문자열
----- | -------- | ------------------- | -------------
1 | 2 | aa | aa**aa**bb2cc3dd4ee5
2 | 5 | aa1bb | aaaabb**aa1bb**cc3dd4ee5
3 | 8 | aa1bb2cc | aaaabbaa1bbcc**aa1bb2cc**dd4ee5
4 | 11 | aa1bb2cc3dd | aaaabbaa1bbccaa1bb2ccdd**aa1bb2cc3dd**ee5
5 | 14 | aa1bb2cc3dd4ee | aaaabbaa1bbccaa1bb2ccddaa1bb2cc3ddee **aa1bb2cc3dd4ee**
 
## <a name="substituting-the-text-after-the-match"></a>일치하는 문자열 뒤에 있는 텍스트 대체

**$'** 대체는 일치하는 문자열을 일치하는 문자열 뒤의 전체 입력 문자열로 바꿉니다. 즉, 일치하는 텍스트를 제거하는 동안 일치하는 문자열 뒤의 입력 문자열을 복제합니다. 일치하는 텍스트 앞에 나오는 텍스트는 결과 문자열에서 변경되지 않습니다. 일치하는 부분이 없으면 **$'** 대체가 아무 효과가 없습니다.

다음 예제에서는 `\d+` 정규식 패턴을 사용하여 입력 문자열에서 하나 이상의 숫자가 연속적으로 표시된 부분을 찾습니다. 바꾸기 문자열 **$'**는 이 숫자를 일치하는 문자열 뒤의 텍스트로 바꿉니다. 

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string input = "aa1bb2cc3dd4ee5";
      string pattern = @"\d+";
      string substitution = "$'";
      Console.WriteLine("Matches:");
      foreach (Match match in Regex.Matches(input, pattern))
         Console.WriteLine("   {0} at position {1}", match.Value, match.Index);
      Console.WriteLine("Input string:  {0}", input);
      Console.WriteLine("Output string: " + 
                        Regex.Replace(input, pattern, substitution));
   }
}
// The example displays the following output:
//    Matches:
//       1 at position 2
//       2 at position 5
//       3 at position 8
//       4 at position 11
//       5 at position 14
//    Input string:  aa1bb2cc3dd4ee5
//    Output string: aaaabbaa1bbccaa1bb2ccddaa1bb2cc3ddeeaa1bb2cc3dd4ee
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim input As String = "aa1bb2cc3dd4ee5"
      Dim pattern As String = "\d+"
      Dim substitution As String = "$'"
      Console.WriteLine("Matches:")
      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine("   {0} at position {1}", match.Value, match.Index)
      Next   
      Console.WriteLine("Input string:  {0}", input)
      Console.WriteLine("Output string: " + _
                        Regex.Replace(input, pattern, substitution))
   End Sub
End Module
' The example displays the following output:
'    Matches:
'       1 at position 2
'       2 at position 5
'       3 at position 8
'       4 at position 11
'       5 at position 14
'    Input string:  aa1bb2cc3dd4ee5
'    Output string: aaaabbaa1bbccaa1bb2ccddaa1bb2cc3ddeeaa1bb2cc3dd4ee
```

이 예제에서는 입력 문자열 `"aa1bb2cc3dd4ee5"`에 다섯 개의 일치 항목이 포함되어 있습니다. 다음 테이블에서는 **$'** 대체를 사용할 경우 정규식 엔진이 입력 문자열에서 일치하는 부분을 어떻게 바꾸는지 보여줍니다. 삽입된 텍스트는 [결과] 문자열에서 굵게 표시됩니다.

일치 | 위치 | 일치하는 문자열 앞에 있는 문자열 | 결과 문자열
----- | -------- | ------------------- | -------------
1 | 2 | bb2cc3dd4ee5 | aa**bb2cc3dd4ee5**bb2cc3dd4ee5
2 | 5 | cc3dd4ee5 | aabb2cc3dd4ee5bb**cc3dd4ee5**cc3dd4ee5
3 | 8 | dd4ee5 | aabb2cc3dd4ee5bbcc3dd4ee5cc**dd4ee5**dd4ee5
4 | 11 | ee5 | aabb2cc3dd4ee5bbcc3dd4ee5ccdd4ee5dd**ee5**ee5
5 | 14 | [String.Empty](xref:System.String.Empty) | aabb2cc3dd4ee5bbcc3dd4ee5ccdd4ee5ddee5ee
 
## <a name="substituting-the-last-captured-group"></a>캡처된 마지막 그룹 대체

**$+** 대체는 일치하는 문자열을 캡처된 마지막 그룹으로 바꿉니다. 캡처된 그룹이 없거나 캡처된 마지막 그룹 값이 [String.Empty](xref:System.String.Empty)이면 **$+** 대체는 아무 효과가 없습니다.

다음 예제에서는 문자열에서 중복 단어를 확인한 다음 **$+** 대체를 사용하여 해당 단어가 한 번만 나타나도록 바꿉니다. [RegexOptions.IgnoreCase](xref:System.Text.RegularExpressions.RegexOptions.IgnoreCase) 옵션은 대소문자만 다르고 나머지는 동일한 단어를 중복으로 인식하도록 하는 데 사용됩니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"\b(\w+)\s\1\b";
      string substitution = "$+";
      string input = "The the dog jumped over the fence fence.";
      Console.WriteLine(Regex.Replace(input, pattern, substitution, 
                        RegexOptions.IgnoreCase));
   }
}
// The example displays the following output:
//      The dog jumped over the fence.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "\b(\w+)\s\1\b"
      Dim substitution As String = "$+"
      Dim input As String = "The the dog jumped over the fence fence."
      Console.WriteLine(Regex.Replace(input, pattern, substitution, _
                                      RegexOptions.IgnoreCase))
   End Sub
End Module
' The example displays the following output:
'      The dog jumped over the fence.
```

정규식 패턴 `\b(\w+)\s\1\b`는 다음 테이블과 같이 정의됩니다.

패턴 | 설명
------- | ----------- 
`\b` | 단어 경계에서 일치 항목 찾기를 시작합니다.
`(\w+)` | 하나 이상의 단어 문자를 찾습니다. 이 그룹은 첫 번째 캡처링 그룹입니다.
`\s` | 공백 문자를 찾습니다.
`\1` | 캡처된 첫 번째 그룹을 찾습니다.
`\b` | 단어 경계에서 일치 항목 찾기를 끝냅니다.
 
## <a name="substituting-the-entire-input-string"></a>전체 입력 문자열 대체

**$_** 대체는 일치하는 문자열을 전체 입력 문자열로 바꿉니다. 즉, 일치하는 텍스트를 제거한 다음 일치하는 텍스트를 포함한 전체 문자열로 바꿉니다.

다음 예제에서는 입력 문자열에서 하나 이상의 10진수 숫자를 찾습니다. **$_** 대체를 사용하여 전체 입력 문자열로 바꿉니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string input = "ABC123DEF456";
      string pattern = @"\d+";
      string substitution = "$_";
      Console.WriteLine("Original string:          {0}", input);
      Console.WriteLine("String with substitution: {0}", 
                        Regex.Replace(input, pattern, substitution));      
   }
}
// The example displays the following output:
//       Original string:          ABC123DEF456
//       String with substitution: ABCABC123DEF456DEFABC123DEF456
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim input As String = "ABC123DEF456"
      Dim pattern As String = "\d+"
      Dim substitution As String = "$_"
      Console.WriteLine("Original string:          {0}", input)
      Console.WriteLine("String with substitution: {0}", _
                        Regex.Replace(input, pattern, substitution))      
   End Sub
End Module
' The example displays the following output:
'       Original string:          ABC123DEF456
'       String with substitution: ABCABC123DEF456DEFABC123DEF456
```

이 예제에서는 입력 문자열 `"ABC123DEF456"`에 두 개의 일치 항목이 포함되어 있습니다. 다음 테이블에서는 **$_** 대체를 사용할 경우 정규식 엔진이 입력 문자열에서 일치하는 부분을 어떻게 바꾸는지 보여줍니다. 삽입된 텍스트는 [결과] 문자열에서 굵게 표시됩니다.

일치 | 위치 | 일치하는 문자열 앞에 있는 문자열 | 결과 문자열
----- | -------- | ------------------- | -------------
1 | 3 | 123 | ABC**ABC123DEF456**DEF456
2 | 5 | 456 | ABCABC123DEF456DEF**ABC123DEF456**
 
## <a name="see-also"></a>참고 항목

[정규식 언어 - 빠른 참조](quick-ref.md)




<!--HONumber=Nov16_HO3-->


