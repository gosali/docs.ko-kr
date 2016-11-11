---
title: "dotnet-publish 명령 | .NET Core SDK"
description: "dotnet-publish 명령은 .NET Core 프로젝트를 디렉터리에 게시합니다."
keywords: "dotnet-publish, CLI, CLI 명령, .NET Core"
author: mairaw
manager: wpickett
ms.date: 10/07/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 8a7e1c52-5c57-4bf5-abad-727450ebeefd
translationtype: Human Translation
ms.sourcegitcommit: c6ee3f5663d0a3f62914e8de474cca4d15340c9d
ms.openlocfilehash: 2b00a2c6da73c2252997b63aca8fc475cac8999f

---

#<a name="dotnetpublish"></a>dotnet-publish

## <a name="name"></a>Name

`dotnet-publish` - 응용 프로그램 및 모든 종속성을 폴더로 압축하여 게시할 준비를 합니다.

## <a name="synopsis"></a>개요

`dotnet publish [project] 
    [--help] [--framework]  
    [--runtime] [--build-base-path] [--output]  
    [--version-suffix] [--configuration] [--native-subdirectory] [--no-build]`

## <a name="description"></a>설명

`dotnet publish`는 응용 프로그램을 컴파일하고 [project.json](project-json.md) 파일에 지정된 종속성을 읽은 다음 결과 파일 집합을 디렉터리에 게시합니다. 

이식 가능한 앱의 유형에 따라 결과 디렉터리에 다음 항목이 포함됩니다.

1. *이식 가능한 응용 프로그램* - 응용 프로그램의 IL(중간 언어) 코드 및 응용 프로그램의 모든 관리 종속성입니다.
    * *기본 종속성이 있는 이식 가능한 응용 프로그램* - 위와 같으며 각 기본 종속성의 지원되는 플랫폼에 대한 하위 디렉터리를 포함합니다. 
2. *자체 포함 응용 프로그램* - 위와 같으며 대상 플랫폼에 대한 전체 런타임을 포함합니다.

자세한 내용은 [.NET Core 응용 프로그램 배포](../deploying/index.md) 항목을 참조하세요.

## <a name="options"></a>옵션

`[project]` 

게시할 프로젝트이며, `[project]`를 지정하지 않을 경우 현재 디렉터리로 기본 설정됩니다. 이 값은 [project.json](project-json.md) 파일 또는 [project.json](project-json.md) 파일을 포함하는 프로젝트 디렉터리에 대한 경로일 수 있습니다. [project.json](project-json.md) 파일이 없으면 `dotnet publish`에서 오류를 throw합니다. 

`-h|--help`

명령에 대한 간단한 도움말을 출력합니다.  

`-f|--framework <FRAMEWORK>`

지정된 FID(프레임워크 식별자)에 대한 응용 프로그램을 게시합니다. 지정하지 않으면 [project.json](project-json.md#frameworks)에서 FID를 읽어 옵니다. 유효한 프레임워크가 없으면 명령에서 오류를 throw합니다. 유효한 프레임워크가 여러 개이면 명령에서 유효한 프레임워크를 모두 게시합니다. 

`-r|--runtime <RUNTIME_IDENTIFIER>`

지정된 런타임에 대한 응용 프로그램을 게시합니다. 사용할 수 있는 RID(런타임 식별자) 목록은 [RID 카탈로그](../rid-catalog.md)를 참조하세요.

`-b|--build-base-path <OUTPUT_DIRECTORY>`

임시 출력을 배치할 디렉터리입니다.

`-o|--output <OUTPUT_PATH>`

디렉터리를 배치할 경로를 지정합니다. 지정하지 않으면 이식 가능한 응용 프로그램의 경우 *_./bin/[configuration]/[framework]/_*로, 자체 포함 배포의 경우 *_./bin/[configuration]/[framework]/[runtime]_*으로 기본 설정됩니다.

`--version-suffix [VERSION_SUFFIX]`

project.json 파일의 버전 필드에서 `*`를 대체할 항목을 정의합니다.

`-c|--configuration [Debug|Release]`

게시할 때 사용할 구성입니다. 기본값은 `Debug`입니다.

`[--native-subdirectory]` - 종속성 패키지의 네이티브 자산에서 하위 디렉터리를 출력에 포함하는 임시 메커니즘입니다. 

`[--no-build]` - 게시하기 전에 프로젝트를 빌드하지 않습니다.

## <a name="examples"></a>예제

`project.json`에 있는 프레임워크를 사용하여 응용 프로그램을 게시합니다. `project.json`에 [runtimes](project-json.md#runtimes) 노드가 포함된 경우 현재 플랫폼의 RID를 게시합니다.

`dotnet publish`

지정된 [project.json](project-json.md)을 사용하여 응용 프로그램을 게시합니다.

`dotnet publish ~/projects/app1/project.json`
    
`netcoreapp1.0` 프레임워크를 사용하여 현재 응용 프로그램을 게시합니다.

`dotnet publish --framework netcoreapp1.0`
    
`netcoreapp1.0` 프레임워크 및 `OS X 10.10`에 대한 런타임을 사용하여 현재 응용 프로그램을 게시합니다. 이 RID는 `project.json` [runtimes](project-json.md#runtimes) 노드에 있어야 합니다.

`dotnet publish --framework netcoreapp1.0 --runtime osx.10.11-x64`

## <a name="see-also"></a>참고 항목
* [프레임워크](../../standard/frameworks.md)
* [RID(런타임 식별자) 카탈로그](../rid-catalog.md)


<!--HONumber=Nov16_HO1-->


