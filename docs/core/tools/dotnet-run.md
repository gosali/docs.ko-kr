---
title: "dotnet-run 명령 | .NET Core SDK"
description: "dotnet-run 명령은 소스 코드에서 응용 프로그램을 실행하는 편리한 옵션을 제공합니다."
keywords: "dotnet-run, CLI, CLI 명령, .NET Core"
author: mairaw
manager: wpickett
ms.date: 10/07/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 495ff50b-cb30-4d30-8f20-beb3d5e7c31f
translationtype: Human Translation
ms.sourcegitcommit: c6ee3f5663d0a3f62914e8de474cca4d15340c9d
ms.openlocfilehash: 18731d9fcc190371d908779a69a81114e0685aba

---

#<a name="dotnetrun"></a>dotnet-run

## <a name="name"></a>이름 

dotnet-run -- 명시적 컴파일이나 시작 명령을 사용하지 않고 소스 코드를 '현재 위치'에서 실행합니다.

## <a name="synopsis"></a>개요

`dotnet run [--help] [--framework] [--configuration]
    [--project] [[--] [application arguments]]`

## <a name="description"></a>설명
`dotnet run` 명령은 하나의 명령을 사용하여 소스 코드에서 응용 프로그램을 실행하는 편리한 옵션을 제공합니다. 이 명령은 소스 코드를 컴파일하고 출력 프로그램을 생성한 다음 해당 프로그램을 실행합니다. 또한 빠른 반복 개발에 유용하며 소스 분산 프로그램(예: 웹 사이트)을 실행하는 데 사용할 수도 있습니다.

이 명령은 [dotnet build](dotnet-build.md)를 사용하여 프로그램을 시작하기 전에 소스 입력을 .NET 어셈블리로 빌드합니다. 이 명령 및 소스 입력 처리에 대한 요구 사항은 모두 빌드 명령에서 상속됩니다. 빌드 명령에 대한 설명서는 이러한 요구 사항에 대한 자세한 정보를 제공합니다.

출력 파일은 자식 *bin* 폴더에 기록되며, 이 폴더가 없을 경우 생성합니다. 필요에 따라 파일을 덮어씁니다. 임시 파일은 자식 *obj* 폴더에 기록됩니다.  

여러 프레임워크가 지정된 프로젝트의 경우 `dotnet run`은 먼저 .NET Core 프레임워크를 선택합니다. 이러한 프레임워크가 없을 경우 오류가 출력됩니다. 다른 프레임워크를 지정하려면 `--framework` 인수를 사용합니다.

`dotnet run` 명령은 빌드된 어셈블리가 아닌 프로젝트의 컨텍스트에서 사용해야 합니다. 이식 가능한 응용 프로그램 DLL을 대신 실행하려는 경우에는 다음 예제와 같이 다른 명령 없이 [dotnet](dotnet.md)을 사용해야 합니다.
 
`dotnet myapp.dll`

`dotnet` 드라이버에 대한 자세한 내용은 [.NET Core 명령줄 도구(CLI)](index.md) 항목을 참조하세요.

## <a name="options"></a>옵션

`--`

실행 중인 응용 프로그램에 대한 인수에서 `dotnet run`에 대한 인수를 구분합니다. 이 인수 다음의 모든 인수가 실행 중인 응용 프로그램에 전달됩니다. 

`-h|--help`

명령에 대한 간단한 도움말을 출력합니다.

`-f`, `--framework <FRAMEWORK_IDENTIFIER>`

지정된 FID(프레임워크 식별자)에 대한 응용 프로그램을 실행합니다. 

`-c`, `--configuration <Debug|Release>`

게시할 때 사용할 구성입니다. 기본값은 `Debug`입니다.

`-p`, `--project [PATH]`

실행할 프로젝트를 지정합니다. [project.json](project-json.md) 파일 또는 [project.json](project-json.md) 파일을 포함하는 디렉터리에 대한 경로일 수 있습니다. 지정하지 않으면 현재 디렉터리로 기본 설정됩니다. 

## <a name="examples"></a>예제

현재 디렉터리에 있는 프로젝트를 실행합니다.`dotnet run` 

지정된 프로젝트를 실행합니다.

`dotnet run --project /projects/proj1/project.json`

현재 디렉터리에 있는 프로젝트를 실행합니다. `--` 인수가 사용되었으므로 이 예제의 `--help` 인수는 실행 중인 응용 프로그램에 전달됩니다.

`dotnet run --configuration Release -- --help`


<!--HONumber=Nov16_HO1-->


