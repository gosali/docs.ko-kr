---
title: "방법: 양력이 아닌 달력의 날짜 표시"
description: "양력이 아닌 달력의 날짜를 표시하는 방법"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 07/26/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 93f06e1d-544b-4ccc-a0b2-95cd674852cb
translationtype: Human Translation
ms.sourcegitcommit: fb00da6505c9edb6a49d2003ae9bcb8e74c11d6c
ms.openlocfilehash: 85c9d450be48c553ea3a1f1a0f16c298941fa325

---

# <a name="how-to-display-dates-in-nongregorian-calendars"></a>방법: 양력이 아닌 달력의 날짜 표시

[DateTime](xref:System.DateTime) 및 [DateTimeOffset](xref:System.DateTimeOffset) 형식은 양력을 기본 달력으로 사용합니다. 즉, 날짜 및 시간 값의 `ToString` 메서드를 호출하면 해당 날짜 및 시간이 다른 달력을 사용하여 생성된 경우에도 해당 날짜 및 시간의 문자열 표현을 양력 달력으로 표시합니다. 이 내용은 두 가지 방법을 사용하여 페르시아력으로 날짜 및 시간 값을 만들지만 [ToString](xref:System.DateTime.ToString) 메서드를 호출할 때 해당 날짜 및 시간 값을 여전히 양력으로 표시하는 다음 예제에 설명되어 있습니다. 이 예제에서는 특정 달력의 날짜를 표시하기 위해 자주 사용되지만 잘못된 두 가지 방법을 보여 줍니다.

```csharp
PersianCalendar persianCal = new PersianCalendar();

DateTime persianDate = persianCal.ToDateTime(1387, 3, 18, 12, 0, 0, 0);
Console.WriteLine(persianDate.ToString());

persianDate = new DateTime(1387, 3, 18, persianCal);
Console.WriteLine(persianDate.ToString());
// The example displays the following output to the console:
//       6/7/2008 12:00:00 PM
//       6/7/2008 12:00:00 AM
```

```vb
Dim persianCal As New PersianCalendar()

Dim persianDate As Date = persianCal.ToDateTime(1387, 3, 18, _
                                                12, 0, 0, 0)
Console.WriteLine(persianDate.ToString())

persianDate = New DateTime(1387, 3, 18, persianCal)
Console.WriteLine(persianDate.ToString())
' The example displays the following output to the console:
'       6/7/2008 12:00:00 PM
'       6/7/2008 12:00:00 AM
```

두 가지 방법을 사용하여 특정 달력의 날짜를 표시할 수 있습니다. 첫 번째 방법에서는 달력이 특정 문화권의 기본 달력이어야 합니다. 두 번째 방법은 모든 달력에 사용할 수 있습니다.

## <a name="to-display-the-date-for-a-cultures-default-calendar"></a>문화권의 기본 달력으로 날짜를 표시하려면

1. 사용할 달력을 나타내는 [Calendar](xref:System.Globalization.Calendar) 클래스에서 파생된 달력 개체를 인스턴스화합니다.

2. 날짜를 표시하는 데 사용할 형식 지정의 문화권을 나타내는 [CultureInfo](xref:System.Globalization.CultureInfo) 개체를 인스턴스화합니다.

3. [Array.Exists&lt;T&gt;](xref:System.Array.Exists``1(``0[],System.Predicate{``0})) 메서드를 호출하여 달력 개체가 [CultureInfo.OptionalCalendars](xref:System.Globalization.CultureInfo.OptionalCalendars) 속성에서 반환된 배열의 멤버인지 여부를 확인합니다. 이는 달력이 [CultureInfo](xref:System.Globalization.CultureInfo) 개체의 기본 달력으로 사용될 수 있음을 나타냅니다. 배열의 멤버가 아닌 경우 "임의 달력의 날짜를 표시하려면" 섹션의 지침을 따르세요.

4. [CultureInfo.DateTimeFormat](xref:System.Globalization.CultureInfo.DateTimeFormat) 속성에서 반환된 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체의 [Calendar](xref:System.Globalization.DateTimeFormatInfo.Calendar) 속성에 달력 개체를 할당합니다.

  > [!NOTE]
  > [CultureInfo](xref:System.Globalization.CultureInfo) 클래스에도 [Calendar](xref:System.Globalization.CultureInfo.Calendar) 속성이 있습니다. 그러나 이 속성은 읽기 전용이고 상수이므로 [DateTimeFormatInfo.Calendar](xref:System.Globalization.DateTimeFormatInfo.Calendar) 속성에 할당된 새 기본 달력에 따라 변경되지 않습니다.
  
5. [DateTime.ToString(IFormatProvider)](xref:System.DateTime.ToString(System.IFormatProvider)) 또는 [DateTime.ToString(String,IFormatProvider)](xref:System.DateTime.ToString(System.String,System.IFormatProvider)) 메서드 중 하나를 호출하고 이전 단계에서 기본 달력이 수정된 [CultureInfo](xref:System.Globalization.CultureInfo) 개체를 전달합니다.

## <a name="to-display-the-date-in-any-calendar"></a>임의 달력의 날짜를 표시하려면

1. 사용할 달력을 나타내는 [Calendar](xref:System.Globalization.Calendar) 클래스에서 파생된 달력 개체를 인스턴스화합니다.

2. 날짜 및 시간 값의 문자열 표현에 표시할 날짜 및 시간 요소를 결정합니다.

3. 표시할 각 날짜 및 시간 요소에 대해 달력 개체의 `Get…` 메서드를 호출합니다. 다음 메서드를 사용할 수 있습니다.

    * [GetYear](xref:System.Globalization.Calendar.GetYear(System.DateTime)) - 해당 달력의 연도를 표시합니다.
    
    * [GetMonth](xref:System.Globalization.Calendar.GetMonth(System.DateTime)) - 해당 달력의 월을 표시합니다. 
    
    * [GetDayOfMonth](xref:System.Globalization.Calendar.GetDayOfMonth(System.DateTime)) - 해당 달력의 일을 표시합니다.
    
    * [GetHour](xref:System.Globalization.Calendar.GetHour(System.DateTime)) - 해당 달력의 시간을 표시합니다.
    
    * [GetMinute](xref:System.Globalization.Calendar.GetMinute(System.DateTime)) - 해당 달력의 분을 표시합니다.
    
    * [GetSecond](xref:System.Globalization.Calendar.GetSecond(System.DateTime)) - 해당 달력의 초를 표시합니다. 
    
    * [GetMilliseconds](xref:System.Globalization.Calendar.GetMilliseconds(System.DateTime)) - 해당 달력의 밀리초를 표시합니다.
    
## <a name="example"></a>예제
    
예제에서는 두 가지 달력을 사용하여 날짜를 표시합니다. 회교식 달력을 ar-JO 문화권의 기본 달력으로 정의한 후 날짜를 표시하고, fa-IR 문화권에서 선택적 달력으로 지원되지 않는 페르시아력을 사용하여 날짜를 표시합니다.

```csharp
using System;
using System.Globalization;

public class CalendarDates
{
   public static void Main()
   {
      HijriCalendar hijriCal = new HijriCalendar();
      CalendarUtility hijriUtil = new CalendarUtility(hijriCal);
      DateTime dateValue1 = new DateTime(1429, 6, 29, hijriCal);
      DateTimeOffset dateValue2 = new DateTimeOffset(dateValue1, 
                                  TimeZoneInfo.Local.GetUtcOffset(dateValue1));
      CultureInfo jc = CultureInfo.CreateSpecificCulture("ar-JO");

      // Display the date using the Gregorian calendar.
      Console.WriteLine("Using the system default culture: {0}", 
                        dateValue1.ToString("d"));
      // Display the date using the ar-JO culture's original default calendar.
      Console.WriteLine("Using the ar-JO culture's original default calendar: {0}", 
                        dateValue1.ToString("d", jc));
      // Display the date using the Hijri calendar.
      Console.WriteLine("Using the ar-JO culture with Hijri as the default calendar:");
      // Display a Date value.
      Console.WriteLine(hijriUtil.DisplayDate(dateValue1, jc));
      // Display a DateTimeOffset value.
      Console.WriteLine(hijriUtil.DisplayDate(dateValue2, jc));

      Console.WriteLine();

      PersianCalendar persianCal = new PersianCalendar();
      CalendarUtility persianUtil = new CalendarUtility(persianCal);
      CultureInfo ic = CultureInfo.CreateSpecificCulture("fa-IR");

      // Display the date using the ir-FA culture's default calendar.
      Console.WriteLine("Using the ir-FA culture's default calendar: {0}",       
                        dateValue1.ToString("d", ic));
      // Display a Date value.
      Console.WriteLine(persianUtil.DisplayDate(dateValue1, ic));
      // Display a DateTimeOffset value.
      Console.WriteLine(persianUtil.DisplayDate(dateValue2, ic));
   }
}

public class CalendarUtility
{
   private Calendar thisCalendar;
   private CultureInfo targetCulture;

   public CalendarUtility(Calendar cal)
   {
      this.thisCalendar = cal;
   }

   private bool CalendarExists(CultureInfo culture)
   {
      this.targetCulture = culture;
      return Array.Exists(this.targetCulture.OptionalCalendars, 
                          this.HasSameName);
   }

   private bool HasSameName(Calendar cal)
   {
      if (cal.ToString() == thisCalendar.ToString())
         return true;
      else
         return false;
   }

   public string DisplayDate(DateTime dateToDisplay, CultureInfo culture)
   {
      DateTimeOffset displayOffsetDate = dateToDisplay;
      return DisplayDate(displayOffsetDate, culture);
   }

   public string DisplayDate(DateTimeOffset dateToDisplay, 
                             CultureInfo culture)
   {
      string specifier = "yyyy/MM/dd";

      if (this.CalendarExists(culture))
      {
         Console.WriteLine("Displaying date in supported {0} calendar...", 
                           this.thisCalendar.GetType().Name);
         culture.DateTimeFormat.Calendar = this.thisCalendar;
         return dateToDisplay.ToString(specifier, culture);
      }
      else
      {
         Console.WriteLine("Displaying date in unsupported {0} calendar...", 
                           thisCalendar.GetType().Name);

         string separator = targetCulture.DateTimeFormat.DateSeparator;

         return thisCalendar.GetYear(dateToDisplay.DateTime).ToString("0000") +
                separator +
                thisCalendar.GetMonth(dateToDisplay.DateTime).ToString("00") + 
                separator +
                thisCalendar.GetDayOfMonth(dateToDisplay.DateTime).ToString("00"); 
      }
   } 
}
// The example displays the following output to the console:
//       Using the system default culture: 7/3/2008
//       Using the ar-JO culture's original default calendar: 03/07/2008
//       Using the ar-JO culture with Hijri as the default calendar:
//       Displaying date in supported HijriCalendar calendar...
//       1429/06/29
//       Displaying date in supported HijriCalendar calendar...
//       1429/06/29
//       
//       Using the ir-FA culture's default calendar: 7/3/2008
//       Displaying date in unsupported PersianCalendar calendar...
//       1387/04/13
//       Displaying date in unsupported PersianCalendar calendar...
//       1387/04/13
```

```vb
Imports System.Globalization

Public Class CalendarDates
   Public Shared Sub Main()
      Dim hijriCal As New HijriCalendar()
      Dim hijriUtil As New CalendarUtility(hijriCal)
      Dim dateValue1 As Date = New Date(1429, 6, 29, hijriCal)
      Dim dateValue2 As DateTimeOffset = New DateTimeOffset(dateValue1, _
                                         TimeZoneInfo.Local.GetUtcOffset(dateValue1))
      Dim jc As CultureInfo = CultureInfo.CreateSpecificCulture("ar-JO")

      ' Display the date using the Gregorian calendar.
      Console.WriteLine("Using the system default culture: {0}", _
                        dateValue1.ToString("d"))
      ' Display the date using the ar-JO culture's original default calendar.
      Console.WriteLine("Using the ar-JO culture's original default calendar: {0}", _
                        dateValue1.ToString("d", jc))
      ' Display the date using the Hijri calendar.
      Console.WriteLine("Using the ar-JO culture with Hijri as the default calendar:")
      ' Display a Date value.
      Console.WriteLine(hijriUtil.DisplayDate(dateValue1, jc))
      ' Display a DateTimeOffset value.
      Console.WriteLine(hijriUtil.DisplayDate(dateValue2, jc))

      Console.WriteLine()

      Dim persianCal As New PersianCalendar()
      Dim persianUtil As New CalendarUtility(persianCal)
      Dim ic As CultureInfo = CultureInfo.CreateSpecificCulture("fa-IR")

      ' Display the date using the ir-FA culture's default calendar.
      Console.WriteLine("Using the ir-FA culture's default calendar: {0}", _      
                        dateValue1.ToString("d", ic))
      ' Display a Date value.
      Console.WriteLine(persianUtil.DisplayDate(dateValue1, ic))
      ' Display a DateTimeOffset value.
      Console.WriteLine(persianUtil.DisplayDate(dateValue2, ic))
   End Sub
End Class

Public Class CalendarUtility
   Private thisCalendar As Calendar
   Private targetCulture As CultureInfo

   Public Sub New(cal As Calendar)
      Me.thisCalendar = cal
   End Sub

   Private Function CalendarExists(culture As CultureInfo) As Boolean
      Me.targetCulture = culture
      Return Array.Exists(Me.targetCulture.OptionalCalendars, _
                          AddressOf Me.HasSameName)
   End Function 

   Private Function HasSameName(cal As Calendar) As Boolean
      If cal.ToString() = thisCalendar.ToString() Then
         Return True
      Else
         Return False
      End If
   End Function

   Public Function DisplayDate(dateToDisplay As Date, _
                               culture As CultureInfo) As String
      Dim displayOffsetDate As DateTimeOffset = dateToDisplay
      Return DisplayDate(displayOffsetDate, culture)
   End Function

   Public Function DisplayDate(dateToDisplay As DateTimeOffset, _
                               culture As CultureInfo) As String
      Dim specifier As String = "yyyy/MM/dd"

      If Me.CalendarExists(culture) Then
         Console.WriteLine("Displaying date in supported {0} calendar...", _
                           thisCalendar.GetType().Name)
         culture.DateTimeFormat.Calendar = Me.thisCalendar
         Return dateToDisplay.ToString(specifier, culture)
      Else
         Console.WriteLine("Displaying date in unsupported {0} calendar...", _
                           thisCalendar.GetType().Name)

         Dim separator As String = targetCulture.DateTimeFormat.DateSeparator

         Return thisCalendar.GetYear(dateToDisplay.DateTime).ToString("0000") & separator & _
                thisCalendar.GetMonth(dateToDisplay.DateTime).ToString("00") & separator & _
                thisCalendar.GetDayOfMonth(dateToDisplay.DateTime).ToString("00") 
      End If             
   End Function
End Class
' The example displays the following output to the console:
'       Using the system default culture: 7/3/2008
'       Using the ar-JO culture's original default calendar: 03/07/2008
'       Using the ar-JO culture with Hijri as the default calendar:
'       Displaying date in supported HijriCalendar calendar...
'       1429/06/29
'       Displaying date in supported HijriCalendar calendar...
'       1429/06/29
'       
'       Using the ir-FA culture's default calendar: 7/3/2008
'       Displaying date in unsupported PersianCalendar calendar...
'       1387/04/13
'       Displaying date in unsupported PersianCalendar calendar...
'       1387/04/13
```

각 [CultureInfo](xref:System.Globalization.CultureInfo) 개체는 [OptionalCalendars](xref:System.Globalization.CultureInfo.OptionalCalendars) 속성에 표시된 하나 이상의 달력을 지원할 수 있습니다. 이러한 달력 중 하나가 문화권의 기본 달력으로 지정되며 읽기 전용 [CultureInfo.Calendar](xref:System.Globalization.CultureInfo.Calendar) 속성에서 반환됩니다. [CultureInfo.DateTimeFormat](xref:System.Globalization.CultureInfo.DateTimeFormat) 속성에서 반환된 [DateTimeFormatInfo.Calendar](xref:System.Globalization.DateTimeFormatInfo.Calendar) 속성에 해당 달력을 나타내는 [Calendar](xref:System.Globalization.Calendar) 개체를 할당하여 다른 선택적 달력을 기본값으로 지정할 수 있습니다. 그러나 [PersianCalendar](xref:System.Globalization.PersianCalendar) 클래스가 나타내는 페르시아력과 같은 일부 달력은 특정 문화권의 선택적 달력으로 사용되지 않습니다.   

예제에서는 다시 사용할 수 있는 달력 유틸리티 클래스인 `CalendarUtility`를 정의하여 날짜의 문자열 표현을 특정 달력으로 생성하는 작업의 많은 세부 정보를 처리합니다. `CalendarUtility` 클래스의 멤버는 다음과 같습니다. 

* 하나의 매개 변수가 날짜를 나타내는 [Calendar](xref:System.Globalization.Calendar) 개체인 매개 변수가 있는 생성자. 클래스의 전용 필드에 할당됩니다.

* `CalendarExists`는 `CalendarUtility` 개체가 나타내는 달력이 메서드에 매개 변수로 전달되는 [CultureInfo](xref:System.Globalization.CultureInfo) 개체에서 지원되는지 여부를 나타내는 부울 값을 반환하는 전용 메서드입니다. 이 메서드는 [CultureInfo.OptionalCalendars](xref:System.Globalization.CultureInfo.OptionalCalendars) 배열이 전달되는 [Array.Exists&lt;T&gt;](xref:System.Array.Exists``1(``0[],System.Predicate{``0})) 메서드 호출을 래핑합니다.

* `HasSameName`은 [Array.Exists&lt;T&gt;](xref:System.Array.Exists``1(``0[],System.Predicate{``0})) 메서드에 매개 변수로 전달되는[Predicate&lt;T&gt;](xref:System.Predicate%601) 대리자에 할당할 전용 메서드입니다. 메서드에서 `true`가 반환될 때까지 배열의 각 멤버가 메서드에 전달됩니다. 이 메서드는 선택적 달력의 이름이 `CalendarUtility` 개체가 나타내는 달력과 같은지 여부를 확인합니다.

* `DisplayDate`는 두 매개 변수, 즉 `CalendarUtility` 개체가 나타내는 달력에 표시할 [DateTime](xref:System.DateTime) 또는 [DateTimeOffset](xref:System.DateTimeOffset) 값과 사용할 서식 지정 규칙의 문화권이 전달되는 오버로드된 공용 메서드입니다. 날짜의 문자열 표현을 반환할 때의 해당 동작은 대상 달력이 사용할 형식 지정 규칙의 문화권에서 지원되는지 여부에 따라 달라집니다.

이 예제에서 [DateTime](xref:System.DateTime) 또는 [DateTimeOffset](xref:System.DateTimeOffset) 값을 만드는 데 사용된 달력에 관계없이 해당 값은 일반적으로 양력 날짜로 표시됩니다. 이는 [DateTime](xref:System.DateTime) 및 [DateTimeOffset](xref:System.DateTimeOffset) 형식이 달력 정보를 유지하지 않기 때문입니다. 내부적으로 두 값은 0001년 1월 1일 자정 이후에 경과된 틱 수로 표시됩니다. 해당 숫자의 해석은 달력에 따라 달라집니다. 대부분의 문화권에서 기본 달력은 양력입니다. 

## <a name="see-also"></a>참고 항목

[서식 지정 작업 수행](performing-formatting-operations.md)



<!--HONumber=Nov16_HO3-->


