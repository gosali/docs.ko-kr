---
title: "표준 TimeSpan 서식 문자열"
description: "표준 TimeSpan 서식 문자열"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 07/26/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 4e0f02f1-4abd-47b5-8995-5c3ff45b0ce1
translationtype: Human Translation
ms.sourcegitcommit: fb00da6505c9edb6a49d2003ae9bcb8e74c11d6c
ms.openlocfilehash: a31f0da1f5b502d8b983f4d496c4b9a520b0309c

---

# <a name="standard-timespan-format-strings"></a>표준 TimeSpan 서식 문자열

표준 [TimeSpan](xref:System.TimeSpan) 서식 문자열은 단일 서식 지정자를 사용하여 서식 지정 작업에서 생성되는 [TimeSpan](xref:System.TimeSpan) 값의 텍스트 표현을 정의합니다. 공백을 포함하여 문자가 두 개 이상 포함된 서식 문자열은 사용자 지정 [TimeSpan](xref:System.TimeSpan) 서식 문자열로 해석됩니다. 자세한 내용은 [사용자 지정 TimeSpan 서식 문자열](custom-timespan.md)을 참조하세요.

[TimeSpan](xref:System.TimeSpan) 값의 문자열 표현은 [String.Format](xref:System.String.Format(System.IFormatProvider,System.String,System.Object))과 같이 복합 서식 지정을 지원하는 메서드뿐만 아니라 [TimeSpan.ToString](xref:System.TimeSpan.ToString) 메서드의 오버로드를 호출하여 생성됩니다. 자세한 내용은 [서식 지정 형식](formatting-types.md) 및 [복합 서식 지정](composite-format.md)을 참조하세요. 다음 예제에서는 서식 지정 작업에 표준 서식 문자열을 사용하는 방법을 보여 줍니다.

```csharp
using System;

public class Example
{
   public static void Main()
   {
      TimeSpan duration = new TimeSpan(1, 12, 23, 62);
      string output = "Time of Travel: " + duration.ToString("c");
      Console.WriteLine(output);

      Console.WriteLine("Time of Travel: {0:c}", duration); 
   }
}
// The example displays the following output:
//       Time of Travel: 1.12:24:02
//       Time of Travel: 1.12:24:02
```

```vb
Module Example
   Public Sub Main()
      Dim duration As New TimeSpan(1, 12, 23, 62)
      Dim output As String = "Time of Travel: " + duration.ToString("c")
      Console.WriteLine(output)

      Console.WriteLine("Time of Travel: {0:c}", duration) 
   End Sub
End Module
' The example displays the following output:
'       Time of Travel: 1.12:24:02
'       Time of Travel: 1.12:24:02
```

표준 [TimeSpan](xref:System.TimeSpan) 서식 문자열은 구문 분석 작업에 필요한 입력 문자열 서식을 정의하기 위해 [TimeSpan.ParseExact](xref:System.TimeSpan.ParseExact(System.String,System.String,System.IFormatProvider)) 및 [TimeSpan.TryParseExact](xref:System.TimeSpan.TryParseExact(System.String,System.String,System.IFormatProvider,System.Globalization.TimeSpanStyles,System.TimeSpan@)) 메서드에서도 사용합니다. 구문 분석 시에는 값의 문자열 표현이 해당 값으로 변환됩니다. 다음 예제에서는 구문 분석 작업에 표준 형식 문자열을 사용하는 방법을 보여 줍니다.

```csharp
using System;

public class Example
{
   public static void Main()
   {
      string value = "1.03:14:56.1667";
      TimeSpan interval;
      try {
         interval = TimeSpan.ParseExact(value, "c", null);
         Console.WriteLine("Converted '{0}' to {1}", value, interval);
      }   
      catch (FormatException) {
         Console.WriteLine("{0}: Bad Format", value);
      }   
      catch (OverflowException) {
         Console.WriteLine("{0}: Out of Range", value);
      }

      if (TimeSpan.TryParseExact(value, "c", null, out interval))
         Console.WriteLine("Converted '{0}' to {1}", value, interval);
      else
         Console.WriteLine("Unable to convert {0} to a time interval.", 
                           value);
   }
}
// The example displays the following output:
//       Converted '1.03:14:56.1667' to 1.03:14:56.1667000
//       Converted '1.03:14:56.1667' to 1.03:14:56.1667000
```

```vb
Module Example
   Public Sub Main()
      Dim value As String = "1.03:14:56.1667"
      Dim interval As TimeSpan
      Try
         interval = TimeSpan.ParseExact(value, "c", Nothing)
         Console.WriteLine("Converted '{0}' to {1}", value, interval)
      Catch e As FormatException
         Console.WriteLine("{0}: Bad Format", value)
      Catch e As OverflowException
         Console.WriteLine("{0}: Out of Range", value)
      End Try

      If TimeSpan.TryParseExact(value, "c", Nothing, interval) Then
         Console.WriteLine("Converted '{0}' to {1}", value, interval)
      Else
         Console.WriteLine("Unable to convert {0} to a time interval.", 
                           value)
      End If                
   End Sub
End Module
' The example displays the following output:
'       Converted '1.03:14:56.1667' to 1.03:14:56.1667000
'       Converted '1.03:14:56.1667' to 1.03:14:56.1667000
```

다음 표에는 표준 시간 간격 형식 지정자가 나와 있습니다.

형식 지정자 | 이름 | 설명 | 예제
---------------- | ---- | ----------- | --------
"c" | 상수(고정) 형식 | 이 지정자는 문화권을 구분하지 않으며 [-][d’.’]hh’:’mm’:’ss[‘.’fffffff] 형식을 사용합니다. "t" 및 "T" 형식 문자열은 같은 결과를 생성합니다. | `TimeSpan.Zero -> 00:00:00`; `New TimeSpan(0, 0, 30, 0) -> 00:30:00`; `New TimeSpan(3, 17, 25, 30, 500) -> 3.17:25:30.5000000`
"g" | 일반 약식 | 이 지정자는 필요한 내용만 출력하고 문화권을 구분하며 형식은 [-][d’:’]h’:’mm’:’ss[.FFFFFFF]입니다. | `New TimeSpan(1, 3, 16, 50, 500) -> 1:3:16:50.5 (en-US)`; `New TimeSpan(1, 3, 16, 50, 500) -> 1:3:16:50,5 (fr-FR)`; `New TimeSpan(1, 3, 16, 50, 599) -> 1:3:16:50.599 (en-US)`; `New TimeSpan(1, 3, 16, 50, 599) -> 1:3:16:50,599 (fr-FR)`
"G" | 일반 긴 형식 | 이 지정자는 항상 일 수와 7자리 소수 자릿수를 출력하고 문화권을 구분하며 형식은 [-]d’:’hh’:’mm’:’ss.fffffff입니다. | `New TimeSpan(18, 30, 0) -> 0:18:30:00.0000000 (en-US)`; `New TimeSpan(18, 30, 0) -> 0:18:30:00,0000000 (fr-FR)` 

## <a name="the-constant-c-format-specifier"></a>상수("c") 형식 지정자

"c" 서식 지정자는 [TimeSpan](xref:System.TimeSpan) 값의 문자열 표현을 다음 형식으로 반환합니다.

[-][_d_.]_hh_:_mm_:_ss_[._fffffff_]

대괄호 ([ 및 ]) 안의 요소는 선택적 요소입니다. 마침표(.)와 콜론(:)은 리터럴 기호입니다. 다음 표에서는 나머지 요소에 대해 설명합니다. 

요소 | 설명
------- | -----------
- | 음수 시간 간격을 나타내는 선택적 음수 기호입니다.
*d* | 앞에 오는 0이 없는 선택적 일 수입니다.
*hh* | "00"부터 "23" 범위의 시간입니다.
*mm* | "00"부터 "59" 범위의 분입니다.
*ss* | "0"부터 "59" 범위의 초입니다.
*fffffff* | 선택적인 초의 소수 부분입니다. 값 범위는 "0000001"(틱 1개 또는 1천만 분의 1초)에서 "9999999"(9,999,999천만 분의 1초 또는 1초-틱 1개)까지입니다. 

"g" 및 "G" 형식 지정자와 달리 "c" 형식 지정자는 문화권을 구분하지 않으며, .NET Framework 4 이전의 모든 .NET Framework 버전에 공통적으로 적용되는 고정된 [TimeSpan](xref:System.TimeSpan) 값의 문자열 표현을 생성합니다. "c"는 기본 [TimeSpan](xref:System.TimeSpan) 서식 문자열입니다. 즉, [TimeSpan.ToString](xref:System.TimeSpan.ToString) 메서드는 "c" 서식 문자열을 사용하여 시간 간격 값의 서식을 지정합니다.

> [!NOTE]
> [TimeSpan](xref:System.TimeSpan)은 "c" 표준 서식 문자열과 동작이 동일한 "t" 및 "T" 표준 서식 문자열도 지원합니다.

다음 예제에서는 두 [TimeSpan](xref:System.TimeSpan) 개체를 인스턴스화한 다음 해당 개체를 사용하여 산술 연산을 수행하고 결과를 표시합니다. 각 경우에서는 "c" 서식 지정자를 사용하여 [TimeSpan](xref:System.TimeSpan) 값을 표시하는 복합 서식을 사용합니다.

```csharp
using System;

public class Example
{
   public static void Main()
   {
      TimeSpan interval1, interval2;
      interval1 = new TimeSpan(7, 45, 16);
      interval2 = new TimeSpan(18, 12, 38);

      Console.WriteLine("{0:c} - {1:c} = {2:c}", interval1, 
                        interval2, interval1 - interval2);
      Console.WriteLine("{0:c} + {1:c} = {2:c}", interval1, 
                        interval2, interval1 + interval2);

      interval1 = new TimeSpan(0, 0, 1, 14, 365);
      interval2 = TimeSpan.FromTicks(2143756);  
      Console.WriteLine("{0:c} + {1:c} = {2:c}", interval1, 
                        interval2, interval1 + interval2);
   }
}
// The example displays the following output:
//       07:45:16 - 18:12:38 = -10:27:22
//       07:45:16 + 18:12:38 = 1.01:57:54
//       00:01:14.3650000 + 00:00:00.2143756 = 00:01:14.5793756
```

```vb
Module Example
   Public Sub Main()
      Dim interval1, interval2 As TimeSpan
      interval1 = New TimeSpan(7, 45, 16)
      interval2 = New TimeSpan(18, 12, 38)

      Console.WriteLine("{0:c} - {1:c} = {2:c}", interval1, 
                        interval2, interval1 - interval2)
      Console.WriteLine("{0:c} + {1:c} = {2:c}", interval1, 
                        interval2, interval1 + interval2)

      interval1 = New TimeSpan(0, 0, 1, 14, 365)
      interval2 = TimeSpan.FromTicks(2143756)      
      Console.WriteLine("{0:c} + {1:c} = {2:c}", interval1, 
                        interval2, interval1 + interval2)
   End Sub
End Module
' The example displays the following output:
'       07:45:16 - 18:12:38 = -10:27:22
'       07:45:16 + 18:12:38 = 1.01:57:54
'       00:01:14.3650000 + 00:00:00.2143756 = 00:01:14.5793756
```

## <a name="the-general-short-g-format-specifier"></a>일반 약식("g") 형식 지정자

"g" [TimeSpan](xref:System.TimeSpan) 서식 지정자는 필요한 요소만 포함하여 [TimeSpan](xref:System.TimeSpan) 값의 문자열 표현을 압축 형식으로 반환합니다. 이 형식 지정자의 형식은 다음과 같습니다.

[-][_d_:]_h_:_mm_:_ss_[._FFFFFFF_]

대괄호 ([ 및 ]) 안의 요소는 선택적 요소입니다. 콜론(:)은 리터럴 기호입니다. 다음 표에서는 나머지 요소에 대해 설명합니다.

요소 | 설명
------- | -----------
- | 음수 시간 간격을 나타내는 선택적 음수 기호입니다.
*d* | 앞에 오는 0이 없는 선택적 일 수입니다.
*hh* | 앞에 오는 0이 없는 "0"부터"23" 범위의 시간입니다. 
*mm* | "00"부터 "59" 범위의 분입니다.
*ss* | "0"부터 "59" 범위의 초입니다.
입니다. | 초 소수 구분 기호입니다.
*FFFFFFF* | 초의 소수 부분입니다. 최대한 적은 자릿수가 표시됩니다.

"G" 형식 지정자와 마찬가지로 "g" 형식 지정자도 지역화됩니다. 해당 초 소수 구분 기호는 현재 문화권을 기반으로 합니다.

다음 예제에서는 두 [TimeSpan](xref:System.TimeSpan) 개체를 인스턴스화한 다음 해당 개체를 사용하여 산술 연산을 수행하고 결과를 표시합니다. 각 경우에서는 "g" 서식 지정자를 사용하여 [TimeSpan](xref:System.TimeSpan) 값을 표시하는 복합 서식을 사용합니다. 또한 현재 시스템 문화권(여기서는 영어 - 미국 또는 en-US) 및 프랑스어 - 프랑스(fr-FR) 문화권의 서식 규칙을 사용하여 [TimeSpan](xref:System.TimeSpan) 값의 서식을 지정합니다.

```csharp
using System;
using System.Globalization;

public class Example
{
   public static void Main()
   {
      TimeSpan interval1, interval2;
      interval1 = new TimeSpan(7, 45, 16);
      interval2 = new TimeSpan(18, 12, 38);

      Console.WriteLine("{0:g} - {1:g} = {2:g}", interval1, 
                        interval2, interval1 - interval2);
      Console.WriteLine(String.Format(new CultureInfo("fr-FR"), 
                        "{0:g} + {1:g} = {2:g}", interval1, 
                        interval2, interval1 + interval2));

      interval1 = new TimeSpan(0, 0, 1, 14, 36);
      interval2 = TimeSpan.FromTicks(2143756);      
      Console.WriteLine("{0:g} + {1:g} = {2:g}", interval1, 
                        interval2, interval1 + interval2);
   }
}
// The example displays the following output:
//       7:45:16 - 18:12:38 = -10:27:22
//       7:45:16 + 18:12:38 = 1:1:57:54
//       0:01:14.036 + 0:00:00.2143756 = 0:01:14.2503756
```

```vb
Imports System.Globalization

Module Example
   Public Sub Main()
      Dim interval1, interval2 As TimeSpan
      interval1 = New TimeSpan(7, 45, 16)
      interval2 = New TimeSpan(18, 12, 38)

      Console.WriteLine("{0:g} - {1:g} = {2:g}", interval1, 
                        interval2, interval1 - interval2)
      Console.WriteLine(String.Format(New CultureInfo("fr-FR"), 
                        "{0:g} + {1:g} = {2:g}", interval1, 
                        interval2, interval1 + interval2))

      interval1 = New TimeSpan(0, 0, 1, 14, 36)
      interval2 = TimeSpan.FromTicks(2143756)      
      Console.WriteLine("{0:g} + {1:g} = {2:g}", interval1, 
                        interval2, interval1 + interval2)
   End Sub
End Module
' The example displays the following output:
'       7:45:16 - 18:12:38 = -10:27:22
'       7:45:16 + 18:12:38 = 1:1:57:54
'       0:01:14.036 + 0:00:00.2143756 = 0:01:14.2503756
```

## <a name="the-general-long-g-format-specifier"></a>일반 긴("G") 형식 지정자

"G" [TimeSpan](xref:System.TimeSpan) 서식 지정자는 항상 일 수와 초의 소수 부분을 모두 포함하는 긴 형식으로 [TimeSpan](xref:System.TimeSpan) 값의 문자열 표현을 반환합니다. "G" 표준 형식 지정자에서 생성 되는 문자열의 형식은 다음과 같습니다.

[-]*d*:*hh*:*mm*:*ss*.*fffffff*

대괄호 ([ 및 ]) 안의 요소는 선택적 요소입니다. 콜론(:)은 리터럴 기호입니다. 다음 표에서는 나머지 요소에 대해 설명합니다.

요소 | 설명
------- | -----------
- | 음수 시간 간격을 나타내는 선택적 음수 기호입니다.
*d* | 앞에 오는 0이 없는 선택적 일 수입니다.
*hh* | "0"부터 "23" 범위의 시간입니다. 
*mm* | "00"부터 "59" 범위의 분입니다.
*ss* | "0"부터 "59" 범위의 초입니다.
입니다. | 초 소수 구분 기호입니다.
*fffffff* | 초의 소수 부분입니다.

"G" 형식 지정자와 마찬가지로 "g" 형식 지정자도 지역화됩니다. 해당 초 소수 구분 기호는 현재 문화권을 기반으로 합니다.

다음 예제에서는 두 [TimeSpan](xref:System.TimeSpan) 개체를 인스턴스화한 다음 해당 개체를 사용하여 산술 연산을 수행하고 결과를 표시합니다. 각 경우에서는 "G" 서식 지정자를 사용하여 [TimeSpan](xref:System.TimeSpan) 값을 표시하는 복합 서식을 사용합니다. 또한 현재 시스템 문화권(여기서는 영어 - 미국 또는 en-US) 및 프랑스어 - 프랑스(fr-FR) 문화권의 서식 규칙을 사용하여 [TimeSpan](xref:System.TimeSpan) 값의 서식을 지정합니다. 

```csharp
using System;
using System.Globalization;

public class Example
{
   public static void Main()
   {
      TimeSpan interval1, interval2;
      interval1 = new TimeSpan(7, 45, 16);
      interval2 = new TimeSpan(18, 12, 38);

      Console.WriteLine("{0:G} - {1:G} = {2:G}", interval1, 
                        interval2, interval1 - interval2);
      Console.WriteLine(String.Format(new CultureInfo("fr-FR"), 
                        "{0:G} + {1:G} = {2:G}", interval1, 
                        interval2, interval1 + interval2));

      interval1 = new TimeSpan(0, 0, 1, 14, 36);
      interval2 = TimeSpan.FromTicks(2143756);      
      Console.WriteLine("{0:G} + {1:G} = {2:G}", interval1, 
                        interval2, interval1 + interval2);
   }
}
// The example displays the following output:
//       0:07:45:16.0000000 - 0:18:12:38.0000000 = -0:10:27:22.0000000
//       0:07:45:16,0000000 + 0:18:12:38,0000000 = 1:01:57:54,0000000
//       0:00:01:14.0360000 + 0:00:00:00.2143756 = 0:00:01:14.2503756
```

```vb
Imports System.Globalization

Module Example
   Public Sub Main()
      Dim interval1, interval2 As TimeSpan
      interval1 = New TimeSpan(7, 45, 16)
      interval2 = New TimeSpan(18, 12, 38)

      Console.WriteLine("{0:G} - {1:G} = {2:G}", interval1, 
                        interval2, interval1 - interval2)
      Console.WriteLine(String.Format(New CultureInfo("fr-FR"), 
                        "{0:G} + {1:G} = {2:G}", interval1, 
                        interval2, interval1 + interval2))

      interval1 = New TimeSpan(0, 0, 1, 14, 36)
      interval2 = TimeSpan.FromTicks(2143756)      
      Console.WriteLine("{0:G} + {1:G} = {2:G}", interval1, 
                        interval2, interval1 + interval2)
   End Sub
End Module
' The example displays the following output:
'       0:07:45:16.0000000 - 0:18:12:38.0000000 = -0:10:27:22.0000000
'       0:07:45:16,0000000 + 0:18:12:38,0000000 = 1:01:57:54,0000000
'       0:00:01:14.0360000 + 0:00:00:00.2143756 = 0:00:01:14.2503756
```

## <a name="see-also"></a>참고 항목

[형식 서식 지정](formatting-types.md)

[복합 서식 지정](composite-format.md)

[문자열 구문 분석](parsing-strings.md)




<!--HONumber=Nov16_HO1-->


