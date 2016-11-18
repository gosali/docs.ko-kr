---
title: "Docker에서 콘솔 응용 프로그램 실행"
description: "기존 .NET Framework 콘솔 응용 프로그램을 가져와 Windows Docker 컨테이너에서 실행하는 방법을 알아봅니다."
author: spboyer
keywords: ".NET, 컨테이너, 콘솔, 응용 프로그램"
manager: wpickett
ms.date: 09/28/2016
ms.topic: article
ms.prod: .net-framework-4.6
ms.technology: vs-ide-deployment
ms.devlang: dotnet
ms.assetid: 85cca1d5-c9a4-4eb2-93e6-4f878de07fd7
translationtype: Human Translation
ms.sourcegitcommit: 15c55a87beb64f265a164db918c7721c7690fadf
ms.openlocfilehash: 6d311674cc50c8a86128cf88c39e3044f70ba183

---

# <a name="running-console-applications-in-windows-containers"></a>Windows 컨테이너에서 콘솔 응용 프로그램 실행

콘솔 응용 프로그램은 간단한 상태 쿼리에서 장기 실행 문서 이미지 처리 태스크에 이르기까지 다양한 용도로 사용됩니다. 어떤 경우든지 이러한 응용 프로그램을 시작하고 크기를 조정하는 기능은 하드웨어 취득, 시작 시간 또는 여러 인스턴스 실행의 제약을 받습니다.

Docker 및 Windows Server 컨테이너를 사용하도록 콘솔 응용 프로그램을 이동하면 이러한 응용 프로그램을 클린 상태에서 시작하고 작업을 수행한 다음 정상적으로 종료되도록 할 수 있습니다. 이 항목에서는 콘솔 응용 프로그램을 Windows 기반 컨테이너로 이동하고 PowerShell 스크립트를 사용하여 시작하는 데 필요한 단계를 보여 줍니다.

샘플 콘솔 응용 프로그램은 인수(이 경우 질문)를 사용하고 임의의 대답을 반환하는 간단한 예제입니다. 이 응용 프로그램은 `customer_id`를 가져와 해당 세금을 처리하거나 `image_url` 인수에 대한 미리 보기를 만듭니다.

대답 외에도 `Environment.MachineName`이 응답에 추가되어 로컬과 Windows 컨테이너에서 실행 중인 응용 프로그램 간의 차이를 보여 줍니다. 로컬에서 응용 프로그램을 실행하는 경우 로컬 컴퓨터 이름이 반환되어야 하며, Windows 컨테이너에서 실행하는 경우 컨테이너 세션 ID가 반환됩니다.

전체 예제는 [GitHub의 dotnet/core-docs 리포지토리](https://github.com/dotnet/docs/tree/master/samples/framework/docker/ConsoleRandomAnswerGenerator)에서 확인할 수 있습니다.

응용 프로그램을 컨테이너로 이동하는 작업을 시작하기 전에 몇 가지 Docker 용어를 알고 있어야 합니다.

> *Docker 이미지*는 OS(운영 체제), 시스템 구성 요소 및 응용 프로그램을 포함하여 실행 중인 컨테이너에 대한 환경을 정의하는 읽기 전용 템플릿입니다.

Docker 이미지의 중요한 특징 중 하나는 기본 이미지에서 이미지가 작성된다는 것입니다. 새 이미지를 작성할 때마다 기존 이미지에 소수의 특징이 추가됩니다. 

> *Docker 컨테이너*는 실행 중인 이미지 인스턴스입니다. 

많은 컨테이너에서 동일한 이미지를 실행하여 응용 프로그램을 확장합니다.
개념상, 이 작업은 여러 호스트에서 동일한 응용 프로그램을 실행하는 것과 비슷합니다.

Docker 사이트에서 [Docker 개요](https://docs.docker.com/engine/understanding-docker/)를 확인하여 Docker 아키텍처에 대해 자세히 알아볼 수 있습니다. 

몇 단계만 수행하면 콘솔 응용 프로그램을 이동할 수 있습니다.

1. [응용 프로그램 빌드](#building-the-application)
1. [이미지에 대한 Dockerfile 만들기](#creating-the-dockerfile)
1. [Docker 컨테이너를 빌드 및 실행하는 프로세스](#creating-the-image)

## <a name="prerequisites"></a>필수 구성 요소
Windows 컨테이너는 [Windows 10 1주년 업데이트](https://www.microsoft.com/en-us/software-download/windows10/) 또는 [Windows Server 2016](https://www.microsoft.com/en-us/cloud-platform/windows-server)에서 지원됩니다.

> [!NOTE]
>Windows Server 2016을 사용하는 경우 Windows용 Docker 설치 관리자에서 해당 기능을 사용하도록 설정되지 않기에, 수동으로 컨테이너를 사용하도록 설정해야 합니다. OS에 대한 업데이트가 모두 실행되었는지 확인한 다음 [컨테이너 호스트 배포](https://msdn.microsoft.com/en-us/virtualization/windowscontainers/deployment/deployment) 문서의 지침에 따라 컨테이너 및 Docker 기능을 설치합니다.

Windows 컨테이너를 지원하려면 Windows용 Docker 버전 1.12 베타 26 이상이 있어야 합니다. 기본적으로 Docker는 Linux 기반 컨테이너를 사용합니다. 시스템 트레이에서 Docker 아이콘을 마우스 오른쪽 단추로 클릭하고 **Windows 컨테이너로 전환**을 선택하여 Windows 컨테이너로 전환합니다. Docker에서 변경 프로세스가 실행되며 다시 시작해야 할 수도 있습니다.

![Windows-Containers](./media/console/SwitchContainer.png)

## <a name="building-the-application"></a>응용 프로그램 빌드
일반적으로 콘솔 응용 프로그램은 설치 관리자, FTP 또는 파일 공유 배포를 통해 배포됩니다. 컨테이너에 배포하는 경우 자산을 컴파일하고 Docker 이미지를 만들 때 사용할 수 있는 위치에 스테이징해야 합니다.

*build.ps1*에서 스크립트는 [MSBuild](https://msdn.microsoft.com/en-us/library/dd393574.aspx)를 통해 응용 프로그램을 컴파일하여 자산 빌드 태스크를 완료합니다. 필요한 자산을 마무리하기 위해 MSBuild에 몇 개의 매개 변수가 전달됩니다. 컴파일할 프로젝트 파일 또는 솔루션의 이름, 출력 위치, 마지막으로 구성(릴리스 또는 디버그)입니다.

`Invoke-MSBuild` 호출에서 `OutputPath`는 **publish**로 설정되고 `Configuration`은 **릴리스**로 설정됩니다. 

```
function Invoke-MSBuild ([string]$MSBuildPath, [string]$MSBuildParameters) {
    Invoke-Expression "$MSBuildPath $MSBuildParameters"
}

Invoke-MSBuild -MSBuildPath "MSBuild.exe" -MSBuildParameters ".\ConsoleRandomAnswerGenerator.csproj /p:OutputPath=.\publish /p:Configuration=Release"
```

## <a name="creating-the-dockerfile"></a>Dockerfile 만들기
콘솔 .NET Framework 응용 프로그램에 사용되는 기본 이미지는 [Docker 허브](https://hub.docker.com/r/microsoft/windowsservercore/)에서 공개적으로 사용할 수 있는 `microsoft/windowsservercore`입니다. 기본 이미지에는 Windows Server 2016 최소 설치와 .NET Framework 4.6.2가 포함되어 있으며 Windows 컨테이너의 기본 OS 이미지 역할을 합니다.

```
FROM microsoft/windowsservercore
ADD publish/ /
ENTRYPOINT ConsoleRandomAnswerGenerator.exe
```
Dockerfile의 첫 번째 줄에서는 [`FROM`](https://docs.docker.com/engine/reference/builder/#/from) 명령을 사용하여 기본 이미지를 지정합니다. 파일에 있는 [`ADD`](https://docs.docker.com/engine/reference/builder/#/add)는 **publish** 폴더의 응용 프로그램 자산을 컨테이너의 루트 폴더에 복사합니다. 마지막으로, 이미지의 [`ENTRYPOINT`](https://docs.docker.com/engine/reference/builder/#/entrypoint) 설정은 컨테이너를 시작할 때 실행할 명령 또는 응용 프로그램을 지정합니다. 

## <a name="creating-the-image"></a>이미지 만들기
Docker 이미지를 만들기 위해 다음 코드가 *build.ps1* 스크립트에 추가되었습니다. 스크립트를 실행하면 [응용 프로그램 빌드](#building-the-application) 섹션에서 정의된 MSBuild를 통해 컴파일된 자산을 사용하여 `console-random-answer-generator` 이미지가 생성됩니다.

```
$ImageName="console-random-answer-generator"

function Invoke-Docker-Build ([string]$ImageName, [string]$ImagePath, [string]$DockerBuildArgs = "") {
    echo "docker build -t $ImageName $ImagePath $DockerBuildArgs"
    Invoke-Expression "docker build -t $ImageName $ImagePath $DockerBuildArgs"
}

Invoke-Docker-Build -ImageName $ImageName -ImagePath "."
```

PowerShell 명령 프롬프트에서 `.\build.ps1`을 사용하여 스크립트를 실행합니다.

빌드가 완료된 후 명령줄 또는 PowerShell 프롬프트에서 `docker images` 명령을 사용하면 이미지가 생성되고 실행할 준비가 완료됩니다.

```
REPOSITORY                        TAG                 IMAGE ID            CREATED             SIZE
console-random-answer-generator   latest              8f7c807db1b5        8 seconds ago       7.33 GB
```

## <a name="running-the-container"></a>컨테이너 실행
Docker 명령을 사용하여 명령줄에서 컨테이너를 시작할 수 있습니다.

```
docker run console-random-answer-generator "Are you a square container?"
```

출력은 다음과 같습니다.

```
The answer to your question: 'Are you a square container?' is Concentrate and ask again on (70C3D48F4343)
```

PowerShell에서 `docker ps -a` 명령을 실행하면 해당 컨테이너가 여전히 있음을 확인할 수 있습니다.

```
CONTAINER ID        IMAGE                             COMMAND                  CREATED             STATUS                          
70c3d48f4343        console-random-answer-generator   "cmd /S /C ConsoleRan"   2 minutes ago       Exited (0) About a minute ago      
```

STATUS 열에 "약 1분 전"이 표시되면 응용 프로그램이 완료된 것이며 종료할 수 있습니다. 명령을 백 번 실행한 경우 수행할 작업 없이 정적으로 유지되는 100개의 컨테이너가 있습니다. 시작 시나리오에서는 작업을 수행한 후 종료하거나 정리하는 것이 좋습니다. 해당 워크플로를 얻기 위해 `docker run` 명령에 `--rm` 옵션을 추가하면 `Exited` 신호를 받는 즉시 컨테이너가 제거됩니다.

```
docker run --rm console-random-answer-generator "Are you a square container?"
```

이 옵션으로 명령을 실행한 다음 `docker ps -a` 명령의 출력을 살펴보면 컨테이너 ID(`Environment.MachineName`)가 목록에 없는 것을 확인할 수 있습니다.

### <a name="running-the-container-using-powershell"></a>PowerShell을 사용하여 컨테이너 실행
샘플 프로젝트 파일에는 PowerShell을 사용하여 인수를 수락하는 응용 프로그램을 실행하는 방법의 예인 *run.ps1*도 있습니다.

실행하려면 PowerShell을 열고 다음 명령을 사용합니다.

```
.\run.ps1 "Is this easy or what?"
```

## <a name="summary"></a>요약
Dockerfile을 추가하고 응용 프로그램을 게시하기만 하면 .NET Framework 콘솔 응용 프로그램을 컨테이너화할 수 있으며, 이제 응용 프로그램 코드를 변경하지 않고 여러 인스턴스 실행, 클린 시작 및 중지 등의 Windows Server 2016 기능을 활용할 수 있습니다.



<!--HONumber=Nov16_HO3-->


