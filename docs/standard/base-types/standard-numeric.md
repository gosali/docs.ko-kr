---
title: "표준 숫자 서식 문자열"
description: "표준 숫자 서식 문자열"
keywords: .NET, .NET Core
author: stevehoag
ms.author: shoag
manager: wpickett
ms.date: 07/26/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 285faf73-466a-4af0-8eba-7e509958f240
translationtype: Human Translation
ms.sourcegitcommit: b20713600d7c3ddc31be5885733a1e8910ede8c6
ms.openlocfilehash: 4887a55690e47f7867cee28ab9b9fe258b208e54

---

# <a name="standard-numeric-format-strings"></a>표준 숫자 서식 문자열

표준 숫자 서식 문자열은 일반 숫자 형식의 서식을 지정하는 데 사용됩니다. 표준 숫자 서식 문자열은 **A**_xx_ 형식을 취합니다. 여기서 

* **A**는 *서식 지정자*라는 단일 영문자입니다. 공백을 포함하여 영문자가 두 개 이상 포함된 숫자 서식 문자열은 사용자 지정 숫자 서식 문자열로 해석됩니다. 자세한 내용은 [사용자 지정 숫자 서식 문자열](custom-numeric.md)을 참조하세요. 

* *xx*는 *전체 자릿수 지정자*라는 선택적 정수입니다. 전체 자릿수 지정자는 0에서 99 사이의 정수이며 결과의 자릿수에 영향을 줍니다. 전체 자릿수 지정자는 숫자의 문자열 표현에서 자릿수를 제어합니다. 숫자 자체는 반올림하지 않습니다. 반올림 작업을 수행하려면 [Math.Ceiling](xref:System.Math), [Math.Floor](xref:System.Math) 또는 [Math.Round](xref:System.Math) 메서드를 사용합니다. 

*전체 자릿수 지정자*가 결과 문자열의 소수 자릿수를 제어하는 경우 결과 문자열은 0에서 멀어지는 쪽으로 반올림되는 숫자를 반영합니다(즉, [MidpointRounding.AwayFromZero](xref:System.MidpointRounding.AwayFromZero) 사용). 

> [!NOTE]
> 전체 자릿수 지정자는 결과 문자열의 자릿수를 결정합니다. 선행 또는 후행 공백을 포함한 결과 문자열을 채우려면 서식 항목에서 [복합 서식 지정](composite-format.md) 기능을 사용하고 *맞춤 구성 요소*를 정의합니다. 

표준 숫자 서식 문자열은 모든 숫자 형식의 `ToString` 메서드를 오버로드하여 사용할 수 있습니다. 예를 들어 [Int32](xref:System.Int32) 형식의 [ToString(String)](xref:System.Int32.ToString(System.String)) 및 [ToString(String, IFormatProvider)](xref:System.Int32.ToString(System.String,System.IFormatProvider)) 메서드에 숫자 서식 문자열을 제공할 수 있습니다. [Console](xref:System.Console) 및 [StreamWriter](xref:System.IO.StreamWriter) 클래스의 일부 `Write` 및 `WriteLine` 메서드, [String.Format](xref:System.String.Format(System.IFormatProvider,System.String,System.Object)) 메서드, [StringBuilder.AppendFormat](xref:System.Text.StringBuilder.AppendFormat(System.IFormatProvider,System.String,System.Object)) 메서드에 사용되는 .NET Framework [복합 서식 지정](composite-format.md) 기능을 통해서도 표준 숫자 서식 문자열을 사용할 수 있습니다. 합성 서식 기능을 사용하여 단일 문자열에 여러 데이터 항목의 문자열 표현을 포함하고, 필드 너비를 지정하며, 필드에서 숫자를 맞출 수 있습니다. 자세한 내용은 [복합 서식 지정](composite-format.md)을 참조하세요. 

다음 표에서는 표준 숫자 서식 지정자 및 각 서식 지정자로 생성되는 샘플 출력을 보여 줍니다. 표준 숫자 서식 문자열을 사용하는 방법에 대한 자세한 내용은 [참고](#notes) 섹션을 참조하고, 이러한 사용 방법을 자세히 보여 주는 예제를 보려면 [예제](#example) 섹션을 참조하세요.

|형식 지정자|이름|설명|예제|  
|----------------------|----------|-----------------|--------------|  
|"C" 또는 "c"|통화|결과: 통화 값<br /><br /> 지원되는 형식: 모든 숫자 형식<br /><br /> 전체 자릿수 지정자: 소수 자릿수<br /><br /> 기본 전체 자릿수 지정자: [NumberFormatInfo.CurrencyDecimalDigits](xref:System.Globalization.NumberFormatInfo.CurrencyDecimalDigits)으로 정의됩니다.<br /><br /> |123.456 ("C", en-US) -> $123.46<br /><br /> 123.456 ("C", fr-FR) -> 123,46 €<br /><br /> 123.456 ("C", ja-JP) -> ¥123<br /><br /> -123.456 ("C3", en-US) -> ($123.456)<br /><br /> -123.456 ("C3", fr-FR) -> -123,456 €<br /><br /> -123.456 ("C3", ja-JP) -> -¥123.456|  
|"D" 또는 "d"|Decimal|결과: 정수(선택적 음수 기호 포함)<br /><br /> 지원되는 형식: 정수 계열 형식만 지원됨<br /><br /> 전체 자릿수 지정자: 최소 자릿수<br /><br /> 기본 전체 자릿수 지정자: 필요한 최소 자릿수<br /><br /> |1234 ("D") -> 1234<br /><br /> -1234 ("D6") -> -001234|  
|"E" 또는 "e"|지수(과학적 표기법)|결과: 지수 표기법<br /><br /> 지원되는 형식: 모든 숫자 형식<br /><br /> 전체 자릿수 지정자: 소수 자릿수<br /><br /> 기본 전체 자릿수 지정자: 6<br /><br /> |1052.0329112756 ("E", en-US) -> 1.052033E+003<br /><br /> 1052.0329112756 ("e", fr-FR) -> 1,052033e+003<br /><br /> -1052.0329112756 ("e2", en-US) -> -1.05e+003<br /><br /> -1052.0329112756 ("E2", fr_FR) -> -1,05E+003|  
|"F" 또는 "f"|고정 소수점|결과: 선택적 음수 기호가 있는 정수 부분과 소수 부분<br /><br /> 지원되는 형식: 모든 숫자 형식<br /><br /> 전체 자릿수 지정자: 소수 자릿수<br /><br /> 기본 전체 자릿수 지정자: [NumberFormatInfo.NumberDecimalDigits](xref:System.Globalization.NumberFormatInfo.NumberDecimalDigits)으로 정의됩니다.<br /><br /> |1234.567 ("F", en-US) -> 1234.57<br /><br /> 1234.567 ("F", de-DE) -> 1234,57<br /><br /> 1234 ("F1", en-US) -> 1234.0<br /><br /> 1234 ("F1", de-DE) -> 1234,0<br /><br /> -1234.56 ("F4", en-US) -> -1234.5600<br /><br /> -1234.56 ("F4", de-DE) -> -1234,5600|  
|"G" 또는 "g"|일반|결과: 더 간단한 형태의 고정 소수점 또는 과학적 표기법<br /><br /> 지원되는 형식: 모든 숫자 형식<br /><br /> 전체 자릿수 지정자: 유효 자릿수<br /><br /> 기본 전체 자릿수 지정자: 숫자 형식에 따라 다름<br /><br /> |-123.456 ("G", en-US) -> -123.456<br /><br /> -123.456 ("G", sv-SE) -> -123,456<br /><br /> 123.4546 ("G4", en-US) -> 123.5<br /><br /> 123.4546 ("G4", sv-SE) -> 123,5<br /><br /> -1.234567890e-25 ("G", en-US) -> -1.23456789E-25<br /><br /> -1.234567890e-25 ("G", sv-SE) -> -1,23456789E-25|  
|"N" 또는 "n"|숫자|결과: 선택적 음수 기호가 있는 정수 부분과 소수 부분, 그룹 구분 기호 및 소수 구분 기호<br /><br /> 지원되는 형식: 모든 숫자 형식<br /><br /> 전체 자릿수 지정자: 필요한 소수 자릿수<br /><br /> 기본 전체 자릿수 지정자: [NumberFormatInfo.NumberDecimalDigits](xref:System.Globalization.NumberFormatInfo.NumberDecimalDigits)으로 정의됩니다.<br /><br /> |1234.567 ("N", en-US) -> 1,234.57<br /><br /> 1234.567 ("N", ru-RU) -> 1 234,57<br /><br /> 1234 ("N1", en-US) -> 1,234.0<br /><br /> 1234 ("N1", ru-RU) -> 1 234,0<br /><br /> -1234.56 ("N3", en-US) -> -1,234.560<br /><br /> -1234.56 ("N3", ru-RU) -> -1 234,560|  
|"P" 또는 "p"|백분율|결과: 100으로 곱하고 백분율 기호와 함께 표시되는 숫자<br /><br /> 지원되는 형식: 모든 숫자 형식<br /><br /> 전체 자릿수 지정자: 필요한 소수 자릿수<br /><br /> 기본 전체 자릿수 지정자: [NumberFormatInfo.PercentDecimalDigits](assetId:///P:System.Globalization.NumberFormatInfo.PercentDecimalDigits?qualifyHint=True&autoUpgrade=True)으로 정의됩니다.<br /><br /> |1 ("P", en-US) -> 100.00 %<br /><br /> 1 ("P", fr-FR) -> 100,00 %<br /><br /> -0.39678 ("P1", en-US) -> -39.7 %<br /><br /> -0.39678 ("P1", fr-FR) -> -39,7 %|  
|"R" 또는 "r"|라운드트립|결과: 해당 숫자로 라운드트립할 수 있는 문자열<br /><br /> [Single](xref:System.Single), [Double](xref:System.Double), 및 [BigInteger](xref:System.Numerics.BigInteger)에서 지원합니다.<br /><br /> 전체 자릿수 지정자: 무시됨<br /><br /> |123456789.12345678 ("R") -> 123456789.12345678<br /><br /> -1234567890.12345678 ("R") -> -1234567890.1234567|  
|"X" 또는 "x"|16진수|결과: 16진수 문자열<br /><br /> 지원되는 형식: 정수 계열 형식만 지원됨<br /><br /> 전체 자릿수 지정자: 결과 문자열의 자릿수<br /><br /> |255 ("X") -> FF<br /><br /> -1 ("x") -> ff<br /><br /> 255 ("x4") -> 00ff<br /><br /> -1 ("X4") -> 00FF|  
|기타 모든 단일 문자|알 수 없는 지정자|결과: 런타임 시 [FormatException](xref:System.FormatException)을 Throw합니다.|| 

## <a name="using-standard-numeric-format-strings"></a>표준 숫자 서식 문자열 사용

표준 숫자 서식 문자열을 사용하여 다음 두 가지 방법 중 하나로 숫자 값의 서식을 정의할 수 있습니다.

* 이 서식 문자열은 *format* 매개 변수가 있는 `ToString` 메서드의 오버로드에 전달될 수 있습니다. 다음 예제에서는 숫자 값의 서식을 현재 문화권(이 예제의 경우 en-US)의 통화 문자열로 지정합니다. 

  ```csharp
  decimal value = 123.456m;
  Console.WriteLine(value.ToString("C2"));
  // Displays $123.46
  ```

  ```vb
  Dim value As Decimal = 123.456d
  Console.WriteLine(value.ToString("C2"))         
  ' Displays $123.46
  ```
  
* 이러한 메서드를 [String.Format](xref:System.String.Format(System.IFormatProvider,System.String,System.Object)), [Console.WriteLine](xref:System.Console.WriteLine) 및 [StringBuilder.AppendFormat](xref:System.Text.StringBuilder.AppendFormat(System.IFormatProvider,System.String,System.Object))로 사용하는 서식 항목에서 *formatString* 인수로 제공될 수 있습니다. 자세한 내용은 [복합 서식 지정](composite-format.md)을 참조하세요. 다음 예제에서는 서식 항목을 사용하여 문자열에 통화 값을 삽입합니다.

  ```csharp
  decimal value = 123.456m;
  Console.WriteLine("Your account balance is {0:C2}.", value);
  // Displays "Your account balance is $123.46."
  ```

  ```vb
  Dim value As Decimal = 123.456d
  Console.WriteLine("Your account balance is {0:C2}.", value)
  ' Displays "Your account balance is $123.46."
  ```
  
  필요에 따라 맞춤 인수를 제공하여 숫자 필드의 너비 및 해당 값이 오른쪽 맞춤인지 또는 왼쪽 맞춤인지 지정할 수 있습니다. 다음 예제에서는 28자 필드의 통화 값을 왼쪽 맞춤으로, 14자 필드의 통화 값을 오른쪽 맞춤으로 지정합니다.
  
  ```csharp
  decimal[] amounts = { 16305.32m, 18794.16m };
  Console.WriteLine("   Beginning Balance           Ending Balance");
  Console.WriteLine("   {0,-28:C2}{1,14:C2}", amounts[0], amounts[1]);
  // Displays:
  //        Beginning Balance           Ending Balance
  //        $16,305.32                      $18,794.16
  ```

  ```vb
  Dim amounts() As Decimal = { 16305.32d, 18794.16d }
  Console.WriteLine("   Beginning Balance           Ending Balance")
  Console.WriteLine("   {0,-28:C2}{1,14:C2}", amounts(0), amounts(1))
  ' Displays:
  '        Beginning Balance           Ending Balance
  '        $16,305.32                      $18,794.16
  ```
  
다음 단원에서는 각 표준 숫자 서식 문자열에 대해 자세히 설명합니다.

## <a name="the-currency-c-format-specifier"></a>통화("C") 서식 지정자

통화("C") 서식 지정자는 숫자를 통화 금액을 나타내는 숫자로 변환합니다. 전체 자릿수 지정자는 결과 문자열에 필요한 소수 자릿수를 나타냅니다. 전체 자릿수 지정자를 생략하면 [NumberFormatInfo.CurrencyDecimalDigits](xref:System.Globalization.NumberFormatInfo.CurrencyDecimalDigits) 속성에 의해 기본 전체 자릿수가 정의됩니다.

서식을 지정할 값의 소수 자릿수가 지정된 소수 자릿수 또는 기본 소수 자릿수보다 크면 결과 문자열에서 소수 값이 반올림됩니다. 지정한 소수 자릿수의 오른쪽에 있는 값이 5 이상인 경우 결과 문자열에서 마지막 자릿수가 양수인 경우 올림, 음수인 경우 내림됩니다(Round Away From Zero 방식).

결과 문자열은 현재 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 개체의 서식 지정 정보에 영향을 받습니다. 다음 테이블에서는 반환된 문자열의 서식을 제어하는 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 속성을 보여 줍니다.

NumberFormatInfo 속성 | 설명
------------------------- | -----------
[CurrencyPositivePattern](xref:System.Globalization.NumberFormatInfo.CurrencyPositivePattern) | 양수 값의 통화 기호 위치를 정의합니다.
[CurrencyNegativePattern](xref:System.Globalization.NumberFormatInfo.CurrencyNegativePattern) | 음수 값의 통화 기호 위치를 정의하고 음수 기호를 괄호로 나타낼지 아니면 [NegativeSign](xref:System.Globalization.NumberFormatInfo.NegativeSign) 속성으로 나타낼지 여부를 지정합니다.
[NegativeSign](xref:System.Globalization.NumberFormatInfo.NegativeSign) | [CurrencyNegativePattern](xref:System.Globalization.NumberFormatInfo.CurrencyNegativePattern)이 괄호가 사용되지 않음을 나타내는 경우에 사용되는 음수 기호를 정의합니다. 
[CurrencySymbol](xref:System.Globalization.NumberFormatInfo.CurrencySymbol) | 통화 기호를 정의합니다.
[CurrencyDecimalDigits](xref:System.Globalization.NumberFormatInfo.CurrencyDecimalDigits) | 통합 값의 기본 소수 자릿수를 정의합니다. 전체 자릿수 지정자를 사용하여 이 값을 재정의할 수 있습니다.
[CurrencyDecimalSeparator](xref:System.Globalization.NumberFormatInfo.CurrencyDecimalSeparator) | 정수 부분과 소수 부분을 구분하는 문자열을 정의합니다.
[CurrencyGroupSeparator](xref:System.Globalization.NumberFormatInfo.CurrencyGroupSeparator) | 정수 그룹을 구분하는 문자열을 정의합니다. 
[CurrencyGroupSizes](xref:System.Globalization.NumberFormatInfo.CurrencyGroupSizes) | 그룹에 표시할 정수 자릿수를 정의합니다.

다음 예제에서는 통화 서식 지정자를 사용하여 [Double](xref:System.Double) 값의 서식을 지정합니다. 

```csharp
double value = 12345.6789;
Console.WriteLine(value.ToString("C", CultureInfo.CurrentCulture));

Console.WriteLine(value.ToString("C3", CultureInfo.CurrentCulture));

Console.WriteLine(value.ToString("C3", 
                  CultureInfo.CreateSpecificCulture("da-DK")));
// The example displays the following output on a system whose
// current culture is English (United States):
//       $12,345.68
//       $12,345.679
//       kr 12.345,679
```

```vb
Dim value As Double = 12345.6789
Console.WriteLine(value.ToString("C", CultureInfo.CurrentCulture))

Console.WriteLine(value.ToString("C3", CultureInfo.CurrentCulture))

Console.WriteLine(value.ToString("C3", _
                  CultureInfo.CreateSpecificCulture("da-DK")))
' The example displays the following output on a system whose
' current culture is English (United States):
'       $12,345.68
'       $12,345.679
'       kr 12.345,679
```

## <a name="the-decimal-d-format-specifier"></a>10진수("D") 서식 지정자

10진수("D") 서식 지정자는 숫자를 10진수(0-9) 문자열로 변환하며, 숫자가 음수이면 앞에 빼기 기호를 붙입니다. 이 서식은 정수 계열 형식에만 사용할 수 있습니다.

전체 자릿수 지정자는 결과 문자열에서 요구하는 최소 자릿수를 나타냅니다. 필요하면 수의 왼쪽을 0으로 채워서 전체 자릿수 지정자에서 지정한 자릿수를 만듭니다. 전체 자릿수 지정자가 지정되지 않은 경우 기본값은 앞에 0이 없이 정수를 나타내는 데 필요한 최소값입니다.

결과 문자열은 현재 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 개체의 서식 지정 정보에 영향을 받습니다. 다음 표와 같이 단일 속성은 결과 문자열의 서식에 영향을 줍니다. 

NumberFormatInfo 속성 | 설명
------------------------- | -----------
[NegativeSign](xref:System.Globalization.NumberFormatInfo.NegativeSign) | 숫자가 음수임을 나타내는 문자열을 정의합니다. 

다음 예제에서는 10진수 서식 지정자를 사용하여 [Int32](xref:System.Int32) 값의 서식을 지정합니다.

```csharp
int value; 

value = 12345;
Console.WriteLine(value.ToString("D"));
// Displays 12345
Console.WriteLine(value.ToString("D8"));
// Displays 00012345

value = -12345;
Console.WriteLine(value.ToString("D"));
// Displays -12345
Console.WriteLine(value.ToString("D8"));
// Displays -00012345
```

```vb
Dim value As Integer 

value = 12345
Console.WriteLine(value.ToString("D"))
' Displays 12345   
Console.WriteLine(value.ToString("D8"))
' Displays 00012345

value = -12345
Console.WriteLine(value.ToString("D"))
' Displays -12345
Console.WriteLine(value.ToString("D8"))
' Displays -00012345
```

## <a name="the-exponential-e-format-specifier"></a>지수("E") 서식 지정자

지수("E") 서식 지정자는 숫자를 "-d.ddd…E+ddd" 또는 "-d.ddd…e+ddd" 형태의 문자열로 변환합니다. 여기서 각 "d"는 숫자(0-9)를 나타냅니다. 숫자가 음수이면 문자열 앞에 빼기 기호가 붙습니다. 소수점 앞에는 항상 숫자가 하나만 있어야 합니다. 

전체 자릿수 지정자는 소수점 뒤에 필요한 자릿수를 나타냅니다. 전체 자릿수 지정자가 생략되면 소수점 뒤에 기본 6자리가 사용됩니다. 

서식 지정자의 대/소문자에 따라 지수에 "E" 또는 "e" 접두사를 붙일 것인지가 결정됩니다. 지수는 항상 더하기 또는 빼기 기호가 포함된 최소 3자리로 구성됩니다. 필요하면 지수를 0으로 채워서 이 조건을 만족시킵니다.

결과 문자열은 현재 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 개체의 서식 지정 정보에 영향을 받습니다. 다음 테이블에서는 반환된 문자열의 서식을 제어하는 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 속성을 보여 줍니다.

NumberFormatInfo 속성 | 설명
------------------------- | -----------
[NegativeSign](xref:System.Globalization.NumberFormatInfo.NegativeSign) | 계수와 지수가 둘 다 음수임을 나타내는 문자열을 정의합니다. 
[NumberDecimalSeparator](xref:System.Globalization.NumberFormatInfo.NumberDecimalSeparator) | 계수에서 정수 부분과 소수 부분을 구분하는 문자열을 정의합니다.
[PositiveSign](xref:System.Globalization.NumberFormatInfo.PositiveSign) | 지수가 양수임을 나타내는 문자열을 정의합니다.

다음 예제에서는 지수 서식 지정자를 사용하여 [Double](xref:System.Double) 값의 서식을 지정합니다. 

```csharp
double value = 12345.6789;
Console.WriteLine(value.ToString("E", CultureInfo.InvariantCulture));
// Displays 1.234568E+004

Console.WriteLine(value.ToString("E10", CultureInfo.InvariantCulture));
// Displays 1.2345678900E+004

Console.WriteLine(value.ToString("e4", CultureInfo.InvariantCulture));
// Displays 1.2346e+004

Console.WriteLine(value.ToString("E", 
                  CultureInfo.CreateSpecificCulture("fr-FR")));
// Displays 1,234568E+004
```

```vb
Dim value As Double = 12345.6789
Console.WriteLine(value.ToString("E", CultureInfo.InvariantCulture))
' Displays 1.234568E+004

Console.WriteLine(value.ToString("E10", CultureInfo.InvariantCulture))
' Displays 1.2345678900E+004

Console.WriteLine(value.ToString("e4", CultureInfo.InvariantCulture))
' Displays 1.2346e+004

Console.WriteLine(value.ToString("E", _
                  CultureInfo.CreateSpecificCulture("fr-FR")))
' Displays 1,234568E+004
```

## <a name="the-fixedpoint-f-format-specifier"></a>고정 소수점("F") 서식 지정자

고정 소수점("F") 서식 지정자는 숫자를 "-ddd.ddd…" 형태의 문자열로 변환합니다. 여기서 각 "d"는 숫자(0-9)를 나타냅니다. 숫자가 음수이면 문자열 앞에 빼기 기호가 붙습니다. 

전체 자릿수 지정자는 필요한 소수 자릿수를 나타냅니다. 전체 자릿수 지정자를 생략하면 현재 [NumberFormatInfo.NumberDecimalDigits](xref:System.Globalization.NumberFormatInfo.NumberDecimalDigits) 속성에서 숫자 전체 자릿수를 제공합니다.

결과 문자열은 현재 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 개체의 서식 지정 정보에 영향을 받습니다. 다음 테이블에서는 결과 문자열의 서식을 제어하는 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 개체의 속성을 보여 줍니다.

NumberFormatInfo 속성 | 설명
------------------------- | -----------
[NegativeSign](xref:System.Globalization.NumberFormatInfo.NegativeSign) | 숫자가 음수임을 나타내는 문자열을 정의합니다.
[NumberDecimalSeparator](xref:System.Globalization.NumberFormatInfo.NumberDecimalSeparator) | 정수 부분과 소수 부분을 구분하는 문자열을 정의합니다.
[NumberFormatInfo.NumberDecimalDigits](xref:System.Globalization.NumberFormatInfo.NumberDecimalDigits) | 기본 소수 자릿수를 정의합니다. 전체 자릿수 지정자를 사용하여 이 값을 재정의할 수 있습니다.

다음 예제에서는 고정 소수점 서식 지정자를 사용하여 [Double](xref:System.Double) 및 [Int32](xref:System.Int32) 값의 서식을 지정합니다.

```csharp
int integerNumber;
integerNumber = 17843;
Console.WriteLine(integerNumber.ToString("F", 
                  CultureInfo.InvariantCulture));
// Displays 17843.00

integerNumber = -29541;
Console.WriteLine(integerNumber.ToString("F3", 
                  CultureInfo.InvariantCulture));
// Displays -29541.000

double doubleNumber;
doubleNumber = 18934.1879;
Console.WriteLine(doubleNumber.ToString("F", CultureInfo.InvariantCulture));
// Displays 18934.19

Console.WriteLine(doubleNumber.ToString("F0", CultureInfo.InvariantCulture));
// Displays 18934

doubleNumber = -1898300.1987;
Console.WriteLine(doubleNumber.ToString("F1", CultureInfo.InvariantCulture));  
// Displays -1898300.2

Console.WriteLine(doubleNumber.ToString("F3", 
                  CultureInfo.CreateSpecificCulture("es-ES")));
// Displays -1898300,199 
```

```vb
Dim integerNumber As Integer
integerNumber = 17843
Console.WriteLine(integerNumber.ToString("F", CultureInfo.InvariantCulture))
' Displays 17843.00

integerNumber = -29541
Console.WriteLine(integerNumber.ToString("F3", CultureInfo.InvariantCulture))
' Displays -29541.000

Dim doubleNumber As Double
doubleNumber = 18934.1879
Console.WriteLine(doubleNumber.ToString("F", CultureInfo.InvariantCulture))
' Displays 18934.19

Console.WriteLine(doubleNumber.ToString("F0", CultureInfo.InvariantCulture))
' Displays 18934

doubleNumber = -1898300.1987
Console.WriteLine(doubleNumber.ToString("F1", CultureInfo.InvariantCulture))  
' Displays -1898300.2

Console.WriteLine(doubleNumber.ToString("F3", _ 
                  CultureInfo.CreateSpecificCulture("es-ES")))
' Displays -1898300,199                        
```

## <a name="the-general-g-format-specifier"></a>일반("G") 서식 지정자

일반("G") 서식 지정자는 숫자의 형식 및 전체 자릿수 지정자의 유무에 따라 숫자를 고정 소수점 또는 과학적 표기법 중에서 더 간단한 서식으로 변환합니다. 전체 자릿수 지정자는 결과 문자열에 표시할 수 있는 최대 유효 자릿수를 정의합니다. 전체 자릿수 지정자가 생략되거나 0이면 다음 표에 나와 있는 대로 숫자의 형식에 따라 기본 자릿수가 결정됩니다. 

숫자 형식 | 기본 전체 자릿수
------------ | -----------------
[바이트](xref:System.Byte) 또는 [SByte](xref:System.SByte) | 3개의 자릿수
[Int16](xref:System.Int16) 또는 [UInt16](xref:System.UInt16) | 5개의 자릿수
[Int32](xref:System.Int32) 또는 [UInt32](xref:System.UInt32) | 10개의 자릿수
[Int64](xref:System.Int64) | 19개의 자릿수
[UInt64](xref:System.UInt64) | 20개의 자릿수
[BigInteger](xref:System.Numerics.BigInteger) | 무제한("R"과 동일)
[Single](xref:System.Single) | 7개의 자릿수
[Double](xref:System.Double) | 15개의 자릿수
[Decimal](xref:System.Decimal) | 29개의 자릿수

숫자를 과학적 표기법으로 나타낸 값이 -5보다 크고 전체 자릿수 지정자보다 작으면 고정 소수점 표기법이 사용되고 그러지 않으면 과학적 표기법이 사용됩니다. 필요한 경우 결과에 소수점이 포함되고 소수점 뒤에 오는 0은 생략됩니다. 전체 자릿수 지정자가 있고 결과의 유효 숫자가 지정된 자릿수를 초과하면 뒤에 오는 초과 자릿수는 반올림을 통해 제거됩니다. 

그러나 숫자가 [10진수](xref:System.Decimal)일 때 전체 자릿수 지정자가 생략되면 항상 고정 소수점 표기법이 사용되며 뒤에 오는 0은 그대로 표시됩니다.

과학적 표기법이 사용되면 서식 지정자가 'G'인 경우 결과의 지수 값 앞에 "E"가 붙고 서식 지정자가 "g"인 경우 앞에 "e"가 붙습니다. 지수는 최소한 2자리로 구성됩니다. 이는 지수 서식 지정자에 의해 생성되며 계수가 최소한 3자리로 구성되는 과학적 표기법의 서식과 다른 점입니다.

결과 문자열은 현재 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 개체의 서식 지정 정보에 영향을 받습니다. 다음 테이블에서는 결과 문자열의 서식을 제어하는 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 속성을 보여 줍니다.

NumberFormatInfo 속성 | 설명
------------------------- | -----------
[NegativeSign](xref:System.Globalization.NumberFormatInfo.NegativeSign) | 숫자가 음수임을 나타내는 문자열을 정의합니다.
[NumberDecimalSeparator](xref:System.Globalization.NumberFormatInfo.NumberDecimalSeparator) | 정수 부분과 소수 부분을 구분하는 문자열을 정의합니다.
[PositiveSign](xref:System.Globalization.NumberFormatInfo.PositiveSign) | 지수가 양수임을 나타내는 문자열을 정의합니다. 

다음 예제에서는 일반 서식 지정자를 사용하여 분류된 부동 소수점 값에 서식을 지정합니다.

```csharp
double number;

number = 12345.6789;      
Console.WriteLine(number.ToString("G", CultureInfo.InvariantCulture));
// Displays  12345.6789
Console.WriteLine(number.ToString("G", 
                  CultureInfo.CreateSpecificCulture("fr-FR")));
// Displays 12345,6789

Console.WriteLine(number.ToString("G7", CultureInfo.InvariantCulture));
// Displays 12345.68 

number = .0000023;
Console.WriteLine(number.ToString("G", CultureInfo.InvariantCulture));
// Displays 2.3E-06       
Console.WriteLine(number.ToString("G", 
                  CultureInfo.CreateSpecificCulture("fr-FR")));
// Displays 2,3E-06

number = .0023;
Console.WriteLine(number.ToString("G", CultureInfo.InvariantCulture));
// Displays 0.0023

number = 1234;
Console.WriteLine(number.ToString("G2", CultureInfo.InvariantCulture));
// Displays 1.2E+03

number = Math.PI;
Console.WriteLine(number.ToString("G5", CultureInfo.InvariantCulture));
// Displays 3.1416    
```

```vb
Dim number As Double

number = 12345.6789      
Console.WriteLine(number.ToString("G", CultureInfo.InvariantCulture))
' Displays  12345.6789
Console.WriteLine(number.ToString("G", _
                  CultureInfo.CreateSpecificCulture("fr-FR")))
' Displays 12345,6789

Console.WriteLine(number.ToString("G7", CultureInfo.InvariantCulture))
' Displays 12345.68 

number = .0000023
Console.WriteLine(number.ToString("G", CultureInfo.InvariantCulture))
' Displays 2.3E-06       
Console.WriteLine(number.ToString("G", _
                  CultureInfo.CreateSpecificCulture("fr-FR")))
' Displays 2,3E-06

number = .0023
Console.WriteLine(number.ToString("G", CultureInfo.InvariantCulture))
' Displays 0.0023

number = 1234
Console.WriteLine(number.ToString("G2", CultureInfo.InvariantCulture))
' Displays 1.2E+03

number = Math.Pi
Console.WriteLine(number.ToString("G5", CultureInfo.InvariantCulture))
' Displays 3.1416
```

## <a name="the-numeric-n-format-specifier"></a>숫자("N") 서식 지정자

숫자("N") 서식 지정자는 숫자를 "-d,ddd,ddd.ddd…" 형태의 문자열로 변환합니다. 여기서 "-"는 필요한 경우 음수 기호를 나타내고, "d"는 숫자(0-9)를 나타내고, ","는 그룹 구분 기호를 나타내고, "."은 소수점 기호를 나타냅니다. 전체 자릿수 지정자는 소수점 뒤에 필요한 자릿수를 나타냅니다. 전체 자릿수 지정자를 생략하면 현재 [NumberFormatInfo.NumberDecimalDigits](xref:System.Globalization.NumberFormatInfo.NumberDecimalDigits) 속성에 의해 소수 자릿수가 정의됩니다.

결과 문자열은 현재 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 개체의 서식 지정 정보에 영향을 받습니다. 다음 테이블에서는 결과 문자열의 서식을 제어하는 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 속성을 보여 줍니다.

NumberFormatInfo 속성 | 설명
------------------------- | -----------
[NegativeSign](xref:System.Globalization.NumberFormatInfo.NegativeSign) | 숫자가 음수임을 나타내는 문자열을 정의합니다.
[NumberNegativePattern](xref:System.Globalization.NumberFormatInfo.NumberNegativePattern) | 음수 값의 서식을 정의하고 음수 기호를 괄호로 나타낼지 아니면 [NegativeSign](xref:System.Globalization.NumberFormatInfo.NegativeSign) 속성으로 나타낼지 여부를 지정합니다.
[NumberGroupSizes](xref:System.Globalization.NumberFormatInfo.NumberGroupSizes) | 그룹 구분 기호 사이에 표시할 정수 자릿수를 정의합니다.
[NumberGroupSeparator](xref:System.Globalization.NumberFormatInfo.NumberGroupSeparator) | 정수 그룹을 구분하는 문자열을 정의합니다.
[NumberDecimalSeparator](xref:System.Globalization.NumberFormatInfo.NumberDecimalSeparator) | 정수 부분과 소수 부분을 구분하는 문자열을 정의합니다.
[NumberDecimalDigits](xref:System.Globalization.NumberFormatInfo.NumberDecimalDigits) | 기본 소수 자릿수를 정의합니다. 전체 자릿수 지정자를 사용하여 이 값을 재정의할 수 있습니다.

다음 예제에서는 숫자 서식 지정자를 사용하여 분류된 부동 소수점 값에 서식을 지정합니다.

```csharp
double dblValue = -12445.6789;
Console.WriteLine(dblValue.ToString("N", CultureInfo.InvariantCulture));
// Displays -12,445.68
Console.WriteLine(dblValue.ToString("N1", 
                  CultureInfo.CreateSpecificCulture("sv-SE")));
// Displays -12 445,7

int intValue = 123456789;
Console.WriteLine(intValue.ToString("N1", CultureInfo.InvariantCulture));
// Displays 123,456,789.0 
```

```vb
Dim dblValue As Double = -12445.6789
Console.WriteLine(dblValue.ToString("N", CultureInfo.InvariantCulture))
' Displays -12,445.68
Console.WriteLine(dblValue.ToString("N1", _
                  CultureInfo.CreateSpecificCulture("sv-SE")))
' Displays -12 445,7

Dim intValue As Integer = 123456789
Console.WriteLine(intValue.ToString("N1", CultureInfo.InvariantCulture))
' Displays 123,456,789.0 
```

## <a name="the-percent-p-format-specifier"></a>백분율("P") 서식 지정자

백분율("P") 서식 지정자는 숫자를 100으로 곱한 다음 백분율을 나타내는 문자열로 변환합니다. 전체 자릿수 지정자는 필요한 소수 자릿수를 나타냅니다. 전체 자릿수 지정자를 생략하면 현재 [PercentDecimalDigits](xref:System.Globalization.NumberFormatInfo.PercentDecimalDigits) 속성에서 제공하는 기본 숫자 전체 자릿수가 사용됩니다.

다음 테이블에서는 반환된 문자열의 서식을 제어하는 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 속성을 보여 줍니다.

umberFormatInfo 속성 | 설명
------------------------- | -----------
[PercentPositivePattern](xref:System.Globalization.NumberFormatInfo.PercentPositivePattern) | 양수 값의 백분율 기호 위치를 정의합니다.
[PercentNegativePattern](xref:System.Globalization.NumberFormatInfo.PercentNegativePattern) | 
[NegativeSign](xref:System.Globalization.NumberFormatInfo.NegativeSign) | 숫자가 음수임을 나타내는 문자열을 정의합니다.
[PercentSymbol](xref:System.Globalization.NumberFormatInfo.PercentSymbol) | 백분율 기호를 정의합니다.
[PercentDecimalDigits](xref:System.Globalization.NumberFormatInfo.PercentDecimalDigits) | 백분율 값의 기본 소수 자릿수를 정의합니다. 전체 자릿수 지정자를 사용하여 이 값을 재정의할 수 있습니다.
[PercentDecimalSeparator](xref:System.Globalization.NumberFormatInfo.PercentDecimalSeparator) | 정수 부분과 소수 부분을 구분하는 문자열을 정의합니다.
[PercentGroupSeparator](xref:System.Globalization.NumberFormatInfo.PercentGroupSeparator) | 정수 그룹을 구분하는 문자열을 정의합니다. 
[PercentGroupSizes](xref:System.Globalization.NumberFormatInfo.PercentGroupSizes) | 그룹에 표시할 정수 자릿수를 정의합니다.

다음 예제에서는 백분율 서식 지정자를 사용하여 부동 소수점 값에 서식을 지정합니다.

```csharp
double number = .2468013;
Console.WriteLine(number.ToString("P", CultureInfo.InvariantCulture));
// Displays 24.68 %
Console.WriteLine(number.ToString("P", 
                  CultureInfo.CreateSpecificCulture("hr-HR")));
// Displays 24,68%     
Console.WriteLine(number.ToString("P1", CultureInfo.InvariantCulture));
// Displays 24.7 %
```

```vb
Dim number As Double = .2468013
Console.WriteLine(number.ToString("P", CultureInfo.InvariantCulture))
' Displays 24.68 %
Console.WriteLine(number.ToString("P", _
                  CultureInfo.CreateSpecificCulture("hr-HR")))
' Displays 24,68%     
Console.WriteLine(number.ToString("P1", CultureInfo.InvariantCulture))
' Displays 24.7 %
```

## <a name="the-roundtrip-r-format-specifier"></a>라운드트립("R") 서식 지정자

라운드트립("R") 형식 지정자는 문자열로 변환된 숫자 값이 같은 숫자 값으로 다시 구문 분석되도록 하는 데 사용됩니다. 이 서식은 [Single](xref:System.Single), [Double](xref:System.Double) 및 [BigInteger](xref:System.Numerics.BigInteger) 형식에 대해서만 지원됩니다. 

이 지정자를 사용하여 [BigInteger](xref:System.Numerics.BigInteger) 값의 서식을 지정하면 해당 문자열 표현에 [BigInteger](xref:System.Numerics.BigInteger) 값의 모든 유효 자릿수가 포함됩니다. 이 지정자를 사용하여 [Single](xref:System.Single) 또는 [Double](xref:System.Double) 값의 서식을 지정하면 [Double](xref:System.Double)에는 15자리의 전체 자릿수를, [Single](xref:System.Single)에는 7자리의 전체 자릿수를 가진 일반 서식을 사용하여 이 값을 먼저 테스트합니다. 이 값이 같은 숫자 값으로 다시 구문 분석되면 일반 서식 지정자를 통해 서식이 지정됩니다. 이 값이 같은 숫자 값으로 다시 구문 분석되지 못하면 [Double](xref:System.Double) 형식에는 17자리 전체 자릿수를, [Single](xref:System.Single) 형식에는 9자리 전체 자릿수를 사용하여 이 값의 서식이 지정됩니다. 

전체 자릿수 지정자는 포함되어 있더라도 무시됩니다. 이 지정자를 사용할 때는 라운드트립이 전체 자릿수보다 우선합니다. 

결과 문자열은 현재 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 개체의 서식 지정 정보에 영향을 받습니다. 다음 테이블에서는 결과 문자열의 서식을 제어하는 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 속성을 보여 줍니다.

NumberFormatInfo 속성 | 설명
------------------------- | -----------
[NegativeSign](xref:System.Globalization.NumberFormatInfo.NegativeSign) | 숫자가 음수임을 나타내는 문자열을 정의합니다.
[NumberDecimalSeparator](xref:System.Globalization.NumberFormatInfo.NumberDecimalSeparator) | 정수 부분과 소수 부분을 구분하는 문자열을 정의합니다.
[PositiveSign](xref:System.Globalization.NumberFormatInfo.PositiveSign) | 지수가 양수임을 나타내는 문자열을 정의합니다.

 다음 예제에서는 라운드트립 서식 지정자를 사용하여 [Double](xref:System.Double) 값의 서식을 지정합니다.

```csharp
double value;

value = Math.PI;
Console.WriteLine(value.ToString("r"));
// Displays 3.1415926535897931
Console.WriteLine(value.ToString("r", 
                  CultureInfo.CreateSpecificCulture("fr-FR")));
// Displays 3,1415926535897931
value = 1.623e-21;
Console.WriteLine(value.ToString("r"));
// Displays 1.623E-21
```

```vb
Dim value As Double

value = Math.Pi
Console.WriteLine(value.ToString("r"))
' Displays 3.1415926535897931
Console.WriteLine(value.ToString("r", _
                  CultureInfo.CreateSpecificCulture("fr-FR")))
' Displays 3,1415926535897931
value = 1.623e-21
Console.WriteLine(value.ToString("r"))
' Displays 1.623E-21
```

## <a name="the-hexadecimal-x-format-specifier"></a>16진수("X") 서식 지정자

16진수("X") 서식 지정자는 숫자를 16진수 문자열로 변환합니다. 서식 지정자의 대/소문자에 따라 9보다 큰 16진수에 대문자를 사용할지 아니면 소문자를 사용할지 여부가 결정됩니다. 예를 들어, "X"를 사용하면 "ABCDEF"가 만들어지고 "x"를 사용하면 "abcdef"가 만들어집니다. 이 서식은 정수 계열 형식에만 사용할 수 있습니다.

전체 자릿수 지정자는 결과 문자열에서 요구하는 최소 자릿수를 나타냅니다. 필요하면 수의 왼쪽을 0으로 채워서 전체 자릿수 지정자에서 지정한 자릿수를 만듭니다.

결과 문자열은 현재 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 개체의 서식 지정 정보에 영향을 받지 않습니다. 

다음 예제에서는 16진수 서식 지정자를 사용하여 [Int32](xref:System.Int32) 값의 서식을 지정합니다.

```csharp
int value; 

value = 0x2045e;
Console.WriteLine(value.ToString("x"));
// Displays 2045e
Console.WriteLine(value.ToString("X"));
// Displays 2045E
Console.WriteLine(value.ToString("X8"));
// Displays 0002045E

value = 123456789;
Console.WriteLine(value.ToString("X"));
// Displays 75BCD15
Console.WriteLine(value.ToString("X2"));
// Displays 75BCD15
```

```vb
Dim value As Integer 

value = &h2045e
Console.WriteLine(value.ToString("x"))
' Displays 2045e
Console.WriteLine(value.ToString("X"))
' Displays 2045E
Console.WriteLine(value.ToString("X8"))
' Displays 0002045E

value = 123456789
Console.WriteLine(value.ToString("X"))
' Displays 75BCD15
Console.WriteLine(value.ToString("X2"))
' Displays 75BCD15
```

## <a name="notes"></a>참고

### <a name="numberformatinfo-properties"></a>NumberFormatInfo 속성

서식 지정 작업은 현재 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 개체의 속성에 따라 영향을 받으며 이 개체는 현재 스레드 문화권에 의해 암시적으로 제공되거나 서식 지정 작업을 호출하는 메서드의 [IFormatProvider](xref:System.IFormatProvider) 매개 변수를 통해 명시적으로 제공됩니다. 해당 매개 변수에 대한 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 또는 [CultureInfo](xref:System.Globalization.CultureInfo) 개체를 지정합니다. 

### <a name="integral-and-floatingpoint-numeric-types"></a>정수 및 부동 소수점 숫자 형식

표준 숫자 서식 지정자에 대한 설명 중에는 정수 계열 및 부동 소수점 숫자 형식이 언급되어 있습니다. 정수 계열 숫자 형식은 [바이트](xref:System.Byte), [SByte](xref:System.SByte), [Int16](xref:System.Int16), [Int32](xref:System.Int32), [Int64](xref:System.Int64), [UInt16](xref:System.UInt16), [UInt32](xref:System.UInt32), [UInt64](xref:System.UInt64) 및 [BigInteger](xref:System.Numerics.BigInteger)입니다. 부동 소수점 숫자 형식은 [10진수](xref:System.Decimal), [Single](xref:System.Single) 및 [Double](xref:System.Double)입니다. 

### <a name="floatingpoint-infinities-and-nan"></a>부동 소수점 무한대 및 NaN

형식 문자열에 관계없이 [Single](xref:System.Single) 또는 [Double](xref:System.Double) 부동 소수점 형식의 값이 양의 무한대, 음의 무한대 또는 NaN(Not a Number)이면 형식이 지정된 문자열은 각각 현재 적용 가능한 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 개체에서 지정하는 해당 [PositiveInfinitySymbol](xref:System.Globalization.NumberFormatInfo.PositiveInfinitySymbol), [NegativeInfinitySymbol](xref:System.Globalization.NumberFormatInfo.NegativeInfinitySymbol) 또는 [NaNSymbol](xref:System.Globalization.NumberFormatInfo.NaNSymbol) 속성의 값입니다.

## <a name="example"></a>예제

다음 예제에서는 en-US 문화권 및 모든 표준 숫자 서식 지정자를 사용하여 정수 숫자 값과 부동 소수점 숫자 값의 서식을 지정합니다. 이 예제에서는 두 개의 특정 숫자 형식([Double](xref:System.Double) 및 [Int32](xref:System.Int32))을 사용하지만 기타 숫자 기본 형식([바이트](xref:System.Byte), [SByte](xref:System.SByte), [Int16](xref:System.Int16), [Int64](xref:System.Int64), [UInt16](xref:System.UInt16), [UInt32](xref:System.UInt32), [UInt64](xref:System.UInt64), and [BigInteger](xref:System.Numerics.BigInteger), [10진수](xref:System.Decimal) 및 [Single](xref:System.Single)) 중 하나에 대해 비슷한 결과를 얻게 됩니다. 

```csharp
using System;
using System.Globalization;
using System.Threading;

public class NumericFormats
{
   public static void Main()
   {
      // Display string representations of numbers for en-us culture
      CultureInfo ci = new CultureInfo("en-us");

      // Output floating point values
      double floating = 10761.937554;
      Console.WriteLine("C: {0}", 
              floating.ToString("C", ci));           // Displays "C: $10,761.94"
      Console.WriteLine("E: {0}", 
              floating.ToString("E03", ci));         // Displays "E: 1.076E+004"
      Console.WriteLine("F: {0}", 
              floating.ToString("F04", ci));         // Displays "F: 10761.9376"         
      Console.WriteLine("G: {0}",  
              floating.ToString("G", ci));           // Displays "G: 10761.937554"
      Console.WriteLine("N: {0}", 
              floating.ToString("N03", ci));         // Displays "N: 10,761.938"
      Console.WriteLine("P: {0}", 
              (floating/10000).ToString("P02", ci)); // Displays "P: 107.62 %"
      Console.WriteLine("R: {0}", 
              floating.ToString("R", ci));           // Displays "R: 10761.937554"            
      Console.WriteLine();

      // Output integral values
      int integral = 8395;
      Console.WriteLine("C: {0}", 
              integral.ToString("C", ci));           // Displays "C: $8,395.00"
      Console.WriteLine("D: {0}", 
              integral.ToString("D6", ci));          // Displays "D: 008395" 
      Console.WriteLine("E: {0}", 
              integral.ToString("E03", ci));         // Displays "E: 8.395E+003"
      Console.WriteLine("F: {0}", 
              integral.ToString("F01", ci));         // Displays "F: 8395.0"    
      Console.WriteLine("G: {0}",  
              integral.ToString("G", ci));           // Displays "G: 8395"
      Console.WriteLine("N: {0}", 
              integral.ToString("N01", ci));         // Displays "N: 8,395.0"
      Console.WriteLine("P: {0}", 
              (integral/10000.0).ToString("P02", ci)); // Displays "P: 83.95 %"
      Console.WriteLine("X: 0x{0}", 
              integral.ToString("X", ci));           // Displays "X: 0x20CB"
      Console.WriteLine();
   }
}
```

```vb
Option Strict On

Imports System.Globalization
Imports System.Threading

Module NumericFormats
   Public Sub Main()
      ' Display string representations of numbers for en-us culture
      Dim ci As New CultureInfo("en-us")

      ' Output floating point values
      Dim floating As Double = 10761.937554
      Console.WriteLine("C: {0}", _
              floating.ToString("C", ci))           ' Displays "C: $10,761.94"
      Console.WriteLine("E: {0}", _
              floating.ToString("E03", ci))         ' Displays "E: 1.076E+004"
      Console.WriteLine("F: {0}", _
              floating.ToString("F04", ci))         ' Displays "F: 10761.9376"         
      Console.WriteLine("G: {0}", _ 
              floating.ToString("G", ci))           ' Displays "G: 10761.937554"
      Console.WriteLine("N: {0}", _
              floating.ToString("N03", ci))         ' Displays "N: 10,761.938"
      Console.WriteLine("P: {0}", _
              (floating/10000).ToString("P02", ci)) ' Displays "P: 107.62 %"
      Console.WriteLine("R: {0}", _
              floating.ToString("R", ci))           ' Displays "R: 10761.937554"            
      Console.WriteLine()

      ' Output integral values
      Dim integral As Integer = 8395
      Console.WriteLine("C: {0}", _
              integral.ToString("C", ci))           ' Displays "C: $8,395.00"
      Console.WriteLine("D: {0}", _
              integral.ToString("D6"))              ' Displays "D: 008395" 
      Console.WriteLine("E: {0}", _
              integral.ToString("E03", ci))         ' Displays "E: 8.395E+003"
      Console.WriteLine("F: {0}", _
              integral.ToString("F01", ci))         ' Displays "F: 8395.0"    
      Console.WriteLine("G: {0}", _ 
              integral.ToString("G", ci))           ' Displays "G: 8395"
      Console.WriteLine("N: {0}", _
              integral.ToString("N01", ci))         ' Displays "N: 8,395.0"
      Console.WriteLine("P: {0}", _
              (integral/10000).ToString("P02", ci)) ' Displays "P: 83.95 %"
      Console.WriteLine("X: 0x{0}", _
              integral.ToString("X", ci))           ' Displays "X: 0x20CB"
      Console.WriteLine()
   End Sub
End Module
```

## <a name="see-also"></a>참고 항목

[System.Globalization.NumberFormatInfo](xref:System.Globalization.NumberFormatInfo)

[사용자 지정 숫자 서식 문자열](custom-numeric.md)

[형식 서식 지정](formatting-types.md)

[방법: 숫자 앞에 0으로 채우기](pad-number.md)

[복합 서식 지정](composite-format.md)



<!--HONumber=Nov16_HO3-->


