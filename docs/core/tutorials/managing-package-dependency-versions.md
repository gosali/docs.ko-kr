---
title: ".NET Core 1.0에 대한 패키지 종속성 버전을 관리하는 방법"
description: ".NET Core 1.0에 대한 패키지 종속성 버전을 관리하는 방법"
keywords: .NET, .NET Core
author: cartermp
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 4424a947-bdf9-4775-8d48-dc350a4e0aee
translationtype: Human Translation
ms.sourcegitcommit: dd32f1dd4d17ab1bb01b5578237cc950b147898c
ms.openlocfilehash: 77e1f5484c0f55ae3989dbbea9bdbf2fb58f7926

---

# <a name="how-to-manage-package-dependency-versions-for-net-core-10"></a>.NET Core 1.0에 대한 패키지 종속성 버전을 관리하는 방법

이 문서에서는 .NET Core 라이브러리 및 앱의 패키지 버전에 대해 알아야 할 내용을 다룹니다.

## <a name="glossary"></a>용어

**고정** - 종속성 고정이란 .NET Core 1.0용 NuGet에 대해 릴리스된 동일한 패키지 "제품군"을 사용한다는 의미입니다.

**메타패키지** - NuGet 패키지 집합을 나타내는 NuGet 패키지입니다.

**잘라내기** - 종속되지 않은 패키지를 메타패키지에서 제거하는 행위입니다.  이는 NuGet 패키지 작성자와 관련이 있습니다.  자세한 내용은 [project.json(project.json으로 패키지 종속성 감소)](../deploying/reducing-dependencies.md)을 참조하세요. 

## <a name="fix-your-dependencies-to-net-core-10"></a>.NET Core 1.0으로 종속성 고정

신뢰할 수 있는 방식으로 패키지를 복원하고 신뢰할 수 있는 코드를 작성하려면 .NET Core 1.0과 함께 제공된 패키지의 버전에 대한 종속성을 수정하는 것이 중요합니다.  즉, 추가 한정자 없이 모든 패키지에 단일 버전이 있어야 합니다.

**1.0으로 고정된 패키지의 예**

`"System.Collections":"4.0.11"`

`"NETStandard.Library":"1.6.0"`

`"Microsoft.NETCore.App":"1.0.0"`

**1.0으로 고정되지 않는 패키지의 예**

`"Microsoft.NETCore.App":"1.0.0-rc4-00454-00"`

`"System.Net.Http":"4.1.0-*"`

`"System.Text.RegularExpressions":"4.0.10-rc3-24021-00"`

### <a name="why-does-this-matter"></a>이것이 왜 문제가 될까요?

.NET Core 1.0과 함께 제공된 것으로 종속성을 고정하면 해당 패키지가 모두 함께 작동하는 것이 보장됩니다.  이렇게 고정되지 않은 패키지를 사용할 경우 작동이 보장되지 않습니다.

### <a name="scenarios"></a>시나리오

.NET Core 1.0과 함께 출시된 모든 패키지와 버전의 큰 목록이 있지만 코드가 특정 시나리오에 놓인 경우 이러한 목록을 검토하지 않아도 될 수 있습니다.

****`NETStandard.Library`**에만 종속되어 있나요?**

이 경우 `NETStandard.Library` 패키지를 버전 `1.6`으로 고정해야 합니다.  이것은 조정된 메타패키지이므로 패키지 종료도 1.0으로 고정됩니다.

****`Microsoft.NETCore.App`**에만 종속되어 있나요?**

이 경우 `Microsoft.NETCore.App` 패키지를 버전 `1.0.0`으로 고정해야 합니다.  이것은 조정된 메타패키지이므로 패키지 종료도 1.0으로 고정됩니다.

****`NETStandard.Library` **또는** `Microsoft.NETCore.App` **메타패키지 종속성을 [잘라내고](../deploying/reducing-dependencies.md) 있나요?**

그렇다면 시작하는 메타패키지도 1.0으로 고정되어 있는지 확인해야 합니다.  잘라낸 후 종속된 개별 패키지도 1.0으로 고정됩니다.

****`NETStandard.Library` **또는** `Microsoft.NETCore.App` **메타패키지 외부의 패키지에 종속되어 있나요?**

그렇다면 다른 종속성을 1.0으로 고정해야 합니다.  이 문서 뒤에 나오는 올바른 패키지 버전 및 빌드 번호를 참조하세요.

### <a name="a-note-on-using-a-splat-string-when-versioning"></a>버전 관리 시 스플랫 문자열(\*) 참고 사항

스플랫(\*) 문자열을 사용하는 버전 관리 패턴을 다음과 같이 조정했을 수 있습니다. `"System.Collections":"4.0.11-*"`

**이렇게 해서는 안 됩니다**.  스플랫 문자열을 사용하면 패키지가 다른 빌드에서 복원될 수 있으며, 이중 일부는 .NET Core 1.0보다 더 나아간 것일 수 있습니다.  이 경우 일부 패키지는 호환되지 않을 수 있습니다.

## <a name="packages-and-version-numbers-organized-by-metapackage"></a>메타패키지로 구성된 패키지 및 버전 번호

[.NET 표준 라이브러리 패키지 및 1.0에 대한 해당 버전을 나열합니다](https://github.com/dotnet/versions/blob/master/build-info/dotnet/corefx/release/1.0.0/Latest_Packages.txt).

[모든 런타임 패키지 및 1.0에 대한 해당 버전을 나열합니다](https://github.com/dotnet/versions/blob/master/build-info/dotnet/coreclr/release/1.0.0/LKG_Packages.txt).

[모든 .NET Core 응용 프로그램 패키지 및 1.0에 대한 해당 버전을 나열합니다](https://github.com/dotnet/versions/blob/master/build-info/dotnet/core-setup/release/1.0.0/Latest_Packages.txt).



<!--HONumber=Nov16_HO1-->


