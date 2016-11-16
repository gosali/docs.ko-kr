---
title: "사용자 지정 TimeSpan 형식 문자열"
description: "사용자 지정 TimeSpan 형식 문자열"
keywords: .NET, .NET Core
author: stevehoag
ms.author: shoag
manager: wpickett
ms.date: 07/25/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: e79745eb-6ebd-4e62-85c4-4f2830c27285
translationtype: Human Translation
ms.sourcegitcommit: b828bb1d6c8fb750ad9ef34f8a7a1b7d2574f4c6
ms.openlocfilehash: 9b891c51f78b44bf0f20476930492fad7ad923a7

---

# <a name="custom-timespan-format-strings"></a>사용자 지정 TimeSpan 형식 문자열

[TimeSpan](xref:System.TimeSpan) 형식 문자열은 형식 지정 작업에서 생성되는 [TimeSpan](xref:System.TimeSpan) 값의 텍스트 표현을 정의합니다. 사용자 지정 형식 문자열은 하나 이상의 사용자 지정 [TimeSpan](xref:System.TimeSpan) 형식 지정자와 임의 개수의 리터럴 문자로 구성됩니다. [표준 TimeSpan](standard-timespan.md) 서식 문자열이 아닌 문자열은 사용자 지정 [TimeSpan](xref:System.TimeSpan) 서식 문자열로 해석됩니다.

> [!IMPORTANT]
> 사용자 지정 [TimeSpan](xref:System.TimeSpan) 형식 지정자는 일과 시간, 시간과 분 또는 초와 소수 자릿수 초를 구분하는 기호 등의 자리 표시자 구분 기호를 포함하지 않습니다. 대신, 이러한 기호는 사용자 지정 형식 문자열에 문자열 리터럴로 포함되어야 합니다. 예를 들어 `"dd\.hh\:mm"`은 일과 시간의 구분 기호로 마침표(.)를 정의하고 시간과 분의 구분 기호로 콜론(:)을 정의합니다. 

> 사용자 지정 [TimeSpan](xref:System.TimeSpan) 형식 지정자도 음수 및 양수 시간 간격을 구분할 수 있도록 하는 부호 기호를 포함하지 않습니다. 부호 기호를 포함하려면 조건부 논리를 사용하여 서식 문자열을 생성해야 합니다. [기타 문자](#other-characters) 섹션에 예제가 포함되어 있습니다. 

[TimeSpan](xref:System.TimeSpan) 값의 문자열 표현은 [String.Format](xref:System.String.Format(System.IFormatProvider,System.String,System.Object))과 같이 복합 서식 지정을 지원하는 메서드뿐만 아니라 [TimeSpan](xref:System.TimeSpan) `ToString` 메서드의 오버로드를 호출하여 생성됩니다. 자세한 내용은 [형식 서식 지정](formatting-types.md) 및 [복합 서식 지정](composite-format.md)을 참조하세요. 다음 예제에서는 서식 지정 작업에 표준 형식 문자열을 사용하는 방법을 보여 줍니다.

```csharp
using System;

public class Example
{
   public static void Main()
   {
      TimeSpan duration = new TimeSpan(1, 12, 23, 62);

      string output = null;
      output = "Time of Travel: " + duration.ToString("%d") + " days";
      Console.WriteLine(output);
      output = "Time of Travel: " + duration.ToString(xref:"dd\.hh\:mm\:ss"); 
      Console.WriteLine(output);

      Console.WriteLine("Time of Travel: {0:%d} day(s)", duration);
      Console.WriteLine("Time of Travel: {0:dd\\.hh\\:mm\\:ss} days", duration);
   }
}
// The example displays the following output:
//       Time of Travel: 1 days
//       Time of Travel: 01.12:24:02
//       Time of Travel: 1 day(s)
//       Time of Travel: 01.12:24:02 days
```

```vb
Module Example
   Public Sub Main()
      Dim duration As New TimeSpan(1, 12, 23, 62)

      Dim output As String = Nothing
      output = "Time of Travel: " + duration.ToString("%d") + " days"
      Console.WriteLine(output)
      output = "Time of Travel: " + duration.ToString("dd\.hh\:mm\:ss") 
      Console.WriteLine(output)

      Console.WriteLine("Time of Travel: {0:%d} day(s)", duration)
      Console.WriteLine("Time of Travel: {0:dd\.hh\:mm\:ss} days", duration)
   End Sub
End Module
' The example displays the following output:
'       Time of Travel: 1 days
'       Time of Travel: 01.12:24:02
'       Time of Travel: 1 day(s)
'       Time of Travel: 01.12:24:02 days
```

사용자 지정 [TimeSpan](xref:System.TimeSpan) 형식 문자열은 구문 분석 작업에 필요한 입력 문자열 형식을 정의하기 위해 [TimeSpan.ParseExact](xref:System.TimeSpan.ParseExact(System.String,System.String,System.IFormatProvider)) 및 [TimeSpan.TryParseExact](xref:System.TimeSpan.TryParseExact(System.String,System.String,System.IFormatProvider,System.Globalization.TimeSpanStyles,System.TimeSpan@)) 메서드에서도 사용됩니다. 구문 분석 시에는 값의 문자열 표현이 해당 값으로 변환됩니다. 다음 예제에서는 구문 분석 작업에 표준 형식 문자열을 사용하는 방법을 보여 줍니다.

```csharp
using System;

public class Example
{
   public static void Main()
   {
      string value = null;
      TimeSpan interval;

      value = "6";
      if (TimeSpan.TryParseExact(value, "%d", null, out interval))
         Console.WriteLine("{0} --> {1}", value, interval.ToString("c"));
      else
         Console.WriteLine("Unable to parse '{0}'", value);

      value = "16:32.05";
      if (TimeSpan.TryParseExact(value, @"mm\:ss\.ff", null, out interval))
         Console.WriteLine("{0} --> {1}", value, interval.ToString("c"));
      else
         Console.WriteLine("Unable to parse '{0}'", value);

      value= "12.035";
      if (TimeSpan.TryParseExact(value, "ss\\.fff", null, out interval))
         Console.WriteLine("{0} --> {1}", value, interval.ToString("c"));
      else
         Console.WriteLine("Unable to parse '{0}'", value);
   }
}
// The example displays the following output:
//       6 --> 6.00:00:00
//       16:32.05 --> 00:16:32.0500000
//       12.035 --> 00:00:12.0350000
```

```vb
Module Example
   Public Sub Main()
      Dim value As String = Nothing
      Dim interval As TimeSpan

      value = "6"
      If TimeSpan.TryParseExact(value, "%d", Nothing, interval) Then
         Console.WriteLine("{0} --> {1}", value, interval.ToString("c"))
      Else
         Console.WriteLine("Unable to parse '{0}'", value)
      End If

      value = "16:32.05"
      If TimeSpan.TryParseExact(value, "mm\:ss\.ff", Nothing, interval) Then
         Console.WriteLine("{0} --> {1}", value, interval.ToString("c"))
      Else
         Console.WriteLine("Unable to parse '{0}'", value)
      End If

      value= "12.035"
      If TimeSpan.TryParseExact(value, "ss\.fff", Nothing, interval) Then
         Console.WriteLine("{0} --> {1}", value, interval.ToString("c"))
      Else
         Console.WriteLine("Unable to parse '{0}'", value)
      End If
   End Sub
End Module
' The example displays the following output:
'       6 --> 6.00:00:00
'       16:32.05 --> 00:16:32.0500000
'       12.035 --> 00:00:12.0350000
```

다음 표에서는 사용자 지정 날짜 및 시간 서식 지정자에 대해 설명합니다.

형식 지정자 | 설명 | 예제
---------------- | ----------- | --------
"d", "%d" | 시간 간격의 전체 일 수입니다. | `new TimeSpan(6, 14, 32, 17, 685):` `%d --> "6"`;  `d\.hh\:mm --> "6.14:32"`
"dd", "dddddddd" | 시간 간격의 전체 일 수로, 필요에 따라 앞에 0으로 채워집니다. | `new TimeSpan(6, 14, 32, 17, 685):` `ddd --> "006"`; `dd\.hh\:mm --> "06.14:32"`
"h", "%h" | 일 수로 계산되지 않은 시간 간격의 전체 시간 수입니다. 한 자리 시간의 경우 앞에 0이 없습니다. | `new TimeSpan(6, 14, 32, 17, 685):` `%h --> "14"`; `hh\:mm --> "14:32"`
"hh" | 일 수로 계산되지 않은 시간 간격의 전체 시간 수입니다. 한 자리 시간의 경우 앞에 0이 있습니다. | `new TimeSpan(6, 14, 32, 17, 685):` `hh --> "14"`  `new TimeSpan(6, 8, 32, 17, 685):` `hh --> 08`
"m", "%m" | 시간 또는 일 수로 계산되지 않은 시간 간격의 전체 분 수입니다. 한 자리 분의 경우 앞에 0이 없습니다. | `new TimeSpan(6, 14, 8, 17, 685):` `%m --> "8"`; `h\:m --> "14:8"`
"mm" | 시간 또는 일 수로 계산되지 않은 시간 간격의 전체 분 수입니다. 한 자리 분의 경우 앞에 0이 있습니다. | `new TimeSpan(6, 14, 8, 17, 685):` `mm --> "08"` `new TimeSpan(6, 8, 5, 17, 685):` `d\.hh\:mm\:ss --> 6.08:05:17`
"s", "%s" | 시간, 일 또는 분 수로 계산되지 않은 시간 간격의 전체 초 수입니다. 한 자리 초의 경우 앞에 0이 없습니다. | `TimeSpan.FromSeconds(12.965):` `%s --> 12`; `s\.fff --> 12.965`
"ss" | 시간, 일 또는 분 수로 계산되지 않은 시간 간격의 전체 초 수입니다. 한 자리 초의 경우 앞에 0이 있습니다. | `TimeSpan.FromSeconds(6.965):` `ss --> 06`; `ss\.fff --> 06.965`
"f", "%f" | 시간 간격의 1/10초입니다. | `TimeSpan.FromSeconds(6.895):` `f --> 8`; `ss\.f --> 06.8`
"ff" | 시간 간격의 1/100초입니다. | `TimeSpan.FromSeconds(6.895):` `ff --> 89`; `ss\.ff --> 06.89`
"fff" | 시간 간격의 밀리초입니다. | `TimeSpan.FromSeconds(6.895):` `fff --> 895`; `ss\.fff --> 06.895`
"ffff" | 시간 간격의 1/10000초입니다. | `TimeSpan.Parse("0:0:6.8954321"):` `ffff --> 8954`; `ss\.ffff --> 06.8954`
"fffff" | 시간 간격의 1/100000초입니다. | `TimeSpan.Parse("0:0:6.8954321"):` `ffff --> 89543`; `ss\.ffff --> 06.89543`
"ffffff" | 시간 간격의 1/1000000초입니다. | `TimeSpan.Parse("0:0:6.8954321"):` `ffff --> 895432`; `ss\.ffff --> 06.895432`
"fffffff" | 시간 간격의 1/10000000초(또는 소수 자릿수 틱)입니다. | `TimeSpan.Parse("0:0:6.8954321"):` `ffff --> 8954321`; `ss\.ffff --> 06.8954321`
"F", "%F" | 시간 간격의 1/10초입니다. 이 자릿수가 0이면 아무 것도 표시되지 않습니다. | `TimeSpan.Parse("00:00:06.32"):` `%F: 3`  `TimeSpan.Parse("0:0:3.091"):` `ss\.F: 03.`
"FF" | 시간 간격의 1/100초입니다. 뒤에 오는 소수 자릿수 0이나 연속 두 자리 0은 포함되지 않습니다. |  `TimeSpan.Parse("00:00:06.3291791"):` `FFFFFF: 32`  `TimeSpan.Parse("0:0:3.1900000"):` `ss\.FFFFFF: 03.1`
"FFF" | 시간 간격의 밀리초입니다. 뒤에 오는 소수 자릿수 0은 포함되지 않습니다. |  `TimeSpan.Parse("00:00:06.3291791"):` `FFFFFF: 329`  `TimeSpan.Parse("0:0:3.1900000"):` `ss\.FFFFFF: 03.1`
"FFFF" | 시간 간격의 1/10000초입니다. 뒤에 오는 소수 자릿수 0은 포함되지 않습니다. |  `TimeSpan.Parse("00:00:06.3291791"):` `FFFFFF: 3291`  `TimeSpan.Parse("0:0:3.1900000"):` `ss\.FFFFFF: 03.1`
"FFFFF" | 시간 간격의 1/100000초입니다. 뒤에 오는 소수 자릿수 0은 포함되지 않습니다. | `TimeSpan.Parse("00:00:06.3291791"):` `FFFFFF: 32917`  `TimeSpan.Parse("0:0:3.1900000"):` `ss\.FFFFFF: 03.1`
"FFFFFF" | 시간 간격의 1/1000000초입니다. 뒤에 오는 소수 자릿수 0은 표시되지 않습니다. | `TimeSpan.Parse("00:00:06.3291791"):` `FFFFFF: 329179`  `TimeSpan.Parse("0:0:3.1900000"):` `ss\.FFFFFF: 03.1`
"FFFFFFF" | 시간 간격의 1/10000000초입니다. 뒤에 오는 소수 자릿수 0이나 연속 일곱 자리 0은 표시되지 않습니다. | `TimeSpan.Parse("00:00:06.3291791"):` `FFFFFF: 3291791`  `TimeSpan.Parse("0:0:3.1900000"):` `ss\.FFFFFF: 03.19`
'string' | 리터럴 문자열 구분 기호 | `new TimeSpan(14, 32, 17):` `hh':'mm':'ss --> "14:32:17"`
\ | 이스케이프 문자입니다. | `new TimeSpan(14, 32, 17):` `hh\:mm\:ss --> "14:32:17"`
기타 문자 | 이스케이프되지 않은 다른 모든 문자는 사용자 지정 형식 지정자로 해석됩니다. | `new TimeSpan(14, 32, 17):` `hh\:mm\:ss --> "14:32:17"`

## <a name="the-d-custom-format-specifier"></a>"d" 사용자 지정 형식 지정자

"d" 사용자 지정 형식 지정자는 시간 간격의 전체 일 수를 나타내는 [TimeSpan.Days](xref:System.TimeSpan.Days) 속성 값을 출력합니다. 값이 두 자리 이상인 경우에도 [TimeSpan](xref:System.TimeSpan) 값에 전체 일 수를 출력합니다. [TimeSpan.Days](xref:System.TimeSpan.Days) 속성 값이 0이면 지정자는 "0"을 출력합니다.

"d" 사용자 지정 형식 지정자만 단독으로 사용하는 경우 표준 형식 문자열로 잘못 해석되지 않도록 "%d"를 지정합니다. 다음 예제에서 이에 대해 설명합니다.

```csharp
TimeSpan ts1 = new TimeSpan(16, 4, 3, 17, 250);
Console.WriteLine(ts1.ToString("%d"));
// Displays 16
```

```vb
Dim ts As New TimeSpan(16, 4, 3, 17, 250)
Console.WriteLine(ts.ToString("%d"))
' Displays 16 
```

다음 예제에서는 "d" 사용자 지정 형식 지정자를 사용하는 방법을 보여 줍니다.

```csharp
TimeSpan ts2 = new TimeSpan(4, 3, 17);
Console.WriteLine(ts2.ToString(xref:"d\.hh\:mm\:ss"));

TimeSpan ts3 = new TimeSpan(3, 4, 3, 17);
Console.WriteLine(ts3.ToString(xref:"d\.hh\:mm\:ss"));
// The example displays the following output:
//       0.04:03:17
//       3.04:03:17
```

```vb
Dim ts2 As New TimeSpan(4, 3, 17)
Console.WriteLine(ts2.ToString("d\.hh\:mm\:ss"))

Dim ts3 As New TimeSpan(3, 4, 3, 17)
Console.WriteLine(ts3.ToString("d\.hh\:mm\:ss"))
' The example displays the following output:
'       0.04:03:17
'       3.04:03:17
```

## <a name="the-dddddddddd-custom-format-specifiers"></a>"dd"-"dddddddd" 사용자 지정 서식 지정자

"dd", "ddd", "dddd", "ddddd", "dddddd", "ddddddd" 및 "dddddddd" 사용자 지정 형식 지정자는 시간 간격의 전체 일 수를 나타내는 [TimeSpan.Days](xref:System.TimeSpan.Days) 속성 값을 출력합니다. 

출력 문자열에는 형식 지정자에 "d" 문자 수로 지정된 최소 자릿수가 포함되며 필요에 따라 앞에 0으로 채워집니다. 일 수의 자릿수가 형식 지정자의 "d" 문자 수를 초과할 경우 전체 일 수가 결과 문자열에 출력됩니다.

다음 예제에서는 이 형식 지정자를 사용하여 두 [TimeSpan](xref:System.TimeSpan) 값의 문자열 표현을 나타냅니다. 첫 번째 시간 간격의 일 구성 요소 값은 0입니다. 두 번째 시간 간격의 일 구성 요소 값은 365입니다.

```csharp
TimeSpan ts1 = new TimeSpan(0, 23, 17, 47);
TimeSpan ts2 = new TimeSpan(365, 21, 19, 45);

for (int ctr = 2; ctr <= 8; ctr++)
{
   string fmt = new String('d', ctr) + @"\.hh\:mm\:ss";
   Console.WriteLine("{0} --> {1:" + fmt + "}", fmt, ts1);  
   Console.WriteLine("{0} --> {1:" + fmt + "}", fmt, ts2);
   Console.WriteLine();
}  
// The example displays the following output:
//       dd\.hh\:mm\:ss --> 00.23:17:47
//       dd\.hh\:mm\:ss --> 365.21:19:45
//       
//       ddd\.hh\:mm\:ss --> 000.23:17:47
//       ddd\.hh\:mm\:ss --> 365.21:19:45
//       
//       dddd\.hh\:mm\:ss --> 0000.23:17:47
//       dddd\.hh\:mm\:ss --> 0365.21:19:45
//       
//       ddddd\.hh\:mm\:ss --> 00000.23:17:47
//       ddddd\.hh\:mm\:ss --> 00365.21:19:45
//       
//       dddddd\.hh\:mm\:ss --> 000000.23:17:47
//       dddddd\.hh\:mm\:ss --> 000365.21:19:45
//       
//       ddddddd\.hh\:mm\:ss --> 0000000.23:17:47
//       ddddddd\.hh\:mm\:ss --> 0000365.21:19:45
//       
//       dddddddd\.hh\:mm\:ss --> 00000000.23:17:47
//       dddddddd\.hh\:mm\:ss --> 00000365.21:19:45
```

```vb
Dim ts1 As New TimeSpan(0, 23, 17, 47)
Dim ts2 As New TimeSpan(365, 21, 19, 45)

For ctr As Integer = 2 To 8
   Dim fmt As String = New String("d"c, ctr) + "\.hh\:mm\:ss"
   Console.WriteLine("{0} --> {1:" + fmt + "}", fmt, ts1) 
   Console.WriteLine("{0} --> {1:" + fmt + "}", fmt, ts2)
   Console.WriteLine()
Next  
' The example displays the following output:
'       dd\.hh\:mm\:ss --> 00.23:17:47
'       dd\.hh\:mm\:ss --> 365.21:19:45
'       
'       ddd\.hh\:mm\:ss --> 000.23:17:47
'       ddd\.hh\:mm\:ss --> 365.21:19:45
'       
'       dddd\.hh\:mm\:ss --> 0000.23:17:47
'       dddd\.hh\:mm\:ss --> 0365.21:19:45
'       
'       ddddd\.hh\:mm\:ss --> 00000.23:17:47
'       ddddd\.hh\:mm\:ss --> 00365.21:19:45
'       
'       dddddd\.hh\:mm\:ss --> 000000.23:17:47
'       dddddd\.hh\:mm\:ss --> 000365.21:19:45
'       
'       ddddddd\.hh\:mm\:ss --> 0000000.23:17:47
'       ddddddd\.hh\:mm\:ss --> 0000365.21:19:45
'       
'       dddddddd\.hh\:mm\:ss --> 00000000.23:17:47
'       dddddddd\.hh\:mm\:ss --> 00000365.21:19:45
```

## <a name="the-h-custom-format-specifier"></a>"h" 사용자 지정 형식 지정자

"h" 사용자 지정 형식 지정자는 일 구성 요소의 일부로 계산되지 않은 시간 간격의 전체 시간 수를 나타내는 [TimeSpan.Hours](xref:System.TimeSpan.Hours) 속성 값을 출력합니다. [TimeSpan.Hours](xref:System.TimeSpan.Hours) 속성 값이 0-9이면 한 자리 문자열 값을 반환하고, [TimeSpan.Hours](xref:System.TimeSpan.Hours) 속성 값이 10-23이면 두 자리 문자열 값을 반환합니다.

"h" 사용자 지정 형식 지정자만 단독으로 사용하는 경우 표준 형식 문자열로 잘못 해석되지 않도록 "%h"를 지정합니다. 다음 예제에서 이에 대해 설명합니다.

```csharp
TimeSpan ts = new TimeSpan(3, 42, 0);
Console.WriteLine("{0:%h} hours {0:%m} minutes", ts);
// The example displays the following output:
//       3 hours 42 minutes
```

```vb
Dim ts As New TimeSpan(3, 42, 0)
Console.WriteLine("{0:%h} hours {0:%m} minutes", ts)
' The example displays the following output:
'       3 hours 42 minutes
```

일반적으로 구문 분석 작업에서 하나의 숫자만 포함하는 입력 문자열은 일 수로 해석됩니다. "%h" 사용자 지정 형식 지정자를 대신 사용하여 숫자 문자열을 시간 수로 해석할 수 있습니다. 다음 예제에서 이에 대해 설명합니다.

```csharp
string value = "8";
TimeSpan interval;
if (TimeSpan.TryParseExact(value, "%h", null, out interval))
   Console.WriteLine(interval.ToString("c"));
else
   Console.WriteLine("Unable to convert '{0}' to a time interval", 
                     value);   
// The example displays the following output:
//       08:00:00
```

```vb
Dim value As String = "8"
Dim interval As TimeSpan
If TimeSpan.TryParseExact(value, "%h", Nothing, interval) Then
   Console.WriteLine(interval.ToString("c"))
Else
   Console.WriteLine("Unable to convert '{0}' to a time interval", 
                     value)   
End If   
' The example displays the following output:
'       08:00:00
```

다음 예제에서는 "h" 사용자 지정 형식 지정자를 사용하는 방법을 보여 줍니다.

```csharp
TimeSpan ts1 = new TimeSpan(14, 3, 17);
Console.WriteLine(ts1.ToString(xref:"d\.h\:mm\:ss"));

TimeSpan ts2 = new TimeSpan(3, 4, 3, 17);
Console.WriteLine(ts2.ToString(xref:"d\.h\:mm\:ss"));
// The example displays the following output:
//       0.14:03:17
//       3.4:03:17
```

```vb
Dim ts1 As New TimeSpan(14, 3, 17)
Console.WriteLine(ts1.ToString("d\.h\:mm\:ss"))

Dim ts2 As New TimeSpan(3, 4, 3, 17)
Console.WriteLine(ts2.ToString("d\.h\:mm\:ss"))
' The example displays the following output:
'       0.14:03:17
'       3.4:03:17
```

## <a name="the-hh-custom-format-specifier"></a>"hh" 사용자 지정 형식 지정자

"hh" 사용자 지정 형식 지정자는 일 구성 요소의 일부로 계산되지 않은 시간 간격의 전체 시간 수를 나타내는 [TimeSpan.Hours](xref:System.TimeSpan.Hours) 속성 값을 출력합니다. 0-9 값의 경우 출력 문자열 앞에 0이 포함됩니다. 

일반적으로 구문 분석 작업에서 하나의 숫자만 포함하는 입력 문자열은 일 수로 해석됩니다. "hh" 사용자 지정 형식 지정자를 대신 사용하여 숫자 문자열을 시간 수로 해석할 수 있습니다. 다음 예제에서 이에 대해 설명합니다.

```csharp
string value = "08";
TimeSpan interval;
if (TimeSpan.TryParseExact(value, "hh", null, out interval))
   Console.WriteLine(interval.ToString("c"));
else
   Console.WriteLine("Unable to convert '{0}' to a time interval", 
                     value);   
// The example displays the following output:
//       08:00:00 
```

```vb
Dim value As String = "08"
Dim interval As TimeSpan
If TimeSpan.TryParseExact(value, "hh", Nothing, interval) Then
   Console.WriteLine(interval.ToString("c"))
Else
   Console.WriteLine("Unable to convert '{0}' to a time interval", 
                     value)   
End If   
' The example displays the following output:
'       08:00:00
```

다음 예제에서는 "hh" 사용자 지정 형식 지정자를 사용하는 방법을 보여 줍니다.

```csharp
TimeSpan ts1 = new TimeSpan(14, 3, 17);
Console.WriteLine(ts1.ToString(xref:"d\.hh\:mm\:ss"));

TimeSpan ts2 = new TimeSpan(3, 4, 3, 17);
Console.WriteLine(ts2.ToString(xref:"d\.hh\:mm\:ss"));
// The example displays the following output:
//       0.14:03:17
//       3.04:03:17
```

```vb
Dim ts1 As New TimeSpan(14, 3, 17)
Console.WriteLine(ts1.ToString("d\.hh\:mm\:ss"))

Dim ts2 As New TimeSpan(3, 4, 3, 17)
Console.WriteLine(ts2.ToString("d\.hh\:mm\:ss"))
' The example displays the following output:
'       0.14:03:17
'       3.04:03:17
```

## <a name="the-m-custom-format-specifier"></a>"m" 사용자 지정 형식 지정자

"m" 사용자 지정 형식 지정자는 일 구성 요소의 일부로 계산되지 않은 시간 간격의 전체 분 수를 나타내는 [TimeSpan.Minutes](xref:System.TimeSpan.Minutes) 속성 값을 출력합니다. [TimeSpan.Minutes](xref:System.TimeSpan.Minutes) 속성 값이 0-9이면 한 자리 문자열 값을 반환하고, [TimeSpan.Minutes](xref:System.TimeSpan.Minutes) 속성 값이 10-59이면 두 자리 문자열 값을 반환합니다.

"m" 사용자 지정 형식 지정자만 단독으로 사용하는 경우 표준 형식 문자열로 잘못 해석되지 않도록 "%m"을 지정합니다. 다음 예제에서 이에 대해 설명합니다.

```csharp
TimeSpan ts = new TimeSpan(3, 42, 0);
Console.WriteLine("{0:%h} hours {0:%m} minutes", ts);
// The example displays the following output:
//       3 hours 42 minutes
```

```vb
Dim ts As New TimeSpan(3, 42, 0)
Console.WriteLine("{0:%h} hours {0:%m} minutes", ts)
' The example displays the following output:
'       3 hours 42 minutes
```

일반적으로 구문 분석 작업에서 하나의 숫자만 포함하는 입력 문자열은 일 수로 해석됩니다. "%m" 사용자 지정 형식 지정자를 대신 사용하여 숫자 문자열을 분 수로 해석할 수 있습니다. 다음 예제에서 이에 대해 설명합니다.

```csharp
string value = "3";
TimeSpan interval;
if (TimeSpan.TryParseExact(value, "%m", null, out interval))
   Console.WriteLine(interval.ToString("c"));
else
   Console.WriteLine("Unable to convert '{0}' to a time interval", 
                     value);   
// The example displays the following output:
//       00:03:00
```

```vb
Dim value As String = "3"
Dim interval As TimeSpan
If TimeSpan.TryParseExact(value, "%m", Nothing, interval) Then
   Console.WriteLine(interval.ToString("c"))
Else
   Console.WriteLine("Unable to convert '{0}' to a time interval", 
                     value)   
End If   
' The example displays the following output:
'       00:03:00                              
```

다음 예제에서는 "m" 사용자 지정 형식 지정자를 사용하는 방법을 보여 줍니다.

```csharp
TimeSpan ts1 = new TimeSpan(0, 6, 32);
Console.WriteLine("{0:m\\:ss} minutes", ts1);

TimeSpan ts2 = new TimeSpan(3, 4, 3, 17);
Console.WriteLine("Elapsed time: {0:m\\:ss}", ts2);
// The example displays the following output:
//       6:32 minutes
//       Elapsed time: 18:44
```

```vb
Dim ts1 As New TimeSpan(0, 6, 32)
Console.WriteLine("{0:m\:ss} minutes", ts1)

Dim ts2 As New TimeSpan(0, 18, 44)
Console.WriteLine("Elapsed time: {0:m\:ss}", ts2)
' The example displays the following output:
'       6:32 minutes
'       Elapsed time: 18:44
```

## <a name="the-mm-custom-format-specifier"></a>"mm" 사용자 지정 형식 지정자

"mm" 사용자 지정 형식 지정자는 시간 또는 일 구성 요소의 일부로 포함되지 않은 시간 간격의 전체 분 수를 나타내는 [TimeSpan.Minutes](xref:System.TimeSpan.Minutes) 속성 값을 출력합니다. 0-9 값의 경우 출력 문자열 앞에 0이 포함됩니다. 

일반적으로 구문 분석 작업에서 하나의 숫자만 포함하는 입력 문자열은 일 수로 해석됩니다. "mm" 사용자 지정 형식 지정자를 대신 사용하여 숫자 문자열을 분 수로 해석할 수 있습니다. 다음 예제에서 이에 대해 설명합니다.

```csharp
string value = "07";
TimeSpan interval;
if (TimeSpan.TryParseExact(value, "mm", null, out interval))
   Console.WriteLine(interval.ToString("c"));
else
   Console.WriteLine("Unable to convert '{0}' to a time interval", 
                     value);   
// The example displays the following output:
//       00:07:00
```

```vb
Dim value As String = "05"
Dim interval As TimeSpan
If TimeSpan.TryParseExact(value, "mm", Nothing, interval) Then
   Console.WriteLine(interval.ToString("c"))
Else
   Console.WriteLine("Unable to convert '{0}' to a time interval", 
                     value)   
End If   
' The example displays the following output:
'       00:05:00
```

다음 예제에서는 "mm" 사용자 지정 형식 지정자를 사용하는 방법을 보여 줍니다.

```csharp
TimeSpan departTime = new TimeSpan(11, 12, 00);
TimeSpan arriveTime = new TimeSpan(16, 28, 00);
Console.WriteLine("Travel time: {0:hh\\:mm}", 
                  arriveTime - departTime);
// The example displays the following output:
//       Travel time: 05:16
```

```vb
Dim departTime As New TimeSpan(11, 12, 00)
Dim arriveTime As New TimeSpan(16, 28, 00)
Console.WriteLine("Travel time: {0:hh\:mm}", 
                  arriveTime - departTime)
' The example displays the following output:
'       Travel time: 05:16
```

## <a name="the-s-custom-format-specifier"></a>"s" 사용자 지정 형식 지정자

"s" 사용자 지정 형식 지정자는 시간, 일 또는 분 구성 요소의 일부로 포함되지 않은 시간 간격의 전체 초 수를 나타내는 [TimeSpan.Seconds](xref:System.TimeSpan.Seconds) 속성 값을 출력합니다. [TimeSpan.Seconds](xref:System.TimeSpan.Seconds) 속성 값이 0-9이면 한 자리 문자열 값을 반환하고, [TimeSpan.Seconds](xref:System.TimeSpan.Seconds) 속성 값이 10-59이면 두 자리 문자열 값을 반환합니다. 

"s" 사용자 지정 형식 지정자만 단독으로 사용하는 경우 표준 형식 문자열로 잘못 해석되지 않도록 "%s"를 지정합니다. 다음 예제에서 이에 대해 설명합니다.

```csharp
TimeSpan ts = TimeSpan.FromSeconds(12.465);
Console.WriteLine(ts.ToString("%s"));
// The example displays the following output:
//       12
```

```vb
Dim ts As TimeSpan = TimeSpan.FromSeconds(12.465)
Console.WriteLine(ts.ToString("%s"))
' The example displays the following output:
'       12
```

일반적으로 구문 분석 작업에서 하나의 숫자만 포함하는 입력 문자열은 일 수로 해석됩니다. "%s" 사용자 지정 형식 지정자를 대신 사용하여 숫자 문자열을 초 수로 해석할 수 있습니다. 다음 예제에서 이에 대해 설명합니다.

```csharp
string value = "9";
TimeSpan interval;
if (TimeSpan.TryParseExact(value, "%s", null, out interval))
   Console.WriteLine(interval.ToString("c"));
else
   Console.WriteLine("Unable to convert '{0}' to a time interval", 
                     value);   
// The example displays the following output:
//       00:00:09
```

```vb
Dim value As String = "9"
Dim interval As TimeSpan
If TimeSpan.TryParseExact(value, "%s", Nothing, interval) Then
   Console.WriteLine(interval.ToString("c"))
Else
   Console.WriteLine("Unable to convert '{0}' to a time interval", 
                     value)   
End If   
' The example displays the following output:
'       00:00:09
```

다음 예제에서는 "s" 사용자 지정 형식 지정자를 사용하는 방법을 보여 줍니다.

```csharp
TimeSpan startTime = new TimeSpan(0, 12, 30, 15, 0);
TimeSpan endTime = new TimeSpan(0, 12, 30, 21, 3);
Console.WriteLine(xref:"Elapsed Time: {0:s\:fff} seconds", 
                  endTime - startTime);
// The example displays the following output:
//       Elapsed Time: 6:003 seconds      
```

```vb
Dim startTime As New TimeSpan(0, 12, 30, 15, 0)
Dim endTime As New TimeSpan(0, 12, 30, 21, 3)
Console.WriteLine("Elapsed Time: {0:s\:fff} seconds", 
                  endTime - startTime)
' The example displays the following output:
'       Elapsed Time: 6:003 seconds
```

## <a name="the-ss-custom-format-specifier"></a>"ss" 사용자 지정 형식 지정자

"ss" 사용자 지정 형식 지정자는 시간, 일 또는 분 구성 요소의 일부로 포함되지 않은 시간 간격의 전체 초 수를 나타내는 [TimeSpan.Seconds](xref:System.TimeSpan.Seconds) 속성 값을 출력합니다. 0-9 값의 경우 출력 문자열 앞에 0이 포함됩니다. 

일반적으로 구문 분석 작업에서 하나의 숫자만 포함하는 입력 문자열은 일 수로 해석됩니다. "ss" 사용자 지정 형식 지정자를 대신 사용하여 숫자 문자열을 초 수로 해석할 수 있습니다. 다음 예제에서 이에 대해 설명합니다.

```csharp
string[] values = { "49", "9", "06" };
TimeSpan interval;
foreach (string value in values)
{
   if (TimeSpan.TryParseExact(value, "ss", null, out interval))
      Console.WriteLine(interval.ToString("c"));
   else
      Console.WriteLine("Unable to convert '{0}' to a time interval", 
                        value);   
}
// The example displays the following output:
//       00:00:49
//       Unable to convert '9' to a time interval
//       00:00:06
```

```vb
Dim values() As String = { "49", "9", "06" }
Dim interval As TimeSpan
For Each value As String In values
   If TimeSpan.TryParseExact(value, "ss", Nothing, interval) Then
      Console.WriteLine(interval.ToString("c"))
   Else
      Console.WriteLine("Unable to convert '{0}' to a time interval", 
                        value)   
   End If   
Next   
' The example displays the following output:
'       00:00:49
'       Unable to convert '9' to a time interval
'       00:00:06
```

다음 예제에서는 "ss" 사용자 지정 형식 지정자를 사용하는 방법을 보여 줍니다.

```csharp
TimeSpan interval1 = TimeSpan.FromSeconds(12.60);
Console.WriteLine(interval1.ToString(xref:"ss\.fff"));

TimeSpan interval2 = TimeSpan.FromSeconds(6.485);
Console.WriteLine(interval2.ToString(xref:"ss\.fff"));
// The example displays the following output:
//       12.600
//       06.485
```

```vb
Dim interval1 As TimeSpan = TimeSpan.FromSeconds(12.60)
Console.WriteLine(interval1.ToString("ss\.fff"))
Dim interval2 As TimeSpan = TimeSpan.FromSeconds(6.485)
Console.WriteLine(interval2.ToString("ss\.fff"))
' The example displays the following output:
'       12.600
'       06.485
```

## <a name="the-f-custom-format-specifier"></a>"f" 사용자 지정 형식 지정자

"f" 사용자 지정 형식 지정자는 시간 간격의 1/10초를 출력합니다. 형식 지정 작업에서 나머지 소수 자릿수는 잘립니다. [TimeSpan.ParseExact](xref:System.TimeSpan.ParseExact(System.String,System.String,System.IFormatProvider)) 또는 [TimeSpan.TryParseExact](xref:System.TimeSpan.TryParseExact(System.String,System.String,System.IFormatProvider,System.Globalization.TimeSpanStyles,System.TimeSpan@)) 메서드를 호출하는 구문 분석 작업에서 입력 문자열에는 정확히 소수 한 자리가 포함되어야 합니다. 

"f" 사용자 지정 형식 지정자만 단독으로 사용하는 경우 표준 형식 문자열로 잘못 해석되지 않도록 "%f"를 지정합니다.

다음 예제에서는 "f" 사용자 지정 형식 지정자를 사용하여 [TimeSpan](xref:System.TimeSpan) 값에 1/10초를 표시합니다. "f"가 먼저 유일한 형식 지정자로 사용된 다음 사용자 지정 형식 문자열에서 "s" 지정자와 함께 사용됩니다.

```csharp
TimeSpan ts = new TimeSpan(1003498765432);
string fmt;
Console.WriteLine(ts.ToString("c"));
Console.WriteLine();

for (int ctr = 1; ctr <= 7; ctr++) {
   fmt = new String('f', ctr);
   if (fmt.Length == 1) fmt = "%" + fmt;
   Console.WriteLine("{0,10}: {1:" + fmt + "}", fmt, ts);
} 
Console.WriteLine();

for (int ctr = 1; ctr <= 7; ctr++) {
   fmt = new String('f', ctr);
   Console.WriteLine("{0,10}: {1:s\\." + fmt + "}", "s\\." + fmt, ts);
}
// The example displays the following output:
//               %f: 8
//               ff: 87
//              fff: 876
//             ffff: 8765
//            fffff: 87654
//           ffffff: 876543
//          fffffff: 8765432
//       
//              s\.f: 29.8
//             s\.ff: 29.87
//            s\.fff: 29.876
//           s\.ffff: 29.8765
//          s\.fffff: 29.87654
//         s\.ffffff: 29.876543
//        s\.fffffff: 29.8765432
```

```vb
Dim ts As New TimeSpan(1003498765432)
Dim fmt As String
Console.WriteLine(ts.ToString("c"))
Console.WriteLine()

For ctr = 1 To 7
   fmt = New String("f"c, ctr)
   If fmt.Length = 1 Then fmt = "%" + fmt
   Console.WriteLine("{0,10}: {1:" + fmt + "}", fmt, ts)
Next 
Console.WriteLine()

For ctr = 1 To 7
   fmt = New String("f"c, ctr)
   Console.WriteLine("{0,10}: {1:s\." + fmt + "}", "s\." + fmt, ts)
Next
' The example displays the following output:
'            %f: 8
'            ff: 87
'           fff: 876
'          ffff: 8765
'         fffff: 87654
'        ffffff: 876543
'       fffffff: 8765432
'    
'           s\.f: 29.8
'          s\.ff: 29.87
'         s\.fff: 29.876
'        s\.ffff: 29.8765
'       s\.fffff: 29.87654
'      s\.ffffff: 29.876543
'     s\.fffffff: 29.8765432
```

## <a name="the-ff-custom-format-specifier"></a>"ff" 사용자 지정 형식 지정자

"ff" 사용자 지정 형식 지정자는 시간 간격의 1/100초를 출력합니다. 형식 지정 작업에서 나머지 소수 자릿수는 잘립니다. [TimeSpan.ParseExact](xref:System.TimeSpan.ParseExact(System.String,System.String,System.IFormatProvider)) 또는 [TimeSpan.TryParseExact](xref:System.TimeSpan.TryParseExact(System.String,System.String,System.IFormatProvider,System.Globalization.TimeSpanStyles,System.TimeSpan@)) 메서드를 호출하는 구문 분석 작업에서 입력 문자열에는 정확히 소수 두 자리가 포함되어야 합니다. 

다음 예제에서는 "ff" 사용자 지정 형식 지정자를 사용하여 [TimeSpan](xref:System.TimeSpan) 값에 1/100초를 표시합니다. "ff"가 먼저 유일한 형식 지정자로 사용된 다음 사용자 지정 형식 문자열에서 "s" 지정자와 함께 사용됩니다.

```csharp
TimeSpan ts = new TimeSpan(1003498765432);
string fmt;
Console.WriteLine(ts.ToString("c"));
Console.WriteLine();

for (int ctr = 1; ctr <= 7; ctr++) {
   fmt = new String('f', ctr);
   if (fmt.Length == 1) fmt = "%" + fmt;
   Console.WriteLine("{0,10}: {1:" + fmt + "}", fmt, ts);
} 
Console.WriteLine();

for (int ctr = 1; ctr <= 7; ctr++) {
   fmt = new String('f', ctr);
   Console.WriteLine("{0,10}: {1:s\\." + fmt + "}", "s\\." + fmt, ts);
}
// The example displays the following output:
//               %f: 8
//               ff: 87
//              fff: 876
//             ffff: 8765
//            fffff: 87654
//           ffffff: 876543
//          fffffff: 8765432
//       
//              s\.f: 29.8
//             s\.ff: 29.87
//            s\.fff: 29.876
//           s\.ffff: 29.8765
//          s\.fffff: 29.87654
//         s\.ffffff: 29.876543
//        s\.fffffff: 29.8765432
```

```vb
Dim ts As New TimeSpan(1003498765432)
Dim fmt As String
Console.WriteLine(ts.ToString("c"))
Console.WriteLine()

For ctr = 1 To 7
   fmt = New String("f"c, ctr)
   If fmt.Length = 1 Then fmt = "%" + fmt
   Console.WriteLine("{0,10}: {1:" + fmt + "}", fmt, ts)
Next 
Console.WriteLine()

For ctr = 1 To 7
   fmt = New String("f"c, ctr)
   Console.WriteLine("{0,10}: {1:s\." + fmt + "}", "s\." + fmt, ts)
Next
' The example displays the following output:
'            %f: 8
'            ff: 87
'           fff: 876
'          ffff: 8765
'         fffff: 87654
'        ffffff: 876543
'       fffffff: 8765432
'    
'           s\.f: 29.8
'          s\.ff: 29.87
'         s\.fff: 29.876
'        s\.ffff: 29.8765
'       s\.fffff: 29.87654
'      s\.ffffff: 29.876543
'     s\.fffffff: 29.8765432
```

## <a name="the-fff-custom-format-specifier"></a>"fff" 사용자 지정 형식 지정자

"fff" 사용자 지정 형식 지정자(세 개의 "f" 문자 포함)는 시간 간격의 밀리초를 출력합니다. 형식 지정 작업에서 나머지 소수 자릿수는 잘립니다. [TimeSpan.ParseExact](xref:System.TimeSpan.ParseExact(System.String,System.String,System.IFormatProvider)) 또는 [TimeSpan.TryParseExact](xref:System.TimeSpan.TryParseExact(System.String,System.String,System.IFormatProvider,System.Globalization.TimeSpanStyles,System.TimeSpan@)) 메서드를 호출하는 구문 분석 작업에서 입력 문자열에는 정확히 소수 세 자리가 포함되어야 합니다. 

다음 예제에서는 "fff" 사용자 지정 형식 지정자를 사용하여 [TimeSpan](xref:System.TimeSpan) 값에 밀리초를 표시합니다. "fff"가 먼저 유일한 형식 지정자로 사용된 다음 사용자 지정 형식 문자열에서 "s" 지정자와 함께 사용됩니다.

```csharp
TimeSpan ts = new TimeSpan(1003498765432);
string fmt;
Console.WriteLine(ts.ToString("c"));
Console.WriteLine();

for (int ctr = 1; ctr <= 7; ctr++) {
   fmt = new String('f', ctr);
   if (fmt.Length == 1) fmt = "%" + fmt;
   Console.WriteLine("{0,10}: {1:" + fmt + "}", fmt, ts);
} 
Console.WriteLine();

for (int ctr = 1; ctr <= 7; ctr++) {
   fmt = new String('f', ctr);
   Console.WriteLine("{0,10}: {1:s\\." + fmt + "}", "s\\." + fmt, ts);
}
// The example displays the following output:
//               %f: 8
//               ff: 87
//              fff: 876
//             ffff: 8765
//            fffff: 87654
//           ffffff: 876543
//          fffffff: 8765432
//       
//              s\.f: 29.8
//             s\.ff: 29.87
//            s\.fff: 29.876
//           s\.ffff: 29.8765
//          s\.fffff: 29.87654
//         s\.ffffff: 29.876543
//        s\.fffffff: 29.8765432
```

```vb
Dim ts As New TimeSpan(1003498765432)
Dim fmt As String
Console.WriteLine(ts.ToString("c"))
Console.WriteLine()

For ctr = 1 To 7
   fmt = New String("f"c, ctr)
   If fmt.Length = 1 Then fmt = "%" + fmt
   Console.WriteLine("{0,10}: {1:" + fmt + "}", fmt, ts)
Next 
Console.WriteLine()

For ctr = 1 To 7
   fmt = New String("f"c, ctr)
   Console.WriteLine("{0,10}: {1:s\." + fmt + "}", "s\." + fmt, ts)
Next
' The example displays the following output:
'            %f: 8
'            ff: 87
'           fff: 876
'          ffff: 8765
'         fffff: 87654
'        ffffff: 876543
'       fffffff: 8765432
'    
'           s\.f: 29.8
'          s\.ff: 29.87
'         s\.fff: 29.876
'        s\.ffff: 29.8765
'       s\.fffff: 29.87654
'      s\.ffffff: 29.876543
'     s\.fffffff: 29.8765432
```

## <a name="the-ffff-custom-format-specifier"></a>"ffff" 사용자 지정 형식 지정자
"ffff" 사용자 지정 형식 지정자(네 개의 "f" 문자 포함)는 시간 간격의 1/10000초를 출력합니다. 형식 지정 작업에서 나머지 소수 자릿수는 잘립니다. [TimeSpan.ParseExact](xref:System.TimeSpan.ParseExact(System.String,System.String,System.IFormatProvider)) 또는 [TimeSpan.TryParseExact](xref:System.TimeSpan.TryParseExact(System.String,System.String,System.IFormatProvider,System.Globalization.TimeSpanStyles,System.TimeSpan@)) 메서드를 호출하는 구문 분석 작업에서 입력 문자열에는 정확히 소수 네 자리가 포함되어야 합니다. 

다음 예제에서는 "ffff" 사용자 지정 형식 지정자를 사용하여 [TimeSpan](xref:System.TimeSpan) 값에 1/10000초를 표시합니다. "ffff"가 먼저 유일한 형식 지정자로 사용된 다음 사용자 지정 형식 문자열에서 "s" 지정자와 함께 사용됩니다.

```csharp
TimeSpan ts = new TimeSpan(1003498765432);
string fmt;
Console.WriteLine(ts.ToString("c"));
Console.WriteLine();

for (int ctr = 1; ctr <= 7; ctr++) {
   fmt = new String('f', ctr);
   if (fmt.Length == 1) fmt = "%" + fmt;
   Console.WriteLine("{0,10}: {1:" + fmt + "}", fmt, ts);
} 
Console.WriteLine();

for (int ctr = 1; ctr <= 7; ctr++) {
   fmt = new String('f', ctr);
   Console.WriteLine("{0,10}: {1:s\\." + fmt + "}", "s\\." + fmt, ts);
}
// The example displays the following output:
//               %f: 8
//               ff: 87
//              fff: 876
//             ffff: 8765
//            fffff: 87654
//           ffffff: 876543
//          fffffff: 8765432
//       
//              s\.f: 29.8
//             s\.ff: 29.87
//            s\.fff: 29.876
//           s\.ffff: 29.8765
//          s\.fffff: 29.87654
//         s\.ffffff: 29.876543
//        s\.fffffff: 29.8765432
```

```vb
Dim ts As New TimeSpan(1003498765432)
Dim fmt As String
Console.WriteLine(ts.ToString("c"))
Console.WriteLine()

For ctr = 1 To 7
   fmt = New String("f"c, ctr)
   If fmt.Length = 1 Then fmt = "%" + fmt
   Console.WriteLine("{0,10}: {1:" + fmt + "}", fmt, ts)
Next 
Console.WriteLine()

For ctr = 1 To 7
   fmt = New String("f"c, ctr)
   Console.WriteLine("{0,10}: {1:s\." + fmt + "}", "s\." + fmt, ts)
Next
' The example displays the following output:
'            %f: 8
'            ff: 87
'           fff: 876
'          ffff: 8765
'         fffff: 87654
'        ffffff: 876543
'       fffffff: 8765432
'    
'           s\.f: 29.8
'          s\.ff: 29.87
'         s\.fff: 29.876
'        s\.ffff: 29.8765
'       s\.fffff: 29.87654
'      s\.ffffff: 29.876543
'     s\.fffffff: 29.8765432
```

## <a name="the-fffff-custom-format-specifier"></a>"fffff" 사용자 지정 형식 지정자
"fffff" 사용자 지정 형식 지정자(다섯 개의 "f" 문자 포함)는 시간 간격의 1/100000초를 출력합니다. 형식 지정 작업에서 나머지 소수 자릿수는 잘립니다. [TimeSpan.ParseExact](xref:System.TimeSpan.ParseExact(System.String,System.String,System.IFormatProvider)) 또는 [TimeSpan.TryParseExact](xref:System.TimeSpan.TryParseExact(System.String,System.String,System.IFormatProvider,System.Globalization.TimeSpanStyles,System.TimeSpan@)) 메서드를 호출하는 구문 분석 작업에서 입력 문자열에는 정확히 소수 다섯 자리가 포함되어야 합니다. 

다음 예제에서는 "fffff" 사용자 지정 형식 지정자를 사용하여 [TimeSpan](xref:System.TimeSpan) 값에 1/100000초를 표시합니다. "fffff"가 먼저 유일한 형식 지정자로 사용된 다음 사용자 지정 형식 문자열에서 "s" 지정자와 함께 사용됩니다.

```csharp
TimeSpan ts = new TimeSpan(1003498765432);
string fmt;
Console.WriteLine(ts.ToString("c"));
Console.WriteLine();

for (int ctr = 1; ctr <= 7; ctr++) {
   fmt = new String('f', ctr);
   if (fmt.Length == 1) fmt = "%" + fmt;
   Console.WriteLine("{0,10}: {1:" + fmt + "}", fmt, ts);
} 
Console.WriteLine();

for (int ctr = 1; ctr <= 7; ctr++) {
   fmt = new String('f', ctr);
   Console.WriteLine("{0,10}: {1:s\\." + fmt + "}", "s\\." + fmt, ts);
}
// The example displays the following output:
//               %f: 8
//               ff: 87
//              fff: 876
//             ffff: 8765
//            fffff: 87654
//           ffffff: 876543
//          fffffff: 8765432
//       
//              s\.f: 29.8
//             s\.ff: 29.87
//            s\.fff: 29.876
//           s\.ffff: 29.8765
//          s\.fffff: 29.87654
//         s\.ffffff: 29.876543
//        s\.fffffff: 29.8765432 
```

```vb
Dim ts As New TimeSpan(1003498765432)
Dim fmt As String
Console.WriteLine(ts.ToString("c"))
Console.WriteLine()

For ctr = 1 To 7
   fmt = New String("f"c, ctr)
   If fmt.Length = 1 Then fmt = "%" + fmt
   Console.WriteLine("{0,10}: {1:" + fmt + "}", fmt, ts)
Next 
Console.WriteLine()

For ctr = 1 To 7
   fmt = New String("f"c, ctr)
   Console.WriteLine("{0,10}: {1:s\." + fmt + "}", "s\." + fmt, ts)
Next
' The example displays the following output:
'            %f: 8
'            ff: 87
'           fff: 876
'          ffff: 8765
'         fffff: 87654
'        ffffff: 876543
'       fffffff: 8765432
'    
'           s\.f: 29.8
'          s\.ff: 29.87
'         s\.fff: 29.876
'        s\.ffff: 29.8765
'       s\.fffff: 29.87654
'      s\.ffffff: 29.876543
'     s\.fffffff: 29.8765432
```

## <a name="the-ffffff-custom-format-specifier"></a>"ffffff" 사용자 지정 형식 지정자

"ffffff" 사용자 지정 형식 지정자(여섯 개의 "f" 문자 포함)는 시간 간격의 1/1000000초를 출력합니다. 형식 지정 작업에서 나머지 소수 자릿수는 잘립니다. [TimeSpan.ParseExact](xref:System.TimeSpan.ParseExact(System.String,System.String,System.IFormatProvider)) 또는 [TimeSpan.TryParseExact](xref:System.TimeSpan.TryParseExact(System.String,System.String,System.IFormatProvider,System.Globalization.TimeSpanStyles,System.TimeSpan@)) 메서드를 호출하는 구문 분석 작업에서 입력 문자열에는 정확히 소수 여섯 자리가 포함되어야 합니다. 

다음 예제에서는 "ffffff" 사용자 지정 형식 지정자를 사용하여 [TimeSpan](xref:System.TimeSpan) 값에 1/1000000초를 표시합니다. 먼저 유일한 형식 지정자로 사용된 다음 사용자 지정 형식 문자열에서 "s" 지정자와 함께 사용됩니다.

```csharp
TimeSpan ts = new TimeSpan(1003498765432);
string fmt;
Console.WriteLine(ts.ToString("c"));
Console.WriteLine();

for (int ctr = 1; ctr <= 7; ctr++) {
   fmt = new String('f', ctr);
   if (fmt.Length == 1) fmt = "%" + fmt;
   Console.WriteLine("{0,10}: {1:" + fmt + "}", fmt, ts);
} 
Console.WriteLine();

for (int ctr = 1; ctr <= 7; ctr++) {
   fmt = new String('f', ctr);
   Console.WriteLine("{0,10}: {1:s\\." + fmt + "}", "s\\." + fmt, ts);
}
// The example displays the following output:
//               %f: 8
//               ff: 87
//              fff: 876
//             ffff: 8765
//            fffff: 87654
//           ffffff: 876543
//          fffffff: 8765432
//       
//              s\.f: 29.8
//             s\.ff: 29.87
//            s\.fff: 29.876
//           s\.ffff: 29.8765
//          s\.fffff: 29.87654
//         s\.ffffff: 29.876543
//        s\.fffffff: 29.8765432 
```

```vb
Dim ts As New TimeSpan(1003498765432)
Dim fmt As String
Console.WriteLine(ts.ToString("c"))
Console.WriteLine()

For ctr = 1 To 7
   fmt = New String("f"c, ctr)
   If fmt.Length = 1 Then fmt = "%" + fmt
   Console.WriteLine("{0,10}: {1:" + fmt + "}", fmt, ts)
Next 
Console.WriteLine()

For ctr = 1 To 7
   fmt = New String("f"c, ctr)
   Console.WriteLine("{0,10}: {1:s\." + fmt + "}", "s\." + fmt, ts)
Next
' The example displays the following output:
'            %f: 8
'            ff: 87
'           fff: 876
'          ffff: 8765
'         fffff: 87654
'        ffffff: 876543
'       fffffff: 8765432
'    
'           s\.f: 29.8
'          s\.ff: 29.87
'         s\.fff: 29.876
'        s\.ffff: 29.8765
'       s\.fffff: 29.87654
'      s\.ffffff: 29.876543
'     s\.fffffff: 29.8765432
```

## <a name="the-fffffff-custom-format-specifier"></a>"fffffff" 사용자 지정 형식 지정자

"fffffff" 사용자 지정 형식 지정자(일곱 개의 "f" 문자 포함)는 시간 간격의 1/10000000초(또는 소수 자릿수 틱)를 출력합니다. [TimeSpan.ParseExact](xref:System.TimeSpan.ParseExact(System.String,System.String,System.IFormatProvider)) 또는 [TimeSpan.TryParseExact](xref:System.TimeSpan.TryParseExact(System.String,System.String,System.IFormatProvider,System.Globalization.TimeSpanStyles,System.TimeSpan@)) 메서드를 호출하는 구문 분석 작업에서 입력 문자열에는 정확히 소수 일곱 자리가 포함되어야 합니다. 

다음 예제에서는 "fffffff" 사용자 지정 형식 지정자를 사용하여 [TimeSpan](xref:System.TimeSpan) 값에 소수 자릿수 틱을 표시합니다. 먼저 유일한 형식 지정자로 사용된 다음 사용자 지정 형식 문자열에서 "s" 지정자와 함께 사용됩니다.

```csharp
TimeSpan ts = new TimeSpan(1003498765432);
string fmt;
Console.WriteLine(ts.ToString("c"));
Console.WriteLine();

for (int ctr = 1; ctr <= 7; ctr++) {
   fmt = new String('f', ctr);
   if (fmt.Length == 1) fmt = "%" + fmt;
   Console.WriteLine("{0,10}: {1:" + fmt + "}", fmt, ts);
} 
Console.WriteLine();

for (int ctr = 1; ctr <= 7; ctr++) {
   fmt = new String('f', ctr);
   Console.WriteLine("{0,10}: {1:s\\." + fmt + "}", "s\\." + fmt, ts);
}
// The example displays the following output:
//               %f: 8
//               ff: 87
//              fff: 876
//             ffff: 8765
//            fffff: 87654
//           ffffff: 876543
//          fffffff: 8765432
//       
//              s\.f: 29.8
//             s\.ff: 29.87
//            s\.fff: 29.876
//           s\.ffff: 29.8765
//          s\.fffff: 29.87654
//         s\.ffffff: 29.876543
//        s\.fffffff: 29.8765432 
```

```vb
Dim ts As New TimeSpan(1003498765432)
Dim fmt As String
Console.WriteLine(ts.ToString("c"))
Console.WriteLine()

For ctr = 1 To 7
   fmt = New String("f"c, ctr)
   If fmt.Length = 1 Then fmt = "%" + fmt
   Console.WriteLine("{0,10}: {1:" + fmt + "}", fmt, ts)
Next 
Console.WriteLine()

For ctr = 1 To 7
   fmt = New String("f"c, ctr)
   Console.WriteLine("{0,10}: {1:s\." + fmt + "}", "s\." + fmt, ts)
Next
' The example displays the following output:
'            %f: 8
'            ff: 87
'           fff: 876
'          ffff: 8765
'         fffff: 87654
'        ffffff: 876543
'       fffffff: 8765432
'    
'           s\.f: 29.8
'          s\.ff: 29.87
'         s\.fff: 29.876
'        s\.ffff: 29.8765
'       s\.fffff: 29.87654
'      s\.ffffff: 29.876543
'     s\.fffffff: 29.8765432
```

## <a name="the-f-custom-format-specifier"></a>"F" 사용자 지정 형식 지정자

"F" 사용자 지정 형식 지정자는 시간 간격의 1/10초를 출력합니다. 형식 지정 작업에서 나머지 소수 자릿수는 잘립니다. 시간 간격의 1/10초 값이 0이면 결과 문자열에 포함되지 않습니다. [TimeSpan.ParseExact](xref:System.TimeSpan.ParseExact(System.String,System.String,System.IFormatProvider)) 또는 [TimeSpan.TryParseExact](xref:System.TimeSpan.TryParseExact(System.String,System.String,System.IFormatProvider,System.Globalization.TimeSpanStyles,System.TimeSpan@)) 메서드를 호출하는 구문 분석 작업에서 1/10초 숫자 표시는 선택 사항입니다.

"F" 사용자 지정 형식 지정자만 단독으로 사용하는 경우 표준 형식 문자열로 잘못 해석되지 않도록 "%F"를 지정합니다.

다음 예제에서는 "F" 사용자 지정 형식 지정자를 사용하여 [TimeSpan](xref:System.TimeSpan) 값에 1/10초를 표시합니다. 또한 구문 분석 작업에서 이 사용자 지정 형식 지정자를 사용합니다.

```csharp
Console.WriteLine("Formatting:");
TimeSpan ts1 = TimeSpan.Parse("0:0:3.669");
Console.WriteLine("{0} ('%F') --> {0:%F}", ts1);

TimeSpan ts2 = TimeSpan.Parse("0:0:3.091");
Console.WriteLine("{0} ('ss\\.F') --> {0:ss\\.F}", ts2);
Console.WriteLine();

Console.WriteLine("Parsing:");
string[] inputs = { "0:0:03.", "0:0:03.1", "0:0:03.12" };
string fmt = @"h\:m\:ss\.F";
TimeSpan ts3;

foreach (string input in inputs) {
   if (TimeSpan.TryParseExact(input, fmt, null, out ts3))
      Console.WriteLine("{0} ('{1}') --> {2}", input, fmt, ts3);
   else
      Console.WriteLine("Cannot parse {0} with '{1}'.", 
                        input, fmt);
}                        
// The example displays the following output:
//       Formatting:
//       00:00:03.6690000 ('%F') --> 6
//       00:00:03.0910000 ('ss\.F') --> 03.
//       
//       Parsing:
//       0:0:03. ('h\:m\:ss\.F') --> 00:00:03
//       0:0:03.1 ('h\:m\:ss\.F') --> 00:00:03.1000000
//       Cannot parse 0:0:03.12 with 'h\:m\:ss\.F'.
```

```vb
Console.WriteLine("Formatting:")
Dim ts1 As TimeSpan = TimeSpan.Parse("0:0:3.669")
Console.WriteLine("{0} ('%F') --> {0:%F}", ts1)

Dim ts2 As TimeSpan = TimeSpan.Parse("0:0:3.091")
Console.WriteLine("{0} ('ss\.F') --> {0:ss\.F}", ts2)
Console.WriteLine()

Console.WriteLine("Parsing:")
Dim inputs() As String = { "0:0:03.", "0:0:03.1", "0:0:03.12" }
Dim fmt As String = "h\:m\:ss\.F"
Dim ts3 As TimeSpan

For Each input As String In inputs
   If TimeSpan.TryParseExact(input, fmt, Nothing, ts3)
      Console.WriteLine("{0} ('{1}') --> {2}", input, fmt, ts3)
   Else
      Console.WriteLine("Cannot parse {0} with '{1}'.", 
                        input, fmt)
   End If  
Next
' The example displays the following output:
'       Formatting:
'       00:00:03.6690000 ('%F') --> 6
'       00:00:03.0910000 ('ss\.F') --> 03.
'       
'       Parsing:
'       0:0:03. ('h\:m\:ss\.F') --> 00:00:03
'       0:0:03.1 ('h\:m\:ss\.F') --> 00:00:03.1000000
'       Cannot parse 0:0:03.12 with 'h\:m\:ss\.F'.
```

## <a name="the-ff-custom-format-specifier"></a>"FF" 사용자 지정 형식 지정자

"FF" 사용자 지정 형식 지정자는 시간 간격의 1/100초를 출력합니다. 형식 지정 작업에서 나머지 소수 자릿수는 잘립니다. 뒤에 오는 소수 자릿수 0이 있는 경우 결과 문자열에 포함되지 않습니다. [TimeSpan.ParseExact](xref:System.TimeSpan.ParseExact(System.String,System.String,System.IFormatProvider)) 또는 [TimeSpan.TryParseExact](xref:System.TimeSpan.TryParseExact(System.String,System.String,System.IFormatProvider,System.Globalization.TimeSpanStyles,System.TimeSpan@)) 메서드를 호출하는 구문 분석 작업에서 1/10초 및 1/100초 숫자 표시는 선택 사항입니다.

다음 예제에서는 "FF" 사용자 지정 형식 지정자를 사용하여 [TimeSpan](xref:System.TimeSpan) 값에 1/100초를 표시합니다. 또한 구문 분석 작업에서 이 사용자 지정 형식 지정자를 사용합니다.

```csharp
Console.WriteLine("Formatting:");
TimeSpan ts1 = TimeSpan.Parse("0:0:3.697");
Console.WriteLine("{0} ('FF') --> {0:FF}", ts1);

TimeSpan ts2 = TimeSpan.Parse("0:0:3.809");
Console.WriteLine("{0} ('ss\\.FF') --> {0:ss\\.FF}", ts2);
Console.WriteLine();

Console.WriteLine("Parsing:");
string[] inputs = { "0:0:03.", "0:0:03.1", "0:0:03.127" };
string fmt = @"h\:m\:ss\.FF";
TimeSpan ts3;

foreach (string input in inputs) {
   if (TimeSpan.TryParseExact(input, fmt, null, out ts3))
      Console.WriteLine("{0} ('{1}') --> {2}", input, fmt, ts3);
   else
      Console.WriteLine("Cannot parse {0} with '{1}'.", 
                        input, fmt);
}
// The example displays the following output:
//       Formatting:
//       00:00:03.6970000 ('FF') --> 69
//       00:00:03.8090000 ('ss\.FF') --> 03.8
//       
//       Parsing:
//       0:0:03. ('h\:m\:ss\.FF') --> 00:00:03
//       0:0:03.1 ('h\:m\:ss\.FF') --> 00:00:03.1000000
//       Cannot parse 0:0:03.127 with 'h\:m\:ss\.FF'. 
```

```vb
Console.WriteLine("Formatting:")
Dim ts1 As TimeSpan = TimeSpan.Parse("0:0:3.697")
Console.WriteLine("{0} ('FF') --> {0:FF}", ts1)

Dim ts2 As TimeSpan = TimeSpan.Parse("0:0:3.809")
Console.WriteLine("{0} ('ss\.FF') --> {0:ss\.FF}", ts2)
Console.WriteLine()

Console.WriteLine("Parsing:")
Dim inputs() As String = { "0:0:03.", "0:0:03.1", "0:0:03.127" }
Dim fmt As String = "h\:m\:ss\.FF"
Dim ts3 As TimeSpan

For Each input As String In inputs
   If TimeSpan.TryParseExact(input, fmt, Nothing, ts3)
      Console.WriteLine("{0} ('{1}') --> {2}", input, fmt, ts3)
   Else
      Console.WriteLine("Cannot parse {0} with '{1}'.", 
                        input, fmt)
   End If  
Next
' The example displays the following output:
'       Formatting:
'       00:00:03.6970000 ('FF') --> 69
'       00:00:03.8090000 ('ss\.FF') --> 03.8
'       
'       Parsing:
'       0:0:03. ('h\:m\:ss\.FF') --> 00:00:03
'       0:0:03.1 ('h\:m\:ss\.FF') --> 00:00:03.1000000
'       Cannot parse 0:0:03.127 with 'h\:m\:ss\.FF'.
```

## <a name="the-fff-custom-format-specifier"></a>"FFF" 사용자 지정 형식 지정자

"FFF" 사용자 지정 형식 지정자(세 개의 "F" 문자 포함)는 시간 간격의 밀리초를 출력합니다. 형식 지정 작업에서 나머지 소수 자릿수는 잘립니다. 뒤에 오는 소수 자릿수 0이 있는 경우 결과 문자열에 포함되지 않습니다. [TimeSpan.ParseExact](xref:System.TimeSpan.ParseExact(System.String,System.String,System.IFormatProvider)) 또는 [TimeSpan.TryParseExact](xref:System.TimeSpan.TryParseExact(System.String,System.String,System.IFormatProvider,System.Globalization.TimeSpanStyles,System.TimeSpan@)) 메서드를 호출하는 구문 분석 작업에서 1/10초, 1/100초 및 1/1000초 숫자 표시는 선택 사항입니다.

다음 예제에서는 "FFF" 사용자 지정 형식 지정자를 사용하여 [TimeSpan](xref:System.TimeSpan) 값에 1/1000초를 표시합니다. 또한 구문 분석 작업에서 이 사용자 지정 형식 지정자를 사용합니다.

```csharp
Console.WriteLine("Formatting:");
TimeSpan ts1 = TimeSpan.Parse("0:0:3.6974");
Console.WriteLine("{0} ('FFF') --> {0:FFF}", ts1);

TimeSpan ts2 = TimeSpan.Parse("0:0:3.8009");
Console.WriteLine("{0} ('ss\\.FFF') --> {0:ss\\.FFF}", ts2);
Console.WriteLine();

Console.WriteLine("Parsing:");
string[] inputs = { "0:0:03.", "0:0:03.12", "0:0:03.1279" };
string fmt = @"h\:m\:ss\.FFF";
TimeSpan ts3;

foreach (string input in inputs) {
   if (TimeSpan.TryParseExact(input, fmt, null, out ts3))
      Console.WriteLine("{0} ('{1}') --> {2}", input, fmt, ts3);
   else
      Console.WriteLine("Cannot parse {0} with '{1}'.", 
                        input, fmt);
}
// The example displays the following output:
//       Formatting:
//       00:00:03.6974000 ('FFF') --> 697
//       00:00:03.8009000 ('ss\.FFF') --> 03.8
//       
//       Parsing:
//       0:0:03. ('h\:m\:ss\.FFF') --> 00:00:03
//       0:0:03.12 ('h\:m\:ss\.FFF') --> 00:00:03.1200000
//       Cannot parse 0:0:03.1279 with 'h\:m\:ss\.FFF'.  
```

```vb
Console.WriteLine("Formatting:")
Dim ts1 As TimeSpan = TimeSpan.Parse("0:0:3.6974")
Console.WriteLine("{0} ('FFF') --> {0:FFF}", ts1)

Dim ts2 As TimeSpan = TimeSpan.Parse("0:0:3.8009")
Console.WriteLine("{0} ('ss\.FFF') --> {0:ss\.FFF}", ts2)
Console.WriteLine()

Console.WriteLine("Parsing:")
Dim inputs() As String = { "0:0:03.", "0:0:03.12", "0:0:03.1279" }
Dim fmt As String = "h\:m\:ss\.FFF"
Dim ts3 As TimeSpan

For Each input As String In inputs
   If TimeSpan.TryParseExact(input, fmt, Nothing, ts3)
      Console.WriteLine("{0} ('{1}') --> {2}", input, fmt, ts3)
   Else
      Console.WriteLine("Cannot parse {0} with '{1}'.", 
                        input, fmt)
   End If  
Next
' The example displays the following output:
'       Formatting:
'       00:00:03.6974000 ('FFF') --> 697
'       00:00:03.8009000 ('ss\.FFF') --> 03.8
'       
'       Parsing:
'       0:0:03. ('h\:m\:ss\.FFF') --> 00:00:03
'       0:0:03.12 ('h\:m\:ss\.FFF') --> 00:00:03.1200000
'       Cannot parse 0:0:03.1279 with 'h\:m\:ss\.FFF'.
```

## <a name="the-ffff-custom-format-specifier"></a>"FFFF" 사용자 지정 형식 지정자

"FFFF" 사용자 지정 형식 지정자(네 개의 "F" 문자 포함)는 시간 간격의 1/10000초를 출력합니다. 형식 지정 작업에서 나머지 소수 자릿수는 잘립니다. 뒤에 오는 소수 자릿수 0이 있는 경우 결과 문자열에 포함되지 않습니다. [TimeSpan.ParseExact](xref:System.TimeSpan.ParseExact(System.String,System.String,System.IFormatProvider)) 또는 [TimeSpan.TryParseExact](xref:System.TimeSpan.TryParseExact(System.String,System.String,System.IFormatProvider,System.Globalization.TimeSpanStyles,System.TimeSpan@)) 메서드를 호출하는 구문 분석 작업에서 1/10초, 1/100초, 1/1000초 및 1/10000초 숫자 표시는 선택 사항입니다.

다음 예제에서는 "FFFF" 사용자 지정 형식 지정자를 사용하여 [TimeSpan](xref:System.TimeSpan) 값에 1/10000초를 표시합니다. 또한 구문 분석 작업에서 "FFFF" 사용자 지정 형식 지정자를 사용합니다.

```csharp
Console.WriteLine("Formatting:");
TimeSpan ts1 = TimeSpan.Parse("0:0:3.69749");
Console.WriteLine("{0} ('FFFF') --> {0:FFFF}", ts1);

TimeSpan ts2 = TimeSpan.Parse("0:0:3.80009");
Console.WriteLine("{0} ('ss\\.FFFF') --> {0:ss\\.FFFF}", ts2);
Console.WriteLine();

Console.WriteLine("Parsing:");
string[] inputs = { "0:0:03.", "0:0:03.12", "0:0:03.12795" };
string fmt = @"h\:m\:ss\.FFFF";
TimeSpan ts3;

foreach (string input in inputs) {
   if (TimeSpan.TryParseExact(input, fmt, null, out ts3))
      Console.WriteLine("{0} ('{1}') --> {2}", input, fmt, ts3);
   else
      Console.WriteLine("Cannot parse {0} with '{1}'.", 
                        input, fmt);
}
// The example displays the following output:
//       Formatting:
//       00:00:03.6974900 ('FFFF') --> 6974
//       00:00:03.8000900 ('ss\.FFFF') --> 03.8
//       
//       Parsing:
//       0:0:03. ('h\:m\:ss\.FFFF') --> 00:00:03
//       0:0:03.12 ('h\:m\:ss\.FFFF') --> 00:00:03.1200000
//       Cannot parse 0:0:03.12795 with 'h\:m\:ss\.FFFF'.
```

```vb
Console.WriteLine("Formatting:")
Dim ts1 As TimeSpan = TimeSpan.Parse("0:0:3.69749")
Console.WriteLine("{0} ('FFFF') --> {0:FFFF}", ts1)

Dim ts2 As TimeSpan = TimeSpan.Parse("0:0:3.80009")
Console.WriteLine("{0} ('ss\.FFFF') --> {0:ss\.FFFF}", ts2)
Console.WriteLine()

Console.WriteLine("Parsing:")
Dim inputs() As String = { "0:0:03.", "0:0:03.12", "0:0:03.12795" }
Dim fmt As String = "h\:m\:ss\.FFFF"
Dim ts3 As TimeSpan

For Each input As String In inputs
   If TimeSpan.TryParseExact(input, fmt, Nothing, ts3)
      Console.WriteLine("{0} ('{1}') --> {2}", input, fmt, ts3)
   Else
      Console.WriteLine("Cannot parse {0} with '{1}'.", 
                        input, fmt)
   End If  
Next
' The example displays the following output:
'       Formatting:
'       00:00:03.6974900 ('FFFF') --> 6974
'       00:00:03.8000900 ('ss\.FFFF') --> 03.8
'       
'       Parsing:
'       0:0:03. ('h\:m\:ss\.FFFF') --> 00:00:03
'       0:0:03.12 ('h\:m\:ss\.FFFF') --> 00:00:03.1200000
'       Cannot parse 0:0:03.12795 with 'h\:m\:ss\.FFFF'.
```

## <a name="the-fffff-custom-format-specifier"></a>"FFFFF" 사용자 지정 형식 지정자

"FFFFF" 사용자 지정 형식 지정자(다섯 개의 "F" 문자 포함)는 시간 간격의 1/100000초를 출력합니다. 형식 지정 작업에서 나머지 소수 자릿수는 잘립니다. 뒤에 오는 소수 자릿수 0이 있는 경우 결과 문자열에 포함되지 않습니다. [TimeSpan.ParseExact](xref:System.TimeSpan.ParseExact(System.String,System.String,System.IFormatProvider)) 또는 [TimeSpan.TryParseExact](xref:System.TimeSpan.TryParseExact(System.String,System.String,System.IFormatProvider,System.Globalization.TimeSpanStyles,System.TimeSpan@)) 메서드를 호출하는 구문 분석 작업에서 1/10초, 1/100초, 1/1000초, 1/10000초 및 1/100000초 숫자 표시는 선택 사항입니다.

다음 예제에서는 "FFFFF" 사용자 지정 형식 지정자를 사용하여 [TimeSpan](xref:System.TimeSpan) 값에 1/100000초를 표시합니다. 또한 구문 분석 작업에서 "FFFFF" 사용자 지정 형식 지정자를 사용합니다.

```csharp
Console.WriteLine("Formatting:");
TimeSpan ts1 = TimeSpan.Parse("0:0:3.697497");
Console.WriteLine("{0} ('FFFFF') --> {0:FFFFF}", ts1);

TimeSpan ts2 = TimeSpan.Parse("0:0:3.800009");
Console.WriteLine("{0} ('ss\\.FFFFF') --> {0:ss\\.FFFFF}", ts2);
Console.WriteLine();

Console.WriteLine("Parsing:");
string[] inputs = { "0:0:03.", "0:0:03.12", "0:0:03.127956" };
string fmt = @"h\:m\:ss\.FFFFF";
TimeSpan ts3;

foreach (string input in inputs) {
   if (TimeSpan.TryParseExact(input, fmt, null, out ts3))
      Console.WriteLine("{0} ('{1}') --> {2}", input, fmt, ts3);
   else
      Console.WriteLine("Cannot parse {0} with '{1}'.", 
                        input, fmt);
}                       
// The example displays the following output:
//       Formatting:
//       00:00:03.6974970 ('FFFFF') --> 69749
//       00:00:03.8000090 ('ss\.FFFFF') --> 03.8
//       
//       Parsing:
//       0:0:03. ('h\:m\:ss\.FFFF') --> 00:00:03
//       0:0:03.12 ('h\:m\:ss\.FFFF') --> 00:00:03.1200000
//       Cannot parse 0:0:03.127956 with 'h\:m\:ss\.FFFF'. 
```

```vb
Console.WriteLine("Formatting:")
Dim ts1 As TimeSpan = TimeSpan.Parse("0:0:3.697497")
Console.WriteLine("{0} ('FFFFF') --> {0:FFFFF}", ts1)

Dim ts2 As TimeSpan = TimeSpan.Parse("0:0:3.800009")
Console.WriteLine("{0} ('ss\.FFFFF') --> {0:ss\.FFFFF}", ts2)
Console.WriteLine()

Console.WriteLine("Parsing:")
Dim inputs() As String = { "0:0:03.", "0:0:03.12", "0:0:03.127956" }
Dim fmt As String = "h\:m\:ss\.FFFFF"
Dim ts3 As TimeSpan

For Each input As String In inputs
   If TimeSpan.TryParseExact(input, fmt, Nothing, ts3)
      Console.WriteLine("{0} ('{1}') --> {2}", input, fmt, ts3)
   Else
      Console.WriteLine("Cannot parse {0} with '{1}'.", 
                        input, fmt)
   End If  
Next
' The example displays the following output:
'       Formatting:
'       00:00:03.6974970 ('FFFFF') --> 69749
'       00:00:03.8000090 ('ss\.FFFFF') --> 03.8
'       
'       Parsing:
'       0:0:03. ('h\:m\:ss\.FFFF') --> 00:00:03
'       0:0:03.12 ('h\:m\:ss\.FFFF') --> 00:00:03.1200000
'       Cannot parse 0:0:03.127956 with 'h\:m\:ss\.FFFF'.
```

## <a name="the-ffffff-custom-format-specifier"></a>"FFFFFF" 사용자 지정 형식 지정자

"FFFFFF" 사용자 지정 형식 지정자(여섯 개의 "F" 문자 포함)는 시간 간격의 1/1000000초를 출력합니다. 형식 지정 작업에서 나머지 소수 자릿수는 잘립니다. 뒤에 오는 소수 자릿수 0이 있는 경우 결과 문자열에 포함되지 않습니다. [TimeSpan.ParseExact](xref:System.TimeSpan.ParseExact(System.String,System.String,System.IFormatProvider)) 또는 [TimeSpan.TryParseExact](xref:System.TimeSpan.TryParseExact(System.String,System.String,System.IFormatProvider,System.Globalization.TimeSpanStyles,System.TimeSpan@)) 메서드를 호출하는 구문 분석 작업에서 1/10초, 1/100초, 1/1000초, 1/10000초, 1/100000초 및 1/1000000초 숫자 표시는 선택 사항입니다.

다음 예제에서는 "FFFFFF" 사용자 지정 형식 지정자를 사용하여 [TimeSpan](xref:System.TimeSpan) 값에 1/1000000초를 표시합니다. 또한 구문 분석 작업에서 이 사용자 지정 형식 지정자를 사용합니다.

```csharp
Console.WriteLine("Formatting:");
TimeSpan ts1 = TimeSpan.Parse("0:0:3.6974974");
Console.WriteLine("{0} ('FFFFFF') --> {0:FFFFFF}", ts1);

TimeSpan ts2 = TimeSpan.Parse("0:0:3.8000009");
Console.WriteLine("{0} ('ss\\.FFFFFF') --> {0:ss\\.FFFFFF}", ts2);
Console.WriteLine();

Console.WriteLine("Parsing:");
string[] inputs = { "0:0:03.", "0:0:03.12", "0:0:03.1279569" };
string fmt = @"h\:m\:ss\.FFFFFF";
TimeSpan ts3;

foreach (string input in inputs) {
   if (TimeSpan.TryParseExact(input, fmt, null, out ts3))
      Console.WriteLine("{0} ('{1}') --> {2}", input, fmt, ts3);
   else
      Console.WriteLine("Cannot parse {0} with '{1}'.", 
                        input, fmt);
}                       
// The example displays the following output:
//       Formatting:
//       00:00:03.6974974 ('FFFFFF') --> 697497
//       00:00:03.8000009 ('ss\.FFFFFF') --> 03.8
//       
//       Parsing:
//       0:0:03. ('h\:m\:ss\.FFFFFF') --> 00:00:03
//       0:0:03.12 ('h\:m\:ss\.FFFFFF') --> 00:00:03.1200000
//       Cannot parse 0:0:03.1279569 with 'h\:m\:ss\.FFFFFF'.
```

```vb
Console.WriteLine("Formatting:")
Dim ts1 As TimeSpan = TimeSpan.Parse("0:0:3.6974974")
Console.WriteLine("{0} ('FFFFFF') --> {0:FFFFFF}", ts1)

Dim ts2 As TimeSpan = TimeSpan.Parse("0:0:3.8000009")
Console.WriteLine("{0} ('ss\.FFFFFF') --> {0:ss\.FFFFFF}", ts2)
Console.WriteLine()

Console.WriteLine("Parsing:")
Dim inputs() As String = { "0:0:03.", "0:0:03.12", "0:0:03.1279569" }
Dim fmt As String = "h\:m\:ss\.FFFFFF"
Dim ts3 As TimeSpan

For Each input As String In inputs
   If TimeSpan.TryParseExact(input, fmt, Nothing, ts3)
      Console.WriteLine("{0} ('{1}') --> {2}", input, fmt, ts3)
   Else
      Console.WriteLine("Cannot parse {0} with '{1}'.", 
                        input, fmt)
   End If  
Next
' The example displays the following output:
'       Formatting:
'       00:00:03.6974974 ('FFFFFF') --> 697497
'       00:00:03.8000009 ('ss\.FFFFFF') --> 03.8
'       
'       Parsing:
'       0:0:03. ('h\:m\:ss\.FFFFFF') --> 00:00:03
'       0:0:03.12 ('h\:m\:ss\.FFFFFF') --> 00:00:03.1200000
'       Cannot parse 0:0:03.1279569 with 'h\:m\:ss\.FFFFFF'
```

## <a name="the-fffffff-custom-format-specifier"></a>"FFFFFFF" 사용자 지정 형식 지정자

"FFFFFFF" 사용자 지정 형식 지정자(일곱 개의 "F" 문자 포함)는 시간 간격의 1/10000000초(또는 소수 자릿수 틱)를 출력합니다. 뒤에 오는 소수 자릿수 0이 있는 경우 결과 문자열에 포함되지 않습니다. [TimeSpan.ParseExact](xref:System.TimeSpan.ParseExact(System.String,System.String,System.IFormatProvider)) 또는 [TimeSpan.TryParseExact](xref:System.TimeSpan.TryParseExact(System.String,System.String,System.IFormatProvider,System.Globalization.TimeSpanStyles,System.TimeSpan@)) 메서드를 호출하는 구문 분석 작업에서 입력 문자열의 소수 일곱 자리 표시는 선택 사항입니다.

다음 예제에서는 "FFFFFFF" 사용자 지정 형식 지정자를 사용하여 [TimeSpan](xref:System.TimeSpan) 값에 1초의 소수 자릿수 부분을 표시합니다. 또한 구문 분석 작업에서 이 사용자 지정 형식 지정자를 사용합니다.

```csharp
Console.WriteLine("Formatting:");
TimeSpan ts1 = TimeSpan.Parse("0:0:3.6974974");
Console.WriteLine("{0} ('FFFFFFF') --> {0:FFFFFFF}", ts1);

TimeSpan ts2 = TimeSpan.Parse("0:0:3.9500000");
Console.WriteLine("{0} ('ss\\.FFFFFFF') --> {0:ss\\.FFFFFFF}", ts2);
Console.WriteLine();

Console.WriteLine("Parsing:");
string[] inputs = { "0:0:03.", "0:0:03.12", "0:0:03.1279569" };
string fmt = @"h\:m\:ss\.FFFFFFF";
TimeSpan ts3;

foreach (string input in inputs) {
   if (TimeSpan.TryParseExact(input, fmt, null, out ts3))
      Console.WriteLine("{0} ('{1}') --> {2}", input, fmt, ts3);
   else
      Console.WriteLine("Cannot parse {0} with '{1}'.", 
                        input, fmt);
}
// The example displays the following output:
//    Formatting:
//    00:00:03.6974974 ('FFFFFFF') --> 6974974
//    00:00:03.9500000 ('ss\.FFFFFFF') --> 03.95
//    
//    Parsing:
//    0:0:03. ('h\:m\:ss\.FFFFFFF') --> 00:00:03
//    0:0:03.12 ('h\:m\:ss\.FFFFFFF') --> 00:00:03.1200000
//    0:0:03.1279569 ('h\:m\:ss\.FFFFFFF') --> 00:00:03.1279569 
```

```vb
Console.WriteLine("Formatting:")
Dim ts1 As TimeSpan = TimeSpan.Parse("0:0:3.6974974")
Console.WriteLine("{0} ('FFFFFFF') --> {0:FFFFFFF}", ts1)

Dim ts2 As TimeSpan = TimeSpan.Parse("0:0:3.9500000")
Console.WriteLine("{0} ('ss\.FFFFFFF') --> {0:ss\.FFFFFFF}", ts2)
Console.WriteLine()

Console.WriteLine("Parsing:")
Dim inputs() As String = { "0:0:03.", "0:0:03.12", "0:0:03.1279569" }
Dim fmt As String = "h\:m\:ss\.FFFFFFF"
Dim ts3 As TimeSpan

For Each input As String In inputs
   If TimeSpan.TryParseExact(input, fmt, Nothing, ts3)
      Console.WriteLine("{0} ('{1}') --> {2}", input, fmt, ts3)
   Else
      Console.WriteLine("Cannot parse {0} with '{1}'.", 
                        input, fmt)
   End If  
Next
' The example displays the following output:
'    Formatting:
'    00:00:03.6974974 ('FFFFFFF') --> 6974974
'    00:00:03.9500000 ('ss\.FFFFFFF') --> 03.95
'    
'    Parsing:
'    0:0:03. ('h\:m\:ss\.FFFFFFF') --> 00:00:03
'    0:0:03.12 ('h\:m\:ss\.FFFFFFF') --> 00:00:03.1200000
'    0:0:03.1279569 ('h\:m\:ss\.FFFFFFF') --> 00:00:03.1279569  
```

## <a name="other-characters"></a>기타 문자

공백 문자를 포함하여 형식 문자열에서 이스케이프되지 않은 다른 모든 문자는 사용자 지정 형식 지정자로 해석됩니다. 대부분의 경우 이스케이프되지 않은 다른 문자가 있으면 [FormatException](xref:System.FormatException)이 발생합니다. 

형식 문자열에 리터럴 문자를 포함하는 방법에는 다음 두 가지가 있습니다.

* 작은따옴표(리터럴 문자열 구분 기호)로 묶습니다. 

* 이스케이프 문자로 해석되는 백슬래시("\")를 앞에 추가합니다. 즉, C#에서는 서식 문자열이 @-quoted,여야 하거나 리터럴 문자 앞에 백슬래시를 추가해야 합니다.

  경우에 따라 조건부 논리를 사용하여 서식 문자열에 이스케이프된 리터럴을 포함해야 할 수 있습니다. 다음 예제에서는 조건부 논리를 사용하여 음수 시간 간격에 대해 부호 기호를 포함합니다. 
  
```csharp
using System;

public class Example
{
   public static void Main()
   {
      TimeSpan result = new DateTime(2010, 01, 01) - DateTime.Now; 
      String fmt = (result < TimeSpan.Zero ?  "\\-" : "") + "dd\\.hh\\:mm";

      Console.WriteLine(result.ToString(fmt));
      Console.WriteLine("Interval: {0:" + fmt + "}", result);
   }
}
// The example displays output like the following:
//       -1291.10:54
//       Interval: -1291.10:54
```

```vb
Module Example
   Public Sub Main()
      Dim result As TimeSpan = New DateTime(2010, 01, 01) - Date.Now 
      Dim fmt As String = If(result < TimeSpan.Zero, "\-", "") + "dd\.hh\:mm"

      Console.WriteLine(result.ToString(fmt))
      Console.WriteLine("Interval: {0:" + fmt + "}", result)
   End Sub
End Module
' The example displays output like the following:
'       -1291.10:54
'       Interval: -1291.10:54
```
  
.NET에서는 시간 간격의 구분 기호에 대한 문법을 정의하지 않습니다. 즉, 일과 시간, 시간과 분, 분과 초, 초와 1초의 소수 자릿수 간 구분 기호를 형식 문자열에서 모두 문자 리터럴로 처리해야 합니다.

다음 예제에서는 이스케이프 문자와 작은따옴표를 둘 다 사용하여 출력 문자열에서 "분" 단어가 포함된 사용자 지정 형식 문자열을 정의합니다. 

```csharp
TimeSpan interval = new TimeSpan(0, 32, 45);
// Escape literal characters in a format string.
string fmt = @"mm\:ss\ \m\i\n\u\t\e\s";
Console.WriteLine(interval.ToString(fmt));
// Delimit literal characters in a format string with the ' symbol.
fmt = "mm':'ss' minutes'";      
Console.WriteLine(interval.ToString(fmt));
// The example displays the following output: 
//       32:45 minutes      
//       32:45 minutes
```

```vb
Dim interval As New TimeSpan(0, 32, 45)
' Escape literal characters in a format string.
Dim fmt As String = "mm\:ss\ \m\i\n\u\t\e\s"
Console.WriteLine(interval.ToString(fmt))
' Delimit literal characters in a format string with the ' symbol.
fmt = "mm':'ss' minutes'"
Console.WriteLine(interval.ToString(fmt))
' The example displays the following output: 
'       32:45 minutes      
'       32:45 minutes
```

## <a name="see-also"></a>참고 항목

[형식 서식 지정](formatting-types.md)

[표준 TimeSpan 서식 문자열](standard-timespan.md)  




<!--HONumber=Nov16_HO3-->


