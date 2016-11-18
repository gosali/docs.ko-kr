---
title: "표준 날짜 및 시간 서식 문자열"
description: "표준 날짜 및 시간 서식 문자열"
keywords: .NET, .NET Core
author: stevehoag
ms.author: shoag
manager: wpickett
ms.date: 07/25/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: be239871-10cc-4949-b548-200bb260630a
translationtype: Human Translation
ms.sourcegitcommit: b20713600d7c3ddc31be5885733a1e8910ede8c6
ms.openlocfilehash: 56da9671a0849b0f0a94d8881cdc28e70bcf8549

---

# <a name="standard-date-and-time-format-strings"></a>표준 날짜 및 시간 서식 문자열

표준 날짜 및 시간 서식 문자열은 단일 서식 지정자를 사용하여 날짜 및 시간 값의 텍스트 표현을 정의합니다. 공백을 포함하여 문자가 두 개 이상 포함된 날짜 및 시간 서식 문자열은 사용자 지정 날짜 및 시간 서식 문자열로 해석됩니다. 자세한 내용은 [사용자 지정 날짜 및 시간 서식 문자열](custom-datetime.md)을 참조하세요. 표준 또는 사용자 지정 서식 문자열은 다음 두 가지 방법으로 사용할 수 있습니다.

* 서식 지정 작업의 결과로 생성되는 문자열을 정의합니다.

* 구문 분석 작업을 통해 [DateTime](xref:System.DateTime) 또는 [DateTimeOffset](xref:System.DateTimeOffset) 값으로 변환할 수 있는 날짜 및 시간 값의 텍스트 표현을 정의합니다.

표준 날짜 및 시간 서식 문자열은 [DateTime](xref:System.DateTime)과 [DateTimeOffset](xref:System.DateTimeOffset) 값 모두에 사용할 수 있습니다. 

다음 표에서는 표준 날짜 및 시간 서식 지정자에 대해 설명합니다. 다른 설명이 없는 한 특정 표준 날짜 및 시간 서식 지정자는 [DateTime](xref:System.DateTime) 또는 [DateTimeOffset](xref:System.DateTimeOffset) 값에 사용할 때 동일한 문자열을 생성합니다. 표준 날짜 및 시간 서식 문자열을 사용하는 방법에 대한 자세한 내용은 [참고](#notes) 섹션을 참조하세요.

형식 지정자 | 설명 | 예제
---------------- | ----------- | --------
"d" | 간단한 날짜 패턴입니다. | `2009-06-15T13:45:30 -> 6/15/2009 (en-US)`; `2009-06-15T13:45:30 -> 15/06/2009 (fr-FR)`; `2009-06-15T13:45:30 -> 2009/06/15 (ja-JP)`
"D" | 자세한 날짜 패턴입니다. | `2009-06-15T13:45:30 -> Monday, June 15, 2009 (en-US)`; `2009-06-15T13:45:30 -> 15 июня 2009 г. (ru-RU)`; `2009-06-15T13:45:30 -> Montag, 15. Juni 2009 (de-DE)`
"f" | 전체 날짜/시간 패턴(간단한 시간)입니다. | `2009-06-15T13:45:30 -> Monday, June 15, 2009 1:45 PM (en-US)`; `2009-06-15T13:45:30 -> den 15 juni 2009 13:45 (sv-SE)`; `2009-06-15T13:45:30 -> Δευτέρα, 15 Ιουνίου 2009 1:45 μμ (el-GR)`
"F" | 전체 날짜/시간 패턴(자세한 시간) | `2009-06-15T13:45:30 -> Monday, June 15, 2009 1:45:30 PM (en-US)`; `2009-06-15T13:45:30 -> den 15 juni 2009 13:45:30 (sv-SE)`; `2009-06-15T13:45:30 -> Δευτέρα, 15 Ιουνίου 2009 1:45:30 μμ (el-GR)`
"g" | 일반 날짜/시간 패턴(간단한 시간) | `2009-06-15T13:45:30 -> 6/15/2009 1:45 PM (en-US)`; `2009-06-15T13:45:30 -> 15/06/2009 13:45 (es-ES)`; `2009-06-15T13:45:30 -> 2009/6/15 13:45 (zh-CN)`
"G" | 일반 날짜/시간 패턴(자세한 시간)입니다. | `2009-06-15T13:45:30 -> 6/15/2009 1:45:30 PM (en-US)`; `2009-06-15T13:45:30 -> 15/06/2009 13:45:30 (es-ES)`; `2009-06-15T13:45:30 -> 2009/6/15 13:45:30 (zh-CN)`
"M", "m' | 월/일 패턴입니다. | `2009-06-15T13:45:30 -> June 15 (en-US)`; `2009-06-15T13:45:30 -> 15. juni (da-DK)`; `2009-06-15T13:45:30 -> 15 Juni (id-ID)`
"O", "o" | 라운드트립 날짜/시간 패턴입니다. | [DateTime](xref:System.DateTime) 값: `2009-06-15T13:45:30 (DateTimeKind.Local) --> 2009-06-15T13:45:30.0000000-07:00`; `2009-06-15T13:45:30 (DateTimeKind.Utc) --> 2009-06-15T13:45:30.0000000Z`; `2009-06-15T13:45:30 (DateTimeKind.Unspecified) --> 2009-06-15T13:45:30.0000000`입. [DateTimeOffset](xref:System.DateTimeOffset) 값: `2009-06-15T13:45:30-07:00 --> 2009-06-15T13:45:30.0000000-07:00`
"R", "r" | RFC1123 패턴입니다. | `2009-06-15T13:45:30 -> Mon, 15 Jun 2009 20:45:30 GMT`
"s" | 정렬 가능한 날짜/시간 패턴입니다. | `2009-06-15T13:45:30 (DateTimeKind.Local) -> 2009-06-15T13:45:30`; `2009-06-15T13:45:30 (DateTimeKind.Utc) -> 2009-06-15T13:45:30`
"t" | 간단한 시간 패턴입니다. | `2009-06-15T13:45:30 -> 1:45 PM (en-US)`; `2009-06-15T13:45:30 -> 13:45 (hr-HR)`; `2009-06-15T13:45:30 -> 01:45 م (ar-EG)` 
"T" | 자세한 시간 패턴 | `2009-06-15T13:45:30 -> 1:45:30 PM (en-US)`; `2009-06-15T13:45:30 -> 13:45:30 (hr-HR)`; `2009-06-15T13:45:30 -> 01:45:30 م (ar-EG)`
"u" | 정렬 가능한 유니버설 날짜/시간 패턴 | [DateTime](xref:System.DateTime) 값 포함: `2009-06-15T13:45:30 -> 2009-06-15 13:45:30Z`입니다. [DateTimeOffset](xref:System.DateTimeOffset) 값 포함: `2009-06-15T13:45:30 -> 2009-06-15 20:45:30Z`
"U" | 유니버설 전체 날짜/시간 패턴입니다. | `2009-06-15T13:45:30 -> Monday, June 15, 2009 8:45:30 PM (en-US)`; `2009-06-15T13:45:30 -> den 15 juni 2009 20:45:30 (sv-SE)`; `2009-06-15T13:45:30 -> Δευτέρα, 15 Ιουνίου 2009 8:45:30 μμ (el-GR)`
"Y", "y" | 연도 월 패턴 | `2009-06-15T13:45:30 -> June, 2009 (en-US)`; `2009-06-15T13:45:30 -> juni 2009 (da-DK)`; `2009-06-15T13:45:30 -> Juni 2009 (id-ID)`
기타 모든 단일 문자 | 알 수 없는 지정자입니다. | 런타임 [FormatException](xref:System.FormatException)을 throw합니다.

## <a name="how-standard-format-strings-work"></a>표준 서식 문자열의 작동 방법

서식 지정 작업에서 표준 서식 문자열은 단순히 사용자 지정 서식 문자열에 대한 별칭입니다. 별칭을 사용하여 사용자 지정 서식 문자열을 참조할 경우 별칭은 변하지 않지만 사용자 지정 서식 문자열 자체는 변경될 수 있다는 장점이 있습니다. 이는 날짜 및 시간 값에 대한 문자열 표현이 일반적으로 문화권마다 다르다는 점을 감안했을 때 매우 중요한 기능입니다. 예를 들어, "d" 표준 서식 문자열은 날짜 및 시간 값을 간단한 날짜 패턴을 사용하여 표시함을 나타냅니다. 고정 문화권의 경우 이 패턴이 "MM/dd/yyyy"이고 fr-FR 문화권의 경우 "dd/MM/yyyy"이며 ja-JP 문화권의 경우 "yyyy/MM/dd"입니다.

서식 지정 작업에서 표준 서식 문자열이 특정 문화권의 사용자 지정 서식 문자열에 매핑되는 경우 응용 프로그램에서는 다음 방법 중 하나로 사용할 사용자 지정 서식 문자열이 포함된 특정 문화권을 정의할 수 있습니다.

* 기본 또는 현재 문화권을 사용할 수 있습니다. 다음 예제에서는 현재 문화권의 간단한 날짜 서식을 사용하여 날짜를 표시합니다. 이 예제의 경우 현재 문화권이 en-US입니다. 

  ```csharp
  // Display using current (en-us) culture's short date format
  DateTime thisDate = new DateTime(2008, 3, 15);
  Console.WriteLine(thisDate.ToString("d"));           // Displays 3/15/2008
  ```

  ```vb
  ' Display using current (en-us) culture's short date format
  Dim thisDate As Date = #03/15/2008#
  Console.WriteLine(thisDate.ToString("d"))     ' Displays 3/15/2008
  ```
  
* 사용할 서식 지정과 관련된 문화권을 나타내는 [CultureInfo](xref:System.Globalization.CultureInfo) 개체를 [IFormatProvider](xref:System.IFormatProvider) 매개 변수가 있는 메서드에 전달할 수 있습니다. 다음 예제에서는 pt-BR 문화권의 간단한 날짜 서식을 사용하여 날짜를 표시합니다.
  
  ```csharp
  // Display using pt-BR culture's short date format
  DateTime thisDate = new DateTime(2008, 3, 15);
  CultureInfo culture = new CultureInfo("pt-BR");      
  Console.WriteLine(thisDate.ToString("d", culture));  // Displays 15/3/2008
  ```

  ```vb
  ' Display using pt-BR culture's short date format
  Dim thisDate As Date = #03/15/2008#
  Dim culture As New CultureInfo("pt-BR")      
  Console.WriteLine(thisDate.ToString("d", culture))   ' Displays 15/3/2008
  ```
  
* 서식 지정 정보를 제공하는 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체를 [IFormatProvider](xref:System.IFormatProvider) 매개 변수가 있는 메서드에 전달할 수 있습니다. 다음 예제에서는 hr-HR 문화권의 [DateTimeFormatInfo] 개체가 제공하는 간단한 날짜 서식을 사용하여 날짜를 표시합니다.  

  ```csharp
  // Display using date format information from hr-HR culture
  DateTime thisDate = new DateTime(2008, 3, 15);
  DateTimeFormatInfo fmt = (new CultureInfo("hr-HR")).DateTimeFormat;
  Console.WriteLine(thisDate.ToString("d", fmt));      // Displays 15.3.2008
  ```

  ```vb
  ' Display using date format information from hr-HR culture
  Dim thisDate As Date = #03/15/2008#
  Dim fmt As DateTimeFormatInfo = (New CultureInfo("hr-HR")).DateTimeFormat
  Console.WriteLine(thisDate.ToString("d", fmt))   ' Displays 15.3.2008
  ```
  
표준 서식 문자열을 보다 긴 고정 사용자 지정 서식 문자열에 대한 약식 표현으로 사용하는 경우도 있습니다. 표준 서식 문자열은 "O"(또는 "o"), "R"(또는 "r"), "s" 및 "u"라는 네 범주로 나뉩니다. 이 문자열은 고정 문화권에 정의된 사용자 지정 서식 문자열에 대응되며, 문화권마다 동일하게 인식되는 날짜 및 시간 값에 대한 문자열 표현을 생성합니다. 다음 표에서는 이러한 네 가지 표준 날짜 및 시간 서식 문자열에 대해 설명합니다.

표준 서식 문자열 | DateTimeFormatInfo.InvariantInfo 속성으로 정의된 문자열 | 사용자 지정 서식 문자열
---------------------- | ---------------------------------------------------- | --------------------
"O" 또는 "o" | 없음 | yyyy'-'MM'-'dd'T'HH':'mm':'ss'.'fffffffzz
"R" 또는 "r" | [RFC1123Pattern](xref:System.Globalization.DateTimeFormatInfo.RFC1123Pattern) | ddd, dd MMM yyyy HH':'mm':'ss 'GMT'
"s" | [SortableDateTime](xref:System.Globalization.DateTimeFormatInfo.SortableDateTimePattern) | yyyy'-'MM'-'dd'T'HH':'mm':'ss
"u" | [UniversalSortableDateTime](xref:System.Globalization.DateTimeFormatInfo.UniversalSortableDateTimePattern) | yyyy'-'MM'-'dd HH':'mm':'ss'Z'

다음 섹션에서는 [DateTime](xref:System.DateTime) 및 [DateTimeOffset](xref:System.DateTimeOffset) 값에 대한 표준 서식 지정자에 대해 설명합니다.

## <a name="the-short-date-d-format-specifier"></a>간단한 날짜("d") 서식 지정자

"d" 표준 서식 지정자는 특정 문화권의 [DateTimeFormatInfo.ShortDatePattern](xref:System.Globalization.DateTimeFormatInfo.ShortDatePattern) 속성에 의해 정의되는 사용자 지정 날짜 및 시간 서식 문자열을 나타냅니다. 예를 들어, 고정 문화권의 [ShortDatePattern](xref:System.Globalization.DateTimeFormatInfo.ShortDatePattern) 속성이 반환하는 사용자 지정 서식 문자열은 "MM/dd/yyyy"입니다. 

다음 테이블에서는 반환된 문자열의 서식을 제어하는 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체의 속성을 보여 줍니다.
다음 예제에서는 "d" 서식 지정자를 사용하여 날짜 및 시간 값을 표시합니다.

```csharp
DateTime date1 = new DateTime(2008,4, 10);
Console.WriteLine(date1.ToString("d", DateTimeFormatInfo.InvariantInfo));
// Displays 04/10/2008
Console.WriteLine(date1.ToString("d", 
                  CultureInfo.CreateSpecificCulture("en-US")));
// Displays 4/10/2008                       
Console.WriteLine(date1.ToString("d", 
                  CultureInfo.CreateSpecificCulture("en-NZ")));
// Displays 10/04/2008                       
Console.WriteLine(date1.ToString("d", 
                  CultureInfo.CreateSpecificCulture("de-DE")));
// Displays 10.04.2008 
```

```vb
Dim date1 As Date = #4/10/2008#
Console.WriteLine(date1.ToString("d", DateTimeFormatInfo.InvariantInfo))
' Displays 04/10/2008
Console.WriteLine(date1.ToString("d", _
                  CultureInfo.CreateSpecificCulture("en-US")))
' Displays 4/10/2008                       
Console.WriteLine(date1.ToString("d", _
                  CultureInfo.CreateSpecificCulture("en-NZ")))
' Displays 10/04/2008                       
Console.WriteLine(date1.ToString("d", _
                  CultureInfo.CreateSpecificCulture("de-DE")))
' Displays 10.04.2008 
```

## <a name="the-long-date-d-format-specifier"></a>자세한 날짜("D") 서식 지정자

"D" 표준 서식 지정자는 현재 [DateTimeFormatInfo.LongDatePattern](xref:System.Globalization.DateTimeFormatInfo.LongDatePattern) 속성에 의해 정의되는 사용자 지정 날짜 및 시간 서식 문자열을 나타냅니다. 예를 들어, 고정 문화권에 대한 사용자 지정 서식 문자열은 "dddd, dd MMMM yyyy"입니다.

다음 테이블에서는 반환된 문자열의 서식을 제어하는 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체의 속성을 보여 줍니다.

속성 | 설명
-------- | -----------
[LongDatePattern](xref:System.Globalization.DateTimeFormatInfo.LongDatePattern) | 결과 문자열의 전체 서식을 정의합니다.
[DayNames](xref:System.Globalization.DateTimeFormatInfo.DayNames) | 결과 문자열에 나타날 수 있는 지역화된 요일 이름을 정의합니다.
[MonthNames](xref:System.Globalization.DateTimeFormatInfo.MonthNames) | 결과 문자열에 나타날 수 있는 지역화된 월 이름을 정의합니다.

다음 예제에서는 "D" 서식 지정자를 사용하여 날짜 및 시간 값을 표시합니다.

```csharp
DateTime date1 = new DateTime(2008, 4, 10);
Console.WriteLine(date1.ToString("D", 
                  CultureInfo.CreateSpecificCulture("en-US")));
// Displays Thursday, April 10, 2008                        
Console.WriteLine(date1.ToString("D", 
                  CultureInfo.CreateSpecificCulture("pt-BR")));
// Displays quinta-feira, 10 de abril de 2008                        
Console.WriteLine(date1.ToString("D", 
                  CultureInfo.CreateSpecificCulture("es-MX")));
// Displays jueves, 10 de abril de 2008
```

```vb
Dim date1 As Date = #4/10/2008#
Console.WriteLine(date1.ToString("D", _
                  CultureInfo.CreateSpecificCulture("en-US")))
' Displays Thursday, April 10, 2008                        
Console.WriteLine(date1.ToString("D", _
                  CultureInfo.CreateSpecificCulture("pt-BR")))
' Displays quinta-feira, 10 de abril de 2008                        
Console.WriteLine(date1.ToString("D", _
                  CultureInfo.CreateSpecificCulture("es-MX")))
' Displays jueves, 10 de abril de 2008
```

## <a name="the-full-date-short-time-f-format-specifier"></a>전체 날짜,간단한 시간("f") 서식 지정자

"f" 표준 서식 지정자는 공백으로 구분된 자세한 날짜("D")와 간단한 시간("t") 패턴의 조합입니다.

결과 문자열은 특정 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체의 서식 지정 정보에 영향을 받습니다. 다음 테이블에서는 반환된 문자열의 서식을 제어할 수 있는 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체의 속성을 보여 줍니다. 일부 문화권의 [DateTimeFormatInfo.LongDatePattern](xref:System.Globalization.DateTimeFormatInfo.LongDatePattern) 및 [DateTimeFormatInfo.ShortTimePattern](xref:System.Globalization.DateTimeFormatInfo.ShortTimePattern) 속성에서 반환하는 사용자 지정 서식 지정자는 모든 속성을 사용할 수 없습니다.

속성 | 설명
-------- | -----------
[LongDatePattern](xref:System.Globalization.DateTimeFormatInfo.LongDatePattern) | 결과 문자열의 날짜 구성 요소 서식을 정의합니다.
[ShortTimePattern](xref:System.Globalization.DateTimeFormatInfo.ShortTimePattern) | 결과 문자열의 시간 구성 요소 서식을 정의합니다.
[DayNames](xref:System.Globalization.DateTimeFormatInfo.DayNames) | 결과 문자열에 나타날 수 있는 지역화된 요일 이름을 정의합니다.
[MonthNames](xref:System.Globalization.DateTimeFormatInfo.MonthNames) | 결과 문자열에 나타날 수 있는 지역화된 월 이름을 정의합니다.
[AMDesignator](xref:System.Globalization.DateTimeFormatInfo.AMDesignator) | 12시간 서식으로 자정부터 정오까지의 시간을 나타내는 문자열을 정의합니다.
[PMDesignator](xref:System.Globalization.DateTimeFormatInfo.PMDesignator) | 12시간 서식으로 정오부터 자정까지의 시간을 나타내는 문자열을 정의합니다.

다음 예제에서는 "f" 서식 지정자를 사용하여 날짜 및 시간 값을 표시합니다.

```csharp
DateTime date1 = new DateTime(2008, 4, 10, 6, 30, 0);
Console.WriteLine(date1.ToString("f", 
                  CultureInfo.CreateSpecificCulture("en-US")));
// Displays Thursday, April 10, 2008 6:30 AM                        
Console.WriteLine(date1.ToString("f", 
                  CultureInfo.CreateSpecificCulture("fr-FR")));
// Displays jeudi 10 avril 2008 06:30 
```

```vb
Dim date1 As Date = #4/10/2008 6:30AM#
Console.WriteLine(date1.ToString("f", _
                  CultureInfo.CreateSpecificCulture("en-US")))
' Displays Thursday, April 10, 2008 6:30 AM                        
Console.WriteLine(date1.ToString("f", _
                  CultureInfo.CreateSpecificCulture("fr-FR")))
' Displays jeudi 10 avril 2008 06:30 
```

## <a name="the-full-date-long-time-f-format-specifier"></a>전체 날짜, 자세한 시간("F") 서식 지정자

"F" 표준 서식 지정자는 현재 [DateTimeFormatInfo.FullDateTimePattern](xref:System.Globalization.DateTimeFormatInfo.FullDateTimePattern) 속성에 의해 정의되는 사용자 지정 날짜 및 시간 서식 문자열을 나타냅니다. 예를 들어, 고정 문화권에 대한 사용자 지정 서식 문자열은 "dddd, dd MMMM yyyy HH:mm:ss"입니다.

다음 테이블에서는 반환된 문자열의 서식을 제어할 수 있는 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체의 속성을 보여 줍니다. 일부 문화권의 [FullDateTimePattern](xref:System.Globalization.DateTimeFormatInfo.FullDateTimePattern) 속성에서 반환하는 사용자 지정 서식 지정자는 일부 속성을 사용하지 못할 수 있습니다.

속성 | 설명
-------- | -----------
[FullDateTimePattern](xref:System.Globalization.DateTimeFormatInfo.FullDateTimePattern) | 결과 문자열의 전체 서식을 정의합니다.
[DayNames](xref:System.Globalization.DateTimeFormatInfo.DayNames) | 결과 문자열에 나타날 수 있는 지역화된 요일 이름을 정의합니다.
[MonthNames](xref:System.Globalization.DateTimeFormatInfo.MonthNames) | 결과 문자열에 나타날 수 있는 지역화된 월 이름을 정의합니다.
[AMDesignator](xref:System.Globalization.DateTimeFormatInfo.AMDesignator) | 12시간 서식으로 자정부터 정오까지의 시간을 나타내는 문자열을 정의합니다.
[PMDesignator](xref:System.Globalization.DateTimeFormatInfo.PMDesignator) | 12시간 서식으로 정오부터 자정까지의 시간을 나타내는 문자열을 정의합니다.

다음 예제에서는 "F" 서식 지정자를 사용하여 날짜 및 시간 값을 표시합니다.

```csharp
DateTime date1 = new DateTime(2008, 4, 10, 6, 30, 0);
Console.WriteLine(date1.ToString("F", 
                  CultureInfo.CreateSpecificCulture("en-US")));
// Displays Thursday, April 10, 2008 6:30:00 AM                        
Console.WriteLine(date1.ToString("F", 
                  CultureInfo.CreateSpecificCulture("fr-FR")));
// Displays jeudi 10 avril 2008 06:30:00 
```

```vb
Dim date1 As Date = #4/10/2008 6:30AM#
Console.WriteLine(date1.ToString("F", _
                  CultureInfo.CreateSpecificCulture("en-US")))
' Displays Thursday, April 10, 2008 6:30:00 AM                        
Console.WriteLine(date1.ToString("F", _
                  CultureInfo.CreateSpecificCulture("fr-FR")))
' Displays jeudi 10 avril 2008 06:30:00 
```

## <a name="the-general-date-short-time-g-format-specifier"></a>일반 날짜, 간단한 시간("g") 서식 지정자

"g" 표준 서식 지정자는 공백으로 구분된 간단한 날짜("d")와 간단한 시간("t") 패턴의 조합입니다.

결과 문자열은 특정 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체의 서식 지정 정보에 영향을 받습니다. 다음 테이블에서는 반환된 문자열의 서식을 제어할 수 있는 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체의 속성을 보여 줍니다. 일부 문화권의 [DateTimeFormatInfo.ShortDatePattern](xref:System.Globalization.DateTimeFormatInfo.ShortDatePattern) 및 [DateTimeFormatInfo.ShortTimePattern](xref:System.Globalization.DateTimeFormatInfo.ShortTimePattern) 속성에서 반환하는 사용자 지정 서식 지정자는 모든 속성을 사용할 수 없습니다.

속성 | 설명
-------- | -----------
[ShortDatePattern](xref:System.Globalization.DateTimeFormatInfo.ShortDatePattern) | 결과 문자열의 날짜 구성 요소 서식을 정의합니다.
[ShortTimePattern](xref:System.Globalization.DateTimeFormatInfo.ShortTimePattern) | 결과 문자열의 시간 구성 요소 서식을 정의합니다.
[AMDesignator](xref:System.Globalization.DateTimeFormatInfo.AMDesignator) | 12시간 서식으로 자정부터 정오까지의 시간을 나타내는 문자열을 정의합니다.
[PMDesignator](xref:System.Globalization.DateTimeFormatInfo.PMDesignator) | 12시간 서식으로 정오부터 자정까지의 시간을 나타내는 문자열을 정의합니다.

다음 예제에서는 "g" 서식 지정자를 사용하여 날짜 및 시간 값을 표시합니다. 

``` csharp
DateTime date1 = new DateTime(2008, 4, 10, 6, 30, 0);
Console.WriteLine(date1.ToString("g", 
                  DateTimeFormatInfo.InvariantInfo));
// Displays 04/10/2008 06:30                      
Console.WriteLine(date1.ToString("g", 
                  CultureInfo.CreateSpecificCulture("en-us")));
// Displays 4/10/2008 6:30 AM                       
Console.WriteLine(date1.ToString("g", 
                  CultureInfo.CreateSpecificCulture("fr-BE")));
// Displays 10/04/2008 6:30 
```

```vb
Dim date1 As Date = #4/10/2008 6:30AM#
Console.WriteLine(date1.ToString("g", _
                  DateTimeFormatInfo.InvariantInfo))
' Displays 04/10/2008 06:30                      
Console.WriteLine(date1.ToString("g", _
                  CultureInfo.CreateSpecificCulture("en-us")))
' Displays 4/10/2008 6:30 AM                       
Console.WriteLine(date1.ToString("g", _
                  CultureInfo.CreateSpecificCulture("fr-BE")))
' Displays 10/04/2008 6:30  
```

## <a name="the-general-date-long-time-g-format-specifier"></a>일반 날짜, 자세한 시간("G") 서식 지정자

"G" 표준 서식 지정자는 공백으로 구분된 간단한 날짜("d")와 자세한 시간("T") 패턴의 조합입니다.

결과 문자열은 특정 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체의 서식 지정 정보에 영향을 받습니다. 다음 테이블에서는 반환된 문자열의 서식을 제어할 수 있는 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체의 속성을 보여 줍니다. 일부 문화권의 [DateTimeFormatInfo.ShortDatePattern](xref:System.Globalization.DateTimeFormatInfo.ShortDatePattern) 및 [DateTimeFormatInfo.LongTimePattern](xref:System.Globalization.DateTimeFormatInfo.LongTimePattern) 속성에서 반환하는 사용자 지정 서식 지정자는 모든 속성을 사용할 수 없습니다.

속성 | 설명
-------- | -----------
[ShortDatePattern](xref:System.Globalization.DateTimeFormatInfo.ShortDatePattern) | 결과 문자열의 날짜 구성 요소 서식을 정의합니다.
[LongTimePattern](xref:System.Globalization.DateTimeFormatInfo.LongTimePattern) | 결과 문자열의 시간 구성 요소 서식을 정의합니다.
[AMDesignator](xref:System.Globalization.DateTimeFormatInfo.AMDesignator) | 12시간 서식으로 자정부터 정오까지의 시간을 나타내는 문자열을 정의합니다.
[PMDesignator](xref:System.Globalization.DateTimeFormatInfo.PMDesignator) | 12시간 서식으로 정오부터 자정까지의 시간을 나타내는 문자열을 정의합니다.

다음 예제에서는 "G" 서식 지정자를 사용하여 날짜 및 시간 값을 표시합니다.

```csharp
DateTime date1 = new DateTime(2008, 4, 10, 6, 30, 0);
Console.WriteLine(date1.ToString("G", 
                  DateTimeFormatInfo.InvariantInfo));
// Displays 04/10/2008 06:30:00
Console.WriteLine(date1.ToString("G", 
                  CultureInfo.CreateSpecificCulture("en-us")));
// Displays 4/10/2008 6:30:00 AM                        
Console.WriteLine(date1.ToString("G", 
                  CultureInfo.CreateSpecificCulture("nl-BE")));
// Displays 10/04/2008 6:30:00  
```

```vb
Dim date1 As Date = #4/10/2008 6:30AM#
Console.WriteLine(date1.ToString("G", _
                  DateTimeFormatInfo.InvariantInfo))
' Displays 04/10/2008 06:30:00
Console.WriteLine(date1.ToString("G", _
                  CultureInfo.CreateSpecificCulture("en-us")))
' Displays 4/10/2008 6:30:00 AM                        
Console.WriteLine(date1.ToString("G", _
                  CultureInfo.CreateSpecificCulture("nl-BE")))
' Displays 10/04/2008 6:30:00                       
```

## <a name="the-month-m-m-format-specifier"></a>월("M", "m") 서식 지정자

"M" 또는 "m" 표준 서식 지정자는 현재 [DateTimeFormatInfo.MonthDayPattern](xref:System.Globalization.DateTimeFormatInfo.MonthDayPattern) 속성에 의해 정의되는 사용자 지정 날짜 및 시간 서식 문자열을 나타냅니다. 예를 들어, 고정 문화권에 대한 사용자 지정 서식 문자열은 "MMMM dd"입니다.

다음 테이블에서는 반환된 문자열의 서식을 제어하는 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체의 속성을 보여 줍니다.

속성 | 설명
-------- | -----------
[MonthDayPattern](xref:System.Globalization.DateTimeFormatInfo.MonthDayPattern) | 결과 문자열의 전체 서식을 정의합니다.
[MonthNames](xref:System.Globalization.DateTimeFormatInfo.MonthNames) | 결과 문자열에 나타날 수 있는 지역화된 월 이름을 정의합니다.

다음 예제에서는 "m" 서식 지정자를 사용하여 날짜 및 시간 값을 표시합니다.

```csharp
DateTime date1 = new DateTime(2008, 4, 10, 6, 30, 0);
Console.WriteLine(date1.ToString("m", 
                  CultureInfo.CreateSpecificCulture("en-us")));
// Displays April 10                        
Console.WriteLine(date1.ToString("m", 
                  CultureInfo.CreateSpecificCulture("ms-MY")));
// Displays 10 April  
```

```vb
Dim date1 As Date = #4/10/2008 6:30AM#
Console.WriteLine(date1.ToString("m", _
                  CultureInfo.CreateSpecificCulture("en-us")))
' Displays April 10                        
Console.WriteLine(date1.ToString("m", _
                  CultureInfo.CreateSpecificCulture("ms-MY")))
' Displays 10 April
```

## <a name="the-roundtrip-o-o-format-specifier"></a>라운드트립("O", "o") 서식 지정자

"O" 또는 "o" 표준 서식 지정자는 표준 시간대 정보를 유지하는 패턴을 사용하여 사용자 지정 날짜 및 시간 서식 문자열을 나타내고 ISO 8601을 준수하는 결과 문자열을 생략합니다. [DateTime](xref:System.DateTime) 값의 경우 이 서식 지정자는 [DateTime.Kind](xref:System.DateTime.Kind) 속성과 함께 날짜 및 시간 값을 텍스트로 유지합니다. 서식이 지정된 문자열은 스타일 매개 변수가 [DateTimeStyles.RoundtripKind](xref:System.Globalization.DateTimeStyles.RoundtripKind)로 설정된 경우 [DateTime.Parse(String, IFormatProvider, DateTimeStyles)](xref:System.DateTime.Parse(System.String,System.IFormatProvider,System.Globalization.DateTimeStyles)) 또는 [DateTime.ParseExact](xref:System.DateTime.ParseExact(System.String,System.String,System.IFormatProvider,System.Globalization.DateTimeStyles)) 메서드를 사용하여 다시 구문 분석될 수 있습니다. 

"O" 또는 "o" 표준 서식 지정자는 [DateTime] 값에 대한 "yyyy'-'MM'-'dd'T'HH':'mm':'ss'.'fffffffK" 사용자 지정 서식 문자열과 [DateTimeOffset](xref:System.DateTimeOffset) 값에 대한 "yyyy'-'MM'-'dd'T'HH':'mm':'ss'.'fffffffzzz" 사용자 지정 문자열에 해당합니다. 이 문자열에서 하이픈, 콜론, 문자 "T" 등의 개별 문자를 구분하는 작은따옴표 쌍은 변경할 수 없는 리터럴입니다. 아포스트로피는 출력 문자열에 표시되지 않습니다. 

"O" 또는 "o" 표준 서식 지정자(및 "yyyy'-'MM'-'dd'T'HH':'mm':'ss'.'fffffffK" 사용자 지정 서식 문자열)는 ISO 8601이 [DateTime](xref:System.DateTime) 값의 [Kind](xref:System.DateTime.Kind) 속성을 유지하기 위해 표준 시간대 정보를 나타내는 세 가지 방법을 이용합니다. 

* [DateTimeKind.Local](xref:System.DateTimeKind.Local) 날짜 및 시간 값의 표준 시간대 구성 요소는 UTC의 오프셋입니다(예: +01:00, -07:00). 모든 [DateTimeOffset] 값은 이 서식으로도 표현됩니다. 

* [DateTimeKind.Utc](xref:System.DateTimeKind.Utc) 날짜 및 시간 값의 표준 시간대 구성 요소는 "Z"(0 오프셋을 의미함)를 사용하여 UTC를 나타냅니다. 

* [DateTimeKind.Unspecified](xref:System.DateTimeKind.Unspecified) 날짜 및 시간 값은 표준 시간대 정보를 포함하지 않습니다. 

"O" 또는 "o" 표준 서식 지정자는 국제 표준을 준수하므로, 이 지정자를 사용하는 서식 지정 또는 구문 분석 작업에서는 항상 고정 문화권 및 양력을 사용합니다. 

[DateTime](xref:System.DateTime) 및 [DateTimeOffset](xref:System.DateTimeOffset)의 `Parse`, `TryParse`, `ParseExact`, `TryParseExact` 메서드에 전달되는 문자열은 이러한 형식 중 하나인 경우 "O" 또는 "o" 서식 지정자를 사용하여 구문 분석할 수 있습니다. [DateTime](xref:System.DateTime) 개체의 경우 호출하는 구문 분석 오버로드는 값이 [DateTimeStyles.RoundtripKind](xref:System.Globalization.DateTimeStyles.RoundtripKind)인 스타일 매개 변수도 포함해야 합니다. "O" 또는 "o" 서식 지정자에 해당하는 사용자 지정 서식 문자열로 구문 분석 메서드를 호출하는 경우 "O" 또는 "o"와 동일한 결과를 얻지 못합니다. 사용자 지정 서식 문자열을 사용하는 구문 분석 메서드는 표준 시간대 구성 요소가 없거나 "Z"를 사용하여 UTC를 나타내는 날짜 및 시간 값의 문자열 표현을 구문 분석할 수 없기 때문입니다. 

다음 예제에서는 "o" 서식 지정자를 사용하여 미국 태평양 표준 시간대에 있는 시스템의 일련의 [DateTime](xref:System.DateTime) 값 및 [DateTimeOffset](xref:System.DateTimeOffset) 값을 표시합니다. 

```csharp
using System;

public class Example
{
   public static void Main()
   {
       DateTime dat = new DateTime(2009, 6, 15, 13, 45, 30, 
                                   DateTimeKind.Unspecified);
       Console.WriteLine("{0} ({1}) --> {0:O}", dat, dat.Kind); 

       DateTime uDat = new DateTime(2009, 6, 15, 13, 45, 30, 
                                    DateTimeKind.Utc);
       Console.WriteLine("{0} ({1}) --> {0:O}", uDat, uDat.Kind);

       DateTime lDat = new DateTime(2009, 6, 15, 13, 45, 30, 
                                    DateTimeKind.Local);
       Console.WriteLine("{0} ({1}) --> {0:O}\n", lDat, lDat.Kind);

       DateTimeOffset dto = new DateTimeOffset(lDat);
       Console.WriteLine("{0} --> {0:O}", dto);
   }
}
// The example displays the following output:
//    6/15/2009 1:45:30 PM (Unspecified) --> 2009-06-15T13:45:30.0000000
//    6/15/2009 1:45:30 PM (Utc) --> 2009-06-15T13:45:30.0000000Z
//    6/15/2009 1:45:30 PM (Local) --> 2009-06-15T13:45:30.0000000-07:00
//    
//    6/15/2009 1:45:30 PM -07:00 --> 2009-06-15T13:45:30.0000000-07:00
```

```vb
Module Example
   Public Sub Main()
       Dim dat As New Date(2009, 6, 15, 13, 45, 30, 
                           DateTimeKind.Unspecified)
       Console.WriteLine("{0} ({1}) --> {0:O}", dat, dat.Kind) 

       Dim uDat As New Date(2009, 6, 15, 13, 45, 30, DateTimeKind.Utc)
       Console.WriteLine("{0} ({1}) --> {0:O}", uDat, uDat.Kind)

       Dim lDat As New Date(2009, 6, 15, 13, 45, 30, DateTimeKind.Local)
       Console.WriteLine("{0} ({1}) --> {0:O}", lDat, lDat.Kind)
       Console.WriteLine()

       Dim dto As New DateTimeOffset(lDat)
       Console.WriteLine("{0} --> {0:O}", dto)
   End Sub
End Module
' The example displays the following output:
'    6/15/2009 1:45:30 PM (Unspecified) --> 2009-06-15T13:45:30.0000000
'    6/15/2009 1:45:30 PM (Utc) --> 2009-06-15T13:45:30.0000000Z
'    6/15/2009 1:45:30 PM (Local) --> 2009-06-15T13:45:30.0000000-07:00
'    
'    6/15/2009 1:45:30 PM -07:00 --> 2009-06-15T13:45:30.0000000-07:00
```

다음 예제에서는 "o" 서식 지정자를 사용하여 서식이 지정된 문자열을 만든 다음 날짜 및 시간 `Parse` 메서드를 호출하여 원래 날짜 및 시간 값을 복원합니다.

```csharp
// Round-trip DateTime values.
DateTime originalDate, newDate;
string dateString;
// Round-trip a local time.
originalDate = DateTime.SpecifyKind(new DateTime(2008, 4, 10, 6, 30, 0), DateTimeKind.Local);
dateString = originalDate.ToString("o");
newDate = DateTime.Parse(dateString, null, DateTimeStyles.RoundtripKind);
Console.WriteLine("Round-tripped {0} {1} to {2} {3}.", originalDate, originalDate.Kind, 
                  newDate, newDate.Kind);
// Round-trip a UTC time.
originalDate = DateTime.SpecifyKind(new DateTime(2008, 4, 12, 9, 30, 0), DateTimeKind.Utc);                  
dateString = originalDate.ToString("o");
newDate = DateTime.Parse(dateString, null, DateTimeStyles.RoundtripKind);
Console.WriteLine("Round-tripped {0} {1} to {2} {3}.", originalDate, originalDate.Kind, 
                  newDate, newDate.Kind);
// Round-trip time in an unspecified time zone.
originalDate = DateTime.SpecifyKind(new DateTime(2008, 4, 13, 12, 30, 0), DateTimeKind.Unspecified);                  
dateString = originalDate.ToString("o");
newDate = DateTime.Parse(dateString, null, DateTimeStyles.RoundtripKind);
Console.WriteLine("Round-tripped {0} {1} to {2} {3}.", originalDate, originalDate.Kind, 
                  newDate, newDate.Kind);

// Round-trip a DateTimeOffset value.
DateTimeOffset originalDTO = new DateTimeOffset(2008, 4, 12, 9, 30, 0, new TimeSpan(-8, 0, 0));
dateString = originalDTO.ToString("o");
DateTimeOffset newDTO = DateTimeOffset.Parse(dateString, null, DateTimeStyles.RoundtripKind);
Console.WriteLine("Round-tripped {0} to {1}.", originalDTO, newDTO);
// The example displays the following output:
//    Round-tripped 4/10/2008 6:30:00 AM Local to 4/10/2008 6:30:00 AM Local.
//    Round-tripped 4/12/2008 9:30:00 AM Utc to 4/12/2008 9:30:00 AM Utc.
//    Round-tripped 4/13/2008 12:30:00 PM Unspecified to 4/13/2008 12:30:00 PM Unspecified.
//    Round-tripped 4/12/2008 9:30:00 AM -08:00 to 4/12/2008 9:30:00 AM -08:00.
```

```vb
' Round-trip DateTime values.
Dim originalDate, newDate As Date
Dim dateString As String
' Round-trip a local time.
originalDate = Date.SpecifyKind(#4/10/2008 6:30AM#, DateTimeKind.Local)
dateString = originalDate.ToString("o")
newDate = Date.Parse(dateString, Nothing, DateTimeStyles.RoundtripKind)
Console.WriteLine("Round-tripped {0} {1} to {2} {3}.", originalDate, originalDate.Kind, _
                  newDate, newDate.Kind)
' Round-trip a UTC time.
originalDate = Date.SpecifyKind(#4/12/2008 9:30AM#, DateTimeKind.Utc)                  
dateString = originalDate.ToString("o")
newDate = Date.Parse(dateString, Nothing, DateTimeStyles.RoundtripKind)
Console.WriteLine("Round-tripped {0} {1} to {2} {3}.", originalDate, originalDate.Kind, _
                  newDate, newDate.Kind)
' Round-trip time in an unspecified time zone.
originalDate = Date.SpecifyKind(#4/13/2008 12:30PM#, DateTimeKind.Unspecified)                  
dateString = originalDate.ToString("o")
newDate = Date.Parse(dateString, Nothing, DateTimeStyles.RoundtripKind)
Console.WriteLine("Round-tripped {0} {1} to {2} {3}.", originalDate, originalDate.Kind, _
                  newDate, newDate.Kind)

' Round-trip a DateTimeOffset value.
Dim originalDTO As New DateTimeOffset(#4/12/2008 9:30AM#, New TimeSpan(-8, 0, 0))
dateString = originalDTO.ToString("o")
Dim newDTO As DateTimeOffset = DateTimeOffset.Parse(dateString, Nothing, DateTimeStyles.RoundtripKind)
Console.WriteLine("Round-tripped {0} to {1}.", originalDTO, newDTO)
' The example displays the following output:
'    Round-tripped 4/10/2008 6:30:00 AM Local to 4/10/2008 6:30:00 AM Local.
'    Round-tripped 4/12/2008 9:30:00 AM Utc to 4/12/2008 9:30:00 AM Utc.
'    Round-tripped 4/13/2008 12:30:00 PM Unspecified to 4/13/2008 12:30:00 PM Unspecified.
'    Round-tripped 4/12/2008 9:30:00 AM -08:00 to 4/12/2008 9:30:00 AM -08:00.
```

## <a name="the-rfc1123-r-r-format-specifier"></a>RFC1123("R", "r") 서식 지정자

"R" 또는 "r" 표준 서식 지정자는 [DateTimeFormatInfo.RFC1123Pattern](xref:System.Globalization.DateTimeFormatInfo.RFC1123Pattern) 속성에 의해 정의되는 사용자 지정 날짜 및 시간 서식 문자열을 나타냅니다. 이 패턴은 정의된 표준을 반영하며 해당 속성은 읽기 전용입니다. 따라서 이 패턴은 사용된 문화권이나 제공된 서식 공급자에 관계없이 항상 같습니다. 사용자 지정 서식 문자열은 "ddd, dd MMM yyyy HH':'mm':'ss 'GMT'"입니다. 이 표준 서식 지정자를 사용할 경우 서식 지정 또는 구문 분석 작업에서 항상 고정 문화권이 사용됩니다.

결과 문자열은 고정 문화권을 나타내는 [DateTimeFormatInfo.InvariantInfo](xref:System.Globalization.DateTimeFormatInfo.InvariantInfo) 속성에서 반환하는 다음과 같은 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체 속성의 영향을 받습니다.

속성 | 설명
-------- | -----------
[RFC1123Pattern](xref:System.Globalization.DateTimeFormatInfo.RFC1123Pattern) | 결과 문자열의 서식을 정의합니다.
[AbbreviatedDayNames](xref:System.Globalization.DateTimeFormatInfo.AbbreviatedDayNames) | 결과 문자열에 나타날 수 있는 약식 요일 이름을 정의합니다.
[AbbreviatedMonthNames](xref:System.Globalization.DateTimeFormatInfo.AbbreviatedMonthNames) | 결과 문자열에 나타날 수 있는 약식 월 이름을 정의합니다.

RFC 1123 표준에 따라 시간이 UTC(Coordinated Universal Time)로 표시되지만 서식 지정 작업 중에 [DateTime](xref:System.DateTime) 또는 개체의 값이 수정되지 않습니다. 따라서 서식 지정 작업을 수행하기 전에 [DateTime.ToUniversalTime](xref:System.DateTime.ToUniversalTime) 메서드를 호출하여 [DateTime](xref:System.DateTime) 값을 UTC로 변환해야 합니다. 반면에 [DateTimeOffset](xref:System.DateTimeOffset) 값은 이 변환을 자동으로 수행하므로, 서식 지정 작업 전에 [DateTimeOffset.ToUniversalTime](xref:System.DateTimeOffset.ToUniversalTime) 메서드를 호출할 필요가 없습니다. 

다음 예제에서는 "r" 서식 지정자를 사용하여 미국 태평양 표준 시간대에 있는 시스템의 [DateTime](xref:System.DateTime) 값 및 [DateTimeOffset](xref:System.DateTimeOffset) 값을 표시합니다.

```csharp
DateTime date1 = new DateTime(2008, 4, 10, 6, 30, 0);
DateTimeOffset dateOffset = new DateTimeOffset(date1, 
                            TimeZoneInfo.Local.GetUtcOffset(date1));
Console.WriteLine(date1.ToUniversalTime().ToString("r"));
// Displays Thu, 10 Apr 2008 13:30:00 GMT                       
Console.WriteLine(dateOffset.ToUniversalTime().ToString("r"));
// Displays Thu, 10 Apr 2008 13:30:00 GMT  
```

```vb
Dim date1 As Date = #4/10/2008 6:30AM#
Dim dateOffset As New DateTimeOffset(date1, TimeZoneInfo.Local.GetUtcOFfset(date1))
Console.WriteLine(date1.ToUniversalTime.ToString("r"))
' Displays Thu, 10 Apr 2008 13:30:00 GMT                       
Console.WriteLine(dateOffset.ToUniversalTime.ToString("r"))
' Displays Thu, 10 Apr 2008 13:30:00 GMT
```

## <a name="the-sortable-s-format-specifier"></a>정렬 가능한("s") 서식 지정자

"s" 표준 서식 지정자는 [DateTimeFormatInfo.SortableDateTimePattern](xref:System.Globalization.DateTimeFormatInfo.SortableDateTimePattern) 속성에 의해 정의되는 사용자 지정 날짜 및 시간 서식 문자열을 나타냅니다. 이 패턴은 정의된 표준(ISO 8601)을 반영하며 해당 속성은 읽기 전용입니다. 따라서 이 패턴은 사용된 문화권이나 제공된 서식 공급자에 관계없이 항상 같습니다. 사용자 지정 서식 문자열은 "yyyy'-'MM'-'dd'T'HH':'mm':'ss"입니다.

"s" 형식 지정자는 날짜 및 시간 값에 따라 오름차순 또는 내림차순으로 일관되게 정렬되는 결과 문자열을 생성하기 위한 것입니다. 따라서, "s" 표준 서식 지정자는 날짜 및 시간 값을 일관된 형식으로 나타내지만 서식 지정 작업에서 [DateTime.Kind](xref:System.DateTime.Kind) 속성이나 해당 [DateTimeOffset.Offset](xref:System.DateTimeOffset.Offset) 값을 반영하도록 형식이 지정되는 날짜 및 시간 개체의 값을 수정하지는 않습니다. 예를 들어 날짜 및 시간 값 2014-11-15T18:32:17+00:00 및 2014-11-15T18:32:17+08:00의 형식을 지정하여 생성된 결과 문자열은 같습니다. 

이 표준 서식 지정자를 사용할 경우 서식 지정 또는 구문 분석 작업에서 항상 고정 문화권이 사용됩니다. 

다음 예제에서는 "s" 서식 지정자를 사용하여 미국 태평양 표준 시간대에 있는 시스템의 [DateTime](xref:System.DateTime) 값 및 [DateTimeOffset](xref:System.DateTimeOffset) 값을 표시합니다.

```csharp
DateTime date1 = new DateTime(2008, 4, 10, 6, 30, 0);
Console.WriteLine(date1.ToString("s"));
// Displays 2008-04-10T06:30:00
```

```vb
Dim date1 As Date = #4/10/2008 6:30AM#
Console.WriteLine(date1.ToString("s"))
' Displays 2008-04-10T06:30:00 
```

## <a name="the-short-time-t-format-specifier"></a>간단한 시간("t") 서식 지정자

"t" 표준 서식 지정자는 현재 [DateTimeFormatInfo.ShortTimePattern](xref:System.Globalization.DateTimeFormatInfo.ShortTimePattern) 속성에 의해 정의되는 사용자 지정 날짜 및 시간 서식 문자열을 나타냅니다. 예를 들어, 고정 문화권에 대한 사용자 지정 서식 문자열은 "HH:mm"입니다.

결과 문자열은 특정 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체의 서식 지정 정보에 영향을 받습니다. 다음 테이블에서는 반환된 문자열의 서식을 제어할 수 있는 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체의 속성을 보여 줍니다. 일부 문화권의 [DateTimeFormatInfo.ShortTimePattern](xref:System.Globalization.DateTimeFormatInfo.ShortTimePattern) 속성에서 반환하는 사용자 지정 서식 지정자는 일부 속성을 사용하지 못할 수 있습니다.

속성 | 설명
-------- | -----------
[DateTimeFormatInfo.ShortTimePattern](xref:System.Globalization.DateTimeFormatInfo.ShortTimePattern) | 결과 문자열의 시간 구성 요소 서식을 정의합니다.
[AMDesignator](xref:System.Globalization.DateTimeFormatInfo.AMDesignator) | 12시간 서식으로 자정부터 정오까지의 시간을 나타내는 문자열을 정의합니다.
[PMDesignator](xref:System.Globalization.DateTimeFormatInfo.PMDesignator) | 12시간 서식으로 정오부터 자정까지의 시간을 나타내는 문자열을 정의합니다.

다음 예제에서는 "t" 서식 지정자를 사용하여 날짜 및 시간 값을 표시합니다.

```csharp
DateTime date1 = new DateTime(2008, 4, 10, 6, 30, 0);
Console.WriteLine(date1.ToString("t", 
                  CultureInfo.CreateSpecificCulture("en-us")));
// Displays 6:30 AM                        
Console.WriteLine(date1.ToString("t", 
                  CultureInfo.CreateSpecificCulture("es-ES")));
// Displays 6:30  
```

```vb
Dim date1 As Date = #4/10/2008 6:30AM#
Console.WriteLine(date1.ToString("t", _
                  CultureInfo.CreateSpecificCulture("en-us")))
' Displays 6:30 AM                        
Console.WriteLine(date1.ToString("t", _
                  CultureInfo.CreateSpecificCulture("es-ES")))
' Displays 6:30
```

## <a name="the-long-time-t-format-specifier"></a>자세한 시간("T") 서식 지정자

"T" 표준 서식 지정자는 특정 문화권의 [DateTimeFormatInfo.LongTimePattern](xref:System.Globalization.DateTimeFormatInfo.LongTimePattern) 속성에 의해 정의되는 사용자 지정 날짜 및 시간 서식 문자열을 나타냅니다. 예를 들어, 고정 문화권에 대한 사용자 지정 서식 문자열은 "HH:mm:ss"입니다.

다음 테이블에서는 반환된 문자열의 서식을 제어할 수 있는 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체의 속성을 보여 줍니다. 일부 문화권의 [DateTimeFormatInfo.LongTimePattern](xref:System.Globalization.DateTimeFormatInfo.LongTimePattern) 속성에서 반환하는 사용자 지정 서식 지정자는 일부 속성을 사용하지 못할 수 있습니다.

속성 | 설명
-------- | -----------
[LongTimePattern](xref:System.Globalization.DateTimeFormatInfo.LongTimePattern) | 결과 문자열의 시간 구성 요소 서식을 정의합니다.
[AMDesignator](xref:System.Globalization.DateTimeFormatInfo.AMDesignator) | 12시간 서식으로 자정부터 정오까지의 시간을 나타내는 문자열을 정의합니다.
[PMDesignator](xref:System.Globalization.DateTimeFormatInfo.PMDesignator) | 12시간 서식으로 정오부터 자정까지의 시간을 나타내는 문자열을 정의합니다.

다음 예제에서는 "T" 서식 지정자를 사용하여 날짜 및 시간 값을 표시합니다.

```csharp
DateTime date1 = new DateTime(2008, 4, 10, 6, 30, 0);
Console.WriteLine(date1.ToString("T", 
                  CultureInfo.CreateSpecificCulture("en-us")));
// Displays 6:30:00 AM                       
Console.WriteLine(date1.ToString("T", 
                  CultureInfo.CreateSpecificCulture("es-ES")));
// Displays 6:30:00  
```

```vb
Dim date1 As Date = #4/10/2008 6:30AM#
Console.WriteLine(date1.ToString("T", _
                  CultureInfo.CreateSpecificCulture("en-us")))
' Displays 6:30:00 AM                       
Console.WriteLine(date1.ToString("T", _
                  CultureInfo.CreateSpecificCulture("es-ES")))
' Displays 6:30:00 
```

## <a name="the-universal-sortable-u-format-specifier"></a>정렬 가능한 유니버설("u") 서식 지정자

"u" 표준 서식 지정자는 [DateTimeFormatInfo.UniversalSortableDateTimePattern](xref:System.Globalization.DateTimeFormatInfo.UniversalSortableDateTimePattern) 속성에 의해 정의되는 사용자 지정 날짜 및 시간 서식 문자열을 나타냅니다. 이 패턴은 정의된 표준을 반영하며 해당 속성은 읽기 전용입니다. 따라서 이 패턴은 사용된 문화권이나 제공된 서식 공급자에 관계없이 항상 같습니다. 사용자 지정 서식 문자열은 "yyyy'-'MM'-'dd HH':'mm':'ss'Z"입니다. 이 표준 서식 지정자를 사용할 경우 서식 지정 또는 구문 분석 작업에서 항상 고정 문화권이 사용됩니다. 

결과 문자열에 시간이 UTC(Coordinated Universal Time)로 표시되어야 하지만 서식 지정 작업 중에 원래 [DateTime](xref:System.DateTime) 값의 변환이 전혀 수행되지 않습니다. 따라서 서식을 지정하기 전에 [DateTime.ToUniversalTime](xref:System.DateTime.ToUniversalTime) 메서드를 호출하여 [DateTime](xref:System.DateTime) 값을 UTC로 변환해야 합니다. 반면에 [DateTimeOffset](xref:System.DateTimeOffset) 값은 이 변환을 자동으로 수행하므로, 서식 지정 작업 전에 [DateTimeOffset.ToUniversalTime](xref:System.DateTimeOffset.ToUniversalTime) 메서드를 호출할 필요가 없습니다.   

다음 예제에서는 "u" 서식 지정자를 사용하여 날짜 및 시간 값을 표시합니다.

```csharp
DateTime date1 = new DateTime(2008, 4, 10, 6, 30, 0);
Console.WriteLine(date1.ToUniversalTime().ToString("u"));
// Displays 2008-04-10 13:30:00Z
```

```vb
Dim date1 As Date = #4/10/2008 6:30AM#
Console.WriteLine(date1.ToUniversalTime.ToString("u"))
' Displays 2008-04-10 13:30:00Z                       
```

## <a name="the-universal-full-u-format-specifier"></a>유니버설 전체("U") 서식 지정자

"U" 표준 서식 지정자는 특정 문화권의 [DateTimeFormatInfo.FullDateTimePattern](xref:System.Globalization.DateTimeFormatInfo.FullDateTimePattern) 속성에 의해 정의되는 사용자 지정 날짜 및 시간 서식 문자열을 나타냅니다. 이 패턴은 "F" 패턴과 동일합니다. 그러나 서식 지정 작업을 수행하기 전에 [DateTime](xref:System.DateTime) 값이 자동으로 UTC로 변환됩니다.

다음 테이블에서는 반환된 문자열의 서식을 제어할 수 있는 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체의 속성을 보여 줍니다. 일부 문화권의 [FullDateTimePattern](xref:System.Globalization.DateTimeFormatInfo.FullDateTimePattern) 속성에서 반환하는 사용자 지정 서식 지정자는 일부 속성을 사용하지 못할 수 있습니다.

속성 | 설명
-------- | -----------
[FullDateTimePattern](xref:System.Globalization.DateTimeFormatInfo.FullDateTimePattern) | 결과 문자열의 전체 서식을 정의합니다.
[DayNames](xref:System.Globalization.DateTimeFormatInfo.DayNames) | 결과 문자열에 나타날 수 있는 지역화된 요일 이름을 정의합니다.
[MonthNames](xref:System.Globalization.DateTimeFormatInfo.MonthNames) | 결과 문자열에 나타날 수 있는 지역화된 월 이름을 정의합니다.
[AMDesignator](xref:System.Globalization.DateTimeFormatInfo.AMDesignator) | 12시간 서식으로 자정부터 정오까지의 시간을 나타내는 문자열을 정의합니다.
[PMDesignator](xref:System.Globalization.DateTimeFormatInfo.PMDesignator) | 12시간 서식으로 정오부터 자정까지의 시간을 나타내는 문자열을 정의합니다.

"U" 서식 지정자는 [DateTimeOffset](xref:System.DateTimeOffset) 유형에서 지원하지 않고 [DateTimeOffset](xref:System.DateTimeOffset) 값의 서식을 지정하는 데 사용되는 경우 [FormatException](xref:System.FormatException)을 throw합니다.

다음 예제에서는 "U" 서식 지정자를 사용하여 날짜 및 시간 값을 표시합니다.

``` csharp
DateTime date1 = new DateTime(2008, 4, 10, 6, 30, 0);
Console.WriteLine(date1.ToString("U", 
                  CultureInfo.CreateSpecificCulture("en-US")));
// Displays Thursday, April 10, 2008 1:30:00 PM                       
Console.WriteLine(date1.ToString("U", 
                  CultureInfo.CreateSpecificCulture("sv-FI")));
// Displays den 10 april 2008 13:30:00  
```

```vb
Dim date1 As Date = #4/10/2008 6:30AM#
Console.WriteLine(date1.ToString("U", CultureInfo.CreateSpecificCulture("en-US")))
' Displays Thursday, April 10, 2008 1:30:00 PM                       
Console.WriteLine(date1.ToString("U", CultureInfo.CreateSpecificCulture("sv-FI")))
' Displays den 10 april 2008 13:30:00                       
```

## <a name="the-year-month-y-y-format-specifier"></a>년 월("Y", "y") 서식 지정자

"Y" 또는 "y" 표준 서식 지정자는 지정된 문화권의 [DateTimeFormatInfo.YearMonthPattern](xref:System.Globalization.DateTimeFormatInfo.YearMonthPattern) 속성에 의해 정의되는 사용자 지정 날짜 및 시간 서식 문자열을 나타냅니다. 예를 들어, 고정 문화권에 대한 사용자 지정 서식 문자열은 "yyyy MMMM"입니다.

다음 테이블에서는 반환된 문자열의 서식을 제어하는 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체의 속성을 보여 줍니다.

속성 | 설명
-------- | -----------
[YearMonthPattern](xref:System.Globalization.DateTimeFormatInfo.YearMonthPattern) | 결과 문자열의 전체 서식을 정의합니다.
[MonthNames](xref:System.Globalization.DateTimeFormatInfo.MonthNames) | 결과 문자열에 나타날 수 있는 지역화된 월 이름을 정의합니다.

다음 예제에서는 "y" 서식 지정자를 사용하여 날짜 및 시간 값을 표시합니다.

```csharp
DateTime date1 = new DateTime(2008, 4, 10, 6, 30, 0);
Console.WriteLine(date1.ToString("Y", 
                  CultureInfo.CreateSpecificCulture("en-US")));
// Displays April, 2008                       
Console.WriteLine(date1.ToString("y", 
                  CultureInfo.CreateSpecificCulture("af-ZA")));
// Displays April 2008 
```

```vb
Dim date1 As Date = #4/10/2008 6:30AM#
Console.WriteLine(date1.ToString("Y", CultureInfo.CreateSpecificCulture("en-US")))
' Displays April, 2008                       
Console.WriteLine(date1.ToString("y", CultureInfo.CreateSpecificCulture("af-ZA")))
' Displays April 2008
```                       

## <a name="notes"></a>참고

### <a name="datetimeformatinfo-properties"></a>DateTimeFormatInfo 속성

형식 지정은 현재 스레드 문화권에서 암시적으로 제공되거나 형식 지정을 호출하는 메서드의 [IFormatProvider](xref:System.IFormatProvider) 매개 변수를 통해 명시적으로 제공되는 현재 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체의 속성에 따라 영향을 받습니다. [IFormatProvider](xref:System.IFormatProvider) 매개 변수에 대해 응용 프로그램에서는 문화권을 나타내는 [CultureInfo](xref:System.Globalization.CultureInfo) 개체를 지정하거나 특정 문화권의 날짜 및 시간 서식 지정 규칙을 나타내는 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체를 지정해야 합니다. 대부분의 표준 날짜 및 시간 서식 지정자는 현재 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 개체의 속성으로 정의된 서식 지정 패턴의 별칭입니다. 따라서 응용 프로그램에서는 해당 [DateTimeFormatInfo](xref:System.Globalization.DateTimeFormatInfo) 속성의 날짜 및 시간 서식 패턴을 변경하여 일부 표준 날짜 및 시간 서식 지정자로 생성되는 결과를 변경할 수 있습니다.

## <a name="see-also"></a>참고 항목

[형식 서식 지정](formatting-types.md)

[사용자 지정 날짜 및 시간 서식 문자열](custom-datetime.md)




<!--HONumber=Nov16_HO3-->


