---
title: ".NET에서 문자열 구문 분석"
description: ".NET에서 문자열 구문 분석"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 07/22/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 8103c0a6-61d3-40dd-a3e9-2a32ba6a4c05
translationtype: Human Translation
ms.sourcegitcommit: fb00da6505c9edb6a49d2003ae9bcb8e74c11d6c
ms.openlocfilehash: 066c721e66192658ae841721c90c194686a0730d

---

# <a name="parsing-strings-in-net"></a>.NET에서 문자열 구문 분석

구문 분석 작업은 .NET 기본 형식을 나타내는 문자열을 해당 기본 형식으로 변환합니다. 구문 분석 작업을 사용하여 문자열을 부동 소수점 숫자나 날짜 및 시간 값으로 변환하는 경우를 예로 들 수 있습니다. 구문 분석 작업을 수행하는 데 가장 일반적으로 사용되는 메서드는 `Parse` 메서드입니다. 구문 분석은 서식 지정(기본 형식을 문자열 표현으로 변환하는 작업 포함)의 반대 작업으로 많은 동일한 규칙이 적용됩니다. 서식 지정이 [IFormatProvider](xref:System.IFormatProvider) 인터페이스를 구현하는 개체를 사용하여 문화권을 구분하는 서식 지정 정보를 제공하는 것과 마찬가지로 구문 분석은 [IFormatProvider](xref:System.IFormatProvider) 인터페이스를 구현하는 개체를 사용하여 문자열 표현을 해석하는 방법을 결정합니다. 자세한 내용은 [.NET에서 서식 지정 형식](formatting-types.md)을 참조하세요.

## <a name="in-this-section"></a>단원 내용

[.NET에서 숫자 문자열 구문 분석](parsing-numeric.md) - 문자열을 .NET 숫자 형식으로 변환하는 방법에 대해 설명합니다.

[.NET에서 날짜 및 시간 문자열 구문 분석](parsing-datetime.md) - 문자열을 .NET `DateTime` 형식으로 변환하는 방법에 대해 설명합니다.

[.NET에서 기타 문자열 구문 분석](parsing-other.md) - 문자열을 [Char](xref:System.Char), [부울](xref:System.Boolean), 및 [Enum](xref:System.Enum) 형식으로 변환하는 방법에 대해 설명합니다.

[.NET에서 서식 지정 형식](formatting-types.md) - 서식 지정자 및 서식 공급자와 같은 기본 서식 지정 개념에 대해 설명합니다.

[.NET에서 형식 변환](type-conversion.md) - 형식을 변환하는 방법에 대해 설명합니다.

[.NET에서 기본 형식 사용](index.md) - .NET 기본 형식에서 수행할 수 있는 일반적인 작업에 대해 설명합니다.




<!--HONumber=Nov16_HO3-->


