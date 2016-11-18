---
title: "날짜, 시간 및 표준 시간대"
description: "날짜, 시간 및 표준 시간대"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 07/22/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 76e6cacc-1c0c-4a71-8cb8-018c112385ba
translationtype: Human Translation
ms.sourcegitcommit: c40c28da09e8a122b542463c197196c82c81dd19
ms.openlocfilehash: 74ba5a2e350db21cdb5a8c228c745ac3f54ed930

---

# <a name="dates-times-and-time-zones"></a>날짜, 시간 및 표준 시간대

기본 [System.DateTime](xref:System.DateTime) 구조 외에도 .NET에서는 표준 시간대를 사용할 수 있는 클래스를 지원합니다.

* [System.TimeZoneInfo](xref:System.TimeZoneInfo)
    
  이 클래스를 사용하여 시스템의 현지 표준 시간대와 UTC(협정 세계시) 영역을 사용합니다.
  
* [System.DateTimeOffset](xref:System.DateTimeOffset)  

  이 구조를 사용하여 UTC의 해당 오프셋(또는 차이)가 알려진 날짜 및 시간을 사용합니다. [DateTimeOffset](xref:System.DateTimeOffset)] 구조는 날짜 및 시간 값을 UTC의 해당 시간 오프셋과 결합합니다. UTC와의 관련성으로 인해 개별 날짜 및 시간 값은 단일 지점을 명확하게 식별합니다. 이렇게 하면 [DateTime](xref:System.DateTime)] 값보다 [DateTimeOffset](xref:System.DateTimeOffset)] 값이 한 컴퓨터에서 다른 컴퓨터로 이식 가능합니다. 
  
설명서의 이 섹션에서는 표준 시간대로 작업하고 날짜 및 시간 한 시간 표준 시간대에서 다른 표준 시간대로 변환할 수 있는 표준 시간대 인식 응용 프로그램을 만들기 위해 필요한 정보를 제공합니다.

## <a name="in-this-section"></a>단원 내용

[표준 시간대 개요](time-zone-overview.md) - 표준 시간대 인식 응용 프로그램을 만드는 데 관련된 용어, 개념 및 문제에 대해 설명합니다.
    
[DateTime, DateTimeOffset, TimeSpan 및 TimeZoneInfo 중 선택](choosing-between-datetime.md) - 날짜 및 시간 데이터를 사용하는 경우 [System.DateTime](xref:System.DateTime), [System.DateTimeOffset](xref:System.DateTimeOffset) 및 [System.TimeZoneInfo](xref:System.TimeZoneInfo) 형식을 사용할 시기를 설명합니다.
    
[로컬 시스템에 정의된 표준 시간대 찾기](finding-the-time-zones-on-local-system.md) - 로컬 시스템에서 발견된 표준 시간대를 열거하는 방법에 대해 설명합니다.

[DateTimeOffset 개체 인스턴스화](instantiating-a-datetimeoffset-object.md) - [System.DateTimeOffset](xref:System.DateTimeOffset) 개체를 인스턴스화할 수 있는 방법 및 [System.DateTime](xref:System.DateTime) 값을 [System.DateTimeOffset](xref:System.DateTimeOffset) 값으로 변환할 수 있는 방법에 대해 설명합니다.

[날짜 및 시간에 대한 산술 연산 수행](performing-arithmetic-operations.md) - [System.DateTime](xref:System.DateTime) 및 [System.DateTimeOffset](xref:System.DateTimeOffset) 값의 추가, 빼기 및 비교와 관련된 문제에 대해 설명합니다.

[DateTime 및 DateTimeOffset 간의 변환](converting-between-datetime-and-offset.md) - [System.DateTime](xref:System.DateTime) 및 [System.DateTimeOffset](xref:System.DateTimeOffset) 값 간에 변환하는 방법에 대해 설명합니다.

[표준 시간대 간에 시간 변환](converting-between-time-zones.md) - 한 표준 시간대를 다른 표준 시간대로 변환하는 방법에 대해 설명합니다.

[방법: 컴퓨터에 있는 표준 시간대 열거](enumerate-time-zones.md) - 컴퓨터의 레지스트리에 정의된 표준 시간대를 열거하고 사용자가 목록에서 미리 정의된 표준 시간대를 선택할 수 있는 예제를 제공합니다.

[방법: 미리 정의된 UTC 및 현지 표준 시간대 개체에 액세스](access-utc-and-local.md) - 협정 세계시 및 현지 표준 시간대에 액세스하는 방법에 대해 설명합니다.

[방법: TimeZoneInfo 개체 인스턴스화](instantiate-time-zone-info.md) - 로컬 시스템 레지스트리에서 [System.TimeZoneInfo](xref:System.TimeZoneInfo) 개체를 인스턴스화하는 방법에 대해 설명합니다.

[방법: 날짜 및 시간 산술 연산의 표준 시간대 사용](use-time-zones-in-arithmetic.md) - 표준 시간대의 조정 규칙을 반영하는 날짜 및 시간 산술 연산을 수행하는 방법에 대해 설명합니다.

[방법: 모호한 시간 확인](resolve-ambiguous-times.md) - 모호한 시간을 표준 시간대의 표준 시간에 매핑하여 해결하는 방법에 대해 설명합니다.

[방법: 사용자의 모호한 시간 확인 작업 허용](let-users-resolve-ambiguous-times.md) - 사용자가 모호한 현지 시간과 협정 세계시 간의 매핑을 확인하도록 하는 방법을 설명합니다.

## <a name="reference"></a>참조

[System.TimeZoneInfo](xref:System.TimeZoneInfo)

[System.DateTimeOffset](xref:System.DateTimeOffset)

[System.DateTime](xref:System.DateTime)



<!--HONumber=Nov16_HO3-->


