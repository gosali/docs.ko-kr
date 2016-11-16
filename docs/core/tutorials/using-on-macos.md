---
title: "macOS에서 .NET Core 시작"
description: "Visual Studio Code를 사용하여 macOS에서 .NET Core 시작"
keywords: .NET, .NET Core
author: bleroy
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 8ad82148-dac8-4b31-9128-b0e9610f4d9b
translationtype: Human Translation
ms.sourcegitcommit: 2339be6aef7e2ff942f1f1999a12f48ee4a77ee8
ms.openlocfilehash: 12b7bed380db53aad04f0615c6efa6152b3035b7

---

# <a name="getting-started-with-net-core-on-macos-using-visual-studio-code"></a>Visual Studio Code를 사용하여 macOS에서 .NET Core 시작

작성자: [Bertrand Le Roy](https://github.com/bleroy), [Phillip Carter](https://github.com/cartermp), [Bill Wagner](https://github.com/billwagner)

참가자: [Toni Solarin-Sodara](https://github.com/tsolarin)

이 문서에서는 [Visual Studio Code](http://code.visualstudio.com)를 사용하여 .NET Core 솔루션을 만드는 단계와 워크플로를 안내합니다.
프로젝트 만들기, 단위 테스트 만들기, 디버깅 도구 사용, [NuGet](http://nuget.org)을 통해 타사 라이브러리 통합 등을 배웁니다.

이 문서에서는 Mac OS에서 Visual Studio Code를 사용합니다. 차이가 있는 경우 Windows 플랫폼에 대한 차이를 가리킵니다.

## <a name="prerequisites"></a>필수 조건

시작하기 전에, 현재 시험판인 [.NET Core SDK](https://www.microsoft.com/net/core)를 설치해야 합니다. .NET Core SDK에는 최신 버전의 .NET Core 프레임워크 및 런타임이 포함되어 있습니다.

[Visual Studio Code](http://code.visualstudio.com)도 설치해야 합니다.
이 문서를 진행하면서 .NET Core 개발자 환경을 개선할 확장도 설치하게 됩니다.

[.NET 홈페이지](http://dot.net)에서 이 모두에 대한 링크를 찾을 수 있습니다.

## <a name="getting-started"></a>시작

이 자습서의 소스는 [GitHub](https://github.com/dotnet/docs/tree/master/samples/core/getting-started/golden)에서 이용할 수 있습니다.

Visual Studio Code를 시작합니다. Ctrl + '\`'(역따옴표)를 눌러 VS Code에서 포함된 터미널을 엽니다. 또는 원하는 경우 별도의 터미널 창을 사용할 수도 있습니다.

작업을 완료하면 세 개의 프로젝트, 즉 라이브러리 프로젝트, 해당 라이브러리 프로젝트에 대한 테스트, 그리고 라이브러리를 사용할 수 있게 하는 콘솔 응용 프로그램을 만들게 됩니다. 세 개의 프로젝트에 대해 표준 폴더 구조를 따릅니다. 이 표준 폴더 구조를 따르면 .NET Core SDK 도구는 프로덕션 코드 프로젝트와 테스트 코드 프로젝트 사이의 관계를 파악할 수 있습니다. 이에 따라 개발 환경의 생산성이 향상됩니다.

폴더를 만드는 것으로 시작해 보겠습니다. 터미널에서 'golden' 디렉터리를 만듭니다. 이 디렉터리 아래에 `src` 및 `test` 디렉터리를 만듭니다. `src` 아래에 `app` 및 `library` 디렉터리를 만듭니다. `test`에서 `test-library` 디렉터리를 만듭니다. VS Code에서 터미널을 사용하거나, VS Code에서 상위 폴더를 클릭하고 "새 폴더" 아이콘을 선택하여 디렉터리를 만들 수도 있습니다.

VS Code에서 'golden' 디렉터리를 엽니다. 이 디렉터리는 솔루션의 루트입니다.

그런 다음 솔루션에 대한 루트 디렉터리에 `global.json` 파일을 만듭니다.
다음은 `global.json`의 내용입니다.

```json
{
    "projects": [
        "src",
        "test"
    ]
}
```

이 시점에서 디렉터리 트리는 다음과 같습니다.


```
/golden
|__global.json
|__/src
   |__/app
   |__/library
|__/test
   |__/test-library
```

### <a name="writing-the-library"></a>라이브러리 작성

다음 작업은 라이브러리를 만드는 것입니다. 터미널 창(VS Code의 포함 된 터미널 또는 다른 터미널)에서 `golden/src/library`로 이동하고 `dotnet new -t lib` 명령을 입력합니다.
이렇게 하면 `project.json` 및 `Library.cs`의 두 파일과 함께 라이브러리 프로젝트가 만들어집니다.

`project.json`에는 다음 정보가 포함되어 있습니다.

```json
{
  "version": "1.0.0-*",
  "buildOptions": {
    "debugType": "portable"
  },
  "dependencies": {},
  "frameworks": {
    "netstandard1.6": {
      "dependencies": {
        "NETStandard.Library": "1.6.0"
      }
    }
  }
}
```


이 프로젝트는 개체의 JSON 표현 방식을 사용하므로, 사용자는 `Newtonsoft.Json` NuGet 패키지에 참조를 추가할 수 있습니다. `project.json`에서 패키지의 최신 시험판 버전을 종속성으로 추가합니다.

```json
"dependencies": {
    "Newtonsoft.Json": "9.0.1-beta1"
},
```

이러한 종속성 추가를 완료한 후 작업 영역에 패키지를 설치해야 합니다. `dotnet restore` 명령을 실행하여 모든 종속성을 업데이트하고, `project.lock.json` 파일을 만들어 프로젝트 디렉터리에 저장합니다. 이 파일에는 프로젝트의 모든 종속성에 대한 전체 종속성 트리가 포함됩니다. 이 파일을 읽을 필요는 없습니다. .NET Core SDK의 도구에서 이 파일을 사용합니다.

이제 C# 코드를 업데이트해 보겠습니다. 하나의 공용 메서드를 포함하는 `Thing` 클래스를 만들어 보겠습니다. 이 메서드는 두 숫자의 합을 반환하지만, 그 과정에서 해당 숫자를 JSON 문자열로 변환하고 역직렬화합니다. 파일의 이름을 `Library.cs`에서 `Thing.cs`로 바꿉니다. 그런 다음 기존 코드(템플릿에서 생성된 Class1)를 다음으로 바꿉니다.

```csharp
using static Newtonsoft.Json.JsonConvert;

namespace Library
{
    public class Thing
    {
        public int Get(int left, int right) =>
            DeserializeObject<int>($"{left + right}");
    }
}
```

여기에는 static 사용, 식 본문 멤버, 보간된 문자열 등 최신 C# 기능이 사용되며, 이러한 기능은 [C# 배우기](../../csharp/index.md) 섹션에서 배울 수 있습니다.

이제 코드를 업데이트했으므로 `dotnet build`를 사용하여 라이브러리를 빌드할 수 있습니다.

이제 `golden/src/library/bin/Debug/netstandard1.6` 아래에 빌드된 `library.dll` 파일이 있습니다.

### <a name="writing-the-test-project"></a>테스트 프로젝트 작성

빌드한 라이브러리에 대한 테스트 프로젝트를 빌드해 보겠습니다. `test/test-library` 디렉터리로 이동합니다. `dotnet new -t xunittest`를 실행하여 새 테스트 프로젝트를 만듭니다. 

위 단계에서 작성한 라이브러리에 대한 종속성 노드를 추가해야 합니다. `project.json`을 열고 종속성 섹션을 다음으로 업데이트합니다(아래의 마지막 노드인 `library` 노드 포함).

```json
"dependencies": {
  "System.Runtime.Serialization.Primitives": "4.1.1",
  "xunit": "2.1.0",
  "dotnet-test-xunit": "1.0.0-rc2-192208-24",
  "library": {
    "target": "project"
  }
}
```

`library` 노드는 이 종속성을 현재 작업 영역의 프로젝트로 확인해야 함을 지정합니다. 이를 명시적으로 지정하지 않으면 테스트 프로젝트가 같은 이름의 NuGet 패키지에 대해 빌드될 수 있습니다.

이제 종속성은 제대로 구성되었으므로 라이브러리에 대한 테스트를 만들어 보겠습니다. `Tests.cs`를 열고 내용을 다음 코드로 바꿉니다.

```csharp
using Library;
using Xunit;

namespace TestApp
{
    public class LibraryTests
    {
        [Fact]
        public void TestThing() {
            Assert.Equal(42, new Thing().Get(19, 23));
        }
    }
}
```

이제 `dotnet restore`, `dotnet build` 및 `dotnet test`를 실행합니다.
xUnit 콘솔 Test Runner가 하나의 테스트를 실행하고 통과를 보고합니다. 

### <a name="writing-the-console-app"></a>콘솔 앱 작성

터미널에서 `golden/src/app` 디렉터리로 이동합니다. `dotnet new`를 실행하여 새 콘솔 응용 프로그램을 만듭니다.

콘솔 응용 프로그램은 이전 단계에서 빌드하고 테스트한 라이브러리에 종속됩니다. 이 종속성을 추가하도록 `project.json`을 편집하여 이를 나타내야 합니다.  `dependencies` 노드에서 다음과 같이 `library` 노드를 추가합니다.

```json
"dependencies": {
  "library": {
    "target": "project"
  }
}
```

`project` 노드는 테스트 라이브러리에 있었기 때문에 여기에서 중요합니다. 즉, 이것이 NuGet 패키지가 아니라 현재 솔루션의 프로젝트임을 나타냅니다.

`dotnet restore`를 실행하여 모든 종속성을 복원합니다. `program.cs`를 열고 `Main` 메서드의 내용을 다음 줄로 바꿉니다.

```csharp
WriteLine($"The answer is {new Thing().Get(19, 23)}");
```

파일 상단에 `using` 지시문 두 줄을 추가해야 합니다.

```csharp
using static System.Console;
using Library;
```

그런 다음 `dotnet build`를 실행합니다. 어셈블리가 만들어지며 `dotnet run`을 입력하여 실행 파일을 실행할 수 있습니다.

### <a name="debugging-your-application"></a>응용 프로그램 디버깅

C# 확장을 통해 VS Code에서 코드를 디버깅할 수 있습니다.
`F1`을 눌러 VS Code 팔레트를 열어서 이 확장을 설치합니다. `ext install`을 입력하여 확장의 목록을 표시합니다. C# 확장을 선택합니다. 자세한 내용은 [Visual Studio Code C# 확장 문서](https://github.com/OmniSharp/omnisharp-vscode/blob/master/debugger.md) 페이지를 참조하세요.

확장을 설치하면 VS Code가 응용 프로그램을 다시 시작하여 새 확장을 로드할지를 물어봅니다. 확장이 설치되면 디버거 탭을 열 수 있습니다(그림 참조).

![VS Code 디버거](./media/using-on-macos/vscodedebugger.png)


디버거를 시작하면 VS Code에서 디비거를 구성하도록 안내합니다. 디버거를 구성할 때 `.vscode` 디렉터리와 두 파일 `tasks.json` 및 `launch.json`이 만들어집니다. 이 두 파일이 디버거 구성을 제어합니다. 대부분의 프로젝트에서 이 디렉터리는 소스 제어에 포함되지 않고
이 안내와 연결된 샘플에 포함됩니다. 따라서 편집해야 할 내용을 볼 수 있습니다.

솔루션에는 여러 프로젝트가 포함되므로, 올바른 명령을 수행하도록 이러한 파일을 각각 수정할 수 있습니다. 먼저 `tasks.json`을 엽니다. 기본 빌드 작업은 작업 영역 소스 디렉터리에서 `dotnet build`를 실행합니다. 대신 `src/app` 디렉터리에서 실행할 수도 있습니다. 현재 작업 디렉터리를 설정하려면 `options` 노드를 추가해야 합니다.

```json
"options": {
    "cwd": "${workspaceRoot}/src/app"
}
```

그런 다음 `launch.json`을 열고 프로그램 경로를 업데이트해야 합니다. 프로그램을 설명하는 "구성" 아래에 노드가 표시됩니다. 다음을 볼 수 있습니다.

```json
"program": "${workspaceRoot}/bin/Debug/<target-framework>/<project-name.dll>",
```

이를 다음으로 변경합니다.

```json
"program": "${workspaceRoot}/src/app/bin/Debug/netcoreapp1.0/app.dll",
```

Windows에서 실행 중인 경우 이식 가능한 PDB 파일을 생성하려면 응용 프로그램의 `project.json`(`src/app` 디렉터리)을 업데이트해야 합니다(Mac OSX 및 Linux에서는 기본적으로 발생).
`buildOptions` 내부에 `debugType` 노드를 추가합니다. `project.json`에서 `src/app` 및 `src/library` 폴더에 대해 `debugType` 노드를 추가해야 합니다.

```json
  "buildOptions": {
    "debugType": "portable"
  },
```

`Main`의 `WriteLine` 문에 중단점을 설정합니다. `F9` 키를 누르거나 중단점을 추가할 줄의 왼쪽 여백에서 마우스를 클릭하면 됩니다. VS Code 화면 왼쪽에 있는 디버그 아이콘을 눌러 디버거를 엽니다(그림 참조). 디버거에서 응용 프로그램을 시작하려면 재생 단추를 누릅니다.

중단점에 도달합니다. `Get` 메서드를 단계별로 실행하며 올바른 인수를 전달했는지 확인합니다. 답이 실제로 42인지 확인합니다.



<!--HONumber=Nov16_HO1-->


