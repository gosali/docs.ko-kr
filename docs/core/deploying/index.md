---
title: ".NET Core 응용 프로그램 배포"
description: ".NET Core 응용 프로그램 배포"
keywords: ".NET, .NET Core, .NET Core 배포"
author: rpetrusha
manager: wpickett
ms.date: 09/08/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: da7a31a0-8072-4f23-82aa-8a19184cb701
translationtype: Human Translation
ms.sourcegitcommit: 663f4102b82512e64ab39d8046c7298a7cf37de7
ms.openlocfilehash: 96eb2cc7ca948b3e372fa1363b1741624d791d27

---

# <a name="net-core-application-deployment"></a>.NET Core 응용 프로그램 배포 #

.NET Core 응용 프로그램에 대해 두 가지 유형을 배포를 만들 수 있습니다. 

- 프레임워크 종속 배포. 이름에서 알 수 있듯이 FDD(프레임워크 종속 배포)는 대상 시스템에 표시하기 위해 .NET Core의 공유 시스템 차원 버전을 사용합니다. .NET Core가 이미 존재하기 때문에 .NET Core 설치 간에 앱을 이식할 수 있습니다. 앱은 고유한 코드와 .NET Core 라이브러리 외부에 있는 타사 종속성만 포함합니다. FDD에는 명령줄에서 [dotnet 유틸리티](../tools/dotnet.md)를 사용하여 시작할 수 있는 .dll 파일이 포함됩니다. 예를 들어 `dotnet app.dll`은 `app`이라는 응용 프로그램을 실행합니다.

- 자체 포함 배포. FDD와 달리 SCD(자체 포함 배포)에서는 대상 시스템에 표시하기 위해 공유 구성 요소를 사용하지 않습니다. .NET Core 라이브러리 및 .NET Core 런타임을 비롯한 모든 구성 요소가 응용 프로그램과 함께 포함되며 다른 .NET Core 응용 프로그램에서 격리됩니다. SCD에는 플랫폼별.NET Core 호스트의 이름이 변경된 버전인 실행 파일(예: Windows 플랫폼에서 `app`이라는 응용 프로그램에 대한 `app.exe`)과 실제 응용 프로그램인 .dll 파일(예: `app.dll`)이 포함됩니다.

## <a name="frameworkdependent-deployments-fdd"></a>프레임워크 종속 배포(FDD) ##

FDD에서는 앱과 타사 종속성만 배포합니다. 앱에서 대상 시스템에 있는 .NET Core의 버전을 사용하므로 .NET Core를 배포할 필요가 없습니다. 이 배포는 .NET Core 앱의 기본 배포 모델입니다.

### <a name="why-create-a-frameworkdependent-deployment"></a>프레임워크 종속 배포를 만드는 이유 ###

FDD 배포에는 다음과 같은 여러 가지 장점이 있습니다.

- .NET Core 앱이 실행될 대상 운영 체제를 미리 정의할 필요가 없습니다. .NET Core는 운영 체제에 관계없이 실행 파일 및 라이브러리에 공용 PE 파일 형식을 사용하므로 기본 운영 체제에 관계없이 앱을 실행할 수 있습니다. PE 파일 형식에 대한 자세한 내용은 [.NET 어셈블리 파일 형식](../../standard/assembly-format.md)을 참조하세요.

- 배포 패키지의 크기가 작습니다. .NET Core 자체가 아닌 앱과 종속성만 배포해야 합니다.

- 여러 앱에서 동일한 .NET Core 설치를 사용하여 호스트 시스템에서 디스크 공간 및 메모리 사용량을 줄일 수 있습니다.

다음과 같은 몇 가지 단점도 있습니다.

- 대상으로 지정한 .NET Core의 버전이나 그 이상 버전이 호스트 시스템에 이미 설치된 경우에만 앱이 실행됩니다.

- .NET Core 런타임 및 라이브러리가 향후 릴리스에서 사용자 모르게 변경될 수 있습니다. 드문 경우지만 이로 인해 앱의 동작이 변경될 수 있습니다.

### <a name="deploying-a-frameworkdependent-deployment"></a>프레임워크 종속 배포 ###

타사 종속성이 없는 프레임워크 종속 배포에는 앱의 빌드, 테스트 및 게시만 포함됩니다. C#으로 작성된 간단한 예제에서는 이 프로세스를 보여 줍니다. 이 예제에서는 명령줄에서 [dotnet 유틸리티](../tools/dotnet.md)를 사용하지만 Visual Studio나 Visual Studio Code와 같은 개발 환경을 사용하여 예제를 컴파일, 테스트 및 게시할 수도 있습니다.

1. 프로젝트에 대한 디렉터리를 만들고 명령줄에서 [dotnet new](../tools/dotnet-new.md)를 입력하여 새 C# 콘솔 프로젝트를 만듭니다.

2. 편집기에서 `Program.cs` 파일을 열고 자동 생성된 코드를 다음 코드로 바꿉니다. 텍스트를 입력하라는 메시지가 표시된 다음 사용자가 입력한 개별 단어가 표시됩니다. 정규식 `\w+`를 사용하여 입력 테스트의 단어를 구분합니다.

    ```cs
    using System;
    using System.Text.RegularExpressions;

    namespace Applications.ConsoleApps
    {
        public class ConsoleParser
        {
            public static void Main()
            {
                 Console.WriteLine("Enter any text, followed by <Enter>:\n");
                 String s = Console.ReadLine();
                 ShowWords(s);
                 Console.Write("\nPress any key to continue... ");
                 Console.ReadKey();
          }

          private static void ShowWords(String s)
          {
              String pattern = @"\w+";
              var matches = Regex.Matches(s, pattern);
              if (matches.Count == 0)
                  Console.WriteLine("\nNo words were identified in your input.");
              else
              {
                  Console.WriteLine("\nThere are {0} words in your string:", matches.Count);
                  for (int ctr = 0; ctr < matches.Count; ctr++)
                      Console.WriteLine("   #{0,2}: '{1}' at position {2}", ctr,
                                        matches[ctr].Value, matches[ctr].Index);
              }
          }
      }
    }
    ```

3. [dotnet restore](../tools/dotnet-restore.md) 명령을 실행하여 프로젝트에 지정된 종속성을 복원합니다.

4. [dotnet build](../tools/dotnet-build.md) 명령을 사용하여 앱의 디버그 빌드를 만듭니다.

5. 프로그램을 디버그하고 테스트한 후에는 `dotnet publish -f netcoreapp1.0 -c release` 명령을 사용하여 앱과 함께 배포할 파일을 만들 수 있습니다. 그러면 앱의 디버그가 아닌 릴리스 버전이 만들어집니다.

   결과 파일은 프로젝트 `.\bin\release\netcoreapp1.0` 하위 디렉터리의 하위 디렉터리에 있는 `publish`는 디렉터리에 배치됩니다.

6. 게시 프로세스에서는 응용 프로그램의 파일과 함께 앱에 대한 디버깅 정보를 포함하는 프로그램 데이터베이스(.pdb) 파일을 내보냅니다. 이 파일은 기본적으로 디버깅 예외에 유용하며 응용 프로그램의 파일과 함께 패키지하지 않도록 선택할 수 있습니다.

응용 프로그램 파일의 전체 집합은 원하는 방식으로 배포할 수 있습니다. 예를 들어 zip 파일로 패키지하거나, 간단한 `copy` 명령을 사용하여 선택한 설치 패키지와 함께 배포할 수 있습니다.

설치 관리자는 응용 프로그램 이진 외에도 공유 프레임워크 설치 관리자를 번들로 제공하거나 응용 프로그램 설치의 일부로 필수 조건을 확인해야 합니다.  공유 프레임워크 설치는 시스템 수준이므로 관리자/루트 액세스 권한이 필요합니다.

### <a name="deploying-a-frameworkdependent-deployment-with-thirdparty-dependencies"></a>타사 종속성이 있는 프레임워크 종속 배포 ###

하나 이상의 타사 종속성이 있는 프레임워크 종속 배포에는 `dotnet restore` 명령을 실행하기 전에 세 가지 추가 단계가 포함됩니다.

1. 모든 타사 라이브러리에 대한 참조를 `project.json` 파일의 `dependencies` 섹션에 추가합니다. 다음 `dependencies` 섹션에서는 Json.NET을 타사 라이브러리로 사용합니다.

    ```json
    "dependencies": {
      "Microsoft.NETCore.App": {
        "type": "platform",
        "version": "1.0.0"
      },
      "Newtonsoft.Json": "9.0.1"
    },
    ```

2. 타사 종속성을 포함하는 NuGet 패키지를 아직 다운로드하지 않은 경우 다운로드합니다. 패키지를 다운로드하려면 종속성을 추가한 후 `dotnet restore` 명령을 실행합니다. 종속성은 게시 시간에 로컬 NuGet 캐시에서 확인되므로 시스템에서 사용할 수 있어야 합니다.

타사 종속성이 있는 프레임워크 종속 배포는 타사 종속성만큼만 이식 가능합니다. 예를 들어 타사 라이브러리에서 macOS를 지원하는 경우 Windows 시스템에 앱을 이식할 수 없습니다. 이러한 현상은 타사 종속성 자체가 네이티브 코드에 종속된 경우에 발생할 수 있습니다. 좋은 예로 Kestrel 서버가 있습니다. 이런 종류의 타사 종속성이 있는 응용 프로그램에 대해 FDD를 만들면 게시된 출력에는 기본 종속성에서 지원하고 NuGet 패키지에 있는 각 [RID(런타임 식별자)](../rid-catalog.md#what-are-rids)에 대한 폴더가 포함됩니다.

## <a name="selfcontained-deployments-scd"></a>자체 포함 배포(SCD) ##

자체 포함 배포에서는 앱과 타사 종속성뿐만 아니라 앱을 빌드하는 데 사용한 .NET Core의 버전도 배포합니다. 그러나 SCD 만들기에서는 [.NET Core의 기본 종속성](https://github.com/dotnet/core/blob/master/Documentation/prereqs.md) 자체를 다양한 플랫폼(예: macOS의 OpenSSL)에 포함하지 않으므로 응용 프로그램을 실행하기 전에 설치할 필요가 없습니다. 

### <a name="why-deploy-a-selfcontained-deployment"></a>자체 포함 배포를 배포하는 이유 ###

자체 포함 배포를 배포하면 다음과 같은 두 가지 주요 장점이 있습니다.

- 앱과 함께 배포되는 .NET Core 버전을 유일하게 제어할 수 있습니다. .NET Core만 제공할 수 있습니다.

- 앱이 실행될 .NET Core 버전을 제공하므로 대상 시스템에서 .NET Core 앱을 실행할 수 있다고 보장할 수 있습니다.

다음과 같은 여러 가지 단점도 있습니다.

- .NET Core가 배포 패키지에 포함되므로 배포 패키지를 빌드할 대상 플랫폼을 미리 선택합니다.

- .NET Core뿐만 아니라 앱과 해당 타사 종속성도 포함해야 하므로 배포 패키지의 크기가 상대적으로 큽니다.

- 다양한 자체 포함 .NET Core 앱을 시스템에 배포하면 각 앱에서 .NET Core 파일을 중복하므로 엄청나게 많은 디스크 공간을 사용합니다.

### <a name="a-namesimpleselfa-deploying-a-simple-selfcontained-deployment"></a>간단한 자체 포함 배포 ###

타사 종속성이 없는 자체 포함 배포에는 프로젝트 만들기, project.json 파일 수정, 앱 빌드, 테스트 및 수정이 포함됩니다.  C#으로 작성된 간단한 예제에서는 이 프로세스를 보여 줍니다. 이 예제에서는 명령줄에서 `dotnet` 유틸리티를 사용하지만 Visual Studio나 Visual Studio Code와 같은 개발 환경을 사용하여 예제를 컴파일, 테스트 및 게시할 수도 있습니다.

1. 프로젝트에 대한 디렉터리를 만들고 명령줄에서 `dotnet new`를 입력하여 새 C# 콘솔 프로젝트를 만듭니다.

2. 편집기에서 `Program.cs` 파일을 열고 자동 생성된 코드를 다음 코드로 바꿉니다. 텍스트를 입력하라는 메시지가 표시된 다음 사용자가 입력한 개별 단어가 표시됩니다. 정규식 `\w+`를 사용하여 입력 테스트의 단어를 구분합니다.

    ```cs
    using System;
    using System.Text.RegularExpressions;

    namespace Applications.ConsoleApps
    {
        public class ConsoleParser
        {
            public static void Main()
            {
                 Console.WriteLine("Enter any text, followed by <Enter>:\n");
                 String s = Console.ReadLine();
                 ShowWords(s);
                 Console.Write("\nPress any key to continue... ");
                 Console.ReadKey();
          }

          private static void ShowWords(String s)
          {
              String pattern = @"\w+";
              var matches = Regex.Matches(s, pattern);
              if (matches.Count == 0)
                  Console.WriteLine("\nNo words were identified in your input.");
              else {
                  Console.WriteLine("\nThere are {0} words in your string:", matches.Count);
                  for (int ctr = 0; ctr < matches.Count; ctr++)
                      Console.WriteLine("   #{0,2}: '{1}' at position {2}", ctr,
                                        matches[ctr].Value, matches[ctr].Index);
              }
          }
      }
    }
    ```

3. `project.json` 파일을 열고 `frameworks` 섹션에서 다음 줄을 제거합니다.

   ```json
   "type": "platform",
   ```
수정한 후에는 프레임워크 섹션이 다음과 같이 표시되어야 합니다.

    ```json
    "frameworks": {
      "netcoreapp1.0": {
        "dependencies": {
          "Microsoft.NETCore.App": {
             "version": "1.0.0"
          }
        }
      }
    }
    ```
`"type": "platform"` 특성을 제거하면 프레임워크가 시스템 차원 플랫폼 패키지가 아니라 앱에 로컬인 구성 요소 집합으로 제공됨을 나타냅니다.

4. `project.json` 파일에서 앱의 대상 플랫폼을 정의하는 `runtimes` 섹션을 만들고 대상으로 지정한 각 플랫폼의 런타임 식별자를 지정합니다. 런타임 식별자 목록은 [런타임 식별자 카탈로그](../rid-catalog.md)를 참조하세요. 예를 들어 다음 `runtimes` 섹션은 앱이 64비트 Windows 10 운영 체제 및 64비트 OS X 버전 10.10 운영 체제에서 실행됨을 나타냅니다.

    ```json
        "runtimes": {
          "win10-x64": {},
          "osx.10.10-x64": {}
        }
    ```
이전 섹션과 `runtimes` 섹션을 구분하려면 쉼표를 추가해야 합니다.
전체 샘플 `project.json` 파일은 이 섹션의 뒷부분에 나옵니다.

6. `dotnet restore` 명령을 실행하여 프로젝트에 지정된 종속성을 복원합니다.

7. 각 대상 플랫폼에서 `dotnet build` 명령을 사용하여 앱의 디버그 빌드를 만듭니다. 빌드하려는 런타임 식별자를 지정하지 않으면 `dotnet build` 명령은 현재 시스템의 런타임 ID에 대한 빌드만 만듭니다. 다음 명령을 사용하여 두 대상 플랫폼에 대한 앱을 빌드할 수 있습니다.

    ```console
    dotnet build -r win10-x64
    dotnet build -r osx.10.10-x64
    ```
각 플랫폼에 대한 앱의 디버그 빌드는 프로젝트의 `.\bin\Debug\netcoreapp1.0\<runtime_identifier>` 하위 디렉터리에 있습니다.

8. 프로그램을 디버그하고 테스트한 후에는 다음과 같이 두 대상 플랫폼에 `dotnet publish` 명령을 사용하여 각 대상 플랫폼에 대해 앱과 함께 배포할 파일을 만들 수 있습니다.

   ```console
   dotnet publish -c release -r win10-x64
   dotnet publish -c release -r osx.10.10-x64
   ```
그러면 각 대상 플랫폼에 대해 앱의 디버그가 아닌 릴리스 버전이 만들어집니다. 결과 파일은 프로젝트 `.\bin\release\netcoreapp1.0\<runtime_identifier>` 하위 디렉터리의 하위 디렉터리에 있는 `publish`라는 하위 디렉터리에 배치됩니다. 각 하위 디렉터리에는 앱을 시작하는 데 필요한 전체 파일 집합(앱 파일 및 모든 .NET Core 파일)이 포함됩니다.

9. 게시 프로세스에서는 응용 프로그램의 파일과 함께 앱에 대한 디버깅 정보를 포함하는 프로그램 데이터베이스(.pdb) 파일을 내보냅니다. 이 파일은 기본적으로 디버깅 예외에 유용하며 응용 프로그램의 파일과 함께 패키지하지 않도록 선택할 수 있습니다.

게시된 파일은 원하는 방식으로 배포할 수 있습니다. 예를 들어 zip 파일로 패키지하거나, 간단한 `copy` 명령을 사용하여 선택한 설치 패키지와 함께 배포할 수 있습니다. 

다음은 이 프로젝트에 대한 전체 `project.json` 파일입니다.

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
      }
    }
  },
  "runtimes": {
    "win10-x64": {},
    "osx.10.10-x64": {}
  }
}
```

### <a name="deploying-a-selfcontained-deployment-with-thirdparty-dependencies"></a>타사 종속성이 있는 자체 포함 배포 ###

하나 이상의 타사 종속성이 있는 자체 포함 배포에는 타사 종속성 추가가 포함됩니다.

1. 모든 타사 라이브러리에 대한 참조를 `project.json` 파일의 `dependencies` 섹션에 추가합니다. 다음 `dependencies` 섹션에서는 Json.NET을 타사 라이브러리로 사용합니다.

    ```json
    "dependencies": {
      "Microsoft.NETCore.App": "1.0.0",
      "Newtonsoft.Json": "9.0.1"
    },
    ```
2. 타사 종속성을 포함하는 NuGet 패키지를 시스템에 아직 다운로드하지 않은 경우 다운로드합니다. 앱에서 종속성을 사용할 수 있도록 하려면 종속성을 추가한 후 `dotnet restore` 명령을 실행합니다. 종속성은 게시 시간에 로컬 NuGet 캐시에서 확인되므로 시스템에서 사용할 수 있어야 합니다.

다음은 이 프로젝트에 대한 전체 project.json 파일입니다.

```json
{
  "version": "1.0.0-*",
  "buildOptions": {
    "debugType": "portable",
    "emitEntryPoint": true
  },
  "dependencies": {
    "Microsoft.NETCore.App": "1.0.0",
    "Newtonsoft.Json": "9.0.1"
  },
  "frameworks": {
    "netcoreapp1.0": {
    }
  },
  "runtimes": {
    "win10-x64": {},
    "osx.10.10-x64": {}
  }
}
```

응용 프로그램을 배포하면 앱에서 사용된 타사 종속성도 응용 프로그램 파일에 포함됩니다. 타사 라이브러리는 앱이 실행되는 시스템에 없어도 됩니다.

타사 라이브러리가 있는 자체 포함 배포는 해당 라이브러리에서 지원하는 플랫폼에만 배포할 수 있습니다. 이 배포는 기본 종속성과 함께 타사 종속성이 있는 프레임워크 종속 배포와 유사합니다. 

### <a name="deploying-a-selfcontained-deployment-with-a-smaller-footprint"></a>공간이 더 작은 자체 포함 배포 ###

대상 시스템에서 적절한 저장소 공간의 가용성이 문제가 될 경우 일부 시스템 구성 요소를 제외하여 앱의 전체 공간을 줄일 수 있습니다. 이렇게 하려면 앱에서 project.json 파일에 포함하는 .NET Core 구성 요소를 명시적으로 정의합니다.

공간이 더 작은 자체 포함 배포를 만들려면 자체 포함 배포를 만드는 처음 두 단계를 수행하여 시작합니다. `dotnet new` 명령을 실행하고 C# 소스 코드를 앱에 추가했으면 다음을 수행합니다.

1. `project.json` 파일을 열고 `frameworks` 섹션을 다음으로 바꿉니다.

    ```json
    "frameworks": {
      "netstandard1.6": { }
    }
    ```
이 작업은 두 가지 역할을 합니다.

    * .NET Core CLR, .NET Core 라이브러리 및 여러 가지 기타 시스템 구성 요소를 포함하는 전체 `netcoreapp1.0` 프레임워크를 사용하는 대신 앱이 .NET 표준 라이브러리를 사용함을 나타냅니다.

    * `"type": "platform"` 특성을 제거하면 프레임워크가 시스템 차원 플랫폼 패키지가 아니라 앱에 로컬인 구성 요소 집합으로 제공됨을 나타냅니다.

2. `dependencies` 섹션을 다음으로 바꿉니다.

    ```json
    "dependencies": {
      "NETStandard.Library": "1.6.0",
      "Microsoft.NETCore.Runtime.CoreCLR": "1.0.2",
      "Microsoft.NETCore.DotNetHostPolicy":  "1.0.1"
    },
    ```
   앱에서 사용하는 시스템 구성 요소를 정의합니다. 앱과 함께 패키지되는 시스템 구성 요소에는 .NET 표준 라이브러리, .NET Core 런타임 및 .NET Core 호스트가 포함됩니다. 그러면 공간이 더 작은 자체 포함 배포가 생성됩니다.

3. [간단한 자체 포함 배포](#simpleSelf)에서처럼 `project.json` 파일에서 앱의 대상 플랫폼을 정의하는 `runtimes` 섹션을 만들고 대상으로 지정한 각 플랫폼의 런타임 식별자를 지정합니다. 런타임 식별자 목록은 [런타임 식별자 카탈로그](../rid-catalog.md)를 참조하세요. 예를 들어 다음 `runtimes` 섹션은 앱이 64비트 Windows 10 운영 체제 및 64비트 OS X 버전 10.10 운영 체제에서 실행됨을 나타냅니다.

    ```json
        "runtimes": {
          "win10-x64": {},
          "osx.10.10-x64": {}
        }
    ```
이전 섹션과 `runtimes` 섹션을 구분하려면 쉼표를 추가해야 합니다.
전체 샘플 `project.json` 파일은 이 섹션의 뒷부분에 나옵니다.

4. `dotnet restore` 명령을 실행하여 프로젝트에 지정된 종속성을 복원합니다.

5. 각 대상 플랫폼에서 `dotnet build` 명령을 사용하여 앱의 디버그 빌드를 만듭니다. 빌드하려는 런타임 식별자를 지정하지 않으면 `dotnet build` 명령은 현재 시스템의 런타임 ID에 대한 빌드만 만듭니다. 다음 명령을 사용하여 두 대상 플랫폼에 대한 앱을 빌드할 수 있습니다.

    ```console
    dotnet build -r win10-x64
    dotnet build -r osx.10.10-x64
    ```

6. 프로그램을 디버그하고 테스트한 후에는 다음과 같이 두 대상 플랫폼에 `dotnet publish` 명령을 사용하여 각 대상 플랫폼에 대해 앱과 함께 배포할 파일을 만들 수 있습니다.

   ```console
   dotnet publish -c release -r win10-x64
   dotnet publish -c release -r osx.10.10-x64
   ```
그러면 각 대상 플랫폼에 대해 앱의 디버그가 아닌 릴리스 버전이 만들어집니다. 결과 파일은 프로젝트 `.\bin\release\netstandard1.6\<runtime_identifier>` 하위 디렉터리의 하위 디렉터리에 있는 `publish`라는 하위 디렉터리에 배치됩니다. 각 하위 디렉터리에는 앱을 시작하는 데 필요한 전체 파일 집합(앱 파일 및 모든 .NET Core 파일)이 포함됩니다.

7. 게시 프로세스에서는 응용 프로그램의 파일과 함께 앱에 대한 디버깅 정보를 포함하는 프로그램 데이터베이스(.pdb) 파일을 내보냅니다. 이 파일은 기본적으로 디버깅 예외에 유용하며 응용 프로그램의 파일과 함께 패키지하지 않도록 선택할 수 있습니다.

게시된 파일은 원하는 방식으로 배포할 수 있습니다. 예를 들어 zip 파일로 패키지하거나, 간단한 `copy` 명령을 사용하여 선택한 설치 패키지와 함께 배포할 수 있습니다. 

다음은 이 프로젝트에 대한 전체 `project.json` 파일입니다.

```json
   {
     "version": "1.0.0-*",
     "buildOptions": {
       "debugType": "portable",
       "emitEntryPoint": true
     },
     "dependencies": {
       "NETStandard.Library": "1.6.0",
       "Microsoft.NETCore.Runtime.CoreCLR": "1.0.2",
       "Microsoft.NETCore.DotNetHostPolicy":  "1.0.1"
     },
     "frameworks": {
       "netstandard1.6": { }
     },
     "runtimes": {
       "win10-x64": {},
       "osx.10.10-x64": {}
     }
   }
```



<!--HONumber=Nov16_HO3-->


