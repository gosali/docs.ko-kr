---
title: "정규식의 문자 클래스"
description: "정규식의 문자 클래스"
keywords: .NET, .NET Core
author: stevehoag
ms.author: shoag
manager: wpickett
ms.date: 07/29/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: c7a9305f-7144-4fe8-80e8-a727bf7d223f
translationtype: Human Translation
ms.sourcegitcommit: b20713600d7c3ddc31be5885733a1e8910ede8c6
ms.openlocfilehash: f8ebb1db670669e2e8666cd5ef90f72237c105e4

---

# <a name="character-classes-in-regular-expressions"></a>정규식의 문자 클래스

문자 클래스는 문자 집합을 정의하며, 이 중 하나가 입력 문자열에서 발생하면 일치하는 것으로 판정할 수 있습니다. .NET의 정규식 언어는 다음과 같은 문자 클래스를 지원합니다.

* 긍정 문자 그룹. 입력 문자열의 문자는 지정된 문자 집합 중 하나와 일치해야 합니다. 자세한 내용은 [긍정 문자 그룹](#positive-character-group--)을 참조하세요.

* 부정 문자 그룹. 입력 문자열의 문자는 지정된 문자 집합 중 하나와 일치하면 안 됩니다. 자세한 내용은 [부정 문자 그룹](#negative-character-group-)을 참조하세요.

* 임의의 문자. . (점 또는 마침표) 문자(정규식)는 **\n**을 제외한 모든 문자와 일치하는 와일드카드 문자입니다. 자세한 내용은 [임의의 문자](#any-character-)를 참조하세요. 

* 일반 유니코드 범주 또는 명명된 블록입니다. 입력 문자열의 문자는 일치하는 것으로 판정하려면 특정 유니코드 범주의 멤버이거나 유니코드 문자의 연속된 범위 내에 있어야 합니다. 자세한 내용은 [유니코드 범주 또는 유니코드 블록](#unicode-category-or-unicode-block-p)을 참조하세요.

* 부정 일반 유니코드 범주 또는 명명된 블록입니다. 입력 문자열의 문자는 일치하는 것으로 판정하려면 특정 유니코드 범주의 멤버가 아니거나 유니코드 문자의 연속된 범위 내에 있으면 안 됩니다. 자세한 내용은 [부정 유니코드 범주 또는 유니코드 블록](#negative-unicode-category-or-unicode-block-p)을 참조하세요.

* 단어 문자입니다. 입력 문자열의 문자는 단어에 있는 문자에 적합한 유니코드 범주에 속할 수 있습니다. 자세한 내용은 [단어 문자](#word-character-w)를 참조하세요.

* 비단어 문자입니다. 입력 문자열의 문자는 단어 문자가 아닌 유니코드 범주에 속할 수 있습니다. 자세한 내용은 [단어가 아닌 문자](#non-word-character-w)를 참조하세요.

* 공백 문자. 입력 문자열의 문자는 유니코드 구분 문자뿐만 아니라 많은 제어 문자 중 하나가 될 수 있습니다. 자세한 내용은 [공백 문자](#white-space-character-s)를 참조하세요.

* 공백이 아닌 문자. 입력 문자열의 문자는 공백 문자가 아닌 문자를 사용할 수 있습니다. 자세한 내용은 [공백이 아닌 문자](#non-white-space-character-s)를 참조하세요.

* 10진수입니다. 입력 문자열의 문자는 유니코드 10진 자릿수로 분류된 모든 문자가 될 수 있습니다. 자세한 내용은 [10진수 숫자 문자](#decimal-digit-character-d)를 참조하세요.

* 10진수가 아닙니다. 입력 문자열의 문자는 유니코드 10진수 이외의 문자는 모두 사용할 수 있습니다. 자세한 내용은 [숫자가 아닌 문자](#non-digit-character-d)를 참조하세요.


.NET에서는 한 문자 클래스에서 다른 문자 클래스를 제외한 결과로 문자 집합을 정의하는 데 사용할 수 있는 문자 클래스 빼기 식을 지원합니다. 자세한 내용은 [문자 클래스 빼기](#character-class-subtraction)를 참조하세요.

## <a name="positive-character-group-"></a>긍정 문자 그룹: [ ]

긍정 문자 그룹은 일치 항목으로 간주되려면 입력 문자열에 나타날 수 있는 문자 목록을 지정합니다. 이 문자 목록은 개별적으로, 범위로 또는 둘 다 지정할 수 있습니다. 

개별 문자 목록을 지정하는 구문은 다음과 같습니다. 

[*character*_*group*]

여기서 *character_group*은 일치가 성공하기 위해 입력 문자열에 나타날 수 있는 개별 문자 목록입니다. *character*_*group*은 하나 이상의 리터럴 문자, [이스케이프 문자](escapes.md) 또는 문자 클래스로 이루어진 조합으로 구성될 수 있습니다. 

문자의 범위를 지정하는 구문은 다음과 같습니다.

```
[firstCharacter-lastCharacter]
```

여기서 *firstCharacter*는 범위를 시작하는 문자이고 *lastCharacter*는 범위를 끝내는 문자입니다. 문자 범위는 일련의 문자로서, 문자 범위를 정의하려면 연속된 문자 중 첫 번째 문자와 마지막 문자를 하이픈(-)으로 연결하여 지정합니다. 두 문자의 유니코드 코드 포인트가 연속되면 이 두 문자는 연속된 문자입니다.

긍정 문자 클래스가 포함된 몇 가지 일반적인 정규식 패턴은 다음과 같습니다.

무늬 | 설명
------- | ----------- 
`[aeiou]` | 모든 모음을 찾습니다.
`[\p{P}\d]` | 모든 문장 부호 및 10진수 문자를 찾습니다.
`[\s\p{P}]` | 모든 공백 및 문장 부호를 찾습니다.
 
다음 예제에서는 입력 문자열이 문자 "grey" 또는 "gray"에 이어 일치할 다른 일치 단어를 포함하도록 단어 "a"와 "e"가 포함된 긍정 문자 그룹을 정의합니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"gr[ae]y\s\S+?[\s\p{P}]";
      string input = "The gray wolf jumped over the grey wall.";
      MatchCollection matches = Regex.Matches(input, pattern);
      foreach (Match match in matches)
         Console.WriteLine(match.Value);
   }
}
// The example displays the following output:
//       gray wolf
//       grey wall.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "gr[ae]y\s\S+?[\s\p{P}]"
      Dim input As String = "The gray wolf jumped over the grey wall."
      Dim matches As MatchCollection = Regex.Matches(input, pattern)
      For Each match As Match In matches
         Console.WriteLine(match.Value)
      Next
   End Sub
End Module
' The example displays the following output:
'       gray wolf
'       grey wall.
```

`gr[ae]y\s\S+?[\s|\p{P}]` 정규식은 다음과 같이 정의됩니다.

무늬 | 설명
------- | ----------- 
`gr` | 리터럴 문자 "gr"을 찾습니다.
`[ae]` | "a" 또는 "e"를 찾습니다.
`y\s` | 리터럴 문자 y 다음에 공백 문자를 찾습니다.
`\S+?` | 하나 이상의 공백이 아닌 문자이지만 가능한 적은 문자를 찾습니다.
`[\s\p{P}]` | 공백 문자 또는 문장 부호를 찾습니다.
 
다음 예제에서는 모든 대문자로 시작하는 단어를 찾습니다. A-Z의 대문자로 범위를 나타내기 위해 `[A-Z]` 하위 식을 사용합니다. 

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"\b[A-Z]\w*\b";
      string input = "A city Albany Zulu maritime Marseilles";
      foreach (Match match in Regex.Matches(input, pattern))
         Console.WriteLine(match.Value);
   }
}
// The example displays the following output:
//       A
//       Albany
//       Zulu
//       Marseilles
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "\b[A-Z]\w*\b"
      Dim input As String = "A city Albany Zulu maritime Marseilles"
      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine(match.Value)
      Next
   End Sub
End Module
```

`\b[A-Z]\w*\b` 정규식은 다음 테이블과 같이 정의됩니다.

무늬 | 설명
------- | ----------- 
`\b` | 단어 경계를 시작합니다.
`[A-Z]` | A-Z의 대문자를 모두 찾습니다.
`\w*` | 0개 이상의 단어 문자를 찾습니다.
`\b` | 단어 경계를 찾습니다.

## <a name="negative-character-group-"></a>부정 문자 그룹: [^]

부정 문자 그룹은 일치 항목으로 간주되려면 입력 문자열에 나타나서는 안 되는 문자 목록을 지정합니다. 문자 목록은 개별적으로, 범위로 또는 둘 다 지정할 수 있습니다. 

개별 문자 목록을 지정하는 구문은 다음과 같습니다.

[^*character*_*group*]

여기서 *character_group*은 일치가 성공하기 위해 입력 문자열에 나타날 수 없는 개별 문자 목록입니다. *character*_*group*은 하나 이상의 리터럴 문자, [이스케이프 문자](escapes.md) 또는 문자 클래스로 이루어진 조합으로 구성될 수 있습니다. 

문자의 범위를 지정하는 구문은 다음과 같습니다.

[^*firstCharacter-lastCharacter*]

여기서 *firstCharacter*는 범위를 시작하는 문자이고 *lastCharacter*는 범위를 끝내는 문자입니다. 문자 범위는 일련의 문자로서, 문자 범위를 정의하려면 연속된 문자 중 첫 번째 문자와 마지막 문자를 하이픈(-)으로 연결하여 지정합니다. 두 문자의 유니코드 코드 포인트가 연속되면 이 두 문자는 연속된 문자입니다.

두 개 이상의 문자 범위를 연결할 수도 있습니다. 예를 들어, "0"부터 "9"까지의 10진수 범위, "a"부터 "f"까지의 소문자 범위 및 "A"부터 "F"까지의 대문자 범위를 지정하려면 `[0-9a-fA-F]`를 사용합니다.

부정 문자 그룹에서 맨 앞의 캐럿 문자(^)는 필수 문자로서, 해당 문자 그룹이 긍정 문자 그룹이 아니라 부정 문자 그룹임을 나타냅니다.

> [!IMPORTANT]
> 큰 정규식 패턴에서 부정 문자 그룹은 너비가 0인 어설션이 아닙니다. 즉, 부정 문자 그룹을 평가한 후 정규식 엔진은 입력 문자열에서 한 문자를 이동합니다.

부정 문자 그룹이 포함된 몇 가지 일반적인 정규식 패턴은 다음과 같습니다.

무늬 | 설명
------- | ----------- 
`[^aeiou]` | 모음을 제외한 모든 문자를 찾습니다.
`[^\p{P}\d]` | 문장 부호 및 10진수 문자를 제외한 모든 문자를 찾습니다.
 
다음 예제에서는 "th" 문자로 시작하고 이어서 "o"가 나오지 않는 단어를 찾습니다. 

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"\bth[^o]\w+\b";
      string input = "thought thing though them through thus thorough this";
      foreach (Match match in Regex.Matches(input, pattern))
         Console.WriteLine(match.Value);
   }
}
// The example displays the following output:
//       thing
//       them
//       through
//       thus
//       this
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "\bth[^o]\w+\b"
      Dim input As String = "thought thing though them through thus " + _
                            "thorough this"
      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine(match.Value)
      Next
   End Sub
End Module
' The example displays the following output:
'       thing
'       them
'       through
'       thus
'       this
```

`\bth[^o]\w+\b` 정규식은 다음 테이블과 같이 정의됩니다.

무늬 | 설명
------- | ----------- 
`\b` | 단어 경계를 시작합니다.
`th` | 리터럴 문자 "th"를 찾습니다.
`[^o]` | "o"가 아닌 모든 문자를 찾습니다.
`\w+` | 하나 이상의 단어 문자를 찾습니다.
`\b` | 단어 경계를 종료합니다.

## <a name="any-character-"></a>임의의 문자: .

마침표 문자(.)는 **\n**(줄 바꿈 문자, **\u000A**)을 제외하고 다음 두 한정자가 있는 모든 문자를 찾습니다.

* 정규식 패턴이 [RegexOptions.Singleline](xref:System.Text.RegularExpressions.RegexOptions.Singleline) 옵션에 의해 수정되거나 문자 클래스를 포함하는 패턴의 일부가 **s** 옵션에 의해 수정된 경우 . 는 모든 문자를 찾습니다. 자세한 내용은 [정규식 옵션](options.md)을 참조하세요.

  다음 예제에서는 . 문자 클래스의 동작이 기본적인 경우와 [RegexOptions.Singleline](xref:System.Text.RegularExpressions.RegexOptions.Singleline) 옵션을 사용하는 경우에 어떻게 다른지를 보여 줍니다. 정규식 `^.+`는 문자열의 시작 부분에서 시작하여 모든 문자를 찾습니다. 기본적으로 일치는 첫째 줄 끝에서 끝납니다. 정규식 패턴은 캐리지 리턴 문자, **\r** 또는 **\u000D**와 일치하지만 **\n**과는 일치하지 않습니다. [RegexOptions.Singleline](xref:System.Text.RegularExpressions.RegexOptions.Singleline) 옵션은 한 줄로 전체 입력 문자열을 해석하기 때문에 **\n**을 포함하여 입력 문자열의 모든 문자를 일치시킵니다.

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

  > [!NOTE]
  > **\n**을 제외한 모든 문자를 찾기 때문에 . 문자 클래스는 **\r**(캐리지 리턴 문자, **\u000D**)도 찾습니다.
 
* 긍정 또는 부정 문자 그룹의 마침표는 문자 클래스가 아니라 리터럴 마침표 문자로 처리됩니다. 자세한 내용은 이 항목 앞부분의 [긍정 문자 그룹](#positive-character-group--) 또는 [부정 문자 그룹](#negative-character-group-)을 참조하세요. 다음 예제에서는 문자 클래스와 긍정 문자 그룹의 멤버로서의 마침표 문자(**.**)를 포함하는 정규식을 정의하는 그림을 제공합니다. `\b.*[.?!;:](\s|\z)` 정규식은 단어 경계에서 시작하여 마침표를 포함하여 네 가지 문장 부호 중 하나와 만날 때까지 임의의 문자를 찾은 다음 공백 문자 또는 문자열 끝을 찾습니다.

  ```csharp
  using System;
  using System.Text.RegularExpressions;

  public class Example
  {
     public static void Main()
     {
        string pattern = @"\b.*[.?!;:](\s|\z)";
        string input = "this. what: is? go, thing.";
        foreach (Match match in Regex.Matches(input, pattern))
           Console.WriteLine(match.Value);
     }
  }
  // The example displays the following output:
  //       this. what: is? go, thing.
  ```

  ```vb
  Imports System.Text.RegularExpressions

  Module Example
     Public Sub Main()
        Dim pattern As STring = "\b.*[.?!;:](\s|\z)"
        Dim input As String = "this. what: is? go, thing."
        For Each match As Match In Regex.Matches(input, pattern)
           Console.WriteLine(match.Value)
        Next   
     End Sub
  End Module
  ' The example displays the following output:
  '       this. what: is? go, thing.
  ```

  > [!NOTE]
  > 모든 문자와 일치하기 때문에 . 언어 요소는 정규식 패턴이 임의의 문자를 여러 번 찾으려 하는 경우 최소 일치 수량자와 함께 사용되는 경우가 많습니다. 자세한 내용은 [정규식의 수량자](quantifiers.md)를 참조하세요. 
 
## <a name="unicode-category-or-unicode-block-p"></a>유니코드 범주 또는 유니코드 블록: \p{}

유니코드 표준에서는 각 문자를 일반 범주에 할당합니다. 예를 들어 특정 문자는 대문자(**Lu** 범주로 표현), 10진수(**Nd** 범주), 수학 기호(**Sm** 범주) 또는 단락 구분 기호(**Zl** 범주)가 될 수 있습니다. 유니코드 표준의 특정 문자 집합이 특정 범위 또는 연속된 코드 포인트의 블록도 차지합니다. 예를 들어 기본 라틴 문자 집합은 **\u0000**부터 **\u007F**까지이고, 아랍어 문자 집합은 **\u0600**부터 **\u06FF**까지입니다. 

정규식 만들기

**\p{**_name_**}**

유니코드 일반 범주 또는 명명된 블록에 속하는 모든 문자를 찾습니다. 여기서 name은 범주 약어 또는 명명된 블록 이름입니다. 범주 약어 목록은 이 항목의 뒷부분에 있는 [지원되는 유니코드 일반 범주](#supported-unicode-general-categories) 섹션을 참조하세요. 명명된 블록 목록은 이 항목의 뒷부분에 있는 [지원되는 명명된 블록](#supported-named-blocks) 섹션을 참조하세요. 

다음 예제에서는 **\p{**_name_**}** 구문을 사용하여 유니코드 일반 범주(이 경우 **Pd** 또는 문장 부호, 대시 범주) 및 명명된 블록(명명된 블록 **IsGreek** 및 **IsBasicLatin**)을 모두 찾습니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"\b(\p{IsGreek}+(\s)?)+\p{Pd}\s(\p{IsBasicLatin}+(\s)?)+";
      string input = "?ata ?a??a??? - The Gospel of Matthew";

      Console.WriteLine(Regex.IsMatch(input, pattern));        // Displays True.
   }
}
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "\b(\p{IsGreek}+(\s)?)+\p{Pd}\s(\p{IsBasicLatin}+(\s)?)+"
      Dim input As String = "Κατα Μαθθαίον - The Gospel of Matthew"

      Console.WriteLine(Regex.IsMatch(input, pattern))         ' Displays True.
   End Sub
End Module
```

`\b(\p{IsGreek}+(\s)?)+\p{Pd}\s(\p{IsBasicLatin}+(\s)?)+` 정규식은 다음 테이블과 같이 정의됩니다.

무늬 | 설명
------- | ----------- 
`\b` | 단어 경계를 시작합니다.
`\p{IsGreek}+` | 둘 이상의 그리스어 문자를 찾습니다.
`(\s)?` | 0번 이상 나오는 공백 문자를 찾습니다.
`(\p{IsGreek}+(\s)?)+` | 하나 이상의 그리스 문자 다음에 0개 또는 1개의 공백 문자가 한 번 이상 나타나는 패턴을 찾습니다. 
`\p{Pd}` | 문장 부호, 대시 문자를 찾습니다.
`\s` | 공백 문자를 찾습니다.
`\p{IsBasicLatin}+` | 하나 이상의 기본 라틴 문자를 찾습니다.
`(\s)?` | 0번 이상 나오는 공백 문자를 찾습니다.
`(\p{IsBasicLatin}+(\s)?)+` | 하나 이상의 기본 라틴 문자 다음에 0개 또는 1개의 공백 문자가 한 번 이상 나타나는 패턴을 찾습니다.

## <a name="negative-unicode-category-or-unicode-block-p"></a>부정 유니코드 범주 또는 유니코드 블록: \P{}

유니코드 표준에서는 각 문자를 일반 범주에 할당합니다. 예를 들어 특정 문자는 대문자(**Lu** 범주로 표현), 10진수(**Nd** 범주), 수학 기호(**Sm** 범주) 또는 단락 구분 기호(**Zl** 범주)가 될 수 있습니다. 유니코드 표준의 특정 문자 집합이 특정 범위 또는 연속된 코드 포인트의 블록도 차지합니다. 예를 들어 기본 라틴 문자 집합은 **\u0000**부터 **\u007F**까지이고, 아랍어 문자 집합은 **\u0600**부터 **\u06FF**까지입니다. 

정규식 만들기

**\P{**_name_**}**

유니코드 일반 범주 또는 명명된 블록에 속하는 모든 문자를 찾습니다. 여기서 name은 범주 약어 또는 명명된 블록 이름입니다. 범주 약어 목록은 이 항목의 뒷부분에 있는 [지원되는 유니코드 일반 범주](#supported-unicode-general-categories) 섹션을 참조하세요. 명명된 블록 목록은 이 항목의 뒷부분에 있는 [지원되는 명명된 블록](#supported-named-blocks) 섹션을 참조하세요.

다음 예제에서는 **\P{**_name_**}** 구문을 사용하여 숫자 문자열에서 모든 통화 기호(이 경우 **Sc** 또는 기호, 통화 범주)를 제거합니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"(\P{Sc})+";

      string[] values = { "$164,091.78", "£1,073,142.68", "73¢", "€120" };
      foreach (string value in values)
         Console.WriteLine(Regex.Match(value, pattern).Value);
   }
}
// The example displays the following output:
//       164,091.78
//       1,073,142.68
//       73
//       120
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "(\P{Sc})+"

      Dim values() As String = { "$164,091.78", "£1,073,142.68", "73¢", "€120"}
      For Each value As String In values
         Console.WriteLine(Regex.Match(value, pattern).Value)
      Next
   End Sub
End Module
' The example displays the following output:
'       164,091.78
'       1,073,142.68
'       73
'       120
```

정규식 패턴 `(\P{Sc})+`는 통화 기호가 아닌 하나 이상의 문자를 찾습니다. 결과 문자열에서 모든 통화 기호를 효과적으로 제거합니다.

## <a name="word-character-w"></a>단어 문자: \w

**\w**는 단어 문자를 찾습니다. 단어 문자는 다음 표에 나열된 유니코드 범주의 멤버입니다. 

범주 | 설명
-------- | ----------- 
Ll | 문자, 소문자
Lu | 문자, 대문자
Lt | 문자, 제목 스타일
Lo | 문자, 기타
Lm | 문자, 한정자
Mn | 표시, 공백 없음
Nd | 숫자, 10진수
Pc | 문장 부호, 연결자. 이 범주는 가장 일반적으로 사용되는 LOWLINE 문자 (_), U + 005F인 10개의 문자를 포함합니다.
 
ECMAScript와 호환되는 동작을 지정한 경우 **\w**는 `[a-zA-Z_0-9]`와 같습니다. ECMAScript 정규식에 대한 자세한 내용은 [정규식 옵션](options.md)에서 [ECMAScript 일치 동작](options.md#ecmascript-matching-behavior) 섹션을 참조하세요. 

> [!NOTE]
> 모든 단어 문자를 찾기 때문에 \w 언어 요소는 정규식 패턴이 특정 단어 문자가 따라오는 임의의 단어 문자를 여러 번 찾으려 하는 경우 최소 일치 수량자와 함께 사용되는 경우가 많습니다. 자세한 내용은 [정규식의 수량자](quantifiers.md)를 참조하세요.

다음 예제에서는 **\w** 언어 요소를 사용하여 단어의 중복 문자를 찾습니다. 예제는 다음과 같이 해석될 수 있는 정규식 패턴 **(\w)\1**을 정의합니다.

요소 | 설명
------- | -----------
(\w) | 단어 문자를 찾습니다. 이 그룹은 첫 번째 캡처링 그룹입니다.
\1 | 첫 번째 캡처의 값을 찾습니다. 
 
```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"(\w)\1";
      string[] words = { "trellis", "seer", "latter", "summer", 
                         "hoarse", "lesser", "aardvark", "stunned" };
      foreach (string word in words)
      {
         Match match = Regex.Match(word, pattern);
         if (match.Success)
            Console.WriteLine("'{0}' found in '{1}' at position {2}.", 
                              match.Value, word, match.Index);
         else
            Console.WriteLine("No double characters in '{0}'.", word);
      }                                                  
   }
}
// The example displays the following output:
//       'll' found in 'trellis' at position 3.
//       'ee' found in 'seer' at position 1.
//       'tt' found in 'latter' at position 2.
//       'mm' found in 'summer' at position 2.
//       No double characters in 'hoarse'.
//       'ss' found in 'lesser' at position 2.
//       'aa' found in 'aardvark' at position 0.
//       'nn' found in 'stunned' at position 3.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "(\w)\1"
      Dim words() As String = { "trellis", "seer", "latter", "summer", _
                                "hoarse", "lesser", "aardvark", "stunned" }
      For Each word As String In words
         Dim match As Match = Regex.Match(word, pattern)
         If match.Success Then
            Console.WriteLine("'{0}' found in '{1}' at position {2}.", _
                              match.Value, word, match.Index)
         Else
            Console.WriteLine("No double characters in '{0}'.", word)
         End If
      Next                                                  
   End Sub
End Module
' The example displays the following output:
'       'll' found in 'trellis' at position 3.
'       'ee' found in 'seer' at position 1.
'       'tt' found in 'latter' at position 2.
'       'mm' found in 'summer' at position 2.
'       No double characters in 'hoarse'.
'       'ss' found in 'lesser' at position 2.
'       'aa' found in 'aardvark' at position 0.
'       'nn' found in 'stunned' at position 3.
```

## <a name="nonword-character-w"></a>단어가 아닌 문자: \W

**\W**는 단어가 아닌 문자를 찾습니다. **\W** 언어 요소는 다음 문자 클래스와 같습니다.

```
[^\p{Ll}\p{Lu}\p{Lt}\p{Lo}\p{Nd}\p{Pc}\p{Lm}]
```

즉, 다음 표에 나열된 유니코드 범주의 문자를 제외한 모든 문자를 찾습니다.

범주 | 설명
-------- | -----------
Ll | 문자, 소문자
Lu | 문자, 대문자
Lt | 문자, 제목 스타일
Lo | 문자, 기타
Lm | 문자, 한정자
Mn | 표시, 공백 없음
Nd | 숫자, 10진수
Pc | 문장 부호, 연결자. 이 범주는 가장 일반적으로 사용되는 LOWLINE 문자 (_), U + 005F인 10개의 문자를 포함합니다.
 
ECMAScript와 호환되는 동작을 지정한 경우 **\W**는 `[^a-zA-Z_0-9]`와 같습니다. ECMAScript 정규식에 대한 자세한 내용은 [정규식 옵션](options.md)에서 [ECMAScript 일치 동작](options.md#ecmascript-matching-behavior) 섹션을 참조하세요. 

> [!NOTE]
> 모든 단어 문자를 찾기 때문에 \w 언어 요소는 정규식 패턴이 특정 단어 문자가 따라오는 임의의 단어 문자를 여러 번 찾으려 하는 경우 최소 일치 수량자와 함께 사용되는 경우가 많습니다. 자세한 내용은 [정규식의 수량자](quantifiers.md)를 참조하세요. 

다음 예제에서는 **\W** 문자 클래스를 보여 줍니다. 공백 또는 문장 부호와 같은 한두 개의 비단어 문자가 따라오는 단어와 일치하는 정규식 패턴 `\b(\w+)(\W){1,2}`를 정의합니다. 정규식은 다음 표와 같이 해석됩니다.

요소 | 설명
------- | ----------- 
\b | 단어 경계에서 일치 항목 찾기를 시작합니다.
(\w+) | 하나 이상의 단어 문자를 찾습니다. 이 그룹은 첫 번째 캡처링 그룹입니다.
(\W){1,2} | 단어가 아닌 문자를 한 개 또는 두 개 찾습니다. 이 그룹은 두 번째 캡처링 그룹입니다.
 
```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"\b(\w+)(\W){1,2}";
      string input = "The old, grey mare slowly walked across the narrow, green pasture.";
      foreach (Match match in Regex.Matches(input, pattern))
      {
         Console.WriteLine(match.Value);
         Console.Write("   Non-word character(s):");
         CaptureCollection captures = match.Groups[2].Captures;
         for (int ctr = 0; ctr < captures.Count; ctr++)
             Console.Write(@"'{0}' (\u{1}){2}", captures[ctr].Value, 
                           Convert.ToUInt16(captures[ctr].Value[0]).ToString("X4"), 
                           ctr < captures.Count - 1 ? ", " : "");
         Console.WriteLine();
      }   
   }
}
// The example displays the following output:
//       The
//          Non-word character(s):' ' (\u0020)
//       old,
//          Non-word character(s):',' (\u002C), ' ' (\u0020)
//       grey
//          Non-word character(s):' ' (\u0020)
//       mare
//          Non-word character(s):' ' (\u0020)
//       slowly
//          Non-word character(s):' ' (\u0020)
//       walked
//          Non-word character(s):' ' (\u0020)
//       across
//          Non-word character(s):' ' (\u0020)
//       the
//          Non-word character(s):' ' (\u0020)
//       narrow,
//          Non-word character(s):',' (\u002C), ' ' (\u0020)
//       green
//          Non-word character(s):' ' (\u0020)
//       pasture.
//          Non-word character(s):'.' (\u002E)
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "\b(\w+)(\W){1,2}"
      Dim input As String = "The old, grey mare slowly walked across the narrow, green pasture."
      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine(match.Value)
         Console.Write("   Non-word character(s):")
         Dim captures As CaptureCollection = match.Groups(2).Captures
         For ctr As Integer = 0 To captures.Count - 1
             Console.Write("'{0}' (\u{1}){2}", captures(ctr).Value, _
                           Convert.ToUInt16(captures(ctr).Value.Chars(0)).ToString("X4"), _
                           If(ctr < captures.Count - 1, ", ", ""))
         Next
         Console.WriteLine()
      Next
   End Sub
End Module
' The example displays the following output:
'       The
'          Non-word character(s):' ' (\u0020)
'       old,
'          Non-word character(s):',' (\u002C), ' ' (\u0020)
'       grey
'          Non-word character(s):' ' (\u0020)
'       mare
'          Non-word character(s):' ' (\u0020)
'       slowly
'          Non-word character(s):' ' (\u0020)
'       walked
'          Non-word character(s):' ' (\u0020)
'       across
'          Non-word character(s):' ' (\u0020)
'       the
'          Non-word character(s):' ' (\u0020)
'       narrow,
'          Non-word character(s):',' (\u002C), ' ' (\u0020)
'       green
'          Non-word character(s):' ' (\u0020)
'       pasture.
'          Non-word character(s):'.' (\u002E)
```

두 번째 캡처링 그룹의 `Group` 개체가 단일 캡처된 비단어 문자만 포함하기 때문에 예제에서는 `CaptureCollection` 속성에 의해 반환되는 `Group.Captures` 개체에서 모든 캡처된 비단어 문자를 검색합니다.

## <a name="whitespace-character-s"></a>공백 문자: \s

**\s**는 공백 문자를 찾습니다. 다음 표에 나열된 이스케이프 시퀀스 및 유니코드 범주와 동일합니다. 

범주 | 설명
-------- | ----------- 
**\f** | 용지 공급 문자, \u000C.
**\n** | 줄 바꿈 문자, \u000A.
**\r** | 캐리지 리턴 문자 \u000D입니다.
**\t** | 탭 문자, \u0009.
**\v** | 세로 탭 문자, \u000B.
**\x85** | 줄임표 또는 NEXT LINE (NEL) 문자 (…), \u0085.
**\p{Z}** | 임의의 구분 기호 문자와 일치합니다.
 

ECMAScript와 호환되는 동작을 지정한 경우 **\s**는 `[ \f\n\r\t\v]`와 같습니다.  ECMAScript 정규식에 대한 자세한 내용은 [정규식 옵션](options.md)에서 [ECMAScript 일치 동작](options.md#ecmascript-matching-behavior) 섹션을 참조하세요. 

다음 예제에서는 \s 문자 클래스를 보여 줍니다. "s" 또는 "es"로 끝나고 그 뒤에 공백 문자나 입력 문자열의 끝이 있는 단어와 일치하는 정규식 패턴 `\b\w+(e)?s(\s|$)`를 정의합니다. 정규식은 다음 표와 같이 해석됩니다.

요소 | 설명
------- | -----------
\b | 단어 경계에서 일치 항목 찾기를 시작합니다.
\w+ | 하나 이상의 단어 문자를 찾습니다. 
(e)? | "e"를 0개 또는 한 개 찾습니다.
s | "s"를 찾습니다.
(\s&#124;$) | 공백 문자 또는 입력 문자열의 끝을 찾습니다.
 
```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"\b\w+(e)?s(\s|$)";
      string input = "matches stores stops leave leaves";
      foreach (Match match in Regex.Matches(input, pattern))
         Console.WriteLine(match.Value);
   }
}
// The example displays the following output:
//       matches
//       stores
//       stops
//       leaves
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "\b\w+(e)?s(\s|$)"
      Dim input As String = "matches stores stops leave leaves"
      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine(match.Value)      
      Next
   End Sub
End Module
' The example displays the following output:
'       matches
'       stores
'       stops
'       leaves
```

## <a name="nonwhitespace-character-s"></a>공백이 아닌 문자: \S

**\S**는 공백 문자가 아닌 문자를 찾습니다. `[^\f\n\r\t\v\x85\p{Z}]` 정규식 패턴과 같거나, 공백 문자를 찾는 **\s**와 같은 정규식 패턴의 반대입니다. 자세한 내용은 이전 섹션인 "공백 문자: \s"를 참조하세요.

ECMAScript와 호환되는 동작을 지정한 경우 **\S**는 `[^ \f\n\r\t\v]`와 같습니다. ECMAScript 정규식에 대한 자세한 내용은 [정규식 옵션](options.md)에서 [ECMAScript 일치 동작](options.md#ecmascript-matching-behavior) 섹션을 참조하세요.

다음 예제에서는 **\S** 언어 요소를 보여 줍니다. 정규식 패턴 \b(\S+)\s?는 공백 문자로 구분된 문자열을 찾습니다. 일치 항목의 GroupCollection 개체에서 두 번째 요소는 일치하는 문자열을 포함합니다. 정규식은 다음 표에 나와 있는 것처럼 해석할 수 있습니다.

요소 | 설명
------- | ----------- 
`\b` | 단어 경계에서 일치 항목 찾기를 시작합니다.
`(\S+)` | 공백이 아닌 문자를 하나 이상 찾습니다. 이 그룹은 첫 번째 캡처링 그룹입니다.
`\s?` | 0번 이상 나오는 공백 문자를 찾습니다. 
 
```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"\b(\S+)\s?";
      string input = "This is the first sentence of the first paragraph. " + 
                            "This is the second sentence.\n" + 
                            "This is the only sentence of the second paragraph.";
      foreach (Match match in Regex.Matches(input, pattern))
         Console.WriteLine(match.Groups[1]);
   }
}
// The example displays the following output:
//    This
//    is
//    the
//    first
//    sentence
//    of
//    the
//    first
//    paragraph.
//    This
//    is
//    the
//    second
//    sentence.
//    This
//    is
//    the
//    only
//    sentence
//    of
//    the
//    second
//    paragraph.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "\b(\S+)\s?"
      Dim input As String = "This is the first sentence of the first paragraph. " + _
                            "This is the second sentence." + vbCrLf + _
                            "This is the only sentence of the second paragraph."
      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine(match.Groups(1))
      Next
   End Sub
End Module
' The example displays the following output:
'    This
'    is
'    the
'    first
'    sentence
'    of
'    the
'    first
'    paragraph.
'    This
'    is
'    the
'    second
'    sentence.
'    This
'    is
'    the
'    only
'    sentence
'    of
'    the
'    second
'    paragraph.
```

## <a name="decimal-digit-character-d"></a>10진수 문자: \d

**\d**는 10진수를 찾습니다. 많은 다른 문자 집합의 10진수뿐만 아니라 표준 10진수 0-9를 포함하는 `\\p{Nd}` 정규식 패턴과 동일합니다.

ECMAScript와 호환되는 동작을 지정한 경우 **\d**는 `[0-9]`와 같습니다. ECMAScript 정규식에 대한 자세한 내용은 [정규식 옵션](options.md)에서 [ECMAScript 일치 동작](options.md#ecmascript-matching-behavior) 섹션을 참조하세요.

다음 예제에서는 **\d** 언어 요소를 보여 줍니다. 입력 문자열이 미국 및 캐나다의 올바른 전화 번호를 나타내는지 여부를 테스트합니다. 정규식 패턴 `^(\(?\d{3}\)?[\s-])?\d{3}-\d{4}$`는 다음 테이블과 같이 정의됩니다.

요소 | 설명
------- | ----------- 
`^` | 입력 문자열의 시작 부분에서 일치 항목 찾기를 시작합니다.
`\(?` | 0개 또는 한 개의 리터럴 "(" 문자를 찾습니다. 
`\d{3}` | 세 개의 10진수를 찾습니다. 
`\)?` | 0개 또는 한 개의 리터럴 ")" 문자를 찾습니다.
`[\s-]` | 하이픈 또는 공백 문자를 찾습니다.
`(\(?\d{3}\)?[\s-])?` | 옵션 여는 괄호에 이어 세 개의 10진수, 옵션 닫는 괄호 및 공백 문자나 하이픈 0개 또는 1개를 찾습니다. 이 그룹은 첫 번째 캡처링 그룹입니다.
`\d{3}-\d{4}` | 세 개의 10진수, 하이픈, 네 개 이상의 10진수를 찾습니다.
`$` | 입력 문자열의 끝 부분을 찾습니다.
 
```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"^(\(?\d{3}\)?[\s-])?\d{3}-\d{4}$";
      string[] inputs = { "111 111-1111", "222-2222", "222 333-444", 
                          "(212) 111-1111", "111-AB1-1111", 
                          "212-111-1111", "01 999-9999" };

      foreach (string input in inputs)
      {
         if (Regex.IsMatch(input, pattern)) 
            Console.WriteLine(input + ": matched");
         else
            Console.WriteLine(input + ": match failed");
      }
   }
}
// The example displays the following output:
//       111 111-1111: matched
//       222-2222: matched
//       222 333-444: match failed
//       (212) 111-1111: matched
//       111-AB1-1111: match failed
//       212-111-1111: matched
//       01 999-9999: match failed
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "^(\(?\d{3}\)?[\s-])?\d{3}-\d{4}$"
      Dim inputs() As String = { "111 111-1111", "222-2222", "222 333-444", _
                                 "(212) 111-1111", "111-AB1-1111", _
                                 "212-111-1111", "01 999-9999" }

      For Each input As String In inputs
         If Regex.IsMatch(input, pattern) Then 
            Console.WriteLine(input + ": matched")
         Else
            Console.WriteLine(input + ": match failed")
         End If   
      Next
   End Sub
End Module
' The example displays the following output:
'       111 111-1111: matched
'       222-2222: matched
'       222 333-444: match failed
'       (212) 111-1111: matched
'       111-AB1-1111: match failed
'       212-111-1111: matched
'       01 999-9999: match failed
```

## <a name="nondigit-character-d"></a>숫자가 아닌 문자: \D

**\D**는 숫자가 아닌 문자를 찾습니다. `\P{Nd}` 정규식 패턴과 동일합니다.

ECMAScript와 호환되는 동작을 지정한 경우 **\D**는 `[^0-9]`와 같습니다. ECMAScript 정규식에 대한 자세한 내용은 [정규식 옵션](options.md)에서 [ECMAScript 일치 동작](options.md#ecmascript-matching-behavior) 섹션을 참조하세요.

다음 예제에서는 **\D** 언어 요소를 보여 줍니다. 부품 번호 같은 문자열이 10진수 문자 및 10진수가 아닌 문자의 적절한 조합으로 구성되어 있는지 여부를 테스트합니다. 정규식 패턴 `^\D\d{1,5}\D*$`는 다음 테이블과 같이 정의됩니다.

요소 | 설명
------- | ----------- 
`^` | 입력 문자열의 시작 부분에서 일치 항목 찾기를 시작합니다.
`\D` | 숫자가 아닌 문자를 찾습니다. 
`\d{1,5}` | 1~5의 10진수를 찾습니다. 
`\D*` | 0개 또는 하나 이상의 10진수가 아닌 문자를 찾습니다. 
`$` | 입력 문자열의 끝 부분을 찾습니다.
 
```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"^\D\d{1,5}\D*$"; 
      string[] inputs = { "A1039C", "AA0001", "C18A", "Y938518" }; 

      foreach (string input in inputs)
      {
         if (Regex.IsMatch(input, pattern))
            Console.WriteLine(input + ": matched");
         else
            Console.WriteLine(input + ": match failed");
      }
   }
}
// The example displays the following output:
//       A1039C: matched
//       AA0001: match failed
//       C18A: matched
//       Y938518: match failed
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "^\D\d{1,5}\D*$" 
      Dim inputs() As String = { "A1039C", "AA0001", "C18A", "Y938518" } 

      For Each input As String In inputs
         If Regex.IsMatch(input, pattern) Then
            Console.WriteLine(input + ": matched")
         Else
            Console.WriteLine(input + ": match failed")
         End If   
      Next
   End Sub
End Module
' The example displays the following output:
```

## <a name="supported-unicode-general-categories"></a>지원되는 유니코드 일반 범주

유니코드는 다음 표에 나와 있는 일반 범주를 정의합니다. 자세한 내용은 [유니코드 문자 데이터베이스](http://www.unicode.org/reports/tr44/)의 하위 항목인 "UCD 파일 형식"과 "일반 범주 값"을 참조하세요.

범주 | 설명
-------- | -----------
**Lu** | 문자, 대문자
**Ll** | 문자, 소문자
**Lt** | 문자, 제목 스타일
**Lm** | 문자, 한정자
**Lo** | 문자, 기타
**L** | 모든 문자 여기에는 **Lu**, **Ll**, **Lt**, **Lm** 및 **Lo** 문자가 포함됩니다.
**Mn** | 표시, 공백 없음
**Mc** | 표시, 공백 조합
**Me** | 표시, 묶기
**M** | 모든 분음 기호 여기에는 **Mn**, **Mc** 및 **Me** 범주가 포함됩니다.
**Nd** | 숫자, 10진수
**Nl** | 숫자, 문자
**No** | 숫자, 기타
**N** | 모든 숫자. 여기에는 **Nd**, **Nl** 및 **No** 범주가 포함됩니다.
**Pc** | 문장 부호, 연결자
**Pd** | 문장 부호, 대시
**Ps** | 문장 부호, 열기
**Pe** | 문장 부호, 닫기
**Pi** | 문장 부호, 처음 따옴표(사용 방식에 따라 Ps 또는 Pe와 같이 동작)
**Pf** | 문장 부호, 마지막 따옴표(사용 방식에 따라 Ps 또는 Pe와 같이 동작)
**Po** | 문장 부호, 기타
**P** | 모든 구두점 문자. 여기에는 **Pc**, **Pd**, **Ps**, **Pe**, **Pi**, **Pf** 및 **Po** 범주가 포함됩니다.
**Sm** | 기호, 수학
**Sc** | 기호, 통화
**Sk** | 기호, 한정자
**So** | 기호, 기타
**S** | 모든 기호. 여기에는 **Sm**, **Sc**, **Sk** 및 **So** 범주가 포함됩니다.
**Zs** | 구분 기호, 공백
**Zl** | 구분 기호, 줄
**Zp** | 구분 기호, 단락
**Z** | 모든 구분 기호 문자. 여기에는 **Zs**, **Zl** 및 **Zp** 범주가 포함됩니다.
**Cc** | 기타, 제어
**Cf** | 기타, 서식
**Cs** | 기타, 서로게이트
**Co** | 기타, 전용 항목
**Cn** | 기타, 지정되지 않음(이 속성을 가진 문자가 없음)
**C** | 모든 제어 문자. 여기에는 **Cc**, **Cf**, **Cs**, **Co** 및 **Cn** 범주가 포함됩니다.
 
##<a name="supported-named-blocks"></a>지원되는 명명된 블록

.NET에서는 다음 표에 나와 있는 명명된 블록을 제공합니다. 지원되는 명명된 블록 집합은 유니코드 4.0 및 Perl 5.6을 기반으로 합니다.

코드 포인트 범위 | 블록 이름
---------------- | ---------- 
0000 - 007F | **IsBasicLatin**
0080 - 00FF | **IsLatin-1Supplement**
0100 - 017F | **IsLatinExtended-A**
0180 - 024F | **IsLatinExtended-B**
0250 - 02AF | **IsIPAExtensions**
02B0 - 02FF | **IsSpacingModifierLetters**
0300 - 036F | **IsCombiningDiacriticalMarks**
0370 - 03FF | **IsGreek** 또는 **IsGreekandCoptic**
0400 - 04FF | **IsCyrillic**
0500 - 052F | **IsCyrillicSupplement**
0530 - 058F | **IsArmenian**
0590 - 05FF | **IsHebrew**
0600 - 06FF | **IsArabic**
0700 - 074F | **IsSyriac**
0780 - 07BF | **IsThaana**
0900 - 097F | **IsDevanagari**
0980 - 09FF | **IsBengali**
0A00 - 0A7F | **IsGurmukhi**
0A80 - 0AFF | **IsGujarati**
0B00 - 0B7F | **IsOriya**
0B80 - 0BFF | **IsTamil**
0C00 - 0C7F | **IsTelugu**
0C80 - 0CFF | **IsKannada**
0D00 - 0D7F | **IsMalayalam**
0D80 - 0DFF | **IsSinhala**
0E00 - 0E7F | **IsThai**
0E80 - 0EFF | **IsLao**
0F00 - 0FFF | **IsTibetan**
1000 - 109F | **IsMyanmar**
10A0 - 10FF | **IsGeorgian**
1100 - 11FF | **IsHangulJamo**
1200 - 137F | **IsEthiopic**
13A0 - 13FF | **IsCherokee**
1400 - 167F | **IsUnifiedCanadianAboriginalSyllabics**
1680 - 169F | **IsOgham**
16A0 - 16FF | **IsRunic**
1700 - 171F | **IsTagalog**
1720 - 173F | **IsHanunoo**
1740 - 175F | **IsBuhid**
1760 - 177F | **IsTagbanwa**
1780 - 17FF | **IsKhmer**
1800 - 18AF | **IsMongolian**
1900 - 194F | **IsLimbu**
1950 - 197F | **IsTaiLe**
19E0 - 19FF | **IsKhmerSymbols**
1D00 - 1D7F | **IsPhoneticExtensions**
1E00 - 1EFF | **IsLatinExtendedAdditional**
1F00 - 1FFF | **IsGreekExtended**
2000 - 206F | **IsGeneralPunctuation**
2070 - 209F | **IsSuperscriptsandSubscripts**
20A0 - 20CF | **IsCurrencySymbols**
20D0 - 20FF | **IsCombiningDiacriticalMarksforSymbols** 또는 **IsCombiningMarksforSymbols**
2100 - 214F | **IsLetterlikeSymbols**
2150 - 218F | **IsNumberForms**
2190 - 21FF | **IsArrows**
2200 - 22FF | **IsMathematicalOperators**
2300 - 23FF | **IsMiscellaneousTechnical**
2400 - 243F | **IsControlPictures**
2440 - 245F | **IsOpticalCharacterRecognition**
2460 - 24FF | **IsEnclosedAlphanumerics**
2500 - 257F | **IsBoxDrawing**
2580 - 259F | **IsBlockElements**
25A0 - 25FF | **IsGeometricShapes**
2600 - 26FF | **IsMiscellaneousSymbols**
2700 - 27BF | **IsDingbats**
27C0 - 27EF | **IsMiscellaneousMathematicalSymbols-A**
27F0 - 27FF | **IsSupplementalArrows-A**
2800 - 28FF | **IsBraillePatterns**
2900 - 297F | **IsSupplementalArrows-B**
2980 - 29FF | **IsMiscellaneousMathematicalSymbols-B**
2A00 - 2AFF | **IsSupplementalMathematicalOperators**
2B00 - 2BFF | **IsMiscellaneousSymbolsandArrows**
2E80 - 2EFF | **IsCJKRadicalsSupplement**
2F00 - 2FDF | **IsKangxiRadicals**
2FF0 - 2FFF | **IsIdeographicDescriptionCharacters**
3000 - 303F | **IsCJKSymbolsandPunctuation**
3040 - 309F | **IsHiragana**
30A0 - 30FF | **IsKatakana**
3100 - 312F | **IsBopomofo**
3130 - 318F | **IsHangulCompatibilityJamo**
3190 - 319F | **IsKanbun**
31A0 - 31BF | **IsBopomofoExtended**
31F0 - 31FF | **IsKatakanaPhoneticExtensions**
3200 - 32FF | **IsEnclosedCJKLettersandMonths**
3300 - 33FF | **IsCJKCompatibility**
3400 - 4DBF | **IsCJKUnifiedIdeographsExtensionA**
4DC0 - 4DFF | **IsYijingHexagramSymbols**
4E00 - 9FFF | **IsCJKUnifiedIdeographs**
A000 - A48F | **IsYiSyllables**
A490 - A4CF | **IsYiRadicals**
AC00 - D7AF | **IsHangulSyllables**
D800 - DB7F | **IsHighSurrogates**
DB80 - DBFF | **IsHighPrivateUseSurrogates**
DC00 - DFFF | **IsLowSurrogates**
E000 - F8FF | **IsPrivateUse** 또는 **IsPrivateUseArea**
F900 - FAFF | **IsCJKCompatibilityIdeographs**
FB00 - FB4F | **IsAlphabeticPresentationForms** 
FB50 - FDFF | **IsArabicPresentationForms-A** 
FE00 - FE0F | **IsVariationSelectors** 
FE20 - FE2F | **IsCombiningHalfMarks** 
FE30 - FE4F | **IsCJKCompatibilityForms** 
FE50 - FE6F | **IsSmallFormVariants**
FE70 - FEFF | **IsArabicPresentationForms-B** 
FF00 - FFEF | **IsHalfwidthandFullwidthForms** 
FFF0 - FFFF | **IsSpecials**
 
<a name="character-class-subtraction"></a>
## <a name="character-class-subtraction-basegroup-excludedgroup"></a>문자 클래스 빼기: [base_group - [excluded_group]]

문자 클래스는 문자 집합을 정의합니다. 문자 클래스 빼기는 한 문자 클래스에서 다른 문자 클래스의 문자를 제외한 결과로 문자 집합을 생성합니다. 

문자 클래스 빼기 식의 형식은 다음과 같습니다.

__[__*base*_*group*-__[__*excluded*_*group*__]]--

대괄호(**[]**)와 하이픈(-)은 필수 요소입니다. *base_group*은 긍정 문자 그룹 또는 부정 문자 그룹입니다. *excluded_group* 구성 요소는 다른 긍정 또는 부정 문자 그룹이거나 다른 문자 클래스 빼기 식입니다. 즉, 문자 클래스 빼기 식을 중첩할 수 있습니다. 

예를 들어, "a"부터 "z"까지의 문자 범위로 구성된 기본 그룹이 있다고 가정합니다. 문자 "m"을 제외한 기본 그룹으로 구성된 문자 집합을 정의하려면 `[a-z-[m]]`을 사용합니다. 문자 "d", "j" 및 "p"를 제외한 기본 그룹으로 구성된 문자 집합을 정의하려면 `[a-z-[djp]]`를 사용합니다. "m"부터 "p"까지의 문자 범위를 제외한 기본 그룹으로 구성된 문자 집합을 정의하려면 `[a-z-[m-p]]`를 사용합니다. 

중첩된 문자 클래스 빼기 식인 `[a-z-[d-w-[m-o]]]`를 고려합니다. 가장 안쪽 문자 범위에서 바깥쪽으로 식이 계산됩니다. 먼저 'd'부터 'w'까지의 문자 범위에서 "m"부터 "o"까지의 문자 범위를 뺍니다. 그러면 "d"부터 "l"까지와 "p"부터 "w"까지의 문자로 구성된 문자 집합이 생성됩니다. 그런 다음 "a"부터 "z"까지의 문자 범위에서 이 집합을 뺍니다. 그러면 문자 집합 `[abcmnoxyz]`가 생성됩니다.

모든 문자 클래스에 문자 클래스 빼기를 사용할 수 있습니다. \u0000부터 \uFFFF까지의 문자에서 공백 문자(**\s**), 문장 부호 일반 범주의 문자(**\p{P}**), 명명된 블록 **IsGreek**의 문자(**\p{IsGreek}**) 및 유니코드 NEXT LINE 제어 문자(\x85)를 제외한 모든 유니코드 문자로 구성된 문자 집합을 정의하려면 `[\u0000-\uFFFF-[\s\p{P}\p{IsGreek}\x85]]`를 사용합니다.

문자 클래스 빼기 식에 사용할 문자 클래스를 선택할 때는 유용한 결과를 생성할 수 있도록 해야 합니다. 어떤 문자도 나타낼 수 없는 빈 문자 집합을 생성하는 식이나 원래 기본 그룹에 해당하는 식은 사용하지 않습니다. 예를 들어 **IsBasicLatin** 일반 범주에서 **IsBasicLatin** 문자 범위의 모든 문자를 빼는 `[\p{IsBasicLatin}-[\x00-\x7F]]` 식을 사용하면 빈 집합이 생성됩니다. 마찬가지로 `[a-z-[0-9]]` 식을 사용하면 원래 기본 그룹이 생성됩니다. "a"부터 "z"까지의 문자 범위를 나타내는 기본 그룹에는 제외할 그룹의 문자, 즉 "0"부터 "9"까지의 10진수 문자 범위가 없기 때문입니다. 

다음 예제에서는 입력 문자열에서 0과 홀수를 찾는 정규식 `^[0-9-[2468]]+$`를 정의합니다. 정규식은 다음 표와 같이 해석됩니다.

요소 | 설명
------- | ----------- 
`^` | 입력 문자열의 시작 부분에서 일치 항목 찾기를 시작합니다.
`[0-9-[2468]]+` | 모든 문자 0에서 9까지 2, 4, 6 및 8을 제외한 하나 이상의 항목을 일치하세요. 즉, 한 번 이상 나오는 0 또는 홀수와 일치합니다.
`$` | 입력 문자열의 끝 부분에서 검색을 종료합니다.
 
```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string[] inputs = { "123", "13579753", "3557798", "335599901" };
      string pattern = @"^[0-9-[2468]]+$";

      foreach (string input in inputs)
      {
         Match match = Regex.Match(input, pattern);
         if (match.Success) 
            Console.WriteLine(match.Value);
      }      
   }
}
// The example displays the following output:
//       13579753
//       335599901
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim inputs() As String = { "123", "13579753", "3557798", "335599901" }
      Dim pattern As String = "^[0-9-[2468]]+$"

      For Each input As String In inputs
         Dim match As Match = Regex.Match(input, pattern)
         If match.Success Then Console.WriteLine(match.Value)
      Next
   End Sub
End Module
' The example displays the following output:
'       13579753
'       335599901
```

## <a name="see-also"></a>참고 항목

[정규식 옵션](options.md)


<!--HONumber=Nov16_HO1-->


