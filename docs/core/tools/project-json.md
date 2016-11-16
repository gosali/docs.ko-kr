---
title: "project.json 참조"
description: "project.json 참조"
keywords: .NET, .NET Core, project.json
author: aL3891
ms.author: mairaw
manager: wpickett
ms.date: 09/30/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 3aef32bd-ee2a-4e24-80f8-a2b615e0336d
translationtype: Human Translation
ms.sourcegitcommit: b20713600d7c3ddc31be5885733a1e8910ede8c6
ms.openlocfilehash: f870dc601a1df5dd663cd165bc19f70df9aa57f4

---

# <a name="projectjson-reference"></a>project.json 참조

project.json 파일은 프로젝트 메타데이터, 컴파일 정보 및 종속성을 정의하기 위해 .NET Core 프로젝트에 사용됩니다. 이 참조 항목에는 project.json 파일에서 정의할 수 있는 모든 속성의 목록이 표시됩니다.

> [!NOTE]
> 이후 릴리스에서 .NET Core 도구는 project.json에서 MSBuild 기반 프로젝트로 이동하고 있습니다. 도구가 릴리스될 때 프로젝트를 MSBuild로 변환할 경로가 마련될 것이므로 새 .NET Core 프로젝트에는 여전히 project.json 파일을 사용하는 것이 좋습니다.
>
> 자세한 내용은 .NET 블로그의 [Changes to project.json(project.json으로 변경)](https://blogs.msdn.microsoft.com/dotnet/2016/05/23/changes-to-project-json/) 게시물 및 [Using MSBuild to build .NET Core projects(MSBuild를 사용하여 .NET Core 프로젝트 빌드)](../tutorials/target-dotnetcore-with-msbuild.md) 항목을 참조하세요.

## <a name="overview"></a>개요

```
{
    "name": String,
    "version": String,
    "description": String,
    "copyright": String,
    "title": String,
    "entryPoint": String,
    "testRunner": String,
    "authors": String[],
    "language": String,
    "embedInteropTypes": Boolean,
    "preprocess": String or String[],
    "shared": String or String[],
    "dependencies": Object {
        version: String,
        type: String,
        target: String,
        include: String,
        exclude: String,
        suppressParent: String
    },
    "tools": Object,
    "scripts": Object,
    "buildOptions": Object {
        "define": String[],
        "nowarn": String[],
        "additionalArguments": String[],
        "warningsAsErrors": Boolean,
        "allowUnsafe": Boolean,
        "emitEntryPoint": Boolean,
        "optimize": Boolean,
        "platform": String,
        "languageVersion": String,
        "keyFile": String,
        "delaySign": Boolean,
        "publicSign": Boolean,
        "debugType": String,
        "xmlDoc": Boolean,
        "preserveCompilationContext": Boolean,
        "outputName": String,
        "compilerName": String,
        "compile": Object {
            "include": String or String[],
            "exclude": String or String[],
            "includeFiles": String or String[],
            "excludeFiles": String or String[],
            "builtIns": Object,
            "mappings": Object
        },
        "embed": Object {
            "include": String or String[],
            "exclude": String or String[],
            "includeFiles": String or String[],
            "excludeFiles": String or String[],
            "builtIns": Object,
            "mappings": Object
        },
        "copyToOutput": Object {
            "include": String or String[],
            "exclude": String or String[],
            "includeFiles": String or String[],
            "excludeFiles": String or String[],
            "builtIns": Object,
            "mappings": Object
        }
    },
    "publishOptions": Object {
        "include": String or String[],
        "exclude": String or String[],
        "includeFiles": String or String[],
        "excludeFiles": String or String[],
        "builtIns": Object,
        "mappings": Object
    },
    "runtimeOptions": Object {
        "configProperties": Object {
            "System.GC.Server": Boolean,
            "System.GC.Concurrent": Boolean,
            "System.GC.RetainVM": Boolean,
            "System.Threading.ThreadPool.MinThreads": Integer,
            "System.Threading.ThreadPool.MaxThreads": Integer
        },
        "framework": Object {
            "name": String,
            "version": String,
        },
        "applyPatches": Boolean
    },
    "packOptions": Object {
        "summary": String,
        "tags": String[],
        "owners": String[],
        "releaseNotes": String,
        "iconUrl": String,
        "projectUrl": String,
        "licenseUrl": String,
        "requireLicenseAcceptance": Boolean,
        "repository": Object {
            "type": String,
            "url": String
        },
        "files": Object {
            "include": String or String[],
            "exclude": String or String[],
            "includeFiles": String or String[],
            "excludeFiles": String or String[],
            "builtIns": Object,
            "mappings": Object
        }
    },
    "analyzerOptions": Object {
        "languageId": String
    },
    "configurations": Object,
    "frameworks": Object {
        "dependencies": Object {
            version: String,
            type: String,
            target: String,
            include: String,
            exclude: String,
            suppressParent: String
        },        
        "frameworkAssemblies": Object,
        "wrappedProject": String,
        "bin": Object {
            assembly: String
        }
    },
    "runtimes": Object,
    "userSecretsId": String
}
```

## <a name="name"></a>name
형식: String

어셈블리 이름은 물론 패키지의 이름에도 사용되는 프로젝트의 이름. 이 속성을 지정하지 않으면 최상위 폴더 이름이 사용됩니다.

예:

```json
{
    "name": "MyLibrary"
}
```

## <a name="version"></a>버전
형식: String

NuGet 패키지에도 사용되는 프로젝트의 [Semver](http://semver.org/spec/v1.0.0.html) 버전.

예:

```json
{
    "version": "1.0.0-*"
}
```

## <a name="description"></a>설명
형식: String

프로젝트의 긴 설명. 어셈블리 속성에 사용됩니다.

예:

```json
{
    "description": "This is my library and it's really great!"
}
```

## <a name="copyright"></a>저작권
형식: String

프로젝트의 저작권 정보. 어셈블리 속성에 사용됩니다.

예:

```json
{
    "copyright": "Fabrikam 2016"
}
```

## <a name="title"></a>제목
형식: String

프로젝트의 이름에는 `name` 속성 사용 시에는 허용되지 않는 공백과 특수 문자를 포함할 수 있습니다. 어셈블리 속성에 사용됩니다.

예:

```json
{
    "title": "My Library"
}
```

## <a name="entrypoint"></a>entryPoint
형식: String

프로젝트에 대한 entrypoint 메서드. 기본적으로 `Main`입니다.

예를 들면 다음과 같습니다.

```json
{
    "entryPoint": "ADifferentMethod"
}
```
    
## <a name="testrunner"></a>testRunner
형식: String

이 프로젝트와 사용할 Test Runner의 이름(예: [NUnit](http://nunit.org/) 또는 [xUnit](http://xunit.github.io/)). 또한 이를 설정하면 프로젝트가 테스트 프로젝트로 표시됩니다.

예:

```json
{
    "testRunner": "NUnit"
}
```

## <a name="authors"></a>authors
형식: String[]

프로젝트 작성자의 이름이 포함된 문자열의 배열.

예:

```json
{
    "authors": ["Anne", "Bob"]
}
```

## <a name="language"></a>language
형식: String

프로젝트의 언어(사람). "중립 언어" 컴파일러 인수에 해당합니다.

예:

```json
{
    "language": "en-US"
}
```

## <a name="embedinteroptypes"></a>embedInteropTypes
형식: Boolean

`true` - 어셈블리에 COM interop 형식을 포함할 경우, `false` - 아닌 경우. 

예를 들면 다음과 같습니다.

```json
{
    "embedInteropTypes": true
}
```

## <a name="preprocess"></a>preprocess
형식: 와일드카드 사용 패턴의 String 또는 String[]

전처리에 포함되어 있는 파일을 지정합니다.

예:

```json
{
    "preprocess": "compiler/preprocess/**/*.cs"
}
```

## <a name="shared"></a>공유
형식: 와일드카드 사용 패턴의 String 또는 String[]

어떤 파일이 공유되는지를 지정하며, 라이브러리 내보내기에 사용됩니다.

예:

```json
{
    "shared": "shared/**/*.cs"
}
```

## <a name="dependencies"></a>종속성
형식: Object

프로젝트의 패키지 종속성을 정의하는 개체. 이 개체의 각 키는 패키지의 이름이며 각 값은 버전 관리 정보를 포함합니다.
자세한 내용은 NuGet 설명서 사이트의 [종속성 확인](https://docs.nuget.org/ndocs/consume-packages/dependency-resolution#dependency-resolution-in-nuget-3-x)을 참조하세요.

예:

```json
    "dependencies": {
        "System.Reflection.Metadata": "1.3.0",
        "Microsoft.Extensions.JsonParser.Sources": {
          "type": "build",
          "version": "1.0.0-rc2-20221"
        },
        "Microsoft.Extensions.HashCodeCombiner.Sources": {
          "type": "build",
          "version": "1.1.0-alpha1-21456"
        },
        "Microsoft.Extensions.DependencyModel": "1.0.0-*"
    }
```

### <a name="version"></a>버전
형식: String

종속성의 버전 또는 버전 범위를 지정합니다. \* 와일드카드를 사용하여 [부동 종속성 버전](https://docs.nuget.org/ndocs/consume-packages/dependency-resolution#floating-versions)을 지정합니다.

예:

```json
"dependencies": { 
    "Newtonsoft.Json": { 
        "version": "9.0.1" 
    }
}
```

### <a name="type"></a>type
형식: String

종속성의 유형을 지정합니다. 값은 `default`, `build` 또는 `platform` 중 하나일 수 있습니다. 기본값은 `default`입니다.

`build` - 개발 종속성이라고 하며 빌드 시간에만 사용됩니다. 즉, 패키지를 출력 `.nupkg` 파일에 종속성으로서 게시하거나 추가해서는 안 됩니다. 그 효과는 [supressParent](#supressparent)를 `all`로 설정한 것과 동일합니다.

`platform` - 공유 SDK를 참조합니다. 자세한 내용은 [.NET Core 응용 프로그램 배포](../deploying/index.md) 항목의 "타사 종속성으로 프레임워크 종속 배포를 배포하기" 섹션을 참조하세요.

예를 들면 다음과 같습니다.

```json
 "dependencies": {
   "Microsoft.NETCore.App": {
     "type": "platform",
     "version": "1.0.0"
   }
 }
```

### <a name="target"></a>target
형식: String

`project` 또는 `package`만 일치하도록 종속성을 제한합니다.

### <a name="include"></a>include
형식: String

종속성 패키지의 부분을 포함합니다. `all`, `runtime`, `compile`, `build`, `contentFiles`, `native`, `analyzers` 또는 `none` 플래그 중 하나 이상을 사용할 수 있습니다.
여러 플래그는 쉼표로 구분된 목록에 의해 정의됩니다.
자세한 내용은 NuGet 리포지토리에 대한 [종속성 패키지 자산 관리](https://github.com/NuGet/Home/wiki/%5BSpec%5D-Managing-dependency-package-assets) 사양을 참조하세요.

예:

```json
{
  "dependencies": {
    "packageA": {
      "version": "1.0.0",
      "include": "runtime"
    }
  }
}
```

### <a name="exclude"></a>Exclude
형식: String

종속성 패키지의 부분을 제외합니다. `all`, `runtime`, `compile`, `build`, `contentFiles`, `native`, `analyzers` 또는 `none` 플래그 중 하나 이상일 수 있습니다.
여러 플래그는 쉼표로 구분된 목록에 의해 정의됩니다.
자세한 내용은 NuGet 리포지토리에 대한 [종속성 패키지 자산 관리](https://github.com/NuGet/Home/wiki/%5BSpec%5D-Managing-dependency-package-assets) 사양을 참조하세요.

예:

```json
{
  "dependencies": {
    "packageA": {
      "version": "1.0.0",
      "exclude": "contentFiles"
    }
  }
}
```

### <a name="supressparent"></a>supressParent
형식: String

프로젝트 소비자에 대한 추가 예외를 정의합니다. `all`, `runtime`, `compile`, `build`, `contentFiles`, `native`, `analyzers` 또는 `none` 플래그 중 하나일 수 있습니다.
자세한 내용은 NuGet 리포지토리에 대한 [종속성 패키지 자산 관리](https://github.com/NuGet/Home/wiki/%5BSpec%5D-Managing-dependency-package-assets) 사양을 참조하세요.

```json
{
  "dependencies": {
    "packageA": {
      "version": "1.0.0",
      "suppressParent": "compile"
    }
  }
}
```

## <a name="tools"></a>도구
형식: Object

참조가 아니라 현재 프로젝트의 도구로 사용되는 패키지 종속성을 정의하는 개체. 여기에 정의된 패키지는 빌드 프로세스 중에 실행되는 스크립트에서는 사용할 수 있지만, 프로젝트 자체의 코드에는 액세스할 수 없습니다. 예를 들어 도구는 코드 생성기를 포함하거나 압축과 관련된 작업을 수행하는 빌드 후 도구를 포함할 수 있습니다.

예:

```json
{
    "tools": {
    "MyObfuscator": "1.2.4"
    }
}
```

## <a name="scripts"></a>스크립트
형식: Object

빌드 프로세스 중에 실행되는 스크립트를 정의하는 개체입니다. 이 개체의 각 키는 빌드에서 스크립트가 실행되는 위치를 식별합니다. 각 값은 스크립트를 실행하는 문자열 또는 순서대로 실행할 스크립트를 포함하는 문자열의 배열입니다.
다음 이벤트가 지원됩니다.
* precompile
* postcompile
* prepublish
* postpublish

예:

```json
{
    "scripts": {
        "precompile": "generateCode.cmd",
        "postcompile": [ "obfuscate.cmd", "removeTempFiles.cmd" ]
    }
}
```

## <a name="buildoptions"></a>buildOptions
형식: Object

컴파일의 여러 부분을 제어하는 속성이 포함된 개체. 유효한 속성은 다음과 같습니다. [프레임워크 섹션](#frameworks)에 설명된 대로 대상 프레임워크당 지정할 수도 있습니다.

예:

```json
    "buildOptions": {
      "allowUnsafe": true,
      "emitEntryPoint": true
    }
```

### <a name="define"></a>define
형식: String[]

코드의 조건부 컴파일에 사용할 수 있는 "DEBUG" 또는 "TRACE" 등의 정의 목록.

예:

```json
{
    "buildOptions": {
        "define": ["TEST", "OTHERCONDITION"]
    }
}
```

### <a name="nowarn"></a>nowarn
형식: String[]

무시할 경고의 목록.

예를 들면 다음과 같습니다.

```json
{
    "buildOptions": {
        "nowarn": ["CS0168", "CS0219"]
    }
}
```

다음 경고를 무시합니다. `The variable 'var' is assigned but its value is never used` 및 `The variable 'var' is assigned but its value is never used`

### <a name="additionalarguments"></a>additionalArguments
형식: String[]

컴파일러에 전달되는 추가 인수 목록.

예:

```json
{
    "buildOptions": {
        "additionalArguments": ["/parallel", "/nostdlib"]
    }
}
```

### <a name="warningsaserrors"></a>warningsAsErrors
형식: Boolean

`true` - 경고를 오류로 처리하는 경우, `false` - 아닌 경우. 기본값은 `false`입니다.

예:

```json
{
    "buildOptions": {
        "warningsAsErrors": true
    }
}
```

### <a name="allowunsafe"></a>allowUnsafe
형식: Boolean

`true` - 이 프로젝트에 안전하지 않은 코드를 허용하는 경우, `false` - 아닌 경우. 기본값은 `false`입니다.

예:

```json
{
    "buildOptions": {
        "allowUnsafe": true
    }
}
```

### <a name="emitentrypoint"></a>emitEntryPoint
형식: Boolean

`true` - 실행 파일을 만드는 경우, `false` - 라이브러리를 생성하는 경우. 기본값은 `false`입니다.

예:

```json
{
    "buildOptions": {
        "emitEntryPoint": true
    }
}
```

### <a name="optimize"></a>optimize
형식: Boolean

`true` - 이 프로젝트의 코드를 최적화하기 위해 컴파일러를 사용하도록 설정하는 경우, `false` - 아닌 경우. 기본값은 `false`입니다.

예:

```json
{
    "buildOptions": {
        "optimize": true
    }
}
```

### <a name="platform"></a>platform
형식: String

AnyCpu, x86 또는 x64 같은 대상 플랫폼의 이름.

예:

```json
{
    "buildOptions": {
        "platform": "x64"
    }
}
```

### <a name="languageversion"></a>languageVersion
형식: String

컴파일러에서 사용되는 언어의 버전: ISO-1, ISO-2, 3, 4, 5, 6 또는 기본값입니다.

예:

```json
{
    "buildOptions": {
        "languageVersion": "5"
    }
}
```

### <a name="keyfile"></a>keyFile
형식: String

이 어셈블리 서명에 사용되는 키 파일에 대한 경로.

예:

```json
{
    "buildOptions": {
        "keyFile": "../keyfile.snk"
    }
}
```

### <a name="delaysign"></a>delaySign
형식: Boolean

`true` - 서명을 지연하는 경우, `false` - 아닌 경우. 기본값은 `false`입니다.

예:

```json
{
    "buildOptions": {
        "delaySign": true
    }
}
```

### <a name="publicsign"></a>publicSign
형식: Boolean

`true` - 결과 어셈블리의 서명을 사용하도록 설정하는 경우, `false` - 아닌 경우. 기본값은 `false`입니다.

예:

```json
{
    "buildOptions": {
        "publicSign": true
    }
}
```

### <a name="debugtype"></a>debugType
형식: String

생성할 기호 파일(PDB 파일) 형식을 나타냅니다. 옵션은 "portable"(.NET Core 프로젝트) 또는 "full"(기존의 Windows 전용 PDB 파일)입니다.

예:

```json
{
    "buildOptions": {
        "debugType": "portable"
    }
}
```

### <a name="xmldoc"></a>xmlDoc
형식: Boolean

`true` - 소스 코드의 삼중 슬래시 주석에서 XML 문서를 생성하는 경우, `false` - 아닌 경우. 기본값은 `false`입니다.

예:

```json
{
    "buildOptions": {
        "xmlDoc": true
    }
}
```

### <a name="preservecompilationcontext"></a>preserveCompilationContext
형식: Boolean

`true` - 참조 어셈블리 및 기타 컨텍스트 데이터를 유지하여 런타임 컴파일을 허용하는 경우, `false` - 아닌 경우. 기본값은 `false`입니다.

예:

```json
{
    "buildOptions": {
        "preserveCompilationContext": true
    }
}
```

### <a name="outputname"></a>outputName
형식: String

출력 파일의 이름을 변경합니다. 

예:

```json
{
    "buildOptions": {
        "outputName": "MyApp"
    }
}
```

### <a name="compilername"></a>compilerName
형식: String

이 프로젝트에 사용되는 컴파일러의 이름. 기본적으로 `csc`입니다. 현재 `csc`(C# 컴파일러) 또는 `fsc`(F# 컴파일러)가 지원됩니다.
 
예:

```json
{
    "compilerName": "fsc"
}
```
    
### <a name="compile"></a>compile
형식: Object

컴파일 구성에 대한 속성을 포함하는 개체.

#### <a name="include"></a>include
형식: 와일드카드 사용 패턴의 String 또는 String[].

빌드에 포함할 파일을 지정합니다. 패턴은 프로젝트 폴더를 기반으로 합니다. 기본값은 none입니다.

예:

```json
{
    "include":["wwwroot", "Views"]
}
```

#### <a name="exclude"></a>Exclude
형식: 와일드카드 사용 패턴의 String 또는 String[].

빌드에서 제외할 파일을 지정합니다. exclude 패턴은 include 패턴보다 우선 순위가 높습니다. 따라서 양쪽의 파일이 모두 제외됩니다. 패턴은 프로젝트 폴더를 기반으로 합니다. 기본값은 none입니다.

예:

```json
{
    "exclude": ["bin/**", "obj/**"]
}
```

#### <a name="includefiles"></a>includeFiles

형식: 와일드카드 사용 패턴의 String 또는 String[].

포함할 파일 경로의 목록. 경로는 프로젝트 폴더를 기반으로 합니다. 이 목록은 include 및 exclude 와일드카드 사용 패턴보다 우선 순위가 높습니다. 따라서 여기 및 exclude 와일드카드 사용 패턴에 나열된 파일이 여전히 포함됩니다. 기본값은 none입니다.

예:

```json
{
    "includeFiles": []
}
```

#### <a name="excludefiles"></a>excludeFiles

형식: 와일드카드 사용 패턴의 String 또는 String[].

제외할 파일 경로의 목록. 경로는 프로젝트 폴더를 기반으로 합니다. 이 목록은 와일드카드 사용 패턴 및 include 경로보다 우선 순위가 높습니다. 따라서 모든 곳에서 발견된 파일이 제외됩니다. 기본값은 none입니다.

예:

```json
{
    "excludeFiles":[],
}
```

#### <a name="builtins"></a>builtIns

형식: Object

시스템에서 제공하는 기본값. `include` 및 `exclude` 속성의 해당 값과 병합되는 `include` 및 `exclude` 와일드카드 사용 패턴을 가질 수 있습니다.

예:

```json
{
    "builtIns":{}
}
```

#### <a name="mappings"></a>매핑
형식: Object

개체에 대한 키는 출력 레이아웃의 대상 경로를 나타냅니다.

값은 포함할 파일의 소스 경로를 나타내는 문자열 또는 개체입니다.  개체 표시에 자체 `include`, `exclude`, `includeFiles` 및 `excludeFiles` 섹션이 포함될 수 있습니다.

문자열 예제:

```json
{
    "mappings": {
        "dest/path": "./src/path"
    }
}
```

개체 예제:

```json
{
    "mappings": {
        "dest/path":{
            "include":"./src/path"
        }
    }
}
```

### <a name="embed"></a>embed
형식: Object

컴파일 구성에 대한 속성을 포함하는 개체.

#### <a name="include"></a>include
형식: 와일드카드 사용 패턴의 String 또는 String[].

```json
{
    "include":["wwwroot", "Views"]
}
```

#### <a name="exclude"></a>Exclude
형식: 와일드카드 사용 패턴의 String 또는 String[].

빌드에서 제외할 파일을 지정합니다.

예:

```json
{
    "exclude": ["bin/**", "obj/**"]
}
```

#### <a name="includefiles"></a>includeFiles

형식: 와일드카드 사용 패턴의 String 또는 String[].

```json
{
    "includeFiles":[],
}
```

#### <a name="excludefiles"></a>excludeFiles

형식: 와일드카드 사용 패턴의 String 또는 String[].

```json
{
    "excludeFiles":[],
}
```

#### <a name="builtins"></a>builtIns
형식: Object

```json
{
    "builtIns":{}
}
```

#### <a name="mappings"></a>매핑
형식: Object

개체에 대한 키는 출력 레이아웃의 대상 경로를 나타냅니다.

값은 포함할 파일의 소스 경로를 나타내는 문자열 또는 개체입니다.  개체 표시에 자체 `include`, `exclude`, `includeFiles` 및 `excludeFiles` 섹션이 포함될 수 있습니다.

문자열 예제:

```json
{
    "mappings": {
        "dest/path": "./src/path"
    }
}
```

개체 예제:

```json
{
    "mappings": {
        "dest/path":{
            "include":"./src/path"
        }
    }
}
```

### <a name="copytooutput"></a>copyToOutput
형식: Object

컴파일 구성에 대한 속성을 포함하는 개체.

#### <a name="include"></a>include
형식: 와일드카드 사용 패턴의 String 또는 String[].

```json
{
    "include":["wwwroot", "Views"]
}
```

#### <a name="exclude"></a>Exclude
형식: 와일드카드 사용 패턴의 String 또는 String[].

빌드에서 제외할 파일을 지정합니다.

예:

```json
{
    "exclude": ["bin/**", "obj/**"]
}
```

#### <a name="includefiles"></a>includeFiles

형식: 와일드카드 사용 패턴의 String 또는 String[].

```json
{
    "includeFiles":[],
}
```

#### <a name="excludefiles"></a>excludeFiles

형식: 와일드카드 사용 패턴의 String 또는 String[].

```json
{
    "excludeFiles":[],
}
```

#### <a name="builtins"></a>builtIns
형식: Object

```json
{
    "builtIns":{}
}
```

#### <a name="mappings"></a>매핑
형식: Object

개체에 대한 키는 출력 레이아웃의 대상 경로를 나타냅니다.

값은 포함할 파일의 소스 경로를 나타내는 문자열 또는 개체입니다.  개체 표시에 자체 `include`, `exclude`, `includeFiles` 및 `excludeFiles` 섹션이 포함될 수 있습니다.

문자열 예제:

```json
{
    "mappings": {
        "dest/path": "./src/path"
    }
}
```

개체 예제:

```json
{
    "mappings": {
        "dest/path":{
            "include":"./src/path"
        }
    }
}
```

## <a name="publishoptions"></a>publishOptions
형식: Object

컴파일 구성에 대한 속성을 포함하는 개체.

### <a name="include"></a>include
형식: 와일드카드 사용 패턴의 String 또는 String[].

```json
{
    "include":["wwwroot", "Views"]
}
```

### <a name="exclude"></a>Exclude
형식: 와일드카드 사용 패턴의 String 또는 String[].

빌드에서 제외할 파일을 지정합니다.

예:

```json
{
    "exclude": ["bin/**", "obj/**"]
}
```

### <a name="includefiles"></a>includeFiles

형식: 와일드카드 사용 패턴의 String 또는 String[].

```json
{
    "includeFiles":[],
}
```

### <a name="excludefiles"></a>excludeFiles

형식: 와일드카드 사용 패턴의 String 또는 String[].

```json
{
    "excludeFiles":[],
}
```

### <a name="builtins"></a>builtIns
형식: Object

```json
{
    "builtIns":{}
}
```

### <a name="mappings"></a>매핑
형식: Object

개체에 대한 키는 출력 레이아웃의 대상 경로를 나타냅니다.

값은 포함할 파일의 소스 경로를 나타내는 문자열 또는 개체입니다.  개체 표시에 자체 `include`, `exclude`, `includeFiles` 및 `excludeFiles` 섹션이 포함될 수 있습니다.

문자열 예제:

```json
{
    "mappings": {
        "dest/file": "./src/file",
        "dest/folder/": "./src/folder/**/*"
    }
}
```

개체 예제:

```json
{
    "mappings": {
        "dest/file":{
            "include":"./src/file"
        },
        "dest/folder/":{
            "include":"./src/folder/**/*"
        }
    }
}
```

## <a name="runtimeoptions"></a>runtimeOptions
형식: Object

초기화하는 동안 런타임에 제공하는 매개 변수를 지정합니다.

### <a name="configproperties"></a>configProperties
형식: Object

런타임 및 프레임워크를 구성할 구성 속성을 포함합니다.

#### <a name="systemgcserver"></a>System.GC.Server
형식: Boolean

`true` - 서버 가비지 수집을 사용하도록 설정하는 경우, `false` - 아닌 경우. 기본값은 `false`입니다.

예:

```json
{
    "runtimeOptions": {
        "configProperties": {
            "System.GC.Server": true
        }
    }
}
```

#### <a name="systemgcconcurrent"></a>System.GC.Concurrent
형식: Boolean

`true` - 동시 가비지 수집을 사용하도록 설정하는 경우, `false` - 아닌 경우. 기본값은 `false`입니다.

예:

```json
{
    "runtimeOptions": {
        "configProperties": {
            "System.GC.Concurrent": true
        }
    }
}
```

#### <a name="systemgcretainvm"></a>System.GC.RetainVM
형식: Boolean

`true` - 삭제해야 할 세그먼트를 OS(운영 체제)에 다시 릴리스하는 대신 나중에 사용하기 위해 대기 목록에 두는 경우, `false` - 아닌 경우.
기본값은 `false`입니다.

예:

```json
{
    "runtimeOptions": {
        "configProperties": {
            "System.GC.RetainVM": true
        }
    }
}
```

#### <a name="systemthreadingthreadpoolminthreads"></a>System.Threading.ThreadPool.MinThreads
형식: Integer

ThreadPool 작업자 풀에 대한 최소 스레드 수를 재정의합니다.

```json
{
    "runtimeOptions": {
        "configProperties": {
            "System.Threading.ThreadPool.MinThreads": 4
        }
    }
}
```

#### <a name="systemthreadingthreadpoolmaxthreads"></a>System.Threading.ThreadPool.MaxThreads
형식: Integer

ThreadPool 작업자 풀에 대한 최대 스레드 수를 재정의합니다.

```json
{
    "runtimeOptions": {
        "configProperties": {
            "System.Threading.ThreadPool.MaxThreads": 25
        }
    }
}
```

### <a name="framework"></a>프레임워크
형식: Object

응용 프로그램을 활성화할 때 사용할 공유 프레임워크 속성을 포함합니다. 이 섹션의 존재는 응용 프로그램이 재배포 가능한 공유 프레임워크를 사용하도록 설계된 이식 가능한 앱임을 나타냅니다.

#### <a name="name"></a>name
형식: String

공유 프레임워크의 이름.

```json
{
    "runtimeOptions": {
        "framework": {
            "name": "Microsoft.DotNetCore"
        }
    }
}
```

#### <a name="version"></a>버전
형식: String

공유 프레임워크의 버전.

```json
{
    "runtimeOptions": {
        "framework": {
            "version": "1.0.1"
        }
    }
}
```

### <a name="applypatches"></a>applyPatches
형식: Boolean

`true` - `SemVer` 패치 필드에서만 다른 동일한 버전 또는 더 높은 버전의 프레임워크를 사용할 경우. `false` - 정확한 프레임워크 버전만을 사용해야 하는 호스트의 경우. 기본값은 `true`입니다.

```json
{
    "runtimeOptions": {
        "applyPatches": false
    }
}
```

## <a name="packoptions"></a>packOptions
형식: Object

NuGet 패키지에 대한 프로젝트 출력의 패키징과 관련된 옵션을 정의합니다.

### <a name="summary"></a>요약
형식: String

프로젝트에 대한 간단한 설명.

예:

```json
{
    "packOptions": {
        "summary": "This is my library."
    }
}
```

### <a name="tags"></a>태그
형식: String[]

NuGet에서 검색하는 데 사용되는, 프로젝트에 대한 태그가 포함된 문자열의 배열.

예:

```json
{
    "packOptions": {
        "tags": ["hyperscale", "cats"]
    }
}
```

### <a name="owners"></a>owners
형식: String[]

프로젝트 소유자의 이름이 포함된 문자열의 배열입니다.

예:

```json
{
    "packOptions": {
        "owners": ["Fabrikam", "Microsoft"]
    }
}
```

### <a name="releasenotes"></a>releaseNotes
형식: String

프로젝트에 대한 릴리스 정보.

예:

```json
{
    "packOptions": {
        "releaseNotes": "Initial version, implemented flimflams."
    }
}
```

### <a name="iconurl"></a>iconUrl
형식: String

패키지 탐색기 등의 다양한 위치에서 사용될 아이콘에 대한 URL.

예:

```json
{
    "packOptions": {
        "iconUrl": "http://www.mylibrary.gov/favicon.ico"
    }
}
```

### <a name="projecturl"></a>projectUrl
형식: String

프로젝트의 홈 페이지에 대한 URL.

예:

```json
{
    "packOptions": {
        "projectUrl": "http://www.mylibrary.gov"
    }
}
```

### <a name="licenseurl"></a>licenseUrl
형식: String

프로젝트 사용 라이선스에 대한 URL.

예:

```json
{
    "packOptions": {
        "licenseUrl": "http://www.mylibrary.gov/licence"
    }
}
```

### <a name="requirelicenseacceptance"></a>requireLicenseAcceptance
형식: Boolean

`true` - 패키지 설치 시 패키지 라이선스를 수락할 프롬프트를 표시할 경우, `false` - 아닌 경우. NuGet 패키지에만 사용되고 다른 용도에서는 무시됩니다. 기본값은 `false`입니다.

예:

```json
{
    "packOptions": {
        "requireLicenseAcceptance": true
    }
}
```
   
### <a name="repository"></a>리포지토리
형식: Object

프로젝트 저장된 저장소에 대한 정보를 포함합니다.

#### <a name="type"></a>type
형식: String

저장소의 형식. 기본값은 "git"입니다.

예:

```json
{
    "packOptions": {
        "repository": {
            "type": "git"
        }
    }
}
```

#### <a name="url"></a>url
형식: String

프로젝트가 저장된 저장소의 URL.

예:

```json
{
    "packOptions": {
        "repository": {
            "url": "http://github.com/dotnet/corefx"
        }
    }
}
```

### <a name="files"></a>파일
형식: Object

#### <a name="include"></a>include
형식: 와일드카드 사용 패턴의 String 또는 String[].

```json
{
    "include":["wwwroot", "Views"]
}
```

#### <a name="exclude"></a>Exclude
형식: 와일드카드 사용 패턴의 String 또는 String[].

빌드에서 제외할 파일을 지정합니다.

예:

```json
{
    "exclude": ["bin/**", "obj/**"]
}
```

#### <a name="includefiles"></a>includeFiles

형식: 와일드카드 사용 패턴의 String 또는 String[].

```json
{
    "includeFiles":[]
}
```

#### <a name="excludefiles"></a>excludeFiles

형식: 와일드카드 사용 패턴의 String 또는 String[].

```json
{
    "excludeFiles":[]
}
```

#### <a name="builtins"></a>builtIns
형식: Object

```json
{
    "builtIns":{}
}
```

#### <a name="mappings"></a>매핑
형식: Object

개체에 대한 키는 출력 레이아웃의 대상 경로를 나타냅니다.

값은 포함할 파일의 소스 경로를 나타내는 문자열 또는 개체입니다.  개체 표시에 자체 `include`, `exclude`, `includeFiles` 및 `excludeFiles` 섹션이 포함될 수 있습니다. 

문자열 예제:

```json
{
    "mappings": {
        "dest/path": "./src/path"
    }
}
```

개체 예제:

```json
{
    "mappings": {
        "dest/path":{
            "include":"./src/path"
        }
    }
}
```

## <a name="analyzeroptions"></a>analyzerOptions
형식: Object

코드 분석기에서 사용하는 속성이 포함된 개체.

예:

```json
{
    "analyzerOptions": { }
}
```

### <a name="languageid"></a>languageId
형식: String

분석할 언어의 ID. "cs"는 C#, "vb"는 Visual Basic, "fs"는 F#을 나타냅니다.

예:

```json
"analyzerOptions": {
    "languageId": "vb"
}
```

## <a name="configurations"></a>구성
형식: Object

해당 속성이 이 프로젝트에 대한 서로 다른 구성을 정의하는 개체(예: Debug 및 Release). 각 값은 이 구성에 대한 옵션과 함께 `buildOptions` 개체를 포함할 수 있는 개체입니다.

예:

```json
"configurations": {
    "Release": {
        "buildOptions": {
            "allowUnsafe": false
        }
    }
}
```

## <a name="frameworks"></a>frameworks
형식: Object

.NET Framework 또는 UWP(유니버설 Windows 플랫폼) 등 이 프로젝트가 지원하는 프레임워크를 지정합니다. 유효한 TFM(대상 프레임워크 모니커)이어야 합니다. 각 값은 `buildOptions`, `analyzerOptions`, `dependencies` 및 다음 섹션의 속성 등 이 프레임워크에 대한 정보를 포함할 수 있는 개체입니다.

예:

```json
"frameworks": {
    "netcoreapp1.0": {
        "buildOptions": {
            "define": ["FOO", "BIZ"]
        }
    }
}
```

### <a name="dependencies"></a>종속성
형식: Object

이 프레임워크에 대한 종속성. 이는 모든 대상에서 패키지 수준 종속성을 지정할 수 없는 시나리오에 유용합니다. 이에 대한 원인은 다른 대상에는 있는 기본 제공 지원이 부족하거나 다른 대상과는 다른 종속성 버전을 요구하는 대상이 포함되었기 때문일 수 있습니다. 이 노드에 대한 다른 속성의 목록을 보려면 이전의 [종속성](#dependencies) 섹션을 참조하세요.

예:

```json
    "frameworks": {
        "netstandard1.5": {
        "dependencies": {
            "Microsoft.Extensions.JsonParser.Sources": "1.0.0-rc2-20221"
        }
    }
}
```

### <a name="frameworkassemblies"></a>frameworkAssemblies
형식: Object

종속성과 유사하지만 NuGet 패키지에 없는 GAC의 어셈블리에 대한 참조를 포함합니다. 종속성 유형뿐만 아니라 사용할 버전을 지정할 수도 있습니다. .NET Framework 및 PCL(이식 가능한 클래스 라이브러리) 대상에 사용됩니다. Windows에서 지정된 것으로만 프로젝트를 빌드할 수 있습니다.

예:

```json
"frameworks": {
    "net451": {
        "frameworkAssemblies": {
            "System.Runtime": {
                "type": "build",
                "version": "4.0.0"
            }
        }
    }
}
```

### <a name="wrappedproject"></a>wrappedProject
형식: String

종속성 프로젝트의 위치를 지정합니다. 

예:

```json
"frameworks": {
    "net451": {
        "wrappedProject": "MyProject.csproj"
    }
}
```

### <a name="bin"></a>bin
형식: Object

DLL 파일 래핑에 사용됩니다. 이 DLL이 포함된 패키지를 생성하고 참조할 수 있습니다. 

값이 어셈블리 경로인 단일 문자열 속성 `assembly`를 포함합니다.   

예:

```json
"frameworks": {
    "netcoreapp1.0": {
        "bin": {
            "assembly": "c:/otherProject/otherdll.dll"
        }
    }
}
```

## <a name="runtimes"></a>runtimes
형식: Object

프로젝트에서 지원하는 [RID(런타임 식별자)](../rid-catalog.md)의 목록([자체 포함된 배포](../deploying/index.md#self-contained-deployments-scd)를 게시할 때 사용).

예:

```json
"runtimes": {
    "win7-x64": {},
    "win8-x64": {},
    "win81-x64": {},
    "win10-x64": {},
    "osx.10.11-x64": {},
    "ubuntu.16.04-x64": {}
}
```

## <a name="usersecretsid"></a>userSecretsId
형식: String

개발 시 사용할 사용자 암호 식별자를 지정합니다. 자세한 내용은 [Safe storage of app secrets during development(개발하는 동안 앱 암호의 안전한 저장소)](https://docs.asp.net/en/latest/security/app-secrets.html)를 참조하세요.

예:

```json
{
    "userSecretsId": "aspnet-WebApp1-c23d27a4-eb88-4b18-9b77-2a93f3b15119"
}
```



<!--HONumber=Nov16_HO1-->


