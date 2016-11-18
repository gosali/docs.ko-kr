---
title: "정규식의 역참조 구문"
description: "정규식의 역참조 구문"
keywords: .NET, .NET Core
author: stevehoag
ms.author: shoag
manager: wpickett
ms.date: 07/28/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: c453ed78-650f-4c3c-9ab4-9d89d250bf88
translationtype: Human Translation
ms.sourcegitcommit: b20713600d7c3ddc31be5885733a1e8910ede8c6
ms.openlocfilehash: 757c8c4e71bbafb12c8add925723daf1a24e06d1

---

# <a name="backreference-constructs-in-regular-expressions"></a>정규식의 역참조 구문

역참조는 문자열 내에서 반복된 문자 또는 부분 문자열을 식별하는 편리한 방법을 제공합니다. 예를 들어 입력 문자열에 임의의 부분 문자열이 여러 번 포함되어 있으면 첫 번째 발생을 캡처링 그룹과 일치시킨 다음 역참조를 사용하여 부분 문자열의 후속 발생을 일치시킬 수 있습니다. 

> [!NOTE]
> 대체 문자열에서 명명된 캡처링 그룹과 번호가 매겨진 캡처링 그룹을 참조하기 위해 별도의 구문이 사용됩니다. 자세한 내용은 [정규식의 대체](substitutions.md)를 참조하세요.
 
.NET에서는 번호가 매겨진 캡처링 그룹과 명명된 캡처링 그룹을 참조하기 위해 별도의 언어 요소를 정의합니다. 캡처링 그룹에 대한 자세한 내용은 [정규식의 그룹화 구문](grouping.md)을 참조하세요.

## <a name="numbered-backreferences"></a>번호가 매겨진 역참조

번호가 매겨진 역참조에는 다음 구문이 사용됩니다.

**\**_number_

여기서 *number*는 정규식에서 캡처링 그룹의 서수 위치입니다. 예를 들어 `\4`는 네 번째 캡처링 그룹의 내용을 찾습니다. *number*가 정규식 패턴에서 정의되지 않은 경우 구문 분석 오류가 발생하고 정규식 엔진이 [ArgumentException](xref:System.ArgumentException)을 throw합니다. 예를 들어 `\b(\w+)\s\1` 정규식은 `(\w+)`가 식의 첫 번째이자 유일한 캡처링 그룹이기 때문에 유효합니다. 반면에 `\b(\w+)\s\2`는 `\2`로 번호가 매겨진 캡처링 그룹이 없기 때문에 유효하지 않으며 인수 예외를 throw합니다.

8진수 이스케이프 코드(예: `\16`)와 동일한 표기법을 사용하는 **\**_number_ 역참조 간의 모호성에 유의하세요. 이러한 모호성은 다음과 같이 해결됩니다.

* `\1`에서 `\9` 사이의 식은 항상 8진수 코드가 아니라 역참조로 해석됩니다.

* 다중 숫자 식의 첫 번째 숫자가 8 또는 9이면(예: `\80` 또는 `\91`) 식은 리터럴로 해석됩니다.

* `\10` 이상인 식은 이 숫자에 해당하는 역참조가 있을 경우 역참조로 간주되고, 없을 경우 8진수 코드로 해석됩니다.

* 정규식에 정의되지 않은 그룹 번호에 대한 역참조가 포함되어 있으면 구문 분석 오류가 발생하고 정규식 엔진이 [ArgumentException](xref:System.ArgumentException)을 throw합니다.

모호성 문제가 발생하는 경우 명확하고 8진수 문자 코드와 혼동되지 않는 **\k<**_name_**>** 표기법을 사용할 수 있습니다. 마찬가지로, `\xdd` 등의 16진수 코드는 명확하며 역참조와 혼동되지 않습니다.

다음 예제에서는 문자열에서 2배 워드 문자를 찾습니다. 다음과 같은 요소로 구성된 `(\w)\1,` 정규식을 정의합니다.

요소 | 설명
------- | -----------
`(\w)` | 단어 문자를 찾아 첫 번째 캡처링 그룹에 할당합니다.
`\1` | 첫 번째 캡처링 그룹의 값과 일치하는 다음 문자를 찾습니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"(\w)\1";
      string input = "trellis llama webbing dresser swagger";
      foreach (Match match in Regex.Matches(input, pattern))
         Console.WriteLine("Found '{0}' at position {1}.", 
                           match.Value, match.Index);
   }
}
// The example displays the following output:
//       Found 'll' at position 3.
//       Found 'll' at position 8.
//       Found 'bb' at position 16.
//       Found 'ss' at position 25.
//       Found 'gg' at position 33.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "(\w)\1"
      Dim input As String = "trellis llama webbing dresser swagger"
      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine("Found '{0}' at position {1}.", _
                           match.Value, match.Index)
      Next   
   End Sub
End Module
' The example displays the following output:
'       Found 'll' at position 3.
'       Found 'll' at position 8.
'       Found 'bb' at position 16.
'       Found 'ss' at position 25.
'       Found 'gg' at position 33.
```

## <a name="named-backreferences"></a>명명된 역참조

명명된 역참조는 다음 구문을 사용하여 정의됩니다.

**\k<**_name_**>**

또는

**\k'**_name_**'**

여기서 *name*은 정규식 패턴에 정의된 캡처링 그룹의 이름입니다. *name*이 정규식 패턴에서 정의되지 않은 경우 구문 분석 오류가 발생하고 정규식 엔진이 [ArgumentException](xref:System.ArgumentException)을 throw합니다.

다음 예제에서는 문자열에서 2배 워드 문자를 찾습니다. 다음과 같은 요소로 구성된 `(?<char>\w)\k<char>` 정규식을 정의합니다.

요소 | 설명
------- | -----------
`(?<char>\w)` | 단어 문자를 찾아 이름이 char인 캡처링 그룹에 할당합니다.
`\k<char>` | char 캡처링 그룹의 값과 일치하는 다음 문자를 찾습니다.
 
```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"(?<char>\w)\k<char>";
      string input = "trellis llama webbing dresser swagger";
      foreach (Match match in Regex.Matches(input, pattern))
         Console.WriteLine("Found '{0}' at position {1}.", 
                           match.Value, match.Index);
   }
}
// The example displays the following output:
//       Found 'll' at position 3.
//       Found 'll' at position 8.
//       Found 'bb' at position 16.
//       Found 'ss' at position 25.
//       Found 'gg' at position 33.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "(?<char>\w)\k<char>"
      Dim input As String = "trellis llama webbing dresser swagger"
      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine("Found '{0}' at position {1}.", _
                           match.Value, match.Index)
      Next   
   End Sub
End Module
' The example displays the following output:
'       Found 'll' at position 3.
'       Found 'll' at position 8.
'       Found 'bb' at position 16.
'       Found 'ss' at position 25.
'       Found 'gg' at position 33.
```

*name*은 숫자의 문자열 표현일 수도 있습니다. 예를 들어 다음 예제에서는 `(?<2>\w)\k<2>` 정규식을 사용하여 문자열에서 2배 워드 문자를 찾습니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"(?<2>\w)\k<2>";
      string input = "trellis llama webbing dresser swagger";
      foreach (Match match in Regex.Matches(input, pattern))
         Console.WriteLine("Found '{0}' at position {1}.", 
                           match.Value, match.Index);
   }
}
// The example displays the following output:
//       Found 'll' at position 3.
//       Found 'll' at position 8.
//       Found 'bb' at position 16.
//       Found 'ss' at position 25.
//       Found 'gg' at position 33.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "(?<2>\w)\k<2>"
      Dim input As String = "trellis llama webbing dresser swagger"
      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine("Found '{0}' at position {1}.", _
                           match.Value, match.Index)
      Next   
   End Sub
End Module
' The example displays the following output:
'       Found 'll' at position 3.
'       Found 'll' at position 8.
'       Found 'bb' at position 16.
'       Found 'ss' at position 25.
'       Found 'gg' at position 33.
```

## <a name="what-backreferences-match"></a>역참조에서 찾는 대상

역참조는 그룹의 가장 최근 정의(왼쪽에서 오른쪽으로 찾을 경우 가장 왼쪽에 있는 정의)를 가리킵니다. 그룹에서 여러 개의 캡처를 만드는 경우 역참조는 가장 최근 캡처를 가리킵니다. 

다음 예제에는 이름이 \1인 그룹을 다시 정의하는 정규식 패턴 `(?<1>a)(?<1>\1b)*`가 포함되어 있습니다. 다음 표에서는 정규식의 각 패턴에 대해 설명합니다. 

패턴 | 설명
------- | -----------
`(?<1>a)` | 문자 "a"를 찾은 다음 이름이 1인 캡처링 그룹에 결과를 할당합니다.
`(?<1>\1b)*` |이름이 1과 "b"로 구성된 그룹을 0번 또는 1번 찾은 다음 이름이 1인 캡처링 그룹에 결과를 할당합니다. 
 
```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"(?<1>a)(?<1>\1b)*";
      string input = "aababb";
      foreach (Match match in Regex.Matches(input, pattern))
      {
         Console.WriteLine("Match: " + match.Value);
         foreach (Group group in match.Groups)
            Console.WriteLine("   Group: " + group.Value);
      }
   }
}
// The example displays the following output:
//          Group: aababb
//          Group: abb
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "(?<1>a)(?<1>\1b)*"
      Dim input As String = "aababb"
      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine("Match: " + match.Value)
         For Each group As Group In match.Groups
            Console.WriteLIne("   Group: " + group.Value)
         Next
      Next
   End Sub
End Module
' The example display the following output:
'          Group: aababb
'          Group: abb
```

입력 문자열("aababb")과 정규식을 비교할 때 정규식 엔진은 다음 작업을 수행합니다.

1. 문자열의 시작 부분에서 시작하여 `(?<1>a)` 식으로 "a"를 찾습니다. 1 그룹의 값이 이제 "a"입니다.

2. 두 번째 문자로 진행하여 `\1b` 또는 "ab" 식으로 "ab" 문자열을 찾습니다. 그런 다음 결과 "ab"를 `\1`에 할당합니다.

3. 네 번째 문자로 진행합니다. `(?<1>\1b)` 식은 0번 이상 일치할 수 있으므로 `\1b` 식으로 "abb" 문자열을 찾습니다. 결과 "abb"를 `\1`에 다시 할당합니다.

이 예제에서 \*는 반복 수량자로, 정규식 엔진이 정의되는 패턴을 찾을 수 없을 때까지 반복해서 평가됩니다. 반복 수량자는 그룹 정의를 지우지 않습니다.

그룹에서 부분 문자열을 캡처하지 않은 경우 해당 그룹에 대한 역참조가 정의되지 않으며 일치되지 않습니다. 이 내용은 다음과 같이 정의된 정규식 패턴 `\b(\p{Lu}{2})(\d{2})?(\p{Lu}{2})\b,`를 통해 확인할 수 있습니다.

패턴 | 설명
------- | -----------
`\b` | 단어 경계에서 일치 항목 찾기를 시작합니다.
`(\p{Lu}{2})` | 두 개의 대문자를 찾습니다. 이 그룹은 첫 번째 캡처링 그룹입니다.
`(\d{2})?` | 두 개의 10진수를 0번 또는 한 번 찾습니다. 이 그룹은 두 번째 캡처링 그룹입니다.
`(\p{Lu}{2})` | 두 개의 대문자를 찾습니다. 이 그룹은 세 번째 캡처링 그룹입니다.
`\b` | 단어 경계에서 일치 항목 찾기를 끝냅니다.

두 번째 캡처링 그룹에서 정의된 두 개의 10진수가 없는 경우에도 입력 문자열이 이 정규식과 일치할 수 있습니다. 다음 예제에서는 일치가 성공해도 성공적인 두 캡처링 그룹 사이에 빈 캡처링 그룹이 있음을 보여 줍니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"\b(\p{Lu}{2})(\d{2})?(\p{Lu}{2})\b";
      string[] inputs = { "AA22ZZ", "AABB" };
      foreach (string input in inputs)
      {
         Match match = Regex.Match(input, pattern);
         if (match.Success)
         {
            Console.WriteLine("Match in {0}: {1}", input, match.Value);
            if (match.Groups.Count > 1)
            {
               for (int ctr = 1; ctr <= match.Groups.Count - 1; ctr++)
               {
                  if (match.Groups[ctr].Success)
                     Console.WriteLine("Group {0}: {1}", 
                                       ctr, match.Groups[ctr].Value);
                  else
                     Console.WriteLine("Group {0}: <no match>", ctr);
               }
            }
         }
         Console.WriteLine();
      }      
   }
}
// The example displays the following output:
//       Match in AA22ZZ: AA22ZZ
//       Group 1: AA
//       Group 2: 22
//       Group 3: ZZ
//       
//       Match in AABB: AABB
//       Group 1: AA
//       Group 2: <no match>
//       Group 3: BB
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "\b(\p{Lu}{2})(\d{2})?(\p{Lu}{2})\b"
      Dim inputs() As String = { "AA22ZZ", "AABB" }
      For Each input As String In inputs
         Dim match As Match = Regex.Match(input, pattern)
         If match.Success Then
            Console.WriteLine("Match in {0}: {1}", input, match.Value)
            If match.Groups.Count > 1 Then
               For ctr As Integer = 1 To match.Groups.Count - 1
                  If match.Groups(ctr).Success Then
                     Console.WriteLine("Group {0}: {1}", _
                                       ctr, match.Groups(ctr).Value)
                  Else
                     Console.WriteLine("Group {0}: <no match>", ctr)
                  End If      
               Next
            End If
         End If
         Console.WriteLine()
      Next      
   End Sub
End Module
' The example displays the following output:
'       Match in AA22ZZ: AA22ZZ
'       Group 1: AA
'       Group 2: 22
'       Group 3: ZZ
'       
'       Match in AABB: AABB
'       Group 1: AA
'       Group 2: <no match>
'       Group 3: BB
```

## <a name="see-also"></a>참고 항목

[정규식 언어 - 빠른 참조](quick-ref.md)




<!--HONumber=Nov16_HO3-->


