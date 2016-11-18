---
title: ".NET Core의 숫자"
description: ".NET Core의 숫자"
keywords: .NET, .NET Core
author: rpetrusha
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 6b8696be-55f5-4b66-98f3-69ff827c2c49
translationtype: Human Translation
ms.sourcegitcommit: d5c7a18af16b4f3416e84b6cf86f0f78f28948da
ms.openlocfilehash: 2930bf6879df3cd16cbd0221ae6dfcba9b41de2e

---

# <a name="numerics-in-net-core"></a>.NET Core의 숫자

.NET Core는 표준 숫자 정수 계열 및 부동 소수점 기본 형식과 [System.Numerics](https://docs.microsoft.com/dotnet/core/api/System.Numerics) 네임스페이스의 [System.Numerics.BigInteger](https://docs.microsoft.com/dotnet/core/api/System.Numerics.BigInteger), 이론적 상한 또는 하한이 없는 정수 형식, [System.Numerics.Complex](https://docs.microsoft.com/dotnet/core/api/System.Numerics.Complex), 복소수를 나타내는 형식, [SIMD](https://en.wikipedia.org/wiki/SIMD)(Single Instruction Multiple Data) 사용 벡터 형식 집합을 지원합니다. 

## <a name="integral-types"></a>정수 계열 형식 표

.NET Core에서는 길이 범위가 1~8바이트인 부호 있는 정수와 부호 없는 정수를 둘 다 지원합니다. 다음 표에서는 정수 계열 형식 및 해당 크기 목록을 제공하고 부호가 있는지 여부를 표시하고 해당 범위를 문서화합니다. 모든 정수는 값 형식입니다. 

형식 | 부호 있음/부호 없음 | 크기(바이트) | 최소값 | 최대값
---- | --------------- | ------------ | ------------- | -------------
[System.Byte](https://docs.microsoft.com/dotnet/core/api/System.Byte) | 부호 없음 | 1 | 0 | 255
[System.Int16](https://docs.microsoft.com/dotnet/core/api/System.Int16) | 서명 | 2 | -32,768 | 32,767
[System.Int32](https://docs.microsoft.com/dotnet/core/api/System.Int32) | 서명 | 4 | -2,147,483,648 | 2,147,483,647
[System.Int64](https://docs.microsoft.com/dotnet/core/api/System.Int64) | 서명 | 9 | -9,223,372,036,854,775,808 | 9,223,372,036,854,775,807
[System.SByte](https://docs.microsoft.com/dotnet/core/api/System.SByte) | 서명 | 1 | -128 | 127
[System.UInt16](https://docs.microsoft.com/dotnet/core/api/System.UInt16) | 부호 없음 | 2 | 0 | 65,535
[System.UInt32](https://docs.microsoft.com/dotnet/core/api/System.UInt32) | 부호 없음 | 4 | 0 | 4,294,967,295
[System.UInt64](https://docs.microsoft.com/dotnet/core/api/System.UInt64) | 부호 없음 | 9 | 0 | 18,446,744,073,709,551,615

각 정수 계열 형식은 산술, 비교, 같음, 명시적 변환 및 암시적 변환 연산자의 표준 집합을 지원합니다. 각 정수에는 같음 비교 및 상대 비교를 수행하고, 숫자의 문자열 표시를 해당 정수로 변환하고, 정수를 해당 문자열 표시로 변환하는 메서드가 포함됩니다. 두 정수의 더 작거나 더 큰 값을 반올림하고 식별하는 연산과 같이 표준 연산자를 통해 처리되는 연산 이외의 몇몇 추가 수치 연산은 [System.Math](https://docs.microsoft.com/dotnet/core/api/System.Math) 클래스에서 제공됩니다. [System.BitConverter](https://docs.microsoft.com/dotnet/core/api/System.BitConverter) 클래스를 사용하여 정수 값의 개별 비트를 사용할 수도 있습니다. 
     
부호 없는 정수 형식은 CLS와 호환되지 않습니다. 자세한 내용은 [.NET 공용 형식 시스템 및 공용 언어 사양](common-type-system.md)을 참조하세요.

## <a name="floatingpoint-types"></a>부동 소수점 형식

.NET Core에는 다음 표에 나열된 세 가지 기본 부동 소수점 형식이 포함되어 있습니다. 

형식 | 크기(바이트) | 최소값 | 최대값
---- | ------------ | ------------- | -------------
[System.Double](https://docs.microsoft.com/dotnet/core/api/System.Double) | 9 | -1.79769313486232e308 | 1.79769313486232e308
[System.Single](https://docs.microsoft.com/dotnet/core/api/System.Single) | 4 | -3.402823e38 | 3.402823e38
[System.Decimal](https://docs.microsoft.com/dotnet/core/api/System.Decimal) | 16 | -79,228,162,514,264,337,593,543,950,335 | 79,228,162,514,264,337,593,543,950,335
   
각 부동 소수점 형식은 산술, 비교, 같음, 명시적 변환 및 암시적 변환 연산자의 표준 집합을 지원합니다. 각 형식에는 같음 비교 및 상대 비교를 수행하고, 부동 소수점 수의 문자열 표시를 변환하고, 부동 소수점 수를 해당 문자열 표시로 변환하는 메서드가 포함됩니다. 일부 추가적인 수치, 대수 및 삼각 연산은 `Math` 클래스에서 제공됩니다. `BitConverter` 클래스를 사용하여 `Double` 및 `Single` 값의 개별 비트를 사용할 수도 있습니다. `Decimal` 구조체에는 10진수 값의 개별 비트를 사용하기 위한 고유한 메서드인 `Decimal.GetBits` 및 `Decimal.Decimal(Int32())`와 몇몇 추가적인 수치 연산을 수행하기 위한 고유한 메서드 집합이 있습니다. 

`Double` 및 `Single` 형식은 기본적으로 정확하지 않은 값(태양계에서 두 별 사이의 거리) 및 정밀도가 높고 반올림 오류가 적을 필요 없는 응용 프로그램에 사용해야 합니다. 정밀도가 더 높아야 하고 반올림 오류가 없어야 하는 경우 `Decimal` 형식을 사용해야 합니다.

## <a name="biginteger"></a>BigInteger

[System.Numerics.BigInteger](https://docs.microsoft.com/dotnet/core/api/System.Numerics.BigInteger)는 이론상 값에 상한이나 하한이 없는 임의로 큰 정수를 나타내는 변경할 수 없는 형식입니다. `BigInteger` 형식의 메서드는 기타 정수 계열 형식의 메서드와 매우 유사합니다.  

## <a name="complex"></a>복합

[System.Numerics.Complex](https://docs.microsoft.com/dotnet/core/api/System.Numerics.Complex) 형식은 실수 부분과 허수 부분이 포함된 숫자인 복소수를 나타냅니다. 이 형식은 산술, 비교, 같음, 명시적 변환 및 암시적 변환 연산자의 표준 집합과 수치, 대수 및 삼각 메서드를 지원합니다. 

## <a name="simdenabled-vector-types"></a>SIMD 사용 벡터 형식

`System.Numerics` 네임스페이스에는 .NET Core를 위한 SIMD 사용 벡터 형식 집합이 있습니다. SIMD를 통해 일부 연산을 하드웨어 수준에서 병렬화할 수 있습니다. 이 덕분에 벡터를 통해 계산을 수행하는 수치, 공학용 및 그래픽 앱의 성능이 훨씬 향상됩니다. 

.NET Core의 SIMD 사용 벡터 형식은 다음과 같습니다. 

* `Single` 형식의 2차원, 3차원 및 4차원 벡터인 [System.Numerics.Vector2](https://docs.microsoft.com/dotnet/core/api/System.Numerics.Vector2), [System.Numerics.Vector3](https://docs.microsoft.com/dotnet/core/api/System.Numerics.Vector3) 및 [System.Numerics.Vector4](https://docs.microsoft.com/dotnet/core/api/System.Numerics.Vector4) 형식

* 기본 숫자 형식의 벡터를 만들 수 있는 [Vector&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Numerics.Vector-1) 구조체. 기본 숫자 형식에는 Decimal을 제외한 System 네임스페이스의 모든 숫자 형식이 포함됩니다.

* 두 행렬 형식 [System.Numerics.Matrix3x2](https://docs.microsoft.com/dotnet/core/api/System.Numerics.Matrix3x2)(3x2 행렬을 나타냄) 및 [System.Numerics.Matrix4x4](https://docs.microsoft.com/dotnet/core/api/System.Numerics.Matrix4x4)(4x4 행렬을 나타냄) 

* [System.Numerics.Plane](https://docs.microsoft.com/dotnet/core/api/System.Numerics.Plane) 형식(3차원 평면을 나타냄) 및 [System.Numerics.Quaternion](https://docs.microsoft.com/dotnet/core/api/System.Numerics.Quaternion) 형식(3차원 물리적 회전을 인코드하는 데 사용되는 벡터를 나타냄)



<!--HONumber=Nov16_HO3-->


