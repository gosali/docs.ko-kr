---
title: "발생한 컬렉션"
description: "발생한 컬렉션"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 08/16/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 3e09b9dd-a800-4e56-b468-619f910ae22e
translationtype: Human Translation
ms.sourcegitcommit: 213ce098bcc2b5e31c55e759d895254d5ca33caa
ms.openlocfilehash: 0bd8998cfb508294ffbc7a9619b571c9881ba294

---

# <a name="induced-collections"></a>발생한 컬렉션

대부분의 경우 가비지 수집기가 수집을 수행할 적절한 시기를 결정할 수 있으며 가비지 수집기가 독립적으로 실행되는 것이 좋습니다. 강제된 컬렉션이 응용 프로그램의 성능을 향상시키는 드문 경우도 있습니다. 이러한 경우에 가비지 수집을 강제하는 [GC.Collect](xref:System.GC.Collect) 메서드를 사용하여 가비지 수집을 발생시킬 수 있습니다. 

응용 프로그램 코드의 특정 지점에서 사용되는 메모리 양이 상당히 감소하는 경우 [Collect](xref:System.GC.Collect) 메서드를 사용합니다. 예를 들어 응용 프로그램이 몇 가지 컨트롤이 있는 복잡한 대화 상자를 사용하는 경우 대화 상자를 닫을 때 [Collect](xref:System.GC.Collect)를 호출하면 즉시 대화 상자에서 사용하는 메모리를 확보하여 성능을 개선할 수 있습니다. 가비지 수집기가 최적이 아닌 시간에 개체를 회수하려고 하는 경우 성능이 저하될 수 있기 때문에 응용 프로그램이 너무 자주 가비지 수집을 발생시키지 않도록 합니다. 다음 섹션에서 설명했듯이 [GCCollectionMode.Optimized](xref:System.GCCollectionMode.Optimized) 열거형 값을 [Collect](xref:System.GC.Collect) 메서드로 제공하여 컬렉션이 생산적인 경우에만 수집할 수 있습니다.

## <a name="gc-collection-mode"></a>GC 컬렉션 모드

다음과 같이 [GCCollectionMode](xref:System.GCCollectionMode) 값을 포함하는 [GC.Collect](xref:System.GC.Collect) 메서드 오버로드 중 하나를 사용하여 강제된 컬렉션에 대한 동작을 지정할 수 있습니다.

GCCollectionMode 값 | 설명
---------------------- | ----------- 
[기본](xref:System.GCCollectionMode.Default) | 실행 중인 버전의 .NET Framework에 대한 기본 가비지 수집 설정을 사용합니다.
[강제](xref:System.GCCollectionMode.Forced) | 가비지 수집이 즉시 실행되도록 강제합니다. [GC.Collect()](xref:System.GC.Collect) 오버로드를 호출하는 작업에 해당합니다. 모든 세대의 전체 차단 컬렉션에서 발생합니다. 또한 전체 차단 가비지 수집을 즉시 강제하기 전에 [GCSettings.LargeObjectHeapCompactionMode](xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode) 속성을 [GCLargeObjectHeapCompactionMode.CompactOnce](xref:System.Runtime.GCLargeObjectHeapCompactionMode.CompactOnce)으로 설정하여 큰 개체 힙을 압축할 수 있습니다. 
[최적화됨](xref:System.GCCollectionMode.Optimized) | 가비지 수집기는 현재 시간이 개체를 회수하기 위한 최적 시간인지 확인할 수 있습니다. 가비지 수집기는 컬렉션이 정당화될 만큼 생산적이지 않다는 것을 결정할 수 있습니다. 이 경우에 개체를 회수하지 않고 반환합니다.
 
## <a name="background-or-blocking-collections"></a>배경 또는 차단 컬렉션

[GC.Collect(Int32, GCCollectionMode, Boolean)](xref:System.GC.Collect(System.Int32,System.GCCollectionMode,System.Boolean)) 메서드 오버 로드를 호출하여 발생한 컬렉션을 차단할지를 지정할 수 있습니다. 수행된 컬렉션의 유형은 메서드의 *mode* 및 *blocking* 매개 변수의 조합에 따라 달라집니다. *mode*는 [GCCollectionMode](xref:System.GCCollectionMode) 열거형의 구성원이며 *blocking*은 [부울](xref:System.Boolean) 값입니다. 다음 테이블에서는 모드와 차단 인수의 상호 작용을 간단하게 설명합니다. 

*모드* | *blocking* = true | *blocking* = false
------ | ----------------- | ------------------
[강제](xref:System.GCCollectionMode.Forced) 또는 [기본](xref:System.GCCollectionMode.Default) | 차단 컬렉션은 가능한 한 빨리 수행됩니다. 배경 컬렉션이 진행되고 생성이 0 또는 1인 경우 [Collect(Int32, GCCollectionMode, Boolean)](xref:System.GC.Collect(System.Int32,System.GCCollectionMode,System.Boolean)) 메서드는 즉시 차단 수집을 트리거하여 컬렉션을 완료할 때 반환합니다. 배경 컬렉션이 진행되고 생성 매개 변수가 2인 경우 메서드는 백그라운드 컬렉션이 완료되고 2세대 컬렉션을 트리거한 다음 반환될 때까지 대기합니다. | 컬렉션은 가능한 한 빨리 수행됩니다. [Collect(Int32, GCCollectionMode, Boolean)](xref:System.GC.Collect(System.Int32,System.GCCollectionMode,System.Boolean)) 메서드는 백그라운드 컬렉션을 요청하지만 보장되지 않습니다. 상황에 따라 차단 컬렉션이 계속 수행될 수 있습니다. 백그라운드 컬렉션이 이미 진행 중인 경우 메서드가 즉시 반환됩니다. 
[최적화됨](xref:System.GCCollectionMode.Optimized) | 가비지 수집기 및 세대 매개 변수의 상태에 따라 차단 컬렉션이 수행될 수 있습니다. 가비지 수집기는 최적의 성능을 제공하려고 합니다. | 가비지 수집기의 상태에 따라 컬렉션이 수행될 수 있습니다. [Collect(Int32, GCCollectionMode, Boolean)](xref:System.GC.Collect(System.Int32,System.GCCollectionMode,System.Boolean)) 메서드는 백그라운드 컬렉션을 요청하지만 보장되지 않습니다. 상황에 따라 차단 컬렉션이 계속 수행될 수 있습니다. 가비지 수집기는 최적의 성능을 제공하려고 합니다. 백그라운드 컬렉션이 이미 진행 중인 경우 메서드가 즉시 반환됩니다. 
 
## <a name="see-also"></a>참고 항목

[대기 시간 모드](latency.md)

[.NET에서 가비지 수집](index.md)



<!--HONumber=Nov16_HO3-->


