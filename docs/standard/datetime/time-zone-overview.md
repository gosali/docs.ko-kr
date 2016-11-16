---
title: "표준 시간대 개요"
description: "표준 시간대 개요"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 08/16/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: e3a10f62-d403-4441-8621-adc964e32c07
translationtype: Human Translation
ms.sourcegitcommit: c40c28da09e8a122b542463c197196c82c81dd19
ms.openlocfilehash: 31a4582c28c208a7a3d86259b360e3472b851249

---

# <a name="time-zone-overview"></a>표준 시간대 개요

[System.TimeZoneInfo](xref:System.TimeZoneInfo) 클래스는 표준 시간대 인식 응용 프로그램을 만드는 과정을 간소화합니다. [TimeZoneInfo](xref:System.TimeZoneInfo) 클래스는 레지스트리에서 미리 정의된 정보에 대한 표준 시간대뿐만 아니라 현지 표준 시간대와 UTC(협정 세계시)를 사용하도록 지원합니다. [TimeZoneInfo](xref:System.TimeZoneInfo)를 사용하여 시스템에 해당 정보가 없는 사용자 지정 표준 시간대를 정의할 수 있습니다.

## <a name="time-zone-essentials"></a>표준 시간대 Essentials

표준 시간대는 동일한 시간이 사용되는 지리적 영역입니다. 항상 그렇지는 않지만 일반적으로 인접한 표준 시간대는 1시간 간격입니다. 전세계 표준 시간대의 시간은 UTC(협정 세계시) 오프셋으로 표현할 수 있습니다.

전세계 표준 시간대는 대부분 일광 절약 시간을 지원합니다. 일광 절약 시간제는 봄이나 초기 여름에 시간을 한 시간 앞으로 이동하고 늦은 여름이나 가을에 일반(또는 표준) 시간으로 돌아와서 일광 시간을 극대화하려고 합니다. 표준 시간 간의 이러한 변경 내용은 조정 규칙으로 알려져 있습니다.

특정 표준 시간대의 일광 절약 시간 간의 전환은 고정 또는 부동 조정 규칙에서 정의될 수 있습니다. 고정 조정 규칙은 일광 절약 시간 간의 전환이 매년 발생하는 특정 날짜를 설정합니다. 예를 들어, 매년 10월 25일에 발생하는 일광 절약 시간에서 표준 시간으로의 전환은 고정 조정 규칙을 따릅니다. 보다 일반적인 규칙은 유동 조정 규칙으로, 일광 절약 시간 간의 전환에 대한 특정 월, 주, 날짜를 설정합니다. 예를 들어 3월의 세 번째 일요일에 발생하는 표준 시간에서 일광 절약 시간으로의 전환은 부동 조정 규칙을 따릅니다.

조정 규칙을 지원하는 표준 시간대의 경우 일광 절약 시간제 간의 전환으로 인해 잘못된 시간과 모호한 시간이라는 두 가지 예외적인 시간이 생성됩니다. 잘못된 시간은 표준 시간에서 일광 절약 시간으로의 전환에서 만든 존재하지 않는 시간입니다. 예를 들어 이 전환이 특정 일의 오전 2시에 발생하고 시간을 오전 3시로 변경하면 오전 2시와 오전 2:59:99 사이의 각 시간 간격은 유효하지 않습니다. 모호한 시간은 단일 표준 시간대에서 두 개의 서로 다른 시간에 매핑될 수 있는 시간입니다. 일광 절약 시간에서 표준 시간으로의 전환에서 생성됩니다. 예를 들어 이 전환이 특정 일의 오전 2시에 발생하고 시간을 오전 1시로 변경하면 오전 1시와 오전 1:59:99 사이의 각 시간 간격은 표준 시간이나 일광 절약 시간으로 해석될 수 있습니다. 

## <a name="time-zone-terminology"></a>표준 시간대 용어

다음 테이블에서는 표준 시간대를 사용하고 표준 시간대 인식 응용 프로그램을 개발할 때 일반적으로 사용되는 용어를 정의합니다.

용어 | 정의
---- | ----------
조정 규칙 | 표준 시간에서 일광 절약 시간으로의 전환 및 일광 절약 시간에서 표준 시간으로의 전환이 발생하는 시기를 정의하는 규칙입니다. 규칙 적용 시기(예: 조정 규칙은 1986년 1월 1일에서 2020년 12월 31일까지 적용됨), 델타(표준 시간이 조정 규칙 응용 프로그램의 결과로 변경되는 시간의 양) 및 조정 기간 중에 발생하는 전환의 특정 날짜와 시간에 대한 정보를 정의하는 시작 및 종료 날짜가 각 조정 규칙에 포함됩니다. 전환은 고정 규칙 또는 유동 규칙을 따르면 됩니다.
모호한 시간 | 단일 표준 시간대에서 두 개의 서로 다른 시간에 매핑될 수 있는 시간입니다. 표준 시간대의 일광 절약 시간에서 표준 시간으로 전환하는 것과 같이 클록 시간을 뒤로 조정하는 경우 발생합니다. 예를 들어 이 전환이 특정 일의 오전 2시에 발생하고 시간을 오전 1시로 변경하면 오전 1시와 오전 1:59:99 사이의 각 시간 간격은 표준 시간이나 일광 절약 시간으로 해석될 수 있습니다. 
고정 규칙 | 조정 규칙은 일광 절약 시간 간의 전환에 대한 특정 날짜를 설정합니다. 예를 들어, 매년 10월 25일에 발생하는 일광 절약 시간에서 표준 시간으로의 전환은 고정 조정 규칙을 따릅니다.
유동 규칙 | 조정 규칙은 일광 절약 시간 간의 전환에 대한 특정 월, 주, 날짜를 설정합니다. 예를 들어 3월의 세 번째 일요일에 발생하는 표준 시간에서 일광 절약 시간으로의 전환은 부동 조정 규칙을 따릅니다.
잘못된 형식 | 표준 시간에서 일광 절약 시간으로의 전환 아티팩트인 존재하지 않는 시간입니다. 표준 시간대의 표준 시간에서 일광 절약 시간으로 전환하는 것과 같이 클록 시간을 앞으로 조정하는 경우 발생합니다. 예를 들어 이 전환이 특정 일의 오전 2시에 발생하고 시간을 오전 3시로 변경하면 오전 2시와 오전 2:59:99 사이의 각 시간 간격은 유효하지 않습니다.
전환 시간 | 특정 표준 시간대에서 일광 절약 시간제를 표준 시간으로 변경하거나 그 반대의 경우와 같은 특정 시간 변경에 대한 정보입니다.

## <a name="time-zones-and-the-timezoneinfo-class"></a>표준 시간대 및 TimeZoneInfo 클래스

.NET에서 [System.TimeZoneInfo](xref:System.TimeZoneInfo) 개체는 운영 체제에서 제공하는 정보에 따라 표준 시간대를 나타냅니다. 운영 체제에 대한 [TimeZoneInfo](xref:System.TimeZoneInfo) 클래스의 종속성이란 표준 시간대 인식 응용 프로그램이 특정 표준 시간대를 모든 운영 체제에 정의했는지 확신할 수 없음을 의미합니다. 결과적으로 현지 표준 시간대 또는 UTC를 나타내는 표준 시간대 이외의 특정 표준 시간대를 인스턴스화하려면 예외 처리를 사용해야 합니다. 또한 필수 [TimeZoneInfo](xref:System.TimeZoneInfo) 개체를 인스턴스화할 수 없는 경우 응용 프로그램을 계속 진행하는 메서드를 제공해야 합니다.

각 표준 시간대가 기존 조정 규칙을 반영하는 UTC의 오프셋뿐만 아니라 UTC의 기본 오프셋을 특징으로 하기 때문에 표준 시간대의 시간은 다른 표준 시간대의 시간으로 쉽게 변환될 수 있습니다. 이를 위해 [TimeZoneInfo](xref:System.TimeZoneInfo) 개체는 다음을 비롯한 여러 가지 변환 메서드를 포함합니다.

* [ConvertTime(DateTime, TimeZoneInfo)](xref:System.TimeZoneInfo.ConvertTime(System.DateTime,System.TimeZoneInfo)), [System.DateTime](xref:System.DateTime)을 특정 표준 시간대의 시간으로 변환합니다.

* [ConvertTime(DateTime, TimeZoneInfo, TimeZoneInfo)](xref:System.TimeZoneInfo.ConvertTime(System.DateTime,System.TimeZoneInfo,System.TimeZoneInfo)), [DateTime](xref:System.DateTime)을 한 표준 시간대에서 다른 표준 시간대로 변환합니다.

* [ConvertTime(DateTimeOffset, TimeZoneInfo)](xref:System.TimeZoneInfo.ConvertTime(System.DateTimeOffset,System.TimeZoneInfo)), [System.DateTimeOffset](xref:System.DateTimeOffset)을 특정 표준 시간대의 시간으로 변환합니다. 

표준 시간대 간의 시간 변환에 대한 세부 정보는 [표준 시간대 간의 시간 변환](converting-between-time-zones.md)을 참조하세요.

## <a name="see-also"></a>참고 항목

[날짜, 시간 및 표준 시간대](index.md)


<!--HONumber=Nov16_HO1-->


