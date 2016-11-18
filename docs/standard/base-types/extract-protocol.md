---
title: "방법: URL에서 프로토콜 및 포트 번호 추출"
description: "URL에서 프로토콜 및 포트 번호를 추출하는 방법"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 07/28/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: d2462fb4-6d61-44ab-8466-73f1f06c3058
translationtype: Human Translation
ms.sourcegitcommit: fb00da6505c9edb6a49d2003ae9bcb8e74c11d6c
ms.openlocfilehash: 72e0c9401406dcac4eb693b056b88a531f2a0748

---

# <a name="how-to-extract-a-protocol-and-port-number-from-a-url"></a>방법: URL에서 프로토콜 및 포트 번호 추출

다음 예제에서는 URL에서 프로토콜 및 포트 번호를 추출합니다. 

## <a name="example"></a>예제

이 예제에서는 [Match.Result](xref:System.Text.RegularExpressions.Match.Result(System.String)) 메서드를 사용하여 프로토콜, 콜론, 포트 번호로 구성된 값을 반환합니다. 

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string url = "http://www.contoso.com:8080/letters/readme";

      Regex r = new Regex(@"^(?<proto>\w+)://[^/]+?(?<port>:\d+)?/",
                          RegexOptions.None, TimeSpan.FromMilliseconds(150));
      Match m = r.Match(url);
      if (m.Success)
         Console.WriteLine(r.Match(url).Result("${proto}${port}")); 
   }
}
// The example displays the following output:
//       http:8080
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim url As String = "http://www.contoso.com:8080/letters/readme.html" 
      Dim r As New Regex("^(?<proto>\w+)://[^/]+?(?<port>:\d+)?/",
                         RegexOptions.None, TimeSpan.FromMilliseconds(150))

      Dim m As Match = r.Match(url)
      If m.Success Then
         Console.WriteLine(r.Match(url).Result("${proto}${port}"))
      End If   
   End Sub
End Module
' The example displays the following output:
'       http:8080
```

정규식 패턴 `^(?<proto>\w+)://[^/]+?(?<port>:\d+)?/`는 다음 표와 같이 해석할 수 있습니다.

패턴 | 설명
------- | ----------- 
`^` | 문자열의 시작 부분에서 일치 항목 찾기를 시작합니다.
`(?<proto>\w+)` | 하나 이상의 단어 문자를 찾습니다. 이 그룹의 이름을 proto로 지정합니다.
`://` | 콜론과 두 개의 슬래시 기호를 찾습니다.
`[^/]+?` | 슬래시 기호 이외의 문자를 한 번 이상(가능한 적게) 찾습니다.
`(?<port>:\d+)?` | 하나 이상의 숫자 문자 앞에 나오는 콜론을 0번 또는 한 번 찾습니다. 이 그룹의 이름을 port로 지정합니다.
`/` | 슬래시 기호를 찾습니다.
 
[Match.Result](xref:System.Text.RegularExpressions.Match.Result(System.String)) 메서드는 정규식 패턴에 캡처된 두 개의 명명된 그룹 값을 연결하는 `${proto}${port}` 대체 시퀀스를 확장합니다. 이 메서드는 [Match.Groups](xref:System.Text.RegularExpressions.Match.Groups) 속성에 반환된 컬렉션 개체에서 검색된 문자열을 명시적으로 연결하는 편리한 대체 방법입니다.

이 예제에서는 [Match.Result](xref:System.Text.RegularExpressions.Match.Result(System.String)) 메서드를 `${proto}` 및 `${port}`라는 두 개의 대체와 함께 사용하여 캡처된 그룹을 출력 문자열에 포함합니다. 대신, 다음 코드와 같이 일치 항목의 [GroupCollection](xref:System.Text.RegularExpressions.GroupCollection) 개체에서 캡처된 그룹을 검색할 수도 있습니다.

```csharp
Console.WriteLine(m.Groups["proto"].Value + m.Groups["port"].Value); 
```

```vb
Console.WriteLine(m.Groups("proto").Value + m.Groups("port").Value)
```

## <a name="see-also"></a>참고 항목

[.NET 정규식](regular-expressions.md)

[정규식 예제](regex-examples.md)



<!--HONumber=Nov16_HO3-->


