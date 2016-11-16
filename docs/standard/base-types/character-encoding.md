---
title: ".NET의 문자 인코딩"
description: ".NET의 문자 인코딩"
keywords: .NET, .NET Core
author: stevehoag
ms.author: shoag
manager: wpickett
ms.date: 07/26/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: bce54e41-e9dc-493a-8988-1cbadc340fe8
translationtype: Human Translation
ms.sourcegitcommit: b20713600d7c3ddc31be5885733a1e8910ede8c6
ms.openlocfilehash: 6080f5fa12a2391dd138828e0afc2219f1e3a11b

---

# <a name="character-encoding-in-net"></a>.NET의 문자 인코딩

문자는 다양한 방법으로 표현할 수 있는 추상 엔터티입니다. 문자 인코딩은 지원되는 문자 집합의 각 문자와 해당 문자를 나타내는 일부 값의 쌍을 만드는 시스템입니다. 예를 들어 모르스 부호는 로마 알파벳의 각 문자와 전화선을 통한 전송에 적합한 점과 대시 패턴의 쌍을 만드는 문자 인코딩입니다. 컴퓨터의 문자 인코딩은 지원되는 문자 집합의 각 문자와 해당 문자를 나타내는 숫자 값의 쌍을 만듭니다. 문자 인코딩에는 다음 두 가지 구성 요소가 있습니다.

* 인코더 - 문자 시퀀스를 숫자 값(바이트) 시퀀스로 변환합니다.

* 디코더 - 바이트 시퀀스를 문자 시퀀스로 변환합니다.

문자 인코딩은 인코더와 디코더가 작동하는 규칙을 설명합니다. 예를 들어 [UTF8Encoding](xref:System.Text.UTF8Encoding) 클래스는 1~4바이트를 사용하여 단일 유니코드 문자를 나타내는 UTF-8(8비트 유니코드 변환 형식)로 인코드 및 디코드하는 규칙을 설명합니다. 인코딩 및 디코딩에 유효성 검사가 포함될 수도 있습니다. 예를 들어 [UnicodeEncoding](xref:System.Text.UnicodeEncoding) 클래스는 모든 서로게이트를 검사하여 유효한 서로게이트 쌍을 구성하는지 확인합니다. 서로게이트 쌍은 코드 포인트가 U+D800에서 U+DBFF 사이의 범위인 문자와 코드 포인트가 U+DC00에서 U+DFFF 사이의 범위인 문자가 이 순서로 결합되어 구성됩니다. 대체(fallback) 전략은 인코더에서 잘못된 문자를 처리하는 방법 또는 디코더에서 잘못된 바이트를 처리하는 방법을 결정합니다. 

> [!WARNING]
> .NET 인코딩 클래스는 문자 데이터를 저장 및 변환하는 방법을 제공합니다. 이진 데이터를 문자열 형식으로 저장하는 데 사용하면 안 됩니다. 사용되는 인코딩에 따라 인코딩 클래스를 통해 이진 데이터를 문자열로 변환할 때 예기치 못한 동작이 발생하고 부정확하거나 손상된 데이터가 생성될 수 있습니다. 이진 데이터를 문자열 형식으로 변환하려면 [Convert.ToBase64String](xref:System.Convert.ToBase64String(System.Byte[])) 메서드를 사용합니다. 
 
.NET에서는 UTF-16 인코딩([UnicodeEncoding](xref:System.Text.UnicodeEncoding) 클래스로 표시)을 사용하여 문자와 문자열을 나타냅니다. 공용 언어 런타임을 대상으로 하는 응용 프로그램은 인코더를 사용하여 공용 언어 런타임에서 지원하는 유니코드 문자 표현을 다른 인코딩 체계에 매핑합니다. 디코더를 사용하여 문자를 비유니코드 인코딩에서 유니코드로 매핑합니다.

이 항목은 다음 섹션으로 구성되어 있습니다.

* [.NET의 인코딩](#encodings-in-net)

* [인코딩 클래스 선택](#selecting-an-encoding-class)

* [인코딩 개체 사용](#using-an-encoding-object)

* [대체(fallback) 전략 선택](#choosing-a-fallback-strategy)

* [사용자 지정 대체(fallback) 전략 구현](#implementing-a-custom-fallback-strategy)

## <a name="encodings-in-net"></a>.NET의 인코딩

.NET의 모든 문자 인코딩 클래스는 모든 문자 인코딩에 공통된 기능을 정의하는 추상 클래스인 [System.Text.Encoding](xref:System.Text.Encoding) 클래스에서 상속받습니다. .NET에서 구현된 개별 인코딩 개체에 액세스하려면 다음을 수행합니다.

* .NET에서 사용할 수 있는 표준 문자 인코딩(ASCII, UTF-7, UTF-8, UTF-16 및 UTF-32)을 나타내는 개체를 반환하는 [Encoding](xref:System.Text.Encoding) 클래스의 정적 속성을 사용합니다. 예를 들어 [Encoding.Unicode](xref:System.Text.Encoding.Unicode) 속성은 [UnicodeEncoding](xref:System.Text.UnicodeEncoding) 개체를 반환합니다. 각 개체는 교체 대체(fallback)를 사용하여 인코딩할 수 없는 문자열과 디코딩할 수 없는 바이트를 처리합니다. (자세한 내용은 [교체 대체(fallback)](#replacement-fallback) 섹션을 참조하세요.)

* 인코딩의 클래스 생성자를 호출합니다. 이런 방식으로 ASCII, UTF-7, UTF-8, UTF-16 및 UTF-32 인코딩에 대한 개체를 인스턴스화할 수 있습니다. 기본적으로 각 개체는 교체 대체(fallback)를 사용하여 인코딩할 수 없는 문자열과 디코딩할 수 없는 바이트를 처리하지만 대신 예외가 발생하도록 지정할 수 있습니다. (자세한 내용은 [교체 대체(fallback)](#replacement-fallback) 및 [예외 대체(fallback)](#exception-fallback) 섹션을 참조하세요.)

* [Encoding.Encoding(Int32)](xref:System.Text.Encoding.GetEncoding(System.Int32)) 생성자를 호출하고 인코딩을 나타내는 정수를 전달합니다. 표준 인코딩 개체는 교체 대체(fallback)를 사용하고, 코드 페이지와 DBCS(더블바이트 문자 집합) 인코딩 개체는 최적 맞춤 대체(fallback)를 사용하여 인코딩할 수 없는 문자열과 디코딩할 수 없는 바이트를 처리합니다. (자세한 내용은 [자동 맞춤 대체(fallback)](#best-fit-fallback) 섹션을 참조하세요.)

* .NET에서 사용할 수 있는 표준, 코드 페이지 또는 DBCS 인코딩을 반환하는 [Encoding.GetEncoding](xref:System.Text.Encoding.GetEncoding(System.Int32)) 메서드를 호출합니다. 오버로드를 통해 인코더와 디코더 둘 다에 대체(fallback) 개체를 지정할 수 있습니다.

> [!NOTE]
> 유니코드 표준은 지원되는 모든 스크립트의 각 문자에 코드 포인트(숫자)와 이름을 할당합니다. 예를 들어 "A" 문자는 코드 포인트 U+0041 및 이름 "LATIN CAPITAL LETTER A"로 표시됩니다. UTF(유니코드 변환 형식) 인코딩은 코드 포인트를 하나 이상의 바이트 시퀀스로 인코딩하는 방법을 정의합니다. 유니코드 인코딩 체계를 사용하면 모든 문자 집합의 문자를 단일 인코딩으로 나타낼 수 있으므로 국제 응용 프로그램 개발이 간소화됩니다. 응용 프로그램 개발자가 더 이상 특정 언어나 쓰기 시스템을 위한 문자를 생성하는 데 사용된 인코딩 체계를 추적할 필요가 없으며 손상 없이 전 세계 시스템 간에 데이터를 공유할 수 있습니다.
>
>  .NET에서는 유니코드 표준에서 정의된 세 가지 인코딩인 UTF-8, UTF-16 및 UTF-32를 지원합니다. 자세한 내용은 [유니코드](http://www.unicode.org/) 홈페이지에서 유니코드 표준을 참조하세요.
 
.NET에서는 다음 표에 나열된 문자 인코딩 시스템을 지원합니다.

인코딩 | 클래스 | 설명 | 장점/단점
-------- | ----- | ----------- | ------------------------ 
ASCII | [ASCIIEncoding](xref:System.Text.ASCIIEncoding) | 바이트의 하위 7비트를 사용하여 제한된 범위의 문자를 인코딩합니다. | 이 인코딩은 U+0000에서 U+007F 사이의 문자 값만 지원하므로 대부분의 경우 국제화된 응용 프로그램에는 적합하지 않습니다.
UTF-7 | [UTF7Encoding](xref:System.Text.UTF7Encoding) | 7비트 ASCII 문자 시퀀스로 문자를 나타냅니다. 비 ASCII 유니코드 문자는 ASCII 문자의 이스케이프 시퀀스로 표시됩니다. | UTF-7은 전자 메일 및 뉴스 그룹 프로토콜과 같은 프로토콜을 지원합니다. 그러나 UTF-7은 특별히 안전하거나 강력하지 않습니다. 경우에 따라 1비트를 변경해도 전체 UTF-7 문자열의 해석이 완전히 바뀔 수 있습니다. 다른 UTF-7 문자열이 동일한 텍스트를 인코딩할 수도 있습니다. 비 ASCII 문자를 포함하는 시퀀스의 경우 UTF-7에서 UTF-8보다 많은 공간이 필요하며 인코딩/디코딩 속도가 느려집니다. 따라서 가능하면 UTF-7 대신 UTF-8을 사용해야 합니다.
UTF-8 | [UTF8Encoding](xref:System.Text.UTF8Encoding) | 각 유니코드 코드 포인트를 1-4바이트의 시퀀스로 나타냅니다. | UTF-8은 8비트 데이터 크기를 지원하며 기존의 많은 운영 체제에서 제대로 작동합니다. ASCII 문자 범위의 경우 UTF-8은 ASCII 인코딩과 동일하며 광범위한 문자 집합을 허용합니다. 그러나 CJK(중국어-일본어-한국어) 스크립트의 경우 UTF-8에서 각 문자에 대해 3바이트를 요구할 수 있으며 데이터 크기가 UTF-16보다 커질 수 있습니다. 때로는 HTML 태그와 같은 ASCII 데이터의 양이 CJK 범위의 크기 증가와 관련이 있습니다.
UTF-16 | [UnicodeEncoding](xref:System.Text.UnicodeEncoding) | 각 유니코드 코드 포인트를 한두 개의 16비트 정수 시퀀스로 나타냅니다. 가장 일반적인 유니코드 문자에는 UTF-16 코드 포인트 한 개만 있으면 됩니다. 단, 유니코드 보조 문자(U+10000 이상)에는 UTF-16 서로게이트 코드 포인트 두 개가 필요합니다. little-endian 및 big-endian 바이트 순서가 둘 다 지원됩니다. | UTF-16 인코딩은 공용 언어 런타임에서 Char 및 String 값을 나타내는 데 사용되며 Windows 운영 체제에서 WCHAR 값을 나타내는 데 사용됩니다.
UTF-32 | [UTF32Encoding](xref:System.Text.UTF32Encoding) | 각 유니코드 코드 포인트를 32비트 정수 한 개로 나타냅니다. little-endian 및 big-endian 바이트 순서가 둘 다 지원됩니다. | UTF-32 인코딩은 인코딩된 공간이 너무 중요한 운영 체제에서 응용 프로그램이 UTF-16 인코딩의 서로게이트 코드 포인트 동작을 방지하려는 경우에 사용됩니다. 디스플레이에 렌더링되는 단일 문자 모양은 여전히 둘 이상의 UTF-32 문자로 인코딩될 수 있습니다.

이러한 인코딩을 통해 유니코드 문자는 물론 레거시 응용 프로그램에서 가장 일반적으로 사용되는 인코딩으로 작업할 수 있습니다. 또한 [Encoding](xref:System.Text.Encoding)에서 파생되는 클래스를 정의하고 해당 멤버를 재정의하여 사용자 지정 인코딩을 만들 수 있습니다.

> [!NOTE]
> 기본적으로 .NET Core에서는 코드 페이지 28591 이외의 코드 페이지 인코딩 및 유니코드 인코딩(예: UTF-8 및 UTF-16)을 사용할 수 없습니다. 그러나 .NET Framework를 대상으로 하는 표준 Windows 앱에 있는 코드 페이지 인코딩을 해당 앱에 추가할 수 있습니다. 자세한 내용은 [EncodingProvider](xref:System.Text.EncodingProvider) 항목을 참조하세요. 

## <a name="selecting-an-encoding-class"></a>인코딩 클래스 선택

응용 프로그램에서 사용할 인코딩을 선택할 수 있는 경우 유니코드 인코딩인 [UTF8Encoding](xref:System.Text.UTF8Encoding) 또는 [UnicodeEncoding](xref:System.Text.UnicodeEncoding)을 사용해야 합니다. .NET에서는 세 번째 유니코드 인코딩인 [UTF32Encoding](xref:System.Text.UTF32Encoding)도 지원합니다. 

ASCII 인코딩([ASCIIEncoding](xref:System.Text.ASCIIEncoding))을 사용하려는 경우 [UTF8Encoding](xref:System.Text.UTF8Encoding)을 대신 선택합니다. ASCII 문자 집합의 경우 두 인코딩이 동일하지만 [UTF8Encoding](xref:System.Text.UTF8Encoding)에는 다음과 같은 이점이 있습니다. 

* [ASCIIEncoding](xref:System.Text.ASCIIEncoding)은 U+0000에서 U+007F 사이의 유니코드 문자 값만 지원하는 반면, 모든 유니코드 문자를 나타낼 수 있습니다.

* 오류 검색 및 향상된 보안을 제공합니다.

* 최대한 빠르도록 조정되었으며 다른 인코딩보다 더 빨라야 합니다. 완전히 ASCII인 콘텐츠의 경우에도 [UTF8Encoding](xref:System.Text.UTF8Encoding)으로 수행된 작업이 [ASCIIEncoding](xref:System.Text.ASCIIEncoding)으로 수행된 작업보다 더 빠릅니다.

[ASCIIEncoding](xref:System.Text.ASCIIEncoding)은 레거시 응용 프로그램에만 사용해야 합니다. 그러나 레거시 응용 프로그램의 경우에도 다음과 같은 이유로 [UTF8Encoding](xref:System.Text.UTF8Encoding)이 더 적합할 수 있습니다(기본 설정 가정).

* 응용 프로그램에 엄격하게 ASCII가 아닌 콘텐츠가 있고 [ASCIIEncoding](xref:System.Text.ASCIIEncoding)으로 인코드하는 경우 각각의 비 ASCII 문자가 물음표(?)로 인코드됩니다. 그런 후에 응용 프로그램에서 이 데이터를 디코딩하면 정보가 손실됩니다.


* 응용 프로그램에 엄격하게 ASCII가 아닌 콘텐츠가 있고 [UTF8Encoding](xref:System.Text.UTF8Encoding)으로 인코드하는 경우 결과를 ASCII로 해석하면 인식할 수 없는 것처럼 보입니다. 그러나 응용 프로그램이 UTF-8 디코더를 사용하여 이 데이터를 디코딩하면 데이터가 성공적으로 라운드트립을 수행합니다.

웹 응용 프로그램에서는 웹 요청에 대한 응답으로 클라이언트에 전송되는 문자가 클라이언트에서 사용되는 인코딩을 반영해야 합니다. 대부분의 경우 사용자에게 필요한 인코딩으로 텍스트를 표시하기 위해 [HttpResponse.ContentEncoding](xref:System.Net.HttpResponseHeader.ContentEncoding) 속성을 [HttpRequestHeader.ContentEncoding](xref:System.Net.HttpRequestHeader.ContentEncoding) 속성에서 반환된 값으로 설정해야 합니다.

## <a name="using-an-encoding-object"></a>인코딩 개체 사용

인코더는 문자의 문자열(가장 일반적으로 유니코드 문자)을 해당 숫자(바이트)로 변환합니다. 예를 들어 콘솔에 표시될 수 있도록 ASCII 인코더를 사용하여 유니코드 문자를 ASCII로 변환할 수 있습니다. 변환을 수행하려면 [Encoding.GetBytes](xref:System.Text.Encoding.GetBytes(System.Char[])) 메서드를 호출합니다. 인코딩을 수행하기 전에 인코딩된 문자를 저장하는 데 필요한 바이트 수를 확인하려는 경우 [GetByteCount](xref:System.Text.Encoding.GetByteCount(System.Char[])) 메서드를 호출할 수 있습니다.

다음 예제에서는 싱글바이트 배열을 사용하여 두 개의 별도 작업에서 문자열을 인코딩합니다. 바이트 배열에서 ASCII로 인코딩된 다음 바이트 집합의 시작 위치를 나타내는 인덱스를 유지 관리합니다. [ASCIIEncoding.GetByteCount(String)](xref:System.Text.ASCIIEncoding.GetByteCount(System.String)) 메서드를 호출하여 바이트 배열이 인코드된 문자열을 포함하기에 충분히 큰지 확인합니다. 그런 다음 [ASCIIEncoding.GetBytes(String, Int32, Int32, Byte[], Int32)](xref:System.Text.ASCIIEncoding.GetBytes(System.Char[],System.Int32,System.Int32,System.Byte[],System.Int32)) 메서드를 호출하여 문자열의 문자를 인코드합니다.

```csharp
using System;
using System.Text;

public class Example
{
   public static void Main()
   {
      string[] strings= { "This is the first sentence. ", 
                          "This is the second sentence. " };
      Encoding asciiEncoding = Encoding.ASCII;

      // Create array of adequate size.
      byte[] bytes = new byte[49];
      // Create index for current position of array.
      int index = 0;

      Console.WriteLine("Strings to encode:");
      foreach (var stringValue in strings) {
         Console.WriteLine("   {0}", stringValue);

         int count = asciiEncoding.GetByteCount(stringValue);
         if (count + index >=  bytes.Length)
            Array.Resize(ref bytes, bytes.Length + 50);

         int written = asciiEncoding.GetBytes(stringValue, 0, 
                                              stringValue.Length, 
                                              bytes, index);    

         index = index + written; 
      } 
      Console.WriteLine("\nEncoded bytes:");
      Console.WriteLine("{0}", ShowByteValues(bytes, index));
      Console.WriteLine();

      // Decode Unicode byte array to a string.
      string newString = asciiEncoding.GetString(bytes, 0, index);
      Console.WriteLine("Decoded: {0}", newString);
   }

   private static string ShowByteValues(byte[] bytes, int last ) 
   {
      string returnString = "   ";
      for (int ctr = 0; ctr <= last - 1; ctr++) {
         if (ctr % 20 == 0)
            returnString += "\n   ";
         returnString += String.Format("{0:X2} ", bytes[ctr]);
      }
      return returnString;
   }
}
// The example displays the following output:
//       Strings to encode:
//          This is the first sentence.
//          This is the second sentence.
//       
//       Encoded bytes:
//       
//          54 68 69 73 20 69 73 20 74 68 65 20 66 69 72 73 74 20 73 65
//          6E 74 65 6E 63 65 2E 20 54 68 69 73 20 69 73 20 74 68 65 20
//          73 65 63 6F 6E 64 20 73 65 6E 74 65 6E 63 65 2E 20
//       
//       Decoded: This is the first sentence. This is the second sentence.
```

```vb
Imports System.Text

Module Example
   Public Sub Main()
      Dim strings() As String = { "This is the first sentence. ", 
                                  "This is the second sentence. " }
      Dim asciiEncoding As Encoding = Encoding.ASCII

      ' Create array of adequate size.
      Dim bytes(50) As Byte
      ' Create index for current position of array.
      Dim index As Integer = 0

      Console.WriteLine("Strings to encode:")
      For Each stringValue In strings
         Console.WriteLine("   {0}", stringValue)

         Dim count As Integer = asciiEncoding.GetByteCount(stringValue)
         If count + index >=  bytes.Length Then
            Array.Resize(bytes, bytes.Length + 50)
         End If
         Dim written As Integer = asciiEncoding.GetBytes(stringValue, 0, 
                                                         stringValue.Length, 
                                                         bytes, index)    

         index = index + written 
      Next 
      Console.WriteLine()
      Console.WriteLine("Encoded bytes:")
      Console.WriteLine("{0}", ShowByteValues(bytes, index))
      Console.WriteLine()

      ' Decode Unicode byte array to a string.
      Dim newString As String = asciiEncoding.GetString(bytes, 0, index)
      Console.WriteLine("Decoded: {0}", newString)
   End Sub

   Private Function ShowByteValues(bytes As Byte(), last As Integer) As String
      Dim returnString As String = "   "
      For ctr As Integer = 0 To last - 1
         If ctr Mod 20 = 0 Then returnString += vbCrLf + "   "
         returnString += String.Format("{0:X2} ", bytes(ctr))
      Next
      Return returnString
   End Function
End Module
' The example displays the following output:
'       Strings to encode:
'          This is the first sentence.
'          This is the second sentence.
'       
'       Encoded bytes:
'       
'          54 68 69 73 20 69 73 20 74 68 65 20 66 69 72 73 74 20 73 65
'          6E 74 65 6E 63 65 2E 20 54 68 69 73 20 69 73 20 74 68 65 20
'          73 65 63 6F 6E 64 20 73 65 6E 74 65 6E 63 65 2E 20
'       
'       Decoded: This is the first sentence. This is the second sentence.
```

디코더는 특정 문자 인코딩을 반영하는 바이트 배열을 문자 배열 또는 문자열의 문자 집합으로 변환합니다. 바이트 배열을 문자 배열로 디코드하려면 [Encoding.GetChars](xref:System.Text.Encoding.GetChars(System.Byte[])) 메서드를 호출합니다. 바이트 배열을 문자열로 디코드하려면 [GetString](xref:System.Text.Encoding.GetString(System.Byte[])) 메서드를 호출합니다. 디코딩을 수행하기 전에 디코드된 바이트를 저장하는 데 필요한 문자 수를 확인하려는 경우 [GetCharCount](xref:System.Text.Encoding.GetCharCount(System.Byte[])) 메서드를 호출할 수 있습니다.

다음 예제에서는 세 개의 문자열을 인코딩한 후 단일 문자 배열로 디코딩합니다. 문자 배열에서 디코딩된 다음 문자 집합의 시작 위치를 나타내는 인덱스를 유지 관리합니다. [GetCharCount](xref:System.Text.Encoding.GetCharCount(System.Byte[])) 메서드를 호출하여 문자 배열이 디코드된 모든 문자를 포함하기에 충분히 큰지 확인합니다. 그런 다음 [ASCIIEncoding.GetChars(Byte[], Int32, Int32, Char[], Int32)](xref:System.Text.ASCIIEncoding.GetChars(System.Byte[],System.Int32,System.Int32,System.Char[],System.Int32)) 메서드를 호출하여 바이트 배열을 디코드합니다.

```csharp
using System;
using System.Text;

public class Example
{
   public static void Main()
   {
      string[] strings = { "This is the first sentence. ", 
                           "This is the second sentence. ",
                           "This is the third sentence. " };
      Encoding asciiEncoding = Encoding.ASCII;
      // Array to hold encoded bytes.
      byte[] bytes;
      // Array to hold decoded characters.
      char[] chars = new char[50];
      // Create index for current position of character array.
      int index = 0;     

      foreach (var stringValue in strings) {
         Console.WriteLine("String to Encode: {0}", stringValue);
         // Encode the string to a byte array.
         bytes = asciiEncoding.GetBytes(stringValue);
         // Display the encoded bytes.
         Console.Write("Encoded bytes: ");
         for (int ctr = 0; ctr < bytes.Length; ctr++)
            Console.Write(" {0}{1:X2}", 
                          ctr % 20 == 0 ? Environment.NewLine : "", 
                          bytes[ctr]);
         Console.WriteLine();

         // Decode the bytes to a single character array.
         int count = asciiEncoding.GetCharCount(bytes);
         if (count + index >=  chars.Length)
            Array.Resize(ref chars, chars.Length + 50);

         int written = asciiEncoding.GetChars(bytes, 0, 
                                              bytes.Length, 
                                              chars, index);              
         index = index + written;
         Console.WriteLine();       
      }

      // Instantiate a single string containing the characters.
      string decodedString = new string(chars, 0, index - 1);
      Console.WriteLine("Decoded string: ");
      Console.WriteLine(decodedString);
   }
}
// The example displays the following output:
//    String to Encode: This is the first sentence.
//    Encoded bytes:
//    54 68 69 73 20 69 73 20 74 68 65 20 66 69 72 73 74 20 73 65
//    6E 74 65 6E 63 65 2E 20
//    
//    String to Encode: This is the second sentence.
//    Encoded bytes:
//    54 68 69 73 20 69 73 20 74 68 65 20 73 65 63 6F 6E 64 20 73
//    65 6E 74 65 6E 63 65 2E 20
//    
//    String to Encode: This is the third sentence.
//    Encoded bytes:
//    54 68 69 73 20 69 73 20 74 68 65 20 74 68 69 72 64 20 73 65
//    6E 74 65 6E 63 65 2E 20
//    
//    Decoded string:
//    This is the first sentence. This is the second sentence. This is the third sentence.
```

```vb
Imports System.Text

Module Example
   Public Sub Main()
      Dim strings() As String = { "This is the first sentence. ", 
                                  "This is the second sentence. ",
                                  "This is the third sentence. " }
      Dim asciiEncoding As Encoding = Encoding.ASCII
      ' Array to hold encoded bytes.
      Dim bytes() As Byte
      ' Array to hold decoded characters.
      Dim chars(50) As Char
      ' Create index for current position of character array.
      Dim index As Integer     

      For Each stringValue In strings
         Console.WriteLine("String to Encode: {0}", stringValue)
         ' Encode the string to a byte array.
         bytes = asciiEncoding.GetBytes(stringValue)
         ' Display the encoded bytes.
         Console.Write("Encoded bytes: ")
         For ctr As Integer = 0 To bytes.Length - 1
            Console.Write(" {0}{1:X2}", If(ctr Mod 20 = 0, vbCrLf, ""), 
                                        bytes(ctr))
         Next         
         Console.WriteLine()

         ' Decode the bytes to a single character array.
         Dim count As Integer = asciiEncoding.GetCharCount(bytes)
         If count + index >=  chars.Length Then
            Array.Resize(chars, chars.Length + 50)
         End If
         Dim written As Integer = asciiEncoding.GetChars(bytes, 0, 
                                                         bytes.Length, 
                                                         chars, index)              
         index = index + written
         Console.WriteLine()       
      Next

      ' Instantiate a single string containing the characters.
      Dim decodedString As New String(chars, 0, index - 1)
      Console.WriteLine("Decoded string: ")
      Console.WriteLine(decodedString)
   End Sub
End Module
' The example displays the following output:
'    String to Encode: This is the first sentence.
'    Encoded bytes:
'    54 68 69 73 20 69 73 20 74 68 65 20 66 69 72 73 74 20 73 65
'    6E 74 65 6E 63 65 2E 20
'    
'    String to Encode: This is the second sentence.
'    Encoded bytes:
'    54 68 69 73 20 69 73 20 74 68 65 20 73 65 63 6F 6E 64 20 73
'    65 6E 74 65 6E 63 65 2E 20
'    
'    String to Encode: This is the third sentence.
'    Encoded bytes:
'    54 68 69 73 20 69 73 20 74 68 65 20 74 68 69 72 64 20 73 65
'    6E 74 65 6E 63 65 2E 20
'    
'    Decoded string:
'    This is the first sentence. This is the second sentence. This is the third sentence.
```

[Encoding](xref:System.Text.Encoding)에서 파생된 클래스의 인코딩 및 디코딩 메서드는 전체 데이터 집합에서 작동하도록 설계되었습니다. 즉, 인코드 또는 디코드할 모든 데이터가 단일 메서드 호출에서 제공됩니다. 그러나 스트림으로 데이터가 제공되고, 인코딩 또는 디코딩할 데이터를 별도의 읽기 작업에서만 사용할 수 있는 경우도 있습니다. 이 경우 인코딩 또는 디코딩 작업이 이전 호출에서 저장된 상태를 기억해야 합니다. [Encoder](xref:System.Text.Encoder) 및 [Decoder](xref:System.Text.Decoder)에서 파생된 클래스의 메서드는 여러 메서드 호출에 걸쳐 있는 인코딩 및 디코딩 작업을 처리할 수 있습니다.

특정 인코딩에 대한 [Encoder](xref:System.Text.Encoder) 개체는 해당 인코딩의 [Encoding.GetEncoder](xref:System.Text.Encoding.GetEncoder) 속성에서 사용할 수 있습니다. 특정 인코딩에 대한 [Decoder](xref:System.Text.Decoder) 개체는 해당 인코딩의 [Encoding.GetDecoder](xref:System.Text.Encoding.GetDecoder) 속성에서 사용할 수 있습니다. 디코딩 작업의 경우 [Decoder](xref:System.Text.Decoder)에서 파생된 클래스에 [Decoder.GetChars](xref:System.Text.Decoder.GetChars(System.Byte[],System.Int32,System.Int32,System.Char[],System.Int32)) 메서드가 포함되어 있지만 [Encoding.GetString](xref:System.Text.Encoding.GetString(System.Byte[]))에 해당하는 메서드가 없습니다.

다음 예제에서는 유니코드 바이트 배열 디코드에 [Encoding.GetChars](xref:System.Text.Encoding.GetChars(System.Byte[])) 및 [Decoder.GetChars](xref:System.Text.Decoder.GetChars(System.Byte[],System.Int32,System.Int32,System.Char[],System.Int32)) 메서드를 사용할 경우의 차이점을 보여 줍니다. 이 예제에서는 일부 유니코드 문자를 포함하는 문자열을 파일로 인코딩한 다음 두 개의 디코딩 메서드를 사용하여 한 번에 10바이트씩 디코딩합니다. 10번째 및 11번째 바이트에서 서로게이트 쌍이 발생하기 때문에 별도 메서드 호출에서 디코딩됩니다. 출력에서 볼 수 있듯이 [Encoding.GetChars](xref:System.Text.Encoding.GetChars(System.Byte[])) 메서드는 바이트를 올바르게 디코드할 수 없으며, 대신 U+FFFD(REPLACEMENT CHARACTER)로 바꿉니다. 반면, [Decoder.GetChars](xref:System.Text.Decoder.GetChars(System.Byte[],System.Int32,System.Int32,System.Char[],System.Int32)) 메서드는 바이트 배열을 성공적으로 디코드하여 원래 문자열을 가져올 수 있습니다.

```csharp
using System;
using System.IO;
using System.Text;

public class Example
{
   public static void Main()
   {
      // Use default replacement fallback for invalid encoding.
      UnicodeEncoding enc = new UnicodeEncoding(true, false, false);

      // Define a string with various Unicode characters.
      string str1 = "AB YZ 19 \uD800\udc05 \u00e4"; 
      str1 += "Unicode characters. \u00a9 \u010C s \u0062\u0308"; 
      Console.WriteLine("Created original string...\n");

      // Convert string to byte array.                     
      byte[] bytes = enc.GetBytes(str1);

      FileStream fs = File.Create(@".\characters.bin");
      BinaryWriter bw = new BinaryWriter(fs);
      bw.Write(bytes);
      bw.Close();

      // Read bytes from file.
      FileStream fsIn = File.OpenRead(@".\characters.bin");
      BinaryReader br = new BinaryReader(fsIn);

      const int count = 10;            // Number of bytes to read at a time. 
      byte[] bytesRead = new byte[10]; // Buffer (byte array).
      int read;                        // Number of bytes actually read. 
      string str2 = String.Empty;      // Decoded string.

      // Try using Encoding object for all operations.
      do { 
         read = br.Read(bytesRead, 0, count);
         str2 += enc.GetString(bytesRead, 0, read); 
      } while (read == count);
      br.Close();
      Console.WriteLine("Decoded string using UnicodeEncoding.GetString()...");
      CompareForEquality(str1, str2);
      Console.WriteLine();

      // Use Decoder for all operations.
      fsIn = File.OpenRead(@".\characters.bin");
      br = new BinaryReader(fsIn);
      Decoder decoder = enc.GetDecoder();
      char[] chars = new char[50];
      int index = 0;                   // Next character to write in array.
      int written = 0;                 // Number of chars written to array.
      do { 
         read = br.Read(bytesRead, 0, count);
         if (index + decoder.GetCharCount(bytesRead, 0, read) - 1 >= chars.Length) 
            Array.Resize(ref chars, chars.Length + 50);

         written = decoder.GetChars(bytesRead, 0, read, chars, index);
         index += written;                          
      } while (read == count);
      br.Close();            
      // Instantiate a string with the decoded characters.
      string str3 = new String(chars, 0, index); 
      Console.WriteLine("Decoded string using UnicodeEncoding.Decoder.GetString()...");
      CompareForEquality(str1, str3); 
   }

   private static void CompareForEquality(string original, string decoded)
   {
      bool result = original.Equals(decoded);
      Console.WriteLine("original = decoded: {0}", 
                        original.Equals(decoded, StringComparison.Ordinal));
      if (! result) {
         Console.WriteLine("Code points in original string:");
         foreach (var ch in original)
            Console.Write("{0} ", Convert.ToUInt16(ch).ToString("X4"));
         Console.WriteLine();

         Console.WriteLine("Code points in decoded string:");
         foreach (var ch in decoded)
            Console.Write("{0} ", Convert.ToUInt16(ch).ToString("X4"));
         Console.WriteLine();
      }
   }
}
// The example displays the following output:
//    Created original string...
//    
//    Decoded string using UnicodeEncoding.GetString()...
//    original = decoded: False
//    Code points in original string:
//    0041 0042 0020 0059 005A 0020 0031 0039 0020 D800 DC05 0020 00E4 0055 006E 0069 0063 006F
//    0064 0065 0020 0063 0068 0061 0072 0061 0063 0074 0065 0072 0073 002E 0020 00A9 0020 010C
//    0020 0073 0020 0062 0308
//    Code points in decoded string:
//    0041 0042 0020 0059 005A 0020 0031 0039 0020 FFFD FFFD 0020 00E4 0055 006E 0069 0063 006F
//    0064 0065 0020 0063 0068 0061 0072 0061 0063 0074 0065 0072 0073 002E 0020 00A9 0020 010C
//    0020 0073 0020 0062 0308
//    
//    Decoded string using UnicodeEncoding.Decoder.GetString()...
//    original = decoded: True
```

```vb
Imports System.IO
Imports System.Text

Module Example
   Public Sub Main()
      ' Use default replacement fallback for invalid encoding.
      Dim enc As New UnicodeEncoding(True, False, False)

      ' Define a string with various Unicode characters.
      Dim str1 As String = String.Format("AB YZ 19 {0}{1} {2}", 
                                         ChrW(&hD800), ChrW(&hDC05), ChrW(&h00e4))
      str1 += String.Format("Unicode characters. {0} {1} s {2}{3}", 
                            ChrW(&h00a9), ChrW(&h010C), ChrW(&h0062), ChrW(&h0308))
      Console.WriteLine("Created original string...")
      Console.WriteLine()

      ' Convert string to byte array.                     
      Dim bytes() As Byte = enc.GetBytes(str1)

      Dim fs As FileStream = File.Create(".\characters.bin")
      Dim bw As New BinaryWriter(fs)
      bw.Write(bytes)
      bw.Close()

      ' Read bytes from file.
      Dim fsIn As FileStream = File.OpenRead(".\characters.bin")
      Dim br As New BinaryReader(fsIn)

      Const count As Integer = 10      ' Number of bytes to read at a time. 
      Dim bytesRead(9) As Byte         ' Buffer (byte array).
      Dim read As Integer              ' Number of bytes actually read. 
      Dim str2 As String = ""          ' Decoded string.

      ' Try using Encoding object for all operations.
      Do 
         read = br.Read(bytesRead, 0, count)
         str2 += enc.GetString(bytesRead, 0, read) 
      Loop While read = count
      br.Close()
      Console.WriteLine("Decoded string using UnicodeEncoding.GetString()...")
      CompareForEquality(str1, str2)
      Console.WriteLine()

      ' Use Decoder for all operations.
      fsIn = File.OpenRead(".\characters.bin")
      br = New BinaryReader(fsIn)
      Dim decoder As Decoder = enc.GetDecoder()
      Dim chars(50) As Char
      Dim index As Integer = 0         ' Next character to write in array.
      Dim written As Integer = 0       ' Number of chars written to array.
      Do 
         read = br.Read(bytesRead, 0, count)
         If index + decoder.GetCharCount(bytesRead, 0, read) - 1 >= chars.Length Then 
            Array.Resize(chars, chars.Length + 50)
         End If   
         written = decoder.GetChars(bytesRead, 0, read, chars, index)
         index += written                          
      Loop While read = count
      br.Close()            
      ' Instantiate a string with the decoded characters.
      Dim str3 As New String(chars, 0, index) 
      Console.WriteLine("Decoded string using UnicodeEncoding.Decoder.GetString()...")
      CompareForEquality(str1, str3) 
   End Sub

   Private Sub CompareForEquality(original As String, decoded As String)
      Dim result As Boolean = original.Equals(decoded)
      Console.WriteLine("original = decoded: {0}", 
                        original.Equals(decoded, StringComparison.Ordinal))
      If Not result Then
         Console.WriteLine("Code points in original string:")
         For Each ch In original
            Console.Write("{0} ", Convert.ToUInt16(ch).ToString("X4"))
         Next
         Console.WriteLine()

         Console.WriteLine("Code points in decoded string:")
         For Each ch In decoded
            Console.Write("{0} ", Convert.ToUInt16(ch).ToString("X4"))
         Next
         Console.WriteLine()
      End If
   End Sub
End Module
' The example displays the following output:
'    Created original string...
'    
'    Decoded string using UnicodeEncoding.GetString()...
'    original = decoded: False
'    Code points in original string:
'    0041 0042 0020 0059 005A 0020 0031 0039 0020 D800 DC05 0020 00E4 0055 006E 0069 0063 006F
'    0064 0065 0020 0063 0068 0061 0072 0061 0063 0074 0065 0072 0073 002E 0020 00A9 0020 010C
'    0020 0073 0020 0062 0308
'    Code points in decoded string:
'    0041 0042 0020 0059 005A 0020 0031 0039 0020 FFFD FFFD 0020 00E4 0055 006E 0069 0063 006F
'    0064 0065 0020 0063 0068 0061 0072 0061 0063 0074 0065 0072 0073 002E 0020 00A9 0020 010C
'    0020 0073 0020 0062 0308
'    
'    Decoded string using UnicodeEncoding.Decoder.GetString()...
'    original = decoded: True
```

## <a name="choosing-a-fallback-strategy"></a>대체(fallback) 전략 선택

메서드가 문자를 인코딩 또는 디코딩하려고 하는데 매핑이 없는 경우 실패한 매핑의 처리 방법을 결정하는 대체(fallback) 전략을 구현해야 합니다. 다음 세 가지 유형의 대체 (fallback) 전략이 있습니다. 

* 자동 맞춤 대체(fallback)

* 교체 대체(fallback)

* 예외 대체(fallback)

> [!IMPORTANT]
> 인코딩 작업의 가장 일반적인 문제는 유니코드 문자를 특정 코드 페이지 인코딩에 매핑할 수 없는 경우에 발생합니다. 디코딩 작업의 가장 일반적인 문제는 잘못된 바이트 시퀀스를 유효한 유니코드 문자로 변환할 수 없는 경우에 발생합니다. 이러한 이유로 특정 인코딩 개체에서 사용하는 대체(fallback) 전략을 알고 있어야 합니다. 가능하면 개체를 인스턴스화할 때 인코딩 개체에서 사용할 대체(fallback) 전략을 지정해야 합니다.
 
### <a name="bestfit-fallback"></a>최적 대체(fallback)

대상 인코딩에 문자와 정확히 일치하는 항목이 없을 경우 인코더가 유사한 문자에 매핑하려고 시도할 수 있습니다. 최적 대체(fallback)는 주로 디코딩 문제가 아니라 인코딩 문제입니다. 성공적으로 유니코드에 매핑할 수 없는 문자가 포함된 코드 페이지는 거의 없습니다. 최적 대체(fallback)는 [Encoding.GetEncoding(Int32)](xref:System.Text.Encoding.GetEncoding(System.Int32)) 및 [Encoding.GetEncoding(String)](xref:System.Text.Encoding.GetEncoding(System.String)) 오버로드를 통해 검색된 코드 페이지 및 더블바이트 문자 집합 인코딩의 기본값입니다.

> [!NOTE]
> 이론적으로 .NET에서 제공되는 유니코드 인코딩 클래스([UTF8Encoding](xref:System.Text.UTF8Encoding), [UnicodeEncoding](xref:System.Text.UnicodeEncoding) 및 [UTF32Encoding](xref:System.Text.UTF32Encoding))는 모든 문자 집합의 모든 문자를 지원하므로 최적 대체(fallback) 문제를 제거하는 데 사용할 수 있습니다. 
 

최적 전략은 코드 페이지마다 다르며 자세히 문서화되지 않습니다. 예를 들어 일부 코드 페이지에서는 전자 라틴 문자가 더 일반적인 반자 라틴 문자에 매핑됩니다. 다른 코드 페이지에서는 이 매핑이 수행되지 않습니다. 적극적인 최적 전략에서도 일부 인코딩의 일부 문자는 상상할 수 있는 최적 항목이 없습니다. 예를 들어 중국어 표의 문자에는 코드 페이지 1252에 적합한 매핑이 없습니다. 이 경우 대체 문자열이 사용됩니다. 기본적으로 이 문자열은 단일 QUESTION MARK(U+003F)입니다.

다음 예제에서는 코드 페이지 1252(서유럽 언어에 대한 Windows 코드 페이지)를 사용하여 최적 매핑과 해당 결점을 보여 줍니다. [Encoding.GetEncoding(Int32](xref:System.Text.Encoding.GetEncoding(System.Int32)) 메서드는 코드 페이지 1252에 대한 인코딩 개체를 검색하는 데 사용됩니다. 기본적으로 지원하지 않는 유니코드 문자에 대해 최적 매핑을 사용합니다. 이 예제에서는 CIRCLED LATIN CAPITAL LETTER S(U+24C8), SUPERSCRIPT FIVE(U+2075) 및 INFINITY(U+221E)라는 3개의 비 ASCII 문자가 공백으로 구분되어 포함된 문자열을 인스턴스화합니다. 예제의 출력에서 볼 수 있듯이, 문자열을 인코딩할 때 공백이 아닌 원래 문자 3자가 QUESTION MARK(U+003F), DIGIT FIVE(U+0035) 및 DIGIT EIGHT(U+0038)으로 대체됩니다. DIGIT EIGHT은 지원되지 않는 INFINITY 문자에 대한 특히 부적절한 대체이고, QUESTION MARK는 원래 문자에 사용할 수 있는 매핑이 없음을 나타냅니다.

```csharp
using System;
using System.Text;

public class Example
{
   public static void Main()
   {
      // Get an encoding for code page 1252 (Western Europe character set).
      Encoding cp1252 = Encoding.GetEncoding(1252);

      // Define and display a string.
      string str = "\u24c8 \u2075 \u221e";
      Console.WriteLine("Original string: " + str);
      Console.Write("Code points in string: ");
      foreach (var ch in str)
         Console.Write("{0} ", Convert.ToUInt16(ch).ToString("X4"));

      Console.WriteLine("\n");   

      // Encode a Unicode string.
      Byte[] bytes = cp1252.GetBytes(str);
      Console.Write("Encoded bytes: ");
      foreach (byte byt in bytes)
         Console.Write("{0:X2} ", byt);
      Console.WriteLine("\n");

      // Decode the string.
      string str2 = cp1252.GetString(bytes);
      Console.WriteLine("String round-tripped: {0}", str.Equals(str2));
      if (! str.Equals(str2)) {
         Console.WriteLine(str2);
         foreach (var ch in str2)
            Console.Write("{0} ", Convert.ToUInt16(ch).ToString("X4"));
      }
   }
}
// The example displays the following output:
//       Original string: Ⓢ ⁵ ∞
//       Code points in string: 24C8 0020 2075 0020 221E
//       
//       Encoded bytes: 3F 20 35 20 38
//       
//       String round-tripped: False
//       ? 5 8
//       003F 0020 0035 0020 0038
```

```vb
Imports System.Text

Module Example
   Public Sub Main()
      ' Get an encoding for code page 1252 (Western Europe character set).
      Dim cp1252 As Encoding = Encoding.GetEncoding(1252)

      ' Define and display a string.
      Dim str As String = String.Format("{0} {1} {2}", ChrW(&h24c8), ChrW(&H2075), ChrW(&h221E))
      Console.WriteLine("Original string: " + str)
      Console.Write("Code points in string: ")
      For Each ch In str
         Console.Write("{0} ", Convert.ToUInt16(ch).ToString("X4"))
      Next
      Console.WriteLine()   
      Console.WriteLine()

      ' Encode a Unicode string.
      Dim bytes() As Byte = cp1252.GetBytes(str)
      Console.Write("Encoded bytes: ")
      For Each byt In bytes
         Console.Write("{0:X2} ", byt)
      Next
      Console.WriteLine()
      Console.WriteLine()

      ' Decode the string.
      Dim str2 As String = cp1252.GetString(bytes)
      Console.WriteLine("String round-tripped: {0}", str.Equals(str2))
      If Not str.Equals(str2) Then
         Console.WriteLine(str2)
         For Each ch In str2
            Console.Write("{0} ", Convert.ToUInt16(ch).ToString("X4"))
         Next
      End If
   End Sub
End Module
' The example displays the following output:
'       Original string: Ⓢ ⁵ ∞
'       Code points in string: 24C8 0020 2075 0020 221E
'       
'       Encoded bytes: 3F 20 35 20 38
'       
'       String round-tripped: False
'       ? 5 8
'       003F 0020 0035 0020 0038
```

최적 매핑은 유니코드 데이터를 코드 페이지 데이터로 인코드하는 [Encoding](xref:System.Text.Encoding) 개체의 기본 동작이며, 이 동작을 사용하는 레거시 응용 프로그램이 있습니다. 그러나 대부분의 새 응용 프로그램은 보안상의 이유로 최적 동작을 피해야 합니다. 예를 들어 응용 프로그램에서 최적 인코딩을 통해 도메인 이름을 넣으면 안 됩니다.

> [!Note]
> 인코딩에 대한 사용자 지정 최적 대체(fallback) 매핑을 구현할 수도 있습니다. 자세한 내용은 [사용자 지정 대체(fallback) 전략 구현](#implementing-a-custom-fallback-strategy) 섹션을 참조하세요.
 
최적 대체(fallback)가 인코딩 개체에 대한 기본값인 경우 [Encoding.GetEncoding(Int32, EncoderFallback, DecoderFallback)](xref:System.Text.Encoding.GetEncoding(System.Int32,System.Text.EncoderFallback,System.Text.DecoderFallback)) 또는 [Encoding.GetEncoding(String, EncoderFallback, DecoderFallback)](xref:System.Text.Encoding.GetEncoding(System.String,System.Text.EncoderFallback,System.Text.DecoderFallback)) 오버로드를 호출하여 [Encoding](xref:System.Text.Encoding) 개체를 검색할 때 다른 대체(fallback) 전략을 선택할 수 있습니다. 다음 섹션에는 코드 페이지 1252로 매핑할 수 없는 각 문자를 별표(\*)로 대체하는 예제가 포함되어 있습니다.

```csharp
using System;
using System.Text;

public class Example
{
   public static void Main()
   {
      Encoding cp1252r = Encoding.GetEncoding(1252, 
                                  new EncoderReplacementFallback("*"),
                                  new DecoderReplacementFallback("*"));

      string str1 = "\u24C8 \u2075 \u221E";
      Console.WriteLine(str1);
      foreach (var ch in str1)
         Console.Write("{0} ", Convert.ToUInt16(ch).ToString("X4"));

      Console.WriteLine();

      byte[] bytes = cp1252r.GetBytes(str1);
      string str2 = cp1252r.GetString(bytes);
      Console.WriteLine("Round-trip: {0}", str1.Equals(str2));
      if (! str1.Equals(str2)) {
         Console.WriteLine(str2);
         foreach (var ch in str2)
            Console.Write("{0} ", Convert.ToUInt16(ch).ToString("X4"));

         Console.WriteLine();
      } 
   }
}
// The example displays the following output:
//       Ⓢ ⁵ ∞
//       24C8 0020 2075 0020 221E
//       Round-trip: False
//       * * *
//       002A 0020 002A 0020 002A
```

```vb
Imports System.Text

Module Example
   Public Sub Main()
      Dim cp1252r As Encoding = Encoding.GetEncoding(1252, 
                                         New EncoderReplacementFallback("*"),
                                         New DecoderReplacementFallback("*"))

      Dim str1 As String = String.Format("{0} {1} {2}", ChrW(&h24C8), ChrW(&h2075), ChrW(&h221E))
      Console.WriteLine(str1)
      For Each ch In str1
         Console.Write("{0} ", Convert.ToUInt16(ch).ToString("X4"))
      Next    
      Console.WriteLine()

      Dim bytes() As Byte = cp1252r.GetBytes(str1)
      Dim str2 As String = cp1252r.GetString(bytes)
      Console.WriteLine("Round-trip: {0}", str1.Equals(str2))
      If Not str1.Equals(str2) Then
         Console.WriteLine(str2)
         For Each ch In str2
            Console.Write("{0} ", Convert.ToUInt16(ch).ToString("X4"))
         Next    
         Console.WriteLine()
      End If 
   End Sub
End Module
' The example displays the following output:
'       Ⓢ ⁵ ∞
'       24C8 0020 2075 0020 221E
'       Round-trip: False
'       * * *
'       002A 0020 002A 0020 002A
```

### <a name="replacement-fallback"></a>교체 대체(fallback)

대상 구성표에 문자와 정확히 일치하는 항목이 없고 매핑할 수 있는 적절한 문자가 없는 경우 응용 프로그램에서 대체 문자 또는 문자열을 지정할 수 있습니다. 이는 유니코드 디코더의 기본 동작으로, 디코딩할 수 없는 모든 2바이트 시퀀스를 REPLACEMENT_CHARACTER(U+FFFD)로 대체합니다. [ASCIIEncoding](xref:System.Text.ASCIIEncoding) 클래스의 기본 동작이기도 하며, 인코드 또는 디코드할 수 없는 각 문자를 물음표로 대체합니다. 다음 예제에서는 이전 예제의 유니코드 문자열에 대한 문자 대체를 보여 줍니다. 출력에서 볼 수 있듯이, ASCII 바이트 값으로 디코딩할 수 없는 각 문자는 물음표의 ASCII 코드인 0x3F로 대체됩니다.

```csharp
using System;
using System.Text;

public class Example
{
   public static void Main()
   {
      Encoding enc = Encoding.ASCII;

      string str1 = "\u24C8 \u2075 \u221E";
      Console.WriteLine(str1);
      foreach (var ch in str1)
         Console.Write("{0} ", Convert.ToUInt16(ch).ToString("X4"));

      Console.WriteLine("\n");

      // Encode the original string using the ASCII encoder.
      byte[] bytes = enc.GetBytes(str1);
      Console.Write("Encoded bytes: ");
      foreach (var byt in bytes)
         Console.Write("{0:X2} ", byt);
      Console.WriteLine("\n");

      // Decode the ASCII bytes.
      string str2 = enc.GetString(bytes);
      Console.WriteLine("Round-trip: {0}", str1.Equals(str2));
      if (! str1.Equals(str2)) {
         Console.WriteLine(str2);
         foreach (var ch in str2)
            Console.Write("{0} ", Convert.ToUInt16(ch).ToString("X4"));

         Console.WriteLine();
      } 
   }
}
// The example displays the following output:
//       Ⓢ ⁵ ∞
//       24C8 0020 2075 0020 221E
//       
//       Encoded bytes: 3F 20 3F 20 3F
//       
//       Round-trip: False
//       ? ? ?
//       003F 0020 003F 0020 003F
```

```vb
Imports System.Text

Module Example
   Public Sub Main()
      Dim enc As Encoding = Encoding.Ascii

      Dim str1 As String = String.Format("{0} {1} {2}", ChrW(&h24C8), ChrW(&h2075), ChrW(&h221E))
      Console.WriteLine(str1)
      For Each ch In str1
         Console.Write("{0} ", Convert.ToUInt16(ch).ToString("X4"))
      Next    
      Console.WriteLine()
      Console.WriteLine() 

      ' Encode the original string using the ASCII encoder.
      Dim bytes() As Byte = enc.GetBytes(str1)
      Console.Write("Encoded bytes: ")
      For Each byt In bytes
         Console.Write("{0:X2} ", byt)
      Next
      Console.WriteLine()
      Console.WriteLine()

      ' Decode the ASCII bytes.
      Dim str2 As String = enc.GetString(bytes)
      Console.WriteLine("Round-trip: {0}", str1.Equals(str2))
      If Not str1.Equals(str2) Then
         Console.WriteLine(str2)
         For Each ch In str2
            Console.Write("{0} ", Convert.ToUInt16(ch).ToString("X4"))
         Next    
         Console.WriteLine()
      End If 
   End Sub
End Module
' The example displays the following output:
'       Ⓢ ⁵ ∞
'       24C8 0020 2075 0020 221E
'       
'       Encoded bytes: 3F 20 3F 20 3F
'       
'       Round-trip: False
'       ? ? ?
'       003F 0020 003F 0020 003F
```

.NET에는 문자가 인코딩 또는 디코딩 작업에서 정확히 매핑되지 않는 경우 대체 문자열로 바꾸는 [EncoderReplacementFallback](xref:System.Text.EncoderReplacementFallback) 및 [DecoderReplacementFallback](xref:System.Text.DecoderReplacementFallback) 클래스가 포함되어 있습니다. 기본적으로 이 대체 문자열은 물음표지만 클래스 생성자 오버로드를 호출하여 다른 문자열을 선택할 수 있습니다. 요구 사항은 아니지만 일반적으로 대체 문자열은 단일 문자입니다. 다음 예제에서는 별표(\*)를 사용하는 [EncoderReplacementFallback](xref:System.Text.EncoderReplacementFallback) 개체를 대체 문자열로 인스턴스화하여 코드 페이지 1252 인코더의 동작을 변경합니다.

```csharp
using System;
using System.Text;

public class Example
{
   public static void Main()
   {
      Encoding cp1252r = Encoding.GetEncoding(1252, 
                                  new EncoderReplacementFallback("*"),
                                  new DecoderReplacementFallback("*"));

      string str1 = "\u24C8 \u2075 \u221E";
      Console.WriteLine(str1);
      foreach (var ch in str1)
         Console.Write("{0} ", Convert.ToUInt16(ch).ToString("X4"));

      Console.WriteLine();

      byte[] bytes = cp1252r.GetBytes(str1);
      string str2 = cp1252r.GetString(bytes);
      Console.WriteLine("Round-trip: {0}", str1.Equals(str2));
      if (! str1.Equals(str2)) {
         Console.WriteLine(str2);
         foreach (var ch in str2)
            Console.Write("{0} ", Convert.ToUInt16(ch).ToString("X4"));

         Console.WriteLine();
      } 
   }
}
// The example displays the following output:
//       Ⓢ ⁵ ∞
//       24C8 0020 2075 0020 221E
//       Round-trip: False
//       * * *
//       002A 0020 002A 0020 002A
```

```vb
Imports System.Text

Module Example
   Public Sub Main()
      Dim cp1252r As Encoding = Encoding.GetEncoding(1252, 
                                         New EncoderReplacementFallback("*"),
                                         New DecoderReplacementFallback("*"))

      Dim str1 As String = String.Format("{0} {1} {2}", ChrW(&h24C8), ChrW(&h2075), ChrW(&h221E))
      Console.WriteLine(str1)
      For Each ch In str1
         Console.Write("{0} ", Convert.ToUInt16(ch).ToString("X4"))
      Next    
      Console.WriteLine()

      Dim bytes() As Byte = cp1252r.GetBytes(str1)
      Dim str2 As String = cp1252r.GetString(bytes)
      Console.WriteLine("Round-trip: {0}", str1.Equals(str2))
      If Not str1.Equals(str2) Then
         Console.WriteLine(str2)
         For Each ch In str2
            Console.Write("{0} ", Convert.ToUInt16(ch).ToString("X4"))
         Next    
         Console.WriteLine()
      End If 
   End Sub
End Module
' The example displays the following output:
'       Ⓢ ⁵ ∞
'       24C8 0020 2075 0020 221E
'       Round-trip: False
'       * * *
'       002A 0020 002A 0020 002A
```

> [!NOTE]
> 인코딩에 대한 대체 클래스를 구현할 수도 있습니다. 자세한 내용은 [사용자 지정 대체(fallback) 전략 구현](#implementing-a-custom-fallback-strategy) 섹션을 참조하세요.
 
QUESTION MARK(U+003F) 외에도 일반적으로 유니코드 REPLACEMENT CHARACTER(U+FFFD)가 대체 문자열로 사용됩니다. 특히 유니코드 문자로 변환할 수 없는 바이트 시퀀스를 디코딩하는 경우에 해당합니다. 그러나 자유롭게 대체 문자열을 선택할 수 있으며 여러 문자를 포함할 수 있습니다.

### <a name="exception-fallback"></a>예외 대체(fallback)

최적 대체(fallback) 또는 대체 문자열을 제공하는 대신 인코더는 문자 집합을 인코드할 수 없는 경우 [EncoderFallbackException](xref:System.Text.EncoderFallbackException)을 throw하고, 디코더는 바이트 배열을 디코드할 수 없는 경우 [DecoderFallbackException](xref:System.Text.DecoderFallbackException)을 throw할 수 있습니다. 인코딩 및 디코딩 작업에서 예외를 throw하기 위해 각각 [EncoderFallbackException](xref:System.Text.EncoderFallbackException) 개체와 [DecoderFallbackException](xref:System.Text.DecoderFallbackException) 개체를 [Encoding.GetEncoding(String, EncoderFallback, DecoderFallback)](xref:System.Text.Encoding.GetEncoding(System.String,System.Text.EncoderFallback,System.Text.DecoderFallback)) 메서드에 제공합니다. 다음 예제에서는 ASCIIEncoding 클래스를 사용한 예외 대체(fallback)를 보여 줍니다.

```csharp
using System;
using System.Text;

public class Example
{
   public static void Main()
   {
      Encoding enc = Encoding.GetEncoding("us-ascii", 
                                          new EncoderExceptionFallback(), 
                                          new DecoderExceptionFallback());

      string str1 = "\u24C8 \u2075 \u221E";
      Console.WriteLine(str1);
      foreach (var ch in str1)
         Console.Write("{0} ", Convert.ToUInt16(ch).ToString("X4"));

      Console.WriteLine("\n");

      // Encode the original string using the ASCII encoder.
      byte[] bytes = {};
      try {
         bytes = enc.GetBytes(str1);
         Console.Write("Encoded bytes: ");
         foreach (var byt in bytes)
            Console.Write("{0:X2} ", byt);

         Console.WriteLine();
      }
      catch (EncoderFallbackException e) {
         Console.Write("Exception: ");
         if (e.IsUnknownSurrogate())
            Console.WriteLine("Unable to encode surrogate pair 0x{0:X4} 0x{1:X3} at index {2}.", 
                              Convert.ToUInt16(e.CharUnknownHigh), 
                              Convert.ToUInt16(e.CharUnknownLow), 
                              e.Index);
         else
            Console.WriteLine("Unable to encode 0x{0:X4} at index {1}.", 
                              Convert.ToUInt16(e.CharUnknown), 
                              e.Index);
         return;
      }
      Console.WriteLine();

      // Decode the ASCII bytes.
      try {
         string str2 = enc.GetString(bytes);
         Console.WriteLine("Round-trip: {0}", str1.Equals(str2));
         if (! str1.Equals(str2)) {
            Console.WriteLine(str2);
            foreach (var ch in str2)
               Console.Write("{0} ", Convert.ToUInt16(ch).ToString("X4"));

            Console.WriteLine();
         } 
      }
      catch (DecoderFallbackException e) {
         Console.Write("Unable to decode byte(s) ");
         foreach (byte unknown in e.BytesUnknown)
            Console.Write("0x{0:X2} ");

         Console.WriteLine("at index {0}", e.Index);
      }
   }
}
// The example displays the following output:
//       Ⓢ ⁵ ∞
//       24C8 0020 2075 0020 221E
//       
//       Exception: Unable to encode 0x24C8 at index 0.
```

```vb
Imports System.Text

Module Example
   Public Sub Main()
      Dim enc As Encoding = Encoding.GetEncoding("us-ascii", 
                                                 New EncoderExceptionFallback(), 
                                                 New DecoderExceptionFallback())

      Dim str1 As String = String.Format("{0} {1} {2}", ChrW(&h24C8), ChrW(&h2075), ChrW(&h221E))
      Console.WriteLine(str1)
      For Each ch In str1
         Console.Write("{0} ", Convert.ToUInt16(ch).ToString("X4"))
      Next    
      Console.WriteLine()
      Console.WriteLine() 

      ' Encode the original string using the ASCII encoder.
      Dim bytes() As Byte = {}
      Try
         bytes = enc.GetBytes(str1)
         Console.Write("Encoded bytes: ")
         For Each byt In bytes
            Console.Write("{0:X2} ", byt)
         Next
         Console.WriteLine()
      Catch e As EncoderFallbackException
         Console.Write("Exception: ")
         If e.IsUnknownSurrogate() Then
            Console.WriteLine("Unable to encode surrogate pair 0x{0:X4} 0x{1:X3} at index {2}.", 
                              Convert.ToUInt16(e.CharUnknownHigh), 
                              Convert.ToUInt16(e.CharUnknownLow), 
                              e.Index)
         Else
            Console.WriteLine("Unable to encode 0x{0:X4} at index {1}.", 
                              Convert.ToUInt16(e.CharUnknown), 
                              e.Index)
         End If                              
         Exit Sub
      End Try
      Console.WriteLine()

      ' Decode the ASCII bytes.
      Try
         Dim str2 As String = enc.GetString(bytes)
         Console.WriteLine("Round-trip: {0}", str1.Equals(str2))
         If Not str1.Equals(str2) Then
            Console.WriteLine(str2)
            For Each ch In str2
               Console.Write("{0} ", Convert.ToUInt16(ch).ToString("X4"))
            Next    
            Console.WriteLine()
         End If 
      Catch e As DecoderFallbackException
         Console.Write("Unable to decode byte(s) ")
         For Each unknown As Byte In e.BytesUnknown
            Console.Write("0x{0:X2} ")
         Next
         Console.WriteLine("at index {0}", e.Index)
      End Try
   End Sub
End Module
' The example displays the following output:
'       Ⓢ ⁵ ∞
'       24C8 0020 2075 0020 221E
'       
'       Exception: Unable to encode 0x24C8 at index 0.
```

> [!NOTE]
> 인코딩 작업에 대한 사용자 지정 예외 처리기를 구현할 수도 있습니다. 자세한 내용은 [사용자 지정 대체(fallback) 전략 구현](#implementing-a-custom-fallback-strategy) 섹션을 참조하세요.
 
[EncoderFallbackException](xref:System.Text.EncoderFallbackException) 및 [DecoderFallbackException](xref:System.Text.DecoderFallbackException) 개체는 예외를 발생시킨 조건에 대한 다음 정보를 제공합니다. 

* [EncoderFallbackException](xref:System.Text.EncoderFallbackException) 개체에는 인코드할 수 없는 문자가 알 수 없는 서로게이트 쌍을 나타내는지(이 경우 메서드에서 `true`가 반환됨) 또는 알 수 없는 단일 문자를 나타내는지(이 경우 메서드에서 `false`가 반환됨)를 표시하는 [IsUnknownSurrogate](xref:System.Text.EncoderFallbackException.IsUnknownSurrogate) 메서드가 포함되어 있습니다. 서로게이트 쌍의 문자는 [EncoderFallbackException.CharUnknownHigh](xref:System.Text.EncoderFallbackException.CharUnknownHigh) 및 [EncoderFallbackException.CharUnknownLow](xref:System.Text.EncoderFallbackException.CharUnknownLow) 속성에서 사용할 수 있습니다. 알 수 없는 단일 문자는 [EncoderFallbackException.CharUnknown](xref:System.Text.EncoderFallbackException.CharUnknown) 속성에서 사용할 수 있습니다. [EncoderFallbackException.Index](xref:System.Text.EncoderFallbackException.Index) 속성은 인코드할 수 없는 첫 번째 문자가 발견된 문자열의 위치를 나타냅니다.

* [DecoderFallbackException](xref:System.Text.DecoderFallbackException) 개체에는 디코드할 수 없는 바이트 배열을 반환하는 [BytesUnknown](xref:System.Text.DecoderFallbackException.BytesUnknown) 속성이 포함되어 있습니다. [DecoderFallbackException.Index](xref:System.Text.DecoderFallbackException.Index) 속성은 알 수 없는 바이트의 시작 위치를 나타냅니다.

[EncoderFallbackException](xref:System.Text.EncoderFallbackException) 및 [DecoderFallbackException](xref:System.Text.DecoderFallbackException) 개체는 예외에 대한 적절한 진단 정보를 제공하지만 인코딩 또는 디코딩 버퍼에 대한 액세스는 제공하지 않습니다. 따라서 인코딩 또는 디코딩 메서드 내에서 잘못된 데이터를 바꾸거나 수정할 수 있도록 허용하지 않습니다.

## <a name="implementing-a-custom-fallback-strategy"></a>사용자 지정 대체(fallback) 전략 구현

코드 페이지에서 내부적으로 구현되는 최적 매핑 외에도 .NET에는 대체(fallback) 전략을 구현하기 위한 다음과 같은 클래스가 포함되어 있습니다.

* [EncoderReplacementFallback](xref:System.Text.EncoderReplacementFallback) 및 [EncoderFallbackBuffer](xref:System.Text.EncoderFallbackBuffer)를 사용하여 인코딩 작업에서 문자를 바꿉니다.

* [DecoderReplacementFallback](xref:System.Text.DecoderReplacementFallback) 및 [DecoderFallbackBuffer](xref:System.Text.DecoderFallbackBuffer)를 사용하여 디코딩 작업에서 문자를 바꿉니다.

* [EncoderExceptionFallback](xref:System.Text.EncoderExceptionFallback) 및 [EncoderFallbackBuffer](xref:System.Text.EncoderFallbackBuffer)를 사용하여 문자를 인코드할 수 없는 경우 [EncoderFallbackException](xref:System.Text.EncoderFallbackException)을 throw합니다.

* [DecoderExceptionFallback](xref:System.Text.DecoderExceptionFallback) 및 [DecoderFallbackBuffer](xref:System.Text.DecoderFallbackBuffer)를 사용하여 문자를 디코드할 수 없는 경우 [DecoderFallbackException](xref:System.Text.DecoderFallbackException)을 throw합니다.

또한 다음 단계를 수행하여 최적 대체(fallback), 교체 대체(fallback) 또는 예외 대체(fallback)를 사용하는 사용자 지정 솔루션을 구현할 수 있습니다. 

1. 인코딩 작업의 경우 [EncoderFallback](xref:System.Text.EncoderFallback)에서 클래스를 파생시키고, 디코딩 작업의 경우 [DecoderFallback](xref:System.Text.DecoderFallback)에서 클래스를 파생시킵니다.

2. 인코딩 작업의 경우 [EncoderFallbackBuffer](xref:System.Text.EncoderFallbackBuffer)에서 클래스를 파생시키고, 디코딩 작업의 경우 [DecoderFallbackBuffer](xref:System.Text.DecoderFallbackBuffer)에서 클래스를 파생시킵니다.

3. 예외 대체(fallback)의 경우 미리 정의된 [EncoderFallbackException](xref:System.Text.EncoderFallbackException) 및 [DecoderFallbackException](xref:System.Text.DecoderFallbackException) 클래스가 요구를 충족하지 않는 경우 [Exception](xref:System.Exception) 또는 [ArgumentException](xref:System.ArgumentException)과 같은 예외 개체에서 클래스를 파생시킵니다.

### <a name="deriving-from-encoderfallback-or-decoderfallback"></a>EncoderFallback 또는 DecoderFallback에서 파생

사용자 지정 대체(fallback) 솔루션을 구현하려면 인코딩 작업의 경우 [EncoderFallback](xref:System.Text.EncoderFallback)에서 상속받고 디코딩 작업의 경우 [DecoderFallback](xref:System.Text.DecoderFallback)에서 상속받는 클래스를 만들어야 합니다. 이러한 클래스의 인스턴스는 [Encoding.GetEncoding(String, EncoderFallback, DecoderFallback)](xref:System.Text.Encoding.GetEncoding(System.String,System.Text.EncoderFallback,System.Text.DecoderFallback)) 메서드에 전달되며 인코딩 클래스와 대체(fallback) 구현 간의 중개자 역할을 합니다.

인코더 또는 디코더에 대한 사용자 지정 대체(fallback) 솔루션을 만드는 경우 다음과 같은 멤버를 구현해야 합니다.

* 최적, 교체 또는 예외 대체(fallback)가 단일 문자를 대체하기 위해 반환할 수 있는 최대 문자 수를 반환하는 [EncoderFallback.MaxCharCount](xref:System.Text.EncoderFallback.MaxCharCount) 또는 [DecoderFallback.MaxCharCount](xref:System.Text.DecoderFallback.MaxCharCount) 속성. 사용자 지정 예외 대체(fallback)의 경우 해당 값은 0입니다. 

* 사용자 지정 [EncoderFallbackBuffer](xref:System.Text.EncoderFallbackBuffer) 또는 [DecoderFallbackBuffer](xref:System.Text.DecoderFallbackBuffer) 구현을 반환하는 [EncoderFallback.CreateFallbackBuffer](xref:System.Text.EncoderFallback.CreateFallbackBuffer) 또는 [DecoderFallback.CreateFallbackBuffer](xref:System.Text.DecoderFallback.CreateFallbackBuffer) 메서드. 메서드는 성공적으로 인코딩할 수 없는 첫 번째 문자를 발견할 때 인코더에 의해 호출되거나 성공적으로 디코딩할 수 없는 첫 번째 바이트를 발견할 때 디코더에 의해 호출됩니다.

### <a name="deriving-from-encoderfallbackbuffer-or-decoderfallbackbuffer"></a>EncoderFallbackBuffer 또는 DecoderFallbackBuffer에서 파생

사용자 지정 대체(fallback) 솔루션을 구현하려면 인코딩 작업의 경우 [EncoderFallbackBuffer](xref:System.Text.EncoderFallbackBuffer)에서 상속받고 디코딩 작업의 경우 [DecoderFallbackBuffer](xref:System.Text.DecoderFallbackBuffer)에서 상속받는 클래스도 만들어야 합니다. 이러한 클래스의 인스턴스는 [EncoderFallback](xref:System.Text.EncoderFallback) 및 [DecoderFallback](xref:System.Text.DecoderFallback) 클래스의 `CreateFallbackBuffer` 메서드에서 반환됩니다. [EncoderFallback.CreateFallbackBuffer](xref:System.Text.EncoderFallback.CreateFallbackBuffer) 메서드는 인코드할 수 없는 첫 번째 문자를 발견할 때 인코더에 의해 호출되고, [DecoderFallback.CreateFallbackBuffer](xref:System.Text.DecoderFallback.CreateFallbackBuffer) 메서드는 디코드할 수 없는 바이트를 하나 이상 발견할 때 디코더에 의해 호출됩니다. [EncoderFallbackBuffer](xref:System.Text.EncoderFallbackBuffer) 및 [DecoderFallbackBuffer](xref:System.Text.DecoderFallbackBuffer) 클래스는 대체 (fallback) 구현을 제공합니다. 각 인스턴스는 인코딩할 수 없는 문자 또는 디코딩할 수 없는 바이트를 대체하는 대체(fallback) 문자가 포함된 버퍼를 나타냅니다.

인코더 또는 디코더에 대한 사용자 지정 대체(fallback) 솔루션을 만드는 경우 다음과 같은 멤버를 구현해야 합니다.

* [EncoderFallbackBuffer.Fallback](xref:System.Text.EncoderFallbackBuffer.%23ctor) 또는 [DecoderFallbackBuffer.Fallback](xref:System.Text.DecoderFallbackBuffer.Fallback(System.Byte[],System.Int32)) 메서드. [EncoderFallbackBuffer.Fallback](xref:System.Text.EncoderFallbackBuffer.Fallback(System.Char,System.Char,System.Int32))은 인코드할 수 없는 문자 정보를 대체(fallback) 버퍼에 제공하기 위해 인코더에서 호출됩니다. 인코딩할 문자가 서로게이트 쌍일 수 있으므로 이 메서드는 오버로드됩니다. 하나의 오버로드에는 인코딩할 문자 및 문자열에서 해당 인덱스가 전달됩니다. 두 번째 오버로드에는 상위 및 하위 서로게이트와 문자열에서 해당 인덱스가 전달됩니다. [DecoderFallbackBuffer.Fallback](xref:System.Text.DecoderFallbackBuffer.Fallback(System.Byte[],System.Int32)) 메서드는 디코드할 수 없는 바이트 정보를 대체(fallback) 버퍼에 제공하기 위해 디코더에서 호출됩니다. 이 메서드에는 디코딩할 수 없는 바이트 배열 및 첫 번째 바이트의 인덱스가 전달됩니다. 대체(fallback) 버퍼가 최적 또는 교체 문자를 제공할 수 있는 경우 대체(fallback) 메서드에서 `true`를 반환해야 하고, 그러지 않으면 `false`를 반환해야 합니다. 예외 대체(fallback)의 경우 대체(fallback) 메서드에서 예외를 발생시켜야 합니다.

* 인코더 또는 디코더가 대체(fallback) 버퍼에서 다음 문자를 가져오기 위해 반복적으로 호출하는 [EncoderFallbackBuffer.GetNextChar](xref:System.Text.EncoderFallbackBuffer.GetNextChar) 또는 [DecoderFallbackBuffer.GetNextChar](xref:System.Text.DecoderFallbackBuffer.GetNextChar) 메서드. 모든 대체(fallback) 문자가 반환되고 나면 메서드에서 U+0000을 반환해야 합니다. 

* 대체(fallback) 버퍼에 남아 있는 문자 수를 반환하는 [EncoderFallbackBuffer.Remaining](xref:System.Text.EncoderFallbackBuffer.Remaining) 또는 [DecoderFallbackBuffer.Remaining](xref:System.Text.DecoderFallbackBuffer.Remaining) 속성

* 대체(fallback) 버퍼의 현재 위치를 이전 문자로 이동하는 [EncoderFallbackBuffer.MovePrevious](xref:System.Text.EncoderFallbackBuffer.MovePrevious) 또는 [DecoderFallbackBuffer.MovePrevious](xref:System.Text.DecoderFallbackBuffer.MovePrevious) 메서드

* 대체(fallback) 버퍼를 다시 초기화하는 [EncoderFallbackBuffer.Reset](xref:System.Text.EncoderFallbackBuffer.Reset) 또는 [DecoderFallbackBuffer.Reset](xref:System.Text.DecoderFallbackBuffer.Reset) 메서드

대체(fallback) 구현이 최적 대체(fallback) 또는 교체 대체(fallback)인 경우 [EncoderFallbackBuffer](xref:System.Text.EncoderFallbackBuffer) 및 [DecoderFallbackBuffer](xref:System.Text.DecoderFallbackBuffer)에서 파생된 클래스가 두 개의 전용 인스턴스 필드(버퍼의 정확한 문자 수 및 버퍼에서 반환할 다음 문자의 인덱스)도 유지 관리합니다.

### <a name="an-encoderfallback-example"></a>EncoderFallback 예제

이전 예제에서는 교체 대체(fallback)를 사용하여 ASCII 문자에 응답하지 않은 유니코드 문자를 별표(\*)로 대체했습니다. 다음 예제에서는 사용자 지정 최적 대체(fallback) 구현을 대신 사용하여 보다 효과적인 비 ASCII 문자 매핑을 제공합니다.

다음 코드에서는 비 ASCII 문자의 최적 매핑을 처리하기 위해 [EncoderFallback](xref:System.Text.EncoderFallback)에서 파생된 `CustomMapper`라는 클래스를 정의합니다. 해당 `CreateFallbackBuffer` 메서드는 [EncoderFallbackBuffer](xref:System.Text.EncoderFallbackBuffer) 구현을 제공하는 `CustomMapperFallbackBuffer` 개체를 반환합니다. `CustomMapper` 클래스는 [Dictionary&lt;TKey, TValue&gt;](xref:System.Collections.Generic.Dictionary%602) 개체를 사용하여 지원되지 않는 유니코드 문자(키 값) 및 해당 8비트 문자(64비트 정수에서는 연속된 2바이트에 저장됨)의 매핑을 저장합니다. 대체(fallback) 버퍼에서 이 매핑을 사용할 수 있도록 `CustomMapper` 인스턴스가 `CustomMapperFallbackBuffer` 클래스 생성자에 매개 변수로 전달됩니다. 가장 긴 매핑은 유니코드 문자 U+221E에 해당하는 문자열 "INF"이므로 `MaxCharCount` 속성은 3을 반환합니다. 

```csharp
public class CustomMapper : EncoderFallback
{
   public string DefaultString;
   internal Dictionary<ushort, ulong> mapping;

   public CustomMapper() : this("*")
   {   
   }

   public CustomMapper(string defaultString)
   {
      this.DefaultString = defaultString;

      // Create table of mappings
      mapping = new Dictionary<ushort, ulong>();
      mapping.Add(0x24C8, 0x53);
      mapping.Add(0x2075, 0x35);
      mapping.Add(0x221E, 0x49004E0046);
   }

   public override EncoderFallbackBuffer CreateFallbackBuffer()
   {
      return new CustomMapperFallbackBuffer(this);
   }

   public override int MaxCharCount
   {
      get { return 3; }
   } 
}
```

```vb
Public Class CustomMapper : Inherits EncoderFallback
   Public DefaultString As String
   Friend mapping As Dictionary(Of UShort, ULong)

   Public Sub New()
      Me.New("?")
   End Sub

   Public Sub New(ByVal defaultString As String)
      Me.DefaultString = defaultString

      ' Create table of mappings
      mapping = New Dictionary(Of UShort, ULong)
      mapping.Add(&H24C8, &H53)
      mapping.Add(&H2075, &H35)
      mapping.Add(&H221E, &H49004E0046)
   End Sub

   Public Overrides Function CreateFallbackBuffer() As System.Text.EncoderFallbackBuffer
      Return New CustomMapperFallbackBuffer(Me)
   End Function

   Public Overrides ReadOnly Property MaxCharCount As Integer
      Get
         Return 3
      End Get
   End Property
End Class
```

다음 코드에서는 [EncoderFallbackBuffer](xref:System.Text.EncoderFallbackBuffer)에서 파생된 `CustomMapperFallbackBuffer` 클래스를 정의합니다. 최적 매핑을 포함하며 `CustomMapper` 인스턴스에서 정의된 사전은 해당 클래스 생성자에서 사용할 수 있습니다. ASCII 인코더가 인코딩할 수 없는 유니코드 문자가 매핑 사전에 정의된 경우 해당 `Fallback` 메서드가 `true`를 반환하고, 그러지 않으면 `false`를 반환합니다. 각 대체(fallback)에서 private `count` 변수는 반환해야 하는 남은 문자 수를 나타내고, private `index` 변수는 문자열 버퍼 `charsToReturn`에서 반환할 다음 문자의 위치를 나타냅니다. 

```csharp
public class CustomMapperFallbackBuffer : EncoderFallbackBuffer
{
   int count = -1;                   // Number of characters to return
   int index = -1;                   // Index of character to return
   CustomMapper fb; 
   string charsToReturn; 

   public CustomMapperFallbackBuffer(CustomMapper fallback)
   {
      this.fb = fallback;
   }

   public override bool Fallback(char charUnknownHigh, char charUnknownLow, int index)
   {
      // Do not try to map surrogates to ASCII.
      return false;
   }

   public override bool Fallback(char charUnknown, int index)
   {
      // Return false if there are already characters to map.
      if (count >= 1) return false;

      // Determine number of characters to return.
      charsToReturn = String.Empty;

      ushort key = Convert.ToUInt16(charUnknown);
      if (fb.mapping.ContainsKey(key)) {
         byte[] bytes = BitConverter.GetBytes(fb.mapping[key]);
         int ctr = 0;
         foreach (var byt in bytes) {
            if (byt > 0) {
               ctr++;
               charsToReturn += (char) byt;
            }
         }
         count = ctr;
      }
      else {
         // Return default.
         charsToReturn = fb.DefaultString;
         count = 1;
      }
      this.index = charsToReturn.Length - 1;

      return true;
   }

   public override char GetNextChar()
   {
      // We'll return a character if possible, so subtract from the count of chars to return.
      count--;
      // If count is less than zero, we've returned all characters.
      if (count < 0) 
         return '\u0000';

      this.index--;
      return charsToReturn[this.index + 1];
   }

   public override bool MovePrevious()
   {
      // Original: if count >= -1 and pos >= 0
      if (count >= -1) {
         count++;
         return true;
      }
      else {
         return false;
      }
   }

   public override int Remaining 
   {
      get { return count < 0 ? 0 : count; }
   }

   public override void Reset()
   {
      count = -1;
      index = -1;
   }
}
```

```vb
Public Class CustomMapperFallbackBuffer : Inherits EncoderFallbackBuffer

   Dim count As Integer = -1        ' Number of characters to return
   Dim index As Integer = -1        ' Index of character to return
   Dim fb As CustomMapper
   Dim charsToReturn As String

   Public Sub New(ByVal fallback As CustomMapper)
      MyBase.New()
      Me.fb = fallback
   End Sub

   Public Overloads Overrides Function Fallback(ByVal charUnknownHigh As Char, ByVal charUnknownLow As Char, ByVal index As Integer) As Boolean
      ' Do not try to map surrogates to ASCII.
      Return False
   End Function

   Public Overloads Overrides Function Fallback(ByVal charUnknown As Char, ByVal index As Integer) As Boolean
      ' Return false if there are already characters to map.
      If count >= 1 Then Return False

      ' Determine number of characters to return.
      charsToReturn = String.Empty

      Dim key As UShort = Convert.ToUInt16(charUnknown)
      If fb.mapping.ContainsKey(key) Then
         Dim bytes() As Byte = BitConverter.GetBytes(fb.mapping.Item(key))
         Dim ctr As Integer
         For Each byt In bytes
            If byt > 0 Then
               ctr += 1
               charsToReturn += Chr(byt)
            End If
         Next
         count = ctr
      Else
         ' Return default.
         charsToReturn = fb.DefaultString
         count = 1
      End If
      Me.index = charsToReturn.Length - 1

      Return True
   End Function

   Public Overrides Function GetNextChar() As Char
      ' We'll return a character if possible, so subtract from the count of chars to return.
      count -= 1
      ' If count is less than zero, we've returned all characters.
      If count < 0 Then Return ChrW(0)

      Me.index -= 1
      Return charsToReturn(Me.index + 1)
   End Function

   Public Overrides Function MovePrevious() As Boolean
      ' Original: if count >= -1 and pos >= 0
      If count >= -1 Then
         count += 1
         Return True
      Else
         Return False
      End If
   End Function

   Public Overrides ReadOnly Property Remaining As Integer
      Get
         Return If(count < 0, 0, count)
      End Get
   End Property

   Public Overrides Sub Reset()
      count = -1
      index = -1
   End Sub
End Class
```

다음 코드는 `CustomMapper` 개체를 인스턴스화하고 해당 인스턴스를 [Encoding.GetEncoding(String, EncoderFallback, DecoderFallback)](xref:System.Text.Encoding.GetEncoding(System.String,System.Text.EncoderFallback,System.Text.DecoderFallback)) 메서드에 전달합니다. 출력은 최적 대체(fallback) 구현에서 원래 문자열에 있는 3자의 비 ASCII 문자를 성공적으로 처리했음을 나타냅니다.

```csharp
using System;
using System.Collections.Generic;
using System.Text;

class Program
{
   static void Main()
   {
      Encoding enc = Encoding.GetEncoding("us-ascii", new CustomMapper(), new DecoderExceptionFallback());

      string str1 = "\u24C8 \u2075 \u221E";
      Console.WriteLine(str1);
      for (int ctr = 0; ctr <= str1.Length - 1; ctr++) {
         Console.Write("{0} ", Convert.ToUInt16(str1[ctr]).ToString("X4"));
         if (ctr == str1.Length - 1) 
            Console.WriteLine();
      }
      Console.WriteLine();

      // Encode the original string using the ASCII encoder.
      byte[] bytes = enc.GetBytes(str1);
      Console.Write("Encoded bytes: ");
      foreach (var byt in bytes)
         Console.Write("{0:X2} ", byt);

      Console.WriteLine("\n");

      // Decode the ASCII bytes.
      string str2 = enc.GetString(bytes);
      Console.WriteLine("Round-trip: {0}", str1.Equals(str2));
      if (! str1.Equals(str2)) {
         Console.WriteLine(str2);
         foreach (var ch in str2)
            Console.Write("{0} ", Convert.ToUInt16(ch).ToString("X4"));

         Console.WriteLine();
      }
   }
}
```

```vb
Imports System.Text
Imports System.Collections.Generic

Module Module1

   Sub Main()
      Dim enc As Encoding = Encoding.GetEncoding("us-ascii", New CustomMapper(), New DecoderExceptionFallback())

      Dim str1 As String = String.Format("{0} {1} {2}", ChrW(&H24C8), ChrW(&H2075), ChrW(&H221E))
      Console.WriteLine(str1)
      For ctr As Integer = 0 To str1.Length - 1
         Console.Write("{0} ", Convert.ToUInt16(str1(ctr)).ToString("X4"))
         If ctr = str1.Length - 1 Then Console.WriteLine()
      Next
      Console.WriteLine()

      ' Encode the original string using the ASCII encoder.
      Dim bytes() As Byte = enc.GetBytes(str1)
      Console.Write("Encoded bytes: ")
      For Each byt In bytes
         Console.Write("{0:X2} ", byt)
      Next
      Console.WriteLine()
      Console.WriteLine()

      ' Decode the ASCII bytes.
      Dim str2 As String = enc.GetString(bytes)
      Console.WriteLine("Round-trip: {0}", str1.Equals(str2))
      If Not str1.Equals(str2) Then
         Console.WriteLine(str2)
         For Each ch In str2
            Console.Write("{0} ", Convert.ToUInt16(ch).ToString("X4"))
         Next
         Console.WriteLine()
      End If
   End Sub
End Module
```

## <a name="see-also"></a>참고 항목

[System.Text.Encoder](xref:System.Text.Encoder)

[System.Text.EncoderFallback](xref:System.Text.EncoderFallback)

[System.Text.Decoder](xref:System.Text.Decoder)

[System.Text.DecoderFallback](xref:System.Text.DecoderFallback)

[System.Text.Encoding](xref:System.Text.Encoding)







<!--HONumber=Nov16_HO1-->


