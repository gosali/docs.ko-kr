---
title: "정규식 예제: HREF 스캐닝"
description: "HREF를 스캐닝하는 정규식 예제"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 07/28/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: d6484880-bdac-47cd-b5e5-9419c9ed14cd
translationtype: Human Translation
ms.sourcegitcommit: fb00da6505c9edb6a49d2003ae9bcb8e74c11d6c
ms.openlocfilehash: 8951e4ca82c0148bae7e8279681920ffabb523be

---

# <a name="regular-expression-example-scanning-for-hrefs"></a>정규식 예제: HREF 스캐닝

다음 예제는 입력 문자열을 검색하고 모든 href="…" 값과 문자열에서의 해당 위치를 보여 줍니다. 

## <a name="the-regex-object"></a>Regex 개체

`DumpHRefs` 메서드를 사용자 코드에서 여러 번 호출할 수 있기 때문에 `static` [Regex.Match(String, String, RegexOptions)](xref:System.Text.RegularExpressions.Regex.Match(System.String,System.String,System.Text.RegularExpressions.RegexOptions)) 메서드를 사용합니다. 그러면 정규식 엔진이 정규식을 캐시하여 메서드를 호출할 때마다 새 [Regex](xref:System.Text.RegularExpressions.Regex) 개체를 인스턴스화하는 오버헤드를 방지할 수 있습니다. [Match](xref:System.Text.RegularExpressions.Match) 개체는 문자열의 모든 일치 항목을 반복하는 데 사용됩니다. 

```csharp
private static void DumpHRefs(string inputString) 
{
   Match m;
   string HRefPattern = "href\\s*=\\s*(?:[\"'](?<1>[^\"']*)[\"']|(?<1>\\S+))";

   try {
      m = Regex.Match(inputString, HRefPattern, 
                      RegexOptions.IgnoreCase | RegexOptions.Compiled, 
                      TimeSpan.FromSeconds(1));
      while (m.Success)
      {
         Console.WriteLine("Found href " + m.Groups[1] + " at " 
            + m.Groups[1].Index);
         m = m.NextMatch();
      }   
   }
   catch (RegexMatchTimeoutException) {
      Console.WriteLine("The matching operation timed out.");
   }
}
```

```vb
Private Sub DumpHRefs(inputString As String) 
   Dim m As Match
   Dim HRefPattern As String = "href\s*=\s*(?:[""'](?<1>[^""']*)[""']|(?<1>\S+))"

   Try
      m = Regex.Match(inputString, HRefPattern, _ 
                      RegexOptions.IgnoreCase Or RegexOptions.Compiled,
                      TimeSpan.FromSeconds(1))
      Do While m.Success
         Console.WriteLine("Found href {0} at {1}.", _
                           m.Groups(1), m.Groups(1).Index)
         m = m.NextMatch()
      Loop   
   Catch e As RegexMatchTimeoutException
      Console.WriteLine("The matching operation timed out.")
   End Try
End Sub
```

다음 예제에서는 `DumpHRefs` 메서드를 호출하는 방법을 보여 줍니다.

```csharp
public static void Main()
{
   string inputString = "My favorite web sites include:</P>" +
                        "<A HREF=\"http://msdn2.microsoft.com\">" +
                        "MSDN Home Page</A></P>" +
                        "<A HREF=\"http://www.microsoft.com\">" +
                        "Microsoft Corporation Home Page</A></P>" +
                        "<A HREF=\"http://blogs.msdn.com/bclteam\">" +
                        ".NET Base Class Library blog</A></P>";
   DumpHRefs(inputString);                     

}
// The example displays the following output:
//       Found href http://msdn2.microsoft.com at 43
//       Found href http://www.microsoft.com at 102
//       Found href http://blogs.msdn.com/bclteam at 176
```

```vb
Public Sub Main()
   Dim inputString As String = "My favorite web sites include:</P>" & _
                               "<A HREF=""http://msdn2.microsoft.com"">" & _
                               "MSDN Home Page</A></P>" & _
                               "<A HREF=""http://www.microsoft.com"">" & _
                               "Microsoft Corporation Home Page</A></P>" & _
                               "<A HREF=""http://blogs.msdn.com/bclteam"">" & _
                               ".NET Base Class Library blog</A></P>"
   DumpHRefs(inputString)                     
End Sub
' The example displays the following output:
'       Found href http://msdn2.microsoft.com at 43
'       Found href http://www.microsoft.com at 102
'       Found href http://blogs.msdn.com/bclteam/) at 176
```

정규식 패턴 `href\s*=\s*(?:["']&#40;?<1>[^"']*)["']|(?<1>\S+))`는 다음 테이블과 같이 해석됩니다.

패턴 | 설명
------- | ----------- 
`href` | 리터럴 문자열 "href"과 일치합니다. 일치 항목 찾기에서는 대/소문자를 구분하지 않습니다.
`\s*` | 0개 이상의 공백 문자가 일치하는지 확인합니다.
`=` |등호와 일치합니다.
`\s*` | 0개 이상의 공백 문자가 일치하는지 확인합니다.
`(?:["']&#40;?<1>[^"']*)"&#124;(?<1>\S+))` | 캡처된 그룹에 결과를 할당하지 않고 다음 중 하나가 일치하는지 확인합니다. 따옴표 또는 아포스트로피 뒤에 따옴표 또는 아포스트로피 이외의 다른 문자가 0개 이상 나오고 그 뒤에 다시 따옴표 또는 아포스트로피가 나오는 항목. `1`이라는 그룹이 이 패턴에 포함됩니다. 또는 하나 이상의 공백이 아닌 문자를 찾습니다. `1`이라는 그룹이 이 패턴에 포함됩니다.
`(?<1>[^"']*)` | `1`이라는 캡처 그룹에 따옴표 또는 아포스트로피 이외의 다른 문자 항목을 0개 이상 할당합니다.
`"(?<1>\S+)` | `1`이라는 캡처링 그룹에 하나 이상의 공백이 아닌 문자를 할당합니다.
 
## <a name="match-result-class"></a>일치 결과 클래스

검색 결과는 [Match](xref:System.Text.RegularExpressions.Match) 클래스에 저장되며 검색에서 추출한 모든 부분 문자열에 대한 액세스를 제공합니다. 또한 문자열을 검색 중이며 정규식을 사용했다는 점을 기억하세요. [Match.NextMatch](xref:System.Text.RegularExpressions.Match.NextMatch) 메서드를 호출하여 마지막 항목이 종료되면 다른 검색을 수행할 수 있습니다.

## <a name="explicitly-named-captures"></a>명시적으로 명명된 캡처

기존의 정규식에서 캡처링 괄호는 자동으로 순서대로 번호가 매겨집니다. 이 경우에 두 가지 문제가 발생합니다. 첫째, 괄호를 삽입하거나 제거하여 정규식을 수정하면 번호가 매겨진 캡처를 참조하는 모든 코드를 다시 작성하여 새로 지정된 번호를 반영해야 합니다. 둘째, 괄호의 다른 집합을 사용하여 허용 가능한 일치 항목에 두 개의 대체 식을 제공하기 때문에 둘 중 어느 쪽이 결과를 반환했는지 확인하기가 어려울 수 있습니다.

이러한 문제를 해결하기 위해 [Regex](xref:System.Text.RegularExpressions.Regex) 클래스는 `(?<name>…)` 구문이 지정된 슬롯에 일치 항목을 캡처링하도록 지원합니다(문자열 또는 정수를 사용하여 슬롯에 이름을 지정할 수 있음. 정수를 더 신속하게 회수할 수 있음). 따라서 동일한 문자열의 대체 일치 항목은 모두 동일한 위치로 지정될 수 있습니다. 충돌이 발생할 경우 슬롯에 삭제된 마지막 일치 항목이 성공적인 일치입니다. (그러나 단일 슬롯에 대한 여러 일치 항목이 있는 경우 전체 목록을 사용할 수 있습니다. 자세한 내용은 [Group.Captures](xref:System.Text.RegularExpressions.Group.Captures) 컬렉션을 참조하세요.)

## <a name="see-also"></a>참고 항목

[.NET 정규식](regular-expressions.md)

[정규식 예제](regex-examples.md)




<!--HONumber=Nov16_HO1-->


