---
title: "정규식의 그룹화 구문"
description: "정규식의 그룹화 구문"
keywords: .NET, .NET Core
author: stevehoag
ms.author: shoag
manager: wpickett
ms.date: 07/29/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: e0bf3718-e64b-460b-b73d-66678cec6093
translationtype: Human Translation
ms.sourcegitcommit: b20713600d7c3ddc31be5885733a1e8910ede8c6
ms.openlocfilehash: 6aa304f5c4ed400faddd3869006cdd011aa06466

---

# <a name="grouping-constructs-in-regular-expressions"></a>정규식의 그룹화 구문

그룹화 구문은 정규식의 하위 식을 나타내며 입력 문자열의 부분 문자열을 캡처합니다. 그룹화 구문은 다음과 같은 경우에 사용할 수 있습니다.

* 입력 문자열에서 반복되는 하위 식을 일치시킵니다.

* 여러 정규식 언어 요소가 있는 하위 식에 수량자를 적용합니다. 수량자에 대한 자세한 내용은 [정규식의 수량자](quantifiers.md)를 참조하세요.

* [Regex.Replace](xref:System.Text.RegularExpressions.Regex.Replace(System.String,System.String)) 및 [Match.Result](xref:System.Text.RegularExpressions.Match.Result(System.String)) 메서드에 의해 반환되는 문자열에 하위 식을 포함합니다.

* [Match.Groups](xref:System.Text.RegularExpressions.Match.Groups) 속성에서 개별 하위 식을 검색하여 전체적으로 일치하는 텍스트와 별도로 처리합니다.

다음 표에는 .NET 정규식 엔진에서 지원되는 그룹화 구문과 해당 그룹화 구문의 캡처링 또는 비 캡처링 여부가 표시되어 있습니다. 

그룹화 구문 | 캡처링 또는 비 캡처링
------------------ | -------------------------
[일치하는 하위 식](#matched-subexpressions) | 캡처 중
[명명된 일치하는 하위 식](#named-matched-subexpressions) | 캡처 중
[균형 조정 그룹 정의](#balancing-group-definitions) | 캡처 중
[비 캡처링 그룹](#noncapturing-groups) | 비 캡처링
[그룹 옵션](#group-options) | 비 캡처링
[너비가 0인 긍정 Lookahead 어설션](#zero-width-positive-lookahead-assertions) | 비 캡처링
[너비가 0인 부정 Lookahead 어설션](#zero-width-negative-lookahead-assertions) | 비 캡처링
[너비가 0인 긍정 Lookbehind 어설션](#zero-width-positive-lookbehind-assertions) | 비 캡처링
[너비가 0인 부정 Lookbehind 어설션](#zero-width-negative-lookbehind-assertions) | 비 캡처링
[역추적하지 않는 하위 식](#nonbacktracking-subexpressions) | 비 캡처링

그룹 및 정규식 개체 모델에 대한 자세한 내용은 [그룹화 구문 및 정규식 개체](#grouping-constructs-and-regular-expression-objects)를 참조하세요. 

## <a name="matched-subexpressions"></a>일치하는 하위 식

다음 그룹화 구문은 일치하는 하위 식을 캡처합니다. 

**(**_subexpression_**)**

여기서 *subexpression*은 임의의 유효한 정규식 패턴입니다. 괄호를 사용하는 캡처는 정규식에서 여는 괄호의 순서에 따라 왼쪽에서 오른쪽으로 자동으로 번호(1부터 시작)가 매겨집니다. 번호가 0인 캡처는 전체 정규식 패턴에 의해 일치되는 텍스트입니다.

> [!NOTE]
> 기본적으로 (subexpression) 언어 요소는 일치하는 하위 식을 캡처합니다. 그러나 정규식 패턴 일치 메서드의 RegexOptions 매개 변수가 RegexOptions.ExplicitCapture 플래그를 포함하거나, n 옵션이 이 하위 식에 적용된 경우(이 항목의 뒷부분에 있는 그룹 옵션 참조) 일치하는 하위 식이 캡처되지 않습니다.
 
캡처된 그룹에는 다음과 같은 4가지 방법으로 액세스할 수 있습니다.

* 정규식 내의 역참조 구문을 사용합니다. 일치하는 하위 식은 **\**_number_ 구문을 통해 동일한 정규식에서 참조됩니다. 여기서 *number*는 캡처된 하위 식의 서수입니다.

* 정규식 내의 명명된 역참조 구문을 사용합니다. 일치하는 하위 식은 **\k<**_name_**>** 구문(여기서 *name*은 캡처링 그룹의 이름) 또는 **\k**_<number_**>** 구문(여기서 *number*는 캡처링 그룹의 서수)을 통해 동일한 정규식에서 참조됩니다. 캡처링 그룹은 해당 서수와 같은 기본 이름을 갖고 있습니다. 자세한 내용은 이 항목의 뒷부분에 있는 그룹화 구문 및 정규식 개체를 참조하세요.

* [Regex.Replace](xref:System.Text.RegularExpressions.Regex.Replace(System.String,System.String)) 또는 [Match.Result](xref:System.Text.RegularExpressions.Match.Result(System.String)) 메서드 호출에서 **$**_number_ 바꾸기 시퀀스를 사용합니다. 여기서 *number*는 캡처된 하위 식의 서수입니다.

* 프로그래밍 방식으로 [Match.Groups](xref:System.Text.RegularExpressions.Match.Groups) 속성에서 반환하는 [GroupCollection](xref:System.Text.RegularExpressions.GroupCollection) 개체를 사용합니다. 컬렉션에서 위치 0에 있는 멤버는 전체 정규식 일치를 나타냅니다. 각 후속 멤버는 일치하는 하위 식을 나타냅니다. 자세한 내용은 [그룹화 구문 및 정규식 개체](#grouping-constructs-and-regular-expression-objects) 섹션을 참조하세요.

다음 예제에서는 텍스트에서 중복된 단어를 식별하는 정규식을 보여 줍니다. 정규식 패턴의 두 캡처링 그룹은 중복된 단어의 두 인스턴스를 나타냅니다. 두 번째 인스턴스는 입력 문자열의 해당 시작 위치를 보고하기 위해 캡처됩니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"(\w+)\s(\1)";
      string input = "He said that that was the the correct answer.";
      foreach (Match match in Regex.Matches(input, pattern, RegexOptions.IgnoreCase))
         Console.WriteLine("Duplicate '{0}' found at positions {1} and {2}.", 
                           match.Groups[1].Value, match.Groups[1].Index, match.Groups[2].Index);
   }
}
// The example displays the following output:
//       Duplicate 'that' found at positions 8 and 13.
//       Duplicate 'the' found at positions 22 and 26.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "(\w+)\s(\1)\W"
      Dim input As String = "He said that that was the the correct answer."
      For Each match As Match In Regex.Matches(input, pattern, RegexOptions.IgnoreCase)
         Console.WriteLine("Duplicate '{0}' found at positions {1} and {2}.", _
                           match.Groups(1).Value, match.Groups(1).Index, match.Groups(2).Index)
      Next
   End Sub
End Module
' The example displays the following output:
'       Duplicate 'that' found at positions 8 and 13.
'       Duplicate 'the' found at positions 22 and 26.
```

정규식 패턴은 다음과 같습니다.

```
(\w+)\s(\1)\W
```

다음 테이블은 정규식 패턴이 해석되는 방법을 보여 줍니다. 

패턴 | 설명
------- | ----------- 
`(\w+)` | 하나 이상의 단어 문자를 찾습니다. 이 그룹은 첫 번째 캡처링 그룹입니다.
`\s` | 공백 문자를 찾습니다.
`(\1)` | 캡처된 첫 번째 그룹의 문자열을 찾습니다. 이 그룹은 두 번째 캡처링 그룹입니다. 이 예제에서는 `Match.Index` 속성에서 중복 단어의 시작 위치를 검색할 수 있도록 두 번째 캡처링 그룹을 캡처된 그룹에 할당합니다.
`\W` | 공백 및 문장 부호를 포함하여 비단어 문자를 찾습니다. 그러면 정규식 패턴이 캡처된 첫 번째 그룹의 단어로 시작하는 단어를 찾지 못합니다.
 
## <a name="named-matched-subexpressions"></a>명명된 일치하는 하위 식

다음 그룹화 구문은 일치하는 하위 식을 캡처하고 사용자가 이름 또는 번호로 해당 하위 식에 액세스할 수 있게 해줍니다. 

```
(?<name>subexpression)
```

또는

```
(?'name'subexpression)
```

여기서 *name*은 유효한 그룹 이름이고, *subexpression*은 임의의 유효한 정규식 패턴입니다. *name*은 문장 부호 문자를 포함해서는 안 되며 숫자로 시작할 수 없습니다.

> [!NOTE]
> 정규식 패턴 일치 메서드의 [RegexOptions](xref:System.Text.RegularExpressions.RegexOptions) 매개 변수가 [RegexOptions.ExplicitCapture](xref:System.Text.RegularExpressions.RegexOptions.ExplicitCapture) 플래그를 포함하거나, **n** 옵션이 이 하위 식에 적용된 경우(이 항목의 뒷부분에 있는 [그룹 옵션](#group-options) 참조) 하위 식을 캡처하는 유일한 방법은 명시적으로 캡처링 그룹의 이름을 지정하는 것입니다.
 
명명된 캡처된 그룹에는 다음과 같은 방법으로 액세스할 수 있습니다.

* 정규식 내의 명명된 역참조 구문을 사용합니다. 일치하는 하위 식은 **\k<**_name_**>** 구문을 통해 동일한 정규식에서 참조됩니다. 여기서 *name*은 캡처된 하위 식의 이름입니다. 

* 정규식 내의 역참조 구문을 사용합니다. 일치하는 하위 식은 **\**_number_ 구문을 통해 동일한 정규식에서 참조됩니다. 여기서 *number*는 캡처된 하위 식의 서수입니다. 명명된 일치하는 하위 식은 하위 식을 일치시킨 후 왼쪽에서 오른쪽으로 연속적으로 번호가 매겨집니다.

* [Regex.Replace](xref:System.Text.RegularExpressions.Regex.Replace(System.String,System.String)) 또는 [Match.Result](xref:System.Text.RegularExpressions.Match.Result(System.String)) 메서드 호출에서 **${**_name_**}** 바꾸기 시퀀스를 사용합니다. 여기서 *name*은 캡처된 하위 식의 이름입니다.

* [Regex.Replace](xref:System.Text.RegularExpressions.Regex.Replace(System.String,System.String)) 또는 [Match.Result](xref:System.Text.RegularExpressions.Match.Result(System.String)) 메서드 호출에서 **$**_number_ 바꾸기 시퀀스를 사용합니다. 여기서 *number*는 캡처된 하위 식의 서수입니다.

* 프로그래밍 방식으로 [Match.Groups](xref:System.Text.RegularExpressions.Match.Groups) 속성에서 반환하는 [GroupCollection](xref:System.Text.RegularExpressions.GroupCollection) 개체를 사용합니다. 컬렉션에서 위치 0에 있는 멤버는 전체 정규식 일치를 나타냅니다. 각 후속 멤버는 일치하는 하위 식을 나타냅니다. 명명된 캡처된 그룹은 캡처된 그룹의 번호를 매긴 후 컬렉션에 저장됩니다.

* 프로그래밍 방식으로 하위 식 이름을 [GroupCollection](xref:System.Text.RegularExpressions.GroupCollection) 개체의 인덱서(C#의 경우) 또는 해당 Item 속성(Visual Basic의 경우)에 제공합니다.

단순 정규식 패턴이 프로그래밍 방식으로 또는 정규식 언어 구문을 사용하여, 번호가 매겨진(명명되지 않은) 그룹 및 명명된 그룹을 참조할 수 있는 방법을 보여 줍니다. 정규식 `((?<One>abc)\d+)?(?<Two>xyz)(.*)`는 번호 및 이름으로 다음 캡처링 그룹을 생성합니다. 첫 번째 캡처링 그룹(번호 0)은 항상 전체 패턴을 지칭합니다.

숫자 | 이름 | 패턴
------ | ---- | ------- 
0 | 0(기본 이름) | `((?<One>abc)\d+)?(?<Two>xyz)(.*)`
1 | 1(기본 이름) | `((?<One>abc)\d+)`
2 | 2(기본 이름) | `(.*)`
3 | 1 | `(?<One>abc)`
4 | 2 | `(?<Two>xyz)`
 
다음 예제에서는 중복된 단어와 중복된 각 단어 바로 뒤에 나오는 단어를 식별하는 정규식을 보여 줍니다. 정규식 패턴은 두 개의 명명된 하위 식 즉, 중복된 단어를 나타내는 `duplicateWord`와 중복된 단어 뒤에 나오는 단어를 나타내는 `nextWord`를 정의합니다. 

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"(?<duplicateWord>\w+)\s\k<duplicateWord>\W(?<nextWord>\w+)";
      string input = "He said that that was the the correct answer.";
      foreach (Match match in Regex.Matches(input, pattern, RegexOptions.IgnoreCase))
         Console.WriteLine("A duplicate '{0}' at position {1} is followed by '{2}'.", 
                           match.Groups["duplicateWord"].Value, match.Groups["duplicateWord"].Index, 
                           match.Groups["nextWord"].Value);

   }
}
// The example displays the following output:
//       A duplicate 'that' at position 8 is followed by 'was'.
//       A duplicate 'the' at position 22 is followed by 'correct'.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "(?<duplicateWord>\w+)\s\k<duplicateWord>\W(?<nextWord>\w+)"
      Dim input As String = "He said that that was the the correct answer."
      Console.WriteLine(Regex.Matches(input, pattern, RegexOptions.IgnoreCase).Count)
      For Each match As Match In Regex.Matches(input, pattern, RegexOptions.IgnoreCase)
         Console.WriteLine("A duplicate '{0}' at position {1} is followed by '{2}'.", _
                           match.Groups("duplicateWord").Value, match.Groups("duplicateWord").Index, _
                           match.Groups("nextWord").Value)
      Next
   End Sub
End Module
' The example displays the following output:
'    A duplicate 'that' at position 8 is followed by 'was'.
'    A duplicate 'the' at position 22 is followed by 'correct'.
```

정규식 패턴은 다음과 같습니다.

```
(?<duplicateWord>\w+)\s\k<duplicateWord>\W(?<nextWord>\w+)
```

다음 테이블은 정규식이 해석되는 방법을 보여 줍니다.

패턴 | 설명
------- | -----------
`(?<duplicateWord>\w+)` | 하나 이상의 단어 문자를 찾습니다. 이 캡처링 그룹의 이름을 `duplicateWord`로 지정합니다.
`\s` | 공백 문자를 찾습니다.
`\k<duplicateWord>` | `duplicateWord`라는 캡처된 그룹에서 문자열을 찾습니다. 
`\W` | 공백 및 문장 부호를 포함하여 비단어 문자를 찾습니다. 그러면 정규식 패턴이 캡처된 첫 번째 그룹의 단어로 시작하는 단어를 찾지 못합니다.
`(?<nextWord>\w+)` | 하나 이상의 단어 문자를 찾습니다. 이 캡처링 그룹의 이름을 `nextWord`로 지정합니다.
 
정규식에서 그룹 이름을 반복할 수 있습니다. 예를 들어 다음 예제와 같이 둘 이상의 그룹에 `digit`라는 이름을 지정할 수 있습니다. 중복된 이름의 경우 [Group](xref:System.Text.RegularExpressions.Group) 개체의 값은 입력 문자열에서 마지막으로 성공한 캡처에 의해 결정됩니다. 또한 그룹 이름이 중복되지 않은 경우와 마찬가지로 [CaptureCollection](xref:System.Text.RegularExpressions.CaptureCollection)에 각 캡처에 대한 정보가 채워집니다. 

다음 예제에서 `\D+(?<digit>\d+)\D+(?<digit>\d+)?` 정규식에는 `digit`라는 그룹 발생이 두 개 포함됩니다. 첫 번째 `digit` 그룹은 하나 이상의 숫자 문자를 캡처합니다. 두 번째 `digit` 그룹은 하나 이상 숫자 문자의 0개 또는 1개 발생을 캡처합니다. 예제의 출력에 표시된 것처럼 두 번째 캡처링 그룹이 텍스트와 일치하면 해당 텍스트의 값이 [Group](xref:System.Text.RegularExpressions.Group) 개체의 값을 정의합니다. 두 번째 캡처링 그룹이 입력 문자열과 일치하지 않으면 마지막으로 성공한 일치의 값이 [Group](xref:System.Text.RegularExpressions.Group) 개체의 값을 정의합니다. 

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      String pattern = @"\D+(?<digit>\d+)\D+(?<digit>\d+)?";
      String[] inputs = { "abc123def456", "abc123def" };
      foreach (var input in inputs) {
         Match m = Regex.Match(input, pattern);
         if (m.Success) {
            Console.WriteLine("Match: {0}", m.Value);
            for (int grpCtr = 1; grpCtr < m.Groups.Count; grpCtr++) {
               Group grp = m.Groups[grpCtr];
               Console.WriteLine("Group {0}: {1}", grpCtr, grp.Value);
               for (int capCtr = 0; capCtr < grp.Captures.Count; capCtr++)
                  Console.WriteLine("   Capture {0}: {1}", capCtr,
                                    grp.Captures[capCtr].Value);
            }
         }
         else {
            Console.WriteLine("The match failed.");
         }
         Console.WriteLine();
      }
   }
}
// The example displays the following output:
//       Match: abc123def456
//       Group 1: 456
//          Capture 0: 123
//          Capture 1: 456
//
//       Match: abc123def
//       Group 1: 123
//          Capture 0: 123
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "\D+(?<digit>\d+)\D+(?<digit>\d+)?"
      Dim inputs() As String = { "abc123def456", "abc123def" }
      For Each input As String In inputs
         Dim m As Match = Regex.Match(input, pattern)
         If m.Success Then
            Console.WriteLine("Match: {0}", m.Value)
            For grpCtr As Integer = 1 to m.Groups.Count - 1
               Dim grp As Group = m.Groups(grpCtr)
               Console.WriteLine("Group {0}: {1}", grpCtr, grp.Value)
               For capCtr As Integer = 0 To grp.Captures.Count - 1
                  Console.WriteLine("   Capture {0}: {1}", capCtr,
                                    grp.Captures(capCtr).Value)
               Next
            Next
         Else
            Console.WriteLine("The match failed.")
         End If
         Console.WriteLine()
      Next
   End Sub
End Module
' The example displays the following output:
'       Match: abc123def456
'       Group 1: 456
'          Capture 0: 123
'          Capture 1: 456
'
'       Match: abc123def
'       Group 1: 123
'          Capture 0: 123
```

다음 테이블은 정규식이 해석되는 방법을 보여 줍니다.

패턴 | 설명
------- | -----------
`\D+` | 하나 이상의 10진수가 아닌 문자를 찾습니다. 
`(?<digit>\d+)` | 하나 이상의 10진수 문자를 찾습니다. 명명된 그룹 `digit`에 일치를 할당합니다. 
`\D+` | 하나 이상의 10진수가 아닌 문자를 찾습니다. 
`(?<digit>\d+)?` | 하나 이상 10진수 문자의 0개 또는 1개 발생을 찾습니다. 명명된 그룹 `digit`에 일치를 할당합니다.
 
## <a name="balancing-group-definitions"></a>균형 조정 그룹 정의

균형 조정 그룹 정의는 이전에 정의된 그룹의 정의를 삭제하고 현재 그룹에 이전에 정의된 그룹과 현재 그룹 간의 간격을 저장합니다. 이 그룹화 구문의 형식은 다음과 같습니다. 

```
(?<name1-name2>subexpression)
```

또는

```
(?'name1-name2' subexpression)
```

여기서 *name1*은 현재 그룹이고(선택 사항), *name2*는 이전에 정의된 그룹이며, *subexpression*은 임의의 유효한 정규식 패턴입니다. 균형 조정 그룹 정의는 *name2*의 정의를 삭제하고 *name1*에 *name2*와 *name1* 간의 간격을 저장합니다. *name2* 그룹이 정의되어 있지 않으면 일치에서 역추적합니다. *name2*의 마지막 정의를 삭제하면 *name2*의 이전 정의가 표시되므로 이 구문을 통해 *name2* 그룹에 대한 캡처 스택을 괄호 또는 여는 대괄호 및 닫는 대괄호와 같은 중첩된 구문을 추적하기 위한 카운터로 사용할 수 있습니다. 

균형 조정 그룹 정의에서는 *name2*를 스택으로 사용합니다. 각 중첩된 구문의 시작 문자는 그룹 및 해당 [Group.Captures](xref:System.Text.RegularExpressions.Group.Captures) 컬렉션에 배치됩니다. 닫는 문자가 일치하면 해당 여는 문자가 그룹에서 제거되고 [Captures](xref:System.Text.RegularExpressions.Group.Captures) 컬렉션이 하나 감소합니다. 모든 중첩된 구문의 여는 문자와 닫는 문자가 일치하고 나면 *name1*이 비어 있습니다.

> [!NOTE]
> 다음 예제에서 중첩된 구문의 적절한 여는 문자와 닫는 문자를 사용하도록 정규식을 수정하고 나면 이러한 정규식을 사용하여 가장 많이 중첩된 구문(예: 여러 중첩 메서드 호출을 포함하는 프로그램 코드 줄 또는 수학식)을 처리할 수 있습니다. 
 
다음 예제에서는 균형 조정 그룹 정의를 사용하여 입력 문자열에서 왼쪽 및 오른쪽 꺾쇠괄호(<>)를 찾습니다. 이 예제에서는 일치하는 꺾쇠괄호 쌍을 추적하는 데 스택처럼 사용되는 두 개의 명명된 그룹 `Open` 및 `Close`를 정의합니다. 캡처된 각 왼쪽 꺾쇠괄호는 `Open` 그룹의 캡처 컬렉션에 푸시되고, 캡처된 각 오른쪽 꺾쇠괄호는 `Close` 그룹의 캡처 컬렉션에 푸시됩니다. 균형 조정 그룹 정의는 각 왼쪽 꺾쇠괄호에 대해 일치하는 오른쪽 꺾쇠괄호가 있도록 보장합니다. 일치하는 오른쪽 꺾쇠괄호가 없는 경우 최종 하위 패턴 `(?(Open)(?!))`는 `Open` 그룹이 비어 있지 않은 경우(및 따라서 모든 중첩된 구문이 닫히지 않은 경우)에만 평가됩니다. 최종 하위 패턴이 평가되면 찾기가 실패하는데, `(?!)` 하위 패턴이 항상 실패하는 너비가 0인 부정 lookahead 어설션이기 때문입니다. 

```csharp
using System;
using System.Text.RegularExpressions;

class Example
{
   public static void Main() 
   {
      string pattern = "^[^<>]*" +
                       "(" + 
                       "((?'Open'<)[^<>]*)+" +
                       "((?'Close-Open'>)[^<>]*)+" +
                       ")*" +
                       "(?(Open)(?!))$";
      string input = "<abc><mno<xyz>>";

      Match m = Regex.Match(input, pattern);
      if (m.Success == true)
      {
         Console.WriteLine("Input: \"{0}\" \nMatch: \"{1}\"", input, m);
         int grpCtr = 0;
         foreach (Group grp in m.Groups)
         {
            Console.WriteLine("   Group {0}: {1}", grpCtr, grp.Value);
            grpCtr++;
            int capCtr = 0;
            foreach (Capture cap in grp.Captures)
            {            
                Console.WriteLine("      Capture {0}: {1}", capCtr, cap.Value);
                capCtr++;
            }
          }
      }
      else
      {
         Console.WriteLine("Match failed.");
      }   
    }
}
// The example displays the following output:
//    Input: "<abc><mno<xyz>>"
//    Match: "<abc><mno<xyz>>"
//       Group 0: <abc><mno<xyz>>
//          Capture 0: <abc><mno<xyz>>
//       Group 1: <mno<xyz>>
//          Capture 0: <abc>
//          Capture 1: <mno<xyz>>
//       Group 2: <xyz
//          Capture 0: <abc
//          Capture 1: <mno
//          Capture 2: <xyz
//       Group 3: >
//          Capture 0: >
//          Capture 1: >
//          Capture 2: >
//       Group 4:
//       Group 5: mno<xyz>
//          Capture 0: abc
//          Capture 1: xyz
//          Capture 2: mno<xyz>
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main() 
        Dim pattern As String = "^[^<>]*" & _
                                "(" + "((?'Open'<)[^<>]*)+" & _
                                "((?'Close-Open'>)[^<>]*)+" + ")*" & _
                                "(?(Open)(?!))$"
        Dim input As String = "<abc><mno<xyz>>"
        Dim rgx AS New Regex(pattern)'
        Dim m As Match = Regex.Match(input, pattern)
        If m.Success Then
            Console.WriteLine("Input: ""{0}"" " & vbCrLf & "Match: ""{1}""", _
                               input, m)
            Dim grpCtr As Integer = 0
            For Each grp As Group In m.Groups
               Console.WriteLine("   Group {0}: {1}", grpCtr, grp.Value)
               grpCtr += 1
               Dim capCtr As Integer = 0
               For Each cap As Capture In grp.Captures            
                  Console.WriteLine("      Capture {0}: {1}", capCtr, cap.Value)
                  capCtr += 1
               Next
            Next
        Else
            Console.WriteLine("Match failed.")
        End If
    End Sub
End Module  
' The example displays the following output:
'       Input: "<abc><mno<xyz>>"
'       Match: "<abc><mno<xyz>>"
'          Group 0: <abc><mno<xyz>>
'             Capture 0: <abc><mno<xyz>>
'          Group 1: <mno<xyz>>
'             Capture 0: <abc>
'             Capture 1: <mno<xyz>>
'          Group 2: <xyz
'             Capture 0: <abc
'             Capture 1: <mno
'             Capture 2: <xyz
'          Group 3: >
'             Capture 0: >
'             Capture 1: >
'             Capture 2: >
'          Group 4:
'          Group 5: mno<xyz>
'             Capture 0: abc
'             Capture 1: xyz
'             Capture 2: mno<xyz>
```

정규식 패턴은 다음과 같습니다.

```
^[^<>]*(((?'Open'<)[^<>]*)+((?'Close-Open'>)[^<>]*)+)*(?(Open)(?!))$
```

정규식은 다음과 같이 해석됩니다.

패턴 | 설명
------- | -----------
`^` | 문자열의 시작 부분에서 시작합니다.
`[^<>]*` | 왼쪽 또는 오른쪽 꺾쇠괄호가 아닌 0개 이상의 문자를 찾습니다.
`(?'Open'<)` | 왼쪽 꺾쇠괄호를 찾아 `Open`이라는 그룹에 할당합니다.
`[^<>]*` | 왼쪽 또는 오른쪽 꺾쇠괄호가 아닌 0개 이상의 문자를 찾습니다.
`((?'Open'<)[^<>]*) +` | 하나 이상의 왼쪽 꺾쇠괄호 다음에 왼쪽 또는 오른쪽 꺾쇠괄호가 아닌 0개 이상의 문자가 있는 일치 항목을 찾습니다. 이 그룹은 두 번째 캡처링 그룹입니다.
`(?'Close-Open'>)` | 오른쪽 꺾쇠괄호를 찾고 `Open` 그룹과 현재 그룹 간의 부분 문자열을 `Close` 그룹에 할당한 다음 `Open` 그룹의 정의를 삭제합니다.
`[^<>]*` | 왼쪽 및 오른쪽 꺾쇠 괄호가 아닌 문자를 0번 이상 찾습니다. 
`((?'Close-Open'>)[^<>]*)+` | 하나 이상의 오른쪽 꺾쇠괄호 다음에 왼쪽 및 오른쪽 꺾쇠괄호가 아닌 0개 이상의 문자가 있는 일치 항목을 찾습니다. 오른쪽 꺾쇠 괄호를 찾을 때 `Open` 그룹과 현재 그룹 간의 부분 문자열을 `Close` 그룹에 할당하고 `Open` 그룹의 정의를 삭제합니다. 이 그룹은 세 번째 캡처링 그룹입니다.
`(((?'Open'<)[^<>]*)+((?'Close-Open'>)[^<>]*)+)*` | 하나 이상의 왼쪽 꺾쇠괄호가 있고, 다음에 0개 이상의 꺾쇠괄호가 아닌 문자가 있고, 다음에 하나 이상의 오른쪽 꺾쇠괄호가 있고, 다음에 0개 이상의 꺾쇠괄호가 아닌 문자가 있는 패턴을 0개 이상 찾습니다. 오른쪽 꺾쇠괄호를 찾을 때 `Open` 그룹의 정의를 삭제하고 `Open` 그룹과 현재 그룹 간의 부분 문자열을 `Close` 그룹에 할당합니다. 이 그룹은 첫 번째 캡처링 그룹입니다.
`(?(Open)(?!))` | `Open` 그룹이 있는 경우 빈 문자열을 찾을 수 있으면 찾기를 중단하되, 문자열에서 정규식 엔진의 위치를 앞으로 이동하지 마세요. 이는 너비가 0인 부정 lookahead 어설션입니다. 입력 문자열에는 항상 암시적으로 빈 문자열이 있기 때문에 이 찾기는 항상 실패합니다. 이 찾기의 실패는 꺾쇠괄호의 짝이 맞지 않음을 의미합니다. 
`$` | 입력 문자열의 끝 부분을 찾습니다.
 
최종 하위 식 `(?(Open)(?!))`는 입력 문자열의 중첩 구문이 짝이 올바르게 맞는지 여부를 나타냅니다(예: 각 왼쪽 꺾쇠괄호가 오른쪽 꺾쇠괄호와 일치하는지 여부). 최종 하위 식에서는 유효한 캡처된 그룹을 기준으로 조건부 일치를 사용합니다. 자세한 내용은 [정규식의 교체 구문](alternation.md)을 참조하세요. `Open` 그룹이 정의되어 있는 경우 정규식 엔진은 입력 문자열에서 하위 식 `(?!)`를 찾으려고 시도합니다. `Open` 그룹은 중첩 구문이 짝이 맞지 않는 경우에만 정의해야 합니다. 따라서 입력 문자열에서 찾을 패턴은 항상 찾기가 실패하도록 만드는 패턴이어야 합니다. 이 경우 `(?!)`는 항상 실패하는 너비가 0인 부정 lookahead 어설션인데, 입력 문자열의 다음 위치에는 항상 암시적으로 빈 문자열이 있기 때문입니다.

이 예제에서 정규식 엔진은 다음 테이블에 표시된 것처럼 입력 문자열 "<abc><mno<xyz>>"를 평가합니다.

단계 | 패턴 | 결과
---- | ------- | ------ 
1 | `^` | 입력 문자열의 시작 부분에서 일치 항목 찾기를 시작합니다.
2 | `[^<>]*` | 왼쪽 꺾쇠괄호 앞에서 꺾쇠괄호가 아닌 문자를 찾습니다. 일치 항목이 없습니다. 
3 | `(((?'Open'<)` | "<abc>"에서 왼쪽 꺾쇠 괄호를 찾아 `Open` 그룹에 할당합니다.
4 | `[^<>]*` | "abc"를 찾습니다.
5 | `)+` | "<abc"는 캡처된 두 번째 그룹의 값입니다. 입력 문자열의 다음 문자가 왼쪽 꺾쇠괄호가 아니므로 정규식 엔진은 `(?'Open'<)[^<>]*)` 하위 패턴으로 루프백하지 않습니다.
6 | `((?'Close-Open'>)` | "<abc>"에서 오른쪽 꺾쇠 괄호를 찾고 `Open` 그룹과 오른쪽 꺾쇠 괄호 사이의 부분 문자열인 "abc"를 `Close` 그룹에 할당한 다음 `Open` 그룹의 현재 값("<")을 삭제하여 이 그룹을 비워 둡니다.
7 | `[^<>]*` | 오른쪽 꺾쇠괄호 뒤에서 꺾쇠괄호가 아닌 문자를 찾습니다. 일치 항목이 없습니다.
8 | `)+` | 캡처된 세 번째 그룹의 값은 ">"입니다. 입력 문자열의 다음 문자가 오른쪽 꺾쇠괄호가 아니므로 정규식 엔진은 `((?'Close-Open'>)[^<>]*)` 하위 패턴으로 루프백하지 않습니다.
9 | `)*` | 캡처된 첫 번째 그룹의 값은 "<abc>"입니다. 입력 문자열의 다음 문자가 왼쪽 꺾쇠 괄호이므로 정규식 엔진은 `(((?'Open'<)` 하위 패턴으로 루프백합니다.
10 | `(((?'Open'<)` | "<mno>"에서 왼쪽 꺾쇠 괄호를 찾아 `Open` 그룹에 할당합니다. 해당 `Group.Captures` 컬렉션에는 이제 단일 값 "<"가 있습니다.
11 | `[^<>]*` | "mno"를 찾습니다.
12 | `)+` | "<mno"는 캡처된 두 번째 그룹의 값입니다. 입력 문자열의 다음 문자가 왼쪽 꺾쇠괄호이므로 정규식 엔진은 `(?'Open'<)[^<>]*)` 하위 패턴으로 루프백합니다.
13 | `(((?'Open'<)` | "<xyz>"에서 왼쪽 꺾쇠 괄호를 찾아 `Open` 그룹에 할당합니다. 이제 `Group.Captures` 그룹의 `Open` 컬렉션에 두 개의 캡처, 즉 "<mno>"의 왼쪽 꺾쇠 괄호와 "<xyz>"의 왼쪽 꺾쇠 괄호가 포함되어 있습니다.
14 | `[^<>]*` | "xyz"를 찾습니다.
15 | `)+` | "<xyz"는 캡처된 두 번째 그룹의 값입니다. 입력 문자열의 다음 문자가 왼쪽 꺾쇠괄호가 아니므로 정규식 엔진은 `(?'Open'<)[^<>]*)` 하위 패턴으로 루프백하지 않습니다.
16 | `((?'Close-Open'>)` | "<xyz>"에서 오른쪽 꺾쇠 괄호를 찾습니다. "xyz"는 `Open` 그룹과 오른쪽 꺾쇠 괄호 사이의 부분 문자열을 `Close` 그룹에 할당하고 `Open` 그룹의 현재 값을 삭제합니다. 이전 캡처의 값("<mno>"의 왼쪽 꺾쇠 괄호)이 `Open` 그룹의 현재 값이 됩니다. 이제 `Captures` 그룹의 `Open` 컬렉션에 단일 캡처인 "<xyz>"의 왼쪽 꺾쇠 괄호가 포함되어 있습니다.
17 | `[^<>]*` | 꺾쇠괄호가 아닌 문자를 찾습니다. 일치 항목이 없습니다.
18 | `)+` | 캡처된 세 번째 그룹의 값은 ">"입니다. 입력 문자열의 다음 문자가 오른쪽 꺾쇠괄호이므로 정규식 엔진은 `((?'Close-Open'>)[^<>]*)` 하위 패턴으로 루프백합니다.
19 | `((?'Close-Open'>)` | "xyz>>"에서 최종 오른쪽 꺾쇠 괄호를 찾고 <xyz> 그룹과 오른쪽 꺾쇠 괄호 사이의 부분 문자열인 "mno`Open`"를 `Close` 그룹에 할당한 다음 `Open` 그룹의 현재 값을 삭제합니다. 이제 `Open` 그룹이 비어 있습니다.
20 | `[^<>]*` | 꺾쇠괄호가 아닌 문자를 찾습니다. 일치 항목이 없습니다.
21 | `)+` | 캡처된 세 번째 그룹의 값은 ">"입니다. 입력 문자열의 다음 문자가 오른쪽 꺾쇠괄호가 아니므로 정규식 엔진은 `((?'Close-Open'>)[^<>]*)` 하위 패턴으로 루프백하지 않습니다.
22 | `)*` | 캡처된 첫 번째 그룹의 값은 "<mno<xyz>>"입니다. 입력 문자열의 다음 문자가 왼쪽 꺾쇠괄호가 아니므로 정규식 엔진은 `(((?'Open'<)` 하위 패턴으로 루프백하지 않습니다.
23 | `(?(Open)(?!))` | `Open` 그룹이 정의되어 있지 않으므로 찾으려는 시도가 이루어지지 않습니다.
24 | `$` | 입력 문자열의 끝 부분을 찾습니다.

## <a name="noncapturing-groups"></a>비 캡처링 그룹

다음 그룹화 구문은 하위 식과 일치하는 부분 문자열을 캡처하지 않습니다.

```
**(?**:_subexpression_**)**
```

여기서 *subexpression*은 임의의 유효한 정규식 패턴입니다. 비 캡처링 그룹 구문은 일반적으로 수량자가 그룹에 적용될 때 사용되지만 그룹에 의해 캡처된 부분 문자열에는 관심을 두지 않습니다.

>[!NOTE]
> 정규식에 중첩된 그룹화 구문이 포함된 경우 외부 비 캡처링 그룹 구문은 내부 중첩된 그룹 구문에 적용되지 않습니다. 
 
다음 예제에서는 비 캡처링 그룹을 포함하는 정규식을 보여 줍니다. 출력에는 캡처된 그룹이 포함되지 않습니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"(?:\b(?:\w+)\W*)+\.";
      string input = "This is a short sentence.";
      Match match = Regex.Match(input, pattern);
      Console.WriteLine("Match: {0}", match.Value);
      for (int ctr = 1; ctr < match.Groups.Count; ctr++)
         Console.WriteLine("   Group {0}: {1}", ctr, match.Groups[ctr].Value);
   }
}
// The example displays the following output:
//       Match: This is a short sentence.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "(?:\b(?:\w+)\W*)+\."
      Dim input As String = "This is a short sentence."
      Dim match As Match = Regex.Match(input, pattern)
      Console.WriteLine("Match: {0}", match.Value)
      For ctr As Integer = 1 To match.Groups.Count - 1
         Console.WriteLine("   Group {0}: {1}", ctr, match.Groups(ctr).Value)
      Next
   End Sub
End Module
' The example displays the following output:
'       Match: This is a short sentence.
```

정규식 `(?:\b(?:\w+)\W*)+\.`는 마침표로 종료된 문장을 찾습니다. 정규식은 개별 단어가 아닌 문장에 초점을 두므로 그룹화 구문은 단독으로 수량자로 사용됩니다. 정규식 패턴은 다음 테이블과 같이 해석됩니다.

패턴 | 설명
------- | -----------
`\b` | 단어 경계에서 일치 항목 찾기를 시작합니다.
`(?:\w+)` | 하나 이상의 단어 문자를 찾습니다. 일치하는 텍스트를 캡처된 그룹에 할당하지 않습니다.
`\W*` | 0개 이상의 단어가 아닌 문자를 찾습니다.
`(?:\b(?:\w+)\W*)+` | 단어 경계에서 시작하는 하나 이상의 단어 문자 뒤에 0개 이상의 단어가 아닌 문자가 한 번 이상 나타나는 패턴을 찾습니다. 일치하는 텍스트를 캡처된 그룹에 할당하지 않습니다.
`\.` | 마침표를 찾습니다.
 
## <a name="group-options"></a>그룹 옵션

다음 그룹화 구문은 하위 식 내에서 지정된 옵션을 적용하거나 사용하지 않도록 설정합니다.

**(?imnsx-imnsx:**_subexpression_**)**

여기서 *subexpression*은 임의의 유효한 정규식 패턴입니다. 예를 들어, `(?i-s:)`는 대/소문자 구분하지 않음을 설정하고 한 줄 모드를 사용하지 않도록 설정합니다. 지정할 수 있는 인라인 옵션에 대한 자세한 내용은 [정규식 옵션](options.md)을 참조하세요.

> [!NOTE]
> [System.Text.RegularExpressions.Regex](xref:System.Text.RegularExpressions.Regex) 클래스 생성자 또는 정적 메서드를 사용하여 하위 식이 아니라 전체 정규식에 적용되는 옵션을 지정할 수 있습니다. 또한 `(?imnsx-imnsx)` 언어 구문을 사용하여 정규식에서 특정 지점 뒤에 적용되는 인라인 옵션을 지정할 수 있습니다.
 
그룹 옵션 구문은 캡처링 그룹이 아닙니다. 즉, *subexpression*에 의해 캡처된 문자열의 일부가 일치 항목에 포함되더라도 캡처된 그룹에 포함되지 않으며 [GroupCollection](xref:System.Text.RegularExpressions.GroupCollection) 개체를 채우는 데 사용되지도 않습니다.

예를 들어, 다음 예제의 정규식 `\b(?ix: d \w+)\s `는 문자 "d"로 시작하는 모든 단어를 식별할 때 대/소문자를 구분하지 않는 일치를 사용하도록 설정하고 패턴 공백을 무시하기 위해 그룹화 구문에 인라인 옵션을 사용합니다. 정규식은 다음 테이블과 같이 정의됩니다.

패턴 | 설명
------- | -----------
`\b` | 단어 경계에서 일치 항목 찾기를 시작합니다.
 `(?ix: d \w+)` | 이 패턴에서 대/소문자를 구분하지 않는 일치를 사용하고 공백을 무시하여 "d" 다음에 하나 이상의 단어 문자가 있는 일치 항목을 찾습니다. 
`\s` | 공백 문자를 찾습니다.
 
```csharp
string pattern = @"\b(?ix: d \w+)\s";
string input = "Dogs are decidedly good pets.";

foreach (Match match in Regex.Matches(input, pattern))
   Console.WriteLine("'{0}// found at index {1}.", match.Value, match.Index);
// The example displays the following output:
//    'Dogs // found at index 0.
//    'decidedly // found at index 9.      
```

```vb
Dim pattern As String = "\b(?ix: d \w+)\s"
Dim input As String = "Dogs are decidedly good pets."

For Each match As Match In Regex.Matches(input, pattern)
   Console.WriteLine("'{0}' found at index {1}.", match.Value, match.Index)
Next
' The example displays the following output:
'    'Dogs ' found at index 0.
'    'decidedly ' found at index 9. 
```

## <a name="zerowidth-positive-lookahead-assertions"></a>너비가 0인 긍정 lookahead 어설션

다음 그룹화 구문은 너비가 0인 긍정 lookahead 어설션을 정의합니다.

**(?**=*subexpression*__)__

여기서 *subexpression*은 임의의 정규식 패턴입니다. 찾기가 성공하려면 일치하는 부분 문자열이 일치 결과에 포함되지 않더라도 입력 문자열이 *subexpression*의 정규식 패턴과 일치해야 합니다. 너비가 0인 긍정 lookahead 어설션은 역추적하지 않습니다.

일반적으로 너비가 0인 긍정 lookahead 어설션은 정규식 패턴의 끝 부분에서 찾을 수 있으며, 일치가 발생하도록 문자열의 끝 부분에 있어야 하지만 일치에 포함되어서는 안 되는 부분 문자열을 정의합니다. 또한 이 어설션은 과도한 역추적을 방지하는 데 유용합니다. 너비가 0인 긍정 lookahead 어설션을 사용하여, 특정 캡처된 그룹이 해당 캡처된 그룹에 대해 정의된 패턴의 하위 집합과 일치하는 텍스트로 시작하도록 할 수 있습니다. 예를 들어, 캡처링 그룹이 연속 단어 문자와 일치하는 경우 너비가 0인 긍정 lookahead 어설션을 사용하여 첫 번째 문자가 대문자 영문자가 되도록 할 수 있습니다.

다음 예제에서는 너비가 0인 긍정 lookahead 어설션을 사용하여 입력 문자열에서 동사 "is" 앞에 오는 단어를 찾습니다. 

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"\b\w+(?=\sis\b)";
      string[] inputs = { "The dog is a Malamute.", 
                          "The island has beautiful birds.", 
                          "The pitch missed home plate.", 
                          "Sunday is a weekend day." };

      foreach (string input in inputs)
      {
         Match match = Regex.Match(input, pattern);
         if (match.Success)
            Console.WriteLine("'{0}' precedes 'is'.", match.Value);
         else
            Console.WriteLine("'{0}' does not match the pattern.", input); 
      }
   }
}
// The example displays the following output:
//    'dog' precedes 'is'.
//    'The island has beautiful birds.' does not match the pattern.
//    'The pitch missed home plate.' does not match the pattern.
//    'Sunday' precedes 'is'.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "\b\w+(?=\sis\b)"
      Dim inputs() As String = { "The dog is a Malamute.", _
                                 "The island has beautiful birds.", _
                                 "The pitch missed home plate.", _
                                 "Sunday is a weekend day." }

      For Each input As String In inputs
         Dim match As Match = Regex.Match(input, pattern)
         If match.Success Then
            Console.WriteLine("'{0}' precedes 'is'.", match.Value)
         Else
            Console.WriteLine("'{0}' does not match the pattern.", input) 
         End If     
      Next
   End Sub
End Module
' The example displays the following output:
'       'dog' precedes 'is'.
'       'The island has beautiful birds.' does not match the pattern.
'       'The pitch missed home plate.' does not match the pattern.
'       'Sunday' precedes 'is'.
```

정규식 \b\w+(?=\sis\b)는 다음 표와 같이 해석됩니다.

패턴 | 설명
------- | -----------
`\b` | 단어 경계에서 일치 항목 찾기를 시작합니다.
`\w+` | 하나 이상의 단어 문자를 찾습니다.
`(?=\sis\b)` | 단어 문자 뒤에 공백 문자와 문자열 "is"(단어 경계에서 끝남)가 오는지 여부를 확인합니다. 그럴 경우 찾기가 성공합니다.

## <a name="zerowidth-negative-lookahead-assertions"></a>너비가 0인 부정 lookahead 어설션

다음 그룹화 구문은 너비가 0인 부정 lookahead 어설션을 정의합니다.

**(?!**_subexpression_**)**

여기서 *subexpression*은 임의의 정규식 패턴입니다. 찾기가 성공하려면 일치하는 문자열이 일치 결과에 포함되지 않더라도 입력 문자열이 *subexpression*의 정규식 패턴과 일치해서는 안 됩니다. 

너비가 0인 부정 lookahead 어설션은 일반적으로 정규식의 시작 부분이나 끝 부분에 사용됩니다. 정규식의 시작 부분에서 이 어설션은 정규식의 시작 부분이 유사하지만 더 일반적인 찾으려는 패턴을 정의할 때 일치해서는 안 되는 특정 패턴을 정의할 수 있습니다. 이 경우 이 어설션은 흔히 역추적을 제한하는 데 사용됩니다. 정규식의 끝 부분에서 이 어설션은 일치의 끝 부분에서 발생할 수 없는 하위 식을 정의할 수 있습니다. 

다음 예제에서는 "un"으로 시작하지 않는 단어를 찾기 위해 정규식의 시작 부분에서 너비가 0인 lookahead 어설션을 사용하는 정규식을 정의합니다. 

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"\b(?!un)\w+\b";
      string input = "unite one unethical ethics use untie ultimate";
      foreach (Match match in Regex.Matches(input, pattern, RegexOptions.IgnoreCase))
         Console.WriteLine(match.Value);
   }
}
// The example displays the following output:
//       one
//       ethics
//       use
//       ultimate
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "\b(?!un)\w+\b"
      Dim input As String = "unite one unethical ethics use untie ultimate"
      For Each match As Match In Regex.Matches(input, pattern, RegexOptions.IgnoreCase)
         Console.WriteLine(match.Value)
      Next
   End Sub
End Module
' The example displays the following output:
'       one
'       ethics
'       use
'       ultimate
```

정규식 \b(?!un)\w+\b는 다음 표와 같이 해석됩니다.

패턴 | 설명
------- | -----------
`\b` | 단어 경계에서 일치 항목 찾기를 시작합니다.
`(?!un)` | 다음 두 문자가 "un"인지 확인합니다. 그러지 않은 경우 일치가 가능합니다.
`\w+` | 하나 이상의 단어 문자를 찾습니다.
`\b` | 단어 경계에서 일치 항목 찾기를 끝냅니다.
 
다음 예제에서는 문장 부호 문자로 끝나지 않는 단어를 찾기 위해 정규식의 끝 부분에서 너비가 0인 lookahead 어설션을 사용하는 정규식을 정의합니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"\b\w+\b(?!\p{P})";
      string input = "Disconnected, disjointed thoughts in a sentence fragment.";
      foreach (Match match in Regex.Matches(input, pattern))
         Console.WriteLine(match.Value);
   }
}
// The example displays the following output:
//       disjointed
//       thoughts
//       in
//       a
//       sentence
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "\b\w+\b(?!\p{P})"
      Dim input As String = "Disconnected, disjointed thoughts in a sentence fragment."
      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine(match.Value)
      Next   
   End Sub
End Module
' The example displays the following output:
'       disjointed
'       thoughts
'       in
'       a
'       sentence
```

정규식 `\b\w+\b(?!\p{P})`는 다음 테이블과 같이 해석됩니다.

패턴 | 설명
------- | -----------
`\b` | 단어 경계에서 일치 항목 찾기를 시작합니다.
`\w+` | 하나 이상의 단어 문자를 찾습니다.
`\b` | 단어 경계에서 일치 항목 찾기를 끝냅니다.
`\p{P})` | 다음 문자가 문장 부호 기호(예: 마침표 또는 쉼표)가 아닌 경우 찾기가 성공합니다.
 
## <a name="zerowidth-positive-lookbehind-assertions"></a>너비가 0인 긍정 lookbehind 어설션

다음 그룹화 구문은 너비가 0인 긍정 lookbehind 어설션을 정의합니다.

**(?<=**_subexpression_**)**

여기서 *subexpression*은 임의의 정규식 패턴입니다. 찾기가 성공하려면 이 일치 결과에 포함되지 않더라도 *subexpression*이 입력 문자열의 현재 위치 왼쪽에서 발생해야 합니다. 너비가 0인 긍정 lookbehind 어설션은 역추적하지 않습니다.

너비가 0인 긍정 lookbehind 어설션은 일반적으로 정규식의 시작 부분에 사용됩니다. 이 어설션이 정의하는 패턴은 이 패턴이 일치 결과에 포함되지 않더라도 일치에 대한 사전 조건입니다. 

예를 들어, 다음 예제에서는 21세기 연도의 마지막 두 자리를 찾습니다(즉, 숫자 "20"이 일치하는 문자열 앞에 와야 함).

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string input = "2010 1999 1861 2140 2009";
      string pattern = @"(?<=\b20)\d{2}\b";

      foreach (Match match in Regex.Matches(input, pattern))
         Console.WriteLine(match.Value);
   }
}
// The example displays the following output:
//       10
//       09
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim input As String = "2010 1999 1861 2140 2009"
      Dim pattern As String = "(?<=\b20)\d{2}\b"

      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine(match.Value)
      Next      
   End Sub
End Module
' The example displays the following output:
'       10
'       09
```

정규식 패턴 `(?<=\b20)\d{2}\b`는 다음 테이블과 같이 해석됩니다.

패턴 | 설명
------- | -----------
`\d{2}` | 두 개의 10진수를 찾습니다.
`{?<=\b20)` | 단어 경계에서 두 개의 10진수 앞에 10진수 "20"이 있는 경우 일치 항목 찾기를 계속합니다.
`\b` | 단어 경계에서 일치 항목 찾기를 끝냅니다.
 
너비가 0인 긍정 lookbehind 어설션은 캡처된 그룹의 마지막 문자(하나 또는 여러 개)가 해당 그룹의 정규식 패턴과 일치하는 문자의 하위 집합이어야 하는 경우 역추적을 제한하는 데도 사용됩니다. 예를 들어, 그룹이 모든 연속 단어 문자를 캡처하는 경우 너비가 0인 긍정 lookbehind 어설션을 사용하여 마지막 문자가 사전순이 되도록 할 수 있습니다. 

## <a name="zerowidth-negative-lookbehind-assertions"></a>너비가 0인 부정 lookbehind 어설션

다음 그룹화 구문은 너비가 0인 부정 lookbehind 어설션을 정의합니다.

**(?<!**_subexpression_**)**

여기서 *subexpression*은 임의의 정규식 패턴입니다. 찾기가 성공하려면 *subexpression*이 입력 문자열의 현재 위치 왼쪽에서 발생해서는 안 됩니다. 그러나 하위 식과 일치하지 않는 모든 부분 문자열은 일치 결과에 포함되지 않습니다.

너비가 0인 부정 lookbehind 어설션은 일반적으로 정규식의 시작 부분에 사용됩니다. 이 어설션이 정의하는 패턴은 뒤에 오는 문자열에서 일치가 불가능하도록 합니다. 또한 이 어설션은 캡처된 그룹의 마지막 문자(하나 또는 여러 개)가 해당 그룹의 정규식 패턴과 일치하는 하나 이상의 문자가 아니어야 하는 경우 역추적을 제한하는 데도 사용됩니다. 예를 들어, 그룹이 모든 연속 단어 문자를 캡처하는 경우 너비가 0인 긍정 lookbehind 어설션을 사용하여 마지막 문자가 밑줄(_)이 아니도록 할 수 있습니다.

다음 예제에서는 주말이 아닌 평일인 날짜를 찾습니다(즉, 토요일 및 일요일이 아님). 

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string[] dates = { "Monday February 1, 2010", 
                         "Wednesday February 3, 2010", 
                         "Saturday February 6, 2010", 
                         "Sunday February 7, 2010", 
                         "Monday, February 8, 2010" };
      string pattern = @"(?<!(Saturday|Sunday) )\b\w+ \d{1,2}, \d{4}\b";

      foreach (string dateValue in dates)
      {
         Match match = Regex.Match(dateValue, pattern);
         if (match.Success)
            Console.WriteLine(match.Value);
      }      
   }
}
// The example displays the following output:
//       February 1, 2010
//       February 3, 2010
//       February 8, 2010
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim dates() As String = { "Monday February 1, 2010", _
                                "Wednesday February 3, 2010", _
                                "Saturday February 6, 2010", _
                                "Sunday February 7, 2010", _
                                "Monday, February 8, 2010" }
      Dim pattern As String = "(?<!(Saturday|Sunday) )\b\w+ \d{1,2}, \d{4}\b"

      For Each dateValue As String In dates
         Dim match As Match = Regex.Match(dateValue, pattern)
         If match.Success Then
            Console.WriteLine(match.Value)
         End If   
      Next      
   End Sub
End Module
' The example displays the following output:
'       February 1, 2010
'       February 3, 2010
'       February 8, 2010
```

정규식 패턴 `(?<!(Saturday|Sunday) )\b\w+ \d{1,2}, \d{4}\b`는 다음 테이블과 같이 해석됩니다.

패턴 | 설명
------- | -----------
`\b` | 단어 경계에서 일치 항목 찾기를 시작합니다.
`\w+` | 하나 이상의 단어 문자 다음에 공백 문자가 있는 일치 항목을 찾습니다.
`\d{1,2},` | 한 개 또는 두 개의 10진수 다음에 공백 문자 하나와 쉼표 하나가 있는 일치 항목을 찾습니다.
`\d{4}\b` | 네 개의 10진수를 찾고 단어 경계에서 일치 항목 찾기를 끝냅니다.
`(?<!(Saturday|Sunday) )` | 일치 항목 앞에 문자열 "Saturday" 또는 "Sunday"가 아닌 문자열이 있고 해당 문자열 뒤에 공백이 하나 있으면 찾기가 성공한 것입니다.

## <a name="nonbacktracking-subexpressions"></a>역추적하지 않는 하위 식

다음 그룹화 구문은 역추적하지 않는 하위 식("greedy" 하위 식이라고도 함)을 나타냅니다.

**(?>**_subexpression_**)**

여기서 *subexpression*은 임의의 정규식 패턴입니다.

대개 정규식에 선택적 또는 대체 일치 패턴이 포함되어 있고 찾기가 실패하는 경우 정규식 엔진이 입력 문자열을 패턴과 일치시키기 위해 여러 방향으로 분기할 수 있습니다. 첫 번째 분기를 사용하여 일치 항목을 찾을 수 없는 경우 정규식 엔진은 첫 번째 일치 항목을 사용한 지점을 백업하거나 해당 지점으로 역추적한 다음 두 번째 분기를 사용하여 찾기를 시도할 수 있습니다. 이 프로세스는 모든 분기가 시도될 때까지 계속될 수 있습니다.

**(?>**_subexpression_**)** 언어 구문은 역추적을 사용하지 않도록 설정합니다. 정규식 엔진은 입력 문자열에서 가능한 한 많은 문자를 찾습니다. 일치가 더 이상 불가능한 경우 정규식 엔진은 대체 패턴 일치를 시도하기 위해 역추적하지 않습니다. (즉, 하위 식은 하위 식 단독으로 일치하는 문자열과만 일치합니다. 정규식 엔진은 하위 식을 기반으로 한 문자열 및 이러한 문자열 뒤에 나오는 하위 식은 찾으려고 시도하지 않습니다.) 

이 옵션은 역추적이 실패할 것임을 아는 경우에 사용하는 것이 좋습니다. 정규식 엔진이 불필요한 검색을 수행하지 않도록 하면 성능이 향상됩니다. 

다음 예제에서는 역추적하지 않는 하위 식이 패턴 일치 결과를 수정하는 방법을 보여 줍니다. 역추적 정규식은 단어 경계에서 뒤에 동일한 문자가 하나 더 있는 일련의 반복된 문자를 찾지만 역추적하지 않는 정규식은 그렇지 못합니다. 

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string[] inputs = { "cccd.", "aaad", "aaaa" };
      string back = @"(\w)\1+.\b";
      string noback = @"(?>(\w)\1+).\b";

      foreach (string input in inputs)
      {
         Match match1 = Regex.Match(input, back);
         Match match2 = Regex.Match(input, noback);
         Console.WriteLine("{0}: ", input);

         Console.Write("   Backtracking : ");
         if (match1.Success)
            Console.WriteLine(match1.Value);
         else
            Console.WriteLine("No match");

         Console.Write("   Nonbacktracking: ");
         if (match2.Success)
            Console.WriteLine(match2.Value);
         else
            Console.WriteLine("No match");
      }
   }
}
// The example displays the following output:
//    cccd.:
//       Backtracking : cccd
//       Nonbacktracking: cccd
//    aaad:
//       Backtracking : aaad
//       Nonbacktracking: aaad
//    aaaa:
//       Backtracking : aaaa
//       Nonbacktracking: No match
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim inputs() As String = { "cccd.", "aaad", "aaaa" }
      Dim back As String = "(\w)\1+.\b"
      Dim noback As String = "(?>(\w)\1+).\b"

      For Each input As String In inputs
         Dim match1 As Match = Regex.Match(input, back)
         Dim match2 As Match = Regex.Match(input, noback)
         Console.WriteLine("{0}: ", input)

         Console.Write("   Backtracking : ")
         If match1.Success Then
            Console.WriteLine(match1.Value)
         Else
            Console.WriteLine("No match")
         End If

         Console.Write("   Nonbacktracking: ")
         If match2.Success Then
            Console.WriteLine(match2.Value)
         Else
            Console.WriteLine("No match")
         End If
      Next
   End Sub
End Module
' The example displays the following output:
'    cccd.:
'       Backtracking : cccd
'       Nonbacktracking: cccd
'    aaad:
'       Backtracking : aaad
'       Nonbacktracking: aaad
'    aaaa:
'       Backtracking : aaaa
'       Nonbacktracking: No match
```

역추적하지 않는 정규식 `(?>(\w)\1+).\b`는 다음 테이블과 같이 정의됩니다.

패턴 | 설명
------- | -----------
`(\w)` | 단일 단어 문자를 찾아 첫 번째 캡처링 그룹에 할당합니다.
`\1+` | 캡처된 첫 번째 부분 문자열의 값을 한 번 이상 찾습니다.
`.` | 임의의 문자를 찾습니다.
`\b` | 단어 경계에서 일치 항목 찾기를 끝냅니다.
`(?>(\w)\1+)` | 하나 이상의 중복된 단어 문자를 찾지만 단어 경계에서 마지막 문자를 일치시키기 위해 역추적하지 않습니다.
 
## <a name="grouping-constructs-and-regular-expression-objects"></a>그룹화 구문 및 정규식 개체

정규식 캡처링 그룹과 일치하는 부분 문자열은 [System.Text.RegularExpressions.Group](xref:System.Text.RegularExpressions.Group) 개체로 표현됩니다. 이 개체는 [Match.Groups](xref:System.Text.RegularExpressions.Match.Groups) 속성에서 반환하는 [System.Text.RegularExpressions.GroupCollection](xref:System.Text.RegularExpressions.GroupCollection) 개체에서 검색할 수 있습니다. [GroupCollection](xref:System.Text.RegularExpressions.GroupCollection) 개체는 다음과 같이 채워집니다.

* 컬렉션의 첫 번째 [Group](xref:System.Text.RegularExpressions.Group) 개체(인덱스 0에 있는 개체)는 전체 일치를 나타냅니다.

* 다음 [Group](xref:System.Text.RegularExpressions.Group) 개체 집합은 명명되지 않은(번호가 매겨진) 캡처링 그룹을 나타냅니다. 이러한 그룹은 정규식에서 정의된 순서로 왼쪽에서 오른쪽으로 나타납니다. 이러한 그룹의 인덱스 값 범위는 1에서 컬렉션에 있는 명명되지 않은 캡처링 그룹 수까지입니다. (특정 그룹의 인덱스는 번호가 매겨진 해당 역참조와 같습니다. 역참조에 대한 자세한 내용은 [정규식의 역참조 구문](backreference.md)을 참조하세요.

* 최종 [Group](xref:System.Text.RegularExpressions.Group) 개체 집합은 명명된 캡처링 그룹을 나타냅니다. 이러한 그룹은 정규식에서 정의된 순서로 왼쪽에서 오른쪽으로 나타납니다. 명명된 첫 번째 캡처링 그룹의 인덱스 값은 명명되지 않은 마지막 캡처링 그룹의 인덱스보다 1 큽니다. 정규식에 명명되지 않은 캡처링 그룹이 없는 경우 명명된 첫 번째 캡처링 그룹의 인덱스 값은 1입니다. 


캡처링 그룹에 수량자를 적용하는 경우 해당 [Group](xref:System.Text.RegularExpressions.Group) 개체의 [Capture.Value](xref:System.Text.RegularExpressions.Capture.Value), [Capture.Index](xref:System.Text.RegularExpressions.Capture.Index) 및 [Capture.Length](xref:System.Text.RegularExpressions.Capture.Length) 속성은 캡처링 그룹에 의해 캡처된 마지막 부분 문자열을 반영합니다. [Group.Captures](xref:System.Text.RegularExpressions.Group.Captures) 속성에서 반환하는 [CaptureCollection](xref:System.Text.RegularExpressions.CaptureCollection) 개체의 수량자를 포함하는 그룹에 의해 캡처된 부분 문자열의 전체 집합을 검색할 수 있습니다.

다음 예제에서는 [Group](xref:System.Text.RegularExpressions.Group) 개체와 [Capture](xref:System.Text.RegularExpressions.Capture) 개체 간의 관계를 명확하게 보여 줍니다. 

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"(\b(\w+)\W+)+";
      string input = "This is a short sentence.";
      Match match = Regex.Match(input, pattern);
      Console.WriteLine("Match: '{0}'", match.Value);
      for (int ctr = 1; ctr < match.Groups.Count; ctr++)
      {
         Console.WriteLine("   Group {0}: '{1}'", ctr, match.Groups[ctr].Value);
         int capCtr = 0;
         foreach (Capture capture in match.Groups[ctr].Captures)
         {
            Console.WriteLine("      Capture {0}: '{1}'", capCtr, capture.Value);
            capCtr++;
         }
      }
   }
}
// The example displays the following output:
//       Match: 'This is a short sentence. '
//          Group 1: 'sentence.'
//             Capture 0: 'This '
//             Capture 1: 'is '
//             Capture 2: 'a '
//             Capture 3: 'short '
//             Capture 4: 'sentence.'
//          Group 2: 'sentence'
//             Capture 0: 'This'
//             Capture 1: 'is'
//             Capture 2: 'a'
//             Capture 3: 'short'
//             Capture 4: 'sentence'
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "(\b(\w+)\W+)+"
      Dim input As String = "This is a short sentence."
      Dim match As Match = Regex.Match(input, pattern)
      Console.WriteLine("Match: '{0}'", match.Value)
      For ctr As Integer = 1 To match.Groups.Count - 1
         Console.WriteLine("   Group {0}: '{1}'", ctr, match.Groups(ctr).Value)
         Dim capCtr As Integer = 0
         For Each capture As Capture In match.Groups(ctr).Captures
            Console.WriteLine("      Capture {0}: '{1}'", capCtr, capture.Value)
            capCtr += 1
         Next
      Next
   End Sub
End Module
' The example displays the following output:
'       Match: 'This is a short sentence.'
'          Group 1: 'sentence.'
'             Capture 0: 'This '
'             Capture 1: 'is '
'             Capture 2: 'a '
'             Capture 3: 'short '
'             Capture 4: 'sentence.'
'          Group 2: 'sentence'
'             Capture 0: 'This'
'             Capture 1: 'is'
'             Capture 2: 'a'
'             Capture 3: 'short'
'             Capture 4: 'sentence'
```

정규식 패턴 `\b(\w+)\W+)+`는 문자열에서 개별 단어를 추출합니다. 이 패턴은 다음 표에서와 같이 정의됩니다.

패턴 | 설명
------- | -----------
`\b` | 단어 경계에서 일치 항목 찾기를 시작합니다.
`(\w+)` | 하나 이상의 단어 문자를 찾습니다. 이러한 문자는 함께 하나의 단어를 형성합니다. 이 그룹은 두 번째 캡처링 그룹입니다.
`\W+` | 하나 이상의 단어가 아닌 문자를 찾습니다.
`(\w+)\W+)+` | 하나 이상의 단어 문자 다음에 하나 이상의 단어가 아닌 문자가 한 번 이상 나타나는 패턴을 찾습니다. 이 그룹은 첫 번째 캡처링 그룹입니다.
 
첫 번째 캡처링 그룹은 문장의 각 단어를 찾습니다. 두 번째 캡처링 그룹은 각 단어와 이러한 단어 뒤에 나오는 문장 부호 및 공백을 함께 찾습니다. 인덱스가 2인 [Group](xref:System.Text.RegularExpressions.Group) 개체는 두 번째 캡처링 그룹과 일치하는 텍스트에 대한 정보를 제공합니다. 캡처링 그룹에 의해 캡처된 단어의 전체 집합은 [Group.Captures](xref:System.Text.RegularExpressions.Group.Captures) 속성에서 반환된 [CaptureCollection](xref:System.Text.RegularExpressions.CaptureCollection) 개체에서 제공합니다.

## <a name="see-also"></a>참고 항목

[정규식 언어 - 빠른 참조](quick-ref.md)

[정규식의 역추적](backtracking.md)



<!--HONumber=Nov16_HO3-->


