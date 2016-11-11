---
title: ".NET 정보"
description: ".NET 제품"
keywords: .NET, .NET Core
author: richlander
manager: wpickett
ms.date: 06/23/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 2e38e9d9-8284-46ee-a15f-199adc4f26f4
translationtype: Human Translation
ms.sourcegitcommit: 15c55a87beb64f265a164db918c7721c7690fadf
ms.openlocfilehash: 52c9993e0d82429e429ba2f2c2c7c851cf901942

---

# <a name="about-net"></a>.NET 정보

> 간단한 .NET Core 응용 프로그램을 만드는 방법을 알아보려면 [".NET Core 시작" 자습서](../core/getting-started.md)를 확인해 보세요. 첫 번째 앱을 만들고 실행하는 데 몇 분밖에 걸리지 않습니다.

.NET은 범용 개발 플랫폼입니다. 범용 솔루션이 사용되는 모든 종류의 앱 유형이나 작업에 사용할 수 있습니다. 자동 메모리 관리, 최신 프로그래밍 언어 등 고품질 응용 프로그램을 효율적으로 쉽게 빌드할 수 있게 해주며 대부분의 개발자에게 유용한 몇 가지 주요 기능이 있습니다. .NET은 기본 메모리 및 API에 대한 하위 수준 액세스를 제공하는 동시에 다양한 편의 기능이 있는 상위 수준의 프로그래밍 환경을 지원합니다.

C#, F# 및 Visual Basic은 .NET 플랫폼을 대상으로 하고 사용하는 인기 있는 언어입니다. .NET 언어는 비동기 프로그래밍 모델, Language-Integrated Query, 제네릭 형식, 형식 시스템 리플렉션 등의 주요 기능으로 알려져 있습니다. 이 언어는 개체 지향 및 기능 프로그래밍 패러다임에 대한 유용한 옵션도 제공합니다.

이러한 언어의 철학과 구문은 다양하지만 공유 형식 시스템에서 제공하는 대칭성도 있습니다. 이 형식 시스템은 기본 런타임 환경에서 제공됩니다. .NET은 다양한 언어(예: 동적 및 정적으로 형식화된 언어)의 요구 사항을 지원하고 언어 간에 상호 운용될 수 있도록 하는 "공용 언어 런타임"의 개념을 중심으로 설계되었습니다. 예를 들어 의미 체계 또는 기능의 손실 없이 언어 간에 `People` 개체 컬렉션을 전달할 수 있습니다.

플랫폼의 기본 사항을 지정하는 개방형 [.NET 표준](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/dotnet-standards.md)에 따라 여러 [.NET 구현 및 제품](products.md)을 사용할 수 있습니다. 응용 프로그램 종류(예: 데스크톱, 모바일, 게임, 클라우드)에 따라 개별적으로 최적화되며 많은 칩(예: x86/x64, ARM)과 운영 체제(예: Windows, Linux, iOS, Android, macOS)를 지원합니다. 오픈 소스도 환경의 OSI 승인 라이선스로 여러 .NET 구현과 많은 라이브러리를 사용할 수 있는 .NET 에코시스템의 중요한 부분입니다.

- [.NET](../standard/index.md)에 대한 자세한 정보
- [C#](../csharp/index.md)에 대한 자세한 정보
- [F#](../fsharp/index.md)에 대한 자세한 정보
- [.NET API 라이브러리](../../api/index.md) 찾아보기
- [공용 언어 런타임 소개](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/intro-to-clr.md)

## <a name="fundamentals"></a>기본 사항

**다중 언어** - .NET은 여러 언어에서 자체적으로 실행하거나 동일한 .NET 구성 요소를 공유 통화로 사용하는 다른 언어와 상호 운용하기 위해 사용할 수 있는 잘 정의된 형식 시스템, 파일 형식, 런타임, 프레임워크 및 도구를 제공합니다.

**관리되는 메모리** - .NET은 가비지 수집기를 통해 메모리를 자동으로 관리합니다. 항상 라이브 개체를 참조하여 버퍼 오버런, 액세스 위반 등의 까다로운 문제를 방지하도록 합니다. 여기에는 배열 범위 검사가 포함됩니다.

**형식 안전성** - 기능 및 메모리 표현의 기본 .NET 모델은 "형식"입니다. 형식은 모양과 필요에 따라 동작을 정의합니다. 런타임에서는 해당 정의 및 지정된 멤버의 표시 여부에 따라 형식에 호출 코드가 적용되도록 하여 일관성 있고 신뢰할 수 있는 안전한 결과를 제공합니다.

## <a name="features"></a>기능

**사용자 정의 값 형식** - 값 형식은 클래스의 경우처럼 "참조로 전달"하지 않고 "값으로 전달"하는 의미 체계를 제공하므로 유용한 형식 범주입니다. 값 형식은 숫자 데이터에 특히 유용합니다. .NET에서는 정수 등의 기본 형식과 사용자 정의 형식 둘 다에 값 형식을 사용할 수 있습니다.

**제네릭 형식** - 제네릭 형식은 인스턴스화별로 지정할 수 있는 하나 이상의 형식 매개 변수가 있는 형식입니다. 이 형식은 콘텐츠를 개체 형식으로 표시하거나 여러 형식 정의가 필요한 여러 형식에 유용합니다. 예를 들어 컬렉션 형식의 지정된 인스턴스화를 사용자, GPS 위치 또는 문자열과 관련된 것으로 설정할 수 있습니다.

**리플렉션** - .NET에서는 이진 파일 내의 형식을 설명하는 메타데이터 형식을 정의합니다. 리플렉션 하위 시스템은 이 데이터를 사용하며 런타임에 형식을 읽고 인스턴스화하기 위한 API를 표시합니다. 이 기능은 프로그램의 정확한 구현을 미리 알기 어려운 동적 시나리오에 매우 유용합니다.

**유연한 코드 생성** - .NET에서는 .NET 이진 파일을 기계어 코드로 변환하는 특정 방법을 규정하지 않습니다. 해석, JIT(Just-In-Time) 컴파일, JIT 대체를 사용한 AOT(Ahead-Of-Time) 컴파일, JIT 대체를 사용하지 않는 AOT 컴파일 등 많은 방법이 성공적으로 사용되었습니다. 이러한 전략은 각각 중요하며 함께 사용할 수 있는 경우도 있습니다.

**플랫폼 간** - .NET은 처음부터 플랫폼 간으로 설계되었습니다. 이진 형식과 명령 집합에 운영 체제, CPU 및 포인터 크기 제약이 없습니다. 32비트 Windows 컴퓨터에서 실행되도록 2000에서 빌드된 지정된 .NET 이진 파일을 수정하지 않고 ARM64 iOS 장치의 2016에서 실행할 수 있습니다.

## <a name="open-source"></a>소스 열기

.NET의 [.NET Core](https://github.com/dotnet/core) 및 [Mono](https://github.com/mono/mono) 구현은 MIT 라이선스를 사용하는 오픈 소스입니다. 설명서에서는 [Creative Commons CC-BY](https://creativecommons.org/licenses/by/4.0/) 라이선스를 사용합니다. .NET Core 및 Mono는 Microsoft의 후원과 많은 커뮤니티 참가자의 도움을 받습니다. 

이러한 범용 런타임을 학술 연구, 교육/학습 또는 상용 제품의 기초로 사용할 수 있습니다. 개방적인 특성 때문에 버그가 있거나 새로운 기능을 원할 경우 누구든지 업스트림 제품 코드에 다시 참가할 수 있습니다.

## <a name="projects"></a>프로젝트

- [CoreCLR](https://github.com/dotnet/coreclr) - .NET Core에서 사용되는 .NET 런타임입니다.
- [Mono](https://github.com/mono/mono) - Xamarin 등에서 사용되는 .NET 런타임입니다.
- [CoreFX](https://github.com/dotnet/coreclr) - .NET Core에서 사용되며 소스 공유를 통해 Mono에서 부분적으로 사용되는 .NET 클래스 라이브러리입니다.
- [Roslyn](https://github.com/dotnet/roslyn) - 대부분의 .NET 플랫폼과 도구에서 사용되는 C# 및 Visual Basic 컴파일러입니다. 소스 코드를 읽고, 쓰고, 분석하기 위한 API를 표시합니다.
- [F#](https://github.com/microsoft/visualfsharp) - F# 컴파일러입니다.
- [Xamarin SDK](http://open.xamarin.com) - C# 및 F#으로 Android, iOS 및 macOS를 작성하는 데 필요한 도구 및 라이브러리입니다.

## <a name="standardized"></a>표준화됨

.NET은 해당 기능을 간략하게 설명하며 새 구현을 만드는 데 사용할 수 있는 개방형 [ECMA 표준](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/dotnet-standards.md)을 통해 지정됩니다. 다른 .NET 구현도 있으며, Mono와 Unity가 Microsoft 구현보다 가장 많이 사용됩니다.




<!--HONumber=Nov16_HO1-->


