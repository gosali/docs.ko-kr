---
title: "dotnet 명령 | .NET Core SDK"
description: "dotnet 명령(.NET Core CLI 도구에 대한 일반 드라이버) 및 사용법에 대해 알아봅니다."
keywords: "dotnet, CLI, CLI 명령, .NET Core"
author: mairaw
manager: wpickett
ms.date: 10/07/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 93015521-2127-4fe9-8fce-ca79bcc4ff49
translationtype: Human Translation
ms.sourcegitcommit: c6ee3f5663d0a3f62914e8de474cca4d15340c9d
ms.openlocfilehash: 77c37ac3d4d0ba9ad1feac539debe40b0ee31161

---

#<a name="dotnet-command"></a>dotnet 명령

## <a name="name"></a>이름

dotnet -- 명령줄 명령을 실행하기 위한 일반 드라이버

## <a name="synopsis"></a>개요

`dotnet [--version] [--verbose] [--info] [command] [arguments] [--help]`

## <a name="description"></a>설명
`dotnet`은 CLI(명령줄 인터페이스) 도구 체인에 대한 일반 드라이버입니다. 자체적으로 호출되어 간단한 사용 방법을 제공합니다. 

각 특정 기능은 명령으로 구현됩니다. 기능을 사용하려면 [`dotnet build`](dotnet-build.md)와 같이 `dotnet`뒤에 명령을 지정합니다. 명령 다음에 오는 모든 인수는 고유한 인수입니다. 

`dotnet` 자체가 명령으로 사용되는 유일한 경우는 이식 가능한 앱을 실행하는 경우입니다. 응용 프로그램을 실행하려면 `dotnet` 동사 뒤에 이식 가능한 응용 프로그램 DLL를 지정하면 됩니다.    

## <a name="options"></a>옵션

`-v|--verbose`

자세한 정보 출력을 사용합니다.

`--version`

CLI 도구의 버전을 출력합니다.

`--info`

현재 운영 체제, 버전에 대한 SHA 커밋 등 CLI 도구에 대한 자세한 정보를 출력합니다. 

`-h|--help`

명령에 대한 간단한 도움말을 출력합니다. `dotnet`과 함께 사용되는 경우에만 사용 가능한 명령 목록도 출력합니다.  

## <a name="dotnet-commands"></a>dotnet 명령

다음과 같은 dotnet 명령이 있습니다.

* [dotnet-new](dotnet-new.md)
   * C# 또는 F# 콘솔 응용 프로그램 프로젝트를 초기화합니다.
* [dotnet-restore](dotnet-restore.md)
  * 지정된 응용 프로그램에 대한 종속성을 복원합니다. 
* [dotnet-build](dotnet-build.md)
  * .NET Core 응용 프로그램을 빌드합니다.
* [dotnet-publish](dotnet-publish.md)
   * .NET 이식 가능 또는 자체 포함 응용 프로그램을 게시합니다.
* [dotnet-run](dotnet-run.md)
   * 소스에서 응용 프로그램을 실행합니다.
* [dotnet-test](dotnet-test.md)
   * project.json에 지정된 Test Runner를 사용하여 테스트를 실행합니다.
* [dotnet-pack](dotnet-pack.md)
   * 코드의 NuGet 패키지를 만듭니다.

## <a name="examples"></a>예제

컴파일하고 실행할 수 있는 샘플 .NET Core 콘솔 응용 프로그램을 초기화합니다.

`dotnet new`

지정된 응용 프로그램에 대한 종속성을 복원합니다.

`dotnet restore`

지정된 디렉터리에서 프로젝트 및 해당 종속성을 빌드합니다. 

`dotnet build`

`myapp.dll`이라는 이식 가능한 앱을 실행합니다. `dotnet myapp.dll`

## <a name="environment"></a>환경 

`DOTNET_PACKAGES`

주 패키지 캐시입니다. 설정하지 않으면 Unix의 경우 $HOME/.nuget/packages로, Windows의 경우 %HOME%\NuGet\Packages로 기본 설정됩니다.

`DOTNET_SERVICING`

런타임을 로드할 때 공유 호스트에서 사용할 서비스 인덱스의 위치를 지정합니다.

`DOTNET_CLI_TELEMETRY_OPTOUT`

.NET Core 도구 사용에 대한 데이터를 수집하여 Microsoft에 전송할지 여부를 지정합니다. `true`이면 원격 분석 기능을 옵트아웃(opt out)하고(값 true, 1 또는 yes 허용), 그렇지 않은 경우에는 `false`(값 false, 0 또는 no 허용)입니다. 설정하지 않으면 `false`로 기본 설정됩니다. 즉, 원격 분석 기능이 설정되어 있습니다.


<!--HONumber=Nov16_HO1-->


