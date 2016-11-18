---
title: "방법: 날짜 및 시간 값의 밀리초 표시"
description: "날짜 및 시간 값의 밀리초를 표시하는 방법"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 07/26/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 78599e33-1c3f-4335-b320-751e35906338
translationtype: Human Translation
ms.sourcegitcommit: fb00da6505c9edb6a49d2003ae9bcb8e74c11d6c
ms.openlocfilehash: 9bcec8a610ed0fd47d168e23fb1454067e2d3fac

---

# <a name="how-to-display-milliseconds-in-date-and-time-values"></a>방법: 날짜 및 시간 값의 밀리초 표시

[DateTime.ToString()](xref:System.DateTime.ToString) 등의 기본 날짜 및 시간 형식 지정 메서드는 시간 값의 시간, 분, 초를 포함하지만 밀리초 구성 요소는 제외됩니다. 이 항목에서는 형식이 지정된 날짜 및 시간 문자열에 날짜 및 시간의 밀리초 구성 요소를 포함하는 방법을 보여 줍니다.

## <a name="to-display-the-millisecond-component-of-a-datetime-value"></a>DateTime 값의 밀리초 구성 요소를 표시하려면

1. 날짜의 문자열 표현으로 작업하는 경우 정적 [DateTime.Parse(String)](xref:System.DateTime.Parse(System.String)) 또는 [DateTimeOffset.Parse(String)](xref:System.DateTimeOffset.Parse(System.String)) 메서드를 사용하여 [DateTime](xref:System.DateTime) 또는 [DateTimeOffset](xref:System.DateTimeOffset) 값으로 변환합니다.

2. 시간 밀리초 구성 요소의 문자열 표현을 추출하려면 날짜 및 시간 값의 [DateTime.ToString(String)](xref:System.DateTime.ToString(System.String)) 또는 [DateTimeOffset.ToString](xref:System.DateTimeOffset.ToString(System.String)) 메서드를 호출하고 `fff` 또는 `FFF` 사용자 지정 형식 패턴을 단독으로 또는 다른 사용자 지정 형식 지정자와 함께 형식 매개 변수로 전달합니다.

## <a name="example"></a>예제

이 예제에서는 [DateTime](xref:System.DateTime) 및 [DateTimeOffset](xref:System.DateTimeOffset) 값의 밀리초 구성 요소를 단독으로 및 긴 날짜 및 시간 문자열에 포함하여 콘솔에 표시합니다. 

```csharp
using System;
using System.Globalization;
using System.Text.RegularExpressions;

public class MillisecondDisplay
{
   public static void Main()
   {
      string dateString = "7/16/2008 8:32:45.126 AM";

      try
      {
         DateTime dateValue = DateTime.Parse(dateString);
         DateTimeOffset dateOffsetValue = DateTimeOffset.Parse(dateString);

         // Display Millisecond component alone.
         Console.WriteLine("Millisecond component only: {0}", 
                           dateValue.ToString("fff"));
         Console.WriteLine("Millisecond component only: {0}", 
                           dateOffsetValue.ToString("fff"));

         // Display Millisecond component with full date and time.
         Console.WriteLine("Date and Time with Milliseconds: {0}", 
                           dateValue.ToString("MM/dd/yyyy hh:mm:ss.fff tt"));                        
         Console.WriteLine("Date and Time with Milliseconds: {0}", 
                           dateOffsetValue.ToString("MM/dd/yyyy hh:mm:ss.fff tt"));

         // Append millisecond pattern to current culture's full date time pattern
         string fullPattern = DateTimeFormatInfo.CurrentInfo.FullDateTimePattern;
         fullPattern = Regex.Replace(fullPattern, "(:ss|:s)", "$1.fff");

         // Display Millisecond component with modified full date and time pattern.
         Console.WriteLine("Modified full date time pattern: {0}", 
                           dateValue.ToString(fullPattern));
         Console.WriteLine("Modified full date time pattern: {0}",
                           dateOffsetValue.ToString(fullPattern));
      }
      catch (FormatException)
      {
         Console.WriteLine("Unable to convert {0} to a date.", dateString);
      }
   }
}
// The example displays the following output if the current culture is en-US:
//    Millisecond component only: 126
//    Millisecond component only: 126
//    Date and Time with Milliseconds: 07/16/2008 08:32:45.126 AM
//    Date and Time with Milliseconds: 07/16/2008 08:32:45.126 AM
//    Modified full date time pattern: Wednesday, July 16, 2008 8:32:45.126 AM
//    Modified full date time pattern: Wednesday, July 16, 2008 8:32:45.126 AM
```

```vb
Imports System.Globalization
Imports System.Text.REgularExpressions

Module MillisecondDisplay
   Public Sub Main()

      Dim dateString As String = "7/16/2008 8:32:45.126 AM"

      Try
         Dim dateValue As Date = Date.Parse(dateString)
         Dim dateOffsetValue As DateTimeOffset = DateTimeOffset.Parse(dateString)

         ' Display Millisecond component alone.
         Console.WriteLine("Millisecond component only: {0}", _
                           dateValue.ToString("fff"))
         Console.WriteLine("Millisecond component only: {0}", _
                           dateOffsetValue.ToString("fff"))

         ' Display Millisecond component with full date and time.
         Console.WriteLine("Date and Time with Milliseconds: {0}", _
                           dateValue.ToString("MM/dd/yyyy hh:mm:ss.fff tt"))                        
         Console.WriteLine("Date and Time with Milliseconds: {0}", _
                           dateOffsetValue.ToString("MM/dd/yyyy hh:mm:ss.fff tt"))

         ' Append millisecond pattern to current culture's full date time pattern
         Dim fullPattern As String = DateTimeFormatInfo.CurrentInfo.FullDateTimePattern
         fullPattern = Regex.Replace(fullPattern, "(:ss|:s)", "$1.fff")

         ' Display Millisecond component with modified full date and time pattern.
         Console.WriteLine("Modified full date time pattern: {0}", _
                           dateValue.ToString(fullPattern))                        
         Console.WriteLine("Modified full date time pattern: {0}", _
                           dateOffsetValue.ToString(fullPattern))
      Catch e As FormatException
         Console.WriteLine("Unable to convert {0} to a date.", dateString)      
      End Try
   End Sub
End Module
' The example displays the following output if the current culture is en-US:
'    Millisecond component only: 126
'    Millisecond component only: 126
'    Date and Time with Milliseconds: 07/16/2008 08:32:45.126 AM
'    Date and Time with Milliseconds: 07/16/2008 08:32:45.126 AM
'    Modified full date time pattern: Wednesday, July 16, 2008 8:32:45.126 AM
'    Modified full date time pattern: Wednesday, July 16, 2008 8:32:45.126 AM
```

`fff` 형식 패턴에서는 밀리초 값의 뒤에 0이 포함됩니다. `FFF` 형식 패턴에서는 표시되지 않습니다. 다음 예제에서 차이점을 보여 줍니다.

```csharp
DateTime dateValue = new DateTime(2008, 7, 16, 8, 32, 45, 180); 
Console.WriteLine(dateValue.ToString("fff"));    
Console.WriteLine(dateValue.ToString("FFF"));
// The example displays the following output to the console:
//    180
//    18 
```

```vb
Dim dateValue As New Date(2008, 7, 16, 8, 32, 45, 180) 
Console.WriteLIne(dateValue.ToString("fff"))    
Console.WriteLine(dateValue.ToString("FFF"))
' The example displays the following output to the console:
'    180
'    18
```

날짜 및 시간의 밀리초 구성 요소를 포함하는 전체 사용자 지정 형식 지정자를 정의할 때 발생하는 문제는 응용 프로그램의 현재 문화권에서 사용되는 시간 요소의 정렬과 일치하지 않는 하드 코드된 형식이 정의될 수 있다는 것입니다. 더 나은 방법은 현재 문화권의 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체에서 정의된 날짜 및 시간 표시 패턴 중 하나를 검색한 후 밀리초를 포함하도록 수정하는 것입니다. 이 방법도 예제에서 보여 줍니다. [DateTimeFormatInfo.FullDateTimePattern](xref:System.Globalization.DateTimeFormatInfo.FullDateTimePattern) 속성에서 현재 문화권의 전체 날짜 및 시간 패턴을 검색한 다음 초 패턴 뒤에 사용자 지정 패턴 `.ffff`를 삽입합니다. 예제에서는 정규식을 사용하여 이 작업을 단일 메서드 호출에서 수행합니다.

사용자 지정 형식 지정자를 사용하여 밀리초가 아닌 초의 소수 자릿수를 표시할 수도 있습니다. 예를 들어 `f` 또는 `F` 사용자 지정 형식 지정자는 1/10초, `ff` 또는 `FF` 사용자 지정 형식 지정자는 1/100초, `ffff` 또는 `FFFF` 사용자 지정 형식 지정자는 1/10000초를 표시합니다. 반환된 문자열에서 밀리초의 소수 자릿수가 반올림되지 않고 잘립니다. 이 형식 지정자는 다음 예제에서 사용됩니다.

```csharp
DateTime dateValue = new DateTime(2008, 7, 16, 8, 32, 45, 180); 
Console.WriteLine("{0} seconds", dateValue.ToString("s.f"));
Console.WriteLine("{0} seconds", dateValue.ToString("s.ff"));      
Console.WriteLine("{0} seconds", dateValue.ToString("s.ffff"));
// The example displays the following output to the console:
//    45.1 seconds
//    45.18 seconds
//    45.1800 seconds
```

```vb
Dim dateValue As New DateTime(2008, 7, 16, 8, 32, 45, 180) 
Console.WriteLine("{0} seconds", dateValue.ToString("s.f"))
Console.WriteLine("{0} seconds", dateValue.ToString("s.ff"))      
Console.WriteLine("{0} seconds", dateValue.ToString("s.ffff"))
' The example displays the following output to the console:
'    45.1 seconds
'    45.18 seconds
'    45.1800 seconds
```

> [!NOTE]
> 1/10000초, 1/100000초 등 1초의 매우 작은 소수 단위를 표시할 수 있습니다. 그러나 이러한 값은 의미가 없을 수 있습니다. 날짜 및 시간 값의 자릿수는 시스템 시계의 정밀도에 따라 달라집니다.

## <a name="see-also"></a>참고 항목

[System.Globalization.DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo)

[사용자 지정 날짜 및 시간 서식 문자열](custom-datetime.md)




<!--HONumber=Nov16_HO3-->


