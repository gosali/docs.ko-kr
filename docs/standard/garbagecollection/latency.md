---
title: "대기 시간 모드"
description: "대기 시간 모드"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 08/18/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 810bd8be-5a48-42c6-b080-3afdb31fc61b
translationtype: Human Translation
ms.sourcegitcommit: de0dab146fc811e895dc32f98f877db5e757f82b
ms.openlocfilehash: e063482aaa1fad01f8e0cd9e8552c87a0b247571

---

# <a name="latency-modes"></a>대기 시간 모드

개체를 회수하려면, 가비지 수집기가 응용 프로그램의 실행 스레드를 모두 중지해야 합니다. 응용 프로그램이 데이터를 검색하거나 콘텐츠를 표시하는 경우와 같은 일부 상황에서는 전체 가비지 컬렉션이 중요한 시간에 발생하여 성능이 저하될 수 있습니다. [GCSettings.LatencyMode](xref:System.Runtime.GCSettings.LatencyMode) 속성을 [System.Runtime.GCLatencyMode](xref:System.Runtime.GCLatencyMode) 값 중 하나로 설정하여 가비지 수집기의 개입을 조정할 수 있습니다. 

대기 시간은 가비지 수집기가 응용 프로그램에 개입하는 시간을 참조합니다. 대기 시간이 짧은 기간 중에는 가비지 수집기가 보다 보수적이고 개체 회수에 덜 개입합니다. [System.Runtime.GCLatencyMode](xref:System.Runtime.GCLatencyMode) 열거형은 두 개의 짧은 대기 시간 설정을 제공합니다.

* [LowLatency](xref:System.Runtime.GCLatencyMode.LowLatency)는 2세대 컬렉션을 사용하지 않고 0세대 및 1세대 컬렉션만 수행합니다. 짧은 시간 동안에만 사용할 수 있습니다. 긴 시간 동안 사용하여 시스템의 메모리 사용량이 많을 경우 가비지 수집기가 응용 프로그램을 잠시 중지하고 시간 결정적 작업을 방해할 수 있는 수집을 트리거합니다. 이 설정은 워크스테이션 가비지 컬렉션에서만 사용할 수 있습니다. 

* [SustainedLowLatency](xref:System.Runtime.GCLatencyMode.SustainedLowLatency)는 2세대 포그라운드 컬렉션을 사용하지 않고 0세대, 1세대 및 2세대 백그라운드 컬렉션만 수행합니다. 오랜 시간 동안 사용할 수 있으며 워크스테이션 및 서버 가비지 컬렉션 모두에 대해 사용할 수 있습니다. [동시 가비지 수집](https://msdn.microsoft.com/library/yhwwzef8.aspx)을 사용하지 않을 경우 이 설정을 사용할 수 없습니다.

대기 시간이 짧은 기간 중에는 다음이 발생하지 않는 한 2세대 컬렉션이 사용되지 않습니다.

* 시스템에 운영 체제에서 메모리 부족 알림을 받습니다.

* 응용 프로그램 코드는 [GC.Collect](xref:System.GC.Collect(System.Int32)) 메서드를 호출하고 세대 매개 변수에 대해 2를 지정하여 컬렉션을 줄입니다.

다음 테이블에서는 [GCLatencyMode](xref:System.Runtime.GCLatencyMode) 값을 사용하는 응용 프로그램 시나리오를 보여 줍니다.

대기 시간 모드 | 응용 프로그램 시나리오
------------ | --------------------- 
[Batch](xref:System.Runtime.GCLatencyMode.Batch) | UI 또는 서버 쪽 작업이 없는 응용 프로그램. [동시 가비지 수집](https://msdn.microsoft.com/library/yhwwzef8.aspx)을 사용하지 않을 때 기본 모드입니다.
[Interactive](xref:System.Runtime.GCLatencyMode.Interactive) | UI가 있는 대부분의 응용 프로그램의 경우. UI 또는 서버 쪽 작업이 없는 응용 프로그램. [동시 가비지 수집](https://msdn.microsoft.com/library/yhwwzef8.aspx)을 사용할 때 기본 모드입니다.
[LowLatency](xref:System.Runtime.GCLatencyMode.LowLatency) | 가비지 수집기의 방해로 인한 영향을 받을 수 있는 단기간의 시간이 중요한 작업이 있는 응용 프로그램(예: 애니메이션 렌더링이나 데이터 취득 기능을 수행하는 응용 프로그램)
[SustainedLowLatency](xref:System.Runtime.GCLatencyMode.SustainedLowLatency) | 가비지 수집기의 방해로 인한 영향을 받을 수 있는 포함된 기간이지만 잠재적으로 장기간의 시간 결정적인 작업이 있는 응용 프로그램(예: 거래 시간 동안 시장 데이터가 변경될 때 빠른 응답 시간을 필요로 하는 응용 프로그램)   이 모드에서는 다른 모드보다 관리되는 힙 크기가 더 큽니다. 관리되는 힙을 압축하지 않기 때문에 조각화의 수준이 더 높을 수 있습니다. 충분한 메모리를 사용할 수 있는지 확인하세요.
 
## <a name="guidelines-for-using-low-latency"></a>짧은 대기 시간 사용에 대한 지침

[LowLatency](xref:System.Runtime.GCLatencyMode.LowLatency) 모드를 사용하는 경우 다음과 같은 지침을 고려합니다.

* 짧은 대기 시간 기간을 가능한 한 짧게 유지합니다.

* 짧은 대기 시간 기간 중 많은 양의 메모리를 할당하지 마세요. 가비지 수집에서 확보하는 개체 수가 더 적기 때문에 메모리 부족 알림이 발생할 수 있습니다. 

* 짧은 대기 시간 모드에서는 할당 수, 특히 대형 개체 힙 및 고정 개체에 대한 할당 수를 최소화합니다. 

* 할당할 수 있는 스레드를 확인합니다. [LatencyMode](xref:System.Runtime.GCSettings.LatencyMode) 속성 설정은 프로세스 전체에 적용되므로 할당될 수 있는 모든 스레드에서 [OutOfMemoryException](xref:System.OutOfMemoryException)을 생성할 수 있습니다. 

* [GC.Collect(Int32, GCCollectionMode)](xref:System.GC.Collect(System.Int32,System.GCCollectionMode)) 메서드를 호출하여 짧은 대기 시간 기간 중 2세대 컬렉션을 강제할 수 있습니다.

## <a name="see-also"></a>참고 항목

[System.GC](xref:System.GC)

[인덱싱된 컬렉션](induced.md)

[.NET에서 가비지 수집](index.md)



<!--HONumber=Nov16_HO1-->


