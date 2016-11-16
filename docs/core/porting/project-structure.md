---
title: ".NET Framework 및 .NET Core를 지원하도록 프로젝트 구성"
description: ".NET Framework 및 .NET Core를 지원하도록 프로젝트 구성"
keywords: .NET, .NET Core
author: conniey
manager: wpickett
ms.date: 07/18/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 3af62252-1dfa-4336-8d2f-5cfdb57d7724
translationtype: Human Translation
ms.sourcegitcommit: 15c55a87beb64f265a164db918c7721c7690fadf
ms.openlocfilehash: ca63b25bb5f5e98167aaa8b74a7204fcd77b3523

---

# <a name="organizing-your-project-to-support-net-framework-and-net-core"></a>.NET Framework 및 .NET Core를 지원하도록 프로젝트 구성

이 문서는 .NET Framework 및 .NET Core에 대해 솔루션을 나란히 컴파일하려는 프로젝트 소유자를 위해 작성되었습니다.  개발자가 이러한 목표를 달성할 수 있도록 프로젝트를 구성하는 몇 가지 옵션이 제공됩니다.  .NET Core를 사용하여 프로젝트 레이아웃을 설정하는 방법을 결정할 때 고려해야 할 몇 가지 일반적인 시나리오는 다음과 같습니다.  여기에서 모든 것을 다루지는 못할 수 있습니다. 프로젝트 요구에 따라 우선 순위를 정하세요.

* [**기존 프로젝트와 .NET Core 프로젝트를 단일 프로젝트로 결합**][option-xproj]
  
  *좋은 점:*
  * 각각 다른 .NET Framework 버전 또는 플랫폼을 대상으로 하는 여러 프로젝트를 컴파일하는 것이 아니라 단일 프로젝트를 컴파일함으로써 빌드 프로세스를 간소화합니다.
  * 단일 프로젝트 파일을 관리해야 하므로 다중 대상 프로젝트에 대한 소스 파일 관리를 간소화합니다.  소스 파일을 추가/제거할 때 대체 방법에서는 이를 다른 프로젝트와 수동으로 동기화해야 합니다.
  * 사용할 NuGet 패키지를 쉽게 생성합니다.
  * 컴파일러 지시문을 사용하여 라이브러리에서 특정 .NET Framework 버전에 대한 코드를 작성할 수 있습니다.
  
  *지원되지 않는 시나리오:*
  * 개발자가 Visual Studio 2015 없이 기존 프로젝트를 열 수 없습니다. Visual Studio의 이전 버전을 지원하려면 [프로젝트 파일을 서로 다른 폴더에 유지](#support-vs)하는 것이 더 낫습니다.
  * 동일한 솔루션 파일의 서로 다른 프로젝트 형식 간에 .NET Core 라이브러리를 공유할 수 없습니다. 이를 지원하려면 [이식 가능한 클래스 라이브러리를 만드는 것](#support-pcl)이 더 낫습니다.
  * MSBuild 대상 및 작업에서 지원하는 프로젝트 빌드나 로드 수정이 허용되지 않습니다. 이를 지원하려면 [이식 가능한 클래스 라이브러리를 만드는 것](#support-pcl)이 더 낫습니다.

* [**기존 프로젝트와 새로운 .NET Core 프로젝트를 별도로 유지**][option-xproj-folder]
  
  *좋은 점:*
  * Visual Studio 2015가 없는 개발자/참가자의 경우 업그레이드하지 않고 기존 프로젝트에 대한 개발을 계속 지원할 수 있습니다.
  * 기존 프로젝트에서 코드 변동이 필요하지 않으므로 새 버그가 발생할 가능성이 줄어듭니다.

* [**기존 프로젝트를 유지하고 .NET Core를 대상으로 하는 PCL(이식 가능한 클래스 라이브러리) 만들기**][option-pcl]

  *좋은 점:*
  * 동일한 솔루션의 전체 .NET Framework를 대상으로 하는 데스크톱 및/또는 웹 프로젝트에서 .NET Core 라이브러리를 참조합니다.
  * 프로젝트 빌드 또는 로드 프로세스에서 수정을 지원합니다. 이러한 수정은 `*.csproj` 파일에서 MSBuild 작업 및 대상에 포함될 수 있습니다.

  *지원되지 않는 시나리오:*
  * [미리 정의된 전처리기 기호][how-to-multitarget]가 지원되지 않으므로 특정 .NET Framework 버전에 대한 코드를 쓸 수 없습니다.

## <a name="example"></a>예제

아래 리포지토리를 고려하세요.

![기존 프로젝트][example-initial-project]

[**소스 코드**][example-initial-project-code]

아래에서 설명하는 기존 프로젝트의 제약 조건 및 복잡성에 따라 이 리포지토리에 대한 .NET Core의 지원을 추가하는 여러 가지 방법이 있습니다.

## <a name="replace-existing-projects-with-a-multitargeted-net-core-project-xproj"></a>기존 프로젝트를 멀티 타기팅 .NET Core 프로젝트(xproj)와 교체

기존 `*.csproj` 파일을 제거하고 여러 프레임워크를 대상으로 하는 단일 `*.xproj` 파일을 만들도록 리포지토리를 재구성할 수 있습니다.  서로 다른 프레임워크에 대해 단일 프로젝트를 컴파일할 수 있으므로 이는 좋은 옵션입니다.  또한 대상 프레임워크에 대해 서로 다른 컴파일 옵션, 종속성 등을 처리할 수도 있습니다.

![여러 프레임워크를 대상으로 하는 xproj 만들기][example-xproj]

[**소스 코드**][example-xproj-code]

주목할 변경 사항은 다음과 같습니다.
* 추가: `global.json`
* `packages.config` 및 `*.csproj`를 다음으로 교체: `project.json` 및 `*.xproj`
* 기존 .NET Framework 및 기타에 대한 빌드를 지원하도록 [Car의 project.json][example-xproj-projectjson] 및 [테스트 프로젝트][example-xproj-projectjson-test]에서 변경된 사항

## <a name="create-a-portable-class-library-pcl-to-target-net-core"></a>.NET Core를 대상으로 하는 PCL(이식 가능한 클래스 라이브러리) 만들기

기존 프로젝트가 `*.csproj` 파일에 복잡한 빌드 작업 또는 속성을 포함하는 경우 PCL을 만드는 것이 더 쉬울 수 있습니다.

![][example-pcl]

[**소스 코드**][example-pcl-code]

주목할 변경 사항은 다음과 같습니다.
*  `project.json` 이름을 다음으로 바꾸기: `{project-name}.project.json`
    * 이렇게 하면 동일한 디렉터리의 라이브러리에 대한 패키지를 복원하려고 시도할 때 Visual Studio 내 충돌 가능성이 해소됩니다. 자세한 내용은 [NuGet FAQ](https://docs.nuget.org/consume/nuget-faq#working-with-packages)의 "_I have multiple projects in the same folder, how can I use separate packages.config or project.json files for each project?(동일한 폴더에 여러 프로젝트가 있는데, 각 프로젝트에 대해 어떻게 별도의 packages.config 또는 project.json 파일을 사용하나요?)_"를 참조하세요.
    *  **대안**: 이 문제를 방지하려면 또 다른 폴더에 PCL을 만들고 원래 소스 코드를 참조합니다.  PCL을 또 다른 폴더에 두면 Visual Studio 2015를 가지고 있지 않은 사용자도 새 솔루션을 로드하지 않은 채 이전 프로젝트에서 작업할 수 있다는 이점이 있습니다.
*  Visual Studio에서 PCL을 만든 후 .NET 표준을 대상으로 지정하려면 **프로젝트의 속성**을 엽니다. **대상** 섹션 아래에서 **".NET 플랫폼 표준을 대상으로 지정"**을 클릭합니다.  동일한 단계를 반복하면 이 변경 내용을 되돌릴 수 있습니다.

## <a name="keep-existing-projects-and-create-a-net-core-project"></a>기존 프로젝트를 유지하고 .NET Core 프로젝트 만들기

이전 프레임워크를 대상으로 하는 기존 프로젝트가 있는 경우, 이러한 프로젝트는 그대로 두고 이후 프레임워크를 대상으로 하는 .NET Core를 사용할 수 있습니다.

![다른 폴더에 기존 PCL이 있는 .NET Core 프로젝트][example-xproj-different-folder]

[**소스 코드**][example-xproj-different-code]

주목할 변경 사항은 다음과 같습니다.
* .NET Core와 기존 프로젝트가 별도의 폴더에 유지됩니다.
    * 따라서 여러 project.json/package.config 파일이 동일한 폴더에 있어서 발생하는 위에서 언급한 패키지 복원 문제가 해소됩니다.
    * 프로젝트를 별도의 폴더에 유지하면 project.json 때문에 Visual Studio 2015를 가지고 있어야 할 필요가 없습니다.  이전 프로젝트만 여는 별도 솔루션을 만들 수 있습니다.

## <a name="see-also"></a>참고 항목

project.json 및 xproj로의 이전에 대한 자세한 지침은 [.NET Core 이식 문서][porting-doc]를 참조하세요.

[porting-doc]: index.md
[example-initial-project]: media/project-structure/project.png "기존 프로젝트"
[example-initial-project-code]: https://github.com/dotnet/docs/tree/master/samples/framework/libraries/migrate-library/

[example-xproj]: media/project-structure/project.xproj.png "다중 프레임워크를 대상으로 하는 xproj 만들기"
[example-xproj-code]: https://github.com/dotnet/docs/tree/master/samples/framework/libraries/migrate-library-xproj/
[example-xproj-projectjson]: https://github.com/dotnet/docs/tree/master/samples/framework/libraries/migrate-library-xproj/src/Car/project.json
[example-xproj-projectjson-test]: https://github.com/dotnet/docs/tree/master/samples/framework/libraries/migrate-library-xproj/tests/Car.Tests/project.json

[example-xproj-different-folder]: media/project-structure/project.xproj.different.png "다른 폴더에 기존 PCL이 있는 .NET Core 프로젝트"
[example-xproj-different-code]: https://github.com/dotnet/docs/tree/master/samples/framework/libraries/migrate-library-xproj-keep-csproj/

[example-pcl]: media/project-structure/project.pcl.png ".NET Core를 대상으로 하는 PCL"
[example-pcl-code]: https://github.com/dotnet/docs/tree/master/samples/framework/libraries/migrate-library-pcl

[option-xproj]: #replace-existing-projects-with-a-multi-targeted-net-core-project-xproj
[option-pcl]: #create-a-portable-class-library-pcl-to-target-net-core
[option-xproj-folder]: #keep-existing-projects-and-create-a-net-core-project

[how-to-multitarget]: ../tutorials/libraries.md#how-to-multitarget



<!--HONumber=Nov16_HO1-->


