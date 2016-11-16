---
title: "정규식 개체 모델"
description: "정규식 개체 모델"
keywords: .NET, .NET Core
author: stevehoag
ms.author: shoag
manager: wpickett
ms.date: 07/28/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: a1e611ec-c6a2-48c6-9c52-0ed845787621
translationtype: Human Translation
ms.sourcegitcommit: b20713600d7c3ddc31be5885733a1e8910ede8c6
ms.openlocfilehash: e4a5237fcb439999efe4454512b06444f129e7b4

---

# <a name="the-regular-expression-object-model"></a>정규식 개체 모델

이 항목에서는 .NET 정규식 작업을 수행하는 데 사용되는 개체 모델을 설명합니다. 여기에는 다음 단원이 포함되어 있습니다.

* [정규식 엔진](#the-regular-expression-engine)

* [MatchCollection 및 Match 개체](#the-matchcollection-and-match-objects)

* [그룹 컬렉션](#the-group-collection)

* [캡처된 그룹](#the-captured-group)

* [캡처 컬렉션](#the-capture-collection)

* [개별 캡처](#the-individual-capture)

## <a name="the-regular-expression-engine"></a>정규식 엔진

.NET의 정규식 엔진은 [Regex](xref:System.Text.RegularExpressions.Regex) 클래스로 표현됩니다. 정규식 엔진은 정규식을 구문 분석 및 컴파일하고, 정규식 패턴을 입력 문자열과 일치시키는 작업 수행을 담당합니다. 이 엔진은 .NET 정규식 개체 모델의 중심 구성 요소입니다.

정규식 엔진은 다음과 같은 두 가지 방법 중 하나로 사용할 수 있습니다.

* [Regex](xref:System.Text.RegularExpressions.Regex) 클래스의 정적 메서드를 호출합니다. 메서드 매개 변수에는 입력 문자열과 정규식 패턴이 포함됩니다. 정규식 엔진은 정적 메서드 호출에 사용되는 정규식을 캐시하므로 동일한 정규식을 사용하는 정적 정규식 메서드에 대한 반복 호출은 상대적으로 좋은 성능을 제공합니다.

* 정규식을 클래스 생성자에 전달하여 [Regex](xref:System.Text.RegularExpressions.Regex) 개체를 인스턴스화합니다. 이 경우 [Regex](xref:System.Text.RegularExpressions.Regex) 개체는 변경 불가능하며(읽기 전용), 단일 정규식과 강력하게 결합된 정규식 엔진을 나타냅니다. Regex 인스턴스에 사용되는 정규식은 캐시되지 않으므로 동일한 정규식으로 [Regex](xref:System.Text.RegularExpressions.Regex) 개체를 여러 번 인스턴스화해서는 안 됩니다.

[Regex](xref:System.Text.RegularExpressions.Regex) 클래스의 메서드를 호출하여 다음과 같은 작업을 수행할 수 있습니다. 

* 문자열이 정규식 패턴과 일치하는지 확인합니다.

* 단일 일치 항목 또는 첫 번째 일치 항목을 추출합니다.

* 모든 일치 항목을 추출합니다.

* 일치하는 부분 문자열을 바꿉니다.

* 단일 문자열을 문자열 배열로 분할합니다.

이러한 작업은 다음 섹션에 설명되어 있습니다.

### <a name="matching-a-regular-expression-pattern"></a>정규식 패턴 일치

[Regex.IsMatch](xref:System.Text.RegularExpressions.Regex.IsMatch(System.String)) 메서드는 문자열이 패턴과 일치할 경우 `true`를 반환하고, 그렇지 않을 경우 `false`를 반환합니다. [IsMatch](xref:System.Text.RegularExpressions.Regex.IsMatch(System.String)) 메서드는 일반적으로 문자열 입력의 유효성을 검사하는 데 사용됩니다. 예를 들어, 다음 코드는 문자열이 미국의 유효한 사회 보장 번호와 일치하도록 합니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string[] values = { "111-22-3333", "111-2-3333"};
      string pattern = @"^\d{3}-\d{2}-\d{4}$";
      foreach (string value in values) {
         if (Regex.IsMatch(value, pattern))
            Console.WriteLine("{0} is a valid SSN.", value);
         else   
            Console.WriteLine("{0}: Invalid", value);
      }
   }
}
// The example displays the following output:
//       111-22-3333 is a valid SSN.
//       111-2-3333: Invalid
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim values() As String = { "111-22-3333", "111-2-3333"}
      Dim pattern As String = "^\d{3}-\d{2}-\d{4}$"
      For Each value As String In values
         If Regex.IsMatch(value, pattern) Then
            Console.WriteLine("{0} is a valid SSN.", value)
         Else   
            Console.WriteLine("{0}: Invalid", value)
         End If   
      Next
   End Sub
End Module
' The example displays the following output:
'       111-22-3333 is a valid SSN.
'       111-2-3333: Invalid
```

정규식 패턴 `^\d{3}-\d{2}-\d{4}$`는 다음 테이블과 같이 해석됩니다.

패턴 | 설명
------- | ----------- 
`^` | 입력 문자열의 시작 부분을 찾습니다.
`\d{3}` | 세 개의 10진수를 찾습니다.
`-` | 하이픈을 찾습니다.
`\d{2}` | 두 개의 10진수를 찾습니다.
`-` | 하이픈을 찾습니다.
`\d{4}` | 네 개의 10진수를 찾습니다.
`$` | 입력 문자열의 끝 부분을 찾습니다.
 
### <a name="extracting-a-single-match-or-the-first-match"></a>단일 일치 항목 또는 첫 번째 일치 항목 추출

[Regex.Match](xref:System.Text.RegularExpressions.Regex.Match(System.String)) 메서드는 정규식 패턴과 일치하는 첫 번째 부분 문자열에 대한 정보가 포함된 [Match](xref:System.Text.RegularExpressions.Match) 개체를 반환합니다. `Match.Success` 속성이 일치 항목을 찾았음을 나타내는 `true`를 반환하는 경우 [Match.NextMatch](xref:System.Text.RegularExpressions.Match.NextMatch) 메서드를 호출하여 후속 일치 항목에 대한 정보를 검색할 수 있습니다. 이러한 메서드 호출은 `Match.Success` 속성이 `false`를 반환할 때까지 계속될 수 있습니다. 예를 들어, 다음 코드에서는 [Regex.Match(String, String)](xref:System.Text.RegularExpressions.Regex.Match(System.String,System.String)) 메서드를 사용하여 문자열에서 첫 번째 중복된 단어를 찾습니다. 그런 다음 [Match.NextMatch](xref:System.Text.RegularExpressions.Match.NextMatch) 메서드를 호출하여 추가 중복된 단어를 찾습니다. 이 예제에서는 현재 찾기가 성공했는지와 [Match.NextMatch](xref:System.Text.RegularExpressions.Match.NextMatch) 메서드에 대한 호출이 뒤따라야 하는지를 확인하기 위해 메서드를 호출한 후마다 `Match.Success` 속성을 검토합니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string input = "This is a a farm that that raises dairy cattle."; 
      string pattern = @"\b(\w+)\W+(\1)\b";
      Match match = Regex.Match(input, pattern);
      while (match.Success)
      {
         Console.WriteLine("Duplicate '{0}' found at position {1}.",  
                           match.Groups[1].Value, match.Groups[2].Index);
         match = match.NextMatch();
      }                       
   }
}
// The example displays the following output:
//       Duplicate 'a' found at position 10.
//       Duplicate 'that' found at position 22.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim input As String = "This is a a farm that that raises dairy cattle." 
      Dim pattern As String = "\b(\w+)\W+(\1)\b"
      Dim match As Match = Regex.Match(input, pattern)
      Do While match.Success
         Console.WriteLine("Duplicate '{0}' found at position {1}.", _ 
                           match.Groups(1).Value, match.Groups(2).Index)
         match = match.NextMatch()
      Loop                       
   End Sub
End Module
' The example displays the following output:
'       Duplicate 'a' found at position 10.
'       Duplicate 'that' found at position 22.
```

정규식 패턴 `\b(\w+)\W+(\1)\b`는 다음 테이블과 같이 해석됩니다.

패턴 | 설명
------- | ----------- 
`\b` | 단어 경계에서 일치 항목 찾기를 시작합니다.
`(\w+)` | 하나 이상의 단어 문자를 찾습니다. 이 그룹은 첫 번째 캡처링 그룹입니다.
`\W+` | 하나 이상의 단어가 아닌 문자를 찾습니다.
`(\1)` | 캡처된 첫 번째 문자열을 찾습니다. 이 그룹은 두 번째 캡처링 그룹입니다. 
`\b` | 단어 경계에서 일치 항목 찾기를 끝냅니다.
 
### <a name="extracting-all-matches"></a>모든 일치 항목 추출

[Regex.Matches](xref:System.Text.RegularExpressions.Regex.Matches(System.String)) 메서드는 정규식 엔진이 입력 문자열에서 찾은 모든 일치 항목에 대한 정보가 포함된 [MatchCollection](xref:System.Text.RegularExpressions.MatchCollection) 개체를 반환합니다. 예를 들어, 이전 예제를 [Match](xref:System.Text.RegularExpressions.Regex.Match(System.String)) 및 [NextMatch](xref:System.Text.RegularExpressions.Match.NextMatch) 메서드 대신 [Matches](xref:System.Text.RegularExpressions.Regex.Matches(System.String)) 메서드를 호출하도록 다시 작성할 수 있습니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string input = "This is a a farm that that raises dairy cattle."; 
      string pattern = @"\b(\w+)\W+(\1)\b";
      foreach (Match match in Regex.Matches(input, pattern))
         Console.WriteLine("Duplicate '{0}' found at position {1}.",  
                           match.Groups[1].Value, match.Groups[2].Index);
   }
}
// The example displays the following output:
//       Duplicate 'a' found at position 10.
//       Duplicate 'that' found at position 22.
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim input As String = "This is a a farm that that raises dairy cattle." 
      Dim pattern As String = "\b(\w+)\W+(\1)\b"
      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine("Duplicate '{0}' found at position {1}.", _ 
                           match.Groups(1).Value, match.Groups(2).Index)
      Next                       
   End Sub
End Module
' The example displays the following output:
'       Duplicate 'a' found at position 10.
'       Duplicate 'that' found at position 22.
```

### <a name="replacing-a-matched-substring"></a>일치하는 부분 문자열 바꾸기

[Regex.Replace](xref:System.Text.RegularExpressions.Regex.Replace(System.String,System.String)) 메서드는 정규식 패턴과 일치하는 각 부분 문자열을 지정된 문자열 또는 정규식 패턴으로 바꾸고, 바뀐 내용이 포함된 전체 입력 문자열을 반환합니다. 예를 들어, 다음 코드는 문자열에서 10진수 앞에 미국 통화 기호를 추가합니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"\b\d+\.\d{2}\b";
      string replacement = "$$$&"; 
      string input = "Total Cost: 103.64";
      Console.WriteLine(Regex.Replace(input, pattern, replacement));     
   }
}
// The example displays the following output:
//       Total Cost: $103.64
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "\b\d+\.\d{2}\b"
      Dim replacement As String = "$$$&" 
      Dim input As String = "Total Cost: 103.64"
      Console.WriteLine(Regex.Replace(input, pattern, replacement))     
   End Sub
End Module
' The example displays the following output:
'       Total Cost: $103.64
```

정규식 패턴 `\b\d+\.\d{2}\b`는 다음 테이블과 같이 해석됩니다.

패턴 | 설명
------- | ----------- 
`\b` | 단어 경계에서 일치 항목 찾기를 시작합니다.
`\d+` | 하나 이상의 10진수 숫자가 일치하는지 확인합니다.
`\.` | 마침표를 찾습니다.
`\d{2}` | 두 개의 10진수를 찾습니다.
`\b` | 단어 경계에서 일치 항목 찾기를 끝냅니다.
 
바꾸기 패턴 `$$$&`는 다음 테이블과 같이 해석됩니다.

패턴 | 바꾸기 문자열
------- | ------------------ 
`$$` | 달러 기호(**$**) 문자
`$&` | 일치하는 전제 부분 문자열
 
### <a name="splitting-a-single-string-into-an-array-of-strings"></a>단일 문자열을 문자열 배열로 분할

[Regex.Split](xref:System.Text.RegularExpressions.Regex.Split(System.String)) 메서드는 정규식 일치에 의해 정의된 위치에서 입력 문자열을 분할합니다. 예를 들어, 다음 코드는 번호 매기기 목록의 항목을 문자열 배열에 배치합니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string input = "1. Eggs 2. Bread 3. Milk 4. Coffee 5. Tea";
      string pattern = @"\b\d{1,2}\.\s";
      foreach (string item in Regex.Split(input, pattern))
      {
         if (! String.IsNullOrEmpty(item))
            Console.WriteLine(item);
      }      
   }
}
// The example displays the following output:
//       Eggs
//       Bread
//       Milk
//       Coffee
//       Tea
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim input As String = "1. Eggs 2. Bread 3. Milk 4. Coffee 5. Tea"
      Dim pattern As String = "\b\d{1,2}\.\s"
      For Each item As String In Regex.Split(input, pattern)
         If Not String.IsNullOrEmpty(item) Then
            Console.WriteLine(item)
         End If
      Next      
   End Sub
End Module
' The example displays the following output:
'       Eggs
'       Bread
'       Milk
'       Coffee
'       Tea
```

정규식 패턴 `\b\d{1,2}\.\s`는 다음 테이블과 같이 해석됩니다.

패턴 | 설명
------- | -----------
`\b` | 단어 경계에서 일치 항목 찾기를 시작합니다.
`\d{1,2}` | 한 개 또는 두 개의 10진수를 찾습니다.
`\.` | 마침표를 찾습니다.
`\s` | 공백 문자를 찾습니다.
 
## <a name="the-matchcollection-and-match-objects"></a>MatchCollection 및 Match 개체

[Regex](xref:System.Text.RegularExpressions.Regex) 메서드는 정규식 메서드가 정규식 개체 모델의 일부인 두 개체 [MatchCollection](xref:System.Text.RegularExpressions.MatchCollection) 개체 및 [Match](xref:System.Text.RegularExpressions.Match) 개체를 반환합니다. 

### <a name="the-match-collection"></a>Match 컬렉션

[Regex.Matches](xref:System.Text.RegularExpressions.Regex.Matches(System.String)) 메서드는 정규식 엔진이 입력 문자열에서 찾은 모든 일치 항목(입력 문자열에서 나타나는 순서대로)을 나타내는 [Match](xref:System.Text.RegularExpressions.Match) 개체가 포함된 [MatchCollection](xref:System.Text.RegularExpressions.MatchCollection) 개체를 반환합니다. 일치가 없는 경우 메서드는 멤버 없이 [Match](xref:System.Text.RegularExpressions.Match) 개체를 포함하는 [MatchCollection](xref:System.Text.RegularExpressions.MatchCollection) 개체를 반환합니다. [MatchCollection](xref:System.Text.RegularExpressions.MatchCollection) `Item` 속성을 사용하여 인덱스(0에서 [MatchCollection.Count](xref:System.Text.RegularExpressions.MatchCollection.Count) 속성 값보다 1 작은 값까지)별로 컬렉션의 개별 멤버에 액세스할 수 있습니다. 'Item`은 컬렉션의 인덱서(C#의 경우)이면서 기본 속성(Visual Basic의 경우)입니다.

기본적으로 [Regex.Matches](xref:System.Text.RegularExpressions.Regex.Matches(System.String)) 메서드에 대한 호출에서는 지연 평가를 사용하여 [MatchCollection](xref:System.Text.RegularExpressions.MatchCollection) 개체를 채웁니다. 완전히 채워진 컬렉션이 필요한 속성(예: [MatchCollection.Count](xref:System.Text.RegularExpressions.MatchCollection.Count) 및 `Item` 속성)에 액세스할 경우 성능이 저하될 수 있습니다. 따라서 [MatchCollection.GetEnumerator](xref:System.Text.RegularExpressions.MatchCollection.GetEnumerator) 메서드에서 반환하는 [IEnumerator](xref:System.Collections.IEnumerator) 개체를 사용하여 컬렉션에 액세스하는 것이 좋습니다. 개별 언어는 컬렉션의 IEnumerator](xref:System.Collections.IEnumerator) 인터페이스를 래핑하는 C#의 `foreach` 및 Visual basic의 `For Each'와 같은 구조를 제공합니다.

다음 예제에서는 [Regex.Matches(String)](xref:System.Text.RegularExpressions.Regex.Matches(System.String)) 메서드를 사용하여, 입력 문자열에서 찾은 모든 일치 항목으로 [MatchCollection](xref:System.Text.RegularExpressions.MatchCollection) 개체를 채웁니다. 이 예제에서는 컬렉션을 열거하고 문자열 배열에 일치 항목을 복사하며 정수 배열에 문자 위치를 기록합니다.

```csharp
using System;
using System.Collections.Generic;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
       MatchCollection matches;
       List<string> results = new List<string>();
       List<int> matchposition = new List<int>();

       // Create a new Regex object and define the regular expression.
       Regex r = new Regex("abc");
       // Use the Matches method to find all matches in the input string.
       matches = r.Matches("123abc4abcd");
       // Enumerate the collection to retrieve all matches and positions.
       foreach (Match match in matches)
       {
          // Add the match string to the string array.
           results.Add(match.Value);
           // Record the character position where the match was found.
           matchposition.Add(match.Index);
       }
       // List the results.
       for (int ctr = 0; ctr < results.Count; ctr++)
         Console.WriteLine("'{0}' found at position {1}.", 
                           results[ctr], matchposition[ctr]);  
   }
}
// The example displays the following output:
//       'abc' found at position 3.
//       'abc' found at position 7.
```

```vb
Imports System.Collections.Generic
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
       Dim matches As MatchCollection
       Dim results As New List(Of String)
       Dim matchposition As New List(Of Integer)

       ' Create a new Regex object and define the regular expression.
       Dim r As New Regex("abc")
       ' Use the Matches method to find all matches in the input string.
       matches = r.Matches("123abc4abcd")
       ' Enumerate the collection to retrieve all matches and positions.
       For Each match As Match In matches
          ' Add the match string to the string array.
           results.Add(match.Value)
           ' Record the character position where the match was found.
           matchposition.Add(match.Index)
       Next
       ' List the results.
       For ctr As Integer = 0 To results.Count - 1
         Console.WriteLine("'{0}' found at position {1}.", _
                           results(ctr), matchposition(ctr))  
       Next
   End Sub
End Module
' The example displays the following output:
'       'abc' found at position 3.
'       'abc' found at position 7.
```

### <a name="the-match"></a>일치

[Match](xref:System.Text.RegularExpressions.Match) 클래스는 단일 정규식 일치의 결과를 나타냅니다. 다음과 같은 두 가지 방법으로 [Match](xref:System.Text.RegularExpressions.Match) 개체에 액세스할 수 있습니다.

* Regex.Matches 메서드에서 반환하는 [MatchCollection](xref:System.Text.RegularExpressions.MatchCollection) 개체에서 해당 개체를 검색합니다. 개별 [Match](xref:System.Text.RegularExpressions.Match) 개체를 검색하려면 `foreach`(C#의 경우) 또는 `For Each...Next`(Visual Basic의 경우) 생성자를 사용하여 컬렉션을 반복하거나 [MatchCollection](xref:System.Text.RegularExpressions.MatchCollection) `Item` 속성을 사용하여 인덱스 또는 이름으로 특정 [Match](xref:System.Text.RegularExpressions.Match) 개체를 검색합니다. 또한 인덱스(0에서 컬렉션의 개체 수보다 1 작은 값까지)로 컬렉션을 반복하여 컬렉션에서 개별 [Match](xref:System.Text.RegularExpressions.Match) 개체를 검색할 수 있습니다. 그러나 이 메서드는 [MatchCollection.Count](xref:System.Text.RegularExpressions.MatchCollection.Count) 속성에 액세스하므로 지연 평가를 사용하지 않습니다. 

  다음 예제에서는 `foreach` 생성자를 사용하여 컬렉션을 반복함으로써 [MatchCollection](xref:System.Text.RegularExpressions.MatchCollection) 개체에서 개별 [Match](xref:System.Text.RegularExpressions.Match) 개체를 검색합니다. 정규식은 단순히 입력 문자열에서 문자열 "abc"를 찾습니다.

  ```csharp
  using System;
  using System.Text.RegularExpressions;

  public class Example
  {
     public static void Main()
     {
        string pattern = "abc";
        string input = "abc123abc456abc789";
        foreach (Match match in Regex.Matches(input, pattern))
           Console.WriteLine("{0} found at position {1}.", 
                             match.Value, match.Index);
     }
  }
  // The example displays the following output:
  //       abc found at position 0.
  //       abc found at position 6.
  //       abc found at position 12.
  ```

  ```vb
  Imports System.Text.RegularExpressions

  Module Example
     Public Sub Main()
        Dim pattern As String = "abc"
        Dim input As String = "abc123abc456abc789"
        For Each match As Match In Regex.Matches(input, pattern)
           Console.WriteLine("{0} found at position {1}.", _
                             match.Value, match.Index)
        Next                     
     End Sub
  End Module
  ' The example displays the following output:
  '       abc found at position 0.
  '       abc found at position 6.
  '       abc found at position 12.
  ```

* 문자열 또는 문자열 일부에서 첫 번째 일치 항목을 나타내는 [Match](xref:System.Text.RegularExpressions.Match) 개체를 반환하는 [Regex.Match](xref:System.Text.RegularExpressions.Regex.Match(System.String)) 메서드를 호출합니다. `Match.Success` 속성 값을 검색하여 일치 항목을 찾았는지를 확인할 수 있습니다. 후속 일치 항목을 나타내는 [Match](xref:System.Text.RegularExpressions.Match) 개체를 검색하려면 반환된 [Match](xref:System.Text.RegularExpressions.Match) 개체의 `Success` 속성이 `false`일 때까지 [Match.NextMatch](xref:System.Text.RegularExpressions.Match.NextMatch) 메서드를 반복적으로 호출합니다. 

  다음 예제에서는 [Regex.Match(String, String)](xref:System.Text.RegularExpressions.Regex.Match(System.String,System.String)) 및 [Match.NextMatch](xref:System.Text.RegularExpressions.Match.NextMatch) 메서드를 사용하여 입력 문자열에서 문자열 "abc"를 찾습니다.

  ```csharp
  using System;
  using System.Text.RegularExpressions;

  public class Example
  {
     public static void Main()
     {
        string pattern = "abc";
        string input = "abc123abc456abc789";
        Match match = Regex.Match(input, pattern);
        while (match.Success)
        {
           Console.WriteLine("{0} found at position {1}.", 
                             match.Value, match.Index);
           match = match.NextMatch();                  
        }                     
     }
  }
  // The example displays the following output:
  //       abc found at position 0.
  //       abc found at position 6.
  //       abc found at position 12.
  ```

  ```vb
  Imports System.Text.RegularExpressions

  Module Example
     Public Sub Main()
        Dim pattern As String = "abc"
        Dim input As String = "abc123abc456abc789"
        Dim match As Match = Regex.Match(input, pattern)
        Do While match.Success
           Console.WriteLine("{0} found at position {1}.", _
                             match.Value, match.Index)
           match = match.NextMatch()                  
        Loop                     
     End Sub
  End Module
  ' The example displays the following output:
  '       abc found at position 0.
  '       abc found at position 6.
  '       abc found at position 12.
  ```

[Match](xref:System.Text.RegularExpressions.Match) 클래스의 두 속성은 컬렉션 개체를 반환합니다.

* [Match.Groups](xref:System.Text.RegularExpressions.Match.Groups) 속성은 정규식 패턴에서 캡처링 그룹과 일치하는 부분 문자열에 대한 정보가 포함된 [GroupCollection](xref:System.Text.RegularExpressions.GroupCollection) 개체를 반환합니다. 

* `Match.Captures` 속성은 사용이 제한된 [CaptureCollection](xref:System.Text.RegularExpressions.CaptureCollection) 개체를 반환합니다. 컬렉션은 `Success` 속성이 `false`인 [Match](xref:System.Text.RegularExpressions.Match) 개체에 대해서는 채워지지 않습니다. 그렇지 않은 경우 이 컬렉션은 [Match](xref:System.Text.RegularExpressions.Match) 개체와 동일한 정보를 가진 단일 [Capture](xref:System.Text.RegularExpressions.Capture) 개체를 포함합니다. 

이러한 개체에 대한 자세한 내용은 이 항목 뒷부분의 [그룹 컬렉션](#the-group-collection) 및 [캡처 컬렉션](#the-capture-collection) 섹션을 참조하세요.

[Match](xref:System.Text.RegularExpressions.Match) 클래스의 두 추가 속성은 일치 항목에 대한 정보를 제공합니다. `Match.Value` 속성은 입력 문자열에서 정규식 패턴과 일치하는 부분 문자열을 반환합니다. `Match.Index` 속성은 입력 문자열에서 일치하는 문자열의 0부터 시작하는 시작 위치를 반환합니다.

또한 [Match](xref:System.Text.RegularExpressions.Match) 클래스에는 두 개의 패턴 일치 메서드가 포함되어 있습니다.

* [Match.NextMatch](xref:System.Text.RegularExpressions.Match.NextMatch) 메서드는 현재 [Match](xref:System.Text.RegularExpressions.Match) 개체가 나타내는 일치 항목 뒤에서 일치 항목을 찾아 해당 일치 항목을 나타내는 [Match](xref:System.Text.RegularExpressions.Match) 개체를 반환합니다.

* [Match.Result](xref:System.Text.RegularExpressions.Match.NextMatch) 메서드는 일치하는 문자열에 대해 지정된 바꾸기 작업을 수행하고 결과를 반환합니다. 


다음 예제에서는 [Match.Result](xref:System.Text.RegularExpressions.Match.NextMatch) 메서드를 사용하여, 두 소수 자릿수를 포함하는 모든 숫자 앞에 **$** 기호와 공백 하나를 추가합니다. 

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"\b\d+(,\d{3})*\.\d{2}\b";
      string input = "16.32\n194.03\n1,903,672.08"; 

      foreach (Match match in Regex.Matches(input, pattern))
         Console.WriteLine(match.Result("$$ $&"));
   }
}
// The example displays the following output:
//       $ 16.32
//       $ 194.03
//       $ 1,903,672.08
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "\b\d+(,\d{3})*\.\d{2}\b"
      Dim input As String = "16.32" + vbCrLf + "194.03" + vbCrLf + "1,903,672.08" 

      For Each match As Match In Regex.Matches(input, pattern)
         Console.WriteLine(match.Result("$$ $&"))
      Next
   End Sub
End Module
' The example displays the following output:
'       $ 16.32
'       $ 194.03
'       $ 1,903,672.08
```

정규식 패턴 `\b\d+(,\d{3})*\.\d{2}\b`는 다음 테이블과 같이 정의됩니다.

패턴 | 설명
------- | -----------
`\b` | 단어 경계에서 일치 항목 찾기를 시작합니다.
`\d+` | 하나 이상의 10진수 숫자가 일치하는지 확인합니다.
`(,\d{3})*` | 쉼표 하나 다음에 세 개의 10진수가 있는 0개 이상의 일치 항목을 찾습니다.
`\.` | 소수점 문자를 찾습니다.
`\d{2} | 두 개의 10진수를 찾습니다.
`\b` | 단어 경계에서 일치 항목 찾기를 끝냅니다.
 
바꾸기 패턴 **$$ $&**는 일치하는 부분 문자열이 달러(**$**) 기호(`$$` 패턴), 공백 하나 및 일치 항목 값(`$&` 패턴)으로 바뀌어야 함을 나타냅니다.

## <a name="the-group-collection"></a>그룹 컬렉션

[Match.Groups](xref:System.Text.RegularExpressions.Match.Groups) 속성은 단일 일치 항목에서 캡처된 그룹을 나타내는 [Group](xref:System.Text.RegularExpressions.Group) 개체가 포함된 [GroupCollection](xref:System.Text.RegularExpressions.GroupCollection) 개체를 반환합니다. 컬렉션의 첫 번째 [Group](xref:System.Text.RegularExpressions.Group) 개체(인덱스 0에 있음)는 전체 일치를 나타냅니다. 뒤에 나오는 각 개체는 단일 캡처링 그룹의 결과를 나타냅니다. 

[GroupCollection.Item](xref:System.Text.RegularExpressions.GroupCollection.Item(System.Int32)) 속성을 사용하여 컬렉션에서 개별 [Group](xref:System.Text.RegularExpressions.Group) 개체를 검색할 수 있습니다. 명명되지 않은 그룹은 컬렉션에서 위치로 검색하고, 명명된 그룹은 이름 또는 위치로 검색할 수 있습니다. 명명되지 않은 캡처는 컬렉션에서 맨 앞에 나타나고, 정규식 패턴에서 나타나는 순서대로 왼쪽에서 오른쪽으로 인덱싱됩니다. 명명된 캡처는 명명되지 않은 캡처 다음에, 정규식 패턴에서 나타나는 순서대로 왼쪽에서 오른쪽으로 인덱싱됩니다. 특정 정규식 일치 메서드에 대해 반환된 컬렉션에서 사용 가능한 번호가 매겨진 그룹을 확인하려면 인스턴스 [Regex.GetGroupNumbers](xref:System.Text.RegularExpressions.Regex.GetGroupNumbers) 메서드를 호출하면 됩니다. 컬렉션에서 사용 가능한 명명된 그룹을 확인하려면 인스턴스 [Regex.GetGroupNames](xref:System.Text.RegularExpressions.Regex.GetGroupNames) 메서드를 호출하면 됩니다. 두 메서드는 모두 정규식으로 찾은 일치 항목을 분석하는 범용 루틴에서 특히 유용합니다. 

[GroupCollection.Item](xref:System.Text.RegularExpressions.GroupCollection.Item(System.Int32)) 속성은 컬렉션의 인덱스(C#의 경우) 및 컬렉션 개체의 기본 속성(Visual Basic의 경우)입니다. 즉, 개별 [Group](xref:System.Text.RegularExpressions.Group) 개체를 다음과 같이 인덱스로(또는 명명된 그룹의 경우 이름으로) 액세스할 수 있습니다. 

```csharp
Group group = match.Groups[ctr];
```

```vb
Dim group As Group = match.Groups(ctr)
```

다음 예제에서는 그룹화 구문을 사용하여 날짜의 월, 일 및 연도를 캡처하는 정규식을 정의합니다. 

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = @"\b(\w+)\s(\d{1,2}),\s(\d{4})\b";
      string input = "Born: July 28, 1989";
      Match match = Regex.Match(input, pattern);
      if (match.Success)
         for (int ctr = 0; ctr <  match.Groups.Count; ctr++)
            Console.WriteLine("Group {0}: {1}", ctr, match.Groups[ctr].Value);
    }
}
// The example displays the following output:
//       Group 0: July 28, 1989
//       Group 1: July
//       Group 2: 28
//       Group 3: 1989
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "\b(\w+)\s(\d{1,2}),\s(\d{4})\b"
      Dim input As String = "Born: July 28, 1989"
      Dim match As Match = Regex.Match(input, pattern)
      If match.Success Then
         For ctr As Integer = 0 To match.Groups.Count - 1
            Console.WriteLine("Group {0}: {1}", ctr, match.Groups(ctr).Value)
         Next      
      End If   
   End Sub
End Module
' The example displays the following output:
'       Group 0: July 28, 1989
'       Group 1: July
'       Group 2: 28
'       Group 3: 1989
```

정규식 패턴 `\b(\w+)\s(\d{1,2}),\s(\d{4})\b`는 다음 테이블과 같이 정의됩니다.

패턴 | 설명
------- | -----------
`\b` | 단어 경계에서 일치 항목 찾기를 시작합니다.
`(\w+)` | 하나 이상의 단어 문자를 찾습니다. 이 그룹은 첫 번째 캡처링 그룹입니다.
`\s` | 공백 문자를 찾습니다.
`(\d{1,2})` | 한 개 또는 두 개의 10진수를 찾습니다. 이 그룹은 두 번째 캡처링 그룹입니다.
`,` | 쉼표 하나를 찾습니다.
`\s` | 공백 문자를 찾습니다.
`(\d{4})` | 네 개의 10진수를 찾습니다. 이 그룹은 세 번째 캡처링 그룹입니다.
`\b` | 단어 경계에서 일치 항목 찾기를 끝냅니다.
 
## <a name="the-captured-group"></a>캡처된 그룹

[Group](xref:System.Text.RegularExpressions.Group) 클래스는 단일 캡처링 그룹의 결과를 나타냅니다. 정규식에 정의된 캡처링 그룹을 나타내는 [Group](xref:System.Text.RegularExpressions.Group) 개체는 [Match.Groups](xref:System.Text.RegularExpressions.Match.Groups) 속성에서 반환하는 [GroupCollection](xref:System.Text.RegularExpressions.GroupCollection) 개체의 [Item](xref:System.Text.RegularExpressions.GroupCollection.Item(System.Int32)) 속성에서 반환합니다. [Item](xref:System.Text.RegularExpressions.GroupCollection.Item(System.Int32)) 속성은 [Group](xref:System.Text.RegularExpressions.Group) 클래스의 인덱스(C#의 경우) 및 기본 속성(Visual Basic의 경우)입니다. 또한 `foreach` 생성자를 사용하여 컬렉션을 반복함으로써 개별 멤버를 검색할 수 있습니다. 예제는 이전 섹션을 참조하세요.

다음 예제에서는 중첩된 그룹화 구문을 사용하여 부분 문자열을 그룹으로 캡처합니다. 정규식 패턴 `(a(b))c`는 문자열 "abc"와 일치합니다. 이 패턴은 부분 문자열 "ab"를 첫 번째 캡처링 그룹에 할당하고 부분 문자열 "b"를 두 번째 캡처링 그룹에 할당합니다.

```csharp
List<int> matchposition = new List<int>();
List<string> results = new List<string>();
// Define substrings abc, ab, b.
Regex r = new Regex("(a(b))c"); 
Match m = r.Match("abdabc");
for (int i = 0; m.Groups[i].Value != ""; i++) 
{
   // Add groups to string array.
   results.Add(m.Groups[i].Value); 
   // Record character position.
   matchposition.Add(m.Groups[i].Index); 
}

// Display the capture groups.
for (int ctr = 0; ctr < results.Count; ctr++)
   Console.WriteLine("{0} at position {1}", 
                     results[ctr], matchposition[ctr]);
// The example displays the following output:
//       abc at position 3
//       ab at position 3
//       b at position 4
```

```vb
Dim matchposition As New List(Of Integer)
 Dim results As New List(Of String)
 ' Define substrings abc, ab, b.
 Dim r As New Regex("(a(b))c") 
 Dim m As Match = r.Match("abdabc")
 Dim i As Integer = 0
 While Not (m.Groups(i).Value = "")    
    ' Add groups to string array.
    results.Add(m.Groups(i).Value)     
    ' Record character position. 
    matchposition.Add(m.Groups(i).Index) 
     i += 1
 End While

 ' Display the capture groups.
 For ctr As Integer = 0 to results.Count - 1
    Console.WriteLine("{0} at position {1}", _ 
                      results(ctr), matchposition(ctr))
 Next                     
' The example displays the following output:
'       abc at position 3
'       ab at position 3
'       b at position 4
```

다음 예제에서는 명명된 그룹화 구문을 사용하여 문자열에서 정규식을 콜론(:)에서 분할하는 "DATANAME:VALUE" 형식의 데이터가 포함된 부분 문자열을 캡처합니다.

```csharp
Regex r = new Regex("^(?<name>\\w+):(?<value>\\w+)");
Match m = r.Match("Section1:119900");
Console.WriteLine(m.Groups["name"].Value);
Console.WriteLine(m.Groups["value"].Value);
// The example displays the following output:
//       Section1
//       119900
```

```vb
Dim r As New Regex("^(?<name>\w+):(?<value>\w+)")
Dim m As Match = r.Match("Section1:119900")
Console.WriteLine(m.Groups("name").Value)
Console.WriteLine(m.Groups("value").Value)
' The example displays the following output:
'       Section1
'       119900
```

정규식 패턴 `^(?<name>\w+):(?<value>\w+)`는 다음 테이블과 같이 정의됩니다.

패턴 | 설명
------- | -----------
`^` | 입력 문자열의 시작 부분에서 일치 항목 찾기를 시작합니다.
`(?<name>\w+)` | 하나 이상의 단어 문자를 찾습니다. 캡처링 그룹은 이름은 name입니다.
`:` | 콜론 하나를 찾습니다.
`(?<value>\w+)` | 하나 이상의 단어 문자를 찾습니다. 캡처링 그룹은 이름은 value입니다.
 
[Group](xref:System.Text.RegularExpressions.Group) 클래스의 속성은 캡처된 그룹에 대한 정보를 제공합니다. `Group.Value` 속성은 캡처된 부분 문자열을 포함하고, `Group.Index` 속성은 입력 텍스트에서 캡처된 그룹의 시작 위치를 나타내며, `Group.Length` 속성은 캡처된 텍스트의 길이를 포함하고, `Group.Success` 속성은 부분 문자열이 캡처링 그룹에 의해 정의된 패턴과 일치하는지를 나타냅니다.

그룹에 수량자를 적용하면(자세한 내용은 [정규식의 수량자](quantifiers.md) 참조) 캡처 그룹별로 한 캡처의 관계가 다음과 같은 두 가지 방법으로 수정됩니다.

* 그룹에 __*__ 또는 __*?__ 수량자(0개 이상의 일치 항목을 지정함)가 적용된 경우 캡처링 그룹은 입력 문자열에 일치 항목이 없을 수도 있습니다. 캡처된 텍스트가 없는 경우 [Group](xref:System.Text.RegularExpressions.Group) 개체의 속성은 다음 테이블과 같이 설정됩니다.

  그룹 속성 | 값
  -------------- | ----- 
  `Success` | `false`
  `Value` | [String.Empty](xref:System.String.Empty) 
  `Length` | 0
 
  다음 예제에서 이에 대해 설명합니다. 정규식 패턴 `aaa(bbb)*ccc`에서 첫 번째 캡처링 그룹(부분 문자열 "bbb")은 0번 이상 일치할 수 있습니다. 입력 문자열 "aaaccc"가 패턴과 일치하므로 캡처링 그룹은 일치 항목이 없습니다.

  ```csharp
  using System;
  using System.Text.RegularExpressions;

  public class Example
  {
     public static void Main()
     {
        string pattern = "aaa(bbb)*ccc";
        string input = "aaaccc";
        Match match = Regex.Match(input, pattern);
        Console.WriteLine("Match value: {0}", match.Value);
        if (match.Groups[1].Success)
           Console.WriteLine("Group 1 value: {0}", match.Groups[1].Value);
        else
           Console.WriteLine("The first capturing group has no match.");
     }
  }
  // The example displays the following output:
  //       Match value: aaaccc
  //       The first capturing group has no match.
  ```

  ```vb
  Imports System.Text.RegularExpressions

  Module Example
     Public Sub Main()
        Dim pattern As String = "aaa(bbb)*ccc"
        Dim input As String = "aaaccc"
        Dim match As Match = Regex.Match(input, pattern)
        Console.WriteLine("Match value: {0}", match.Value)
        If match.Groups(1).Success Then
           Console.WriteLine("Group 1 value: {0}", match.Groups(1).Value)
        Else
           Console.WriteLine("The first capturing group has no match.")
       End If   
     End Sub
  End Module
  ' The example displays the following output:
  '       Match value: aaaccc
  '       The first capturing group has no match.
  ```

* 수량자는 캡처링 그룹에 의해 정의된 패턴과 여러 번 일치할 수 있습니다. 이 경우 [Group](xref:System.Text.RegularExpressions.Group) 개체의 `Value` 및 `Length` 속성은 캡처된 마지막 부분 문자열에 대한 정보만 포함합니다. 예를 들어, 다음 정규식은 마침표로 끝나는 단일 문장과 일치합니다. 이 정규식에서는 두 개의 그룹화 구문을 사용하는데, 첫 번째 그룹화 구문은 개별 단어를 공백 문자와 함께 캡처하고, 두 번째 그룹화 구문은 개별 단어를 캡처합니다. 예제의 출력이 보여 주는 것처럼, 정규식이 전체 문장을 캡처하는 데 성공하더라도 두 번째 캡처링 그룹은 마지막 단어만 캡처합니다.

  ```csharp
  using System;
  using System.Text.RegularExpressions;

  public class Example
  {
     public static void Main()
     {
        string pattern = @"\b((\w+)\s?)+\.";
        string input = "This is a sentence. This is another sentence.";
        Match match = Regex.Match(input, pattern);
        if (match.Success)
        {
           Console.WriteLine("Match: " + match.Value);
           Console.WriteLine("Group 2: " + match.Groups[2].Value);
        }   
     }
  }
  // The example displays the following output:
  //       Match: This is a sentence.
  //       Group 2: sentence
  ```

  ```vb
  Imports System.Text.RegularExpressions

  Module Example
     Public Sub Main()
        Dim pattern As String = "\b((\w+)\s?)+\."
        Dim input As String = "This is a sentence. This is another sentence."
        Dim match As Match = Regex.Match(input, pattern)
        If match.Success Then
           Console.WriteLine("Match: " + match.Value)
           Console.WriteLine("Group 2: " + match.Groups(2).Value)
        End If   
     End Sub
  End Module
  ' The example displays the following output:
  '       Match: This is a sentence.
  '       Group 2: sentence
  ```

## <a name="the-capture-collection"></a>캡처 컬렉션

[Group](xref:System.Text.RegularExpressions.Group) 개체는 마지막 캡처에 대한 정보만 포함합니다. 그러나 캡처링 그룹에 의해 만들어진 캡처의 전체 집합은 [Group.Captures](xref:System.Text.RegularExpressions.Group.Captures) 속성에서 반환하는 [CaptureCollection](xref:System.Text.RegularExpressions.CaptureCollection) 개체에서 계속 제공합니다. 컬렉션의 각 멤버는 해당 캡처링 그룹에 의해 만들어진 캡처를 캡처된 순서(및 따라서 캡처된 문자열이 입력 문자열에서 왼쪽에서 오른쪽으로 검색된 순서)대로 나타내는 [Capture](xref:System.Text.RegularExpressions.Capture) 개체입니다. 다음과 같은 두 가지 방법 중 하나로 컬력션에서 개별 [Capture](xref:System.Text.RegularExpressions.Capture) 개체를 검색할 수 있습니다. 

* `foreach`(C#의 경우) 또는 `For Each`(Visual Basic의 경우)와 같은 생성자를 사용하여 컬렉션을 반복합니다.

* [CaptureCollection.Item](xref:System.Text.RegularExpressions.CaptureCollection.Item(System.Int32)) 속성을 사용하여 인덱스로 특정 개체를 검색합니다. Item 속성은 [CaptureCollection](xref:System.Text.RegularExpressions.CaptureCollection) 개체의 기본 속성(Visual Basic의 경우) 또는 인덱서(C#의 경우)입니다.


캡처링 그룹에 수량자가 적용되지 않은 경우 [CaptureCollection](xref:System.Text.RegularExpressions.CaptureCollection) 개체는 별로 관심이 없는 단일 [Capture](xref:System.Text.RegularExpressions.Capture) 개체를 포함하는데, 해당 [Group](xref:System.Text.RegularExpressions.Group) 개체와 동일한 일치 항목에 대한 정보를 제공하기 때문입니다. 캡처링 그룹에 수량자가 적용된 경우 [CaptureCollection](xref:System.Text.RegularExpressions.CaptureCollection) 개체는 캡처링 그룹에 의해 만들어진 모든 캡처를 포함하고, 컬렉션의 마지막 멤버는 [Group](xref:System.Text.RegularExpressions.Group) 개체와 동일한 캡처를 나타냅니다. 

예를 들어, 정규식 패턴 `((a(b))c)+`(여기서 `+` 수량자는 하나 이상의 일치 항목을 지정함)를 사용하여 문자열 "abcabcabc"에서 일치 항목을 캡처하는 경우 각 [Group](xref:System.Text.RegularExpressions.Group) 개체의 [CaptureCollection](xref:System.Text.RegularExpressions.CaptureCollection) 개체는 세 개의 멤버를 포함합니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string pattern = "((a(b))c)+";
      string input = "abcabcabc";

      Match match = Regex.Match(input, pattern);
      if (match.Success)
      {
         Console.WriteLine("Match: '{0}' at position {1}",  
                           match.Value, match.Index);
         GroupCollection groups = match.Groups;
         for (int ctr = 0; ctr < groups.Count; ctr++) {
            Console.WriteLine("   Group {0}: '{1}' at position {2}", 
                              ctr, groups[ctr].Value, groups[ctr].Index);
            CaptureCollection captures = groups[ctr].Captures;
            for (int ctr2 = 0; ctr2 < captures.Count; ctr2++) {
               Console.WriteLine("      Capture {0}: '{1}' at position {2}", 
                                 ctr2, captures[ctr2].Value, captures[ctr2].Index);
            }                     
         }
      }
   }
}
// The example displays the following output:
//       Match: 'abcabcabc' at position 0
//          Group 0: 'abcabcabc' at position 0
//             Capture 0: 'abcabcabc' at position 0
//          Group 1: 'abc' at position 6
//             Capture 0: 'abc' at position 0
//             Capture 1: 'abc' at position 3
//             Capture 2: 'abc' at position 6
//          Group 2: 'ab' at position 6
//             Capture 0: 'ab' at position 0
//             Capture 1: 'ab' at position 3
//             Capture 2: 'ab' at position 6
//          Group 3: 'b' at position 7
//             Capture 0: 'b' at position 1
//             Capture 1: 'b' at position 4
//             Capture 2: 'b' at position 7
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "((a(b))c)+"
      Dim input As STring = "abcabcabc"

      Dim match As Match = Regex.Match(input, pattern)
      If match.Success Then
         Console.WriteLine("Match: '{0}' at position {1}", _ 
                           match.Value, match.Index)
         Dim groups As GroupCollection = match.Groups
         For ctr As Integer = 0 To groups.Count - 1
            Console.WriteLine("   Group {0}: '{1}' at position {2}", _
                              ctr, groups(ctr).Value, groups(ctr).Index)
            Dim captures As CaptureCollection = groups(ctr).Captures
            For ctr2 As Integer = 0 To captures.Count - 1
               Console.WriteLine("      Capture {0}: '{1}' at position {2}", _
                                 ctr2, captures(ctr2).Value, captures(ctr2).Index)
            Next
         Next
      End If
   End Sub
End Module
' The example dosplays the following output:
'       Match: 'abcabcabc' at position 0
'          Group 0: 'abcabcabc' at position 0
'             Capture 0: 'abcabcabc' at position 0
'          Group 1: 'abc' at position 6
'             Capture 0: 'abc' at position 0
'             Capture 1: 'abc' at position 3
'             Capture 2: 'abc' at position 6
'          Group 2: 'ab' at position 6
'             Capture 0: 'ab' at position 0
'             Capture 1: 'ab' at position 3
'             Capture 2: 'ab' at position 6
'          Group 3: 'b' at position 7
'             Capture 0: 'b' at position 1
'             Capture 1: 'b' at position 4
'             Capture 2: 'b' at position 7
```

다음 예제에서는 정규식 `(Abc)+`를 사용하여 문자열 "XYZAbcAbcAbcXYZAbcAb"에서 문자열 "Abc"의 하나 이상의 연속 발생을 찾습니다. 이 예제에서는 [Group.Captures](xref:System.Text.RegularExpressions.Group.Captures) 속성을 사용하여, 캡처된 부분 문자열의 여러 그룹을 반환하는 방법을 보여 줍니다.

```csharp
{
   int counter;
   Match m;
   CaptureCollection cc;
   GroupCollection gc;

   // Look for groupings of "Abc".
   Regex r = new Regex("(Abc)+"); 
   // Define the string to search.
   m = r.Match("XYZAbcAbcAbcXYZAbcAb"); 
   gc = m.Groups;

   // Display the number of groups.
   Console.WriteLine("Captured groups = " + gc.Count.ToString());

   // Loop through each group.
   for (int i=0; i < gc.Count; i++) 
   {
      cc = gc[i].Captures;
      counter = cc.Count;

      // Display the number of captures in this group.
      Console.WriteLine("Captures count = " + counter.ToString());

      // Loop through each capture in the group.
      for (int ii = 0; ii < counter; ii++) 
      {
         // Display the capture and its position.
         Console.WriteLine(cc[ii] + "   Starts at character " + 
              cc[ii].Index);
      }
   }
}
// The example displays the following output:
//       Captured groups = 2
//       Captures count = 1
//       AbcAbcAbc   Starts at character 3
//       Captures count = 3
//       Abc   Starts at character 3
//       Abc   Starts at character 6
//       Abc   Starts at character 9  
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim pattern As String = "((a(b))c)+"
      Dim input As STring = "abcabcabc"

      Dim match As Match = Regex.Match(input, pattern)
      If match.Success Then
         Console.WriteLine("Match: '{0}' at position {1}", _ 
                           match.Value, match.Index)
         Dim groups As GroupCollection = match.Groups
         For ctr As Integer = 0 To groups.Count - 1
            Console.WriteLine("   Group {0}: '{1}' at position {2}", _
                              ctr, groups(ctr).Value, groups(ctr).Index)
            Dim captures As CaptureCollection = groups(ctr).Captures
            For ctr2 As Integer = 0 To captures.Count - 1
               Console.WriteLine("      Capture {0}: '{1}' at position {2}", _
                                 ctr2, captures(ctr2).Value, captures(ctr2).Index)
            Next
         Next
      End If
   End Sub
End Module
' The example displays the following output:
'       Match: 'abcabcabc' at position 0
'          Group 0: 'abcabcabc' at position 0
'             Capture 0: 'abcabcabc' at position 0
'          Group 1: 'abc' at position 6
'             Capture 0: 'abc' at position 0
'             Capture 1: 'abc' at position 3
'             Capture 2: 'abc' at position 6
'          Group 2: 'ab' at position 6
'             Capture 0: 'ab' at position 0
'             Capture 1: 'ab' at position 3
'             Capture 2: 'ab' at position 6
'          Group 3: 'b' at position 7
'             Capture 0: 'b' at position 1
'             Capture 1: 'b' at position 4
'             Capture 2: 'b' at position 7
```

## <a name="the-individual-capture"></a>개별 캡처

[Capture](xref:System.Text.RegularExpressions.Capture) 클래스는 단일 하위 식 캡처의 결과를 포함합니다. [Capture.Value](xref:System.Text.RegularExpressions.Capture.Value) 속성은 일치하는 텍스트를 포함하고, [Capture.Index](xref:System.Text.RegularExpressions.Capture.Index) 속성은 입력 문자열에서 일치하는 하위 문장열이 시작하는 위치(0부터 시작함)를 나타냅니다. 

다음 예제에서는 선택된 도시의 온도에 대한 입력 문자열을 구문 분석합니다. 도시와 해당 온도를 구분하는 데 쉼표(",")가 사용되었으며, 각 도시의 데이터를 구분하는 데 세미콜론(";")이 사용되었습니다. 전체 입력 문자열은 단일 일치 항목을 나타냅니다. 문자열을 구문 분석하는 데 사용되는 정규식 패턴 `((\w+(\s\w+)*),(\d+);)+`에서 도시 이름은 두 번째 캡처링 그룹에 할당되었고, 온도는 네 번째 캡처링 그룹에 할당되었습니다.

```csharp
using System;
using System.Text.RegularExpressions;

public class Example
{
   public static void Main()
   {
      string input = "Miami,78;Chicago,62;New York,67;San Francisco,59;Seattle,58;"; 
      string pattern = @"((\w+(\s\w+)*),(\d+);)+";
      Match match = Regex.Match(input, pattern);
      if (match.Success)
      {
         Console.WriteLine("Current temperatures:");
         for (int ctr = 0; ctr < match.Groups[2].Captures.Count; ctr++)
            Console.WriteLine("{0,-20} {1,3}", match.Groups[2].Captures[ctr].Value, 
                              match.Groups[4].Captures[ctr].Value);
      }
   }
}
// The example displays the following output:
//       Current temperatures:
//       Miami                 78
//       Chicago               62
//       New York              67
//       San Francisco         59
```

```vb
Imports System.Text.RegularExpressions

Module Example
   Public Sub Main()
      Dim input As String = "Miami,78;Chicago,62;New York,67;San Francisco,59;Seattle,58;" 
      Dim pattern As String = "((\w+(\s\w+)*),(\d+);)+"
      Dim match As Match = Regex.Match(input, pattern)
      If match.Success Then
         Console.WriteLine("Current temperatures:")
         For ctr As Integer = 0 To match.Groups(2).Captures.Count - 1
            Console.WriteLine("{0,-20} {1,3}", match.Groups(2).Captures(ctr).Value, _
                              match.Groups(4).Captures(ctr).Value)
         Next
      End If
   End Sub
End Module
' The example displays the following output:
'       Current temperatures:
'       Miami                 78
'       Chicago               62
'       New York              67
'       San Francisco         59
```

정규식은 다음 테이블과 같이 정의됩니다.

패턴 | 설명
------- | -----------
`\w+` | 하나 이상의 단어 문자를 찾습니다.
`(\s\w+)*` | 공백 문자 다음에 하나 이상의 단어 문자가 있는 0개 이상의 일치 항목을 찾습니다. 이 패턴에서는 여러 단어로 된 도시 이름을 찾습니다. 이 그룹은 세 번째 캡처링 그룹입니다.
`(\w+(\s\w+)*)` | 하나 이상의 단어 문자 다음에 0개 이상의 공백 문자 및 하나 이상의 단어 문자가 있는 일치 항목을 찾습니다. 이 그룹은 두 번째 캡처링 그룹입니다.
`,` | 쉼표 하나를 찾습니다.
`(\d+)` | 하나 이상의 숫자를 찾습니다. 이 그룹은 네 번째 캡처링 그룹입니다.
`;` | 세미콜론을 하나 찾습니다.
`((\w+(\s\w+)*),(\d+);)+` | 단어 하나 다음에 추가 단어가 있고 그 다음에 쉼표 하나, 하나 이상의 숫자 및 세미콜론 하나가 한 번 이상 나타나는 패턴을 찾습니다. 이 그룹은 첫 번째 캡처링 그룹입니다. 
 
## <a name="see-also"></a>참고 항목

[System.Text.RegularExpressions](xref:System.Text.RegularExpressions)

[.NET 정규식](regular-expressions.md)

[정규식 언어 - 빠른 참조](quick-ref.md)




<!--HONumber=Nov16_HO1-->


