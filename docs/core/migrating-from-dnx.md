---
title: "DNX에서 .NET Core CLI로 마이그레이션"
description: "DNX에서 .NET Core CLI로 마이그레이션"
keywords: .NET, .NET Core
author: blackdwarf
ms.author: mairaw
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: c0d70120-78c8-4d26-bb3c-801f42fc2366
translationtype: Human Translation
ms.sourcegitcommit: 956a0766fe0171052983627f2cf2e8264d6b0365
ms.openlocfilehash: e79746734c179c3f7797a10bdcd79606b818afea

---

# <a name="migrating-from-dnx-to-net-core-cli"></a>DNX에서 .NET Core CLI로 마이그레이션

## <a name="overview"></a>개요
.NET Core 및 ASP.NET Core 1.0의 RC1 릴리스에서 DNX 도구를 발표했습니다. .NET Core 및 ASP.NET Core 1.0의 RC2 릴리스에서 DNX에서 .NET Core CLI로 전환했습니다.

가볍게 복습하는 의미에서 DNX에 대해 정리해 보겠습니다. DNX는 .NET Core, 좀 더 구체적으로 말해서 ASP.NET Core 1.0 응용 프로그램 빌드에 사용된 런타임 및 도구 집합이었습니다. DNX는 3개의 주요 부분으로 구성되었습니다.

1. DNVM - DNX를 얻기 위한 설치 스크립트
2. DNX(Dotnet 실행 런타임) - 코드를 실행하는 런타임
3. DNU(Dotnet 개발자 유틸리티) - 종속성을 관리하고 응용 프로그램을 빌드 및 게시하기 위한 도구

CLI의 도입에 따라 이제 위의 모든 요소는 단일 도구 집합의 일부입니다. 그러나 DNX는 RC1 시기에 사용 가능했으므로 이를 사용해 빌드한 프로젝트가 있고, 새 CLI 도구에서 해당 프로젝트를 이동하고자 할 수 있습니다. 

이 마이그레이션 가이드에서는 DNX에서 .NET Core CLI로 프로젝트를 마이그레이션하는 방법의 필수 사항을 다룹니다. .NET Core에서 막 프로젝트를 시작하는 경우 이 문서를 건너뛸 수 있습니다. 

## <a name="main-changes-in-the-tooling"></a>도구의 주요 변경 사항
먼저 설명해야 할 몇 가지 일반적인 도구 변경 사항이 있습니다. 

### <a name="no-more-dnvm"></a>더 이상 DNVM 없음
DNVM(*DotNet Version Manager*)은 컴퓨터에 DNX를 설치하는 데 사용된 bash/PowerShell 스크립트였습니다. DNVM을 통해 사용자는 지정한 피드(또는 기본 피드)에서 필요한 DNX를 가져와 특정 DNX를 "활성"으로 표시하여 특정 세션의 $PATH에 둘 수 있습니다. 이렇게 하면 다양한 도구를 사용할 수 있습니다.

DNVM은 기능 집합이 .NET Core CLI 도구에 포함된 변경 사항과 중복되므로 단종되었습니다.

[개요 문서](tools/index.md#installation)에 설명된 대로 CLI 도구는 중요한 두 가지 방법으로 패키지됩니다.

1. 지정된 플랫폼에 대한 기본 설치 관리자
2. 다른 상황(예: CI 서버)에 대한 설치 스크립트

이러한 점에서 DNVM 설치 기능이 필요하지 않습니다. 그러나 런타임 선택 기능은 어떤가요? 

특정 버전의 패키지를 종속성에 추가하여 `project.json`에서 런타임을 참조합니다. 이 변경으로 응용 프로그램은 새로운 런타임 비트를 사용할 수 있게 됩니다. 이러한 비트를 컴퓨터로 가져오는 것은 CLI와 마찬가지입니다. 지원되는 기본 설치 관리자 중 하나 또는 설치 스크립트를 통해 런타임을 설치합니다. 

### <a name="different-commands"></a>다양한 명령
DNX를 사용한 경우 세 부분(DNX, DNU 또는 DNVM) 중 하나에서 몇 가지 명령을 사용했습니다. CLI에서는 이러한 명령 중 몇 개는 변경되고, 일부는 사용할 수 없으며, 일부는 동일하지만 의미 체계가 약간 다릅니다. 

다음 표에서는 DNX/DNU 명령 및 해당 CLI 명령 간의 매핑을 보여줍니다.


| DNX 명령                       | CLI 명령       | 설명                                                                                                       |
|--------------------------------   |----------------   |-----------------------------------------------------------------------------------------------------------------  |
| dnx run                           | dotnet run        | 소스에서 코드를 실행합니다.                                                                                             |
| dnu build                         | dotnet build      | 코드의 IL 바이너리를 빌드합니다.                                                                                  |
| dnu pack                          | dotnet pack       | 코드의 NuGet 패키지를 패키지합니다.                                                                          |
| dnx \[command](예: "dnx web")   | 해당 없음\*             | DNX 세계에서 project.json에 정의된 대로 명령을 실행합니다.                                                       |
| dnu install                       | 해당 없음\*             | DNX 세계에서 종속성으로 패키지를 설치합니다.                                                              |
| dnu restore                       | dotnet restore    | Project.json에 지정된 종속성을 복원합니다.                                                              |
| dnu publish                       | dotnet publish    | 배포할 응용 프로그램을 세 가지 형식(이식 가능, 이식 가능과 네이티브, 독립 실행형) 중 하나로 게시합니다.    |
| dnu wrap                          | 해당 없음\*             | DNX 세계에서 csproj의 project.json을 래핑합니다.                                                                      |
| dnu commands                      | 해당 없음\*             | DNX 세계에서 전역적으로 설치된 명령을 관리합니다.                                                             |

(\*) - 이 기능은 설계상 CLI에서 지원되지 않습니다. 

## <a name="dnx-features-that-are-not-supported"></a>지원되지 않는 DNX 기능
위의 표에서 알 수 있듯이, 적어도 당분간은 CLI에서 지원하지 않기로 한 DNX 세계의 기능이 있습니다. 이 섹션에서는 그러한 기능 중 가장 중요한 것을 살펴보고, 지원 중단 배후의 원리, 그리고 그러한 기능이 필요한 경우의 해결 방법을 간략하게 설명합니다.

### <a name="global-commands"></a>전역 명령
DNU는 "명령 전역"이라는 개념과 함께 제공되었습니다. 이는 응용 프로그램 실행을 위해 지정한 DNX를 호출하는 셸 스크립트와 함께 NuGet 패키지로 패키지된 콘솔 응용 프로그램이었습니다. 

CLI는 이 개념을 지원하지 않습니다. 대신 친숙한 `dotnet <command>` 구문을 사용하여 호출할 수 있는 프로젝트별 명령을 추가하는 개념을 지원합니다. 자세한 내용은 [확장성 개요](tools/index.md#extensibility)에서 찾아볼 수 있습니다. 

### <a name="installing-dependencies"></a>종속성 설치
v1부터 .NET Core CLI 도구에는 종속성 설치를 위한 `install` 명령이 없습니다. NuGet에서 패키지를 설치하려면 이를 종속성으로 `project.json` 파일에 추가한 후 `dotnet restore`를 실행해야 합니다. 

### <a name="running-your-code"></a>코드 실행
코드를 실행하는 두 가지 중요한 방법이 있습니다. 하나는 소스에서 `dotnet run`으로 실행하는 것입니다. `dnx run`과는 달리, 이 경우 메모리 내 컴파일을 수행하지 않습니다. 실제로 `dotnet build`를 호출하여 코드를 빌드하고 빌드된 바이너리를 실행합니다. 

또 다른 방법은 `dotnet` 자체를 사용하여 코드를 실행하는 것입니다. 이는 어셈블리에 경로를 제공하여 수행됩니다(`dotnet path/to/an/assembly.dll`). 

## <a name="migrating-your-dnx-project-to-net-core-cli"></a>DNX 프로젝트를 .NET Core CLI로 마이그레이션
코드로 작업할 때 새 명령을 사용하는 것 외에도 DNX에서 마이그레이션할 때 세 가지 중요한 사항이 남아 있습니다.

1. CLI를 사용할 수 있도록 `global.json` 파일을 마이그레이션합니다(있는 경우).
2. 프로젝트 파일(`project.json`) 자체를 CLI 도구로 마이그레이션합니다.
3. DNX API를 해당 BCL API로 마이그레이션합니다. 

### <a name="changing-the-globaljson-file"></a>Global.json 파일 변경
`global.json` 파일은 RC1 및 RC2(또는 그 이상) 프로젝트 모두에 대한 솔루션 파일 역할을 합니다. Visual Studio는 물론 CLI 도구를 RC1 및 그 이후 버전과 차별화하기 위해, `"sdk": { "version" }` 속성을 사용하여 프로젝트가 RC1인지 또는 그 이후 버전인지를 구분합니다. `global.json`에 이 노드가 없는 경우 최신으로 간주됩니다. 

`global.json` 파일을 업데이트하려면 속성을 제거하거나 사용할 정확한 도구 버전으로 설정합니다(이 경우**1.0.0-preview2-003121**).

```json
{
    "sdk": {
        "version": "1.0.0-preview2-003121"
    }
}
```

### <a name="migrating-the-project-file"></a>프로젝트 파일 마이그레이션
CLI와 DNX는 둘 다 `project.json` 파일 기반의 동일한 기본 프로젝트 시스템을 사용합니다. 프로젝트 파일의 구문 및 의미 체계는 시나리오에 따른 약간의 차이를 제외하면 상당 부분 동일합니다. 또한 [스키마 파일](http://json.schemastore.org/project) 또는 좀 더 친숙한 [project.json 참조](tools/project-json.md)에서 볼 수 있는 스키마에 대한 몇 가지 변경 사항이 있습니다. 

콘솔 응용 프로그램을 작성하는 경우 프로젝트 파일에 다음 조각을 추가해야 합니다.

```json
"buildOptions": {
    "emitEntryPoint": true
}
```

이렇게 하면 `dotnet build`는 응용 프로그램의 진입점을 내보내서 코드 실행이 효과적으로 수행되도록 합니다. 클래스 라이브러리를 빌드하는 경우 위 섹션을 생략하면 됩니다. 물론 위 코드 조각을 `project.json` 파일에 추가한 후 정적 진입점을 추가해야 합니다. DNX에서 나오면 DNX가 지원하는 DI를 더 이상 사용할 수 없으므로 `static void Main()`이 기본 .NET 진입점이 되어야 합니다.

`project.json`에 "commands" 섹션이 있는 경우 이를 제거할 수 있습니다. DNU 명령으로 사용되었던 명령 중 일부(예: Entity Framework CLI 명령)는 CLI에 프로젝트별 확장으로 이식됩니다. 프로젝트에서 사용하는 명령을 빌드한 경우 이를 CLI 확장으로 교체해야 합니다. 이 경우 `project.json`의 `commands` 노드를 `tools` 노드로 교체하고, [CLI 확장성 섹션](tools/index.md#extensibility)에 설명된 대로 도구 종속성을 나열해야 합니다. 

이러한 작업이 완료되면 어떤 이식성 유형을 앱에 사용할지를 결정해야 합니다. .NET Core에서는 선택 가능한 폭넓은 이식성 옵션이 제공됩니다. 예를 들어 완전히 *이식 가능한* 응용 프로그램을 원할 수도 있고 *자체 포함* 응용 프로그램을 원할 수도 있습니다. 이식 가능한 응용 프로그램 옵션은 .NET Framework 응용 프로그램 작업과 유사하며, 대상 컴퓨터(.NET Core)에서 실행하기 위한 공유 구성 요소가 필요합니다. 자체 포함 응용 프로그램의 경우 .NET Core를 대상에 설치할 필요가 없지만, 지원할 각 OS에 대해 하나의 응용 프로그램을 생성해야 합니다. 이러한 이식성 형식 등에 대해서는  [응용 프로그램 이식성 유형](deploying/index.md) 문서에서 설명합니다. 

원하는 이식성 형식을 호출하면 대상 프레임워크를 변경해야 합니다. .NET Core용 응용 프로그램을 작성한 경우 대상 프레임워크로 `dnxcore50`을 사용할 가능성이 높습니다. 새로운 [.NET 표준 라이브러리](https://github.com/dotnet/corefx/blob/master/Documentation/architecture/net-platform-standard.md)가 가져온 CLI 및 변경 사항과 함께 프레임워크는 다음 중 하나여야 합니다.

1. `netcoreapp1.0`- .NET Core에서 응용 프로그램을 작성하는 경우(ASP.NET Core 응용 프로그램 포함)
2. `netstandard1.6`- .NET Core용 클래스 라이브러리를 작성하는 경우

`dnx451` 같은 다른 `dnx` 대상을 사용 중인 경우에도 역시 변경해야 합니다. `dnx451`을`net451` 로 변경해야 합니다. 자세한 내용은 [.NET 표준 라이브러리 문서](https://github.com/dotnet/corefx/blob/master/Documentation/architecture/net-platform-standard.md)를 참조하세요. 

`project.json`이 거의 준비되었습니다. 종속성 목록을 살펴보고, 특히 ASP.NET Core 종속성을 사용하는 경우 종속성을 새 버전으로 업데이트해야 합니다. 별도의 BCL API용 패키지를 사용한 경우 [응용 프로그램 이식성 유형](deploying/index.md) 문서에 설명된 대로 런타임 패키지를 사용할 수 있습니다. 

준비가 되면 `dotnet restore`로 복원을 시도할 수 있습니다. 종속성의 버전에 따라, NuGet이 위의 대상 프레임워크 중 하나에 대한 종속성을 확인할 수 없는 경우 오류가 발생할 수 있습니다. 이는 "시점" 문제입니다. 시간이 지나면서 이러한 프레임워크를 지원하는 패키지가 점점 더 많아질 것입니다. 지금은 `framework` 노드 내에서 `imports` 문을 사용하여, 프레임워크를 대상으로 하는 패키지를 복원할 수 있는 NuGet을 "imports" 문 내에 지정할 수 있습니다. 이 경우 표시되는 복원 오류는 어떤 프레임워크를 가져와야 하는지에 대한 충분한 정보를 제공합니다. 잘 모르는 경우 일반적으로 `imports` 문에서 `dnxcore50` 및 `portable-net45+win8`을 지정하면 도움이 될 수 있습니다. 다음의 JSON 코드 조각은 이를 보여 줍니다.

```json
    "frameworks": {
        "netcoreapp1.0": { 
            "imports": ["dnxcore50", "portable-net45+win8"]
        }
    }
```

`dotnet build`를 실행하면 너무 많지는 않더라도 궁극적인 빌드 오류가 표시됩니다. 코드를 빌드하여 적절히 실행한 후에는 Runner로 테스트할 수 있습니다. `dotnet <path-to-your-assembly>`를 실행하고 살펴보세요.





<!--HONumber=Nov16_HO2-->


