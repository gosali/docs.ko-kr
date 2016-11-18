---
title: "방법: 컴퓨터에 있는 표준 시간대 열거"
description: "컴퓨터에 있는 표준 시간대를 열거하는 방법"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 08/15/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: c5ae4a6c-1790-4355-b5b1-879aaf956129
translationtype: Human Translation
ms.sourcegitcommit: c40c28da09e8a122b542463c197196c82c81dd19
ms.openlocfilehash: 5f385636137777013b540e8c1233751624139859

---

# <a name="how-to-enumerate-time-zones-present-on-a-computer"></a>방법: 컴퓨터에 있는 표준 시간대 열거

지정한 표준 시간대를 성공적으로 사용하려면 해당 표준 시간대 관련 정보를 시스템에서 사용할 수 있어야 합니다. 예를 들어 Windows 운영 체제에서는 이 정보를 레지스트리에 저장합니다. 그러나 전세계에 있는 표준 시간대의 전체 수는 상당히 많지만 레지스트리에는 이들 중 일부에 대한 정보만 포함됩니다. 또한 레지스트리 자체도 동적 구조이므로 그 내용이 실수나 고의로 변경될 수 있습니다. 따라서 언제나 응용 프로그램에서 특정 표준 시간대가 정의되어 있고 시스템에서 사용할 수 있다고 가정할 수는 없습니다. 표준 시간대 정보 응용 프로그램을 사용하는 많은 응용 프로그램의 첫 번째 단계는 필요한 표준 시간대를 로컬 시스템에서 사용할 수 있는지를 확인하거나 사용자에게 표준 시간대를 선택할 수 있는 목록을 제공하는 것입니다. 이렇게 하려면 응용 프로그램에서 로컬 시스템에 정의되어 있는 표준 시간대를 나열해야 합니다. 

## <a name="to-enumerate-the-time-zones-present-on-the-local-system"></a>로컬 시스템에 있는 표준 시간대를 열거하려면

1. [TimeZoneInfo.GetSystemTimeZones](xref:System.TimeZoneInfo.GetSystemTimeZones) 메서드를 호출합니다. 이 메서드는 [TimeZoneInfo](xref:System.TimeZoneInfo) 개체의 제네릭 [ReadOnlyCollection&lt;T&gt;](xref:System.Collections.ObjectModel.ReadOnlyCollection%601) 컬렉션을 반환합니다. 컬렉션의 엔트리는 해당 [DisplayName](xref:System.TimeZoneInfo.DisplayName) 속성을 기준으로 정렬됩니다. 예를 들면 다음과 같습니다.

    ```csharp
    ReadOnlyCollection<TimeZoneInfo> tzCollection;
    tzCollection = TimeZoneInfo.GetSystemTimeZones();
    ```

    ```vb
    Dim tzCollection As ReadOnlyCollection(Of TimeZoneInfo) = TimeZoneInfo.GetSystemTimeZones
    ```

2. `foreach` 루프(C#의 경우) 또는 `For Each…Next` 루프(Visual Basic의 경우)를 사용하여 컬렉션의 개별 [TimeZoneInfo](xref:System.TimeZoneInfo) 개체를 열거하고 각 개체에 필요한 처리를 수행합니다. 예를 들어 다음 코드에서는 1단계에서 반환된 [TimeZoneInfo](xref:System.TimeZoneInfo) 개체의 [ReadOnlyCollection&lt;T&gt;](xref:System.Collections.ObjectModel.ReadOnlyCollection%601) 컬렉션을 열거하고 각 표준 시간대의 표시 이름을 콘솔에 나열합니다.

    ```csharp
    foreach (TimeZoneInfo timeZone in tzCollection)
    Console.WriteLine("   {0}: {1}", timeZone.Id, timeZone.DisplayName);
    ```

    ```vb
    For Each timeZone As TimeZoneInfo In tzCollection
        Console.WriteLine("   {0}: {1}", timeZone.Id, timeZone.DisplayName)
    Next
    ```

## <a name="see-also"></a>참고 항목

[날짜, 시간 및 표준 시간대](index.md)

[로컬 시스템에 정의된 표준 시간대 찾기](finding-the-time-zones-on-local-system.md)




<!--HONumber=Nov16_HO3-->


