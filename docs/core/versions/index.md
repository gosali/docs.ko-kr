---
title: ".NET Core 버전 관리"
description: ".NET Core 버전 관리"
keywords: .NET, .NET Core
author: richlander
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: f6f684b1-1d2c-4105-8376-7c1959e23803
translationtype: Human Translation
ms.sourcegitcommit: bb15293c569fa92f1acf6315c5fe7f2cd9cb6f68
ms.openlocfilehash: 3db91c84c887645bd3054fab70841657aa045c54

---

# <a name="net-core-versioning"></a>.NET Core 버전 관리

.NET Core는 [NuGet 패키지](../packages.md) 및 프레임워크의 플랫폼이며 하나의 단위로 배포됩니다. 이러한 각 플랫폼 계층의 버전은 제품 민첩성을 제공하고 제품 변경 내용을 정확하게 설명하기 위해 별도로 관리할 수 있습니다. 버전 관리 유연성도 중요하지만 제품을 더 쉽게 이해하기 위해 플랫폼의 버전을 하나의 단위로 관리하려고 할 수 있습니다.

어떤 점에서 제품은 고유하며, 패키지 관리자(NuGet)을 통해 패키지로 설명되고 제공됩니다. 일반적으로 독립 실행형 SDK로 .NET Core를 획득하지만 SDK는 대체로 NuGet 패키지를 통해 편리한 환경이 되므로 패키지와 구별되지 않습니다. 결과적으로 버전 관리는 패키지 측면에서 가장 중요하며 다른 버전 관리 환경은 그 다음입니다.

## <a name="semantic-versioning"></a>유의적 버전

.NET Core는 [유의적 버전(SemVer)](http://semver.org/)을 사용하여 major.minor.patch 버전 관리를 사용하고 버전 번호의 다양한 부분으로 변경의 수준 및 종류를 설명합니다.

다음 버전 관리 템플릿은 일반적으로 .NET Core에 적용됩니다. 기존 버전 관리에 맞게 조정된 경우가 있습니다. 이러한 경우는 이 문서의 뒷부분에서 설명합니다. 예를 들어 프레임워크는 플랫폼 및 API 기능만 나타내도록 되어 있어 주/부 버전 관리에 맞게 조정됩니다.

### <a name="versioning-form"></a>버전 관리 양식

MAJOR.MINOR.PATCH[-PRERELEASE-BUILDNUMBER]

### <a name="decision-tree"></a>의사 결정 트리

MAJOR인 경우:
  - 플랫폼에 대한 지원 삭제
  - 기존 종속성의 최신 MAJOR 버전 채택 
  - 기본적으로 호환성 쿼크 해제 사용 안 함

MINOR인 경우:
  - 공용 API 노출 영역 추가 
  - 새 동작 추가
  - 기존 종속성의 최신 MINOR 버전 채택
  - 새 종속성 도입 
  
PATCH인 경우
  - 버그 수정
  - 최신 플랫폼에 대한 지원 추가
  - 기존 종속성의 최신 PATCH 버전 채택
  - 다른 모든 변경(달리 캡처되지 않음)

여러 가지 변경 내용이 있는 경우 증분할 항목을 결정할 때 가장 높은 종류의 변경 내용을 선택하세요.

## <a name="versioning-scheme"></a>버전 관리 체계

다음과 같은 방법으로 .NET Core를 정의하고 버전 관리할 수 있습니다.

- 패키지로 배포되는 런타임 및 프레임워크 구현. 특히 패치 버전 관리의 경우 각 패키지 버전을 독립적으로 관리합니다.
- 세분화된 패키지를 버전이 있는 단위로 참조하는 메타패키지 집합. 메타패키지는 패키지와 별도로 버전 관리합니다.
- 버전이 있는 스냅숏 집합에 설명된 대로 점점 더 커지는 API 집합을 나타내는 프레임워크 집합(예: `netstandard`).

### <a name="packages"></a>패키지

라이브러리 패키지는 독립적으로 발전하고 버전 관리됩니다. .NET Framework System.\* 어셈블리와 겹치는 패키지는 일반적으로 .NET Framework 4.x 버전 관리(기록 선택)를 지원하는 4.x 버전을 사용합니다. .NET Framework 라이브러리(예: [System.Reflection.Metadata](https://www.nuget.org/packages/System.Reflection.Metadata))와 겹치지 않는 패키지는 일반적으로 1.0에서 시작하여 증가합니다.

[NETStandard.Library](https://www.nuget.org/packages/NETStandard.Library)에 설명된 패키지는 플랫폼의 맨 아래에 있기 때문에 특별하게 처리됩니다.

- NETStandard.Library 패키지는 해당 패키지 간에 구현 수준 종속성이 있기 때문에 일반적으로 집합으로 버전 관리됩니다.
- .NET Core 주 또는 부 릴리스의 일부로 API만 NETStandard.Library 패키지에 추가되는데, 이렇게 하면 새 `netstandard` 버전을 추가해야 하기 때문입니다. 이는 SemVer 요구 사항 이외의 추가 요구 사항입니다.

### <a name="metapackages"></a>메타패키지

.NET Core 메타패키지에 대한 버전 관리는 해당 메타패키지가 매핑되는 프레임워크를 기반으로 합니다. 메타패키지는 패키지 종료 시 매핑되는 프레임워크의 가장 높은 버전 번호(예: netstandard1.6)를 채택합니다. 

메타패키지의 패치 버전은 업데이트된 패키지를 참조하도록 메타패키지에 대한 업데이트를 나타내는 데 사용됩니다. 패치 버전에는 업데이트된 프레임워크 버전이 포함되지 않습니다. 결과적으로 메타패키지의 버전 관리 체계에서는 기본 패키지의 변경 수준을 나타내지 않고 주로 API 수준을 나타내기 때문에 메타패키지는 엄격하게 SemVer 규격이 아닙니다. 

.NET Core에 대한 주 메타패키지는 두 가지가 있습니다.

**NETStandard.Library**

- .NET Core 1.0 기준 v1.6(이러한 버전은 일반적으로나 의도적으로 일치하지 않음).
- `netstandard` 프레임워크에 매핑됩니다. 
- 최신 앱 개발에 필요한 것으로 간주되고 .NET 플랫폼에서 [.NET 표준](../../standard/library.md) 플랫폼으로 간주되도록 구현해야 하는 패키지를 설명합니다.

**Microsoft.NETCore.App**

- .NET Core 1.0 기준 v1.0(이러한 버전은 일치함).
- `netcoreapp` 프레임워크에 매핑됩니다.
- .NET Core 배포의 패키지를 설명합니다.

참고: [`Microsoft.NETCore.Portable.Compatibility`](https://www.nuget.org/packages/Microsoft.NETCore.Portable.Compatibility)는 또 다른 .NET Core 메타패키지입니다. 특정 프레임워크에 매핑되지 않으므로 패키지처럼 버전 관리합니다.

### <a name="frameworks"></a>프레임워크

프레임워크 버전은 새 API가 추가될 때 업데이트됩니다. 이러한 버전은 구현 관련 사항이 아닌 API 셰이프를 나타내므로 패치 버전에 대한 개념이 없습니다. 주 및 부 버전 관리는 앞 부분에 지정된 SemVer 규칙을 따릅니다.

`netcoreapp` 프레임워크는 .NET Core 배포에 연결되어 있습니다. 따라서 .NET Core에서 사용하는 버전 번호를 따릅니다. 예를 들어 .NET Core 2.0이 릴리스되면 `netcoreapp2.0`을 대상으로 합니다. 모든 프레임워크에 동일하게 적용된다는 점을 고려하면 `netstandard` 프레임워크는 .NET 런타임의 버전 관리 체계와 일치하지 않습니다.

## <a name="versioning-in-practice"></a>실제 버전 관리

GitHub의 .NET Core 리포지토리에는 매일 많은 라이브러리의 새로운 빌드를 생성하는 커밋 및 PR이 있습니다. .NET Core의 모든 변경 내용에 대해 새로운 공개 버전을 만드는 것은 실용적이지 않습니다. 대신 느슨하게 정의된 일정 기간(예: 주 또는 월) 동안 변경 내용을 집계한 후 .NET Core의 안정적인 새 공개 버전을 만듭니다.

.NET Core의 새 버전은 다음과 같은 여러 가지를 의미할 수 있습니다.

- 패키지 및 메타패키지의 새 버전.
- 새 API의 추가를 가정하는 다양한 프레임워크의 새 버전.
- .NET Core 배포의 새 버전.

### <a name="shipping-a-patch-release"></a>패치 릴리스 전달

.NET Core v1.0.0 안정적 버전을 전달한 후 버그를 수정하고 성능 및 안정성을 개선하기 위해 .NET Core 라이브러리에서 패치 수준 변경(새 API 없음)을 수행합니다. 다양한 메타패키지가 업데이트된 .NET Core 라이브러리 패키지를 참조하도록 업데이트됩니다. 이 메타패키지는 패치 업데이트(x.y.z)로 버전 관리됩니다. 프레임워크는 업데이트되지 않습니다. 새로운 .NET Core 배포가 `Microsoft.NETCore.App` 메타패키지와 일치하는 버전 번호로 릴리스됩니다.

아래의 project.json 예제에 표시된 패치 업데이트를 확인할 수 있습니다.

```
{
  "dependencies": {
    "Microsoft.NETCore.App": "1.0.1"
  },
  "frameworks": {
    "netcoreapp1.0": {}
  }
}
```

### <a name="shipping-a-minor-release"></a>부 릴리스 전달

.NET Core v1.0.0 안정적 버전을 전달한 후 새 시나리오를 사용하기 위해 새 API를 .NET Core 라이브러리에 추가합니다. 다양한 메타패키지가 업데이트된 .NET Core 라이브러리 패키지를 참조하도록 업데이트됩니다. 메타패키지는 더 높은 프레임워크 버전과 일치하도록 패치 업데이트(x.y)로 버전 관리됩니다. 새 API를 설명하도록 다양한 프레임워크가 업데이트됩니다. 새로운 .NET Core 배포가 `Microsoft.NETCore.App` 메타패키지와 일치하는 버전 번호로 릴리스됩니다.

아래의 project.json 예제에 표시된 부분 업데이트를 확인할 수 있습니다.

```
{
  "dependencies": {
    "Microsoft.NETCore.App": "1.1.0"
  },
  "frameworks": {
    "netcoreapp1.1": {}
  }
}
```

### <a name="shipping-a-major-release"></a>주 릴리스 전달

.NET Core v1.y.z 안정적 버전을 지정하고 주요 새 시나리오를 사용하기 위해 새 API를 .NET Core 라이브러리에 추가합니다. 플랫폼에 대한 지원이 삭제될 수 있습니다. 다양한 메타패키지가 업데이트된 .NET Core 라이브러리 패키지를 참조하도록 업데이트됩니다. `Microsoft.NETCore.App` 메타패키지 및 `netcore` 프레임워크는 주요 업데이트(x.)로 버전 관리됩니다. `NETStandard.Library` 메타패키지는 여러 .NET 구현에 적용되므로 부분 업데이트(x.y)로 버전 관리될 수 있습니다. 새로운 .NET Core 배포가 `Microsoft.NETCore.App` 메타패키지와 일치하는 버전 번호로 릴리스됩니다.

아래 예제의 project.json 메타패키지 참조에 표시된 주요 업데이트를 확인할 수 있습니다.

```
{
  "dependencies": {
    "Microsoft.NETCore.App": "2.0.0"
  },
  "frameworks": {
    "netcoreapp2.0": {}
  }
}
```



<!--HONumber=Nov16_HO3-->


