---
title: "Windows에서 .NET Core와 함께 MSTest 사용"
description: "Windows에서 Visual Studio 2015를 사용하여 .NET Core와 함께 MSTest를 사용하는 방법"
keywords: MSTest, .NET, .NET Core
author: ncarandini
manager: wpickett
ms.date: 08/18/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: ed447641-3e85-4e50-b7ed-004630048a3e
translationtype: Human Translation
ms.sourcegitcommit: 6795f1ace77e6f4d1b2fe07e6281f4acb6d21271
ms.openlocfilehash: 11faa46347b18240e9ddf1c27f27a814103e2565

---

# <a name="unit-testing-with-mstest-and-net-core-on-windows-using-visual-studio-2015"></a>Windows에서 Visual Studio 2015를 사용하여 MSTest 및 .NET Core로 단위 테스트

여러 플랫폼을 대상으로 할 경우 xUnit을 선택하는 것이 더 나을 수 있지만, Windows에서 .NET Core와 함께 MSTest를 사용할 수도 있습니다.

## <a name="prerequisites"></a>필수 조건

[Windows에서 .NET Core 시작](../tutorials/using-on-windows.md)의 지침에 따라 라이브러리 프로젝트를 만듭니다.

### <a name="writing-the-test-project-using-mstest"></a>MSTest를 사용하여 테스트 프로젝트 작성

1. 솔루션 탐색기에서 **솔루션** 노드의 상황에 맞는 메뉴를 열고 **추가**, **새 솔루션 폴더**를 선택합니다. 폴더 이름을 "test"로 지정합니다. 
   이것은 실제 폴더가 아니라 솔루션 폴더일 뿐입니다.

2. **test** 폴더의 상황에 맞는 메뉴를 열고 **추가**를 선택합니다. **새 프로젝트**. **새 프로젝트** 대화 상자에서 **콘솔 응용 프로그램(.NET Core)**을 선택합니다. 이름을 "TestLibrary"로 지정하고 `Golden\test` 경로에 명시적으로 저장합니다. 

   > [!IMPORTANT]
   > 프로젝트는 클래스 라이브러리가 아니라 콘솔 응용 프로그램이어야 합니다.

3. **TestLibrary** 프로젝트에서 **참조** 노드의 상황에 맞는 메뉴를 열고 **참조 추가**를 선택합니다. 

4. **참조 관리자** 대화 상자에서 **프로젝트** 아래에 있는 **라이브러리**, **솔루션** 노드를 선택한 후 **확인**을 클릭합니다. 

5. **TestLibrary** 프로젝트에서 `project.json` 파일을 열고 `"Library": "1.0.0-*"`을 `"Library": {"target": "project"}`으로 바꿉니다. 

   이는 `Library` 프로젝트가 동일한 이름의 NuGet 패키지로 해석되는 것을 방지하기 위해서입니다. 대상을 "프로젝트"로 명시적으로 설정하면, 도구가 먼저 해당 이름의 프로젝트(패키지가 아닌)를 검색합니다. 

6. **참조** 노드의 상황에 맞는 메뉴를 열고 **NuGet 패키지 관리**를 선택합니다.

7. **패키지 소스**로 "nuget.org"를 선택하고 **찾아보기** 탭을 선택합니다. **시험판 포함** 확인란을 선택한 다음 **MSTest.TestFramework** 버전 1.0.1-preview 이상을 검색하고 **설치**를 클릭합니다. 

8. **dotnet-test-mstest** 버전 1.1.1-preview 이상을 검색하고 **설치**를 클릭합니다.

9. `project.json`을 편집하여 `"version"` 섹션 뒤에 `"testRunner": "mstest",`를 추가합니다.

10. **TestLibrary** 프로젝트에 `LibraryTests.cs` 클래스 파일을 추가하고, `using` 지시문 `Microsoft.VisualStudio.TestTools.UnitTesting;` 및 `using Library;`를 파일 상단에 추가하고, 다음 코드를 클래스에 추가합니다.
    ```csharp
    [TestClass]
    public class LibraryTests
    {
        [TestMethod]
        public void ThingGetsObjectValFromNumber()
        {
            Assert.AreEqual(42, new Thing().Get(42));
        }
    }
    ```
    * 선택적으로, **TestLibrary** 프로젝트에서 `Program.cs` 파일을 삭제하고 `project.json`에서 `"buildOptions": {"emitEntryPoint": true},`를 제거합니다.

   이제 솔루션을 빌드할 수 있습니다. 
   
11. **테스트** 메뉴에서 **Windows**, **테스트 탐색기**를 선택하고, 테스트 탐색기에서 **모두 실행**을 선택합니다.
   
   테스트를 전달해야 합니다.



<!--HONumber=Nov16_HO3-->


