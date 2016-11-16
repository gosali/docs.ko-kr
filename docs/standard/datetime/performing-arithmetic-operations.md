---
title: "날짜 및 시간에 대한 산술 연산 수행"
description: "날짜 및 시간에 대한 산술 연산 수행"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 08/16/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 589ac5ec-8365-4a0d-bc38-72183718110c
translationtype: Human Translation
ms.sourcegitcommit: c40c28da09e8a122b542463c197196c82c81dd19
ms.openlocfilehash: 64fbb3b25d5959ac1dd781d2076775560d926e1a

---

# <a name="performing-arithmetic-operations-with-dates-and-times"></a>날짜 및 시간에 대한 산술 연산 수행

[System.DateTime](xref:System.DateTime) 및 [System.DateTimeOffset](xref:System.DateTimeOffset) 구조는 모두 해당 값에 산술 연산을 수행하는 구성원을 제공하지만 산술 연산의 결과는 매우 다릅니다. 이 문서는 이러한 차이점을 검사하고 날짜 및 시간 데이터에서 표준 시간대 인식 수준에 연결하며 날짜 및 시간 데이터를 사용하여 표준 시간대 인식 작업을 완벽하게 수행하는 방법에 대해 설명합니다.

## <a name="comparisons-and-arithmetic-operations-with-datetime-values"></a>DateTime 값을 사용한 비교 및 산술 연산

[System.DateTime](xref:System.DateTime) 값은 제한된 수준의 표준 시간대 인식을 보유합니다. [DateTime.Kind](xref:System.DateTime.Kind) 속성을 사용하면 [System.DateTimeKind](xref:System.DateTimeKind) 값을 현지 시간 또는 UTC(협정 세계시) 또는 지정되지 않은 표준 시간대의 시간을 나타내는지 여부를 나타내는 날짜 및 시간에 할당할 수 있습니다. 그러나 [DateTime](xref:System.DateTime) 값에 대한 날짜 및 시간 산술 연산을 비교하고 수행할 경우 이 제한된 표준 시간대 정보는 무시됩니다. 현재 UTC 시간을 포함한 현재 현지 시간을 비교하는 다음 예제에서는 이를 보여 줍니다.

```csharp
using System;

public enum TimeComparison
{
   EarlierThan = -1,
   TheSameAs = 0,
   LaterThan = 1
}

public class DateManipulation
{
   public static void Main()
   {
      DateTime localTime = DateTime.Now;
      DateTime utcTime = DateTime.UtcNow;

      Console.WriteLine("Difference between {0} and {1} time: {2}:{3} hours", 
                        localTime.Kind.ToString(), 
                        utcTime.Kind.ToString(), 
                        (localTime - utcTime).Hours, 
                        (localTime - utcTime).Minutes);
      Console.WriteLine("The {0} time is {1} the {2} time.", 
                        localTime.Kind.ToString(), 
                        Enum.GetName(typeof(TimeComparison), localTime.CompareTo(utcTime)), 
                        utcTime.Kind.ToString());  
   }
}
// If run in the U.S. Pacific Standard Time zone, the example displays 
// the following output to the console:
//    Difference between Local and Utc time: -7:0 hours
//    The Local time is EarlierThan the Utc time.
```

```vb
Public Enum TimeComparison As Integer
   EarlierThan = -1
   TheSameAs = 0
   LaterThan = 1
End Enum

Module DateManipulation
   Public Sub Main()
      Dim localTime As Date = Date.Now
      Dim utcTime As Date = Date.UtcNow

      Console.WriteLine("Difference between {0} and {1} time: {2}:{3} hours", _
                        localTime.Kind.ToString(), _
                        utcTime.Kind.ToString(), _
                        (localTime - utcTime).Hours, _
                        (localTime - utcTime).Minutes)
      Console.WriteLine("The {0} time is {1} the {2} time.", _
                        localTime.Kind.ToString(), _ 
                        [Enum].GetName(GetType(TimeComparison), localTime.CompareTo(utcTime)), _
                        utcTime.Kind.ToString())  
      ' If run in the U.S. Pacific Standard Time zone, the example displays 
      ' the following output to the console:
      '    Difference between Local and Utc time: -7:0 hours
      '    The Local time is EarlierThan the Utc time.                                                    
   End Sub
End Module
```

[DateTime.CompareTo(DateTime, DateTime)](xref:System.DateTime.Compare(System.DateTime,System.DateTime)) 메서드는 현지 시간이 UTC 시간보다 빠르거나 느리다고 보고하고, 빼기 연산은 미국에 있는 시스템의 UTC와 현지 시간 간 차이를 나타냅니다. 태평양 표준 시간대는 7시간입니다. 하지만 이러한 두 개의 값은 시간에서 단일 지점의 다양한 표현을 제공하기 때문에 이 경우에 이 시간 간격이 UTC에서 현지 표준 시간대 오프셋을 발생시키는 것이 명확합니다. 

보다 일반적으로 해당 결과의 해석에 영향을 줄 수 있지만 [DateTimeKind](xref:System.DateTimeKind) 속성은 [DateTime](xref:System.DateTime) 비교 및 산술 연산 메서드에서 반환되는 결과에 영향을 주지 않습니다(시간에서 두 개의 동일한 지점을 비교하면 알 수 있듯이). 예:

* [DateTimeKind](xref:System.DateTimeKind) 속성이 둘 다 [DateTimeKind.Utc](xref:System.DateTimeKind.Utc)인 두 개의 날짜 및 시간 값에 대해 수행된 모든 산술 연산 작업의 결과는 두 값 사이의 실제 시간 간격을 반영합니다. 마찬가지로 이러한 두 개의 날짜 및 시간 값을 비교하면 시간 간의 관계를 정확하게 반영합니다.

* [DateTimeKind](xref:System.DateTimeKind) 속성이 둘 다 [DateTimeKind.Local](xref:System.DateTimeKind.Local)인 두 개의 날짜 및 시간 값 또는 서로 다른 [DateTimeKind](xref:System.DateTimeKind) 속성 값인 두 개의 날짜 및 시간 값에 대해 수행된 모든 산술 연산 또는 비교 작업은 두 값 사이에 있는 클록 시간의 차이를 반영합니다. 

* 로컬 날짜 및 시간 값에 대한 산술 또는 비교 작업은 특정 값이 모호하거나 잘못되었는지 여부를 고려하지 않거나 현지 표준 시간대 전환 또는 일광 절약 시간에서 발생하는 모든 조정 규칙의 효과를 고려하지 않습니다.

* UTC와 현지 시간 사이의 차이를 비교하거나 계산하는 작업에는 결과에 있는 UTC에서 현지 표준 시간대 오프셋과 동일한 시간 간격이 포함됩니다. 

* 지정되지 않은 시간과 UTC 또는 현지 시간 사이의 차이를 비교하거나 계산하는 작업은 간단한 클록 시간을 반영합니다. 표준 시간대 차이를 고려하지 않으면 결과가 표준 시간대 조정 규칙의 응용 프로그램을 반영하지 않습니다. 

* 두 개의 지정되지 않은 시간 사이의 차이를 비교하거나 계산하는 작업에는 두 개의 다른 시간대의 시간 사이의 차이를 반영하는 알 수 없는 간격이 포함될 수 있습니다.

다양한 시나리오에서 표준 시간대 차이가 날짜 및 시간 계산에 영향을 주지 않거나 날짜 및 시간 데이터의 컨텍스트가 비교 또는 산술 연산 작업의 의미를 정의합니다. 이 중 일부에 대한 설명은 [DateTime, DateTimeOffset, TimeSpan 및 TimeZoneInfo 중 선택](choosing-between-datetime.md)을 참조하세요.

## <a name="comparisons-and-arithmetic-operations-with-datetimeoffset-values"></a>DateTimeOffset 값을 사용한 비교 및 산술 연산

[System.DateTimeOffset](xref:System.DateTimeOffset) 값은 UTC에 상대적인 해당 날짜 및 시간을 명확하게 정의하는 오프셋뿐만 아니라 날짜 및 시간을 포함합니다. 이렇게 하면 [System.DateTime](xref:System.DateTime) 값의 경우와 다르게 같음을 정의할 수 있습니다. 동일한 날짜 및 시간 값을 갖는 경우 [DateTime](xref:System.DateTime) 값이 같은 반면 두 값이 시간에서 동일한 지점을 의미하는 경우 [DateTimeOffset](xref:System.DateTimeOffset) 값이 같습니다. 이렇게 하면 두 날짜 및 시간 사이의 간격을 결정하는 비교 및 대부분의 산술 연산에서 사용되는 경우 [DateTimeOffset](xref:System.DateTimeOffset) 값은 더 정확해지고 해석의 필요가 적어집니다. 로컬과 UTC DateTime 값을 비교하는 이전 예제와 같은 [DateTimeOffset](xref:System.DateTimeOffset)에 대한 다음 예제에서는 해당 동작의 차이를 보여 줍니다.

```csharp
using System;

public enum TimeComparison
{
   EarlierThan = -1,
   TheSameAs = 0,
   LaterThan = 1
}

public class DateTimeOffsetManipulation
{
   public static void Main()
   {
      DateTimeOffset localTime = DateTimeOffset.Now;
      DateTimeOffset utcTime = DateTimeOffset.UtcNow;

      Console.WriteLine("Difference between local time and UTC: {0}:{1:D2} hours", 
                        (localTime - utcTime).Hours, 
                        (localTime - utcTime).Minutes);
      Console.WriteLine("The local time is {0} UTC.", 
                        Enum.GetName(typeof(TimeComparison), localTime.CompareTo(utcTime)));  
   }
}
// Regardless of the local time zone, the example displays 
// the following output to the console:
//    Difference between local time and UTC: 0:00 hours.
//    The local time is TheSameAs UTC.
```

```vb
Public Enum TimeComparison As Integer
   EarlierThan = -1
   TheSameAs = 0
   LaterThan = 1
End Enum

Module DateTimeOffsetManipulation
   Public Sub Main()
      Dim localTime As DateTimeOffset = DateTimeOffset.Now
      Dim utcTime As DateTimeOffset = DateTimeOffset.UtcNow

      Console.WriteLine("Difference between local time and UTC: {0}:{1:D2} hours.", _
                        (localTime - utcTime).Hours, _
                        (localTime - utcTime).Minutes)
      Console.WriteLine("The local time is {0} UTC.", _
                        [Enum].GetName(GetType(TimeComparison), localTime.CompareTo(utcTime)))  
   End Sub
End Module
' Regardless of the local time zone, the example displays 
' the following output to the console:
'    Difference between local time and UTC: 0:00 hours.
'    The local time is TheSameAs UTC.
'          Console.WriteLine(e.GetType().Name)
```

이 예제에서 [DateTimeOffset.CompareTo](xref:System.DateTimeOffset.CompareTo(System.DateTimeOffset)) 메서드는 현재 현지 시간과 현재 UTC 시간이 동일함을 나타내며 [DateTimeOffset](xref:System.DateTimeOffset) 값의 빼기는 두 시간 사이의 차이가 [TimeSpan.Zero](xref:System.TimeSpan.Zero)임을 나타냅니다. 

날짜 및 시간 산술 연산에서 [DateTimeOffset](xref:System.DateTimeOffset) 값 을 사용하는 주요 제한 사항은 [DateTimeOffset](xref:System.DateTimeOffset) 값에 표준 시간대 인식이 포함됨에도 불구하고 완전한 표준 시간대 인식이 아니라는 점입니다. [DateTimeOffset](xref:System.DateTimeOffset) 값의 오프셋이 UTC의 표준 시간대 오프셋을 반영하지만 [DateTimeOffset](xref:System.DateTimeOffset) 변수에 값이 먼저 할당된 경우 이후의 표준 시간과 분리됩니다. 더 이상 식별 가능한 시간과 직접 연결되어 있기 때문에 날짜 및 시간 간격의 더하기 및 빼기는 표준 시간대의 조정 규칙을 고려하지 않습니다. 

예를 들어 미국 중부 표준시에서는 2008년 3월 9일 오전 2시에 표준 시간대가 일광 절약 시간제로 전환됩니다. 즉, 중부 표준시 2008년 3월 9일 오전 1시 30분에 2시간 30분을 더하면 날짜와 시간은 2008년 3월 9일 오전 5시가 되어야 합니다. 그러나 다음 예제와 같이 이 더하기 연산의 결과는 2008년 3월 9일 오전 4시가 됩니다. on March 9, 2008. 관심 있는 표준 시간대의 시간이 아니지만(즉, 예상된 표준 시간대 오프셋이 아님) 이 작업의 결과는 올바른 특정 시점을 나타냅니다.

```csharp
using System;

public class IntervalArithmetic
{
   public static void Main()
   {
      DateTime generalTime = new DateTime(2008, 3, 9, 1, 30, 0);
      const string tzName = "Central Standard Time";
      TimeSpan twoAndAHalfHours = new TimeSpan(2, 30, 0);

      // Instantiate DateTimeOffset value to have correct CST offset
      try
      {
         DateTimeOffset centralTime1 = new DateTimeOffset(generalTime, 
                    TimeZoneInfo.FindSystemTimeZoneById(tzName).GetUtcOffset(generalTime));

         // Add two and a half hours      
         DateTimeOffset centralTime2 = centralTime1.Add(twoAndAHalfHours);
         // Display result
         Console.WriteLine("{0} + {1} hours = {2}", centralTime1, 
                                                    twoAndAHalfHours.ToString(), 
                                                    centralTime2);  
      }
      catch (TimeZoneNotFoundException)
      {
         Console.WriteLine("Unable to retrieve Central Standard Time zone information.");
      }
   }
}
// The example displays the following output to the console:
//    3/9/2008 1:30:00 AM -06:00 + 02:30:00 hours = 3/9/2008 4:00:00 AM -06:00
```

```vb
Module IntervalArithmetic
   Public Sub Main()
      Dim generalTime As Date = #03/09/2008 1:30AM#
      Const tzName As String = "Central Standard Time"
      Dim twoAndAHalfHours As New TimeSpan(2, 30, 0)

      ' Instantiate DateTimeOffset value to have correct CST offset
      Try
         Dim centralTime1 As New DateTimeOffset(generalTime, _
                    TimeZoneInfo.FindSystemTimeZoneById(tzName).GetUtcOffset(generalTime))

         ' Add two and a half hours      
         Dim centralTime2 As DateTimeOffset = centralTime1.Add(twoAndAHalfHours)
         ' Display result
         Console.WriteLine("{0} + {1} hours = {2}", centralTime1, _
                                                    twoAndAHalfHours.ToString(), _
                                                    centralTime2)   
      Catch e As TimeZoneNotFoundException
         Console.WriteLine("Unable to retrieve Central Standard Time zone information.")
      End Try
   End Sub
End Module
' The example displays the following output to the console:
'    3/9/2008 1:30:00 AM -06:00 + 02:30:00 hours = 3/9/2008 4:00:00 AM -06:00
```

## <a name="arithmetic-operations-with-times-in-time-zones"></a>표준 시간대의 시간을 사용한 산술 연산

[System.TimeZoneInfo](xref:System.TimeZoneInfo) 클래스는 날짜 및 시간 산술 연산을 수행할 때 자동으로 조정 규칙을 적용하는 메서드를 제공하지 않습니다. 그러나 이렇게 하려면 표준 시간대의 시간을 UTC로 변환하고 산술 연산을 수행하며 UTC에서 표준 시간대의 시간으로 다시 변환해야 합니다. 자세한 내용은 [방법: 날짜 및 시간 산술 연산의 표준 시간대 사용](use-time-zones-in-arithmetic.md)을 참조하세요.

예를 들어 다음 코드는 2008년 3월 9일 오전 2시에 2시간 30분을 더한 이전 코드와 유사합니다. on March 9, 2008. 그러나 날짜 및 시간 산술 연산을 수행하기 전에 중부 표준시를 UTC로 변환한 다음 UTC의 결과를 중부 표준시로 다시 변환하기 때문에 결과 시간은 일광 절약 시간제로 전환된 중앙 표준 시간대를 반영합니다.

```csharp
using System;

public class TimeZoneAwareArithmetic
{
   public static void Main()
   {
      const string tzName = "Central Standard Time";

      DateTime generalTime = new DateTime(2008, 3, 9, 1, 30, 0);
      TimeZoneInfo cst = TimeZoneInfo.FindSystemTimeZoneById(tzName);
      TimeSpan twoAndAHalfHours = new TimeSpan(2, 30, 0);

      // Instantiate DateTimeOffset value to have correct CST offset
      try
      {
         DateTimeOffset centralTime1 = new DateTimeOffset(generalTime, 
                                       cst.GetUtcOffset(generalTime));

         // Add two and a half hours
         DateTimeOffset utcTime = centralTime1.ToUniversalTime();
         utcTime += twoAndAHalfHours;

         DateTimeOffset centralTime2 = TimeZoneInfo.ConvertTime(utcTime, cst);
         // Display result
         Console.WriteLine("{0} + {1} hours = {2}", centralTime1, 
                                                    twoAndAHalfHours.ToString(), 
                                                    centralTime2);  
      }
      catch (TimeZoneNotFoundException)
      {
         Console.WriteLine("Unable to retrieve Central Standard Time zone information.");
      }
   }
}
// The example displays the following output to the console:
//    3/9/2008 1:30:00 AM -06:00 + 02:30:00 hours = 3/9/2008 5:00:00 AM -05:00
```

```vb
Module TimeZoneAwareArithmetic
   Public Sub Main()
      Const tzName As String = "Central Standard Time"

      Dim generalTime As Date = #03/09/2008 1:30AM#
      Dim cst As TimeZoneInfo = TimeZoneInfo.FindSystemTimeZoneById(tzName) 
      Dim twoAndAHalfHours As New TimeSpan(2, 30, 0)

      ' Instantiate DateTimeOffset value to have correct CST offset
      Try
         Dim centralTime1 As New DateTimeOffset(generalTime, _
                    cst.GetUtcOffset(generalTime))

         ' Add two and a half hours 
         Dim utcTime As DateTimeOffset = centralTime1.ToUniversalTime()
         utcTime += twoAndAHalfHours

         Dim centralTime2 As DateTimeOffset = TimeZoneInfo.ConvertTime(utcTime, cst)
         ' Display result
         Console.WriteLine("{0} + {1} hours = {2}", centralTime1, _
                                                    twoAndAHalfHours.ToString(), _
                                                    centralTime2)   
      Catch e As TimeZoneNotFoundException
         Console.WriteLine("Unable to retrieve Central Standard Time zone information.")
      End Try
   End Sub
End Module
' The example displays the following output to the console:
'    3/9/2008 1:30:00 AM -06:00 + 02:30:00 hours = 3/9/2008 5:00:00 AM -05:00
```

## <a name="see-also"></a>참고 항목

[날짜, 시간 및 표준 시간대](index.md)

[방법: 날짜 및 시간 산술 연산의 표준 시간대 사용](use-time-zones-in-arithmetic.md)





<!--HONumber=Nov16_HO1-->


