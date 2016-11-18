---
title: "Dotnet 테스트를 사용하여 .NET Core에서 단위 테스트"
description: "Dotnet 테스트를 사용하여 .NET Core에서 단위 테스트"
keywords: .NET, .NET Core
author: ardalis
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: bdcdb812-6f13-4f20-9e90-0c0977937142
translationtype: Human Translation
ms.sourcegitcommit: 15c55a87beb64f265a164db918c7721c7690fadf
ms.openlocfilehash: 0c98084786408a285111ae724ed404ce32160aea

---

# <a name="unit-testing-in-net-core-using-dotnet-test"></a>Dotnet 테스트를 사용하여 .NET Core에서 단위 테스트

작성자: [Steve Smith](http://ardalis.com) 및 [Bill Wagner](https://github.com/BillWagner)

[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/docs/tree/master/samples/core/getting-started/unit-testing-using-dotnet-test)

## <a name="creating-the-projects"></a>프로젝트 만들기

[플랫폼 간 도구로 라이브러리 작성](../tutorials/libraries.md)에서는 소스와 테스트 모두의 다중 프로젝트 솔루션 구성에 대한 정보를 제공합니다. 이 문서는 그러한 규칙을 따릅니다. 최종 프로젝트 구조는 다음과 같습니다.

```
/unit-testing-using-dotnet-test
|__global.json
|__/src
   |__/PrimeService
      |__Source Files
      |__project.json
|__/test
   |__/PrimeService.Tests
      |__Test Files
      |__project.json
```

루트 디렉터리에서 `src` 및 `test` 디렉터리의 이름을 포함하는 `global.json`을 만들어야 합니다.

```json
{
    "projects": [
        "src",
        "test"
    ]
}
```

### <a name="creating-the-source-project"></a>소스 프로젝트 만들기

그런 다음 `src` 디렉터리에서 `PrimeService` 디렉터리를 만듭니다.
해당 디렉터리로 이동하고 `dotnet new -t lib`를 실행하여 소스 프로젝트를 만듭니다.


`Library.cs`의 이름을 `PrimeService.cs`로 바꿉니다. TDD(테스트 기반 개발)를 사용하기 위해 `PrimeService` 클래스의 실패 구현을 만듭니다.

```cs
using System;

namespace Prime.Services
{
    public class PrimeService
    {
        public bool IsPrime(int candidate) 
        {
            throw new NotImplementedException("Please create a test first");
        } 
    }
}

```

### <a name="creating-the-test-project"></a>테스트 프로젝트 만들기

이제 'test' 디렉터리로 이동하여 `PrimeServices.Tests` 디렉터리를 만듭니다.
`PrimeServices.Tests` 디렉터리로 이동하고 `dotnet new -t xunittest`를 사용하여 새 프로젝트를 만듭니다. `dotnet new -t xunittest`는 xunit을 테스트 라이브러리로 사용하는 테스트 프로젝트를 만듭니다. 

생성된 템플릿이 `project.json`의 루트에 Test Runner를 구성했습니다.

```json
{
  "version": "1.0.0-*",
  "testRunner": "xunit",
  // ...
}
```

템플릿은 또한 `netcoreapp1.0`을 사용하도록 프레임워크 노드를 설정하며, xUnit.net이 .NET Core RTM과 작동하도록 필수 가져오기를 포함합니다.

```json
  "frameworks": {
    "netcoreapp1.0": {
      "imports": [
        "dotnet54",
        "portable-net45+win8" 
      ]
    }
  }
```

테스트 프로제트는 다른 패키지에 단위 테스트를 만들고 실행하도록 요구합니다.
`dotnet new`는 xunit 및 xunit runner를 추가했습니다. PrimeService 패키지를 또 다른 종속성으로 프로젝트에 추가해야 합니다.

```json
"dependencies": {
  "Microsoft.NETCore.App": {
    "type":"platform",
    "version": "1.0.0"
  },
  "xunit":"2.1.0",
  "dotnet-test-xunit": "1.0.0-rc2-192208-24",
  "PrimeService": {
    "target": "project"
  }
}
```

`PrimeService` 프로젝트는 디렉터리 경로 정보를 포함하지 않습니다. `src` 및 `test`의 확장 구성과 일치하도록 프로젝트 구조를 만들었으므로, `global.json` 파일은 빌드 시스템이 프로젝트에 대한 올바른 위치를 찾을 것임을 나타냅니다. `"target": "project"` 요소를 추가하여 NuGet에 NuGet 피드가 아닌 프로젝트 디렉터리를 조회하도록 알립니다. 이 키가 없는 경우 내부 라이브러리와 같은 이름의 패키지를 다운로드할 수 있습니다.

GitHub의 [샘플 리포지토리](https://github.com/dotnet/docs/blob/master/samples/core/getting-started/unit-testing-using-dotnet-test/test/PrimeService.Tests/project.json)에서 전체 파일을 볼 수 있습니다.

이 초기 구조가 자리 잡으면 첫 번째 테스트를 작성할 수 있습니다.
첫 번째 단위 테스트를 확인한 후에는 모든 것이 구성되며 기능과 테스트를 추가할 때 원활하게 실행됩니다.

## <a name="creating-the-first-test"></a>첫 번째 테스트 만들기

TDD 접근 방식에서는 하나의 실패 테스트를 작성하고, 통과시키고, 이 프로세스를 반복해야 합니다. 따라서 하나의 실패 테스트를 작성하겠습니다. `PrimeService.Tests` 디렉터리에서 `program.cs`를 제거하고 다음과 같은 내용으로 새 C# 파일을 만듭니다.

```cs
namespace Prime.UnitTests.Services
{
    public class PrimeService_IsPrimeShould
    {
        private readonly PrimeService _primeService;
         public PrimeService_IsPrimeShould()
         {
             _primeService = new PrimeService();
         }

        [Fact]
        public void ReturnFalseGivenValueOf1()
        {
            var result = _primeService.IsPrime(1);

            Assert.False(result, $"1 should not be prime");
        }
    }
}
```

`[Fact]` 특성은 메서드를 단일 테스트로 나타냅니다. 

이 파일을 저장한 다음 `dotnet build`를 실행하여 테스트 프로젝트를 빌드합니다.
`PrimeService` 프로젝트를 아직 빌드하지 않은 경우, 이 프로젝트는 테스트 프로젝트의 종속성이기 때문에 빌드 시스템이 이를 탐지하고 빌드합니다.

이제 `dotnet test`를 실행하여 콘솔에서 테스트를 실행합니다.
xunit Test Runner는 콘솔에서 테스트를 실행하기 위한 프로그램 진입점을 가지고 있습니다. `dotnet test`는 Test Runner를 시작하고, 테스트가 포함된 어셈블리를 나타내는 testrunner에 명령줄 인수를 제공합니다.

테스트가 실패합니다. 구현은 아직 만들지 않았습니다.
이 하나의 테스트를 통과시킬 가장 간단한 코드를 작성합니다.

```cs
public bool IsPrime(int candidate) 
{
    if(candidate == 1) 
    { 
        return false;
    } 
    throw new NotImplementedException("Please create a test first");
} 
```

### <a name="adding-more-features"></a>더 많은 기능 추가

이제 하나의 테스트를 통과했으므로 더 작성할 수 있습니다.
소수에 대한 몇 가지 다른 간단한 사례가 있습니다(0, -1). 이들을 `[Fact]` 특성과 함께 새 테스트로 추가할 수도 있지만, 이렇게 하면 금방 지루해질 수 있습니다. 비슷한 테스트 모음을 작성하는 데 사용할 수 있는 다른 xunit 특성이 있습니다.  `Theory`는 같은 코드를 실행하는 테스트 모음을 나타내지만, 서로 다른 입력 인수를 가지고 있습니다.
`[InlineData]` 특성을 사용하여 그러한 입력의 값을 지정할 수 있습니다. 
 
 새 테스트를 만드는 대신 이 두 가지 특성을 사용하여 2보다 작은 일부 값(가장 낮은 소수)을 테스트하는 단일 이론을 만듭니다.

```cs
[Theory]
[InlineData(-1)]
[InlineData(0)]
[InlineData(1)]
public void ReturnFalseGivenValuesLessThan2(int value)
{
    var result = _primeService.IsPrime(value);

    Assert.False(result, $"{value} should not be prime");
}
```

`dotnet test`를 실행하면 이러한 테스트 중 두 개가 실패하는 것을 알 수 있습니다.
서비스를 변경하여 이들을 통과시킬 수 있습니다. 메서드의 시작 부분에서 `if` 절을 변경해야 합니다.

```cs
if(candidate < 2)
```

이제 이러한 테스트가 모두 통과됩니다.

기본 라이브러리에서 더 많은 테스트, 더 많은 이론, 더 많은 코드를 추가하여 계속 반복합니다. [테스트의 완료된 버전](https://github.com/dotnet/docs/blob/master/samples/core/getting-started/unit-testing-using-dotnet-test/test/PrimeService.Tests/PrimeServie_IsPrimeShould.cs) 및 [라이브러리의 완전한 구현](https://github.com/dotnet/docs/blob/master/samples/core/getting-started/unit-testing-using-dotnet-test/src/PrimeService/PrimeService.cs)으로 신속하게 종료하게 됩니다.

작은 라이브러리 및 이 라이브러리에 대한 단위 테스트 집합을 작성했습니다.
새 패키지와 테스트를 원활하게 추가할 수 있도록 이 솔루션을 구성했으므로 이제 당면한 문제에 집중할 수 있습니다. 도구는 자동으로 실행됩니다.
   
   > [!TIP]
   > Windows 플랫폼에서 MSTest를 사용할 수 있습니다. 자세한 내용은 [Using MSTest on Windows document(Windows 문서에서 MSTest 사용)](./using-mstest-on-windows.md)를 참조하세요.



<!--HONumber=Nov16_HO3-->


