---
title: "가비지 컬렉션"
description: "가비지 컬렉션"
keywords: .NET, .NET Core
author: richlander
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: db39a0f5-e363-490f-a7e6-adb9a6ff2a8c
translationtype: Human Translation
ms.sourcegitcommit: 213ce098bcc2b5e31c55e759d895254d5ca33caa
ms.openlocfilehash: 2406a03fa64eb02c70f05c1e8240e4bc5981e98d

---

# <a name="garbage-collection"></a>가비지 수집

GC(가비지 수집기)는 .NET 관리되는 코드 플랫폼의 중요한 기능 중 하나입니다. 가비지 수집기에서 메모리의 할당 및 해제를 관리 합니다. 사용자가 메모리를 할당하고 해제하거나 해당 메모리를 사용하는 개체의 수명을 관리하는 방식을 알고 있을 필요는 없습니다. _새 개체를 만들거나_ 값 형식이 boxed일 때마다 언제든지 할당이 수행됩니다. 일반적으로 할당은 매우 빠르게 수행됩니다. 개체를 할당하는 데 메모리가 충분하지 못하면 GC에서 가비지 메모리를 수집하고 삭제하여 새 할당을 위해 메모리를 사용할 수 있도록 해야 합니다. 이 프로세스를 "가비지 수집"이라고 합니다.

가비지 수집기는 자동 메모리 관리자 역할을 합니다. 가비지 수집은 다음과 같은 이점을 제공합니다.

*   응용 프로그램을 개발할 때 메모리를 해제할 필요가 없습니다.
*   관리되는 힙에 효율적으로 개체를 할당합니다.
*   더 이상 사용되지 않는 개체를 회수하고 이러한 개체의 메모리를 비워 이후 할당에서 이 메모리를 사용할 수 있도록 합니다. 관리되는 개체는 자동으로 시작을 위한 정리된 콘텐츠를 받으므로 개체의 생성자가 모든 데이터 필드를 초기화할 필요가 없습니다.
*   개체에서 다른 개체의 콘텐츠를 사용할 수 없도록 하여 메모리 안전을 제공합니다.

.NET GC는 세대별로 진행하며 3개 세대로 구성됩니다. 각 세대에는 할당된 개체의 저장에 사용하는 자체의 힙이 갖춰져 있습니다. 대부분의 개체는 짧거나 길게 잔존한다는 기본 원칙이 있습니다. 0세대는 개체가 최초로 할당되는 공간입니다. 다음 가비지 수집이 발생할 때까지 더 이상 사용되지 않기(범위 밖) 때문에 첫 번째 세대를 거친 후에는 대개 개체의 수명이 다합니다. 할당되는 힙이 작기 때문에 0세대에서 수집하는 속도가 빠릅니다. 1세대는 실제로 두 번째 기회 공간입니다. 수명이 짧지만 0세대 수집에서 잔존한(대개 조건부 타이밍 기준) 개체는 1세대로 이동합니다\. 연결된 힙 또한 작기 때문에 1세대 수집도 빨리 수행됩니다. 개체가 수집되거나 다음 세대 힙으로 승격되기 때문에 처음 두 힙이 작은 크기로 남아 있습니다. 2세대는 길게 잔존한 모든 개체들이 머무는 공간입니다. 보유한 개체들이 오랫동안 잔존할 수 있고 개체를 더 높은 수준으로 승격할 3세대 힙이 없기 때문에 2세대 힙은 매우 큰 크기로 확장됩니다.

GC에는 LOH(대형 개체 힙)이라는 대형 개체를 위한 추가 힙이 있으며, 이는 85,000바이트 이상의 개체를 위해 예약되어 있습니다. 85k 요소를 포함한 바이트 배열(Byte[])은 대형 개체의 한 예입니다. 대형 개체는 세대 힙에 할당되지 않고 LOH에 직접 할당됩니다.

오랫동안 실행하거나 대용량 데이터를 연산하는 프로그램의 경우 2세대 및 LOH 수집에서 상당한 시간이 걸릴 수 있습니다. 대형 서버 프로그램에서는 10s/GB 처리 속도의 힙이 사용된다고 알려져 있습니다. GC에는 프로그램 실행을 차단하는 데 걸리는 시간을 줄이기 위해 다양한 기법들이 적용되고 있습니다. 프로그램 실행을 방해하지 않는 방식으로 백그라운드 스레드에서 최대한 많은 가비지 수집 작업을 수행하는 것이 기본적인 방법입니다. 또한 GC는 해당 동작에 영향을 줄 수 있는 몇 가지 방법을 제공하며, 개발자가 성능을 향상시키는 데 있어 이러한 방법이 매우 유용할 수도 있습니다.

## <a name="related-topics"></a>관련 항목

제목 | 설명
----- | ----------- 
[자동 메모리 관리 및 가비지 수집](gc.md) | .NET 메모리 관리의 기본 개념에 대해 설명합니다.
[가비지 컬렉션 기본 사항](fundamentals.md) | 가비지 수집이 작동하는 방법, 관리되는 힙에 개체를 할당하는 방법 및 기타 핵심 개념에 대해 설명합니다.
[도출된 컬렉션](induced.md) | 가비지 수집을 발생시키는 방법에 대해 설명합니다.
[대기 시간 모드](latency.md) | 가비지 수집의 실행 시기를 결정하는 모드에 대해 설명합니다.
[약한 참조](weak-references.md) | 응용 프로그램에서 개체에 계속 액세스하는 동안 가비지 수집기에서 해당 개체를 수집할 수 있도록 하는 기능에 대해 설명합니다.
 
## <a name="reference"></a>참조

[System.GC](xref:System.GC)

[System.GCCollectionMode](xref:System.GCCollectionMode)

[System.Runtime.GCLatencyMode](xref:System.Runtime.GCLatencyMode)

[System.Runtime.GCSettings](xref:System.Runtime.GCSettings)

[GCSettings.LargeObjectHeapCompactionMode](xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode)

[Object.Finalize](xref:System.Object.Finalize)

[System.IDisposable](xref:System.IDisposable)

## <a name="see-also"></a>참고 항목

[관리되지 않는 리소스 정리](unmanaged.md)




<!--HONumber=Nov16_HO1-->


