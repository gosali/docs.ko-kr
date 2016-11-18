---
title: "MSBuild를 사용하여 .NET Core 프로젝트 빌드"
description: "MSBuild를 사용하여 .NET Core 프로젝트 빌드"
keywords: .NET, .NET Core
author: dsplaisted
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 13c66464-4f14-4db6-aa8b-06f25e7ba894
translationtype: Human Translation
ms.sourcegitcommit: a04755da6417bb28bad5f28a18ead9feeba2d957
ms.openlocfilehash: ee8f409bf11f4b4a7136b886114616b233bc2cc0

---

# <a name="using-msbuild-to-build-net-core-projects"></a>MSBuild를 사용하여 .NET Core 프로젝트 빌드

.NET Core 도구는 [project.json에서 MSBuild 기반 프로젝트로 이동](https://blogs.msdn.microsoft.com/dotnet/2016/05/23/changes-to-project-json/)하고 있습니다.
MSBuild를 사용하는 .NET Core 도구의 첫 번째 버전이 Visual Studio의 다음 버전과 함께 출시될 것으로 예상합니다.  그러나 지금은 .NET Core용 MSBuild 프로젝트를 사용할 수 있으며, 이 페이지에서 그 방법을 보여 줍니다.

오늘날 *새* 프로젝트에서 .NET Core를 대상으로 하는 대부분의 사용자는 다음과 같은 이유 때문에 project.json의 기본 도구 환경을 사용하는 것이 좋습니다.

- MSBuild는 project.json의 많은 장점을 아직 지원하지 않습니다.
- 많은 ASP.NET 기반 도구가 현재 MSBuild 프로젝트와 작동하지 않습니다.
- MSBuild를 사용하는 .NET Core 도구가 릴리스되면 project.json에서 MSBuild 프로젝트로 자동으로 변환될 수 있을 것입니다. 

이미 MSBuild를 사용하며 .NET Core로 이식하려는 기존 프로젝트에 대해 .NET Core를 대상으로 지정하기 위해 MSBuild를 사용할 수 있습니다. 또는 project.json 프로젝트에 대해 잘 지원되지 않는 시나리오의 경우 빌드에서 MSBuild 확장성을 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- [Visual Studio 2015 업데이트 3](https://www.visualstudio.com/en-us/news/releasenotes/vs2015-update3-vs) 이상
- [Visual Studio 2015용 .NET Core 도구](https://www.visualstudio.com/downloads/download-visual-studio-vs)
- NuGet Visual Studio 확장 [v3.5.0-beta](https://dist.nuget.org/visualstudio-2015-vsix/v3.5.0-beta/NuGet.Tools.vsix) 이상

## <a name="creating-a-library-targeting-net-core"></a>.NET Core를 대상으로 하는 라이브러리 만들기

1. Visual Studio 메뉴 모음에서 **파일** | **새로 만들기** | **프로젝트**를 선택하고 **클래스 라이브러리(이식 가능)**를 선택합니다.

  ![새 프로젝트](./media/target-dotnetcore-with-msbuild/new-project-dialog-class-library-portable.png)

2. 프로젝트의 이름과 위치를 선택하고 **확인**을 클릭합니다.

3. "이식 가능한 클래스 라이브러리 추가" 대화 상자가 나타납니다.  **.NET Framework 4.6** 및 **ASP.NET Core 1.0**을 대상으로 선택하고 **확인**을 클릭합니다.

  ![휴대용 대상 대화 상자](./media/target-dotnetcore-with-msbuild/pcl-targets-dialog-net46-aspnetcore10.png)

4. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.
5. 프로젝트 속성의 **라이브러리** 탭에서 **.NET 플랫폼 표준을 대상으로 지정** 링크를 클릭하고 표시되는 대화 상자에서 **예**를 클릭합니다.
6. 프로젝트에서 `project.json` 파일을 열고 다음과 같이 변경합니다.
    - `NETStandard.Library` 패키지의 버전 번호를 `1.6.0`(패키지의 .NET Core 1.0 버전)으로 변경합니다.
    - 아래의 `imports` 정의를 `netstandard1.6` 프레임워크 정의 내부에 추가합니다.  이렇게 하면 프로젝트가 .NET 표준을 대상으로 하도록 업데이트되지 않은, .NET Core와 호환되는 NuGet 패키지를 참조할 수 있습니다.

        ```json
        "netstandard1.6": {
            "imports": [ "dnxcore50", "portable-net452" ]
        }
        ```

## <a name="creating-a-net-core-console-application"></a>.NET Core 콘솔 응용 프로그램 만들기
.NET Core에 대한 콘솔 응용 프로그램을 빌드하려면 MSBuild 빌드 프로세스의 일부를 사용자 지정해야 합니다.  [corefxlab](https://github.com/dotnet/corefxlab) 리포지토리에서 [CoreApp](https://github.com/dotnet/corefxlab/tree/master/samples/NetCoreSample/CoreApp)이라고 하는 .NET Core 콘솔 응용 프로그램에 대한 샘플 프로젝트를 찾을 수 있습니다.  또 다른 좋은 옵션은, 프로젝트 파일을 직접 변경하는 대신 .NET Core를 대상으로 지정하기 위해 별도의 MSBuild 대상 파일을 사용하는 [coretemplate](https://github.com/mellinoe/coretemplate)으로 시작하는 것입니다.  

Visual Studio에서 프로젝트를 만드는 것으로 시작한 다음 이를 수정하여 .NET Core를 대상으로 지정하는 것도 가능합니다.  다음 지침은 이를 위한 최소 단계를 보여 줍니다.  [CoreApp](https://github.com/dotnet/corefxlab/tree/master/samples/NetCoreSample/CoreApp) 또는 [coretemplate](https://github.com/mellinoe/coretemplate)과는 달리, 이 방식으로 만든 프로젝트에는 Linux 및 macOS를 대상으로 하는 구성이 포함되어 있지 않습니다.

1. Visual Studio 메뉴 모음에서 **파일** | **새로 만들기** | **프로젝트**를 선택하고 **콘솔 응용 프로그램**을 선택합니다.
2. 프로젝트의 이름과 위치를 선택하고 **확인**을 클릭합니다.
3. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택한 다음 **빌드** 탭으로 이동합니다.
4. **구성** 드롭다운(속성 페이지 상단)에서 **모든 구성**을 선택하고 **플랫폼 대상**을 **x64**로 변경합니다.
5. 프로젝트에서 `app.config` 파일 삭제
6. 다음 내용으로 `project.json` 파일을 프로젝트에 추가합니다.

    ```json
    {
        "dependencies": {
            "Microsoft.NETCore.App": "1.0.0-rc2-3002702"
        },
        "runtimes": {
            "win7-x64": { },
            "ubuntu.14.04-x64": { },
            "osx.10.10-x64": { }
        },
        "frameworks": {
            "netcoreapp1.0": {
                "imports": [ "dnxcore50", "portable-net452" ]
            }
        }
    }
    ```

7. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **프로젝트 언로드**를 선택하고, 마우스 오른쪽 단추를 다시 클릭하여 **편집 _MyProj.csproj_**를 선택하고 다음과 같이 변경합니다.
    - 모든 기본 `Reference` 항목을 제거합니다(`System`, `System.Core` 등).
    - 다음 속성을 프로젝트의 첫 번째 `PropertyGroup`에 추가합니다.

        ```xml
        <TargetFrameworkIdentifier>.NETCoreApp</TargetFrameworkIdentifier>
        <TargetFrameworkVersion>v1.0</TargetFrameworkVersion>
        <BaseNuGetRuntimeIdentifier>win7</BaseNuGetRuntimeIdentifier>
        <NoStdLib>true</NoStdLib>
        <NoWarn>$(NoWarn);1701</NoWarn>
        ```

    - 파일의 끝에 다음을 추가합니다(`Microsoft.CSharp.Targets`의 가져오기 이후).

        ```xml
        <PropertyGroup>
            <!-- We don't use any of MSBuild's resolution logic for resolving the framework, so just set these two
                    properties to any folder that exists to skip the GetReferenceAssemblyPaths task (not target) and
                    to prevent it from outputting a warning (MSB3644).
                -->
            <_TargetFrameworkDirectories>$(MSBuildThisFileDirectory)</_TargetFrameworkDirectories>
            <_FullFrameworkReferenceAssemblyPaths>$(MSBuildThisFileDirectory)</_FullFrameworkReferenceAssemblyPaths>

            <!-- MSBuild thinks all EXEs need binding redirects, not so for CoreCLR! -->
            <AutoUnifyAssemblyReferences>true</AutoUnifyAssemblyReferences>
            <AutoGenerateBindingRedirects>false</AutoGenerateBindingRedirects>

            <!-- Set up debug options to run with host, and to use the CoreCLR debug engine -->
            <StartAction>Program</StartAction>
            <StartProgram>$(TargetDir)dotnet.exe</StartProgram>
            <StartArguments>$(TargetPath)</StartArguments>
            <DebugEngines>{2E36F1D4-B23C-435D-AB41-18E608940038}</DebugEngines>
        </PropertyGroup>
        ```

    - .csproj 파일을 닫고 Visual Studio에서 프로젝트를 다시 로드합니다.

8. Visual Studio에서 F5 키를 눌러 프로그램을 실행하거나, 출력 폴더의 명령줄에서 다음을 사용하여 프로그램을 실행할 수 있습니다.`dotnet MyApp.exe` 



<!--HONumber=Nov16_HO3-->


