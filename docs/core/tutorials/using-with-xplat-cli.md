---
title: "명령줄을 사용하여 Windows/Linux/macOS에서 .NET Core 시작"
description: "CLI(명령줄 인터페이스)를 사용하여 Windows, Linux 또는 macOS에서 .NET Core 시작"
keywords: .NET, .NET Core
author: cartermp
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: be988f09-7349-43b0-97fb-3a703d4587ce
translationtype: Human Translation
ms.sourcegitcommit: aeb199a9aeb1584570ad2a2942e2f22c75a59616
ms.openlocfilehash: aafa0c110dc3a2820f7e050d70b9450af1db35d8

---

# <a name="getting-started-with-net-core-on-windowslinuxmacos-using-the-command-line"></a>명령줄을 사용하여 Windows/Linux/macOS에서 .NET Core 시작

이 가이드에서는 .NET Core CLI 도구를 사용하여 플랫폼 간 콘솔 앱을 빌드하는 방법을 보여 줍니다.  가장 기본적인 콘솔 앱으로 시작하여 테스트를 비롯한 여러 프로젝트로 확장됩니다. 이러한 기능을 단계별로 추가하며, 이미 확인하고 빌드한 것 위에서 빌드하게 됩니다.

.NET Core CLI 도구 집합에 익숙하지 않은 경우 [.NET Core SDK 개요](../sdk.md)를 읽어 보세요.

## <a name="prerequisites"></a>필수 조건

시작하기 전에 [최신 .NET Core CLI 도구](https://www.microsoft.com/net/core)를 가지고 있는지 확인합니다.  텍스트 편집기도 필요합니다.

## <a name="hello-console-app"></a>Hello, 콘솔 앱!

먼저, 원하는 이름의 폴더로 이동하거나 폴더를 새로 만듭니다.  "Hello"는 샘플 코드에 대해 선택한 이름으로, [여기](https://github.com/dotnet/docs/tree/master/samples/core/console-apps/Hello)서 찾을 수 있습니다.

명령 프롬프트를 열고 다음을 입력합니다.

```
$ dotnet new
$ dotnet restore
$ dotnet run
```

이제 간단한 연습을 해보겠습니다.

1. `$ dotnet new`

   [`dotnet new`](../tools/dotnet-new.md)는 콘솔 앱을 빌드하는 데 필요한 NuGet 종속성이 있는 최신 `project.json` 파일입니다.  응용 프로그램에 대한 진입점을 포함하는 기본 파일인 `Program.cs`도 만듭니다.
   
   `project.json`:
   ```json
   {
        "version": "1.0.0-*",
        "buildOptions": {
            "emitEntryPoint": true
        },
        "dependencies": {
            "Microsoft.NETCore.App": {
                "type": "platform",
                "version": "1.0.0"
            }
        },   
       "frameworks": {
            "netcoreapp1.0": {
                "imports": "dnxcore50"
            }
        }
   }
   ```
   `Program.cs`:
   ```csharp
   using System;

   namespace ConsoleApplication
   {
       public class Program
       {
           public static void Main(string[] args)
           {
               Console.WriteLine("Hello World!");
           }
       }
   }
   ```

2. `$ dotnet restore`

   [`dotnet restore`](../tools/dotnet-restore.md)는 NuGet을 호출하여 종속성 트리를 복원합니다. NuGet은 `project.json` 파일을 분석하고, 파일에 명시된 종속성을 다운로드하고(또는 컴퓨터의 캐시에서 종속성을 가져오고), `project.lock.json` 파일을 작성합니다.  `project.lock.json` 파일은 컴파일 및 실행하려면 필요합니다.
   
   `project.lock.json` 파일은 NuGet 종속성 및 앱을 설명하는 기타 정보로 구성된 그래프의 지속적이고 전체적인 집합입니다.  `dotnet build` 및 `dotnet run` 같은 다른 도구에서는 이 파일을 읽고, NuGet 종속성 및 바인딩 확인의 올바른 집합으로 소스 코드를 처리합니다.
   
3. `$ dotnet run`

   [`dotnet run`](../tools/dotnet-run.md)은 `dotnet build`를 호출하여 빌드 대상이 빌드되었는지를 확인하고 `dotnet <assembly.dll>`을 호출하여 대상 응용 프로그램을 실행합니다.
   
```
$ dotnet run
Hello, World!
```

또한 [`dotnet build`](../tools/dotnet-build.md)를 실행하여 빌드 콘솔 응용 프로그램을 실행하지 않고 코드를 컴파일할 수도 있습니다.

### <a name="building-a-selfcontained-application"></a>자체 포함 응용 프로그램 빌드

이식 가능한 응용 프로그램 대신 자체 포함 응용 프로그램을 컴파일해 보겠습니다. 서로 다른 응용 프로그램 유형 및 이들의 배포 방법을 알아보려면 [.NET Core의 이식성 유형](../deploying/index.md)에 대해 읽어볼 수 있습니다.

도구를 사용하여 자체 포함 응용 프로그램을 빌드하려면 `project.json` 파일을 일부 변경해야 할 수 있습니다. 샘플 디렉터리의 [HelloNative](https://github.com/dotnet/docs/tree/master/samples/core/console-apps/HelloNative) 프로젝트에서 이 내용을 볼 있습니다.

첫 번째 변경 사항은 모든 종속성에서 `"type": "platform"` 요소를 제거하는 것입니다. 지금까지 이 프로젝트의 유일한 종속성은 `"Microsoft.NETCore.App"`입니다. `dependencies` 섹션은 다음과 같이 표시됩니다.

```json
"dependencies": {
    "Microsoft.NETCore.App": {
        "version": "1.0.0"
    }
},
```

이제 `runtimes` 노드를 추가하여 모든 대상 실행 환경을 지정해야 합니다. 예를 들어, 다음 `runtimes` 노드는 빌드 시스템에 Windows 10의 64비트 버전 및 Mac OS X 버전 10.11의 64비트 버전에 대한 실행 파일을 만들도록 지시합니다.
빌드 시스템은 현재 환경에 대한 네이티브 실행 파일을 생성합니다. Windows 컴퓨터에서 다음 단계를 따르는 경우 Windows 실행 파일이 빌드됩니다. Mac에서 다음 단계를 따르는 경우 OS X 실행 파일이 빌드됩니다.

```json 
"runtimes": {
  "win10-x64": {},
  "osx.10.11-x64": {}
}
```

[RID 카탈로그](../rid-catalog.md)에서 지원되는 런타임의 전체 목록을 참조하세요. 
 
이러한 두 가지 변경을 수행한 후 `dotnet restore`, `dotnet build`를 차례로 실행하여 네이티브 실행 파일을 만듭니다. 그런 다음 생성된 네이티브 실행 파일을 실행할 수 있습니다. 

다음 예제에서는 Windows에 대한 명령을 보여 줍니다. 다음 예제는 네이티브 실행 파일이 어디에서 생성되는지를 보여주며, 프로젝트 디렉터리의 이름이 HelloNative라고 가정합니다.

```
$ dotnet restore 
$ dotnet build 
$ .\bin\Debug\netcoreapp1.0\win10-x64\HelloNative.exe
Hello World!
```

네이티브 응용 프로그램은 빌드 시간은 약간 더 느리지만 실행 시간은 조금 더 빠릅니다. 응용 프로그램이 커지면 이 동작이 더 분명해집니다.

`project.json`이 네이티브 빌드를 만들 때 빌드 프로세스에서 몇 가지 파일이 더 생성됩니다. 이러한 파일은 `bin\Debug\netcoreapp1.0\<platform>`에 생성되는데, 여기서 `<platform>`은 선택한 RID입니다. 프로젝트의 `HelloNative.dll` 외에도 런타임을 로드하고 응용 프로그램을 시작하는 `HelloNative.exe`가 있습니다.
프로젝트 디렉터리의 이름이 변경되었기 때문에 생성된 응용 프로그램의 이름이 변경되었습니다.  

.NET 런타임을 포함하지 않는 컴퓨터에서 실행할 수 있도록 이 응용 프로그램을 패키지할 수도 있습니다.
이 경우 `dotnet publish` 명령을 사용하면 됩니다. `dotnet publish` 명령은 `./bin/Debug/netcoreapp1.0/<platform>` 디렉터리 아래에 `publish`라는 새 하위 디렉터리를 만듭니다. 실행 파일, 모든 종속 DLL 및 프레임워크가 이 하위 디렉터리에 복사됩니다. 해당 디렉터리를 다른 컴퓨터(또는 컨테이너)로 패키지하고 그곳에서 응용 프로그램을 실행할 수 있습니다. 

이것을 첫 번째 Hello World 샘플에 있는 `dotnet publish`의 동작과 비교해 보겠습니다. 해당 응용 프로그램은 .NET Core의 기본 응용 프로그램 형식인 *이식 가능한 응용 프로그램*입니다. 이식 가능한 응용 프로그램을 실행하려면 대상 컴퓨터에 .NET Core가 설치되어 있어야 합니다. 이식 가능한 응용 프로그램은 한 컴퓨터에서 빌드하고 어디서나 실행할 수 있습니다. 네이티브 응용 프로그램은 각 대상 컴퓨터에 대해 개별적으로 빌드해야 합니다. `dotnet publish`는 응용 프로그램의 DLL 및 플랫폼 설치에 포함되지 않는 모든 종속 DLL이 있는 디렉터리를 만듭니다.

### <a name="augmenting-the-program"></a>프로그램 보강

파일을 아주 조금만 변경하겠습니다.  피보나치(Fibonacci) 숫자가 흥미로우므로 시도해 보겠습니다(기본 버전 사용).

`Program.cs`:

```csharp
using static System.Console;

namespace ConsoleApplication
{
    public class Program
    {
        public static int FibonacciNumber(int n)
        {
            int a = 0;
            int b = 1;
            int tmp;
            
            for (int i = 0; i < n; i++)
            {
                tmp = a;
                a = b;
                b += tmp;
            }
            
            return a;   
        }
        
        public static void Main(string[] args)
        {
            WriteLine("Hello World!");
            WriteLine("Fibonacci Numbers 1-15:");
            
            for (int i = 0; i < 15; i++)
            {
                WriteLine($"{i+1}: {FibonacciNumber(i)}");
            }
        }
    }
}
```

프로그램을 실행합니다(플랫폼은 Windows이며 프로젝트 디렉터리 이름을 Fibonacci로 변경한 것으로 가정).

```
$ dotnet build
$ .\bin\Debug\netcoreapp1.0\win10-x64\Fibonacci.exe
1: 0
2: 1
3: 1
4: 2
5: 3
6: 5
7: 8
8: 13
9: 21
10: 34
11: 55
12: 89
13: 144
14: 233
15: 377
```

됐습니다!  원하는 대로 `Program.cs`를 보강할 수 있습니다.

## <a name="adding-some-new-files"></a>몇 가지 새 파일 추가

단순한 일회용 프로그램에는 단일 파일도 괜찮지만, 여러 구성 요소가 있는 프로그램을 빌드하는 경우 기능을 여러 파일로 분할해야 할 수 있습니다.  여러 파일은 이런 경우에 사용합니다.

새 파일을 만들고 고유한 네임스페이스를 지정합니다.

```csharp
using System;

namespace NumberFun
{
    // code can go here
} 
```

이런 다음 `Program.cs` 파일에 포함합니다.

```csharp
using static System.Console;
using NumberFun;
```

마지막으로 파일을 빌드할 수 있습니다.

`$ dotnet build`

이제부터가 흥미로운 부분입니다. 새 파일로 작업을 수행하는 것입니다.

### <a name="example-a-fibonacci-sequence-generator"></a>예제: 피보나치 시퀀스 생성기

일부 피보나치 값을 캐시하여 이전 [피보나치 예제](https://github.com/dotnet/docs/tree/master/samples/core/console-apps/Fibonacci)에서 빌드하고 약간의 재귀 기능을 추가하려 한다고 가정해 보겠습니다.  [더 나은 피보나치 예제](https://github.com/dotnet/docs/tree/master/samples/core/console-apps/FibonacciBetter)에 대한 코드는 다음과 같을 수 있습니다.

```csharp
using System;
using System.Collections.Generic;

namespace NumberFun
{
    public class FibonacciGenerator
    {
        private Dictionary<int, int> _cache = new Dictionary<int, int>();
        
        private int Fib(int n) => n < 2 ? n : FibValue(n - 1) + FibValue(n - 2);
        
        private int FibValue(int n)
        {
            if (!_cache.ContainsKey(n))
            {
                _cache.Add(n, Fib(n));
            }
            
            return _cache[n];
        }
        
        public IEnumerable<int> Generate(int n)
        {
            for (int i = 0; i < n; i++)
            {
                yield return FibValue(i);
            }
        }
    }
}
```

`Dictionary<int, int>` 및 `IEnumerable<int>`를 사용한다는 것은 `System.Collections` 네임스페이스를 통합한다는 의미입니다.
`Microsoft.NetCore.App` 패키지는 .NET Framework의 많은 핵심 어셈블리를 포함하는 *메타패키지*입니다. 이 메타패키지를 포함함으로써 이미 프로젝트의 일부로 `System.Collections.dll` 어셈블리를 포함한 것입니다. `dotnet publish`를 실행하고 설치된 패키지의 일부인 파일을 검사하여 이를 확인할 수 있습니다. 목록에 `System.Collections.dll`이 있습니다. 

```json
{ 
  "version": "1.0.0-*", 
  "buildOptions": { 
    "debugType": "portable", 
    "emitEntryPoint": true 
  }, 
  "dependencies": {}, 
  "frameworks": { 
    "netcoreapp1.0": { 
      "dependencies": { 
        "Microsoft.NETCore.App": { 
          "version": "1.0.0" 
        } 
      }, 
      "imports": "dnxcore50" 
    } 
  },
  "runtimes": {
    "win10-x64": {},
    "osx.10.11-x64": {}
  }
}
```

이제 아래와 같이 `Program.cs` 파일에서 `Main()` 메서드를 조정합니다. 예제에서는 `Program.cs`에 `using System;` 문이 있다고 가정합니다. `using static System.Console;` 문이 있는 경우 `Console.`을 `Console.WriteLine`에서 제거합니다.  

```csharp
public static void Main(string[] args)
{
    var generator = new FibonacciGenerator();
    foreach (var digit in generator.Generate(15))
    {
        WriteLine(digit);
    }
}
```

마지막으로 실행합니다!

```
$ dotnet run
0
1
1
2
3
5
8
13
21
34
55
89
144
233
377
```

됐습니다!

## <a name="using-folders-to-organize-code"></a>폴더를 사용하여 코드 구성

작업할 몇 가지 새로운 형식을 도입하려 한다고 가정해 보겠습니다.  이렇게 하려면 파일을 더 추가하고 `Program.cs` 파일에 포함할 수 있는 네임스페이스를 부여하면 됩니다.

```
/MyProject
|__Program.cs
|__AccountInformation.cs
|__MonthlyReportRecords.cs
|__project.json
```

프로젝트의 크기가 비교적 작은 경우에는 이 방법이 매우 효과적입니다.  그러나 다양한 데이터 형식 및 여러 레이어가 포함된 더 큰 앱이 있는 경우 항목을 논리적으로 구성해야 할 수 있습니다.  여기에서 폴더를 생각할 수 있습니다.  이 가이드에서 다루는 [NewTypes 샘플 프로젝트](https://github.com/dotnet/docs/tree/master/samples/core/console-apps/NewTypes)를 따를 수도 있고, 자신의 파일과 폴더를 만들 수도 있습니다.

시작하려면 프로젝트의 루트 아래에 새 폴더를 만듭니다.  `/Model`이 여기서 선택되었습니다.

```
/NewTypes
|__/Model
|__Program.cs
|__project.json
```

이제 몇 가지 새로운 형식을 폴더에 추가합니다.

```
/NewTypes
|__/Model
   |__AccountInformation.cs
   |__MonthlyReportRecords.cs
|__Program.cs
|__project.json
```

이제 마치 이들이 동일한 디렉터리의 파일인 것처럼, `Program.cs`에 포함할 수 있도록 모든 파일에 동일한 네임스페이스를 부여합니다.

### <a name="example-pet-types"></a>예제: Pet 형식

이 예제에서는 두 개의 새 형식, `Dog` 및 `Cat`를 만들고 `IPet` 인터페이스에서 구현합니다.

폴더 구조:

```
/NewTypes
|__/Pets
   |__Dog.cs
   |__Cat.cs
   |__IPet.cs
|__Program.cs
|__project.json
```

`IPet.cs`:
```csharp
using System;

namespace Pets
{
    public interface IPet
    {
        string TalkToOwner();
    }
}
```

`Dog.cs`:
```csharp
using System;

namespace Pets
{
    public class Dog : IPet
    {
        public string TalkToOwner() => "Woof!";
    }
}
```

`Cat.cs`:
```csharp
using System;

namespace Pets
{
    public class Cat : IPet
    {
        public string TalkToOwner() => "Meow!";
    }
}
```

`Program.cs`:
```csharp
using System;
using Pets;
using System.Collections.Generic;

namespace ConsoleApplication
{
    public class Program
    {
        public static void Main(string[] args)
        {
            List<IPet> pets = new List<IPet>
            {
                new Dog(),
                new Cat()  
            };
            
            foreach (var pet in pets)
            {
                Console.WriteLine(pet.TalkToOwner());
            }
        }
    }
}
```

`project.json`:
```json
{
  "version": "1.0.0-*",
  "buildOptions": {
    "emitEntryPoint": true
  },
  "dependencies": {
    "Microsoft.NETCore.App": {
      "type": "platform",
      "version": "1.0.0"
    }
  },
  "frameworks": {
    "netcoreapp1.0": {
      "imports": "dnxcore50"
    }
  }
}
```

이를 실행하면 다음이 가능합니다.

```
$ dotnet restore
$ dotnet run
Woof!
Meow!
```

새로운 pet 형식을 추가하여(예: `Bird`) 이 프로젝트를 확장할 수 있습니다.

## <a name="testing-your-console-app"></a>콘솔 앱 테스트

어떤 지점에 도달하면 프로젝트를 테스트해야 할 수 있습니다.  이 경우 다음 방법을 사용할 수 있습니다.

1. 기존 프로젝트의 모든 소스를 새로운 `src` 폴더로 이동합니다.

   ```
   /Project
   |__/src
   ```

2. `/test` 디렉터리를 만듭니다.

   ```
   /Project
   |__/src
   |__/test
   ```

3. 새 `global.json` 파일을 만듭니다.

   ```
   /Project
   |__/src
   |__/test
   |__global.json
   ```

   `global.json`:
   ```json
   {
      "projects": [
         "src", "test"
      ]
   }
   ```

   이 파일은 빌드 시스템에 이것이 다중 프로젝트 시스템임을 알립니다. 그러면 빌드 시스템은 현재 실행 중인 폴더 이외의 폴더에서도 종속성을 검색합니다.  이것이 중요한 이유는 테스트 프로젝트에서 테스트 중인 코드에 종속성을 배치할 수 있기 때문입니다.
   
### <a name="example-extending-the-newtypes-project"></a>예제: NewTypes 프로젝트 확장

프로젝트 시스템이 준비되었으므로 이제 테스트 프로젝트를 만들고 테스트 작성을 시작할 수 있습니다.  여기서부터 이 가이드는 [샘플 Types 프로젝트](https://github.com/dotnet/docs/tree/master/samples/core/console-apps/NewTypes)를 사용하고 확장합니다.  또한 [Xunit](https://xunit.github.io/) 테스트 프레임워크를 사용합니다.  이 가이드의 과정을 따라 해도 되고, 테스트와 함께 고유한 다중 프로젝트 시스템을 만들어도 됩니다.


전체 프로젝트 구조는 다음과 같습니다.

```
/NewTypes
|__/src
   |__/NewTypes
      |__/Pets
         |__Dog.cs
         |__Cat.cs
         |__IPet.cs
      |__Program.cs
      |__project.json
|__/test
   |__NewTypesTests
      |__PetTests.cs
      |__project.json
|__global.json
```

테스트 프로젝트에 있는지 확인해야 할 두 가지가 있습니다.

1. 다음이 포함된 올바른 `project.json`:

   * 다음에 대한 참조:`xunit`
   * 다음에 대한 참조:`dotnet-test-xunit`
   * 테스트 중인 코드에 해당하는 네임스페이스에 대한 참조

2. Xunit 테스트 클래스

`NewTypesTests/project.json`:
```json
{
  "version": "1.0.0-*",
  "testRunner": "xunit",

  "dependencies": {
    "Microsoft.NETCore.App": {
      "type":"platform",
      "version": "1.0.0"
    },
    "xunit":"2.2.0-beta2-build3300",
    "dotnet-test-xunit": "2.2.0-preview2-build1029",
    "NewTypes": "1.0.0"
  },
  "frameworks": {
    "netcoreapp1.0": {
      "imports": [
        "dnxcore50",
        "portable-net45+win8" 
      ]
    }
  }
}
```

`PetTests.cs`: 
```csharp
using System;
using Xunit;
using Pets;
public class PetTests
{
    [Fact]
    public void DogTalkToOwnerTest()
    {
        string expected = "Woof!";
        string actual = new Dog().TalkToOwner();
        
        Assert.Equal(expected, actual);
    }
    
    [Fact]
    public void CatTalkToOwnerTest()
    {
        string expected = "Meow!";
        string actual = new Cat().TalkToOwner();
        
        Assert.Equal(expected, actual);
    }
}
```
   
이제 테스트를 실행할 수 있습니다!  [`dotnet test`](../tools/dotnet-test.md) 명령은 프로젝트에 지정된 Test Runner를 실행합니다. 최상위 디렉터리에서 시작해야 합니다.
 
```
$ dotnet restore
$ cd test/NewTypesTests
$ dotnet test
```
 
출력은 다음과 같습니다.
 
```
xUnit.net .NET CLI test runner (64-bit win10-x64)
  Discovering: NewTypesTests
  Discovered:  NewTypesTests
  Starting:    NewTypesTests
  Finished:    NewTypesTests
=== TEST EXECUTION SUMMARY ===
   NewTypesTests  Total: 2, Errors: 0, Failed: 0, Skipped: 0, Time: 0.144s
SUMMARY: Total: 1 targets, Passed: 1, Failed: 0.
```
 
## <a name="conclusion"></a>결론
 
이 가이드가 기본적인 사항에서 단위 테스트가 포함된 다중 프로젝트 시스템에 이르기까지 .NET Core 콘솔 앱을 만드는 방법을 배우는 데 도움이 되었길 바랍니다.  다음 단계는 자신만의 멋진 콘솔 앱을 만드는 것입니다.
 
흥미로운 고급 콘솔 앱의 예제를 보려면 [Using the CLI tools to write console apps: An advanced step-by-step guide(CLI 도구를 사용하여 콘솔 앱 작성: 고급 단계별 가이드)](cli-console-app-tutorial-advanced.md) 자습서를 참조하세요.



<!--HONumber=Nov16_HO1-->


