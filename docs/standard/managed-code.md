---
title: "&quot;관리 코드&quot;란?"
description: "&quot;관리 코드&quot;란?"
keywords: .NET, .NET Core
author: blackdwarf
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 20bb7ea8-192e-4a96-8ef3-e10e1950fd3d
translationtype: Human Translation
ms.sourcegitcommit: 0013cf5604bfd1e8a16e5ca15577ac81fce49612
ms.openlocfilehash: 905c3706717a03f468e3dd5717c049e83f211e67

---

# <a name="what-is-managed-code"></a>"관리 코드"란?

.NET Framework로 작업하는 경우 "관리 코드"란 용어를 자주 발견하게 됩니다. 이 문서에서는 이 용어의 의미 및 관련된 추가 정보를 설명합니다.

간단히 말해서, 관리 코드란 런타임에서 실행이 관리되는 코드입니다. 이 경우 해당 런타임을 구현([Mono](http://www.mono-project.com/), .NET Framework 또는 .NET Core)에 관계없이 **공용 언어 런타임** 또는 CLR이라고 합니다. CLR은 관리 코드를 가져와서 기계어 코드로 컴파일한 다음 실행합니다. 이 외에도 런타임에서는 자동 메모리 관리, 보안 경계, 형식 안전성 등 몇 가지 중요한 서비스를 제공합니다.

"비관리 코드"라고도 하는 C/C++ 프로그램 실행 방법과 이를 대조해 보세요. 관리되지 않는 환경에서는 프로그래머가 거의 모든 작업을 수행합니다. 실제 프로그램은 기본적으로 OS(운영 체제)에서 메모리에 로드하고 시작하는 이진 파일입니다. 메모리 관리에서 보안 고려 사항에 이르기까지 다른 모든 작업은 프로그래머의 몫입니다.

관리 코드는 C#, Visual Basic, F# 등 .NET 플랫폼에서 실행할 수 있는 고급 언어 중 하나로 작성됩니다. 이러한 언어로 작성된 코드를 해당 컴파일러로 컴파일할 때는 기계어 코드가 생성되지 않습니다. **중간 언어** 코드가 생성되며, 런타임에서 이 코드를 컴파일하고 실행합니다. 단, C++는 이 규칙의 유일한 예외이며 Windows에서 실행되는 관리되지 않는 네이티브 이진 파일을 생성할 수도 있습니다.

## <a name="intermediate-language-execution"></a>중간 언어 및 실행

"중간 언어"(또는 줄여서 IL)란? 고급 .NET 언어로 작성된 코드를 컴파일하여 생성된 결과입니다. 이러한 언어 중 하나로 작성된 코드를 컴파일하면 IL로 작성된 이진 파일을 얻게 됩니다. IL은 런타임에서 실행되는 모든 특정 언어에 독립적입니다. 필요한 경우 읽을 수 있는 별도의 사양도 있습니다.

고급 코드에서 IL을 생성한 후에는 대부분 실행하려고 할 것입니다. 이때 CLR이 사용되며 IL 상태의 코드를 **Just-In-Time** 컴파일 또는 **JIT 처리**하여 실제로 CPU에서 실행할 수 있는 기계어 코드로 변환하는 프로세스를 시작합니다. 이러한 방식에서는 CLR이 코드에서 수행하는 작업을 정확히 알고 효과적으로 _관리_할 수 있습니다.

중간 언어를 CIL(공용 중간 언어) 또는 MSIL(Microsoft 중간 언어)이라고도 합니다.

## <a name="unmanaged-code-interoperability"></a>비관리 코드와의 상호 운용성

물론, CLR에서 관리되는 환경과 관리되지 않는 환경 사이의 경계를 건널 수 있으며 [기본 클래스 라이브러리](framework-libraries.md)에도 이 작업을 수행하는 많은 코드가 있습니다. 이를 **상호 운용성** 또는 줄여서 **interop**라고 합니다. 예를 들어 이러한 프로비전을 통해 관리되지 않는 라이브러리를 래핑하고 호출할 수 있습니다. 그러나 이렇게 할 경우 코드가 런타임의 경계를 건널 때 실제 실행 관리가 다시 비관리 코드로 넘어가므로 동일한 제한이 적용됩니다.

이와 마찬가지로, C#은 CLR에서 실행이 관리되지 않는 코드 조각을 지정하는 **안전하지 않은 컨텍스트**를 활용하여 코드에서 직접 포인터 등의 관리되지 않는 구문을 사용할 수 있도록 하는 언어 중 하나입니다.

## <a name="more-resources"></a>추가 리소스

*   [.NET Framework 개념 개요](https://msdn.microsoft.com/library/zw4w595w.aspx)
*   [안전하지 않은 코드 및 포인터](https://msdn.microsoft.com/library/t2yzs44b.aspx)
*   [상호 운용성(C# 프로그래밍 가이드)](https://msdn.microsoft.com/library/ms173184.aspx)



<!--HONumber=Nov16_HO3-->


