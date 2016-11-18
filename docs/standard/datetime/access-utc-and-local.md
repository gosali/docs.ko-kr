---
title: "방법: 미리 정의된 UTC 및 현지 표준 시간대 개체에 대한 액세스"
description: "미리 정의된 UTC 및 현지 표준 시간대 개체에 액세스하는 방법"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 08/11/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 13454d47-d957-421b-9ecd-940058b8835e
translationtype: Human Translation
ms.sourcegitcommit: c40c28da09e8a122b542463c197196c82c81dd19
ms.openlocfilehash: ab419cf365b61399ea41e99c15e276584ad0db31

---

# <a name="how-to-access-the-predefined-utc-and-local-time-zone-objects"></a>방법: 미리 정의된 UTC 및 현지 표준 시간대 개체에 대한 액세스

[System.TimeZoneInfo](xref:System.TimeZoneInfo) 클래스에는 두 가지 속성, `Utc` 및 `Local`이 있는데 미리 정의된 표준 시간대 개체에 대한 액세스 코드를 제공합니다. 이 항목에서는 이러한 속성들이 반환하는 `TimeZoneInfo` 개체에 액세스하는 방법에 설명합니다.

## <a name="to-access-the-coordinated-universal-time-utc-timezoneinfo-object"></a>UTC TimeZoneInfo 개체에 액세스하려면

1. **static**(Visual Basic의 경우는 **Shared**) [TimeZoneInfo.Utc](xref:System.TimeZoneInfo.Utc) 속성을 사용하여 UTC(협정 세계시)에 액세스합니다.

2. 속성에서 반환하는 [TimeZoneInfo](xref:System.TimeZoneInfo) 개체를 개체 변수에 할당하는 대신 [TimeZoneInfo.Utc](xref:System.TimeZoneInfo.Utc) 속성을 통해 UTC에 계속 액세스합니다.


## <a name="to-access-the-local-time-zone"></a>현지 표준 시간대 TimeZoneInfo 개체에 액세스하려면

1. **static**(Visual Basic의 경우 **Shared**) [TimeZoneInfo.Local](xref:System.TimeZoneInfo.Local) 속성을 사용하여 로컬 시스템 표준 시간대에 액세스합니다.

2. 속성에서 반환하는 [TimeZoneInfo](xref:System.TimeZoneInfo) 개체를 개체 변수에 할당하는 대신 [TimeZoneInfo.Local](xref:System.TimeZoneInfo.Local) 속성을 통해 표준 시간대에 계속 액세스합니다.

## <a name="example"></a>예제

다음 코드에서는 [TimeZoneInfo.Local](xref:System.TimeZoneInfo.Local) 및 [TimeZoneInfo.Utc](xref:System.TimeZoneInfo.Utc) 속성을 사용하여 시간을 미국 및 캐나다 동부 표준시에서 변환하고 해당 표준시 이름을 콘솔에 표시합니다.

```csharp
// Create Eastern Standard Time value and TimeZoneInfo object      
DateTime estTime = new DateTime(2007, 1, 1, 00, 00, 00);
string timeZoneName = "Eastern Standard Time";
try
{
   TimeZoneInfo est = TimeZoneInfo.FindSystemTimeZoneById(timeZoneName);

   // Convert EST to local time
   DateTime localTime = TimeZoneInfo.ConvertTime(estTime, est, TimeZoneInfo.Local);
   Console.WriteLine("At {0} {1}, the local time is {2} {3}.", 
           estTime, 
           est, 
           localTime, 
           TimeZoneInfo.Local.IsDaylightSavingTime(localTime) ?
                     TimeZoneInfo.Local.DaylightName : 
                     TimeZoneInfo.Local.StandardName);

   // Convert EST to UTC
   DateTime utcTime = TimeZoneInfo.ConvertTime(estTime, est, TimeZoneInfo.Utc);
   Console.WriteLine("At {0} {1}, the time is {2} {3}.", 
           estTime, 
           est, 
           utcTime, 
           TimeZoneInfo.Utc.StandardName);
}
catch (TimeZoneNotFoundException)
{
   Console.WriteLine("The {0} zone cannot be found in the registry.", 
                     timeZoneName);
}
catch (InvalidTimeZoneException)
{
   Console.WriteLine("The registry contains invalid data for the {0} zone.", 
                     timeZoneName);
}
```

```vb
' Create Eastern Standard Time value and TimeZoneInfo object      
Dim estTime As Date = #01/01/2007 00:00:00#
Dim timeZoneName As String = "Eastern Standard Time"
Try
   Dim est As TimeZoneInfo = TimeZoneInfo.FindSystemTimeZoneById(timeZoneName)

   ' Convert EST to local time
   Dim localTime As Date = TimeZoneInfo.ConvertTime(estTime, est, TimeZoneInfo.Local)
   Console.WriteLine("At {0} {1}, the local time is {2} {3}.", _
           estTime, _
           est, _
           localTime, _
           IIf(TimeZoneInfo.Local.IsDaylightSavingTime(localTime), _
               TimeZoneInfo.Local.DaylightName, _
               TimeZoneInfo.Local.StandardName))

   ' Convert EST to UTC
   Dim utcTime As Date = TimeZoneInfo.ConvertTime(estTime, est, TimeZoneInfo.Utc)
   Console.WriteLine("At {0} {1}, the time is {2} {3}.", _
           estTime, _
           est, _
           utcTime, _
           TimeZoneInfo.Utc.StandardName)
Catch e As TimeZoneNotFoundException
   Console.WriteLine("The {0} zone cannot be found in the registry.", _
                     timeZoneName)
Catch e As InvalidTimeZoneException
   Console.WriteLine("The registry contains invalid data for the {0} zone.", _
                     timeZoneName)
End Try
```

현지 표준 시간대를 [TimeZoneInfo](xref:System.TimeZoneInfo) 개체 변수에 할당하는 대신 항상 [TimeZoneInfo.Local](xref:System.TimeZoneInfo.Local) 속성을 통해 현지 표준 시간대에 액세스해야 합니다. 마찬가지로 UTC 시간대를 [TimeZoneInfo](xref:System.TimeZoneInfo) 개체 변수에 할당하는 대신 항상 [TimeZoneInfo.LocalTimeZoneInfo.Utc](xref:System.TimeZoneInfo.Utc) 속성을 통해 UTC에 액세스해야 합니다. 이렇게 하면 외부 메서드에서 [TimeZoneInfo](xref:System.TimeZoneInfo) 개체 변수를 무효화하지 않습니다.


## <a name="see-also"></a>참고 항목

[날짜, 시간 및 표준 시간대](index.md)

[로컬 시스템에 정의된 표준 시간대 찾기](finding-the-time-zones-on-local-system.md)



<!--HONumber=Nov16_HO3-->


