---
title: ".NET Core로 이식 - 라이브러리"
description: ".NET Core로 이식 - 라이브러리"
keywords: .NET, .NET Core
author: cartermp
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: a0fd860d-d6b6-4659-b325-8a6e6f5fa4a1
translationtype: Human Translation
ms.sourcegitcommit: 46061efa8e33c6a73befa5181eb33b8deb2fa637
ms.openlocfilehash: 051c8d46abdafe722eec77a440e384efbae0e70a

---

# <a name="porting-to-net-core-libraries"></a>.NET Core로 이식 - 라이브러리

.NET Core 1.0이 릴리스되면서 기존 라이브러리 코드를 이식하여 플랫폼 간에 실행할 수 있게 되었습니다.  이 문서에서는 .NET 표준 라이브러리, 사용할 수 없는 기술, .NET Core 1.0에서 제공되는 더 적은 수의 API를 확인하는 방법, .NET Core SDK Preview 2와 함께 제공되는 도구를 사용하는 방법 및 코드 이식에 권장되는 접근 방식에 대해 설명합니다.

이식은 특히 코드베이스가 큰 경우 시간이 걸릴 수 있는 작업입니다.  또한 코드에 가장 적합하도록 필요에 따라 이 지침을 조정할 수 있어야 합니다.  코드베이스는 모두 각기 다르기 때문에 이 문서에서는 관련 내용을 최대한 포괄하도록 유연하게 구성하려고 하지만 경우에 따라 설명된 지침과는 다르게 작업해야 할 수도 있습니다.

## <a name="prerequisites"></a>필수 조건

이 문서에서는 Windows에서 Visual Studio 2015 이상을 사용하고 있다고 가정합니다.  .NET Core 코드를 작성하는 데 필요한 비트는 이전 버전의 Visual Studio에서 사용할 수 없습니다.

또한 이 문서에서는 [권장되는 이식 프로세스](index.md)를 이해하고 있으며 [타사 종속성](third-party-deps.md) 관련 문제를 모두 해결했다고 가정합니다.

## <a name="targeting-the-net-standard-library"></a>.NET 표준 라이브러리를 대상으로 지정

.NET Core에 대한 플랫폼 간 라이브러리를 작성하는 가장 좋은 방법은 [.NET 표준 라이브러리](../../standard/library.md)를 대상으로 지정하는 것입니다.  .NET 표준 라이브러리는 모든 .NET 런타임에서 사용할 수 있는 .NET API의 공식 사양이며,  .NET Core 런타임에서 지원됩니다.

즉, 사용할 수 있는 API와 지원할 수 있는 플랫폼 간에 균형을 이루어야 하며 원하는 항목에 가장 적합한 .NET 플랫폼 표준의 버전을 선택해야 한다는 의미입니다.

지금까지는 .NET 표준 1.0부터 1.6까지 7개의 다양한 버전을 고려할 수 있습니다.  더 높은 버전을 선택하면 더 적은 대상에서 실행하는 대신 더 많은 API에 액세스할 수 있습니다.  더 낮은 버전을 선택하면 더 적은 API를 사용하는 대신 더 많은 대상에서 코드를 실행할 수 있습니다.

편의를 위해 다음에서는 각 .NET 표준 버전과 해당 버전이 실행되는 각 특정 영역에 대한 매트릭스를 제공합니다.

| 플랫폼 이름 | Alias |  |  |  |  |  | | |
| :---------- | :--------- |:--------- |:--------- |:--------- |:--------- |:--------- |:--------- |:--------- |
|.NET 표준 | netstandard | 1.0 | 1.1 | 1.2 | 1.3 | 1.4 | 1.5 | 1.6 |
|.NET Core|netcoreapp|&rarr;|&rarr;|&rarr;|&rarr;|&rarr;|&rarr;|1.0|
|.NET Framework|net|&rarr;|4.5|4.5.1|4.6|4.6.1|4.6.2|4.6.3|
|Mono/Xamarin 플랫폼||&rarr;|&rarr;|&rarr;|&rarr;|&rarr;|&rarr;|*|
|범용 Windows 플랫폼|uap|&rarr;|&rarr;|&rarr;|&rarr;|10.0|||
|창|win|&rarr;|8.0|8.1|||||
|Windows Phone|wpa|&rarr;|&rarr;|8.1|||||
|Windows Phone Silverlight|wp|8.0|||||||

이해하고 있어야 할 핵심 사항은 **더 낮은 버전을 대상으로 하는 프로젝트는 더 높은 버전을 대상으로 하는 프로젝트를 참조할 수 없다**는 것입니다.  예를 들어 .NET 플랫폼 표준 버전 1.2를 대상으로 하는 프로젝트는 .NET 플랫폼 표준 버전 1.3 이상을 대상으로 하는 프로젝트를 참조할 수 없습니다.  그러나 프로젝트가 더 낮은 버전을 참조할 수는 **있으므로** .NET 플랫폼 표준 1.3을 대상으로 하는 프로젝트는 .NET 플랫폼 표준 1.2 이하를 대상으로 하는 프로젝트를 참조할 수 있습니다.

가능한 가장 낮은 .NET 표준 버전을 선택하여 프로젝트 전체에서 사용하는 것이 좋습니다.

자세한 내용은 [.NET 플랫폼 표준 라이브러리](../../standard/library.md)를 참조하세요.

## <a name="key-technologies-not-yet-available-on-the-net-standard-or-net-core"></a>.NET 표준 또는 .NET Core에서 아직 사용할 수 없는 핵심 기술

현재 .NET Core에는 사용할 수 없는 일부 기술을 .NET Framework에서 사용 중일 수 있습니다.  다음 하위 섹션은 각각 이러한 기술 중 하나에 해당합니다.  채택할 수 있는 대체 옵션이 있는 경우 나열됩니다.

### <a name="app-domains"></a>응용 프로그램 도메인

AppDomain은 .NET Framework에서 다양한 용도로 사용할 수 있습니다. 코드 격리의 경우 별도의 프로세스 및/또는 컨테이너를 대신 사용하는 것이 좋습니다. 어셈블리를 동적으로 로드하기 위해 새 @System.Runtime.Loader.AssemblyLoadContext 클래스를 사용하는 것이 좋습니다.

### <a name="remoting"></a>원격 통신

프로세스 간 통신을 위해 [파이프](https://docs.microsoft.com/dotnet/core/api/system.io.pipes) 또는 [메모리 매핑된 파일](https://docs.microsoft.com/dotnet/core/api/system.io.memorymappedfiles.memorymappedfile)과 같은 IPC(Inter-process communication) 메커니즘을 원격 통신 대신 사용할 수 있습니다.

컴퓨터 간에는 HTTP와 같이 가급적 오버헤드가 낮은 일반 텍스트 프로토콜인 네트워크 기반 솔루션을 대신 사용할 수 있습니다.  여기에서 ASP.NET Core에서 사용하는 웹 서버인 [KestrelHttpServer](https://github.com/aspnet/KestrelHttpServer)가 옵션이 될 수 있습니다.  [Castle.Core](https://github.com/castleproject/Core)를 통한 원격 프록시 생성도 고려할 옵션입니다.

### <a name="binary-serialization"></a>이진 Serialization

이진 Serialization 대신 여러 가지 다른 serialization 기술을 선택할 수 있습니다.  형식 및 공간 목표에 적합한 기술을 선택해야 합니다.  많이 사용되는 옵션은 다음과 같습니다.

* JSON의 경우 [JSON.NET](http://www.newtonsoft.com/json)
* XML 및 JSON의 경우 @System.Runtime.Serialization.DataContractSerializer
* XML의 경우 @System.Xml.Serialization.XmlSerializer
* 프로토콜 버퍼의 경우 [protobuf-net](https://github.com/mgravell/protobuf-net)

각 옵션의 장점에 대해 알아보고 요구 사항에 맞는 옵션을 선택하려면 연결된 리소스를 참조하세요.  여러 가지 다른 serialization 형식과 기술이 있으며, 그 중 대부분이 오픈 소스입니다.

### <a name="sandboxes"></a>샌드박스

샌드박싱 대신 최소 권한 집합으로 프로세스를 실행하기 위한 사용자 계정과 같이 운영 체제에서 제공하는 보안 경계를 사용할 수 있습니다.

## <a name="overview-of-projectjson"></a>`project.json` 개요

[project.json 프로젝트 모델](../tools/project-json.md)은 .NET Core SDK 1.0 Preview 2와 함께 제공되는 프로젝트 모델입니다.  이 모델은 현재 활용할 수 있는 다음과 같은 몇 가지 장점을 제공합니다.

* 단일 빌드에서 대상별 어셈블리를 생성할 수 있는 간단한 멀티 타기팅.
* 프로젝트의 빌드를 사용하여 NuGet 패키지를 쉽게 생성하는 기능.
* 프로젝트 파일에서 파일을 나열할 필요가 없음.
* NuGet 패키지 종속성과 프로젝트 간 종속성의 통합.

> `project.json`은 결국 사용되지 않지만 현재는 .NET 표준에서 라이브러리를 빌드하는 데 사용할 수 있습니다.

### <a name="the-project-file-projectjson"></a>프로젝트 파일: `project.json`

.NET Core 프로젝트는 `project.json` 파일을 포함하는 디렉터리로 정의됩니다.  이 파일은 패키지 종속성, 컴파일러 구성, 런타임 구성 등 프로젝트의 측면이 선언되는 위치입니다.

`dotnet restore` 명령은 이 프로젝트 파일을 읽고 프로젝트의 모든 종속성을 복원한 다음 `project.lock.json` 파일을 생성합니다.  이 파일에는 빌드 시스템에서 프로젝트를 빌드하는 데 필요한 모든 정보가 포함되어 있습니다.

`project.json` 파일에 대한 자세한 내용은 [project.json 참조](../tools/project-json.md)를 참조하세요.

### <a name="the-solution-file-globaljson"></a>솔루션 파일: `global.json`

`global.json` 파일은 여러 프로젝트를 포함하는 솔루션에 포함할 선택적 파일입니다.  일반적으로 이 파일은 프로젝트 집합의 루트 디렉터리에 있으며,  프로젝트를 포함할 수 있는 다양한 하위 디렉터리에 대해 빌드 시스템에 알리는 데 사용할 수 있습니다.  여러 프로젝트로 구성된 더 큰 시스템용입니다.

예를 들어 다음과 같이 최상위 `/src` 및 `/test` 폴더로 코드를 구성할 수 있습니다.

```json
{
    "projects":[ "src", "test" ]
}
```

그런 다음 `/src` 및 `/test` 내의 고유한 하위 폴더에 아래에 여러 `project.json` 파일을 넣을 수 있습니다.

### <a name="how-to-multitarget-with-projectjson"></a>다음을 사용한 멀티 타기팅 방법: `project.json`

많은 라이브러리는 최대한 광범위한 범위를 갖도록 멀티 타기팅됩니다.  .NET Core를 사용하면 멀티 타기팅은 "최고 수준의 개체"입니다. 즉, 단일 빌드로 플랫폼별 어셈블리를 쉽게 생성할 수 있습니다.

멀티 타기팅은 `project.json` 파일에 올바른 TFM(대상 프레임워크 모니커)을 추가하고, 각 대상에 대한 올바른 종속성(.NET Core의 경우 `dependencies` 및 .NET Framework의 경우 `frameworkAssemblies`)을 사용하고, 잠재적으로 `#if` 지시문을 사용하여 플랫폼별 API 사용에 대한 소스 코드를 조건부로 컴파일하는 것만큼 간단합니다.

예를 들어 일부 네트워크 작업을 수행하려는 라이브러리를 작성하고 모든 .NET Framework 버전, PCL(이식 가능한 클래스 라이브러리 Class) 프로필 및 .NET Core에서 해당 라이브러리를 실행하려는 경우를 가정해 보겠습니다.  .NET Core 및 .NET Framework 4.5+ 대상의 경우 `System.Net.Http` 라이브러리 및 `async`/`await`를 사용할 수 있습니다.  그러나 이전 버전의 .NET Framework에서는 해당 API를 사용할 수 없습니다.

다음은 .NET Framework 버전 2.0, 3.5, 4.0, 4.5 및 .NET 표준 1.6을 대상으로 하는 `project.json`의 샘플 `frameworks` 섹션입니다.

```javascript
{
    "frameworks":{
        "net20":{
            "frameworkAssemblies":{
                "System.Net":""
            }
        },
        "net35":{
            "frameworkAssemblies":{
                "System.Net":""
            }
        },
        "net40":{
            "frameworkAssemblies":{
                "System.Net":""
            }
        },
        "net45":{
            "frameworkAssemblies":{
                "System.Net.Http":"",
                "System.Threading.Tasks":""
            }
        },
        ".NETPortable,Version=v4.5,Profile=Profile259": {
            "buildOptions": {
                "define": [ "PORTABLE" ]
             },
             "frameworkAssemblies":{
                 "mscorlib":"",
                 "System":"",
                 "System.Core":"",
                 "System.Net.Http":""
             }
        },
        "netstandard16":{
            "dependencies":{
                "NETStandard.Library":"1.6.0",
                "System.Net.Http":"4.0.1",
                "System.Threading.Tasks":"4.0.11"
            }
        },
    }
}
```

PCL 대상은 특별합니다. 컴파일러에서 인식할 수 있도록 빌드 정의를 지정해야 하며 `mscorlib`를 포함하여 사용하는 모든 어셈블리를 지정해야 합니다.  

그러면 소스 코드에서 다음과 같이 종속성을 사용할 수 있습니다.

```csharp
#if (NET20 || NET35 || NET40 || PORTABLE)
using System.Net;
#else
using System.Net.Http;
using System.Threading.Tasks;
#endif
```

모든 .NET Framework 및 .NET 표준 대상에는 컴파일러에서 인식할 수 있는 이름이 있어야 합니다.

```
.NET Framework 2.0   --> NET20
.NET Framework 3.5   --> NET35
.NET Framework 4.0   --> NET40
.NET Framework 4.5   --> NET45
.NET Framework 4.5.1 --> NET451
.NET Framework 4.5.2 --> NET452
.NET Framework 4.6   --> NET46
.NET Framework 4.6.1 --> NET461
.NET Framework 4.6.2 --> NET462
.NET Standard 1.0    --> NETSTANDARD1_0
.NET Standard 1.1    --> NETSTANDARD1_1
.NET Standard 1.2    --> NETSTANDARD1_2
.NET Standard 1.3    --> NETSTANDARD1_3
.NET Standard 1.4    --> NETSTANDARD1_4
.NET Standard 1.5    --> NETSTANDARD1_5
.NET Standard 1.6    --> NETSTANDARD1_6
```

위에서 설명한 대로 PCL을 대상으로 하는 경우에는 컴파일러에서 이해할 수 있도록 빌드 정의를 지정해야 합니다.  컴파일러에서 사용할 수 있는 기본 정의는 없습니다.

### <a name="using-projectjson-in-visual-studio"></a>Visual Studio에서 `project.json` 사용

Visual Studio에서 `project.json`을 사용하는 두 가지 옵션은 다음과 같습니다.

1. 새 xproj 프로젝트 형식.
2. 표준 .NET을 지원하는, 대상이 다시 지정된 PCL 프로젝트.

각각에는 서로 다른 장점 및 단점이 있습니다.

#### <a name="when-to-pick-an-xproj-project"></a>Xproj 프로젝트를 선택하는 경우

Visual Studio의 새로운 Xproj 프로젝트 시스템에서는 `project.json` 기반 프로젝트 모델의 기능을 활용하여 기존 프로젝트 형식에 비해 두 가지 주요 기능을 제공합니다. 즉, 여러 어셈블리를 빌드하여 원활한 멀티 타기팅 및 빌드에서 NuGet 패키지를 직접 생성하는 기능을 제공합니다.

그러나 다음과 같이 사용할 수 있는 특정 기능이 부족해질 수 있습니다.

- F# 또는 Visual Basic에 대한 지원
- 지역화된 리소스 문자열로 위성 어셈블리 생성
- 파일 시스템에서 `.dll` 파일 직접 참조
- 참조 관리자에서 csproj 기반 프로젝트를 참조하는 기능(`.dll` 파일에 따라 직접 지원됨)

프로젝트 요구 사항이 상대적으로 적고 xproj의 새로운 기능을 활용할 수 있는 경우 프로젝트 시스템으로 선택해야 합니다.  이 작업은 Visual Studio에서 다음과 같이 수행할 수 있습니다.

1. Visual Studio 2015 이상을 사용하고 있는지 확인합니다.
2. 파일 | 새 프로젝트를 선택합니다.
3. Visual C#에서 ".NET Core"를 선택합니다.
4. "클래스 라이브러리(.NET Core)" 템플릿을 선택합니다. 

#### <a name="when-to-pick-a-pcl-project"></a>PCL 프로젝트를 선택하는 경우

Visual Studio에서 PCL(이식 가능한 클래스 라이브러리)을 만들고 프로젝트 구성 대화 상자에서 ".NET Core"를 선택하여 기존 프로젝트 시스템에서 .NET Core를 대상으로 지정할 수 있습니다.  그런 다음 .NET 표준을 기반으로 하도록 프로젝트 대상을 다시 지정해야 합니다.

1. Visual Studio에서 프로젝트 파일을 마우스 오른쪽 단추로 클릭하고 속성을 선택합니다.
2. 빌드에서 ".NET 표준으로 변환"을 선택합니다.

고급 프로젝트 시스템 요구 사항이 있는 경우 이 옵션을 선택해야 합니다.  `xproj` 프로젝트 시스템에서처럼 플랫폼별 어셈블리를 생성하여 멀티 타기팅하려면 [How to Make Portable Class Libraries Work for You(이식 가능한 클래스 라이브러리가 작동하도록 설정하는 방법)](https://blogs.msdn.microsoft.com/dsplaisted/2012/08/27/how-to-make-portable-class-libraries-work-for-you/)에 설명된 대로 "Bait 및 스위치" PCL을 만들어야 합니다.

## <a name="retargeting-your-net-framework-code-to-net-framework-462"></a>.NET Framework 4.6.2로 .NET Framework 코드 대상 다시 지정

코드가 .NET Framework 4.6.2를 대상으로 하지 않는 경우 대상을 다시 지정하는 것이 좋습니다.  그러면 .NET 표준에서 기존 API를 지원할 수 없는 경우 최신 API를 대신 사용할 수 있습니다.

Visual Studio에서 이식하려는 각 프로젝트에 대해 다음을 수행합니다.

1. 프로젝트를 마우스 오른쪽 단추로 클릭하고 속성을 선택합니다.
2. "대상 프레임워크" 드롭다운에서 ".NET Framework 4.6.2"를 선택합니다.
3. 프로젝트를 다시 컴파일합니다.

됐습니다!  프로젝트가 .NET Framework 4.6.2를 대상으로 하기 때문에 해당 버전의 .NET Framework를 코드 이식의 기반으로 사용할 수 있습니다.

## <a name="determining-the-portability-of-your-code"></a>코드의 이식성 확인

다음 단계에서는 API Portability Analyzer(ApiPort)를 실행하여 분석을 시작할 수 있는 이식성 보고서를 생성합니다.

[API 이식성 도구(ApiPort)](https://github.com/Microsoft/dotnet-apiport/blob/master/docs/HowTo/)를 이해하고 .NET Core를 대상으로 하는 이식성 보고서를 생성할 수 있어야 합니다.  이 작업을 수행하는 방법은 요구 사항 및 개인적 취향에 따라 달라질 수 있습니다.  다음은 몇 가지 다양한 접근 방식입니다. 코드가 구성된 방법에 따라 각 접근 방식을 혼합하여 사용할 수 있습니다.

### <a name="dealing-primarily-with-the-compiler"></a>주로 컴파일러 처리

이 접근 방식은 작은 프로젝트 또는 많은 .NET Framework API를 사용하지 않는 프로젝트에 가장 적합할 수 있습니다.  이 접근 방식은 매우 간단합니다.

1. 필요에 따라 프로젝트에서 ApiPort를 실행합니다.
2. ApiPort가 실행된 경우 보고서를 신속하게 살펴봅니다.
3. 모든 코드를 새 .NET Core 프로젝트에 복사합니다.
4. 필요한 경우 이식성 보고서를 참조하여 컴파일될 때까지 컴파일러 오류를 해결합니다.
5. 필요에 따라 반복합니다.

이 접근 방식은 매우 구조적이지는 않지만 코드 중심 접근 방식을 사용하면 문제를 신속하게 해결할 수 있으므로 더 작은 프로젝트나 라이브러리에 가장 적합한 방법이 될 수 있습니다.  데이터 모델만 포함하는 프로젝트가 여기에 적합한 후보가 될 수 있습니다.

### <a name="staying-on-the-net-framework-until-portability-issues-are-resolved"></a>이식성 문제가 해결될 때까지 .NET Framework 유지

이 접근 방식은 전체 프로세스 중에 컴파일되는 코드를 원하는 경우 적합할 수 있습니다.  이 접근 방식은 다음과 같습니다.

1. 프로젝트에서 ApiPort를 실행합니다.
2. 이식 가능한 다른 API를 사용하여 문제를 해결합니다.
3. 직접적인 대안을 사용할 수 없는 영역을 기록해 둡니다.
4. 각 프로젝트를 .NET Core 프로젝트에 복사할 준비가 되었다고 확신할 때까지 이식하려는 모든 프로젝트에 대해 1-3단계를 반복합니다.
5. 새 .NET Core 프로젝트에 코드를 복사합니다.
6. 기록해 둔 문제를 해결합니다.

이 신중한 접근 방식은 단순히 컴파일러 오류를 해결하는 것보다는 구조적이지만 비교적 코드 중심적이며 항상 컴파일할 수 있는 코드가 있다는 장점이 있습니다.  다른 API를 사용하여 해결할 수 없는 특정 문제를 해결하는 방법은 현저하게 달라질 수 있습니다.  특정 프로젝트에 대해 보다 포괄적인 계획을 개발해야 할 수 있으며, 이는 다음 접근 방식에서 설명합니다.

### <a name="developing-a-comprehensive-plan-of-attack"></a>포괄적인 공격 계획 개발

이 접근 방식은 .NET Core를 지원하기 위해 코드를 재구성하거나 특정 영역을 다시 작성해야 할 수 있는 더 크고 더 복잡한 프로젝트에 가장 적합할 수 있습니다.  이 접근 방식은 다음과 같습니다.

1. 프로젝트에서 ApiPort를 실행합니다.
2. 코드에서 이식 불가능한 각 형식이 사용되고 있는 위치 및 해당 형식이 전체 이식성에 어떻게 영향을 주는지를 파악합니다.

   a.  해당 형식의 특성을 이해합니다.  수는 적은데 자주 사용되나요?  아니면 수는 많은데 자주 사용되지 않나요?  집중적으로 사용되나요? 아니면 코드 전체에 분산되어 있나요?
   
   b. 이식할 수 없는 코드는 격리가 쉬우므로 더 쉽게 처리할 수 있나요?
   
   c. 코드를 리팩터링해야 하나요?
   
   d. 이식할 수 없는 해당 형식에 대해 동일한 작업을 수행하는 다른 API가 있나요?  예를 들어 `WebClient` 클래스를 사용하고 있는 경우 `HttpClient` 클래스를 대신 사용할 수 있습니다.
   
   e. 드롭인 대체가 아닌 경우에도 작업을 수행하는 데 사용할 수 있는 이식 가능한 다른 API가 있나요?  예를 들어 `XmlSchema`를 사용하여 XML의 구문을 분석하는데 XML 스키마 검색이 필요하지 않은 경우 `System.Linq.Xml` API를 사용하고 데이터를 직접 구문 분석할 수 있습니다.

3. 이식하기 어려운 어셈블리가 있는 경우 지금은 .NET Framework에 유지하는 것이 나을까요?  다음과 같은 몇 가지 사항을 고려해야 합니다.

   a. .NET Framework 또는 Windows 관련 기능을 너무 많이 사용하기 때문에 .NET Core와 호환되지 않는 일부 기능이 라이브러리에 있을 수 있습니다.  지금은 해당 기능을 유지하고 당분간은 기능이 더 적은 라이브러리의 .NET Core 버전을 릴리스하는 것이 더 나을까요?
   
   b. 여기에서 리팩터링이 도움이 될까요?
   
4. 사용할 수 없는 .NET Framework API의 고유한 구현을 작성하는 것이 합리적일까요?

   대신 [.NET Framework 참조 소스](https://github.com/Microsoft/referencesource)에서 코드를 복사, 수정 및 사용하는 것이 좋을 수 있습니다.  이 코드는 [MIT 라이선스](https://github.com/Microsoft/referencesource/blob/master/LICENSE.txt)에 따라 사용이 허가되므로 매우 자유롭게 이 작업을 수행할 수 있습니다.  코드에서 Microsoft 특성을 제대로 지정하기만 하면 됩니다.
   
5. 필요에 따라 다른 프로젝트에 대해 이 프로세스를 반복합니다.
6. 계획을 설정했으면 해당 계획을 실행합니다.
 
분석 단계는 코드베이스의 크기에 따라 다소 시간이 걸릴 수 있습니다.  이 단계에서 시간을 할애하여 필요한 변경의 범위를 철저하게 이해하고 계획을 개발하면 장기 실행, 특히 더 복잡한 코드베이스가 있는 경우 많은 시간을 절약할 수 있습니다.

계획에 코드베이스의 중요한 변경 작업을 포함하는 동시에 .NET Framework 4.6.2를 계속 대상으로 지정할 수 있으므로 이전 접근 방식보다 더 구조화된 버전으로 만들 수 있습니다.  계획 실행을 시작하는 방법은 코드베이스에 따라 달라집니다.

### <a name="mixing-approaches"></a>접근 방식 혼합

위의 접근 방식을 프로젝트 단위로 혼합하여 사용할 수 있습니다.  사용자 및 코드베이스에 가장 적합한 방법을 수행해야 합니다.

## <a name="porting-your-tests"></a>테스트 이식

코드를 이식한 경우 모든 항목이 제대로 작동하는지 확인하는 가장 좋은 방법은 .NET Core에 이식할 때 코드를 테스트하는 것입니다.  이렇게 하려면 .NET Core에 대한 테스트를 빌드하고 실행하는 테스트 프레임워크를 사용해야 합니다.  현재는 다음과 같은 세 가지 옵션이 있습니다.

* [xUnit](https://xunit.github.io/)
   - [시작](http://xunit.github.io/docs/getting-started-dnx.html)
   - [MSTest 프로젝트를 xUnit으로 변환하는 도구](https://github.com/dotnet/codeformatter/tree/master/src/XUnitConverter)
* [NUnit](http://www.nunit.org/)
  - [시작](https://github.com/nunit/docs/wiki/Installation)
  - [MSTest에서 NUnit으로 마이그레이션에 대한 블로그 게시물](http://www.florian-rappl.de/News/Page/275/convert-mstest-to-nunit)
* [MSTest](https://msdn.microsoft.com/library/ms243147.aspx)

## <a name="recommended-approach-to-porting"></a>이식에 권장되는 접근 방식

마지막으로 코드 자체를 이식합니다.  궁극적으로 실제 이식 작업은 .NET Framework 코드가 구성된 방법에 따라 현저하게 달라집니다.  그러나 다음은 코드베이스에서 제대로 작동할 수 있는 권장 접근 방식입니다.

코드를 이식하려면 라이브러리의 "기본"으로 시작하는 것이 좋습니다.  이는 다른 모든 항목에서 직접적으로나 간접적으로 사용하는 데이터 모델이나 일부 다른 기본 클래스 및 메서드가 될 수 있습니다.

1. 현재 이식하고 있는 라이브러리의 계층을 테스트하는 테스트 프로젝트를 이식합니다.
2. 라이브러리의 "기본"을 새 .NET Core 프로젝트에 복사하고 지원하려는 .NET 표준의 버전을 선택합니다.
3. 코드를 컴파일하는 데 필요한 대로 내용을 변경합니다.  이 작업의 많은 부분에서 NuGet 패키지 종속성을 `project.json` 파일에 추가해야 할 수 있습니다.
4. 테스트를 실행하고 필요에 따라 조정합니다.
5. 이식할 다음 코드 계층을 선택하고 2단계와 3단계를 반복합니다.

기본 라이브러리의 바깥쪽으로 조직적으로 이동하고 필요에 따라 각 계층을 테스트하면 이식은 한 번에 하나의 코드 계층으로 문제가 격리되는 체계적인 프로세스가 됩니다.



<!--HONumber=Nov16_HO1-->


