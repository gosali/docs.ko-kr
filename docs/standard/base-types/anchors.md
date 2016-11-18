---
title: "정규식의 앵커"
description: "정규식의 앵커"
keywords: .NET, .NET Core
author: stevehoag
ms.author: shoag
manager: wpickett
ms.date: 07/28/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 96dff1be-3005-4ba5-af1b-323182a26085
translationtype: Human Translation
ms.sourcegitcommit: b20713600d7c3ddc31be5885733a1e8910ede8c6
ms.openlocfilehash: ef2a63115f1efbe2418c348a3379fe7dd2face86

---

# <a name="anchors-in-regular-expressions"></a>정규식의 앵커

앵커 또는 원자성 너비가 0인 어설션은 문자열에서 일치 항목이 나타나야 하는 위치를 지정합니다. 검색 식에서 앵커를 사용하면 정규식 엔진은 문자열을 통과하거나 문자를 사용하지 않고, 지정된 위치에서만 일치 항목을 검색합니다. 예를 들어 **^** 기호는 줄 또는 문자열의 시작 부분에서 일치 항목 찾기를 시작하도록 지정합니다. 따라서 정규식 `^http:`은 줄의 시작 부분에 나타날 때만 "http:"을 찾습니다. 다음 표에서는 .NET의 정규식에서 지원하는 앵커를 보여 줍니다. 

앵커 | 설명
------ | ----------- 
**^** | 일치 항목이 문자열 또는 줄의 시작 부분에서 시작해야 합니다.
**$** | 문자열 또는 줄의 끝 부분이나 문자열 또는 줄의 끝에 있는 \n 앞에서 일치 항목 찾기를 수행해야 합니다.
**\A** | 문자열의 시작 부분에서만 일치 항목 찾기를 수행해야 합니다(여러 줄 지원 없음).
**\Z** | 문자열의 끝 부분이나 문자열의 끝에 있는 \n 앞에서 일치 항목 찾기를 수행해야 합니다.
**\z** | 일치 항목이 문자열의 끝 부분에만 있어야 합니다. 
**\G** | 일치 항목이 이전 일치 항목이 종료된 위치에서 시작해야 합니다.
**\b** | 일치 항목이 단어 경계에 있어야 합니다.
**\B** | 일치 항목이 단어 경계에 있으면 안 됩니다.
 
## <a name="start-of-string-or-line-"></a>문자열 또는 줄의 시작: ^

**^** 앵커는 다음 패턴이 문자열의 첫 번째 문자 위치에서 시작하도록 지정합니다. **^**를 [RegexOptions.Multiline](xref:System.Text.RegularExpressions.RegexOptions.Multiline) 옵션과 함께 사용하는 경우([정규식 옵션](options.md) 참조) 일치 항목이 각 줄의 시작 부분에서 발생해야 합니다.

다음 예제에서는 일부 전문 야구팀이 있던 기간(년)에 대한 정보를 추출하는 정규식에서 **^** 앵커를 사용합니다. 이 예제에서는 `Regex.Matches` 메서드의 오버로드 두 개를 호출합니다. 

* [Matches(String, String)](xref:System.Text.RegularExpressions.Regex.Matches(System.String,System.String)) 오버로드 호출은 입력 문자열에서 정규식 패턴과 일치하는 첫 번째 부분 문자열만 찾습니다. 

* options 매개 변수를 [RegexOptions.Multiline](xref:System.Text.RegularExpressions.RegexOptions.Multiline)으로 설정하여 [Matches(String, String, RegexOptions)](xref:System.Text.RegularExpressions.Regex.Matches(System.String,System.String,System.Text.RegularExpressions.RegexOptions)) 오버로드를 호출하면 부분 문자열 5개를 모두 찾습니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      int startPos = 0, endPos = 70;
      string input = "Brooklyn Dodgers, National League, 1911, 1912, 1932-1957\n" +
                     "Chicago Cubs, National League, 1903-present\n" + 
                     "Detroit Tigers, American League, 1901-present\n" + 
                     "New York Giants, National League, 1885-1957\n" +  
                     "Washington Senators, American League, 1901-1960\n";   
      string pattern = @"^((\w+(\s?)){2,}),\s(\w+\s\w+),(\s\d{4}(-(\d{4}|present))?,?)+";
      Match match;

      if (input.Substring(startPos, endPos).Contains(",")) {
         match = Regex.Match(input, pattern);
         while (match.Success) {
            Console.Write("The {0} played in the {1} in", 
                              match.Groups[1].Value, match.Groups[4].Value);
            foreach (Capture capture in match.Groups[5].Captures)
               Console.Write(capture.Value);

            Console.WriteLine(".");
            startPos = match.Index + match.Length;
            endPos = startPos + 70 <= input.Length ? 70 : input.Length - startPos;
            if (! input.Substring(startPos, endPos).Contains(",")) break;
            match = match.NextMatch();
         }
         Console.WriteLine();
      }

      if (input.Substring(startPos, endPos).Contains(",")) {
         match = Regex.Match(input, pattern, RegexOptions.Multiline);
         while (match.Success) {
            Console.Write("The {0} played in the {1} in", 
                              match.Groups[1].Value, match.Groups[4].Value);
            foreach (Capture capture in match.Groups[5].Captures)
               Console.Write(capture.Value);

            Console.WriteLine(".");
            startPos = match.Index + match.Length;
            endPos = startPos + 70 <= input.Length ? 70 : input.Length - startPos;
            if (! input.Substring(startPos, endPos).Contains(",")) break;
            match = match.NextMatch();
         }
         Console.WriteLine();
      }
   }
}
// The example displays the following output:
//    The Brooklyn Dodgers played in the National League in 1911, 1912, 1932-1957.
//    
//    The Brooklyn Dodgers played in the National League in 1911, 1912, 1932-1957.
//    The Chicago Cubs played in the National League in 1903-present.
//    The Detroit Tigers played in the American League in 1901-present.
//    The New York Giants played in the National League in 1885-1957.
//    The Washington Senators played in the American League in 1901-1960.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim startPos As Integer = 0
      Dim endPos As Integer = 70
      Dim input As String = "Brooklyn Dodgers, National League, 1911, 1912, 1932-1957" + vbCrLf + _
                            "Chicago Cubs, National League, 1903-present" + vbCrLf + _
                            "Detroit Tigers, American League, 1901-present" + vbCrLf + _
                            "New York Giants, National League, 1885-1957" + vbCrLf + _
                            "Washington Senators, American League, 1901-1960" + vbCrLf  

      Dim pattern As String = "^((\w+(\s?)){2,}),\s(\w+\s\w+),(\s\d{4}(-(\d{4}|present))?,?)+"
      Dim match As Match

      ' Provide minimal validation in the event the input is invalid.
      If input.Substring(startPos, endPos).Contains(",") Then
         match = Regex.Match(input, pattern)
         Do While match.Success
            Console.Write("The {0} played in the {1} in", _
                              match.Groups(1).Value, match.Groups(4).Value)
            For Each capture As Capture In match.Groups(5).Captures
               Console.Write(capture.Value)
            Next
            Console.WriteLine(".")
            startPos = match.Index + match.Length 
            endPos = CInt(IIf(startPos + 70 <= input.Length, 70, input.Length - startPos))
            If Not input.Substring(startPos, endPos).Contains(",") Then Exit Do
            match = match.NextMatch()            
         Loop
         Console.WriteLine()                               
      End If      

      startPos = 0
      endPos = 70
      If input.Substring(startPos, endPos).Contains(",") Then
         match = Regex.Match(input, pattern, RegexOptions.Multiline)
         Do While match.Success
            Console.Write("The {0} played in the {1} in", _
                              match.Groups(1).Value, match.Groups(4).Value)
            For Each capture As Capture In match.Groups(5).Captures
               Console.Write(capture.Value)
            Next
            Console.WriteLine(".")
            startPos = match.Index + match.Length 
            endPos = CInt(IIf(startPos + 70 <= input.Length, 70, input.Length - startPos))
            If Not input.Substring(startPos, endPos).Contains(",") Then Exit Do
            match = match.NextMatch()            
         Loop
         Console.WriteLine()                               
      End If      


'       For Each match As Match in Regex.Matches(input, pattern, RegexOptions.Multiline)
'          Console.Write("The {0} played in the {1} in", _
'                            match.Groups(1).Value, match.Groups(4).Value)
'          For Each capture As Capture In match.Groups(5).Captures
'             Console.Write(capture.Value)
'          Next
'          Console.WriteLine(".")
'       Next
   End Sub
End Module
' The example displays the following output:
'    The Brooklyn Dodgers played in the National League in 1911, 1912, 1932-1957.
'    
'    The Brooklyn Dodgers played in the National League in 1911, 1912, 1932-1957.
'    The Chicago Cubs played in the National League in 1903-present.
'    The Detroit Tigers played in the American League in 1901-present.
'    The New York Giants played in the National League in 1885-1957.
'    The Washington Senators played in the American League in 1901-1960.
```

정규식 패턴 `^((\w+(\s?)){2,}),\s(\w+\s\w+),(\s\d{4}(-(\d{4}|present))?,?)+`는 다음 테이블과 같이 정의됩니다.

패턴 | 설명
------- | ----------- 
`^` | 입력 문자열의 시작 부분(또는 메서드가 `RegexOptions.Multiline` 옵션과 함께 호출될 경우 줄의 시작 부분)에서 일치 항목 찾기를 시작합니다.
`((\w+(\s?)){2,}` | 단어 문자 하나 이상과 0 또는 공백 하나 순으로 정확히 두 번 나타내는 일치 항목을 찾습니다. 이 그룹은 첫 번째 캡처링 그룹입니다. 이 식은 두 번째 및 세 번째 캡처링 그룹도 정의합니다. 두 번째 캡처 도구는 캡처된 단어로 구성되고 세 번째는 캡처된 공백으로 구성됩니다. 
`,\s` | 쉼표, 공백 문자 순의 일치 항목을 찾습니다.
`(\w+\s\w+)` | 단어 문자 하나 이상, 공백, 단어 문자 하나 이상 순의 일치 항목을 찾습니다. 이 그룹은 네 번째 캡처링 그룹입니다.
`,` | 쉼표 하나를 찾습니다.
`\s\d{4}` | 공백, 10 진수 4개 순의 일치 항목을 찾습니다.
`(-(\d{4}`&#124;`present))?` |  하이픈과 10진수 4개 또는 문자열 "present" 순으로 나타나는 일치 항목 0개 또는 하나를 찾습니다. 이 그룹은 6번째 캡처 그룹입니다. 7번째 캡처 그룹도 포함됩니다. 
`,?` | 쉼표 0개 또는 1개를 찾습니다.
`(\s\d{4}(-(\d{4}`&#124;`present))?,?)+` | 공백, 10진수 4개, 하이픈과 10진수 4개 또는 문자열 "present", 쉼표 0개 또는 하나 순으로 나타나는 일치 항목 하나 이상을 찾습니다. 이 그룹은 5번째 캡처 그룹입니다.
 
## <a name="end-of-string-or-line-"></a>문자열 또는 줄의 끝: $

**$** 앵커는 입력 문자열의 끝 부분이나 입력 문자열의 끝에 있는 \n 앞에서 선행 패턴을 수행하도록 지정합니다. 

[RegexOptions.Multiline](xref:System.Text.RegularExpressions.RegexOptions.Multiline) 옵션과 함께 **$**를 사용하면 줄의 끝 부분에서 일치 항목을 찾을 수도 있습니다. **$**는 **\n**과 일치하지만 **\r\n**(캐리지 리턴 및 줄 바꿈 문자 조합 또는 CR/LF)과는 일치하지 않습니다. CR/LF 문자 조합을 찾으려면 정규식 패턴에 **\r?$**를 포함합니다.

다음 예제에서는 **$** 앵커를 이전 "문자열 또는 줄의 시작" 섹션의 예제에서 사용된 정규식 패턴에 추가합니다. 텍스트 5줄을 포함하는 원래 입력 문자열에서 사용될 경우 [Regex.Matches(String, String)](xref:System.Text.RegularExpressions.Regex.Matches(System.String,System.String)) 메서드는 일치 항목을 찾을 수 없습니다. 이는 첫째 줄의 끝 부분이 **$** 패턴과 일치하지 않기 때문입니다. 원래 입력 문자열을 문자열 배열로 분할하면 `Regex.Matches(String, String)` 메서드는 5줄의 각 줄을 찾는 데 성공합니다. *options* 매개 변수를 `RegexOptions.Multiline`으로 설정하여 [Regex.Matches(String, String, RegexOptions)](xref:System.Text.RegularExpressions.Regex.Matches(System.String,System.String,System.Text.RegularExpressions.RegexOptions)) 메서드를 호출하면 정규식 패턴에서 캐리지 리턴 요소(\u+000D)를 고려하지 않으므로 일치 항목이 발견되지 않습니다. 그러나 **$**를 **\r?$**로 바꿔서 정규식 패턴을 수정할 경우 *options* 매개 변수를 `RegexOptions.Multiline`으로 설정하여 `Regex.Matches(String, String, RegexOptions)` 메서드를 다시 호출하면 일치 항목 5개가 발견됩니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      int startPos = 0, endPos = 70;
      string cr = Environment.NewLine;
      string input = "Brooklyn Dodgers, National League, 1911, 1912, 1932-1957" + cr +
                     "Chicago Cubs, National League, 1903-present" + cr + 
                     "Detroit Tigers, American League, 1901-present" + cr + 
                     "New York Giants, National League, 1885-1957" + cr +  
                     "Washington Senators, American League, 1901-1960" + cr;   
      Match match;

      string basePattern = @"^((\w+(\s?)){2,}),\s(\w+\s\w+),(\s\d{4}(-(\d{4}|present))?,?)+";
      string pattern = basePattern + "$";
      Console.WriteLine("Attempting to match the entire input string:");
      if (input.Substring(startPos, endPos).Contains(",")) {
         match = Regex.Match(input, pattern);
         while (match.Success) {
            Console.Write("The {0} played in the {1} in", 
                              match.Groups[1].Value, match.Groups[4].Value);
            foreach (Capture capture in match.Groups[5].Captures)
               Console.Write(capture.Value);

            Console.WriteLine(".");
            startPos = match.Index + match.Length;
            endPos = startPos + 70 <= input.Length ? 70 : input.Length - startPos;
            if (! input.Substring(startPos, endPos).Contains(",")) break;
            match = match.NextMatch();
         }
         Console.WriteLine();
      }

      string[] teams = input.Split(new String[] { cr }, StringSplitOptions.RemoveEmptyEntries);
      Console.WriteLine("Attempting to match each element in a string array:");
      foreach (string team in teams)
      {
         if (team.Length > 70) continue;

         match = Regex.Match(team, pattern);
         if (match.Success)
         {
            Console.Write("The {0} played in the {1} in", 
                          match.Groups[1].Value, match.Groups[4].Value);
            foreach (Capture capture in match.Groups[5].Captures)
               Console.Write(capture.Value);
            Console.WriteLine(".");
         }
      }
      Console.WriteLine();

      startPos = 0;
      endPos = 70;
      Console.WriteLine("Attempting to match each line of an input string with '$':");
      if (input.Substring(startPos, endPos).Contains(",")) {
         match = Regex.Match(input, pattern, RegexOptions.Multiline);
         while (match.Success) {
            Console.Write("The {0} played in the {1} in", 
                              match.Groups[1].Value, match.Groups[4].Value);
            foreach (Capture capture in match.Groups[5].Captures)
               Console.Write(capture.Value);

            Console.WriteLine(".");
            startPos = match.Index + match.Length;
            endPos = startPos + 70 <= input.Length ? 70 : input.Length - startPos;
            if (! input.Substring(startPos, endPos).Contains(",")) break;
            match = match.NextMatch();
         }
         Console.WriteLine();
      }

      startPos = 0;
      endPos = 70;
      pattern = basePattern + "\r?$"; 
      Console.WriteLine(@"Attempting to match each line of an input string with '\r?$':");
      if (input.Substring(startPos, endPos).Contains(",")) {
         match = Regex.Match(input, pattern, RegexOptions.Multiline);
         while (match.Success) {
            Console.Write("The {0} played in the {1} in", 
                              match.Groups[1].Value, match.Groups[4].Value);
            foreach (Capture capture in match.Groups[5].Captures)
               Console.Write(capture.Value);

            Console.WriteLine(".");
            startPos = match.Index + match.Length;
            endPos = startPos + 70 <= input.Length ? 70 : input.Length - startPos;
            if (! input.Substring(startPos, endPos).Contains(",")) break;
            match = match.NextMatch();
         }
         Console.WriteLine();
      }
   }
}
// The example displays the following output:
//    Attempting to match the entire input string:
//    
//    Attempting to match each element in a string array:
//    The Brooklyn Dodgers played in the National League in 1911, 1912, 1932-1957.
//    The Chicago Cubs played in the National League in 1903-present.
//    The Detroit Tigers played in the American League in 1901-present.
//    The New York Giants played in the National League in 1885-1957.
//    The Washington Senators played in the American League in 1901-1960.
//    
//    Attempting to match each line of an input string with '$':
//    
//    Attempting to match each line of an input string with '\r+$':
//    The Brooklyn Dodgers played in the National League in 1911, 1912, 1932-1957.
//    The Chicago Cubs played in the National League in 1903-present.
//    The Detroit Tigers played in the American League in 1901-present.
//    The New York Giants played in the National League in 1885-1957.
//    The Washington Senators played in the American League in 1901-1960.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim startPos As Integer = 0
      Dim endPos As Integer = 70
      Dim input As String = "Brooklyn Dodgers, National League, 1911, 1912, 1932-1957" + vbCrLf + _
                            "Chicago Cubs, National League, 1903-present" + vbCrLf + _
                            "Detroit Tigers, American League, 1901-present" + vbCrLf + _
                            "New York Giants, National League, 1885-1957" + vbCrLf + _
                            "Washington Senators, American League, 1901-1960" + vbCrLf  

      Dim basePattern As String = "^((\w+(\s?)){2,}),\s(\w+\s\w+),(\s\d{4}(-(\d{4}|present))?,?)+"
      Dim match As Match

      Dim pattern As String = basePattern + "$"
      Console.WriteLine("Attempting to match the entire input string:")
      ' Provide minimal validation in the event the input is invalid.
      If input.Substring(startPos, endPos).Contains(",") Then
         match = Regex.Match(input, pattern)
         Do While match.Success
            Console.Write("The {0} played in the {1} in", _
                              match.Groups(1).Value, match.Groups(4).Value)
            For Each capture As Capture In match.Groups(5).Captures
               Console.Write(capture.Value)
            Next
            Console.WriteLine(".")
            startPos = match.Index + match.Length 
            endPos = CInt(IIf(startPos + 70 <= input.Length, 70, input.Length - startPos))
            If Not input.Substring(startPos, endPos).Contains(",") Then Exit Do
            match = match.NextMatch()            
         Loop
         Console.WriteLine()                               
      End If      

      Dim teams() As String = input.Split(New String() { vbCrLf }, StringSplitOptions.RemoveEmptyEntries)
      Console.WriteLine("Attempting to match each element in a string array:")
      For Each team As String In teams
         If team.Length > 70 Then Continue For
         match = Regex.Match(team, pattern)
         If match.Success Then
            Console.Write("The {0} played in the {1} in", _
                           match.Groups(1).Value, match.Groups(4).Value)
            For Each capture As Capture In match.Groups(5).Captures
               Console.Write(capture.Value)
            Next
            Console.WriteLine(".")
         End If
      Next
      Console.WriteLine()

      startPos = 0
      endPos = 70
      Console.WriteLine("Attempting to match each line of an input string with '$':")
      ' Provide minimal validation in the event the input is invalid.
      If input.Substring(startPos, endPos).Contains(",") Then
         match = Regex.Match(input, pattern, RegexOptions.Multiline)
         Do While match.Success
            Console.Write("The {0} played in the {1} in", _
                              match.Groups(1).Value, match.Groups(4).Value)
            For Each capture As Capture In match.Groups(5).Captures
               Console.Write(capture.Value)
            Next
            Console.WriteLine(".")
            startPos = match.Index + match.Length 
            endPos = CInt(IIf(startPos + 70 <= input.Length, 70, input.Length - startPos))
            If Not input.Substring(startPos, endPos).Contains(",") Then Exit Do
            match = match.NextMatch()            
         Loop
         Console.WriteLine()                               
      End If      


      startPos = 0
      endPos = 70
      pattern = basePattern + "\r?$" 
      Console.WriteLine("Attempting to match each line of an input string with '\r?$':")
      ' Provide minimal validation in the event the input is invalid.
      If input.Substring(startPos, endPos).Contains(",") Then
         match = Regex.Match(input, pattern, RegexOptions.Multiline)
         Do While match.Success
            Console.Write("The {0} played in the {1} in", _
                              match.Groups(1).Value, match.Groups(4).Value)
            For Each capture As Capture In match.Groups(5).Captures
               Console.Write(capture.Value)
            Next
            Console.WriteLine(".")
            startPos = match.Index + match.Length 
            endPos = CInt(IIf(startPos + 70 <= input.Length, 70, input.Length - startPos))
            If Not input.Substring(startPos, endPos).Contains(",") Then Exit Do
            match = match.NextMatch()            
         Loop
         Console.WriteLine()                               
      End If      
   End Sub
End Module
' The example displays the following output:
'    Attempting to match the entire input string:
'    
'    Attempting to match each element in a string array:
'    The Brooklyn Dodgers played in the National League in 1911, 1912, 1932-1957.
'    The Chicago Cubs played in the National League in 1903-present.
'    The Detroit Tigers played in the American League in 1901-present.
'    The New York Giants played in the National League in 1885-1957.
'    The Washington Senators played in the American League in 1901-1960.
'    
'    Attempting to match each line of an input string with '$':
'    
'    Attempting to match each line of an input string with '\r+$':
'    The Brooklyn Dodgers played in the National League in 1911, 1912, 1932-1957.
'    The Chicago Cubs played in the National League in 1903-present.
'    The Detroit Tigers played in the American League in 1901-present.
'    The New York Giants played in the National League in 1885-1957.
'    The Washington Senators played in the American League in 1901-1960.
```

## <a name="start-of-string-only-a"></a>문자열의 시작만: \A

**\A** 앵커는 일치 항목 찾기가 입력 문자열의 시작 부분에서 수행되도록 지정합니다. **\A**는 [RegexOptions.Multiline](xref:System.Text.RegularExpressions.RegexOptions.Multiline) 옵션을 무시한다는 점을 제외하고 **^** 앵커와 동일합니다. 따라서 여러 줄 입력 문자열에서 첫 번째 줄의 시작 부분만 찾을 수 있습니다.

다음 예제는 **^** 및 **$** 앵커에 대한 예제와 비슷합니다. 일부 전문 야구팀이 있던 기간(년)에 대한 정보를 추출하는 정규식에서 **\A** 앵커를 사용합니다. 입력 문자열은 5줄을 포함합니다. [Regex.Matches(String, String, RegexOptions)](xref:System.Text.RegularExpressions.Regex.Matches(System.String,System.String,System.Text.RegularExpressions.RegexOptions)) 메서드 호출은 입력 문자열에서 정규식 패턴과 일치하는 첫 번째 부분 문자열만 찾습니다. 예제에서 볼 수 있듯이 `Multiline` 옵션은 아무 영향도 주지 않습니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      int startPos = 0, endPos = 70;
      string input = "Brooklyn Dodgers, National League, 1911, 1912, 1932-1957\n" +
                     "Chicago Cubs, National League, 1903-present\n" + 
                     "Detroit Tigers, American League, 1901-present\n" + 
                     "New York Giants, National League, 1885-1957\n" +  
                     "Washington Senators, American League, 1901-1960\n";   

      string pattern = @"\A((\w+(\s?)){2,}),\s(\w+\s\w+),(\s\d{4}(-(\d{4}|present))?,?)+";
      Match match;

      if (input.Substring(startPos, endPos).Contains(",")) {
         match = Regex.Match(input, pattern, RegexOptions.Multiline);
         while (match.Success) {
            Console.Write("The {0} played in the {1} in", 
                              match.Groups[1].Value, match.Groups[4].Value);
            foreach (Capture capture in match.Groups[5].Captures)
               Console.Write(capture.Value);

            Console.WriteLine(".");
            startPos = match.Index + match.Length;
            endPos = startPos + 70 <= input.Length ? 70 : input.Length - startPos;
            if (! input.Substring(startPos, endPos).Contains(",")) break;
            match = match.NextMatch();
         }
         Console.WriteLine();
      }
   }
}
// The example displays the following output:
//    The Brooklyn Dodgers played in the National League in 1911, 1912, 1932-1957.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim startPos As Integer = 0
      Dim endPos As Integer = 70
      Dim input As String = "Brooklyn Dodgers, National League, 1911, 1912, 1932-1957" + vbCrLf + _
                            "Chicago Cubs, National League, 1903-present" + vbCrLf + _
                            "Detroit Tigers, American League, 1901-present" + vbCrLf + _
                            "New York Giants, National League, 1885-1957" + vbCrLf + _
                            "Washington Senators, American League, 1901-1960" + vbCrLf  

      Dim pattern As String = "\A((\w+(\s?)){2,}),\s(\w+\s\w+),(\s\d{4}(-(\d{4}|present))?,?)+"
      Dim match As Match

      ' Provide minimal validation in the event the input is invalid.
      If input.Substring(startPos, endPos).Contains(",") Then
         match = Regex.Match(input, pattern, RegexOptions.Multiline)
         Do While match.Success
            Console.Write("The {0} played in the {1} in", _
                              match.Groups(1).Value, match.Groups(4).Value)
            For Each capture As Capture In match.Groups(5).Captures
               Console.Write(capture.Value)
            Next
            Console.WriteLine(".")
            startPos = match.Index + match.Length 
            endPos = CInt(IIf(startPos + 70 <= input.Length, 70, input.Length - startPos))
            If Not input.Substring(startPos, endPos).Contains(",") Then Exit Do
            match = match.NextMatch()            
         Loop
         Console.WriteLine()                               
      End If      
   End Sub   
End Module
' The example displays the following output:
'    The Brooklyn Dodgers played in the National League in 1911, 1912, 1932-1957.
```

## <a name="end-of-string-or-before-ending-newline-z"></a>문자열의 끝 또는 줄 바꿈 종료 전: \Z

**\Z** 앵커는 입력 문자열의 끝 부분이나 입력 문자열의 끝에 있는 **\n** 앞에서 일치 항목 찾기를 수행하도록 지정합니다. **\Z**는 [RegexOptions.Multiline](xref:System.Text.RegularExpressions.RegexOptions.Multiline) 옵션을 무시한다는 점을 제외하고 **$** 앵커와 동일합니다. 따라서 여러 줄 문자열에서는 마지막 줄의 끝이나 **\n** 앞의 마지막 줄만 찾을 수 있습니다.

**\Z**는 **\n**과 일치하지만 **\r\n**(CR/LF 문자 조합)과는 일치하지 않습니다. CR/LF를 찾으려면 정규식 패턴에 **\r?\Z**를 포함합니다.

다음 예제에서는 일부 전문 야구팀이 있던 기간(년)에 대한 정보를 추출하는 이전 "문자열 또는 줄의 시작" 섹션의 예제와 비슷한 정규식에서 **\Z** 앵커를 사용합니다. 정규식 `^((\w+(\s?)){2,}),\s(\w+\s\w+),(\s\d{4}(-(\d{4}|present))?,?)+\r?\Z`의 하위 식 `\r?\Z`는 문자열 끝과 일치하고 **\n** 또는 **\r\n**으로 끝나는 문자열과도 일치합니다. 따라서 배열의 각 요소는 정규식 패턴과 일치합니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string[] inputs = { "Brooklyn Dodgers, National League, 1911, 1912, 1932-1957",  
                          "Chicago Cubs, National League, 1903-present" + Environment.NewLine, 
                          "Detroit Tigers, American League, 1901-present" + Regex.Unescape(@"\n"), 
                          "New York Giants, National League, 1885-1957", 
                          "Washington Senators, American League, 1901-1960" + Environment.NewLine}; 
      string pattern = @"^((\w+(\s?)){2,}),\s(\w+\s\w+),(\s\d{4}(-(\d{4}|present))?,?)+\r?\Z";

      foreach (string input in inputs)
      {
         if (input.Length > 70 || ! input.Contains(",")) continue;

         Console.WriteLine(Regex.Escape(input));
         Match match = Regex.Match(input, pattern);
         if (match.Success)
            Console.WriteLine("   Match succeeded.");
         else
            Console.WriteLine("   Match failed.");
      }   
   }
}
// The example displays the following output:
//    Brooklyn\ Dodgers,\ National\ League,\ 1911,\ 1912,\ 1932-1957
//       Match succeeded.
//    Chicago\ Cubs,\ National\ League,\ 1903-present\r\n
//       Match succeeded.
//    Detroit\ Tigers,\ American\ League,\ 1901-present\n
//       Match succeeded.
//    New\ York\ Giants,\ National\ League,\ 1885-1957
//       Match succeeded.
//    Washington\ Senators,\ American\ League,\ 1901-1960\r\n
//       Match succeeded.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim inputs() As String = { "Brooklyn Dodgers, National League, 1911, 1912, 1932-1957",  _
                            "Chicago Cubs, National League, 1903-present" + vbCrLf, _
                            "Detroit Tigers, American League, 1901-present" + vbLf, _
                            "New York Giants, National League, 1885-1957", _
                            "Washington Senators, American League, 1901-1960" + vbCrLf }  
      Dim pattern As String = "^((\w+(\s?)){2,}),\s(\w+\s\w+),(\s\d{4}(-(\d{4}|present))?,?)+\r?\Z"

      For Each input As String In inputs
         If input.Length > 70 Or Not input.Contains(",") Then Continue For

         Console.WriteLine(Regex.Escape(input))
         Dim match As Match = Regex.Match(input, pattern)
         If match.Success Then
            Console.WriteLine("   Match succeeded.")
         Else
            Console.WriteLine("   Match failed.")
         End If
      Next   
   End Sub
End Module
' The example displays the following output:
'    Brooklyn\ Dodgers,\ National\ League,\ 1911,\ 1912,\ 1932-1957
'       Match succeeded.
'    Chicago\ Cubs,\ National\ League,\ 1903-present\r\n
'       Match succeeded.
'    Detroit\ Tigers,\ American\ League,\ 1901-present\n
'       Match succeeded.
'    New\ York\ Giants,\ National\ League,\ 1885-1957
'       Match succeeded.
'    Washington\ Senators,\ American\ League,\ 1901-1960\r\n
'       Match succeeded.
```

## <a name="end-of-string-only-z"></a>문자열의 끝만: \z

**\z** 앵커는 입력 문자열의 끝 부분에서 일치 항목 찾기를 수행하도록 지정합니다. **$** 언어 요소와 같이 **\z**는 [RegexOptions.Multiline](xref:System.Text.RegularExpressions.RegexOptions.Multiline) 옵션을 무시합니다. **\Z** 언어 요소와 달리 **\z**는 문자열의 끝에서 **\n** 문자를 찾지 않습니다. 따라서 입력 문자열의 마지막 줄만 찾을 수 있습니다.

다음 예제에서는 일부 전문 야구팀이 있던 기간(년)에 대한 정보를 추출하는 이전 섹션의 예제와 동일한 정규식에서 **\z** 앵커를 사용합니다. 예제에서는 정규식 패턴 `^((\w+(\s?)){2,}),\s(\w+\s\w+),(\s\d{4}(-(\d{4}|present))?,?)+\r?\z`를 사용하여 문자열 배열에서 요소 5개를 각각 찾으려고 합니다. 문자열 중 두 개는 캐리지 리턴 및 줄 바꿈 문자로 끝나고, 하나는 줄 바꿈 문자로 끝나고, 두 개는 캐리지 리턴과 줄 바꿈 문자가 둘 다 없이 끝납니다. 출력과 같이 캐리지 리턴이나 줄 바꿈 문자가 없는 문자열만 패턴과 일치합니다. 

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string[] inputs = { "Brooklyn Dodgers, National League, 1911, 1912, 1932-1957", 
                          "Chicago Cubs, National League, 1903-present" + Environment.NewLine,
                          "Detroit Tigers, American League, 1901-present\\r",
                          "New York Giants, National League, 1885-1957",
                          "Washington Senators, American League, 1901-1960" + Environment.NewLine };  
      string pattern = @"^((\w+(\s?)){2,}),\s(\w+\s\w+),(\s\d{4}(-(\d{4}|present))?,?)+\r?\z";

      foreach (string input in inputs)
      {
         if (input.Length > 70 || ! input.Contains(",")) continue;

         Console.WriteLine(Regex.Escape(input));
         Match match = Regex.Match(input, pattern);
         if (match.Success)
            Console.WriteLine("   Match succeeded.");
         else
            Console.WriteLine("   Match failed.");
      }   
   }
}
// The example displays the following output:
//    Brooklyn\ Dodgers,\ National\ League,\ 1911,\ 1912,\ 1932-1957
//       Match succeeded.
//    Chicago\ Cubs,\ National\ League,\ 1903-present\r\n
//       Match failed.
//    Detroit\ Tigers,\ American\ League,\ 1901-present\n
//       Match failed.
//    New\ York\ Giants,\ National\ League,\ 1885-1957
//       Match succeeded.
//    Washington\ Senators,\ American\ League,\ 1901-1960\r\n
//       Match failed.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim inputs() As String = { "Brooklyn Dodgers, National League, 1911, 1912, 1932-1957",  _
                            "Chicago Cubs, National League, 1903-present" + vbCrLf, _
                            "Detroit Tigers, American League, 1901-present" + vbLf, _
                            "New York Giants, National League, 1885-1957", _
                            "Washington Senators, American League, 1901-1960" + vbCrLf }  
      Dim pattern As String = "^((\w+(\s?)){2,}),\s(\w+\s\w+),(\s\d{4}(-(\d{4}|present))?,?)+\r?\z"

      For Each input As String In inputs
         If input.Length > 70 Or Not input.Contains(",") Then Continue For

         Console.WriteLine(Regex.Escape(input))
         Dim match As Match = Regex.Match(input, pattern)
         If match.Success Then
            Console.WriteLine("   Match succeeded.")
         Else
            Console.WriteLine("   Match failed.")
         End If
      Next   
   End Sub
End Module
' The example displays the following output:
'    Brooklyn\ Dodgers,\ National\ League,\ 1911,\ 1912,\ 1932-1957
'       Match succeeded.
'    Chicago\ Cubs,\ National\ League,\ 1903-present\r\n
'       Match failed.
'    Detroit\ Tigers,\ American\ League,\ 1901-present\n
'       Match failed.
'    New\ York\ Giants,\ National\ League,\ 1885-1957
'       Match succeeded.
'    Washington\ Senators,\ American\ League,\ 1901-1960\r\n
'       Match failed.
```

## <a name="contiguous-matches-g"></a>연속 일치: \G

**\G** 앵커는 이전 일치 항목 찾기가 끝난 지점에서 일치 항목 찾기를 수행하도록 지정합니다. 이 앵커를 [Regex.Matches](xref:System.Text.RegularExpressions.Regex.Matches(System.String)) 또는 [Match.NextMatch](xref:System.Text.RegularExpressions.Match.NextMatch) 메서드와 함께 사용하면 모든 일치 항목이 연속으로 나타납니다. 

다음 예제에서는 정규식을 사용하여 쉼표로 구분된 문자열에서 설치류의 이름을 추출합니다. 

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string input = "capybara,squirrel,chipmunk,porcupine,gopher," + 
                     "beaver,groundhog,hamster,guinea pig,gerbil," + 
                     "chinchilla,prairie dog,mouse,rat";
      string pattern = @"\G(\w+\s?\w*),?";
      Match match = Regex.Match(input, pattern);
      while (match.Success) 
      {
         Console.WriteLine(match.Groups[1].Value);
         match = match.NextMatch();
      } 
   }
}
// The example displays the following output:
//       capybara
//       squirrel
//       chipmunk
//       porcupine
//       gopher
//       beaver
//       groundhog
//       hamster
//       guinea pig
//       gerbil
//       chinchilla
//       prairie dog
//       mouse
//       rat
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim input As String = "capybara,squirrel,chipmunk,porcupine,gopher," + _
                            "beaver,groundhog,hamster,guinea pig,gerbil," + _
                            "chinchilla,prairie dog,mouse,rat"
      Dim pattern As String = "\G(\w+\s?\w*),?"
      Dim match As Match = Regex.Match(input, pattern)
      Do While match.Success
         Console.WriteLine(match.Groups(1).Value)
         match = match.NextMatch()
      Loop 
   End Sub
End Module
' The example displays the following output:
'       capybara
'       squirrel
'       chipmunk
'       porcupine
'       gopher
'       beaver
'       groundhog
'       hamster
'       guinea pig
'       gerbil
'       chinchilla
'       prairie dog
'       mouse
'       rat
```

정규식 `\G(\w+\s?\w*),?`는 다음 테이블과 같이 해석됩니다.

패턴 | 설명
------- | ----------- 
`\G` | 마지막 일치 항목 찾기가 종료된 위치에서 시작합니다.
`\w+` | 하나 이상의 단어 문자를 찾습니다.
`\s?` | 0개 또는 1개의 공백을 찾습니다.
`\w*` | 0개 이상의 단어 문자를 찾습니다.
`(\w+\s?\w*)` | 단어 문자 하나 이상, 공백 0개 이상, 단어 문자 0개 이상 순의 일치 항목을 찾습니다. 이 그룹은 첫 번째 캡처링 그룹입니다.
`,?` | 리터럴 쉼표 문자 0개 또는 하나를 찾습니다.
 
## <a name="word-boundary-b"></a>단어 경계: \b

**\b** 앵커는 단어 문자(**\w** 언어 요소)와 비단어 문자(**\W** 언어 요소) 사이 경계에서 일치 항목 찾기를 수행하도록 지정합니다. 단어 문자는 영숫자 문자 및 밑줄로 구성되고, 비단어 문자는 영숫자나 밑줄이 아닌 문자입니다. (자세한 내용은 [정규식의 문자 클래스](classes.md)를 참조하세요.) 일치 항목은 문자열의 시작 또는 끝의 단어 경계에 있을 수도 있습니다.

**\b** 앵커는 하위 식이 단어의 시작이나 끝이 아닌 전체 단어와 일치하는지 확인하는 데 자주 사용됩니다. 다음 예제의 정규식 `\bare\w*\b`는 이 사용법을 보여 줍니다. 하위 문자열 "are"로 시작하는 단어를 찾습니다. 예제 출력에서는 **\b**가 입력 문자열의 시작 및 끝과 둘 다 일치함을 보여 줍니다. 

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string input = "area bare arena mare";
      string pattern = @"\bare\w*\b";
      Console.WriteLine("Words that begin with 'are':");
      foreach (Match match in Regex.Matches(input, pattern))
         Console.WriteLine("'{0}' found at position {1}",
                           match.Value, match.Index);
   }
}
// The example displays the following output:
//       Words that begin with 'are':
//       'area' found at position 0
//       'arena' found at position 10
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim input As String = "area bare arena mare"
      Dim pattern As String = "\bare\w*\b"
      Console.WriteLine("Words that begin with 'are':")
      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine("'{0}' found at position {1}", _
                           match.Value, match.Index)
      Next
   End Sub
End Module
' The example displays the following output:
'       Words that begin with 'are':
'       'area' found at position 0
'       'arena' found at position 10
```

정규식 패턴은 다음 테이블과 같이 해석됩니다.

패턴 | 설명
------- | ----------- 
`\b` | 단어 경계에서 일치 항목 찾기를 시작합니다.
`are` | "are"를 찾습니다.
`\w*` | 0개 이상의 단어 문자를 찾습니다.
`\b` | 단어 경계에서 일치 항목 찾기를 끝냅니다.
 
## <a name="nonword-boundary-b"></a>비단어 경계: \B

**\B** 앵커는 단어 경계에서 일치 항목 찾기를 수행하지 않도록 지정합니다. **\b** 앵커와 반대 작업을 수행합니다.

다음 예제에서는 **\B** 앵커를 사용하여 단어에서 부분 문자열 "qu"를 찾습니다. 정규식 패턴 `\Bqu\w+`는 단어를 시작하지 않고 단어의 끝으로 계속되는 "qu"로 시작하는 하위 문자열을 찾습니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string input = "equity queen equip acquaint quiet";
      string pattern = @"\Bqu\w+";
      foreach (Match match in Regex.Matches(input, pattern))
         Console.WriteLine("'{0}' found at position {1}", 
                           match.Value, match.Index);
   }
}
// The example displays the following output:
//       'quity' found at position 1
//       'quip' found at position 14
//       'quaint' found at position 21
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim input As String = "equity queen equip acquaint quiet"
      Dim pattern As String = "\Bqu\w+"
      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine("'{0}' found at position {1}", _
                           match.Value, match.Index)
      Next
   End Sub
End Module
' The example displays the following output:
'       'quity' found at position 1
'       'quip' found at position 14
'       'quaint' found at position 21
```

정규식 패턴은 다음 테이블과 같이 해석됩니다.

패턴 | 설명
------- | ----------- 
`\B` | 단어 경계에서 일치 항목 찾기를 시작하지 않습니다.
`qu` | "qu" 하위 문자열을 찾습니다.
`\w+` | 하나 이상의 단어 문자를 찾습니다.
 
## <a name="see-also"></a>참고 항목

[정규식 언어 - 빠른 참조](quick-ref.md)

[정규식 옵션](options.md)
 



<!--HONumber=Nov16_HO3-->


