---
title: .NET Core
description: .NET Core
keywords: .NET, .NET Core
author: richlander
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: f2b312cb-f80c-4b0d-9101-93908f06a6fa
translationtype: Human Translation
ms.sourcegitcommit: 8ada0e4c57febd4068be69330e7f9633517ce7ff
ms.openlocfilehash: 8b6b38aa7f59002dcfbb229ce6522df67b611028

---

# <a name="net-core"></a>.NET Core

> 간단한 .NET Core 응용 프로그램을 만드는 방법을 알아보려면 ["시작" 자습서](getting-started.md)를 확인하세요. 첫 번째 앱을 만들고 실행하는 데 몇 분밖에 걸리지 않습니다.

.NET Core는 Microsoft 및 [GitHub](https://github.com/dotnet/core)의 .NET 커뮤니티에서 유지 관리하는 범용 개발 플랫폼입니다. 플랫폼 간으로 Windows, macOS 및 Linux를 지원하며 장치, 클라우드 및 포함/IoT 시나리오에서 사용할 수 있습니다. 

다음은 .NET Core를 가장 잘 정의하는 특징입니다.

- **유연한 배포:** 앱이나 설치된 side-by-side사용자 또는 시스템 수준에 포함할 수 있습니다.
- **플랫폼 간:** Windows, macOS 및 Linux에서 실행되며 다른 OS에 이식할 수 있습니다. [지원되는 OS(운영 체제)](https://github.com/dotnet/core/blob/master/roadmap.md), CPU 및 응용 프로그램 시나리오는 시간에 따라 증가하며 Microsoft, 다른 회사 및 개인이 제공합니다.
- **명령줄 도구:** 모든 제품 시나리오를 명령줄에서 실행할 수 있습니다. 
- **호환:** .NET Core는 [.NET 표준 라이브러리](../standard/library.md)를 통해 .NET Framework, Xamarin 및 Mono와 호환됩니다.
- **오픈 소스:** .NET Core 플랫폼은 MIT 및 Apache 2 라이선스를 사용하는 오픈 소스입니다. 설명서는 [CC-BY](http://creativecommons.org/licenses/by/4.0/)에 따라 사용이 허가됩니다. .NET Core는 [.NET Foundation](http://www.dotnetfoundation.org/) 프로젝트입니다.
- **Microsoft에서 지원:** .NET Core는 [.NET Core 지원](https://www.microsoft.com/net/core/support/)에 따라 Microsoft에서 지원됩니다.

## <a name="composition"></a>컴퍼지션

.NET Core는 다음과 같은 부분으로 구성됩니다.

- [.NET 런타임](https://github.com/dotnet/coreclr) - 형식 시스템, 어셈블리 로드, 가비지 수집기, 네이티브 interop 및 기타 기본 서비스를 제공합니다. 
- [프레임워크 라이브러리](https://github.com/dotnet/corefx) 집합 - 기본 데이터 형식, 앱 구성 형식 및 기본 유틸리티를 제공합니다. 
- [SDK 도구 집합](https://github.com/dotnet/cli) 및 [언어 컴파일러](https://github.com/dotnet/roslyn) - [.NET Core SDK](sdk.md)에서 사용할 수 있는 기본 개발자 환경을 사용할 수 있도록 합니다.
- 'dotnet' 앱 호스트 - .NET Core 앱을 시작하는 데 사용됩니다. 이 호스트는 런타임을 선택하고 런타임을 호스트하며 어셈블리 로드 정책을 제공하고 앱을 시작합니다. 동일한 호스트를 사용하여 거의 동일한 방식으로 SDK 도구를 시작할 수도 있습니다.

### <a name="languages"></a>언어

C# 및 F# 언어(Visual Basic은 곧 제공 예정)를 사용하여 .NET Core에 대한 응용 프로그램 및 라이브러리를 작성할 수 있습니다. 컴파일러는 .NET Core에서 실행되며, 실행되는 모든 위치에서 .NET Core를 개발할 수 있습니다. 일반적으로 컴파일러는 직접 사용하지 않고 SDK 도구를 통해 간접적으로 사용합니다.

C# 및 F# 컴파일러와 .NET Core 도구는 Visual Studio, [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp), Sublime Text 및 Vim을 포함한 여러 텍스트 편집기 및 IDE에 통합되어 즐겨찾는 코딩 환경 및 OS에서 .NET Core 개발을 옵션으로 만들 수 있습니다. 이 통합은 [OmniSharp 프로젝트](http://www.omnisharp.net/)의 적합한 담당자에 의해 부분적으로 제공됩니다.

### <a name="net-apis-and-compatibility"></a>.NET API 및 호환성

.NET Core는 .NET Framework BCL(기본 클래스 라이브러리) 계층에서 .NET Framework의 플랫폼 간 버전이라고 할 수 있으며, [.NET 표준 라이브러리](../standard/library.md) 사양을 구현합니다. .NET Core는 .NET Framework 또는 Mono/Xamarin에서 사용할 수 있는 API의 하위 집합을 제공합니다. 경우에 따라 형식이 완전히 구현되지 않으며, 일부 멤버는 사용할 수 없거나 이동되었습니다.

.NET Core API 로드맵에 대한 자세한 내용은 [.NET Core roadmap(.NET Core 로드맵)](https://github.com/dotnet/core/blob/master/roadmap.md)을 참조하세요.

### <a name="relationship-to-the-net-standard-library"></a>.NET 표준 라이브러리와의 관계

[.NET 표준 라이브러리](../standard/library.md)는 개발자가 각 .NET 구현에서 예상할 수 있는 일관된 .NET API 집합을 설명하는 API 사양입니다. .NET 표준 라이브러리를 규격으로 간주하고 .NET 표준 라이브러리를 대상으로 하는 라이브러리를 지원하려면 .NET 구현에서 이 사양을 구현해야 합니다. 

.NET Core는 .NET 표준 라이브러리를 구현하므로 .NET 표준 라이브러리를 지원합니다.

### <a name="workloads"></a>작업

.NET Core는 자체적으로 도구, 로컬 서비스 및 텍스트 기반 게임에 유용한 단일 응용 프로그램 모델 즉, 콘솔 앱을 포함합니다. 기능을 확장하기 위해 다음과 같은 추가 응용 프로그램 모델이 .NET Core를 기반으로 구축되었습니다.

- [ASP.NET Core](http://asp.net)
- [Windows 10 UWP(유니버설 Windows 플랫폼)](https://developer.microsoft.com/windows)
- [Xamarin.Forms](https://www.xamarin.com/forms)

### <a name="open-source"></a>오픈 소스

[.NET Core](https://github.com/dotnet/core)는 오픈 소스(MIT 라이선스)이며 2014년 Microsoft에 의해 [.NET Foundation](http://dotnetfoundation.org)에 제공되었습니다. 이제 가장 많이 사용되는 .NET Foundation 프로젝트 중의 하나입니다. 이 프로젝트는 개인, 교육 또는 상업용으로 개인 및 회사에서 자유롭게 채택할 수 있습니다. 여러 회사에서 앱, 도구, 새 플랫폼 및 호스팅 서비스의 일부로 .NET Core를 사용합니다. 이러한 회사 중 일부는 GitHub에서 .NET Core에 대한 중요한 정보를 제공하고 [.NET Foundation Technical Steering Group(.NET Foundation 기술 방향 설정 그룹)](http://www.dotnetfoundation.org/blog/tsg-welcome)의 일부로 제품 판매에 대한 지침을 제공합니다.

## <a name="acquisition"></a>취득

.NET Core는 NuGet.org의 패키지 및 독립 실행형 배포라는 두 가지 주요 방식으로 배포됩니다.

### <a name="distributions"></a>분포

.NET Core는 [.NET Core Getting Started(.NET Core 시작)](https://www.microsoft.com/net/core) 페이지에서 다운로드할 수 있습니다.

- *Microsoft .NET Core* 배포에는 CoreCLR 런타임, 관련 라이브러리, 콘솔 응용 프로그램 호스트 및 `dotnet` 앱 시작 관리자가 포함됩니다. 이 배포는 [`Microsoft.NETCore.App`](https://www.nuget.org/packages/Microsoft.NETCore.App) 메타패키지에서 설명합니다.
- *Microsoft .NET Core SDK* 배포에는 .NET Core와 NuGet 패키지를 복원하고 앱을 컴파일 및 빌드하는 도구 집합이 포함됩니다. 

일반적으로 .NET Core 개발을 시작하려면 먼저 .NET Core SDK를 설치합니다. 추가 .NET Core(아마도 시험판) 빌드를 설치할 수 있습니다.

### <a name="packages"></a>패키지

- [.NET Core 패키지](packages.md)에는 .NET Core 런타임 및 라이브러리(참조 어셈블리 및 구현)이 포함됩니다. 예를 들어 [System.Net.Http](https://www.nuget.org/packages/System.Net.Http/)가 있습니다.
- [.NET Core 메타패키지](packages.md)에서는 버전이 있는 적합한 라이브러리 패키지 집합을 참조하여 다양한 계층 및 앱 모델에 대해 설명합니다.

## <a name="architecture"></a>아키텍처

.NET Core는 플랫폼 간 .NET 구현입니다. .NET Core의 고유한 기본 아키텍처 문제는 .Net Core에서 지원되는 플랫폼에 플랫폼별 구현을 제공하는 것과 관련이 있습니다.

### <a name="environments"></a>환경

.NET Core는 Microsoft에 의해 Windows, macOS 및 Linux에서 지원됩니다. Linux의 경우 Microsoft는 주로 RHEL(Red Hat Enterprise Linux) 및 Debian 배포 제품군에서 .NET Core 실행을 지원합니다.

.NET Core는 현재 X64 CPU를 지원합니다. Windows에서는 X86도 지원됩니다. ARM64 및 ARM32는 진행 중입니다.

[.NET Core Roadmap(.NET Core 로드맵)](https://github.com/dotnet/core/blob/master/roadmap.md)에서는 워크로드 및 OS와 CPU 환경 지원 및 계획에 대한 자세한 정보를 제공합니다.

다른 회사 또는 그룹에서 다른 앱 형식 및 환경의 .NET Core를 지원할 수 있습니다.

### <a name="designed-for-adaptability"></a>뛰어난 적응성

.NET Core는 다른 .NET 제품에 비해 매우 유사하지만 고유한 제품으로 구축되었습니다. .NET Core는 새 플랫폼, 새 워크로드 및 새로운 컴파일러 도구 체인에서 광범위하게 적응되도록 설계되었습니다. 여러 개의 OS 및 CPU 포트가 진행 중이며 훨씬 더 많은 포트로 이식할 수 있습니다. 예를 들어 [LLVM](http://llvm.org/) 컴파일러를 통한 .NET Core 네이티브 컴파일의 초기 프로토타입인 [LLILC](https://github.com/dotnet/llilc) 프로젝트가 있습니다.

제품은 여러 조각으로 구분되어 다양한 부품을 서로 다른 일정으로 새 플랫폼에 적용할 수 있습니다. 런타임 및 플랫폼별 기본 라이브러리는 하나의 단위로 이식해야 합니다. 플랫폼 제약 없는 라이브러리는 구성에 따라 모든 플랫폼에서 있는 그대로 작동합니다. 플랫폼별 구현을 줄여 개발자 효율성을 향상시키거나, 알고리즘이나 API를 전체 또는 일부로 구현할 수 있을 때마다 플랫폼 중립적인 C# 코드를 선호한다는 등 프로젝트에 대한 편견이 있습니다.

일반적으로 사용자는 여러 운영 체제를 지원하기 위해 .NET Core를 구현하는 방법을 질문합니다. 또한 별도의 구현이 있는지 또는 [조건부 컴파일](https://en.wikipedia.org/wiki/Conditional_compilation)이 사용되는지에 대해서도 질문합니다. 둘 다 조건부 컴파일에 대한 강력한 편견이 있습니다.

대부분의 [CoreFX](https://github.com/dotnet/corefx)가 모든 플랫폼에서 공유되는 플랫폼 중립 코드임을 다음 차트에서 확인할 수 있습니다. 플랫폼 중립 코드는 모든 플랫폼에서 사용할 수 있는 이식 가능한 단일 어셈블리로 구현할 수 있습니다.

![CoreFX: 플랫폼별 코드 줄](../images/corefx-platforms-loc.png)

Windows 및 Unix 구현은 크기가 비슷합니다. CoreFX는 [Microsoft.Win32.Registry](https://github.com/dotnet/corefx/tree/master/src/Microsoft.Win32.Registry)와 같은 일부 Windows 전용 기능을 구현하고 Unix 전용 개념은 구현하지 않기 때문에 Windows 구현이 더 큽니다. 또한 대부분의 Linux 및 macOS 구현이 Unix 구현에서 공유되는데 Linux 및 macOS 관련 구현의 크기가 거의 비슷함을 확인할 수 있습니다.


.NET Core에는 플랫폼별 라이브러리와 플랫폼 중립 라이브러리가 혼합되어 있습니다. 몇 가지 예제에서 패턴을 확인할 수 있습니다.

- [CoreCLR](https://github.com/dotnet/coreclr)는 플랫폼별이며, C/C++에서 빌드되어 플랫폼별로 구성됩니다.
- [System.IO](https://github.com/dotnet/corefx/tree/master/src/System.IO) 및 [System.Security.Cryptography.Algorithms](https://github.com/dotnet/corefx/tree/master/src/System.Security.Cryptography.Algorithms)는 저장소 및 암호화 API가 각 OS에서 현저하게 다르기 때문에 플랫폼별입니다. 
- [System.Collections](https://github.com/dotnet/corefx/tree/master/src/System.Collections) 및 [System.Linq](https://github.com/dotnet/corefx/tree/master/src/System.Linq)는 데이터 구조를 통해 만들고 작동하기 때문에 플랫폼 중립입니다.

## <a name="comparisons-to-other-net-platforms"></a>다른 .NET 플랫폼과 비교

기존 .NET 플랫폼과 비교하여 .NET Core의 크기 및 모양을 이해하는 것이 가장 쉽습니다. 

### <a name="comparison-with-net-framework"></a>.NET Framework와 비교

.NET 플랫폼은 Microsoft가 2000년에 처음 발표한 후 발전해 왔습니다. .NET Framework는 Microsoft가 15년 이상 동안 생성한 기본 .NET 제품이었습니다. 

.NET Core와 .NET Framework 간의 주요 차이점은 다음과 같습니다. 

- **앱 모델** -- 많은 .NET Framework 앱 모델이 WPF(DirectX를 기반으로 구축됨)와 같은 Windows 기술을 기반으로 구축되었기 때문에 .NET Core는 이러한 앱 모델을 모두 지원하지는 않습니다. 콘솔 및 ASP.NET Core 앱 모델은 .NET Core와 .NET Framework 둘 다에서 지원됩니다. 
- **API** -- .NET Core는 .NET Framework와 대부분 동일하지만 더 적은 API를 포함하며 다른 팩터링(어셈블리 이름이 다르고 형식 모양이 주요 사례에서 다름)을 사용합니다. 일반적으로 이러한 차이로 인해 현재 포트 소스를 .NET Core로 변경해야 합니다. .NET Core는 시간에 따라 더 많은 .NET Framework BCL API를 포함하도록 확장되는 [.NET 표준 라이브러리](../standard/library.md) API를 구현합니다.
- **하위 시스템** -- .Net Core는 더 간단한 구현 및 프로그래밍 모델 구축을 위해 .NET Framework에 하위 시스템의 하위 집합을 구현합니다. 예를 들어 CAS(코드 액세스 보안)는 지원되지 않지만 리플렉션은 지원됩니다.
- **플랫폼** -- .NET Framework는 Windows와 Windows Server를 지원하는 반면 .NET Core는 macOS 및 Linux도 지원합니다.
- **오픈 소스** -- .NET Core는 오픈 소스이며, [.NET Framework의 읽기 전용 하위 집합](https://github.com/microsoft/referencesource)은 오픈 소스입니다.

.NET Core는 고유하고 .NET Framework 및 기타 .NET 플랫폼과 현저한 차이가 있지만 소스 또는 이진 공유 기술을 사용하여 간단하게 코드를 공유할 수 있습니다. 

### <a name="comparison-with-mono"></a>Mono와 비교

[Mono](http://www.mono-project.com/)는 원래 플랫폼 간 [오픈 소스](https://github.com/mono/mono) .NET 구현으로, 2004년에 처음 제공되었습니다. .NET Framework의 커뮤니티 복제본으로 생각할 수 있습니다. Mono 프로젝트 팀은 호환되는 구현을 제공하기 위해 Microsoft에서 게시한 오픈 [.NET 표준](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/dotnet-standards.md)(특히 ECMA 335)을 사용했습니다.

.NET Core와 Mono 간의 주요 차이점은 다음과 같습니다.

- **앱 모델** -- Mono는 Xamarin 제품을 통해 .NET Framework 앱 모델의 하위 집합(예: Windows Forms) 및 몇 가지 추가 모델(예: [Xamarin.iOS](https://www.xamarin.com/platform))을 지원합니다. .NET Core는 이러한 모델을 지원하지 않습니다.
- **API** -- Mono는 .NET Framework API의 [큰 하위 집합](http://docs.go-mono.com/?link=root%3a%2fclasslib)을 지원하며, 동일한 어셈블리 이름 및 팩터링을 사용합니다.
- **플랫폼** -- Mono는 여러 플랫폼 및 CPU를 지원합니다.
- **오픈 소스** -- Mono와 .NET Core 둘 다 MIT 라이선스를 사용하며 .NET Foundation 프로젝트입니다.
- **포커스** -- 최근 몇 년 간 Mono의 주요 포커스는 모바일 플랫폼인 반면 .NET Core의 포커스는 클라우드 워크로드입니다.



<!--HONumber=Nov16_HO3-->


