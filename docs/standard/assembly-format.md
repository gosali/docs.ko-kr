---
title: ".NET 어셈블리 파일 형식"
description: ".NET 어셈블리 파일 형식"
keywords: .NET, .NET Core
author: richlander
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 6520323e-ff28-4c8a-ba80-e64a413199e6
translationtype: Human Translation
ms.sourcegitcommit: 30175813af95911c8ab4f2f0e39c40bed49a23b3
ms.openlocfilehash: e6212a63e74f2d1525e87480b092861be9f92379

---

# <a name="net-assembly-file-format"></a>.NET 어셈블리 파일 형식

.NET 플랫폼에서는 .NET 프로그램을 완벽하게 설명하고 포함하는 데 사용되는 이진 파일 형식("어셈블리")를 정의합니다. 어셈블리는 프로그램 자체와 모든 종속 라이브러리에 사용됩니다. 적절한 .NET 런타임 외에 다른 필수 아티팩트 없이 추가 어셈블리 중 하나로 .NET 프로그램을 실행할 수 있습니다. 운영 체제 API를 비롯한 기본 종속성은 별도의 문제이며, .NET 어셈블리 형식으로 설명되는 경우도 있지만(예: WinRT) 이 형식에 포함되지 않습니다.

> 각 CLI 구성 요소는 해당 구성 요소와 관련된 선언, 구현 및 참조에 대한 메타데이터를 전달합니다. 따라서 구성 요소 관련 메타데이터는 구성 요소별 메타데이터라고 하며 결과 구성 요소는 자기 설명적입니다(ECMA 335 I.9.1, Components and assemblies).

형식은 완벽하게 지정되며 ECMA 335로 표준화됩니다. 모든 .NET 컴파일러와 런타임에서 이 형식을 사용합니다. 문서화되고 자주 업데이트되지 않는 이진 형식이 있을 경우 상호 운용성에 특히 도움이 됩니다(요구 사항인 경우도 있음). 형식은 2005(.NET 2.0)에서 대체 방법을 사용하여 제네릭 및 프로세서 아키텍처에 맞게 마지막으로 업데이트되었습니다.

형식은 CPU 및 OS와 관련이 없습니다. 많은 칩과 CPU를 대상으로 하는 .NET 런타임의 일부로 사용되었습니다. 형식 자체에 Windows 유산이 있을 경우 모든 운영 체제에서 구현할 수 있습니다. 논란의 여지는 있지만 OS 상호 운용성에 가장 중요한 선택 사항은 대부분의 값을 little-endian 형식으로 저장하는 것입니다. 컴퓨터 포인터 크기(예: 32비트, 64비트)에 대한 특정 선호도는 없습니다.

.NET 어셈블리 형식은 지정된 프로그램 또는 라이브러리의 구조에 대해 매우 설명적이기도 합니다. 어셈블리의 내부 구성 요소, 특히 정의된 어셈블리 참조 및 형식과 해당 내부 구조를 설명합니다. 도구 또는 API는 표시하거나 프로그래밍 방식으로 결정을 내리기 위해 이 정보를 읽고 처리할 수 있습니다.

## <a name="format"></a>서식

.NET 이진 형식은 Windows [PE 파일](http://en.wikipedia.org/wiki/Portable_Executable) 형식을 기반으로 합니다. 실제로 .NET 클래스 라이브러리는 규칙에 부합되는 Windows PE이며, 처음에는 Windows DLL(동적 연결 라이브러리) 또는 EXE(응용 프로그램 실행 파일)처럼 보입니다. 이는 Windows에서 매우 유용한 특성으로, 네이티브 실행 이진 파일로 가장하고 동일한 처리 중 일부(예: OS 로드, PE 도구)를 얻을 수 있습니다.

![어셈블리 헤더](./media/assembly-format/assembly-headers.png)

ECMA 335 II.25.1 런타임 파일 형식 구조의 어셈블리 헤더입니다.

## <a name="processing-the-assemblies"></a>어셈블리 처리

어셈블리를 처리할 도구 또는 API를 작성할 수 있습니다. 어셈블리 정보를 사용하면 런타임에 프로그래밍 방식으로 결정을 내리고, 어셈블리를 다시 작성하고, 편집기에서 API IntelliSense를 제공하고, 설명서를 생성할 수 있습니다. [System.Reflection](https://msdn.microsoft.com/library/system.reflection.aspx) 및 [Mono.Cecil](http://www.mono-project.com/docs/tools+libraries/libraries/Mono.Cecil/)은 이러한 용도에 자주 사용되는 도구의 좋은 예입니다.



<!--HONumber=Nov16_HO3-->


