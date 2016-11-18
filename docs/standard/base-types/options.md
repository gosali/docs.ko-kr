---
title: "정규식 옵션"
description: "정규식 옵션"
keywords: .NET, .NET Core
author: stevehoag
ms.author: shoag
manager: wpickett
ms.date: 07/29/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 2db2c3e6-953e-4913-8168-d707c437f2df
translationtype: Human Translation
ms.sourcegitcommit: b20713600d7c3ddc31be5885733a1e8910ede8c6
ms.openlocfilehash: a2a9fe356a0b2e9cf9415714bc01b77ea86229fc

---

# <a name="regular-expression-options"></a>정규식 옵션

기본적으로 입력 문자열을 정규식 패턴의 리터럴 문자와 비교할 때는 대/소문자를 구분하고, 정규식 패턴의 공백은 리터럴 공백 문자로 해석되며, 정규식의 캡처링 그룹은 명시적 및 암시적으로 명명됩니다. 정규식 옵션을 지정하여 기본 정규식 동작의 이러한 측면과 몇 가지 다른 측면을 수정할 수 있습니다. 다음 테이블에 나열되어 있는 이러한 옵션은 정규식 패턴의 일부로 인라인으로 포함되거나, [System.Text.RegularExpressions.Regex](xref:System.Text.RegularExpressions.Regex) 클래스 생성자 또는 고정 패턴 일치 메서드에 [System.Text.RegularExpressions.RegexOptions](xref:System.Text.RegularExpressions.RegexOptions) 열거형 값으로 제공될 수 있습니다. 

RegexOptions 멤버 | 인라인 문자 | 효과
------------------- | ---------------- | ------ 
[없음](xref:System.Text.RegularExpressions.RegexOptions.None) | 사용할 수 없음 | 기본 동작을 사용합니다. 자세한 내용은 [기본 옵션](#default-options)을 참조하세요.
[IgnoreCase](xref:System.Text.RegularExpressions.RegexOptions.IgnoreCase) | **i** | 대/소문자를 구분하지 않는 일치를 사용합니다. 자세한 내용은 [대/소문자를 구분하지 않는 일치](#case-insensitive-matching)를 참조하세요.
[Multiline](xref:System.Text.RegularExpressions.RegexOptions.Multiline) | **m** | 여러 줄 모드를 사용합니다. 여기서 **^** 및 **$**는 각 줄의 시작 부분 및 끝 부분과 일치합니다(입력 문자열의 시작 부분 및 끝 부분 대신). 자세한 내용은 [여러 줄 모드](#multiline-mode)를 참조하세요.
[Singleline](xref:System.Text.RegularExpressions.RegexOptions.Singleline) | **s** | 한 줄 모드를 사용합니다. 여기서 마침표(**.**)는 모든 문자와 일치합니다(**\n**을 제외한 모든 문자 대신). 자세한 내용은 [한 줄 모드](#single-line-mode)를 참조하세요.
[ExplicitCapture](xref:System.Text.RegularExpressions.RegexOptions.ExplicitCapture) | **n** | 명명되지 않은 그룹을 캡처하지 않습니다. 유효한 캡처만이 **(?<**_name_**>** _subexpression_**)** 형식의 명시적으로 명명되거나 번호가 매겨진 그룹입니다. 자세한 내용은 [명시적 캡처만 해당](#explicit-captures-only)을 참조하세요.
[Compiled](xref:System.Text.RegularExpressions.RegexOptions.Compiled) | 사용할 수 없음 | 정규식을 어셈블리로 컴파일합니다. 자세한 내용은 [컴파일된 정규식](#compiled-regular-expressions)을 참조하세요.
[IgnorePatternWhitespace](xref:System.Text.RegularExpressions.RegexOptions.IgnorePatternWhitespace) | **x** | 이스케이프되지 않은 공백을 패턴에서 제외하고 숫자 기호(**#**) 뒤에 주석을 사용하도록 설정합니다. 자세한 내용은 [공백 무시](#ignore-white-space)를 참조하세요.
[RightToLeft](xref:System.Text.RegularExpressions.RegexOptions.RightToLeft) | 사용할 수 없음 | 검색 방향을 변경합니다. 검색이 왼쪽에서 오른쪽으로 대신 오른쪽에서 왼쪽으로 이동합니다. 자세한 내용은 [오른쪽에서 왼쪽 모드](#right-to-left-mode)를 참조하세요.
[ECMAScript](xref:System.Text.RegularExpressions.RegexOptions.ECMAScript) | 사용할 수 없음 | 식에 대해 ECMAScript와 호환되는 동작을 사용하도록 설정합니다. 자세한 내용은 [ECMAScript 일치 동작](#ecmascript-matching-behavior)을 참조하세요.
[CultureInvariant](xref:System.Text.RegularExpressions.RegexOptions.CultureInvariant) | 사용할 수 없음 | 언어의 문화권 차이를 무시합니다. 자세한 내용은 [고정 문화권을 사용한 비교](#comparison-using-the-invariant-culture)를 참조하세요.
 
## <a name="specifying-the-options"></a>옵션 지정

정규식에 대한 옵션은 다음과 같은 세 가지 방법 중 하나로 지정할 수 있습니다.

* [Regex.Regex (String, RegexOptions)](xref:System.Text.RegularExpressions.Regex.%23ctor(System.String,System.Text.RegularExpressions.RegexOptions))와 같은 [System.Text.RegularExpressions.Regex](xref:System.Text.RegularExpressions.Regex) 클래스 생성자의 *options* 매개 변수에서 또는 [Regex.Match(String, String, RegexOptions)](xref:System.Text.RegularExpressions.Regex.Match(System.String,System.String,System.Text.RegularExpressions.RegexOptions))과 같은 고정(Visual Basic의 경우 공유) 패턴 일치 메서드에서 가능합니다. *옵션* 매개 변수는 [System.Text.RegularExpressions.RegexOptions](xref:System.Text.RegularExpressions.RegexOptions) 열거된 값의 비트 OR 조합입니다. 

  클래스 생성자의 *options* 매개 변수를 사용하여 [Regex](xref:System.Text.RegularExpressions.Regex) 인스턴스에 옵션을 제공하면 해당 옵션이 [System.Text.RegularExpressions.RegexOptions](xref:System.Text.RegularExpressions.RegexOptions) 속성에 할당됩니다. 그러나 [System.Text.RegularExpressions.RegexOptions](xref:System.Text.RegularExpressions.RegexOptions) 속성은 정규식 패턴 자체에 인라인 옵션을 반영하지는 않습니다. 

  다음 예제에서 이에 대해 설명합니다. 이 예제에서는 [Regex.Match(String, String, RegexOptions)](xref:System.Text.RegularExpressions.Regex.Match(System.String,System.String,System.Text.RegularExpressions.RegexOptions)) 메서드의 *options* 매개 변수를 사용하여 대/소문자를 구분하지 않는 일치를 사용하도록 설정하고 문자 "d"로 시작하는 단어를 식별할 때 패턴 공백을 무시합니다.

  ```csharp
  string pattern = @"d \w+ \s";
  string input = "Dogs are decidedly good pets.";
  RegexOptions options = RegexOptions.IgnoreCase | RegexOptions.IgnorePatternWhitespace;
  
  foreach (Match match in Regex.Matches(input, pattern, options))
     Console.WriteLine("'{0}// found at index {1}.", match.Value, match.Index);
  // The example displays the following output:
  //    'Dogs // found at index 0.
  //    'decidedly // found at index 9.      
  ```

  ```vb
  Dim pattern As String = "d \w+ \s"
  Dim input As String = "Dogs are decidedly good pets."
  Dim options As RegexOptions = RegexOptions.IgnoreCase Or RegexOptions.IgnorePatternWhitespace

  For Each match As Match In Regex.Matches(input, pattern, options)
     Console.WriteLine("'{0}' found at index {1}.", match.Value, match.Index)
  Next
  ' The example displays the following output:
  '    'Dogs ' found at index 0.
  '    'decidedly ' found at index 9.  
  ```

* **(?imnsx-imnsx)** 구문을 사용하여 정규식 패턴에 인라인 옵션을 적용합니다. 이 옵션은 옵션이 정의된 지점에서 패턴의 끝 부분까지 또는 다른 인라인 옵션에 의해 옵션이 정의되지 않은 지점까지 패턴에 적용됩니다. [Regex](xref:System.Text.RegularExpressions.Regex) 인스턴스의 [System.Text.RegularExpressions.RegexOptions](xref:System.Text.RegularExpressions.RegexOptions) 속성은 이러한 인라인 옵션을 반영하지 않습니다. 자세한 내용은 [정규식의 기타 구문](miscellaneous.md) 항목을 참조하세요.

  다음 예제에서 이에 대해 설명합니다. 이 예제에서는 인라인 옵션을 사용하여 대/소문자를 구분하지 않는 일치를 사용하도록 설정하고 문자 "d"로 시작하는 단어를 식별할 때 패턴 공백을 무시합니다.

  ```csharp
  string pattern = @"(?ix) d \w+ \s";
  string input = "Dogs are decidedly good pets.";
  
  foreach (Match match in Regex.Matches(input, pattern))
     Console.WriteLine("'{0}// found at index {1}.", match.Value, match.Index);
  // The example displays the following output:
  //    'Dogs // found at index 0.
  //    'decidedly // found at index 9.      
  ```

  ```vb
  Dim pattern As String = "\b(?ix) d \w+ \s"
  Dim input As String = "Dogs are decidedly good pets."

  For Each match As Match In Regex.Matches(input, pattern)
     Console.WriteLine("'{0}' found at index {1}.", match.Value, match.Index)
  Next
  ' The example displays the following output:
  '    'Dogs ' found at index 0.
  '    'decidedly ' found at index 9.  
  ```

* **(?imnsx-imnsx:**_subexpression_**)** 구문을 사용하여 정규식 패턴의 특정 그룹화 구문에 인라인 옵션을 적용합니다. 옵션 집합 앞에 기호가 없으면 집합이 설정되고, 옵션 집합 앞에 빼기 기호가 있으면 집합이 해제됩니다. **?** 는 옵션의 사용 여부에 따라 필요한 언어 구문의 고정 부분입니다. 이 옵션은 해당 그룹에만 적용됩니다. 자세한 내용은 [정규식의 그룹화 구문](grouping.md)을 참조하세요.

  다음 예제에서 이에 대해 설명합니다. 이 예제에서는 그룹화 구문에 인라인 옵션을 사용하여 대/소문자를 구분하지 않는 일치를 사용하도록 설정하고 문자 "d"로 시작하는 단어를 식별할 때 패턴 공백을 무시합니다.

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


옵션이 인라인으로 지정된 경우 옵션 또는 옵션 집합 앞에 빼기 기호(-)가 있으면 해당 옵션이 해제됩니다. 예를 들어, 인라인 구문 **(?ix-ms)**이 [RegexOptions.IgnoreCase](xref:System.Text.RegularExpressions.RegexOptions.IgnoreCase) 및 [RegexOptions.IgnorePatternWhitespace](xref:System.Text.RegularExpressions.RegexOptions.IgnorePatternWhitespace) 옵션을 켜고 [RegexOptions.Multiline](xref:System.Text.RegularExpressions.RegexOptions.Multiline) 및 [는 RegexOptions.Singleline](xref:System.Text.RegularExpressions.RegexOptions.Singleline) 옵션을 끕니다. 모든 정규식 옵션은 기본적으로 해제되어 있습니다.

> [!NOTE]
> 생성자 또는 메서드 호출의 options 매개 변수에 지정된 정규식 옵션이 정규식 패턴에 인라인으로 지정된 옵션과 충돌하는 경우 인라인 옵션이 사용됩니다.
 

다음 5개의 정규식 옵션은 *options* 매개 변수와 인라인으로 모두 설정할 수 있습니다.

* [RegexOptions.IgnoreCase](xref:System.Text.RegularExpressions.RegexOptions.IgnoreCase)

* [RegexOptions.Multiline](xref:System.Text.RegularExpressions.RegexOptions.Multiline)

* [RegexOptions.Singleline](xref:System.Text.RegularExpressions.RegexOptions.Singleline)

* [RegexOptions.ExplicitCapture](xref:System.Text.RegularExpressions.RegexOptions.ExplicitCapture)

* [RegexOptions.IgnorePatternWhitespace](xref:System.Text.RegularExpressions.RegexOptions.IgnorePatternWhitespace)

다음 5개의 정규식 옵션은 *options* 매개 변수를 사용하여 설정할 수 있지만 인라인으로는 설정할 수 없습니다.

* [RegexOptions.None](xref:System.Text.RegularExpressions.RegexOptions.None)

* [RegexOptions.Compiled](xref:System.Text.RegularExpressions.RegexOptions.Compiled)

* [RegexOptions.RightToLeft](xref:System.Text.RegularExpressions.RegexOptions.RightToLeft)

* [RegexOptions.CultureInvariant](xref:System.Text.RegularExpressions.RegexOptions.CultureInvariant)

* [RegexOptions.ECMAScript](xref:System.Text.RegularExpressions.RegexOptions.ECMAScript)

## <a name="determining-the-options"></a>옵션 확인

읽기 전용 [Regex.Options](xref:System.Text.RegularExpressions.Regex.Options) 속성 값을 검색하여 [Regex](xref:System.Text.RegularExpressions.Regex) 개체를 인스턴스화했을 때 해당 개체에 제공된 옵션을 확인할 수 있습니다.

[RegexOptions.None](xref:System.Text.RegularExpressions.RegexOptions.None)을 제외한 모든 옵션의 존재 유무를 테스트하려면 [Regex.Options](xref:System.Text.RegularExpressions.Regex.Options) 속성 값 및 관심 있는 [RegexOptions](xref:System.Text.RegularExpressions.RegexOptions) 값으로 AND 작업을 수행합니다. 그런 다음 결과가 해당 [RegexOptions](xref:System.Text.RegularExpressions.RegexOptions) 값과 같은지 테스트합니다. 다음 예제에서는 [RegexOptions.IgnoreCase](xref:System.Text.RegularExpressions.RegexOptions.IgnoreCase) 옵션이 설정되었는지 테스트합니다. 

```csharp
if ((rgx.Options & RegexOptions.IgnoreCase) == RegexOptions.IgnoreCase)
   Console.WriteLine("Case-insensitive pattern comparison.");
else
   Console.WriteLine("Case-sensitive pattern comparison.");
```

```vb
If (rgx.Options And RegexOptions.IgnoreCase) = RegexOptions.IgnoreCase Then
   Console.WriteLine("Case-insensitive pattern comparison.")
Else
   Console.WriteLine("Case-sensitive pattern comparison.")
End If 
```

[RegexOptions.None](xref:System.Text.RegularExpressions.RegexOptions.None)을 테스트하려면 다음 예제와 같이 [RegexOptions](xref:System.Text.RegularExpressions.RegexOptions) 속성 값이 [RegexOptions.None](xref:System.Text.RegularExpressions.RegexOptions.None)과 같은지를 결정합니다. 

```csharp
if (rgx.Options == RegexOptions.None)
   Console.WriteLine("No options have been set.");
```

```vb
If rgx.Options = RegexOptions.None Then
   Console.WriteLine("No options have been set.")
End If
```

다음 섹션에는 .NET의 정규식에서 지원하는 옵션이 나열되어 있습니다. 

## <a name="default-options"></a>기본 옵션

[RegexOptions.None](xref:System.Text.RegularExpressions.RegexOptions.None) 옵션은 지정된 옵션이 없고 정규식 엔진에서 해당 기본 동작을 사용함을 나타냅니다. 여기에는 다음과 같은 사항이 포함됩니다.

* 패턴이 ECMAScript 정규식이 아니라 정식으로 해석됩니다.

* 정규식 패턴이 입력 문자열에서 왼쪽에서 오른쪽으로 일치됩니다.

* 비교는 대/소문자를 구분합니다.

* **^** 및 **$** 언어 요소는 입력 문자열의 시작 부분 및 끝 부분과 일치합니다.

* **.** 언어 요소는 **\n**을 제외한 모든 문자와 일치합니다.

* 정규식 패턴의 모든 공백은 리터럴 공백 문자로 해석됩니다.

* 패턴을 입력 문자열과 비교할 때 현재 문화권의 규칙이 사용됩니다. 

* 정규식 패턴의 캡처링 그룹은 명시적 및 암시적입니다. 

> [!NOTE]
> [RegexOptions.None](xref:System.Text.RegularExpressions.RegexOptions.None) 옵션과 동일한 인라인 옵션은 없습니다. 정규식 옵션이 인라인으로 적용된 경우 기본 동작은 특정 옵션을 해제하여 옵션별로 복원됩니다. 예를 들어, `(?i)`는 대/소문자를 구분하지 않는 비교를 설정하고 `(?-i)`는 기본 대/소문자를 구분하는 비교를 복원합니다.
 
[RegexOptions.None](xref:System.Text.RegularExpressions.RegexOptions.None) 옵션은 정규식 엔진의 기본 동작을 나타내므로 메서드 호출에 명시적으로 지정되는 경우가 드뭅니다. 대신 생성자 또는 정적 패턴 일치 메서드가 options 매개 변수 없이 호출됩니다.

## <a name="caseinsensitive-matching"></a>대/소문자를 구분하지 않는 일치

[RegexOptions.IgnoreCase](xref:System.Text.RegularExpressions.RegexOptions.IgnoreCase) 옵션 또는 **i** 인라인 옵션은 대/소문자를 구분하지 않는 일치를 제공합니다. 기본적으로 현재 문화권의 대/소문자 사용 규칙이 사용됩니다.

다음 예제에서는 "the"로 시작하는 모든 단어와 일치하는 정규식 패턴 `\bthe\w*\b`를 정의합니다. Match 메서드에 대한 첫 번째 호출에서 기본 대/소문자를 구분하는 비교를 사용하므로 출력은 문장을 시작하는 문자열 "The"가 일치하지 않음을 나타냅니다. [Match](xref:System.Text.RegularExpressions.Regex.Match(System.String,System.String,System.Text.RegularExpressions.RegexOptions)) 메서드가 옵션이 [IgnoreCase](xref:System.Text.RegularExpressions.RegexOptions.IgnoreCase)로 설정된 상태로 호출되면 이 문자열이 일치합니다. 

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"\bthe\w*\b";
      string input = "The man then told them about that event.";
      foreach (Match match in Regex.Matches(input, pattern))
         Console.WriteLine("Found {0} at index {1}.", match.Value, match.Index);

      Console.WriteLine();
      foreach (Match match in Regex.Matches(input, pattern, 
                                            RegexOptions.IgnoreCase))
         Console.WriteLine("Found {0} at index {1}.", match.Value, match.Index);
   }
}
// The example displays the following output:
//       Found then at index 8.
//       Found them at index 18.
//       
//       Found The at index 0.
//       Found then at index 8.
//       Found them at index 18.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "\bthe\w*\b"
      Dim input As String = "The man then told them about that event."
      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine("Found {0} at index {1}.", match.Value, match.Index)
      Next
      Console.WriteLine()
      For Each match As Match In Regex.Matches(input, pattern, _
                                               RegexOptions.IgnoreCase)
         Console.WriteLine("Found {0} at index {1}.", match.Value, match.Index)
      Next
   End Sub
End Module
' The example displays the following output:
'       Found then at index 8.
'       Found them at index 18.
'       
'       Found The at index 0.
'       Found then at index 8.
'       Found them at index 18.
```

다음 예제에서는 대/소문자를 구분하지 않는 비교를 제공하기 위해 *options* 매개 변수 대신 인라인 옵션을 사용하도록 이전 예제의 정규식 패턴을 수정합니다. 첫 번째 패턴은 문자열 "the"에서 문자 "t"에만 적용되는 그룹화 구문에 대/소문자를 구분하지 않는 옵션을 정의합니다. 옵션 구문이 패턴의 시작 부분에서 발생하므로 두 번째 패턴은 대/소문자를 구분하지 않는 옵션을 전체 정규식에 적용합니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"\b(?i:t)he\w*\b";
      string input = "The man then told them about that event.";
      foreach (Match match in Regex.Matches(input, pattern))
         Console.WriteLine("Found {0} at index {1}.", match.Value, match.Index);

      Console.WriteLine();
      pattern = @"(?i)\bthe\w*\b";
      foreach (Match match in Regex.Matches(input, pattern, 
                                            RegexOptions.IgnoreCase))
         Console.WriteLine("Found {0} at index {1}.", match.Value, match.Index);
   }
}
// The example displays the following output:
//       Found The at index 0.
//       Found then at index 8.
//       Found them at index 18.
//       
//       Found The at index 0.
//       Found then at index 8.
//       Found them at index 18.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "\b(?i:t)he\w*\b"
      Dim input As String = "The man then told them about that event."
      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine("Found {0} at index {1}.", match.Value, match.Index)
      Next
      Console.WriteLine()
      pattern = "(?i)\bthe\w*\b"
      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine("Found {0} at index {1}.", match.Value, match.Index)
      Next
   End Sub
End Module
' The example displays the following output:
'       Found The at index 0.
'       Found then at index 8.
'       Found them at index 18.
'       
'       Found The at index 0.
'       Found then at index 8.
'       Found them at index 18.
```

## <a name="multiline-mode"></a>여러 줄 모드

[RegexOptions.Multiline](xref:System.Text.RegularExpressions.RegexOptions.Multiline) 옵션 또는 **m** 인라인 옵션은 정규식 엔진이 여러 줄로 구성된 입력 문자열을 처리할 수 있게 해줍니다. 이 옵션은 **^** 및 **$** 언어 요소가 입력 문자열의 시작 부분 및 끝 부분 대신 줄의 시작 부분 및 끝 부분과 일치하도록 해당 언어 요소의 해석을 변경합니다. 

기본적으로 **$**는 입력 문자열의 끝 부분과만 일치합니다. [RegexOptions.Multiline](xref:System.Text.RegularExpressions.RegexOptions.Multiline) 옵션을 지정하면 줄 바꿈 문자**(\n)** 또는 입력 문자열의 끝 부분과 일치합니다. 그러나 캐리지 리턴/줄 바꿈 문자 조합과는 일치하지 않습니다. 성공적으로 일치시키려면 단순히 **$**만 사용할 것이 아니라 하위 식 **\r?$**를 사용합니다. 

다음 예제에서는 볼링하는 사람의 이름과 점수를 추출하여 이를 내림차순으로 정렬하는 [SortedList&lt;TKey, TValue&gt;](xref:System.Collections.Generic.SortedList%602) 컬렉션에 추가합니다. [Matches](xref:System.Text.RegularExpressions.Regex.Matches(System.String)) 메서드가 두 번 호출됩니다. 첫 번째 메서드 호출에서 정규식은 `^(\w+)\s(\d+)$`이며 옵션이 설정되지 않았습니다. 출력에 표시된 것처럼, 정규식 엔진이 입력 패턴을 입력 문자열의 시작 부분 및 끝 부분과 함께 일치시킬 수 없어 찾은 일치 항목이 없습니다. 두 번째 메서드 호출에서 정규식은 `^(\w+)\s(\d+)\r?$`로 변경되었으며 옵션이 [RegexOptions.Multiline](xref:System.Text.RegularExpressions.RegexOptions.Multiline)으로 설정되었습니다. 출력에 표시된 것처럼, 이름 및 점수가 성공적으로 일치되었으며 점수가 내림차순으로 표시되었습니다.

```csharp
using System;
using System.Collections.Generic;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      SortedList<int, string> scores = new SortedList<int, string>(new DescendingComparer<int>());

      string input = "Joe 164\n" + 
                     "Sam 208\n" + 
                     "Allison 211\n" + 
                     "Gwen 171\n"; 
      string pattern = @"^(\w+)\s(\d+)$";
      bool matched = false;

      Console.WriteLine("Without Multiline option:");
      foreach (Match match in Regex.Matches(input, pattern))
      {
         scores.Add(Int32.Parse(match.Groups[2].Value), (string) match.Groups[1].Value);
         matched = true;
      }
      if (! matched)
         Console.WriteLine("   No matches.");
      Console.WriteLine();

      // Redefine pattern to handle multiple lines.
      pattern = @"^(\w+)\s(\d+)\r*$";
      Console.WriteLine("With multiline option:");
      foreach (Match match in Regex.Matches(input, pattern, RegexOptions.Multiline))
         scores.Add(Int32.Parse(match.Groups[2].Value), (string) match.Groups[1].Value);

      // List scores in descending order. 
      foreach (KeyValuePair<int, string> score in scores)
         Console.WriteLine("{0}: {1}", score.Value, score.Key);
   }
}

public class DescendingComparer<T> : IComparer<T>
{
   public int Compare(T x, T y)
   {
      return Comparer<T>.Default.Compare(x, y) * -1;       
   }
}
// The example displays the following output:
//   Without Multiline option:
//      No matches.
//   
//   With multiline option:
//   Allison: 211
//   Sam: 208
//   Gwen: 171
//   Joe: 164
```

```vb
Imports System.Collections.Generic
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim scores As New SortedList(Of Integer, String)(New DescendingComparer(Of Integer)())

      Dim input As String = "Joe 164" + vbCrLf + _
                            "Sam 208" + vbCrLf + _
                            "Allison 211" + vbCrLf + _
                            "Gwen 171" + vbCrLf
      Dim pattern As String = "^(\w+)\s(\d+)$"
      Dim matched As Boolean = False

      Console.WriteLine("Without Multiline option:")
      For Each match As Match In Regex.Matches(input, pattern)
         scores.Add(CInt(match.Groups(2).Value), match.Groups(1).Value)
         matched = True
      Next
      If Not matched Then Console.WriteLine("   No matches.")
      Console.WriteLine()

      ' Redefine pattern to handle multiple lines.
      pattern = "^(\w+)\s(\d+)\r*$"
      Console.WriteLine("With multiline option:")
      For Each match As Match In Regex.Matches(input, pattern, RegexOptions.Multiline)
         scores.Add(CInt(match.Groups(2).Value), match.Groups(1).Value)
      Next
      ' List scores in descending order. 
      For Each score As KeyValuePair(Of Integer, String) In scores
         Console.WriteLine("{0}: {1}", score.Value, score.Key)
      Next
   End Sub
End Module

Public Class DescendingComparer(Of T) : Implements IComparer(Of T)
   Public Function Compare(x As T, y As T) As Integer _
          Implements IComparer(Of T).Compare
      Return Comparer(Of T).Default.Compare(x, y) * -1       
   End Function
End Class
' The example displays the following output:
'    Without Multiline option:
'       No matches.
'    
'    With multiline option:
'    Allison: 211
'    Sam: 208
'    Gwen: 171
'    Joe: 164
```

정규식 패턴 `^(\w+)\s(\d+)\r*$`는 다음 테이블과 같이 정의됩니다.

패턴 | 설명
------- | ----------- 
`^` | 줄의 시작 부분에서 시작합니다.
`(\w+)` | 하나 이상의 단어 문자를 찾습니다. 이 그룹은 첫 번째 캡처링 그룹입니다.
`\s` | 공백 문자를 찾습니다.
`(\d+)` | 하나 이상의 10진수 숫자가 일치하는지 확인합니다. 이 그룹은 두 번째 캡처링 그룹입니다.
`\r?` | 0개 또는 1개의 캐리지 리턴 문자를 찾습니다.
`$` | 줄의 끝 부분에서 끝납니다.
 
다음 예제는 인라인 옵션**(?m)**을 사용하여 여러 줄 옵션을 설정한다는 점을 제외하고는 이전 예제와 동일합니다.

```csharp
using System;
using System.Collections.Generic;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      SortedList<int, string> scores = new SortedList<int, string>(new DescendingComparer<int>());

      string input = "Joe 164\n" +  
                     "Sam 208\n" +  
                     "Allison 211\n" +  
                     "Gwen 171\n"; 
      string pattern = @"(?m)^(\w+)\s(\d+)\r*$";

      foreach (Match match in Regex.Matches(input, pattern, RegexOptions.Multiline))
         scores.Add(Convert.ToInt32(match.Groups[2].Value), match.Groups[1].Value);

      // List scores in descending order. 
      foreach (KeyValuePair<int, string> score in scores)
         Console.WriteLine("{0}: {1}", score.Value, score.Key);
   }
}

public class DescendingComparer<T> : IComparer<T>
{
   public int Compare(T x, T y) 
   {
      return Comparer<T>.Default.Compare(x, y) * -1;       
   }
}
// The example displays the following output:
//    Allison: 211
//    Sam: 208
//    Gwen: 171
//    Joe: 164
```

```vb
Imports System.Collections.Generic
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim scores As New SortedList(Of Integer, String)(New DescendingComparer(Of Integer)())

      Dim input As String = "Joe 164" + vbCrLf + _
                            "Sam 208" + vbCrLf + _
                            "Allison 211" + vbCrLf + _
                            "Gwen 171" + vbCrLf
      Dim pattern As String = "(?m)^(\w+)\s(\d+)\r*$"

      For Each match As Match In Regex.Matches(input, pattern, RegexOptions.Multiline)
         scores.Add(CInt(match.Groups(2).Value), match.Groups(1).Value)
      Next
      ' List scores in descending order. 
      For Each score As KeyValuePair(Of Integer, String) In scores
         Console.WriteLine("{0}: {1}", score.Value, score.Key)
      Next
   End Sub
End Module

Public Class DescendingComparer(Of T) : Implements IComparer(Of T)
   Public Function Compare(x As T, y As T) As Integer _
          Implements IComparer(Of T).Compare
      Return Comparer(Of T).Default.Compare(x, y) * -1       
   End Function
End Class
' The example displays the following output:
'    Allison: 211
'    Sam: 208
'    Gwen: 171
'    Joe: 164
```

## <a name="singleline-mode"></a>한 줄 모드

[RegexOptions.Singleline](xref:System.Text.RegularExpressions.RegexOptions.Singleline) 옵션 또는 s 인라인 옵션은 정규식 엔진이 입력 문자열이 한 줄로 구성된 것처럼 입력 문자열을 처리하도록 합니다. 이 옵션은 마침표(**.**) 언어 요소가 줄 바꿈 문자 **\n** 또는 \u000A를 제외한 모든 문자와 일치하는 대신 모든 문자와 일치하도록 해당 언어 요소의 동작을 변경하여 그렇게 합니다.

다음 예제에서는 [RegexOptions.Singleline](xref:System.Text.RegularExpressions.RegexOptions.Singleline) 옵션을 사용할 때 . 언어 요소의 동작이 변경되는 방법을 설명합니다. 정규식 `^.+`는 문자열의 시작 부분에서 시작하여 모든 문자를 찾습니다. 기본적으로 일치는 첫째 줄 끝에서 끝납니다. 정규식 패턴은 캐리지 리턴 문자, **\r** 또는 \u000D와 일치하지만 **\n**과는 일치하지 않습니다. [RegexOptions.Singleline](xref:System.Text.RegularExpressions.RegexOptions.Singleline) 옵션은 한 줄로 전체 입력 문자열을 해석하기 때문에 **\n**을 포함하여 입력 문자열의 모든 문자를 일치시킵니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = "^.+";
      string input = "This is one line and" + Environment.NewLine + "this is the second.";
      foreach (Match match in Regex.Matches(input, pattern))
         Console.WriteLine(Regex.Escape(match.Value));

      Console.WriteLine();
      foreach (Match match in Regex.Matches(input, pattern, RegexOptions.Singleline))
         Console.WriteLine(Regex.Escape(match.Value));
   }
}
// The example displays the following output:
//       This\ is\ one\ line\ and\r
//       
//       This\ is\ one\ line\ and\r\nthis\ is\ the\ second\.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "^.+"
      Dim input As String = "This is one line and" + vbCrLf + "this is the second."
      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine(Regex.Escape(match.Value))
      Next
      Console.WriteLine()
      For Each match As Match In Regex.Matches(input, pattern, RegexOptions.SingleLine)
         Console.WriteLine(Regex.Escape(match.Value))
      Next
   End Sub
End Module
' The example displays the following output:
'       This\ is\ one\ line\ and\r
'       
'       This\ is\ one\ line\ and\r\nthis\ is\ the\ second\.
```

다음 예제는 인라인 옵션 **(?s)**를 사용하여 한 줄 모드를 사용하도록 설정한다는 점을 제외하고는 이전 예제와 동일합니다. 

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {      
      string pattern = "(?s)^.+";
      string input = "This is one line and" + Environment.NewLine + "this is the second.";

      foreach (Match match in Regex.Matches(input, pattern))
         Console.WriteLine(Regex.Escape(match.Value));
   }
}
// The example displays the following output:
//       This\ is\ one\ line\ and\r\nthis\ is\ the\ second\.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "(?s)^.+"
      Dim input As String = "This is one line and" + vbCrLf + "this is the second."

      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine(Regex.Escape(match.Value))
      Next
   End Sub
End Module
' The example displays the following output:
'       This\ is\ one\ line\ and\r\nthis\ is\ the\ second\.
```

## <a name="explicit-captures-only"></a>명시적 캡처만 해당

기본적으로 캡처링 그룹은 정규식 패턴에 괄호를 사용하여 정의됩니다. 명명된 그룹은 **(?<**_name_**>** _subexpression_**)** 언어 옵션에 의해 이름 또는 번호가 할당되는 반면, 명명되지 않은 그룹은 인덱스로 액세스할 수 있습니다. [GroupCollection](xref:System.Text.RegularExpressions.GroupCollection) 개체에서 명명되지 않은 그룹은 명명된 그룹 앞에 있습니다. 

그룹화 구문은 일반적으로 여러 언어 요소에 수량자를 적용하는 데만 사용되고, 캡처된 부분 문자열은 관심을 두는 부분이 아닙니다. 예를 들어, 다음 정규식

```
\b\(?((\w+),?\s?)+[\.!?]\)?
```

가 문서에서 마침표, 느낌표 또는 물음표로 끝나는 문장만 추출하려는 의도로 사용된 경우 결과 문장([Match](xref:System.Text.RegularExpressions.Match) 개체로 표현됨)만 관심을 두는 부분입니다. 컬렉션의 개별 단어는 관심을 두는 부분이 아닙니다. 

정규식 엔진이 [GroupCollection](xref:System.Text.RegularExpressions.GroupCollection) 및 [CaptureCollection](xref:System.Text.RegularExpressions.CaptureCollection) 컬렉션 개체를 둘 다 채워야 하므로 이후에 사용되지 않는 캡처링 그룹에는 비용이 많이 들 수 있습니다. 대안으로 [RegexOptions.ExplicitCapture](xref:System.Text.RegularExpressions.RegexOptions.ExplicitCapture) 옵션 또는 **n** 인라인 옵션을 사용하여 유효한 캡처가 **(?<**_name_**>** _subexpression_**)** 구문에 의해 지정된, 명시적으로 명명되거나 번호가 매겨진 그룹임을 지정할 수 있습니다. 

다음 예제에서는 [Match](xref:System.Text.RegularExpressions.Regex.Match(System.String,System.Int32)) 메서드가 [RegexOptions.ExplicitCapture](xref:System.Text.RegularExpressions.RegexOptions.ExplicitCapture) 옵션을 사용하여 호출된 경우와 이 옵션을 사용하지 않고 호출된 경우에 `\b\(?((\w+),?\s?)+[\.!?]\)?` 정규식 패턴에서 반환하는 일치 항목에 대한 정보를 표시합니다. 첫 번째 메서드 호출의 출력이 보여 주는 것처럼, 정규식 엔진은 캡처된 부분 문자열에 대한 정보로 [GroupCollection](xref:System.Text.RegularExpressions.GroupCollection) 및 [CaptureCollection](xref:System.Text.RegularExpressions.CaptureCollection) 컬렉션 개체를 완전히 채웁니다. 두 번째 메서드는 options을 [RegexOptions.ExplicitCapture](xref:System.Text.RegularExpressions.RegexOptions.ExplicitCapture)로 설정한 상태로 호출되었기 때문에 그룹에 대한 정보를 캡처하지 않습니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string input = "This is the first sentence. Is it the beginning " + 
                     "of a literary masterpiece? I think not. Instead, " + 
                     "it is a nonsensical paragraph.";
      string pattern = @"\b\(?((?>\w+),?\s?)+[\.!?]\)?";
      Console.WriteLine("With implicit captures:");
      foreach (Match match in Regex.Matches(input, pattern))
      {
         Console.WriteLine("The match: {0}", match.Value);
         int groupCtr = 0;
         foreach (Group group in match.Groups)
         {
            Console.WriteLine("   Group {0}: {1}", groupCtr, group.Value);
            groupCtr++;
            int captureCtr = 0;
            foreach (Capture capture in group.Captures)
            {
               Console.WriteLine("      Capture {0}: {1}", captureCtr, capture.Value);
               captureCtr++;
            }
         }
      }
      Console.WriteLine();
      Console.WriteLine("With explicit captures only:");
      foreach (Match match in Regex.Matches(input, pattern, RegexOptions.ExplicitCapture))
      {
         Console.WriteLine("The match: {0}", match.Value);
         int groupCtr = 0;
         foreach (Group group in match.Groups)
         {
            Console.WriteLine("   Group {0}: {1}", groupCtr, group.Value);
            groupCtr++;
            int captureCtr = 0;
            foreach (Capture capture in group.Captures)
            {
               Console.WriteLine("      Capture {0}: {1}", captureCtr, capture.Value);
               captureCtr++;
            }
         }
      }
   }
}
// The example displays the following output:
//    With implicit captures:
//    The match: This is the first sentence.
//       Group 0: This is the first sentence.
//          Capture 0: This is the first sentence.
//       Group 1: sentence
//          Capture 0: This
//          Capture 1: is
//          Capture 2: the
//          Capture 3: first
//          Capture 4: sentence
//       Group 2: sentence
//          Capture 0: This
//          Capture 1: is
//          Capture 2: the
//          Capture 3: first
//          Capture 4: sentence
//    The match: Is it the beginning of a literary masterpiece?
//       Group 0: Is it the beginning of a literary masterpiece?
//          Capture 0: Is it the beginning of a literary masterpiece?
//       Group 1: masterpiece
//          Capture 0: Is
//          Capture 1: it
//          Capture 2: the
//          Capture 3: beginning
//          Capture 4: of
//          Capture 5: a
//          Capture 6: literary
//          Capture 7: masterpiece
//       Group 2: masterpiece
//          Capture 0: Is
//          Capture 1: it
//          Capture 2: the
//          Capture 3: beginning
//          Capture 4: of
//          Capture 5: a
//          Capture 6: literary
//          Capture 7: masterpiece
//    The match: I think not.
//       Group 0: I think not.
//          Capture 0: I think not.
//       Group 1: not
//          Capture 0: I
//          Capture 1: think
//          Capture 2: not
//       Group 2: not
//          Capture 0: I
//          Capture 1: think
//          Capture 2: not
//    The match: Instead, it is a nonsensical paragraph.
//       Group 0: Instead, it is a nonsensical paragraph.
//          Capture 0: Instead, it is a nonsensical paragraph.
//       Group 1: paragraph
//          Capture 0: Instead,
//          Capture 1: it
//          Capture 2: is
//          Capture 3: a
//          Capture 4: nonsensical
//          Capture 5: paragraph
//       Group 2: paragraph
//          Capture 0: Instead
//          Capture 1: it
//          Capture 2: is
//          Capture 3: a
//          Capture 4: nonsensical
//          Capture 5: paragraph
//    
//    With explicit captures only:
//    The match: This is the first sentence.
//       Group 0: This is the first sentence.
//          Capture 0: This is the first sentence.
//    The match: Is it the beginning of a literary masterpiece?
//       Group 0: Is it the beginning of a literary masterpiece?
//          Capture 0: Is it the beginning of a literary masterpiece?
//    The match: I think not.
//       Group 0: I think not.
//          Capture 0: I think not.
//    The match: Instead, it is a nonsensical paragraph.
//       Group 0: Instead, it is a nonsensical paragraph.
//          Capture 0: Instead, it is a nonsensical paragraph.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim input As String = "This is the first sentence. Is it the beginning " + _
                            "of a literary masterpiece? I think not. Instead, " + _
                            "it is a nonsensical paragraph."
      Dim pattern As String = "\b\(?((?>\w+),?\s?)+[\.!?]\)?"
      Console.WriteLine("With implicit captures:")
      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine("The match: {0}", match.Value)
         Dim groupCtr As Integer = 0
         For Each group As Group In match.Groups
            Console.WriteLine("   Group {0}: {1}", groupCtr, group.Value)
            groupCtr += 1
            Dim captureCtr As Integer = 0
            For Each capture As Capture In group.Captures
               Console.WriteLine("      Capture {0}: {1}", captureCtr, capture.Value)
               captureCtr += 1
            Next
         Next
      Next
      Console.WriteLine()
      Console.WriteLine("With explicit captures only:")
      For Each match As Match In Regex.Matches(input, pattern, RegexOptions.ExplicitCapture)
         Console.WriteLine("The match: {0}", match.Value)
         Dim groupCtr As Integer = 0
         For Each group As Group In match.Groups
            Console.WriteLine("   Group {0}: {1}", groupCtr, group.Value)
            groupCtr += 1
            Dim captureCtr As Integer = 0
            For Each capture As Capture In group.Captures
               Console.WriteLine("      Capture {0}: {1}", captureCtr, capture.Value)
               captureCtr += 1
            Next
         Next
      Next
   End Sub
End Module
' The example displays the following output:
'    With implicit captures:
'    The match: This is the first sentence.
'       Group 0: This is the first sentence.
'          Capture 0: This is the first sentence.
'       Group 1: sentence
'          Capture 0: This
'          Capture 1: is
'          Capture 2: the
'          Capture 3: first
'          Capture 4: sentence
'       Group 2: sentence
'          Capture 0: This
'          Capture 1: is
'          Capture 2: the
'          Capture 3: first
'          Capture 4: sentence
'    The match: Is it the beginning of a literary masterpiece?
'       Group 0: Is it the beginning of a literary masterpiece?
'          Capture 0: Is it the beginning of a literary masterpiece?
'       Group 1: masterpiece
'          Capture 0: Is
'          Capture 1: it
'          Capture 2: the
'          Capture 3: beginning
'          Capture 4: of
'          Capture 5: a
'          Capture 6: literary
'          Capture 7: masterpiece
'       Group 2: masterpiece
'          Capture 0: Is
'          Capture 1: it
'          Capture 2: the
'          Capture 3: beginning
'          Capture 4: of
'          Capture 5: a
'          Capture 6: literary
'          Capture 7: masterpiece
'    The match: I think not.
'       Group 0: I think not.
'          Capture 0: I think not.
'       Group 1: not
'          Capture 0: I
'          Capture 1: think
'          Capture 2: not
'       Group 2: not
'          Capture 0: I
'          Capture 1: think
'          Capture 2: not
'    The match: Instead, it is a nonsensical paragraph.
'       Group 0: Instead, it is a nonsensical paragraph.
'          Capture 0: Instead, it is a nonsensical paragraph.
'       Group 1: paragraph
'          Capture 0: Instead,
'          Capture 1: it
'          Capture 2: is
'          Capture 3: a
'          Capture 4: nonsensical
'          Capture 5: paragraph
'       Group 2: paragraph
'          Capture 0: Instead
'          Capture 1: it
'          Capture 2: is
'          Capture 3: a
'          Capture 4: nonsensical
'          Capture 5: paragraph
'    
'    With explicit captures only:
'    The match: This is the first sentence.
'       Group 0: This is the first sentence.
'          Capture 0: This is the first sentence.
'    The match: Is it the beginning of a literary masterpiece?
'       Group 0: Is it the beginning of a literary masterpiece?
'          Capture 0: Is it the beginning of a literary masterpiece?
'    The match: I think not.
'       Group 0: I think not.
'          Capture 0: I think not.
'    The match: Instead, it is a nonsensical paragraph.
'       Group 0: Instead, it is a nonsensical paragraph.
'          Capture 0: Instead, it is a nonsensical paragraph.
```

정규식 패턴 `\b\(?((?>\w+),?\s?)+[\.!?]\)?`는 다음 테이블과 같이 정의됩니다.

패턴 | 설명
------- | ----------- 
`\b` | 단어 경계에서 시작합니다.
`\(?` | 0개 또는 1개의 여는 괄호("(")를 찾습니다.
`(?>\w+),?` | 하나 이상의 단어 문자 다음에 0개 또는 1개의 쉼표가 있는 일치 항목을 찾습니다. 단어 문자를 찾을 때 역추적하지 않습니다.
`\s?` | 0회 이상 나오는 공백 문자를 찾습니다.
`((\w+),?\s?)+` | 하나 이상의 단어 문자 다음에 0개 또는 1개의 쉼표 및 0개 또는 1개의 공백 문자가 한 번 이상 나타나는 조합을 찾습니다.
`[\.!?]\)?` | 세 가지 문장 부호 기호 다음에 0개 또는 1개의 닫는 괄호(")")가 있는 모든 일치 항목을 찾습니다.
 
또한 **(?n)** 인라인 요소를 사용하여 자동 캡처를 억제할 수 있습니다. 다음 예제에서는 [RegexOptions.ExplicitCapture](xref:System.Text.RegularExpressions.RegexOptions.ExplicitCapture) 옵션 대신 **(?n)** 인라인 요소를 사용하도록 이전 정규식 패턴을 수정합니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string input = "This is the first sentence. Is it the beginning " + 
                     "of a literary masterpiece? I think not. Instead, " + 
                     "it is a nonsensical paragraph.";
      string pattern = @"(?n)\b\(?((?>\w+),?\s?)+[\.!?]\)?";

      foreach (Match match in Regex.Matches(input, pattern))
      {
         Console.WriteLine("The match: {0}", match.Value);
         int groupCtr = 0;
         foreach (Group group in match.Groups)
         {
            Console.WriteLine("   Group {0}: {1}", groupCtr, group.Value);
            groupCtr++;
            int captureCtr = 0;
            foreach (Capture capture in group.Captures)
            {
               Console.WriteLine("      Capture {0}: {1}", captureCtr, capture.Value);
               captureCtr++;
            }
         }
      }
   }
}
// The example displays the following output:
//       The match: This is the first sentence.
//          Group 0: This is the first sentence.
//             Capture 0: This is the first sentence.
//       The match: Is it the beginning of a literary masterpiece?
//          Group 0: Is it the beginning of a literary masterpiece?
//             Capture 0: Is it the beginning of a literary masterpiece?
//       The match: I think not.
//          Group 0: I think not.
//             Capture 0: I think not.
//       The match: Instead, it is a nonsensical paragraph.
//          Group 0: Instead, it is a nonsensical paragraph.
//             Capture 0: Instead, it is a nonsensical paragraph.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim input As String = "This is the first sentence. Is it the beginning " + _
                            "of a literary masterpiece? I think not. Instead, " + _
                            "it is a nonsensical paragraph."
      Dim pattern As String = "(?n)\b\(?((?>\w+),?\s?)+[\.!?]\)?"

      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine("The match: {0}", match.Value)
         Dim groupCtr As Integer = 0
         For Each group As Group In match.Groups
            Console.WriteLine("   Group {0}: {1}", groupCtr, group.Value)
            groupCtr += 1
            Dim captureCtr As Integer = 0
            For Each capture As Capture In group.Captures
               Console.WriteLine("      Capture {0}: {1}", captureCtr, capture.Value)
               captureCtr += 1
            Next
         Next
      Next
   End Sub
End Module
' The example displays the following output:
'       The match: This is the first sentence.
'          Group 0: This is the first sentence.
'             Capture 0: This is the first sentence.
'       The match: Is it the beginning of a literary masterpiece?
'          Group 0: Is it the beginning of a literary masterpiece?
'             Capture 0: Is it the beginning of a literary masterpiece?
'       The match: I think not.
'          Group 0: I think not.
'             Capture 0: I think not.
'       The match: Instead, it is a nonsensical paragraph.
'          Group 0: Instead, it is a nonsensical paragraph.
'             Capture 0: Instead, it is a nonsensical paragraph.
```

마지막으로, 인라인 그룹 요소 **(?n:)**를 사용하여 그룹별로 자동 캡처를 억제할 수 있습니다. 다음 예제에서는 바깥쪽 그룹 `((?>\w+),?\s?)`에서 명명되지 않은 캡처를 억제하도록 이전 패턴을 수정합니다. 여기서는 안쪽 그룹에서도 명명되지 않은 캡처가 억제됩니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string input = "This is the first sentence. Is it the beginning " + 
                     "of a literary masterpiece? I think not. Instead, " + 
                     "it is a nonsensical paragraph.";
      string pattern = @"\b\(?(?n:(?>\w+),?\s?)+[\.!?]\)?";

      foreach (Match match in Regex.Matches(input, pattern))
      {
         Console.WriteLine("The match: {0}", match.Value);
         int groupCtr = 0;
         foreach (Group group in match.Groups)
         {
            Console.WriteLine("   Group {0}: {1}", groupCtr, group.Value);
            groupCtr++;
            int captureCtr = 0;
            foreach (Capture capture in group.Captures)
            {
               Console.WriteLine("      Capture {0}: {1}", captureCtr, capture.Value);
               captureCtr++;
            }
         }
      }
   }
}
// The example displays the following output:
//       The match: This is the first sentence.
//          Group 0: This is the first sentence.
//             Capture 0: This is the first sentence.
//       The match: Is it the beginning of a literary masterpiece?
//          Group 0: Is it the beginning of a literary masterpiece?
//             Capture 0: Is it the beginning of a literary masterpiece?
//       The match: I think not.
//          Group 0: I think not.
//             Capture 0: I think not.
//       The match: Instead, it is a nonsensical paragraph.
//          Group 0: Instead, it is a nonsensical paragraph.
//             Capture 0: Instead, it is a nonsensical paragraph.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim input As String = "This is the first sentence. Is it the beginning " + _
                            "of a literary masterpiece? I think not. Instead, " + _
                            "it is a nonsensical paragraph."
      Dim pattern As String = "\b\(?(?n:(?>\w+),?\s?)+[\.!?]\)?"

      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine("The match: {0}", match.Value)
         Dim groupCtr As Integer = 0
         For Each group As Group In match.Groups
            Console.WriteLine("   Group {0}: {1}", groupCtr, group.Value)
            groupCtr += 1
            Dim captureCtr As Integer = 0
            For Each capture As Capture In group.Captures
               Console.WriteLine("      Capture {0}: {1}", captureCtr, capture.Value)
               captureCtr += 1
            Next
         Next
      Next
   End Sub
End Module
' The example displays the following output:
'       The match: This is the first sentence.
'          Group 0: This is the first sentence.
'             Capture 0: This is the first sentence.
'       The match: Is it the beginning of a literary masterpiece?
'          Group 0: Is it the beginning of a literary masterpiece?
'             Capture 0: Is it the beginning of a literary masterpiece?
'       The match: I think not.
'          Group 0: I think not.
'             Capture 0: I think not.
'       The match: Instead, it is a nonsensical paragraph.
'          Group 0: Instead, it is a nonsensical paragraph.
'             Capture 0: Instead, it is a nonsensical paragraph.
```

## <a name="compiled-regular-expressions"></a>컴파일된 정규식

기본적으로 .NET의 정규식은 해석됩니다. [Regex](xref:System.Text.RegularExpressions.Regex) 개체가 인스턴스화되거나 고정 [Regex](xref:System.Text.RegularExpressions.Regex) 메서드가 호출되면 정규식 패턴이 일련의 사용자 지정 opcode로 구문 분석되고 해석기는 이러한 opcode를 사용하여 정규식을 실행합니다. 여기에는 절충 사항이 수반됩니다. 즉, 정규식 엔진의 초기화 비용은 런타임 성능을 희생하여 최소화됩니다.

[RegexOptions.Compiled](xref:System.Text.RegularExpressions.RegexOptions.Compiled) 옵션을 사용하여, 해석된 정규식 대신 컴파일된 정규식을 사용할 수 있습니다. 이 경우 패턴이 정규식 엔진에 전달되면 일련의 opcode로 구문 분석된 다음, 공용 언어 런타임으로 직접 전달될 수 있는 MSIL(Microsoft Intermediate Language)로 변환됩니다. 컴파일된 정규식은 초기화 시간을 희생하여 런타임 성능을 최대화합니다.

> [!NOTE]
> 정규식은 [RegexOptions.Compiled](xref:System.Text.RegularExpressions.RegexOptions.Compiled) 값을 [Regex](xref:System.Text.RegularExpressions.Regex) 클래스 생성자 또는 정적 패턴 일치 메서드의 options 매개 변수에 제공해서만 컴파일할 수 있으며, 인라인 옵션으로는 사용할 수 없습니다. 
 

컴파일된 정규식은 정적 정규식과 인스턴스 정규식 둘 다에 대한 호출에 사용할 수 있습니다. 정적 정규식에서 [RegexOptions.Compiled](xref:System.Text.RegularExpressions.RegexOptions.Compiled) 옵션은 정규식 패턴 일치 메서드의 options 매개 변수에 전달됩니다. 인스턴스 정규식에서 이 옵션은 [Regex](xref:System.Text.RegularExpressions.Regex) 클래스 생성자의 options 매개 변수에 전달됩니다. 따라서 두 가지 경우 모두에서 성능이 향상됩니다. 

그러나 이 성능 향상은 다음 조건에서만 발생합니다.

* 특정 정규식을 나타내는 [Regex](xref:System.Text.RegularExpressions.Regex) 개체가 정규식 패턴 일치 메서드에 대한 여러 번의 호출에 사용됩니다.

* [Regex](xref:System.Text.RegularExpressions.Regex) 개체가 범위를 벗어날 수 없어 재사용될 수 있습니다.

* 정적 정규식이 정규식 패턴 일치 메서드에 대한 여러 번의 호출에 사용됩니다. (정적 메서드 호출에 사용된 정규식이 정규식 엔진에 의해 캐시되므로 성능 향상이 가능합니다.) 

## <a name="ignore-white-space"></a>공백 무시

기본적으로 정규식 패턴에서 공백은 중요합니다. 공백은 정규식 엔진이 입력 문자열에서 공백 문자를 강제로 일치시키도록 합니다. 따라서 정규식 `"\b\w+\s"`와 `"\b\w+ "`는 거의 동일한 정규식입니다. 또한 정규식 패턴에 숫자 기호(**#**)가 있으면 이는 일치시킬 리터럴 문자로 해석됩니다.

[RegexOptions.IgnorePatternWhitespace](xref:System.Text.RegularExpressions.RegexOptions.IgnorePatternWhitespace) 옵션 또는 **x** 인라인 옵션은 다음과 같이 이 기본 동작을 변경합니다.

* 정규식 패턴에서 이스케이프되지 않은 공백은 무시됩니다. 정규식 패턴의 일부가 되려면 공백 문자가 이스케이프되어야 합니다(예: **\s** 또는 "**\** "로).

* 숫자 기호(**#**)는 리터럴 문자가 아니라 주석의 시작 부분으로 해석됩니다. 정규식 패턴에서 모든 텍스트는 **#** 문자부터 문자열의 끝 부분까지 주석으로 해석됩니다.

그러나 다음 경우에 정규식의 공백 문자는 [RegexOptions.IgnorePatternWhitespace](xref:System.Text.RegularExpressions.RegexOptions.IgnorePatternWhitespace) 옵션을 사용하더라도 무시되지 않습니다. 

* 문자 클래스 내의 공백은 항상 리터럴로 해석됩니다. 예를 들어, 정규식 패턴 `[ .,;:]`는 모든 단일 공백 문자, 마침표, 쉼표, 세미콜론 또는 콜론과 일치합니다. 

* 중괄호로 묶은 수량자 내에는 공백이 허용되지 않습니다(예: **{**_n_**}**, **{**_n_**,}** 및 **{**_n_**,**_m_**}**). 예를 들어, 정규식 패턴 **\d{1. 3}**은 공백 문자를 포함하고 있기 때문에 1자리에서 3자리까지의 숫자로 이루어진 어떠한 숫자 시퀀스와도 일치하지 않습니다. 

* 언어 요소를 도입하는 문자 시퀀스 내에는 공백이 허용되지 않습니다. 예: 

    * 언어 요소 **(?:**_subexpression_**)**는 비 캡처링 그룹을 나타내고, 요소의 **(?:** 부분은 공백을 포함할 수 없습니다. **(? :**_subexpression_**)** 패턴은 정규식 엔진이 패턴을 구문 분석할 수 없고 **(? :**_subexpression_**)** 패턴이 *subexpression*과 일치하지 않으므로 런타임에 [ArgumentException](xref:System.ArgumentException)을 throw합니다.

    * 유니코드 범주 또는 명명된 블록을 나타내는 언어 요소 **\p{**_name_**}**는 요소의 **\p{** 부분에 공백을 포함할 수 없습니다. 공백을 포함하는 경우 이 요소가 런타임에 [ArgumentException](xref:System.ArgumentException)을 throw합니다. 

이 옵션을 사용하면 일반적으로 구문 분석 및 이해가 어려운 정규식을 단순화하는 데 도움이 됩니다. 이 옵션은 가독성을 향상시키고 정규식을 문서화할 수 있게 해줍니다. 

다음 예제에서는 다음과 같은 정규식 패턴을 정의합니다.

`\b \(? ( (?>\w+) ,?\s? )+ [\.!?] \)? # Matches an entire sentence`.

이 패턴은 [RegexOptions.IgnorePatternWhitespace](xref:System.Text.RegularExpressions.RegexOptions.IgnorePatternWhitespace) 옵션을 사용하여 패턴 공백을 무시하는 점을 제외하고는 [명시적 캡처만 해당](#explicit-captures-only) 섹션에 정의된 패턴과 유사합니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string input = "This is the first sentence. Is it the beginning " + 
                     "of a literary masterpiece? I think not. Instead, " + 
                     "it is a nonsensical paragraph.";
      string pattern = @"\b\(?((?>\w+),?\s?)+[\.!?]\)?";

      foreach (Match match in Regex.Matches(input, pattern, RegexOptions.IgnorePatternWhitespace))
         Console.WriteLine(match.Value);
   }
}
// The example displays the following output:
//       This is the first sentence.
//       Is it the beginning of a literary masterpiece?
//       I think not.
//       Instead, it is a nonsensical paragraph.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim input As String = "This is the first sentence. Is it the beginning " + _
                            "of a literary masterpiece? I think not. Instead, " + _
                            "it is a nonsensical paragraph."
      Dim pattern As String = "\b \(? ( (?>\w+) ,?\s? )+  [\.!?] \)? # Matches an entire sentence."

      For Each match As Match In Regex.Matches(input, pattern, RegexOptions.IgnorePatternWhitespace)
         Console.WriteLine(match.Value)
      Next
   End Sub
End Module
' The example displays the following output:
'       This is the first sentence.
'       Is it the beginning of a literary masterpiece?
'       I think not.
'       Instead, it is a nonsensical paragraph.
```

다음 예제에서는 인라인 옵션 **(?x)**를 사용하여 패턴 공백을 무시합니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string input = "This is the first sentence. Is it the beginning " + 
                     "of a literary masterpiece? I think not. Instead, " + 
                     "it is a nonsensical paragraph.";
      string pattern = @"(?x)\b \(? ( (?>\w+) ,?\s? )+  [\.!?] \)? # Matches an entire sentence.";

      foreach (Match match in Regex.Matches(input, pattern))
         Console.WriteLine(match.Value);
   }
}
// The example displays the following output:
//       This is the first sentence.
//       Is it the beginning of a literary masterpiece?
//       I think not.
//       Instead, it is a nonsensical paragraph.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim input As String = "This is the first sentence. Is it the beginning " + _
                            "of a literary masterpiece? I think not. Instead, " + _
                            "it is a nonsensical paragraph."
      Dim pattern As String = "(?x)\b \(? ( (?>\w+) ,?\s? )+  [\.!?] \)? # Matches an entire sentence."

      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine(match.Value)
      Next
   End Sub
End Module
' The example displays the following output:
'       This is the first sentence.
'       Is it the beginning of a literary masterpiece?
'       I think not.
'       Instead, it is a nonsensical paragraph.
```

## <a name="righttoleft-mode"></a>오른쪽에서 왼쪽 모드

기본적으로 정규식 엔진은 왼쪽에서 오른쪽으로 검색합니다. [RegexOptions.RightToLeft](xref:System.Text.RegularExpressions.RegexOptions.RightToLeft) 옵션을 사용하여 검색 방향을 반대로 할 수 있습니다. 검색은 자동으로 문자열의 마지막 문자 위치에서 시작됩니다. 시작 위치 매개 변수를 포함한 패턴 일치 메서드의 경우(예: [Regex.Match(String, Int32)](xref:System.Text.RegularExpressions.Regex.Match(System.String,System.Int32))) 시작 위치는 검색이 시작될 가장 오른쪽 문자 위치의 인덱스입니다. 

> [!NOTE]
> 오른쪽에서 왼쪽 패턴 모드는 [RegexOptions.RightToLeft](xref:System.Text.RegularExpressions.RegexOptions.RightToLeft) 값을 [Regex](xref:System.Text.RegularExpressions.Regex) 클래스 생성자 또는 정적 패턴 일치 메서드의 options 매개 변수에 제공해서만 사용할 수 있으며, 인라인 옵션으로는 사용할 수 없습니다. 
 

[RegexOptions.RightToLeft](xref:System.Text.RegularExpressions.RegexOptions.RightToLeft) 옵션은 검색 방향만 변경하며 오른쪽에서 왼쪽으로 정규식 패턴을 해석하지는 않습니다. 예를 들어, 정규식 `\bb\w+\s`는 문자 "b"로 시작하고 뒤에 공백 문자가 있는 단어와 일치합니다. 다음 예제에서 입력 문자열은 하나 이상의 "b" 문자를 포함하는 세 단어로 구성됩니다. 첫 번째 단어는 "b"로 시작하고, 두 번째 단어는 "b"로 끝나며, 세 번째 단어는 단어 중간에 두 개의 "b" 문자를 포함합니다. 예제의 출력이 보여 주는 것처럼, 첫 번째 단어만 정규식 패턴과 일치합니다. 

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"\bb\w+\s";
      string input = "builder rob rabble";
      foreach (Match match in Regex.Matches(input, pattern, RegexOptions.RightToLeft))
         Console.WriteLine("'{0}' found at position {1}.", match.Value, match.Index);     
   }
}
// The example displays the following output:
//       'builder ' found at position 0.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "\bb\w+\s"
      Dim input As String = "builder rob rabble"
      For Each match As Match In Regex.Matches(input, pattern, RegexOptions.RightToLeft)
         Console.WriteLine("'{0}' found at position {1}.", match.Value, match.Index)     
      Next
   End Sub
End Module
' The example displays the following output:
'       'builder ' found at position 0.
```

lookahead 어설션(**(?**=_subexpression_**)** 언어 요소) 및 lookbehind 어설션(**(?<**=_subexpression_**)** 언어 요소)은 방향을 변경하지 않습니다. lookahead 어설션은 오른쪽을 확인하고, lookbehind 어설션은 왼쪽을 확인합니다. 예를 들어, 정규식 `(?<=\d{1,2}\s)\w+,?\s\d{4}`에서는 lookbehind 어설션을 사용하여 월 이름 앞에 있는 날짜에 대해 테스트합니다. 그런 다음 정규식은 월 및 연도를 일치시킵니다. lookahead 및 lookbehind 어설션에 대한 자세한 내용은 [정규식의 그룹화 구문](grouping.md)을 참조하세요.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string[] inputs = { "1 May 1917", "June 16, 2003" };
      string pattern = @"(?<=\d{1,2}\s)\w+,?\s\d{4}";

      foreach (string input in inputs)
      {
         Match match = Regex.Match(input, pattern, RegexOptions.RightToLeft);
         if (match.Success)
            Console.WriteLine("The date occurs in {0}.", match.Value);
         else
            Console.WriteLine("{0} does not match.", input);
      }
   }
}
// The example displays the following output:
//       The date occurs in May 1917.
//       June 16, 2003 does not match.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim inputs() As String = { "1 May 1917", "June 16, 2003" }
      Dim pattern As String = "(?<=\d{1,2}\s)\w+,?\s\d{4}"

      For Each input As String In inputs
         Dim match As Match = Regex.Match(input, pattern, RegexOptions.RightToLeft)
         If match.Success Then
            Console.WriteLine("The date occurs in {0}.", match.Value)
         Else
            Console.WriteLine("{0} does not match.", input)
         End If
      Next
   End Sub
End Module
' The example displays the following output:
'       The date occurs in May 1917.
'       June 16, 2003 does not match.
```

이 정규식 패턴은 다음 테이블과 같이 정의됩니다.

패턴 | 설명
------- | ----------- 
`(?<=\d{1,2}\s)` | 일치 항목의 시작 부분 앞에는 한 개 또는 두 개의 10진수와 그 뒤에 공백이 하나 있어야 합니다.
`\w+` | 하나 이상의 단어 문자를 찾습니다.
`,?` | 0개 또는 1개의 쉼표 문자를 찾습니다.
`\s` | 공백 문자를 찾습니다.
`\d{4}` | 네 개의 10진수를 찾습니다.
 
## <a name="ecmascript-matching-behavior"></a>ECMAScript 일치 동작

기본적으로 정규식 엔진은 정규식 패턴을 입력 텍스트에 일치시킬 때 정식 동작을 사용합니다. 그러나 [RegexOptions.ECMAScript](xref:System.Text.RegularExpressions.RegexOptions.ECMAScript) 옵션을 지정하여 ECMAScript 일치 동작을 사용하도록 정규식 엔진에 지시할 수 있습니다. 

> [!NOTE]
> ECMAScript와 호환되는 동작은 [RegexOptions.ECMAScript](xref:System.Text.RegularExpressions.RegexOptions.ECMAScript) 값을 [Regex](xref:System.Text.RegularExpressions.Regex) 클래스 생성자 또는 정적 패턴 일치 메서드의 options 매개 변수에 제공해서만 사용할 수 있으며, 인라인 옵션으로는 사용할 수 없습니다. 
 
[RegexOptions.ECMAScript](xref:System.Text.RegularExpressions.RegexOptions.ECMAScript) 옵션은 [RegexOptions.IgnoreCase](xref:System.Text.RegularExpressions.RegexOptions.IgnoreCase) 및 [RegexOptions.Multiline](xref:System.Text.RegularExpressions.RegexOptions.Multiline) 옵션과 결합해야만 사용할 수 있습니다. 정규식에 다른 옵션을 사용하면 [ArgumentOutOfRangeException](xref:System.ArgumentOutOfRangeException)이 발생합니다.

ECMAScript의 동작과 정식 정규식의 동작은 문자 클래스 구문, 자신을 참조하는 캡처링 그룹 및 8진수 대 역참조 해석의 세 가지 영역에서 다릅니다. 

* 문자 클래스 구문. 정식 정규식은 유니코드를 지원하는 반면 ECMAScript는 지원하지 않으므로, ECMAScript의 문자 클래스의 구문이 더 제한되어 있으며 일부 문자 클래스 언어 요소는 다른 의미를 지닙니다. 예를 들어, ECMAScript는 유니코드 범주 또는 블록 요소 *\p* 및 **\P**와 같은 언어 요소를 지원하지 않습니다. 마찬가지로, 단어 문자와 일치하는 **\w** 요소는 ECMAScript를 사용할 경우 **[a-zA-Z_0-9]** 문자 클래스와 동일하고 정식 동작을 사용할 경우 **[\p{Ll}\p{Lu}\p{Lt}\p{Lo}\p{Nd}\p{Pc}\p{Lm}]**과 동일합니다. 자세한 내용은 [정규식의 문자 클래스](classes.md)를 참조하세요.

  다음 예제에서는 정식 패턴 일치와 ECMAScript 패턴 일치 간의 차이점을 보여 줍니다. 이 예제에서는 뒤에 공백 문자가 있는 단어와 일치하는 정규식 `\b(\w+\s*)+`를 정의합니다. 입력은 두 개의 문자열로 구성되어 있는데, 한 문자열은 라틴 문자 집합을 사용하고 다른 문자열은 키릴 자모 문자 집합을 사용합니다. 출력에 표시된 것처럼, ECMAScript 일치를 사용하는 [Regex.IsMatch(String, String, RegexOptions)](xref:System.Text.RegularExpressions.Regex.IsMatch(System.String,System.String,System.Text.RegularExpressions.RegexOptions)) 메서드에 대한 호출은 키릴 자모 단어와 일치하지 않는 반면 정식 일치를 사용하는 메서드 호출은 이러한 단어와 일치합니다. 

  ```csharp
  using System;
  using System.Text.RegularExpressions;
  
  public class Example
  {
     public static void Main()
     {
        string[] values = { "целый мир", "the whole world" };
        string pattern = @"\b(\w+\s*)+";
        foreach (var value in values)
        {
           Console.Write("Canonical matching: ");
           if (Regex.IsMatch(value, pattern))
              Console.WriteLine("'{0}' matches the pattern.", value);
           else
              Console.WriteLine("{0} does not match the pattern.", value);
  
           Console.Write("ECMAScript matching: ");
           if (Regex.IsMatch(value, pattern, RegexOptions.ECMAScript))
              Console.WriteLine("'{0}' matches the pattern.", value);
           else
              Console.WriteLine("{0} does not match the pattern.", value);
           Console.WriteLine();
        }
     }
  }
  // The example displays the following output:
  //       Canonical matching: 'целый мир' matches the pattern.
  //       ECMAScript matching: целый мир does not match the pattern.
  //       
  //       Canonical matching: 'the whole world' matches the pattern.
  //       ECMAScript matching: 'the whole world' matches the pattern.
  ```

  ```vb
  Imports System.Text.RegularExpressions

  Module Example
     Public Sub Main()
        Dim values() As String = { "целый мир", "the whole world" }
        Dim pattern As String = "\b(\w+\s*)+"
        For Each value In values
           Console.Write("Canonical matching: ")
           If Regex.IsMatch(value, pattern)
              Console.WriteLine("'{0}' matches the pattern.", value)
           Else
              Console.WriteLine("{0} does not match the pattern.", value)
           End If

           Console.Write("ECMAScript matching: ")
           If Regex.IsMatch(value, pattern, RegexOptions.ECMAScript)
              Console.WriteLine("'{0}' matches the pattern.", value)
           Else
              Console.WriteLine("{0} does not match the pattern.", value)
           End If
           Console.WriteLine()
        Next
     End Sub
  End Module
  ' The example displays the following output:
  '       Canonical matching: 'целый мир' matches the pattern.
  '       ECMAScript matching: целый мир does not match the pattern.
  '       
  '       Canonical matching: 'the whole world' matches the pattern.
  '       ECMAScript matching: 'the whole world' matches the pattern.
  ```

* 자신을 참조하는 캡처링 그룹. 자신에 대한 역참조가 있는 정규식 캡처 클래스는 각 캡처 반복으로 업데이트되어야 합니다. 다음 예제에서 보여 주는 것처럼, 이 기능은 정규식 `((a+)(\1) ?)+`가 ECMAScript를 사용할 경우에는 입력 문자열 " aa aaaa aaaaaa "과 일치하고 정식 일치를 사용할 경우에는 일치하지 않도록 합니다. 

  ```csharp
  using System;
  using System.Text.RegularExpressions;

  public class Example
  {
     static string pattern;
  
     public static void Main()
     {
        string input = "aa aaaa aaaaaa "; 
        pattern = @"((a+)(\1) ?)+";
  
        // Match input using canonical matching.
        AnalyzeMatch(Regex.Match(input, pattern));

        // Match input using ECMAScript.
        AnalyzeMatch(Regex.Match(input, pattern, RegexOptions.ECMAScript));
     }   

     private static void AnalyzeMatch(Match m)
     {
        if (m.Success)
        {
           Console.WriteLine("'{0}' matches {1} at position {2}.",  
                             pattern, m.Value, m.Index);
           int grpCtr = 0;
           foreach (Group grp in m.Groups)
           {
              Console.WriteLine("   {0}: '{1}'", grpCtr, grp.Value);
              grpCtr++;
              int capCtr = 0;
              foreach (Capture cap in grp.Captures)
              {
                 Console.WriteLine("      {0}: '{1}'", capCtr, cap.Value);
                 capCtr++;
              }
           }
        }
        else
        {
           Console.WriteLine("No match found.");
        }   
        Console.WriteLine();
     }
  }
  // The example displays the following output:
  //    No match found.
  //    
  //    '((a+)(\1) ?)+' matches aa aaaa aaaaaa  at position 0.
  //       0: 'aa aaaa aaaaaa '
  //          0: 'aa aaaa aaaaaa '
  //       1: 'aaaaaa '
  //          0: 'aa '
  //          1: 'aaaa '
  //          2: 'aaaaaa '
  //       2: 'aa'
  //          0: 'aa'
  //          1: 'aa'
  //          2: 'aa'
  //       3: 'aaaa '
  //          0: ''
  //          1: 'aa '
  //          2: 'aaaa '
  ```

  ```vb
  Imports System.Text.RegularExpressions

  Module Example
     Dim pattern As String

     Public Sub Main()
        Dim input As String = "aa aaaa aaaaaa " 
        pattern = "((a+)(\1) ?)+"
  
        ' Match input using canonical matching.
        AnalyzeMatch(Regex.Match(input, pattern))

        ' Match input using ECMAScript.
        AnalyzeMatch(Regex.Match(input, pattern, RegexOptions.ECMAScript))
     End Sub   

     Private Sub AnalyzeMatch(m As Match)
        If m.Success
           Console.WriteLine("'{0}' matches {1} at position {2}.", _ 
                             pattern, m.Value, m.Index)
           Dim grpCtr As Integer = 0
           For Each grp As Group In m.Groups
              Console.WriteLine("   {0}: '{1}'", grpCtr, grp.Value)
              grpCtr += 1
              Dim capCtr As Integer = 0
              For Each cap As Capture In grp.Captures
                 Console.WriteLine("      {0}: '{1}'", capCtr, cap.Value)
                 capCtr += 1
              Next
           Next
        Else
           Console.WriteLine("No match found.")
        End If   
        Console.WriteLine()
     End Sub
  End Module
  ' The example displays the following output:
  '    No match found.
  '    
  '    '((a+)(\1) ?)+' matches aa aaaa aaaaaa  at position 0.
  '       0: 'aa aaaa aaaaaa '
  '          0: 'aa aaaa aaaaaa '
  '       1: 'aaaaaa '
  '          0: 'aa '
  '          1: 'aaaa '  
  '          2: 'aaaaaa '
  '       2: 'aa'
  '          0: 'aa'
  '          1: 'aa'
  '          2: 'aa'
  '       3: 'aaaa '
  '          0: ''
  '          1: 'aa '
  '          2: 'aaaa '
  ``

  The regular expression is defined as shown in the following table.

Pattern | Description
------- | ----------- 
`(a+)` | Match the letter "a" one or more times. This is the second capturing group.
`(\1)` | Match the substring captured by the first capturing group. This is the third capturing group.
`?` | Match zero or one space characters.
`((a+)(\1) ?)+` | Match the pattern of one or more "a" characters followed by a string that matches the first capturing group followed by zero or one space characters one or more times. This is the first capturing group.
  
* Resolution of ambiguities between octal escapes and backreferences. The following table summarizes the differences in octal versus backreference interpretation by canonical and ECMAScript regular expressions.

Regular expression | Canonical behavior | ECMAScript behavior
------------------ | ------------------ | ------------------- 
`\0` followed by 0 to 2 octal digits | Interpret as an octal. For example, `\044` is always interpreted as an octal value and means "**$**". | Same behavior.
**\** followed by a digit from 1 to 9, followed by no additional decimal digits | Interpret as a backreference. For example, \9 always means backreference 9, even if a ninth capturing group does not exist. If the capturing group does not exist, the regular expression parser throws an [ArgumentException](xref:System.ArgumentException). | If a single decimal digit capturing group exists, backreference to that digit. Otherwise, interpret the value as a literal.
**\** followed by a digit from 1 to 9, followed by additional decimal digits | Interpret the digits as a decimal value. If that capturing group exists, interpret the expression as a backreference. Otherwise, interpret the leading octal digits up to octal 377; that is, consider only the low 8 bits of the value. Interpret the remaining digits as literals. For example, in the expression `\3000`, if capturing group 300 exists, interpret as backreference 300; if capturing group 300 does not exist, interpret as octal 300 followed by 0. | Interpret as a backreference by converting as many digits as possible to a decimal value that can refer to a capture. If no digits can be converted, interpret as an octal by using the leading octal digits up to octal 377; interpret the remaining digits as literals.
 
## Comparison using the invariant culture

By default, when the regular expression engine performs case-insensitive comparisons, it uses the casing conventions of the current culture to determine equivalent uppercase and lowercase characters. 

However, this behavior is undesirable for some types of comparisons, particularly when comparing user input to the names of system resources, such as passwords, files, or URLs. The following example illustrates such as scenario. The code is intended to block access to any resource whose URL is prefaced with **FILE://**. The regular expression attempts a case-insensitive match with the string by using the regular expression `$FILE://`. However, when the current system culture is tr-TR (Turkish-Turkey), "I" is not the uppercase equivalent of "i". As a result, the call to the [Regex.IsMatch](xref:System.Text.RegularExpressions.Regex.IsMatch(System.String)) method returns `false`, and access to the file is allowed. 

```csharp
CultureInfo defaultCulture = Thread.CurrentThread.CurrentCulture;
Thread.CurrentThread.CurrentCulture = new CultureInfo("tr-TR");

string input = "file://c:/Documents.MyReport.doc";
string pattern = "FILE://";

Console.WriteLine("Culture-sensitive matching ({0} culture)...", 
                  Thread.CurrentThread.CurrentCulture.Name);
if (Regex.IsMatch(input, pattern, RegexOptions.IgnoreCase))
   Console.WriteLine("URLs that access files are not allowed.");      
else
   Console.WriteLine("Access to {0} is allowed.", input);

Thread.CurrentThread.CurrentCulture = defaultCulture;
// The example displays the following output:
//       Culture-sensitive matching (tr-TR culture)...
//       Access to file://c:/Documents.MyReport.doc is allowed.
```

```vb
Dim defaultCulture As CultureInfo = Thread.CurrentThread.CurrentCulture
Thread.CurrentThread.CurrentCulture = New CultureInfo("tr-TR")

Dim input As String = "file://c:/Documents.MyReport.doc"
Dim pattern As String = "$FILE://"

Console.WriteLine("Culture-sensitive matching ({0} culture)...", _
                  Thread.CurrentThread.CurrentCulture.Name)
If Regex.IsMatch(input, pattern, RegexOptions.IgnoreCase) Then
   Console.WriteLine("URLs that access files are not allowed.")      
Else
   Console.WriteLine("Access to {0} is allowed.", input)
End If

Thread.CurrentThread.CurrentCulture = defaultCulture
' The example displays the following output:
'       Culture-sensitive matching (tr-TR culture)...
'       Access to file://c:/Documents.MyReport.doc is allowed.
```

> [!NOTE]
>   대/소문자를 구분하고 고정 문화권을 사용하는 문자열 비교에 대한 자세한 내용은 [문자열 사용에 대한 유용한 정보](best-practices.md)를 참조하세요.
 
현재 문화권의 대/소문자를 구분하지 않는 비교를 사용하는 대신 [RegexOptions.CultureInvariant](xref:System.Text.RegularExpressions.RegexOptions.CultureInvariant) 옵션을 지정하여 언어의 문화적 차이를 무시하고 고정 문화권의 규칙을 사용할 수 있습니다.

> [!NOTE]
> 고정 문화권을 사용하는 비교는 [RegexOptions.CultureInvariant](xref:System.Text.RegularExpressions.RegexOptions.CultureInvariant) 값을 [Regex](xref:System.Text.RegularExpressions.Regex) 클래스 생성자 또는 정적 패턴 일치 메서드의 options 매개 변수에 제공해서만 사용할 수 있으며, 인라인 옵션으로는 사용할 수 없습니다. 
 
다음 예제는 [RegexOptions.CultureInvariant](xref:System.Text.RegularExpressions.RegexOptions.CultureInvariant)를 포함하는 옵션을 사용하여 고정 [Regex.IsMatch(String, String, RegexOptions)](xref:System.Text.RegularExpressions.Regex.IsMatch(System.String,System.String,System.Text.RegularExpressions.RegexOptions)) 메서드를 호출한다는 점을 제외하고 이전 예제와 동일합니다. 현재 문화권이 터키어(터키)로 설정된 경우에도 정규식 엔진은 성공적으로 "FILE"과 "file"을 일치시키고 파일 리소스에 대한 액세스를 차단할 수 있습니다. 

```csharp
CultureInfo defaultCulture = Thread.CurrentThread.CurrentCulture;
Thread.CurrentThread.CurrentCulture = new CultureInfo("tr-TR");

string input = "file://c:/Documents.MyReport.doc";
string pattern = "FILE://";

Console.WriteLine("Culture-insensitive matching...");
if (Regex.IsMatch(input, pattern, 
                  RegexOptions.IgnoreCase | RegexOptions.CultureInvariant)) 
   Console.WriteLine("URLs that access files are not allowed.");
else
   Console.WriteLine("Access to {0} is allowed.", input);

Thread.CurrentThread.CurrentCulture = defaultCulture;
// The example displays the following output:
//       Culture-insensitive matching...
//       URLs that access files are not allowed.
```

```vb
Dim defaultCulture As CultureInfo = Thread.CurrentThread.CurrentCulture
Thread.CurrentThread.CurrentCulture = New CultureInfo("tr-TR")

Dim input As String = "file://c:/Documents.MyReport.doc"
Dim pattern As String = "$FILE://"

Console.WriteLine("Culture-insensitive matching...")
If Regex.IsMatch(input, pattern, _
               RegexOptions.IgnoreCase Or RegexOptions.CultureInvariant) Then
   Console.WriteLine("URLs that access files are not allowed.")      
Else
   Console.WriteLine("Access to {0} is allowed.", input)
End If
Thread.CurrentThread.CurrentCulture = defaultCulture
' The example displays the following output:
'        Culture-insensitive matching...
'        URLs that access files are not allowed.
```

## <a name="see-also"></a>참고 항목

[정규식 언어 - 빠른 참조](quick-ref.md)




<!--HONumber=Nov16_HO3-->


