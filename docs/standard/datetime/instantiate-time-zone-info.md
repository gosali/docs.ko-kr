---
title: "방법: TimeZoneInfo 개체 인스턴스화"
description: "방법: TimeZoneInfo 개체 인스턴스화"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 08/15/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: bff137e5-d550-44c3-b460-b3f2dabd4035
translationtype: Human Translation
ms.sourcegitcommit: c40c28da09e8a122b542463c197196c82c81dd19
ms.openlocfilehash: 1c619cf6bd150e009367b43d1d83fa1713ec2690

---

# <a name="how-to-instantiate-a-timezoneinfo-object"></a>방법: TimeZoneInfo 개체 인스턴스화

[TimeZoneInfo](xref:System.TimeZoneInfo) 개체를 인스턴스화하는 가장 일반적인 방법은 운영 체제에서 해당 정보를 검색하는 것입니다. 이 항목에서는 로컬 시스템에서 [TimeZoneInfo](xref:System.TimeZoneInfo) 개체를 인스턴스화하는 방법을 설명합니다.

## <a name="to-instantiate-a-timezoneinfo-object"></a>TimeZoneInfo 개체를 인스턴스화하려면

1. [TimeZoneInfo](xref:System.TimeZoneInfo) 개체를 선언합니다.

2. `static`(Visual Basic의 `Shared`) [TimeZoneInfo.FindSystemTimeZoneById](xref:System.TimeZoneInfo.FindSystemTimeZoneById(System.String)) 메서드를 호출합니다.

3. 메서드에서 throw한 예외를 처리합니다.

## <a name="example"></a>예제

다음 코드는 동부 표준시 표준 시간대를 나타내고 현지 시간에 해당하는 동부 표준시를 표시하는 [TimeZoneInfo](xref:System.TimeZoneInfo) 개체를 검색합니다.

```csharp
DateTime timeNow = DateTime.Now;
try
{
   TimeZoneInfo easternZone = TimeZoneInfo.FindSystemTimeZoneById("Eastern Standard Time");
   DateTime easternTimeNow = TimeZoneInfo.ConvertTime(timeNow, TimeZoneInfo.Local, 
                                                   easternZone);
   Console.WriteLine("{0} {1} corresponds to {2} {3}.",
                     timeNow, 
                     TimeZoneInfo.Local.IsDaylightSavingTime(timeNow) ?
                               TimeZoneInfo.Local.DaylightName : 
                               TimeZoneInfo.Local.StandardName,
                     easternTimeNow, 
                     easternZone.IsDaylightSavingTime(easternTimeNow) ?
                                 easternZone.DaylightName : 
                                 easternZone.StandardName);
}
// Handle exception
//
// As an alternative to simply displaying an error message, an alternate Eastern
// Standard Time TimeZoneInfo object could be instantiated here either by restoring
// it from a serialized string or by providing the necessary data to the
// CreateCustomTimeZone method.
catch (InvalidTimeZoneException)
{
   Console.WriteLine("The Eastern Standard Time Zone contains invalid or missing data.");
}
catch (SecurityException)
{
   Console.WriteLine("The application lacks permission to read time zone information from the registry.");
}
catch (OutOfMemoryException)
{
   Console.WriteLine("Not enough memory is available to load information on the Eastern Standard Time zone.");
}
// If we weren't passing FindSystemTimeZoneById a literal string, we also 
// would handle an ArgumentNullException.
```

```vb
Dim timeNow As Date = Date.Now
Try
   Dim easternZone As TimeZoneInfo = TimeZoneInfo.FindSystemTimeZoneById("Eastern Standard Time")
   Dim easternTimeNow As Date = TimeZoneInfo.ConvertTime(timeNow, TimeZoneInfo.Local, easternZone)
   Console.WriteLine("{0} {1} corresponds to {2} {3}.", _
                     timeNow, _
                     IIf(TimeZoneInfo.Local.IsDaylightSavingTime(timeNow), _
                         TimeZoneInfo.Local.DaylightName, TimeZoneInfo.Local.StandardName), _
                     easternTimeNow, _
                     IIf(easternZone.IsDaylightSavingTime(easternTimeNow), _
                         easternZone.DaylightName, easternZone.StandardName))
' Handle exception
'
' As an alternative to simply displaying an error message, an alternate Eastern
' Standard Time TimeZoneInfo object could be instantiated here either by restoring
' it from a serialized string or by providing the necessary data to the
' CreateCustomTimeZone method.
Catch e As InvalidTimeZoneException
   Console.WriteLine("The Eastern Standard Time Zone contains invalid or missing data.")   
Catch e As SecurityException
   Console.WriteLine("The application lacks permission to read time zone information from the registry.")
Catch e As OutOfMemoryException
   Console.WriteLine("Not enough memory is available to load information on the Eastern Standard Time zone.")
' If we weren't passing FindSystemTimeZoneById a literal string, we also 
' would handle an ArgumentNullException.
End
``` 

[TimeZoneInfo.FindSystemTimeZoneById](xref:System.TimeZoneInfo.FindSystemTimeZoneById(System.String)) 메서드의 단일 매개 변수는 개체의 [TimeZoneInfo.Id](xref:System.TimeZoneInfo.Id) 속성에 해당하는, 검색할 표준 시간대의 식별자입니다. 표준 시간대 식별자는 표준 시간대를 고유하게 식별하는 키 필드입니다. 대부분 키는 비교적 짧지만 표준 시간대 식별자는 비교적 깁니다. 대부분의 경우 해당 값은 표준 시간대의 표준 시간 이름을 제공하는 데 사용되는 [TimeZoneInfo](xref:System.TimeZoneInfo) 개체의 [StandardName](xref:System.TimeZoneInfo.StandardName) 속성에 해당합니다. 그러나 예외 사항이 있습니다. 유효한 식별자를 제공하는지 확인하는 가장 좋은 방법은 시스템에서 사용 가능한 표준 시간대를 열거하고 표시된 표준 시간대의 식별자를 확인하는 것입니다. 설명은 [방법: 컴퓨터에 있는 표준 시간대 열거](enumerate-time-zones.md)를 참조하세요. [로컬 시스템에 정의된 표준 시간대 찾기](finding-the-time-zones-on-local-system.md) 항목은 선택한 표준 시간대 식별자의 목록도 포함합니다.

표준 시간대가 있는 경우 메서드는 해당 [TimeZoneInfo](xref:System.TimeZoneInfo) 개체를 반환합니다. 표준 시간대가 있지만 해당 데이터가 손상되었거나 불완전한 경우 메서드에서는 [InvalidTimeZoneException](xref:System.InvalidTimeZoneException)을 throw합니다. 

## <a name="see-also"></a>참고 항목

[날짜, 시간 및 표준 시간대](index.md)

[로컬 시스템에 정의된 표준 시간대 찾기](finding-the-time-zones-on-local-system.md)


<!--HONumber=Nov16_HO3-->


