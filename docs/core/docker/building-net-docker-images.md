---
title: ".NET Core Docker 이미지 작성"
description: "Docker 이미지 및 .NET Core 이해"
keywords: .NET, .NET Core, Docker
author: spboyer
manager: wpickett
ms.date: 08/29/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 03c28597-7e73-46d6-a9c3-f9cb55642739
translationtype: Human Translation
ms.sourcegitcommit: 1cb9e19ec9c9c0764244aeec5f62b812cbd91aef
ms.openlocfilehash: be658d2c660aff736eceffe23757c13154e78582

---
 

#<a name="building-docker-images-for-net-core-applications"></a>.NET Core 응용 프로그램에 대한 Docker 이미지 작성

.NET Core와 Docker를 함께 사용하는 방법을 이해하려면 먼저 제공되는 다양한 Docker 이미지와 올바른 사용 시기에 대해 알고 있어야 합니다. 여기서는 제공되는 변형 이미지를 살펴보고, ASP.NET Core Web API를 빌드하며, Yeoman Docker 도구를 사용하여 디버깅 가능한 컨테이너를 만들 뿐만 아니라 Visual Studio Code가 이 프로세스에 어떻게 도움이 되는지를 살펴봅니다. 

## <a name="docker-image-optimizations"></a>Docker 이미지 최적화

개발자를 위한 Docker 이미지를 작성할 때 다음 세 가지 주요 시나리오를 중점적으로 고려했습니다.

- .NET Core 앱을 개발하는 데 사용되는 이미지
- .NET Core 앱을 빌드하는 데 사용되는 이미지
- .NET Core 앱을 실행하는 데 사용되는 이미지

왜 이 세 가지 이미지가 중요할까요?
컨테이너화된 응용 프로그램을 개발, 빌드 및 실행할 때 서로 다른 우선 순위가 있습니다.
- **개발:**  얼마나 빨리 변경을 반복할 수 있는지 그리고 변경 내용을 디버그하는 기능이 중요합니다. 이미지의 크기는 중요하지 않으며 코드를 변경하고 변경 내용을 신속하게 확인할 수 있어야 합니다. [yo docker](https://aka.ms/yodocker)와 같이 VS Code에서 사용하는 일부 도구에서 개발하는 동안 이 이미지를 사용합니다. 
- **빌드:** 앱을 컴파일하는 데 무엇이 필요한지가 중요합니다. 여기에는 컴파일러와 이진 파일을 최적화하기 위한 다른 모든 종속성이 포함됩니다. 이 이미지는 배포하는 이미지가 아니며, 프로덕션 이미지에 배치하는 콘텐츠를 빌드하는 데 사용되는 이미지입니다. 이 이미지는 연속 통합 또는 빌드 환경에서 사용됩니다. 예를 들어 모든 종속성을 빌드 에이전트에 직접 설치하는 대신 빌드 에이전트는 빌드 이미지를 인스턴스화하여 이미지 내에 포함된 앱을 빌드하는 데 필요한 모든 종속성을 사용하여 응용 프로그램을 컴파일합니다. 빌드 에이전트는 이 Docker 이미지를 실행하는 방법만 알고 있으면 됩니다. 
- **프로덕션:** 이미지를 배포하고 시작할 수 있는 속도가 중요합니다. 이 이미지는 작기 때문에 Docker 레지스트리에서 Docker 호스트 사이의 네트워크를 신속하게 이동할 수 있습니다. 콘텐츠를 실행할 준비가 되면 Docker 실행부터 결과 처리까지 가장 빠른 시간에 수행할 수 있습니다. 변경이 불가능한 Docker 모델에서는 코드를 동적으로 컴파일할 필요가 없습니다. 이 이미지에 배치되는 콘텐츠는 이진 파일과 응용 프로그램을 실행하는 데 필요한 콘텐츠로 제한됩니다. 컴파일된 이진 파일, 이미지, .js 및 .css 파일을 포함하는 `dotnet publish`를 사용하는 게시된 출력을 예로 들 수 있습니다. 시간이 지남에 따라 미리 JIT 컴파일된 패키지를 포함하는 이미지가 표시됩니다.  

여러 버전의 .NET Core 이미지가 있지만 모두 하나 이상의 레이어를 공유합니다. 모든 이미지가 동일한 기본 레이어와 잠재적으로 다른 레이어를 공유하기 때문에 저장하는 데 필요한 디스크 공간의 크기나 레지스트리에서 끌어오는 델타가 전체보다 훨씬 더 작습니다.  

## <a name="docker-image-variations"></a>Docker 이미지 변형

위의 목표를 달성하기 위해 [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/)에서 이미지 변형을 제공합니다.

- `microsoft/dotnet:<version>-sdk`: 즉, **microsoft/dotnet:1.0.0-preview2-sdk**로, 이 이미지는 .NET Core 및 CLI(명령줄 도구)가 포함된 .NET Core SDK를 포함합니다. 이 이미지는 **개발 시나리오**에 매핑됩니다. 로컬 개발, 디버깅 및 단위 테스트에 이 이미지를 사용합니다. 예를 들어 코드 체크 인 전의 모든 개발 작업에 사용합니다. 이 이미지는 **빌드** 시나리오에도 사용할 수 있습니다.

- `microsoft/dotnet:<version>-core`: 즉, **microsoft/dotnet:1.0.0-core**로, [이식 가능한 .NET Core 응용 프로그램](../deploying/index.md)을 실행하는 이미지이며 **프로덕션**에서 응용 프로그램을 실행하는 데 맞게 최적화되어 있습니다. SDK를 포함하지 않으며 `dotnet publish`의 최적화된 출력을 수행하기 위한 것입니다. 이식 가능한 런타임은 실행 중인 여러 컨테이너에서 공유 이미지 레이어를 활용하는 경우 Docker 컨테이너 시나리오에 적합합니다.  

## <a name="alternative-images"></a>대체 이미지

개발, 빌드 및 프로덕션에 최적화된 시나리오 외에도 추가 이미지를 제공합니다.

- `microsoft/dotnet:<version>-onbuild`: 즉, **microsoft/dotnet:1.0.0-preview2-onbuild**로, [ONBUILD](https://docs.docker.com/engine/reference/builder/#/onbuild) 트리거를 포함합니다. 빌드는 응용 프로그램을 [COPY](https://docs.docker.com/engine/reference/builder/#/copy)하고, `dotnet restore`를 실행하며, Docker 이미지가 실행될 때 응용 프로그램을 실행하는 [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#/entrypoint) `dotnet run` 명령을 만듭니다. 프로덕션에 최적화된 이미지는 아니지만 단순히 소스 코드를 이미지에 복사하여 실행할 때 유용한 경우는 있습니다. 

- `microsoft/dotnet:<version>-core-deps`: 즉, **microsoft/dotnet:1.0.0-core-deps**로, 자체 포함 응용 프로그램을 실행하려는 경우 이 이미지를 사용합니다. .NET Core에 필요한 모든 기본 종속성과 함께 운영 체제를 포함합니다. 이 이미지는 고유한 사용자 지정 CoreFX 또는 CoreCLR 빌드에 대한 기본 이미지로 사용할 수도 있습니다. **onbuild** 변형이 단순히 코드를 이미지에 배치하고 실행하도록 최적화된 반면 이 이미지는 .NET 런타임이 응용 프로그램과 함께 패키지로 제공되는 .NET Core 앱을 실행하는 데 필요한 운영 체제 종속성만 갖도록 최적화되어 있습니다. 각 이미지가 응용 프로그램 내에서 .NET Core 런타임을 전달하기 때문에 이 이미지는 일반적으로 동일한 호스트에서 여러 .NET Core 컨테이너를 실행하는 데 최적화되어 있지 않으며 이미지 레이어를 활용할 수 없습니다.   

각 변형의 최신 버전:

- `microsoft/dotnet` 또는 `microsoft/dotnet:latest`(SDK 포함)
- `microsoft/dotnet:onbuild`
- `microsoft/dotnet:core`
- `microsoft/dotnet:core-deps`

다음은 개발 컴퓨터에서 다양한 크기를 보여 주는 `docker pull <imagename>` 다음의 이미지 목록입니다. 개발/빌드 변형인 `microsoft/dotnet:1.0.0-preview2-sdk`는 응용 프로그램을 개발하고 빌드하는 SDK를 포함하기 때문에 더 큽니다. 프로덕션 변형인 `microsoft/dotnet:core`는 .NET Core 런타임만 포함하기 때문에 더 작습니다. Linux에서 사용할 수 있는 최소 이미지인 `core-deps`는 훨씬 더 작지만 응용 프로그램에서 이 이미지를 사용하여 .NET 런타임의 개인 복사본을 복사해야 합니다. 컨테이너가 이미 개인 격리 장벽이기 때문에 여러 dotnet 기반 컨테이너를 실행할 경우 최적화가 손실됩니다. 

```
REPOSITORY          TAG                     IMAGE ID            SIZE
microsoft/dotnet    1.0.0-preview2-onbuild  19b6a6c4b1db        540.4 MB
microsoft/dotnet    onbuild                 19b6a6c4b1db        540.4 MB
microsoft/dotnet    1.0.0-preview2-sdk      a92c3d9ad0e7        540.4 MB
microsoft/dotnet    core                    5224a9f2a2aa        253.2 MB
microsoft/dotnet    1.0.0-core-deps         c981a2eebe0e        166.2 MB
microsoft/dotnet    core-deps               c981a2eebe0e        166.2 MB
microsoft/dotnet    latest                  03c10abbd08a        540.4 MB
microsoft/dotnet    1.0.0-core              b8da4a1fd280        253.2 MB
```

## <a name="prerequisites"></a>필수 조건

빌드하고 실행하려면 다음과 같은 몇 가지 항목을 설치해야 합니다.

- [.NET Core](http://dot.net)
- Docker 컨테이너를 로컬로 실행하기 위한 [Docker](https://www.docker.com/products/docker) 
- Web API 응용 프로그램을 만들기 위한 [ASP.NET용 Yeoman 생성기](https://github.com/omnisharp/generator-aspnet)
- Microsoft의 [Docker용 Yeoman 생성기](http://aka.ms/yodocker)

npm을 사용하여 ASP.NET Core 및 Docker용 Yeoman 생성기 설치 

```
npm install -g yo generator-aspnet generator-docker
```

> [!NOTE]
> 이 샘플에서는 [Visual Studio Code](http://code.visualstudio.com)를 편집기에 사용합니다.

## <a name="creating-the-web-api-application"></a>Web API 응용 프로그램 만들기

참조 지점으로 응용 프로그램을 컨테이너화하기 전에 먼저 로컬로 응용 프로그램을 실행합니다. 

완성된 응용 프로그램은 [GitHub의 dotnet/core-docs 리포지토리](https://github.com/dotnet/docs/tree/master/samples/core/docker/building-net-docker-images)에 있습니다.

응용 프로그램에 대한 디렉터리를 만듭니다.

해당 디렉터리에서 명령 또는 터미널 세션을 열고 다음을 입력하여 ASP.NET Yeoman 생성기를 사용합니다.
```
yo aspnet
```

**Web API 응용 프로그램**을 선택하고 앱의 이름으로 **api**를 입력한 다음 <Enter> 키를 탭합니다.  응용 프로그램이 스캐폴드되면 `/api` 디렉터리로 변경하고 `dotnet restore`를 사용하여 NuGet 종속성을 복원합니다.

```
cd api
dotnet restore
```

`dotnet run`을 사용하고 **http://localhost:5000/api/values**로 이동하여 응용 프로그램을 테스트합니다.

```javascript
[
    "value1",
    "value2"
]
```

`Ctrl+C`를 사용하여 응용 프로그램을 중지합니다.

## <a name="adding-docker-support"></a>Docker 지원 추가

Microsoft용 Yeoman 생성기를 사용하여 프로젝트에 Docker 지원을 추가할 수 있습니다. 현재 컨테이너 내에서 프로젝트를 빌드하고 실행하는 데 도움이 되는 Dockerfile 및 스크립트를 만들어 .NET Core, Node.js 및 Go 프로젝트를 지원합니다. 편집기 디버깅 및 명령 팔레트를 지원하기 위해 Visual Studio Code 관련 파일(launch.json, tasks.json)도 추가됩니다.

```console
$ yo docker

     _-----_     ╭──────────────────────────╮
    |       |    │   Welcome to the Docker  │
    |--(o)--|    │        generator!        │
   `---------´   │     Let's add Docker     │
    ( _´U`_ )    │  container magic to your │
    /___A___\   /│           app!           │
     |  ~  |     ╰──────────────────────────╯
   __'.___.'__
 ´   `  |° ´ Y `

? What language is your project using? (Use arrow keys)
❯ .NET Core
  Golang
  Node.js

```

- `.NET Core`를 프로젝트 형식으로 선택
- `rtm`: .NET Core의 버전
- `Y`: 프로젝트에서 웹 서버 사용
- `5000`: Web API 응용 프로그램에서 수신 대기하는 포트(http://localhost:5000)
- `api`: 이미지 이름
- `api`: 서비스 이름
- `api`: 작성 프로젝트 
- `Y`: 현재 Dockerfile 덮어쓰기

생성기가 완료되면 다음 파일이 프로젝트에 추가됩니다.

- .vscode/launch.json
- Dockerfile.debug
- Dockerfile
- docker-compose.debug.yml
- docker-compose.yml
- dockerTask.ps1
- dockerTask.sh
- .vscode/tasks.json

생성자는 두 개의 Dockerfile을 만듭니다.

**Dockerfile.debug** - 이 파일은 이미지 변형 목록에서 확인할 경우 SDK, CLI 및 .NET Core를 포함하고 개발 및 디버깅(F5)에 사용되는 이미지인 **microsoft/dotnet:1.0.0-preview2-sdk** 이미지를 기반으로 합니다. 이러한 모든 구성 요소를 포함하면 약 540MB의 더 큰 이미지가 생성됩니다.

**Dockerfile** - 이 이미지는 **microsoft/dotnet:1.0.0-core**를 기반으로 하는 릴리스 이미지이며 프로덕션에 사용됩니다. 빌드할 때 이 이미지는 약 253MB입니다.

### <a name="creating-the-docker-images"></a>Docker 이미지 만들기
`dockerTask.sh` 또는 `dockerTask.ps1` 스크립트를 사용하여 특정 환경에서 **api** 응용 프로그램에 대한 이미지 및 컨테이너를 빌드하거나 작성할 수 있습니다. 다음 명령을 실행하여 **debug** 이미지를 빌드합니다.

```bash
./dockerTask.sh build debug
```

이 이미지는 ASP.NET 응용 프로그램을 빌드하고 `dotnet restore`를 실행하며 디버거를 이미지에 추가하고 `ENTRYPOINT`를 설정한 다음 마지막으로 이미지에 앱을 복사합니다. 결과는 `TAG`가 *debug*인 *api*라는 Docker 이미지입니다.  `docker images`를 사용하여 컴퓨터의 이미지를 확인하세요.

```bash
docker images

REPOSITORY          TAG                  IMAGE ID            CREATED             SIZE
api                 debug                70e89fbc5dbe        a few seconds ago   779.8 MB
```

이미지를 생성하고 Docker 컨테이너 내에서 응용 프로그램을 실행하는 또 다른 방법은 Visual Studio Code에서 응용 프로그램을 열고 디버깅 도구를 사용하는 것입니다. 

VS Code의 왼쪽에 있는 보기 표시줄에서 디버깅 아이콘을 선택합니다.

![vscode 디버깅 아이콘](./media/building-net-docker-images/debugging_debugicon.png)

그런 다음 재생 아이콘이나 <F5> 키를 탭하여 이미지를 생성하고 컨테이너 내에서 응용 프로그램을 시작합니다. Web API가 http://localhost:5000에서 기본 웹 브라우저를 사용하여 시작됩니다.

![VSCode Docker 도구 디버그](./media/building-net-docker-images/docker-tools-vscode-f5.png)

마치 응용 프로그램이 컨테이너 내부가 아닌 개발 컴퓨터에서 로컬로 실행되는 것처럼 응용 프로그램에서 중단점을 설정하고 단계별로 실행하는 등의 작업을 수행할 수 있습니다. 컨테이너 내에서 디버깅하는 장점은 프로덕션 환경에 배포되는 것과 동일한 이미지가 사용된다는 점입니다.

릴리스 또는 프로덕션 이미지를 만들려면 `release` 환경 이름을 전달하여 터미널에서 명령을 실행하기만 하면 됩니다.

```bash
./dockerTask build release
```

이 명령은 더 작은 **microsoft/dotnet:core** 기본 이미지를 기반으로 이미지를 만들고, 포트 5000을 [EXPOSE](https://docs.docker.com/engine/reference/builder/#/expose) `dotnet api.dll`에 대한 [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#/entrypoint) 설정하며, `/app` 디렉터리에 이 이미지를 복사합니다. 훨씬 더 작은 이미지를 생성하는 디버거, SDK 또는 `dotnet restore`가 없습니다. 이미지 이름은 `TAG`가 **latest**인 **api**입니다.

```
REPOSITORY          TAG                  IMAGE ID            CREATED             SIZE
api                 debug                70e89fbc5dbe        1 hour ago        779.8 MB
api                 latest               ef17184c8de6        1 hour ago        260.7 MB
```

## <a name="summary"></a>요약

Docker 생성기를 사용하여 Web API 응용 프로그램에 필요한 파일을 추가하면 이미지의 개발 및 프로덕션 버전을 만드는 프로세스가 간단해집니다.  도구는 컨테이너 내에서 응용 프로그램의 단계별 실행 디버깅을 제공하는 Windows 및 Visual Studio Code 통합에서 동일한 결과를 얻도록 하는 PowerShell 스크립트도 제공하여 플랫폼 간 도구입니다. 이미지 변형 및 대상 시나리오를 이해하면 프로덕션 배포에 최적화된 이미지를 얻는 동시에 내부 루프 개발 프로세스를 최적화할 수 있습니다.  





<!--HONumber=Nov16_HO2-->


