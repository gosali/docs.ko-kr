---
title: "Dispose 메서드 구현"
description: "Dispose 메서드 구현"
keywords: .NET, .NET Core
author: stevehoag
ms.author: shoag
manager: wpickett
ms.date: 08/16/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: eca6cdc3-6a14-4296-86fb-1eb2f21455b0
translationtype: Human Translation
ms.sourcegitcommit: b20713600d7c3ddc31be5885733a1e8910ede8c6
ms.openlocfilehash: fcb7944a7a9cce1cf23b42790133ee051c0e05f0

---

# <a name="implementing-a-dispose-method"></a>Dispose 메서드 구현

응용 프로그램에서 사용하는 관리되지 않는 리소스를 해제하려면 [Dispose](xref:System.IDisposable.Dispose) 메서드를 구현합니다. .NET 가비지 수집기는 관리되지 않는 메모리를 할당하거나 해제하지 않습니다. 

삭제 패턴이라고도 하는 개체 삭제 패턴에서는 개체의 수명에 순서를 적용합니다. 삭제 패턴은 파일 핸들, 파이프 핸들, 레지스트리 핸들, 대기 핸들 또는 관리되지 않는 메모리의 블록에 대한 포인터와 같이 관리되지 않는 리소스에 액세스하는 개체에만 사용됩니다. 이는 가비지 수집기가 사용되지 않은 관리되는 개체를 회수하는 데 매우 효율적이지만, 관리되지 않는 개체는 회수할 수 없기 때문입니다.

삭제 패턴에는 두 가지 변형이 있습니다.

* SafeHandle, 즉 [System.Runtime.InteropServices.SafeHandle](xref:System.Runtime.InteropServices.SafeHandle)에서 파생된 클래스에서 사용하는 형식의 관리되지 않는 리소스마다 각각 래핑합니다. 이 경우 [IDisposable](xref:System.IDisposable) 인터페이스와 추가 `Dispose(Boolean)` 메서드를 구현합니다. 이는 권장되는 변형이며, [Object.Finalize](xref:System.Object.Finalize) 메서드를 재정의할 필요가 없습니다. 

> [!NOTE]
> [Microsoft.Win32.SafeHandles](xref:Microsoft.Win32.SafeHandles) 네임스페이스는 [SafeHandle](xref:System.Runtime.InteropServices.SafeHandle)에서 파생된 클래스 집합을 제공합니다. 이러한 클래스는 [SafeHandle 사용](#using-safe-handles) 섹션에서 나열하고 있습니다. 관리되지 않는 리소스를 해제하는 데 적합한 클래스를 찾을 수 없는 경우 직접 [SafeHandle](xref:System.Runtime.InteropServices.SafeHandle) 서브클래스를 구현할 수 있습니다. 
 
* [IDisposable](xref:System.IDisposable) 인터페이스와 추가 `Dispose(Boolean` 메서드를 구현하고 [Object.Finalize](xref:System.Object.Finalize) 메서드도 재정의합니다. 사용자 지정 형식의 소비자에서 [IDisposable.Dispose](xref:System.IDisposable.Dispose) 구현을 호출하지 않는 경우 관리되지 않는 리소스를 삭제하도록 [Finalize](xref:System.Object.Finalize)를 재정의해야 합니다. 이전 글머리 기호에서 설명된 권장 방법을 사용하는 경우 사용자를 대신하여 [System.Runtime.InteropServices.SafeHandle](xref:System.Runtime.InteropServices.SafeHandle) 클래스에서 이 작업을 수행합니다. 

항상 리소스가 적절히 정리되도록 하려면 예외를 throw하지 않은 채 [Dispose](xref:System.IDisposable.Dispose) 메서드를 여러 번 호출할 수 있어야 합니다. 

[GC.KeepAlive](xref:System.GC.KeepAlive(System.Object)) 메서드의 코드 예제에서는 적극적인 가비지 수집으로 인해 회수되는 개체의 멤버가 아직 실행되고 있어도 종료자를 실행할 수 있게 하는 방법을 보여 줍니다. `Dispose` 메서드를 오래 실행한 후에 [KeepAlive](xref:System.GC.KeepAlive(System.Object)) 메서드를 호출하는 것이 좋습니다.

## <a name="dispose-and-disposeboolean"></a>Dispose() 및 Dispose(Boolean)

[IDisposable](xref:System.IDisposable) 인터페이스에서는 매개 변수가 없는 단일 메서드인 [Dispose](xref:System.IDisposable.Dispose)를 구현해야 합니다. 그러나 삭제 패턴을 사용하려면 다음과 같은 두 가지 `Dispose` 메서드를 구현해야 합니다. 

* 매개 변수가 없는 공용 비가상(Visual Basic의 경우 `NonInheritable`) [IDisposable.Dispose](xref:System.IDisposable.Dispose) 구현

* 시그니처가 다음과 같은 보호된 가상(Visual Basic의 `Overridable`) `Dispose` 메서드:

  ```cs
  protected virtual void Dispose(bool disposing)
  ```

  ```vb
  Protected Overridable Sub Dispose(disposing As Boolean)
  ```

### <a name="the-dispose-overload"></a>Dispose() 오버로드

공용, 비가상(Visual Basic의 경우 `NonInheritable`), 매개 변수가 없는 `Dispose` 메서드는 형식의 소비자에 의해 호출되므로, 관리되지 않는 리소스를 해제하고 종료자(있는 경우)를 실행할 필요가 없음을 나타내기 위한 것입니다. 이로 인해 다음과 같은 표준 구현이 수행됩니다.

```cs
public void Dispose()
{
   // Dispose of unmanaged resources.
   Dispose(true);
   // Suppress finalization.
   GC.SuppressFinalize(this);
}
```

```vb
Public Sub Dispose() _
           Implements IDisposable.Dispose
   ' Dispose of unmanaged resources.
   Dispose(True)
   ' Suppress finalization.
   GC.SuppressFinalize(Me)
End Sub
```

`Dispose` 메서드에서 모든 개체를 정리하므로 가비지 수집기에서 개체의 [Object.Finalize](xref:System.Object.Finalize) 재정의를 더 이상 호출할 필요가 없습니다. 따라서 [GC.SuppressFinalize](xref:System.GC.SuppressFinalize(System.Object)) 메서드를 호출하면 가비지 수집기에서 종료자를 실행하지 않도록 차단합니다. 종료자가 형식에 없는 경우 [SuppressFinalize](xref:System.GC.SuppressFinalize(System.Object))를 호출하더라도 아무런 영향을 미치지 않습니다. `Dispose` 메서드의 두 번째 오버로드에서 실제로 관리되지 않는 리소스를 해제하는 작업이 수행됩니다.

### <a name="the-disposeboolean-overload"></a>Dispose(Boolean) 오버로드

두 번째 오버로드에서 메서드 호출이 [Dispose](xref:System.IDisposable.Dispose)(`true` 값)에서 나오는지 또는 종료자(`false` 값)에서 나오는지를 나타내는 [Boolean](xref:System.Boolean)이 *disposing* 매개 변수입니다. 

메서드 본문은 다음과 같은 두 가지 코드 블록으로 구성됩니다. 

* 관리되지 않는 리소스를 해제하는 블록. *disposing* 매개 변수의 값에 관계없이 실행되는 블록입니다. 

* 관리되는 리소스를 해제하는 조건부 블록. *disposing* 값이 `true`일 때 실행되는 블록입니다. 해제되는 관리되는 리소스는 다음과 같습니다. 

    **IDisposable을 구현하는 관리되는 개체**. 조건부 블록을 사용하여 [Dispose](xref:System.IDisposable.Dispose) 구현을 호출할 수 있습니다. 관리되지 않는 리소스를 래핑하기 위해 SafeHandle을 사용한 경우에는 여기서 [SafeHandle.Dispose(Boolean](xref:System.Runtime.InteropServices.SafeHandle.Dispose(System.Boolean))) 구현을 호출해야 합니다. 

    **많은 메모리를 사용하거나 부족한 리소스를 사용하는 관리되는 개체.** 이러한 개체를 `Dispose` 메서드에서 명시적으로 해제하면 가비지 수집기에서 명확하지 않게 회수한 경우보다 빠르게 해제할 수 있습니다. 


메서드 호출이 종료자에서 나오는 경우, 즉 *disposing*이 `false`인 경우 관리되지 않는 리소스를 해제하는 코드만 실행됩니다. 종료하는 동안 가비지 수집기가 관리되는 개체를 제거하는 순서가 정의되어 있지 않기 때문에 `Dispose` 값으로 이 `false` 오버로드를 호출하면 종료자가 이미 회수된 관리되는 리소스를 해제하려고 시도하지 못합니다. 

## <a name="implementing-the-dispose-pattern-for-a-base-class"></a>기본 클래스에 대한 삭제 패턴 구현

기본 클래스에 대한 삭제 패턴을 구현하는 경우 다음을 제공해야 합니다. 

> [!IMPORTANT]
> [IDisposable](xref:System.IDisposable)을 구현하고 `sealed`가 아닌 모든 기본 클래스에서 이 패턴을 구현해야 합니다. 
 
* `Dispose(Boolean)` 메서드를 호출하는 [Dispose](xref:System.IDisposable.Dispose) 구현 

* 실제로 리소스를 해제하는 작업을 수행하는 `Dispose(Boolean)` 메서드 

* 관리되지 않는 리소스를 래핑하는 [SafeHandle](xref:System.Runtime.InteropServices.SafeHandle)에서 파생된 클래스(권장) 또는 [Object.Finalize](xref:System.Object.Finalize) 메서드 재정의. [SafeHandle](xref:System.Runtime.InteropServices.SafeHandle) 클래스는 코딩할 필요가 없는 종료자를 제공합니다. 

SafeHandle을 사용하는 기본 클래스에 대한 삭제 패턴을 구현하는 일반적인 패턴은 다음과 같습니다. 

```cs
using Microsoft.Win32.SafeHandles;
using System;
using System.Runtime.InteropServices;

class BaseClass : IDisposable
{
   // Flag: Has Dispose already been called?
   bool disposed = false;
   // Instantiate a SafeHandle instance.
   SafeHandle handle = new SafeFileHandle(IntPtr.Zero, true);

   // Public implementation of Dispose pattern callable by consumers.
   public void Dispose()
   { 
      Dispose(true);
      GC.SuppressFinalize(this);           
   }

   // Protected implementation of Dispose pattern.
   protected virtual void Dispose(bool disposing)
   {
      if (disposed)
         return; 

      if (disposing) {
         handle.Dispose();
         // Free any other managed objects here.
         //
      }

      // Free any unmanaged objects here.
      //
      disposed = true;
   }
}
```

```vb
Imports Microsoft.Win32.SafeHandles
Imports System.Runtime.InteropServices

Class BaseClass : Implements IDisposable
   ' Flag: Has Dispose already been called?
   Dim disposed As Boolean = False
   ' Instantiate a SafeHandle instance.
   Dim handle As SafeHandle = New SafeFileHandle(IntPtr.Zero, True)

   ' Public implementation of Dispose pattern callable by consumers.
   Public Sub Dispose() _
              Implements IDisposable.Dispose
      Dispose(True)
      GC.SuppressFinalize(Me)           
   End Sub

   ' Protected implementation of Dispose pattern.
   Protected Overridable Sub Dispose(disposing As Boolean)
      If disposed Then Return

      If disposing Then
         handle.Dispose()
         ' Free any other managed objects here.
         '
      End If

      ' Free any unmanaged objects here.
      '
      disposed = True
   End Sub
End Class
```

> [!NOTE] 
> 이전 예제에서는 [SafeFileHandle](xref:Microsoft.Win32.SafeHandles.SafeFileHandle) 개체를 사용하여 패턴을 보여 줍니다. [SafeHandle](xref:System.Runtime.InteropServices.SafeHandle)에서 파생된 개체를 대신 사용할 수도 있습니다. 예제에서는 해당 [SafeFileHandle](xref:Microsoft.Win32.SafeHandles.SafeFileHandle) 개체를 제대로 인스턴스화하지 않습니다. 
 
[Object.Finalize](xref:System.Object.Finalize)를 재정의하는 기본 클래스의 삭제 패턴을 구현하는 일반적인 패턴은 다음과 같습니다. 

```cs
using System;

class BaseClass : IDisposable
{
   // Flag: Has Dispose already been called?
   bool disposed = false;

   // Public implementation of Dispose pattern callable by consumers.
   public void Dispose()
   { 
      Dispose(true);
      GC.SuppressFinalize(this);           
   }

   // Protected implementation of Dispose pattern.
   protected virtual void Dispose(bool disposing)
   {
      if (disposed)
         return; 

      if (disposing) {
         // Free any other managed objects here.
         //
      }

      // Free any unmanaged objects here.
      //
      disposed = true;
   }

   ~BaseClass()
   {
      Dispose(false);
   }
}
```

```vb
Class BaseClass : Implements IDisposable
   ' Flag: Has Dispose already been called?
   Dim disposed As Boolean = False

   ' Public implementation of Dispose pattern callable by consumers.
   Public Sub Dispose() _
              Implements IDisposable.Dispose
      Dispose(True)
      GC.SuppressFinalize(Me)           
   End Sub

   ' Protected implementation of Dispose pattern.
   Protected Overridable Sub Dispose(disposing As Boolean)
      If disposed Then Return

      If disposing Then
         ' Free any other managed objects here.
         '
      End If

      ' Free any unmanaged objects here.
      '
      disposed = True
   End Sub

   Protected Overrides Sub Finalize()
      Dispose(False)      
   End Sub
End Class
```

> [!NOTE]
> C#의 경우 `destructor`를 정의하여 [Object.Finalize](xref:System.Object.Finalize)를 재정의합니다. 


## <a name="implementing-the-dispose-pattern-for-a-derived-class"></a>파생된 클래스에 대한 삭제 패턴 구현

파생된 클래스에서 [IDisposable.Dispose](xref:System.IDisposable.Dispose)의 기본 클래스 구현을 상속하므로 [IDisposable](xref:System.IDisposable) 인터페이스를 구현하는 클래스로부터 파생된 클래스에서 [IDisposable](xref:System.IDisposable)을 구현하지 않아야 합니다. 대신 파생된 클래스에 대한 삭제 패턴을 구현하려면 다음을 제공합니다. 

* 기본 클래스 메서드를 재정의하고 파생된 클래스의 리소스를 해제하는 실제 작업을 수행하는 `protected Dispose(Boolean)` 메서드. 이 메서드도 기본 클래스의 `Dispose(Boolean)` 메서드를 호출하여 *disposing* 인수의 `true` 값을 전달합니다. 

* 관리되지 않는 리소스를 래핑하는 [SafeHandle](xref:System.Runtime.InteropServices.SafeHandle)에서 파생된 클래스(권장) 또는 [Object.Finalize](xref:System.Object.Finalize) 메서드 재정의. [SafeHandle](xref:System.Runtime.InteropServices.SafeHandle) 클래스는 코딩할 필요가 없는 종료자를 제공합니다. 종료자를 제공하는 경우 *disposing* 인수가 `false`인 `Dispose(Boolean)` 오버로드를 호출해야 합니다. 

SafeHandle을 사용하는 파생된 클래스에 대한 삭제 패턴을 구현하는 일반적인 패턴은 다음과 같습니다. 

```cs
using Microsoft.Win32.SafeHandles;
using System;
using System.Runtime.InteropServices;

class DerivedClass : BaseClass
{
   // Flag: Has Dispose already been called?
   bool disposed = false;
   // Instantiate a SafeHandle instance.
   SafeHandle handle = new SafeFileHandle(IntPtr.Zero, true);

   // Protected implementation of Dispose pattern.
   protected override void Dispose(bool disposing)
   {
      if (disposed)
         return; 

      if (disposing) {
         handle.Dispose();
         // Free any other managed objects here.
         //
      }

      // Free any unmanaged objects here.
      //

      disposed = true;
      // Call base class implementation.
      base.Dispose(disposing);
   }
}
```

```vb
Imports Microsoft.Win32.SafeHandles
Imports System.Runtime.InteropServices

Class DerivedClass : Inherits BaseClass 
   ' Flag: Has Dispose already been called?
   Dim disposed As Boolean = False
   ' Instantiate a SafeHandle instance.
   Dim handle As SafeHandle = New SafeFileHandle(IntPtr.Zero, True)

   ' Protected implementation of Dispose pattern.
   Protected Overrides Sub Dispose(disposing As Boolean)
      If disposed Then Return

      If disposing Then
         handle.Dispose()
         ' Free any other managed objects here.
         '
      End If

      ' Free any unmanaged objects here.
      '
      disposed = True

      ' Call base class implementation.
      MyBase.Dispose(disposing)
   End Sub
End Class
```

> [!NOTE] 
> 이전 예제에서는 [SafeFileHandle](xref:Microsoft.Win32.SafeHandles.SafeFileHandle) 개체를 사용하여 패턴을 보여 줍니다. [SafeHandle](xref:System.Runtime.InteropServices.SafeHandle)에서 파생된 개체를 대신 사용할 수도 있습니다. 예제에서는 해당 [SafeFileHandle](xref:Microsoft.Win32.SafeHandles.SafeFileHandle) 개체를 제대로 인스턴스화하지 않습니다. 

[Object.Finalize](xref:System.Object.Finalize)를 재정의하는 파생된 클래스의 삭제 패턴을 구현하는 일반적인 패턴은 다음과 같습니다.

```cs
using System;

class DerivedClass : BaseClass
{
   // Flag: Has Dispose already been called?
   bool disposed = false;

   // Protected implementation of Dispose pattern.
   protected override void Dispose(bool disposing)
   {
      if (disposed)
         return; 

      if (disposing) {
         // Free any other managed objects here.
         //
      }

      // Free any unmanaged objects here.
      //
      disposed = true;

      // Call the base class implementation.
      base.Dispose(disposing);
   }

   ~DerivedClass()
   {
      Dispose(false);
   }
}
```

```vb
Class DerivedClass : Inherits BaseClass
   ' Flag: Has Dispose already been called?
   Dim disposed As Boolean = False

   ' Protected implementation of Dispose pattern.
   Protected Overrides Sub Dispose(disposing As Boolean)
      If disposed Then Return

      If disposing Then
         ' Free any other managed objects here.
         '
      End If

      ' Free any unmanaged objects here.
      '
      disposed = True

      ' Call the base class implementation.
      MyBase.Dispose(disposing)
   End Sub

   Protected Overrides Sub Finalize()
      Dispose(False)
   End Sub 
End Class
```

> [!NOTE]
> C#의 경우 `destructor`를 정의하여 [Object.Finalize](xref:System.Object.Finalize)를 재정의합니다.

## <a name="using-safe-handles"></a>SafeHandle 사용

개체 종료자에 대한 코드를 작성하는 작업은 올바르게 수행되지 않을 경우 문제를 일으킬 수 있는 복잡한 작업입니다. 따라서 종료자를 구현하는 대신 [System.Runtime.InteropServices.SafeHandle](xref:System.Runtime.InteropServices.SafeHandle) 개체를 생성하는 것이 좋습니다.

[System.Runtime.InteropServices.SafeHandle](xref:System.Runtime.InteropServices.SafeHandle) 클래스에서 파생된 클래스는 중단 없이 핸들을 할당하고 해제하여 개체 수명 문제를 단순화합니다. 여기에는 응용 프로그램 도메인을 언로드하는 동안 실행이 보장되는 중요한 종료자가 포함됩니다. [Microsoft.Win32.SafeHandles](xref:Microsoft.Win32.SafeHandles) 네임스페이스에서 SafeHandle을 제공하는 파생된 클래스는 다음과 같습니다. 

* 파일, 메모리 매핑된 파일 및 파이프에 대한 [SafeFileHandle](xref:Microsoft.Win32.SafeHandles.SafeFileHandle), [SafeMemoryMappedFileHandle](xref:Microsoft.Win32.SafeHandles.SafeMemoryMappedFileHandle) 및 [SafePipeHandle](xref:Microsoft.Win32.SafeHandles.SafePipeHandle) 클래스 

* 메모리 뷰에 대한 [SafeMemoryMappedViewHandle](xref:Microsoft.Win32.SafeHandles.SafeMemoryMappedViewHandle) 클래스 

* 암호화 구문에 대한 [SafeNCryptKeyHandle](https://msdn.microsoft.com/en-us/library/microsoft.win32.safehandles.safencryptkeyhandle(v=vs.110).aspx), [SafeNCryptProviderHandle](https://msdn.microsoft.com/en-us/library/microsoft.win32.safehandles.safencryptproviderhandle(v=vs.110).aspx) 및 [SafeNCryptSecretHandle](https://msdn.microsoft.com/en-us/library/microsoft.win32.safehandles.safencryptsecrethandle(v=vs.110).aspx) 클래스

* 레지스트리 키에 대한 [SafeRegistryHandle](xref:Microsoft.Win32.SafeHandles.SafeRegistryHandle) 클래스 

* 대기 핸들에 대한 [SafeWaitHandle](xref:Microsoft.Win32.SafeHandles.SafeWaitHandle) 클래스 

## <a name="using-a-safe-handle-to-implement-the-dispose-pattern-for-a-base-class"></a>SafeHandle을 사용하여 기본 클래스에 대한 삭제 패턴 구현

다음 예제는 SafeHandle을 사용하여 관리되지 않는 리소스를 캡슐화하는 기본 클래스에 대한 삭제 패턴인 `DisposableStreamResource`를 보여 줍니다. 이는 [SafeFileHandle](xref:Microsoft.Win32.SafeHandles.SafeFileHandle)을 사용하여 열려 있는 파일을 나타내는 [Stream](xref:System.IO.Stream) 개체를 래핑하는 `DisposableResource` 클래스를 정의합니다. 또한 `DisposableResource` 메서드에는 파일 스트림에서 총 바이트 수를 반환하는 단일 속성인 `Size`도 포함됩니다. 

```cs
using Microsoft.Win32.SafeHandles;
using System;
using System.IO;
using System.Runtime.InteropServices;

public class DisposableStreamResource : IDisposable
{
   // Define constants.
   protected const uint GENERIC_READ = 0x80000000;
   protected const uint FILE_SHARE_READ = 0x00000001;
   protected const uint OPEN_EXISTING = 3;
   protected const uint FILE_ATTRIBUTE_NORMAL = 0x80;
   protected IntPtr INVALID_HANDLE_VALUE = new IntPtr(-1);
   private const int INVALID_FILE_SIZE = unchecked((int) 0xFFFFFFFF);

   // Define Windows APIs.
   [DllImport("kernel32.dll", EntryPoint = "CreateFileW", CharSet = CharSet.Unicode)]
   protected static extern IntPtr CreateFile (
                                  string lpFileName, uint dwDesiredAccess, 
                                  uint dwShareMode, IntPtr lpSecurityAttributes, 
                                  uint dwCreationDisposition, uint dwFlagsAndAttributes, 
                                  IntPtr hTemplateFile);

   [DllImport("kernel32.dll")]
   private static extern int GetFileSize(SafeFileHandle hFile, out int lpFileSizeHigh);

   // Define locals.
   private bool disposed = false;
   private SafeFileHandle safeHandle; 
   private long bufferSize;
   private int upperWord;

   public DisposableStreamResource(string filename)
   {
      if (filename == null)
         throw new ArgumentNullException("The filename cannot be null.");
      else if (filename == "")
         throw new ArgumentException("The filename cannot be an empty string.");

      IntPtr handle = CreateFile(filename, GENERIC_READ, FILE_SHARE_READ,
                                 IntPtr.Zero, OPEN_EXISTING, FILE_ATTRIBUTE_NORMAL,
                                 IntPtr.Zero);
      if (handle != INVALID_HANDLE_VALUE)
         safeHandle = new SafeFileHandle(handle, true);
      else
         throw new FileNotFoundException(String.Format("Cannot open '{0}'", filename));

      // Get file size.
      bufferSize = GetFileSize(safeHandle, out upperWord); 
      if (bufferSize == INVALID_FILE_SIZE)
         bufferSize = -1;
      else if (upperWord > 0) 
         bufferSize = (((long)upperWord) << 32) + bufferSize;
   }

   public long Size 
   { get { return bufferSize; } }

   public void Dispose()
   {
      Dispose(true);
      GC.SuppressFinalize(this);
   }           

   protected virtual void Dispose(bool disposing)
   {
      if (disposed) return;

      // Dispose of managed resources here.
      if (disposing)
         safeHandle.Dispose();

      // Dispose of any unmanaged resources not wrapped in safe handles.

      disposed = true;
   }  
}
```

```vb
Imports Microsoft.Win32.SafeHandles
Imports System.IO

Public Class DisposableStreamResource : Implements IDisposable
   ' Define constants.
   Protected Const GENERIC_READ As UInteger = &H80000000ui
   Protected Const FILE_SHARE_READ As UInteger = &H0000000i
   Protected Const OPEN_EXISTING As UInteger = 3
   Protected Const FILE_ATTRIBUTE_NORMAL As UInteger = &H80
   Protected INVALID_HANDLE_VALUE As New IntPtr(-1)
   Private Const INVALID_FILE_SIZE As Integer = &HFFFFFFFF

   ' Define Windows APIs.
   Protected Declare Function CreateFile Lib "kernel32" Alias "CreateFileA" (
                                         lpFileName As String, dwDesiredAccess As UInt32, 
                                         dwShareMode As UInt32, lpSecurityAttributes As IntPtr, 
                                         dwCreationDisposition As UInt32, dwFlagsAndAttributes As UInt32, 
                                         hTemplateFile As IntPtr) As IntPtr
   Private Declare Function GetFileSize Lib "kernel32" (hFile As SafeFileHandle, 
                                                        ByRef lpFileSizeHigh As Integer) As Integer

   ' Define locals.
   Private disposed As Boolean = False
   Private safeHandle As SafeFileHandle 
   Private bufferSize As Long 
   Private upperWord As Integer

   Public Sub New(filename As String)
      If filename Is Nothing Then
         Throw New ArgumentNullException("The filename cannot be null.")
      Else If filename = ""
         Throw New ArgumentException("The filename cannot be an empty string.")
      End If

      Dim handle As IntPtr = CreateFile(filename, GENERIC_READ, FILE_SHARE_READ,
                                        IntPtr.Zero, OPEN_EXISTING, FILE_ATTRIBUTE_NORMAL,
                                        IntPtr.Zero)
      If handle <> INVALID_HANDLE_VALUE Then
         safeHandle = New SafeFileHandle(handle, True)
      Else
         Throw New FileNotFoundException(String.Format("Cannot open '{0}'", filename))
      End If

      ' Get file size.
      bufferSize = GetFileSize(safeHandle, upperWord) 
      If bufferSize = INVALID_FILE_SIZE Then
         bufferSize = -1
      Else If upperWord > 0 Then 
         bufferSize = (CLng(upperWord) << 32) + bufferSize
      End If     
   End Sub

   Public ReadOnly Property Size As Long
      Get
         Return bufferSize
      End Get
   End Property

   Public Sub Dispose() _
              Implements IDisposable.Dispose
      Dispose(True)
      GC.SuppressFinalize(Me)
   End Sub           

   Protected Overridable Sub Dispose(disposing As Boolean)
      If disposed Then Exit Sub

      ' Dispose of managed resources here.
      If disposing Then
         safeHandle.Dispose()
      End If

      ' Dispose of any unmanaged resources not wrapped in safe handles.

      disposed = True
   End Sub  
End Class
```

## <a name="using-a-safe-handle-to-implement-the-dispose-pattern-for-a-derived-class"></a>SafeHandle을 사용하여 파생된 클래스에 대한 삭제 패턴 구현

다음 예제는 이전 예제에 제공된 `DisposableStreamResource2` 클래스에서 상속되는 파생된 클래스에 대한 삭제 패턴인 `DisposableStreamResource`를 보여 줍니다. 클래스에서 `WriteFileInfo` 추가 메서드를 추가하고 [SafeFileHandle](xref:Microsoft.Win32.SafeHandles.SafeFileHandle) 개체를 사용하여 쓰기 가능 파일의 핸들을 래핑합니다. 

```cs
using Microsoft.Win32.SafeHandles;
using System;
using System.IO;
using System.Runtime.InteropServices;
using System.Threading;

public class DisposableStreamResource2 : DisposableStreamResource
{
   // Define additional constants.
   protected const uint GENERIC_WRITE = 0x40000000; 
   protected const uint OPEN_ALWAYS = 4;

   // Define additional APIs.
   [DllImport("kernel32.dll")]   
   protected static extern bool WriteFile(
                                SafeFileHandle safeHandle, string lpBuffer, 
                                int nNumberOfBytesToWrite, out int lpNumberOfBytesWritten,
                                IntPtr lpOverlapped);

   // Define locals.
   private bool disposed = false;
   private string filename;
   private bool created = false;
   private SafeFileHandle safeHandle;

   public DisposableStreamResource2(string filename) : base(filename)
   {
      this.filename = filename;
   }

   public void WriteFileInfo()
   { 
      if (! created) {
         IntPtr hFile = CreateFile(xref:".\FileInfo.txt", GENERIC_WRITE, 0, 
                                   IntPtr.Zero, OPEN_ALWAYS, 
                                   FILE_ATTRIBUTE_NORMAL, IntPtr.Zero);
         if (hFile != INVALID_HANDLE_VALUE)
            safeHandle = new SafeFileHandle(hFile, true);
         else
            throw new IOException("Unable to create output file.");

         created = true;
      }

      string output = String.Format("{0}: {1:N0} bytes\n", filename, Size);
      int bytesWritten;
      bool result = WriteFile(safeHandle, output, output.Length, out bytesWritten, IntPtr.Zero);                                     
   }

   protected new virtual void Dispose(bool disposing)
   {
      if (disposed) return;

      // Release any managed resources here.
      if (disposing)
         safeHandle.Dispose();

      disposed = true;

      // Release any unmanaged resources not wrapped by safe handles here.

      // Call the base class implementation.
      base.Dispose(true);
   }
}
```

```vb
Imports Microsoft.Win32.SafeHandles
Imports System.IO

Public Class DisposableStreamResource2 : Inherits DisposableStreamResource
   ' Define additional constants.
   Protected Const GENERIC_WRITE As Integer = &H40000000 
   Protected Const OPEN_ALWAYS As Integer = 4

   ' Define additional APIs.
   Protected Declare Function WriteFile Lib "kernel32.dll" (
                              safeHandle As SafeFileHandle, lpBuffer As String, 
                              nNumberOfBytesToWrite As Integer, ByRef lpNumberOfBytesWritten As Integer,
                              lpOverlapped As Object) As Boolean

   ' Define locals.
   Private disposed As Boolean = False
   Private filename As String
   Private created As Boolean = False
   Private safeHandle As SafeFileHandle

   Public Sub New(filename As String)
      MyBase.New(filename)
      Me.filename = filename
   End Sub

   Public Sub WriteFileInfo() 
      If Not created Then
         Dim hFile As IntPtr = CreateFile(".\FileInfo.txt", GENERIC_WRITE, 0, 
                                          IntPtr.Zero, OPEN_ALWAYS, 
                                          FILE_ATTRIBUTE_NORMAL, IntPtr.Zero)
         If hFile <> INVALID_HANDLE_VALUE Then
            safeHandle = New SafeFileHandle(hFile, True)
         Else
            Throw New IOException("Unable to create output file.")
         End If
         created = True
      End If
      Dim output As String = String.Format("{0}: {1:N0} bytes {2}", filename, Size, 
                                           vbCrLf)
      WriteFile(safeHandle, output, output.Length, 0&, Nothing)                                     
   End Sub

   Protected Overloads Overridable Sub Dispose(disposing As Boolean)
      If disposed Then Exit Sub

      ' Release any managed resources here.
      If disposing Then
         safeHandle.Dispose()
      End If

      disposed = True
      ' Release any unmanaged resources not wrapped by safe handles here.

      ' Call the base class implementation.
      MyBase.Dispose(True)
   End Sub
End Class
```

## <a name="see-also"></a>참고 항목

[SuppressFinalize](xref:System.GC.SuppressFinalize(System.Object))

[IDisposable](xref:System.IDisposable)

[IDisposable.Dispose](xref:System.IDisposable.Dispose)

[Microsoft.Win32.SafeHandles](xref:Microsoft.Win32.SafeHandles)

[System.Runtime.InteropServices.SafeHandle](xref:System.Runtime.InteropServices.SafeHandle)

[IDisposable.Dispose](xref:System.IDisposable.Dispose)



<!--HONumber=Nov16_HO3-->


