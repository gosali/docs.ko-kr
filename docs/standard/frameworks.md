---
title: "프레임워크"
description: "프레임워크"
keywords: .NET, .NET Core
author: richlander
manager: wpickett
ms.date: 09/19/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 6ef56a2e-593d-497b-925a-1e25bb6df2e6
translationtype: Human Translation
ms.sourcegitcommit: 246d381246e78a27b7097d6b8126f421b52014de
ms.openlocfilehash: 5b87ddfcfc66ebc05e2e559517180f93813a0806

---

# <a name="frameworks"></a>프레임워크

.NET 에코시스템에는 프레임워크의 개념이 있습니다. 프레임워크는 특정 플랫폼을 대상으로 지정하는 데 사용할 수 있는 API를 정의합니다. .NET Framework 4.6도 이러한 플랫폼 중 하나입니다. 프레임워크는 Visual Studio와 기타 IDE 및 편집기에서 올바른 API 집합을 제공하는 데 사용됩니다. 또한 NuGet에서 NuGet 패키지의 생성 및 소비 둘 다에 사용되어 대상으로 지정할 프레임워크에 적합한 패키지(및 기본 자산)를 생성 및 사용하도록 합니다. 프레임워크는 .NET 에코시스템의 주요 통화 중 하나로 간주될 수 있습니다. 이 개념의 목적은 사용자와 고객에게 @System.MissingMethodException이 표시되는 것을 방지하고 런타임에 친구들을 돕기 위해 정확성을 보장하는 것입니다.

## <a name="framework-versions"></a>Framework 버전

아래 표에서는 사용할 수 있는 프레임워크 집합, 프레임워크를 참조하는 방법 및 프레임워크에서 구현하는 [.NET 표준 라이브러리](library.md) 버전을 정의합니다.

| 프레임워크 | 최신 버전 | TFM(대상 프레임워크 모니커) | Compact TFM(대상 프레임워크 모니커) | .NET 표준 버전 | Metapackage |
|:--------: | :--: | :--: | :--: | :--: | :--: | :--: |
| .NET 표준 | 1.6 | .NETStandard,Version=1.6 | netstandard1.6 | N/A | [NETStandard.Library](https://www.nuget.org/packages/NETStandard.Library)|
| .NET Core 응용 프로그램 | 1.0.1 | .NETCoreApp,Version=1.0 | netcoreapp1.0 | 1.6 | [Microsoft.NETCore.App](https://www.nuget.org/packages/Microsoft.NETCore.App)|
| .NET Framework | 4.6.2 | .NETFramework,Version=4.6.2 | net462 | 1.5 | N/A |

> [!NOTE]
> 이러한 프레임워크 버전은 안정적인 최신 버전입니다. 이 표에서 설명되지 않은 시험판 버전도 있을 수 있습니다.

## <a name="writing-about-frameworks"></a>프레임워크에 대한 문서 작성

문서 형식에서 프레임워크를 참조하는 여러 가지 방법이 있으며, 대부분 이 설명서에서 사용됩니다. 이 설명서를 해석하기 위한 범례를 제공하고 다른 문서에서 올바로 사용하도록 안내하기 위해 아래에 해당 방법이 설명되어 있습니다.

.NET Framework 4.6.1을 예로 들면 다음 형식을 사용할 수 있습니다.

**제품 참조**

.NET 플랫폼 또는 런타임을 참조할 수 있습니다.

- ".NET Framework 4.6.1"

**프레임워크 참조**

길거나 짧은 형식의 TFM을 사용하여 프레임워크 또는 프레임워크의 대상 지정을 참조할 수 있습니다. 일반적인 경우 둘 다 유효합니다.

- `.NETFramework,Version=4.6.1`
- `net461`

**프레임워크 패밀리 참조**

길거나 짧은 형식의 프레임워크 ID를 사용하여 프레임워크 패밀리를 참조할 수 있습니다. 일반적인 경우 둘 다 유효합니다.

- `.NETFramework`
- `net`



<!--HONumber=Nov16_HO1-->


