---
title: "표준 시간대 간 시간 변환"
description: "표준 시간대 간 시간 변환"
keywords: .NET, .NET Core
author: stevehoag
ms.author: shoag
manager: wpickett
ms.date: 08/15/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: bf8f74e6-e7f2-4c2a-a04c-57db0e28dd36
translationtype: Human Translation
ms.sourcegitcommit: b20713600d7c3ddc31be5885733a1e8910ede8c6
ms.openlocfilehash: 051a4891336470e79bda9d7b9bb1be4e2c9187b8

---

# <a name="converting-times-between-time-zones"></a>표준 시간대 간 시간 변환

날짜 및 시간을 사용하는 응용 프로그램에서 표준 시간대 간의 차이를 처리하는 것이 더욱 중요해지고 있습니다. 더 이상 응용 프로그램에서 모든 시간을 [System.DateTime](xref:System.DateTime) 구조체에서 제공되는 현지 시간으로 표시할 수 있다고 가정할 수 없습니다. 예를 들어 동아시아 지역 고객에게는 미국 동부 지역의 현재 시간을 표시하는 웹 페이지의 신뢰성이 떨어집니다. 이 항목에서는 한 표준 시간대에서 다른 표준 시간대로 변환하는 방법 및 표준 시간대 인식 수준이 제한된 [System.DateTimeOffset](xref:System.DateTimeOffset) 값을 변환하는 방법에 대해 설명합니다.

## <a name="converting-to-coordinated-universal-time"></a>UTC로 변환

UTC(협정 세계시)는 고정밀 원자 시간 표준입니다. 전세계의 표준 시간대가 UTC의 양 또는 음 오프셋으로 표시됩니다.. 따라서 UTC는 어느 정도 표준 시간대에서 자유롭거나 중립적인 시간을 제공합니다. 컴퓨터 간에 날짜 및 시간의 이식성이 중요한 경우 UTC 시간을 사용하는 것이 좋습니다. 개별 표준 시간대를 UTC로 변환하면 시간을 보다 쉽게 비교할 수 있습니다.

> [!NOTE]
> 또한 [DateTimeOffset](xref:System.DateTimeOffset) 구조체를 serialize하여 단일 시점을 명확하게 나타낼 수 있습니다. [DateTimeOffset](xref:System.DateTimeOffset) 개체에서는 날짜 및 시간 값과 자체의 UTC 오프셋을 함께 저장하므로 항상 UTC 기준의 특정 시점을 나타냅니다.

시간을 UTC로 변환하는 가장 쉬운 방법으로 `static`(Visual Basic의 경우 `Shared`) [TimeZoneInfo.ConvertTimeToUtc(DateTime)](https://msdn.microsoft.com/en-us/library/bb381744(v=vs.110).aspx) 메서드를 호출합니다. 

> [!IMPORTANT]
> `TimeZoneInfo.ConvertTimeToUtc(DateTime)` 메서드는 현재 .NET Core에서 사용할 수 없습니다. 

이 메서드에서 수행되는 정확한 변환은 다음 표에서 보여 주듯이 `DateTime` 매개 변수의 [Kind](xref:System.DateTime.Kind) 속성 값에 따라 다릅니다.

[DateTime.Kind](xref:System.DateTimeKind) 속성 | 변환
---------------------------------------------------------------------------------------------- | ----------
[DateTimeKind.Local](xref:System.DateTimeKind.Local) | 현지 시간을 UTC로 변환합니다.
[DateTimeKind.Unspecified](xref:System.DateTimeKind.Unspecified) | `DateTime` 매개 변수가 현지 시간인 것으로 가정하고 현지 시간을 UTC로 변환합니다.
[DateTimeKind.Utc](xref:System.DateTimeKind.Utc) | 변경하지 않은 `DateTime` 매개 변수를 반환합니다.

다음 코드에서는 현재 현지 시간을 UTC로 변환하고 그 결과를 콘솔에 표시합니다.

```csharp
DateTime dateNow = DateTime.Now;
Console.WriteLine("The date and time are {0} UTC.", 
                   TimeZoneInfo.ConvertTimeToUtc(dateNow));
```

```vb
Dim dateNow As Date = Date.Now      
Console.WriteLine("The date and time are {0} UTC.", _
                  TimeZoneInfo.ConvertTimeToUtc(dateNow))
```

> [!NOTE]
>[TimeZoneInfo.ConvertTimeToUtc(DateTime)](https://msdn.microsoft.com/en-us/library/bb381744(v=vs.110).aspx) 메서드에서 [TimeZone.ToUniversalTime](https://msdn.microsoft.com/en-us/library/System.TimeZone.ToUniversalTime(v=vs.110).aspx) 및 [DateTime.ToUniversalTime](xref:System.DateTime.ToUniversalTime) 메서드와 다른 결과를 생성할 수도 있습니다. 호스트 시스템의 현지 표준 시간대에 여러 조정 규칙이 포함된 경우 [TimeZoneInfo.ConvertTimeToUtc(DateTime)](https://msdn.microsoft.com/en-us/library/System.TimeZone.ConvertTimeToUtc(v=vs.110).aspx)는 특정 날짜 및 시간에 적절한 규칙을 적용합니다. 다른 두 메서드는 항상 최신 조정 규칙을 적용합니다.

날짜 및 시간 값이 현지 시간이나 UTC를 나타내지 않는 경우 [ToUniversalTime](https://msdn.microsoft.com/en-us/library/System.TimeZone.ToUniversalTime(v=vs.110).aspx) 메서드에서 잘못된 결과를 반환할 수 있습니다. 그러나 [TimeZoneInfo.ConvertTimeToUtc](https://msdn.microsoft.com/en-us/library/bb381744(v=vs.110).aspx) 메서드를 사용하면 지정된 표준 시간대의 날짜 및 시간을 변환할 수 있습니다. 대상 표준 시간대를 나타내는 TimeZoneInfo 개체 검색에 대한 자세한 내용은 [로컬 시스템에 정의된 표준 시간대 찾기](finding-the-time-zones-on-local-system.md)를 참조하세요. 다음 코드에서는 [TimeZoneInfo.ConvertTimeToUtc](https://msdn.microsoft.com/en-us/library/bb381744(v=vs.110).aspx) 메서드를 사용하여 동부 표준시를 UTC로 변환합니다.

```csharp
DateTime easternTime = new DateTime(2007, 01, 02, 12, 16, 00);
string easternZoneId = "Eastern Standard Time";
try
{
   TimeZoneInfo easternZone = TimeZoneInfo.FindSystemTimeZoneById(easternZoneId);
   Console.WriteLine("The date and time are {0} UTC.", 
                     TimeZoneInfo.ConvertTimeToUtc(easternTime, easternZone));
}
catch (TimeZoneNotFoundException)
{
   Console.WriteLine("Unable to find the {0} zone in the registry.", 
                     easternZoneId);
}                           
catch (InvalidTimeZoneException)
{
   Console.WriteLine("Registry data on the {0} zone has been corrupted.", 
                     easternZoneId);
}
```

```vb
Dim easternTime As New Date(2007, 01, 02, 12, 16, 00)
Dim easternZoneId As String = "Eastern Standard Time"
Try
   Dim easternZone As TimeZoneInfo = TimeZoneInfo.FindSystemTimeZoneById(easternZoneId)
   Console.WriteLine("The date and time are {0} UTC.", _ 
                     TimeZoneInfo.ConvertTimeToUtc(easternTime, easternZone))
Catch e As TimeZoneNotFoundException
   Console.WriteLine("Unable to find the {0} zone in the registry.", _
                     easternZoneId)
Catch e As InvalidTimeZoneException
   Console.WriteLine("Registry data on the {0} zone has been corrupted.", _ 
                     easternZoneId)
End Try    
```

[DateTime](xref:System.DateTime) 개체의 [Kind](xref:System.DateTimeKind) 속성과 표준 시간대가 일치하지 않는 경우 이 메서드에서 [ArgumentException](xref:System.ArgumentException)을 throw합니다. Kind 속성이 [DateTimeKind.Local](xref:System.DateTimeKind.Local)이지만 [TimeZoneInfo](xref:System.TimeZoneInfo) 개체에서 현지 표준 시간대를 나타내지 않는 경우 또는 Kind 속성이 [DateTimeKind.Utc](xref:System.DateTimeKind.Utc)이지만 [TimeZoneInfo](xref:System.TimeZoneInfo) 개체가 [DateTimeKind.Utc](xref:System.DateTimeKind.Utc)와 같지 않은 경우 불일치가 발생합니다.

이러한 메서드는 모두 [DateTime](xref:System.DateTime) 값을 매개 변수로 사용하고 [DateTime](xref:System.DateTime) 값을 반환합니다. [DateTimeOffset](xref:System.DateTimeOffset) 값의 경우[DateTimeOffset](xref:System.DateTimeOffset) 구조체에 현재 인스턴스의 날짜 및 시간을 UTC로 변환하는 [ToUniversalTime](xref:System.DateTimeOffset.ToUniversalTime) 인스턴스 메서드가 있습니다. 다음 예제에서는 [ToUniversalTime](xref:System.DateTimeOffset.ToUniversalTime) 메서드를 호출하여 현지 시간과 여러 다른 시간을 UTC로 변환합니다.

```csharp
DateTimeOffset localTime, otherTime, universalTime;

// Define local time in local time zone
localTime = new DateTimeOffset(new DateTime(2007, 6, 15, 12, 0, 0));
Console.WriteLine("Local time: {0}", localTime);
Console.WriteLine();

// Convert local time to offset 0 and assign to otherTime
otherTime = localTime.ToOffset(TimeSpan.Zero);
Console.WriteLine("Other time: {0}", otherTime);
Console.WriteLine("{0} = {1}: {2}", 
                  localTime, otherTime, 
                  localTime.Equals(otherTime));
Console.WriteLine("{0} exactly equals {1}: {2}", 
                  localTime, otherTime, 
                  localTime.EqualsExact(otherTime));
Console.WriteLine();

// Convert other time to UTC
universalTime = localTime.ToUniversalTime(); 
Console.WriteLine("Universal time: {0}", universalTime);
Console.WriteLine("{0} = {1}: {2}", 
                  otherTime, universalTime, 
                  universalTime.Equals(otherTime));
Console.WriteLine("{0} exactly equals {1}: {2}", 
                  otherTime, universalTime, 
                  universalTime.EqualsExact(otherTime));
Console.WriteLine();
// The example produces the following output to the console:
//    Local time: 6/15/2007 12:00:00 PM -07:00
//    
//    Other time: 6/15/2007 7:00:00 PM +00:00
//    6/15/2007 12:00:00 PM -07:00 = 6/15/2007 7:00:00 PM +00:00: True
//    6/15/2007 12:00:00 PM -07:00 exactly equals 6/15/2007 7:00:00 PM +00:00: False
//    
//    Universal time: 6/15/2007 7:00:00 PM +00:00
//    6/15/2007 7:00:00 PM +00:00 = 6/15/2007 7:00:00 PM +00:00: True
//    6/15/2007 7:00:00 PM +00:00 exactly equals 6/15/2007 7:00:00 PM +00:00: True 
```

```vb
Dim localTime, otherTime, universalTime As DateTimeOffset

' Define local time in local time zone
localTime = New DateTimeOffset(#6/15/2007 12:00:00PM#)
Console.WriteLine("Local time: {0}", localTime)
Console.WriteLine()

' Convert local time to offset 0 and assign to otherTime
otherTime = localTime.ToOffset(TimeSpan.Zero)
Console.WriteLine("Other time: {0}", otherTime)
Console.WriteLine("{0} = {1}: {2}", _
                  localTime, otherTime, _
                  localTime.Equals(otherTime))
Console.WriteLine("{0} exactly equals {1}: {2}", _ 
                  localTime, otherTime, _
                  localTime.EqualsExact(otherTime))
Console.WriteLine()

' Convert other time to UTC
universalTime = localTime.ToUniversalTime() 
Console.WriteLine("Universal time: {0}", universalTime)
Console.WriteLine("{0} = {1}: {2}", _
                  otherTime, universalTime, _ 
                  universalTime.Equals(otherTime))
Console.WriteLine("{0} exactly equals {1}: {2}", _ 
                  otherTime, universalTime, _
                  universalTime.EqualsExact(otherTime))
Console.WriteLine()
' The example produces the following output to the console:
'    Local time: 6/15/2007 12:00:00 PM -07:00
'    
'    Other time: 6/15/2007 7:00:00 PM +00:00
'    6/15/2007 12:00:00 PM -07:00 = 6/15/2007 7:00:00 PM +00:00: True
'    6/15/2007 12:00:00 PM -07:00 exactly equals 6/15/2007 7:00:00 PM +00:00: False
'    
'    Universal time: 6/15/2007 7:00:00 PM +00:00
'    6/15/2007 7:00:00 PM +00:00 = 6/15/2007 7:00:00 PM +00:00: True
'    6/15/2007 7:00:00 PM +00:00 exactly equals 6/15/2007 7:00:00 PM +00:00: True 
```

## <a name="converting-utc-to-a-designated-time-zone"></a>UTC를 지정한 표준 시간대로 변환

UTC를 현지 시간으로 변환하려면 다음에 나오는 [UTC를 현지 시간으로 변환](#converting-utc-to-local-time) 섹션을 참조하세요. 

UTC를 사용자가 지정하는 표준 시간대 시간으로 변환하려면 [ConvertTimeFromUtc](https://msdn.microsoft.com/en-us/library/System.TimeZoneInfo.converttimefromutc(v=vs.110).aspx) 메서드를 호출합니다. 

> [!IMPORTANT]
> `TimeZoneInfo.ConvertTimeFromUtc' 메서드는 현재 .NET Core에서 제공되고 있지 않습니다. 

이 메서드에서 사용하는 두 개의 매개 변수는 다음과 같습니다.

* 변환할 UTC. [Kind](xref:System.DateTime.Kind) 속성이 [DateTimeKind.Utc](xref:System.DateTimeKind.Utc) 또는 [DateTimeKind.Unspecified](xref:System.DateTimeKind.Unspecified)로 설정된 [DateTime](xref:System.DateTime) 값이어야 합니다. 

* UTC를 변환한 값이 속하게 될 표준 시간대 

다음 코드는 UTC를 중부 표준시로 변환합니다.

```csharp
DateTime timeUtc = DateTime.UtcNow;
try
{
   TimeZoneInfo cstZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");
   DateTime cstTime = TimeZoneInfo.ConvertTimeFromUtc(timeUtc, cstZone);
   Console.WriteLine("The date and time are {0} {1}.", 
                     cstTime, 
                     cstZone.IsDaylightSavingTime(cstTime) ?
                             cstZone.DaylightName : cstZone.StandardName);
}
catch (TimeZoneNotFoundException)
{
   Console.WriteLine("The registry does not define the Central Standard Time zone.");
}                           
catch (InvalidTimeZoneException)
{
   Console.WriteLine("Registry data on the Central Standard Time zone has been corrupted.");
}
```

```vb
Dim timeUtc As Date = Date.UtcNow
Try
   Dim cstZone As TimeZoneInfo = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time")
   Dim cstTime As Date = TimeZoneInfo.ConvertTimeFromUtc(timeUtc, cstZone)
   Console.WriteLine("The date and time are {0} {1}.", _
                     cstTime, _
                     IIf(cstZone.IsDaylightSavingTime(cstTime), _
                         cstZone.DaylightName, cstZone.StandardName))
Catch e As TimeZoneNotFoundException
   Console.WriteLine("The registry does not define the Central Standard Time zone.")
Catch e As InvalidTimeZoneException
   Console.WriteLine("Registry data on the Central Standard Time zone has been corrupted.")
End Try
``` 

## <a name="converting-utc-to-local-time"></a>UTC를 현지 시간으로 변환

UTC를 현지 시간으로 변환하려면 시간을 변환할 [DateTime](xref:System.DateTime) 개체의 [DateTime.ToLocalTime](xref:System.DateTime) 메서드를 호출합니다. 이 메서드의 정확한 동작은 다음 표에서 보여 주듯이 개체의 [Kind](xref:System.DateTime.Kind) 속성 값에 따라 다릅니다.

[DateTime.Kind](xref:System.DateTimeKind) 속성 | 변환
---------------------------------------------------------------------------------------------- | ----------
[DateTimeKind.Local](xref:System.DateTimeKind.Local) | 변경되지 않은 [DateTime](xref:System.DateTime) 값을 반환합니다.
[DateTimeKind.Unspecified](xref:System.DateTimeKind.Unspecified) | [DateTime](xref:System.DateTime) 값이 UTC인 것으로 가정하고 UTC를 현지 시간으로 변환합니다.
[DateTimeKind.Utc](xref:System.DateTimeKind.Utc) | [DateTime](xref:System.DateTime) 값을 현지 시간으로 변환합니다.

## <a name="converting-between-any-two-time-zones"></a>두 표준 시간대 간 변환

[TimeZoneInfo.ConvertTime](xref:System.TimeZoneInfo.ConvertTime(System.DateTime,System.TimeZoneInfo))을 사용하면 두 표준 시간대 간에 변환할 수 있습니다. 이 메서드의 매개 변수는 변환할 [DateTime](xref:System.DateTime) 값, 이 날짜 및 시간 값의 표준 시간대를 나타내는 [TimeZoneInfo](xref:System.TimeZoneInfo) 개체, 변환된 날짜 및 시간 값이 속하게 될 표준 시간대를 나타내는 [TimeZoneInfo](xref:System.TimeZoneInfo) 개체입니다.

이 메서드에서는 변환할 날짜 및 시간 값의 [Kind](xref:System.DateTime.Kind) 속성과 표준 시간대를 나타내는 [TimeZoneInfo](xref:System.TimeZoneInfo) 개체 또는 표준 시간대 식별자가 서로 일치해야 합니다. 그렇지 않으면 [ArgumentException](xref:System.ArgumentException)이 throw됩니다. 예를 들어 날짜 및 시간 값의 [Kind](xref:System.DateTime.Kind) 속성이 [DateTimeKind.Local](xref:System.DateTimeKind.Local)이면 메서드에 매개 변수로 전달된 [TimeZoneInfo](xref:System.TimeZoneInfo) 개체가 [TimeZoneInfo.Local](xref:System.TimeZoneInfo.Local)과 같지 않은 경우 예외가 throw됩니다. 또한 메서드에 매개 변수로 전달된 식별자가 [TimeZoneInfo.Id](xref:System.TimeZoneInfo.Id)와 같지 않은 경우에도 예외가 throw됩니다.

다음 예제에서는 [ConvertTime](xref:System.TimeZoneInfo.ConvertTime(System.DateTime,System.TimeZoneInfo)) 메서드를 사용하여 하와이 표준시를 현지 시간으로 변환합니다.

```csharp
DateTime hwTime = new DateTime(2007, 02, 01, 08, 00, 00);
try
{
   TimeZoneInfo hwZone = TimeZoneInfo.FindSystemTimeZoneById("Hawaiian Standard Time");
   Console.WriteLine("{0} {1} is {2} local time.", 
           hwTime, 
           hwZone.IsDaylightSavingTime(hwTime) ? hwZone.DaylightName : hwZone.StandardName, 
           TimeZoneInfo.ConvertTime(hwTime, hwZone, TimeZoneInfo.Local));
}
catch (TimeZoneNotFoundException)
{
   Console.WriteLine("The registry does not define the Hawaiian Standard Time zone.");
}                           
catch (InvalidTimeZoneException)
{
   Console.WriteLine("Registry data on the Hawaiian STandard Time zone has been corrupted.");
}
```

```vb
Dim hwTime As Date = #2/01/2007 8:00:00 AM#
Try
   Dim hwZone As TimeZoneInfo = TimeZoneInfo.FindSystemTimeZoneById("Hawaiian Standard Time")
   Console.WriteLine("{0} {1} is {2} local time.", _
                     hwTime, _
                     IIf(hwZone.IsDaylightSavingTime(hwTime), hwZone.DaylightName, hwZone.StandardName), _
                     TimeZoneInfo.ConvertTime(hwTime, hwZone, TimeZoneInfo.Local))
Catch e As TimeZoneNotFoundException
   Console.WriteLine("The registry does not define the Hawaiian Standard Time zone.")
Catch e As InvalidTimeZoneException
   Console.WriteLine("Registry data on the Hawaiian Standard Time zone has been corrupted.")
End Try
```

## <a name="converting-datetimeoffset-values"></a>DateTimeOffset 값 변환

[System.DateTimeOffset](xref:System.DateTimeOffset) 개체는 인스턴스화될 때 표준 시간대에서 분리되므로 이 개체에서 나타낸 날짜 및 시간 값은 표준 시간대를 완전하게 인식하지 않습니다. 그러나 많은 경우에는 응용 프로그램에서 특정 표준 시간대의 시간이 아니라 단순히 서로 다른 UTC 오프셋 두 개를 기준으로 날짜 및 시간을 변환하면 됩니다. 이러한 변환을 수행하려면 현재 인스턴스의 [ToOffset](xref:System.DateTimeOffset.ToOffset(System.TimeSpan)) 메서드를 호출합니다. 이 메서드의 단일 매개 변수는 메서드에서 반환할 새 날짜 및 시간 값의 오프셋을 나타내는 [TimeSpan](xref:System.TimeSpan)입니다.  

예를 들어 사용자가 웹 페이지에서 요청한 날짜 및 시간이 알려져 있고 이 값이 MM/dd/yyyy hh:mm:ss zzzz 형식의 문자열로 serialize된 경우 뒤따르는 `ReturnTimeOnServer` 메서드에서 이 날짜 및 시간 값을 웹 서버의 날짜 및 시간 값으로 변환합니다.

```csharp
public DateTimeOffset ReturnTimeOnServer(string clientString)
{
   string format = @"M/d/yyyy H:m:s zzz";
   TimeSpan serverOffset = TimeZoneInfo.Local.GetUtcOffset(DateTimeOffset.Now);

   try
   {      
      DateTimeOffset clientTime = DateTimeOffset.ParseExact(clientString, format, CultureInfo.InvariantCulture);
      DateTimeOffset serverTime = clientTime.ToOffset(serverOffset);
      return serverTime;
   }
   catch (FormatException)
   {
      return DateTimeOffset.MinValue;
   }
}
```

```vb
Public Function ReturnTimeOnServer(clientString As String) As DateTimeOffset
   Dim format As String = "M/d/yyyy H:m:s zzz"
   Dim serverOffset As TimeSpan = TimeZoneInfo.Local.GetUtcOffset(DateTimeOffset.Now)

   Try      
      Dim clientTime As DateTimeOffset = DateTimeOffset.ParseExact(clientString, format, CultureInfo.InvariantCulture)
      Dim serverTime As DateTimeOffset = clientTime.ToOffset(serverOffset)
      Return serverTime
   Catch e As FormatException
      Return DateTimeOffset.MinValue
   End Try    
End Function
```

UTC보다 다섯 시간 빠른 표준 시간대의 날짜 및 시간을 나타내는 "9/1/2007 5:32:07 -05:00" 문자열이 메서드에 전달되는 경우 이 메서드에서는 미국 태평양 표준 시간대에 있는 서버에 9/1/2007 3:32:07 AM -07:00을 반환합니다.

또한 [TimeZoneInfo](xref:System.TimeZoneInfo) 클래스에는 [System.DateTimeOffset](xref:System.DateTimeOffset) 값으로 표준 시간대 변환을 수행하기 위해 오버로드되는 [TimeZoneInfo.ConvertTime(DateTimeOffset, TimeZoneInfo)](xref:System.TimeZoneInfo.ConvertTime(System.DateTimeOffset,System.TimeZoneInfo)) 메서드가 포함됩니다. 이 메서드의 매개 변수는 [System.DateTimeOffset](xref:System.DateTimeOffset) 값 및 변환된 시간이 속하는 표준 시간대에 대한 참조입니다. 이 메서드를 호출하면 [System.DateTimeOffset](xref:System.DateTimeOffset) 값을 반환합니다. 예를 들어 이전 예제의 `ReturnTimeOnServer` 메서드를 다음과 같이 다시 작성하면 [ConvertTime(DateTimeOffset, TimeZoneInfo)](xref:System.TimeZoneInfo.ConvertTime(System.DateTimeOffset,System.TimeZoneInfo)) 메서드를 호출할 수 있습니다.

```csharp
public DateTimeOffset ReturnTimeOnServer(string clientString)
{
   string format = @"M/d/yyyy H:m:s zzz";

   try
   {      
      DateTimeOffset clientTime = DateTimeOffset.ParseExact(clientString, format, 
                                  CultureInfo.InvariantCulture);
      DateTimeOffset serverTime = TimeZoneInfo.ConvertTime(clientTime, 
                                  TimeZoneInfo.Local);
      return serverTime;
   }
   catch (FormatException)
   {
      return DateTimeOffset.MinValue;
   }
}
```

```vb
Public Function ReturnTimeOnServer(clientString As String) As DateTimeOffset
   Dim format As String = "M/d/yyyy H:m:s zzz"

   Try      
      Dim clientTime As DateTimeOffset = DateTimeOffset.ParseExact(clientString, format, CultureInfo.InvariantCulture)
      Dim serverTime As DateTimeOffset = TimeZoneInfo.ConvertTime(clientTime, TimeZoneInfo.Local)
      Return serverTime
   Catch e As FormatException
      Return DateTimeOffset.MinValue
   End Try    
End Function
```

## <a name="see-also"></a>참고 항목

[TimeZoneInfo](xref:System.TimeZoneInfo)

[날짜, 시간 및 표준 시간대](index.md)

[로컬 시스템에 정의된 표준 시간대 찾기](finding-the-time-zones-on-local-system.md)





<!--HONumber=Nov16_HO1-->


