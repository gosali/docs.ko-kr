---
title: ".NET Core로 이식 - 타사 종속성 분석"
description: ".NET Core로 이식 - 타사 종속성 분석"
keywords: .NET, .NET Core
author: cartermp
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: b446e9e0-72f6-48f6-92c6-70ad0ce3f86a
translationtype: Human Translation
ms.sourcegitcommit: 46061efa8e33c6a73befa5181eb33b8deb2fa637
ms.openlocfilehash: 7e4e96183484d102d102eeab97191f8be9b9be8a

---

# <a name="porting-to-net-core-analyzing-your-thirdparty-party-dependencies"></a>.NET Core로 이식 - 타사 종속성 분석

이식 프로세스의 첫 번째 단계는 타사 종속성을 이해하는 것입니다.  .NET Core에서 아직 실행되지 않는 것이 무엇인지를 파악하고 이에 대한 대체 계획을 개발해야 합니다.

## <a name="prerequisites"></a>필수 조건

이 문서에서는 사용자가 Windows 및 Visual Studio를 사용하며 .NET Framework에서 실행되는 코드를 가지고 있다고 가정합니다.

## <a name="analyzing-nuget-packages"></a>NuGet 패키지 분석

이식성에 대한 NuGet 패키지 분석은 매우 쉽습니다.  NuGet 패키지는 플랫폼별 어셈블리를 포함하는 폴더의 집합 자체이므로, .NET Core 어셈블리를 포함하는 폴더가 있는지만 확인하면 됩니다.

NuGet 패키지 폴더 검사는 [NuGet 패키지 탐색기](https://github.com/NuGetPackageExplorer/NuGetPackageExplorer) 도구로 수행하는 것이 가장 쉽습니다.  방법은 다음과 같습니다.

1. NuGet 패키지 탐색기를 다운로드하여 엽니다.
2. "온라인 피드에서 패키지 열기"를 클릭합니다.
3. 패키지의 이름을 검색합니다.
4. 오른쪽에서 "lib" 폴더를 확장하고 폴더 이름을 찾습니다.

패키지에 대한 페이지의 **종속성** 섹션에 있는 [nuget.org](https://www.nuget.org/)에서 패키지가 지원하는 내용을 확인할 수 있습니다.

어떤 경우든 [nuget.org](https://www.nuget.org/)에서 다음 이름의 폴더 또는 항목을 찾아야 합니다.

```
netstandard1.0
netstandard1.1
netstandard1.2
netstandard1.3
netstandard1.4
netstandard1.5
netstandard1.6
netcoreapp1.0
portable-net45-win8
portable-win8-wpa8
portable-net451-win81
portable-net45-win8-wpa8-wpa81
```

[.NET 표준 라이브러리](../../standard/library.md)의 버전에 매핑되는 TFM(대상 프레임워크 모니커) 및 .NET Core와 호환되는 기존의 PCL(이식 가능한 클래스 라이브러리) 프로필입니다.  호환되는 `netcoreapp1.0`은 응용 프로그램용이며 라이브러리용이 아닙니다.  `netcoreapp1.0` 기반 라이브러리를 사용하는 것에는 문제가 없지만, 해당 라이브러리는 다른 `netcoreapp1.0` 응용 프로그램이 사용하는 것 *이외의* 용도가 아닐 수 있습니다.

호환 가능한 .NET Core의 시험판에서 사용되는 일부 레거시 TFM도 있습니다.

```
dnxcore50
dotnet5.0
dotnet5.1
dotnet5.2
dotnet5.3
dotnet5.4
dotnet5.5
```

**이러한 것도 코드와 호환될 수는 있지만 호환성이 보장되지는 않습니다.**  이러한 TFM이 포함된 패키지는 시험판 .NET Core 패키지로 빌드된 것입니다.  이러한 패키지를 `netstandard` 기반으로 업데이트할 경우에는 주의해야 합니다.

> [!NOTE]
> 기존 PCL 또는 시험판 .NET Core를 대상으로 하는 패키지를 사용하려면 `project.json` 파일에서 `imports` 지시문을 사용해야 합니다.

### <a name="what-to-do-when-your-nuget-package-dependency-doesnt-run-on-net-core"></a>NuGet 패키지 종속성이 .NET Core에서 실행되지 않는 경우 수행할 작업

종속된 NuGet 패키지가 .NET Core에서 실행되지 않을 경우 수행할 수 있는 몇 가지가 있습니다.

1. 프로젝트가 오픈 소스이고 GitHub 같은 곳에 호스트된 경우 개발자가 직접 참여하도록 할 수 있습니다.
2. 패키지를 검색하고 패키지 페이지의 왼쪽에서 "연락처 소유자"를 클릭하여 [nuget.org](https://www.nuget.org/)에서 작성자에게 직접 연락할 수 있습니다.
3. 사용하던 패키지와 동일한 작업을 수행하는, .NET Core에서 실행되는 다른 패키지를 찾을 수도 있습니다.
4. 패키지가 수행하고 있는 코드를 직접 작성해볼 수 있습니다.
5. 적어도 패키지의 호환 버전을 사용할 수 있을 때까지는 앱의 기능을 변경하여 패키지에 대한 종속성을 없앨 수 있습니다.

오픈 소스 프로젝트 유지 관리자 및 NuGet 패키지 게시자는 특정 도메인을 무료로 관리하며 낮에는 다른 업무가 있는 지원자인 경우가 많다는 점에 유의하세요. 연락되는 경우 .NET Core 지원을 요청하기 전에 라이브러리에 대한 긍정적인 문장으로 시작할 수 있습니다.

위 방법으로 문제를 해결할 수 없는 경우 나중에 .NET Core로 이식해야 할 수 있습니다.

.NET 팀은 다음에 .NET Core를 지원하기 위해 어떤 라이브러리가 가장 중요한지 알고자 합니다. 또한 사용하고 싶은 라이브러리에 대해 dotnet@microsoft.com으로 메일을 보낼 수 있습니다.

## <a name="analyzing-dependencies-which-arent-nuget-packages"></a>NuGet 패키지가 아닌 종속성 분석

파일 시스템의 DLL처럼, NuGet 패키지가 아닌 종속성이 있을 수 있습니다.  해당 종속성의 이식 가능성을 확인하는 유일한 방법은 [ApiPort 도구](https://github.com/Microsoft/dotnet-apiport/blob/master/docs/HowTo/)를 실행하는 것입니다.

## <a name="next-steps"></a>다음 단계

라이브러리를 이식하려는 경우 [라이브러리 이식](libraries.md)을 확인합니다.



<!--HONumber=Nov16_HO3-->


