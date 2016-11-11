---
title: "dotnet-restore 명령 | .NET Core SDK"
description: "dotnet restore 명령을 사용하여 종속성 및 프로젝트 관련 도구를 복원하는 방법을 알아봅니다."
keywords: "dotnet-restore, CLI, CLI 명령, .NET Core"
author: mairaw
manager: wpickett
ms.date: 10/07/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 60489b25-38de-47e6-bed1-59d9f42e2d46
translationtype: Human Translation
ms.sourcegitcommit: c6ee3f5663d0a3f62914e8de474cca4d15340c9d
ms.openlocfilehash: 3c6c651aebfac0c27f340021d7779d37aa8bfe38

---

#<a name="dotnetrestore"></a>dotnet-restore

## <a name="name"></a>Name

`dotnet-restore` - 프로젝트의 종속성 및 도구를 복원합니다.

## <a name="synopsis"></a>개요

`dotnet restore [root] [--help] [--force-english-output] [--source]  
    [--packages] [--disable-parallel] [--fallbacksource] [--configfile] 
    [--no-cache] [--infer-runtimes] [--verbosity] [--ignore-failed-sources]`

## <a name="description"></a>설명

`dotnet restore` 명령은 NuGet을 사용하여 [project.json](project-json.md) 파일에 지정된 종속성 및 프로젝트 관련 도구를 복원합니다. 기본적으로 종속성 및 도구의 복원은 병렬로 수행됩니다.

종속성을 복원하려면 NuGet에 패키지가 있는 피드가 필요합니다. 일반적으로 피드는 NuGet.config 구성 파일을 통해 제공되며, 기본적으로 CLI 도구를 설치할 때 제공됩니다. 프로젝트 디렉터리에 고유한 NuGet.config 파일을 만들어 더 많은 피드를 지정할 수 있습니다. 명령 프롬프트에서 호출당 피드를 지정할 수도 있습니다. 

종속성의 경우 복원 작업 중 `--packages` 인수를 사용하여 복원된 패키지가 배치될 위치를 지정할 수 있습니다. 지정하지 않으면 기본 NuGet 패키지 캐시가 사용됩니다. 이 캐시는 모든 운영 체제에서 사용자의 홈 디렉터리(예: Linux의 경우 */home/user1* 또는 Windows의 경우 *C:\Users\user1*)에 있는 `.nuget/packages` 디렉터리에 있습니다.

프로젝트 관련 도구의 경우 `dotnet restore`는 먼저 도구가 압축된 패키지를 복원한 다음 [project.json](project-json.md)에 지정된 대로 도구의 종속성을 계속 복원합니다. 

## <a name="options"></a>옵션

`[root]` 
    
 복원하려는 프로젝트 또는 프로젝트 폴더의 목록입니다. 각 값은 [project.json](project-json.md) 또는 [global.json](global-json.md) 파일이나 폴더에 대한 경로일 수 있습니다. 폴더를 지정하면 복원 작업에서 모든 하위 디렉터리에 있는 [project.json](project-json.md) 파일을 재귀적으로 검색하고 검색된 각 [project.json](project-json.md) 파일을 복원합니다.

`-h|--help`

명령에 대한 간단한 도움말을 출력합니다.

 `--force-english-output`

고정 영어 기반 문화권을 사용하여 응용 프로그램을 강제로 실행합니다.

`-s|--source <SOURCE>`

복원 작업 중 사용할 NuGet 패키지 소스를 지정합니다. 이 소스는 NuGet.config 파일에 지정된 모든 소스를 재정의합니다. 이 옵션을 여러 번 지정하여 여러 소스를 제공할 수 있습니다.

`--packages <PACKAGES_DIRECTORY]`

복원된 패키지를 배치할 디렉터리를 지정합니다. 

`--disable-parallel`

여러 프로젝트를 병렬로 복원을 사용하지 않도록 설정합니다. 

`-f|--fallbacksource <FEED>`

다른 모든 소스가 실패할 경우 복원 작업에서 대체 소스로 사용할 패키지 소스 목록을 지정합니다. 모든 유효한 피드 형식이 허용됩니다. 이 옵션을 여러 번 지정하여 여러 대체 소스를 제공할 수 있습니다.

`--configfile <FILE>`

복원 작업에 사용할 NuGet 구성 파일(NuGet.config)입니다.

`--no-cache`

패키지 및 HTTP 요청을 캐시하지 하도록 지정합니다.

`--infer-runtimes`

NuGet이 레거시 리포지토리에서 RID(런타임 식별자)를 유추하도록 허용하는 임시 옵션입니다.

`--verbosity [LEVEL]`

사용할 로깅의 자세한 정도입니다. 허용되는 값은 `Debug`, `Verbose`, `Information`, `Minimal`, `Warning` 또는 `Error`입니다.

` --ignore-failed-sources`

버전 요구 사항을 충족하는 패키지가 있는 경우 실패한 소스에 대해서만 경고합니다.

## <a name="examples"></a>예제

현재 디렉터리에 있는 프로젝트에 대한 종속성 및 도구를 복원합니다.

`dotnet restore` 

지정된 경로에 있는 `app1` 프로젝트에 대한 종속성 및 도구를 복원합니다.

`dotnet restore ~/projects/app1/project.json`
    
대체 소스로 제공된 파일 경로를 사용하여 현재 디렉터리에 있는 프로젝트에 대한 종속성 및 도구를 복원합니다.

`dotnet restore -f c:\packages\mypackages` 

대체 소스로 제공된 두 개의 파일 경로를 사용하여 현재 디렉터리에 있는 프로젝트에 대한 종속성 및 도구를 복원합니다.

`dotnet restore -f c:\packages\mypackages -f c:\packages\myotherpackages` 

현재 디렉터리에 있는 프로젝트에 대한 종속성 및 도구를 복원하고 오류만 출력에 표시합니다.

`dotnet restore --verbosity Error`


<!--HONumber=Nov16_HO1-->


