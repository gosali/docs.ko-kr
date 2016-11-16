---
title: "방법: 특정 날짜의 요일 추출"
description: "특정 날짜의 요일을 추출하는 방법"
keywords: .NET, .NET Core
author: stevehoag
ms.author: shoag
manager: wpickett
ms.date: 07/26/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 88a8f8b9-f5c9-4503-b968-84468b52bb8e
translationtype: Human Translation
ms.sourcegitcommit: b828bb1d6c8fb750ad9ef34f8a7a1b7d2574f4c6
ms.openlocfilehash: 298d2faf847b8518eb5852c55972409e4e3cf29c

---

# <a name="how-to-extract-the-day-of-the-week-from-a-specific-date"></a>방법: 특정 날짜의 요일 추출

.NET을 사용하면 쉽게 특정 날짜가 일주일 중 몇 번째 날인지 확인하고, 특정 날짜의 지역화된 요일 이름을 표시할 수 있습니다. 특정 날짜에 해당하는 요일을 나타내는 열거형 값은 [Datetime.DayOfWeek](xref:System.DateTime.DayOfWeek) 또는 [DateTimeOffset.DayOfWeek](xref:System.DateTimeOffset.DayOfWeek) 속성에서 제공합니다. 이와 대조적으로 요일 이름 검색은 날짜 및 시간 값의 `ToString` 메서드 또는 [String.Format](xref:System.String.Format(System.IFormatProvider,System.String,System.Object)) 메서드와 같은 형식 지정 메서드를 호출하여 수행할 수 있는 형식 지정 작업입니다. 이 항목에서는 이러한 서식 지정 작업을 수행하는 방법을 보여 줍니다.

## <a name="to-extract-a-number-indicating-the-day-of-the-week-from-a-specific-date"></a>특정 날짜에서 요일을 나타내는 숫자를 추출하려면

1. 날짜의 문자열 표현으로 작업하는 경우 정적 [DateTime.Parse](xref:System.DateTime.Parse(System.String)) 또는 [DateTimeOffset.Parse](xref:System.DateTimeOffset.Parse(System.String)) 메서드를 사용하여 [DateTime](xref:System.DateTime) 또는 [DateTimeOffset](xref:System.DateTimeOffset) 값으로 변환합니다.

2. [Datetime.DayOfWeek](xref:System.DateTime.DayOfWeek) 또는 [DateTimeOffset.DayOfWeek](xref:System.DateTimeOffset.DayOfWeek) 속성을 사용하여 요일을 나타내는 [DayOfWeek](xref:System.DayOfWeek) 값을 검색합니다.

3. 필요한 경우 [DayOfWeek](xref:System.DayOfWeek) 값을 정수로 캐스팅합니다. 

다음 예제에서는 특정 날짜의 요일을 나타내는 정수를 표시합니다. 

```csharp
using System;

public class Example
{
   public static void Main()
   {
      DateTime dateValue = new DateTime(2008, 6, 11);
      Console.WriteLine((int) dateValue.DayOfWeek);      
   }
}
// The example displays the following output:
//       3
```

```vb
Module Example
   Public Sub Main()
      Dim dateValue As Date = #6/11/2008#
      Console.WriteLine(dateValue.DayOfWeek)           
   End Sub
End Module
' The example displays the following output:
'    3
```

## <a name="to-extract-the-abbreviated-weekday-name-from-a-specific-date"></a>특정 날짜에서 간략화된 요일 이름을 추출하려면

1. 날짜의 문자열 표현으로 작업하는 경우 정적 [DateTime.Parse](xref:System.DateTime.Parse(System.String)) 또는 [DateTimeOffset.Parse](xref:System.DateTimeOffset.Parse(System.String)) 메서드를 사용하여 [DateTime](xref:System.DateTime) 또는 [DateTimeOffset](xref:System.DateTimeOffset) 값으로 변환합니다.

2. 현재 문화권 또는 특정 문화권의 간략화된 요일 이름을 추출할 수 있습니다.

    a. 현재 문화권의 간략화된 요일 이름을 추출하려면 날짜 및 시간 값의 [DateTime.ToString(String)](xref:System.DateTimeSystem.DateTime.ToString(System.String) 또는 [DateTimeOffset.ToString(String)](xref:System.DateTimeOffset.ToString(System.String)) 인스턴스 메서드를 호출하고 문자열 "ddd"를 *format* 매개 변수로 전달합니다. 다음 예제에서는 `ToString(String)` 메서드를 호출하는 방법을 보여 줍니다.
    
```csharp
using System;

public class Example
{
   public static void Main()
   {
  DateTime dateValue = new DateTime(2008, 6, 11);
  Console.WriteLine(dateValue.ToString("ddd"));   
   }
}
// The example displays the following output:
//       Wed
```

```vb
Module Example
   Public Sub Main()
  Dim dateValue As Date = #6/11/2008#
      Console.WriteLine(dateValue.ToString("ddd"))    
   End Sub
End Module
' The example displays the following output:
'       Wed
```

    b. To extract the abbreviated weekday name for a specific culture, call the date and time value’s [DateTime.ToString(String, IFormatProvider)](xref:System.DateTime.ToString(System.String,System.IFormatProvider)) or [DateTimeOffset.ToString(String, IFormatProvider)](xref:System.DateTimeOffset.ToString(System.String,System.IFormatProvider)) instance method. Pass the string "ddd" as the *format* parameter. Pass either a [CultureInfo](xref:System.Globalization.CultureInfo) or a [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) object that represents the culture whose weekday name you want to retrieve as the *provider* parameter. The following code illustrates a call to the [ToString(String, IFormatProvider)](xref:System.DateTime.ToString(System.String,System.IFormatProvider)) method using a [CultureInfo](xref:System.Globalization.CultureInfo) object that represents the fr-FR culture.
    
```csharp
using System;
using System.Globalization;

public class Example
{
    public static void Main()
    {
        DateTime dateValue = new DateTime(2008, 6, 11);
        Console.WriteLine(dateValue.ToString("ddd", 
                            new CultureInfo("fr-FR")));    
    }
}
// The example displays the following output:
//       mer. 
```

```vb
Imports System.Globalization

Module Example
   Public Sub Main()
      Dim dateValue As Date = #6/11/2008#
      Console.WriteLine(dateValue.ToString("ddd", 
                        New CultureInfo("fr-FR")))
   End Sub
End Module
' The example displays the following output:
'       mer.
```

## <a name="to-extract-the-full-weekday-name-from-a-specific-date"></a>특정 날짜에서 전체 요일 이름을 추출하려면

1. 날짜의 문자열 표현으로 작업하는 경우 정적 [DateTime.Parse](xref:System.DateTime.Parse(System.String)) 또는 [DateTimeOffset.Parse](xref:System.DateTimeOffset.Parse(System.String)) 메서드를 사용하여 [DateTime](xref:System.DateTime) 또는 [DateTimeOffset](xref:System.DateTimeOffset) 값으로 변환합니다.

2. 현재 문화권 또는 특정 문화권의 간략화된 요일 이름을 추출할 수 있습니다.

    a. 현재 문화권의 간략화된 요일 이름을 추출하려면 날짜 및 시간 값의 [DateTime.ToString(String)](xref:System.DateTimeSystem.DateTime.ToString(System.String) 또는 [DateTimeOffset.ToString(String)](xref:System.DateTimeOffset.ToString(System.String)) 인스턴스 메서드를 호출하고 문자열 "dddd"를 *format* 매개 변수로 전달합니다. 다음 예제에서는 `ToString(String)` 메서드를 호출하는 방법을 보여 줍니다.

```csharp
using System;

public class Example
{
    public static void Main()
    {
        DateTime dateValue = new DateTime(2008, 6, 11);
        Console.WriteLine(dateValue.ToString("dddd"));    
    }
}
// The example displays the following output:
//       Wednesday
```

```vb
Module Example
   Public Sub Main()
      Dim dateValue As Date = #6/11/2008#
      Console.WriteLine(dateValue.ToString("dddd"))
   End Sub
End Module
' The example displays the following output:
'       Wednesday
```

    b. To extract the weekday name for a specific culture, call the date and time value’s [DateTime.ToString(String, IFormatProvider)](xref:System.DateTime.ToString(System.String,System.IFormatProvider)) or [DateTimeOffset.ToString(String, IFormatProvider)](xref:System.DateTimeOffset.ToString(System.String,System.IFormatProvider)) instance method. Pass the string "dddd" as the *format* parameter. Pass either a [CultureInfo](xref:System.Globalization.CultureInfo) or a [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) object that represents the culture whose weekday name you want to retrieve as the *provider* parameter. The following code illustrates a call to the [ToString(String, IFormatProvider)](xref:System.DateTime.ToString(System.String,System.IFormatProvider)) method using a [CultureInfo](xref:System.Globalization.CultureInfo) object that represents the es-ES  culture.

```csharp
using System;
using System.Globalization;

public class Example
{
    public static void Main()
    {
        DateTime dateValue = new DateTime(2008, 6, 11);
        Console.WriteLine(dateValue.ToString("dddd", 
                            new CultureInfo("es-ES")));    
    }
}
// The example displays the following output:
//       miércoles.
```

```vb
Imports System.Globalization

Module Example
   Public Sub Main()
      Dim dateValue As Date = #6/11/2008#
      Console.WriteLine(dateValue.ToString("dddd", _
                        New CultureInfo("es-ES"))) 
   End Sub
End Module
' The example displays the following output:
'       miércoles.
```

## <a name="example"></a>예제

이 예제에서는 특정 날짜의 요일, 간략화된 요일 이름 및 전체 요일 이름을 나타내는 숫자를 검색하기 위한 [Datetime.DayOfWeek](xref:System.DateTime.DayOfWeek) 및 [DateTimeOffset.DayOfWeek](xref:System.DateTimeOffset.DayOfWeek) 속성과 [DateTime.ToString(String)](xref:System.DateTime.ToString(System.String) 또는 [DateTimeOffset.ToString(String)](xref:System.DateTimeOffset.ToString(System.String)) 메서드 호출을 보여 줍니다. 

```csharp
using System;
using System.Globalization;

public class Example
{
   public static void Main()
   {
      string dateString = "6/11/2007";
      DateTime dateValue;
      DateTimeOffset dateOffsetValue;

      try
      {
         DateTimeFormatInfo dateTimeFormats;
         // Convert date representation to a date value
         dateValue = DateTime.Parse(dateString, CultureInfo.InvariantCulture);
         dateOffsetValue = new DateTimeOffset(dateValue, 
                                      TimeZoneInfo.Local.GetUtcOffset(dateValue));         

         // Convert date representation to a number indicating the day of week
         Console.WriteLine((int) dateValue.DayOfWeek);
         Console.WriteLine((int) dateOffsetValue.DayOfWeek);

         // Display abbreviated weekday name using current culture
         Console.WriteLine(dateValue.ToString("ddd"));
         Console.WriteLine(dateOffsetValue.ToString("ddd"));

         // Display full weekday name using current culture
         Console.WriteLine(dateValue.ToString("dddd"));
         Console.WriteLine(dateOffsetValue.ToString("dddd"));

         // Display abbreviated weekday name for de-DE culture
         Console.WriteLine(dateValue.ToString("ddd", new CultureInfo("de-DE")));
         Console.WriteLine(dateOffsetValue.ToString("ddd", 
                                                     new CultureInfo("de-DE")));

         // Display abbreviated weekday name with de-DE DateTimeFormatInfo object
         dateTimeFormats = new CultureInfo("de-DE").DateTimeFormat;
         Console.WriteLine(dateValue.ToString("ddd", dateTimeFormats));
         Console.WriteLine(dateOffsetValue.ToString("ddd", dateTimeFormats));

         // Display full weekday name for fr-FR culture
         Console.WriteLine(dateValue.ToString("ddd", new CultureInfo("fr-FR")));
         Console.WriteLine(dateOffsetValue.ToString("ddd", 
                                                    new CultureInfo("fr-FR")));

         // Display abbreviated weekday name with fr-FR DateTimeFormatInfo object
         dateTimeFormats = new CultureInfo("fr-FR").DateTimeFormat;
         Console.WriteLine(dateValue.ToString("dddd", dateTimeFormats));
         Console.WriteLine(dateOffsetValue.ToString("dddd", dateTimeFormats));
      }
      catch (FormatException)
      {
         Console.WriteLine("Unable to convert {0} to a date.", dateString);
      }
   }
}
// The example displays the following output:
//       1
//       1
//       Mon
//       Mon
//       Monday
//       Monday
//       Mo
//       Mo
//       Mo
//       Mo
//       lun.
//       lun.
//       lundi
//       lundi
```

```vb
Imports System.Globalization

Module Example
   Public Sub Main()
      Dim dateString As String = "6/11/2007"
      Dim dateValue As Date
      Dim dateOffsetValue As DateTimeOffset

      Try
         Dim dateTimeFormats As DateTimeFormatInfo
         ' Convert date representation to a date value
         dateValue = Date.Parse(dateString, CultureInfo.InvariantCulture)
         dateOffsetValue = New DateTimeOffset(dateValue, _
                                     TimeZoneInfo.Local.GetUtcOffset(dateValue))            
         ' Convert date representation to a number indicating the day of week
         Console.WriteLine(dateValue.DayOfWeek)
         Console.WriteLine(dateOffsetValue.DayOfWeek)

         ' Display abbreviated weekday name using current culture
         Console.WriteLine(dateValue.ToString("ddd"))
         Console.WriteLine(dateOffsetValue.ToString("ddd"))

         ' Display full weekday name using current culture
         Console.WriteLine(dateValue.ToString("dddd"))
         Console.WriteLine(dateOffsetValue.ToString("dddd"))

         ' Display abbreviated weekday name for de-DE culture
         Console.WriteLine(dateValue.ToString("ddd", New CultureInfo("de-DE")))
         Console.WriteLine(dateOffsetValue.ToString("ddd", _
                                                    New CultureInfo("de-DE")))

         ' Display abbreviated weekday name with de-DE DateTimeFormatInfo object
         dateTimeFormats = New CultureInfo("de-DE").DateTimeFormat
         Console.WriteLine(dateValue.ToString("ddd", dateTimeFormats))
         Console.WriteLine(dateOffsetValue.ToString("ddd", dateTimeFormats))

         ' Display full weekday name for fr-FR culture
         Console.WriteLine(dateValue.ToString("ddd", New CultureInfo("fr-FR")))
         Console.WriteLine(dateOffsetValue.ToString("ddd", _
                                                    New CultureInfo("fr-FR")))

         ' Display abbreviated weekday name with fr-FR DateTimeFormatInfo object
         dateTimeFormats = New CultureInfo("fr-FR").DateTimeFormat
         Console.WriteLine(dateValue.ToString("dddd", dateTimeFormats))
         Console.WriteLine(dateOffsetValue.ToString("dddd", dateTimeFormats))
      Catch e As FormatException
         Console.WriteLine("Unable to convert {0} to a date.", dateString)
      End Try
   End Sub
End Module
' The example displays the following output to the console:
'       1
'       1
'       Mon
'       Mon
'       Monday
'       Monday
'       Mo
'       Mo
'       Mo
'       Mo
'       lun.
'       lun.
'       lundi
'       lundi
```

개별 언어는 .NET에서 제공하는 기능과 중복되거나 이러한 기능을 보완하는 기능을 제공할 수 있습니다. 예를 들어, Visual Basic에는 다음과 같은 두 가지 함수가 포함되어 있습니다.

* `Weekday`: 특정 날짜의 요일을 나타내는 숫자를 반환합니다. 이 함수는 요일의 첫날 서수 값을 1로 간주하는 반면, [Datetime.DayOfWeek](xref:System.DateTime.DayOfWeek) 속성은 0으로 간주합니다.

* `WeekdayName`: 현재 문화권에서 특정 요일 숫자에 해당하는 요일 이름을 반환합니다.

다음 예제에서는 Visual Basic `Weekday` 및 `WeekdayName` 함수의 사용 방법을 보여 줍니다.

```vb
Imports System.Globalization
Imports System.Threading

Module Example
   Public Sub Main()
      Dim dateValue As Date = #6/11/2008#

      ' Get weekday number using Visual Basic Weekday function
      Console.WriteLine(Weekday(dateValue))                 ' Displays 4
      ' Compare with .NET DateTime.DayOfWeek property
      Console.WriteLine(dateValue.DayOfWeek)                ' Displays 3

      ' Get weekday name using Weekday and WeekdayName functions
      Console.WriteLine(WeekdayName(Weekday(dateValue)))    ' Displays Wednesday

      ' Change culture to de-DE
      Dim originalCulture As CultureInfo = Thread.CurrentThread.CurrentCulture
      Thread.CurrentThread.CurrentCulture = New CultureInfo("de-DE")
      ' Get weekday name using Weekday and WeekdayName functions
      Console.WriteLine(WeekdayName(Weekday(dateValue)))   ' Displays Donnerstag

      ' Restore original culture
      Thread.CurrentThread.CurrentCulture = originalCulture   
   End Sub
End Module
``` 

또한 [Datetime.DayOfWeek](xref:System.DateTime.DayOfWeek) 속성에서 반환하는 값을 사용하여 특정 날짜의 요일 이름을 검색할 수 있습니다. 이렇게 하려면 속성에서 반환하는 [DayOfWeek](xref:System.DayOfWeek) 값에 대해 [Enum.ToString](xref:System.Enum.ToString(System.String)) 메서드를 호출하기만 하면 됩니다. 그러나 이 방법은 다음 예제에 나와 있는 것처럼 현재 문화권의 지역화된 요일 이름을 생성하지 않습니다. 

```csharp
using System;
using System.Globalization;
using System.Threading;

public class Example
{
   public static void Main()
   {
      // Change current culture to fr-FR
      CultureInfo originalCulture = Thread.CurrentThread.CurrentCulture;
      Thread.CurrentThread.CurrentCulture = new CultureInfo("fr-FR");

      DateTime dateValue = new DateTime(2008, 6, 11);
      // Display the DayOfWeek string representation
      Console.WriteLine(dateValue.DayOfWeek.ToString());   
      // Restore original current culture
      Thread.CurrentThread.CurrentCulture = originalCulture;
   }
}
// The example displays the following output:
//       Wednesday
```

```vb
Imports System.Globalization

Module Example
   Public Sub Main()
      Dim dateString As String = "6/11/2007"
      Dim dateValue As Date
      Dim dateOffsetValue As DateTimeOffset

      Try
         Dim dateTimeFormats As DateTimeFormatInfo
         ' Convert date representation to a date value
         dateValue = Date.Parse(dateString, CultureInfo.InvariantCulture)
         dateOffsetValue = New DateTimeOffset(dateValue, _
                                     TimeZoneInfo.Local.GetUtcOffset(dateValue))            
         ' Convert date representation to a number indicating the day of week
         Console.WriteLine(dateValue.DayOfWeek)
         Console.WriteLine(dateOffsetValue.DayOfWeek)

         ' Display abbreviated weekday name using current culture
         Console.WriteLine(dateValue.ToString("ddd"))
         Console.WriteLine(dateOffsetValue.ToString("ddd"))

         ' Display full weekday name using current culture
         Console.WriteLine(dateValue.ToString("dddd"))
         Console.WriteLine(dateOffsetValue.ToString("dddd"))

         ' Display abbreviated weekday name for de-DE culture
         Console.WriteLine(dateValue.ToString("ddd", New CultureInfo("de-DE")))
         Console.WriteLine(dateOffsetValue.ToString("ddd", _
                                                    New CultureInfo("de-DE")))

         ' Display abbreviated weekday name with de-DE DateTimeFormatInfo object
         dateTimeFormats = New CultureInfo("de-DE").DateTimeFormat
         Console.WriteLine(dateValue.ToString("ddd", dateTimeFormats))
         Console.WriteLine(dateOffsetValue.ToString("ddd", dateTimeFormats))

         ' Display full weekday name for fr-FR culture
         Console.WriteLine(dateValue.ToString("ddd", New CultureInfo("fr-FR")))
         Console.WriteLine(dateOffsetValue.ToString("ddd", _
                                                    New CultureInfo("fr-FR")))

         ' Display abbreviated weekday name with fr-FR DateTimeFormatInfo object
         dateTimeFormats = New CultureInfo("fr-FR").DateTimeFormat
         Console.WriteLine(dateValue.ToString("dddd", dateTimeFormats))
         Console.WriteLine(dateOffsetValue.ToString("dddd", dateTimeFormats))
      Catch e As FormatException
         Console.WriteLine("Unable to convert {0} to a date.", dateString)
      End Try
   End Sub
End Module
' The example displays the following output to the console:
'       1
'       1
'       Mon
'       Mon
'       Monday
'       Monday
'       Mo
'       Mo
'       Mo
'       Mo
'       lun.
'       lun.
'       lundi
'       lundi
```

## <a name="see-also"></a>참고 항목

[서식 지정 작업 수행](performing-formatting-operations.md)

[표준 날짜 및 시간 서식 문자열](standard-datetime.md)

[사용자 지정 날짜 및 시간 서식 문자열](custom-datetime.md)
    



<!--HONumber=Nov16_HO3-->


