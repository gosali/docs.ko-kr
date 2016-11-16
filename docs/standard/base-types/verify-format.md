---
title: "방법: 문자열이 올바른 전자 메일 형식인지 확인"
description: "방법: 문자열이 올바른 전자 메일 형식인지 확인"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 07/28/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 6d735520-4059-4754-b34c-d117299d36f1
translationtype: Human Translation
ms.sourcegitcommit: fb00da6505c9edb6a49d2003ae9bcb8e74c11d6c
ms.openlocfilehash: d04c77b2d7597371651f15ca4a7e203c9cbb8ccb

---

# <a name="how-to-verify-that-strings-are-in-valid-email-format"></a>방법: 문자열이 올바른 전자 메일 형식인지 확인

다음 예제에서는 정규식을 사용하여 문자열이 올바른 전자 메일 형식인지 확인합니다.

## <a name="example"></a>예제

이 예제에서는 문자열에 올바른 전자 메일 주소가 포함되어 있으면 `IsValidEmail`를 반환하고, 그렇지 않으면 `true`를 반환하지만 다른 작업을 수행하지 않는 `false` 메서드를 정의합니다. 

전자 메일 주소가 올바른지 확인하기 위해 `IsValidEmail` 메서드는 `(@)(.+)$` 정규식 패턴으로 [Regex.Replace(String, String, MatchEvaluator)](xref:System.Text.RegularExpressions.Regex.Replace(System.String,System.Text.RegularExpressions.MatchEvaluator)) 메서드를 호출하여 전자 메일 주소에서 도메인 이름을 분리합니다. 세 번째 매개 변수는 일치하는 텍스트를 처리하고 대체하는 메서드를 나타내는 [MatchEvaluator](xref:System.Text.RegularExpressions.MatchEvaluator) 대리자입니다. 정규식 패턴은 다음과 같이 해석됩니다. 

패턴 | 설명
------- | ----------- 
`(@)` | @ 문자를 찾습니다. 이 그룹은 첫 번째 캡처링 그룹입니다.
`(.+)` | 하나 이상의 문자를 찾습니다. 이 그룹은 두 번째 캡처링 그룹입니다.
`$` | 문자열의 끝 부분에서 일치 항목 찾기를 끝냅니다.
 
@ 문자와 함께 도메인 이름이 `DomainMapper` 메서드로 전달됩니다. 이 메서드는 [IdnMapping](xref:System.Globalization.IdnMapping) 클래스를 사용하여 US-ASCII 문자 범위 외부에 있는 유니코드 문자를 Punycode로 변환합니다. 이 메서드는 [IdnMapping.GetAscii](xref:System.Globalization.IdnMapping.GetAscii(System.String)) 메서드가 도메인 이름에서 잘못된 문자를 발견하는 경우 `invalid` 플래그를 `true`로 설정합니다. 메서드는 앞에 @ 기호가 있는 Punycode 도메인 이름을 `IsValidEmail` 메서드에 반환합니다. 

그러면 `IsValidEmail` 메서드는 [Regex.IsMatch(String, String)](xref:System.Text.RegularExpressions.Regex.IsMatch(System.String,System.String)) 메서드를 호출하여 주소가 정규식 패턴을 따르는지 확인합니다. 

`IsValidEmail` 메서드는 전자 메일 주소의 유효성을 검사하기 위해 인증을 수행하는 것이 아닙니다. 단지 형식이 전자 메일 주소에 올바른지 여부만 확인합니다. 또한 `IsValidEmail` 메서드는 최상위 도메인 이름이 조회 작업 시 요구되는 [IANA 루트 영역 데이터베이스](https://www.iana.org/domains/root/db)에 나열된 올바른 도메인 이름인지 확인하지 않습니다. 대신, 정규식은 최상위 도메인 이름이 2~24개의 ASCII 문자(첫 번째 문자와 마지막 문자는 영숫자이고, 나머지 문자는 영숫자이거나 하이픈(-)임)로 구성되었는지만 확인합니다. 

```csharp
using System;
using System.Globalization;
using System.Text.RegularExpressions;

public class RegexUtilities
{
   bool invalid = false;

   public bool IsValidEmail(string strIn)
   {
       invalid = false;
       if (String.IsNullOrEmpty(strIn))
          return false;

       // Use IdnMapping class to convert Unicode domain names.
       try {
          strIn = Regex.Replace(strIn, @"(@)(.+)$", this.DomainMapper,
                                RegexOptions.None, TimeSpan.FromMilliseconds(200));
       }
       catch (RegexMatchTimeoutException) {
         return false;
       }

        if (invalid)
           return false;

       // Return true if strIn is in valid e-mail format.
       try {
          return Regex.IsMatch(strIn,
                @"^(?("")("".+?(?<!\\)""@)|(([0-9a-z]((\.(?!\.))|[-!#\$%&'\*\+/=\?\^`\{\}\|~\w])*)(?<=[0-9a-z])@))" +
                @"(?(\[)(\[(\d{1,3}\.){3}\d{1,3}\])|(([0-9a-z][-\w]*[0-9a-z]*\.)+[a-z0-9][\-a-z0-9]{0,22}[a-z0-9]))$",
                RegexOptions.IgnoreCase, TimeSpan.FromMilliseconds(250));
       }
       catch (RegexMatchTimeoutException) {
          return false;
       }
   }

   private string DomainMapper(Match match)
   {
      // IdnMapping class with default property values.
      IdnMapping idn = new IdnMapping();

      string domainName = match.Groups[2].Value;
      try {
         domainName = idn.GetAscii(domainName);
      }
      catch (ArgumentException) {
         invalid = true;
      }
      return match.Groups[1].Value + domainName;
   }
}
```

```vb
Imports System.Globalization
Imports System.Text.RegularExpressions

Public Class RegexUtilities
   Dim invalid As Boolean = False

   public Function IsValidEmail(strIn As String) As Boolean
       invalid = False
       If String.IsNullOrEmpty(strIn) Then Return False

       ' Use IdnMapping class to convert Unicode domain names.
       Try
          strIn = Regex.Replace(strIn, "(@)(.+)$", AddressOf Me.DomainMapper, 
                                RegexOptions.None, TimeSpan.FromMilliseconds(200))
       Catch e As RegexMatchTimeoutException
          Return False
       End Try

       If invalid Then Return False

       ' Return true if strIn is in valid e-mail format.
       Try
          Return Regex.IsMatch(strIn,
                 "^(?("")("".+?(?<!\\)""@)|(([0-9a-z]((\.(?!\.))|[-!#\$%&'\*\+/=\?\^`\{\}\|~\w])*)(?<=[0-9a-z])@))" +
                 "(?(\[)(\[(\d{1,3}\.){3}\d{1,3}\])|(([0-9a-z][-\w]*[0-9a-z]*\.)+[a-z0-9][\-a-z0-9]{0,22}[a-z0-9]))$",
                 RegexOptions.IgnoreCase, TimeSpan.FromMilliseconds(250))
       Catch e As RegexMatchTimeoutException
          Return False
       End Try  
   End Function

   Private Function DomainMapper(match As Match) As String
      ' IdnMapping class with default property values.
      Dim idn As New IdnMapping()

      Dim domainName As String = match.Groups(2).Value
      Try
         domainName = idn.GetAscii(domainName)
      Catch e As ArgumentException
         invalid = True      
      End Try      
      Return match.Groups(1).Value + domainName
   End Function
End Class
```

이 예제에서 `^(?(")(".+?(?<!\\)"@)|(([0-9a-z]((\.(?!\.))|[-!#\$%&'\*\+/=\?\^`\{\}\|~\w])*)(?<=[0-9a-z])@))(?(\[)(\[(\d{1,3}\.){3}\d{1,3}\])|(([0-9a-z][-\w]*[0-9a-z]*\.)+[a-z0-9][\-a-z0-9]{0,22}[a-z0-9]))$` 정규식 패턴은 다음 표와 같이 해석됩니다. 정규식은 [RegexOptions.IgnoreCase](xref:System.Text.RegularExpressions.RegexOptions.IgnoreCase) 플래그를 사용하여 컴파일됩니다.

패턴 | 설명
------- | ----------- 
`^` | 문자열의 시작 부분에서 일치 항목 찾기를 시작합니다.
`(?(")` | 첫 번째 문자가 따옴표인지 확인합니다. `(?(")`는 대체 구문의 시작 부분입니다.
`(?("")("".+?(?<!\\)""@)` | 첫 번째 문자가 따옴표인 경우 시작 따옴표 뒤에 임의의 문자가 1개 이상 나오고 그 뒤에 끝 따옴표가 나오는 일치 항목을 찾습니다. 끝 따옴표는 앞에 백슬래시 문자가 없어야 합니다. `(\). (?<!`는 너비가 0인 부정 lookbehind 어설션의 시작 부분입니다. 문자열은 (@). 기호로 끝나야 합니다.
`&#124;(([0-9a-z] | 첫 번째 문자가 따옴표가 아니면 a-z 또는 A-Z(비교는 대/소문자를 구분하지 않음)의 영문자 또는 0-9의 숫자를 찾습니다.
`(\.(?!\.))` | 다음 문자가 마침표이면 일치하게 됩니다. 마침표가 아니면 왼쪽에서 오른쪽으로 다음 문자를 검색하여 일치 항목을 계속 찾습니다. `(?!\.)`는 전자 메일 주소의 로컬 부분에 마침표 두 개가 연속으로 나타나지 않도록 하며 너비가 0인 부정 lookahead 어설션입니다.
`&#124;[-!#\$%&'\*\+/=\?\^`\{\}\&#124;~\w] | 다음 문자가 마침표가 아니면 단어 문자 또는 -!#$%'*+=?^`{}&#124;~ 중 하나를 찾습니다. 
`((\.(?!\.))&#124;[-!#\$%'\*\+/=\?\^`\{\}\&#124;~\w])* | 교체 패턴(마침표가 아닌 문자 또는 여러 문자 중 하나 앞에 마침표가 있는 패턴)을 0번 이상 찾습니다.
`@` | @ 문자를 찾습니다.
`(?<=[0-9a-z])` | @ 문자 앞의 문자가 영문자(A-Z, a-z) 또는 숫자(0-9)인 경우 일치 항목 찾기를 계속합니다. `(?<=[0-9a-z])` 구문은 너비가 0인 긍정 lookbehind 어설션을 정의합니다.
`(?(\[)` | @ 뒤의 문자가 여는 대괄호인지 확인합니다.
`(\[(\d{1,3}\.){3}\d{1,3}\])` | 여는 대괄호이면 IP 주소(1자리에서 3자리까지의 숫자로 이루어진 4개의 집합이며 각 집합은 마침표로 구분됨)와 닫는 대괄호 앞에 있는 여는 대괄호를 찾습니다.
`&#124;(([0-9a-z][-\w]*[0-9a-z]*\.)+` | @ 뒤의 문자가 여는 대괄호가 아니면 영숫자(A-Z, a-z 또는 0-9) 1개, 단어 문자 또는 하이픈 0개 이상, 영숫자(A-Z, a-z 또는 0-9) 0개 또는 1개와 마침표 1개로 구성된 패턴과 일치하는 항목을 찾습니다. 이 패턴은 한 번 이상 반복될 수 있으며, 뒤에 최상위 도메인 이름이 있어야 합니다. 
`[a-z0-9][\-a-z0-9]{0,22}[a-z0-9]))` | 최상위 도메인 이름은 영숫자(a-z, A-Z 및 0-9)로 시작하고 끝나야 하며, 영숫자이거나 하이픈인 0~22개의 ASCII 문자를 포함할 수도 있습니다. 
`$` | 문자열의 끝 부분에서 일치 항목 찾기를 끝냅니다.
 
다음과 같은 코드를 사용하여 `IsValidEmail` 및 `DomainMapper` 메서드를 호출할 수 있습니다.

```csharp
public class Application
{
   public static void Main()
   {
      RegexUtilities util = new RegexUtilities();
      string[] emailAddresses = { "david.jones@proseware.com", "d.j@server1.proseware.com",
                                  "jones@ms1.proseware.com", "j.@server1.proseware.com",
                                  "j@proseware.com9", "js#internal@proseware.com",
                                  "j_9@[129.126.118.1]", "j..s@proseware.com",
                                  "js*@proseware.com", "js@proseware..com",
                                  "js@proseware.com9", "j.s@server1.proseware.com",
                                   "\"j\\\"s\\\"\"@proseware.com", "js@contoso.中国" };

      foreach (var emailAddress in emailAddresses) {
         if (util.IsValidEmail(emailAddress))
            Console.WriteLine("Valid: {0}", emailAddress);
         else
            Console.WriteLine("Invalid: {0}", emailAddress);
      }                                            
   }
}
// The example displays the following output:
//       Valid: david.jones@proseware.com
//       Valid: d.j@server1.proseware.com
//       Valid: jones@ms1.proseware.com
//       Invalid: j.@server1.proseware.com
//       Valid: j@proseware.com9
//       Valid: js#internal@proseware.com
//       Valid: j_9@[129.126.118.1]
//       Invalid: j..s@proseware.com
//       Invalid: js*@proseware.com
//       Invalid: js@proseware..com
//       Valid: js@proseware.com9
//       Valid: j.s@server1.proseware.com
//       Valid: "j\"s\""@proseware.com
//       Valid: js@contoso.ä¸­å›½
```

```vb
Public Class Application
   Public Shared Sub Main()
      Dim util As New RegexUtilities()
      Dim emailAddresses() As String = { "david.jones@proseware.com", "d.j@server1.proseware.com", _
                                         "jones@ms1.proseware.com", "j.@server1.proseware.com", _
                                         "j@proseware.com9", "js#internal@proseware.com", _
                                         "j_9@[129.126.118.1]", "j..s@proseware.com", _
                                         "js*@proseware.com", "js@proseware..com", _
                                         "js@proseware.com9", "j.s@server1.proseware.com",
                                         """j\""s\""""@proseware.com", "js@contoso.中国" }

      For Each emailAddress As String In emailAddresses
         If util.IsValidEmail(emailAddress) Then
            Console.WriteLine("Valid: {0}", emailAddress)
         Else
            Console.WriteLine("Invalid: {0}", emailAddress)
         End If      
      Next                                            
   End Sub
End Class
' The example displays the following output:
'       Valid: david.jones@proseware.com
'       Valid: d.j@server1.proseware.com
'       Valid: jones@ms1.proseware.com
'       Invalid: j.@server1.proseware.com
'       Valid: j@proseware.com9
'       Valid: js#internal@proseware.com
'       Valid: j_9@[129.126.118.1]
'       Invalid: j..s@proseware.com
'       Invalid: js*@proseware.com
'       Invalid: js@proseware..com
'       Valid: js@proseware.com9
'       Valid: j.s@server1.proseware.com
'       Valid: "j\"s\""@proseware.com
'       Valid: js@contoso.ä¸­å›½
```

## <a name="see-also"></a>참고 항목

[.NET 정규식](regular-expressions.md)

[정규식 예제](regex-examples.md)



<!--HONumber=Nov16_HO1-->


