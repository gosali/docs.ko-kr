---
title: "Docker용 Visual Studio Tools"
description: "Docker용 Visual Studio Tools 사용"
keywords: .NET, .NET Core, Docker, ASP.NET Core, Visual Studio 2015
author: spboyer
manager: wpickett
ms.date: 09/16/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 1f3b9a68-4dea-4b60-8cb3-f46164eedbbf
translationtype: Human Translation
ms.sourcegitcommit: 03193513706967a46f74bccdba08a56946dd67df
ms.openlocfilehash: f2792154a2f231fffc8e6f79c32b96e05edc71fa

---

# <a name="visual-studio-tools-for-docker"></a>Docker용 Visual Studio Tools 
Docker 컨테이너에서 응용 프로그램을 개발 및 디버깅하는 것은 다양한 도구를 사용하여 설정하는 일련의 절차입니다. [Docker용 Visual Studio Tools](https://visualstudiogallery.msdn.microsoft.com/0f5b2caa-ea00-41c8-b8a2-058c7da0b3e4)를 사용하면 장애를 통과하여 F5 키로 버그를 찾고 로컬에서 호스트된 Docker 컨테이너에서 직접 응용 프로그램을 디버그할 수 있습니다. 

> [!NOTE]
>현재 버전은 Linux Docker 컨테이너를 대상으로 하며 Windows 컨테이너도 곧 제공됩니다.

## <a name="prerequisites"></a>필수 구성 요소
- [Microsoft Visual Studio 2015 업데이트 3](https://www.visualstudio.com/downloads/download-visual-studio-vs)
- [.NET Core 1.0.1 - VS 2015 Tooling Preview 2](https://go.microsoft.com/fwlink/?LinkID=827546)
- Docker 컨테이너를 로컬로 실행하기 위한 [Windows용 Docker](https://www.docker.com/products/docker#/windows)

## <a name="installation-and-setup"></a>설치 및 설정
[Visual Studio 갤러리](http://visualstudiogallery.msdn.microsoft.com/)에서 [Docker용 Visual Studio Tools](https://aka.ms/DockerToolsForVS)를 다운로드 및 설치할 수도 있고, Visual Studio 내 **확장 및 업데이트**에서 검색할 수도 있습니다. 

필요한 구성은 Windows용 Docker에서 **[공유 드라이브](https://docs.docker.com/docker-for-windows/#/shared-drives)**를 설정하는 것입니다. 이 설정은 볼륨 매핑 및 디버깅 지원을 위해 필요합니다.

시스템 트레이에서 Docker 아이콘을 마우스 오른쪽 단추로 클릭하고 설정을 클릭한 다음 공유 드라이브를 선택합니다.

![공유 드라이브](./media/visual-studio-tools-for-docker/settings-shared-drives-win.png) 

## <a name="create-an-aspnet-web-application-and-add-docker-support"></a>ASP.NET 웹 응용 프로그램을 만들고 Docker 지원 추가

Visual Studio를 사용하여 새로운 ASP.NET Core 웹 응용 프로그램을 만듭니다. 응용 프로그램이 로드되면 **프로젝트 메뉴**에서 **Docker 지원 추가**를 선택하거나, 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **Docker 지원**을 선택합니다.

프로젝트 메뉴

![프로젝트 Docker 지원 추가](./media/visual-studio-tools-for-docker/project-add-docker-support.png)

프로젝트 상황에 맞는 메뉴

![Docker 지원 추가를 마우스 오른쪽 단추로 클릭](./media/visual-studio-tools-for-docker/right-click-add-docker-support.png)

다음 파일이 프로젝트에 추가됩니다.

- **Dockerfile**: ASP.NET Core 응용 프로그램용 Docker 파일은 [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore) 이미지를 기반으로 합니다. 이 이미지는 시작 성능을 개선하도록 prejit된 ASP.NET Core NuGet 패키지를 포함합니다. .NET Core 콘솔 응용 프로그램을 빌드할 때 Dockerfile FROM은 가장 최근의 [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet) 이미지를 참조합니다.
- **docker-compose.yml**: 빌드되어 docker-compose build/run으로 실행할 이미지 컬렉션을 정의하는 데 사용되는 기본 Docker Compose 파일입니다.  
- **docker compose.dev.debug.yml**: 디버그를 위해 구성을 설정할 때 반복되는 변경 사항에 사용할 추가 docker-compose 파일. Visual Studio는 -f docker-compose.yml -f docker-compose.dev.debug.yml을 호출하여 이들을 병합합니다. Visual Studio 개발 도구에서 이 compose 파일을 사용합니다.   
- **docker compose.dev.release.yml**: 릴리스 정의를 디버그하기 위한 추가 Docker Compose 파일입니다. 프로덕션 이미지의 내용을 변경하지 않도록 디버거를 볼륨 탑재합니다.  

docker-compose.yml 파일 프로젝트를 실행할 때 생성되는 이미지의 이름을 포함합니다. 

```
version '2'

services:
  hellodockertools:
    image:  user/hellodockertools${TAG}
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "80"

``` 

이 예제에서 `image: user/hellodockertools${TAG}`는 응용 프로그램이 **디버그** 모드에서 실행될 때 이미지 `user/hellodockertools:dev`, **릴리스** 모드에서 실행될 때 `user/hellodockertools:latest`를 각각 생성합니다. 

레지스트리에 이미지를 푸시하려는 경우 `user`를 Docker 허브 사용자 이름으로 변경할 수 있습니다. 예를 들어 구성에 따라 `spboyer/hellodockertools`로 변경하거나, 개인 레지스트리 url `privateregistry.domain.com/`으로 변경합니다.

### <a name="debugging"></a>디버깅
도구 모음의 디버그 드롭다운에서 **Docker**를 선택하고 F5 키를 눌러 응용 프로그램에서 디버깅을 시작합니다. 

- microsoft/aspnetcore 이미지를 가져옵니다(캐시에 아직 없는 경우).
- ASPNETCORE_ENVIRONMENT가 컨테이너 내에서 Development로 설정됩니다.
- 포트 80이 노출되고 동적으로 할당된 localhost용 포트에 매핑됩니다. 포트는 docker 호스트에 의해 결정되며 docker ps를 사용하여 쿼리할 수 있습니다. 
- 응용 프로그램이 컨테이너에 복사됩니다.
- 컨테이너에 연결된 디버거와 함께 기본 브라우저가 시작되며 동적으로 할당된 포트를 사용합니다. 

빌드된 결과 Docker 이미지는 응용 프로그램의 `dev` 이미지이며 `microsoft/aspnetcore` 이미지가 기본 이미지입니다.
참고: Debug 구성은 반복 환경을 제공하기 위해 탑재되는 볼륨을 사용하므로 dev 이미지에는 앱 콘텐츠가 없습니다. 이미지를 푸시하려면 Release 구성을 사용합니다.

```console
REPOSITORY                  TAG         IMAGE ID            CREATED         SIZE
spboyer/hellodockertools    dev         0b6e2e44b3df        4 minutes ago   268.9 MB
microsoft/aspnetcore        1.0.1       189ad4312ce7        5 days ago      268.9 MB
```

응용 프로그램은 `docker ps` 명령을 실행하여 볼 수 있는 컨테이너를 사용하여 실행됩니다.

```console
CONTAINER ID        IMAGE                          COMMAND               CREATED             STATUS              PORTS                   NAMES
3f240cf686c9        spboyer/hellodockertools:dev   "tail -f /dev/null"   4 minutes ago       Up 4 minutes        0.0.0.0:32769->80/tcp   hellodockertools_hellodockertools_1
```

### <a name="edit-and-continue"></a>편집하며 계속하기
정적 파일 및/또는 razor 템플릿 파일(.cshtml)에 대한 변경 사항은 자동으로 업데이트되므로 컴파일 단계가 필요하지 않습니다. 내용을 변경 및 저장하고 브라우저에서 새로 고침을 눌러 업데이트를 확인합니다.  

코드 파일을 수정하려면 컨테이너 내에서 컴파일하고 Kestrel을 다시 시작해야 합니다. 변경 후 컨테이너 내에서 CTRL + F5를 사용하여 프로세스를 수행하고 응용 프로그램을 시작합니다. Docker 컨테이너는 다시 빌드되거나 중지되지 않습니다. 명령줄에서 `docker ps`를 사용하면 원래 컨테이너가 10분 전을 기준으로 여전히 실행되고 있음을 알 수 있습니다. 

```console
CONTAINER ID        IMAGE                          COMMAND               CREATED             STATUS              PORTS                   NAMES
3f240cf686c9        spboyer/hellodockertools:dev   "tail -f /dev/null"   10 minutes ago      Up 10 minutes       0.0.0.0:32769->80/tcp   hellodockertools_hellodockertools_1
```

### <a name="publishing-docker-images"></a>Docker 이미지 게시 
응용 프로그램의 개발 및 디버그 주기를 완료하면 Docker용 Visual Studio Tools를 이용해 응용 프로그램의 프로덕션 이미지를 만들 수 있습니다. 디버그 드롭다운을 **릴리스**로 변경하고 응용 프로그램을 빌드합니다. 도구에서 `:latest` 태그의 이미지를 생성하면 이를 개인 레지스트리 또는 Docker Hub에 푸시할 수 있습니다. 

`docker images`를 사용하여 이미지의 목록을 볼 수 있습니다.

```console
REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
spboyer/hellodockertools   latest              8184ae38ba91        5 seconds ago       278.4 MB
spboyer/hellodockertools   dev                 0b6e2e44b3df        About an hour ago   268.9 MB
microsoft/aspnetcore       1.0.1               189ad4312ce7        5 days ago          268.9 MB
```

**dev** 이미지에 비해 프로덕션 또는 릴리스 이미지의 크기가 더 작을 수 있다고 예상할 수도 있지만, 볼륨 매핑을 사용하여 디버거와 응용 프로그램은 실제로 컨테이너 내에서가 아니라 로컬 컴퓨터에서 실행되었습니다. **최신** 이미지는 호스트 컴퓨터에서 응용 프로그램을 실행하기 위해 필요한 전체 응용 프로그램 코드를 패키지했으므로, 델타가 응용 프로그램 코드의 크기입니다.



<!--HONumber=Nov16_HO1-->


