---
title: "Windows에서 .NET Core 시작"
description: "Visual Studio 2015를 사용하여 Windows에서 .NET Core 시작"
keywords: .NET, .NET Core
author: bleroy
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: d743134a-08a3-4ff6-aab7-49f71f0568c3
translationtype: Human Translation
ms.sourcegitcommit: 54da8aebd64e86c064214074bc261f72c3b0aedc
ms.openlocfilehash: 299d479ce74a0e1f41ff42a0e6619f4964788194

---

# <a name="getting-started-with-net-core-on-windows-using-visual-studio-2015"></a>Visual Studio 2015를 사용하여 Windows에서 .NET Core 시작

작성자: [Bertrand Le Roy](https://github.com/bleroy) 및 [Phillip Carter](https://github.com/cartermp)

Visual Studio 2015는 .NET Core 응용 프로그램 개발을 위해 필요한 모든 기능을 갖춘 개발 환경을 제공합니다. 이 문서의 절차에서는 Visual Studio 사용하여 일반적인 .NET Core 솔루션 또는 .NET Core 구성 요소가 포함된 솔루션을 빌드하는 데 필요한 단계를 설명합니다. 최신 .NET Core 버전용으로 명시적으로 빌드되지 않은 타사 라이브러리의 테스트 및 사용도 시나리오에 포함됩니다. 

## <a name="prerequisites"></a>필수 조건

[필수 조건 페이지](../windows-prerequisites.md)의 지침에 따라 환경을 업데이트합니다.

## <a name="getting-started"></a>시작

다음 단계를 통해 .NET Core 개발을 위한 Visual Studio 2015를 설정합니다.

1. Visual Studio를 열고 **파일** 메뉴에서 **새로 만들기**, **프로젝트**를 선택합니다.

2. **새 프로젝트** 대화 상자의 **템플릿** 목록에서 **Visual C#** 노드를 확장하고 **.NET Core**를 선택합니다. **클래스 라이브러리(.NET Core)**, **콘솔 응용 프로그램(.NET Core)** 및 **ASP.NET Core 웹 응용 프로그램(.NET Core)**에 대한 세 개의 새 프로젝트 템플릿이 표시됩니다.

<a name="a-solution-using-only-net-core-projects"></a>.NET Core 프로젝트만을 사용하는 솔루션
----------------------------------------

### <a name="writing-the-library"></a>라이브러리 작성

1. Visual Studio에서 **파일**, **새로 만들기**, **프로젝트**를 선택합니다. **새 프로젝트** 대화 상자에서 **Visual C#** 노드를 확장하고 **.NET Core** 노드를 선택한 다음 **클래스 라이브러리(.NET Core)**를 선택합니다. 

2. 프로젝트 이름을 "Library", 솔루션 이름을 "Golden"으로 지정합니다. **솔루션용 디렉터리 만들기** 확인란을 선택한 상태로 둡니다. **확인**을 클릭합니다.

3. 솔루션 탐색기에서 **참조** 노드의 상황에 맞는 메뉴를 열고 **NuGet 패키지 관리**를 선택합니다.

4. **패키지 소스**로 "nuget.org"를 선택하고 **찾아보기** 탭을 선택합니다. **Newtonsoft.Json**을 찾습니다. **설치**를 클릭합니다. 

5. **참조** 노드의 상황에 맞는 메뉴를 열고 **패키지 복원**을 선택합니다.

6. `Class1.cs` 파일이 이름을 `Thing.cs`로 바꿉니다. 클래스의 이름 바꾸기를 적용합니다. 생성자를 제거하고 메서드를 추가합니다.`public int Get(int number) => Newtonsoft.Json.JsonConvert.DeserializeObject<int>($"{number}");`

7. **빌드** 메뉴에서 **솔루션 빌드**를 선택합니다.

   솔루션이 오류 없이 빌드됩니다.

### <a name="writing-the-test-project"></a>테스트 프로젝트 작성

1. 솔루션 탐색기에서 **솔루션** 노드의 상황에 맞는 메뉴를 열고 **추가**, **새 솔루션 폴더**를 선택합니다. 폴더 이름을 "test"로 지정합니다. 
   이것은 실제 폴더가 아니라 솔루션 폴더일 뿐입니다.

2. **test** 폴더의 상황에 맞는 메뉴를 열고 **추가**를 선택합니다. **새 프로젝트**. **새 프로젝트** 대화 상자에서 **콘솔 응용 프로그램(.NET Core)**을 선택합니다. 이름을 "TestLibrary"로 지정하고 `Golden\test` 경로에 명시적으로 저장합니다. 

> [!IMPORTANT]
> 프로젝트는 클래스 라이브러리가 아니라 콘솔 응용 프로그램이어야 합니다.

3. **TestLibrary** 프로젝트에서 **참조** 노드의 상황에 맞는 메뉴를 열고 **참조 추가**를 선택합니다. 

4. **참조 관리자** 대화 상자에서 **프로젝트** 아래에 있는 **라이브러리**, **솔루션** 노드를 선택한 후 **확인**을 클릭합니다. 

5. **TestLibrary** 프로젝트에서 `project.json` 파일을 열고 `"Library": "1.0.0-*"`을 `"Library": {"target": "project", "version": "1.0.0-*"}`으로 바꿉니다. 

   이는 `Library` 프로젝트가 동일한 이름의 NuGet 패키지로 해석되는 것을 방지하기 위해서입니다. 대상을 "프로젝트"로 명시적으로 설정하면, 도구가 먼저 해당 이름의 프로젝트(패키지가 아닌)를 검색합니다. 
   
6. **TestLibrary** 프로젝트에서 **참조** 노드의 상황에 맞는 메뉴를 열고 **패키지 복원**을 선택합니다.

7. **참조** 노드의 상황에 맞는 메뉴를 열고 **NuGet 패키지 관리**를 선택합니다.

8. **패키지 소스**로 "nuget.org"를 선택하고 **찾아보기** 탭을 선택합니다. **시험판 포함** 확인란을 선택한 다음 **xUnit** 2.2.0 버전 이상을 검색하고 **설치**를 클릭합니다. 

9. **dotnet-test-xunit** 버전 2.2.0 이상을 검색하고 **설치**를 클릭합니다.

10. `project.json`을 편집하고 `"imports": "dnxcore50"`을 `"imports": [ "dnxcore50", "portable-net45+win8" ]`로 바꿉니다. 

   이렇게 하면 프로젝트에서 xunit 라이브러리를 올바르게 복원하고 사용합니다. 이러한 라이브러리는 "portable-net45+win8"이 포함된 이식 가능한 프로필과 함께 사용하도록 컴파일되었습니다. .NET Core는 이러한 라이브러리의 빌드 당시 존재하지 않았습니다. `import`는 빌드 시 도구 버전 검사 기능을 완화합니다. 이제 오류 없이 패키지를 복원할 수 있습니다.

11. `project.json`을 편집하여 `"frameworks"` 섹션 뒤에 `"testRunner": "xunit",`를 추가합니다.

12. **TestLibrary** 프로젝트에 `LibraryTests.cs` 클래스 파일을 추가하고, `using` 지시문 `using Xunit;` 및 `using Library;`를 파일 상단에 추가하고, 다음 코드를 클래스에 추가합니다.
    ```csharp
    [Fact]
    public void ThingGetsObjectValFromNumber() {
        Assert.Equal(42, new Thing().Get(42));
    }
    ```
    * 선택적으로, **TestLibrary** 프로젝트에서 `Program.cs` 파일을 삭제하고 `project.json`에서 `"buildOptions": {"emitEntryPoint": true},`를 제거합니다.

   이제 솔루션을 빌드할 수 있습니다. 
   
13. **테스트** 메뉴에서 **Windows**, **테스트 탐색기**를 선택하고, 테스트 탐색기에서 **모두 실행**을 선택합니다.
   
   테스트를 전달해야 합니다.

### <a name="writing-the-console-app"></a>콘솔 앱 작성

1. 솔루션 탐색기에서 `src` 폴더의 상황에 맞는 메뉴를 열고 새 **콘솔 응용 프로그램(.NET Core)** 프로젝트를 추가합니다. 이름을 "App"으로 지정하고 위치를 `Golden\src`로 설정합니다.

2. **App** 프로젝트에서 **참조** 노드의 상황에 맞는 메뉴를 열고 **추가**, **참조**를 선택합니다. 

3. **참조 관리자** 대화 상자에서 **프로젝트** 아래에 있는 **라이브러리**, **솔루션** 노드를 선택한 후 **확인**을 클릭합니다.

4. **App** 프로젝트에서 `project.json` 파일을 열고 `"Library": "1.0.0-*"`을 `"Library": {"target": "project"}`로 바꿉니다.

5. **참조** 노드의 상황에 맞는 메뉴를 열고 **패키지 복원**을 선택합니다.

6. **앱** 노드의 상황에 맞는 메뉴를 열고 **시작 프로젝트로 설정**을 선택합니다.

7. `Program.cs` 파일을 열고 `using Library;` 지시문을 파일의 상단에 추가한 다음 `Console.WriteLine($"The answer is {new Thing().Get(42)}");`을 `Main` 메서드에 추가합니다.

8. 방금 추가한 줄 뒤에 중단점을 설정합니다.

9. F5 키를 눌러 응용 프로그램을 실행합니다.

   응용 프로그램이 오류 없이 빌드되고 중단점에 도달합니다. 또한 응용 프로그램 출력이 "The answer is 42."인지 확인할 수 있습니다.

<a name="a-mixed-net-core-library-and-net-framework-application"></a>.NET Core 라이브러리 및 .NET Framework 응용 프로그램 혼합
--------------------------------------------------------

이전 스크립트에서 얻은 솔루션부터 시작하여 다음 단계를 실행합니다.

1. 솔루션 탐색기에서 **라이브러리** 프로젝트에 대한 `project.json` 파일을 열고 `"frameworks": {
    "netstandard1.6" }`을 `"frameworks": {
    "netstandard1.4" }`로 바꿉니다.

2. **Library** 프로젝트에서 **참조** 노드의 상황에 맞는 메뉴를 열고 **패키지 복원**을 선택합니다.

   솔루션이 여전히 빌드되며 전처럼 정확히 작동합니다. 테스트가 통과되고 콘솔 응용 프로그램이 실행되며 디버깅 가능합니다.

3. **Library** 프로젝트에서 상황에 맞는 메뉴를 열고 **빌드**를 선택합니다.

4. 솔루션 탐색기에서 `src` 폴더의 상황에 맞는 메뉴를 열고 **추가**, **새 프로젝트**를 선택합니다.

5. **새 프로젝트** 대화 상자에서 **Visual C#** 노드를 선택한 다음 **콘솔 응용 프로그램**을 선택합니다.

> [!IMPORTANT]
> .NET Core 버전이 아니라 표준 콘솔 응용 프로그램을 선택했는지 확인합니다. 이 섹션에서는 .NET Framework 응용 프로그램에서 라이브러리를 사용합니다.

6. 프로젝트 이름을 "FxApp"으로 지정하고 위치를 `Golden\src`로 설정합니다.

7. **FxApp** 프로젝트에서 **참조** 노드의 상황에 맞는 메뉴를 열고 **참조 추가**를 선택합니다.

8. **참조 관리자** 대화 상자에서 **찾아보기**를 선택하고, 빌드된 `Library.dll`의 위치(..Golden\src\Library\bin\Debug\netstandard1.4 경로 아래)로 이동한 다음 **추가**를 클릭합니다. 

   또한 라이브러리를 패키지하고, .NET Framework에서 .NET Core 코드를 참조하는 또 다른 방법으로서 패키지를 참조할 수 있습니다.

9. **참조** 노드의 상황에 맞는 메뉴를 열고 **NuGet 패키지 관리**를 선택합니다.

10. **패키지 소스**로 "nuget.org"를 선택하고 **찾아보기** 탭을 선택합니다. **시험판 포함** 확인란을 선택한 다음 **Newtonsoft.Json**을 찾습니다. **설치**를 클릭합니다. 

11. **FxApp** 프로젝트에서 `Program.cs` 파일을 열고, 파일 상단에 `using Library;` 지시문을 추가하고, `Console.WriteLine($"The answer is {new Thing().Get(42)}.");`을 프로그램의 `Main` 메서드에 추가합니다.

12. 방금 추가한 줄 뒤에 중단점을 설정합니다.

13. **FxApp**을 솔루션의 시작 응용 프로그램으로 설정합니다.

14. F5 키를 눌러 앱을 실행합니다.

   응용 프로그램이 빌드되고 중단점에 도달합니다. 응용 프로그램 출력이 "The answer is 42."여야 합니다.
   
   > [!TIP]
   > Windows 플랫폼에서 MSTest를 사용할 수 있습니다. 자세한 내용은 [Using MSTest on Windows document(Windows 문서에서 MSTest 사용)](../testing/using-mstest-on-windows.md)를 참조하세요.

<a name="moving-a-library-from-netstandard-14-to-13"></a>netstandard 1.4에서 1.3으로 라이브러리 이동
--------------------------------------------

1. 솔루션 탐색기의 **라이브러리** 프로젝트에서 `project.json` 파일을 엽니다.

2. `frameworks": { "netstandard1.4" }`을 `frameworks": { "netstandard1.3" }`로 바꿉니다.

3. **Library** 프로젝트에서 **참조** 노드의 상황에 맞는 메뉴를 열고 **패키지 복원**을 선택합니다.

4. **빌드** 메뉴에서 **라이브러리 빌드**를 선택합니다.

5. **FxApp**에서 `Library` 참조를 제거하고 ..Golden\src\Library\bin\Debug\netstandard1.3 경로를 사용하여 다시 추가합니다. 이제 1.3 버전을 참조합니다.

6. F5 키를 눌러 응용 프로그램을 실행합니다.

   모든 것이 여전히 이전처럼 작동합니다. 응용 프로그램 출력이 "The answer is 42."이고, 중단점에 도달하고, 변수를 검사할 수 있는지 확인합니다.

<a name="a-mixed-pcl-library-and-net-framework-application"></a>PCL 라이브러리 및 .NET Framework 응용 프로그램 혼합
--------------------------------------------------

이전 솔루션이 열려 있었다면 이를 닫습니다. 이 섹션에서 새 스크립트를 시작하게 됩니다.

### <a name="writing-the-library"></a>라이브러리 작성

1. Visual Studio에서 **파일**, **새로 만들기**, **프로젝트**를 선택합니다. **새 프로젝트** 대화 상자에서 **Visual C#** 노드를 확장하고 **클래스 라이브러리(iOS, Android 및 Windows의 경우 이식 가능)**를 선택합니다. 

2. 프로젝트 이름을 "PCLLibrary", 솔루션 이름을 "GoldenPCL"로 지정합니다. **솔루션용 디렉터리 만들기** 확인란을 선택한 상태로 둡니다. **확인**을 클릭합니다.

3. 솔루션 탐색기에서 **참조** 노드의 상황에 맞는 메뉴를 열고 **NuGet 패키지 관리**를 선택합니다.

4. **패키지 소스**로 "nuget.org"를 선택하고 **찾아보기** 탭을 선택합니다. **시험판 포함** 확인란을 선택한 다음 **Newtonsoft.Json**을 찾습니다. **설치**를 클릭합니다. 

5. 클래스 이름을 "Thing"으로 바꾸고 메서드를 추가합니다.`public int Get(int number) => Newtonsoft.Json.JsonConvert.DeserializeObject<int>($"{number}");`

6. **빌드** 메뉴에서 **솔루션 빌드**를 선택하고, 솔루션이 빌드되는지 확인합니다.

### <a name="writing-the-console-app"></a>콘솔 앱 작성

1. 솔루션 탐색기에서 **솔루션 'GoldenPCL'** 노드의 상황에 맞는 메뉴를 열고 **추가**를 선택합니다. **새 프로젝트**. **새 프로젝트** 대화 상자에서 **Visual C#** 노드를 확장하고 **콘솔 응용 프로그램**을 선택하고 프로젝트 이름을 "App"으로 지정합니다. 

2. **App** 프로젝트에서 **참조** 노드의 상황에 맞는 메뉴를 열고 **추가**, **참조**를 선택합니다. 

3. **참조 관리자** 대화 상자에서 **찾아보기**를 선택하고, 빌드된 `PCLLibrary.dll`의 위치(..\GoldenPCL\PCLLibrary\bin\Debug 경로 아래)로 이동한 다음 **추가**를 클릭합니다. 

4. **App** 프로젝트에서 `Program.cs` 파일을 열고, 파일 상단에 `using PCLLibrary;` 지시문을 추가하고, `Console.WriteLine($"The answer is {new Thing().Get(42)}.");`을 프로그램의 `Main` 메서드에 추가합니다.

5. 방금 추가한 줄 뒤에 중단점을 설정합니다.

6. 솔루션 탐색기에서 **앱** 노드의 상황에 맞는 메뉴를 열고 **시작 프로젝트로 설정**을 선택합니다.

7. F5 키를 눌러 앱을 실행합니다.

   응용 프로그램이 빌드 및 실행되고 "The answer is 42." 출력 이후 중단점에 도달합니다.

<a name="moving-a-pcl-to-a-netstandard-library"></a>PCL을 NetStandard 라이브러리로 이동
-------------------------------------
이식 가능한 클래스 라이브러리 도구는 .NET 표준을 대상으로 지정하도록 PCL을 자동으로 수정할 수 있습니다. 

1.  "속성" 노드를 두 번 클릭하여 프로젝트 속성 페이지를 엽니다.

2.  "대상 지정 헤더"에서 ".NET 플랫폼 표준을 대상으로 지정" 하이퍼링크를 클릭합니다.

3.  확인 메시지가 표시되면 "예"를 클릭합니다.

PCL에서 원래 대상으로 지정한 모든 대상을 포함하는 .NET 표준 버전이 자동으로 선택됩니다. 프로젝트 속성 페이지에서 .NET 표준 드롭다운을 사용하여 .NET 표준의 다른 버전을 대상으로 지정할 수 있습니다.
 
* 전에 packages.config가 있었던 경우, 변환 전에 설치된 모든 패키지를 제거하라는 메시지가 표시될 수 있습니다.

### <a name="manually-edit-projectjson-to-target-net-standard-from-an-existing-portable-class-library"></a>기존의 이식 가능한 클래스 라이브러리에서 .NET 표준을 대상으로 지정하도록 project.json을 수동으로 편집

1.  project.json의 "supports" 요소에 "dnxcore50"이 포함된 경우 이를 제거합니다.

2.  "Microsoft.NETCore"에 대한 종속성을 제거합니다.

3.  "Microsoft.NETCore.Portable.Compatibility" 버전 "1.0.0"에 대한 종속성을 버전 "1.0.1"로 수정합니다.

4.  "NETStandard.Library" 버전 "1.6.0"에 대한 종속성을 추가합니다.

5.  "frameworks" 요소에서 "dotnet" 프레임워크(및 그 안의 "imports" 요소)를 제거합니다.

6.  frameworks 요소에 ` "netstandard1.x” : { } `를 추가합니다. 여기서 x는 대상으로 지정할 .NET 표준의 버전으로 교체됩니다.

### <a name="example-projectjson"></a>project.json 예제

이 project.json은 UWP 및 .NET 4.6에 대한 지원 절을 포함하며, netstandard1.3을 대상으로 지정합니다.
```
{
  "supports": {
    "net46.app": {},
    "uwp.10.0.app": {},
  },
  "dependencies": {
    "NETStandard.Library": "1.6.0",
    "Microsoft.NETCore.Portable.Compatibility": "1.0.1"
  },
  "frameworks": {
    "netstandard1.3" : {}
  }
}
```





<!--HONumber=Nov16_HO1-->


