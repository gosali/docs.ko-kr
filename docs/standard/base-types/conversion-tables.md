---
title: "형식 변환표"
description: "형식 변환표"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 07/22/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: d602f260-e7cf-49c8-a37f-731f40e4a538
translationtype: Human Translation
ms.sourcegitcommit: fb00da6505c9edb6a49d2003ae9bcb8e74c11d6c
ms.openlocfilehash: 01e15d9dc3881c7e2e4ea17db666991d72aa1b84

---

# <a name="type-conversion-tables"></a>형식 변환표

확대 변환은 한 형식의 값을 크기가 같거나 더 큰 다른 형식으로 변환할 때 발생합니다. 축소 변환은 한 형식의 값을 크기가 더 작은 다른 형식의 값으로 변환할 때 발생합니다. 이 항목의 표에서는 두 가지 유형의 변환에서 모두 나타나는 동작을 설명합니다.

## <a name="widening-conversions"></a>확대 변환

형식 | 데이터 손실 없이 다음 형식으로 변환할 수 있음
---- | -------------------------------------
[Byte](xref:System.Byte) | [UInt16](xref:System.UInt16), [Int16](xref:System.Int16), [UInt32](xref:System.UInt32), [Int32](xref:System.Int32), [UInt64](xref:System.UInt64), [Int64](xref:System.Int64), [Single](xref:System.Single), [Double](xref:System.Double), [Decimal](xref:System.Decimal)
[SByte](xref:System.SByte) | [Int16](xref:System.Int16), [Int32](xref:System.Int32), [Int64](xref:System.Int64), [Single](xref:System.Single), [Double](xref:System.Double), [Decimal](xref:System.Decimal)
[Int16](xref:System.Int16) | [Int32](xref:System.Int32), [Int64](xref:System.Int64), [Single](xref:System.Single), [Double](xref:System.Double), [Decimal](xref:System.Decimal)
[UInt16](xref:System.UInt16) | [UInt32](xref:System.UInt32), [Int32](xref:System.Int32), [UInt64](xref:System.UInt64), [Int64](xref:System.Int64), [Single](xref:System.Single), [Double](xref:System.Double), [Decimal](xref:System.Decimal)
[Char](xref:System.Char) | [UInt16](xref:System.UInt16), [UInt32](xref:System.UInt32), [Int32](xref:System.Int32), [UInt64](xref:System.UInt64), [Int64](xref:System.Int64), [Single](xref:System.Single), [Double](xref:System.Double), [Decimal](xref:System.Decimal)
[Int32](xref:System.Int32) | [Int64](xref:System.Int64), [Double](xref:System.Double), [Decimal](xref:System.Decimal)
[UInt32](xref:System.UInt32) | [Int64](xref:System.Int64), [UInt64](xref:System.UInt64), [Double](xref:System.Double), [Decimal](xref:System.Decimal)
[Int64](xref:System.Int64) | [Decimal](xref:System.Decimal)
[UInt64](xref:System.UInt64) | [Decimal](xref:System.Decimal)
[Single](xref:System.Single) | [Double](xref:System.Double)

[Single](xref:System.Single) 또는 [Double](xref:System.Double)로의 일부 확대 변환에서는 정밀도 손실이 발생할 수 있습니다. 다음 표에서는 때때로 정보 손실이 발생할 수 있는 확대 변환에 대해 설명합니다.

형식 | 다음 형식으로 변환할 수 있음
---- | -------------------
[Int32](xref:System.Int32) | [Single](xref:System.Single)
[UInt32](xref:System.UInt32) | [Single](xref:System.Single)
[Int64](xref:System.Int64) | [Single](xref:System.Single), [Double](xref:System.Double)
[UInt64](xref:System.UInt64) | [Single](xref:System.Single), [Double](xref:System.Double)
[Decimal](xref:System.Decimal) | [Single](xref:System.Single), [Double](xref:System.Double)

## <a name="narrowing-conversions"></a>축소 변환

[Single](xref:System.Single) 또는 [Double](xref:System.Double)로의 축소 변환에서는 정보 손실이 발생할 수 있습니다. 대상 형식이 소스 크기를 제대로 표시할 수 없는 경우 결과 형식이 상수 `PositiveInfinity` 또는 `NegativeInfinity`로 설정됩니다. `PositiveInfinity`는 양수를 0으로 나눈 결과이며 [Single](xref:System.Single) 또는 [Double](xref:System.Double) 값이 `MaxValue` 필드 값을 초과하는 경우에도 반환됩니다. `NegativeInfinity`는 음수를 0으로 나눈 결과이며 [Single](xref:System.Single) 또는 [Double](xref:System.Double) 값이 `MinValue` 필드 값보다 작은 경우에도 반환됩니다. [Double](xref:System.Double)에서 [Single](xref:System.Single)로 변환하면 `PositiveInfinity` 또는 `NegativeInfinity`가 발생할 수 있습니다.

축소 변환 시 다른 데이터 형식에 대한 정보 손실도 발생할 수 있습니다. 그러나 변환 중인 형식의 값이 대상 형식의 `MaxValue` 및 `MinValue` 필드에 지정된 범위를 벗어나면 [OverflowException](xref:System.OverflowException)이 throw되며, 런타임에서 변환을 검사하여 대상 형식의 값이 해당 `MaxValue` 또는 `MinValue`를 초과하지 않는지 확인합니다. [System.Convert](xref:System.Convert) 클래스로 수행하는 변환은 항상 이런 방식으로 검사됩니다.

다음 표에서는 [OverflowException](xref:System.OverflowException)을 throw하는 [System.Convert](xref:System.Convert)를 사용한 변환 또는 변환 중인 형식의 값이 정의된 결과 형식 범위를 벗어난 경우 검사된 모든 변환을 보여 줍니다.

형식 | 다음 형식으로 변환할 수 있음
---- | -------------------
[Byte](xref:System.Byte) | [SByte](xref:System.SByte)
[SByte](xref:System.SByte) | [Byte](xref:System.Byte), [UInt16](xref:System.UInt16), [UInt32](xref:System.UInt32), [UInt64](xref:System.UInt64)
[Int16](xref:System.Int16) | [Byte](xref:System.Byte), [SByte](xref:System.SByte), [UInt16](xref:System.UInt16)
[UInt16](xref:System.UInt16) | [Byte](xref:System.Byte), [SByte](xref:System.SByte), [Int16](xref:System.Int16)
[Int32](xref:System.Int32) | [Byte](xref:System.Byte), [SByte](xref:System.SByte), [Int16](xref:System.Int16), [UInt16](xref:System.UInt16), [UInt32](xref:System.UInt32)
[UInt32](xref:System.UInt32) | [Byte](xref:System.Byte), [SByte](xref:System.SByte), [Int16](xref:System.Int16), [UInt16](xref:System.UInt16), [Int32](xref:System.Int32)
[Int64](xref:System.Int64) | [Byte](xref:System.Byte), [SByte](xref:System.SByte), [Int16](xref:System.Int16), [UInt16](xref:System.UInt16), [Int32](xref:System.Int32), [UInt32](xref:System.UInt32), [UInt64](xref:System.UInt64)
[UInt64](xref:System.UInt64) | [Byte](xref:System.Byte), [SByte](xref:System.SByte), [Int16](xref:System.Int16), [UInt16](xref:System.UInt16), [Int32](xref:System.Int32), [UInt32](xref:System.UInt32), [Int64](xref:System.Int64)
[Decimal](xref:System.Decimal) | [Byte](xref:System.Byte), [SByte](xref:System.SByte), [Int16](xref:System.Int16), [UInt16](xref:System.UInt16), [Int32](xref:System.Int32), [UInt32](xref:System.UInt32), [Int64](xref:System.Int64), [UInt64](xref:System.UInt64)
[Single](xref:System.Single) | [Byte](xref:System.Byte), [SByte](xref:System.SByte), [Int16](xref:System.Int16), [UInt16](xref:System.UInt16), [Int32](xref:System.Int32), [UInt32](xref:System.UInt32), [Int64](xref:System.Int64), [UInt64](xref:System.UInt64)
[Double](xref:System.Double) | [Byte](xref:System.Byte), [SByte](xref:System.SByte), [Int16](xref:System.Int16), [UInt16](xref:System.UInt16), [Int32](xref:System.Int32), [UInt32](xref:System.UInt32), [Int64](xref:System.Int64), [UInt64](xref:System.UInt64)

## <a name="see-also"></a>참고 항목

[System.Convert](xref:System.Convert)

[형식 변환](type-conversion.md)




<!--HONumber=Nov16_HO1-->


