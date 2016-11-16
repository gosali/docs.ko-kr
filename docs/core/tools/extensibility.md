---
title: ".NET Core CLI 확장성 모델"
description: ".NET Core CLI 확장성 모델"
keywords: "CLI, 확장성, 사용자 지정 명령, .NET Core"
author: mairaw
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 1bebd25a-120f-48d3-8c25-c89965afcbcd
translationtype: Human Translation
ms.sourcegitcommit: aeb199a9aeb1584570ad2a2942e2f22c75a59616
ms.openlocfilehash: ea16d4b841f5c93da222df56db36d6fb70ea35f9

---

# <a name="net-core-cli-extensibility-model"></a>.NET Core CLI 확장성 모델 

## <a name="overview"></a>개요
이 문서에서는 CLI 도구를 확장하는 주요 방법 및 각 방법을 구동하는 시나리오에 대해 설명합니다. 도구를 사용하는 방법에 대해 간략하게 설명할 뿐만 아니라 두 종류의 도구를 빌드하는 방법에 대한 간단한 메모를 제공합니다. 

## <a name="how-to-extend-cli-tools"></a>CLI 도구를 확장하는 방법
CLI 도구는 다음과 같은 두 가지 주요 방법으로 확장할 수 있습니다.

1. 프로젝트 단위로 NuGet 패키지 사용
2. 시스템의 PATH 사용

위에서 간략하게 설명한 두 가지 확장성 메커니즘은 둘 다를 사용하거나 하나만 사용할 수 있습니다. 선택하는 방법은 확장을 통해 달성하려는 목표에 따라 크게 달라집니다.

## <a name="perproject-based-extensibility"></a>프로젝트 단위 기반 확장성
프로젝트 단위 도구는 NuGet 패키지로 배포되는 [이식 가능한 콘솔 응용 프로그램](../deploying/index.md)입니다. 도구는 해당 도구를 참조하고 도구가 복원되는 프로젝트의 컨텍스트에서만 사용할 수 있습니다. 프로젝트 컨텍스트 외부(예: 프로젝트를 포함하는 디렉터리 외부)에서 호출하면 명령을 찾을 수 없기 때문에 실패합니다.

이러한 도구는 빌드 서버에도 완벽한데, `project.json` 외부의 항목이 필요하지 않기 때문입니다. 빌드 프로세스에서는 빌드하는 프로젝트에 대해 복원을 실행하므로 도구를 사용할 수 있습니다. F#과 같은 언어 프로젝트도 이 범주에 속합니다. 결국 각 프로젝트는 하나의 특정 언어로만 작성할 수 있습니다. 

마지막으로 이 확장성 모델에서는 프로젝트의 빌드된 출력에 액세스해야 하는 도구를 만들 수 있습니다. 예를 들어 [ASP.NET](https://www.asp.net/) MVC 응용 프로그램의 다양한 Razor 보기 도구는 이 범주에 속합니다. 

### <a name="consuming-perproject-tools"></a>프로젝트 단위 도구 사용
이러한 도구를 사용하려면 `tools` 노드를 `project.json`에 추가해야 합니다. `tools` 노드 내에서는 도구가 상주하는 패키지를 참조합니다. `dotnet restore`를 실행하면 도구 및 해당 종속성이 복원됩니다. 

프로젝트의 빌드 출력을 로드하여 실행해야 하는 도구의 경우 일반적으로 프로젝트 파일의 일반 종속성 아래에 나열되는 다른 종속성이 있습니다. 즉, 프로젝트의 코드를 로드하는 도구에는 다음과 같은 두 가지 구성 요소가 있습니다. 

1. "도구" 기본 호출자
2. 작업에 사용할 논리를 포함하는 여러 가지 다른 도구 

두 가지인 이유는 무엇인가요? 프로젝트의 빌드 출력을 로드해야 하는 도구에는 작업 중인 프로젝트와 통합된 종속성 그래프가 있어야 합니다. 종속성 비트를 추가하여 NuGet에서 이러한 종속성을 통합 그래프로 확인하도록 합니다. 호출자는 종속성 도구의 프레임워크뿐만 아니라 위치에 대해 추론해야 하기 때문에 있습니다. 호출자는 사용자가 종속성 도구를 지정하고 찾는 모든 리디렉션 인수(`-c`, `-o`, `-b`)를 허용할 수 있습니다. 또한 여러 프레임워크에 대해 여러 종속성 도구가 존재하여 모든 도구를 실행하거나 하나만 실행하는 등의 경우에 대한 정책을 구현할 수 있습니다. 일반적으로 논리는 이러한 두 도구 간에 필요한 방식으로 공유할 수 있습니다. 

간단한 도구를 추가하는 예제를 검토해 보겠습니다. 간단한 프로젝트에 도구만 추가합니다. 다음은 NuGet 패키지에서 지정된 API를 검색할 수 있는 `dotnet-api-search`라는 예제 명령이 지정된 경우 해당 도구를 사용하는 콘솔 응용 프로그램의 `project.json` 파일입니다.

```json
{
    "version": "1.0.0",
    "compilationOptions": {
        "emitEntryPoint": true
    },
    "dependencies": {
        "Microsoft.NETCore.App": {
            "type": "platform",
            "version": "1.0.0"
        }
    },
    "tools": {
        "dotnet-api-search": {
            "version": "1.0.0",
            "imports": ["dnxcore50"]
        }
    },
    "frameworks": {
        "netcoreapp1.0": {}
    }
}
```

`tools` 노드는 `dependencies` 노드와 유사한 방식으로 구성됩니다. 이 노드에는 도구와 최소 버전을 포함하는 패키지의 패키지 ID가 필요합니다. 위의 예제에서 `imports`라는 또 다른 문을 확인할 수 있습니다. 이 문은 도구의 복원 프로세스에 영향을 주고 도구가 대상 프레임워크 외에 `dnxcore50` 대상과도 호환되도록 지정합니다. 자세한 내용은 [project.json 참조](project-json.md)를 참조하세요.

### <a name="building-tools"></a>도구 빌드
앞에서 설명한 대로 도구는 단순히 이식 가능한 콘솔 응용 프로그램입니다. 모든 콘솔 응용 프로그램을 빌드하는 것처럼 도구도 빌드합니다. 도구를 빌드한 후에는 [`dotnet pack`](dotnet-pack.md) 명령을 사용하여 코드, 종속성에 대한 정보 등을 포함하는 NuGet 패키지(nupkg)를 만듭니다. 패키지 이름은 작성자가 원하는 대로 지정할 수 있지만 응용 프로그램 내부 실제 도구 이진은 `dotnet-<command>`의 규칙을 준수해야 `dotnet`을 호출할 수 있습니다. 

도구는 이식 가능한 응용 프로그램이므로 도구 사용자가 도구를 실행하려면 도구가 빌드된 버전의 .NET Core 라이브러리가 있어야 합니다. 도구에서 사용하고 .NET Core 라이브러리 내에서 포함되지 않은 다른 모든 종속성은 NuGet 캐시에서 복원되고 배치됩니다. 따라서 전체 도구는 .NET Core 라이브러리의 어셈블리뿐만 아니라 NuGet 캐시의 어셈블리를 사용하여 실행됩니다. 

이러한 종류의 도구에는 해당 도구를 사용하는 프로젝트의 종속성 그래프와 완전히 구분되는 종속성 그래프가 있습니다. 복원 프로세스에서는 먼저 프로젝트의 종속성을 복원한 다음 각 도구 및 해당 종속성을 복원합니다. 

보다 풍부한 예제 및 다양한 조합은 [.NET Core CLI 리포지토리](https://github.com/dotnet/cli/tree/rel/1.0.0-preview2/TestAssets/TestProjects)에서 확인할 수 있습니다. 동일한 리포지토리에서 [사용된 도구의 구현](https://github.com/dotnet/cli/tree/rel/1.0.0-preview2/TestAssets/TestPackages)도 확인할 수 있습니다. 

프로젝트의 빌드 출력을 로드하여 실행하는 도구 빌드는 약간 다릅니다. 앞에서 설명한 대로 이러한 종류의 도구에는 다음과 같은 두 가지 구성 요소가 있습니다.

1. 사용자가 호출하는 디스패처 도구
2. 빌드 출력을 찾는 방법 및 수행할 작업에 대한 논리를 포함하는 프레임워크별 종속성

이러한 주요 예제로는 [EF(Entity Framework)](https://github.com/aspnet/EntityFramework) 명령 및 [`dotnet test`](dotnet-test.md) 명령이 있습니다. 두 경우 모두 `project.json`의 `tools` 노드에서 참조되고 기본 디스패처인 도구가 있습니다. 사용자는 명령줄에서 이 도구를 호출합니다. 퍼즐의 두 번째 조각은 프로젝트의 기본 종속성(루트 종속성이나 프레임워크별 종속성)에 지정된 종속성입니다. 이 패키지에는 도구의 실제 논리가 포함됩니다. 패키지는 일반적인 종속성이므로 프로젝트에 대한 복원 프로세스의 일부로 복원됩니다. 

이전 종류의 도구와 달리 이러한 도구는 실제로 해당 도구를 사용하는 프로젝트 그래프의 일부입니다. 이는 프로젝트의 코드 및 잠재적으로 모든 종속성에 액세스해야 하기 때문입니다. 예를 들어 EF 도구는 어셈블리를 검색하여 필요한 코드를 찾아야 하기 때문에 필요합니다.  

이러한 두 가지 장기 솔루션 존재하는 또 다른 이유는 클리너 호출 모델을 허용하기 위해서입니다. 디스크에서 특정 아티팩트를 삭제하는 대부분의 CLI 명령(예: `dotnet build`, `dotnet publish`)을 통해 사용자는 `--output` 인수, `--build-base-path` 인수 또는 `--configuration` 인수를 사용하여 출력을 다른 경로로 리디렉션할 수 있습니다. 예를 들어 EF 도구에서 프로젝트의 빌드 출력을 찾을 수 있으려면 *두* `dotnet` 드라이버 및 `ef` 명령에 동일한 인수를 동일한 값으로 제공해야 합니다. 호출 모델을 사용하여 사용자는 디스패처 도구에 인수를 전달한 다음 해당 인수를 사용하여 출력 디렉터리에서 논리를 포함하는 필요한 이진을 찾을 수 있습니다. 

이 접근 방식의 좋은 예제는 [.NET Core CLI 리포지토리](https://github.com/dotnet/cli)에서 찾을 수 있습니다.

* [샘플 project.json 파일](https://github.com/dotnet/cli/blob/rel/1.0.0-preview2/TestAssets/DesktopTestProjects/AppWithDirectDependencyDesktopAndPortable/project.json)
* [디스패처의 구현](https://github.com/dotnet/cli/tree/rel/1.0.0-preview2/TestAssets/TestPackages/dotnet-dependency-tool-invoker)
* [프레임워크별 종속성의 구현](https://github.com/dotnet/cli/tree/rel/1.0.0-preview2/TestAssets/TestPackages/dotnet-desktop-and-portable)


### <a name="pathbased-extensibility"></a>PATH 기반 확장성
일반적으로 PATH 기반 확장성은 개념적으로 둘 이상의 프로젝트를 포함하는 도구가 필요한 개발 컴퓨터에 사용됩니다. 이 확장 메커니즘의 주요 단점은 도구가 있는 컴퓨터에 연결되어 있다는 점입니다. 따라서 다른 컴퓨터에서 필요한 경우에는 배포해야 합니다.

CLI 도구 집합 확장성의 이 패턴은 매우 간단합니다. [.NET Core CLI 개요](index.md)에서 설명한 대로 `dotnet` 드라이버는 `dotnet-<command>` 규칙에 따라 명명된 모든 명령을 실행할 수 있습니다. 기본 해결 논리에서는 여러 위치를 먼저 검색하고 마지막으로 시스템 PATH를 검색합니다. 요청한 명령이 시스템 PATH에 있고 호출할 수 있는 이진인 경우 `dotnet` 드라이버에서 호출합니다. 

이진은 운영 체제에서 실행할 수 있는 거의 모든 항목이 될 수 있습니다. Unix 시스템에서는 `chmod +x`를 통해 실행 비트를 설정하는 모든 항목을 의미하고, Windows에서는 Windows가 실행 방법을 알고 있는 모든 항목을 의미합니다. 

예를 들어 `dotnet clean` 명령의 매우 간단한 구현을 살펴보겠습니다. `bash`를 사용하여 이 명령을 구현합니다. 이 명령은 현재 디렉터리에서 `bin/` 및 `obj/` 디렉터리를 삭제하기만 합니다. `--lock` 인수가 전달되면 `project.lock.json` 파일도 삭제합니다. 전체 명령은 다음과 같습니다. 

```bash
#!/bin/bash

# Delete the bin and obj dirs
rm -rf bin/ obj/

LOCK_FILE=$1
if [[ "$LOCK_FILE" = "--lock" ]]; then
    rm project.lock.json
fi


echo "Cleaning complete..."
```

MacOS에서는 이 스크립트를 `dotnet-clean`으로 저장하고 `chmod +x dotnet-clean`을 사용하여 실행 파일 비트를 설정할 수 있습니다. 그런 다음 `ln -s dotnet-clean /usr/local/bin/` 명령을 사용하여 `/usr/local/bin`에 바로 가기 링크를 만들 수 있습니다. 그러면 `dotnet clean` 구문을 사용하여 정리 명령을 호출할 수 있습니다. 앱을 만들고 해당 앱에서 `dotnet build`를 실행한 다음 `dotnet clean`을 실행하여 테스트할 수 있습니다. 

## <a name="conclusion"></a>결론
.NET Core CLI 도구는 두 가지 주요 확장 포인트를 허용합니다. 프로젝트 단위 도구는 프로젝트의 컨텍스트 내에 포함되지만 복원을 통해 간편한 설치를 허용합니다. PATH 기반 도구는 단일 컴퓨터에서 사용할 수 있는 일반적인 프로젝트 간 도구에 적합합니다. 



<!--HONumber=Nov16_HO1-->


