---
title: "방법: 날짜 및 시간 값 라운드트립"
description: "날짜 및 시간 값을 라운드트립하는 방법"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 07/26/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 15690f18-1bb9-4bb8-bc11-0b737e2f0859
translationtype: Human Translation
ms.sourcegitcommit: fb00da6505c9edb6a49d2003ae9bcb8e74c11d6c
ms.openlocfilehash: 00a09c8a60138a1828d4e8c62dd72b88abbf4bbe

---

# <a name="how-to-roundtrip-date-and-time-values"></a>방법: 날짜 및 시간 값 라운드트립

많은 응용 프로그램에서 날짜 및 시간 값은 단일 시점을 명확하게 식별하는 데 사용됩니다. 이 항목에서는 [DateTime](xref:System.DateTime) 값 및 [DateTimeOffset](xref:System.DateTimeOffset) 값을 저장하고 복원하는 방법을 보여 주며 복원된 값은 저장된 값과 같은 시간을 식별합니다.

## <a name="to-roundtrip-a-datetime-value"></a>DateTime 값을 라운드트립하려면

1. "o" 서식 지정자를 포함한 [DateTime.ToString(String)](xref:System.DateTime.ToString(System.String)) 메서드를 호출하여 [DateTime](xref:System.DateTime) 값을 해당 문자열 표현으로 변환합니다.

2. 파일에 대한 [DateTime](xref:System.DateTime) 값의 문자열 표현을 저장하거나 프로세스, 응용 프로그램 도메인 또는 컴퓨터 경계를 넘어 전달합니다.

3. [DateTime](xref:System.DateTime) 값을 나타내는 문자열을 검색합니다.

4. [DateTime.Parse(String, IFormatProvider, DateTimeStyles)](xref:System.DateTime.Parse(System.String,System.IFormatProvider,System.Globalization.DateTimeStyles)) 메서드를 호출하고 [DateTimeStyles.RoundtripKind](xref:System.Globalization.DateTimeStyles.RoundtripKind)를 *DateTimeStyles* 매개 변수의 값으로 전달합니다.

다음 예제에서는 [DateTime](xref:System.DateTime) 값을 라운드트립하는 방법을 보여 줍니다.

```csharp
const string fileName = @".\DateFile.txt";

StreamWriter outFile = new StreamWriter(fileName);

// Save DateTime value.
DateTime dateToSave = DateTime.SpecifyKind(new DateTime(2008, 6, 12, 18, 45, 15), 
                                           DateTimeKind.Local);
string dateString = dateToSave.ToString("o");      
Console.WriteLine("Converted {0} ({1}) to {2}.", 
                  dateToSave.ToString(), 
                  dateToSave.Kind.ToString(), 
                  dateString);      
outFile.WriteLine(dateString);
Console.WriteLine("Wrote {0} to {1}.", dateString, fileName);
outFile.Close();

// Restore DateTime value.
DateTime restoredDate;

StreamReader inFile = new StreamReader(fileName);
dateString = inFile.ReadLine();
inFile.Close();
restoredDate = DateTime.Parse(dateString, null, DateTimeStyles.RoundtripKind);
Console.WriteLine("Read {0} ({2}) from {1}.", restoredDate.ToString(), 
                                              fileName, 
                                              restoredDate.Kind.ToString());
// The example displays the following output:
//    Converted 6/12/2008 6:45:15 PM (Local) to 2008-06-12T18:45:15.0000000-05:00.
//    Wrote 2008-06-12T18:45:15.0000000-05:00 to .\DateFile.txt.
//    Read 6/12/2008 6:45:15 PM (Local) from .\DateFile.txt.
```

```vb
Const fileName As String = ".\DateFile.txt"

Dim outFile As New StreamWriter(fileName)

' Save DateTime value.
Dim dateToSave As Date = DateTime.SpecifyKind(#06/12/2008 6:45:15 PM#, _
                                              DateTimeKind.Local)
Dim dateString As String = dateToSave.ToString("o")      
Console.WriteLine("Converted {0} ({1}) to {2}.", dateToSave.ToString(), _
                  dateToSave.Kind.ToString(), dateString)      
outFile.WriteLine(dateString)
Console.WriteLine("Wrote {0} to {1}.", dateString, fileName)
outFile.Close()   

' Restore DateTime value.
Dim restoredDate As Date

Dim inFile As New StreamReader(fileName)
dateString = inFile.ReadLine()
inFile.Close()
restoredDate = DateTime.Parse(dateString, Nothing, DateTimeStyles.RoundTripKind)
Console.WriteLine("Read {0} ({2}) from {1}.", restoredDate.ToString(), _
                  fileName, restoredDAte.Kind.ToString())
' The example displays the following output:
'    Converted 6/12/2008 6:45:15 PM (Local) to 2008-06-12T18:45:15.0000000-05:00.
'    Wrote 2008-06-12T18:45:15.0000000-05:00 to .\DateFile.txt.
'    Read 6/12/2008 6:45:15 PM (Local) from .\DateFile.txt.
```

[DateTime](xref:System.DateTime) 값을 라운드트립할 경우 이 기술은 모든 현지 시간 및 범용 시간을 성공적으로 유지합니다. 예를 들어 로컬 [DateTime](xref:System.DateTime) 값이 미국에 있는 시스템에 저장되는 경우입니다. 태평양 표준 시간대는 미국에 있는 시스템에서 복원됩니다. 중앙 표준 시간대인 복원된 날짜 및 시간은 원래 시간보다 2시간이 늦어지며 이것은 두 표준 시간대 사이의 시간 차이를 반영합니다. 그러나 이 기술은 지정되지 않은 시간에 대해 반드시 정확하지는 않습니다. [Kind](xref:System.DateTime.Kind) 속성이 [지정되지 않음](xref:System.DateTimeKind.Unspecified)인 모든 [DateTime](xref:System.DateTime) 값은 현지 시간인 것처럼 처리됩니다. 그렇지 않은 경우 [DateTime](xref:System.DateTime)은 올바른 시점을 성공적으로 식별하지 않습니다. 이 제한에 대한 해결책은 저장 및 복원 작업의 표준 시간대와 날짜 및 시간 값을 밀접하게 연결하는 것입니다.

## <a name="to-roundtrip-a-datetimeoffset-value"></a>DateTimeOffset 값을 라운드트립하려면

"o" 서식 지정자를 포함한 [DateTimeOffset.ToString(String)](xref:System.DateTimeOffset.ToString(System.String)) 메서드를 호출하여 [DateTimeOffset](xref:System.DateTimeOffset) 값을 해당 문자열 표현으로 변환합니다.

2. 파일에 대한 [DateTimeOffset](xref:System.DateTimeOffset) 값의 문자열 표현을 저장하거나 프로세스, 응용 프로그램 도메인 또는 컴퓨터 경계를 넘어 전달합니다.

3. [DateTimeOffset](xref:System.DateTimeOffset) 값을 나타내는 문자열을 검색합니다.

4. [DateTimeOffset.Parse(String, IFormatProvider, DateTimeStyles)](xref:System.DateTimeOffset.Parse(System.String,System.IFormatProvider,System.Globalization.DateTimeStyles)) 메서드를 호출하고 [DateTimeStyles.RoundtripKind](xref:System.Globalization.DateTimeStyles.RoundtripKind)를 *styles* 매개 변수의 값으로 전달합니다.

다음 예제에서는 [DateTimeOffset](xref:System.DateTimeOffset) 값을 라운드트립하는 방법을 보여 줍니다.

```csharp
const string fileName = @".\DateOff.txt";

StreamWriter outFile = new StreamWriter(fileName);

// Save DateTime value.
DateTimeOffset dateToSave = new DateTimeOffset(2008, 6, 12, 18, 45, 15, 
                                               new TimeSpan(7, 0, 0));
string dateString = dateToSave.ToString("o");      
Console.WriteLine("Converted {0} to {1}.", dateToSave.ToString(), 
                  dateString);      
outFile.WriteLine(dateString);
Console.WriteLine("Wrote {0} to {1}.", dateString, fileName);
outFile.Close();

// Restore DateTime value.
DateTimeOffset restoredDateOff;

StreamReader inFile = new StreamReader(fileName);
dateString = inFile.ReadLine();
inFile.Close();
restoredDateOff = DateTimeOffset.Parse(dateString, null, 
                                       DateTimeStyles.RoundtripKind);
Console.WriteLine("Read {0} from {1}.", restoredDateOff.ToString(), 
                  fileName);
// The example displays the following output:
//    Converted 6/12/2008 6:45:15 PM +07:00 to 2008-06-12T18:45:15.0000000+07:00.
//    Wrote 2008-06-12T18:45:15.0000000+07:00 to .\DateOff.txt.
//    Read 6/12/2008 6:45:15 PM +07:00 from .\DateOff.txt.
```

```vb
Const fileName As String = ".\DateOff.txt"

Dim outFile As New StreamWriter(fileName)

' Save DateTime value.
Dim dateToSave As New DateTimeOffset(2008, 6, 12, 18, 45, 15, _
                                     New TimeSpan(7, 0, 0))
Dim dateString As String = dateToSave.ToString("o")      
Console.WriteLine("Converted {0} to {1}.", dateToSave.ToString(), dateString)      
outFile.WriteLine(dateString)
Console.WriteLine("Wrote {0} to {1}.", dateString, fileName)
outFile.Close()   

' Restore DateTime value.
Dim restoredDateOff As DateTimeOffset

Dim inFile As New StreamReader(fileName)
dateString = inFile.ReadLine()
inFile.Close()
restoredDateOff = DateTimeOffset.Parse(dateString, Nothing, DateTimeStyles.RoundTripKind)
Console.WriteLine("Read {0} from {1}.", restoredDateOff.ToString(), fileName)
' The example displays the following output:
'    Converted 6/12/2008 6:45:15 PM +07:00 to 2008-06-12T18:45:15.0000000+07:00.
'    Wrote 2008-06-12T18:45:15.0000000+07:00 to .\DateOff.txt.
'    Read 6/12/2008 6:45:15 PM +07:00 from .\DateOff.txt.
```

이 기술은 [DateTimeOffset](xref:System.DateTimeOffset) 값을 항상 단일 시점으로 명확하게 식별합니다. [DateTimeOffset.ToUniversalTime](xref:System.DateTimeOffset.ToUniversalTime) 메서드를 호출하여 값을 UTC(협정 세계시)로 변환하거나 [DateTimeOffset.ToOffset](xref:System.DateTimeOffset.ToOffset(System.TimeSpan)) 또는 [TimeZoneInfo.ConvertTime (DateTimeOffset TimeZoneInfo)](xref:System.TimeZoneInfo.ConvertTime(System.DateTime,System.TimeZoneInfo) 메서드를 호출하여 특정 표준 시간대의 시간으로 변환할 수 있습니다. 이 기술에 대한 주요 제한은 해당 날짜 및 시간 산술 연산이며 특정 표준 시간대의 시간을 나타내는 [DateTimeOffset](xref:System.DateTimeOffset) 값에서 수행되는 경우 해당 표준 시간대에 대한 정확한 결과를 생성하지 않을 수 있습니다. [DateTimeOffset](xref:System.DateTimeOffset) 값이 인스턴스화될 때 표준 시간대에서 분리되었기 때문입니다. 따라서 날짜 및 시간 계산을 수행할 경우에 해당 표준 시간대의 조정 규칙은 더 이상 적용될 수 없습니다. 날짜 및 시간 값과 동반되는 표준 시간대를 포함하는 사용자 지정 형식을 정의하여 이 문제를 해결할 수 있습니다.

## <a name="see-also"></a>참고 항목

[서식 지정 작업 수행](performing-formatting-operations.md)

[표준 날짜 및 시간 서식 문자열](standard-datetime.md)




<!--HONumber=Nov16_HO1-->


