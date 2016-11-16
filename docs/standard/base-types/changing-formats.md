---
title: "정규식 예제: 날짜 형식 변경"
description: "날짜 형식을 변경하는 정규식 예제"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 07/28/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 3e196697-981c-4c1d-93dd-c3b236ef36dd
translationtype: Human Translation
ms.sourcegitcommit: fb00da6505c9edb6a49d2003ae9bcb8e74c11d6c
ms.openlocfilehash: 8b333db10f7dc9d0e4701899b7af46f45f60aa8e

---

# <a name="regular-expression-example-changing-date-formats"></a>정규식 예제: 날짜 형식 변경

다음 코드 예제에서는 [Regex.Replace](xref:System.Text.RegularExpressions.Regex.Replace(System.String,System.String)) 메서드를 사용하여 *mm/dd/yy* 형식의 날짜를 *dd-mm-yy* 형식의 날짜로 바꿉니다.

## <a name="example"></a>예제

```csharp
static string MDYToDMY(string input) 
{
   try {
      return Regex.Replace(input, 
            "\\b(?<month>\\d{1,2})/(?<day>\\d{1,2})/(?<year>\\d{2,4})\\b",
            "${day}-${month}-${year}", RegexOptions.None,
            TimeSpan.FromMilliseconds(150));
   }         
   catch (RegexMatchTimeoutException) {
      return input;
   }
}
```

```vb
Function MDYToDMY(input As String) As String
   Try
      Return Regex.Replace(input, _
             "\b(?<month>\d{1,2})/(?<day>\d{1,2})/(?<year>\d{2,4})\b", _
             "${day}-${month}-${year}", RegexOptions.None,
             TimeSpan.FromMilliseconds(150))
    Catch e As RegexMatchTimeoutException
       Return input
    End Try         
End Function
```

다음 코드에서는 응용 프로그램에서 `MDYToDMY` 메서드를 호출하는 방법을 보여 줍니다. 

```csharp
using System;
using System.Globalization;
using System.Text.RegularExpressions;

public class Class1
{
   public static void Main()
   {
      string dateString = DateTime.Today.ToString("d", 
                                        DateTimeFormatInfo.InvariantInfo);
      string resultString = MDYToDMY(dateString);
      Console.WriteLine("Converted {0} to {1}.", dateString, resultString);
   }

   static string MDYToDMY(string input) 
   {
      try {
         return Regex.Replace(input, 
               "\\b(?<month>\\d{1,2})/(?<day>\\d{1,2})/(?<year>\\d{2,4})\\b",
               "${day}-${month}-${year}", RegexOptions.None,
               TimeSpan.FromMilliseconds(150));
      }         
      catch (RegexMatchTimeoutException) {
         return input;
      }
   }

}
// The example displays the following output to the console if run on 8/21/2007:
//      Converted 08/21/2007 to 21-08-2007.
```

```vb
Imports System.Globalization
Imports System.Text.RegularExpressions

Module DateFormatReplacement
   Public Sub Main()
      Dim dateString As String = Date.Today.ToString("d", _
                                           DateTimeFormatInfo.InvariantInfo)
      Dim resultString As String = MDYToDMY(dateString)
      Console.WriteLine("Converted {0} to {1}.", dateString, resultString)
   End Sub

    Function MDYToDMY(input As String) As String
       Try
          Return Regex.Replace(input, _
                 "\b(?<month>\d{1,2})/(?<day>\d{1,2})/(?<year>\d{2,4})\b", _
                 "${day}-${month}-${year}", RegexOptions.None,
                 TimeSpan.FromMilliseconds(150))
        Catch e As RegexMatchTimeoutException
           Return input
        End Try         
    End Function
End Module
' The example displays the following output to the console if run on 8/21/2007:
'      Converted 08/21/2007 to 21-08-2007.
```

## <a name="comments"></a>설명

정규식 패턴 `\b(?<month>\d{1,2})/(?<day>\d{1,2})/(?<year>\d{2,4})\b`는 다음 테이블과 같이 해석됩니다.

패턴 | 설명
------- | ----------- 
`\b` | 단어 경계에서 일치 항목 찾기를 시작합니다.
`(?<month>\d{1,2})` | 한 개 또는 두 개의 10진수를 찾습니다. 캡처된 `month` 그룹입니다.
`/` | 슬래시 기호를 찾습니다.
`(?<day>\d{1,2})` | 한 개 또는 두 개의 10진수를 찾습니다. 캡처된 `day` 그룹입니다.
`/` | 슬래시 기호를 찾습니다.
`(?<year>\d{2,4})` | 2~4개의 10진수를 찾습니다. 캡처된 `year` 그룹입니다.
`\b` | 단어 경계에서 일치 항목 찾기를 끝냅니다.
 
`${day}-${month}-${year}` 패턴은 다음 표와 같이 대체 문자열을 정의합니다.

패턴 | 설명
------- | ----------- 
`$(day)` | `day` 캡처링 그룹에 의해 캡처된 부분 문자열을 추가합니다.
`-` | 하이픈을 추가합니다.
`$(month)` | `month` 캡처링 그룹에 의해 캡처된 부분 문자열을 추가합니다.
`-` | 하이픈을 추가합니다.
`$(year)` | `year` 캡처링 그룹에 의해 캡처된 부분 문자열을 추가합니다.
 
## <a name="see-also"></a>참고 항목

[.NET 정규식](regular-expressions.md)

[정규식 예제](regex-examples.md)



<!--HONumber=Nov16_HO1-->


