---
title: "문자열 사용에 대한 모범 사례"
description: "문자열 사용에 대한 모범 사례"
keywords: .NET, .NET Core
author: stevehoag
ms.author: shoag
manager: wpickett
ms.date: 07/26/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: b3cefaa4-0a3f-4a96-aba9-1de30fb07c29
translationtype: Human Translation
ms.sourcegitcommit: b828bb1d6c8fb750ad9ef34f8a7a1b7d2574f4c6
ms.openlocfilehash: d5827f8b8ade216a365fd53f9d4547819188fe9b

---

# <a name="best-practices-for-using-strings"></a>문자열 사용에 대한 모범 사례

.NET에서는 지역화된 응용 프로그램과 전역화된 응용 프로그램을 개발하기 위한 광범위한 지원을 제공하고 문자열 정렬 및 표시와 같은 일반적인 작업을 수행할 때 현재 문화권이나 특정 문화권의 규칙을 쉽게 적용할 수 있습니다. 그러나 문자열 정렬 및 비교가 항상 문화권이 구분되는 작업은 아닙니다. 예를 들어 응용 프로그램에서 내부적으로 사용되는 문자열은 대기 모든 문화권에서 동일하게 처리해야 합니다. XML 태그, HTML 태그, 사용자 이름, 파일 경로 및 시스템 개체 이름과 같이 문화권을 구분하지 않는 문자열 데이터가 문화권이 구분되는 것처럼 해석되면 응용 프로그램 코드에는 감지하기 어려운 버그, 성능 저하 및 경우에 따라 보안 문제가 발생할 수 있습니다.

이 문서에서는 .NET의 문자열 정렬, 비교 및 대/소문자 구분 방법을 살펴보고, 적절한 문자열 처리 방법 선택을 위한 권장 사항을 제공하고, 문자열 처리 방법에 대한 추가 정보를 제공합니다. 또한 숫자 데이터 및 날짜/시간 데이터와 같은 형식이 지정된 데이터를 표시 및 저장을 위해 처리하는 방법을 살펴봅니다. 

이 자료에는 다음과 같은 섹션이 포함되어 있습니다.

* [문자열 사용에 대한 권장 사항](#recommendations-for-string-usage)

* [명시적으로 문자열 비교 지정](#specifying-string-comparisons-explicitly)

* [문자열 비교 세부 정보](#the-details-of-string-comparison)

* [메서드 호출을 위한 StringComparison 멤버 선택](#choosing-a-stringcomparison-member-for-your-method-call)

* [일반적인 문자열 비교 방법](#common-string-comparison-methods)

* [문자열 비교를 간접적으로 수행하는 방법](#methods-that-perform-string-comparison-indirectly)

* [서식이 지정된 데이터 표시 및 유지](#displaying-and-persisting-formatted-data)

## <a name="recommendations-for-string-usage"></a>문자열 사용에 대한 권장 사항

.NET으로 개발하는 경우 문자열을 사용할 때 다음과 같은 간단한 권장 사항을 따르세요. 

* 문자열 작업에 대한 문자열 비교 규칙을 명시적으로 지정하는 오버로드를 사용합니다. 일반적으로 이 작업은 [StringComparison](xref:System.StringComparison) 형식 매개 변수가 포함된 메서드 오버로드를 호출하는 작업입니다.

* 문화권에 독립적인 문자열 일치를 위한 안전한 기본값으로 [StringComparison.Ordinal](xref:System.StringComparison.Ordinal) 또는 [StringComparison.OrdinalIgnoreCase](xref:System.StringComparison.OrdinalIgnoreCase)를 비교에 사용합니다.

* 성능을 향상시키려면 [StringComparison.Ordinal](xref:System.StringComparison.Ordinal) 또는 [StringComparison.OrdinalIgnoreCase](xref:System.StringComparison.OrdinalIgnoreCase)를 비교에 사용합니다.

* 사용자에게 출력을 표시하는 경우 [StringComparison.CurrentCulture](xref:System.StringComparison.CurrentCulture)를 기반으로 하는 문자열 작업을 사용합니다.

* 비교에 언어적 관련성이 없을 경우(예: 기호) [CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture)를 기반으로 하는 문자열 작업 대신 비언어적인 [StringComparison.Ordinal](xref:System.StringComparison.Ordinal) 또는 [StringComparison.OrdinalIgnoreCase](xref:System.StringComparison.OrdinalIgnoreCase) 값을 사용합니다.

* 비교할 문자열을 정규화하는 경우 [String.ToLowerInvariant](xref:System.String.ToLowerInvariant) 메서드 대신 [String.ToUpperInvariant](xref:System.String.ToUpperInvariant) 메서드를 사용합니다.

* [String](xref:System.String) `Equals` 메서드의 오버로드를 사용하여 두 문자열이 같은지를 테스트합니다.

* [String](xref:System.String) `Compare` 및 [String.CompareTo](xref:System.String.CompareTo(System.String)) 메서드의 오버로드를 사용하여 문자열이 같은지 확인하지 않고 문자열을 정렬합니다.

* 문화권이 구분되는 형식 지정을 사용하여 사용자 인터페이스에서 숫자 및 날짜와 같이 문자열이 아닌 데이터를 표시합니다. 고정 문화권과 함께 형식 지정을 사용하여 문자열 양식에서 문자열이 아닌 데이터를 유지합니다.

문자열을 사용할 때 다음 사례를 사용하지 마세요.

* 문자열 작업에 대한 문자열 비교 규칙을 명시적 또는 암시적으로 지정하지 않는 오버로드를 사용하지 마세요. 

* 두 문자열이 같은지를 확인하기 위해 [String](xref:System.String) `Compare` 또는 [String.CompareTo](xref:System.String.CompareTo(System.String)) 메서드의 오버로드 및 반환 값 0 테스트를 사용하지 마세요.

* 문자열 양식에서 숫자 데이터나 날짜 및 시간 데이터를 유지하는 데 문화권이 구분되는 형식 지정을 사용하지 마세요.

## <a name="specifying-string-comparisons-explicitly"></a>명시적으로 문자열 비교 지정

.NET의 문자열 조작 메서드는 대부분 오버로드됩니다. 일반적으로 오버로드 하나 이상은 기본 설정을 그대로 사용하지만, 다른 오버로드는 기본값을 사용하지 않고 문자열을 비교 및 조작하는 정확한 방법을 정의합니다. 기본값을 사용하지 않는 메서드에는 대부분 문화권 및 사례별로 문자열 비교 규칙을 명시적으로 지정하는 열거형인 [StringComparison](xref:System.StringComparison) 형식의 매개 변수가 포함되어 있습니다. 다음 표에서는 [StringComparison](xref:System.StringComparison) 열거형 멤버에 대해 설명합니다. 

StringComparison 멤버 | 설명
----------------------- | -----------
[StringComparison.CurrentCulture](xref:System.StringComparison.CurrentCulture) | 현재 문화권을 사용하여 대/소문자를 구분하는 비교를 수행합니다.
[CurrentCultureIgnoreCase](xref:System.StringComparison.CurrentCultureIgnoreCase) | 현재 문화권을 사용하여 대/소문자를 구분하지 않는 비교를 수행합니다.
[StringComparison.Ordinal](xref:System.StringComparison.Ordinal) | 서수 비교를 수행합니다.
[StringComparison.OrdinalIgnoreCase](xref:System.StringComparison.OrdinalIgnoreCase) | 대소문자를 구분하지 않는 서수 비교를 수행합니다.

예를 들어 문자 또는 문자열과 일치하는 [String](xref:System.String) 개체의 부분 문자열 인덱스를 반환하는 [String](xref:System.String) `IndexOf` 메서드에는 다음과 같은 오버로드 9개가 있습니다.

* [IndexOf(Char)](xref:System.String.IndexOf(System.Char)), [IndexOf(Char, Int32)](xref:System.String.IndexOf(System.Char,System.Int32)) 및 [IndexOf(Char, Int32, Int32)](xref:System.String.IndexOf(System.Char,System.Int32,System.Int32)) - 기본적으로 문자열에서 문자에 대한 서수 검색(대/소문자 구분 및 문화권을 구분하지 않음)을 수행합니다.

* [IndexOf(String)](xref:System.String.IndexOf(System.String)), [IndexOf(String, Int32)](xref:System.String.IndexOf(System.String,System.Int32)) 및 [IndexOf(String, Int32, Int32)](xref:System.String.IndexOf(System.String,System.Int32,System.Int32)) - 기본적으로 문자열에서 부분 문자열에 대한 대/소문자 구분 및 문화권 구분 검색을 수행합니다.

* [IndexOf(String, StringComparison)](xref:System.String.IndexOf(System.String,System.StringComparison)), [IndexOf(String, Int32, StringComparison)](xref:System.String.IndexOf(System.String,System.Int32,System.StringComparison)) 및 [IndexOf(String, Int32, Int32, StringComparison)](xref:System.String.IndexOf(System.String,System.Int32,System.Int32,System.StringComparison)) - 비교 형식을 지정할 수 있는 StringComparison 형식의 매개 변수를 포함합니다.

다음 이유로 기본값을 사용하지 않는 오버로드를 선택하는 것이 좋습니다.

* 기본 매개 변수가 있는 일부 오버로드(문자열 인스턴스에서 [Char](xref:System.Char)를 검색하는 오버로드)는 서수 비교를 수행하지만, 다른 오버로드(문자열 인스턴스에서 문자열을 검색하는 오버로드)는 문화권이 구분됩니다. 어떤 메서드가 어떤 기본값을 사용하는지 기억하기 어렵고 오버로드를 혼동하기 쉽습니다.

* 메서드 호출에 대해 기본값을 사용하는 코드의 의도는 분명하지 않습니다. 기본값을 사용하는 다음 예제에서는 개발자가 실제로 두 문자열의 서수 또는 언어 비교를 의도했는지 또는 `protocol`과 "http" 간의 대/소문자 차이로 인해 같음 테스트에서 `false`가 반환될 수 있는지를 잘 알 수 없습니다.

```csharp
string protocol = GetProtocol(url);       
if (String.Equals(protocol, "http", StringComparison.OrdinalIgnoreCase)) {
   // ...Code to handle HTTP protocol.
}
else {
   throw new InvalidOperationException();
}
```

```vb
Dim protocol As String = GetProtocol(url)       
If String.Equals(protocol, "http") Then
  ' ...Code to handle HTTP protocol.
Else
   Throw New InvalidOperationException()
End If
```

일반적으로 기본값을 사용하지 않는 메서드를 사용하면 코드의 의도가 분명해지므로 이 메서드를 호출하는 것이 좋습니다. 이 메서드를 사용하면 코드를 더 쉽게 읽을 수 있고 더 쉽게 디버그 및 유지 관리할 수도 있습니다. 다음 예제에서는 이전 예제와 관련된 질문을 해결합니다. 예제에서는 서수 비교가 사용되고 대/소문자 차이가 무시됩니다. 

```csharp
string protocol = GetProtocol(url);       
if (String.Equals(protocol, "http", StringComparison.OrdinalIgnoreCase)) {
   // ...Code to handle HTTP protocol.
}
else {
   throw new InvalidOperationException();
}
```

```vb
Dim protocol As String = GetProtocol(url)       
If String.Equals(protocol, "http", StringComparison.OrdinalIgnoreCase) Then
   ' ...Code to handle HTTP protocol.
Else
   Throw New InvalidOperationException()
End If
```

## <a name="the-details-of-string-comparison"></a>문자열 비교 세부 정보

문자열 비교는 특히 정렬 및 같음 테스트와 같은 다양한 문자열 관련 작업의 핵심입니다. 결정된 순서의 문자열 정렬: 정렬된 문자열 목록에서 "my"가 "string" 앞에 나타나면 "my"가 "string"과 같거나 작은지 비교해야 합니다. 또한 비교는 암시적으로 같음을 정의합니다. 비교 작업은 같은 것으로 판단되는 문자열에 대해 0을 반환합니다. 다른 문자열보다 작은 문자열이 없다고 해석하는 것이 적절합니다. 문자열과 관련된 대부분 의미 있는 작업에는 다른 문자열과 비교 및 잘 정의된 정렬 작업 실행이라는 두 가지 절차가 둘 다 또는 두 가지 중 하나가 포함됩니다.

그러나 두 문자열의 같음 또는 정렬 순서를 평가할 때 하나의 올바른 결과가 생성되지는 않습니다. 결과는 문자열 비교에 사용되는 기준에 따라 다릅니다. 특히 서수 문자열 비교나 현재 문화권이나 고정 문화권(영어를 기준으로 로캘을 구분하지 않는 문화권)의 대/소문자 구분 및 정렬 규칙을 기준으로 한 문자열 비교에서는 다른 결과가 생성될 수 있습니다.

### <a name="string-comparisons-that-use-the-current-culture"></a>현재 문화권을 사용하는 문자열 비교

문자열 비교 시 현재 문화권의 규칙을 사용하는 한 가기 기준이 포함됩니다. 현재 문화권을 기준으로 한 비교에는 스레드의 현재 문화권 또는 로캘이 사용됩니다. 데이터가 언어적으로 관련되는 경우와 문화권이 구분되는 사용자 조작을 반영하는 경우에는 항상 현재 문화권을 기준으로 한 비교를 사용해야 합니다. 

그러나 문화권이 변경되면 .NET의 비교 및 대/소문자 지정 동작도 바뀝니다. 응용 프로그램이 개발된 컴퓨터와 다른 문화권을 포함하는 컴퓨터에서 응용 프로그램을 실행하거나 실행 스레드가 문화권을 변경할 경우 이 동작이 수행됩니다. 이 동작은 의도적이지만 대부분 개발자가 이해하기가 분명하지는 않습니다. 다음 예제에서는 미국 영어("en-US") 및 스웨덴어("sv-SE") 문화권의 정렬 순서 차이를 보여 줍니다. 정렬된 문자열 배열에서 단어 "ångström", "Windows" 및 "Visual Studio"가 다른 위치에 나타남을 알 수 있습니다.

```csharp
using System;
using System.Globalization;
using System.Threading;

public class Example
{
   public static void Main()
   {
      string[] values= { "able", "ångström", "apple", "Æble", 
                         "Windows", "Visual Studio" };
      Array.Sort(values);
      DisplayArray(values);

      // Change culture to Swedish (Sweden).
      string originalCulture = CultureInfo.CurrentCulture.Name;
      Thread.CurrentThread.CurrentCulture = new CultureInfo("sv-SE");
      Array.Sort(values);
      DisplayArray(values);

      // Restore the original culture.
      Thread.CurrentThread.CurrentCulture = new CultureInfo(originalCulture);
    }

    private static void DisplayArray(string[] values)
    {
      Console.WriteLine("Sorting using the {0} culture:",  
                        CultureInfo.CurrentCulture.Name);
      foreach (string value in values)
         Console.WriteLine("   {0}", value);

      Console.WriteLine();
    }
}
// The example displays the following output:
//       Sorting using the en-US culture:
//          able
//          Æble
//          ångström
//          apple
//          Visual Studio
//          Windows
//       
//       Sorting using the sv-SE culture:
//          able
//          Æble
//          apple
//          Windows
//          Visual Studio
//          ångström
```

```vb
Imports System.Globalization
Imports System.Threading

Module Example
   Public Sub Main()
      Dim values() As String = { "able", "ångström", "apple", _
                                 "Æble", "Windows", "Visual Studio" }
      Array.Sort(values)
      DisplayArray(values)

      ' Change culture to Swedish (Sweden).
      Dim originalCulture As String = CultureInfo.CurrentCulture.Name
      Thread.CurrentThread.CurrentCulture = New CultureInfo("sv-SE")
      Array.Sort(values)
      DisplayArray(values)

      ' Restore the original culture.
      Thread.CurrentThread.CurrentCulture = New CultureInfo(originalCulture)
    End Sub

    Private Sub DisplayArray(values() As String)
      Console.WRiteLine("Sorting using the {0} culture:", _ 
                        CultureInfo.CurrentCulture.Name)
      For Each value As String In values
         Console.WriteLine("   {0}", value)
      Next
      Console.WriteLine()   
    End Sub
End Module
' The example displays the following output:
'       Sorting using the en-US culture:
'          able
'          Æble
'          ångström
'          apple
'          Visual Studio
'          Windows
'       
'       Sorting using the sv-SE culture:
'          able
'          Æble
'          apple
'          Windows
'          Visual Studio
'          ångström
```

현재 문화권을 사용하는 대/소문자를 구분하지 않는 비교는 스레드의 현재 문화권에 지정된 대/소문자를 무시한다는 점을 제외하고 문화권 구분 비교와 같습니다. 이 동작은 정렬 순서에서도 나타날 수 있습니다. 

현재 문화권 의미 체계를 사용하는 비교는 다음 메서드의 기본값입니다.

* [StringComparison](xref:System.StringComparison) 매개 변수를 포함하지 않는 [String](xref:System.String) `Compare` 오버로드

* [String.CompareTo](xref:System.String.CompareTo(System.String)) 오버로드

* 기본 [String.StartsWith(String)](xref:System.String.StartsWith(System.String)) 메서드 

* 기본 [String.EndsWith(String)](xref:System.String.EndsWith(System.String)) 메서드

* [String](xref:System.String)을 검색 매개 변수로 사용하고 [StringComparison](xref:System.StringComparison) 매개 변수를 포함하지 않는 [String](xref:System.String) `IndexOf` 오버로드

* [String](xref:System.String)을 검색 매개 변수로 사용하고 [StringComparison](xref:System.StringComparison) 매개 변수를 포함하지 않는 [String](xref:System.String) `LastIndexOf` 오버로드

어떤 경우든지 메서드 호출의 의도가 분명하게 나타나도록 [StringComparison](xref:System.StringComparison) 매개 변수가 있는 오버로드를 호출하는 것이 좋습니다. 

비언어적인 문자열 데이터가 언어적으로 해석되거나 특정 문화권의 문자열 데이터가 다른 문화권의 규칙을 통해 해석되면 감지하기 어려운 버그와 감지할 수 있는 버그가 발생할 수 있습니다. 대표적인 예는 Turkish-I 문제입니다.

미국 영어를 포함한 거의 모든 라틴어 알파벳에서 문자 "i"(\u0069)는 문자 "I"(\u0049)의 소문자 버전입니다. 이 대/소문자 구분 규칙은 해당 문화권에서 프로그래밍하는 사용자에게 빠르게 기본 규칙이 되었습니다. 그러나 터키어("tr-TR") 알파벳에는 "i"의 대문자 버전인 "점이 있는 I" 문자 "İ"(\u0130)가 포함됩니다. 터키어에는 대문자 버전이 "I"인 "점이 없는 i" 문자 , "ı"(\u0131)도 포함됩니다. 이 동작은 아제르바이잔어("az") 문화권에서도 발생합니다.

따라서 "i"를 대문자로 변환하거나 "I"를 소문자로 변환하는 방법에 대한 가정이 모든 문화권에서 유효한 것은 아닙니다. 문자열 비교 루틴에 대해 기본 오버로드를 사용하면 이 오버로드에는 문화권 간의 차이가 적용됩니다. 문자열 "file" 및 "FILE"의 대/소문자를 구분하지 않는 비교를 수행하는 다음 시도와 같이, 비교할 데이터가 비언어적일 경우 기본 오버로드를 사용하면 원하지 않는 결과가 생성될 수 있습니다.

```csharp
using System;
using System.Globalization;
using System.Threading;

public class Example
{
   public static void Main()
   {
      string fileUrl = "file";
      Thread.CurrentThread.CurrentCulture = new CultureInfo("en-US");
      Console.WriteLine("Culture = {0}",
                        Thread.CurrentThread.CurrentCulture.DisplayName);
      Console.WriteLine("(file == FILE) = {0}", 
                       fileUrl.StartsWith("FILE", true, null));
      Console.WriteLine();

      Thread.CurrentThread.CurrentCulture = new CultureInfo("tr-TR");
      Console.WriteLine("Culture = {0}",
                        Thread.CurrentThread.CurrentCulture.DisplayName);
      Console.WriteLine("(file == FILE) = {0}", 
                        fileUrl.StartsWith("FILE", true, null));
   }
}
// The example displays the following output:
//       Culture = English (United States)
//       (file == FILE) = True
//       
//       Culture = Turkish (Turkey)
//       (file == FILE) = False
```

```vb
Imports System.Globalization
Imports System.Threading

Module Example
   Public Sub Main()
      Dim fileUrl = "file"
      Thread.CurrentThread.CurrentCulture = New CultureInfo("en-US")
      Console.WriteLine("Culture = {0}", _
                        Thread.CurrentThread.CurrentCulture.DisplayName)
      Console.WriteLine("(file == FILE) = {0}", _ 
                       fileUrl.StartsWith("FILE", True, Nothing))
      Console.WriteLine()

      Thread.CurrentThread.CurrentCulture = New CultureInfo("tr-TR")
      Console.WriteLine("Culture = {0}", _
                        Thread.CurrentThread.CurrentCulture.DisplayName)
      Console.WriteLine("(file == FILE) = {0}", _ 
                        fileUrl.StartsWith("FILE", True, Nothing))
   End Sub
End Module
' The example displays the following output:
'       Culture = English (United States)
'       (file == FILE) = True
'       
'       Culture = Turkish (Turkey)
'       (file == FILE) = False
```

다음 예제와 같이 보안 관련 설정에서 문화권이 의도치 않게 사용되면 이 비교로 인해 큰 문제가 발생할 수 있습니다. `IsFileURI("file:")`와 같은 메서드 호출은 현재 문화권이 미국 영어이면 `true`를 반환하지만 현재 문화권이 터키어이면 `false`를 반환합니다. 따라서 터키어 시스템에서는 누군가 "FILE:"으로 시작하는 대/소문자를 구분하지 않는 URI에 대한 액세스를 차단하는 보안 조치를 회피할 수 있습니다.

```csharp
public static bool IsFileURI(String path) 
{
   return path.StartsWith("FILE:", true, null);
}
```

```vb
Public Shared Function IsFileURI(path As String) As Boolean 
   Return path.StartsWith("FILE:", True, Nothing)
End Function
```

이 경우 "file:"은 비언어적, 문화권을 구분하지 않는 식별자로 해석되므로 코드를 다음 예제와 같이 작성해야 합니다.

```csharp
public static bool IsFileURI(string path) 
{
   return path.StartsWith("FILE:", StringComparison.OrdinalIgnoreCase);
}
```

```vb
Public Shared Function IsFileURI(path As String) As Boolean 
    Return path.StartsWith("FILE:", StringComparison.OrdinalIgnoreCase)
End Function
```

## <a name="ordinal-string-operations"></a>서수 문자열 작업

메서드 호출에서 [StringComparison.Ordinal](xref:System.StringComparison.Ordinal) 또는 [StringComparison.OrdinalIgnoreCase](xref:System.StringComparison.OrdinalIgnoreCase) 값을 지정하는 것은 자연어의 특징이 무시되는 비언어적인 비교를 나타냅니다. 이러한 [StringComparison](xref:System.StringComparison) 값을 사용하여 호출되는 메서드는 문화권별로 매개 변수화되는 대/소문자 구분 또는 동일성 테이블이 아닌 단순 바이트 비교를 기반으로 하여 문자열 작업을 결정합니다. 대부분 경우에 이 접근 방법은 의도된 문자열 해석에 가장 적합하지만 코드를 더 빠르고 더 안정적으로 만듭니다. 

서수 비교는 언어적 해석 없이 각 문자열의 각 바이트가 비교되는 문자열 비교입니다. 예를 들어 "windows"는 "Windows"와 일치하지 않습니다. 컨텍스트가 문자열이 정확히 일치해야 함을 나타내거나 보수적인 일치 정책을 요구할 경우 이 비교를 사용합니다. 또한 서수 비교는 결과를 결정할 때 언어 규칙을 적용하지 않으므로 가장 빠른 비교 작업입니다.

.NET의 문자열에는 포함된 null 문자가 있을 수 있습니다. 서수 비교와 문화권 구분 비교(고정 문화권을 사용하는 비교 포함) 간 가장 분명한 차이의 하나는 문자열의 포함된 null 문자 처리와 관련됩니다. [String](xref:System.String) `Compare` 및 [String](xref:System.String) `Equals` 메서드를 사용하여 문화권 구분 비교(고정 문화권을 사용하는 비교 포함)를 수행하는 경우에는 이러한 문자가 무시됩니다. 따라서 문화권 구분 비교에서 포함된 null 문자가 들어 있는 문자열은 해당 문자가 들어 있지 않은 문자열과 같은 것으로 간주할 수 있습니다. 

> [!IMPORTANT]
> 문자열 비교 메서드는 포함된 null 문자를 무시하지만 [String.Contains](xref:System.String.Contains(System.String)), [String.EndsWith](xref:System.String.EndsWith(System.String)), [String.IndexOf](xref:System.String.IndexOf(System.Char)), [String.LastIndexOf](xref:System.String.LastIndexOf(System.String)) 및 [String.StartsWith](xref:System.String.StartsWith(System.String))와 같은 문자열 검색 메서드는 무시하지 않습니다. 

다음 예제에서는 문자열 "Aa" 및 "A"와 "a" 사이에 여러 포함된 null 문자를 포함하는 비슷한 문자열의 문화권 구분 비교를 수행하고 두 문자열을 어떻게 같은 것으로 간주하는지 보여 줍니다. 

```csharp
using System;

public class Example
{
   public static void Main()
   {
      string str1 = "Aa";
      string str2 = "A" + new String('\u0000', 3) + "a";
      Console.WriteLine("Comparing '{0}' ({1}) and '{2}' ({3}):", 
                        str1, ShowBytes(str1), str2, ShowBytes(str2));
      Console.WriteLine("   With String.Compare:");
      Console.WriteLine("      Current Culture: {0}", 
                        String.Compare(str1, str2, StringComparison.CurrentCulture));
      Console.WriteLine("      Invariant Culture: {0}", 
                        String.Compare(str1, str2, StringComparison.InvariantCulture));

      Console.WriteLine("   With String.Equals:");
      Console.WriteLine("      Current Culture: {0}", 
                        String.Equals(str1, str2, StringComparison.CurrentCulture));
      Console.WriteLine("      Invariant Culture: {0}", 
                        String.Equals(str1, str2, StringComparison.InvariantCulture));
   }

   private static string ShowBytes(string str)
   {
      string hexString = String.Empty;
      for (int ctr = 0; ctr < str.Length; ctr++)
      {
         string result = String.Empty;
         result = Convert.ToInt32(str[ctr]).ToString("X4");
         result = " " + result.Substring(0,2) + " " + result.Substring(2, 2);
         hexString += result;
      }
      return hexString.Trim();
   }
}
// The example displays the following output:
//    Comparing 'Aa' (00 41 00 61) and 'A   a' (00 41 00 00 00 00 00 00 00 61):
//       With String.Compare:
//          Current Culture: 0
//          Invariant Culture: 0
//       With String.Equals:
//          Current Culture: True
//          Invariant Culture: True
```

```vb
Module Example
   Public Sub Main()
      Dim str1 As String = "Aa"
      Dim str2 As String = "A" + New String(Convert.ToChar(0), 3) + "a"
      Console.WriteLine("Comparing '{0}' ({1}) and '{2}' ({3}):", _
                        str1, ShowBytes(str1), str2, ShowBytes(str2))
      Console.WriteLine("   With String.Compare:")
      Console.WriteLine("      Current Culture: {0}", _
                        String.Compare(str1, str2, StringComparison.CurrentCulture))
      Console.WriteLine("      Invariant Culture: {0}", _
                        String.Compare(str1, str2, StringComparison.InvariantCulture))

      Console.WriteLine("   With String.Equals:")
      Console.WriteLine("      Current Culture: {0}", _
                        String.Equals(str1, str2, StringComparison.CurrentCulture))
      Console.WriteLine("      Invariant Culture: {0}", _
                        String.Equals(str1, str2, StringComparison.InvariantCulture))
   End Sub

   Private Function ShowBytes(str As String) As String
      Dim hexString As String = String.Empty
      For ctr As Integer = 0 To str.Length - 1
         Dim result As String = String.Empty
         result = Convert.ToInt32(str.Chars(ctr)).ToString("X4")
         result = " " + result.Substring(0,2) + " " + result.Substring(2, 2)
         hexString += result
      Next
      Return hexString.Trim()
   End Function
End Module
```

그러나 다음 예제와 같이 서수 비교를 사용할 경우 문자열을 같은 것으로 간주하지 않습니다.

```csharp
Console.WriteLine("Comparing '{0}' ({1}) and '{2}' ({3}):", 
                  str1, ShowBytes(str1), str2, ShowBytes(str2));
Console.WriteLine("   With String.Compare:");
Console.WriteLine("      Ordinal: {0}", 
                  String.Compare(str1, str2, StringComparison.Ordinal));

Console.WriteLine("   With String.Equals:");
Console.WriteLine("      Ordinal: {0}", 
                  String.Equals(str1, str2, StringComparison.Ordinal));
// The example displays the following output:
//    Comparing 'Aa' (00 41 00 61) and 'A   a' (00 41 00 00 00 00 00 00 00 61):
//       With String.Compare:
//          Ordinal: 97
//       With String.Equals:
//          Ordinal: False
```

```vb
Console.WriteLine("Comparing '{0}' ({1}) and '{2}' ({3}):", _
                  str1, ShowBytes(str1), str2, ShowBytes(str2))
Console.WriteLine("   With String.Compare:")
Console.WriteLine("      Ordinal: {0}", _
                  String.Compare(str1, str2, StringComparison.Ordinal))

Console.WriteLine("   With String.Equals:")
Console.WriteLine("      Ordinal: {0}", _
                  String.Equals(str1, str2, StringComparison.Ordinal))
' The example displays the following output:
'    Comparing 'Aa' (00 41 00 61) and 'A   a' (00 41 00 00 00 00 00 00 00 61):
'       With String.Compare:
'          Ordinal: 97
'       With String.Equals:
'          Ordinal: False
```

대/소문자를 구분하지 않는 서수 비교는 다음으로 가장 보수적인 접근 방법입니다. 이들 비교는 대부분 대/소문자 구분을 무시합니다. 예를 들어 "windows"는 "Windows"와 일치합니다. ASCII 문자를 처리할 때 이 정책은 사용 가능한 ASCII 대/소문자 구분을 무시한다는 점을 제외하고 [StringComparison.Ordinal](xref:System.StringComparison.Ordinal)과 같습니다. 따라서 [A, Z](\u0041-\u005A)의 모든 문자는 [a,z](\u0061-\007A)의 해당 문자와 일치합니다. ASCII 범위를 벗어난 대/소문자 구분에는 고정 문화권 테이블이 사용됩니다. 따라서 다음 비교는

```csharp
String.Compare(strA, strB, StringComparison.OrdinalIgnoreCase);
```

```vb
String.Compare(strA, strB, StringComparison.OrdinalIgnoreCase)
```

다음 비교와 같지만 더 빠릅니다. 

```csharp
String.Compare(strA.ToUpperInvariant(), strB.ToUpperInvariant(), 
               StringComparison.Ordinal);
```

```vb
String.Compare(strA.ToUpperInvariant(), strB.ToUpperInvariant(), 
               StringComparison.Ordinal)
```

이들 비교는 매우 빠릅니다.

[StringComparison.Ordinal](xref:System.StringComparison.Ordinal)과 [StringComparison.OrdinalIgnoreCase](xref:System.StringComparison.OrdinalIgnoreCase)는 둘 다 이진 값을 직접 사용하며 일치에 가장 적합합니다. 사용 중인 비교 설정을 확신할 수 없으면 다음 두 값의 하나를 사용하세요. 그러나 이들 값은 바이트 단위 비교를 수행하므로 영어 사전처럼 언어 정렬 순서별로 정렬하는 것이 아니라 이진 정렬 순서별로 정렬합니다. 사용자에게 표시되는 결과가 대부분 컨텍스트에서 이상하게 표시될 수 있습니다.

서수 의미 체계는 [StringComparison](xref:System.StringComparison) 인수(같음 연산자 포함)를 포함하지 않는 [String](xref:System.String) `Equals` 오버로드의 기본값입니다. 어떤 경우든지 [StringComparison](xref:System.StringComparison) 매개 변수가 있는 오버로드를 호출하는 것이 좋습니다.

### <a name="string-operations-that-use-the-invariant-culture"></a>고정 문화권을 사용하는 문자열 작업

고정 문화권을 사용한 비교에는 정적 [CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) 속성에서 반환되는 [CompareInfo](xref:System.Globalization.CompareInfo) 속성이 사용됩니다. 이 동작은 모든 시스템에서 동일하며, 범위 밖의 모든 문자를 동일한 고정 문화권으로 간주하는 문자로 변환합니다. 이 정책은 문화권에 걸쳐 단일 문자열 동작 집합을 유지 관리할 경우 유용하지만 예기치 않은 경과를 제공하기도 합니다.

고정 문화권을 사용한 대/소문자를 비교하지 않는 비교에는 비교 정보를 위해 정적 [CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) 속성에서 반환되는 정적 [CompareInfo](xref:System.Globalization.CompareInfo) 속성이 사용됩니다. 모든 경우에 변환된 문자 간의 차이는 무시됩니다.

[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) 개체를 사용하면 [String](xref:System.String) `Compare` 메서드가 특정 문자 집합을 동일한 것으로 해석합니다. 예를 들어 다음 동일성은 고정 문화권에서 유효합니다.

InvariantCulture: a + ̊ = å

Latin Small Letter A 문자 "a"(\u0061)는 Combining Ring Above 문자 "+ " ̊"(\u030a) 옆에 있을 경우 Latin Small Letter A With Ring Above 문자 "å"(\u00e5)로 해석됩니다. 다음 예제와 같이 이 동작은 서수 비교와 다릅니다.

```csharp
string separated = "\u0061\u030a";
string combined = "\u00e5";

Console.WriteLine("Equal sort weight of {0} and {1} using InvariantCulture: {2}",
                  separated, combined, 
                  String.Compare(separated, combined, 
                                 StringComparison.InvariantCulture) == 0);

Console.WriteLine("Equal sort weight of {0} and {1} using Ordinal: {2}",
                  separated, combined,
                  String.Compare(separated, combined, 
                                 StringComparison.Ordinal) == 0);
// The example displays the following output:
//    Equal sort weight of a° and å using InvariantCulture: True
//    Equal sort weight of a° and å using Ordinal: False 
```

```vb
Dim separated As String = ChrW(&h61) + ChrW(&h30a)
Dim combined As String = ChrW(&he5)

Console.WriteLine("Equal sort weight of {0} and {1} using InvariantCulture: {2}", _
                  separated, combined, _
                  String.Compare(separated, combined, _ 
                                 StringComparison.InvariantCulture) = 0)

Console.WriteLine("Equal sort weight of {0} and {1} using Ordinal: {2}", _
                  separated, combined, _
                  String.Compare(separated, combined, _
                                 StringComparison.Ordinal) = 0)
' The example displays the following output:
'    Equal sort weight of a° and å using InvariantCulture: True
'    Equal sort weight of a° and å using Ordinal: False
```

파일 이름, 쿠키 또는 "å"와 같은 조합이 나타날 수 있는 다른 항목을 해석할 때 서수 비교는 가장 투명하고 적합한 동작을 제공합니다.

모든 것을 고려해 보면 고정 문화권에는 비교에 유용할 수 있는 속성이 거의 없습니다. 고정 문화권은 완전한 기호 동일성을 보장하지 못하게 하는 언어 관련 방식으로 비교를 수행하지만 모든 문화권에서 표시하기에 적합하지 않습니다. 예를 들어 표시하기 위한 정렬된 식별자 목록을 포함하는 큰 데이터 파일이 응용 프로그램과 함께 제공될 경우 이 목록에 추가하려면 고정 스타일 정렬을 사용한 삽입이 필요합니다.

## <a name="choosing-a-stringcomparison-member-for-your-method-call"></a>메서드 호출을 위한 StringComparison 멤버 선택

다음 표에서는 의미 체계 문자열 컨텍스트에서 [StringComparison](xref:System.StringComparison) 열거형 멤버로의 매핑을 간략하게 설명합니다.

데이터 | 동작 | 해당 System.StringComparison 값
---- | -------- | -------------------------------------------
대/소문자 구분 내부 식별자, 표준의 대/소문자 구분 식별자(예: XML 및 HTTP) 또는 대/소문자 구분 보안 관련 설정입니다. | 바이트가 정확히 일치하는 비언어적 식별자. | [StringComparison.Ordinal](xref:System.StringComparison.Ordinal)
대/소문자를 구분하지 않는 내부 식별자, 표준의 대/소문자를 구분하지 않는 식별자(예: XML 및 HTTP), 파일 경로, 레지스트리 키 및 값, 환경 변수, 리소스 식별자(예: 핸들 이름) 또는 대/소문자를 구분하지 않는 보안 관련 설정입니다. | 대/소문자와 관련이 없는 비언어적 식별자입니다. | [StringComparison.OrdinalIgnoreCase](xref:System.StringComparison.OrdinalIgnoreCase)
사용자 또는 대부분의 사용자 입력에 표시되는 데이터입니다. | 로컬 언어적 사용자 지정이 필요한 데이터. | [StringComparison.CurrentCulture](xref:System.StringComparison.CurrentCulture) 또는 [CurrentCultureIgnoreCase](xref:System.StringComparison.CurrentCultureIgnoreCase)

## <a name="common-string-comparison-methods"></a>일반적인 문자열 비교 방법

다음 섹션에서는 문자열 비교에 가장 일반적으로 사용되는 메서드에 대해 설명합니다.

### <a name="stringcompare"></a>String.Compare

기본 해석: [StringComparison.CurrentCulture](xref:System.StringComparison.CurrentCulture).

문자열 해석의 가장 중심적인 작업으로, 이들 메서드 호출의 모든 인스턴스를 검사하여 문자열이 현재 문화권에 따라 해석되는지, 아니면 문화권과 관계가 없는지를 결정해야 합니다. 일반적으로 이는 후자이고 [StringComparison.Ordinal](xref:System.StringComparison.Ordinal) 비교를 사용해야 합니다.

[CultureInfo.CompareInfo](xref:System.Globalization.CultureInfo.CompareInfo) 속성에서 반환되는 [System.Globalization.CompareInfo](xref:System.Globalization.CompareInfo) 클래스에는 [CompareOptions](xref:System.Globalization.CompareOptions) 플래그 열거형을 통해 다양한 일치 항목 찾기 옵션(서수, 공백 무시, 가나 형식 무시 등)을 제공하는 [Compare](xref:System.Globalization.CompareInfo.Compare(System.String,System.Int32,System.String,System.Int32,System.Globalization.CompareOptions)) 메서드도 포함되어 있습니다. 

### <a name="stringcompareto"></a>String.CompareTo

기본 해석: [StringComparison.CurrentCulture](xref:System.StringComparison.CurrentCulture).

이 메서드는 현재 [StringComparison](xref:System.StringComparison) 형식을 지정하는 오버로드를 제공하지 않습니다. 일반적으로 이 메서드를 권장되는 [String.Compare(String, String, StringComparison)](xref:System.String.Compare(System.String,System.String,System.StringComparison)) 형식으로 변환할 수 있습니다.

[IComparable](xref:System.IComparable) 및 [IComparable&lt;T&gt;](xref:System.IComparable%601) 인터페이스를 구현하는 형식이 이 메서드를 구현합니다. [StringComparison](xref:System.StringComparison) 매개 변수의 옵션이 제공되지 않으므로 사용자는 대체로 구현 형식을 통해 생성자에서 [StringComparer](xref:System.StringComparer)를 지정할 수 있습니다. 다음 예제에서는 클래스 생성자에 [StringComparer](xref:System.StringComparer) 매개 변수가 포함된 `FileName` 클래스를 정의합니다. 그런 다음 `FileName.CompareTo` 메서드에서 이 [StringComparer](xref:System.StringComparer) 개체를 사용합니다.

```csharp
using System;

public class FileName : IComparable
{
   string fname;
   StringComparer comparer; 

   public FileName(string name, StringComparer comparer)
   {
      if (String.IsNullOrEmpty(name))
         throw new ArgumentNullException("name");

      this.fname = name;

      if (comparer != null)
         this.comparer = comparer;
      else
         this.comparer = StringComparer.OrdinalIgnoreCase;
   }

   public string Name
   {
      get { return fname; }
   }

   public int CompareTo(object obj)
   {
      if (obj == null) return 1;

      if (! (obj is FileName))
         return comparer.Compare(this.fname, obj.ToString());
      else
         return comparer.Compare(this.fname, ((FileName) obj).Name);
   }
}
```

```vb
Public Class FileName : Implements IComparable
   Dim fname As String
   Dim comparer As StringComparer 

   Public Sub New(name As String, comparer As StringComparer)
      If String.IsNullOrEmpty(name) Then
         Throw New ArgumentNullException("name")
      End If

      Me.fname = name

      If comparer IsNot Nothing Then
         Me.comparer = comparer
      Else
         Me.comparer = StringComparer.OrdinalIgnoreCase
      End If      
   End Sub

   Public ReadOnly Property Name As String
      Get
         Return fname
      End Get   
   End Property

   Public Function CompareTo(obj As Object) As Integer _
          Implements IComparable.CompareTo
      If obj Is Nothing Then Return 1

      If Not TypeOf obj Is FileName Then
         obj = obj.ToString()
      Else
         obj = CType(obj, FileName).Name
      End If         
      Return comparer.Compare(Me.fname, obj)
   End Function
End Class
```

### <a name="stringequals"></a>String.Equals

기본 해석: [StringComparison.Ordinal](xref:System.StringComparison.Ordinal).

[String](xref:System.String) 클래스를 통해 정적 또는 인스턴스 `Equals` 메서드 오버로드를 호출하거나 정적 같음 연산자를 사용하여 같음 여부를 테스트할 수 있습니다. 오버로드 및 연산자에는 기본적으로 서수 비교가 사용됩니다. 그러나 서수 비교를 수행하려는 경우에도 [StringComparison](xref:System.StringComparison) 형식을 명시적으로 지정하는 오버로드를 호출하는 것이 좋습니다. 이렇게 하면 특정 문자열 해석을 위해 코드를 더 쉽게 검색할 수 있습니다. 

### <a name="stringtoupper-and-stringtolower"></a>String.ToUpper 및 String.ToLower

기본 해석: [StringComparison.CurrentCulture](xref:System.StringComparison.CurrentCulture).

문자열을 대문자 또는 소문자에 적용하는 방법은 대/소문자와 관계없이 문자열 비교 시 작은 정규화로 사용되므로 이들 메서드를 사용할 때 주의해야 합니다. 이들 메서드를 사용할 경우 대/소문자를 구분하지 않는 비교를 사용하는 것이 좋습니다. 

[String.ToUpperInvariant](xref:System.String.ToUpperInvariant) 및 [String.ToLowerInvariant](xref:System.String.ToLowerInvariant) 메서드도 사용할 수 있습니다. [ToUpperInvariant](xref:System.String.ToUpperInvariant)는 대/소문자를 정규화하는 표준 방법입니다. [StringComparison.OrdinalIgnoreCase](xref:System.StringComparison.OrdinalIgnoreCase)를 사용한 비교의 동작은 두 가지 호출(두 문자열 인수에 대한 [ToUpperInvariant](xref:System.String.ToUpperInvariant) 호출 및 [StringComparison.Ordinal](xref:System.StringComparison.Ordinal)을 사용한 비교 수행)의 컴퍼지션입니다.

문화권을 나타내는 [CultureInfo](xref:System.Globalization.CultureInfo) 개체를 메서드에 전달하여 특정 문화권에서 대문자 및 소문자로 변환하는 데도 오버로드를 사용할 수 있습니다.

### <a name="chartoupper-and-chartolower"></a>Char.ToUpper 및 Char.ToLower

기본 해석: [StringComparison.CurrentCulture](xref:System.StringComparison.CurrentCulture).

이 메서드는 이전 섹션에서 설명된 [String.ToUpper](xref:System.String.ToUpper) 및 [String.ToLower](xref:System.String.ToLower) 메서드와 비슷하게 작동합니다.

### <a name="stringstartswith-and-stringendswith"></a>String.StartsWith 및 String.EndsWith

기본 해석: [StringComparison.CurrentCulture](xref:System.StringComparison.CurrentCulture).

기본적으로 이들 메서드는 둘 다 문화권 구분 비교를 수행합니다.

### <a name="stringindexof-and-stringlastindexof"></a>String.IndexOf 및 String.LastIndexOf

기본 해석: [StringComparison.CurrentCulture](xref:System.StringComparison.CurrentCulture).

이들 메서드의 기본 오버로드가 비교를 수행하는 방식에는 일관성이 없습니다. [Char](xref:System.Char) 매개 변수를 포함하는 모든 [String](xref:System.String) `IndexOf` and [String](xref:System.String) `LastIndexOf` 메서드는 서수 비교를 수행하지만, [String](xref:System.String) 매개 변수를 포함하는 기본 [String](xref:System.String) `IndexOf` 및 [String`](xref:System.String) `LastIndexOf` 메서드는 문화권 구분 비교를 수행합니다. 

` `IndexOf` or `LastIndexOf` 메서드를 호출하고 현재 인스턴스에서 찾을 문자열을 전달할 경우 [StringComparison](xref:System.StringComparison) 형식을 명시적으로 지정하는 오버로드를 호출하는 것이 좋습니다. [Char](xref:System.Char) 인수를 포함하는 오버로드에서는 [StringComparison](xref:System.StringComparison) 형식을 지정할 수 없습니다.

## <a name="methods-that-perform-string-comparison-indirectly"></a>문자열 비교를 간접적으로 수행하는 방법

문자열 비교를 중심 작업으로 포함하는 일부 문자열이 아닌 메서드에는 [StringComparer](xref:System.StringComparer) 형식이 사용됩니다. [StringComparer](xref:System.StringComparer) 클래스에는 해당 `Compare` 메서드가 다음 형식의 문자열 비교를 수행하는 [StringComparer](xref:System.StringComparer) 인스턴스를 반환하는 정적 속성 4개가 포함되어 있습니다.

* 현재 문화권을 사용한 문화권 구분 문자열 비교. 이 [StringComparer](xref:System.StringComparer) 개체는 [StringComparer.CurrentCulture](xref:System.StringComparer.CurrentCulture) 속성에서 반환됩니다.

* 현재 문화권을 사용한 대/소문자를 구분하지 않는 문자열 비교. 이 [StringComparer](xref:System.StringComparer) 개체는 [StringComparer.CurrentCultureIgnoreCase](xref:System.StringComparer.CurrentCultureIgnoreCase) 속성에서 반환됩니다.

* 서수 비교. 이 [StringComparer](xref:System.StringComparer) 개체는 [StringComparer.Ordinal](xref:System.StringComparer.Ordinal) 속성에서 반환됩니다. 

* 대소문자를 구분하지 않는 서수 비교. 이 [StringComparer](xref:System.StringComparer) 개체는 [StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase) 속성에서 반환됩니다.

### <a name="arraysort-and-arraybinarysearch"></a>Array.Sort 및 Array.BinarySearch

기본 해석: [StringComparison.CurrentCulture](xref:System.StringComparison.CurrentCulture).

컬렉션에 데이터를 저장하거나 영구 데이터를 파일이나 데이터베이스에서 컬렉션으로 읽을 때 현재 문화권을 전환하면 컬렉션에서 고정이 무효화될 수 있습니다. [Array.BinarySearch](xref:System.Array.BinarySearch(System.Array,System.Object)) 메서드는 검색할 배열의 요소가 이미 정렬되었다고 가정합니다. 배열에서 문자열 요소를 정렬하기 위해 [Array.Sort](xref:System.Array.Sort(System.Array)) 메서드는 [String] `Compare` 메서드를 호출하여 개별 요소의 순서를 지정합니다. 배열이 정렬된 시간과 콘텐츠가 검색된 시간 사이에 문화권이 변경될 경우 문화권 구분 비교자 사용이 위험할 수 있습니다. 예를 들어 다음 코드에서 저장 및 검색은 `Thread.CurrentThread.CurrentCulture` 속성을 통해 명시적으로 제공된 비교자에서 작동합니다. `StoreNames` 및 `DoesNameExist`에 대한 호출 사이에 문화권이 변경되고 특히 배열 콘텐츠가 두 메서드 호출 사이에 지속되면 이진 검색이 실패할 수 있습니다. 

```csharp
// Incorrect.
string []storedNames;

public void StoreNames(string [] names)
{
   int index = 0;
   storedNames = new string[names.Length];

   foreach (string name in names)
   {
      this.storedNames[index++] = name;
   }

   Array.Sort(names); // Line A.
}

public bool DoesNameExist(string name)
{
   return (Array.BinarySearch(this.storedNames, name) >= 0);  // Line B.
}
```

```vb
' Incorrect.
Dim storedNames() As String

Public Sub StoreNames(names() As String)
   Dim index As Integer = 0
   ReDim storedNames(names.Length - 1)

   For Each name As String In names
      Me.storedNames(index) = name
      index+= 1
   Next

   Array.Sort(names)          ' Line A.
End Sub

Public Function DoesNameExist(name As String) As Boolean
   Return Array.BinarySearch(Me.storedNames, name) >= 0      ' Line B.
End Function
```

배열을 정렬 및 검색할 때 둘 다 같은 서수(문화권을 구분하지 않는) 비교 메서드를 사용하는 다음 예제에서 권장되는 변형을 보여 줍니다. 변경 코드는 두 예제의 `Line A` 및 `Line B` 줄에 반영됩니다.

```csharp
// Correct.
string []storedNames;

public void StoreNames(string [] names)
{
   int index = 0;
   storedNames = new string[names.Length];

   foreach (string name in names)
   {
      this.storedNames[index++] = name;
   }

   Array.Sort(names, StringComparer.Ordinal);  // Line A.
}

public bool DoesNameExist(string name)
{
   return (Array.BinarySearch(this.storedNames, name, StringComparer.Ordinal) >= 0);  // Line B.
}
```

```vb
' Correct.
Dim storedNames() As String

Public Sub StoreNames(names() As String)
   Dim index As Integer = 0
   ReDim storedNames(names.Length - 1)

   For Each name As String In names
      Me.storedNames(index) = name
      index+= 1
   Next

   Array.Sort(names, StringComparer.Ordinal)           ' Line A.
End Sub

Public Function DoesNameExist(name As String) As Boolean
   Return Array.BinarySearch(Me.storedNames, name, StringComparer.Ordinal) >= 0      ' Line B.
End Function
```

이 데이터가 문화권에 걸쳐 지속 및 이동되고 이 데이터를 사용자에게 제공할 때 정렬을 사용할 경우 향상된 사용자 출력을 위해 언어적으로 작동하지만 문화권 변경의 영향을 받지 않는 `StringComparison.InvariantCulture`를 사용하는 것이 좋을 수 있습니다. 다음 예제에서는 두 가지 이전 예제에서 배열 정렬 및 검색에 고정 문화권을 사용하도록 수정합니다.

```csharp
// Correct.
string []storedNames;

public void StoreNames(string [] names)
{
   int index = 0;
   storedNames = new string[names.Length];

   foreach (string name in names)
   {
      this.storedNames[index++] = name;
   }

   Array.Sort(names, StringComparer.InvariantCulture);  // Line A.
}

public bool DoesNameExist(string name)
{
   return (Array.BinarySearch(this.storedNames, name, StringComparer.InvariantCulture) >= 0);  // Line B.
}
```

```vb
' Correct.
Dim storedNames() As String

Public Sub StoreNames(names() As String)
   Dim index As Integer = 0
   ReDim storedNames(names.Length - 1)

   For Each name As String In names
      Me.storedNames(index) = name
      index+= 1
   Next

   Array.Sort(names, StringComparer.InvariantCulture)           ' Line A.
End Sub

Public Function DoesNameExist(name As String) As Boolean
   Return Array.BinarySearch(Me.storedNames, name, StringComparer.InvariantCulture) >= 0      ' Line B.
End Function
```

### <a name="collections-example-hashtable-constructor"></a>컬렉션 예제: 해시 가능한 생성자

문자열 비교 방법이 영향을 미치는 작업의 두 번째 예로는 문자열 해시가 있습니다. 

다음 예제에서는 [StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase) 속성에서 반환되는 [StringComparer](xref:System.StringComparer) 개체를 전달하여 [Hashtable](xref:System.Collections.Hashtable) 개체를 인스턴스화합니다. [StringComparer](xref:System.StringComparer)에서 파생된 [StringComparer](xref:System.StringComparer) 클래스는 [IEqualityComparer](xref:System.Collections.IEqualityComparer) 인터페이스를 구현하므로 해당 [GetHashCode](xref:System.Collections.IEqualityComparer) 메서드는 해시 테이블에서 문자열의 해시 코드를 계산하는 데 사용됩니다.

```csharp
const int initialTableCapacity = 100;
Hashtable h;

public void PopulateFileTable(string directory)
{
   h = new Hashtable(initialTableCapacity, 
                     StringComparer.OrdinalIgnoreCase);

   foreach (string file in Directory.GetFiles(directory))
         h.Add(file, File.GetCreationTime(file));
}

public void PrintCreationTime(string targetFile)
{
   Object dt = h[targetFile];
   if (dt != null)
   {
      Console.WriteLine("File {0} was created at time {1}.",
         targetFile, 
         (DateTime) dt);
   }
   else
   {
      Console.WriteLine("File {0} does not exist.", targetFile);
   }
}
```

```vb
Const initialTableCapacity As Integer = 100
Dim h As Hashtable

Public Sub PopulateFileTable(dir As String)
   h = New Hashtable(initialTableCapacity, _
                     StringComparer.OrdinalIgnoreCase)

   For Each filename As String In Directory.GetFiles(dir)
      h.Add(filename, File.GetCreationTime(filename))
   Next                        
End Sub

Public Sub PrintCreationTime(targetFile As String)
   Dim dt As Object = h(targetFile)
   If dt IsNot Nothing Then
      Console.WriteLine("File {0} was created at {1}.", _
         targetFile, _
         CDate(dt))
   Else
      Console.WriteLine("File {0} does not exist.", targetFile)
   End If
End Sub  
```

## <a name="displaying-and-persisting-formatted-data"></a>서식이 지정된 데이터 표시 및 유지

숫자와 날짜 및 시간과 같은 문자열이 아닌 데이터를 사용자에게 표시할 때 사용자의 문화권 설정을 사용하여 형식을 지정합니다. 기본적으로 [String.Format](xref:System.String.Format(System.IFormatProvider,System.String,System.Object)) 메서드와 숫자 형식 및 날짜/시간 형식의 `ToString` 메서드는 형식 지정 작업에 현재 스레드 문화권을 사용합니다. 형식 지정 메서드가 현재 문화권을 사용하도록 명시적으로 지정하려면 [String.Format(IFormatProvider, String, Object[])](xref:System.String.Format(System.IFormatProvider,System.String,System.Object)) 또는 [DateTime.ToString(IFormatProvider)](xref:System.DateTime.ToString(System.IFormatProvider))와 같은 provider 매개 변수가 있는 형식 지정 메서드의 오버로드를 호출하고 [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture) 속성을 전달하면 됩니다. 

문자열이 아닌 데이터를 이진 데이터 또는 형식이 지정된 데이터로 유지할 수 있습니다. 형식이 지정된 데이터로 저장하는 경우 *provider* 매개 변수를 포함하는 형식 지정 메서드 오버로드를 호출하고 [CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) 속성을 전달해야 합니다. 고정 문화권은 문화권 및 컴퓨터와 관계없는 형식이 지정된 데이터에 대해 일관된 형식을 제공합니다. 반대로 고정 문화권이 아닌 문화권을 사용하여 형식이 지정된 영구 데이터에는 많은 제한 사항이 있습니다. 

* 다른 문화권이 포함된 시스템에서 데이터를 검색하거나 현재 시스템의 사용자가 현재 문화권을 변경하고 데이터를 검색하려고 하면 데이터를 사용하지 못할 수 있습니다. 

* 특정 컴퓨터의 문화권 속성은 표준 값과 다를 수 있습니다. 언제든지 사용자가 문화권 구분 표시 설정을 사용자 지정할 수 있습니다. 이로 인해 사용자가 문화권 설정을 사용자 지정한 후에는 시스템에 저장된 형식이 지정된 데이터를 읽지 못할 수 있습니다. 컴퓨터 간에 형식이 지정된 데이터의 이식성은 훨씬 더 제한적일 수 있습니다. 

* 숫자 또는 날짜/시간의 형식 지정을 관리하는 국제, 지역 또는 국가 표준은 시간에 지나면서 변경되고 이러한 변경 내용은 운영 체제 업데이트에 통합됩니다. 형식 지정 규칙이 변경될 때 이전 규칙을 사용하여 형식이 지정된 데이터를 읽지 못하게 될 수 있습니다. 

다음 예제에서는 문화권 구분 형식 지정을 사용하여 데이터를 유지함으로 인해 제한된 이식성을 보여 줍니다. 예제에서는 날짜 및 시간 값 배열을 파일에 저장합니다. 이들 값은 영어(미국) 문화권의 규칙을 사용하여 형식이 지정됩니다. 응용 프로그램이 현재 스레드 문화권을 프랑스어(스위스)로 변경하고 나면 현재 문화권의 형식 지정 규칙을 사용하여 저장된 값을 읽으려고 합니다. 두 데이터 항목을 읽으려는 시도로 인해 [FormatException](xref:System.FormatException) 예외가 throw되고 날짜 배열에는 [MinValue](xref:System.DateTime.MinValue)와 같은 잘못된 두 가지 요소가 포함됩니다. 

```csharp
using System;
using System.Globalization;
using System.IO;
using System.Text;
using System.Threading;

public class Example
{
   private static string filename = @".\dates.dat";

   public static void Main()
   {
      DateTime[] dates = { new DateTime(1758, 5, 6, 21, 26, 0), 
                           new DateTime(1818, 5, 5, 7, 19, 0), 
                           new DateTime(1870, 4, 22, 23, 54, 0),  
                           new DateTime(1890, 9, 8, 6, 47, 0), 
                           new DateTime(1905, 2, 18, 15, 12, 0) }; 
      // Write the data to a file using the current culture.
      WriteData(dates);
      // Change the current culture.
      Thread.CurrentThread.CurrentCulture = CultureInfo.CreateSpecificCulture("fr-CH");
      // Read the data using the current culture.
      DateTime[] newDates = ReadData();
      foreach (var newDate in newDates)
         Console.WriteLine(newDate.ToString("g"));
   }

   private static void WriteData(DateTime[] dates) 
   {
      StreamWriter sw = new StreamWriter(filename, false, Encoding.UTF8);    
      for (int ctr = 0; ctr < dates.Length; ctr++) {
         sw.Write("{0}", dates[ctr].ToString("g", CultureInfo.CurrentCulture));
         if (ctr < dates.Length - 1) sw.Write("|");   
      }      
      sw.Close();
   }

   private static DateTime[] ReadData() 
   {
      bool exceptionOccurred = false;

      // Read file contents as a single string, then split it.
      StreamReader sr = new StreamReader(filename, Encoding.UTF8);
      string output = sr.ReadToEnd();
      sr.Close();   

      string[] values = output.Split( new char[] { '|' } );
      DateTime[] newDates = new DateTime[values.Length]; 
      for (int ctr = 0; ctr < values.Length; ctr++) {
         try {
            newDates[ctr] = DateTime.Parse(values[ctr], CultureInfo.CurrentCulture);
         }
         catch (FormatException) {
            Console.WriteLine("Failed to parse {0}", values[ctr]);
            exceptionOccurred = true;
         }
      }      
      if (exceptionOccurred) Console.WriteLine();
      return newDates;
   }
}
// The example displays the following output:
//       Failed to parse 4/22/1870 11:54 PM
//       Failed to parse 2/18/1905 3:12 PM
//       
//       05.06.1758 21:26
//       05.05.1818 07:19
//       01.01.0001 00:00
//       09.08.1890 06:47
//       01.01.0001 00:00
//       01.01.0001 00:00
```

```vb
Imports System.Globalization
Imports System.IO
Imports System.Text
Imports System.Threading

Module Example
   Private filename As String = ".\dates.dat"

   Public Sub Main()
      Dim dates() As Date = { #5/6/1758 9:26PM#, #5/5/1818 7:19AM#, _ 
                              #4/22/1870 11:54PM#, #9/8/1890 6:47AM#, _ 
                              #2/18/1905 3:12PM# }
      ' Write the data to a file using the current culture.
      WriteData(dates)
      ' Change the current culture.
      Thread.CurrentThread.CurrentCulture = CultureInfo.CreateSpecificCulture("fr-CH")
      ' Read the data using the current culture.
      Dim newDates() As Date = ReadData()
      For Each newDate In newDates
         Console.WriteLine(newDate.ToString("g"))
      Next
   End Sub

   Private Sub WriteData(dates() As Date)
      Dim sw As New StreamWriter(filename, False, Encoding.Utf8)    
      For ctr As Integer = 0 To dates.Length - 1
         sw.Write("{0}", dates(ctr).ToString("g", CultureInfo.CurrentCulture))
         If ctr < dates.Length - 1 Then sw.Write("|")   
      Next      
      sw.Close()
   End Sub

   Private Function ReadData() As Date()
      Dim exceptionOccurred As Boolean = False

      ' Read file contents as a single string, then split it.
      Dim sr As New StreamReader(filename, Encoding.Utf8)
      Dim output As String = sr.ReadToEnd()
      sr.Close()   

      Dim values() As String = output.Split( {"|"c } )
      Dim newDates(values.Length - 1) As Date 
      For ctr As Integer = 0 To values.Length - 1
         Try
            newDates(ctr) = DateTime.Parse(values(ctr), CultureInfo.CurrentCulture)
         Catch e As FormatException
            Console.WriteLine("Failed to parse {0}", values(ctr))
            exceptionOccurred = True
         End Try
      Next      
      If exceptionOccurred Then Console.WriteLine()
      Return newDates
   End Function
End Module
' The example displays the following output:
'       Failed to parse 4/22/1870 11:54 PM
'       Failed to parse 2/18/1905 3:12 PM
'       
'       05.06.1758 21:26
'       05.05.1818 07:19
'       01.01.0001 00:00
'       09.08.1890 06:47
'       01.01.0001 00:00
'       01.01.0001 00:00
'
```

그러나 [DateTime.ToString(String, IFormatProvider)](xref:System.DateTime.ToString(System.String,System.IFormatProvider)) 및 [DateTime.Parse(String, IFormatProvider)](xref:System.DateTime.Parse(System.String,System.IFormatProvider)) 호출에서 [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture) 속성을 [CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture)로 바꾸면 다음 출력과 같이 보관된 날짜 및 시간 데이터가 복원됩니다.

```
// 06.05.1758 21:26
// 05.05.1818 07:19
// 22.04.1870 23:54
// 08.09.1890 06:47
// 18.02.1905 15:12
```

## <a name="see-also"></a>참고 항목

[문자열 조작](manipulating-strings.md)



<!--HONumber=Nov16_HO3-->


