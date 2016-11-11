---
title: ".NET Core 필수 조건"
description: ".NET Core 필수 조건"
keywords: .NET, .NET Core
author: billwagner
manager: wpickett
ms.date: 09/15/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: c33b1241-ab66-4583-9eba-52cf51146f5a
translationtype: Human Translation
ms.sourcegitcommit: 130b94a745b0e3222e205d8af26194239130ec9c
ms.openlocfilehash: 04018ec65272745ef98a2a96eb30009bcf44cb2e

---

# <a name="prerequisites-for-windows-development"></a>Windows 개발을 위한 필수 조건

Visual Studio를 사용하여 Windows에서 .NET Core로 개발하려면 다음이 필요합니다.

* 지원되는 Windows 클라이언트 또는 운영 체제 버전
* Visual Studio 2015 업데이트 3.3 이상
* Visual Studio용 NuGet Manager 확장
* .NET Core Tooling Preview 2

## <a name="supported-windows-versions"></a>지원되는 Windows 버전

.NET Core는 다음 Windows 버전에서 지원됩니다.

* Windows 7 SP1
* Windows 8.1
* Windows 10
* Windows Server 2008 R2 SP1(전체 서버 또는 Server Core)
* Windows Server 2012 SP1(전체 서버 또는 Server Core)
* Windows Server 2012 R2 SP1(전체 서버 또는 Server Core)
* Windows Server 2016(전체 서버, Server Core 또는 Nano 서버)

[.NET Core 릴리스 정보](https://github.com/dotnet/core/blob/master/release-notes/1.0/1.0.0.md)에서 [지원되는 운영 체제](https://github.com/dotnet/core/blob/master/release-notes/1.0/1.0.0.md#rtm-platform-support)의 전체 집합을 볼 수 있습니다.

## <a name="net-core-dependencies"></a>.NET Core 종속성

.NET Core를 Windows에서 실행하려면 VC++ 재배포 가능 패키지가 필요합니다. .NET Core 설치 관리자에서 이를 설치합니다. 설치 관리자 스크립트를 통해 .NET Core를 설치하는 경우 Visual C++ 재배포 가능 패키지를 수동으로 설치해야 합니다(`dotnet-install.ps1`). 

Visual C++ 재배포 가능 패키지 버전은 Windows 버전에 따라 다릅니다.

* Windows 10
    * [Visual Studio 2015용 Visual C++ 재배포 가능 패키지](https://www.microsoft.com/en-us/download/details.aspx?id=48145)
* Windows 7 이상(Windows 10 제외)
    * 설치된 Windows가 최신 버전이며 Windows 업데이트를 통해 설치된 핫픽스 [KB2533623](https://support.microsoft.com/en-us/kb/2533623)이 포함되어 있는지 확인하세요.
    * [유니버설 CRT 업데이트](https://www.microsoft.com/en-us/download/details.aspx?id=48234)(유니버설 CRT에 대한 자세한 내용은 [이 블로그 게시물](https://blogs.msdn.microsoft.com/vcblog/2015/03/03/introducing-the-universal-crt/) 참조)

## <a name="visual-studio"></a>Visual Studio

.NET Core 앱을 개발하려면 Visual Studio 2015가 필요합니다. [Visual Studio Community 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs)를 무료로 다운로드할 수 있습니다. 

[Visual Studio 2015 업데이트 3.3](https://msdn.microsoft.com/library/mt752379.aspx)을 실행 중인지 확인하세요.

* **도움말** 메뉴에서 **Microsoft Visual Studio 정보**를 선택합니다.
* **Microsoft Visual Studio 정보** 대화 상자에서 버전 번호가 14.0.25424.00 이상이어야 하며, "업데이트 3"을 포함해야 합니다.
* 업데이트 3이 없으면 먼저 [Visual Studio 2015 업데이트 3](https://www.visualstudio.com/news/releasenotes/vs2015-update3-vs)을 다운로드 및 설치해야 합니다.
* 업데이트 3이 있지만 버전 번호가 14.0.25424.00보다 작은 경우 [Visual Studio 2015 업데이트 3.3](https://msdn.microsoft.com/library/mt752379.aspx)을 다운로드 및 설치해야 합니다.

[릴리스 정보](https://www.visualstudio.com/news/releasenotes/vs2015-update3-vs)에서 업데이트 3의 변경 내용에 대해 자세히 알아볼 수 있습니다.

## <a name="nuget-manager-extension-for-visual-studio"></a>Visual Studio용 NuGet Manager 확장

NuGet은 .NET Core를 포함한 Microsoft 개발 플랫폼에 대한 패키지 관리자입니다. .NET Core 앱을 빌드하려면 [NuGet 3.5.0](https://dist.nuget.org/visualstudio-2015-vsix/v3.5.0-beta/NuGet.Tools.vsix) 이상이 필요합니다.

## <a name="net-core-tools-for-visual-studio-2015"></a>Visual Studio 2015용 .NET Core 도구

[.NET Core 1.0.1 - VS 2015 Tooling Preview 2][sdk]를 다운로드 및 설치합니다. 

.NET Core 도구 패키지는 Visual Studio 2015용 .NET Core, 프로젝트 템플릿 및 기타 도구를 설치합니다.

> [!NOTE]
지금은 [.NET Core 1.0.1 - VS 2015 Tooling Preview 2][sdk]용 오프라인 설치 관리자를 다운로드할 수 없습니다. 오프라인 레이아웃을 이용하려면 대신 [일반 부트스트래퍼][sdk]를 다운로드하고 명령줄 옵션(예: `/layout c:\path`)과 함께 실행해야 합니다. 그런 다음 오프라인 설치 관리자로 사용할 수 있습니다. 로컬 경로에서 바로 실행 파일을 실행합니다. 이 절차는 전체 레이아웃이므로 지원되는 모든 언어의 모든 패키지를 다운로드하며 크기는 약 1GB입니다.

[sdk]: https://go.microsoft.com/fwlink/?LinkID=827546



<!--HONumber=Nov16_HO1-->


