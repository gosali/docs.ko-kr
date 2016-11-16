---
title: "IDisposable을 구현하는 개체 사용"
description: "IDisposable을 구현하는 개체 사용"
keywords: .NET, .NET Core
author: stevehoag
manager: wpickett
ms.date: 08/19/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: df780a6e-734e-44b8-9747-9f7783f79e20
translationtype: Human Translation
ms.sourcegitcommit: 213ce098bcc2b5e31c55e759d895254d5ca33caa
ms.openlocfilehash: 51655742b8975c84eae3f58c4ef0f7381a0bed6b

---

# <a name="using-objects-that-implement-idisposable"></a>IDisposable을 구현하는 개체 사용

공통 언어 런타임의 가비지 수집기가 관리되지 않는 개체에서 사용하는 메모리를 회수하지만, 관리되지 않는 리소스를 사용하는 유형에서는 이 관리되지 않는 메모리를 회수할 수 있도록 [IDisposable](xref:System.IDisposable) 인터페이스를 구현합니다. [IDisposable](xref:System.IDisposable)을 구현하는 개체의 사용을 마치면 해당 개체의 [IDisposable.Dispose](xref:System.IDisposable.Dispose) 구현을 호출해야 합니다. 이 작업은 다음 두 가지 방법 중 하나로 수행할 수 있습니다.

* C# `using` 문 또는 Visual Basic `Using` 문 사용

* `try/finally` 블록 구현 

## <a name="the-using-statement"></a>using 문

C#의 `using` 문과 Visual Basic의 `Using` 문은 개체를 만들고 정리하기 위해 작성해야 하는 코드를 단순화합니다. `using` 문은 하나 이상의 리소스를 가져와서, 사용자가 지정하는 문을 실행한 다음, 개체를 자동으로 삭제합니다. 그러나 `using` 문은 개체가 생성된 메서드의 범위 내에서 사용되는 개체에만 유용합니다. 

다음 예제에서는 `using` 문을 사용하여 [System.IO.StreamReader](xref:System.IO.StreamReader) 개체를 만들고 해제합니다.

```cs
using System;
using System.IO;

public class Example
{
   public static void Main()
   {
      Char[] buffer = new Char[50];
      using (StreamReader s = new StreamReader("File1.txt")) {
         int charsRead = 0;
         while (s.Peek() != -1) {
            charsRead = s.Read(buffer, 0, buffer.Length);
            //
            // Process characters read.
            //   
         }
         s.Close();    
      }

   }
}
```

```vb
Imports System.IO

Module Example
   Public Sub Main()
      Dim buffer(49) As Char
      Using s As New StreamReader("File1.txt")
         Dim charsRead As Integer
         Do While s.Peek() <> -1
            charsRead = s.Read(buffer, 0, buffer.Length)         
            ' 
            ' Process characters read.
            '
         Loop
         s.Close()
      End Using
   End Sub
End Module
```

[StreamReader](xref:System.IO.StreamReader) 클래스가 [IDisposable](xref:System.IDisposable) 인터페이스를 구현하며, 이는 관리되지 않는 리소스를 사용함을 나타내지만 예제에서는 [StreamReader.Dispose](xref:System.IO.StreamReader.Dispose(System.Boolean)) 메서드를 명시적으로 호출하지 않습니다. C# 또는 Visual Basic 컴파일러가 `using` 문을 발견하면 `try/finally` 블록을 명시적으로 포함하는 다음 코드와 동일한 중간 언어(IL)를 표시합니다. 

```cs
using System;
using System.IO;

public class Example
{
   public static void Main()
   {
      Char[] buffer = new Char[50];
      {
         StreamReader s = new StreamReader("File1.txt"); 
         try {
            int charsRead = 0;
            while (s.Peek() != -1) {
               charsRead = s.Read(buffer, 0, buffer.Length);
               //
               // Process characters read.
               //   
            }
            s.Close();
         }
         finally {
            if (s != null)
               ((IDisposable)s).Dispose();     
         }       
      }
   }
}
```

```vb
Imports System.IO

Module Example
   Public Sub Main()
      Dim buffer(49) As Char
''      Dim s As New StreamReader("File1.txt")
With s As New StreamReader("File1.txt")
      Try
         Dim charsRead As Integer
         Do While s.Peek() <> -1
            charsRead = s.Read(buffer, 0, buffer.Length)         
            ' 
            ' Process characters read.
            '
         Loop
         s.Close()
      Finally
         If s IsNot Nothing Then DirectCast(s, IDisposable).Dispose()
      End Try
End With
   End Sub
End Module
```

또한 C# `using` 문을 사용하면 단일 문으로 여러 리소스를 가져올 수 있으며, 이는 중첩된 using 문의 기능과 내부적으로 동일합니다. 다음 예제에서는 서로 다른 두 파일의 내용을 읽을 수 있도록 두 개의 [StreamReader](xref:System.IO.StreamReader) 개체를 인스턴스화합니다. 

```cs
using System;
using System.IO;

public class Example
{
   public static void Main()
   {
      Char[] buffer1 = new Char[50], buffer2 = new Char[50];

      using (StreamReader version1 = new StreamReader("file1.txt"),
                          version2 = new StreamReader("file2.txt")) {
         int charsRead1, charsRead2 = 0;
         while (version1.Peek() != -1 && version2.Peek() != -1) {
            charsRead1 = version1.Read(buffer1, 0, buffer1.Length);
            charsRead2 = version2.Read(buffer2, 0, buffer2.Length);
            //
            // Process characters read.
            //
         }
         version1.Close();
         version2.Close();
      }
   }
}
```

## <a name="tryfinally-block"></a>Try/finally 블록

`using` 문에서 `try/finally` 블록을 래핑하는 대신 `try/finally` 블록을 직접 구현하도록 선택할 수 있습니다. 개인적인 코딩 스타일에 따라서 또는 다음 이유 중 하나로 인해 이를 수행할 수 있습니다. 

* `catch` 블록에서 throw된 모든 예외를 처리하기 위해 `try` 블록을 포함하려는 경우 그렇지 않으면, `using` 문에서 throw된 예외가 `try/catch` 블록이 없는 경우 `using` 블록 내에 throw되는 예외와 마찬가지로 처리됩니다. 

* 범위가 선언된 범위 내의 블록에 대해 로컬이 아닌 [IDisposable](xref:System.IDisposable)을 구현하는 개체를 인스턴스화하려는 경우 

`try/catch/finally` 블록을 사용하여 [StreamReader](xref:System.IO.StreamReader) 개체를 인스턴스화, 사용 및 삭제하고 [StreamReader](xref:System.IO.StreamReader) 생성자 및 해당 [ReadToEnd](xref:System.IO.StreamReader.ReadToEnd) 메서드에서 throw된 예외를 처리한다는 점을 제외하면 다음 예제는 이전 예제와 유사합니다. `finally` 블록의 코드가 [Dispose](xref:System.IDisposable.Dispose) 메서드를 호출하기 전에 [IDisposable](xref:System.IDisposable)을 구현하는 개체가 `null`이 아닌지 확인합니다. 이렇게 하지 않으면 런타임에 [NullReferenceException](xref:System.NullReferenceException) 예외가 발생할 수 있습니다. 

```cs
using System;
using System.Globalization;
using System.IO;

public class Example
{
   public static void Main()
   {
      StreamReader sr = null;
      try {
         sr = new StreamReader("file1.txt");
         String contents = sr.ReadToEnd();
         sr.Close();
         Console.WriteLine("The file has {0} text elements.", 
                           new StringInfo(contents).LengthInTextElements);    
      }      
      catch (FileNotFoundException) {
         Console.WriteLine("The file cannot be found.");
      }   
      catch (IOException) {
         Console.WriteLine("An I/O error has occurred.");
      }
      catch (OutOfMemoryException) {
         Console.WriteLine("There is insufficient memory to read the file.");   
      }
      finally {
         if (sr != null) sr.Dispose();     
      }
   }
}
```

```vb
Imports System.Globalization
Imports System.IO

Module Example
   Public Sub Main()
      Dim sr As StreamReader = Nothing
      Try 
         sr = New StreamReader("file1.txt")
         Dim contents As String = sr.ReadToEnd()
         sr.Close()
         Console.WriteLine("The file has {0} text elements.", 
                           New StringInfo(contents).LengthInTextElements)    
      Catch e As FileNotFoundException
         Console.WriteLine("The file cannot be found.")
      Catch e As IOException
         Console.WriteLine("An I/O error has occurred.")
      Catch e As OutOfMemoryException
         Console.WriteLine("There is insufficient memory to read the file.")   
      Finally 
         If sr IsNot Nothing Then sr.Dispose()     
      End Try
   End Sub
End Module
```

프로그래밍 언어가 `using` 문을 지원하지 않지만, [Dispose](xref:System.IDisposable.Dispose) 메서드에 대한 직접 호출을 허용하므로 `try/finally` 블록을 구현하도록 선택하거나 구현해야 하는 경우 이 기본 패턴을 따를 수 있습니다. 

## <a name="see-also"></a>참고 항목

[관리되지 않는 리소스 정리](unmanaged.md)





<!--HONumber=Nov16_HO1-->


