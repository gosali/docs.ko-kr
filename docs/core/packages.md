---
title: "패키지, 메타패키지 및 프레임워크"
description: "패키지, 메타패키지 및 프레임워크"
keywords: .NET, .NET Core
author: richlander
ms.author: mairaw
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 609b0845-49e7-4864-957b-21ffe1b93bf2
translationtype: Human Translation
ms.sourcegitcommit: cb2e83b35b5a4aae14c89bcbdf26b064885a477a
ms.openlocfilehash: 6b5bb7042fdaa453966a4fc576291b3c90ee5410

---

# <a name="packages-metapackages-and-frameworks"></a>패키지, 메타패키지 및 프레임워크

.NET Core는 NuGet 패키지로 구성된 플랫폼입니다. 일부 제품은 정교하게 정의된 패키지로 구성되는 반면 다른 제품은 다소 거칠게 정의된 패키지로 구성됩니다. 이러한 이중성을 수용하기 위해 이 제품은 세분화된 패키지 집합으로 배포되고, 그런 다음 비공식적으로 "메타패키지"라고 하는 패키지 형식의 거친 덩어리로 기술됩니다.

각 .NET Core 패키지는 프레임워크라고 하는 여러 .NET 런타임에서의 실행을 지원합니다. 그러한 프레임워크 중 일부는 `net46`처럼 .NET Framework를 나타내는 기존의 프레임워크입니다. 또 다른 집합은 "패키지 기반 프레임워크"로 간주할 수 있는 새로운 프레임워크로, 프레임워크를 정의하기 위한 새 모델을 설정합니다. 이러한 패키지 기반 프레임워크는 패키지로서 완전히 구성 및 정의되어, 패키지와 프레임워크 간에 강력한 관계를 형성합니다.

## <a name="packages"></a>패키지

.NET Core는 기본 형식, 더 높은 수준의 데이터 형식, 앱 구성 형식 및 공통 유틸리티를 제공하는 패키지 집합으로 나누어집니다. 이러한 각 패키지는 동일한 이름의 단일 어셈블리를 나타냅니다. 예를 들어 [System.Runtime](https://www.nuget.org/packages/System.Runtime)은 System.Runtime.dll을 포함합니다. 

세분화된 방식으로 패키지를 정의하는 데 따른 이점이 있습니다.

- 세분화된 패키지는 비교적 제한적으로 타 패키지를 테스트하며 자체 일정으로 출시될 수 있습니다.
- 세분화된 패키지는 다른 OS 및 CPU 지원을 제공할 수 있습니다.
- 세분화된 패키지는 하나의 라이브러리에 대한 종속성을 가질 수 있습니다.
- 참조되지 않은 패키지는 앱 배포에 포함되지 않으므로 앱의 크기가 더 작습니다.

이러한 혜택 중 일부는 특정 상황에서만 사용됩니다. 예를 들어 .NET Core 패키지는 일반적으로 동일한 플랫폼 지원과 함께 동일한 일정으로 출시됩니다. 서비스의 경우 수정은 작은 단일 패키지 업데이트로서 배포 및 설치될 수 있습니다. 변경 범위가 좁기 때문에 수정을 준비하는 시간과 유효성 검사는 단일 라이브러리에 필요한 것으로 제한됩니다.

다음은 .NET Core용 핵심 NuGet 패키지의 목록입니다.

- [System.Runtime](https://www.nuget.org/packages/System.Runtime) - 가장 기본적인 .NET Core 패키지로 [Object](http://docs.microsoft.com/dotnet/core/api/System.Object), [String](http://docs.microsoft.com/dotnet/core/api/System.String), [Array](http://docs.microsoft.com/dotnet/core/api/System.Array), [Action](http://docs.microsoft.com/dotnet/core/api/System.Action) 및 [IList&lt;T&gt;](http://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.IList-1) 포함.
- [System.Collections](https://www.nuget.org/packages/System.Collections) - 주로 제네릭 컬렉션 집합으로 [List&lt;T&gt;](http://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.List-1) 및 [Dictionary&lt;K,V&gt;](http://docs.microsoft.com/dotnet/core/api/System.Collections.Generic.Dictionary-2) 포함.
- [System.Net.Http](https://www.nuget.org/packages/System.Net.Http) - HTTP 네트워크 통신에 대한 형식의 집합으로 [HttpClient](http://docs.microsoft.com/dotnet/core/api/System.Net.Http.HttpClient) 및 [HttpResponseMessage](http://docs.microsoft.com/dotnet/core/api/System.Net.Http.HttpResponseMessage) 포함.
- [System.IO.FileSystem](https://www.nuget.org/packages/System.IO.FileSystem) - 로컬 및 네트워크 디스크 기본 저장소에 대한 읽기 및 쓰기를 위한 형식의 집합으로 [File](http://docs.microsoft.com/dotnet/core/api/System.IO.File) 및 [Directory](http://docs.microsoft.com/dotnet/core/api/System.IO.Directory) 포함.
- [System.Linq](https://www.nuget.org/packages/System.Linq) - 개체 쿼리를 위한 형식의 집합으로 Enumerable 및 [ILookup&lt;TKey, TElement&gt;](http://docs.microsoft.com/dotnet/core/api/System.Linq.ILookup-2) 포함.
- [System.Reflection](https://www.nuget.org/packages/System.Reflection) - 형식의 로드, 검사 및 활성화를 위한 형식의 집합으로 [Assembly](http://docs.microsoft.com/dotnet/core/api/System.Reflection.Assembly), [TypeInfo](http://docs.microsoft.com/dotnet/core/api/System.Reflection.TypeInfo) 및 [MethodInfo](http://docs.microsoft.com/dotnet/core/api/System.Reflection.MethodInfo) 포함.

패키지는 project.json에서 참조됩니다. 다음 예제에서는 [System.Runtime](https://www.nuget.org/packages/System.Runtime/) 패키지가 참조됩니다. 

```json
{
  "dependencies": {
    "System.Runtime": "4.1.0"
  },
  "frameworks": {
    "netstandard1.6": {}
  }
}
```

관리할 패키지가 너무 많아지기 때문에 대부분의 경우 하위 수준 .NET Core 패키지를 직접 참조하지 않습니다. 대신 메타패키지를 참조하게 됩니다.

## <a name="metapackages"></a>메타패키지

메타패키지는 서로 의미가 있는 패키지 집합을 기술하기 위한 NuGet 패키지 규칙입니다. 메타패키지는 종속성을 설정함으로써 이러한 패키지 집합을 나타냅니다. 선택적으로, 프레임워크를 지정하여 이 패키지 집합에 대한 프레임워크를 설정할 수 있습니다. 

메타패키지를 참조함으로써 각각의 종속된 패키지에 단일 제스처로서 참조를 추가하게 됩니다. 즉, IntelliSense(또는 유사한 경험) 및 앱 게시(lib만)에 대해 해당 패키지의 모든 라이브러리(ref 또는 lib)를 사용할 수 있습니다. 

참고: 'lib' 및 'ref' 용어는 NuGet 패키지의 폴더를 가리킵니다. 'ref' 폴더는 어셈블리 메타데이터를 통해 패키지의 공용 API를 설명합니다. 'lib' 폴더는 지정된 프레임워크에 대한 해당 공용 API의 구현을 포함합니다. 

메타패키지 사용에 따른 이점이 있습니다.

- 세분화된 패키지의 큰 집합을 참조하기 위한 편리한 사용자 환경을 제공합니다. 
- 테스트되고 함께 잘 작동하는 패키지 집합을 정의합니다(특정 버전 포함).

.NET 표준 라이브러리 메타패키지:

- [NETStandard.Library](https://www.nuget.org/packages/NETStandard.Library) - ".NET 표준 라이브러리"에 포함된 라이브러리를 설명합니다. .NET 표준 라이브러리를 지원하는 모든 .NET 구현(예: .NET Framework, .NET Core 및 Mono)에 적용됩니다. 'netstandard' 프레임워크를 설정합니다.

다음은 .NET Core 메타패키지입니다.

- [Microsoft.NETCore.App](https://www.nuget.org/packages/Microsoft.NETCore.App) - .NET Core 배포에 포함된 라이브러리를 설명합니다. [`.NETCoreApp` 프레임워크를 설정합니다](https://github.com/dotnet/core-setup/blob/master/pkg/projects/Microsoft.NETCore.App/Microsoft.NETCore.App.pkgproj). 더 작은 `NETStandard.Library`에 종속됩니다.
- [Microsoft.NETCore.Portable.Compatibility](https://www.nuget.org/packages/Microsoft.NETCore.Portable.Compatibility) - .NET Core에서 실행되는 mscorlib 기반 PCL(이식 가능한 클래스 라이브러리)을 사용하도록 설정하는 호환성 외관의 집합입니다.

메타패키지는 project.json의 다른 NuGet 패키지와 마찬가지로 참조됩니다. 

다음 예제에서는 `NETStandard.Library` 메타패키지가 참조되는데, 이는 .NET 런타임 전체에서 이식 가능한 라이브러리를 만드는 데 사용됩니다.

```json
{
  "dependencies": {
    "NETStandard.Library": "1.6.0"
  },
  "frameworks": {
    "netstandard1.6": {}
  }
}
```

다음 예제에서는 `Microsoft.NETCore.App` 메타패키지가 참조되는데, 이는 .NET Core에서 실행되고 .NET Core를 충분히 활용할 앱과 라이브러리를 만드는 데 사용됩니다. `NETStandard.Library`에서 제공하는 것보다 더 큰 라이브러리 집합에 대한 액세스 권한을 제공합니다.

```json
{
  "dependencies": {
    "Microsoft.NETCore.App": "1.0.0"
  },
  "frameworks": {
    "netcoreapp1.0": {}
  }
}
```

## <a name="frameworks"></a>프레임워크

각 .NET Core 패키지는 framework 폴더(앞서 언급한 lib 및 ref 폴더 내)에서 선언된 프레임워크 집합을 지원합니다. 프레임워크는 사용 가능한 API 집합(및 기타 가능한 특성)을 설명하며, 이러한 집합은 특정 프레임워크를 대상으로 할 때 사용할 수 있습니다. 새 API가 추가될 때 버전이 지정됩니다.

예를 들어 [System.IO.FileSystem](https://www.nuget.org/packages/System.IO.FileSystem)은 다음 프레임워크를 지원합니다.

- .NETFramework,Version=4.6
- .NETStandard,Version=1.3
- 6개 Xamarin 플랫폼(예: xamarinios10)

처음 두 프레임워크는 프레임워크를 정의하는 두 가지 방법의 예를 보여주므로 대조해보면 유용합니다.

`.NETFramework,Version=4.6` 프레임워크는 .NET Framework 4.6의 사용 가능한 API를 나타냅니다. .NET Framework 4.6 참조 어셈블리로 컴파일된 라이브러리를 생성하고 net46 lib 폴더의 NuGet 패키지에 배포할 수 있습니다. 이는 .NET Framework 4.6을 대상으로 하거나 이와 호환되는 앱에 사용됩니다. 모든 프레임워크는 지금까지 이러한 방식으로 작동했습니다.

`.NETStandard,Version=1.3` 프레임워크는 패키지 기반 프레임워크로서, API를 정의하고 노출하는 프레임워크를 대상으로 하는 패키지에 의존합니다.

## <a name="packagebased-frameworks"></a>패키지 기반 프레임워크

프레임워크와 패키지 간에 양방향 관계가 있습니다. 첫 번째 부분은 특정 프레임워크(예: `netstandard1.3`)에 사용 가능한 API를 정의하는 것입니다. `netstandard1.3`(또는 `netstandard1.0`과 같은 호환되는 프레임워크)을 대상으로 하는 패키지는 `netstandard1.3`에 사용 가능한 API를 정의합니다. 순환 정의처럼 들릴 수 있지만 그렇지 않습니다. "패키지 기반" 덕분에 프레임워크의 API 정의는 패키지에서 옵니다. 프레임워크 자체는 API를 정의하지 않습니다.

관계의 두 번째 부분은 자산 선택입니다. 패키지는 여러 프레임워크에 대한 자산을 포함할 수 있습니다. 패키지 및/또는 메타패키지 집합에 대한 참조를 고려하여, 프레임워크는 어떤 자산을 선택할지를 결정해야 합니다(예: `net46` 또는 `netstandard1.3`). 올바른 자산을 선택하는 것이 중요합니다. 예를 들어 한 `net46` 자산은 .NET Framework 4.0 또는 .NET Core 1.0과 호환될 것 같지 않습니다.

![패키지 기반 프레임워크 구성](./media/packages/package-framework.png)

위의 그림에서 이 관계를 볼 수 있습니다. *API*는 *프레임워크*를 대상으로 하고 정의합니다. *프레임워크*는 *자산 선택*에 사용됩니다. *자산*은 API를 제공합니다.

패키지 기반 프레임워크의 정의가 어디서 끝나고 정의의 사용이 어디서 시작되는가는 흥미로운 질문입니다. 프레임워크의 보기를 지정된 project.json 파일의 기능이라고 생각할 수 있습니다. 종속성이 프레임워크의 보기를 만듭니다(종속성의 게시자와 독립적으로).

.NET Core와 사용되는 두 가지 기본 패키지 기반 프레임워크는 다음과 같습니다.

- `netstandard`
- `netcoreapp`

### <a name="net-standard"></a>.NET 표준

.NET 표준(TFM: `netstandard`) 프레임워크는 [.NET 표준 라이브러리](../standard/library.md)에 의해 정의되고 그 위에 빌드되는 API를 나타냅니다. 여러 런타임에서 실행되도록 만들어진 라이브러리는 이 프레임워크를 대상으로 하며, .NET Core, .NET Framework 및 Mono/Xamarin 같은 .NET 표준 호환 런타임에서 지원됩니다. 이러한 각 런타임은 구현하는 API에 따라 .NET 표준 버전 집합을 지원합니다. 

`NETStandard.Library` 메타패키지는 `netstandard` 프레임워크를 대상으로 합니다. `netstandard`를 대상으로 하는 가장 일반적인 방법은 이 메타패키지를 참조하는 것입니다. 이 메타패키지는 최대 40개의 .NET 라이브러리 및 .NET 표준 라이브러리를 정의하는 관련 API를 설명하고 액세스할 수 있도록 합니다. 추가 API에 대한 액세스를 얻기 위해 `netstandard`를 대상으로 하는 추가 패키지를 참조할 수 있습니다.

지정된 [NETStandard.Library 버전](versions/index.md)은 `netstandard`의 최고 노출 버전과 일치합니다(클로저를 통해). project.json의 프레임워크 참조는 기본 패키지에서 올바른 자산을 선택하는 데 사용됩니다. 이 경우 예를 들면 `netstandard1.4` 또는 `net46`과 반대로 `netstandard1.6` 자산이 필요합니다. 

```json
{
  "dependencies": {
    "NETStandard.Library": "1.6.0"
  },
  "frameworks": {
    "netstandard1.6": {}
  }
}
```

project.json의 프레임워크 및 메타패키지 참조는 일치하지 않습니다. 예를 들어, 다음 project.json은 유효합니다.

```json
{
  "dependencies": {
    "NETStandard.Library": "1.6.0"
  },
  "frameworks": {
    "netstandard1.3": {}
  }
}
```

`netstandard1.3`을 대상으로 하지만 `NETStandard.Library`의 1.6.0 버전을 사용하는 것이 이상하게 보일 수 있습니다. 메타패키지가 이전 `netstandard` 버전에 대한 지원을 유지하므로 이는 유효한 사용 사례입니다. 메타패키지의 1.6.0 버전에서 표준화하고 모든 라이브러리에 사용하여 다양한 `netstandard` 버전을 대상으로 하는 사례일 수 있습니다. 이 방법을 통해 `NETStandard.Library` 1.6.0만을 복원할 수 있으며 그 이전 버전은 복원할 수 없습니다. 

`NETStandard.Library`의 1.3.0 버전으로 `netstandard1.6`를 대상으로 하는 반대의 경우는 유효하지 않습니다. 더 낮은 메타패키지로 더 높은 프레임워크를 대상으로 할 수 없습니다. 더 낮은 버전의 메타패키지는 더 높은 프레임워크의 자산을 노출하지 않기 때문입니다. 메타패키지의 [버전 관리 체계]는 메타패키지가 프레임워크의 최고 버전과 일치해야 한다고 주장합니다. 버전 관리 체계 덕분에, `netstandard1.6` 자산을 포함하는 경우 `NETStandard.Library`의 첫 번째 버전은 v1.6.0입니다. 위의 예제에는 v1.3.0이 사용되지만(위 예제와 대칭을 위해), 실제로 존재하지는 않습니다.

### <a name="net-core-application"></a>.NET Core 응용 프로그램

.NET Core 응용 프로그램(TFM: `netcoreapp`) 프레임워크는 .NET Core 배포와 함께 제공되는 패키지 및 관련 API, 그리고 콘솔 응용 프로그램 모델을 나타냅니다. .NET Core 앱은 콘솔 응용 프로그램 모델을 대상으로 하기 때문에 이 프레임워크를 사용해야 하며, .NET Core에서만 실행되도록 만들어진 라이브러리도 마찬가지입니다. 이 프레임워크를 사용하면 앱과 라이브러리의 실행이 .NET Core로만 제한됩니다. 

`Microsoft.NETCore.App` 메타패키지는 `netcoreapp` 프레임워크를 대상으로 합니다. 최대 60개 라이브러리, `NETStandard.Library` 패키지에서 제공하는 최대 40개 및 추가 20개에 대한 액세스를 제공합니다. 추가 API에 대한 액세스 권한을 얻기 위해 `netcoreapp` 또는 호환 프레임워크(예: `netstandard`)를 대상으로 하는 추가 라이브러리를 참조할 수 있습니다. 

다른 `netstandard` 라이브러리에서 종속성을 충족하는 경우 `Microsoft.NETCore.App`에서 제공하는 추가 라이브러리 대부분은 또한 `netstandard`를 대상으로 합니다. 즉, `netstandard` 라이브러리 역시 해당 패키지를 종속성으로 참조할 수 있습니다. 


<!--HONumber=Nov16_HO3-->


