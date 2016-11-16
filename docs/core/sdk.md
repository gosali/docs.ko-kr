---
title: ".NET Core SDK 개요"
description: ".NET Core SDK 개요"
keywords: .NET, .NET Core
author: mairaw
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 26bc9822-e42b-48ec-b0d6-499dc604add7
translationtype: Human Translation
ms.sourcegitcommit: b7dbb80c16ca0d08ff6fe4c4f3a7b221f64c6608
ms.openlocfilehash: a1808cdc0efb9087ffbda0a1631cd08e6bde0b3f

---

# <a name="net-core-sdk-overview"></a>.NET Core SDK 개요 

## <a name="introduction"></a>소개
.NET Core SDK(소프트웨어 개발 키트)는 개발자들이 .NET Core 응용 프로그램과 라이브러리를 만드는 데 사용할 수 있는 라이브러리 및 도구 집합입니다. 이 SDK는 개발자들이 가장 많이 사용할만한 패키지로서 

다음 구성 요소를 포함합니다.

1. 응용 프로그램을 빌드하는 데 사용되는 .NET Core 명령줄 도구
2. 응용 프로그램을 빌드 및 실행할 수 있는 .NET Core(라이브러리 및 런타임)
3. 응용 프로그램은 물론 [CLI 명령](tools/index.md)도 실행하기 위한 `dotnet` 드라이버


## <a name="acquiring-the-net-core-sdk"></a>.NET Core SDK 가져오기
모든 도구와 마찬가지로 먼저 컴퓨터에 도구를 가져와야 합니다. 시나리오에 따라, 기본 설치 관리자를 사용하여 SDK를 설치할 수도 있고 설치 셸 스크립트를 사용할 수도 있습니다.

기본 설치 관리자는 주로 개발자의 컴퓨터를 위한 것입니다. Ubuntu의 DEB 패키지 또는 Windows MSI 번들처럼 SDK는 지원되는 플랫폼의 기본 설치 메커니즘을 사용하여 배포됩니다. 이러한 설치 관리자는 설치 후 SDK를 즉시 사용하려는 사용자에게 필요한 환경을 설치 및 설정합니다. 그러나 이러한 사용자는 컴퓨터에 대한 관리자 권한이 필요합니다. [.NET Core 시작 페이지](https://aka.ms/dotnetcoregs)에서 설치 지침을 볼 수 있습니다.

반면 설치 스크립트를 사용할 경우에는 관리 권한이 필요하지 않습니다. 그러나 컴퓨터에 필수 구성 요소도 설치되지 않습니다. 모든 필수 구성 요소를 수동으로 설치해야 합니다. 스크립트는 대개 빌드 서버를 설정하거나 관리자 권한 없이 도구를 설치할 경우 사용됩니다(위의 필수 구성 요소 주의 사항 참조). 자세한 내용은 [스크립트 참조 설치 항목](tools/dotnet-install-script.md)에서 확인할 수 있습니다. CI 빌드 서버에 SDK를 설치하는 방법에 관심이 있는 경우 [CI 서버와 SDK](tools/using-ci-with-cli.md) 문서를 살펴볼 수 있습니다. 

기본적으로 SDK는 "SxS”(병렬) 방식으로 설치됩니다. 즉, 특정 시간에 CLI 도구의 여러 버전이 단일 컴퓨터에 공존할 수 있습니다. 그러나 사용되는 올바른 버전에 대해서는 .NET Core 명령줄 도구 항목의 [드라이버 섹션](tools/index.md#driver)에 자세히 설명되어 있습니다. 




<!--HONumber=Nov16_HO1-->


