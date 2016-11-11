---
title: "dotnet-install 스크립트 | .NET Core SDK"
description: ".NET Core CLI 도구 및 공유 런타임을 설치하는 dotnet-install 스크립트에 대해 알아봅니다."
keywords: "dotnet-install, dotnet-install 스크립트, .NET Core"
author: blackdwarf
ms.author: mairaw
manager: wpickett
ms.date: 10/12/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 59b9c456-2bfd-4adc-8202-a1c6a0a6c787
translationtype: Human Translation
ms.sourcegitcommit: b20713600d7c3ddc31be5885733a1e8910ede8c6
ms.openlocfilehash: 30e969c55d08b3ac276c6e7786fa96985dbb0a6b

---

#<a name="dotnetinstall-scripts-reference"></a>dotnet-install 스크립트 참조

## <a name="name"></a>이름
dotnet-install.ps1 | dotnet-install.sh - CLI(명령줄 인터페이스) 도구 및 공유 런타임을 설치하는 데 사용되는 스크립트

## <a name="synopsis"></a>개요
Windows:

`dotnet-install.ps1 [-Channel] [-Version]
    [-InstallDir] [-Debug] [-NoPath] 
    [-SharedRuntime]`

macOS/Linux:

`dotnet-install.sh [--channel] [--version]
    [--install-dir] [--debug] [--no-path] 
    [--shared-runtime]`

## <a name="description"></a>설명
`dotnet-install` 스크립트는 CLI 도구 체인 및 공유 런타임의 관리자가 아닌 일반 설치를 수행하는 데 사용됩니다. 스크립트는 [CLI GitHub 리포지토리](https://github.com/dotnet/cli/tree/rel/1.0.0-preview2/scripts/obtain)에서 다운로드할 수 있습니다. 

주요 사용 사례는 자동화 시나리오 및 관리자가 아닌 일반 설치에 도움을 주는 것입니다. 스크립트는 Windows에서 작동하는 PowerShell용 스크립트와 Linux/OS X에서 작동하는 bash 스크립트의 두 가지가 있습니다. 둘 다 동작은 동일합니다. 또한 Bash 스크립트는 PowerShell 스위치를 "인식"하므로 보드 전체에서 사용할 수 있습니다. 

설치 스크립트는 CLI 빌드 저장 위치에서 ZIP/tarball 파일을 다운로드하여 기본 위치나 `--install-dir`로 지정한 위치에 설치를 계속 진행합니다. 기본적으로 설치 스크립트는 SDK를 다운로드하여 설치합니다. 공유 런타임만 다운로드하려는 경우 `--shared-runtime` 인수를 지정할 수 있습니다. 

기본적으로 스크립트는 현재 세션에 대한 $PATH에 설치 위치를 추가합니다. 이 위치는 `--no-path` 인수를 사용하는 경우 재정의할 수 있습니다. 

스크립트를 실행하기 전에 필요한 모든 [종속성](https://github.com/dotnet/core/blob/master/Documentation/prereqs.md)을 설치하세요.

`--version` 인수를 사용하여 특정 버전을 설치할 수 있습니다. 버전은 세 부분으로 구성된 버전(예: 1.0.0-13232)으로 지정해야 합니다. 생략하면 스크립트가 호출된 폴더 위의 계층 구조에서 검색된, `version` 속성을 포함하는 첫 번째 [global.json](global-json.md) 파일로 기본 설정됩니다. 이 파일이 없을 경우 최신 파일을 사용합니다.

또한 이 스크립트를 사용하면 `--debug` 인수를 사용하여 디버그 기호와 함께 SDK 또는 공유 런타임 디버그 이진 파일을 가져올 수 있습니다. 첫 번째 설치에서 이 작업을 수행하지 않고 나중에 디버그 기호가 필요함을 인식한 경우 이 인수와 설치한 비트의 버전을 사용하여 스크립트를 다시 실행할 수 있습니다. 

## <a name="options"></a>옵션
옵션은 스크립트 구현에 따라 다릅니다. 

### <a name="powershell-windows"></a>PowerShell(Windows)
`-Channel [CHANNEL]`

설치할 채널(예: `future`, `preview`, `production`)입니다. 기본값은 `production`입니다.

`-Version [VERSION]`

설치할 CLI 버전입니다. 버전은 세 부분으로 구성된 버전(예: 1.0.0-13232)으로 지정해야 합니다. 생략하면 `version` 속성을 포함하는 첫 번째 [global.json](global-json.md) 파일로 기본 설정됩니다. 이 파일이 없을 경우 최신 파일을 사용합니다.     

`-InstallDir [DIR]`

설치할 경로입니다. 디렉터리가 없을 경우 만듭니다. 기본값은 *%LocalAppData%\.dotnet*입니다.

`-Debug`

`true`이면 디버깅 기호를 포함하는 더 큰 패키지를 사용해야 함을 나타내고, 그러지 않으면 `false`입니다. 기본값은 `false`입니다.

`-NoPath`

`true`이면 현재 세션에 대한 경로로 접두사/installdi을 내보내지 않음을 나타내고, 그러지 않으면 `false`입니다. 기본값은 `false`이며, PATH가 수정됩니다. 따라서 설치 후 바로 CLI 도구를 사용할 수 있게 됩니다. 

`-SharedRuntime`

`true`이면 공유 런타임 비트만 설치하고, `false`이면 전체 SDK를 설치합니다. 기본값은 `false`입니다.

### <a name="bash-macoslinux"></a>Bash(Linux/macOS)
`--channel [CHANNEL]`

설치할 채널(예: "future", "preview", "production")입니다. 기본값은 "Production"입니다.

`--version [VERSION]`

설치할 CLI 버전입니다. 버전은 세 부분으로 구성된 버전(예: 1.0.0-13232)으로 지정해야 합니다. 생략하면 `version` 속성을 포함하는 첫 번째 [global.json](global-json.md) 파일로 기본 설정됩니다. 이 파일이 없을 경우 최신 파일을 사용합니다.     

`--install-dir [DIR]`

설치할 위치 경로입니다. 디렉터리가 없을 경우 만듭니다. 기본값은 `$HOME/.dotnet`입니다.

`--debug`

`true`이면 디버깅 기호를 포함하는 더 큰 패키지를 사용해야 함을 나타내고, 그러지 않으면 `false`입니다. 기본값은 `false`입니다.

`--no-path`

`true`이면 현재 세션에 대한 경로로 접두사/installdi을 내보내지 않음을 나타내고, 그러지 않으면 `false`입니다. 기본값은 `false`이며, PATH가 수정됩니다. 따라서 설치 후 바로 CLI 도구를 사용할 수 있게 됩니다.  

`--shared-runtime`

`true`이면 공유 런타임 비트만 설치하고, `false`이면 전체 SDK를 설치합니다. 기본값은 `false`입니다.

## <a name="examples"></a>예제

기본 위치에 개발 최신 버전을 설치합니다.

Windows:

`./dotnet-install.ps1 -Channel Future`

macOS/Linux:

`./dotnet-install.sh --channel Future`

지정된 위치에 최신 미리 보기를 설치합니다.

Windows:

`./dotnet-install.ps1 -Channel preview -InstallDir C:\cli`

macOS/Linux:

`./dotnet-install.sh --channel preview --install-dir ~/cli`



<!--HONumber=Nov16_HO1-->


