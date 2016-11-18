---
title: "방법: 사용자 지정 숫자 형식 공급자 정의 및 사용"
description: "사용자 지정 숫자 형식 공급자를 정의 및 사용하는 방법"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 07/26/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 9b184114-6612-4c1a-a2db-2e24e65b0f77
translationtype: Human Translation
ms.sourcegitcommit: fb00da6505c9edb6a49d2003ae9bcb8e74c11d6c
ms.openlocfilehash: bb62bdd03d4af4f764ac3bc8734c67902fffa798

---

# <a name="how-to-define-and-use-custom-numeric-format-providers"></a>방법: 사용자 지정 숫자 형식 공급자 정의 및 사용

.NET에서는 숫자 값의 문자열 표현을 광범위하게 제어할 수 있습니다. 숫자 값의 형식을 사용자 지정하기 위한 다음과 같은 기능을 지원합니다.

* 숫자를 해당 문자열 표현으로 변환하기 위한 미리 정의된 형식 집합을 제공하는 표준 숫자 형식 문자열입니다. format 매개 변수가 있는 숫자 형식 지정 메서드(예: [Decimal.ToString(String](xref:System.Decimal.ToString(System.String)))와 함께 사용할 수 있습니다. 자세한 내용은 [표준 숫자 형식 문자열](standard-numeric.md)을 참조하세요.

* 함께 결합되어 사용자 지정 숫자 형식 지정자를 정의할 수 있는 기호 집합을 제공하는 사용자 지정 숫자 형식 문자열입니다. format 매개 변수가 있는 숫자 형식 지정 메서드(예: [Decimal.ToString(String](xref:System.Decimal.ToString(System.String)))와 함께 사용할 수도 있습니다. 자세한 내용은 [사용자 지정 숫자 형식 문자열](custom-numeric.md)을 참조하세요.

* 숫자 값의 문자열 표현을 표시하는 데 사용되는 기호 및 형식 패턴을 정의하는 사용자 지정 [CultureInfo](xref:System.Globalization.CultureInfo) 또는 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 개체입니다. *provider* 매개 변수가 있는 숫자 형식 지정 메서드(예: [ToString](xref:System.Int32.ToString(System.IFormatProvider)))와 함께 사용할 수 있습니다. 일반적으로 *provider* 매개 변수는 문화권별 형식 지정에 사용됩니다.

응용 프로그램에서 형식이 지정된 계정 번호, ID 번호 또는 우편 번호를 표시해야 하는 경우와 같이 이 세 가지 방법이 부적절한 경우도 있습니다. .NET에서는 [CultureInfo](xref:System.Globalization.CultureInfo) 또는 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 개체가 아닌 형식 지정 개체를 정의하여 숫자 값의 형식 지정 방법을 결정할 수도 있습니다. 이 항목에서는 이러한 개체를 구현하기 위한 단계별 지침을 제공하고 전화 번호 형식을 지정하는 예제를 제공합니다.

## <a name="to-define-a-custom-format-provider"></a>사용자 지정 형식 공급자를 정의하려면

1. [IFormatProvider](xref:System.IFormatProvider) 및 [ICustomFormatter](xref:System.ICustomFormatter) 인터페이스를 구현하는 클래스를 정의합니다. 

2. [IFormatProvider.GetFormat](xref:System.IFormatProvider.GetFormat(System.Type)) 메서드를 구현합니다. [GetFormat](xref:System.IFormatProvider.GetFormat(System.Type))은 형식 지정 메서드(예: [String.Format(IFormatProvider,String,Object[])](xref:System.String.Format(System.IFormatProvider,System.String,System.Object)) 메서드)가 실제로 사용자 지정 형식 지정을 수행하는 개체를 검색하기 위해 호출하는 콜백 메서드입니다. 일반적인 [GetFormat](xref:System.IFormatProvider.GetFormat(System.Type)) 구현에서는 다음 작업을 수행합니다.

    a. 메서드 매개 변수로 전달되는 [Type](xref:System.Type) 개체가 [ICustomFormatter](xref:System.ICustomFormatter) 인터페이스를 나타내는지 여부를 결정합니다.
    
    b. 매개 변수가 [ICustomFormatter](xref:System.ICustomFormatter) 인터페이스를 나타내지 않는 경우 [GetFormat](xref:System.IFormatProvider.GetFormat(System.Type))은 사용자 지정 형식 지정을 제공하는 [ICustomFormatter](xref:System.ICustomFormatter) 인터페이스를 구현하는 개체를 반환합니다. 일반적으로 사용자 지정 형식 지정 개체 자체가 반환됩니다. 
    
    c. 매개 변수가 [ICustomFormatter](xref:System.ICustomFormatter) 인터페이스를 나타내지 않는 경우 [GetFormat](xref:System.IFormatProvider.GetFormat(System.Type))은 `null`을 반환합니다.
    
3. [ICustomFormatter.Format](xref:System.ICustomFormatter.Format(System.String,System.Object,System.IFormatProvider)) 메서드를 구현합니다. 이 메서드는 [String.Format(IFormatProvider,String,Object[])](xref:System.String.Format(System.IFormatProvider,System.String,System.Object)) 메서드에서 호출되며 숫자의 문자열 표현을 반환합니다. 일반적으로 메서드를 구현하는 과정은 다음과 같습니다.

    a. 필요에 따라 *provider* 매개 변수를 검사하여 메서드가 형식 지정 서비스를 제공하기에 적합한지 확인합니다. [IFormatProvider](xref:System.IFormatProvider) 및 [ICustomFormatter](xref:System.ICustomFormatter)를 둘 다 구현하는 형식 지정 개체의 경우 *provider* 매개 변수가 현재 형식 지정 개체와 같은지 테스트해야 합니다.
    
    b. 형식 지정 개체가 사용자 지정 형식 지정자를 지원해야 하는지 여부를 결정합니다. 예를 들어 "N" 형식 지정자는 미국 전화 번호가 NANP 형식으로, "I" 형식 지정자는 ITU-T 권장 E.123 형식으로 출력되어야 함을 나타낼 수 있습니다. 형식 지정자가 사용된 경우 메서드에서 특정 형식 지정자를 처리해야 합니다. 지정자는 메서드의 format 매개 변수에 전달됩니다. 지정자가 없는 경우 *format* 매개 변수 값은 [String.Empty](xref:System.String#System_String_Empty)입니다.
    
    c. 메서드에 *arg* 매개 변수로 전달되는 숫자 값을 검색합니다. 문자열 표현으로 변환하는 데 필요한 조작을 수행합니다.
    
    d. *arg* 매개 변수의 문자열 표현을 반환합니다.
    
## <a name="to-use-a-custom-numeric-formatting-object"></a>사용자 지정 숫자 형식 지정 개체를 사용하려면

1. 사용자 지정 형식 지정 클래스의 새 인스턴스를 만듭니다.

2. [String.Format(IFormatProvider,String,Object[])](xref:System.String.Format(System.IFormatProvider,System.String,System.Object)) 형식 지정 메서드를 호출하고 사용자 지정 형식 지정 개체, 형식 지정자(또는 사용하지 않는 경우 [String.Empty](xref:System.String.Empty)) 및 형식을 지정할 숫자 값을 전달합니다. 

## <a name="example"></a>예제

다음 예제에서는 미국 전화 번호를 나타내는 숫자를 NANP 또는 E.123 형식으로 변환하는 `TelephoneFormatter`라는 사용자 지정 숫자 형식 공급자를 정의합니다. 메서드는 두 가지 형식 지정자 "N"(NANP 형식 출력)과 "I"(국제 E.123 형식 출력)를 처리합니다.

```csharp
using System;
using System.Globalization;

public class TelephoneFormatter : IFormatProvider, ICustomFormatter
{
   public object GetFormat(Type formatType)
   {
      if (formatType == typeof(ICustomFormatter))
         return this;
      else
         return null;
   }               

   public string Format(string format, object arg, IFormatProvider formatProvider)
   {
      // Check whether this is an appropriate callback             
      if (! this.Equals(formatProvider))
         return null; 

      // Set default format specifier             
      if (string.IsNullOrEmpty(format)) 
         format = "N";

      string numericString = arg.ToString();

      if (format == "N")
      {
         if (numericString.Length <= 4)
            return numericString;
         else if (numericString.Length == 7)
            return numericString.Substring(0, 3) + "-" + numericString.Substring(3, 4); 
         else if (numericString.Length == 10)
               return "(" + numericString.Substring(0, 3) + ") " +
                      numericString.Substring(3, 3) + "-" + numericString.Substring(6);   
         else
            throw new FormatException( 
                      string.Format("'{0}' cannot be used to format {1}.", 
                                    format, arg.ToString()));
      }
      else if (format == "I")
      {
         if (numericString.Length < 10)
            throw new FormatException(string.Format("{0} does not have 10 digits.", arg.ToString()));
         else
            numericString = "+1 " + numericString.Substring(0, 3) + " " + numericString.Substring(3, 3) + " " + numericString.Substring(6);
      }
      else
      {
         throw new FormatException(string.Format("The {0} format specifier is invalid.", format));
      } 
      return numericString;  
   }
}

public class TestTelephoneFormatter
{
   public static void Main()
   {
      Console.WriteLine(String.Format(new TelephoneFormatter(), "{0}", 0));
      Console.WriteLine(String.Format(new TelephoneFormatter(), "{0}", 911));
      Console.WriteLine(String.Format(new TelephoneFormatter(), "{0}", 8490216));
      Console.WriteLine(String.Format(new TelephoneFormatter(), "{0}", 4257884748));

      Console.WriteLine(String.Format(new TelephoneFormatter(), "{0:N}", 0));
      Console.WriteLine(String.Format(new TelephoneFormatter(), "{0:N}", 911));
      Console.WriteLine(String.Format(new TelephoneFormatter(), "{0:N}", 8490216));
      Console.WriteLine(String.Format(new TelephoneFormatter(), "{0:N}", 4257884748));

      Console.WriteLine(String.Format(new TelephoneFormatter(), "{0:I}", 4257884748));
   }
}
```

```vb
Public Class TelephoneFormatter : Implements IFormatProvider, ICustomFormatter
   Public Function GetFormat(formatType As Type) As Object _
                   Implements IFormatProvider.GetFormat
      If formatType Is GetType(ICustomFormatter) Then
         Return Me
      Else
         Return Nothing
      End If               
   End Function               

   Public Function Format(fmt As String, arg As Object, _
                          formatProvider As IFormatProvider) As String _
                   Implements ICustomFormatter.Format
      ' Check whether this is an appropriate callback             
      If Not Me.Equals(formatProvider) Then Return Nothing 

      ' Set default format specifier             
      If String.IsNullOrEmpty(fmt) Then fmt = "N"

      Dim numericString As String = arg.ToString

      If fmt = "N" Then
         Select Case numericString.Length
            Case <= 4 
               Return numericString
            Case 7
               Return Left(numericString, 3) & "-" & Mid(numericString, 4) 
            Case 10
               Return "(" & Left(numericString, 3) & ") " & _
                      Mid(numericString, 4, 3) & "-" & Mid(numericString, 7)   
            Case Else
               Throw New FormatException( _
                         String.Format("'{0}' cannot be used to format {1}.", _
                                       fmt, arg.ToString()))
         End Select
      ElseIf fmt = "I" Then
         If numericString.Length < 10 Then
            Throw New FormatException(String.Format("{0} does not have 10 digits.", arg.ToString()))
         Else
            numericString = "+1 " & Left(numericString, 3) & " " & Mid(numericString, 4, 3) & " " & Mid(numericString, 7)
         End If      
      Else
         Throw New FormatException(String.Format("The {0} format specifier is invalid.", fmt))
      End If 
      Return numericString  
   End Function
End Class

Public Module TestTelephoneFormatter
   Public Sub Main
      Console.WriteLine(String.Format(New TelephoneFormatter, "{0}", 0))
      Console.WriteLine(String.Format(New TelephoneFormatter, "{0}", 911))
      Console.WriteLine(String.Format(New TelephoneFormatter, "{0}", 8490216))
      Console.WriteLine(String.Format(New TelephoneFormatter, "{0}", 4257884748))

      Console.WriteLine(String.Format(New TelephoneFormatter, "{0:N}", 0))
      Console.WriteLine(String.Format(New TelephoneFormatter, "{0:N}", 911))
      Console.WriteLine(String.Format(New TelephoneFormatter, "{0:N}", 8490216))
      Console.WriteLine(String.Format(New TelephoneFormatter, "{0:N}", 4257884748))

      Console.WriteLine(String.Format(New TelephoneFormatter, "{0:I}", 4257884748))
   End Sub
End Module
```

사용자 지정 숫자 형식 공급자는 [String.Format(IFormatProvider,String,Object[])](xref:System.String.Format(System.IFormatProvider,System.String,System.Object)) 메서드에만 사용할 수 있습니다. [IFormatProvider](xref:System.IFormatProvider) 형식의 매개 변수가 있는 숫자 형식 지정 메서드의 다른 오버로드(예: `ToString`)는 모두 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 형식을 나타내는 [Type](xref:System.Type) 개체를 [IFormatProvider.GetFormat](xref:System.IFormatProvider.GetFormat(System.Type)) 구현에 전달합니다. 반환 시 메서드가 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 개체를 반환할 것으로 예상합니다. 반환하지 않을 경우 사용자 지정 숫자 형식 공급자는 무시되고 현재 문화권에 대한 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 개체가 대신 사용됩니다. 예제에서 `TelephoneFormatter.GetFormat` 메서드는 메서드 매개 변수를 검사하고 [ICustomFormatter](xref:System.ICustomFormatter) 이외의 다른 형식을 나타내는 경우 *null*을 반환하여 숫자 형식 지정 메서드에 부적절하게 전달될 수 있는 가능성을 처리합니다.

사용자 지정 숫자 형식 공급자가 형식 지정자 집합을 지원하는 경우 [String.Format(IFormatProvider,String,Object[])](xref:System.String.Format(System.IFormatProvider,System.String,System.Object)) 메서드 호출에 사용된 형식 항목에 형식 지정자가 제공되지 않은 경우의 기본 동작을 제공해야 합니다. 예제에서는 "N"이 기본 형식 지정자입니다. 이렇게 하면 명시적 형식 지정자를 제공하여 숫자를 형식이 지정된 전화 번호로 변환할 수 있습니다. 다음 예제에서는 이러한 메서드 호출을 보여 줍니다.

```csharp
Console.WriteLine(String.Format(new TelephoneFormatter(), "{0:N}", 4257884748));
```

```vb
Console.WriteLine(String.Format(New TelephoneFormatter, "{0:N}", 4257884748))
```

그러나 형식 지정자가 없는 경우에도 변환을 수행할 수 있습니다. 다음 예제에서는 이러한 메서드 호출을 보여 줍니다.

```csharp
Console.WriteLine(String.Format(new TelephoneFormatter(), "{0}", 4257884748));
```

```vb
Console.WriteLine(String.Format(New TelephoneFormatter, "{0}", 4257884748))
```

기본 형식 지정자가 정의되어 있지 않으면 .NET이 코드에서 지원하지 않는 형식 지정을 제공할 수 있도록 [ICustomFormatter.Format](xref:System.ICustomFormatter.Format(System.String,System.Object,System.IFormatProvider)) 메서드 구현에서 다음과 같은 코드를 포함해야 합니다.

```csharp
if (arg is IFormattable) 
   s = ((IFormattable)arg).ToString(format, formatProvider);
else if (arg != null)    
   s = arg.ToString();
```

```vb
If TypeOf(arg) Is IFormattable Then 
   s = DirectCast(arg, IFormattable).ToString(fmt, formatProvider)
ElseIf arg IsNot Nothing Then    
   s = arg.ToString()
End If
```

이 예제의 경우 [ICustomFormatter.Format](xref:System.ICustomFormatter.Format(System.String,System.Object,System.IFormatProvider))을 구현하는 메서드가 [String.Format(IFormatProvider,String,Object[])](xref:System.String.Format(System.IFormatProvider,System.String,System.Object)) 메서드의 콜백 메서드 역할을 합니다. 따라서 메서드는 *formatProvider* 매개 변수를 검사하여 현재 `TelephoneFormatter` 개체에 대한 참조가 있는지 여부를 확인합니다. 그러나 코드에서 메서드를 직접 호출할 수도 있습니다. 이 경우 *formatProvider* 매개 변수를 사용하여 문화권별 형식 지정 정보를 제공하는 [CultureInfo](xref:System.Globalization.CultureInfo) 또는 [NumberFormatInfo](xref:System.Globalization.NumberFormatInfo) 개체를 제공할 수 있습니다.

## <a name="see-also"></a>참고 항목

[서식 지정 작업 수행](performing-formatting-operations.md)



<!--HONumber=Nov16_HO3-->


