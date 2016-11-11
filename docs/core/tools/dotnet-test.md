---
title: "dotnet-test 명령 | .NET Core SDK"
description: "`dotnet test` 명령은 지정된 프로젝트에서 단위 테스트를 실행하는 데 사용됩니다."
keywords: "dotnet-test, CLI, CLI 명령, .NET Core"
author: mairaw
manager: wpickett
ms.date: 10/07/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 3a0fa917-eb0a-4d7e-9217-d06e65455675
translationtype: Human Translation
ms.sourcegitcommit: c6ee3f5663d0a3f62914e8de474cca4d15340c9d
ms.openlocfilehash: b12861f0ce3c40bf4db51994ea5d4a92b8ef0162

---

#<a name="dotnettest"></a>dotnet-test

## <a name="name"></a>Name

`dotnet-test` - 구성된 Test Runner를 사용하여 단위 테스트를 실행합니다.

## <a name="synopsis"></a>개요

`dotnet test [project] [--help] 
    [--parentProcessId] [--port] [--configuration]   
    [--output] [--build-base-path] [--framework] [--runtime]
    [--no-build]`  

## <a name="description"></a>설명

`dotnet test` 명령은 지정된 프로젝트에서 단위 테스트를 실행하는 데 사용됩니다. 단위 테스트는 단위 테스트 프레임워크(예: NUnit 또는 xUnit) 및 해당 단위 테스트 프레임워크에 대한 dotnet Test Runner에 종속되어 있는 클래스 라이브러리 프로젝트입니다. 이러한 프로젝트는 NuGet 패키지로 패키지되고 프로젝트에 대한 일반 종속성으로 복원됩니다.

또한 테스트 프로젝트는 project.json에서 "testRunner" 노드를 사용하여 Test Runner 속성을 지정해야 합니다. 이 값에는 단위 테스트 프레임워크의 이름이 포함되어야 합니다.

다음 샘플 project.json에서는 필요한 속성을 보여 줍니다.

```json
{
  "version": "1.0.0-*",
  "buildOptions": {
    "debugType": "portable"
  },
  "dependencies": {
    "System.Runtime.Serialization.Primitives": "4.1.1",
    "xunit": "2.1.0",
    "dotnet-test-xunit": "1.0.0-rc2-192208-24"
  },
  "testRunner": "xunit",
  "frameworks": {
    "netcoreapp1.0": {
      "dependencies": {
        "Microsoft.NETCore.App": {
          "type": "platform",
          "version": "1.0.0"
        }
      },
      "imports": [
        "dotnet5.4",
        "portable-net451+win8"
      ]
    }
  }
}
```

`dotnet test`는 두 가지 실행 모드를 지원합니다.

1. 콘솔: 콘솔 모드에서 `dotnet test`는 전달되는 모든 명령을 완전히 실행하기만 하고 결과를 출력합니다. --port를 전달하지 않고 `dotnet test`를 호출할 때마다 콘솔 모드에서 실행된 다음 콘솔 모드에서 Runner가 실행되도록 합니다.
2. 디자인 타임: 편집기나 IDE(통합 개발 환경) 같은 다른 도구의 컨텍스트에서 사용됩니다. 이 모드에 대한 자세한 내용은 [dotnet-test 프로토콜](test-protocol.md) 문서에서 확인할 수 있습니다. 

## <a name="options"></a>옵션

`[project]`
    
테스트 프로젝트에 대한 경로를 지정합니다. 생략하면 현재 디렉터리로 기본 설정됩니다.

`-?|-h|--help`

명령에 대한 간단한 도움말을 출력합니다.

`--parentProcessId`

IDE에서 해당 프로세스 ID를 지정하는 데 사용됩니다. 부모 프로세스가 종료되면 테스트가 종료됩니다.

`--port`

IDE에서 연결을 수신 대기할 포트 번호를 지정하는 데 사용됩니다.

`-c|--configuration <Debug|Release>`

빌드할 구성입니다. 기본값은 `Release`입니다. 

`-o|--output [OUTPUT_DIRECTORY]`

실행할 이진 파일을 찾을 디렉터리입니다.

`-b|--build-base-path <OUTPUT_DIRECTORY>`

임시 출력을 배치할 디렉터리입니다.

`-f|--framework [FRAMEWORK]`

특정 프레임워크에 대한 테스트 이진 파일을 찾습니다.

`-r|--runtime [RUNTIME_IDENTIFIER]`

지정된 런타임에 대한 테스트 이진 파일을 찾습니다.

`--no-build` 

텍스트 프로젝트를 실행하기 전에 빌드하지 않습니다. 

## <a name="examples"></a>예제

현재 디렉터리에 있는 프로젝트에서 테스트를 실행합니다.

`dotnet test` 

test1 프로젝트에서 테스트를 실행합니다.

`dotnet test /projects/test1/project.json` 

## <a name="see-also"></a>참고 항목

[dotnet-test 통신 프로토콜](test-protocol.md)

[프레임워크](../../standard/frameworks.md)

[RID(런타임 식별자) 카탈로그](../rid-catalog.md)


<!--HONumber=Nov16_HO1-->


