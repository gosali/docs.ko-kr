---
title: ".NET Core CLI(명령줄 인터페이스) 도구"
description: "CLI(명령줄 인터페이스)의 개념 및 주요 기능에 대한 개요"
keywords: "CLI, CLI 도구, .NET, .NET Core"
author: blackdwarf
ms.author: mairaw
manager: wpickett
ms.date: 10/06/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: b70e9ac0-c8be-49f7-9332-95ab93e0e7bc
translationtype: Human Translation
ms.sourcegitcommit: b20713600d7c3ddc31be5885733a1e8910ede8c6
ms.openlocfilehash: 3a3326f96cf69d0ac466e52107597d80c2790b62

---

# <a name="net-core-commandline-interface-tools"></a>.NET Core 명령줄 인터페이스 도구

.NET Core CLI(명령줄 인터페이스)는 .NET Core 응용 프로그램 개발에 사용되는 새로운 기본 플랫폼 간 도구 체인입니다. IDE(통합 개발 환경), 편집기 및 빌드 Orchestrator와 같은 기타 상위 수준 도구의 기반이 될 수 있는 주 계층이기 때문에 "기본"입니다. 

기본적으로 플랫폼 간 인터페이스이며 지원되는 각 플랫폼에 동일한 노출 영역이 있습니다. 즉, 도구 사용 방법을 익히면 지원되는 모든 플랫폼에서 동일한 방식으로 사용할 수 있습니다. 

## <a name="installation"></a>설치
모든 도구와 마찬가지로 먼저 컴퓨터에 도구를 가져와야 합니다. 시나리오에 따라 기본 설치 관리자를 사용하여 CLI를 설치하거나 설치 셸 스크립트를 사용할 수 있습니다.

기본 설치 관리자는 주로 개발자의 컴퓨터를 위한 것입니다. Ubuntu의 DEB 패키지 또는 Windows의 MSI 번들처럼 CLI는 지원되는 각 플랫폼의 기본 설치 메커니즘을 사용하여 배포됩니다. 이러한 설치 관리자는 설치 후 CLI를 즉시 사용하려는 사용자에게 필요한 환경을 설치 및 설정합니다. 그러나 이러한 사용자는 컴퓨터에 대한 관리자 권한이 필요합니다. [.NET Core 시작 페이지](https://aka.ms/dotnetcoregs)에서 설치 지침을 볼 수 있습니다.

반면 설치 스크립트를 사용할 경우에는 관리 권한이 필요하지 않습니다. 그러나 컴퓨터에 필수 구성 요소도 설치되지 않습니다. 모든 필수 구성 요소를 수동으로 설치해야 합니다. 스크립트는 대개 빌드 서버를 설정하거나 관리자 권한 없이 도구를 설치할 경우 사용됩니다(위의 필수 조건 주의 사항 참조). 자세한 내용은 [스크립트 참조 설치 항목](dotnet-install-script.md)에서 확인할 수 있습니다. CI(연속 통합) 빌드 서버에 CLI를 설치하는 방법에 관심이 있는 경우 [CI 서버와 CLI](using-ci-with-cli.md) 항목을 살펴볼 수 있습니다. 

기본적으로 CLI는 "SxS”(병렬) 방식으로 설치됩니다. 즉, 특정 시간에 CLI 도구의 여러 버전이 단일 컴퓨터에 공존할 수 있습니다. 그러나 사용되는 올바른 버전에 대해서는 [드라이버 섹션](#driver)에 자세히 설명되어 있습니다. 

### <a name="what-commands-come-in-the-box"></a>기본적으로 제공되는 명령
다음은 기본적으로 설치되는 명령입니다.

* [new](dotnet-new.md)
* [restore](dotnet-restore.md)
* [run](dotnet-run.md)
* [build](dotnet-build.md)
* [test](dotnet-test.md)
* [publish](dotnet-publish.md)
* [pack](dotnet-pack.md)

프로젝트 단위로 더 많은 명령을 가져올 뿐만 아니라 고유한 명령을 추가할 수도 있습니다. 이 과정은 [확장성 섹션](#extensibility)에서 더 자세히 설명합니다. 

## <a name="working-with-the-cli"></a>CLI 작업

자세한 정보를 검토하기 전에 CLI를 사용한 작업이 10,000피트 보기에서 어떻게 표시되는지 살펴보겠습니다. 다음 예제에서는 CLI 표준 설치에서 여러 가지 명령을 활용하여 간단한 새 콘솔 응용 프로그램을 초기화하고 종속성을 복원한 다음 응용 프로그램을 빌드하여 실행합니다. 

```console
dotnet new
dotnet restore
dotnet build --output /stuff
dotnet /stuff/new.dll
```

이전 예제와 같이 CLI 도구를 사용하는 방식에는 패턴이 있습니다. 해당 패턴 내에서 각 명령의 세 가지 주요 조각을 식별할 수 있습니다.

1. [드라이버("dotnet")](#driver)
2. [명령 또는 "동사"](#the-verb)
3. [명령 인수](#the-arguments)

### <a name="driver"></a>드라이버
드라이버 이름은 [dotnet](dotnet.md)이며, 처음으로 호출되는 부분입니다. 드라이버에는 두 가지 책임이 있습니다.

1. 이식 가능한 앱 실행
2. 동사 실행

수행하는 작업은 명령줄에 지정된 항목에 따라 달라집니다. 첫 번째 경우에는 `dotnet`이 `dotnet /path/to/your.dll`과 같이 실행되는 이식 가능한 앱 DLL을 지정합니다. 

두 번째 경우에는 드라이버가 지정된 명령을 호출하려고 시도합니다. 그러면 CLI 명령 실행 프로세스가 시작됩니다. 먼저 드라이버에서 원하는 도구의 버전을 확인합니다. [global.json](global-json.md) 파일에서 `version` 속성을 사용하여 버전을 지정할 수 있습니다. 사용할 수 없는 경우 드라이버는 디스크에 설치된 최신 버전의 도구를 찾아 해당 버전을 사용합니다. 버전이 확인되면 명령을 실행합니다. 

### <a name="the-verb"></a>"동사"
동사는 작업을 수행하는 명령일 뿐입니다. `dotnet build`는 코드를 빌드합니다. `dotnet publish`는 코드를 게시합니다. 동사는 `dotnet-{verb}`와 같이 규칙에 따라 명명된 콘솔 응용 프로그램으로 구현됩니다.합니다. 모든 논리는 동사를 나타내는 콘솔 응용 프로그램에서 구현됩니다. 

### <a name="the-arguments"></a>인수
명령줄에서 전달하는 인수는 호출하려는 실제 동사/명령에 대한 인수입니다. 예를 들어 `dotnet publish --output publishedapp`를 입력하면 `--output` 인수가 `publish` 명령에 전달됩니다. 

## <a name="types-of-application-portability"></a>응용 프로그램 이식성 유형
CLI에서는 다음과 같은 두 가지 주요 방법으로 응용 프로그램을 이식할 수 있습니다.

1. .NET Core가 설치되어 있는 모든 위치에서 실행할 수 있는 완전히 이식 가능한 응용 프로그램
2. 자체 포함 배포

이 두 가지 방법에 대한 자세한 내용은 [.NET Core 응용 프로그램 배포](../deploying/index.md) 항목을 참조하세요. 

## <a name="migration-from-dnx"></a>DNX에서 마이그레이션
.NET Core 1.0 RC1에서 DNX를 사용하는 경우 이러한 도구에 발생되는 내용 및 이러한 새 도구가 DNX 도구에 연결되는 방법이 궁금할 수 있습니다. 즉, DNX 도구가 .NET Core CLI 도구로 대체되었습니다. 기존 프로젝트가 있거나 명령이 매핑되는 방법이 궁금한 경우 [DNX에서 CLI로 마이그레이션](../migrating-from-dnx.md) 항목을 사용하여 자세한 정보를 확인할 수 있습니다. 

## <a name="extensibility"></a>확장성
워크플로에서 사용할 수 있는 일부 도구는 핵심 CLI 도구의 일부가 아닐 수 있습니다. 그러나 .NET Core CLI에는 프로젝트에 맞게 추가 도구를 지정할 수 있는 확장성 모델이 있습니다. 자세한 내용은 [.NET Core CLI 확장성 모델](extensibility.md) 항목에서 확인할 수 있습니다.

## <a name="summary"></a>요약
CLI의 가장 중요한 기능에 대한 간략한 개요입니다. 자세한 내용은 이 사이트의 참조 및 개념 항목을 사용하여 확인할 수 있습니다. 다음과 같은 기타 리소스도 사용할 수 있습니다.
* [dotnet/CLI](https://github.com/dotnet/cli/) GitHub 리포지토리
* [시작 지침](https://aka.ms/dotnetcoregs/)



<!--HONumber=Nov16_HO1-->


