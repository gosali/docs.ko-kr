---
title: ".NET 클래스 라이브러리"
description: ".NET 클래스 라이브러리"
keywords: .NET, .NET Core
author: richlander
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: a67484c3-fe92-44d8-8fa3-36fa2071d880
translationtype: Human Translation
ms.sourcegitcommit: 62fdb3e60b206728d86220076867eb8fd68af82e
ms.openlocfilehash: 8ed35197078e43f096931adb18c454004f83cccf

---

# <a name="net-class-libraries"></a>.NET 클래스 라이브러리

클래스 라이브러리는 .NET에 대한 [공유 라이브러리](http://en.wikipedia.org/wiki/Library_%28computing%29#Shared_libraries) 개념입니다. 유용한 기능을 여러 응용 프로그램에서 사용할 수 있는 모듈로 구성 요소화할 수 있게 합니다. 불필요하거나 응용 프로그램 시작 시 알 수 없는 기능을 로드하는 수단으로 사용할 수도 있습니다. 클래스 라이브러리는 [.NET 어셈블리 파일 형식](assembly-format.md)을 사용하여 설명됩니다.

사용할 수 있는 세 가지 유형의 클래스 라이브러리는 다음과 같습니다.

*   **플랫폼별** 클래스 라이브러리는 지정된 플랫폼(예: .NET Framework, Xamarin iOS)의 모든 API에 액세스할 수 있지만 해당 플랫폼을 대상으로 하는 앱과 라이브러리만 사용할 수 있습니다.
*   **이식 가능** 클래스 라이브러리는 API 하위 집합에 액세스할 수 있으며 여러 플랫폼을 대상으로 하는 앱과 라이브러리에서 사용할 수 있습니다.
*   **.NET Core** 클래스 라이브러리는 플랫폼별 및 이식 가능한 라이브러리 개념을 두 가지의 장점을 제공하는 하나의 모델로 통합합니다.

## <a name="platformspecific-class-libraries"></a>플랫폼별 클래스 라이브러리

플랫폼별 라이브러리는 단일 .NET 플랫폼(예: Windows의 .NET Framework)에 바인딩되어 있으므로 알려진 실행 환경에 종속될 수 있습니다. 이러한 환경은 알려진 API 집합(.NET 및 OS API)을 표시하며 필요한 상태(예: Windows 레지스트리)를 유지 관리하고 표시합니다.

플랫폼별 라이브러리를 만드는 개발자는 기본 플랫폼을 충분히 활용할 수 있습니다. 라이브러리는 지정된 플랫폼에서만 실행되므로 플랫폼 검사 또는 다른 형태의 조건부 코드가 불필요합니다(여러 플랫폼용 모듈로 단일 소싱 코드).

플랫폼별 라이브러리는 .NET Framework의 기본 클래스 라이브러리 형식이었습니다. 다른 .NET 플랫폼이 등장한 후에도 플랫폼별 라이브러리가 주요 라이브러리 형식으로 유지되었습니다.

## <a name="portable-class-libraries"></a>이식 가능한 클래스 라이브러리

이식 가능한 라이브러리는 여러 .NET 플랫폼에서 지원됩니다. 이 라이브러리도 알려진 실행 환경에 종속되지만 환경이 구체적인 .NET 플랫폼 집합의 교집합에 의해 생성되는 가상 환경입니다. 즉, 표시되는 API 및 플랫폼 가정이 플랫폼별 라이브러리에서 사용할 수 있는 전체의 하위 집합입니다.

이식 가능한 라이브러리를 만드는 경우 플랫폼 구성을 선택합니다. 이러한 구성은 지원해야 하는 플랫폼 집합(예: .NET Framework 4.5 이상, Windows Phone 8.0 이상)입니다. 더 많은 플랫폼을 지원하도록 선택할수록 만들 수 있는 API 및 플랫폼 가정(최소 공통 분모)이 줄어듭니다. "많을수록 더 좋다"고 생각하는 경향이 있으므로 이 특성은 처음에 혼동을 줄 수도 있지만 지원되는 플랫폼이 많을수록 사용 가능한 API가 줄어듭니다.

많은 라이브러리 개발자가 한 소스에서 여러 플랫폼별 라이브러리를 생성하는 형태(조건부 컴파일 지시문 사용)에서 이식 가능한 라이브러리로 전환했습니다. 이식 가능한 라이브러리 내에서 플랫폼별 기능에 액세스하기 위한 [여러 가지 방법](http://blog.stephencleary.com/2012/11/portable-class-library-enlightenment.html)이 있으며, [bait-and-switch](http://log.paulbetts.org/the-bait-and-switch-pcl-trick/)가 현재 가장 광범위하게 사용되는 기술입니다.

### <a name="net-core-class-libraries"></a>.NET Core 클래스 라이브러리

.NET Core 라이브러리는 플랫폼별 및 이식 가능한 라이브러리 개념을 대체합니다. 기본 플랫폼의 모든 기능을 표시한다는 점에서 플랫폼별 라이브러리입니다(가상 플랫폼 또는 플랫폼 교차 부분 아님). 또한 지원되는 모든 플랫폼에서 작동한다는 점에서 이식 가능한 라이브러리입니다.

.NET Core는 라이브러리 _계약_ 집합을 표시합니다. .NET 플랫폼은 각 계약을 완전히 지원하거나 전혀 지원하지 않아야 합니다. 따라서 각 플랫폼은 .NET Core 계약 집합을 지원합니다. 필연적인 결과로, 각 .NET Core 클래스 라이브러리는 해당 계약 종속성을 지원하는 플랫폼에서 지원됩니다.

.NET Core는 .NET Framework의 전체 기능을 표시하지는 않지만(이를 지향하지 않음), 이식 가능한 클래스 라이브러리보다 많은 API를 표시합니다. 시간이 지나면 더 많은 API가 추가될 것입니다.

.NET Core 클래스 라이브러리를 지원하는 플랫폼은 다음과 같습니다.

*   .NET Core
*   ASP.NET Core
*   .NET Framework 4.5 이상
*   Windows 스토어 앱
*   Windows Phone 8 이상

### <a name="mono-class-libraries"></a>Mono 클래스 라이브러리

위에서 설명한 세 가지 형식의 라이브러리를 포함하여 클래스 라이브러리는 Mono에서 지원됩니다. Mono는 Microsoft .NET Framework의 플랫폼 간 구현으로 간주되는 경우가 많습니다(맞음). 부분적으로 이는 플랫폼별 .NET Framework 라이브러리를 수정하거나 컴파일하지 않고 Mono 런타임에서 실행할 수 있기 때문이었습니다. 이 특성은 이식 가능한 클래스 라이브러리를 만들기 전에 구현되었으므로 한 방향으로만 작동한다는 제약에도 불구하고 .NET Framework와 Mono 간에 이진 이식성을 사용하는 것이 당연했습니다.



<!--HONumber=Nov16_HO1-->


