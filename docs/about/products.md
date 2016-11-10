---
title: ".NET 제품"
description: ".NET 제품"
keywords: .NET, .NET Core
author: richlander
manager: wpickett
ms.date: 06/23/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 2e38e9d9-8284-46ee-a15f-199adc4f26f4
translationtype: Human Translation
ms.sourcegitcommit: 15c55a87beb64f265a164db918c7721c7690fadf
ms.openlocfilehash: 90deb1903eea6ae1336326ca91f1e7863485dfd1

---

# <a name="net-products"></a>.NET 제품

.NET은 개발자 제품을 빌드하기 위한 매우 유연하고 기본적으로 플랫폼 간인 범용 [사양](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/dotnet-standards.md)입니다. 데스크톱, 모바일, 클라우드, 게임, IoT 등 가장 인기 있는 앱 범주에 모두 사용됩니다.

이 문서에서 사용되는 다음 두 용어에는 미묘한 차이가 있습니다.

- ".NET 제품" - 하나 이상의 대상 플랫폼용 앱을 빌드할 수 있습니다.
- ".NET 구현" - 제품의 기반이 되는 ".NET 코드"를 실행할 수 있는 런타임, 프레임워크 및 도구의 일부 조합입니다.

## <a name="product-categories"></a>제품 범주

.NET 제품은 다음 제품 범주에 각각 사용할 수 있습니다.

### <a name="desktop"></a>바탕 화면

Windows 및 macOS용 데스크톱 앱을 빌드할 수 있습니다.

- [유니버설 Windows 앱](https://developer.microsoft.com/windows) 및 [.NET 네이티브](#net-native)
- Windows용 [WPF(Windows Presentation Foundation)](https://msdn.microsoft.com/library/ms754130.aspx) 및 [.NET Framework](#net-framework)
- Windows용 [Windows Forms](https://msdn.microsoft.com/library/dd30h2yb.aspx) 및 [.NET Framework](#net-framework)
- macOS용 Cocoa 및 [Xamarin](#xamarin-sdk)
- 플랫폼 간 데스크톱용 [Electron](http://electron.atom.io/) 및 [electron-edge](https://github.com/kexplo/electron-edge)

### <a name="games"></a>게임

여러 데스크톱, 모바일, 콘솔 및 가상/증강 현실 장치용 게임을 빌드할 수 있습니다.

- [CRYENGINE](http://docs.cryengine.com/display/CEPROG/CE%23+Programming) 및 [Mono](#mono)
- [MonoGame](http://www.monogame.net/documentation/?page=main) 및 [Mono](#mono)
- [Unity](http://docs.unity3d.com/Manual/index.html) 및 [Mono](#mono)

### <a name="iot"></a>IoT

Raspberry Pi 2/3을 포함하여 Windows 10 IoT Core용 IoT 앱을 빌드할 수 있습니다.

- [Windows 10 IoT Core](https://developer.microsoft.com/windows/iot) 및 [.NET 네이티브](#net-native)

### <a name="mobile"></a>휴대폰

iOS, Android 및 Windows용 모바일 앱을 빌드할 수 있습니다.

- iOS 앱 및 [Xamarin](#xamarin-sdk)
- Android 앱 및 [Xamarin](#xamarin-sdk)
- [유니버설 Windows 앱](https://developer.microsoft.com/windows) 및 [.NET 네이티브](#net-native)

### <a name="web-and-cloud"></a>웹 및 클라우드

Windows와 Linux용 웹 및 클라우드 앱을 빌드할 수 있습니다.

- Windows용 [ASP.NET](http://www.asp.net/) 및 [.NET Framework](#net-framework)
- Windows, macOS, Linux용 [ASP.NET Core](http://docs.asp.net/) 및 [.NET Core](#net-core)

## <a name="net-implementations"></a>.NET 구현

아래에는 주요 상용 및 오픈 소스 .NET 구현이 사전순으로 나와 있습니다.

### <a name="net-core"></a>.NET Core

.NET Core는 장치, 웹, 클라우드 및 포함/IoT 앱을 빌드하는 데 사용됩니다. [오픈 소스](https://github.com/dotnet/core) 및 플랫폼 간이며 Windows, macOS, Linux를 지원합니다. [ASP.NET Core](http://docs.asp.net/)는 가장 인기 있는 .NET Core 작업입니다. 온-프레미스 및 클라우드 배포에 대한 웹앱과 서비스를 빌드하는 데 사용할 수 있습니다. 또한 .NET Core를 사용하여 도구, 유틸리티 및 클라우드 작업자 앱을 빌드할 수 있습니다.

- [.NET Core](../core/index.md)에 대한 자세한 정보
- [ASP.NET Core](http://docs.asp.net/)에 대한 자세한 정보
- [.NET Core 다운로드](http://dot.net/core)

다음은 .NET Core의 주요 특성입니다.

**플랫폼 간** - .NET Core는 Linux, Windows 및 macOS의 세 가지 운영 체제 제품군을 지원합니다. .NET Core 앱은 기본적으로 플랫폼 간입니다. 지원되는 OS 간에 수정하지 않고 실행되는 앱과 라이브러리를 작성할 수 있습니다.

GitHub에서 [MIT](https://github.com/dotnet/coreclr/blob/master/LICENSE.TXT) 및 [Apache 2](https://github.com/dotnet/roslyn/blob/master/License.txt) 라이선스로 **오픈 소스** - [.NET Core](https://github.com/dotnet/core)를 사용할 수 있습니다(라이선스는 구성 요소별로 허가됨). 설명서는 [CC-BY](https://github.com/dotnet/docs/blob/master/license.txt)입니다. .NET Core는 [.NET Core 릴리스 정보](https://github.com/dotnet/core/releases)에 나열된 중요한 오픈 소스 산업 종속성 집합을 활용합니다. 

**자연 취득** - NET Core는 특정 개발자 요구에 따라 여러 형식으로 배포됩니다. [.NET Core SDK](https://dot.net/core) 설치 관리자(또는 zip) 또는 OS 특정 패키지 관리자(예: APT 및 Yum)를 통해 .NET Core를 취득할 수 있습니다. Docker 허브에서 [공식 .NET Core Docker 이미지](https://hub.docker.com/r/microsoft/dotnet/)를 사용할 수 있습니다. [NuGet](http://www.nuget.org/)에서 상위 수준 프레임워크 라이브러리 및 대규모 .NET 라이브러리 에코시스템을 사용할 수 있습니다. 

**모듈식 플랫폼** - .NET Core는 모듈식 디자인으로 빌드되었으므로 필요한 .NET Core 라이브러리와 종속성만 응용 프로그램에 포함할 수 있습니다. 각 응용 프로그램에서 자체 .NET Core 버전 관리를 선택하여 공유 구성 요소와 충돌하지 않도록 합니다. 이 방법은 Docker 등의 컨테이너 기술을 사용하여 소프트웨어를 개발하는 추세에 부합됩니다.

### <a name="net-framework"></a>.NET Framework

.NET Framework는 Windows 및 Windows Server용 앱을 빌드하는 데 사용됩니다. .NET Framework를 사용하여 WPF(Windows Presentation Framework) 및 Windows Forms와의 풍부한 사용자 인터페이스를 빌드할 수 있습니다. ASP.NET Web Forms, ASP.NET MVC, WCF(Windows Communication Framework)를 사용한 서버 앱 빌드도 지원합니다. Visual Studio에서는 .NET Framework용 풍부한 디자이너 환경을 제공하여 쉽게 클라이언트 및 서버 앱을 빌드할 수 있게 합니다. Windows용 앱 작성에 가장 적합한 선택 항목입니다.

- [.NET Framework](https://msdn.microsoft.com/library/w0x726c2.aspx)에 대한 자세한 정보
- [.NET Framework 다운로드](https://dot.net)

[Windows Forms](https://msdn.microsoft.com/library/dd30h2yb.aspx)를 사용하면 다른 기술보다 신속하게 "데이터 폼" 데스크톱 UI를 빌드할 수 있습니다. UI 및 비-UI 컨트롤의 끌어서 놓기를 지원하는 디자이너를 사용하여 대부분의 개발 작업을 하나의 제스처 및 개념적 모델로 줄입니다.

[WPF(Windows Presentation Foundation)](https://msdn.microsoft.com/library/ms754130.aspx)에서는 [XAML](https://msdn.microsoft.com/library/ms752059.aspx) 태그 언어로 UI를 설명하여 코드와 UI 문제를 구분합니다. WPF는 매우 유연하며 더 복잡한 사용자 모델이나 더 세련된 모양이 필요한 UI에 자주 사용됩니다.

[WCF(Windows Communication Foundation)](https://msdn.microsoft.com/library/ms731082.aspx)는 SOAP 웹 서비스용 라이브러리 집합입니다. 다양한 데이터 형식을 사용하여 지원되는 여러 프로토콜을 통해 통신할 수 있고 선택한 모든 프로세스에서 호스트될 수 있는 서비스를 만들 수 있습니다. 서비스가 특정 호스팅 전략이나 방법에 연결되지 않는 WCF의 주요 특징 중 하나는 여기에서 비롯됩니다.

[ASP.NET](http://www.asp.net/)은 웹 프레임워크입니다. 최신 고성능 웹 응용 프로그램을 생성하는 데 사용되는 여러 고유한 부분으로 구성됩니다. 

- [ASP.NET Web Forms](http://www.asp.net/web-forms)를 사용하면 웹 컨트롤의 끌어서 놓기를 지원하는 디자이너를 통해 다른 대부분의 웹 기술보다 신속하게 "데이터 폼" UI를 빌드할 수 있습니다. 
- [ASP.NET MVC](http://www.asp.net/mvc)를 사용하면 HTTP 계층에서 사용자 인터페이스에 이르기까지 전체 웹 파이프라인에 대한 제어가 강화됩니다. 
- [ASP.NET WebAPI](http://www.asp.net/web-api)는 REST 서비스를 만들기 위한 규칙 기반 프레임워크입니다. 
- [SignalR](http://www.asp.net/signalr)을 사용하면 [WebSocket](https://en.wikipedia.org/wiki/WebSocket) 프로토콜을 통해 웹 응용 프로그램에 푸시 기반 통신을 제공할 수 있습니다.

### <a name="net-native"></a>.NET 네이티브

.NET 네이티브는 .NET Core용 네이티브 빌드 도구 집합입니다. .NET 네이티브는 IL 바이트 코드를 네이티브 기계어 코드로 컴파일하여 네이티브 응용 프로그램을 생성하는 AOT(Ahead-of-Time) 도구 체인입니다. 따라서 결과 이진 파일이 OS에서 실행되며 JIT 처리와 런타임 컴파일이 없습니다. 이 때문에 일부 보안상의 장점뿐만 아니라 시작 성능이 향상됩니다.

.NET 네이티브는 .NET [UWP(유니버설 Windows 플랫폼)](https://msdn.microsoft.com/library/windows/apps/dn726767.aspx) 응용 프로그램에서 주로 사용됩니다.

### <a name="mono"></a>Mono

[Mono](http://www.mono-project.com/docs/about-mono/)는 [Mono 프로젝트](http://mono-project.com) 커뮤니티에서 생성된 .NET의 최초 오픈 소스 플랫폼 간 구현입니다. 현재 Microsoft의 후원을 받고 있습니다. .NET Framework의 개방형 플랫폼 간 버전으로 간주될 수 있습니다. 해당 API는 .NET Core가 아니라 .NET Framework의 진행 상황을 따릅니다.

Mono를 구성하는 다음 몇 가지 구성 요소가 있습니다.

**C# 컴파일러** - Mono C# 컴파일러는 C# 6용 전체 기능입니다.

**Mono 런타임** - 런타임에서 ECMA CLI(공용 언어 인프라)를 구현합니다. 런타임에서 JIT(Just-in-Time) 컴파일러, AOT(Ahead-of-Time) 컴파일러, 라이브러리 로더, 가비지 수집기, 스레딩 시스템 및 상호 운용성 기능을 제공합니다.

**기본 클래스 라이브러리** - Mono 플랫폼은 응용 프로그램을 빌드할 견고한 토대를 제공하는 포괄적인 클래스 집합을 제공합니다. 이러한 클래스는 Microsoft .Net Framework 클래스와 호환됩니다.

**Mono 클래스 라이브러리** - Mono에서도 .NET Framework에서 제공하는 기본 클래스 라이브러리 외에 많은 클래스를 제공합니다. 이러한 클래스는 Linux 응용 프로그램 빌드에 특히 유용한 추가 기능을 제공합니다. 몇 가지 예로 Gtk+, Zip 파일, LDAP, OpenGL, Cairo, POSIX 등의 클래스가 있습니다.

### <a name="xamarin-sdk"></a>Xamarin SDK

[Xamarin SDK](http://open.xamarin.com)는 Apple 및 Google 에코시스템용 네이티브 모바일 및 장치 앱을 빌드하는 데 사용됩니다. Mono를 기반으로 하며 MIT 라이선스를 사용하는 오픈 소스입니다. 휴대폰, 태블릿, 시계용 iOS 및 Android 앱을 빌드하는 데 사용할 수 있습니다. [Xamarin.Forms](https://www.xamarin.com/forms)는 Apple, Google 및 Windows 앱에서 다시 사용할 수 있는 UI를 작성하는 데 많이 사용되는 방법입니다.

- [Xamarin SDK](https://developer.xamarin.com/)에 대한 자세한 정보
- [Xamarin 다운로드](https://www.xamarin.com/platform)



<!--HONumber=Nov16_HO1-->


