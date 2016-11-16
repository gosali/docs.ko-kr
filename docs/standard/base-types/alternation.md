---
title: "정규식의 교체 구문"
description: "정규식의 교체 구문"
keywords: .NET, .NET Core
author: stevehoag
ms.author: shoag
manager: wpickett
ms.date: 07/28/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 59ffac4d-fc6e-461f-8783-d9f8dc88ce2c
translationtype: Human Translation
ms.sourcegitcommit: b20713600d7c3ddc31be5885733a1e8910ede8c6
ms.openlocfilehash: 05b72d6a3d8ebdfb3808ba2f9a47476942686de2

---

# <a name="alternation-constructs-in-regular-expressions"></a>정규식의 교체 구문

교체 구문은 either/or 또는 조건부 일치를 허용하도록 정규식을 수정합니다. .NET에서는 다음 세 가지 교체 구문을 지원합니다.

* **|**를 사용한 패턴 일치

* **(?(**_expression_**)**_yes_**|**_no_**)**를 사용한 조건부 일치

* 유효한 캡처 그룹을 기준으로 조건부 일치

## <a name="pattern-matching-with-"></a>|를 사용한 패턴 일치

세로 막대(|) 문자를 사용하여 일련의 패턴 중 하나를 찾을 수 있습니다. 여기서 | 문자는 각 패턴을 구분합니다. 

긍정 문자 클래스처럼 | 문자를 사용하여 여러 단일 문자 중 하나를 찾을 수 있습니다. 다음 예제에서는 긍정 문자 클래스 및 either/or 패턴 일치 둘 다를 | 문자와 함께 사용하여 문자열에서 단어 "gray" 또는 "grey"를 찾습니다. 이 경우 | 문자는 더 자세한 정규식을 생성합니다. 

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      // Regular expression using character class.
      string pattern1 = @"\bgr[ae]y\b";
      // Regular expression using either/or.
      string pattern2 = @"\bgr(a|e)y\b";

      string input = "The gray wolf blended in among the grey rocks.";
      foreach (Match match in Regex.Matches(input, pattern1))
         Console.WriteLine("'{0}' found at position {1}", 
                           match.Value, match.Index);
      Console.WriteLine();
      foreach (Match match in Regex.Matches(input, pattern2))
         Console.WriteLine("'{0}' found at position {1}", 
                           match.Value, match.Index);
   }
}
// The example displays the following output:
//       'gray' found at position 4
//       'grey' found at position 35
//       
//       'gray' found at position 4
//       'grey' found at position 35           
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      ' Regular expression using character class.
      Dim pattern1 As String = "\bgr[ae]y\b"
      ' Regular expression using either/or.
      Dim pattern2 As String = "\bgr(a|e)y\b"

      Dim input As String = "The gray wolf blended in among the grey rocks."
      For Each match As Match In Regex.Matches(input, pattern1)
         Console.WriteLine("'{0}' found at position {1}", _
                           match.Value, match.Index)
      Next      
      Console.WriteLine()
      For Each match As Match In Regex.Matches(input, pattern2)
         Console.WriteLine("'{0}' found at position {1}", _
                           match.Value, match.Index)
      Next      
   End Sub
End Module
' The example displays the following output:
'       'gray' found at position 4
'       'grey' found at position 35
'       
'       'gray' found at position 4
'       'grey' found at position 35 
```

| 문자를 사용하는 정규식인 `\bgr(a|e)y\b,`는 다음 표와 같이 해석됩니다.

패턴 | 설명
------- | -----------
`\b` | 단어 경계를 시작합니다.
`gr` | 문자 "gr"을 찾습니다.
`(a|e)` | "a" 또는 "e"를 찾습니다.
`y\b` | 단어 경계에서 "y"를 찾습니다.


| 문자를 사용하여 문자 리터럴과 정규식 언어 요소의 조합을 포함할 수 있는 여러 문자나 하위 식을 통해 either/or 일치 항목을 찾을 수도 있습니다. 문자 클래스는 이 기능을 제공하지 않습니다. 다음 예제에서는 | 문자를 사용하여 미국 SSN(사회 보장 번호)(*ddd-dd-dddd* 형식의 9자리 숫자) 또는 미국 EIN(고용주 식별 번호)(*dd-ddddddd* 형식의 9자리 숫자)을 추출합니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"\b(\d{2}-\d{7}|\d{3}-\d{2}-\d{4})\b";
      string input = "01-9999999 020-333333 777-88-9999";
      Console.WriteLine("Matches for {0}:", pattern);
      foreach (Match match in Regex.Matches(input, pattern))
         Console.WriteLine("   {0} at position {1}", match.Value, match.Index);
   }
}
// The example displays the following output:
//       Matches for \b(\d{2}-\d{7}|\d{3}-\d{2}-\d{4})\b:
//          01-9999999 at position 0
//          777-88-9999 at position 22
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "\b(\d{2}-\d{7}|\d{3}-\d{2}-\d{4})\b"
      Dim input As String = "01-9999999 020-333333 777-88-9999"
      Console.WriteLine("Matches for {0}:", pattern)
      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine("   {0} at position {1}", match.Value, match.Index)
      Next   
   End Sub
End Module
' The example displays the following output:
'       Matches for \b(\d{2}-\d{7}|\d{3}-\d{2}-\d{4})\b:
'          01-9999999 at position 0
'          777-88-9999 at position 22
```

정규식 `\b(\d{2}-\d{7}|\d{3}-\d{2}-\d{4})\b`는 다음 테이블과 같이 해석됩니다.

패턴 | 설명
------- | -----------
`\b` | 단어 경계를 시작합니다.
`(\d{2}-\d{7}|;\d{3}-\d{2}-\d{4})` | 10진수 2개, 하이픈, 10진수 7개 순의 일치 항목이나 10진수 3개, 하이픈, 10진수 2개, 또 다른 하이픈, 10진수 4개 순의 일치 항목을 찾습니다. 
`\d` | 단어 경계에서 일치 항목 찾기를 끝냅니다.
                                         
## <a name="conditional-matching-with-an-expression"></a>식을 사용한 조건부 일치

이 언어 요소는 초기 패턴을 찾을지 여부에 따라 두 패턴의 하나를 찾으려고 합니다. 사용되는 구문은 다음과 같습니다.

**(?(**_expression_**)**_yes_**|**_no_**)**

여기서 *expression*은 일치 항목을 찾을 초기 패턴이고, *yes*는 expression과 일치할 경우 일치 항목을 찾을 패턴이고, *no*는 *expression*이 일치하지 않을 경우 일치 항목을 찾을 선택적 패턴입니다. 정규식 엔진은 *expression*을 너비가 0인 어설션으로 처리합니다. 즉, 정규식 엔진은 *expression*을 계산한 후 입력 스트림에서 앞으로 이동하지 않습니다. 따라서 이 구문은 다음 구문과 같습니다.

**(?(?**=_expression_**)**_yes_**|**_no_**)**

여기서 **(?**=_expression_**)**은 너비가 0인 어설션 구문입니다. (자세한 내용은 [정규식의 그룹화 구문](grouping.md)을 참조하세요.) 정규식 엔진이 *expression*을 앵커(너비가 0인 어설션)로 해석하므로 *expression*은 너비가 0인 어설션(자세한 내용은 [정규식의 앵커](anchors.md) 참조) 또는 *yes*에도 포함되는 하위 식이어야 합니다. 그렇지 않으면 *yes* 패턴을 찾을 수 없습니다. 

> [!NOTE]
> *expression*이 이름이나 숫자가 지정된 캡처링 그룹인 경우 교체 구문은 캡처 테스트로 해석됩니다. 자세한 내용은 다음 섹션인 [유효한 캡처 그룹을 기준으로 조건부 일치](#conditional-matching-based-on-a-valid-captured-group)를 참조하세요. 즉, 정규식 엔진은 캡처된 하위 문자열을 찾으려고 하지 않지만, 대신 그룹의 존재 여부를 테스트합니다.
 

다음 예제는 이전 섹션에 있는 예제의 변형입니다. 조건부 일치를 사용하여 단어 경계 뒤의 처음 문자 3개가 숫자 2개, 하이픈 순의 문자열인지를 확인합니다. 해당 문자열이면 미국 EIN(고용주 식별 번호)을 찾으려고 합니다. 그러지 않으면 미국 SSN(사회 보장 번호)을 찾으려고 합니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"\b(?(\d{2}-)\d{2}-\d{7}|\d{3}-\d{2}-\d{4})\b";
      string input = "01-9999999 020-333333 777-88-9999";
      Console.WriteLine("Matches for {0}:", pattern);
      foreach (Match match in Regex.Matches(input, pattern))
         Console.WriteLine("   {0} at position {1}", match.Value, match.Index);
   }
}
// The example displays the following output:
//       Matches for \b(\d{2}-\d{7}|\d{3}-\d{2}-\d{4})\b:
//          01-9999999 at position 0
//          777-88-9999 at position 22
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "\b(?(\d{2}-)\d{2}-\d{7}|\d{3}-\d{2}-\d{4})\b"
      Dim input As String = "01-9999999 020-333333 777-88-9999"
      Console.WriteLine("Matches for {0}:", pattern)
      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine("   {0} at position {1}", match.Value, match.Index)
      Next   
   End Sub
End Module
' The example displays the following output:
'       Matches for \b(?(\d{2}-)\d{2}-\d{7}|\d{3}-\d{2}-\d{4})\b:
'          01-9999999 at position 0
'          777-88-9999 at position 22
```

정규식 패턴 `\b(?(\d{2}-)\d{2}-\d{7}|\d{3}-\d{2}-\d{4})\b`는 다음 테이블과 같이 해석됩니다.

패턴 | 설명
------- | -----------
`\b` | 단어 경계를 시작합니다.
`(?(\d{2}-)` | 다음 문자 3개가 숫자 2개, 하이픈 순으로 구성되는지를 확인합니다.
`\d{2}-\d{7}` | 이전 패턴이 일치하면 숫자 2개, 하이픈, 숫자 7개 순의 일치 항목을 찾습니다.
`\d{3}-\d{2}-\d{4}` | 이전 패턴이 일치하지 않으면 10진수 3개, 하이픈, 10진수 2개, 또 다른 하이픈, 10진수 4개 순의 일치 항목을 찾습니다. 
`\b` | 단어 경계를 찾습니다.
 
## <a name="conditional-matching-based-on-a-valid-captured-group"></a>유효한 캡처 그룹을 기준으로 조건부 일치

이 언어 요소는 지정된 캡처링 그룹에 일치시켰는지 여부에 따라 두 패턴 중 하나에 일치시키려고 시도합니다. 사용되는 구문은 다음과 같습니다.

**(?(**_name_**)**_yes_**|**_no_**)**

또는

**(?(**_number_**)**_yes_**|**_no_**)**

여기서 *name*은 이름이고, *number*는 캡처링 그룹 수이고, *yes*는 name 또는 number에 일치 항목이 있을 경우 일치 항목을 찾을 식이고, *no*는 일치 항목이 없을 경우 일치 항목을 찾을 선택적 식입니다.

*name*이 정규식 패턴에서 사용되는 캡처링 그룹의 이름에 해당하지 않는 경우에는 교체 구문이 앞 섹션에서 설명한 대로 식 테스트로 해석됩니다. 일반적으로 이는 expression이 `false`로 계산됨을 의미합니다. `number`가 정규식 패턴에 사용되는 번호가 매겨진 캡처링 그룹에 해당하지 않는 경우 정규식 엔진이 [ArgumentException](xref:System.ArgumentException)을 throw합니다.

다음 예제는 이전 섹션에 있는 예제의 변형입니다. 숫자 2개, 하이픈 순으로 구성된 `n2`라는 캡처 그룹을 사용합니다. 교체 구문은 입력 문자열에서 이 캡처 그룹이 일치했는지를 테스트합니다. 일치한다면 교체 구문은 9자리 미국 EIN(고용주 식별 번호)을 찾으려고 합니다. 일치하지 않는다면 9자리 미국 SSN(사회 보장 번호)을 찾으려고 합니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"\b(?<n2>\d{2}-)*(?(n2)\d{7}|\d{3}-\d{2}-\d{4})\b";
      string input = "01-9999999 020-333333 777-88-9999";
      Console.WriteLine("Matches for {0}:", pattern);
      foreach (Match match in Regex.Matches(input, pattern))
         Console.WriteLine("   {0} at position {1}", match.Value, match.Index);
   }
}
// The example displays the following output:
//       Matches for \b(?<n2>\d{2}-)*(?(n2)\d{7}|\d{3}-\d{2}-\d{4})\b:
//          01-9999999 at position 0
//          777-88-9999 at position 22
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "\b(?<n2>\d{2}-)*(?(n2)\d{7}|\d{3}-\d{2}-\d{4})\b"
      Dim input As String = "01-9999999 020-333333 777-88-9999"
      Console.WriteLine("Matches for {0}:", pattern)
      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine("   {0} at position {1}", match.Value, match.Index)
      Next   
   End Sub
End Module
```

정규식 패턴 `\b(?<n2>\d{2}-)*(?(n2)\d{7}|\d{3}-\d{2}-\d{4})\b`는 다음 테이블과 같이 해석됩니다.

패턴 | 설명
------- | -----------
`\b` | 단어 경계를 시작합니다.
`(?<n2>\d{2}-)*` | 숫자 2개, 하이픈 순의 일치 항목 0개 또는 1개를 찾습니다. 이 캡처링 그룹의 이름을 `n2`로 지정합니다.
`(?(n2)` | `n2`가 입력 문자열에서 일치하는지 여부를 테스트합니다. 
`)\d{7}` | `n2`가 일치하는 경우 일곱 개의 10진수를 일치시킵니다.
`|;\d{3}-\d{2}-\d{4}` | `n2`가 일치하지 않는 경우 세 10진수, 하이픈, 두 10진수, 다른 하이픈 및 네 10진수를 일치시킵니다. 
`\b` | 단어 경계를 찾습니다.
 
다음 예제에서는 명명된 그룹 대신 번호가 있는 그룹을 사용하는 이 예제의 변형을 보여 줍니다. 정규식 패턴은 `\b(\d{2}-)*(?(1)\d{7}|\d{3}-\d{2}-\d{4})\b`입니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"\b(\d{2}-)*(?(1)\d{7}|\d{3}-\d{2}-\d{4})\b";
      string input = "01-9999999 020-333333 777-88-9999";
      Console.WriteLine("Matches for {0}:", pattern);
      foreach (Match match in Regex.Matches(input, pattern))
         Console.WriteLine("   {0} at position {1}", match.Value, match.Index);
   }
}
// The example display the following output:
//       Matches for \b(\d{2}-)*(?(1)\d{7}|\d{3}-\d{2}-\d{4})\b:
//          01-9999999 at position 0
//          777-88-9999 at position 22
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "\b(\d{2}-)*(?(1)\d{7}|\d{3}-\d{2}-\d{4})\b"
      Dim input As String = "01-9999999 020-333333 777-88-9999"
      Console.WriteLine("Matches for {0}:", pattern)
      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine("   {0} at position {1}", match.Value, match.Index)
      Next   
   End Sub
End Module
' The example displays the following output:
'       Matches for \b(\d{2}-)*(?(1)\d{7}|\d{3}-\d{2}-\d{4})\b:
'          01-9999999 at position 0
'          777-88-9999 at position 22
```

참고 항목

[정규식 언어 - 빠른 참조](quick-ref.md)




<!--HONumber=Nov16_HO1-->


