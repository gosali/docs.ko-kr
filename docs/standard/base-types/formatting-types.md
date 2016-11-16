---
title: "서식 지정 형식"
description: "서식 지정 형식"
keywords: .NET, .NET Core
author: stevehoag
ms.author: shoag
manager: wpickett
ms.date: 07/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: cf497639-9f91-45cb-836f-998d1cea2f43
translationtype: Human Translation
ms.sourcegitcommit: b20713600d7c3ddc31be5885733a1e8910ede8c6
ms.openlocfilehash: 6c6ddfdbe288fe012adf31fd4d45af1b697d1132

---

# <a name="formatting-types"></a>서식 지정 형식

형식 지정은 대개 결과 문자열을 사용자에게 표시하거나 deserialize하여 원본 데이터 형식으로 복원하기 위해 클래스, 구조체 또는 열거형 값의 인스턴스를 해당 문자열 표현으로 변환하는 프로세스입니다. 이 변환 프로세스에는 다음과 같은 여러 가지 문제점이 나타날 수 있습니다.

* 내부적으로 값을 저장하는 방식에 사용자가 원하는 표시 방식이 반영되지 않을 수 있습니다. 예를 들어 전화 번호가 사용자에게 친숙하지 않은 **8009999999** 형식으로 저장될 수 있습니다. 이 전화 번호는 **800-999-9999**로 표시되어야 합니다. 이러한 방식으로 숫자의 서식을 지정하는 예제는 [사용자 지정 서식 문자열](#custom-format-strings) 섹션을 참조하세요. 

* 개체를 문자열 표현으로 변환하는 과정이 명확하지 않을 수 있습니다. 예를 들어 **Temperature** 또는 **Person** 개체의 문자열 표현이 어떻게 표시될지 명확하지 않습니다. 다양한 방식으로 **Temperature** 개체의 서식을 지정하는 예제는 [표준 서식 문자열](#standard-format-strings) 섹션을 참조하세요.

* 값에 문화권별 형식 지정이 필요할 수 있습니다. 예를 들어, 숫자를 사용하여 통화 값을 나타내는 응용 프로그램에서는 숫자 문자열에 현재 문화권의 통화 기호, 그룹 구분 기호(대부분의 경우 1000 단위 구분 기호임) 및 소수점 기호가 포함되어야 합니다. 예제는 [서식 공급자 및 IFormatProvider 인터페이스를 사용하여 문화권 구분 서식 지정](#culture-sensitive-formatting-with-format-providers-and-the-iformatprovider-interface) 섹션을 참조하세요. 

* 응용 프로그램에서 같은 값을 여러 가지 방법으로 표시해야 하는 경우도 있을 수 있습니다. 예를 들어, 응용 프로그램에서 해당 이름의 문자열 표현을 표시하거나 해당 내부 값을 표시하여 열거형 멤버를 나타낼 수 있습니다. 다양한 방식으로 [DayOfWeek](xref:System.DayOfWeek) 열거형 멤버의 형식을 지정하는 예제는 [표준 서식 문자열](#standard-format-strings) 섹션을 참조하세요.

.NET에서는 개발자가 이러한 요구 사항을 처리할 수 있는 다양한 형식 지정을 지원합니다. 

> [!NOTE]
> 형식 지정은 형식의 값을 문자열 표현으로 변환합니다. 구문 분석은 형식 지정과 반대 과정으로 진행됩니다. 구문 분석 작업은 해당 문자열 표현에서 데이터 형식의 인스턴스를 만듭니다. 문자열을 다른 데이터 형식으로 변환하는 방법에 대한 자세한 내용은 [문자열 구문 분석](parsing-strings.md)을 참조하세요.

이 개요는 다음과 같은 단원으로 구성됩니다.

* [.NET의 서식 지정](#formatting-in-net)

* [ToString 메서드를 사용한 기본 서식 지정](#default-formatting-using-the-tostring-method)

* [ToString 메서드 재정의](#overriding-the-tostring-method)

* [ToString 메서드 및 서식 문자열](#the-tostring-method-and-format-strings)

    * [표준 서식 문자열](#standard-format-strings)
    
    * [사용자 지정 서식 문자열](#custom-format-strings)
    
    * [서식 문자열 및 .NET 형식](#format-strings-and-net-types)
    
* [서식 공급자 및 IFormatProvider 인터페이스를 사용하여 문화권 구분 서식 지정](#culture-sensitive-formatting-with-format-providers-and-the-iformatprovider-interface)

    * [숫자 값의 문화권 구분 서식 지정](#culture-sensitive-formatting-of-numeric-values)
    
    * [날짜 및 시간 값의 문화권 구분 서식 지정](#culture-sensitive-formatting-of-date-and-time-values)
    
* [IFormattable 인터페이스](#the-iformattable-interface)

* [복합 서식 지정](#composite-formatting)

* [ICustomFormatter를 사용한 사용자 지정 서식 지정](#custom-formatting-with-icustomformatter)

* [관련 항목](#related-topics)

* [참조](#reference)

## <a name="formatting-in-net"></a>.NET의 형식 지정

서식 지정의 기본 메커니즘은 [Object.ToString](xref:System.Object.ToString) 메서드의 기본 구현으로, 이 항목의 뒷부분에 있는 [ToString 메서드를 사용한 기본 서식 지정](#default-formatting-using-the-tostring-method) 섹션에서 설명합니다. 그러나 .NET에서는 기본 형식 지정 지원을 수정하고 확장할 수 있는 여러 가지 방법을 제공합니다. 이러한 요구 사항은 다음과 같습니다.

* [Object.ToString](xref:System.Object.ToString) 메서드를 재정의하여 개체 값의 사용자 지정 문자열 표현을 정의합니다. 자세한 내용은 이 항목의 뒷부분에 있는 [ToString 메서드 재정의](#overriding-the-tostring-method) 섹션을 참조하세요.

* 개체의 값에 대한 문자열 표현에서 여러 형식을 사용할 수 있도록 형식 지정자를 정의합니다. 예를 들어, 다음 문의 "X" 형식 지정자는 정수를 16진수 값의 문자열 표현으로 변환합니다.

  ```csharp
  int integerValue = 60312;
  Console.WriteLine(integerValue.ToString("X"));   // Displays EB98.
  ```

  ```vb
  Dim integerValue As Integer = 60312
  Console.WriteLine(integerValue.ToString("X"))   ' Displays EB98.
  ```
  
  서식 지정자에 대한 자세한 내용은 [ToString 메서드 및 서식 문자열](#the-tostring-method-and-format-strings) 섹션을 참조하세요.
  
* 형식 공급자를 사용하여 특정 문화권의 형식 지정 규칙을 사용합니다. 예를 들어, 다음 문은 en-US 문화권의 형식 지정 규칙을 사용하여 통화 값을 표시합니다. 

  ```csharp
  double cost = 1632.54; 
  Console.WriteLine(cost.ToString("C", 
                  new System.Globalization.CultureInfo("en-US")));   
  // The example displays the following output:
  //       $1,632.54
  ```

  ```vb
  Dim cost As Double = 1632.54
  Console.WriteLine(cost.ToString("C", New System.Globalization.CultureInfo("en-US")))
  ' The example displays the following output:
  '       $1,632.54
  ```
  
  서식 공급자를 사용하여 서식을 지정하는 방법에 대한 자세한 내용은 [서식 공급자 및 IFormatProvider 인터페이스를 사용하여 문화권 구분 서식 지정](#culture-sensitive-formatting-with-format-providers-and-the-iformatprovider-interface) 섹션을 참조하세요.  
  
* [IFormattable](xref:System.IFormattable) 인터페이스를 구현하여 [Convert](xref:System.Convert) 클래스를 사용한 문자열 변환과 복합 형식 지정을 둘 다 지원합니다. 자세한 내용은 [IFormattable 인터페이스](#the-iformattable-interface) 섹션을 참조하세요.

* 복합 형식 지정을 사용하여 값의 문자열 표현을 더 큰 문자열에 포함합니다. 자세한 내용은 [복합 서식 지정](#composite-formatting) 섹션을 참조하세요.

* [ICustomFormatter](xref:System.ICustomFormatter) 및 [IFormatProvider](xref:System.IFormatProvider)를 구현하여 완벽한 사용자 지정 형식 지정 솔루션을 제공합니다. 자세한 내용은 [ICustomFormatter를 사용한 사용자 지정 서식 지정](#custom-formatting-with-icustomformatter) 섹션을 참조하세요.

다음 단원에서는 개체를 문자열 표현으로 변환하는 데 대해 이러한 메서드를 검토합니다.

## <a name="default-formatting-using-the-tostring-method"></a>ToString 메서드를 사용한 기본 서식 지정

[System.Object](xref:System.Object)에서 파생된 모든 형식은 기본적으로 형식의 이름을 반환하는, 매개 변수가 없는 [ToString](xref:System.Object.ToString) 메서드를 자동으로 상속받습니다. 다음 예제에서는 기본 [ToString](xref:System.Object.ToString) 메서드를 보여 줍니다. 이 예제에서는 구현이 없는 `Automobile`이라는 클래스를 정의합니다. 클래스를 인스턴스화하고 [ToString](xref:System.Object.ToString) 메서드를 호출하면 해당 형식 이름이 표시됩니다. 예제에서는 [ToString](xref:System.Object.ToString) 메서드를 명시적으로 호출하지 않습니다. [Console.WriteLine(Object)](xref:System.Console.WriteLine(System.Object)) 메서드는 인수로 전달된 개체의 [ToString](xref:System.Object.ToString) 메서드를 암시적으로 호출합니다. 

```csharp
using System;

public class Automobile
{
   // No implementation. All members are inherited from Object.
}

public class Example
{
   public static void Main()
   {
      Automobile firstAuto = new Automobile();
      Console.WriteLine(firstAuto);
   }
}
// The example displays the following output:
//       Automobile
```

```vb 
Public Class Automobile
   ' No implementation. All members are inherited from Object.
End Class

Module Example
   Public Sub Main()
      Dim firstAuto As New Automobile()
      Console.WriteLine(firstAuto)
   End Sub
End Module
' The example displays the following output:
'       Automobile
```

인터페이스를 제외한 모든 형식이 [Object](xref:System.Object)에서 파생되기 때문에 이 함수는 사용자 지정 클래스 또는 구조체에 자동으로 제공됩니다. 그러나 기본 [ToString](xref:System.Object.ToString) 메서드에서 제공하는 기능은 제한적이므로 형식을 식별하더라도 해당 형식의 인스턴스에 대한 정보를 제공하지 못합니다. 이 개체에 대한 정보를 제공하는 개체의 문자열 표현을 제공하려면 [ToString](xref:System.Object.ToString) 메서드를 재정의해야 합니다.

> [!NOTE]
> 구조체는 [ValueType](xref:System.ValueType)에서 상속받고, 이 형식은 다시 [Object](xref:System.Object)에서 파생됩니다. [ValueType](xref:System.ValueType)이 [Object.ToString](xref:System.Object.ToString)을 재정의해도 해당 구현은 동일합니다.

## <a name="overriding-the-tostring-method"></a>ToString 메서드 재정의

형식 이름을 표시할 수 있는 경우가 종종 제한되어 형식의 사용자가 서로 다른 인스턴스를 구분할 수 없는 경우가 있습니다. 그러나 [ToString](xref:System.Object.ToString) 메서드를 재정의하여 개체 값을 더 유용하게 표현할 수 있습니다. 다음 예제에서는 `Temperature` 개체를 정의하고 해당 [ToString](xref:System.Object.ToString) 메서드를 재정의하여 온도를 섭씨 단위로 표시합니다.

```csharp
using System;

public class Temperature
{
   private decimal temp;

   public Temperature(decimal temperature)
   {
      this.temp = temperature;   
   }

   public override string ToString()
   {
      return this.temp.ToString("N1") + "°C";
   }
}

public class Example
{
   public static void Main()
   {
      Temperature currentTemperature = new Temperature(23.6m);
      Console.WriteLine("The current temperature is " +
                        currentTemperature.ToString());
   }
}
// The example displays the following output:
//       The current temperature is 23.6°C.
```

```vb
Public Class Temperature
   Private temp As Decimal

   Public Sub New(temperature As Decimal)
      Me.temp = temperature
   End Sub

   Public Overrides Function ToString() As String
      Return Me.temp.ToString("N1") + "°C"   
   End Function
End Class

Module Example
   Public Sub Main()
      Dim currentTemperature As New Temperature(23.6d)
      Console.WriteLine("The current temperature is " +
                        currentTemperature.ToString())
   End Sub
End Module
' The example displays the following output:
'       The current temperature is 23.6°C.
```

.NET에서 각 기본 값 형식의 [ToString](xref:System.Object.ToString) 메서드는 개체의 이름 대신 개체의 값을 표시하도록 재정의되었습니다. 다음 표에는 각 기본 형식의 재정의가 나와 있습니다. 재정의된 메서드의 대부분은 [ToString](xref:System.Object.ToString) 메서드의 다른 오버로드를 호출하고 해당 형식의 일반적인 서식을 정의하는 "G" 형식 지정자와 현재 문화권을 나타내는 [IFormatProvider](xref:System.IFormatProvider) 개체를 전달합니다.

형식 | ToString 재정의
---- | -----------------
[Boolean](xref:System.Boolean) | [Boolean.TrueString](xref:System.Boolean.TrueString) 또는 [Boolean.FalseString](xref:System.Boolean.FalseString)을 반환합니다.
[Byte](xref:System.Byte) | `Byte.ToString("G", NumberFormatInfo.CurrentInfo)`을 호출하여 현재 문화권에 대한 [Byte](xref:System.Byte) 값의 형식을 지정합니다.
[Char](xref:System.Char) | 문자를 문자열로 반환합니다.
[DateTime](xref:System.DateTime) | `DateTime.ToString("G", DatetimeFormatInfo.CurrentInfo)`을 호출하여 현재 문화권에 대한 날짜 및 시간 값의 형식을 지정합니다. 
[Decimal](xref:System.Decimal) | `Decimal.ToString("G", NumberFormatInfo.CurrentInfo)`을 호출하여 현재 문화권에 대한 [Decimal](xref:System.Decimal) 값의 형식을 지정합니다.
[Double](xref:System.Double) | `Double.ToString("G", NumberFormatInfo.CurrentInfo)`을 호출하여 현재 문화권에 대한 [Double](xref:System.Double) 값의 형식을 지정합니다.
[Int16](xref:System.Int16) | `Int16.ToString("G", NumberFormatInfo.CurrentInfo)`을 호출하여 현재 문화권에 대한 [Int16](xref:System.Int16) 값의 형식을 지정합니다.
[Int32](xref:System.Int32) | `Int16.ToString("G", NumberFormatInfo.CurrentInfo)`을 호출하여 현재 문화권에 대한 [Int32](xref:System.Int32) 값의 형식을 지정합니다.
[Int64](xref:System.Int64) | `Int16.ToString("G", NumberFormatInfo.CurrentInfo)`을 호출하여 현재 문화권에 대한 [Int64](xref:System.Int64) 값의 형식을 지정합니다.
[SByte](xref:System.SByte) | `Int16.ToString("G", NumberFormatInfo.CurrentInfo)`을 호출하여 현재 문화권에 대한 [SByte](xref:System.SByte) | 값의 형식을 지정합니다.
[Single](xref:System.Single) | `Int16.ToString("G", NumberFormatInfo.CurrentInfo)`을 호출하여 현재 문화권에 대한 [Single](xref:System.Single) 값의 형식을 지정합니다.
[UInt32](xref:System.UInt32) | `Int16.ToString("G", NumberFormatInfo.CurrentInfo)`을 호출하여 현재 문화권에 대한 [UInt32](xref:System.UInt32) 값의 형식을 지정합니다.
[UInt32](xref:System.UInt32) | `Int16.ToString("G", NumberFormatInfo.CurrentInfo)`을 호출하여 현재 문화권에 대한 [UInt32](xref:System.UInt32) 값의 형식을 지정합니다.
[UInt64](xref:System.UInt64) | `Int16.ToString("G", NumberFormatInfo.CurrentInfo)`을 호출하여 현재 문화권에 대한 [UInt64](xref:System.UInt64) 값의 형식을 지정합니다.

## <a name="the-tostring-method-and-format-strings"></a>ToString 메서드 및 서식 문자열

개체에 문자열 표현이 하나만 있는 경우에는 기본 [ToString](xref:System.Object.ToString) 메서드를 사용하거나 [ToString](xref:System.Object.ToString)을 재정의합니다. 하지만 개체의 값에 여러 표현이 있는 경우가 자주 있습니다. 예를 들어 온도는 화씨, 섭씨 또는 캘빈으로 표시할 수 있습니다. 마찬가지로 정수 값 10도 10, 10.0, 1.0e01, $10.00 등과 같은 여러 가지 방식으로 표현될 수 있습니다.

하나의 값에 여러 문자열 표현을 허용하기 위해 .NET에서는 형식 문자열을 사용합니다. 형식 문자열은 하나 이상의 미리 정의된 형식 지정자가 들어 있는 문자열이며, 이러한 형식 지정자는 [ToString](xref:System.Object.ToString) 메서드가 해당 출력의 형식을 지정하는 방식을 정의한 단일 문자 또는 문자 그룹입니다. 형식 문자열은 개체의 [ToString](xref:System.Object.ToString) 메서드에 매개 변수로 전달되어 개체의 값에 대한 문자열 표현의 표시 방법을 결정합니다.

.NET의 모든 숫자 형식, 날짜/시간 형식 및 열거형 형식은 미리 정의된 형식 지정자 집합을 지원합니다. 형식 문자열을 사용하여 응용 프로그램 정의 데이터 형식의 여러 문자열 표현도 정의할 수 있습니다.

### <a name="standard-format-strings"></a>표준 서식 문자열

표준 형식 문자열에는 개체의 문자열 표현을 정의하는 영문자인 단일 형식 지정자와 함께 결과 문자열을 표시하는 데 사용할 자릿수에 영향을 주는 선택적 전체 자릿수 지정자가 포함됩니다. 전체 자릿수 지정자가 생략되었거나 지원되지 않으면 표준 형식 지정자는 표준 형식 문자열과 같습니다. 

.NET에서는 모든 숫자 형식, 날짜/시간 형식 및 열거형 형식에 대한 표준 형식 지정자 집합을 정의합니다. 예를 들어, 이러한 각 범주는 해당 형식 값에 대한 일반적인 문자열 표현을 정의하는 "G" 표준 형식 지정자를 지원합니다.

열거형 형식의 표준 형식 문자열은 값의 문자열 표현을 직접 제어합니다. 열거형 값의 [ToString](xref:System.Object.ToString) 메서드에 전달된 형식 문자열은 값이 문자열 이름("G" 및 "F" 형식 지정자), 내부 정수 값("D" 형식 지정자) 또는 16진수 값("X" 형식 지정자)을 사용하여 표시되는지 여부를 결정합니다. 다음 예제에서는 표준 형식 문자열을 사용하여 [DayOfWeek](xref:System.DayOfWeek) 열거형 값의 형식을 지정하는 방법을 보여 줍니다. 

```csharp
DayOfWeek thisDay = DayOfWeek.Monday;
string[] formatStrings = {"G", "F", "D", "X"};

foreach (string formatString in formatStrings)
   Console.WriteLine(thisDay.ToString(formatString));
// The example displays the following output:
//       Monday
//       Monday
//       1
//       00000001
```

```vb
Dim thisDay As DayOfWeek = DayOfWeek.Monday
Dim formatStrings() As String = {"G", "F", "D", "X"}

For Each formatString As String In formatStrings
   Console.WriteLine(thisDay.ToString(formatString))
Next
' The example displays the following output:
'       Monday
'       Monday
'       1
'       00000001
```

열거형 서식 문자열에 대한 자세한 내용은 [열거형 서식 문자열](enumeration-format.md)을 참조하세요.

일반적으로 숫자 형식의 표준 형식 문자열은 정확한 모양이 하나 이상의 속성 값에 의해 제어되는 결과 문자열을 정의합니다. 예를 들어, "C" 형식 지정자는 숫자의 형식을 통화 값으로 지정합니다. "C" 형식 지정자를 유일한 매개 변수로 사용하여 [ToString](xref:System.Object.ToString) 메서드를 호출하면 현재 문화권의 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 개체에 있는 다음 속성 값이 숫자 값의 문자열 표현을 정의하는 데 사용됩니다.

* 현재 문화권의 통화 기호를 지정하는 [CurrencySymbol](xref:System.Globalization.NumberFormatInfo.CurrencySymbol) 속성

* 다음과 같은 사항을 결정하는 정수를 반환하는 [CurrencyNegativePattern](xref:System.Globalization.NumberFormatInfo.CurrencyNegativePattern) 또는 [CurrencyPositivePattern](xref:System.Globalization.NumberFormatInfo.CurrencyPositivePattern) 속성 

    * 통화 기호의 위치
    
    * 음수 값이 선행 음수 기호, 후행 음수 기호 또는 괄호로 표시되는지 여부
    
    * 숫자 값과 통화 기호 사이에 공백이 표시되는지 여부
    
* 결과 문자열의 소수 자릿수를 정의하는 [CurrencyDecimalDigits](xref:System.Globalization.NumberFormatInfo.CurrencyDecimalDigits) 속성

* 결과 문자열의 소수 구분 기호를 정의하는 [CurrencyDecimalSeparator](xref:System.Globalization.NumberFormatInfo.CurrencyDecimalSeparator) 속성

* 그룹 구분 기호를 정의하는 [CurrencyGroupSeparator](xref:System.Globalization.NumberFormatInfo.CurrencyGroupSeparator) 속성

* 정수 부분의 각 그룹 자릿수를 정의하는 [CurrencyGroupSizes](xref:System.Globalization.NumberFormatInfo.CurrencyGroupSizes) 속성

* 음수 값을 나타내는 데 괄호가 사용되지 않는 경우 결과 문자열에 사용되는 음수 기호를 결정하는 [NegativeSign](xref:System.Globalization.NumberFormatInfo.NegativeSign) 속성

이외에도 숫자 형식 문자열에는 전체 자릿수 지정자가 포함될 수도 있습니다. 이 지정자의 의미는 해당 지정자가 사용되는 형식 문자열에 따라 달라지지만 일반적으로 결과 문자열에 표시될 전체 자릿수나 소수 자릿수를 나타냅니다. 예를 들어, 다음 예제에서는 "X4" 표준 숫자 문자열과 전체 자릿수 지정자를 사용하여 네 자리의 16진수로 구성된 문자열 값을 만듭니다.

```csharp
byte[] byteValues = { 12, 163, 255 };
foreach (byte byteValue in byteValues)
   Console.WriteLine(byteValue.ToString("X4"));
// The example displays the following output:
//       000C
//       00A3
//       00FF
```

```vb
Dim byteValues() As Byte = { 12, 163, 255 }
For Each byteValue As Byte In byteValues
   Console.WriteLine(byteValue.ToString("X4"))
Next
' The example displays the following output:
'       000C
'       00A3
'       00FF
```

표준 숫자 서식 문자열에 대한 자세한 내용은 [표준 숫자 서식 문자열](standard-numeric.md)을 참조하세요. 

날짜 및 시간 값의 표준 형식 문자열은 특정 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 클래스에 저장된 사용자 지정 형식 문자열의 별칭입니다. 예를 들어, "D" 형식 지정자를 사용하여 날짜 및 시간 값의 [ToString](xref:System.Object.ToString) 메서드를 호출하면 현재 문화권의 [DateTimeFormatInfo.LongDatePattern](xref:System.Globalization.DateTimeFormatInfo.LongDatePattern) 속성에 저장된 사용자 지정 형식 문자열을 사용하여 날짜 및 시간이 표시됩니다. 사용자 지정 서식 문자열에 대한 자세한 내용은 [사용자 지정 서식 문자열](#custom-format-strings)을 참조하세요. 다음 예제에서는 이러한 관계를 보여 줍니다.

```csharp
using System;
using System.Globalization;

public class Example
{
   public static void Main()
   {
      DateTime date1 = new DateTime(2017, 6, 30);
      Console.WriteLine("D Format Specifier:     {0:D}", date1);
      string longPattern = CultureInfo.CurrentCulture.DateTimeFormat.LongDatePattern;
      Console.WriteLine("'{0}' custom format string:     {1}", 
                        longPattern, date1.ToString(longPattern));
   }
}
// The example displays the following output when run on a system whose
// current culture is en-US:
//    D Format Specifier:     Tuesday, June 30, 2017
//    'dddd, MMMM dd, yyyy' custom format string:     Tuesday, June 30, 2017
```

```vb
Imports System.Globalization

Module Example
   Public Sub Main()
      Dim date1 As Date = #6/30/2009#
      Console.WriteLine("D Format Specifier:     {0:D}", date1)
      Dim longPattern As String = CultureInfo.CurrentCulture.DateTimeFormat.LongDatePattern
      Console.WriteLine("'{0}' custom format string:     {1}", _
                        longPattern, date1.ToString(longPattern))
   End Sub
End Module
' The example displays the following output when run on a system whose
' current culture is en-US:
'    D Format Specifier:     Tuesday, June 30, 2009
'    'dddd, MMMM dd, yyyy' custom format string:     Tuesday, June 30, 2009
```

표준 날짜 및 시간 서식 문자열에 대한 자세한 내용은 [표준 날짜 및 시간 서식 문자열](standard-datetime.md)을 참조하세요.

표준 형식 문자열을 사용하여 개체의 `ToString(String)` 메서드에 의해 생성되는 응용 프로그램 정의 개체의 문자열 표현도 정의할 수 있습니다. 개체가 지원하는 특정 표준 형식 지정자를 정의하고 이러한 지정자가 대/소문자를 구분하는지 여부를 결정할 수 있습니다. `ToString(String)` 메서드의 구현에서는 다음을 지원해야 합니다.

* 개체의 사용자 지정 또는 일반 형식을 나타내는 "G" 형식 지정자. 개체의 `ToString` 메서드에 대한 매개 변수가 없는 오버로드는 해당 `ToString(String)` 오버로드를 호출하고 이를 "G" 표준 형식 문자열에 전달해야 합니다.

* null 참조와 동일한 형식 지정자에 대한 지원. null 참조와 동일한 형식 지정자는 "G" 형식 지정자와 같은 것으로 간주됩니다.

예를 들어 `Temperature` 클래스는 섭씨 온도를 내부적으로 저장하고 형식 지정자를 사용하여 `Temperature` 개체의 값을 섭씨, 화씨 및 캘빈으로 표시할 수 있습니다. 다음 예제에서 이에 대해 설명합니다.

```csharp
using System;

public class Temperature
{
   private decimal m_Temp;

   public Temperature(decimal temperature)
   {
      this.m_Temp = temperature;
   }

   public decimal Celsius
   {
      get { return this.m_Temp; }
   }

   public decimal Kelvin
   {
      get { return this.m_Temp + 273.15m; }   
   }

   public decimal Fahrenheit
   {
      get { return Math.Round(((decimal) (this.m_Temp * 9 / 5 + 32)), 2); }
   }

   public override string ToString()
   {
      return this.ToString("C");
   }

   public string ToString(string format)
   {  
      // Handle null or empty string.
      if (String.IsNullOrEmpty(format)) format = "C";
      // Remove spaces and convert to uppercase.
      format = format.Trim().ToUpperInvariant();      

      // Convert temperature to Fahrenheit and return string.
      switch (format)
      {
         // Convert temperature to Fahrenheit and return string.
         case "F":
            return this.Fahrenheit.ToString("N2") + " °F";
         // Convert temperature to Kelvin and return string.
         case "K":
            return this.Kelvin.ToString("N2") + " K";
         // return temperature in Celsius.
         case "G":
         case "C":
            return this.Celsius.ToString("N2") + " °C";
         default:
            throw new FormatException(String.Format("The '{0}' format string is not supported.", format));
      }      
   }
}

public class Example
{
   public static void Main()
   {
      Temperature temp1 = new Temperature(0m);
      Console.WriteLine(temp1.ToString());
      Console.WriteLine(temp1.ToString("G"));
      Console.WriteLine(temp1.ToString("C"));
      Console.WriteLine(temp1.ToString("F"));
      Console.WriteLine(temp1.ToString("K"));

      Temperature temp2 = new Temperature(-40m);
      Console.WriteLine(temp2.ToString());
      Console.WriteLine(temp2.ToString("G"));
      Console.WriteLine(temp2.ToString("C"));
      Console.WriteLine(temp2.ToString("F"));
      Console.WriteLine(temp2.ToString("K"));

      Temperature temp3 = new Temperature(16m);
      Console.WriteLine(temp3.ToString());
      Console.WriteLine(temp3.ToString("G"));
      Console.WriteLine(temp3.ToString("C"));
      Console.WriteLine(temp3.ToString("F"));
      Console.WriteLine(temp3.ToString("K"));

      Console.WriteLine(String.Format("The temperature is now {0:F}.", temp3));
   }
}
// The example displays the following output:
//       0.00 °C
//       0.00 °C
//       0.00 °C
//       32.00 °F
//       273.15 K
//       -40.00 °C
//       -40.00 °C
//       -40.00 °C
//       -40.00 °F
//       233.15 K
//       16.00 °C
//       16.00 °C
//       16.00 °C
//       60.80 °F
//       289.15 K
//       The temperature is now 16.00 °C.
```

```vb
Public Class Temperature
   Private m_Temp As Decimal

   Public Sub New(temperature As Decimal)
      Me.m_Temp = temperature
   End Sub

   Public ReadOnly Property Celsius() As Decimal
      Get
         Return Me.m_Temp
      End Get   
   End Property

   Public ReadOnly Property Kelvin() As Decimal
      Get
         Return Me.m_Temp + 273.15d   
      End Get
   End Property

   Public ReadOnly Property Fahrenheit() As Decimal
      Get
         Return Math.Round(CDec(Me.m_Temp * 9 / 5 + 32), 2)
      End Get      
   End Property

   Public Overrides Function ToString() As String
      Return Me.ToString("C")
   End Function

   Public Overloads Function ToString(format As String) As String  
      ' Handle null or empty string.
      If String.IsNullOrEmpty(format) Then format = "C"
      ' Remove spaces and convert to uppercase.
      format = format.Trim().ToUpperInvariant()      

      Select Case format
         Case "F"
           ' Convert temperature to Fahrenheit and return string.
            Return Me.Fahrenheit.ToString("N2") & " °F"
         Case "K"
            ' Convert temperature to Kelvin and return string.
            Return Me.Kelvin.ToString("N2") & " K"
         Case "C", "G"
            ' Return temperature in Celsius.
            Return Me.Celsius.ToString("N2") & " °C"
         Case Else
            Throw New FormatException(String.Format("The '{0}' format string is not supported.", format))
      End Select      
   End Function
End Class

Public Module Example
   Public Sub Main()
      Dim temp1 As New Temperature(0d)
      Console.WriteLine(temp1.ToString())
      Console.WriteLine(temp1.ToString("G"))
      Console.WriteLine(temp1.ToString("C"))
      Console.WriteLine(temp1.ToString("F"))
      Console.WriteLine(temp1.ToString("K"))

      Dim temp2 As New Temperature(-40d)
      Console.WriteLine(temp2.ToString())
      Console.WriteLine(temp2.ToString("G"))
      Console.WriteLine(temp2.ToString("C"))
      Console.WriteLine(temp2.ToString("F"))
      Console.WriteLine(temp2.ToString("K"))

      Dim temp3 As New Temperature(16d)
      Console.WriteLine(temp3.ToString())
      Console.WriteLine(temp3.ToString("G"))
      Console.WriteLine(temp3.ToString("C"))
      Console.WriteLine(temp3.ToString("F"))
      Console.WriteLine(temp3.ToString("K"))

      Console.WriteLine(String.Format("The temperature is now {0:F}.", temp3))
   End Sub
End Module
' The example displays the following output:
'       0.00 °C
'       0.00 °C
'       0.00 °C
'       32.00 °F
'       273.15 K
'       -40.00 °C
'       -40.00 °C
'       -40.00 °C
'       -40.00 °F
'       233.15 K
'       16.00 °C
'       16.00 °C
'       16.00 °C
'       60.80 °F
'       289.15 K
'       The temperature is now 16.00 °C.
```

### <a name="custom-format-strings"></a>사용자 지정 서식 문자열

표준 형식 문자열 외에도 .NET에서는 숫자 값과 날짜 및 시간 값에 대한 사용자 지정 형식 문자열을 정의합니다. 사용자 지정 형식 문자열은 값의 문자열 표현을 정의하는 하나 이상의 사용자 지정 형식 지정자로 구성됩니다. 예를 들어, en-US 문화권의 경우 사용자 지정 날짜 및 시간 형식 문자열 "yyyy/mm/dd hh:mm:ss t zzz"는 날짜를 "2008/11/15 07:45:00.0000 P -08:00" 형태의 문자열 표현으로 변환합니다. 마찬가지로 사용자 지정 형식 문자열 "0000"은 정수 값 12를 "0012"로 변환합니다. 사용자 지정 서식 문자열의 전체 목록은 [사용자 지정 날짜 및 시간 서식 문자열](custom-datetime.md) 및 [사용자 지정 숫자 서식 문자열](custom-numeric.md)을 참조하세요.

형식 문자열이 단일 사용자 지정 형식 지정자로 구성된 경우에는 표준 형식 지정자와 혼동되지 않도록 형식 지정자 앞에 백분율 기호(%)가 와야 합니다. 다음 예제에서는 "M" 사용자 지정 형식 지정자를 사용하여 특정 날짜의 월에 해당하는 한 자리 또는 두 자리 숫자를 표시합니다.

```csharp
DateTime date1 = new DateTime(2009, 9, 8);
Console.WriteLine(date1.ToString("%M"));       
// Displays 9
```

```vb
Dim date1 As Date = #09/08/2009#
Console.WriteLine(date1.ToString("%M"))      ' Displays 9
```

많은 표준 형식 문자열은 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체의 속성에 정의된 사용자 지정 형식 문자열의 별칭입니다. 또한 사용자 지정 형식 문자열을 사용하면 상당히 융통성 있게 숫자 값 또는 날짜 및 시간 값에 대한 응용 프로그램 정의 형식 지정 기능을 제공할 수 있습니다. 여러 사용자 지정 형식 지정자를 하나의 사용자 지정 형식 문자열로 결합하여 숫자 값과 날짜 및 시간 값에 대한 사용자 지정 결과 문자열을 정의할 수 있습니다. 다음 예제에서는 월 이름, 일, 연도 및 괄호로 묶은 요일을 차례로 표시하는 사용자 지정 형식 문자열을 정의합니다.

```csharp
string customFormat = "MMMM dd, yyyy (dddd)";
DateTime date1 = new DateTime(2009, 8, 28);
Console.WriteLine(date1.ToString(customFormat));   
// The example displays the following output if run on a system
// whose language is English:
//       August 28, 2009 (Friday) 
```

```vb
Dim customFormat As String = "MMMM dd, yyyy (dddd)"
Dim date1 As Date = #8/28/2009#
Console.WriteLine(date1.ToString(customFormat))   
' The example displays the following output if run on a system
' whose language is English:
'       August 28, 2009 (Friday) 
```

다음 예제에서는 [Int64](xref:System.Int64) 값을 7자리 표준 미국 전화 번호로 지역 번호와 함께 표시하는 사용자 지정 형식 문자열을 정의합니다. 

```csharp
using System;

public class Example
{
   public static void Main()
   {
      long number = 8009999999;
      string fmt = "000-000-0000";
      Console.WriteLine(number.ToString(fmt));
   }
}
// The example displays the following output:
//        800-999-9999
```

```vb
Module Example
   Public Sub Main()
      Dim number As Long = 8009999999
      Dim fmt As String = "000-000-0000"
      Console.WriteLine(number.ToString(fmt))
   End Sub
End Module
' The example displays the following output:

' The example displays the following output:
'       800-999-9999
```

일반적으로 표준 형식 문자열로 응용 프로그램 정의 형식의 형식 지정 요구를 대부분 처리할 수 있지만 사용자 지정 형식 지정자를 정의하여 형식의 형식을 지정할 수도 있습니다. 

### <a name="format-strings-and-net-types"></a>서식 문자열 및 .NET 형식

모든 숫자 형식(즉, [Byte](xref:System.Byte), [Decimal](xref:System.Decimal), [Double](xref:System.Double), [Int16](xref:System.Int16), [Int32](xref:System.Int32), [Int64](xref:System.Int64), [SByte](xref:System.SByte), [Single](xref:System.Single), [UInt16](xref:System.UInt16), [UInt32](xref:System.UInt32), [UInt64](xref:System.UInt64), [BigInteger](xref:System.Numerics.BigInteger) 형식), [DateTime](xref:System.DateTime), [DateTimeOffset](xref:System.DateTimeOffset), [TimeSpan](xref:System.TimeSpan), [Guid](xref:System.Guid) 및 모든 열거형 형식은 형식 문자열을 사용한 형식 지정을 지원합니다. 각 형식에서 지원되는 형식 문자열에 대한 자세한 내용은 다음 항목을 참조하세요. 

제목 | 정의
----- | ----------
[표준 숫자 서식 문자열](standard-numeric.md) | 숫자 값의 일반적으로 사용되는 문자열 표현을 만드는 표준 형식 문자열에 대해 설명합니다. 
[사용자 지정 숫자 서식 문자열](custom-numeric.md) | 숫자 값의 응용 프로그램별 형식을 만드는 사용자 지정 형식 문자열에 대해 설명합니다.
[표준 날짜 및 시간 서식 문자열](standard-datetime.md) | [DateTime](xref:System.DateTime) 값의 일반적으로 사용되는 문자열 표현을 만드는 표준 형식 문자열에 대해 설명합니다.
[사용자 지정 날짜 및 시간 서식 문자열](custom-datetime.md) | [DateTime](xref:System.DateTime) 값의 응용 프로그램별 형식을 만드는 사용자 지정 형식 문자열에 대해 설명합니다.
[표준 TimeSpan 서식 문자열](standard-timespan.md) | 시간 간격의 일반적으로 사용되는 문자열 표현을 만드는 표준 형식 문자열에 대해 설명합니다.
[사용자 지정 TimeSpan 서식 문자열](custom-timespan.md) | 시간 간격의 응용 프로그램별 형식을 만드는 사용자 지정 형식 문자열에 대해 설명합니다.
[열거형 서식 문자열](enumeration-format.md) | 열거형 값의 문자열 표현을 만드는 데 사용되는 표준 형식 문자열에 대해 설명합니다.
[Guid.ToString(String)](xref:System.Guid.ToString(System.String)) | [Guid](xref:System.Guid) 값의 표준 형식 문자열에 대해 설명합니다.

## <a name="culturesensitive-formatting-with-format-providers-and-the-iformatprovider-interface"></a>형식 공급자 및 IFormatProvider 인터페이스를 사용하여 문화권 구분 서식 지정

형식 지정자를 사용하여 개체의 형식 지정을 사용자 지정할 수 있기는 하지만 의미 있는 개체의 문자열 표현을 만들려면 추가 형식 지정 정보가 필요한 경우가 종종 있습니다. 예를 들어, C" 표준 형식 문자열이나 "$ #,#.00" 같은 사용자 지정 형식 문자열을 사용하여 숫자의 형식을 통화 값으로 지정하려면 최소한 올바른 통화 기호, 그룹 구분 기호 및 소수 구분 기호에 대한 정보를 형식 지정된 문자열에 포함할 수 있어야 합니다. .NET에서는 이러한 추가 형식 지정 정보를 [IFormatProvider](xref:System.IFormatProvider) 인터페이스를 통해 사용할 수 있습니다. 이러한 인터페이스는 숫자 형식과 날짜 및 시간 형식의 `ToString` 메서드에 대한 하나 이상의 오버로드에 매개 변수로 제공됩니다. [IFormatProvider](xref:System.IFormatProvider) 구현은 문화권별 형식 지정을 지원하기 위해 .NET에서 사용됩니다. 다음 예제에서는 서로 다른 문화권을 나타내는 세 [IFormatProvider](xref:System.IFormatProvider) 개체를 사용하여 형식을 지정할 때 개체의 문자열 표현이 어떻게 바뀌는지 보여 줍니다.

```csharp
using System;
using System.Globalization;

public class Example
{
   public static void Main()
   {
      decimal value = 1603.42m;
      Console.WriteLine(value.ToString("C3", new CultureInfo("en-US")));
      Console.WriteLine(value.ToString("C3", new CultureInfo("fr-FR")));
      Console.WriteLine(value.ToString("C3", new CultureInfo("de-DE")));
   }
}
// The example displays the following output:
//       $1,603.420
//       1 603,420 €
//       1.603,420 €
```

```vb
Imports System.Globalization

Public Module Example
   Public Sub Main()
      Dim value As Decimal = 1603.42d
      Console.WriteLine(value.ToString("C3", New CultureInfo("en-US")))
      Console.WriteLine(value.ToString("C3", New CultureInfo("fr-FR")))
      Console.WriteLine(value.ToString("C3", New CultureInfo("de-DE")))
   End Sub
End Module
' The example displays the following output:
'       $1,603.420
'       1 603,420 €
'       1.603,420 €
```

[IFormatProvider](xref:System.IFormatProvider) 인터페이스에는 [GetFormat(Type)](xref:System.IFormatProvider.GetFormat(System.Type)) 메서드가 하나 있으며, 이 메서드에는 형식 지정 정보를 제공하는 개체의 형식을 지정하는 매개 변수가 하나 있습니다. 이 메서드는 해당 형식의 개체를 제공할 수 있는 경우 해당 형식의 개체를 반환합니다. 제공할 수 없으면 null 참조를 반환합니다.

[IFormatProvider.GetFormat](xref:System.IFormatProvider.GetFormat(System.Type))은 콜백 메서드입니다. [IFormatProvider](xref:System.IFormatProvider) 매개 변수가 포함된 `ToString` 메서드 오버로드를 호출하면 해당 [IFormatProvider](xref:System.IFormatProvider) 개체의 [GetFormat](xref:System.IFormatProvider.GetFormat(System.Type)) 메서드가 호출됩니다. [GetFormat](xref:System.IFormatProvider.GetFormat(System.Type)) 메서드는 *formatType* 매개 변수에 지정된 대로 필요한 형식 지정 정보를 제공하는 개체를 `ToString` 메서드에 반환합니다. 

[IFormatProvider](xref:System.IFormatProvider) 형식의 매개 변수를 포함하는 형식 지정 또는 문자열 변환 메서드가 많이 있기는 하지만 대부분의 경우 메서드를 호출할 때 매개 변수의 값이 무시됩니다. 다음 표에서는 매개 변수를 사용하는 형식 지정 메서드와 이러한 메서드가 [IFormatProvider.GetFormat](xref:System.IFormatProvider.GetFormat(System.Type)) 메서드로 전달하는 [Type](xref:System.Type) 개체의 형식을 보여 줍니다. 

메서드 | *formatType* 매개 변수의 유형
------ | ------------------------------
숫자 형식의 `ToString` 메서드 | [System.Globalization.NumberFormatInfo](xref:System.Globalization.NumberFormatInfo)
날짜 및 시간 형식의 `ToString` 메서드 | [System.Globalization.DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo)
[String.Format](xref:System.String.Format(System.IFormatProvider,System.String,System.Object)) | [System.ICustomFormatter](xref:System.ICustomFormatter)
[StringBuilder.AppendFormat](xref:System.Text.StringBuilder.AppendFormat(System.IFormatProvider,System.String,System.Object)) | [System.ICustomFormatter](xref:System.ICustomFormatter)

.NET에는 [IFormatProvider](xref:System.IFormatProvider)를 구현하는 세 가지 클래스가 있습니다. 

* 특정 문화권의 날짜 및 시간 값에 대한 형식 지정 정보를 제공하는 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 클래스. 이 클래스에 대해 [IFormatProvider.GetFormat](xref:System.IFormatProvider.GetFormat(System.Type))을 구현하면 해당 클래스의 인스턴스가 반환됩니다.

* 특정 문화권의 숫자 형식 지정 정보를 제공하는 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 클래스. 이 클래스에 대해 [IFormatProvider.GetFormat](xref:System.IFormatProvider.GetFormat(System.Type))을 구현하면 해당 클래스의 인스턴스가 반환됩니다.

* [CultureInfo](xref:System.Globalization.CultureInfo). 이 클래스에 대해 [IFormatProvider.GetFormat](xref:System.IFormatProvider.GetFormat(System.Type))을 구현하면 숫자 형식 지정 정보를 제공하는 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 개체 또는 날짜 및 시간 값에 대한 형식 지정 정보를 제공하는 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체가 반환될 수 있습니다. 

사용자 고유의 형식 공급자를 구현하여 이러한 클래스 중 하나를 대체할 수도 있습니다. 그러나 `GetFormat` 메서드에 형식 지정 정보를 제공해야 하는 경우에는 구현된 형식 공급자의 `ToString` 메서드에서 위의 표에 나와 있는 형식의 개체를 반환해야 합니다.

### <a name="culturesensitive-formatting-of-numeric-values"></a>숫자 값의 문화권 구분 서식 지정

기본적으로 숫자 값의 형식은 문화권을 구분합니다. 형식 지정 메서드를 호출할 때 문화권을 지정하지 않으면 현재 스레드 문화권의 형식 규칙이 사용됩니다. 이는 현재 스레드 문화권을 4번 변경한 후 [Decimal.ToString(String)](xref:System.Decimal.ToString(System.String)) 메서드를 호출하는 다음 예제에 나와 있습니다. 각각의 경우 결과 문자열은 현재 문화권의 형식 규칙을 반영합니다. 이는 `ToString` 및 `ToString(String)` 메서드가 각 숫자 형식의 `ToString(String, IFormatProvider)` 메서드에 대한 호출을 래핑하기 때문입니다. 

```csharp
using System;
using System.Globalization;
using System.Threading;

public class Example
{
   public static void Main()
   {
      string[] cultureNames = { "en-US", "fr-FR", "es-MX", "de-DE" };
      Decimal value = 1043.17m;

      foreach (var cultureName in cultureNames) {
         // Change the current thread culture.
         Thread.CurrentThread.CurrentCulture = CultureInfo.CreateSpecificCulture(cultureName);
         Console.WriteLine("The current culture is {0}", 
                           Thread.CurrentThread.CurrentCulture.Name);
         Console.WriteLine(value.ToString("C2"));
         Console.WriteLine();
      }   
   }
}
// The example displays the following output:
//       The current culture is en-US
//       $1,043.17
//       
//       The current culture is fr-FR
//       1 043,17 €
//       
//       The current culture is es-MX
//       $1,043.17
//       
//       The current culture is de-DE
//       1.043,17 €
```

```vb
Imports System.Globalization
Imports System.Threading 

Module Example
   Public Sub Main()
      Dim cultureNames() As String = { "en-US", "fr-FR", "es-MX", "de-DE" }
      Dim value As Decimal = 1043.17d 

      For Each cultureName In cultureNames
         ' Change the current thread culture.
         Thread.CurrentThread.CurrentCulture = CultureInfo.CreateSpecificCulture(cultureName)
         Console.WriteLine("The current culture is {0}", 
                           Thread.CurrentThread.CurrentCulture.Name)
         Console.WriteLine(value.ToString("C2"))
         Console.WriteLine()
      Next                  
   End Sub
End Module
' The example displays the following output:
'       The current culture is en-US
'       $1,043.17
'       
'       The current culture is fr-FR
'       1 043,17 €
'       
'       The current culture is es-MX
'       $1,043.17
'       
'       The current culture is de-DE
'       1.043,17 €
```

*provider* 매개 변수를 포함하는 `ToString` 오버로드를 호출하고 여기에 다음 중 하나를 전달하여 특정 문화권에 대한 숫자 값의 형식을 지정할 수도 있습니다. 

* 사용할 형식 규칙이 포함된 문화권을 나타내는 [CultureInfo](xref:System.Globalization.CultureInfo) 개체. 해당 [CultureInfo.GetFormat](xref:System.Globalization.CultureInfo.GetFormat(System.Type)) 메서드는 숫자 값에 대한 문화권별 형식 정보를 제공하는 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 개체인 [CultureInfo.NumberFormat](xref:System.Globalization.CultureInfo.NumberFormat) 속성의 값을 반환합니다.

* 사용할 문화권별 형식 규칙을 정의하는 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 개체. 해당 [GetFormat](xref:System.Globalization.NumberFormatInfo.GetFormat(System.Type)) 메서드는 자신의 인스턴스를 반환합니다.

다음 예제에서는 부동 소수점 숫자의 형식을 지정하기 위해 영어(미국) 및 영어(영국) 문화권과 프랑스어 및 러시아어 중립 문화권을 나타내는 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 개체를 사용합니다.

```csharp
using System;
using System.Globalization;

public class Example
{
   public static void Main()
   {                                                                                                    
      Double value = 1043.62957;
      string[] cultureNames = { "en-US", "en-GB", "ru", "fr" };

      foreach (var name in cultureNames) {
         NumberFormatInfo nfi = CultureInfo.CreateSpecificCulture(name).NumberFormat;
         Console.WriteLine("{0,-6} {1}", name + ":", value.ToString("N3", nfi));
      }   
   }
}
// The example displays the following output:
//       en-US: 1,043.630
//       en-GB: 1,043.630
//       ru:    1 043,630
//       fr:    1 043,630
```

```vb
Imports System.Globalization

Module Example
   Public Sub Main()
      Dim value As Double = 1043.62957
      Dim cultureNames() As String = { "en-US", "en-GB", "ru", "fr" }

      For Each name In cultureNames
         Dim nfi As NumberFormatInfo = CultureInfo.CreateSpecificCulture(name).NumberFormat
         Console.WriteLine("{0,-6} {1}", name + ":", value.ToString("N3", nfi))
      Next   
   End Sub
End Module
' The example displays the following output:
'       en-US: 1,043.630
'       en-GB: 1,043.630
'       ru:    1 043,630
'       fr:    1 043,630
```

### <a name="culturesensitive-formatting-of-date-and-time-values"></a>날짜 및 시간 값의 문화권 구분 서식 지정

기본적으로 날짜 및 시간 값의 형식은 문화권을 구분합니다. 형식 지정 메서드를 호출할 때 문화권을 지정하지 않으면 현재 스레드 문화권의 형식 규칙이 사용됩니다. 이는 현재 스레드 문화권을 4번 변경한 후 [DateTime.ToString(String)](xref:System.DateTime.ToString(System.String)) 메서드를 호출하는 다음 예제에 나와 있습니다. 각각의 경우 결과 문자열은 현재 문화권의 형식 규칙을 반영합니다. 이는 [DateTime.ToString()](xref:System.DateTime.ToString), [DateTime.ToString(String)](xref:System.DateTime.ToString(System.String)), [DateTimeOffset.ToString()](xref:System.DateTimeOffset.ToString(System.String)) 및 [DateTimeOffset.ToString(String)](xref:System.DateTimeOffset.ToString(System.String)) 메서드가 [DateTime.ToString(String, IFormatProvider)](xref:System.DateTime.ToString(System.String,System.IFormatProvider)) 및 [DateTimeOffset.ToString(String, IFormatProvider)](xref:System.DateTimeOffset.ToString(System.String,System.IFormatProvider)) 메서드 호출을 래핑하기 때문입니다.

```csharp
using System;
using System.Globalization;
using System.Threading;

public class Example
{
   public static void Main()
   {
      string[] cultureNames = { "en-US", "fr-FR", "es-MX", "de-DE" };
      DateTime dateToFormat = new DateTime(2012, 5, 28, 11, 30, 0);

      foreach (var cultureName in cultureNames) {
         // Change the current thread culture.
         Thread.CurrentThread.CurrentCulture = CultureInfo.CreateSpecificCulture(cultureName);
         Console.WriteLine("The current culture is {0}", 
                           Thread.CurrentThread.CurrentCulture.Name);
         Console.WriteLine(dateToFormat.ToString("F"));
         Console.WriteLine();
      }   
   }
}
// The example displays the following output:
//       The current culture is en-US
//       Monday, May 28, 2012 11:30:00 AM
//       
//       The current culture is fr-FR
//       lundi 28 mai 2012 11:30:00
//       
//       The current culture is es-MX
//       lunes, 28 de mayo de 2012 11:30:00 a.m.
//       
//       The current culture is de-DE
//       Montag, 28. Mai 2012 11:30:00
```

```vb
Imports System.Globalization
Imports System.Threading 

Module Example
   Public Sub Main()
      Dim cultureNames() As String = { "en-US", "fr-FR", "es-MX", "de-DE" }
      Dim dateToFormat As Date = #5/28/2012 11:30AM#

      For Each cultureName In cultureNames
         ' Change the current thread culture.
         Thread.CurrentThread.CurrentCulture = CultureInfo.CreateSpecificCulture(cultureName)
         Console.WriteLine("The current culture is {0}", 
                           Thread.CurrentThread.CurrentCulture.Name)
         Console.WriteLine(dateToFormat.ToString("F"))
         Console.WriteLine()
      Next                  
   End Sub
End Module
' The example displays the following output:
'       The current culture is en-US
'       Monday, May 28, 2012 11:30:00 AM
'       
'       The current culture is fr-FR
'       lundi 28 mai 2012 11:30:00
'       
'       The current culture is es-MX
'       lunes, 28 de mayo de 2012 11:30:00 a.m.
'       
'       The current culture is de-DE
'       Montag, 28. Mai 2012 11:30:00
```

provider 매개 변수를 포함하는 [DateTime.ToString](xref:System.DateTime.ToString(System.String,System.IFormatProvider)) 또는 [DateTimeOffset.ToString](xref:System.DateTimeOffset.ToString(System.String,System.IFormatProvider)) 오버로드를 호출하고 여기에 다음 중 하나를 전달하여 특정 문화권에 대한 날짜 및 시간 값의 형식을 지정할 수도 있습니다. 

* 사용할 형식 규칙이 포함된 문화권을 나타내는 [CultureInfo](xref:System.Globalization.CultureInfo) 개체. 해당 [CultureInfo.GetFormat](xref:System.Globalization.CultureInfo.GetFormat(System.Type)) 메서드는 숫자 값에 대한 문화권별 형식 정보를 제공하는 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체인 [CultureInfo.NumberFormat](xref:System.Globalization.CultureInfo.NumberFormat) 속성의 값을 반환합니다.

* 사용할 문화권별 형식 규칙을 정의하는 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체. 해당 [GetFormat](xref:System.Globalization.DateTimeFormatInfo.GetFormat(System.Type)) 메서드는 자신의 인스턴스를 반환합니다.

다음 예제에서는 날짜의 형식을 지정하기 위해 영어(미국) 및 영어(영국) 문화권과 프랑스어 및 러시아어 중립 문화권을 나타내는 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체를 사용합니다. 

```csharp
using System;
using System.Globalization;

public class Example
{
   public static void Main()
   {                                                                                                    
      DateTime dat1 = new DateTime(2012, 5, 28, 11, 30, 0);
      string[] cultureNames = { "en-US", "en-GB", "ru", "fr" };

      foreach (var name in cultureNames) {
         DateTimeFormatInfo dtfi = CultureInfo.CreateSpecificCulture(name).DateTimeFormat;
         Console.WriteLine("{0}: {1}", name, dat1.ToString(dtfi));
      }   
   }
}
// The example displays the following output:
//       en-US: 5/28/2012 11:30:00 AM
//       en-GB: 28/05/2012 11:30:00
//       ru: 28.05.2012 11:30:00
//       fr: 28/05/2012 11:30:00
```

```vb
Imports System.Globalization

Module Example
   Public Sub Main()
      Dim dat1 As Date = #5/28/2012 11:30AM#
      Dim cultureNames() As String = { "en-US", "en-GB", "ru", "fr" }

      For Each name In cultureNames
         Dim dtfi As DateTimeFormatInfo = CultureInfo.CreateSpecificCulture(name).DateTimeFormat
         Console.WriteLine("{0}: {1}", name, dat1.ToString(dtfi))
      Next   
   End Sub
End Module
' The example displays the following output:
'       en-US: 5/28/2012 11:30:00 AM
'       en-GB: 28/05/2012 11:30:00
'       ru: 28.05.2012 11:30:00
'       fr: 28/05/2012 11:30:00
```

## <a name="the-iformattable-interface"></a>IFormattable 인터페이스

일반적으로 형식 문자열과 [IFormatProvider](xref:System.IFormatProvider) 매개 변수로 `ToString` 메서드를 오버로드하는 형식은 [IFormattable](xref:System.IFormattable) 인터페이스도 구현합니다. 이 인터페이스에는 형식 문자열과 형식 공급자가 매개 변수로 포함되어 있는 [IFormattable.ToString(String, IFormatProvider)](xref:System.IFormattable.ToString(System.String,System.IFormatProvider)) 멤버가 하나 있습니다.

응용 프로그램 정의 클래스에 대해 [IFormattable](xref:System.IFormattable) 인터페이스를 구현하면 다음과 같은 두 가지 이점이 있습니다. 

* [Convert](xref:System.Convert) 클래스를 사용한 문자열 변환을 지원합니다. [Convert.ToString(Object)](xref:System.Convert.ToString(System.Object)) 및 [Convert.ToString(Object, IFormatProvider)](xref:System.Convert.ToString(System.Object,System.IFormatProvider)) 메서드를 호출하면 사용자 [IFormattable](xref:System.IFormattable) 구현이 자동으로 호출됩니다.

* 복합 형식 지정을 지원합니다. 형식 문자열을 포함한 형식 항목을 사용하여 사용자 지정 형식의 서식을 지정하는 경우 공용 언어 런타임에서 자동으로 사용자 [IFormattable](xref:System.IFormattable) 구현을 호출하고 형식 문자열을 전달합니다. `String.Format` 또는 `Console.WriteLine` 같은 메서드를 사용하는 복합 서식 지정에 대한 자세한 내용은 [복합 서식 지정](#composite-formatting) 섹션을 참조하세요.

다음 예제에서는 [IFormattable](xref:System.IFormattable) 인터페이스를 구현하는 `Temperature` 클래스를 정의합니다. 이 클래스는 온도를 섭씨로 표시하는 "C" 또는 "G" 형식 지정자, 온도를 화씨로 표시하는 "F" 형식 지정자 또는 온도를 켈빈으로 표시하는 "K" 형식 지정자를 지원합니다.

```csharp
using System;
using System.Globalization;

public class Temperature : IFormattable
{
   private decimal m_Temp;

   public Temperature(decimal temperature)
   {
      this.m_Temp = temperature;
   }

   public decimal Celsius
   {
      get { return this.m_Temp; }
   }

   public decimal Kelvin
   {
      get { return this.m_Temp + 273.15m; }   
   }

   public decimal Fahrenheit
   {
      get { return Math.Round((decimal) this.m_Temp * 9 / 5 + 32, 2); }
   }

   public override string ToString()
   {
      return this.ToString("G", null);
   }

   public string ToString(string format)
   {
      return this.ToString(format, null);
   }

   public string ToString(string format, IFormatProvider provider)  
   {
      // Handle null or empty arguments.
      if (String.IsNullOrEmpty(format)) format = "G";
      // Remove any white space and convert to uppercase.
      format = format.Trim().ToUpperInvariant();

      if (provider == null) provider = NumberFormatInfo.CurrentInfo;

      switch (format)
      {
         // Convert temperature to Fahrenheit and return string.
         case "F":
            return this.Fahrenheit.ToString("N2", provider) + "°F";
         // Convert temperature to Kelvin and return string.
         case "K":
            return this.Kelvin.ToString("N2", provider) + "K";
         // Return temperature in Celsius.
         case "C":
         case "G":
            return this.Celsius.ToString("N2", provider) + "°C";
         default:
            throw new FormatException(String.Format("The '{0}' format string is not supported.", format));
      }      
   }
}
```

```vb
Imports System.Globalization

Public Class Temperature : Implements IFormattable
   Private m_Temp As Decimal

   Public Sub New(temperature As Decimal)
      Me.m_Temp = temperature
   End Sub

   Public ReadOnly Property Celsius() As Decimal
      Get
         Return Me.m_Temp
      End Get   
   End Property

   Public ReadOnly Property Kelvin() As Decimal
      Get
         Return Me.m_Temp + 273.15d   
      End Get
   End Property

   Public ReadOnly Property Fahrenheit() As Decimal
      Get
         Return Math.Round(CDec(Me.m_Temp * 9 / 5 + 32), 2)
      End Get      
   End Property

   Public Overrides Function ToString() As String
      Return Me.ToString("G", Nothing)
   End Function

   Public Overloads Function ToString(format As String) As String
      Return Me.ToString(format, Nothing)
   End Function

   Public Overloads Function ToString(format As String, provider As IFormatProvider) As String _  
      Implements IFormattable.ToString

      ' Handle null or empty arguments.
      If String.IsNullOrEmpty(format) Then format = "G"
      ' Remove any white space and convert to uppercase.
      format = format.Trim().ToUpperInvariant()

      If provider Is Nothing Then provider = NumberFormatInfo.CurrentInfo

      Select Case format
         ' Convert temperature to Fahrenheit and return string.
         Case "F"
            Return Me.Fahrenheit.ToString("N2", provider) & "°F"
         ' Convert temperature to Kelvin and return string.
         Case "K"
            Return Me.Kelvin.ToString("N2", provider) & "K"
         ' Return temperature in Celsius.
         Case "C", "G"
            Return Me.Celsius.ToString("N2", provider) & "°C"
         Case Else
            Throw New FormatException(String.Format("The '{0}' format string is not supported.", format))
      End Select      
   End Function
End Class
```

다음 예제에서는 `Temperature` 개체를 인스턴스화합니다. 그런 다음 [ToString](xref:System.Convert.ToString(System.Object,System.IFormatProvider)) 메서드를 호출하고 여러 복합 형식 문자열을 사용하여 `Temperature` 개체의 다양한 문자열 표현을 얻습니다. 이 메서드를 호출할 때마다 자동으로 `Temperature` 클래스의 [IFormattable](xref:System.IFormattable) 구현도 호출됩니다.

```csharp
public class Example
{
   public static void Main()
   {
      Temperature temp1 = new Temperature(22m);
      Console.WriteLine(Convert.ToString(temp1, new CultureInfo("ja-JP")));
      Console.WriteLine("Temperature: {0:K}", temp1);
      Console.WriteLine("Temperature: {0:F}", temp1);
      Console.WriteLine(String.Format(new CultureInfo("fr-FR"), "Temperature: {0:F}", temp1));
   }
}
// The example displays the following output:
//       22.00°C
//       Temperature: 295.15°K
//       Temperature: 71.60°F
//       Temperature: 71,60°F
```

```vb
Public Module Example
   Public Sub Main()
      Dim temp1 As New Temperature(22d)
      Console.WriteLine(Convert.ToString(temp1, New CultureInfo("ja-JP")))
      Console.WriteLine("Temperature: {0:K}", temp1)
      Console.WriteLine("Temperature: {0:F}", temp1)
      Console.WriteLine(String.Format(New CultureInfo("fr-FR"), "Temperature: {0:F}", temp1)) 
   End Sub
End Module
' The example displays the following output:
'       22.00°C
'       Temperature: 295.15°K
'       Temperature: 71.60°F
'       Temperature: 71,60°F
```

## <a name="composite-formatting"></a>복합 형식 지정

`String.Format` 및 `StringBuilder.AppendFormat` 같은 일부 메서드는 복합 형식 지정을 지원합니다. 복합 형식 문자열은 0개 이상의 개체에 대한 문자열 표현이 통합된 단일 문자열을 반환하는 일종의 템플릿입니다. 복합 형식 문자열에서는 각 개체가 인덱싱된 형식 항목으로 표현됩니다. 형식 항목의 인덱스는 메서드의 매개 변수 목록에 표시되는 개체의 위치에 해당합니다. 인덱스는 0에서 시작합니다. 예를 들어 다음과 같은 `String.Format` 메서드 호출에서 첫 번째 형식 항목인 `{0:D}`는 `thatDate`의 문자열 표현으로 바뀌고, 두 번째 형식 항목인 `{1}`은 `item1`의 문자열 표현으로 바뀌고, 세 번째 형식 항목인 `{2:C2}`는 `item1.Value`의 문자열 표현으로 바뀝니다.

```csharp
result = String.Format("On {0:d}, the inventory of {1} was worth {2:C2}.", 
                       thatDate, item1, item1.Value);
Console.WriteLine(result);                            
// The example displays output like the following if run on a system
// whose current culture is en-US:
//       On 5/1/2009, the inventory of WidgetA was worth $107.44.
```

```vb
result = String.Format("On {0:d}, the inventory of {1} was worth {2:C2}.", _
                       thatDate, item1, item1.Value)
Console.WriteLine(result)                            
' The example displays output like the following if run on a system
' whose current culture is en-US:
'       On 5/1/2009, the inventory of WidgetA was worth $107.44.
```

형식 항목을 해당 개체의 문자열 표현으로 바꾸는 것 외에도 형식 항목을 통해 다음을 제어할 수 있습니다. 

* 개체가 [IFormattable](xref:System.IFormattable) 인터페이스를 구현하고 형식 문자열을 지원하는 경우 개체가 문자열로 표현되는 특정 방식. 이렇게 하려면 형식 항목의 인덱스 뒤에 :(콜론) 및 유효한 형식 문자열을 추가합니다. 이전 예제에서는 날짜 값의 형식을 "d"(짧은 날짜 패턴) 형식 문자열(예: `{0:d}`)로 지정하고 숫자 값의 형식을 "C2" 형식 문자열(예: `{2:C2}`)로 지정하여 소수 두 자리의 통화 값으로 숫자를 나타냈습니다. 

* 개체의 문자열 표현을 포함하는 필드의 너비 및 해당 필드의 문자열 표현 맞춤. 이렇게 하려면 형식 항목의 인덱스 뒤에 ,(쉼표) 및 필드 너비를 추가합니다. 필드 너비가 양수 값이면 문자열이 필드에 오른쪽 맞춤되고, 필드 너비가 음수 값이면 왼쪽 맞춤됩니다. 다음 예제에서는 날짜 값을 20자 필드에 왼쪽 맞춤하고, 소수 1자리의 10진수 값을 11자 필드에 오른쪽 맞춤합니다. 

```csharp
DateTime startDate = new DateTime(2015, 8, 28, 6, 0, 0);
decimal[] temps = { 73.452m, 68.98m, 72.6m, 69.24563m,
                   74.1m, 72.156m, 72.228m };
Console.WriteLine("{0,-20} {1,11}\n", "Date", "Temperature");
for (int ctr = 0; ctr < temps.Length; ctr++)
   Console.WriteLine("{0,-20:g} {1,11:N1}", startDate.AddDays(ctr), temps[ctr]);

// The example displays the following output:
//       Date                 Temperature
//
//       8/28/2015 6:00 AM           73.5
//       8/29/2015 6:00 AM           69.0
//       8/30/2015 6:00 AM           72.6
//       8/31/2015 6:00 AM           69.2
//       9/1/2015 6:00 AM            74.1
//       9/2/2015 6:00 AM            72.2
//       9/3/2015 6:00 AM            72.2
```

```vb
Dim startDate As New Date(2015, 8, 28, 6, 0, 0)
Dim temps() As Decimal = { 73.452, 68.98, 72.6, 69.24563,
                           74.1, 72.156, 72.228 }
Console.WriteLine("{0,-20} {1,11}", "Date", "Temperature")
Console.WriteLine()
For ctr As Integer = 0 To temps.Length - 1
   Console.WriteLine("{0,-20:g} {1,11:N1}", startDate.AddDays(ctr), temps(ctr))
Next
' The example displays the following output:
'       Date                 Temperature
'
'       8/28/2015 6:00 AM           73.5
'       8/29/2015 6:00 AM           69.0
'       8/30/2015 6:00 AM           72.6
'       8/31/2015 6:00 AM           69.2
'       9/1/2015 6:00 AM            74.1
'       9/2/2015 6:00 AM            72.2
'       9/3/2015 6:00 AM            72.2
```

맞춤 문자열 구성 요소와 형식 문자열 구성 요소가 둘 다 있는 경우 맞춤 문자열 구성 요소가 우선합니다(예: `{0,-20:g}`). 

복합 서식 지정에 대한 자세한 내용은 [복합 서식 지정](composite-format.md)을 참조하세요.

## <a name="custom-formatting-with-icustomformatter"></a>ICustomFormatter를 사용한 사용자 지정 서식 지정

두 복합 형식 지정 메서드 [String.Format(IFormatProvider, String, Object[])](xref:System.String.Format(System.IFormatProvider,System.String,System.Object[])) 및 [StringBuilder.AppendFormat(IFormatProvider, String, Object[])](xref:System.Text.StringBuilder.AppendFormat(System.IFormatProvider,System.String,System.Object))에는 사용자 지정 형식 지정을 지원하는 형식 공급자 매개 변수가 포함되어 있습니다. 이러한 형식 지정 메서드 중 하나를 호출하면 [ICustomFormatter](xref:System.ICustomFormatter) 인터페이스를 나타내는 [Type](xref:System.Type) 개체가 형식 공급자의 `GetFormat` 메서드에 전달됩니다. 그러면 `GetFormat` 메서드가 사용자 지정 형식 지정을 제공하는 [ICustomFormatter](xref:System.ICustomFormatter) 구현을 반환합니다.

[ICustomFormatter](xref:System.ICustomFormatter) 인터페이스에는 복합 형식 지정 메서드에 의해 자동으로 복합 형식 문자열의 각 형식 항목마다 한 번씩 호출되는 [Format(String, Object, IFormatProvider)](xref:System.ICustomFormatter.Format(System.String,System.Object,System.IFormatProvider)) 메서드가 하나 있습니다. [Format(String, Object, IFormatProvider)](xref:System.ICustomFormatter.Format(System.String,System.Object,System.IFormatProvider)) 메서드에는 세 개의 매개 변수, 즉 형식 항목의 *formatString* 인수를 나타내는 형식 문자열, 형식을 지정할 개체 및 형식 지정 서비스를 제공하는 [IFormatProvider](xref:System.IFormatProvider) 개체가 포함되어 있습니다. 일반적으로 [ICustomFormatter](xref:System.ICustomFormatter)를 구현하는 클래스는 [IFormatProvider](xref:System.IFormatProvider)도 구현하여 이 마지막 매개 변수가 사용자 지정 형식 지정 클래스를 참조하도록 합니다. 이 메서드는 형식을 지정할 개체의 사용자 지정 형식 문자열 표현을 반환합니다. 이 메서드가 개체의 형식을 지정할 수 없는 경우에는 null 참조가 반환되어야 합니다.

다음 예제에서는 정수 값을 뒤에 공백이 오는 두 자리 16진수 값 시퀀스로 표시하는 `ByteByByteFormatter`라는 [ICustomFormatter](xref:System.ICustomFormatter) 구현을 제공합니다.

```csharp
public class ByteByByteFormatter : IFormatProvider, ICustomFormatter
{
   public object GetFormat(Type formatType)
   { 
      if (formatType == typeof(ICustomFormatter))
         return this;
      else
         return null;
   }

   public string Format(string format, object arg, 
                          IFormatProvider formatProvider)
   {   
      if (! formatProvider.Equals(this)) return null;

      // Handle only hexadecimal format string.
      if (! format.StartsWith("X")) return null;

      byte[] bytes;
      string output = null;

      // Handle only integral types.
      if (arg is Byte) 
         bytes = BitConverter.GetBytes((Byte) arg);
      else if (arg is Int16)
         bytes = BitConverter.GetBytes((Int16) arg);
      else if (arg is Int32)
         bytes = BitConverter.GetBytes((Int32) arg);
      else if (arg is Int64)   
         bytes = BitConverter.GetBytes((Int64) arg);
      else if (arg is SByte)
         bytes = BitConverter.GetBytes((SByte) arg);
      else if (arg is UInt16)
         bytes = BitConverter.GetBytes((UInt16) arg);
      else if (arg is UInt32)
         bytes = BitConverter.GetBytes((UInt32) arg);
      else if (arg is UInt64)
         bytes = BitConverter.GetBytes((UInt64) arg);
      else
         return null;

      for (int ctr = bytes.Length - 1; ctr >= 0; ctr--)
         output += String.Format("{0:X2} ", bytes[ctr]);   

      return output.Trim();
   }
}
```

```vb
Public Class ByteByByteFormatter : Implements IFormatProvider, ICustomFormatter
   Public Function GetFormat(formatType As Type) As Object _
                   Implements IFormatProvider.GetFormat
      If formatType Is GetType(ICustomFormatter) Then
         Return Me
      Else
         Return Nothing
      End If
   End Function

   Public Function Format(fmt As String, arg As Object, 
                          formatProvider As IFormatProvider) As String _
                          Implements ICustomFormatter.Format

      If Not formatProvider.Equals(Me) Then Return Nothing

      ' Handle only hexadecimal format string.
      If Not fmt.StartsWith("X") Then 
            Return Nothing
      End If

      ' Handle only integral types.
      If Not typeof arg Is Byte AndAlso
         Not typeof arg Is Int16 AndAlso
         Not typeof arg Is Int32 AndAlso
         Not typeof arg Is Int64 AndAlso
         Not typeof arg Is SByte AndAlso
         Not typeof arg Is UInt16 AndAlso
         Not typeof arg Is UInt32 AndAlso
         Not typeof arg Is UInt64 Then _
            Return Nothing

      Dim bytes() As Byte = BitConverter.GetBytes(arg)
      Dim output As String = Nothing

      For ctr As Integer = bytes.Length - 1 To 0 Step -1
         output += String.Format("{0:X2} ", bytes(ctr))   
      Next

      Return output.Trim()
   End Function
End Class
```

다음 예제에서는 `ByteByByteFormatter` 클래스를 사용하여 정수 값의 형식을 지정합니다. [ICustomFormatter.Format](xref:System.ICustomFormatter.Format(System.String,System.Object,System.IFormatProvider)) 메서드는 두 번째 [String.Format(IFormatProvider, String, Object[])](xref:System.ICustomFormatter.Format(System.String,System.Object,System.IFormatProvider)) 메서드 호출에서 두 번 이상 호출되고 `.ByteByByteFormatter.Format` 메서드가 "N0" 형식 문자열을 인식하지 못하고 null 참조를 반환하기 때문에 세 번째 메서드 호출에서는 기본 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 공급자가 사용됩니다.

```csharp
public class Example
{
   public static void Main()
   {
      long value = 3210662321; 
      byte value1 = 214;
      byte value2 = 19;

      Console.WriteLine(String.Format(new ByteByByteFormatter(), "{0:X}", value));
      Console.WriteLine(String.Format(new ByteByByteFormatter(), "{0:X} And {1:X} = {2:X} ({2:000})", 
                                      value1, value2, value1 & value2));                                
      Console.WriteLine(String.Format(new ByteByByteFormatter(), "{0,10:N0}", value));
   }
}
// The example displays the following output:
//       00 00 00 00 BF 5E D1 B1
//       00 D6 And 00 13 = 00 12 (018)
//       3,210,662,321
```

```vb
Public Module Example
   Public Sub Main()
      Dim value As Long = 3210662321 
      Dim value1 As Byte = 214
      Dim value2 As Byte = 19

      Console.WriteLine((String.Format(New ByteByByteFormatter(), "{0:X}", value)))
      Console.WriteLine((String.Format(New ByteByByteFormatter(), "{0:X} And {1:X} = {2:X} ({2:000})", 
                                      value1, value2, value1 And value2)))                                
      Console.WriteLine(String.Format(New ByteByByteFormatter(), "{0,10:N0}", value))
   End Sub
End Module
' The example displays the following output:
'       00 00 00 00 BF 5E D1 B1
'       00 D6 And 00 13 = 00 12 (018)
'       3,210,662,321
```

## <a name="related-topics"></a>관련 항목

제목 | 정의
----- | ----------
[표준 숫자 서식 문자열](standard-numeric.md) | 숫자 값의 일반적으로 사용되는 문자열 표현을 만드는 표준 형식 문자열에 대해 설명합니다. 
[사용자 지정 숫자 서식 문자열](custom-numeric.md) | 숫자 값의 응용 프로그램별 형식을 만드는 사용자 지정 형식 문자열에 대해 설명합니다.
[표준 날짜 및 시간 서식 문자열](standard-datetime.md) |  [DateTime](xref:System.DateTime) 값의 일반적으로 사용되는 문자열 표현을 만드는 표준 형식 문자열에 대해 설명합니다.
[사용자 지정 날짜 및 시간 서식 문자열](custom-datetime.md) | [DateTime](xref:System.DateTime) 값의 응용 프로그램별 형식을 만드는 사용자 지정 형식 문자열에 대해 설명합니다.
[표준 TimeSpan 서식 문자열](standard-timespan.md) | 시간 간격의 일반적으로 사용되는 문자열 표현을 만드는 표준 형식 문자열에 대해 설명합니다.
[사용자 지정 TimeSpan 서식 문자열](custom-timespan.md) | 시간 간격의 응용 프로그램별 형식을 만드는 사용자 지정 형식 문자열에 대해 설명합니다.
[열거형 서식 문자열](enumeration-format.md) | 열거형 값의 문자열 표현을 만드는 데 사용되는 표준 형식 문자열에 대해 설명합니다.
[복합 서식 지정](composite-format.md) | 문자열에 형식이 지정된 하나 이상의 값을 포함시키는 방법에 대해 설명합니다. 그런 후 해당 문자열을 콘솔에 표시하거나 스트림에 쓸 수 있습니다.
[서식 지정 작업 수행](performing-formatting-operations.md) | 특정 형식 지정 작업을 수행하기 위한 단계별 지침을 제공하는 항목을 나열합니다.
[문자열 구문 분석](parsing-strings.md) | 개체를 해당 개체의 문자열 표현으로 표시되는 값으로 초기화하는 방법에 대해 설명합니다. 구문 분석은 형식 지정의 역순으로 진행됩니다.

## <a name="reference"></a>참조

[System.IFormattable](xref:System.IFormattable)

[System.IFormatProvider](xref:System.IFormatProvider)

[System.ICustomFormatter](xref:System.ICustomFormatter)



<!--HONumber=Nov16_HO1-->


