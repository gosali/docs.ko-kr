---
title: ".NET Core SDK 및 CI(연속 통합)의 도구 사용"
description: ".NET Core SDK 및 CI(연속 통합)의 도구 사용"
keywords: .NET, .NET Core
author: mairaw
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 5fb15297-a276-417f-8c4f-267281357769
translationtype: Human Translation
ms.sourcegitcommit: 15c55a87beb64f265a164db918c7721c7690fadf
ms.openlocfilehash: 2ceb14c2d9ea990037d5c14a295cac02a0c44e48

---

# <a name="using-net-core-sdk-and-tools-in-continuous-integration-ci"></a>.NET Core SDK 및 CI(연속 통합)의 도구 사용

## <a name="overview"></a>개요
이 문서는 .NET Core SDK 및 빌드 서버의 도구를 사용하는 방법의 개요를 제공합니다. 일반적으로 CI 빌드 서버에서는 어떤 방식으로든 설치를 자동화하고자 합니다. 자동화에서는 가능한 한 관리자 권한을 요구하지 않는 것이 바람직합니다. 

SaaS CI 솔루션에는 몇 가지 옵션이 있습니다. 이 문서에서는 매우 인기 있는 두 가지, [TravisCI](https://travis-ci.org/) 및 [AppVeyor](https://www.appveyor.com/)에 대해 다룹니다. 물론 다른 서비스도 많이 있지만, 설치 및 사용법 메커니즘은 유사해야 합니다.

## <a name="installation-options-for-ci-build-servers"></a>CI 빌드 서버에 대한 설치 옵션

## <a name="using-the-native-installers"></a>기본 설치 관리자 사용
관리 권한을 요구하는 설치 관리자를 사용하는 것이 문제가 되지 않는 경우, 각 플랫폼의 기본 설치 관리자를 사용하여 빌드 서버를 설치할 수 있습니다. 이 접근법은 특히 Linux 빌드 서버의 경우, 실행할 SDK에 필요한 종속성을 자동으로 설치한다는 한 가지 이점이 있습니다. 기본 설치 관리자는 또한 시스템 차원의 SDK 버전을 설치하는데, 이것이 바람직할 수 있습니다. 이렇게 하지 않는 경우 아래에 요약된 [설치 관리자 스크립트 사용법](#using-the-installer-script)을 살펴보아야 합니다. 

이 접근법은 사용이 간단합니다. Linux에서는 피드 기반 패키지 관리자를 사용하거나(Ubuntu의 경우 `apt-get`, CentOS의 경우 `yum`), 패키지 자체를 사용할 수 있습니다(즉, DEB 또는 RPM). 전자의 경우 패키지가 포함된 피드를 설정해야 합니다.

Windows 플랫폼에서는 MSI를 사용할 수 있습니다. 

안정적인 최신 릴리스를 안내하는 [.NET Core 시작 페이지](https://aka.ms/dotnetcoregs)에서 모든 이진을 찾을 수 있습니다. 더 새로운 릴리스(불안정할 수 있음)나 최신 릴리스를 사용하려면 [CLI 리포지토리](https://github.com/dotnet/cli)의 링크를 사용할 수 있습니다. 

## <a name="using-the-installer-script"></a>설치 관리자 스크립트 사용
설치 관리자 스크립트를 사용하면 관리자가 아닌 사용자도 빌드 서버에 설치할 수 있습니다. 자동화도 매우 쉽게 수행할 수 있습니다. 스크립트 자체는 필요한 ZIP/tarball 파일을 다운로드하고 압축을 풉니다. 또한 설치 후 바로 도구를 호출할 수 있도록 로컬 컴퓨터의 설치 위치를 PATH에 추가합니다. 

필요한 SDK 버전을 가져와서 설치할 수 있도록 빌드 시작 부분에서 설치 관리자 스크립트를 손쉽게 자동화할 수 있습니다. "필요한 버전"이란 빌드 중인 응용 프로그램에 필요한 버전을 말합니다. SDK를 로컬에 설치하고 빌드가 완료된 후 정리할 수 있도록 설치 경로를 선택할 수 있습니다. 이렇게 하면 빌드 프로세스에 캡슐화 및 원자성이 추가됩니다. 

설치 스크립트 참조는 [dotnet-install](dotnet-install-script.md) 문서에서 찾을 수 있습니다. 

### <a name="dealing-with-the-dependencies"></a>종속성 처리
설치 관리자 스크립트를 사용하는 경우 기본 종속성이 자동으로 설치되지 않으며, 설치 중인 운영 체제에 아직 없는 경우 종속성을 설치해야 합니다. [CLI 리포지토리](https://github.com/dotnet/core/blob/master/Documentation/prereqs.md)에서 필수 조건 목록을 볼 수 있습니다. 

## <a name="ci-services-setup-examples"></a>CI 서비스 설치 예제
아래 섹션에서는 언급한 CI SaaS 제품을 사용하여 구성하는 예제를 보여 줍니다. 

### <a name="travisci"></a>TravisCI

`csharp` 언어 및 `dotnet` 키를 사용하여 .NET Core SDK를 설치하도록 [travis-ci](https://travis-ci.org/)를 구성할 수 있습니다.

다음을 사용하면 됩니다.

```yaml
dotnet: 1.0.0-preview2-003121
```

Travis는 빌드 행렬에서 `osx`(OS X 10.11) 및 `linux`(Ubuntu 14.04) 작업을 모두 실행할 수 있습니다. 자세한 내용은 [example .travis.yml](https://github.com/dotnet/docs/blob/master/.travis.yml)을 참조하세요.

### <a name="appveyor"></a>AppVeyor

[appveyor.com ci](https://www.appveyor.com/)의 빌드 작업자 이미지 `Visual Studio 2015`에 .NET Core SDK preview2가 이미 설치되어 있습니다.

다음을 사용하면 됩니다.

```yaml
os: Visual Studio 2015
```

.NET Core SDK의 특정 버전을 설치할 수 있습니다. 자세한 내용은 [예제 appveyor.yml](https://github.com/dotnet/docs/blob/master/appveyor.yml)을 참조하세요. 

이 예제에서는 .NET Core SDK 이진을 다운로드하고, 하위 디렉터리에 압축을 해제하고, `PATH` 환경 변수에 추가했습니다.

빌드 행렬을 추가하여 .NET Core SDK의 여러 버전으로 통합 테스트를 실행할 수 있습니다.

```yaml
environment:
  matrix:
    - CLI_VERSION: 1.0.0-preview2-003121
    - CLI_VERSION: Latest

install:
  # .NET Core SDK binaries
  - ps: $url = "https://dotnetcli.blob.core.windows.net/dotnet/preview/Binaries/$($env:CLI_VERSION)/dotnet-dev-win-x64.$($env:CLI_VERSION.ToLower()).zip"
  # follow normal installation from binaries
```




<!--HONumber=Nov16_HO1-->


