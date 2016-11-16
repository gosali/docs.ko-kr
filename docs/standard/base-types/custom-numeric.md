---
title: "사용자 지정 숫자 형식 문자열"
description: "사용자 지정 숫자 형식 문자열"
keywords: .NET, .NET Core
author: stevehoag
ms.author: shoag
manager: wpickett
ms.date: 07/25/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 7b1c16ee-956f-4e9c-8502-c3dd6598c51d
translationtype: Human Translation
ms.sourcegitcommit: b20713600d7c3ddc31be5885733a1e8910ede8c6
ms.openlocfilehash: e36c0117f6f011589299fa59a8abd139870c2257

---

# <a name="custom-numeric-format-strings"></a>사용자 지정 숫자 형식 문자열

하나 이상의 사용자 지정 숫자 서식 지정자로 구성된 사용자 지정 숫자 서식 문자열을 만들어 숫자 데이터의 서식을 지정하는 방법을 정의할 수 있습니다. 사용자 지정 숫자 형식 문자열은 [표준 숫자 형식 문자열](standard-numeric.md)이 아닌 모든 형식 문자열입니다. 

사용자 지정 숫자 서식 문자열은 모든 숫자 형식의 `ToString` 메서드를 오버로드하여 사용할 수 있습니다. 예를 들어 [Int32](xref:System.Int32) 형식의 [ToString(String)](xref:System.Int32.ToString(System.String)) 및 [ToString(String, IFormatProvider)](xref:System.Int32.ToString(System.String,System.IFormatProvider)) 메서드에 숫자 형식 문자열을 제공할 수 있습니다. [Console](xref:System.Console) 및 [StreamWriter](xref:System.IO.StreamWriter) 클래스의 일부 `Write` 및 `WriteLine` 메서드, [String.Format](xref:System.String.Format(System.IFormatProvider,System.String,System.Object)) 메서드, [StringBuilder.AppendFormat](xref:System.Text.StringBuilder.AppendFormat(System.IFormatProvider,System.String,System.Object)) 메서드에서 사용되는 .NET Framework [복합 형식 지정](composite-format.md) 기능에서도 사용자 지정 숫자 형식 문자열을 지원합니다.

다음 표에서는 사용자 지정 숫자 서식 지정자 및 각 서식 지정자로 생성되는 샘플 출력을 보여 줍니다. 사용자 지정 숫자 형식 문자열을 사용하는 방법에 대한 추가 정보는 [참고](#notes) 섹션을 참조하고, 이러한 사용을 자세히 보여 주는 예제는 [예제](#example) 섹션을 참조하세요.

형식 지정자 | 이름 | 설명 | 예제
---------------- | ---- | ----------- | --------
"0" | 0 자리 표시자 | 해당 숫자가 있을 경우 0을 해당 숫자로 바꾸고, 그렇지 않으면 결과 문자열에 0을 표시합니다. | `1234.5678 ("00000") -> 01235`; `0.45678 ("0.00", en-US) -> 0.46`; `0.45678 ("0.00", fr-FR) -> 0,46`
"#" | 10진수 자리 표시자 | 해당 숫자가 있을 경우 "#" 기호를 해당 숫자로 바꾸고, 그렇지 않으면 결과 문자열에 숫자를 표시하지 않습니다. 입력 문자열의 해당 숫자가 의미 없는 0인 경우 결과 문자열에 숫자를 표시하지 않습니다. 예를 들어 0003 ("####") -> 3입니다. | `1234.5678 ("#####") -> 1235`; `0.45678 ("#.##", en-US) -> .46`; `0.45678 ("#.##", fr-FR) -> ,46`
"." | 소수점 | 결과 문자열에서 소수 구분 기호의 위치를 결정합니다. | `0.45678 ("0.00", en-US) -> 0.46`; `0.45678 ("0.00", fr-FR) -> 0,46`
"," | 그룹 구분 기호 및 숫자 배율 | 그룹 구분 기호 지정자와 숫자 크기 조정 지정자로 모두 사용됩니다. 그룹 구분 기호로 사용될 경우 각 그룹 사이에 지역화된 그룹 구분 기호 문자를 삽입합니다. 숫자 크기 조정 지정자로 사용될 경우 숫자를 쉼표 단위로 끊어서 1000으로 나눕니다. | 그룹 구분 기호 지정자: `2147483647 ("##,#", en-US) -> 2,147,483,647`; `2147483647 ("##,#", es-ES) -> 2.147.483.647`. 크기 조정 지정자: `2147483647 ("#,#,,", en-US) -> 2,147`; `2147483647 ("#,#,,", es-ES) -> 2.147`
"%" | 백분율 자리 표시자 | 숫자에 100을 곱하고 결과 문자열에 지역화된 백분율 기호를 삽입합니다. | `0.3697 ("%#0.00", en-US) -> %36.97`; `0.3697 ("%#0.00", el-GR) -> %36,97`; `0.3697 ("##.0 %", en-US) -> 37.0 %`; `0.3697 ("##.0 %", el-GR) -> 37,0 %`
"‰" | 천분율 자리 표시자 | 숫자에 1000을 곱하고 결과 문자열에 지역화된 천분율 기호를 삽입합니다. | `0.03697 ("#0.00‰", en-US) -> 36.97‰`; `0.03697 ("#0.00‰", ru-RU) -> 36,97‰`
"E0", "E+0", "E-0", "e0", "e+0", "e-0" | 지수 표기법 | 적어도 하나의 0이 뒤에 오면 지수 표기법을 사용하여 결과의 서식을 지정합니다. "E" 또는 "e" 문자는 결과 문자열에 표시되는 지수 기호의 대/소문자를 나타냅니다. "E" 또는 "e" 문자 뒤에 오는 0의 수에 따라 지수의 최소 자릿수가 결정됩니다. 더하기 기호(+)는 기호 문자가 항상 지수 앞에 온다는 것을 나타냅니다. 빼기 기호(-)는 기호 문자가 음의 지수 앞에만 온다는 것을 나타냅니다. | `987654 ("#0.0e0") -> 98.8e4`; `1503.92311 ("0.0##e+00") -> 1.504e+03`; `1.8901385E-16 ("0.0e+00") -> 1.9e-16`
\ | 이스케이프 문자 | 뒤에 오는 문자가 사용자 지정 형식 지정자가 아닌 리터럴로 해석되도록 합니다. | `987654 ("\###00\#") -> #987654#`
'string', "string" | 리터럴 문자열 구분 기호 | 괄호로 묶인 문자가 변경되지 않은 상태로 결과 문자열에 복사되어야 함을 나타냅니다. | `68 ("# ' degrees'") -> 68 degrees`
; | 섹션 구분 기호 | 양수, 음수 및 0에 따라 별도의 서식 문자열을 사용하여 섹션을 정의합니다. | `12.345 ("#0.0#;(#0.0#);-\0-") -> 12.35`; `0 ("#0.0#;(#0.0#);-\0-") -> -0-`; `-12.345 ("#0.0#;(#0.0#);-\0-") -> (12.35)`; `12.345 ("#0.0#;(#0.0#)") -> 12.35`; `0 ("#0.0#;(#0.0#)") -> 0.0 ; -12.345 ("#0.0#;(#0.0#)") -> (12.35)`
기타 | 다른 모든 문자 | 문자가 변경되지 않은 상태로 결과 문자열에 복사됩니다. | `68 ("# °") -> 68 °`

다음 단원에서는 각 사용자 지정 숫자 서식 지정자에 대해 자세히 설명합니다.

## <a name="the-0-custom-specifier"></a>"0" 사용자 지정 지정자

"0" 사용자 지정 지정자는 0 자리 표시자 기호로 사용됩니다. 서식을 지정할 값이 서식 문자열의 0이 표시된 위치에 숫자를 가지고 있으면 해당 숫자가 결과 문자열로 복사되고, 그렇지 않으면 결과 문자열에 0이 표시됩니다. 소수점 앞 가장 왼쪽의 0과 소수점 뒤 가장 오른쪽 0의 위치는 결과 문자열에 항상 표시될 자릿수의 범위를 결정합니다. 

"00" 지정자를 사용하면 해당 값이 소수점 뒤 첫째 자리에서 반올림되며 항상 0 이상의 정수 값으로 표시됩니다. 예를 들어, 34.5의 서식을 "00"으로 지정하면 결과는 35가 됩니다.

다음 예제에서는 0 자리 표시자가 포함된 사용자 지정 서식 문자열을 사용하여 여러 가지 값을 표시합니다.

```csharp
double value;

value = 123;
Console.WriteLine(value.ToString("00000"));
Console.WriteLine(String.Format("{0:00000}", value));
// Displays 00123

value = 1.2;
Console.WriteLine(value.ToString("0.00", CultureInfo.InvariantCulture));
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                "{0:0.00}", value));
// Displays 1.20

Console.WriteLine(value.ToString("00.00", CultureInfo.InvariantCulture));
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:00.00}", value));
// Displays 01.20

CultureInfo daDK = CultureInfo.CreateSpecificCulture("da-DK");
Console.WriteLine(value.ToString("00.00", daDK)); 
Console.WriteLine(String.Format(daDK, "{0:00.00}", value));
// Displays 01,20

value = .56;
Console.WriteLine(value.ToString("0.0", CultureInfo.InvariantCulture));
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:0.0}", value));
// Displays 0.6

value = 1234567890;
Console.WriteLine(value.ToString("0,0", CultureInfo.InvariantCulture)); 
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:0,0}", value)); 
// Displays 1,234,567,890      

CultureInfo elGR = CultureInfo.CreateSpecificCulture("el-GR");
Console.WriteLine(value.ToString("0,0", elGR)); 
Console.WriteLine(String.Format(elGR, "{0:0,0}", value));   
// Displays 1.234.567.890

value = 1234567890.123456;
Console.WriteLine(value.ToString("0,0.0", CultureInfo.InvariantCulture));   
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:0,0.0}", value));   
// Displays 1,234,567,890.1  

value = 1234.567890;
Console.WriteLine(value.ToString("0,0.00", CultureInfo.InvariantCulture));  
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:0,0.00}", value));  
// Displays 1,234.57
```

```vb
Dim value As Double

value = 123
Console.WriteLine(value.ToString("00000"))
Console.WriteLine(String.Format("{0:00000}", value))
' Displays 00123

value = 1.2
Console.Writeline(value.ToString("0.00", CultureInfo.InvariantCulture))
Console.Writeline(String.Format(CultureInfo.InvariantCulture, 
                  "{0:0.00}", value))
' Displays 1.20
Console.WriteLine(value.ToString("00.00", CultureInfo.InvariantCulture))
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:00.00}", value))
' Displays 01.20
Dim daDK As CultureInfo = CultureInfo.CreateSpecificCulture("da-DK")
Console.WriteLine(value.ToString("00.00", daDK))
Console.WriteLine(String.Format(daDK, "{0:00.00}", value))
' Displays 01,20

value = .56
Console.WriteLine(value.ToString("0.0", CultureInfo.InvariantCulture))
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:0.0}", value))
' Displays 0.6

value = 1234567890
Console.WriteLine(value.ToString("0,0", CultureInfo.InvariantCulture))  
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:0,0}", value))  
' Displays 1,234,567,890      
Dim elGR As CultureInfo = CultureInfo.CreateSpecificCulture("el-GR")
Console.WriteLine(value.ToString("0,0", elGR))
Console.WriteLine(String.Format(elGR, "{0:0,0}", value))    
' Displays 1.234.567.890

value = 1234567890.123456
Console.WriteLine(value.ToString("0,0.0", CultureInfo.InvariantCulture))    
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:0,0.0}", value))    
' Displays 1,234,567,890.1  

value = 1234.567890
Console.WriteLine(value.ToString("0,0.00", CultureInfo.InvariantCulture))   
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:0,0.00}", value))   
' Displays 1,234.57
```

## <a name="the-custom-specifier"></a>"#" 사용자 지정 지정자

"#" 사용자 지정 지정자는 숫자 표시자 기호로 사용됩니다. 서식을 지정할 값이 서식 문자열의 "#" 기호가 표시된 위치에 숫자를 가지고 있으면 해당 숫자가 결과 문자열로 복사되고, 그렇지 않으면 결과 문자열의 해당 위치에 아무 것도 저장되지 않습니다. 

0이 유효 자릿수가 아니면 이 지정자는 문자열에서 0이 유일한 숫자라 할지라도 0을 표시하지 않습니다. 표시되는 숫자에서 0이 유효 자릿수이면 이 지정자는 0을 표시합니다. 

"##" 서식 문자열을 사용하면 해당 값이 소수점 뒤 첫째 자리에서 반올림되며 항상 0 이상의 정수로 표시됩니다. 예를 들어, 34.5의 서식을 "##"으로 지정하면 결과는 35가 됩니다.

다음 예제에서는 숫자 자리 표시자가 포함된 사용자 지정 서식 문자열을 사용하여 여러 가지 값을 표시합니다.

```csharp
double value;

value = 1.2;
Console.WriteLine(value.ToString("#.##", CultureInfo.InvariantCulture));
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:#.##}", value));
// Displays 1.2

value = 123;
Console.WriteLine(value.ToString("#####"));
Console.WriteLine(String.Format("{0:#####}", value));
// Displays 123

value = 123456;
Console.WriteLine(value.ToString("[##-##-##]"));      
Console.WriteLine(String.Format("{0:[##-##-##]}", value));      
// Displays [12-34-56]

value = 1234567890;
Console.WriteLine(value.ToString("#"));
Console.WriteLine(String.Format("{0:#}", value));
// Displays 1234567890

Console.WriteLine(value.ToString("(###) ###-####"));
Console.WriteLine(String.Format("{0:(###) ###-####}", value));
// Displays (123) 456-7890
```

```vb
Dim value As Double

value = 1.2
Console.WriteLine(value.ToString("#.##", CultureInfo.InvariantCulture))
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:#.##}", value))
' Displays 1.2

value = 123
Console.WriteLine(value.ToString("#####"))
Console.WriteLine(String.Format("{0:#####}", value))
' Displays 123

value = 123456
Console.WriteLine(value.ToString("[##-##-##]"))      
Console.WriteLine(String.Format("{0:[##-##-##]}", value))      
' Displays [12-34-56]

value = 1234567890
Console.WriteLine(value.ToString("#"))
Console.WriteLine(String.Format("{0:#}", value))
' Displays 1234567890

Console.WriteLine(value.ToString("(###) ###-####"))
Console.WriteLine(String.Format("{0:(###) ###-####}", value))
' Displays (123) 456-7890
```

빈 숫자나 선행 0이 공백으로 대체되는 결과 문자열을 반환하려면 다음 예제와 같이 [복합 형식 지정](composite-format.md) 기능을 사용하고 필드 너비를 지정합니다.

```csharp
using System;

public class Example
{
   public static void Main()
   {
      Double value = .324;
      Console.WriteLine("The value is: '{0,5:#.###}'", value);
   }
}
// The example displays the following output if the current culture
// is en-US:
//      The value is: ' .324'
```

```vb
Module Example
   Public Sub Main()
      Dim value As Double = .324
      Console.WriteLine("The value is: '{0,5:#.###}'", value)
   End Sub
End Module
' The example displays the following output if the current culture
' is en-US:
'      The value is: ' .324'
```

## <a name="the-custom-specifier"></a>"." 사용자 지정 지정자

"." 사용자 지정 서식 지정자는 결과 문자열에 지역화된 소수 구분 기호를 삽입합니다. 서식 문자열의 첫째 마침표 문자는 서식이 지정될 값에서 소수 구분 기호의 위치를 결정하며, 다른 마침표 문자는 무시됩니다. 

결과 문자열에서 소수 구분 기호로 사용되는 문자가 항상 마침표는 아니며 형식 지정을 제어하는 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 개체의 [NumberDecimalSeparator](xref:System.Globalization.NumberFormatInfo.NumberDecimalSeparator) 속성에서 결정됩니다.

다음 예제에서는 "." 서식 지정자를 사용하여 여러 결과 문자열에서 소수점의 위치를 정의합니다.

```csharp
double value;

value = 1.2;
Console.WriteLine(value.ToString("0.00", CultureInfo.InvariantCulture));
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:0.00}", value));
// Displays 1.20

Console.WriteLine(value.ToString("00.00", CultureInfo.InvariantCulture));
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:00.00}", value));
// Displays 01.20

Console.WriteLine(value.ToString("00.00", 
                CultureInfo.CreateSpecificCulture("da-DK")));
Console.WriteLine(String.Format(CultureInfo.CreateSpecificCulture("da-DK"),
                "{0:00.00}", value));
// Displays 01,20

value = .086;
Console.WriteLine(value.ToString("#0.##%", CultureInfo.InvariantCulture)); 
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:#0.##%}", value)); 
// Displays 8.6%

value = 86000;
Console.WriteLine(value.ToString("0.###E+0", CultureInfo.InvariantCulture));
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                "{0:0.###E+0}", value));
// Displays 8.6E+4
```

```vb
Dim value As Double

  value = 1.2
  Console.Writeline(value.ToString("0.00", CultureInfo.InvariantCulture))
  Console.Writeline(String.Format(CultureInfo.InvariantCulture, 
                                  "{0:0.00}", value))
  ' Displays 1.20

  Console.WriteLine(value.ToString("00.00", CultureInfo.InvariantCulture))
  Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                  "{0:00.00}", value))
  ' Displays 01.20

  Console.WriteLine(value.ToString("00.00", _
                    CultureInfo.CreateSpecificCulture("da-DK")))
  Console.WriteLine(String.Format(CultureInfo.CreateSpecificCulture("da-DK"),
                    "{0:00.00}", value))
  ' Displays 01,20

  value = .086
  Console.WriteLine(value.ToString("#0.##%", CultureInfo.InvariantCulture)) 
  Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                  "{0:#0.##%}", value)) 
  ' Displays 8.6%

  value = 86000
  Console.WriteLine(value.ToString("0.###E+0", CultureInfo.InvariantCulture))
  Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                    "{0:0.###E+0}", value))
' Displays 8.6E+4
```

## <a name="the-custom-specifier"></a>"," 사용자 지정 지정자

"," 문자는 그룹 구분 기호 지정자와 숫자 크기 조정 지정자로 사용됩니다. 

* 그룹 구분 기호 지정자: 두 개의 10진수 자리 표시자(0 또는 #) 사이에 정수 계열 자릿수의 서식을 지정하는 하나 이상의 쉼표 문자가 지정된 경우 정수 계열 출력 부분의 각 숫자 그룹 사이에 그룹 구분 문자가 삽입됩니다. 

  현재 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 개체의 [NumberGroupSeparator](xref:System.Globalization.NumberFormatInfo.NumberGroupSeparator) 및 [NumberGroupSizes](xref:System.Globalization.NumberFormatInfo.NumberGroupSizes) 속성은 숫자 그룹 구분 기호로 사용되는 문자와 각 숫자 그룹의 크기를 결정합니다. 예를 들어, 문자열 "#,#"과 고정 문화권을 사용하여 숫자 1000의 서식을 지정할 경우 "1,000"이 출력됩니다.
  
* 숫자 크기 조정 지정자: 명시적 또는 암시적 소수점의 바로 왼쪽에 하나 이상의 쉼표가 지정된 경우 서식이 지정될 숫자는 쉼표 단위로 끊어서 1000으로 나뉩니다. 예를 들어, 문자열 "0,,"을 사용하여 숫자 100000000의 서식을 지정할 경우 "100"이 출력됩니다. 

동일한 서식 문자열에 그룹 구분 기호와 숫자 크기 조정 지정자를 함께 사용할 수 있습니다. 예를 들어, 문자열 "#,0,,"과 고정 문화권을 사용하여 숫자 1000000000의 서식을 지정할 경우 "1,000"이 출력됩니다. 

다음 예제에서는 쉼표를 그룹 구분 기호로 사용하는 방법을 보여 줍니다.

```csharp
double value = 1234567890;
Console.WriteLine(value.ToString("#,#", CultureInfo.InvariantCulture));
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:#,#}", value));
// Displays 1,234,567,890      

Console.WriteLine(value.ToString("#,##0,,", CultureInfo.InvariantCulture));
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:#,##0,,}", value));
// Displays 1,235
```

```vb
Dim value As Double = 1234567890
Console.WriteLine(value.ToString("#,#", CultureInfo.InvariantCulture))
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:#,#}", value))
' Displays 1,234,567,890      

Console.WriteLine(value.ToString("#,##0,,", CultureInfo.InvariantCulture))
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:#,##0,,}", value))
' Displays 1,235
```

다음 예제에서는 쉼표를 숫자 크기 조정 지정자로 사용하는 방법을 보여 줍니다.

```csharp
double value = 1234567890;
Console.WriteLine(value.ToString("#,,", CultureInfo.InvariantCulture)); 
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:#,,}", value)); 
// Displays 1235   

Console.WriteLine(value.ToString("#,,,", CultureInfo.InvariantCulture));
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:#,,,}", value));
// Displays 1  

Console.WriteLine(value.ToString("#,##0,,", CultureInfo.InvariantCulture));       
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:#,##0,,}", value));       
// Displays 1,235
```  

```vb
Dim value As Double = 1234567890
  Console.WriteLine(value.ToString("#,,", CultureInfo.InvariantCulture))    
  Console.WriteLine(String.Format(CultureInfo.InvariantCulture, "{0:#,,}", value))  
  ' Displays 1235   

  Console.WriteLine(value.ToString("#,,,", CultureInfo.InvariantCulture))
  Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                  "{0:#,,,}", value))
' Displays 1  

  Console.WriteLine(value.ToString("#,##0,,", CultureInfo.InvariantCulture))       
  Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                  "{0:#,##0,,}", value))       
' Displays 1,235
```

## <a name="the-custom-specifier"></a>"%" 사용자 지정 지정자

서식 문자열에 백분율 기호(%)가 있으면 서식이 지정되기 전에 해당 수에 100이 곱해집니다. 서식 문자열에서 %가 표시된 위치에 있는 숫자에는 지역화된 백분율 기호가 삽입됩니다. 사용되는 퍼센트 문자는 현재 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 개체의 [PercentSymbol](xref:System.Globalization.NumberFormatInfo.PercentSymbol) 속성에 의해 정의됩니다.

다음 예제에서는 "%" 사용자 지정 지정자가 포함된 여러 가지 사용자 지정 서식 문자열을 정의합니다.

```csharp
double value = .086;
Console.WriteLine(value.ToString("#0.##%", CultureInfo.InvariantCulture));
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:#0.##%}", value));
// Displays 8.6%     
```

```vb
Dim value As Double = .086
Console.WriteLine(value.ToString("#0.##%", CultureInfo.InvariantCulture))
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:#0.##%}", value))
' Displays 8.6% 
```

## <a name="the-custom-specifier"></a>"‰" 사용자 지정 지정자

서식 문자열에 천분율 문자(‰ 또는 \u2030)가 있으면 서식이 지정되기 전에 해당 수에 1000이 곱해집니다. 서식 문자열에서 ‰가 표시된 위치에 있는 반환된 문자열에는 적절한 천분율 기호가 삽입됩니다. 사용되는 1000분의 1 문자는 문화권별 형식 지정 정보를 제공하는 개체의 [NumberFormatInfo.PerMilleSymbol](xref:System.Globalization.NumberFormatInfo.PerMilleSymbol) 속성에서 정의됩니다.

다음 예제에서는 "‰" 사용자 지정 지정자가 포함된 사용자 지정 서식 문자열을 정의합니다.

```csharp
double value = .00354;
string perMilleFmt = "#0.## " + '\u2030';
Console.WriteLine(value.ToString(perMilleFmt, CultureInfo.InvariantCulture));
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:" + perMilleFmt + "}", value));
// Displays 3.54‰   
```

```vb
Dim value As Double = .00354
Dim perMilleFmt As String = "#0.## " & ChrW(&h2030)
Console.WriteLine(value.ToString(perMilleFmt, CultureInfo.InvariantCulture))
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:" + perMilleFmt + "}", value))
' Displays 3.54 ‰
```

## <a name="the-e-and-e-custom-specifiers"></a>"E" 및 "e" 사용자 지정 지정자

서식 문자열에 "E", "E+", "E-", "e", "e+" 또는 "e-" 문자열이 표시되고 바로 뒤에 적어도 하나의 0이 오면, 해당 수와 지수 사이에 "E" 또는 "e"가 삽입되는 과학적 표기법으로 서식이 지정됩니다. 과학적 표기법 표시기 뒤에 오는 0의 개수는 이 숫자의 지수로 나타낼 최소 자릿수를 결정합니다. "E+" 및 "e+" 서식은 더하기 기호나 빼기 기호가 항상 지수 앞에 와야 한다는 것을 나타냅니다. "E", "E-", "e" 또는 "e-" 서식은 기호 문자가 음의 지수 앞에만 와야 한다는 것을 나타냅니다.

다음 예제에서는 과학적 표기법 지정자를 사용하여 여러 가지 숫자 값에 서식을 지정합니다.

```csharp
double value = 86000;
Console.WriteLine(value.ToString("0.###E+0", CultureInfo.InvariantCulture));
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:0.###E+0}", value));
// Displays 8.6E+4

Console.WriteLine(value.ToString("0.###E+000", CultureInfo.InvariantCulture));
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:0.###E+000}", value));
// Displays 8.6E+004

Console.WriteLine(value.ToString("0.###E-000", CultureInfo.InvariantCulture));
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:0.###E-000}", value));
// Displays 8.6E004
```

```vb
Dim value As Double = 86000
Console.WriteLine(value.ToString("0.###E+0", CultureInfo.InvariantCulture))
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:0.###E+0}", value))
' Displays 8.6E+4

Console.WriteLine(value.ToString("0.###E+000", CultureInfo.InvariantCulture))
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:0.###E+000}", value))
' Displays 8.6E+004

Console.WriteLine(value.ToString("0.###E-000", CultureInfo.InvariantCulture))
Console.WriteLine(String.Format(CultureInfo.InvariantCulture, 
                                "{0:0.###E-000}", value))
' Displays 8.6E004
```

## <a name="the-escape-character"></a>"\" 이스케이프 문자

서식 문자열의 "#", "0", ".", ",", "%" 및 "‰" 기호는 리터럴 문자가 아닌 서식 지정자로 해석됩니다. 사용자 지정 서식 문자열에서 이러한 문자가 있는 위치에 따라 대문자 및 소문자 "E"와 + 및 - 기호도 서식 지정자로 해석될 수 있습니다. 

문자가 서식 지정자로 해석되지 않도록 하려면 해당 문자 앞에 이스케이프 문자인 백슬래시를 삽입하면 됩니다. 이스케이프 문자는 뒤에 오는 문자가 변경되지 않은 상태로 결과 문자열에 포함되어야 하는 문자 리터럴임을 나타냅니다.

결과 문자열에 백슬래시를 포함하려면 \\처럼 두 개의 백슬래시를 연속해서 입력해야 합니다. 

> [!NOTE]
> C# 컴파일러 같은 일부 컴파일러에서는 하나의 백슬래시 문자가 이스케이프 문자로 해석될 수도 있습니다. 형식을 지정할 때 문자열이 올바로 해석되도록 하려면 해당 문자열 앞에 축자 문자열 리터럴 문자(@ 문자)를 사용하거나(C#의 경우) 각 백슬래시 앞에 또 다른 백슬래시 문자를 추가하면 됩니다. 다음 C# 예제에서는 이 두 가지 방법을 모두 보여 줍니다.

다음 예제에서는 이스케이프 문자를 사용하여 형식 지정 작업에서 "#", "0" 및 "\" 문자가 이스케이프 문자나 형식 지정자로 해석되지 않도록 합니다. 예제에서는 추가 백슬래시를 사용하여 백슬래시가 리터럴 문자로 해석되도록 합니다.

```csharp
int value = 123;
Console.WriteLine(value.ToString("\\#\\#\\# ##0 dollars and \\0\\0 cents \\#\\#\\#"));
Console.WriteLine(String.Format("{0:\\#\\#\\# ##0 dollars and \\0\\0 cents \\#\\#\\#}",
                                value));
// Displays ### 123 dollars and 00 cents ###

Console.WriteLine(value.ToString(xref:"\#\#\# ##0 dollars and \0\0 cents \#\#\#"));
Console.WriteLine(String.Format(xref:"{0:\#\#\# ##0 dollars and \0\0 cents \#\#\#}",
                                value));
// Displays ### 123 dollars and 00 cents ###

Console.WriteLine(value.ToString("\\\\\\\\\\\\ ##0 dollars and \\0\\0 cents \\\\\\\\\\\\"));
Console.WriteLine(String.Format("{0:\\\\\\\\\\\\ ##0 dollars and \\0\\0 cents \\\\\\\\\\\\}",
                                value));
// Displays \\\ 123 dollars and 00 cents \\\

Console.WriteLine(value.ToString(xref:"\\\\\\ ##0 dollars and \0\0 cents \\\\\\"));
Console.WriteLine(String.Format(xref:"{0:\\\\\\ ##0 dollars and \0\0 cents \\\\\\}",
                                value));
// Displays \\\ 123 dollars and 00 cents \\\
```

```vb
Dim value As Integer = 123
Console.WriteLine(value.ToString("\#\#\# ##0 dollars and \0\0 cents \#\#\#"))
Console.WriteLine(String.Format("{0:\#\#\# ##0 dollars and \0\0 cents \#\#\#}", 
                                value))
' Displays ### 123 dollars and 00 cents ###

Console.WriteLine(value.ToString("\\\\\\ ##0 dollars and \0\0 cents \\\\\\"))
Console.WriteLine(String.Format("{0:\\\\\\ ##0 dollars and \0\0 cents \\\\\\}", 
                                value))
' Displays \\\ 123 dollars and 00 cents \\\
```

## <a name="the-section-separator"></a>";" 섹션 구분 기호

세미콜론(;)은 해당 값이 양수인지 여부에 따라 서로 다른 서식을 숫자에 적용하는 조건부 서식 지정자입니다. 이렇게 하려면 사용자 지정 서식 문자열에 세미콜론으로 구분된 최대 세 개의 섹션이 포함되어야 합니다. 다음 표에서는 이러한 섹션에 대해 설명합니다. 

섹션 수 | 설명
------------------ | -----------
한 섹션 | 형식 문자열이 모든 값에 적용됩니다.
두 섹션 | 첫째 섹션은 양수 값과 0에 적용되고, 둘째 섹션은 음수 값에 적용됩니다. 서식을 지정할 수가 음수였는데 둘째 섹션의 서식에 따라 반올림한 후에 0이 된 경우, 결과값 0은 첫째 섹션에 따라 서식이 지정됩니다.
세 섹션 | 첫째 섹션은 양수 값에 적용되고, 둘째 섹션은 음수 값에 적용되며, 셋째 섹션은 0에 적용됩니다. 세미콜론 사이에 아무 것도 없어서 둘째 섹션이 비어 있는 경우에는 첫째 섹션을 0이 아닌 모든 값에 적용합니다. 서식을 지정할 수가 0이 아니었는데 첫째 또는 둘째 섹션의 서식에 따라 반올림한 후에 0이 된 경우, 결과값 0은 셋째 섹션에 따라 서식이 지정됩니다.

섹션 구분 기호는 마지막 값의 서식을 지정할 때 숫자와 연관된 기존 서식을 무시합니다. 예를 들어, 섹션 구분 기호가 사용되면 음수 값은 항상 빼기 기호 없이 표시됩니다. 마지막에 서식을 지정한 값에 빼기 기호를 붙이려면 빼기 기호를 사용자 지정 서식 지정자의 일부로 명시적으로 포함시켜야 합니다. 

다음 예제에서는 ";" 서식 지정자를 사용하여 양수, 음수 및 0의 서식을 각각 다르게 지정합니다.

```csharp
double posValue = 1234;
double negValue = -1234;
double zeroValue = 0;

string fmt2 = "##;(##)";
string fmt3 = "##;(##);**Zero**";

Console.WriteLine(posValue.ToString(fmt2));  
Console.WriteLine(String.Format("{0:" + fmt2 + "}", posValue));    
// Displays 1234

Console.WriteLine(negValue.ToString(fmt2));  
Console.WriteLine(String.Format("{0:" + fmt2 + "}", negValue));    
// Displays (1234)

Console.WriteLine(zeroValue.ToString(fmt3)); 
Console.WriteLine(String.Format("{0:" + fmt3 + "}", zeroValue));
// Displays **Zero**
```

```vb
Dim posValue As Double = 1234
Dim negValue As Double = -1234
Dim zeroValue As Double = 0

Dim fmt2 As String = "##;(##)"
Dim fmt3 As String = "##;(##);**Zero**"

Console.WriteLine(posValue.ToString(fmt2))   
Console.WriteLine(String.Format("{0:" + fmt2 + "}", posValue))    
' Displays 1234

Console.WriteLine(negValue.ToString(fmt2))   
Console.WriteLine(String.Format("{0:" + fmt2 + "}", negValue))    
' Displays (1234)

Console.WriteLine(zeroValue.ToString(fmt3))  
Console.WriteLine(String.Format("{0:" + fmt3 + "}", zeroValue))
' Displays **Zero**
```

## <a name="notes"></a>노트

### <a name="floatingpoint-infinities-and-nan"></a>부동 소수점 무한대 및 NaN

형식 문자열에 관계없이 [Single](xref:System.Single) 또는 [Double](xref:System.Double) 부동 소수점 형식의 값이 양의 무한대, 음의 무한대 또는 NaN(Not a Number)이면 형식이 지정된 문자열은 각각 현재 적용 가능한 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 개체에서 지정하는 해당 [PositiveInfinitySymbol](xref:System.Globalization.NumberFormatInfo.PositiveInfinitySymbol), [NegativeInfinitySymbol](xref:System.Globalization.NumberFormatInfo.NegativeInfinitySymbol) 또는 [NaNSymbol](xref:System.Globalization.NumberFormatInfo.NaNSymbol) 속성의 값입니다. 

### <a name="rounding-and-fixedpoint-format-strings"></a>반올림 및 고정 소수점 서식 문자열

고정 소수점 서식 문자열, 즉 과학적 표기법 서식 문자가 들어 있지 않은 서식 문자열의 경우 소수점 오른쪽에 있는 10진수 자리 표시자와 같은 개수의 소수 자릿수만큼 숫자가 반올림됩니다. 서식 문자열에 소수점이 없으면 숫자는 가장 근접한 정수로 반올림됩니다. 숫자의 자릿수가 소수점 왼쪽의 10진수 자리 표시자보다 많으면, 초과 숫자가 결과 문자열의 첫째 10진수 자리 표시자 바로 앞에 복사됩니다.

## <a name="example"></a>예제

다음 예제에서는 두 개의 사용자 지정 숫자 서식 문자열을 보여 줍니다. 두 경우 모두, 숫자 자리 표시자(#)는 숫자 데이터를 표시하고 다른 모든 문자는 결과 문자열에 복사됩니다.

```csharp
double number1 = 1234567890;
string value1 = number1.ToString("(###) ###-####");
Console.WriteLine(value1);

int number2 = 42;
string value2 = number2.ToString("My Number = #");
Console.WriteLine(value2);
// The example displays the following output:
//       (123) 456-7890
//       My Number = 42
```

```vb
Dim number1 As Double = 1234567890
Dim value1 As String = number1.ToString("(###) ###-####")
Console.WriteLine(value1)

Dim number2 As Integer = 42
Dim value2 As String = number2.ToString("My Number = #")
Console.WriteLine(value2)
' The example displays the following output:
'       (123) 456-7890
'       My Number = 42
```

## <a name="see-also"></a>참고 항목

[System.Globalization.NumberFormatInfo](xref:System.Globalization.NumberFormatInfo)

[형식 서식 지정](formatting-types.md)

[표준 숫자 서식 문자열](standard-numeric.md)

[방법: 숫자 앞에 0으로 채우기](pad-number.md)

[복합 서식 지정](composite-format.md)




<!--HONumber=Nov16_HO1-->


