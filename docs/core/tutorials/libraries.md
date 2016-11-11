---
title: "플랫폼 간 도구로 라이브러리 개발"
description: "플랫폼 간 도구로 라이브러리 개발"
keywords: .NET, .NET Core
author: cartermp
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 9f6e8679-bd7e-4317-b3f9-7255a260d9cf
translationtype: Human Translation
ms.sourcegitcommit: 0882a5ca2f7814e2fd168dce40705d11b199f102
ms.openlocfilehash: caf72bec4a5d3276d1fdeafc4fa3816e5f00c296

---

# <a name="developing-libraries-with-cross-platform-tools"></a>플랫폼 간 도구로 라이브러리 개발

**도구 체인이 발전함에 따라 몇 가지 세부 정보는 변경될 수 있습니다.**

이 문서에서는 플랫폼 간 CLI 도구를 사용하여 .NET용 라이브러리를 작성하는 방법을 다룹니다.  CLI는 지원되는 운영 체제에서 작동하는 효율적인 하위 수준 환경을 제공합니다.  Visual Studio로 라이브러리를 빌드할 수 있습니다. 그러한 환경을 선호하는 경우 [Visual Studio 설명서를 참조하세요](libraries-with-vs.md).

## <a name="prerequisites"></a>필수 조건

컴퓨터에 [.NET Core SDK 및 CLI](https://www.microsoft.com/net/core)를 설치해야 합니다.

.NET Framework 버전 또는 PCL(Portable Class Libraries)을 다루는 이 문서의 섹션에서는 [.NET Framework](http://getdotnet.azurewebsites.net/)가 설치된 Windows 컴퓨터가 필요합니다.  

또한 이전.NET Framework 대상을 지원하려는 경우 [.NET 대상 플랫폼 페이지](http://getdotnet.azurewebsites.net/target-dotnet-platforms.html)에서 이전 프레임워크 버전용 타기팅/개발자 팩을 설치해야 합니다.  다음 표를 참조하세요.

| .NET Framework 버전 | 다운로드할 파일 |
| ---------------------- | ----------------- |
| 4.6.1 | .NET Framework 4.6.1 타기팅 팩 |
| 4.6 | .NET framework 4.6 타기팅 팩 |
| 4.5.2 | .NET framework 4.5.2 개발자 팩 |
| 4.5.1 | .NET framework 4.5.1 개발자 팩 |
| 4.5 | Windows 8용 Windows 소프트웨어 개발 키트 |
| 4.0 | Windows 7 및 .NET Framework 4용 Windows SDK |
| 2.0, 3.0 및 3.5 | .NET Framework 3.5 SP1 런타임(또는 Windows 8 이상 버전) |

## <a name="how-to-target-the-net-standard"></a>.NET 표준을 대상으로 지정하는 방법

.NET 표준에 익숙하지 않은 경우 자세한 내용은 [.NET 표준 라이브러리](../../standard/library.md)를 참조하세요.

이 문서에는 .NET 표준을 다양한 구현에 매핑하는 표가 있습니다.

| 플랫폼 이름 | Alias |  |  |  |  |  | | |
| :---------- | :--------- |:--------- |:--------- |:--------- |:--------- |:--------- |:--------- |:--------- |
|.NET 표준 | netstandard | 1.0 | 1.1 | 1.2 | 1.3 | 1.4 | 1.5 | 1.6 |
|.NET Core|netcoreapp|&rarr;|&rarr;|&rarr;|&rarr;|&rarr;|&rarr;|1.0|
|.NET Framework|net|&rarr;|4.5|4.5.1|4.6|4.6.1|4.6.2|4.6.3|
|Mono/Xamarin 플랫폼||&rarr;|&rarr;|&rarr;|&rarr;|&rarr;|&rarr;|*|
|범용 Windows 플랫폼|uap|&rarr;|&rarr;|&rarr;|&rarr;|10.0|||
|창|win|&rarr;|8.0|8.1|||||
|Windows Phone|wpa|&rarr;|&rarr;|8.1|||||
|Windows Phone Silverlight|wp|8.0|||||||

라이브러리 만들기 작업에서 이 표의 의미는 다음과 같습니다.

선택한 .NET 플랫폼 표준 버전에 따라 최신 API에 대한 액세스와 더 많은 .NET 플랫폼 및 Framework 버전을 대상으로 하는 기능 간에 균형을 유지하게 됩니다.  `netstandardX.X`의 버전을 선택하고(여기서 `X.X`는 버전 번호) 이를 `project.json` 파일에 추가하여 대상 지정이 가능한 플랫폼과 버전의 범위를 제어합니다.

또한 [사용할 해당 NuGet 패키지](https://www.nuget.org/packages/NETStandard.Library/)는 `NETStandard.Library` 버전 `1.6.0`입니다.  콘솔 앱에서처럼 `Microsoft.NETCore.App`의 사용을 방해하는 것은 아무것도 없지만, 일반적으로 권장되지는 않습니다.  `NETStandard.Library`에 지정되지 않은 패키지의 API가 필요한 경우 `project.json` 파일의 `dependencies` 섹션에서 `NETStandard.Library` 외에 해당 패키지를 항상 지정해야 합니다.

.NET 표준을 대상으로 할 때 요구에 따라 세 가지 기본 옵션이 있습니다.

1. .NET 표준의 최신 버전 `netstandard1.6`을 사용할 수 있습니다. 대부분의 API에 액세스하고자 하며 전체 구현에서 도달률이 떨어져도 괜찮은 경우 사용할 수 있습니다.
2. 이전 .NET 구현을 대상으로 하려면 .NET 표준의 하위 버전을 사용할 수 있습니다. 이때의 문제는 일부 최신 API에 액세스하지 못한다는 것입니다.
    
    예를 들어 .NET Framework 4.6 이상과의 호환성을 보장하려는 경우 `netstandard1.3`을 선택할 수 있습니다.

    ```json
    {
        "dependencies":{
            "NETStandard.Library":"1.6.0"
        },
        "frameworks":{
            "netstandard1.3":{}
        }
    }
    ```
    
    .NET 표준 버전은 이전 버전과 호환됩니다. 즉, `netstandard1.0` 라이브러리는 `netstandard1.1` 플랫폼 이상에서 실행됩니다.  그러나 이후 버전과는 호환되지 않습니다. 더 낮은 .NET 표준 플랫폼은 더 높은 버전을 참조할 수 없습니다.  즉, `netstandard1.0` 라이브러리는 `netstandard1.1` 이상을 대상으로 하는 라이브러리를 참조할 수 없습니다.  요구에 맞게 API와 플랫폼 지원이 올바르게 혼합된 표준 버전을 선택하세요.
    
3. .NET Framework 버전 4.0 이하를 대상으로 하거나 .NET Framework에서는 사용 가능하지만 .NET 표준에서는 사용할 수 없는 API를 사용하려는 경우(예: `System.Drawing`) 다음 섹션을 읽어보고 멀티 타기팅 방법을 알아보세요.

## <a name="how-to-target-the-net-framework"></a>.NET Framework를 대상으로 지정하는 방법

> [!NOTE]
> 다음 지침은 컴퓨터에 .NET Framework가 설치된 것으로 가정합니다.  설치된 종속성을 알아보려면 [필수 조건](#prerequisites)을 참조하세요.

여기서 사용된 .NET Framework 버전 중 일부는 더 이상 지원되지 않습니다.  지원되지 않는 버전은 [.NET Framework Support Lifecycle Policy FAQ(.NET Framework 지원 수명 주기 정책 FAQ)](https://support.microsoft.com/gp/framework_faq/en-us)를 참조하세요.

가장 많은 수의 개발자 및 프로젝트에 도달하려면 기본 대상으로 .NET Framework 4를 사용하세요. .NET Framework를 대상으로 하려면 지원할 .NET Framework 버전에 해당하는 올바른 TFM(Target Framework Moniker)을 사용하여 시작해야 합니다.

```
.NET Framework 2.0   --> net20
.NET Framework 3.0   --> net30
.NET Framework 3.5   --> net35
.NET Framework 4.0   --> net40
.NET Framework 4.5   --> net45
.NET Framework 4.5.1 --> net451
.NET Framework 4.5.2 --> net452
.NET Framework 4.6   --> net46
.NET Framework 4.6.1 --> net461
.NET Framework 4.6.2 --> net462
.NET Framework 4.6.3 --> net463
```

예를 들어 .NET Framework 4를 대상으로 하는 라이브러리의 작성 방법을 같습니다.

```json
{
    "frameworks":{
        "net40":{}
    }
}
```

됐습니다!  이는 .NET Framework 4에 대해서만 컴파일되지만 이후 버전의 .NET Framework에서 라이브러리를 사용할 수 있습니다.

## <a name="how-to-target-a-portable-class-library-pcl"></a>PCL(이식 가능한 클래스 라이브러리) 대상 지정 방법

> [!NOTE]
> 다음 지침은 컴퓨터에 .NET Framework가 설치된 것으로 가정합니다.  설치된 종속성을 알아보려면 [필수 조건](#prerequisites)을 참조하세요.

PCL 프로필을 대상으로 하는 것은 .NET 표준 또는 .NET Framework를 대상으로 하는 것보다 좀 더 까다롭습니다.  우선 대상으로 지정하는 PCL 프로필에 해당하는 NuGet 대상을 찾으려면 [이 PCL 프로필 목록을 참조](http://embed.plnkr.co/03ck2dCtnJogBKHJ9EjY/preview)하세요.

다음을 수행해야 합니다.

1. `project.json`의 `frameworks`에 아래에 새 항목(`.NETPortable,Version=v{version},Profile=Profile{profile}`)을 만듭니다. 여기서 `{version}` 및 `{profile}`은 각각 PCL 버전 번호와 프로필 번호에 해당합니다.
2. 이 새 항목에서, `frameworkAssemblies` 항목 아래에 해당 대상에 사용되는 모든 단일 어셈블리를 나열합니다.  여기에는 `mscorlib`, `System` 및 `System.Core`가 포함됩니다.
3. 멀티 타기팅하는 경우(다음 섹션 참조), 대상 항목 아래에서 각 대상에 대한 종속성을 명시적으로 나열해야 합니다.  전역 `dependencies` 항목은 더 이상 사용할 수 없습니다.

다음은 PCL Profile 328을 대상으로 하는 예제입니다. 프로필 328은 .NET 표준 1.4, .NET Framework 4, Windows 8, Windows Phone 8.1, Windows Phone Silverlight 8.1 및 Silverlight 5를 지원합니다.

```json
{
    "frameworks":{
        ".NETPortable,Version=v4.0,Profile=Profile328":{
            "frameworkAssemblies":{
                "mscorlib":"",
                "System":"",
                "System.Core":""
            }
        }
    }
}
```

*project.json* 파일의 프레임워크로 PCL Profile 328을 포함하는 프로젝트를 빌드할 경우 */bin/debug* 폴더에 다음의 하위 폴더가 생깁니다.

```
portable-net40+sl50+netcore45+wpa81+wp8/
```

이 폴더에는 라이브러리를 실행하는 데 필요한 `.dll` 파일이 포함됩니다.

## <a name="how-to-multitarget"></a>멀티 타기팅 방법

> [!NOTE]
> 다음 지침에서는 컴퓨터에 .NET Framework가 설치된 것으로 가정합니다.  설치해야 할 종속성 및 다운로드할 위치에 대해 알아보려면 [필수 조건](#prerequisites) 섹션을 참조하세요.

프로젝트가 .NET Framework 및 .NET Core를 모두 지원하는 경우 이전 버전의 .NET Framework를 대상으로 해야 할 수 있습니다. 이 시나리오에서, 최신 대상에 최신 API 및 언어 구조를 사용하려는 경우 코드에 `#if` 지시문을 사용하세요. 각각의 경우에 필요한 서로 다른 API를 추가하려면 대상으로 지정하는 각 플랫폼에 대해 `project.json file`에서 서로 다른 패키지와 종속성을 추가해야 할 수 있습니다.

예를 들어 HTTP를 통해 네트워킹 작업을 수행하는 라이브러리가 있다고 가정해 보겠습니다. .NET 표준 및 .NET Framework 버전 4.5 이상 경우 `System.Net.Http` 네임스페이스의 `HttpClient` 클래스를 사용할 수 있습니다. 그러나 이전 버전의 .NET Framework에는 `HttpClient` 클래스가 없으므로, 이에 대해 `System.Net` 네임스페이스의 `WebClient` 클래스를 사용할 수 있습니다.

따라서는 `project.json` 파일은 다음과 같이 표시될 수 있습니다.

```json
{
    "frameworks":{
        "net40":{
            "frameworkAssemblies": {
                "System.Net":"",
                "System.Text.RegularExpressions":""
            }
        },
        "net452":{
            "frameworkAssemblies":{
                "System.Net":"",
                "System.Net.Http":"",
                "System.Text.RegularExpressions":"",
                "System.Threading.Tasks":""
            }
        },
        "netstandard1.6":{
            "dependencies": {
                "NETStandard.Library":"1.6.0",
            }
        }
    }
}
```

.NET Framework 어셈블리는 `net40` 및 `net452` 대상에서 명시적으로 참조되어야 하며, NuGet 참조도 `netstandard1.6` 대상에 명시적으로 나열되어야 합니다.  멀티 타기팅 시나리오에서 필요합니다.

이제 소스 파일의 `using` 문에서 다음과 같이 조정할 수 있습니다.

```csharp
#if NET40
// This only compiles for the .NET Framework 4 targets
using System.Net;
#else
// This compiles for all other targets
using System.Net.Http;
using System.Threading.Tasks;
#endif
```

빌드 시스템은 `#if` 지시문에 사용된 다음의 전처리기 기호를 인식합니다.

```
.NET Framework 2.0   --> NET20
.NET Framework 3.5   --> NET35
.NET Framework 4.0   --> NET40
.NET Framework 4.5   --> NET45
.NET Framework 4.5.1 --> NET451
.NET Framework 4.5.2 --> NET452
.NET Framework 4.6   --> NET46
.NET Framework 4.6.1 --> NET461
.NET Framework 4.6.2 --> NET462
.NET Standard 1.0    --> NETSTANDARD1_0
.NET Standard 1.1    --> NETSTANDARD1_1
.NET Standard 1.2    --> NETSTANDARD1_2
.NET Standard 1.3    --> NETSTANDARD1_3
.NET Standard 1.4    --> NETSTANDARD1_4
.NET Standard 1.5    --> NETSTANDARD1_5
.NET Standard 1.6    --> NETSTANDARD1_6
```

소스 중간에 `#if` 지시문을 사용하여 조건부로 해당 라이브러리를 사용할 수 있습니다. 예:

```csharp
    public class Library
    {
#if NET40
        private readonly WebClient _client = new WebClient();
        private readonly object _locker = new object();
#else
        private readonly HttpClient _client = new HttpClient();
#endif

#if NET40
        // .NET Framework 4.0 does not have async/await
        public string GetDotNetCount()
        {
            string url = "http://www.dotnetfoundation.org/";
          
            var uri = new Uri(url);
            
            string result = "";
            
            // Lock here to provide thread-safety.
            lock(_locker)
            {
                result = _client.DownloadString(uri);
            }
            
            int dotNetCount = Regex.Matches(result, ".NET").Count;
            
            return $"Dotnet Foundation mentions .NET {dotNetCount} times!";
        }
#else
        // .NET 4.5+ can use async/await!
        public async Task<string> GetDotNetCountAsync()
        {
            string url = "http://www.dotnetfoundation.org/";
            
            // HttpClient is thread-safe, so no need to explicitly lock here
            var result = await _client.GetStringAsync(url);
            
            int dotNetCount = Regex.Matches(result, ".NET").Count;
            
            return $"dotnetfoundation.orgmentions .NET {dotNetCount} times in its HTML!";
        }
#endif
    }
```

`net40`, `net45` 및 `netstandard1.6`을 *project.json* 파일에 프레임워크로 포함하는 프로젝트를 빌드하면, */bin/debug* 폴더에 이러한 하위 폴더가 생성됩니다.

```
net40/
net45/
netstandard1.6/
```

### <a name="but-what-about-multitargeting-with-portable-class-libraries"></a>하지만 이식 가능한 클래스 라이브러리를 이용한 멀티 타기팅은 어떻습니까?

PCL 대상과 교차 컴파일을 수행하려는 경우 PCL 대상의 `buildOptions` 아래에 있는 `project.json` 파일에 빌드 정의를 추가해야 합니다.  그러면 전처리 기호로 빌드 정의를 사용하는 `#if` 지시문을 소스에서 사용할 수 있습니다.

예를 들어 [PCL Profile 328](http://embed.plnkr.co/03ck2dCtnJogBKHJ9EjY/preview)(.NET Framework 4, Windows 8, Windows Phone Silverlight 8, Windows Phone 8.1, Silverlight 5)을 대상으로 지정하려는 경우, 교차 컴파일 시 이를 "PORTABLE328"로 참조할 수 있습니다.  간단히 이를 `project.json` 파일에 `buildOptions` 특성으로 추가할 수 있습니다.

```json
{
    "frameworks":{
        "netstandard1.6":{
           "dependencies":{
                "NETStandard.Library":"1.6.0",
            }
        },
        ".NETPortable,Version=v4.0,Profile=Profile328":{
            "buildOptions": {
                "define": [ "PORTABLE328" ]
            },
            "frameworkAssemblies":{
                "mscorlib":"",
                "System":"",
                "System.Core":"",
                "System.Net"
            }
        }
    }
}

```

이제 해당 대상에 대해 조건부로 컴파일할 수 있습니다.

```csharp
#if !PORTABLE328
using System.Net.Http;
using System.Threading.Tasks;
// Potentially other namespaces which aren't compatible with Profile 328
#endif
```

이제 컴파일러에서 `PORTABLE328`을 인식하므로, 컴파일러에 의해 생성된 PCL Profile 328 라이브러리에 `System.Net.Http` 또는 `System.Threading.Tasks`가 포함되지 않습니다.

PCL Profile 328 및 `netstandard1.6`을 *project.json* 파일에 프레임워크로 포함하는 프로젝트를 빌드하면 */bin/debug* 폴더에 이러한 하위 폴더가 생성됩니다.

```
portable-net40+sl50+netcore45+wpa81+wp8/
netstandard1.6/
```

## <a name="how-to-use-native-dependencies"></a>기본 종속성 사용 방법

기본 `.dll` 파일에 종속된 라이브러리를 작성할 수 있습니다.  이러한 라이브러리를 작성하는 경우 두 가지 옵션이 있습니다.

1. `.dll`을 `project.json`에서 직접 참조합니다.
2. `.dll`을 자체 NuGet 패키지에 패키지하고 이에 종속됩니다.

첫 번째 옵션에서는 `project.json` 파일에 다음을 포함해야 합니다.

1. `buildOptions` 섹션에서 `allowUnsafe`를 `true`로 설정합니다.
2. `runtimes` 섹션에서 [RID(런타임 식별자)](../rid-catalog.md)를 지정합니다.
3. 참조하는 기본 `.dll` 파일에 대한 경로를 지정합니다.

다음은 Windows에서 실행되는 프로젝트의 루트 디렉터리에 있는 기본 `.dll` 파일에 대한 `project.json` 예제입니다.

```json
{
    "buildOptions":{
        "allowUnsafe":true
    },
    "runtimes":{
        "win10-x64":{}
    },
    "packOptions":{
        "files":{
            "mappings":{
                "runtimes/win10-x64/native":{
                    "includeFiles":[ "native-lib.dll"]
                }
            }            
        }
    }
}
```

라이브러리를 패키지로 배포하는 경우 `.dll` 파일을 프로젝트의 루트 수준에 두는 것이 좋습니다.

두 번째 옵션에서는 `.dll` 파일에서 NuGet 패키지를 빌드하고, NuGet 또는 MyGet 피드에 호스트하고, 이에 직접 종속되어야 합니다.  여전히 `project.json`의 `buildOptions` 섹션에서 `allowUnsafe`를 `true`로 설정해야 합니다.  다음은 이에 대한 예제입니다(`MyNativeLib`가 버전 `1.2.0`의 NuGet 패키지라고 가정).

```json
{
    "buildOptions":{
        "allowUnsafe":true
    },
    "dependencies":{
        "MyNativeLib":"1.2.0"
    }
}
```

플랫폼 간 기본 바이너리를 패키지하는 예제를 보려면 [ASP.NET Libuv 패키지](https://github.com/aspnet/libuv-package) 및 [KestrelHttpServer의 해당 참조](https://github.com/aspnet/KestrelHttpServer/blob/1.0.0/src/Microsoft.AspNetCore.Server.Kestrel/project.json#L19)를 확인하세요.

## <a name="how-to-test-libraries-on-net-core"></a>.NET Core에서 라이브러리를 테스트하는 방법

플랫폼 간에 테스트할 수 있는 기능이 중요합니다.  .NET Core 프로젝트에도 사용되는 테스트 도구인 [xUnit](http://xunit.github.io/)을 사용하는 것이 가장 쉬운 방법입니다.  테스트 프로젝트로 솔루션을 설정하는 방법은 [솔루션 구조](#structuring-a-solution)에 따라 달라집니다.  다음 예제에서는 모든 소스 프로젝트가 최상위 `/src` 폴더에 있고 모든 테스트 프로젝트가 최상위 `/test` 폴더에 있는 것으로 가정합니다.

1. 테스트 프로젝트가 어디에 있는지를 아는 솔루션 수준에 `global.json` 파일이 있어야 합니다.
    
    ```json
    {
        "projects":[ "src", "test"]
    }
    ```
    
    그러면 솔루션 폴더 구조가 다음과 같이 표시됩니다.
    
    ```
    /SolutionWithSrcAndTest
    |__global.json
    |__/src
    |__/test
    ```
    
2. `/test` 폴더 아래에 프로젝트 폴더를 만들고, 새 프로젝트 폴더에 `project.json` 파일을 만들어 새 테스트 프로젝트를 만듭니다.  `project.json` 파일을 만들려면 `dotnet new` 명령을 실행하고 그 이후에 `project.json` 파일을 수정할 수 있습니다.  파일에 다음이 있어야 합니다.

   * `netcoreapp1.0` - `frameworks` 아래에 유일한 항목으로 나열
   * `Microsoft.NETCore.App` 버전 `1.0.0`에 대한 참조
   * XUnit 버전 `2.2.0-beta2-build3300`에 대한 참조
   * `dotnet-test-xunit` 버전에 대한 참조`2.2.0-preview2-build1029`
   * 테스트 중인 라이브러리에 대한 프로젝트 참조
   * `"testRunner":"xunit"` 항목
   
   다음은 이에 대한 예제입니다(`LibraryUnderTest` 버전 `1.0.0`은 테스트 중인 라이브러리).
   
   ```json
   {
        "testRunner":"xunit",
        "dependencies":{
            "LibraryUnderTest":{
                "version":"1.0.0",
                "target":"project"
            },
            "Microsoft.NETCore.App":{
                "version":"1.0.0",
                "type":"platform"
            },
            "xunit":"2.2.0-beta2-build3300",
            "dotnet-test-xunit":"2.2.0-preview2-build1029",
        },
        "frameworks":{
            "netcoreapp1.0":{}
        }
   }
   ```
3. `dotnet restore`를 실행하여 패키지를 복원합니다.  아직 패키지를 복원하지 않은 경우 솔루션 수준에서 이를 수행해야 합니다.

4. 테스트 프로젝트로 이동하여 `dotnet test`로 테스트를 실행합니다.

    ```
    $ cd path-to-your-test-project
    $ dotnet test
    ```

됐습니다!  이제 명령줄 도구를 사용하여 모든 플랫폼 라이브러리를 테스트할 수 있습니다.  이제 모든 것이 설정되어 계속해서 테스트하려는 경우 라이브러리 테스트는 매우 간단합니다.

1. 라이브러리를 변경합니다.
2. 명령줄의 테스트 디렉터리에서 `dotnet test` 명령으로 테스트를 실행합니다.

`dotnet test` 명령을 호출하면 자동으로 코드가 다시 빌드됩니다.

새 종속성을 추가할 때 언제든지 명령줄에서 `dotnet restore`를 실행하면 됩니다.

## <a name="how-to-use-multiple-projects"></a>여러 프로젝트를 사용하는 방법

더 큰 라이브러리의 경우 일반적으로 서로 다른 프로젝트에 기능을 배치해야 합니다.

자연스러운 C# 및 F#에 사용할 수 있는 라이브러리를 빌드하려 한다고 가정해 보겠습니다.  즉, 라이브러리의 소비자가 C# 및 F#에 자연스러운 방식으로 라이브러리를 사용한다는 의미입니다.  예를 들어 C#에서 다음과 같이 라이브러리를 사용할 수 있습니다.

```csharp
var convertResult = await AwesomeLibrary.ConvertAsync(data);
var result = AwesomeLibrary.Process(convertResult);
```  

F#에서는 다음과 같습니다.

```fsharp
let result =
    data
    |> AwesomeLibrary.convertAsync 
    |> Async.RunSynchronously 
    |> AwesomeLibrary.process
```

이와 같은 사용 시나리오는, 액세스하는 API가 C# 및 F#에 대해 다른 구조를 가지고 있어야 한다는 뜻입니다.  이를 수행하는 일반적인 방법은 Core 프로젝트로 호출하는 API 계층을 정의하는 C# 및 F# 프로젝트에서 라이브러리의 모든 논리를 해당 Core 프로젝트로 팩터링하는 것입니다.  섹션의 나머지 부분에서는 다음 이름을 사용합니다.

* **AwesomeLibrary.Core** - 라이브러리에 대한 모든 논리를 포함하는 Core 프로젝트
* **AwesomeLibrary.CSharp** - C에서 사용하기 위한 공용 API가 포함된 프로젝트#
* **AwesomeLibrary.FSharp** - F에서 사용하기 위한 공용 API가 포함된 프로젝트#

### <a name="projecttoproject-referencing"></a>프로젝트 간 참조

프로젝트를 참조하는 가장 좋은 방법은 다음을 수행하는 것입니다.

1. 참조할 프로젝트가 디스크의 상위 폴더에 대해 올바른 이름을 가지고 있는지 확인합니다.  이 이름은 프로젝트를 참조하는 데 사용됩니다.
2. `"target":"project"`를 지정하는 사용 프로젝트의 `project.json` 파일에서 (1)의 이름을 참조합니다.

**AwesomeLibrary.CSharp** 및 **AwesomeLibrary.FSharp** 모두에 대한 `project.json` 파일은 이제 **AwesomeLibrary.Core**를 `project` 대상으로 참조해야 합니다.  멀티 타기팅하지 않는 경우 전역 `dependencies` 항목을 사용할 수 있습니다.

```json
{
    "dependencies":{
        "AwesomeLibrary.Core":{
            "target":"project"
        }
    }
}
```

멀티 타기팅하는 경우 전역 `dependencies` 항목을 사용하지 못할 수 있으며, 대상 수준 `dependencies` 항목에서 **AwesomeLibrary.Core**를 참조해야 할 수 있습니다.  `netstandard1.6`을 대상으로 지정하는 경우 다음과 같이 할 수 있습니다.

```json
{
    "frameworks":{
        "netstandard1.6":{
            "dependencies":{
                "AwesomeLibrary.Core":{
                    "target":"project"
                }
            }
        }
    }
}
```

### <a name="structuring-a-solution"></a>솔루션 구성

다중 프로젝트 솔루션의 또 다른 중요한 측면은 전체 프로젝트 구조를 올바르게 설정하는 것입니다. 다중 프로젝트 라이브러리를 구성하려면 최상위 `/src` 및 `/test` 폴더를 사용해야 합니다.

```
/AwesomeLibrary
|__global.json
|__/src
   |__/AwesomeLibrary.Core
      |__Source Files
      |__project.json
   |__/AwesomeLibrary.CSharp
      |__Source Files
      |__project.json
   |__/AwesomeLibrary.FSharp
      |__Source Files
      |__project.json
|__/test
   |__/AwesomeLibrary.Core.Tests
      |__Test Files
      |__project.json
   |__/AwesomeLibrary.CSharp.Tests
      |__Test Files
      |__project.json
   |__/AwesomeLibrary.FSharp.Tests
      |__Test Files
      |__project.json
```

이 솔루션의 `global.json` 파일은 다음과 같습니다.

```json
{
    "projects":["src", "test"]
}
```

이 방법은 `dotnet new` 명령에서 프로젝트 템플릿에 의해 설정된 것과 동일한 패턴을 따릅니다. 여기서 모든 프로젝트는 `/src` 디렉터리에 배치되고 모든 테스트는 `/test` 디렉터리에 배치됩니다.

다음은 패키지를 복원하고 전체 프로젝트를 빌드 및 테스트하는 방법입니다.

```
$ dotnet restore
$ cd src/AwesomeLibrary.FSharp
$ dotnet build
$ cd ../AwesomeLibrary.CSharp
$ dotnet build
$ cd ../../test/AwesomeLibrary.Core.Tests
$ dotnet test
$ cd ../AwesomeLibrary.CSharp.Tests
$ dotnet test
$ cd ../AwesomeLibrary.FSharp.Tests
$ dotnet test
```

됐습니다!



<!--HONumber=Nov16_HO1-->


