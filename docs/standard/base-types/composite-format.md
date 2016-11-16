---
title: "복합 형식 지정"
description: "복합 형식 지정"
keywords: .NET, .NET Core
author: stevehoag
ms.author: shoag
manager: wpickett
ms.date: 07/25/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: a01efc8f-c242-4535-bd32-acd0032d9590
translationtype: Human Translation
ms.sourcegitcommit: b20713600d7c3ddc31be5885733a1e8910ede8c6
ms.openlocfilehash: 9bcf2ff74237f7e7faf2890849c2d68a0a602353

---

# <a name="composite-formatting"></a>복합 형식 지정

.NET의 복합 형식 지정 기능에는 개체 목록과 복합 형식 문자열이 입력으로 사용됩니다. 합성 서식 문자열은 고정 텍스트와 목록의 개체에 해당하는 인덱싱된 자리 표시자(서식 항목이라고 함)가 결합된 형태로 구성됩니다. 서식 지정 작업을 통해 원래의 고정 텍스트와 목록에 있는 개체의 문자열 표현이 결합된 형태의 결과 문자열을 얻을 수 있습니다. 

다음과 같은 메서드에서 합성 형식 지정 기능을 지원합니다. 

* [String.Format](xref:System.String.Format(System.IFormatProvider,System.String,System.Object)) - 형식이 지정된 결과 문자열을 반환합니다. 

* [StringBuilder.AppendFormat](xref:System.Text.StringBuilder.AppendFormat(System.IFormatProvider, System.String, System.Object) - 형식이 지정된 결과 문자열을 [StringBuilder](xref:System.Text.StringBuilder) 개체에 추가합니다.

* 콘솔에 형식이 지정된 결과 문자열을 표시하는 [Console](xref:System.Console) `WriteLine` 메서드의 오버로드.  

* 스트림 또는 파일에 형식이 지정된 결과 문자열을 쓰는 [TextWriter](xref:System.IO.TextWriter) `WriteLine` 메서드의 오버로드. [StreamWriter](xref:System.IO.StreamWriter) 등의 [TextWriter](xref:System.IO.TextWriter)에서 파생된 클래스도 이 기능을 공유합니다.

* [Debug.WriteLine(String, Object[])](xref:System.Diagnostics.Debug.WriteLine(System.String,System.Object[])) - 형식이 지정된 메시지를 추적 수신기에 출력합니다. 

* [Trace.TraceError(String, Object[])](xref:System.Diagnostics.Trace.TraceError(System.String,System.Object[])), [Trace.TraceInformation(String, Object[])](xref:System.Diagnostics.Trace.TraceInformation(System.String,System.Object[])) 및 [Trace.TraceWarning(String, Object[])](xref:System.Diagnostics.Trace.TraceWarning(System.String,System.Object[])) 메서드 - 형식이 지정된 메시지를 추적 수신기에 출력합니다. 

* [TraceSource.TraceInformation(String, Object[])](xref:System.Diagnostics.TraceSource.TraceInformation(System.String,System.Object[])) 메서드 - 정보 메서드를 추적 수신기에 씁니다. 

## <a name="composite-format-string"></a>합성 서식 문자열

합성 서식 문자열과 개체 목록은 합성 서식 지정 기능을 지원하는 메서드의 인수로 사용됩니다. 합성 서식 문자열은 0개 이상의 고정 텍스트가 하나 이상의 서식 항목과 결합된 형태로 구성됩니다. 고정 텍스트는 사용자가 선택하는 임의의 문자열이고, 각 서식 항목은 목록의 개체나 boxed 구조체에 해당합니다. 합성 서식 지정 기능은 각 서식 항목을 목록에 있는 해당 개체의 문자열 표현으로 바꿔 새로운 결과 문자열을 반환합니다.

다음과 같은 [Format](xref:System.String.Format(System.String.Format(System.IFormatProvider,System.String,System.Object)) 코드 조각을 가정해봅니다.

```csharp
string name = "Fred";
String.Format("Name = {0}, hours = {1:hh}", name, DateTime.Now);
```

```vb
Dim name As String = "Fred"
String.Format("Name = {0}, hours = {1:hh}", name, DateTime.Now)
```

고정 텍스트는 `"Name = "` 및 `", hours = "`입니다. 형식 항목은 인덱스가 0이고 `name` 개체에 해당하는 `"{0}"`과(와) 인덱스가 1이고 `DateTime.Now` 개체에 해당하는 `"{1:hh}"`입니다.

## <a name="format-item-syntax"></a>서식 항목 구문

각 서식 항목의 형태와 구성 요소는 다음과 같습니다.

__{__*index*[,*alignment*][:*formatString*]__}__

여기서 중괄호("{"와 "}")의 짝이 반드시 맞아야 합니다. 
 
### <a name="index-component"></a>Index 구성 요소

매개 변수 지정자라고도 하는 필수 *index* 구성 요소는 0부터 시작하는 숫자로, 개체 목록에서 해당하는 항목을 식별합니다. 즉, 매개 변수 지정자가 0인 서식 항목은 목록에 있는 첫째 개체의 서식을 지정하고 매개 변수 지정자가 1인 서식 항목은 목록에 있는 둘째 개체의 서식을 지정하는 식으로 적용됩니다. 다음 예제에는 10보다 작은 소수를 나타내고 0부터 3까지 번호가 매겨진 4개의 매개 변수 지정자가 포함되어 있습니다. 

```csharp
string primes;
primes = String.Format("Prime numbers less than 10: {0}, {1}, {2}, {3}",
                       2, 3, 5, 7 );
Console.WriteLine(primes);
// The example displays the following output:
//      Prime numbers less than 10: 2, 3, 5, 7
```

```vb
Dim primes As String
primes = String.Format("Prime numbers less than 10: {0}, {1}, {2}, {3}",
                       2, 3, 5, 7 )
Console.WriteLine(primes)
' The example displays the following output:
'      Prime numbers less than 10: 2, 3, 5, 7
```

동일한 매개 변수 지정자를 지정하여 여러 서식 항목이 개체 목록의 동일한 요소를 참조하도록 할 수 있습니다. 예를 들어 다음 예제와 같이 복합 형식 문자열을 "0x{0:X} {0:E} {0:N}"과 같이 지정하여 동일한 숫자 값을 16진수, 지수 및 숫자 형식으로 지정할 수 있습니다. 

```csharp
string multiple = String.Format("0x{0:X} {0:E} {0:N}",
                                Int64.MaxValue);
Console.WriteLine(multiple);
// The example displays the following output:
//      0x7FFFFFFFFFFFFFFF 9.223372E+018 9,223,372,036,854,775,807.00
```

```vb
Dim multiple As String = String.Format("0x{0:X} {0:E} {0:N}",
                                       Int64.MaxValue)
Console.WriteLine(multiple)
' The example displays the following output:
'      0x7FFFFFFFFFFFFFFF 9.223372E+018 9,223,372,036,854,775,807.00
```

각 서식 항목은 목록의 어떤 개체나 참조할 수 있습니다. 예를 들어, 세 개의 개체가 있을 경우 "{1} {0} {2}"와 같이 복합 서식 문자열을 지정하여 둘째, 첫째 및 셋째 개체의 서식을 지정할 수 있습니다. 서식 항목에서 참조하지 않는 개체는 무시됩니다. 매개 변수 지정자가 개체 목록 범위를 벗어나는 항목을 지정하면 런타임에 [FormatException](xref:System.FormatException)이 발생합니다.

### <a name="alignment-component"></a>Alignment 구성 요소

선택적인 *alignment* 구성 요소는 기본 형식의 필드 너비를 나타내는 부호 있는 정수입니다. *alignment* 값이 형식이 지정된 문자열보다 작으면 *alignment*는 무시되고 형식이 지정된 문자열의 길이가 필드 너비로 사용됩니다. *alignment*가 양수이면 필드에서 형식이 지정된 데이터가 오른쪽 맞춤되고 *alignment*가 음수이면 왼쪽 맞춤됩니다. 채우기가 필요하면 공백이 사용됩니다. *alignment*를 지정하는 경우 쉼표가 필요합니다.

다음 예제에서는 두 배열, 즉 직원의 이름을 포함하는 배열과 2주 동안의 작업 시간을 포함하는 배열을 정의합니다. 복합 형식 문자열은 20자 필드에 이름을 왼쪽 맞춤하고 5자 필드에 해당 시간을 오른쪽 맞춤합니다. 소수 1자리로 시간 형식을 지정하기 위해 "N1" 표준 형식 문자열도 사용됩니다. 

```csharp
using System;

public class Example
{
   public static void Main()
   {
      string[] names = { "Adam", "Bridgette", "Carla", "Daniel",
                         "Ebenezer", "Francine", "George" };
      decimal[] hours = { 40, 6.667m, 40.39m, 82, 40.333m, 80,
                                 16.75m };

      Console.WriteLine("{0,-20} {1,5}\n", "Name", "Hours");
      for (int ctr = 0; ctr < names.Length; ctr++)
         Console.WriteLine("{0,-20} {1,5:N1}", names[ctr], hours[ctr]);

   }
}
// The example displays the following output:
//       Name                 Hours
//
//       Adam                  40.0
//       Bridgette              6.7
//       Carla                 40.4
//       Daniel                82.0
//       Ebenezer              40.3
//       Francine              80.0
//       George                16.8
```

```vb
Module Example
   Public Sub Main()
      Dim names() As String = { "Adam", "Bridgette", "Carla", "Daniel",
                                "Ebenezer", "Francine", "George" }
      Dim hours() As Decimal = { 40, 6.667d, 40.39d, 82, 40.333d, 80,
                                 16.75d }

      Console.WriteLine("{0,-20} {1,5}", "Name", "Hours")
      Console.WriteLine()
      For ctr As Integer = 0 To names.Length - 1
         Console.WriteLine("{0,-20} {1,5:N1}", names(ctr), hours(ctr))
      Next
   End Sub
End Module
' The example displays the following output:
'       Name                 Hours
'
'       Adam                  40.0
'       Bridgette              6.7
'       Carla                 40.4
'       Daniel                82.0
'       Ebenezer              40.3
'       Francine              80.0
'       George                16.8
```

### <a name="format-string-component"></a>Format String 구성 요소

선택적 *formatString* 구성 요소는 서식을 지정할 개체 형식에 적절한 형식 문자열입니다. 해당 개체가 숫자 값이면 표준 또는 사용자 지정 숫자 형식 문자열을, [DateTime](xref:System.DateTime) 개체이면 표준 또는 사용자 지정 날짜 및 시간 형식 문자열을, 열거형 값이면 [열거형 형식 문자열](enumeration-format.md)을 지정합니다. *formatString*을 지정하지 않으면 숫자, 날짜 및 시간, 또는 열거형 형식에 대해 일반("G") 형식 지정자가 사용됩니다. *formatString*을 지정하는 경우 콜론이 필요합니다.

다음 표에는 미리 정의된 서식 문자열 집합을 지원하는 .NET Framework 클래스 라이브러리의 형식 또는 형식 범주와 지원되는 서식 문자열을 나열하는 항목에 대한 링크가 나와 있습니다. 문자열 서식 지정은 응용 프로그램 정의 형식에서 지원하는 형식 문자열 집합을 정의하는, 모든 기존 형식을 위한 새 형식 문자열을 정의하는 확장 가능한 메커니즘입니다. 자세한 내용은 [IFormattable](xref:System.IFormattable) 및 [ICustomFormatter](xref:System.ICustomFormatter) 인터페이스 항목을 참조하세요. 

형식 또는 형식 범주 | 참조
--------------------- | ---
날짜 및 시간 형식([DateTime](xref:System.DateTime), [DateTimeOffset](xref:System.DateTimeOffset)) | [표준 날짜 및 시간 서식 문자열](standard-datetime.md), [사용자 지정 날짜 및 시간 서식 문자열](custom-datetime.md)
열거형 형식([System.Enum](xref:System.Enum)에서 파생된 모든 형식) | [열거형 서식 문자열](enumeration-format.md)
숫자 형식([BigInteger](xref:System.Numerics.BigInteger), [Byte](xref:System.Byte), [Decimal](xref:System.Decimal), [Double](xref:System.Double), [Int16](xref:System.Int16), [Int32](xref:System.Int32), [Int64](xref:System.Int64), [SByte](xref:System.SByte), [Single](xref:System.Single), [UInt16](xref:System.UInt16), [UInt32](xref:System.UInt32), [UInt64](xref:System.UInt64)) | [표준 숫자 형식 문자열](standard-numeric.md), [사용자 지정 숫자 형식 문자열](custom-numeric.md)
[Guid](xref:System.Guid) | [Guid.ToString(String)](xref:System.Guid.ToString(System.String))
[TimeSpan](xref:System.TimeSpan) | [표준 TimeSpan 형식 문자열](standard-timespan.md), [사용자 지정 TimeSpan 형식 문자열](custom-timespan.md)

### <a name="escaping-braces"></a>이스케이프 중괄호

여는 중괄호와 닫는 중괄호는 서식 항목의 시작과 끝으로 해석됩니다. 따라서 리터럴 여는 중괄호나 닫는 중괄호를 표시하려면 이스케이프 시퀀스를 사용해야 합니다. 고정 텍스트에서 여는 중괄호 2개("{{")를 사용하면 여는 중괄호 1개("{")가, 닫는 중괄호 2개("}}")를 사용하면 닫는 중괄호 1개("}")가 표시됩니다. 서식 항목에서 중괄호는 나타나는 순서대로 해석됩니다. 중첩 중괄호 해석은 지원되지 않습니다. 

이스케이프된 중괄호가 해석되는 방식에 따라 예기치 않은 결과가 나올 수도 있습니다. 예를 들어 여는 중괄호, 10진수로 서식 지정된 숫자 값 및 닫는 중괄호를 표시하기 위해 서식 항목 "{{{0:D}}}"를 사용했다고 가정해 봅시다. 그러나 이 서식 항목은 다음과 같이 해석됩니다. 

1. 맨 처음 여는 중괄호 2개("{{")는 이스케이프되어 여는 중괄호 1개가 됩니다. 

2. 그 다음 3개의 문자("{0:")는 서식 항목의 시작으로 해석됩니다.

3. 다음 문자("D")는 10진 표준 숫자 서식 지정자로 해석되지만, 그 다음 이스케이프된 중괄호 2개("}}")는 중괄호 1개로 인식됩니다. 결과 문자열("D}")은 표준 숫자 서식 지정자가 아니므로 리터럴 문자열 "D}"를 표시하는 사용자 지정 서식 문자열로 해석됩니다. 

4. 마지막 중괄호("}")는 서식 항목의 끝으로 해석됩니다. 

5. 표시되는 최종 결과는 리터럴 문자열 "{D}"입니다. 서식 지정 시 의도했던 숫자 값이 표시되지 않습니다.

이스케이프된 중괄호 및 서식 항목이 잘못 해석되지 않도록 코드를 작성하는 방법 중 하나는 중괄호와 서식 항목의 서식을 따로 지정하는 것입니다. 즉, 첫째 서식 작업에서 리터럴 여는 중괄호를 표시하고 다음 작업에서 서식 항목의 결과를 표시한 다음 마지막 작업에서 리터럴 닫는 괄호를 표시합니다. 다음 예제에서 이 방법을 보여 줍니다.

```csharp
int value = 6324;
string output = string.Format("{0}{1:D}{2}", 
                             "{", value, "}");
Console.WriteLine(output);
// The example displays the following output:
//       {6324} 
```

```vb
Dim value As Integer = 6324
Dim output As String = String.Format("{0}{1:D}{2}", _
                                     "{", value, "}")
Console.WriteLine(output)   
' The example displays the following output:
'       {6324}
```

### <a name="processing-order"></a>처리 순서

복합 형식 지정 메서드 호출에 값이 null이 아닌 [IFormatProvider](xref:System.IFormatProvider) 인수가 포함되어 있으면 런타임에서 해당 [IFormatProvider.GetFormat](xref:System.IFormatProvider.GetFormat(System.Type)) 메서드를 호출하여 [ICustomFormatter](xref:System.ICustomFormatter) 구현을 요청합니다. 이 메서드가 [ICustomFormatter](xref:System.ICustomFormatter) 구현을 반환할 수 있는 경우 나중에 사용할 수 있도록 캐시됩니다. 

다음 단계를 수행하면 서식 항목에 상응하는 매개 변수 목록의 각 값이 문자열로 변환됩니다. 처음 세 단계의 조건 중 해당 사항이 하나라도 있으면 해당 단계에서 값의 문자열 표현이 반환되고 이후의 단계는 실행되지 않습니다.

1. 서식을 지정할 값이 `null`이면 빈 문자열("")이 반환됩니다. 

2. [ICustomFormatter](xref:System.ICustomFormatter) 구현을 사용할 수 있는 경우 런타임에서 해당 [Format](xref:System.ICustomFormatter.Format(System.String,System.Object,System.IFormatProvider)) 메서드를 호출합니다. [IFormatProvider](xref:System.IFormatProvider) 구현과 함께 형식 항목의 *formatString* 값(있는 경우) 또는 `null`(없는 경우)을 메서드에 전달합니다. 

3. 값이 [IFormattable](xref:System.IFormattable) 인터페이스를 구현하는 경우 인터페이스의 [ToString(String,IFormatProvider)](xref:System.IFormattable.ToString(System.String,System.IFormatProvider)) 메서드가 호출됩니다. *formatString* 값(형식 항목에 있는 경우) 또는 `null`(없는 경우)이 메서드에 전달됩니다. [IFormatProvider](xref:System.IFormatProvider) 인수는 다음과 같이 결정됩니다.

    *   숫자 값의 경우, null이 아닌 [IFormatProvider](xref:System.IFormatProvider) 인수가 있는 복합 형식 지정 메서드를 호출하면 런타임이 해당 [IFormatProvider.GetFormat](xref:System.IFormatProvider.GetFormat(System.Type)) 메서드에서 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 개체를 요청합니다. 값을 제공할 수 없거나, 인수 값이 `null`이거나, 복합 형식 지정 메서드에 [IFormatProvider](xref:System.IFormatProvider) 매개 변수가 없는 경우 현재 스레드 문화권에 대한 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo 개체가 사용됩니다. 
    
    * 날짜 및 시간 값의 경우, null이 아닌 [IFormatProvider](xref:System.IFormatProvider) 인수가 있는 복합 형식 지정 메서드를 호출하면 런타임이 해당 [IFormatProvider.GetFormat](xref:System.IFormatProvider._GetFormat(System.Type) 메서드에서 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체를 요청합니다. 값을 제공할 수 없거나, 인수 값이 `null`이거나, 복합 형식 지정 메서드에 [IFormatProvider](xref:System.IFormatProvider) 매개 변수가 없는 경우 현재 스레드 문화권에 대한 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체가 사용됩니다. 
    
    * 다른 개체 형식의 경우 [IFormatProvider](xref:System.IFormatProvider) 인수로 복합 형식 지정을 호출하면 해당 값([IFormatProvider](xref:System.IFormatProvider) 개체가 제공되지 않은 경우 `null` 포함)이 [IFormattable.ToString](xref:System.IFormattable.ToString(System.String,System.IFormatProvider)) 구현에 직접 전달됩니다. 그러지 않으면 현재 스레드 문화권을 나타내는 [CultureInfo](xref:System.Globalization.CultureInfo) 개체가 [IFormattable.ToString](xref:System.IFormattable.ToString(System.String,System.IFormatProvider)) 구현에 전달됩니다. 
    
4. [Object.ToString()](xref:System.Object.ToString)을 재정의하거나 기본 클래스의 동작을 상속받는, 형식의 매개 변수가 없는 `ToString` 메서드가 호출됩니다. 이 경우, 형식 항목에서 *formatString* 구성 요소로 지정된 형식 문자열(있는 경우)은 무시됩니다.

앞의 단계가 수행된 후에 맞춤이 적용됩니다. 

## <a name="code-examples"></a>코드 예제

다음 예제에서는 합성 서식 지정을 사용하여 만든 문자열과 개체의 `ToString` 메서드를 사용하여 만든 문자열을 보여 줍니다. 두 형식의 서식을 지정한 결과는 같습니다. 

```csharp
string FormatString1 = String.Format("{0:dddd MMMM}", DateTime.Now);
string FormatString2 = DateTime.Now.ToString("dddd MMMM");
```

```vb
Dim FormatString1 As String = String.Format("{0:dddd MMMM}", DateTime.Now)
Dim FormatString2 As String = DateTime.Now.ToString("dddd MMMM")
```

오늘이 5월의 목요일이라고 가정할 때 앞의 예제에서 두 문자열의 값은 미국 영어 문화권에서 `Thursday May` 입니다.

[Console.WriteLine](xref:System.Console.WriteLine)은 [String.Format](xref:System.String.Format(System.IFormatProvider,System.String,System.Object))과 동일한 기능을 표시합니다. 두 메서드 간의 유일한 차이점은 [String.Format](xref:System.String.Format(System.IFormatProvider,System.String,System.Object))은 결과를 문자열로 반환하는 반면 [Console.WriteLine](xref:System.Console.WriteLine)은 [Console](xref:System.Console) 개체와 연결된 출력 스트림에 결과를 쓴다는 것입니다. 다음 예제에서는 [Console.WriteLine](xref:System.Console.WriteLine) 메서드를 사용하여 `MyInt` 값의 형식을 통화 값으로 지정합니다.

```csharp
int MyInt = 100;
Console.WriteLine("{0:C}", MyInt);
// The example displays the following output 
// if en-US is the current culture:
//        $100.00
```

```vb
Dim MyInt As Integer = 100
Console.WriteLine("{0:C}", MyInt)
' The example displays the following output
' if en-US is the current culture:
'        $100.00
```

다음 예제에서는 하나의 개체 서식을 두 가지 다른 방법으로 지정하는 경우를 비롯하여 여러 개체의 서식을 지정하는 방법을 보여 줍니다.

```csharp
string myName = "Fred";
Console.WriteLine(String.Format("Name = {0}, hours = {1:hh}, minutes = {1:mm}",
      myName, DateTime.Now));
// Depending on the current time, the example displays output like the following:
//    Name = Fred, hours = 11, minutes = 30                 
```

```vb
Dim myName As String = "Fred"
Console.WriteLine(String.Format("Name = {0}, hours = {1:hh}, minutes = {1:mm}", _
                  myName, DateTime.Now))
' Depending on the current time, the example displays output like the following:
'    Name = Fred, hours = 11, minutes = 30
```

다음 예제에서는 서식 지정에서 맞춤을 사용하는 방법을 보여 줍니다. 서식 지정되는 인수가 세로줄 문자('|') 사이에 위치하면서 결과 맞춤이 강조됩니다.

```csharp
string myFName = "Fred";
string myLName = "Opals";
int myInt = 100;
string FormatFName = String.Format("First Name = |{0,10}|", myFName);
string FormatLName = String.Format("Last Name = |{0,10}|", myLName);
string FormatPrice = String.Format("Price = |{0,10:C}|", myInt); 
Console.WriteLine(FormatFName);
Console.WriteLine(FormatLName);
Console.WriteLine(FormatPrice);
Console.WriteLine();

FormatFName = String.Format("First Name = |{0,-10}|", myFName);
FormatLName = String.Format("Last Name = |{0,-10}|", myLName);
FormatPrice = String.Format("Price = |{0,-10:C}|", myInt);
Console.WriteLine(FormatFName);
Console.WriteLine(FormatLName);
Console.WriteLine(FormatPrice);
// The example displays the following output on a system whose current
// culture is en-US:
//          First Name = |      Fred|
//          Last Name = |     Opals|
//          Price = |   $100.00|
//
//          First Name = |Fred      |
//          Last Name = |Opals     |
//          Price = |$100.00   |
```

```vb
Dim myFName As String = "Fred"
Dim myLName As String = "Opals"

Dim myInt As Integer = 100
Dim FormatFName As String = String.Format("First Name = |{0,10}|", myFName)
Dim FormatLName As String = String.Format("Last Name = |{0,10}|", myLName)
Dim FormatPrice As String = String.Format("Price = |{0,10:C}|", myInt)
Console.WriteLine(FormatFName)
Console.WriteLine(FormatLName)
Console.WriteLine(FormatPrice)
Console.WriteLine()

FormatFName = String.Format("First Name = |{0,-10}|", myFName)
FormatLName = String.Format("Last Name = |{0,-10}|", myLName)
FormatPrice = String.Format("Price = |{0,-10:C}|", myInt)
Console.WriteLine(FormatFName)
Console.WriteLine(FormatLName)
Console.WriteLine(FormatPrice)
' The example displays the following output on a system whose current
' culture is en-US:
'          First Name = |      Fred|
'          Last Name = |     Opals|
'          Price = |   $100.00|
'
'          First Name = |Fred      |
'          Last Name = |Opals     |
'          Price = |$100.00   |
```

## <a name="see-also"></a>참고 항목

[Console.WriteLine](xref:System.Console.WriteLine)

[String.Format](xref:System.String.Format(System.IFormatProvider,System.String,System.Object))

[형식 서식 지정](formatting-types.md)

[표준 날짜 및 시간 서식 문자열](standard-datetime.md)

[사용자 지정 날짜 및 시간 서식 문자열](custom-datetime.md)

[열거형 서식 문자열](enumeration-format.md)

[표준 숫자 서식 문자열](standard-numeric.md)

[사용자 지정 숫자 서식 문자열](custom-numeric.md)

[표준 TimeSpan 서식 문자열](standard-timespan.md)

[사용자 지정 TimeSpan 서식 문자열](custom-timespan.md)



<!--HONumber=Nov16_HO1-->


