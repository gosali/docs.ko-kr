---
title: "ASP.NET MVC 응용 프로그램을 Windows 컨테이너로 마이그레이션"
description: "기존 ASP.NET MVC 응용 프로그램을 가져와 Windows Docker 컨테이너에서 실행하는 방법을 알아봅니다."
keywords: "Windows 컨테이너, Docker, ASP.NET MVC"
author: BillWagner
manager: wpickett
ms.date: 09/28/2016
ms.topic: article
ms.prod: .net-framework-4.6
ms.technology: dotnet-mvc
ms.devlang: dotnet
ms.assetid: c9f1d52c-b4bd-4b5d-b7f9-8f9ceaf778c4
translationtype: Human Translation
ms.sourcegitcommit: 15c55a87beb64f265a164db918c7721c7690fadf
ms.openlocfilehash: bde267042883d2f25848747047845a16b181e549

---

# <a name="migrating-aspnet-mvc-applications-to-windows-containers"></a>ASP.NET MVC 응용 프로그램을 Windows 컨테이너로 마이그레이션

Windows 컨테이너에서 기존 .NET Framework 기반 응용 프로그램을 실행하려면 응용 프로그램이 포함된 Docker 이미지를 만들고 해당 이미지를 실행할 컨테이너를 하나 이상 시작해야 합니다. 이 항목에서는 기존 [ASP.NET MVC 응용 프로그램](http://www.asp.net/mvc)을 가져오고 Windows 컨테이너에 배포하기 위해 수행해야 하는 태스크를 설명합니다.

먼저 기존 ASP.NET MVC 응용 프로그램으로 시작합니다. 그런 다음 Visual Studio를 사용하여 게시된 자산을 빌드합니다. Docker를 사용하여 응용 프로그램이 포함된 이미지를 만들고 시작 시 해당 응용 프로그램을 실행합니다. 작업이 끝나면 Windows 컨테이너에서 실행하는 사이트에 브라우저를 연결하고 응용 프로그램이 실행되는지 확인할 수 있습니다.

이 문서에서는 Docker에 대한 기본적인 지식이 있다고 가정합니다. 이러한 개념이 생소한 경우 Docker 사이트에서 [Docker 개요](https://docs.docker.com/engine/understanding-docker/)를 확인하여 Docker 아키텍처에 대해 자세히 알아볼 수 있습니다.

컨테이너에서 실행할 응용 프로그램은 임의로 질문에 대답하는 간단한 웹 사이트입니다. 이 응용 프로그램은 인증 지원이나 데이터베이스 저장소가 없는 기본 MVC 응용 프로그램으로, 웹 계층을 컨테이너로 이동하는 데 집중할 수 있게 합니다. 이후 항목에서는 컨테이너화된 응용 프로그램에서 영구 저장소를 이동 및 관리하는 방법을 보여줍니다.

응용 프로그램 이동 과정은 다음 단계로 이루어집니다.

1. [이미지에 대한 자산을 빌드하는 게시 태스크 만들기](#publish-script)
2. [응용 프로그램을 실행할 Docker 이미지 빌드](#build-the-image)
3. [이미지를 실행하는 Docker 컨테이너 시작](#start-a-container)
4. [브라우저를 사용하여 응용 프로그램 확인](#verify-in-the-browser)

완성된 응용 프로그램은 [GitHub의 dotnet/core-docs 리포지토리](https://github.com/dotnet/docs/tree/master/samples/framework/docker/MVCRandomAnswerGenerator)에 있습니다.

## <a name="prerequisites"></a>필수 조건

최소한 개발 컴퓨터에서 [Windows 10 1주년 업데이트](https://www.microsoft.com/en-us/software-download/windows10/) 또는 [Windows Server 2016](https://www.microsoft.com/en-us/cloud-platform/windows-server)을 실행해야 합니다. 

시작하기 전에 [Windows용 Docker](https://docs.docker.com/docker-for-windows/) 버전 1.12 베타 26 이상을 설치해야 합니다. Windows 컨테이너 지원은 현재 베타 채널에서만 사용할 수 있습니다.

> [!IMPORTANT]
> Windows Server 2016을 사용하는 경우 먼저 [컨테이너 호스트 배포 - Windows Server](https://msdn.microsoft.com/en-us/virtualization/windowscontainers/deployment/deployment)에 대한 지침을 따라야 Docker 컨테이너를 실행할 수 있습니다.

Docker를 설치 및 시작한 후 Windows를 기반으로 해서 Docker 이미지를 실행하려면 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 **Windows 컨테이너로 전환**을 선택해야 합니다. 이 명령을 실행하는 데 몇 초 정도 걸립니다.

![Windows 컨테이너][windows-container]

## <a name="publish-script"></a>게시 스크립트

첫 번째 단계는 Docker 이미지에 로드해야 하는 모든 자산을 한 곳에 모으는 것입니다. 다행히 Visual Studio **게시** 명령을 사용하여 응용 프로그램에 대한 게시 프로필을 만들 수 있습니다. 이 프로필은 이 자습서의 뒷부분에서 대상 이미지에 복사할 모든 자산을 디렉터리 트리 하나에 배치합니다.

**게시 단계**

1. Visual Studio에서 웹 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.
2. **사용자 지정 프로필 단추를 클릭하고 **파일 시스템**을 방법으로 선택합니다.
3. 디렉터리를 선택합니다. 규칙에 따라 다운로드한 샘플은 `bin/PublishOutput`을 사용합니다.

![게시 연결][publish-connection]

**설정** 탭의 **파일 게시 옵션** 섹션을 엽니다. **게시 중 미리 컴파일**을 선택합니다. 이 최적화는 Docker 컨테이너의 뷰가 컴파일되며 미리 컴파일된 뷰를 복사함을 의미합니다.

![게시 설정][publish-settings]

**게시**를 클릭하면 Visual Studio에서 필요한 모든 자산을 대상 폴더에 복사합니다. 

## <a name="build-the-image"></a>이미지 빌드

기본 이미지, 추가 구성 요소, 실행할 응용 프로그램 및 기타 구성 이미지에 대한 지침이 들어 있는 Docker 이미지를 Dockerfile에서 정의합니다.  Dockerfile은 이미지를 만드는 `docker build` 명령에 대한 입력입니다.

[Docker 허브](https://hub.docker.com/r/microsoft/aspnet/)에 있는 `microsft/aspnet` 이미지를 기반으로 해서 이미지를 빌드합니다.
기본 이미지인 `microsoft/aspnet`은 Windows Server 이미지입니다. Windows Server Core 외에도 IIS와 ASP.NET 4.6.2가 설치되어 사용할 수 있습니다. 컨테이너에서 이 이미지를 실행하면 IIS가 자동으로 시작되고 설치된 모든 웹 사이트가 활성화됩니다.

이미지를 만드는 Dockerfile은 다음과 같이 표시됩니다.

```
# The `FROM` instruction specifies the base image. You are
# extending the `microsoft/aspnet` image.

FROM microsoft/aspnet

# Next, this Dockerfile creates a directory for your application
RUN mkdir C:\randomanswers

# configure the new site in IIS.
RUN powershell -NoProfile -Command \
    Import-module IISAdministration; \
    New-IISSite -Name "ASPNET" -PhysicalPath C:\randomanswers -BindingInformation "*:8000:"

# This instruction tells the container to listen on port 8000. 
EXPOSE 8000

# The final instruction copies the site you published earlier into the container.
ADD containerImage/ /randomanswers
```

이 Dockerfile에는 `ENTRYPOINT` 명령이 없습니다. 해당 명령은 필요하지 않습니다.
기본 이미지에서는 컨테이너를 시작할 때 IIS가 시작되도록 합니다. 

Docker 빌드 명령을 실행하여 ASP.NET 응용 프로그램을 실행할 이미지를 만들어야 합니다. 이렇게 하려면 PowerShell 창을 열고 솔루션 디렉터리에서 다음 명령을 입력합니다.

```
docker build -t mvcrandomanswers .
```

이 명령은 Dockerfile의 지침에 따라 새 이미지를 빌드합니다. 이 과정에 [Docker 허브](http://hub.docker.com)에서 기본 이미지 끌어오기가 포함될 수도 있습니다. 해당 이미지에 응용 프로그램이 추가됩니다.

명령이 완료되면 `docker images` 명령을 실행하여 새 이미지에 대한 정보를 확인할 수 있습니다.

```
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
mvcrandomanswers              latest              86838648aab6        2 minutes ago       8.104 GB
```

사용자 컴퓨터의 이미지 ID는 다릅니다. 이제 응용 프로그램을 실행해 보겠습니다.

## <a name="start-a-container"></a>컨테이너 시작

다음 `docker run` 명령을 실행하여 컨테이너를 시작합니다.

```
docker run -d -p 8000:8000 --name randomanswers mvcrandomanswers
```

`-d` 인수는 분리된 모드로 이미지를 시작하도록 Docker에 지정합니다. 즉, Docker 이미지가 현재 셸에서 연결이 끊긴 상태로 실행됩니다.

`-p 8000:8000` 인수는 수신 포트를 매핑하는 방법을 Docker에 지정합니다. 이 예제에서는 호스트와 컨테이너 둘 다에서 포트 8000을 사용합니다.

`--name randomanswers`는 실행 중인 컨테이너에 이름을 제공합니다. 대부분의 명령에서 컨테이너 ID 대신 이 이름을 사용할 수 있습니다.

`mvcrandomanswers`는 시작할 이미지의 이름입니다.

## <a name="verify-in-the-browser"></a>브라우저에서 확인

> [!NOTE]
> 현재 릴리스에서는 `http://localhost`를 사용하여 사이트로 이동할 수 없습니다. 이 문제는 WinNAT에서 알려진 동작 때문이며 앞으로 해결될 예정입니다. 문제가 해결될 때까지 컨테이너의 IP 주소를 사용해야 합니다.

컨테이너가 시작되면 브라우저에서 실행 중인 컨테이너에 연결할 수 있도록 해당 IP 주소를 찾아야 합니다.

```
docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" randomanswers
172.31.194.61
```

IPv4 주소와 구성된 포트(8000)(표시된 예제에서는 `http://172.31.194.61:8000`)를 사용하여 실행 중인 컨테이너에 연결할 수 있습니다. 해당 URL을 브라우저에 입력하면 실행 중인 사이트가 표시됩니다.

> [!NOTE]
> 일부 VPN 또는 프록시 소프트웨어가 사이트로 이동하지 못하도록 차단할 수도 있습니다.
> 일시적으로 사용하지 않도록 설정하여 컨테이너가 작동하는지 확인할 수 있습니다.

GitHub의 샘플 디렉터리에는 이러한 명령을 자동으로 실행하는 [PowerShell 스크립트](https://github.com/dotnet/docs/tree/master/samples/framework/docker/MVCRandomAnswerGenerator/run.ps1)가 들어 있습니다. PowerShell 창을 열고 디렉터리를 솔루션 디렉터리로 변경한 후 다음을 입력합니다.

```
./run.ps1
```

이 명령은 이미지를 빌드하고 컴퓨터에 있는 이미지 목록을 표시하며 컨테이너를 시작하고 해당 컨테이너의 IP 주소를 표시합니다. 

작업이 끝나고 컨테이너를 중지하려는 경우 `docker
stop` 명령을 실행합니다.

```
docker stop randomanswers
```

컨테이너를 제거하려면 `docker rm` 명령을 실행합니다.

```
docker rm randomanswers
```

## <a name="summary"></a>요약

이 항목에서는 Windows Server 컨테이너에서 기존 ASP.NET MVC 응용 프로그램을 이동 및 실행하는 단계를 살펴보았습니다. 기존 응용 프로그램을 실행하기 위해 응용 프로그램을 변경하지 않아도 됩니다. 응용 프로그램을 게시하고 Docker 이미지를 빌드하며 새 컨테이너에서 해당 이미지를 시작하는 태스크를 실행해야 합니다. Windows Server 컨테이너를 활용하는 것이 기존 응용 프로그램을 이 환경으로 마이그레이션하는 가장 쉬운 경로입니다.

[windows-container]: media/aspnetmvc/SwitchContainer.png "Windows 컨테이너로 전환"
[publish-connection]: media/aspnetmvc/PublishConnection.png "파일 시스템에 게시"
[publish-settings]: media/aspnetmvc/PublishSettings.png "게시 설정"



<!--HONumber=Nov16_HO1-->


