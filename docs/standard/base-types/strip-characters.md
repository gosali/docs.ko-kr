---
title: "방법: 문자열에서 유효하지 않은 문자 제거"
description: "문자열에서 유효하지 않은 문자 제거하는 방법"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 07/28/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: f1df4967-7887-41d2-b60f-0da9be67c8fa
translationtype: Human Translation
ms.sourcegitcommit: fb00da6505c9edb6a49d2003ae9bcb8e74c11d6c
ms.openlocfilehash: 2d5b0756ab8c34cca0c4ca7c1eb73f81a7a10b70

---

# <a name="how-to-strip-invalid-characters-from-a-string"></a>방법: 문자열에서 유효하지 않은 문자 제거

다음 예제에서는 고정 [Regex.Replace](xref:System.Text.RegularExpressions.Regex.Replace(System.String,System.String,System.String,System.Text.RegularExpressions.RegexOptions,System.TimeSpan)) 메서드를 사용하여 문자열에서 잘못된 문자를 제거합니다. 

## <a name="example"></a>예제

이 예제에 정의된 `CleanInput` 메서드가 사용하여 사용자 입력을 허용하는 텍스트 필드에 입력한 문제가 될 수 있는 문자를 제거할 수 있습니다. 이 경우에 `CleanInput`은 마침표(.), 기호((@),), 하이픈(-)을 제외한 모든 영숫자가 아닌 문자를 제거하고 나머지 문자열을 반환합니다. 그러나 입력 문자열에 포함되어야 하는 모든 문자를 제거하도록 정규식 패턴을 수정할 수 있습니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
    static string CleanInput(string strIn)
    {
        // Replace invalid characters with empty strings.
        try {
           return Regex.Replace(strIn, @"[^\w\.@-]", "", 
                                RegexOptions.None, TimeSpan.FromSeconds(1.5)); 
        }
        // If we timeout when replacing invalid characters, 
        // we should return Empty.
        catch (RegexMatchTimeoutException) {
           return String.Empty;   
        }
    }
}
```

```vb
Imports System.Text.RegularExpressions

Module Example
    Function CleanInput(strIn As String) As String
        ' Replace invalid characters with empty strings.
        Try
           Return Regex.Replace(strIn, "[^\w\.@-]", "")
        ' If we timeout when replacing invalid characters, 
        ' we should return String.Empty.
        Catch e As RegexMatchTimeoutException
           Return String.Empty         
        End Try
    End Function
End Module
```

정규식 패턴 `[^\w\.@-]`은 단어 문자, 마침표, @ 기호 또는 하이픈이 아닌 모든 문자를 찾습니다. 단어 문자는 문자, 숫자 또는 밑줄과 같은 문장 부호입니다. 이 패턴과 일치하는 모든 문자는 바꾸기 패턴에 정의된 [String.Empty](xref:System.String.Empty) 문자열로 바뀝니다. 사용자 입력에서 추가 문자를 허용하려면 해당 문자를 정규식 패턴의 문자 클래스에 추가합니다. 예를 들어, 정규식 패턴 `[^\w\.@-\\%]`도 입력 문자열에 백분율 기호 및 백슬래시를 허용합니다.

## <a name="see-also"></a>참고 항목

[.NET 정규식](regular-expressions.md)

[정규식 예제](regex-examples.md)



<!--HONumber=Nov16_HO3-->


