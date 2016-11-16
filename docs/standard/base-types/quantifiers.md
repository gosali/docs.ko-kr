---
title: "정규식의 수량자"
description: "정규식의 수량자"
keywords: .NET, .NET Core
author: stevehoag
ms.author: shoag
manager: wpickett
ms.date: 07/29/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 8e5124c4-20b5-4c57-ab68-301d1d7311c4
translationtype: Human Translation
ms.sourcegitcommit: b20713600d7c3ddc31be5885733a1e8910ede8c6
ms.openlocfilehash: 016ee9a4f05fdf36982c5b369780526296b53a7d

---

# <a name="quantifiers-in-regular-expressions"></a>정규식의 수량자

수량자는 찾을 일치 항목의 입력에 있어야 하는 문자, 그룹 또는 문자 클래스의 인스턴스 수를 지정합니다. 다음 테이블에서는 .NET에서 지원하는 수량자를 보여 줍니다.

탐욕적 수량자 | 게으른 수량자 | 설명
----------------- | --------------- | ----------- 
**\*** | **\*?** | 0번 이상 일치합니다.
**+** | **+?** | 1번 이상 일치합니다.
**?** | **??** | 0번 또는 1번 일치합니다.
**{**_n_**}** | **{**_n_**}?** | 정확히 n번 일치합니다.
**{**_n_**,}** | **{**_n_**,}?** | 적어도 n번 일치합니다.
**{**_n_**,**_m_**}** | **{**_n_**,**_m_**}?** | n번에서 m번까지 일치합니다.
 
수량 *n* 및 *m*은 정수 상수입니다. 일반적으로 수량자는 탐욕적입니다. 그러면 최대한의 정규식 엔진이 특정 패턴과 일치하게 됩니다. `?` 문자를 수량자에 추가하면 게으른 수량자로 만들 수 있습니다. 그러면 최소한의 정규식 엔진이 일치하게 됩니다. 탐욕적 수량자와 게으른 수량자 간의 차이에 대한 설명은 이 항목의 뒷부분에 나오는 [탐욕적 및 게으른 수량자](#greedy-and-lazy-quantifiers) 섹션을 참조하세요.

> [!IMPORTANT]
> 예를 들어, 정규식 패턴 `(a*)*`과 같이 수량자가 중첩되면 정규식 엔진이 입력 문자열에 있는 문자 수의 지 수 함수로 수행해야 하는 비교의 수를 증가시킬 수 있습니다. 이 동작 및 해결 방법에 대한 자세한 내용은 [정규식의 역추적](backtracking.md)을 참조하세요.

## <a name="regular-expression-quantifiers"></a>정규식 수량자

다음 섹션에는 .NET의 정규식에서 지원하는 수량자를 보여 줍니다. 

> [!NOTE]
> \*, +, ?, { 및 } 문자가 정규식 패턴에 나타난 경우 [문자 클래스](classes.md)에 포함된 경우가 아니면 정규식 엔진은 수량자 또는 수량자 구조의 일부로 해석합니다. 이를 문자 클래스 외부의 리터럴 문자로 해석하려면 앞에 백슬래시를 추가하여 이스케이프해야 합니다. 예를 들어 정규식 패턴의 `\*` 문자열은 리터럴 별표("*") 문자로 해석됩니다.

### <a name="match-zero-or-more-times-"></a>0번 이상 일치: \*

\* 수량자는 이전 요소를 0번 이상 일치시킵니다. **{0,}** 수량자와 같습니다. **\***는 게으른 수량자가 **\*?**인 탐욕적 수량자입니다.

다음 예제에서는 이 정규식을 설명합니다. 입력 문자열에서 9자리 숫자 중에 5개는 패턴과 일치하고 4개(`95`, `929`, `9129` 및 `9919`)는 일치하지 않습니다.

```csharp
string pattern = @"\b91*9*\b";   
string input = "99 95 919 929 9119 9219 999 9919 91119";
foreach (Match match in Regex.Matches(input, pattern))
   Console.WriteLine("'{0}' found at position {1}.", match.Value, match.Index);

// The example displays the following output:   
//       '99' found at position 0.
//       '919' found at position 6.
//       '9119' found at position 14.
//       '999' found at position 24.
//       '91119' found at position 33.
```

```vb
Dim pattern As String = "\b91*9*\b"   
Dim input As String = "99 95 919 929 9119 9219 999 9919 91119"
For Each match As Match In Regex.Matches(input, pattern)
   Console.WriteLine("'{0}' found at position {1}.", match.Value, match.Index)
Next     
' The example displays the following output:   
'       '99' found at position 0.
'       '919' found at position 6.
'       '9119' found at position 14.
'       '999' found at position 24.
'       '91119' found at position 33.
```

이 정규식 패턴은 다음 테이블과 같이 정의됩니다.

패턴 | 설명
------- | ----------- 
`\b` | 단어 경계를 시작합니다.
`91*` | 뒤에 0개 이상의 "1" 문자가 있는 "9"를 찾습니다.
`9*` | 0개 이상의 "9" 문자를 찾습니다.
`\b` | 단어 경계를 종료합니다.
 
### <a name="match-one-or-more-times-"></a>1번 이상 일치: +

**+** 수량자는 이전 요소를 1번 이상 일치시킵니다. **{1,}**과 같습니다. **+**은 게으른 수량자가 **+?**인 탐욕적 수량자입니다.

예를 들어, 정규식 `\ban+\w*?\b`은 `n` 문자의 인스턴스가 하나 이상 뒤에 오는 `a` 문자로 시작하는 전체 단어를 찾으려고 합니다. 다음 예제에서는 이 정규식을 설명합니다. 정규식은 `an`, `annual`, `announcement` 및 `antique` 단어를 찾며 `autumn` 및 `all` 단어를 찾는 데 실패합니다.

```csharp
string pattern = @"\ban+\w*?\b";

string input = "Autumn is a great time for an annual announcement to all antique collectors.";
foreach (Match match in Regex.Matches(input, pattern, RegexOptions.IgnoreCase))
   Console.WriteLine("'{0}' found at position {1}.", match.Value, match.Index);

// The example displays the following output:   
//       'an' found at position 27.
//       'annual' found at position 30.
//       'announcement' found at position 37.
//       'antique' found at position 57.      
```

```vb
Dim pattern As String = "\ban+\w*?\b"

Dim input As String = "Autumn is a great time for an annual announcement to all antique collectors."
For Each match As Match In Regex.Matches(input, pattern, RegexOptions.IgnoreCase)
   Console.WriteLine("'{0}' found at position {1}.", match.Value, match.Index)
Next   
' The example displays the following output:   
'       'an' found at position 27.
'       'annual' found at position 30.
'       'announcement' found at position 37.
'       'antique' found at position 57. 
```

이 정규식 패턴은 다음 테이블과 같이 정의됩니다.

패턴 | 설명
------- | ----------- 
`\b` | 단어 경계를 시작합니다.
`an+` | 뒤에 하나 이상의 "n" 문자가 있는 "a"를 찾습니다. 
`\w*?` | 단어 문자와 0번 이상의 일치하지만 가능한 적은 수로 일치합니다.
`\b` | 단어 경계를 종료합니다.
 
### <a name="match-zero-or-one-time-"></a>0번 또는 1번 일치: ?

**?** 수량자는 이전 요소를 0번 또는 1번 일치시킵니다. **{0,1}**과 같습니다. **?** 은 게으른 수량자가 **??**인 탐욕적 수량자입니다.

예를 들어, 정규식 `\ban?\b`은 `n` 문자의 인스턴스가 0개 또는 1개 뒤에 오는 `a` 문자로 시작하는 전체 단어를 찾으려고 합니다. 즉, 단어 `a` 및 `an`를 찾으려고 합니다. 다음 예제에서는 이 정규식을 설명합니다.

```csharp
string pattern = @"\ban?\b";
string input = "An amiable animal with a large snount and an animated nose.";
foreach (Match match in Regex.Matches(input, pattern, RegexOptions.IgnoreCase))
   Console.WriteLine("'{0}' found at position {1}.", match.Value, match.Index);

// The example displays the following output:   
//        'An' found at position 0.
//        'a' found at position 23.
//        'an' found at position 42.
```

```vb
Dim pattern As String = "\ban?\b"
Dim input As String = "An amiable animal with a large snount and an animated nose."
For Each match As Match In Regex.Matches(input, pattern, RegexOptions.IgnoreCase)
   Console.WriteLine("'{0}' found at position {1}.", match.Value, match.Index)
Next  
' The example displays the following output:   
'       'An' found at position 0.
'       'a' found at position 23.
'       'an' found at position 42.
```

이 정규식 패턴은 다음 테이블과 같이 정의됩니다.

패턴 | 설명
------- | ----------- 
`\b` | 단어 경계를 시작합니다.
`an?` | 뒤에 0개 또는 1개의 "n" 문자가 있는 "a"를 찾습니다. 
`\b` | 단어 경계를 종료합니다.
 
### <a name="match-exactly-n-times-n"></a>정확히 n번 일치: {n}

**{**_n_**}** 수량자는 정확하게 *n*번 이전 요소와 일치하며 여기서 *n*은 정수입니다. **{**_n_**}**은 게으른 수량자가 **{**_n_**}?**인 탐욕적 수량자입니다.

예를 들어, 정규식 `\b\d+\,\d{3}\b`는 뒤에 하나 이상의 10진수 숫자, 세 개의 10진수 숫자와 단어 경계가 있는 단어 경계를 찾으려고 합니다. 다음 예제에서는 이 정규식을 설명합니다. 

```csharp
string pattern = @"\b\d+\,\d{3}\b";
string input = "Sales totaled 103,524 million in January, " + 
                      "106,971 million in February, but only " + 
                      "943 million in March.";
foreach (Match match in Regex.Matches(input, pattern))
   Console.WriteLine("'{0}' found at position {1}.", match.Value, match.Index);

//  The example displays the following output:   
//        '103,524' found at position 14.
//        '106,971' found at position 45.
```

```vb
Dim pattern As String = "\b\d+\,\d{3}\b"
Dim input As String = "Sales totaled 103,524 million in January, " + _
                      "106,971 million in February, but only " + _
                      "943 million in March."
For Each match As Match In Regex.Matches(input, pattern)
   Console.WriteLine("'{0}' found at position {1}.", match.Value, match.Index)
Next     
' The example displays the following output:   
'       '103,524' found at position 14.
'       '106,971' found at position 45.
```

이 정규식 패턴은 다음 테이블과 같이 정의됩니다.

패턴 | 설명
------- | ----------- 
`\b` | 단어 경계를 시작합니다.
`\d+` | 하나 이상의 10진수 숫자가 일치하는지 확인합니다. 
`\,` | 쉼표 문자를 찾습니다.
`\d{3}` | 세 개의 10진수를 찾습니다.
`\b` | 단어 경계를 종료합니다.
 
### <a name="match-at-least-n-times-n"></a>적어도 n번 일치: {n,}

**{**_n_**}** 수량자는 적어도 *n*번 이전 요소와 일치하며 여기서 *n*은 정수입니다. **{**_n_**,}**은 게으른 수량자가 **{**_n_**}?**인 탐욕적 수량자입니다.

예를 들어, 정규식 `\b\d{2,}\b\D+`는 뒤에 적어도 두 개의 숫자, 단어 경계와 숫자가 아닌 문자가 있는 단어 경계를 찾으려고 합니다. 다음 예제에서는 이 정규식을 설명합니다. 정규식은 "7일"이라는 구를 찾는 데 실패합니다. 하나의 숫자를 포함하지만 "10주 및 300년"이라는 구와 일치하기 때문입니다.

```csharp
string pattern = @"\b\d{2,}\b\D+";   
string input = "7 days, 10 weeks, 300 years";
foreach (Match match in Regex.Matches(input, pattern))
   Console.WriteLine("'{0}' found at position {1}.", match.Value, match.Index);

//  The example displays the following output:
//        '10 weeks, ' found at position 8.
// 
``` 

```vb
Dim pattern As String = "\b\d{2,}\b\D+"  
 Dim input As String = "7 days, 10 weeks, 300 years"
For Each match As Match In Regex.Matches(input, pattern)
   Console.WriteLine("'{0}' found at position {1}.", match.Value, match.Index)
Next 
' The example displays the following output:
'       '10 weeks, ' found at position 8.
'       '300 years' found at position 18.
      '300 years' found at position 18.
```

이 정규식 패턴은 다음 테이블과 같이 정의됩니다.

패턴 | 설명
------- | ----------- 
`\b` | 단어 경계를 시작합니다.
`\d{2,}` | 적어도 두 개의 10진수를 찾습니다. 
`\b` | 단어 경계를 찾습니다.
`\D+` | 적어도 하나의 10진수가 아닌 수를 찾습니다.
 
### <a name="match-between-n-and-m-times-nm"></a>n번에서 m번 사이에 일치: {n, m}

**{**_n_**,**_m_**}** 수량자는 이전 요소와 적어도 *n*번 일치하지만 *m*번보다 많지 않습니다. 여기서 *n* 및 *m*은 정수입니다. **{**_n_**,**_m_**}**은 게으른 수량자가 **{**_n_**,**_m_**}?**인 탐욕적 수량자입니다.

다음 예제에서 정규식 `(00\s){2,4}`은 뒤에 공백이 있는 두 개의 숫자 0이 2회에서 4회 사이에 발생하는 경우를 찾습니다. 입력 문자열의 마지막 부분에는 이 패턴이 5번이나 포함됩니다(최대 4번 아님). 그러나 공백 및 0의 다섯 번째 쌍까지의 하위 문자열의 처음 부분만이 정규식 패턴과 일치합니다.

```csharp
string pattern = @"(00\s){2,4}";
string input = "0x00 FF 00 00 18 17 FF 00 00 00 21 00 00 00 00 00";
foreach (Match match in Regex.Matches(input, pattern))
   Console.WriteLine("'{0}' found at position {1}.", match.Value, match.Index);

//  The example displays the following output:
//        '00 00 ' found at position 8.
//        '00 00 00 ' found at position 23.
//        '00 00 00 00 ' found at position 35.
```

```vb
Dim pattern As String = "(00\s){2,4}"
Dim input As String = "0x00 FF 00 00 18 17 FF 00 00 00 21 00 00 00 00 00"
For Each match As Match In Regex.Matches(input, pattern)
   Console.WriteLine("'{0}' found at position {1}.", match.Value, match.Index)
Next 
' The example displays the following output:
'       '00 00 ' found at position 8.
'       '00 00 00 ' found at position 23.
'       '00 00 00 00 ' found at position 35.
```

### <a name="match-zero-or-more-times-lazy-match-"></a>0번 이상 일치(게으른 일치 항목): \*?

**\*?** 수량자는 이 요소와 0번 이상 일치하지만 가능한 적은 수로 일치합니다. 탐욕적 수량자 **\***의 게으른 수량자입니다.

다음 예제에서 정규식 `\b\w*?oo\w*?\b`은 문자열 `oo`이 포함된 모든 단어를 찾습니다. 

```csharp
 string pattern = @"\b\w*?oo\w*?\b";
 string input = "woof root root rob oof woo woe";
 foreach (Match match in Regex.Matches(input, pattern, RegexOptions.IgnoreCase))
    Console.WriteLine("'{0}' found at position {1}.", match.Value, match.Index);

 //  The example displays the following output:
//        'woof' found at position 0.
//        'root' found at position 5.
//        'root' found at position 10.
//        'oof' found at position 19.
//        'woo' found at position 23.
```

```vb
Dim pattern As String = "\b\w*?oo\w*?\b"
 Dim input As String = "woof root root rob oof woo woe"
 For Each match As Match In Regex.Matches(input, pattern, RegexOptions.IgnoreCase)
    Console.WriteLine("'{0}' found at position {1}.", match.Value, match.Index)
 Next 
 ' The example displays the following output:
'       'woof' found at position 0.
'       'root' found at position 5.
'       'root' found at position 10.
'       'oof' found at position 19.
'       'woo' found at position 23.
```

이 정규식 패턴은 다음 테이블과 같이 정의됩니다.

패턴 | 설명
------- | ----------- 
`\b` | 단어 경계를 시작합니다.
`\w*?` | 0개 이상의 단어 문자(가능한 한 적은 문자)를 찾습니다. 
`oo` | "oo" 문자열을 찾습니다.
`\w*?` | 0개 이상의 단어 문자(가능한 한 적은 문자)를 찾습니다.
`\b` | 단어 경계를 종료합니다.
 
### <a name="match-one-or-more-times-lazy-match-"></a>0번 이상 일치(게으른 일치 항목): +?

**+?** 수량자는 이 요소와 1번 이상 일치하지만 가능한 적은 수로 일치합니다. 탐욕적 수량자 **+**의 게으른 수량자입니다.

예를 들어, 정규식 `\b\w+?\b`은 단어 경계로 구분된 1개 이상의 문자를 찾습니다. 다음 예제에서는 이 정규식을 설명합니다.

```csharp
string pattern = @"\b\w+?\b";
string input = "Aa Bb Cc Dd Ee Ff";
foreach (Match match in Regex.Matches(input, pattern))
   Console.WriteLine("'{0}' found at position {1}.", match.Value, match.Index);

//  The example displays the following output:
//        'Aa' found at position 0.
//        'Bb' found at position 3.
//        'Cc' found at position 6.
//        'Dd' found at position 9.
//        'Ee' found at position 12.
//        'Ff' found at position 15.
```

```vb
Dim pattern As String = "\b\w+?\b"
 Dim input As String = "Aa Bb Cc Dd Ee Ff"
For Each match As Match In Regex.Matches(input, pattern)
   Console.WriteLine("'{0}' found at position {1}.", match.Value, match.Index)
Next 
' The example displays the following output:
'       'Aa' found at position 0.
'       'Bb' found at position 3.
'       'Cc' found at position 6.
'       'Dd' found at position 9.
'       'Ee' found at position 12.
'       'Ff' found at position 15.
```

### <a name="match-zero-or-one-time-lazy-match-"></a>0번 또는 1번 일치(게으른 일치): ??

**??** 수량자는 이 요소와 0번 또는 2번 일치하지만 가능한 적은 수로 일치합니다. 탐욕적 수량자 **?**의 게으른 수량자입니다.

예를 들어, 정규식 `^\s*(System.)??Console.Write(Line)??\(??`은 "Console.Write" 또는 "Console.WriteLine" 문자열을 찾으려고 합니다. 문자열은 "Console" 앞에 "System."를 포함할 수도 있고 문자열 뒤에 여는 괄호가 있을 수 있습니다. 문자열 앞에는 공백이 있을 수 있지만 줄의 시작 부분에 있어야 합니다. 다음 예제에서는 이 정규식을 설명합니다.

```csharp
string pattern = @"^\s*(System.)??Console.Write(Line)??\(??";
string input = "System.Console.WriteLine(\"Hello!\")\n" + 
                      "Console.Write(\"Hello!\")\n" + 
                      "Console.WriteLine(\"Hello!\")\n" + 
                      "Console.ReadLine()\n" + 
                      "   Console.WriteLine";
foreach (Match match in Regex.Matches(input, pattern, 
                                      RegexOptions.IgnorePatternWhitespace | 
                                      RegexOptions.IgnoreCase | 
                                      RegexOptions.Multiline))
   Console.WriteLine("'{0}' found at position {1}.", match.Value, match.Index);

//  The example displays the following output:
//        'System.Console.Write' found at position 0.
//        'Console.Write' found at position 36.
//        'Console.Write' found at position 61.
//        '   Console.Write' found at position 110.
```

```vb
Dim pattern As String = "^\s*(System.)??Console.Write(Line)??\(??"
Dim input As String = "System.Console.WriteLine(""Hello!"")" + vbCrLf + _
                      "Console.Write(""Hello!"")" + vbCrLf + _
                      "Console.WriteLine(""Hello!"")" + vbCrLf + _
                      "Console.ReadLine()" + vbCrLf + _
                      "   Console.WriteLine"
For Each match As Match In Regex.Matches(input, pattern, _
                                         RegexOptions.IgnorePatternWhitespace Or RegexOptions.IgnoreCase Or RegexOptions.MultiLine)
   Console.WriteLine("'{0}' found at position {1}.", match.Value, match.Index)
Next 
' The example displays the following output:
'       'System.Console.Write' found at position 0.
'       'Console.Write' found at position 36.
'       'Console.Write' found at position 61.
'       '   Console.Write' found at position 110.
```

이 정규식 패턴은 다음 테이블과 같이 정의됩니다.

패턴 | 설명
------- | ----------- 
`^` | 입력 스트림의 시작 부분을 찾습니다.
`\s*` | 0개 이상의 공백 문자가 일치하는지 확인합니다.
`(System.)??` | "System."이라는 0개 또는 1개의 문자열을 찾습니다.
`Console.Write` | "Console.Write"라는 문자열을 찾습니다.
`(Line)??` | "Line"이라는 0개 또는 1개의 문자열을 찾습니다.
`\(??` | 0개 또는 1개의 여는 괄호를 찾습니다.
 
### <a name="match-exactly-n-times-lazy-match-n"></a>정확히 n번 일치(게으른 일치): {n}?

**{**_n_**}?** 수량자는 정확하게 *n*번 이전 요소와 일치하며 여기서 *n*은 정수입니다. 탐욕적 수량자 **{**_n_**}+**의 게으른 수량자입니다.

다음 예제에서 정규식 `\b(\w{3,}?\.){2}?\w{3,}?\b`은 웹 사이트 주소를 식별하는 데 사용됩니다. "www.microsoft.com" 및 "msdn.microsoft.com"과 일치하지만 "mywebsite" 또는 "mycompany.com"과 일치하지 않습니다.

```csharp
string pattern = @"\b(\w{3,}?\.){2}?\w{3,}?\b";
string input = "www.microsoft.com msdn.microsoft.com mywebsite mycompany.com";
foreach (Match match in Regex.Matches(input, pattern))
   Console.WriteLine("'{0}' found at position {1}.", match.Value, match.Index);

//  The example displays the following output:
//        'www.microsoft.com' found at position 0.
//        'msdn.microsoft.com' found at position 18.
```

```vb
Dim pattern As String = "\b(\w{3,}?\.){2}?\w{3,}?\b"
 Dim input As String = "www.microsoft.com msdn.microsoft.com mywebsite mycompany.com"
For Each match As Match In Regex.Matches(input, pattern)
   Console.WriteLine("'{0}' found at position {1}.", match.Value, match.Index)
Next     
' The example displays the following output:
'       'www.microsoft.com' found at position 0.
'       'msdn.microsoft.com' found at position 18.
```

이 정규식 패턴은 다음 테이블과 같이 정의됩니다.

패턴 | 설명
------- | -----------
`\b` | 단어 경계를 시작합니다.
`(\w{3,}?\.)` | 뒤에 점 또는 마침표 문자가 있는 가능한 적은 수의 최소한 3개의 단어 문자를 찾습니다. 이 그룹은 첫 번째 캡처링 그룹입니다.
`(\w{3,}?\.){2}?` | 첫 번째 그룹의 패턴과 두 번 일치하지만 가능한 적은 수로 일치합니다.
`\b` | 단어 경계에서 일치 항목 찾기를 끝냅니다.
 
### <a name="match-at-least-n-times-lazy-match-n"></a>적어도 n번 일치(게으른 일치): {n,}?

**{**_n_**,}?** 수량자는 이전 요소와 적어도 *n*번 일치하며 여기서 *n*은 정수입니다. 가능한 적은 수로 일치합니다. 탐욕적 수량자 **{**_n_**,}**의 게으른 수량자입니다.

설명은 이전 섹션에서 **{**_n_**}?**  수량자에 대한 예제를 참조하세요. 해당 예제에서 정규식은 **{**_n_**,}** 수량자를 사용하여 뒤에 마침표가 있는 적어도 3개의 문자가 있는 문자열을 찾습니다.

### <a name="match-between-n-and-m-times-lazy-match-nm"></a>n번에서 m번 사이에 일치(게으른 일치): {n,m}?

**{**_n_**,**_m_**}?** 수량자는 이전 요소와 *n*번에서 *m*번 사이에 일치합니다. 여기서 *n* 및 *m*은 정수이며 가능한 적은 수로 일치합니다. 탐욕적 수량자 **{**_n_**,**_m_**}**의 게으른 수량자입니다.

다음 예제에서 정규식 `\b[A-Z](\w*\s+){1,10}?[.!?]`은 하나에서 열 개 단어 사이를 포함하는 문장을 찾습니다. 18개의 단어가 포함된 한 문장을 제외하고 입력 문자열에 있는 모든 문장을 찾습니다.

```csharp
string pattern = @"\b[A-Z](\w*?\s*?){1,10}[.!?]";
string input = "Hi. I am writing a short note. Its purpose is " + 
                      "to test a regular expression that attempts to find " + 
                      "sentences with ten or fewer words. Most sentences " + 
                      "in this note are short.";
foreach (Match match in Regex.Matches(input, pattern))
   Console.WriteLine("'{0}' found at position {1}.", match.Value, match.Index);

//  The example displays the following output:
//        'Hi.' found at position 0.
//        'I am writing a short note.' found at position 4.
//        'Most sentences in this note are short.' found at position 132.
```

```vb
Dim pattern As String = "\b[A-Z](\w*\s?){1,10}?[.!?]"
Dim input As String = "Hi. I am writing a short note. Its purpose is " + _
                      "to test a regular expression that attempts to find " + _
                      "sentences with ten or fewer words. Most sentences " + _
                      "in this note are short."
For Each match As Match In Regex.Matches(input, pattern)
   Console.WriteLine("'{0}' found at position {1}.", match.Value, match.Index)
Next 
' The example displays the following output:
'       'Hi.' found at position 0.
'       'I am writing a short note.' found at position 4.
'       'Most sentences in this note are short.' found at position 132.
```

이 정규식 패턴은 다음 테이블과 같이 정의됩니다.

패턴 | 설명
------- | -----------
`\b` | 단어 경계를 시작합니다.
`[A-Z]` | A-Z의 대문자를 찾습니다.
`(\w*\s+)` | 뒤에 1개 이상의 공백 문자가 있는 0개 이상의 단어 문자를 찾습니다. 이 그룹은 첫 번째 캡처 그룹입니다.
`{1,10}?` | 이전 패턴과 1번에서 10번 사이에 일치하지만 가능한 적은 수로 일치합니다.
`[.!?]` | 문장 부호 문자 ".", "!" 또는 "?" 중 하나를 찾습니다.
 
## <a name="greedy-and-lazy-quantifiers"></a>탐욕적 및 게으른 수량자

여러 수량자에는 두 가지 버전이 있습니다. 

* 탐욕적 버전 

  탐욕적 수량자는 요소를 최대한 많이 찾으려고 합니다. 


* •탐욕적이 아닌(또는 게으른) 버전 

 탐욕적이 아닌 수량자는 요소를 최대한 적게 찾으려고 합니다. **?**를 추가하기만 하면 탐욕적 수량자를 게으른 수량자로 설정할 수 있습니다.

신용 카드 번호와 같은 숫자 문자열에서 마지막 4자리를 추출하기 위한 간단한 정규식을 사용합니다. **\*** 탐욕적 수량자를 사용하는 정규식의 버전은 `\b.*([0-9]{4})\b`입니다. 그러나 문자열이 두 개의 숫자를 포함하는 경우 다음 예제와 같이 정규식은 두 번째 숫자의 마지막 4자리가 일치합니다.

```csharp
string greedyPattern = @"\b.*([0-9]{4})\b";
string input1 = "1112223333 3992991999";
foreach (Match match in Regex.Matches(input1, greedyPattern))
   Console.WriteLine("Account ending in ******{0}.", match.Groups[1].Value);

// The example displays the following output:
//       Account ending in ******1999.
```

```vb
Dim greedyPattern As String = "\b.*([0-9]{4})\b"
Dim input1 As String = "1112223333 3992991999"
For Each match As Match In Regex.Matches(input1, greedypattern)
   Console.WriteLine("Account ending in ******{0}.", match.Groups(1).Value)
Next
' The example displays the following output:
'       Account ending in ******1999.
```

**\*** 수량자가 문자열의 끝에서 일치 항목을 찾도록 전체 문자열에서 최대한 많은 이전 요소를 일치시키기 때문에 정규식은 첫 번째 숫자를 찾는 데 실패합니다.

이러한 동작이 발생하지 않아야 합니다. 대신 다음 예제와 같이 **\*?** 게으른 수량자를 사용하여 두 번호에서 숫자를 추출할 수 있습니다.

```csharp
string lazyPattern = @"\b.*?([0-9]{4})\b";
string input2 = "1112223333 3992991999";
foreach (Match match in Regex.Matches(input2, lazyPattern))
   Console.WriteLine("Account ending in ******{0}.", match.Groups[1].Value);

// The example displays the following output:
//       Account ending in ******3333.
//       Account ending in ******1999.
```

```vb
Dim lazyPattern As String = "\b.*?([0-9]{4})\b"
Dim input2 As String = "1112223333 3992991999"
For Each match As Match In Regex.Matches(input2, lazypattern)
   Console.WriteLine("Account ending in ******{0}.", match.Groups(1).Value)
Next     
' The example displays the following output:
'       Account ending in ******3333.
'       Account ending in ******1999.
```

대부분의 경우 탐욕적 및 게으른 수량자를 포함하는 정규식은 동일한 일치 항목을 반환합니다. 와일드 카드(**.**) 메타문자를 사용할 경우 가장 일반적으로 다른 결과를 반환하며 모든 문자를 찾습니다. 

## <a name="quantifiers-and-empty-matches"></a>수량자 및 빈 일치 항목

수량자 **\***, **+** 및 **{**_n_**,**_m_**}**과 해당 게으른 수량자는 최소한의 캡처 수를 찾는 경우 빈 일치 항목 후에 반복되지 않습니다. 가능한 그룹 캡처의 최대 수가 무한 또는 거의 무한인 경우 이 규칙은 수량자가 빈 하위 식을 찾기 위해 무한 루프를 입력하지 않도록 방지합니다.

예를 들어 다음 코드는 0개 또는 1개의 "a" 문자를 0번 이상 찾는 정규식 패턴 `(a?)*,`을 포함한 [Regex.Match](xref:System.Text.RegularExpressions.Regex.Match(System.String)) 메서드에 대한 호출의 결과를 표시합니다. 단일 캡처링 그룹은 [String.Empty](xref:System.String.Empty)뿐만 아니라 "a"를 캡처하지만 첫 번째 빈 일치 항목으로 인해 수량자가 반복을 멈추기 때문에 두 번째 빈 일치 항목이 없습니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = "(a?)*";
      string input = "aaabbb";
      Match match = Regex.Match(input, pattern);
      Console.WriteLine("Match: '{0}' at index {1}", 
                        match.Value, match.Index);
      if (match.Groups.Count > 1) {
         GroupCollection groups = match.Groups;
         for (int grpCtr = 1; grpCtr <= groups.Count - 1; grpCtr++) {
            Console.WriteLine("   Group {0}: '{1}' at index {2}", 
                              grpCtr, 
                              groups[grpCtr].Value,
                              groups[grpCtr].Index);
            int captureCtr = 0;
            foreach (Capture capture in groups[grpCtr].Captures) {
               captureCtr++;
               Console.WriteLine("      Capture {0}: '{1}' at index {2}", 
                                 captureCtr, capture.Value, capture.Index);
            }
         } 
      }   
   }
}
// The example displays the following output:
//       Match: 'aaa' at index 0
//          Group 1: '' at index 3
//             Capture 1: 'a' at index 0
//             Capture 2: 'a' at index 1
//             Capture 3: 'a' at index 2
//             Capture 4: '' at index 3
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "(a?)*"
      Dim input As String = "aaabbb"
      Dim match As Match = Regex.Match(input, pattern)
      Console.WriteLine("Match: '{0}' at index {1}", 
                        match.Value, match.Index)
      If match.Groups.Count > 1 Then
         Dim groups As GroupCollection = match.Groups
         For grpCtr As Integer = 1 To groups.Count - 1
            Console.WriteLine("   Group {0}: '{1}' at index {2}", 
                              grpCtr, 
                              groups(grpCtr).Value,
                              groups(grpCtr).Index)
            Dim captureCtr As Integer = 0
            For Each capture As Capture In groups(grpCtr).Captures
               captureCtr += 1
               Console.WriteLine("      Capture {0}: '{1}' at index {2}", 
                                 captureCtr, capture.Value, capture.Index)
            Next
         Next 
      End If   
   End Sub
End Module
' The example displays the following output:
'       Match: 'aaa' at index 0
'          Group 1: '' at index 3
'             Capture 1: 'a' at index 0
'             Capture 2: 'a' at index 1
'             Capture 3: 'a' at index 2
'             Capture 4: '' at index 3
```

캡처의 최소 및 최대 수를 정의하는 캡처링 그룹과 캡처의 고정 수를 정의하는 그룹 사이의 실질적인 차이를 확인하려면 정규식 패턴 `(a\1|(?(1)\1)){0,2}` 및 `(a\1|(?(1)\1)){2}`를 사용하는 것이 좋습니다. 두 정규식은 단일 캡처링 그룹으로 구성되며 다음 테이블과 같이 정의됩니다. 

패턴 | 설명
------- | -----------
`(a\1` | 첫 번째 캡처된 그룹의 값과 함께 "a"를 찾거나…
`&#124;(?(1)` | … 또는 첫 번째 캡처된 그룹이 정의되어 있는지 여부를 테스트합니다. **(?(1)** 구문은 캡처링 그룹을 정의하지 않습니다.
`\1))` | 첫 번째 캡처된 그룹이 있는 경우 해당 값을 찾습니다. 해당 그룹이 존재하지 않는 경우 그룹은 [String.Empty](xref:System.String.Empty)를 찾습니다. 
 
첫 번째 정규식이 0번과 두 번 사이에 이 패턴을 찾으려 합니다. 두 번째 정규식은 정확히 두 번입니다. 첫 번째 패턴이 [String.Empty](xref:System.String.Empty)의 첫 번째 캡처를 사용하여 최소의 캡처 수에 도달하기 때문에 `{0,2}` 수량자가 마지막 반복에서 빈 일치 항목을 허용하는 `a\1;`를 찾기 위해 반복하지 않습니다. 두 번째 정규식이 `a\1`을 두 번째로 평가하기 때문에 "a"를 찾는 반면 반복의 최소수인 2는 빈 일치 항목 뒤에 엔진이 반복되도록 강제합니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern, input;

      pattern = @"(a\1|(?(1)\1)){0,2}";
      input = "aaabbb"; 

      Console.WriteLine("Regex pattern: {0}", pattern);
      Match match = Regex.Match(input, pattern);
      Console.WriteLine("Match: '{0}' at position {1}.", 
                        match.Value, match.Index);
      if (match.Groups.Count > 1) {
         for (int groupCtr = 1; groupCtr <= match.Groups.Count - 1; groupCtr++)
         {
            Group group = match.Groups[groupCtr];         
            Console.WriteLine("   Group: {0}: '{1}' at position {2}.", 
                              groupCtr, group.Value, group.Index);
            int captureCtr = 0;
            foreach (Capture capture in group.Captures) {
               captureCtr++;
               Console.WriteLine("      Capture: {0}: '{1}' at position {2}.", 
                                 captureCtr, capture.Value, capture.Index);
            }   
         }
      }
      Console.WriteLine();

      pattern = @"(a\1|(?(1)\1)){2}";
      Console.WriteLine("Regex pattern: {0}", pattern);
      match = Regex.Match(input, pattern);
         Console.WriteLine("Matched '{0}' at position {1}.", 
                           match.Value, match.Index);
      if (match.Groups.Count > 1) {
         for (int groupCtr = 1; groupCtr <= match.Groups.Count - 1; groupCtr++)
         {
            Group group = match.Groups[groupCtr];         
            Console.WriteLine("   Group: {0}: '{1}' at position {2}.", 
                              groupCtr, group.Value, group.Index);
            int captureCtr = 0;
            foreach (Capture capture in group.Captures) {
               captureCtr++;
               Console.WriteLine("      Capture: {0}: '{1}' at position {2}.", 
                                 captureCtr, capture.Value, capture.Index);
            }   
         }
      }
   }
}
// The example displays the following output:
//       Regex pattern: (a\1|(?(1)\1)){0,2}
//       Match: '' at position 0.
//          Group: 1: '' at position 0.
//             Capture: 1: '' at position 0.
//       
//       Regex pattern: (a\1|(?(1)\1)){2}
//       Matched 'a' at position 0.
//          Group: 1: 'a' at position 0.
//             Capture: 1: '' at position 0.
//             Capture: 2: 'a' at position 0.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern, input As String

      pattern = "(a\1|(?(1)\1)){0,2}"
      input = "aaabbb" 

      Console.WriteLine("Regex pattern: {0}", pattern)
      Dim match As Match = Regex.Match(input, pattern)
      Console.WriteLine("Match: '{0}' at position {1}.", 
                        match.Value, match.Index)
      If match.Groups.Count > 1 Then
         For groupCtr As Integer = 1 To match.Groups.Count - 1
            Dim group As Group = match.Groups(groupCtr)         
            Console.WriteLine("   Group: {0}: '{1}' at position {2}.", 
                              groupCtr, group.Value, group.Index)
            Dim captureCtr As Integer = 0
            For Each capture As Capture In group.Captures
               captureCtr += 1
               Console.WriteLine("      Capture: {0}: '{1}' at position {2}.", 
                                 captureCtr, capture.Value, capture.Index)
            Next   
         Next
      End If
      Console.WriteLine()

      pattern = "(a\1|(?(1)\1)){2}"
      Console.WriteLine("Regex pattern: {0}", pattern)
      match = Regex.Match(input, pattern)
         Console.WriteLine("Matched '{0}' at position {1}.", 
                           match.Value, match.Index)
      If match.Groups.Count > 1 Then
         For groupCtr As Integer = 1 To match.Groups.Count - 1
            Dim group As Group = match.Groups(groupCtr)         
            Console.WriteLine("   Group: {0}: '{1}' at position {2}.", 
                              groupCtr, group.Value, group.Index)
            Dim captureCtr As Integer = 0
            For Each capture As Capture In group.Captures
               captureCtr += 1
               Console.WriteLine("      Capture: {0}: '{1}' at position {2}.", 
                                 captureCtr, capture.Value, capture.Index)
            Next   
         Next
      End If
   End Sub
End Module
' The example displays the following output:
'       Regex pattern: (a\1|(?(1)\1)){0,2}
'       Match: '' at position 0.
'          Group: 1: '' at position 0.
'             Capture: 1: '' at position 0.
'       
'       Regex pattern: (a\1|(?(1)\1)){2}
'       Matched 'a' at position 0.
'          Group: 1: 'a' at position 0.
'             Capture: 1: '' at position 0.
'             Capture: 2: 'a' at position 0.
```

## <a name="see-also"></a>참고 항목

[정규식 언어 - 빠른 참조](quick-ref.md)

[정규식의 역추적](backtracking.md)




<!--HONumber=Nov16_HO1-->


