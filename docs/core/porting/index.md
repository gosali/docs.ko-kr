---
title: ".NET Framework에서 .NET Core로 이식"
description: ".NET Framework에서 .NET Core로 이식"
keywords: .NET, .NET Core
author: cartermp
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 00d00d38-99af-44f4-a75f-defcd9729dc5
translationtype: Human Translation
ms.sourcegitcommit: ba6907f0054c3f4cdbaa687277ad70554670f0bb
ms.openlocfilehash: 677fe36a17b56aae0198140d82a2bfdd0d6e8224

---

# <a name="porting-to-net-core-from-net-framework"></a>.NET Framework에서 .NET Core로 이식

.NET Framework에서 코드를 실행한 적이 있으면 .NET Core 1.0에서 코드를 실행하는 데 관심이 있을 수 있습니다.  이 문서에서는 이식 프로세스 및 .NET Core로 이식할 때 유용하게 사용할 수 있는 도구 목록에 대해 간략하게 설명합니다.

## <a name="overview-of-the-porting-process"></a>이식 프로세스의 개요

권장되는 이식 프로세스는 다음 단계를 따르는 것입니다.  이러한 프로세스의 각 부분은 향후 문서에서 더 자세히 설명합니다.

1. 타사 종속성을 식별하고 확인합니다.

   여기에서는 타사 종속성의 개념, 종속 방법, .NET Core에서 실행되는지 확인하는 방법 및 실행되지 않을 경우 수행할 수 있는 단계를 이해합니다.
   
2. 대상 .NET Framework 4.6.2로 이식하려는 모든 프로젝트의 대상을 다시 지정합니다.

   그러면 .NET Core에서 특정 API를 지원할 수 없는 경우 .NET Framework 관련 대상에 API 대안을 사용할 수 있습니다.
   
3. [API Portability Analyzer 도구](https://github.com/Microsoft/dotnet-apiport/)를 사용하여 어셈블리를 분석하고 결과에 따라 이식 계획을 수립합니다.

   API Portability Analyzer 도구는 컴파일된 어셈블리를 분석하고 상위 수준 이식성 요약 및 .NET Core에서 사용할 수 없는 사용 중인 각 API에 대한 분석 결과를 보여 주는 보고서를 생성합니다.  코드베이스에 대한 분석과 함께 이 보고서를 사용하여 코드를 이식할 방법에 대한 계획을 개발할 수 있습니다.
   
4. 테스트 코드를 이식합니다.

   .NET Core로 이식하면 코드베이스가 너무 많이 변경되기 때문에 코드를 이식할 때 테스트를 실행할 수 있도록 테스트를 이식하는 것이 가장 좋습니다.  현재 MSTest, xUnit 및 NUnit 모두 .NET Core 1.0을 지원합니다.
   
6. 이식에 대한 계획을 실행합니다.

## <a name="tools-to-help"></a>유용한 도구

다음은 유용하게 사용할 수 있는 간단한 도구 목록입니다.

* NuGet - [Nuget 클라이언트](https://dist.nuget.org/index.html) 또는 [NuGet 패키지 탐색기](https://github.com/NuGetPackageExplorer/NuGetPackageExplorer)로, Microsoft의 .NET 플랫폼용 패키지 관리자입니다.
* Api Portability Analyzer - [명령줄 도구](https://github.com/Microsoft/dotnet-apiport/releases) 또는 [Visual Studio 확장](https://visualstudiogallery.msdn.microsoft.com/1177943e-cfb7-4822-a8a6-e56c7905292b)으로, 문제에 대한 어셈블리 단위 분석 결과를 사용하여 .NET Framework와 .NET Core 간에 코드를 이식할 수 있는 정도에 대한 보고서를 생성할 수 있는 도구 체인입니다.  자세한 내용은 [프로세스에 도움이 되는 도구](https://github.com/Microsoft/dotnet-apiport/blob/master/docs/HowTo/)를 참조하세요.
* 역방향 패키지 검색 - 형식을 검색하고 해당 형식을 포함하는 패키지를 찾을 수 있는 [유용한 웹 서비스](https://packagesearch.azurewebsites.net)입니다.

## <a name="next-steps"></a>다음 단계

[타사 종속성 분석](third-party-deps.md)
   



<!--HONumber=Nov16_HO1-->


