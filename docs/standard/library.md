---
title: ".NET 표준 라이브러리"
description: ".NET 표준 라이브러리"
keywords: .NET, .NET Core
author: richlander
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: c044882c-af15-45f2-96d1-534557a5ee9b
translationtype: Human Translation
ms.sourcegitcommit: f9ffbb2e300df2080276096095a7269736260ba1
ms.openlocfilehash: ff61319f15fea4c838517ed209e8095b37186c2e

---

# <a name="net-standard-library"></a>.NET 표준 라이브러리

.NET 표준 라이브러리는 모든 .NET 런타임에서 사용할 수 있는 .NET API의 공식 사양입니다. 표준 라이브러리는 .NET 에코시스템의 균일성을 높이기 위한 것입니다. [ECMA 335](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/dotnet-standards.md)에서는 .NET 런타임 동작에 대한 균일성을 계속 설정하지만 .NET 라이브러리 구현의 .NET BCL(기본 클래스 라이브러리)에 대한 유사한 사양은 없습니다. 

.NET 표준 라이브러리를 통해 다음과 같은 주요 시나리오를 사용할 수 있습니다. 

- 작업에 독립적으로 구현할 모든 .NET 플랫폼에 대해 균일한 BCL API 집합을 정의합니다.
- 개발자가 이 동일한 API 집합을 사용하여 .NET 런타임에서 사용할 수 있는 이식 가능한 라이브러리를 생성할 수 있게 합니다.
- OS API에 대해서만 .NET API로 인한 공유 소스의 조건부 컴파일을 줄이고 가능할 경우 제거합니다.

다양한 .NET 런타임에서 특정 버전의 .NET 표준 라이브러리를 구현합니다. 각 .NET 런타임 버전은 지원하는 최신 .NET 표준 버전을 보급합니다. 이는 이전 버전도 지원함을 의미합니다. 예를 들어 .NET Framework 4.6에서는 .NET 표준 라이브러리 1.3을 구현합니다. 즉, .NET 표준 라이브러리 버전 1.0에서 1.3까지에 정의된 모든 API를 표시합니다. 마찬가지로, .NET Framework 4.6.2에서는 .NET 표준 라이브러리 1.5를 구현하고 .NET Core 1.0에서는 .NET 표준 라이브러리 1.6을 구현합니다.

## <a name="net-platforms-support"></a>.NET 플랫폼 지원

.NET 표준 라이브러리를 지원하는 .NET 런타임의 전체 집합을 확인할 수 있습니다.

| 플랫폼 이름 | Alias |  |  |  |  |  | | | |
| :---------- | :--------- |:--------- |:--------- |:--------- |:--------- |:--------- |:--------- |:--------- |:--------- |
|.NET 표준 | netstandard | 1.0 | 1.1 | 1.2 | 1.3 | 1.4 | 1.5 | 1.6 | 2.0 |
|.NET Core|netcoreapp|&rarr;|&rarr;|&rarr;|&rarr;|&rarr;|&rarr;|1.0|vNext|
|.NET Framework|net|&rarr;|4.5|4.5.1|4.6|4.6.1|4.6.2|vNext|4.6.1|
|Mono/Xamarin 플랫폼||&rarr;|&rarr;|&rarr;|&rarr;|&rarr;|&rarr;|&rarr;|vNext|
|범용 Windows 플랫폼|uap|&rarr;|&rarr;|&rarr;|&rarr;|10.0|&rarr;|&rarr;|vNext|
|창|win|&rarr;|8.0|8.1||||||
|Windows Phone|wpa|&rarr;|&rarr;|8.1||||||
|Windows Phone Silverlight|wp|8.0||||||||

## <a name="comparison-to-portable-class-libraries"></a>이식 가능한 클래스 라이브러리와 비교

.NET 표준 라이브러리는 차세대 PCL([이식 가능한 클래스 라이브러리](https://msdn.microsoft.com/library/gg597391.aspx))로 간주될 수 있습니다. .NET 표준 라이브러리는 표준 BCL을 조정하고 그 결과로 .NET 런타임 간에 균일성을 높여 이식 가능한 라이브러리를 만드는 환경을 향상합니다. .NET 표준 라이브러리를 대상으로 하는 라이브러리는 PCL 또는 ".NET 표준 기반 PCL"입니다. 기존 PCL은 "프로필 기반 PCL"입니다.

.NET 표준 라이브러리 및 PCL 프로필은 비슷한 용도로 생성되었지만 주요 차이점도 있습니다.

유사점:

- 이진 코드 공유에 사용할 수 있는 API를 정의합니다.

차이점:

- .NET 표준 라이브러리는 조정된 API 집합이지만, PCL 프로필은 기존 플랫폼의 교차 부분으로 정의됩니다.
- .NET 표준 라이브러리 버전은 선형으로 관리되지만, PCL 프로필 버전은 선형으로 관리되지 않습니다.
- PCL 프로필은 Microsoft 플랫폼을 나타내지만 .NET 표준 라이브러리는 플랫폼에 독립적입니다.

## <a name="specification"></a>사양

.NET 표준 라이브러리 사양은 표준화된 API 집합입니다. 사양은 .NET 런타임 구현자, 특히 Microsoft(.NET Framework, .NET Core, Mono 포함) 및 Unity에 의해 유지 관리됩니다. 공용 피드백 프로세스는 새로운 .NET 표준 라이브러리 버전을 설정하는 과정의 일부로 사용됩니다.

### <a name="official-artifacts"></a>공식 아티팩트

공식 사양은 표준의 일부인 API를 정의하는 .cs 파일 집합입니다. 각 [구성 요소](https://github.com/dotnet/corefx/tree/master/src)에 대한 [ref 디렉터리](https://github.com/dotnet/corefx/tree/master/src/System.Runtime/ref)에서 .NET 표준 라이브러리 API를 정의합니다. ref 아티팩트는 [CoreFX 리포지토리](https://github.com/dotnet/corefx)에 있지만 .NET Core 특정은 아닙니다.

[NETStandard.Library](https://www.nuget.org/packages/NETStandard.Library) 메타패키지([소스](https://github.com/dotnet/corefx/blob/master/pkg/NETStandard.Library/NETStandard.Library.packages.targets))는 하나 이상의 .NET 표준 라이브러리 버전을 부분적으로 정의하는 라이브러리 집합에 대해 설명합니다.

System.Runtime 등의 지정된 구성 요소는 다음에 대해 설명합니다.

- .NET 표준 라이브러리의 일부(해당 범위만)
- 해당 범위에 대한 .NET 표준 라이브러리의 여러 버전

보다 편리하게 읽을 수 있고 특정 개발자 시나리오(예: 컴파일러 사용)를 지원할 수 있도록 파생 아티팩트가 제공됩니다.

- markdown의 API 목록(TBD)
- [NuGet 패키지](../core/packages.md)로 배포되고 [NETStandard.Library](https://www.nuget.org/packages/NETStandard.Library/) 메타패키지에서 참조되는 참조 어셈블리입니다.

### <a name="package-representation"></a>패키지 표현

.NET 표준 라이브러리 참조 어셈블리의 기본 배포 수단은 [NuGet 패키지](../core/packages.md)입니다. 구현은 각 .NET 런타임에 적절한 여러 가지 방법으로 전달됩니다.

NuGet 패키지는 하나 이상의 [프레임워크](frameworks.md)를 대상으로 합니다. .NET 표준 라이브러리 패키지는 ".NET 표준" 프레임워크를 대상으로 합니다. `netstandard` [compact TFM](frameworks.md)(예: `netstandard1.4`)을 사용하여 .NET 표준 프레임워크를 대상으로 지정할 수 있습니다. 여러 런타임에서 실행되도록 만들어진 라이브러리는 이 프레임워크를 대상으로 해야 합니다. 

`NETStandard.Library` 메타패키지는 .NET 표준 라이브러리를 정의하는 NuGet 패키지의 전체 집합을 참조합니다.  `netstandard`를 대상으로 지정하는 가장 일반적인 방법은 이 메타패키지를 참조하는 것입니다. 이 메타패키지는 최대 40개의 .NET 라이브러리 및 .NET 표준 라이브러리를 정의하는 관련 API를 설명하고 액세스할 수 있도록 합니다. 추가 API에 액세스하기 위해 `netstandard`를 대상으로 하는 추가 패키지를 참조할 수 있습니다. 

### <a name="versioning"></a>버전 관리

사양은 단수형이 아니라 점진적으로 증가하고 선형으로 버전이 관리되는 API 집합입니다. 첫 번째 버전의 표준에서는 API의 기준 집합을 설정합니다. 이후 버전에서는 API를 추가하고 이전 버전에서 정의한 API를 상속받습니다. 표준에서 API 제거와 관련해서 설정된 프로비전은 없습니다.

.NET 표준 라이브러리는 하나의 .NET 런타임 특정이 아니며 이러한 런타임 중 하나의 버전 관리 체계와 일치하지도 않습니다.

런타임(예:.NET Framework, .NET Core, Mono) 중 하나에 추가된 API는 특히 기본적인 특성으로 간주되는 경우 사양에 추가할 후보로 고려될 수 있습니다. .NET 런타임 릴리스에 따라 새 [.NET 표준 라이브러리 버전](https://github.com/dotnet/corefx/blob/master/Documentation/architecture/net-platform-standard.md#list-of-net-corefx-apis-and-their-associated-net-platform-standard-version)이 생성되므로 .NET 표준 PCL에서 새 API를 대상으로 지정할 수 있습니다. 버전 관리 메커니즘에 대해서는 [.NET Core 버전 관리](../core/versions/index.md)에서 자세히 설명합니다.

.NET 표준 라이브러리 버전 관리는 사용에 중요합니다. .NET 표준 라이브러리 버전이 지정된 경우 동일한 버전이나 하위 버전을 대상으로 하는 라이브러리를 사용할 수 있습니다. 다음 방법에서는 .NET 표준 라이브러리 대상 지정과 관련된 .NET 표준 라이브러리 PCL 사용 워크플로에 대해 설명합니다.

- PCL에 사용할 .NET 표준 라이브러리 버전을 선택합니다.
- 동일한 .NET 표준 라이브러리 버전이나 하위 버전에 종속된 라이브러리를 사용합니다.
- 상위 .NET 표준 라이브러리 버전에 종속된 라이브러리가 있을 경우 동일한 버전을 채택하거나 해당 라이브러리를 사용하지 않도록 결정해야 합니다.

### <a name="pcl-compatibility"></a>PCL 호환성

.NET 표준 라이브러리는 PCL 프로필의 하위 집합과 호환됩니다. .NET 표준 라이브러리 1.0, 1.1 및 1.2는 각각 PCL 프로필 집합과 겹칩니다. 이러한 중첩은 다음 두 가지 이유로 생성되었습니다.

- .NET 표준 기반 PCL이 프로필 기반 PCL을 참조할 수 있습니다.
- 프로필 기반 PCL을 .NET 표준 기반 PCL로 패키지할 수 있습니다.

프로필 기반 PCL 호환성은 [Microsoft.NETCore.Portable.Compatibility](https://www.nuget.org/packages/Microsoft.NETCore.Portable.Compatibility) NuGet 패키지에서 제공됩니다. 이러한 종속성은 프로필 기반 PCL을 포함하는 NuGet 패키지를 참조할 때 필요합니다.

`netstandard`로 패키지된 프로필 기반 PCL은 일반적으로 project.json에 패키지된 프로필 기반 PCL보다 사용하기가 더 쉽습니다. `netstandard` 패키징은 기존 사용자와 호환됩니다.

.NET 표준과 호환되는 PCL 프로필 집합을 확인할 수 있습니다. 

| 프로필 | .NET 플랫폼 표준 버전 |
| ---------| --------------- |
| Profile7 .NET 이식 가능한 하위 집합(.NET Framework 4.5, Windows 8) | 1.1 |
| Profile31 .NET 이식 가능한 하위 집합(Windows 8.1, Windows Phone Silverlight 8.1)| 1.0 |
| Profile32 .NET 이식 가능한 하위 집합(Windows 8.1, Windows Phone 8.1) | 1.2 |
| Profile44 .NET 이식 가능한 하위 집합(.NET Framework 4.5.1, Windows 8.1) | 1.2 |
| Profile49 .NET 이식 가능한 하위 집합(.NET Framework 4.5, Windows Phone Silverlight 8) | 1.0 |
| Profile78 .NET 이식 가능한 하위 집합(.NET Framework 4.5, Windows 8, Windows Phone Silverlight 8) | 1.0 |
| Profile84 .NET 이식 가능한 하위 집합(Windows Phone 8.1, Windows Phone Silverlight 8.1) | 1.0 |
| Profile111 .NET 이식 가능한 하위 집합(.NET Framework 4.5, Windows 8, Windows Phone 8.1) | 1.1 |
| Profile151 .NET 이식 가능한 하위 집합(.NET Framework 4.5.1, Windows 8.1, Windows Phone 8.1) | 1.2 |
| Profile157 .NET 이식 가능한 하위 집합(Windows 8.1, Windows Phone 8.1, Windows Phone Silverlight 8.1) | 1.0 |
| Profile259 .NET 이식 가능한 하위 집합(.NET Framework 4.5, Windows 8, Windows Phone 8.1, Windows Phone Silverlight 8) | 1.0 |

## <a name="targeting-net-standard-library"></a>.NET 표준 라이브러리를 대상으로 지정

`netstandard` 프레임워크와 NETStandard.Library 메타패키지의 조합을 사용하여 [.NET 표준 라이브러리를 빌드](../core/tutorials/libraries.md)할 수 있습니다. [.NET Core 도구를 사용하여 .NET 표준 라이브러리를 대상으로 지정](../core/packages.md)하는 예를 확인할 수 있습니다.



<!--HONumber=Nov16_HO1-->


