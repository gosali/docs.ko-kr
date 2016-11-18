---
title: "로컬 시스템에 정의된 표준 시간대 찾기"
description: "로컬 시스템에 정의된 표준 시간대 찾기"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 08/15/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 3a6ee323-f3cf-486d-aa0c-103931f1eba9
translationtype: Human Translation
ms.sourcegitcommit: c40c28da09e8a122b542463c197196c82c81dd19
ms.openlocfilehash: e7f07a1f86ca31a24e25d98de2f8918ff098db24

---

# <a name="finding-the-time-zones-defined-on-a-local-system"></a>로컬 시스템에 정의된 표준 시간대 찾기

[System.TimeZoneInfo](xref:System.TimeZoneInfo) 클래스는 public 생성자를 노출하지 않습니다. 따라서 `new` 키워드는 새 [TimeZoneInfo](xref:System.TimeZoneInfo) 개체를 만드는 데 사용할 수 없습니다. 대신 [TimeZoneInfo](xref:System.TimeZoneInfo) 개체는 운영 체제에서 미리 정의된 표준 시간대 정보를 검색하여 인스턴스화됩니다. 이 항목에서는 운영 체제에서 저장한 데이터로부터 표준 시간대를 인스턴스화하는 방법을 설명합니다. 또한 [TimeZoneInfo](xref:System.TimeZoneInfo) 클래스의 `static`(Visual Basic의 경우 `Shared`) 속성은 협정 세계시(UTC) 및 현지 표준 시간대에 대한 액세스를 제공합니다.

## <a name="accessing-individual-time-zones"></a>개별 표준 시간대 액세스

[TimeZoneInfo](xref:System.TimeZoneInfo) 클래스는 UTC 시간과 현지 표준 시간대를 나타내는 미리 정의된 표준 시간대 개체 두 개를 제공합니다. 두 개체는 각각 [TimeZoneInfo.Utc](xref:System.TimeZoneInfo.Utc) 및 [TimeZoneInfo.Local](xref:System.TimeZoneInfo.Local) 속성에서 사용할 수 있습니다. UTC 또는 현지 표준 시간대의 액세스에 대한 지침은 [방법: 미리 정의된 UTC 및 현지 표준 시간대 개체에 대한 액세스](access-utc-and-local.md)를 참조하십시오. 

또한 운영 체제에서 정의한 표준 시간대를 나타내는 [TimeZoneInfo](xref:System.TimeZoneInfo) 개체를 인스턴스화할 수 있습니다. 특정 표준 시간대 개체의 인스턴스화에 대한 지침은 [방법: TimeZoneInfo 개체 인스턴스화](instantiate-time-zone-info.md)를 참조하십시오.

## <a name="time-zone-identifiers"></a>표준 시간대 식별자

표준 시간대 식별자는 표준 시간대를 고유하게 식별하는 키 필드입니다. 대부분 키는 비교적 짧지만 표준 시간대 식별자는 비교적 깁니다. 대부분 경우 해당 값은 표준 시간대의 표준 시간 이름을 제공하는 데 사용되는 [TimeZoneInfo.StandardName](xref:System.TimeZoneInfo.StandardName) 속성에 해당합니다. 그러나 예외 사항이 있습니다. 올바른 식별자를 제공하는지 확인하는 가장 좋은 방법은 시스템에서 사용 가능한 표준 시간대를 열거하고 관련 식별자를 확인하는 것입니다. 표준 시간대 열거에 대한 지침은 [방법: 컴퓨터에 있는 표준 시간대 열거](enumerate-time-zones.md)를 참조하십시오.

## <a name="see-also"></a>참고 항목

[날짜, 시간 및 표준 시간대](index.md)

[방법: 미리 정의된 UTC 및 현지 표준 시간대 개체에 액세스](access-utc-and-local.md)

[방법: TimeZoneInfo 개체 인스턴스화](instantiate-time-zone-info.md)

[방법: 컴퓨터에 있는 표준 시간대 열거](enumerate-time-zones.md)

[표준 시간대 간에 시간 변환](converting-between-time-zones.md)


<!--HONumber=Nov16_HO3-->


