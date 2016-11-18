---
title: "형식 변환"
description: "형식 변환"
keywords: .NET, .NET Core
author: stevehoag
ms.author: shoag
manager: wpickett
ms.date: 07/22/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: c9a53222-89cb-47e5-983d-4f0f204e31ba
translationtype: Human Translation
ms.sourcegitcommit: b20713600d7c3ddc31be5885733a1e8910ede8c6
ms.openlocfilehash: 9f3f0dc381f8892dfb24d027ccc1f46a9b89135a

---

# <a name="type-conversion"></a>형식 변환

모든 값에는 연결된 형식이 있으며, 이러한 형식은 값에 할당되는 공간, 포함할 수 있는 값의 범위, 값을 통해 사용할 수 있는 멤버 등의 특성을 정의합니다. 대부분의 값들은 하나 이상의 형식으로 표현될 수 있습니다. 예를 들어, `4`라는 값은 정수 값 또는 부동 소수점 값으로 표현될 수 있습니다. 형식 변환을 수행하면 이전 형식과 동일한 값을 가지는 새 형식이 만들어지지만, 원래 개체의 ID(또는 실제 값)가 항상 동일하게 유지되지는 않습니다.

.NET는 다음과 같은 변환을 자동으로 지원합니다.

* 파생 클래스에서 기본 클래스로 변환. 예를 들어, 클래스 또는 구조체 인스턴스가 [Object](xref:System.Object) 인스턴스로 변환될 수 있다는 의미입니다. 이러한 변환에는 캐스팅 연산자가 필요하지 않습니다.

* 기본 클래스에서 원본 파생 클래스로 다시 변환. C#에서는 이러한 변환에 캐스팅 연산자가 필요합니다. Visual Basic에서는 `Option Strict`가 on이면 `CType` 연산자가 필요합니다. 

* 인터페이스를 구현하는 형식에서 그 인터페이스를 나타내는 인터페이스 개체로 변환. 이러한 변환에는 캐스팅 연산자가 필요하지 않습니다.

* 인터페이스 개체에서 그 인터페이스를 구현하는 원래 형식으로 다시 변환. C#에서는 이러한 변환에 캐스팅 연산자가 필요합니다. Visual Basic에서는 `Option Strict`가 on이면 `CType` 연산자가 필요합니다. 

이러한 자동 변환 외에 .NET는 사용자 지정 형식 변환을 지원하는 몇 가지 기능을 제공합니다. 이러한 요구 사항은 다음과 같습니다. 

* 사용 가능한 형식 간 확대 변환을 정의하는 `Implicit` 연산자. 자세한 내용은 [암시적 연산자를 사용한 암시적 변환](#implicit-conversion-with-the-implicit-operator) 섹션을 참조하세요.

* 사용 가능한 형식 간 축소 변환을 정의하는 `Explicit` 연산자. 자세한 내용은 [명시적 연산자를 사용한 명시적 변환](#explicit-conversion-with-the-explicit-operator) 섹션을 참조하세요.

* 기본 .NET Framework 데이터 형식 각각에 대한 변환을 정의하는 [IConvertible](xref:System.IConvertible) 인터페이스. 자세한 내용은 [IConvertible 인터페이스](#the-iconvertible-interface) 섹션을 참조하세요.

* `IConvertible` 인터페이스의 메서드를 구현하는 메서드 집합을 제공하는 [Convert](xref:System.Convert) 클래스. 자세한 내용은 [Convert 클래스](#the-convert-class) 섹션을 참조하세요.

* 지정된 형식을 임의의 형식으로 변환하는 작업을 지원하도록 확장될 수 있는 기본 클래스인 [TypeConverter](xref:System.ComponentModel.TypeConverter) 클래스. 자세한 내용은 [TypeConverter 클래스](#the-typeconverter-class) 섹션을 참조하세요.

## <a name="implicit-conversion-with-the-implicit-operator"></a>암시적 연산자를 사용한 암시적 변환

확대 변환에는 대상 형식보다 제한적인 범위나 제한적인 멤버 목록이 있는 기존 형식의 값에서 새 값을 만드는 작업이 포함됩니다. 확대 변환을 수행하면 정밀도 손실은 발생할 수 있어도 데이터 손실은 발생할 수 없습니다. 데이터 손실이 발생할 수 없기 때문에 컴파일러에서 명시적 변환 메서드나 캐스팅 연산자를 사용할 필요 없이 변환을 암시적이나 투명하게 처리할 수 있습니다.

> [!NOTE]
> 암시적 변환을 수행하는 코드에서 변환 메서드를 호출하거나 캐스팅 연산자를 사용할 수 있지만 암시적 변환을 지원하는 컴파일러에서는 이렇게 할 필요가 없습니다.

예를 들어 [Decimal](xref:System.Decimal) 형식은 [Byte](xref:System.Byte), [Char](xref:System.Char), [Int16](xref:System.Int16), [Int32](xref:System.Int32), [Int64](xref:System.Int64), [SByte](xref:System.SByte), [UInt16](xref:System.UInt16), [UInt32](xref:System.UInt32) 및 [UInt64](xref:System.UInt64) 값에서 암시적 변환을 지원합니다. 다음 예제에서는 값을 `Decimal` 변수에 할당할 때의 이러한 암시적 변환을 보여 줍니다.

```csharp
byte byteValue = 16;
short shortValue = -1024;
int intValue = -1034000;
long longValue = 1152921504606846976;
ulong ulongValue = UInt64.MaxValue;

decimal decimalValue;

decimalValue = byteValue;
Console.WriteLine("After assigning a {0} value, the Decimal value is {1}.", 
                  byteValue.GetType().Name, decimalValue); 

decimalValue = shortValue;
Console.WriteLine("After assigning a {0} value, the Decimal value is {1}.", 
                  shortValue.GetType().Name, decimalValue); 

decimalValue = intValue;
Console.WriteLine("After assigning a {0} value, the Decimal value is {1}.", 
                  intValue.GetType().Name, decimalValue); 

decimalValue = longValue;
Console.WriteLine("After assigning a {0} value, the Decimal value is {1}.", 
                  longValue.GetType().Name, decimalValue); 

decimalValue = ulongValue;
Console.WriteLine("After assigning a {0} value, the Decimal value is {1}.", 
                  longValue.GetType().Name, decimalValue); 
// The example displays the following output:
//    After assigning a Byte value, the Decimal value is 16.
//    After assigning a Int16 value, the Decimal value is -1024.
//    After assigning a Int32 value, the Decimal value is -1034000.
//    After assigning a Int64 value, the Decimal value is 1152921504606846976.
//    After assigning a Int64 value, the Decimal value is 18446744073709551615.
```

```vb
Dim byteValue As Byte = 16
Dim shortValue As Short = -1024
Dim intValue As Integer = -1034000
Dim longValue As Long = CLng(1024^6)
Dim ulongValue As ULong = ULong.MaxValue

Dim decimalValue As Decimal

decimalValue = byteValue
Console.WriteLine("After assigning a {0} value, the Decimal value is {1}.",
                  byteValue.GetType().Name, decimalValue) 

decimalValue = shortValue
Console.WriteLine("After assigning a {0} value, the Decimal value is {1}.",
                  shortValue.GetType().Name, decimalValue) 

decimalValue = intValue
Console.WriteLine("After assigning a {0} value, the Decimal value is {1}.",
                  intValue.GetType().Name, decimalValue) 

decimalValue = longValue
Console.WriteLine("After assigning a {0} value, the Decimal value is {1}.",
                  longValue.GetType().Name, decimalValue) 

decimalValue = ulongValue
Console.WriteLine("After assigning a {0} value, the Decimal value is {1}.",
                  longValue.GetType().Name, decimalValue) 
' The example displays the following output:
'    After assigning a Byte value, the Decimal value is 16.
'    After assigning a Int16 value, the Decimal value is -1024.
'    After assigning a Int32 value, the Decimal value is -1034000.
'    After assigning a Int64 value, the Decimal value is 1152921504606846976.
'    After assigning a Int64 value, the Decimal value is 18446744073709551615.
```

특정 언어 컴파일러에서 오버로드된 사용자 지정 연산자를 지원하는 경우 고유한 사용자 지정 형식에서 암시적 변환을 정의할 수도 있습니다. 다음 예제에서는 부호 및 크기 표현을 사용하는 `ByteWithSign`이라는 부호 있는 바이트 데이터 형식을 부분적으로 구현합니다. 이 예제에서는 [Byte](xref:System.Byte) 및 [SByte](xref:System.SByte) 값을 `ByteWithSign` 값으로 암시적으로 변환하는 작업을 지원합니다.

```csharp
public struct ByteWithSign
{
   private SByte signValue; 
   private Byte value;

   public static implicit operator ByteWithSign(SByte value) 
   {
      ByteWithSign newValue;
      newValue.signValue = (SByte) Math.Sign(value);
      newValue.value = (byte) Math.Abs(value);
      return newValue;
   }  

   public static implicit operator ByteWithSign(Byte value)
   {
      ByteWithSign  newValue;
      newValue.signValue = 1;
      newValue.value = value;
      return newValue;
   }

   public override string ToString()
   { 
      return (signValue * value).ToString();
   }
}
```

```vb
Public Structure ByteWithSign
   Private signValue As SByte 
   Private value As Byte

   Public Overloads Shared Widening Operator CType(value As SByte) As ByteWithSign
      Dim newValue As ByteWithSign
      newValue.signValue = CSByte(Math.Sign(value))
      newValue.value = CByte(Math.Abs(value))
      Return newValue
   End Operator  

   Public Overloads Shared Widening Operator CType(value As Byte) As ByteWithSign
      Dim NewValue As ByteWithSign
      newValue.signValue = 1
      newValue.value = value
      Return newValue
   End Operator

   Public Overrides Function ToString() As String 
      Return (signValue * value).ToString()
   End Function
End Structure
```

클라이언트 코드에서는 다음 예제와 같이 명시적 변환을 수행하거나 캐스팅 연산자를 사용하지 않고 `ByteWithSign` 변수를 선언하여 [Byte](xref:System.Byte) 및 [SByte](xref:System.SByte) 값에 할당할 수 있습니다.

```csharp
SByte sbyteValue = -120;
ByteWithSign value = sbyteValue;
Console.WriteLine(value);
value = Byte.MaxValue;
Console.WriteLine(value);
// The example displays the following output:
//       -120
//       255
```

```vb
Dim sbyteValue As SByte = -120
Dim value As ByteWithSign = sbyteValue
Console.WriteLine(value.ToString()) 
value = Byte.MaxValue
Console.WriteLine(value.ToString()) 
' The example displays the following output:
'       -120
'       255
```

## <a name="explicit-conversion-with-the-explicit-operator"></a>명시적 연산자를 사용한 명시적 변환

축소 변환에는 대상 형식보다 큰 범위나 큰 멤버 목록이 있는 기존 형식의 값에서 새 값을 만드는 작업이 포함됩니다. 축소 변환을 수행하면 데이터 손실이 발생할 수 있기 때문에 컴파일러에서 변환 메서드 호출이나 캐스팅 연산자를 통해 명시적으로 변환을 수행하도록 요구하는 경우가 많습니다. 즉, 개발자 코드에서 변환이 명시적으로 처리되어야 합니다. 

> [!NOTE]
> 축소 변환에 변환 메서드나 캐스팅 연산자를 요구하는 주요 목적은 개발자가 데이터 손실이나 [OverflowException](xref:System.OverflowException)의 가능성을 인식하여 코드에서 처리할 수 있게 하는 것입니다. 그러나 일부 컴파일러에서는 이 요구 사항을 완화할 수 있습니다. 예를 들어, Visual Basic에서 `Option Strict`가 해제(기본 설정)되면 Visual Basic 컴파일러에서 축소 변환을 암시적으로 수행하려고 합니다.

예를 들어, 다음 표와 같이 [UInt32](xref:System.UInt32), [Int64](xref:System.Int64) 및 [UInt64](xref:System.UInt64) 데이터 형식은 [Int32](xref:System.Int32) 데이터 형식을 초과하는 범위를 포함합니다.

형식 | Int32 범위와 비교
---- | ------------------------------
[Int64](xref:System.Int64) | [Int64.MaxValue](xref:System.Int64.MaxValue)는 [Int32.MaxValue](xref:System.Int32#System_Int32_MaxValue)보다 크고 [Int64.MinValue](xref:System.Int64.MinValue)는 [Int32.MinValue](xref:System.Int32#System_Int32_MinValue)보다 작습니다(즉, 큰 음의 범위를 가짐).
[UInt32](xref:System.UInt32) | [UInt32.MaxValue](xref:System.UInt32.MaxValue)는 [Int32.MaxValue](xref:System.Int32.MaxValue)보다 큽니다.
[UInt64](xref:System.UInt64) | [UInt64.MaxValue](xref:System.UInt64.MaxValue)는 [Int32.MaxValue](xref:System.Int32.MaxValue)보다 큽니다.

이러한 축소 변환을 처리하기 위해 .NET에서는 형식에서 `Explicit` 연산자를 정의할 수 있게 합니다. 그러면 개별 언어 컴파일러에서 고유 구문을 사용하여 이 연산자를 구현할 수 있거나 변환을 수행하기 위해 [Convert](xref:System.Convert) 클래스의 멤버가 호출될 수 있습니다. (`Convert` 클래스에 대한 자세한 내용은 이 항목의 뒷부분에서 [Convert 클래스](#the-convert-class)를 참조하세요.) 다음 예제에서는 언어 기능을 사용하여 범위를 벗어날 수 있는 이러한 정수 값을 [Int32](xref:System.Int32) 값으로 명시적으로 변환하는 작업을 처리하는 방법을 보여 줍니다. 

```csharp
long number1 = int.MaxValue + 20L;
uint number2 = int.MaxValue - 1000;
ulong number3 = int.MaxValue;

int intNumber;

try {
   intNumber = checked((int) number1);
   Console.WriteLine("After assigning a {0} value, the Integer value is {1}.", 
                     number1.GetType().Name, intNumber); 
}
catch (OverflowException) {
   if (number1 > int.MaxValue)
      Console.WriteLine("Conversion failed: {0} exceeds {1}.", 
                        number1, int.MaxValue);
   else
      Console.WriteLine("Conversion failed: {0} is less than {1}.", 
                        number1, int.MinValue);
}

try {
   intNumber = checked((int) number2);
   Console.WriteLine("After assigning a {0} value, the Integer value is {1}.", 
                     number2.GetType().Name, intNumber); 
}
catch (OverflowException) {
   Console.WriteLine("Conversion failed: {0} exceeds {1}.", 
                     number2, int.MaxValue);
}

try {
   intNumber = checked((int) number3);
   Console.WriteLine("After assigning a {0} value, the Integer value is {1}.", 
                     number3.GetType().Name, intNumber); 
}
catch (OverflowException) {
   Console.WriteLine("Conversion failed: {0} exceeds {1}.", 
                     number1, int.MaxValue);
}

// The example displays the following output:
//    Conversion failed: 2147483667 exceeds 2147483647.
//    After assigning a UInt32 value, the Integer value is 2147482647.
//    After assigning a UInt64 value, the Integer value is 2147483647.
```

```vb
Dim number1 As Long = Integer.MaxValue + 20L
Dim number2 As UInteger = Integer.MaxValue - 1000
Dim number3 As ULong = Integer.MaxValue

Dim intNumber As Integer

Try
   intNumber = CInt(number1)
   Console.WriteLine("After assigning a {0} value, the Integer value is {1}.", 
                       number1.GetType().Name, intNumber)
Catch e As OverflowException
   If number1 > Integer.MaxValue Then
      Console.WriteLine("Conversion failed: {0} exceeds {1}.", 
                                        number1, Integer.MaxValue)
   Else
      Console.WriteLine("Conversion failed: {0} is less than {1}.\n", 
                                        number1, Integer.MinValue)
   End If
End Try

Try
   intNumber = CInt(number2)
   Console.WriteLine("After assigning a {0} value, the Integer value is {1}.", 
                       number2.GetType().Name, intNumber)
Catch e As OverflowException
   Console.WriteLine("Conversion failed: {0} exceeds {1}.", 
                                     number2, Integer.MaxValue)
End Try

Try
   intNumber = CInt(number3)
   Console.WriteLine("After assigning a {0} value, the Integer value is {1}.", 
                       number3.GetType().Name, intNumber)
Catch e As OverflowException
   Console.WriteLine("Conversion failed: {0} exceeds {1}.",
                                     number1, Integer.MaxValue)
End Try
' The example displays the following output:
'    Conversion failed: 2147483667 exceeds 2147483647.
'    After assigning a UInt32 value, the Integer value is 2147482647.
'    After assigning a UInt64 value, the Integer value is 2147483647.
```

명시적 변환의 결과는 언어마다 다를 수 있으며 해당 [Convert](xref:System.Convert) 메서드에서 반환하는 값에 따라서도 다를 수 있습니다. 예를 들어, [Double](xref:System.Double) 값 **12.63251**이 [Int32](xref:System.Int32)로 변환되면 .NET [Convert.ToInt32(Double)](xref:System.Convert.ToInt32(System.Double)) 메서드 및 Visual Basic `CInt` 메서드는 [Double](xref:System.Double)을 반올림하여 값 **13**을 반환하지만 C# `(int)` 연산자는 [Double](xref:System.Double)을 잘라 값 **12**를 반환합니다. 마찬가지로 C# `(int)` 연산자는 부울을 정수로 변환하는 것을 지원하지만 Visual Basic `CInt` 메서드는 값 `true`를 **-1**로 변환합니다. 반면에 [Convert.ToInt32(Boolean)](xref:System.Convert.ToInt32(System.Boolean)) 메서드는 값 `true`를 **1**로 변환합니다.

대부분의 컴파일러에서는 검사 변환 방식이나 비검사 변환 방식으로 명시적 변환을 수행할 수 있습니다. 검사 변환을 수행하는 경우 변환할 형식의 값이 대상 형식의 범위를 벗어나면 [OverflowException](xref:System.OverflowException)이 throw됩니다. 같은 조건에서 비검사 변환을 수행하면 예외가 throw되지는 않지만 정확한 동작이 정의되지 않으며 잘못된 결과 값이 생성될 수 있습니다.

> [!NOTE]
> C#에서 검사 변환을 수행하려면 캐스팅 연산자와 함께 `checked` 키워드를 사용하거나 `/checked+` 컴파일러 옵션을 지정합니다. 반대로 비검사 변환을 수행하려면 캐스팅 연산자와 함께 `unchecked` 키워드를 사용하거나 `/checked-` 컴파일러 옵션을 지정합니다. 기본적으로 명시적 변환은 검사되지 않습니다. Visual Basic에서 확인된 변환은 `/removeintchecks-` 컴파일러 옵션을 지정하여 수행될 수 있습니다. 반대로 확인되지 않은 변환은 `/removeintchecks+` 컴파일러 옵션을 지정하여 수행될 수 있습니다. 기본적으로 명시적 변환은 검사됩니다.

다음 C# 예제에서는 `checked` 및 `unchecked` 키워드를 사용하여 [Byte](xref:System.Byte) 범위 밖에 있는 값을 `Byte`로 변환할 때 동작이 어떻게 다른지 보여 줍니다. 검사 변환은 예외를 throw하지만 비검사 변환은 [Byte.MaxValue](xref:System.Byte.MaxValue)를 `Byte` 변수에 할당합니다.

```csharp
int largeValue = Int32.MaxValue;
byte newValue;

try {
   newValue = unchecked((byte) largeValue);
   Console.WriteLine("Converted the {0} value {1} to the {2} value {3}.", 
                     largeValue.GetType().Name, largeValue,
                     newValue.GetType().Name, newValue);
}
catch (OverflowException) {
   Console.WriteLine("{0} is outside the range of the Byte data type.", 
                     largeValue);
}

try {
   newValue = checked((byte) largeValue);
   Console.WriteLine("Converted the {0} value {1} to the {2} value {3}.", 
                     largeValue.GetType().Name, largeValue,
                     newValue.GetType().Name, newValue);
}
catch (OverflowException) {
   Console.WriteLine("{0} is outside the range of the Byte data type.", 
                     largeValue);
}
// The example displays the following output:
//    Converted the Int32 value 2147483647 to the Byte value 255.
//    2147483647 is outside the range of the Byte data type.
```

특정 언어 컴파일러에서 오버로드된 사용자 지정 연산자를 지원하는 경우 고유한 사용자 지정 형식에서 명시적 변환을 정의할 수도 있습니다. 다음 예제에서는 부호 및 크기 표현을 사용하는 `ByteWithSign`이라는 부호 있는 바이트 데이터 형식을 부분적으로 구현합니다. 이 예제에서는 [Int32](xref:System.Int32) 및 [UInt32](xref:System.UInt32) 값을 `ByteWithSign` 값으로 명시적으로 변환하는 작업을 지원합니다.

```csharp
public struct ByteWithSign
{
   private SByte signValue; 
   private Byte value;

   private const byte MaxValue = byte.MaxValue;
   private const int MinValue = -1 * byte.MaxValue;

   public static explicit operator ByteWithSign(int value) 
   {
      // Check for overflow.
      if (value > ByteWithSign.MaxValue || value < ByteWithSign.MinValue)
         throw new OverflowException(String.Format("'{0}' is out of range of the ByteWithSign 
                                                   data type.", 
                                                   value));

      ByteWithSign newValue;
      newValue.signValue = (SByte) Math.Sign(value);
      newValue.value = (byte) Math.Abs(value);
      return newValue;
   }  

   public static explicit operator ByteWithSign(uint value)
   {
      if (value > ByteWithSign.MaxValue) 
         throw new OverflowException(String.Format("'{0}' is out of range of the ByteWithSign 
                                                   data type.", 
                                                   value));

      ByteWithSign newValue;
      newValue.signValue = 1;
      newValue.value = (byte) value;
      return newValue;
   }

   public override string ToString()
   { 
      return (signValue * value).ToString();
   }
}
```

```vb
Public Structure ByteWithSign
   Private signValue As SByte 
   Private value As Byte

   Private Const MaxValue As Byte = Byte.MaxValue
   Private Const MinValue As Integer = -1 * Byte.MaxValue

   Public Overloads Shared Narrowing Operator CType(value As Integer) As ByteWithSign
      ' Check for overflow.
      If value > ByteWithSign.MaxValue Or value < ByteWithSign.MinValue Then
         Throw New OverflowException(String.Format("'{0}' is out of range of the ByteWithSign 
                                                   data type.", value))
      End If

      Dim newValue As ByteWithSign

      newValue.signValue = CSByte(Math.Sign(value))
      newValue.value = CByte(Math.Abs(value))
      Return newValue
   End Operator

   Public Overloads Shared Narrowing Operator CType(value As UInteger) As ByteWithSign
      If value > ByteWithSign.MaxValue Then 
         Throw New OverflowException(String.Format("'{0}' is out of range of the ByteWithSign 
                                                   data type.", value))
      End If

      Dim NewValue As ByteWithSign

      newValue.signValue = 1
      newValue.value = CByte(value)
      Return newValue
   End Operator

   Public Overrides Function ToString() As String 
      Return (signValue * value).ToString()
   End Function
End Structure
```

클라이언트 코드에서는 할당에 캐스팅 연산자나 변환 메서드가 포함된 경우 다음 예제와 같이 `ByteWithSign` 변수를 선언하여 [Int32](xref:System.Int32) 및 [UInt32](xref:System.UInt32) 값에 할당할 수 있습니다.

```csharp
ByteWithSign value;

try {
   int intValue = -120;
   value = (ByteWithSign) intValue;
   Console.WriteLine(value);
}
catch (OverflowException e) {
   Console.WriteLine(e.Message);
}

try {
   uint uintValue = 1024;
   value = (ByteWithSign) uintValue;
   Console.WriteLine(value);
}
catch (OverflowException e) {
    Console.WriteLine(e.Message);
}
// The example displays the following output:
//       -120
//       '1024' is out of range of the ByteWithSign data type.
```

```vb
Dim value As ByteWithSign

Try  
   Dim intValue As Integer = -120
   value = CType(intValue, ByteWithSign)
   Console.WriteLine(value) 
Catch e As OverflowException
   Console.WriteLine(e.Message)
End Try

Try
   Dim uintValue As UInteger = 1024
   value = CType(uintValue, ByteWithSign)
   Console.WriteLine(value) 
Catch e As OverflowException
   Console.WriteLine(e.Message)
End Try
' The example displays the following output:
'       -120
'       '1024' is out of range of the ByteWithSign data type.
```

## <a name="the-iconvertible-interface"></a>IConvertible 인터페이스

형식을 공용 언어 런타임 기본 형식으로 변환하는 작업을 지원하기 위해 .NET에서는 [IConvertible](xref:System.IConvertible) 인터페이스를 제공합니다. 구현하는 형식은 다음을 제공해야 합니다.

* 구현하는 형식의 [TypeCode](xref:System.TypeCode)를 반환하는 메서드.

* 구현하는 형식을 각 공용 언어 런타임 기본 형식([Boolean](xref:System.Boolean), [Byte](xref:System.Byte), [DateTime](xref:System.DateTime), [Decimal](xref:System.Decimal), [Double](xref:System.Double) 등)으로 변환하는 메서드.

* 구현하는 형식의 인스턴스를 다른 지정된 형식으로 변환하는 일반화된 변환 메서드. 지원되지 않는 변환은 [InvalidCastException](xref:System.InvalidCastException)을 throw해야 합니다.

각 공용 언어 런타임 기본 형식(즉, [DBNull](xref:System.DBNull) 및 [Enum](xref:System.Enum) 형식뿐만 아니라 [Boolean](xref:System.Boolean), [Byte](xref:System.Byte), [Char](xref:System.Char), [DateTime](xref:System.DateTime), [Decimal](xref:System.Decimal), [Double](xref:System.Double), [Int16](xref:System.Int16), [Int32](xref:System.Int32), [Int64](xref:System.Int64), [SByte](xref:System.SByte), [Single](xref:System.Single), [String](xref:System.String), [UInt16](xref:System.UInt16), [UInt32](xref:System.UInt32) 및 [UInt64](xref:System.UInt64))은 [IConvertible](xref:System.IConvertible) 인터페이스를 구현합니다. 그러나 이러한 구현은 명시적 인터페이스 구현입니다. 변환 메서드는 다음 예제와 같이 [IConvertible](xref:System.IConvertible) 인터페이스 변수를 통해서만 호출할 수 있습니다. 이 예제에서는 [Int32](xref:System.Int32) 값을 해당하는 [Char](xref:System.Char) 값으로 변환합니다.

```csharp
int codePoint = 1067;
IConvertible iConv = codePoint;
char ch = iConv.ToChar(null);
Console.WriteLine("Converted {0} to {1}.", codePoint, ch);
```

```vb
Dim codePoint As Integer = 1067
Dim iConv As IConvertible = codePoint
Dim ch As Char = iConv.ToChar(Nothing)
Console.WriteLine("Converted {0} to {1}.", codePoint, ch)
```

구현하는 형식이 아니라 인터페이스에 대해 변환 메서드를 호출해야 하기 때문에 명시적 인터페이스 구현 비용이 비교적 높아집니다. 따라서 공용 언어 런타임 기본 형식 간에 변환하려면 이 방법 대신 [Convert](xref:System.Convert) 클래스의 적절한 멤버를 호출하는 것이 좋습니다. 자세한 내용은 다음에 나오는 [Convert 클래스](#the-convert-class) 섹션을 참조하세요. 

> [!NOTE]
> .NET에서 제공하는 [IConvertible](xref:System.IConvertible) 인터페이스와 [Convert](xref:System.Convert) 클래스 외에도 개별 언어에서 변환을 수행하는 방법을 제공할 수 있습니다. 예를 들어, C#에서는 캐스팅 연산자가 사용되고, Visual Basic에서는 `CType`, `CInt` 및 `DirectCast` 등의 컴파일러 구현 변환 함수가 사용됩니다.

대부분의 경우 [IConvertible](xref:System.IConvertible) 인터페이스는 .NET에서 기본 형식 간의 변환을 지원하도록 설계되었습니다. 그러나 사용자 지정 형식에서도 이 인터페이스를 구현하여 해당 사용자 지정 형식을 다른 사용자 지정 형식으로 변환하는 작업을 지원할 수 있습니다. 자세한 내용은 이 항목의 뒷부분에 나오는 [ChangeType 메서드를 사용한 사용자 지정 변환](#custom-conversions-with-the-changetype-method) 섹션을 참조하세요.

## <a name="the-convert-class"></a>Convert 클래스

각 기본 형식의 [IConvertible](xref:System.IConvertible) 인터페이스 구현을 호출하여 형식 변환을 수행할 수 있지만 [System.Convert](xref:System.Convert) 클래스의 메서드를 호출하는 것이 한 기본 형식에서 다른 형식으로 변환하는 데 권장되는 언어와 무관한 방법입니다. 또한 [Convert.ChangeType(Object, Type, IFormatProvider)](xref:System.Convert.ChangeType(System.Object,System.Type,System.IFormatProvider)) 메서드를 사용하여 지정된 사용자 지정 형식을 다른 형식으로 변환할 수 있습니다. 

### <a name="conversions-between-base-types"></a>기본 형식 간 변환

[Convert](xref:System.Convert) 클래스를 사용하여 언어와 무관하게 기본 형식 간의 변환을 수행할 수 있으므로 공용 언어 런타임을 대상으로 하는 모든 언어에서 이 클래스를 사용할 수 있습니다. 이 클래스는 확대 및 축소 변환을 위한 전체 메서드 집합을 제공하며 지원되지 않는 변환([DateTime](xref:System.DateTime) 값을 정수 값으로 변환하는 등)의 경우 [InvalidCastException](xref:System.InvalidCastException)을 throw합니다. 축소 변환은 확인된 컨텍스트에서 수행되며 변환이 실패하는 경우 [OverflowException](xref:System.OverflowException)이 throw됩니다.

> [!IMPORTANT]
> [Convert](xref:System.Convert) 클래스에 각 기본 형식으로 변환하거나 각 기본 형식에서 변환하는 메서드가 포함되어 있기 때문에 각 기본 형식의 [IConvertible](xref:System.IConvertible) 명시적 인터페이스 구현을 호출할 필요가 없습니다.

다음 예제에서는 [System.Convert](xref:System.Convert) 클래스를 사용하여 .NET 기본 형식 간의 몇 가지 축소 및 확대 변환을 수행하는 방법을 보여 줍니다.

```csharp
// Convert an Int32 value to a Decimal (a widening conversion).
int integralValue = 12534;
decimal decimalValue = Convert.ToDecimal(integralValue);
Console.WriteLine("Converted the {0} value {1} to " +  
                                  "the {2} value {3:N2}.", 
                                  integralValue.GetType().Name, 
                                  integralValue, 
                                  decimalValue.GetType().Name, 
                                  decimalValue);
// Convert a Byte value to an Int32 value (a widening conversion).
byte byteValue = Byte.MaxValue;
int integralValue2 = Convert.ToInt32(byteValue);                                  
Console.WriteLine("Converted the {0} value {1} to " +  
                                  "the {2} value {3:G}.", 
                                  byteValue.GetType().Name, 
                                  byteValue, 
                                  integralValue2.GetType().Name, 
                                  integralValue2);

// Convert a Double value to an Int32 value (a narrowing conversion).
double doubleValue = 16.32513e12;
try {
   long longValue = Convert.ToInt64(doubleValue);
   Console.WriteLine("Converted the {0} value {1:E} to " +  
                                     "the {2} value {3:N0}.", 
                                     doubleValue.GetType().Name, 
                                     doubleValue, 
                                     longValue.GetType().Name, 
                                     longValue);
}
catch (OverflowException) {
   Console.WriteLine("Unable to convert the {0:E} value {1}.", 
                                     doubleValue.GetType().Name, doubleValue);
}

// Convert a signed byte to a byte (a narrowing conversion).     
sbyte sbyteValue = -16;
try {
   byte byteValue2 = Convert.ToByte(sbyteValue);
   Console.WriteLine("Converted the {0} value {1} to " +  
                                     "the {2} value {3:G}.", 
                                     sbyteValue.GetType().Name, 
                                     sbyteValue, 
                                     byteValue2.GetType().Name, 
                                     byteValue2);
}
catch (OverflowException) {
   Console.WriteLine("Unable to convert the {0} value {1}.", 
                                     sbyteValue.GetType().Name, sbyteValue);
}                                         
// The example displays the following output:
//       Converted the Int32 value 12534 to the Decimal value 12,534.00.
//       Converted the Byte value 255 to the Int32 value 255.
//       Converted the Double value 1.632513E+013 to the Int64 value 16,325,130,000,000.
//       Unable to convert the SByte value -16.
```

```vb
' Convert an Int32 value to a Decimal (a widening conversion).
Dim integralValue As Integer = 12534
Dim decimalValue As Decimal = Convert.ToDecimal(integralValue)
Console.WriteLine("Converted the {0} value {1} to the {2} value {3:N2}.", 
                  integralValue.GetType().Name,
                  integralValue,
                  decimalValue.GetType().Name,
                  decimalValue)

' Convert a Byte value to an Int32 value (a widening conversion).
Dim byteValue As Byte = Byte.MaxValue
Dim integralValue2 As Integer = Convert.ToInt32(byteValue)                                  
Console.WriteLine("Converted the {0} value {1} to " + 
                                  "the {2} value {3:G}.",
                                  byteValue.GetType().Name,
                                  byteValue,
                                  integralValue2.GetType().Name,
                                  integralValue2)

' Convert a Double value to an Int32 value (a narrowing conversion).
Dim doubleValue As Double = 16.32513e12
Try
   Dim longValue As Long = Convert.ToInt64(doubleValue)
   Console.WriteLine("Converted the {0} value {1:E} to " + 
                                     "the {2} value {3:N0}.",
                                     doubleValue.GetType().Name,
                                     doubleValue,
                                     longValue.GetType().Name,
                                     longValue)
Catch e As OverflowException
   Console.WriteLine("Unable to convert the {0:E} value {1}.",
                                     doubleValue.GetType().Name, doubleValue)
End Try                                                             

' Convert a signed byte to a byte (a narrowing conversion).     
Dim sbyteValue As SByte = -16
Try
   Dim byteValue2 As Byte = Convert.ToByte(sbyteValue)
   Console.WriteLine("Converted the {0} value {1} to " + 
                                     "the {2} value {3:G}.",
                                     sbyteValue.GetType().Name,
                                     sbyteValue,
                                     byteValue2.GetType().Name,
                                     byteValue2)
Catch e As OverflowException
   Console.WriteLine("Unable to convert the {0} value {1}.",
                                     sbyteValue.GetType().Name, sbyteValue)
End Try 
' The example displays the following output:
'       Converted the Int32 value 12534 to the Decimal value 12,534.00.
'       Converted the Byte value 255 to the Int32 value 255.
'       Converted the Double value 1.632513E+013 to the Int64 value 16,325,130,000,000.
'       Unable to convert the SByte value -16.
```

부동 소수점 값으로 변환하거나 부동 소수점 값에서 변환하는 등의 경우에는 변환을 수행할 때 [OverflowException](xref:System.OverflowException)이 throw되지 않아도 정밀도가 손실될 수 있습니다. 다음 예제에서는 이러한 정밀도 손실을 보여 줍니다. 첫 번째 경우에 [Decimal](xref:System.Decimal) 값은 [Double](xref:System.Double)로 변환될 때 정밀도가 낮아집니다(유효 자릿수가 적어짐). 두 번째 경우에 [Double](xref:System.Double) 값은 변환을 완료하기 위해 **42.72**에서 **43**으로 반올림됩니다.

```csharp
double doubleValue; 

// Convert a Double to a Decimal.
decimal decimalValue = 13956810.96702888123451471211m;
doubleValue = Convert.ToDouble(decimalValue);
Console.WriteLine("{0} converted to {1}.", decimalValue, doubleValue);

doubleValue = 42.72;
try {
   int integerValue = Convert.ToInt32(doubleValue);
   Console.WriteLine("{0} converted to {1}.", 
                                     doubleValue, integerValue);
}
catch (OverflowException) {      
   Console.WriteLine("Unable to convert {0} to an integer.", 
                                     doubleValue);
}   
// The example displays the following output:
//       13956810.96702888123451471211 converted to 13956810.9670289.
//       42.72 converted to 43.
```

```vb
Dim doubleValue As Double 

' Convert a Double to a Decimal.
Dim decimalValue As Decimal = 13956810.96702888123451471211d
doubleValue = Convert.ToDouble(decimalValue)
Console.WriteLine("{0} converted to {1}.", decimalValue, doubleValue)

doubleValue = 42.72
Try
   Dim integerValue As Integer = Convert.ToInt32(doubleValue)
   Console.WriteLine("{0} converted to {1}.",
                                     doubleValue, integerValue)
Catch e As OverflowException
   Console.WriteLine("Unable to convert {0} to an integer.",
                                     doubleValue)
End Try   
' The example displays the following output:
'       13956810.96702888123451471211 converted to 13956810.9670289.
'       42.72 converted to 43.
```

[Convert](xref:System.Convert) 클래스에서 지원하는 확대 및 축소 변환의 목록이 포함된 표는 [형식 변환표](conversion-tables.md)를 참조하세요.

### <a name="custom-conversions-with-the-changetype-method"></a>ChangeType 메서드를 사용한 사용자 지정 변환

각 기본 형식에 대한 변환을 지원하는 것 외에도 [Convert](xref:System.Convert) 클래스를 사용하여 사용자 지정 형식을 하나 이상의 미리 정의된 형식으로 변환할 수 있습니다. 이 변환은[Convert.ChangeType(Object, Type, IFormatProvider)](xref:System.Convert.ChangeType(System.Object,System.Type,System.IFormatProvider)) 메서드에서 수행되면 값 매개 변수의 [IConvertible.ToType](xref:System.IConvertible.ToType(System.Type,System.IFormatProvider)) 메서드에 대한 호출을 래핑합니다. 즉, 값 매개 변수가 나타내는 개체가 [IConvertible](xref:System.IConvertible) 인터페이스의 구현을 제공해야 합니다.

> [!NOTE]
> [Convert.ChangeType(Object, Type)](xref:System.Convert.ChangeType(System.Object,System.Type)) 및 [Convert.ChangeType(Object, Type, IFormatProvider)](xref:System.Convert.ChangeType(System.Object,System.Type,System.IFormatProvider)) 메서드는 [Type](xref:System.Type) 개체를 사용하여 값이 변환되는 대상 값을 지정하기 때문에, 이런 메서드를 사용하여 컴파일 타임에 형식을 알 수 없는 개체로 동적 변환을 수행할 수 있습니다. 하지만, 값의 [IConvertible](xref:System.IConvertible) 구현에서는 이 변환을 계속 지원해야 합니다. 

다음 예제에서는 `TemperatureCelsius` 개체와 `TemperatureFahrenheit` 개체 간을 변환할 수 있게 하는 [IConvertible](xref:System.IConvertible) 인터페이스의 가능한 구현을 보여 줍니다. 이 예제에서는 [IConvertible](xref:System.IConvertible) 인터페이스를 구현하고 [Object.ToString](xref:System.Object.ToString) 메서드를 재정의하는 기본 클래스 `Temperature`를 정의합니다. 파생된 `TemperatureCelsius` 및 `TemperatureFahrenheit` 클래스는 기본 클래스의 `ToType` 및 `ToString` 메서드를 각각 재정의합니다. 

```csharp
using System;

public abstract class Temperature : IConvertible
{
   protected decimal temp;

   public Temperature(decimal temperature)
   {
      this.temp = temperature;
   }

   public decimal Value
   { 
      get { return this.temp; } 
      set { this.temp = Value; }        
   }

   public override string ToString()
   {
      return temp.ToString(null as IFormatProvider) + "º";
   }

   // IConvertible implementations.
   public TypeCode GetTypeCode() { 
      return TypeCode.Object;
   }   

   public bool ToBoolean(IFormatProvider provider) {
      throw new InvalidCastException(String.Format("Temperature-to-Boolean conversion is not supported."));
   }

   public byte ToByte(IFormatProvider provider) {
      if (temp < Byte.MinValue || temp > Byte.MaxValue)
         throw new OverflowException(String.Format("{0} is out of range of the Byte data type.", temp));
      else
         return (byte) temp;
   }

   public char ToChar(IFormatProvider provider) {
      throw new InvalidCastException("Temperature-to-Char conversion is not supported.");
   }

   public DateTime ToDateTime(IFormatProvider provider) {
      throw new InvalidCastException("Temperature-to-DateTime conversion is not supported.");
   }

   public decimal ToDecimal(IFormatProvider provider) {
      return temp;
   }

   public double ToDouble(IFormatProvider provider) {
      return (double) temp;
   }

   public short ToInt16(IFormatProvider provider) {
      if (temp < Int16.MinValue || temp > Int16.MaxValue)
         throw new OverflowException(String.Format("{0} is out of range of the Int16 data type.", temp));
      else
         return (short) Math.Round(temp);
   }

   public int ToInt32(IFormatProvider provider) {
      if (temp < Int32.MinValue || temp > Int32.MaxValue)
         throw new OverflowException(String.Format("{0} is out of range of the Int32 data type.", temp));
      else
         return (int) Math.Round(temp);
   }

   public long ToInt64(IFormatProvider provider) {
      if (temp < Int64.MinValue || temp > Int64.MaxValue)
         throw new OverflowException(String.Format("{0} is out of range of the Int64 data type.", temp));
      else
         return (long) Math.Round(temp);
   }

   public sbyte ToSByte(IFormatProvider provider) {
      if (temp < SByte.MinValue || temp > SByte.MaxValue)
         throw new OverflowException(String.Format("{0} is out of range of the SByte data type.", temp));
      else
         return (sbyte) temp;
   }

   public float ToSingle(IFormatProvider provider) {
      return (float) temp;
   }

   public virtual string ToString(IFormatProvider provider) {
      return temp.ToString(provider) + "°";
   }

   // If conversionType is implemented by another IConvertible method, call it.
   public virtual object ToType(Type conversionType, IFormatProvider provider) {
      switch (Type.GetTypeCode(conversionType))
      {
         case TypeCode.Boolean:
            return this.ToBoolean(provider);
         case TypeCode.Byte:
            return this.ToByte(provider);
         case TypeCode.Char:
            return this.ToChar(provider);
         case TypeCode.DateTime:
            return this.ToDateTime(provider);
         case TypeCode.Decimal:
            return this.ToDecimal(provider);
         case TypeCode.Double:
            return this.ToDouble(provider);
         case TypeCode.Empty:
            throw new NullReferenceException("The target type is null.");
         case TypeCode.Int16:
            return this.ToInt16(provider);
         case TypeCode.Int32:
            return this.ToInt32(provider);
         case TypeCode.Int64:
            return this.ToInt64(provider);
         case TypeCode.Object:
            // Leave conversion of non-base types to derived classes.
            throw new InvalidCastException(String.Format("Cannot convert from Temperature to {0}.", 
                                           conversionType.Name));
         case TypeCode.SByte:
            return this.ToSByte(provider);
         case TypeCode.Single:
            return this.ToSingle(provider);
         case TypeCode.String:
            IConvertible iconv = this;
            return iconv.ToString(provider);
         case TypeCode.UInt16:
            return this.ToUInt16(provider);
         case TypeCode.UInt32:
            return this.ToUInt32(provider);
         case TypeCode.UInt64:
            return this.ToUInt64(provider);
         default:
            throw new InvalidCastException("Conversion not supported.");
      }
   }

   public ushort ToUInt16(IFormatProvider provider) {
      if (temp < UInt16.MinValue || temp > UInt16.MaxValue)
         throw new OverflowException(String.Format("{0} is out of range of the UInt16 data type.", temp));
      else
         return (ushort) Math.Round(temp);
   }

   public uint ToUInt32(IFormatProvider provider) {
      if (temp < UInt32.MinValue || temp > UInt32.MaxValue)
         throw new OverflowException(String.Format("{0} is out of range of the UInt32 data type.", temp));
      else
         return (uint) Math.Round(temp);
   }

   public ulong ToUInt64(IFormatProvider provider) {
      if (temp < UInt64.MinValue || temp > UInt64.MaxValue)
         throw new OverflowException(String.Format("{0} is out of range of the UInt64 data type.", temp));
      else
         return (ulong) Math.Round(temp);
   }
}

public class TemperatureCelsius : Temperature, IConvertible
{
   public TemperatureCelsius(decimal value) : base(value)
   {
   }

   // Override ToString methods.
   public override string ToString()
   {
      return this.ToString(null);
   }

   public override string ToString(IFormatProvider provider)
   {
      return temp.ToString(provider) + "°C"; 
   }

   // If conversionType is a implemented by another IConvertible method, call it.
   public override object ToType(Type conversionType, IFormatProvider provider) {
      // For non-objects, call base method.
      if (Type.GetTypeCode(conversionType) != TypeCode.Object) {
         return base.ToType(conversionType, provider);
      }   
      else
      {   
         if (conversionType.Equals(typeof(TemperatureCelsius)))
            return this;
         else if (conversionType.Equals(typeof(TemperatureFahrenheit)))
            return new TemperatureFahrenheit((decimal) this.temp * 9 / 5 + 32);
         else
            throw new InvalidCastException(String.Format("Cannot convert from Temperature to {0}.", 
                                           conversionType.Name));
      }
   }
}

public class TemperatureFahrenheit : Temperature, IConvertible 
{
   public TemperatureFahrenheit(decimal value) : base(value)
   {
   }

   // Override ToString methods.
   public override string ToString()
   {
      return this.ToString(null);
   }

   public override string ToString(IFormatProvider provider)
   {
      return temp.ToString(provider) + "°F"; 
   }

   public override object ToType(Type conversionType, IFormatProvider provider)
   { 
      // For non-objects, call base methood.
      if (Type.GetTypeCode(conversionType) != TypeCode.Object) {
         return base.ToType(conversionType, provider);
      }   
      else
      {   
         // Handle conversion between derived classes.
         if (conversionType.Equals(typeof(TemperatureFahrenheit))) 
            return this;
         else if (conversionType.Equals(typeof(TemperatureCelsius)))
            return new TemperatureCelsius((decimal) (this.temp - 32) * 5 / 9);
         // Unspecified object type: throw an InvalidCastException.
         else
            throw new InvalidCastException(String.Format("Cannot convert from Temperature to {0}.", 
                                           conversionType.Name));
      }                                 
   }
}
```

```vb
Public MustInherit Class Temperature
   Implements IConvertible

   Protected temp As Decimal

   Public Sub New(temperature As Decimal)
      Me.temp = temperature
   End Sub

   Public Property Value As Decimal
      Get 
         Return Me.temp
      End Get
      Set
         Me.temp = Value
      End Set   
   End Property

   Public Overrides Function ToString() As String
      Return temp.ToString() & "º"
   End Function

   ' IConvertible implementations.
   Public Function GetTypeCode() As TypeCode Implements IConvertible.GetTypeCode
      Return TypeCode.Object
   End Function   

   Public Function ToBoolean(provider As IFormatProvider) As Boolean Implements IConvertible.ToBoolean
      Throw New InvalidCastException(String.Format("Temperature-to-Boolean conversion is not supported."))
   End Function

   Public Function ToByte(provider As IFormatProvider) As Byte Implements IConvertible.ToByte
      If temp < Byte.MinValue Or temp > Byte.MaxValue Then
         Throw New OverflowException(String.Format("{0} is out of range of the Byte data type.", temp))
      Else
         Return CByte(temp)
      End If    
   End Function

   Public Function ToChar(provider As IFormatProvider) As Char Implements IConvertible.ToChar
      Throw New InvalidCastException("Temperature-to-Char conversion is not supported.")
   End Function

   Public Function ToDateTime(provider As IFormatProvider) As DateTime Implements IConvertible.ToDateTime
      Throw New InvalidCastException("Temperature-to-DateTime conversion is not supported.")
   End Function

   Public Function ToDecimal(provider As IFormatProvider) As Decimal Implements IConvertible.ToDecimal
      Return temp
   End Function

   Public Function ToDouble(provider As IFormatProvider) As Double Implements IConvertible.ToDouble
      Return CDbl(temp)
   End Function

   Public Function ToInt16(provider As IFormatProvider) As Int16 Implements IConvertible.ToInt16
      If temp < Int16.MinValue Or temp > Int16.MaxValue Then
         Throw New OverflowException(String.Format("{0} is out of range of the Int16 data type.", temp))
      End If
      Return CShort(Math.Round(temp))
   End Function

   Public Function ToInt32(provider As IFormatProvider) As Int32 Implements IConvertible.ToInt32
      If temp < Int32.MinValue Or temp > Int32.MaxValue Then
         Throw New OverflowException(String.Format("{0} is out of range of the Int32 data type.", temp))
      End If
      Return CInt(Math.Round(temp))
   End Function

   Public Function ToInt64(provider As IFormatProvider) As Int64 Implements IConvertible.ToInt64
      If temp < Int64.MinValue Or temp > Int64.MaxValue Then
         Throw New OverflowException(String.Format("{0} is out of range of the Int64 data type.", temp))
      End If
      Return CLng(Math.Round(temp))
   End Function

   Public Function ToSByte(provider As IFormatProvider) As SByte Implements IConvertible.ToSByte
      If temp < SByte.MinValue Or temp > SByte.MaxValue Then
         Throw New OverflowException(String.Format("{0} is out of range of the SByte data type.", temp))
      Else
         Return CSByte(temp)
      End If    
   End Function

   Public Function ToSingle(provider As IFormatProvider) As Single Implements IConvertible.ToSingle
      Return CSng(temp)
   End Function

   Public Overridable Overloads Function ToString(provider As IFormatProvider) As String Implements IConvertible.ToString
      Return temp.ToString(provider) & " °C"
   End Function

   ' If conversionType is a implemented by another IConvertible method, call it.
   Public Overridable Function ToType(conversionType As Type, provider As IFormatProvider) As Object Implements IConvertible.ToType
      Select Case Type.GetTypeCode(conversionType)
         Case TypeCode.Boolean
            Return Me.ToBoolean(provider)
         Case TypeCode.Byte
            Return Me.ToByte(provider)
         Case TypeCode.Char
            Return Me.ToChar(provider)   
         Case TypeCode.DateTime
            Return Me.ToDateTime(provider)
         Case TypeCode.Decimal
            Return Me.ToDecimal(provider)
         Case TypeCode.Double
            Return Me.ToDouble(provider)
         Case TypeCode.Empty
            Throw New NullReferenceException("The target type is null.")
         Case TypeCode.Int16
            Return Me.ToInt16(provider)
         Case TypeCode.Int32
            Return Me.ToInt32(provider)
         Case TypeCode.Int64
            Return Me.ToInt64(provider)
         Case TypeCode.Object
            ' Leave conversion of non-base types to derived classes.
            Throw New InvalidCastException(String.Format("Cannot convert from Temperature to {0}.", _
                                           conversionType.Name))
         Case TypeCode.SByte
            Return Me.ToSByte(provider)
         Case TypeCode.Single
            Return Me.ToSingle(provider)
         Case TypeCode.String
            Return Me.ToString(provider)
         Case TypeCode.UInt16
            Return Me.ToUInt16(provider)
         Case TypeCode.UInt32
            Return Me.ToUInt32(provider)
         Case TypeCode.UInt64
            Return Me.ToUInt64(provider)
         Case Else
            Throw New InvalidCastException("Conversion not supported.")   
      End Select
   End Function

   Public Function ToUInt16(provider As IFormatProvider) As UInt16 Implements IConvertible.ToUInt16
      If temp < UInt16.MinValue Or temp > UInt16.MaxValue Then
         Throw New OverflowException(String.Format("{0} is out of range of the UInt16 data type.", temp))
      End If
      Return CUShort(Math.Round(temp))
   End Function

   Public Function ToUInt32(provider As IFormatProvider) As UInt32 Implements IConvertible.ToUInt32
      If temp < UInt32.MinValue Or temp > UInt32.MaxValue Then
         Throw New OverflowException(String.Format("{0} is out of range of the UInt32 data type.", temp))
      End If
      Return CUInt(Math.Round(temp))
   End Function

   Public Function ToUInt64(provider As IFormatProvider) As UInt64 Implements IConvertible.ToUInt64
      If temp < UInt64.MinValue Or temp > UInt64.MaxValue Then
         Throw New OverflowException(String.Format("{0} is out of range of the UInt64 data type.", temp))
      End If
      Return CULng(Math.Round(temp))
   End Function
End Class

Public Class TemperatureCelsius : Inherits Temperature : Implements IConvertible
   Public Sub New(value As Decimal)
      MyBase.New(value)
   End Sub

   ' Override ToString methods.
   Public Overrides Function ToString() As String
      Return Me.ToString(Nothing)
   End Function

   Public Overrides Function ToString(provider As IFormatProvider ) As String
      Return temp.ToString(provider) + "°C" 
   End Function

  ' If conversionType is a implemented by another IConvertible method, call it.
   Public Overrides Function ToType(conversionType As Type, provider As IFormatProvider) As Object
      ' For non-objects, call base method.
      If Type.GetTypeCode(conversionType) <> TypeCode.Object Then
         Return MyBase.ToType(conversionType, provider)
      Else   
         If conversionType.Equals(GetType(TemperatureCelsius)) Then
            Return Me
         ElseIf conversionType.Equals(GetType(TemperatureFahrenheit))
            Return New TemperatureFahrenheit(CDec(Me.temp * 9 / 5 + 32))
         ' Unspecified object type: throw an InvalidCastException.
         Else
            Throw New InvalidCastException(String.Format("Cannot convert from Temperature to {0}.", _ 
                                           conversionType.Name))
         End If
      End If                                  
   End Function
End Class

Public Class TemperatureFahrenheit : Inherits Temperature : Implements IConvertible
   Public Sub New(value As Decimal)
      MyBase.New(value)
   End Sub

   ' Override ToString methods.
   Public Overrides Function ToString() As String
      Return Me.ToString(Nothing)
   End Function

   Public Overrides Function ToString(provider As IFormatProvider ) As String
      Return temp.ToString(provider) + "°F" 
   End Function

   Public Overrides Function ToType(conversionType As Type, provider As IFormatProvider) As Object
      ' For non-objects, call base methood.
      If Type.GetTypeCode(conversionType) <> TypeCode.Object Then
         Return MyBase.ToType(conversionType, provider)
      Else   
         ' Handle conversion between derived classes.
         If conversionType.Equals(GetType(TemperatureFahrenheit)) Then 
            Return Me
         ElseIf conversionType.Equals(GetType(TemperatureCelsius))
            Return New TemperatureCelsius(CDec((MyBase.temp - 32) * 5 / 9))
         ' Unspecified object type: throw an InvalidCastException.
         Else
            Throw New InvalidCastException(String.Format("Cannot convert from Temperature to {0}.", _
                                           conversionType.Name))
         End If
      End If                                  
   End Function
End Class
```

다음 예제에서는 이러한 [IConvertible](xref:System.IConvertible) 구현을 몇 번 호출하여 `TemperatureCelsius` 개체와 `TemperatureFahrenheit` 개체 간에 변환하는 방법을 보여 줍니다.

```csharp
TemperatureCelsius tempC1 = new TemperatureCelsius(0);
TemperatureFahrenheit tempF1 = (TemperatureFahrenheit) Convert.ChangeType(tempC1, typeof(TemperatureFahrenheit), null);
Console.WriteLine("{0} equals {1}.", tempC1, tempF1);
TemperatureCelsius tempC2 = (TemperatureCelsius) Convert.ChangeType(tempC1, typeof(TemperatureCelsius), null);
Console.WriteLine("{0} equals {1}.", tempC1, tempC2);
TemperatureFahrenheit tempF2 = new TemperatureFahrenheit(212);
TemperatureCelsius tempC3 = (TemperatureCelsius) Convert.ChangeType(tempF2, typeof(TemperatureCelsius), null);
Console.WriteLine("{0} equals {1}.", tempF2, tempC3);
TemperatureFahrenheit tempF3 = (TemperatureFahrenheit) Convert.ChangeType(tempF2, typeof(TemperatureFahrenheit), null);
Console.WriteLine("{0} equals {1}.", tempF2, tempF3);
// The example displays the following output:
//       0°C equals 32°F.
//       0°C equals 0°C.
//       212°F equals 100°C.
//       212°F equals 212°F.
```

```vb
Dim tempC1 As New TemperatureCelsius(0)
Dim tempF1 As TemperatureFahrenheit = CType(Convert.ChangeType(tempC1, GetType(TemperatureFahrenheit), Nothing), TemperatureFahrenheit)
Console.WriteLine("{0} equals {1}.", tempC1, tempF1) 
Dim tempC2 As TemperatureCelsius = CType(Convert.ChangeType(tempC1, GetType(TemperatureCelsius), Nothing), TemperatureCelsius)
Console.WriteLine("{0} equals {1}.", tempC1, tempC2) 
Dim tempF2 As New TemperatureFahrenheit(212)
Dim tempC3 As TEmperatureCelsius = CType(Convert.ChangeType(tempF2, GEtType(TemperatureCelsius), Nothing), TemperatureCelsius)
Console.WriteLine("{0} equals {1}.", tempF2, tempC3) 
Dim tempF3 As TemperatureFahrenheit = CType(Convert.ChangeType(tempF2, GetType(TemperatureFahrenheit), Nothing), TemperatureFahrenheit)
Console.WriteLine("{0} equals {1}.", tempF2, tempF3)
' The example displays the following output:
'       0°C equals 32°F.
'       0°C equals 0°C.
'       212°F equals 100°C.
'       212°F equals 212°F.
```

## <a name="the-typeconverter-class"></a>TypeConverter 클래스

.NET에서는 [System.ComponentModel.TypeConverter](xref:System.ComponentModel.TypeConverter) 클래스를 확장하고 [System.ComponentModel.TypeConverterAttribute](xref:System.ComponentModel.TypeConverterAttribute) 특성을 통해 형식 변환기를 해당 형식과 연결하여 사용자 지정 형식에 대한 형식 변환기를 정의할 수 있습니다. 다음 테이블에는 이 방법과 사용자 지정 형식에 대한 [IConvertible](xref:System.IConvertible) 인터페이스를 구현하는 방법의 차이점이 나와 있습니다.

> [!NOTE]
> 사용자 지정 형식에 대해 형식 변환기가 정의되어 있는 경우에만 사용자 지정 형식에 디자인 타임 지원이 제공될 수 있습니다.

TypeConverter를 사용한 변환 | IConvertible를 사용한 변환
------------------------------ | -----------------------------
[TypeConverter](xref:System.ComponentModel.TypeConverter)에서 별도의 클래스를 파생시켜 사용자 지정 형식에 대해 구현됩니다. 이 파생 클래스는 [TypeConverterAttribute](xref:System.ComponentModel.TypeConverterAttribute) 특성을 적용하여 사용자 지정 형식과 연결됩니다. | 변환을 수행하기 위해 사용자 지정 형식에 의해 구현됩니다. 형식의 사용자가 해당 형식에 대해 [IConvertible](xref:System.IConvertible) 변환 메서드를 호출합니다.
디자인 타임 및 런타임 모두에서 사용할 수 있습니다. | 런타임에서만 사용할 수 있습니다.
리플렉션을 사용합니다. 따라서 [IConvertible](xref:System.IConvertible)로 활성화된 변환보다 느립니다. | 리플렉션을 사용하지 않습니다.
사용자 지정 형식에서 다른 데이터 형식으로, 다른 데이터 형식에서 사용자 지정 형식으로의 양방향 형식 변환을 허용합니다. 예를 들어 `MyType`에 대해 정의된 [TypeConverter](xref:System.ComponentModel.TypeConverter)를 사용하면 `MyType`를 [String](xref:System.String)으로 변환하고 [String](xref:System.String)을 `MyType`로 변환할 수 있습니다. | 사용자 지정 형식에서 다른 데이터 형식으로의 변환은 허용하지만, 다른 데이터 형식에서 사용자 지정 형식으로의 변환은 허용하지 않습니다.

변환기를 사용하여 변환을 수행하는 방법에 대한 자세한 내용은 [System.ComponentModel.TypeConverter](xref:System.ComponentModel.TypeConverter)를 참조하세요.

## <a name="see-also"></a>참고 항목

[System.Convert](xref:System.Convert)

[IConvertible](xref:System.IConvertible)

[형식 변환표](conversion-tables.md)


<!--HONumber=Nov16_HO3-->


