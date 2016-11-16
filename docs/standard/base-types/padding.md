---
title: "문자열 채우기"
description: "문자열 채우기"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 07/26/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 1c8b3b44-d370-49e1-90b5-64ac81c02ae91c8b3b44-d370-49e1-90b5-64ac81c02ae9
translationtype: Human Translation
ms.sourcegitcommit: fb00da6505c9edb6a49d2003ae9bcb8e74c11d6c
ms.openlocfilehash: 73452c1f432fb8cb50a36e739fb91962693f8e51

---

# <a name="padding-strings"></a>문자열 채우기

다음 [System.String](xref:System.String) 메서드 중 하나를 사용하여 선행 또는 후행 문자로 지정된 총 길이를 채운 원래 문자열에 구성된 새 문자열을 만듭니다. 채움 문자는 공백이나 지정된 문자를 사용할 수 있고 결과적으로 오른쪽에 맞춤인지 아니면 왼쪽 맞춤인지를 표시합니다.

메서드 이름 | 기능
----------- | ---
[String.PadLeft](xref:System.String.PadLeft(System.Int32)) | 선행 문자로 문자열의 지정한 총 길이를 채웁니다.
[String.PadRight](xref:System.String.PadRight(System.Int32)) | 후행 문자로 문자열의 지정한 총 길이를 채웁니다.

## <a name="padleft"></a>PadLeft

[String.PadLeft](xref:System.String.PadLeft(System.Int32)) 메서드는 지정된 총 길이에 맞도록 필요한 만큼 원래 문자열에 선행 채움 문자를 연결하여 새 문자열을 만듭니다. [String.PadLeft(Int32)](xref:System.String.PadLeft(System.Int32)) 메서드는 공백을 채움 문자로 사용하며 [String.PadLeft (Int32, Char)](xref:System.String.PadLeft(System.Int32,System.Char)) 메서드를 사용하면 고유한 채움 문자를 지정할 수 있습니다.

다음 코드 예제에서는 [PadLeft(Int32, Char)](xref:System.String.PadLeft(System.Int32,System.Char)) 메서드를 사용하여 20자 길이의 새 문자열을 만듭니다. 이 예제에서는 콘솔에 "`--------Hello World!`"를 표시합니다.

```csharp
string MyString = "Hello World!";
Console.WriteLine(MyString.PadLeft(20, '-'));
```

```vb
Dim MyString As String = "Hello World!"
Console.WriteLine(MyString.PadLeft(20, "-"c))
```

## <a name="padright"></a>PadRight

[String.PadRight](xref:System.String.PadRight(System.Int32)) 메서드는 지정된 총 길이에 맞도록 필요한 만큼 원래 문자열에 후행 채움 문자를 연결하여 새 문자열을 만듭니다. [String.PadRight(Int32)](xref:System.String.PadRight(System.Int32)) 메서드는 공백을 채움 문자로 사용하며 [String.PadRight (Int32, Char)](xref:System.String.PadRight(System.Int32,System.Char)) 메서드를 사용하면 고유한 채움 문자를 지정할 수 있습니다.

다음 코드 예제에서는 [PadRight(Int32, Char)](xref:System.String.PadRight(System.Int32,System.Char)) 메서드를 사용하여 20자 길이의 새 문자열을 만듭니다. 이 예제에서는 콘솔에 "`Hello World!--------`"를 표시합니다.

```csharp
string MyString = "Hello World!";
Console.WriteLine(MyString.PadRight(20, '-'));
```

```vb
Dim MyString As String = "Hello World!"
Console.WriteLine(MyString.PadRight(20, "-"c))
```

## <a name="see-also"></a>참고 항목

[기본적인 문자열 작업](basic-string-operations.md)




<!--HONumber=Nov16_HO1-->


