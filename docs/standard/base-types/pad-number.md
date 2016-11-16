---
title: "방법: 숫자 앞에 0으로 채우기"
description: "방법: 숫자 앞에 0으로 채우기"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 07/26/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: a517c066-b11e-4815-826b-9262611eac40
translationtype: Human Translation
ms.sourcegitcommit: fb00da6505c9edb6a49d2003ae9bcb8e74c11d6c
ms.openlocfilehash: 991fd8a0e631b73a0a484ebbe71ac1189f37e1f6

---

# <a name="how-to-pad-a-number-with-leading-zeros"></a>방법: 숫자 앞에 0으로 채우기

"D" [표준 숫자 서식 문자열](standard-numeric.md)과 함께 전체 자릿수 지정자를 사용하여 앞에 오는 0을 정수에 추가할 수 있습니다. [사용자 지정 숫자 서식 문자열](custom-numeric.md)을 사용하여 정수와 부동 소수점 숫자 둘 다에 앞에 오는 0을 추가할 수 있습니다. 이 항목에서는 두 개의 메서드를 사용하여 앞에 오는 0으로 숫자를 채우는 방법을 보여 줍니다.

## <a name="to-pad-an-integer-with-leading-zeros-to-a-specific-length"></a>앞에 오는 0으로 특정 길이까지 정수를 채우려면

1. 표시할 정수 값의 최소 자릿수를 결정합니다. 이 숫자에 원하는 선행 자릿수를 포함합니다.

2. 정수를 10진수 값으로 표시할지 아니면 16진수 값으로 표시할지를 결정합니다.

    * 정수를 10진수 값으로 표시하려면 해당 `ToString(String)` 메서드를 호출하고 문자열 "D*n*"을 서식 매개 변수의 값으로 전달합니다. 여기서 *n*은 문자열의 최소 길이를 나타냅니다.
    
    * 정수를 16진수 값으로 표시하려면 해당 `ToString(String)` 메서드를 호출하고 문자열 "X*n*"을 서식 매개 변수의 값으로 전달합니다. 여기서 *n*은 문자열의 최소 길이를 나타냅니다.
    
  또한 [합성 서식 지정](composite-format.md)을 사용하는 메서드에 [Console.WriteLine](xref:System.Console.WriteLine) 또는 [String.Format](xref:System.String.Format(System.IFormatProvider,System.String,System.Object))과 같은 서식 문자열을 사용할 수 있습니다.  
  
다음 예제에서는 서식이 지정된 숫자의 전체 길이가 8자 이상이 되도록 앞에 오는 0으로 여러 정수 값의 서식을 지정합니다.

```csharp
byte byteValue = 254;
short shortValue = 10342;
int intValue = 1023983;
long lngValue = 6985321;               
ulong ulngValue = UInt64.MaxValue;

// Display integer values by calling the ToString method.
Console.WriteLine("{0,22} {1,22}", byteValue.ToString("D8"), byteValue.ToString("X8"));
Console.WriteLine("{0,22} {1,22}", shortValue.ToString("D8"), shortValue.ToString("X8"));
Console.WriteLine("{0,22} {1,22}", intValue.ToString("D8"), intValue.ToString("X8"));
Console.WriteLine("{0,22} {1,22}", lngValue.ToString("D8"), lngValue.ToString("X8"));
Console.WriteLine("{0,22} {1,22}", ulngValue.ToString("D8"), ulngValue.ToString("X8"));
Console.WriteLine();

// Display the same integer values by using composite formatting.
Console.WriteLine("{0,22:D8} {0,22:X8}", byteValue);
Console.WriteLine("{0,22:D8} {0,22:X8}", shortValue);
Console.WriteLine("{0,22:D8} {0,22:X8}", intValue);
Console.WriteLine("{0,22:D8} {0,22:X8}", lngValue);
Console.WriteLine("{0,22:D8} {0,22:X8}", ulngValue);
// The example displays the following output:
//                     00000254               000000FE
//                     00010342               00002866
//                     01023983               000F9FEF
//                     06985321               006A9669
//         18446744073709551615       FFFFFFFFFFFFFFFF
//       
//                     00000254               000000FE
//                     00010342               00002866
//                     01023983               000F9FEF
//                     06985321               006A9669
//         18446744073709551615       FFFFFFFFFFFFFFFF
//         18446744073709551615       FFFFFFFFFFFFFFFF
```

```vb
Dim byteValue As Byte = 254
Dim shortValue As Short = 10342
Dim intValue As Integer = 1023983
Dim lngValue As Long = 6985321               
Dim ulngValue As ULong = UInt64.MaxValue

' Display integer values by calling the ToString method.
Console.WriteLine("{0,22} {1,22}", byteValue.ToString("D8"), byteValue.ToString("X8"))
Console.WriteLine("{0,22} {1,22}", shortValue.ToString("D8"), shortValue.ToString("X8"))
Console.WriteLine("{0,22} {1,22}", intValue.ToString("D8"), intValue.ToString("X8"))
Console.WriteLine("{0,22} {1,22}", lngValue.ToString("D8"), lngValue.ToString("X8"))
Console.WriteLine("{0,22} {1,22}", ulngValue.ToString("D8"), ulngValue.ToString("X8"))
Console.WriteLine()

' Display the same integer values by using composite formatting.
Console.WriteLine("{0,22:D8} {0,22:X8}", byteValue)
Console.WriteLine("{0,22:D8} {0,22:X8}", shortValue)
Console.WriteLine("{0,22:D8} {0,22:X8}", intValue)
Console.WriteLine("{0,22:D8} {0,22:X8}", lngValue)
Console.WriteLine("{0,22:D8} {0,22:X8}", ulngValue)
' The example displays the following output:
'                     00000254               000000FE
'                     00010342               00002866
'                     01023983               000F9FEF
'                     06985321               006A9669
'         18446744073709551615       FFFFFFFFFFFFFFFF
'       
'                     00000254               000000FE
'                     00010342               00002866
'                     01023983               000F9FEF
'                     06985321               006A9669
'         18446744073709551615       FFFFFFFFFFFFFFFF
```

## <a name="to-pad-an-integer-with-a-specific-number-of-leading-zeros"></a>특정 수의 앞에 오는 0으로 정수를 채우려면

1. 정수 값을 표시하는 데 사용할 앞에 오는 0의 수를 결정합니다.

2. 정수를 10진수 값으로 표시할지 아니면 16진수 값으로 표시할지를 결정합니다. 10진수 값으로 서식을 지정하려면 "D" 표준 서식 지정자를 사용해야 하고, 16진수 값으로 서식을 지정하려면 "X" 표준 서식 지정자를 사용해야 합니다.

3. 정수 값의 `ToString("D").Length` 또는 `ToString("X").Length` 메서드를 호출하여 채워지지 않은 숫자 문자열의 길이를 확인합니다. 

4. 서식이 지정된 문자열에 포함할 앞에 오는 0의 수를 채워지지 않은 숫자 문자열의 길이에 추가합니다. 그러면 채워진 문자열의 전체 길이가 정의됩니다.

5. 정수 값의 `ToString(String)` 메서드를 호출하고, 10진수 문자열의 경우 문자열 "D*n*"을 전달하고 16진수 문자열의 경우 "X*n*"을 전달합니다. 여기서 *n*은 채워진 문자열의 전체 길이를 나타냅니다. 또한 합성 서식 지정을 지원하는 메서드에 "D*n*" 또는 "X*n*" 서식 문자열을 사용할 수 있습니다.

다음 예제에서는 5개의 앞에 오는 0으로 정수 값을 채웁니다.

```csharp
int value = 160934;
int decimalLength = value.ToString("D").Length + 5;
int hexLength = value.ToString("X").Length + 5;
Console.WriteLine(value.ToString("D" + decimalLength.ToString()));
Console.WriteLine(value.ToString("X" + hexLength.ToString()));
// The example displays the following output:
//       00000160934
//       00000274A6
```

```vb
Dim value As Integer = 160934
Dim decimalLength As Integer = value.ToString("D").Length + 5
Dim hexLength As Integer = value.ToString("X").Length + 5
Console.WriteLine(value.ToString("D" + decimalLength.ToString()))
Console.WriteLine(value.ToString("X" + hexLength.ToString()))
' The example displays the following output:
'       00000160934
'       00000274A6 
```

## <a name="to-pad-a-numeric-value-with-leading-zeros-to-a-specific-length"></a>앞에 오는 0으로 특정 길이까지 숫자 값을 채우려면

1. 숫자의 문자열 표현에서 정수 부분을 표시하는 데 사용할 자릿수를 결정합니다. 이 숫자의 전체 자릿수에 원하는 수의 앞에 오는 0을 포함합니다.

2. 0 자리 표시자("0")를 사용하여 0의 최소 수를 나타내는 사용자 지정 숫자 서식 문자열을 정의합니다.

3. 숫자의 `ToString(String)` 메서드를 호출하여 사용자 지정 서식 문자열에 전달합니다. 또한 합성 서식 지정을 지원하는 메서드에 사용자 지정 서식 문자열을 사용할 수 있습니다.

다음 예제에서는 서식이 지정된 숫자에서 정수 부분의 전체 길이가 8자 이상이 되도록 앞에 오는 0으로 여러 숫자 값의 서식을 지정합니다.

```csharp
string fmt = "00000000.##";
int intValue = 1053240;
decimal decValue = 103932.52m;
float sngValue = 1549230.10873992f;
double dblValue = 9034521202.93217412;

// Display the numbers using the ToString method.
Console.WriteLine(intValue.ToString(fmt));
Console.WriteLine(decValue.ToString(fmt));           
Console.WriteLine(sngValue.ToString(fmt));
Console.WriteLine(dblValue.ToString(fmt));           
Console.WriteLine();

// Display the numbers using composite formatting.
string formatString = " {0,15:" + fmt + "}";
Console.WriteLine(formatString, intValue);      
Console.WriteLine(formatString, decValue);      
Console.WriteLine(formatString, sngValue);      
Console.WriteLine(formatString, dblValue);      
// The example displays the following output:
//       01053240
//       00103932.52
//       01549230
//       9034521202.93
//       
//               01053240
//            00103932.52
//               01549230
//          9034521202.93  
```

```vb
Dim fmt As String = "00000000.##"
Dim intValue As Integer = 1053240
Dim decValue As Decimal = 103932.52d
Dim sngValue As Single = 1549230.10873992
Dim dblValue As Double = 9034521202.93217412

' Display the numbers using the ToString method.
Console.WriteLine(intValue.ToString(fmt))
Console.WriteLine(decValue.ToString(fmt))            
Console.WriteLine(sngValue.ToString(fmt))
Console.WriteLine(dblValue.ToString(fmt))            
Console.WriteLine()

' Display the numbers using composite formatting.
Dim formatString As String = " {0,15:" + fmt + "}"
Console.WriteLine(formatString, intValue)      
Console.WriteLine(formatString, decValue)      
Console.WriteLine(formatString, sngValue)      
Console.WriteLine(formatString, dblValue)      
' The example displays the following output:
'       01053240
'       00103932.52
'       01549230
'       9034521202.93
'       
'               01053240
'            00103932.52
'               01549230
'          9034521202.93
```

## <a name="to-pad-a-numeric-value-with-a-specific-number-of-leading-zeros"></a>특정 수의 앞에 오는 0으로 숫자 값을 채우려면

1. 숫자 값을 표시하는 데 사용할 앞에 오는 0의 수를 결정합니다.

2. 채워지지 않은 숫자 문자열에서 정수 부분의 자릿수를 확인합니다. 이렇게 하려면 다음을 수행합니다.

    a. 숫자의 문자열 표현에 소수점 기호가 포함되어 있는지 확인합니다.
    
    b. 소수점 기호가 포함되어 있으면 정수 부분에 있는 문자 수를 확인합니다.       
    
    또는
       
    소수점 기호가 포함되어 있지 않으면 문자열의 길이를 확인합니다. 
       
3. 문자열에 표시할 각 앞에 오는 0에 대해 0 자리 표시자("0")를 사용하고 0 자리 표시자 또는 10진수 자리 표시자("#")를 사용하여 기본 문자열에 있는 각 자릿수를 나타내는 사용자 지정 서식 문자열을 만듭니다. 

4. 사용자 지정 서식 문자열을 숫자의 ToString(String) 메서드에 대한 매개 변수 또는 합성 서식 지정을 지원하는 메서드에 대한 매개 변수로 제공합니다.

다음 예제에서는 5개의 앞에 오는 0으로 두 개의 [Double](xref:System.Double) 값을 채웁니다.

```csharp
double[] dblValues = { 9034521202.93217412, 9034521202 };
foreach (double dblValue in dblValues)
{
   string decSeparator = System.Globalization.NumberFormatInfo.CurrentInfo.NumberDecimalSeparator;
   string fmt, formatString;

   if (dblValue.ToString().Contains(decSeparator))
   {
      int digits = dblValue.ToString().IndexOf(decSeparator);
      fmt = new String('0', 5) + new String('#', digits) + ".##";
   }
   else
   {
      fmt = new String('0', dblValue.ToString().Length);   
   }
   formatString = "{0,20:" + fmt + "}";

   Console.WriteLine(dblValue.ToString(fmt));
   Console.WriteLine(formatString, dblValue);
}
// The example displays the following output:
//       000009034521202.93
//         000009034521202.93
//       9034521202
//                 9034521202 
```

```vb
Dim dblValues() As Double = { 9034521202.93217412, 9034521202 }
For Each dblValue As Double In dblValues
   Dim decSeparator As String = System.Globalization.NumberFormatInfo.CurrentInfo.NumberDecimalSeparator
   Dim fmt, formatString As String

   If dblValue.ToString.Contains(decSeparator) Then
      Dim digits As Integer = dblValue.ToString().IndexOf(decSeparator)
      fmt = New String("0"c, 5) + New String("#"c, digits) + ".##"
   Else
      fmt = New String("0"c, dblValue.ToString.Length)   
   End If
   formatString = "{0,20:" + fmt + "}"

   Console.WriteLine(dblValue.ToString(fmt))
   Console.WriteLine(formatString, dblValue)
Next
' The example displays the following output:
'       000009034521202.93
'         000009034521202.93
'       9034521202
'                 9034521202
```

## <a name="see-also"></a>참고 항목

[표준 숫자 서식 문자열](standard-numeric.md)

[사용자 지정 숫자 서식 문자열](custom-numeric.md)

[복합 서식 지정](composite-format.md)




<!--HONumber=Nov16_HO1-->


