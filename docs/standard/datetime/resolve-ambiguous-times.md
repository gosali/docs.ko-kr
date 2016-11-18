---
title: "방법: 모호한 시간 확인"
description: "모호한 시간을 확인하는 방법"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 08/16/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: e86050c6-d16d-405e-8bba-7205945c9a81
translationtype: Human Translation
ms.sourcegitcommit: c40c28da09e8a122b542463c197196c82c81dd19
ms.openlocfilehash: b31ea18cd7a7e32863f384cb279dc715fd62b3df

---

# <a name="how-to-resolve-ambiguous-times"></a>방법: 모호한 시간 확인

모호한 시간은 하나 이상의 UTC(협정 세계시)를 매핑하는 시간입니다. 표준 시간대의 일광 절약 시간에서 표준 시간으로 전환하는 것과 같이 클록 시간을 뒤로 조정하는 경우 발생합니다. 모호한 시간을 처리하는 경우 다음 중 하나를 수행할 수 있습니다.

* 시간을 UTC로 매핑하는 방법에 대해 가정합니다. 예를 들어 있는 모호한 시간을 항상 표준 시간대의 표준 시간으로 표시한다고 가정할 수 있습니다.

* 모호한 시간이 사용자로부터 입력된 데이터 항목인 경우 사용자가 모호성을 해결하도록 맡기면 됩니다.

이 문서에서는 모호한 시간이 표준 시간대의 표준 시간을 나타낸다고 가정하여 확인하는 방법을 보여 줍니다.

## <a name="to-map-an-ambiguous-time-to-a-time-zones-standard-time"></a>모호한 시간을 표준 시간대의 표준 시간으로 매핑하려면

1. [System.TimeZoneInfo.IsAmbiguousTime(DateTime)](xref:System.TimeZoneInfo.IsAmbiguousTime(System.DateTime)) 또는 [System.TimeZoneInfo.IsAmbiguousTime(DateTimeOffset)](xref:System.TimeZoneInfo.IsAmbiguousTime(System.DateTimeOffset)) 메서드를 호출하여 시간이 모호한지를 결정합니다.

2. 시간이 모호한 경우 표준 시간대의 [BaseUtcOffset](xref:System.TimeZoneInfo.BaseUtcOffset) 속성에서 반환된 [TimeSpan](xref:System.TimeSpan) 개체에서 시간을 뺍니다.

3. `static`(Visual basic의 `Shared`) [SpecifyKind](xref:System.DateTime.SpecifyKind(System.DateTime,System.DateTimeKind)) 메서드를 호출하여 UTC 날짜 및 시간 값의 [Kind](xref:System.DateTime.Kind) 속성을 [DateTimeKind.Utc](xref:System.DateTimeKind.Utc)로 설정합니다.

## <a name="example"></a>예제

다음 예제에서는 모호한 [DateTime](xref:System.DateTime)이 현지 표준 시간대의 표준 시간을 나타낸다고 가정하여 UTC로 변환하는 방법을 설명합니다. 

```csharp
private DateTime ResolveAmbiguousTime(DateTime ambiguousTime)
{
   // Time is not ambiguous
   if (! TimeZoneInfo.Local.IsAmbiguousTime(ambiguousTime))
   { 
      return ambiguousTime; 
   }
   // Time is ambiguous
   else
   {
      DateTime utcTime = DateTime.SpecifyKind(ambiguousTime - TimeZoneInfo.Local.BaseUtcOffset, 
                                              DateTimeKind.Utc);      
      Console.WriteLine("{0} local time corresponds to {1} {2}.", 
                        ambiguousTime, utcTime, utcTime.Kind.ToString());
      return utcTime;            
   }   
}
```

```vb
Private Function ResolveAmbiguousTime(ambiguousTime As Date) As Date
   ' Time is not ambiguous
   If Not TimeZoneInfo.Local.IsAmbiguousTime(ambiguousTime) Then 
      Return TimeZoneInfo.ConvertTimeToUtc(ambiguousTime) 
   ' Time is ambiguous
   Else
      Dim utcTime As Date = DateTime.SpecifyKind(ambiguousTime - TimeZoneInfo.Local.BaseUtcOffset, DateTimeKind.Utc)      
      Console.WriteLine("{0} local time corresponds to {1} {2}.", ambiguousTime, utcTime, utcTime.Kind.ToString())
      Return utcTime            
   End If   
End Function
```

예제에서는 전달된 [DateTime](xref:System.DateTime) 값이 모호한지 여부를 결정하는 `ResolveAmbiguousTime`라는 메서드로 구성됩니다. 값이 모호한 경우 메서드는 해당 UTC 시간을 나타내는 [DateTime](xref:System.DateTime) 값을 반환합니다. 메서드는 현지 시간에서 현지 표준 시간대의 [BaseUtcOffset](xref:System.TimeZoneInfo.BaseUtcOffset) 속성 값을 빼서 이 변환을 처리합니다. 

일반적으로 모호한 시간에서 가능한 UTC 오프셋을 포함하는 [TimeSpan](xref:System.TimeSpan) 개체의 배열을 검색하는 [GetAmbiguousTimeOffsets](xref:System.TimeZoneInfo.GetAmbiguousTimeOffsets(System.DateTime)) 메서드를 호출하여 모호한 시간을 처리합니다. 그러나 이 예제에서는 모호한 시간이 표준 시간대의 표준 시간으로 항상 매핑되어야 한다고 임의로 가정합니다. [BaseUtcOffset](xref:System.TimeZoneInfo.BaseUtcOffset) 속성은 UTC와 표준 시간대의 표준 시간 간에 오프셋을 반환합니다.

## <a name="see-also"></a>참고 항목

[날짜, 시간 및 표준 시간대](index.md)

[방법: 사용자의 모호한 시간 확인 허용](let-users-resolve-ambiguous-times.md)




<!--HONumber=Nov16_HO3-->


