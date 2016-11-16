---
title: "방법: 날짜 및 시간 산술 연산의 표준 시간대 사용"
description: "날짜 및 시간 산술 연산의 표준 시간대를 사용하는 방법"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 08/16/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 26870cdc-1709-4978-831b-ff2a2f24856f
translationtype: Human Translation
ms.sourcegitcommit: c40c28da09e8a122b542463c197196c82c81dd19
ms.openlocfilehash: 735ae2f1d11ddf6b88b40c7b89083a45d1f07523

---

# <a name="how-to-use-time-zones-in-date-and-time-arithmetic"></a>방법: 날짜 및 시간 산술 연산의 표준 시간대 사용

일반적으로 [System.DateTimeOffset](xref:System.DateTimeOffset) 값을 사용하여 날짜 및 시간 산술 연산을 수행할 경우 결과는 표준 시간대 조정 규칙을 반영하지 않습니다. 날짜의 표준 시간대 및 시간 값을 명확하게 식별할 수 있는 경우에도 동일합니다. 이 문서에서는 특정 표준 시간대에 속하는 날짜 및 시간 값에 대한 산술 연산 작업을 수행하는 방법을 보여 줍니다. 산술 연산 작업의 결과는 표준 시간대의 조정 규칙을 반영합니다.

## <a name="to-apply-adjustment-rules-to-date-and-time-arithmetic"></a>날짜 및 시간 산술 연산에 조정 규칙을 적용하려면

1. 날짜 및 시간 값을 속해 있는 표준 시간대와 밀접하게 연결하는 몇 가지 메서드를 구현합니다. 예를 들어, 날짜 및 시간 값 및 해당 표준 시간대를 모두 포함하는 구조체를 선언합니다. 다음 예제에서는이 방법을 사용하여 [DateTimeOffset](xref:System.DateTimeOffset) 값을 표준 시간대와 연결합니다.

    ```csharp
    // Define a structure for DateTime values for internal use only
    internal struct TimeWithTimeZone
    {
    TimeZoneInfo TimeZone;
    DateTimeOffset Time;
    }
    ```

    ```vb
    ' Define a structure for DateTime values for internal use only
    Friend Structure TimeWithTimeZone
       Dim TimeZone As TimeZoneInfo
       Dim Time As Date
    End Structure
    ```
    
2. [TimeZoneInfo.ConvertTime (DateTime, TimeZoneInfo)](xref:System.TimeZoneInfo.ConvertTime(System.DateTime,System.TimeZoneInfo)) 메서드를 호출하여 시간을 UTC(협정 세계시)로 변환합니다.

3. UTC 시간에 대한 산술 연산을 수행합니다.

4. [TimeZoneInfo.ConvertTime(DateTime, TimeZoneInfo)](xref:System.TimeZoneInfo.ConvertTime(System.DateTime,System.TimeZoneInfo)) 메서드를 호출하여 UTC 시간을 원래 시간과 연관된 표준 시간대로 변환합니다. 

## <a name="example"></a>예제

다음 예제에서는 중부 표준시 2008년 3월 9일 오전 1시 30분에 2시간 30분을 추가합니다. 30분 뒤인 2008년 3월 9일 오전 2시에 표준 시간대가 일광 절약 시간으로 전환됩니다. 이 예제에서는 이전 섹션의 네 단계를 수행하므로 결과 시간이 2008년 3월 9일 오전 5시로 올바르게 보고됩니다. 

```csharp
using System;

public struct TimeZoneTime
{
   public TimeZoneInfo TimeZone;
   public DateTimeOffset Time;

   public TimeZoneTime(TimeZoneInfo tz, DateTimeOffset time)
   {
      if (tz == null) 
         throw new ArgumentNullException("The time zone cannot be a null reference.");

      this.TimeZone = tz;
      this.Time = time;   
   }

   public TimeZoneTime AddTime(TimeSpan interval)
   {
      // Convert time to UTC
      DateTimeOffset utcTime = TimeZoneInfo.ConvertTime(this.Time, TimeZoneInfo.Utc);      
      // Add time interval to time
      utcTime = utcTime.Add(interval);
      // Convert time back to time in time zone
      return new TimeZoneTime(this.TimeZone, TimeZoneInfo.ConvertTime(utcTime, this.TimeZone));
   }
}

public class TimeArithmetic
{
   public const string tzName = "Central Standard Time";

   public static void Main()
   {
      try
      {
         TimeZoneTime cstTime1, cstTime2;

         TimeZoneInfo cst = TimeZoneInfo.FindSystemTimeZoneById(tzName);
         DateTime time1 = new DateTime(2008, 3, 9, 1, 30, 0);          
         TimeSpan twoAndAHalfHours = new TimeSpan(2, 30, 0);

         cstTime1 = new TimeZoneTime(cst, 
                        new DateTimeOffset(time1, cst.GetUtcOffset(time1)));
         cstTime2 = cstTime1.AddTime(twoAndAHalfHours);
         Console.WriteLine("{0} + {1} hours = {2}", cstTime1.Time, 
                                                    twoAndAHalfHours.ToString(),  
                                                    cstTime2.Time);
      }
      catch
      {
         Console.WriteLine("Unable to find {0}.", tzName);
      }
   }
}
```

```vb
Public Structure TimeZoneTime
   Public TimeZone As TimeZoneInfo
   Public Time As Date

   Public Sub New(tz As TimeZoneInfo, time As Date)
      If tz Is Nothing Then _
         Throw New ArgumentNullException("The time zone cannot be a null reference.")

      Me.TimeZone = tz
      Me.Time = time
   End Sub

   Public Function AddTime(interval As TimeSpan) As TimeZoneTime
      ' Convert time to UTC
      Dim utcTime As DateTime = TimeZoneInfo.ConvertTimeToUtc(Me.Time, _
                                                              Me.TimeZone)      
      ' Add time interval to time
      utcTime = utcTime.Add(interval)
      ' Convert time back to time in time zone
      Return New TimeZoneTime(Me.TimeZone, TimeZoneInfo.ConvertTime(utcTime, _
                              TimeZoneInfo.Utc, Me.TimeZone))
   End Function
End Structure

Module TimeArithmetic
   Public Const tzName As String = "Central Standard Time"

   Public Sub Main()
      Try
         Dim cstTime1, cstTime2 As TimeZoneTime

         Dim cst As TimeZoneInfo = TimeZoneInfo.FindSystemTimeZoneById(tzName)
         Dim time1 As Date = #03/09/2008 1:30AM#
         Dim twoAndAHalfHours As New TimeSpan(2, 30, 0)

         cstTime1 = New TimeZoneTime(cst, time1)
         cstTime2 = cstTime1.AddTime(twoAndAHalfHours)

         Console.WriteLine("{0} + {1} hours = {2}", cstTime1.Time, _
                                                    twoAndAHalfHours.ToString(), _ 
                                                    cstTime2.Time)  
      Catch
         Console.WriteLine("Unable to find {0}.", tzName)
      End Try   
   End Sub   
End Module
```

이 추가 작업이 [DateTimeOffset](xref:System.DateTimeOffset) 값을 UTC로 변환하지 않고 간단히 수행하는 경우 결과는 올바른 시점을 반영하지만 해당 오프셋은 해당 시간에 대한 지정된 표준 시간대의 시점을 반영하지 않습니다. 

[DateTimeOffset](xref:System.DateTimeOffset) 값은 속해 있는 표준 시간대에서 분리됩니다. 자동으로 표준 시간대의 조정 규칙을 적용하는 방식으로 날짜 및 시간 산술 연산을 수행하려면 모든 날짜 및 시간 값이 속해 있는 표준 시간대를 즉시 식별할 수 있어야 합니다. 즉, 날짜 및 시간과 관련된 표준 시간대는 밀접하게 결합되어야 합니다. 여러 가지 방법을 통해 다음을 포함하는 작업을 수행할 수 있습니다.

* 응용 프로그램에서 사용되는 모든 시간이 특정 표준 시간대에 속한다고 가정합니다. 일부 경우에는 적합하지만 이 방식은 제한된 유연성과 이식성을 제공합니다.

* 모두 형식의 필드로 포함하여 관련된 표준 시간대와 날짜 및 시간을 밀접하게 결합하는 형식을 정의합니다. 이 방법은 코드 예제에 사용되며 여기서 두 개의 구성원 필드에 있는 날짜 및 시간과 표준 시간대를 저장하는 구조체를 정의합니다.

## <a name="see-also"></a>참고 항목

[날짜, 시간 및 표준 시간대](index.md)

[날짜 및 시간에 대한 산술 연산 수행](performing-arithmetic-operations.md)



<!--HONumber=Nov16_HO1-->


