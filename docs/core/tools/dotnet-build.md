---
title: "dotnet-build 명령 | .NET Core SDK"
description: "dotnet-build 명령은 프로젝트와 모든 종속성을 빌드합니다."
keywords: "dotnet-build, CLI, CLI 명령, .NET Core"
author: mairaw
manager: wpickett
ms.date: 10/13/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 70285a83-4103-4617-be8b-d0e1e9a4a91d
translationtype: Human Translation
ms.sourcegitcommit: c6ee3f5663d0a3f62914e8de474cca4d15340c9d
ms.openlocfilehash: 344f8154c63bbb3c5ce6840bc7c7b1659950c223

---

#<a name="dotnetbuild"></a>dotnet-build

## <a name="name"></a>이름 
dotnet-build -- 프로젝트 및 모든 종속성을 빌드합니다. 

## <a name="synopsis"></a>개요

`dotnet build [--help] [--output]  
    [--build-base-path] [--framework]  
    [--configuration]  [--runtime] [--version-suffix]
    [--build-profile]  [--no-incremental] [--no-dependencies]
    [<project>]`

## <a name="description"></a>설명

`dotnet build` 명령은 소스 프로젝트의 여러 소스 파일과 해당 종속성을 이진으로 빌드합니다. 기본적으로 결과 이진은 IL(중간 언어)이며 DLL 확장명을 갖습니다. 
`dotnet build`는 또한 호스트에서 응용 프로그램을 실행하는 데 필요한 항목을 간략하게 설명하는 `\*.deps` 파일을 삭제합니다.  

빌드하려면 잠금 파일이 있어야 합니다. 즉, 코드를 빌드하기 전에 [`dotnet restore`](dotnet-restore.md)를 실행해야 합니다.

컴파일을 시작하기 전에 `build` 동사는 증분 안전 검사를 위해 프로젝트 및 해당 종속성을 분석합니다.
모든 검사를 통과하면 빌드에서 프로젝트 및 해당 종속성의 증분 컴파일을 계속 진행하고, 그러지 않으면 비증분 컴파일로 대체합니다. 프로필 플래그를 통해 사용자는 빌드 시간을 개선하는 방법에 대한 추가 정보를 받도록 선택할 수 있습니다.

컴파일 프로세스가 증분이 되려면 컴파일해야 하는 종속성 그래프의 모든 프로젝트가 다음 안전 검사를 통과해야 합니다.
- 사전/사후 컴파일 스크립트를 사용하지 않음
- PATH에서 컴파일 도구(예: resgen, 컴파일러)를 로드하지 않음
- 알려진 컴파일러(csc, vbc, fsc)만 사용

라이브러리 대신 실행 가능한 응용 프로그램을 빌드하려면 project.json 파일의 [특수한 구성](project-json.md#emitentrypoint) 섹션이 필요합니다.

```json
{ 
    "buildOptions": {
      "emitEntryPoint": true
    }
}
```

## <a name="options"></a>옵션

`-h|--help`

명령에 대한 간단한 도움말을 출력합니다.  

`-o|--output <OUTPUT_DIRECTORY>`

빌드된 이진 파일을 배치할 디렉터리입니다. 이 옵션을 지정하는 경우 `--framework`도 정의해야 합니다.

`-b|--build-base-path <OUTPUT_DIRECTORY>`

임시 출력을 배치할 디렉터리입니다.

`-f|--framework <FRAMEWORK>`

특정 프레임워크에 대해 컴파일합니다. 프레임워크는 [project.json](project-json.md#frameworks) 파일에 정의해야 합니다.

`-c|--configuration [Debug|Release]`

빌드할 구성을 정의합니다.  생략하면 `Debug`로 기본 설정됩니다.

`-r|--runtime [RUNTIME_IDENTIFIER]`

빌드할 대상 런타임입니다. 사용할 수 있는 RID(런타임 식별자) 목록은 [RID 카탈로그](../rid-catalog.md)를 참조하세요. 

`--version-suffix [VERSION_SUFFIX]`

[project.json](project-json.md#version) 파일의 버전 필드에서 `*`를 대체할 항목을 정의합니다. 형식은 NuGet의 버전 지침을 따릅니다. 

`--build-profile`

증분 컴파일이 자동으로 설정되도록 하기 위해 사용자가 해결해야 하는 증분 안전 검사를 출력합니다.

`--no-incremental`

빌드를 증분 빌드에 안전하지 않은 것으로 표시합니다. 따라서 증분 컴파일이 해제되고 프로젝트 종속성 그래프를 강제로 완전히 다시 빌드합니다.

`--no-dependencies`

프로젝트 간 참조를 무시하고 빌드하도록 지정된 루트 프로젝트만 빌드합니다.

## <a name="examples"></a>예제

프로젝트 및 해당 종속성을 빌드합니다.

`dotnet build`

릴리스 구성을 사용하여 프로젝트 및 해당 종속성을 빌드합니다.

`dotnet build --configuration Release`

특정 런타임(이 예제의 경우 Ubuntu 16.04)에 대한 프로젝트 및 해당 종속성을 빌드합니다.

`dotnet build --runtime ubuntu.16.04-x64`


<!--HONumber=Nov16_HO1-->


