---
title: "기본 상호 운용성"
description: "기본 상호 운용성"
keywords: .NET, .NET Core
author: blackdwarf
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 3c357112-35fb-44ba-a07b-6a1c140370ac
translationtype: Human Translation
ms.sourcegitcommit: 3aeaba5c8cf800c652941b5e6c2bc9f072849893
ms.openlocfilehash: 36041eda54290484741c375ae776b7bf1a74d7a1

---

# <a name="native-interoperability"></a>기본 상호 운용성

이 문서에서는 .NET 플랫폼에서 사용할 수 있는 "기본 상호 운용성"을 수행하는 세 가지 방법을 좀 더 살펴보겠습니다.

네이티브 코드를 호출하려는 이유 중 몇 가지는 다음과 같습니다.

*   운영 체제에서 관리되는 클래스 라이브러리에 없는 많은 API를 제공합니다. 이러한 경우의 대표적인 예는 하드웨어 또는 운영 체제 관리 기능 액세스입니다.
*   C 스타일 ABI(네이티브 ABI)를 포함하거나 생성할 수 있는 다른 구성 요소와 통신합니다. 예를 들어 [JNI(Java 기본 인터페이스)](http://docs.oracle.com/javase/8/docs/technotes/guides/jni/)를 통해 표시되는 Java 코드 또는 기본 구성 요소를 생성할 수 있는 다른 모든 관리되는 언어가 여기에 포함됩니다.
*   Windows에서는 Microsoft Office 제품군 등 설치되는 대부분의 소프트웨어가 해당 프로그램을 나타내며 개발자가 자동화하거나 사용할 수 있도록 하는 COM 구성 요소를 등록합니다. 이 경우 기본 상호 운용성도 필요합니다.

물론, 개발자가 기본 구성 요소를 조작하려 하거나 조작해야 하는 모든 잠재적인 상황 및 시나리오가 위 목록에 포함된 것은 아닙니다. 예를 들어 .NET 클래스 라이브러리는 기본 상호 운용성 지원을 사용하여 콘솔 지원 및 조작, 파일 시스템 액세스 등의 많은 API를 구현합니다. 그러나 필요한 경우 한 가지 옵션이 있다는 것에 유의해야 합니다.

> [!NOTE]
> 이 문서의 예제는 대부분 .NET Core에 지원되는 세 가지 플랫폼(Windows, Linux 및 macOS)에 대해 모두 제공됩니다. 그러나 간략하고 명확한 일부 예제의 경우 Windows 파일 이름과 확장명(즉, 라이브러리의 경우 "dll")을 사용하는 한 가지 샘플만 표시됩니다. Linux 또는 macOS에서 해당 기능을 사용할 수 없다는 의미는 아니며, 단지 편의를 위한 것입니다.

## <a name="platform-invoke-pinvoke"></a>P/Invoke(플랫폼 호출)

P/Invoke는 관리 코드에서 관리되지 않는 라이브러리의 구조체, 콜백 및 함수에 액세스할 수 있는 기술입니다. P/Invoke API는 대부분 `System` 및 `System.Runtime.InteropServices`라는 두 네임스페이스에 포함되어 있습니다. 이러한 두 네임스페이스를 사용하면 기본 구성 요소와 통신하는 방법을 설명하는 특성에 액세스할 수 있습니다.

관리 코드에서 관리되지 않는 함수를 호출하는 가장 일반적인 예제에서 시작하겠습니다. 명령줄 응용 프로그램에서 메시지 상자를 표시하겠습니다.

```cs
using System.Runtime.InteropServices;

public class Program {

    // Import user32.dll (containing the function we need) and define
    // the method corresponding to the native function.
    [DllImport("user32.dll")]
    public static extern int MessageBox(IntPtr hWnd, String text, String caption, int options);

    public static void Main(string[] args) {
        // Invoke the function as a regular managed method.
        MessageBox(IntPtr.Zero, "Command-line message box", "Attention!", 0);
    }
}

```

위의 예제는 상당히 간단하지만 관리 코드에서 관리되지 않는 함수를 호출하는 데 필요한 사항을 보여 줍니다. 예제를 단계별로 살펴보겠습니다.

*   줄 #1에서는 필요한 항목이 모두 포함되어 있는 네임스페이스인 `System.Runtime.InteropServices`에 대한 using 문을 보여 줍니다.
*   줄 #5에서는 `DllImport` 특성을 도입합니다. 이 특성은 관리되지 않는 DLL을 로드하도록 런타임에 지정하므로 중요합니다. 이 DLL을 호출하려고 합니다.
*   줄 #6은 P/Invoke 작업의 핵심입니다. 관리되지 않는 메서드와 **동일한 시그니처**가 있는 관리되는 메서드를 정의합니다. 선언에서 확인할 수 있는 새 키워드 `extern`은 이 메서드가 외부 메서드이며 호출 시 런타임이 `DllImport` 특성에 지정된 DLL에서 찾도록 런타임에 지정합니다.

예제의 나머지 부분에서는 단순히 다른 관리되는 메서드와 마찬가지로 메서드를 호출합니다.

macOS에 대한 샘플도 이와 비슷합니다. 물론 macOS에서는 다른 동적 라이브러리 명명 체계를 사용하므로 `DllImport` 특성의 라이브러리 이름을 변경해야 합니다. 아래 샘플에서는 `getpid(2)` 함수를 사용하여 응용 프로그램의 프로세스 ID를 가져오고 콘솔에 출력합니다.

```cs
using System;
using System.Runtime.InteropServices;

namespace PInvokeSamples {
    public static class Program {

        // Import the libc and define the method corresponding to the native function.
        [DllImport("libSystem.dylib")]
        private static extern int getpid();

        public static void Main(string[] args){
            // Invoke the function and get the process ID.
            int pid = getpid();
            Console.WriteLine(pid);
        }
    }
}

```

물론 Linux에서도 이와 비슷합니다. `getpid(2)`는 [POSIX](https://en.wikipedia.org/wiki/POSIX) 시스템 호출이기 때문에 함수 이름이 같습니다.

```cs
using System;
using System.Runtime.InteropServices;

namespace PInvokeSamples {
    public static class Program {

        // Import the libc and define the method corresponding to the native function.
        [DllImport("libc.so.6")]
        private static extern int getpid();

        public static void Main(string[] args){
            // Invoke the function and get the process ID.
            int pid = getpid();
            Console.WriteLine(pid);
        }
    }
}

```

### <a name="invoking-managed-code-from-unmanaged-code"></a>비관리 코드에서 관리 코드 호출

물론, 런타임에서 양방향 통신을 허용하므로 함수 포인터를 사용하여 네이티브 함수에서 관리되는 아티팩트를 호출할 수 있습니다. 관리 코드에서 함수 포인터와 가장 가까운 것은 **대리자**이므로, 대리자를 사용하여 네이티브 코드에서 관리 코드로의 콜백을 허용합니다.

이 기능을 사용하는 방법은 위에서 설명한 관리 코드에서 네이티브 코드로의 프로세스와 비슷합니다. 지정된 콜백에 대해 시그니처와 일치하는 대리자를 정의하고 외부 메서드에 전달합니다. 다른 모든 작업은 런타임에서 수행합니다.

```cs
using System;
using System.Runtime.InteropServices;

namespace ConsoleApplication1 {

    class Program {

        // Define a delegate that corresponds to the unmanaged function.
        delegate bool EnumWC(IntPtr hwnd, IntPtr lParam);

        // Import user32.dll (containing the function we need) and define
        // the method corresponding to the native function.
        [DllImport("user32.dll")]
        static extern int EnumWindows(EnumWC hWnd, IntPtr lParam);

        // Define the implementation of the delegate; here, we simply output the window handle.
        static bool OutputWindow(IntPtr hwnd, IntPtr lParam) {
            Console.WriteLine(hwnd.ToInt64());
            return true;
        }

        static void Main(string[] args) {
            // Invoke the method; note the delegate as a first parameter.
            EnumWindows(OutputWindow, IntPtr.Zero);
        }
    }
}

```

예제를 살펴보기 전에 작업해야 하는 관리되지 않는 함수의 시그니처를 확인하는 것이 좋습니다. 모든 창을 열거하기 위해 호출하려는 함수에는 다음과 같은 시그니처가 있습니다. `BOOL EnumWindows (WNDENUMPROC lpEnumFunc, LPARAM lParam);`

첫 번째 매개 변수는 콜백입니다. 이 콜백에는 다음과 같은 시그니처가 있습니다. `BOOL CALLBACK EnumWindowsProc (HWND hwnd, LPARAM lParam);`

이 점에 유의하여 예제를 살펴보겠습니다.

*   예제의 줄 #8에서는 비관리 코드의 콜백 시그니처와 일치하는 대리자를 정의합니다. 관리 코드에서 `IntPtr`을 사용하여 LPARAM 및 HWND 형식을 나타내는 방법을 확인합니다.
*   줄 #10 및 #11에서는 user32.dll 라이브러리의 `EnumWindows` 함수를 도입합니다.
*   줄 #13-16에서는 대리자를 구현합니다. 이 간단한 예제에서는 단순히 핸들을 콘솔에 출력하려고 합니다.
*   마지막으로, 줄 #19에서는 외부 메서드를 호출하고 대리자를 전달합니다.

Linux 및 macOS 예제는 다음과 같습니다. 해당 예제에서는 C 라이브러리 `libc`에 있는 `ftw` 함수를 사용합니다. 이 함수는 디렉터리 계층 구조를 트래버스하는 데 사용되며, 함수에 대한 포인터를 해당 매개 변수 중 하나로 사용합니다. 이 함수에는 다음과 같은 시그니처가 있습니다. `int (*fn) (const char *fpath, const struct stat *sb, int typeflag)`.

```cs
using System;
using System.Runtime.InteropServices;

namespace PInvokeSamples {
    public static class Program {

            // Define a delegate that has the same signature as the native function.
            delegate int DirClbk(string fName, StatClass stat, int typeFlag);

            // Import the libc and define the method to represent the native function.
            [DllImport("libc.so.6")]
            static extern int ftw(string dirpath, DirClbk cl, int descriptors);

            // Implement the above DirClbk delegate;
            // this one just prints out the filename that is passed to it.
            static int DisplayEntry(string fName, StatClass stat, int typeFlag) {
                    Console.WriteLine(fName);
                    return 0;
            }

            public static void Main(string[] args){
                    // Call the native function.
                    // Note the second parameter which represents the delegate (callback).
                    ftw(".", DisplayEntry, 10);
            }
    }

    // The native callback takes a pointer to a struct. The below class
    // represents that struct in managed code. You can find more information
    // about this in the section on marshalling below.
    [StructLayout(LayoutKind.Sequential)]
    public class StatClass {
            public uint DeviceID;
            public uint InodeNumber;
            public uint Mode;
            public uint HardLinks;
            public uint UserID;
            public uint GroupID;
            public uint SpecialDeviceID;
            public ulong Size;
            public ulong BlockSize;
            public uint Blocks;
            public long TimeLastAccess;
            public long TimeLastModification;
            public long TimeLastStatusChange;
    }
}

```

macOS 예제에서는 동일한 함수를 사용하며, macOS에서 `libc`가 다른 위치에 유지되므로 `DllImport` 특성에 대한 인수만 다릅니다.

```cs
using System;
using System.Runtime.InteropServices;

namespace PInvokeSamples {
        public static class Program {

                // Define a delegate that has the same signature as the native function.
                delegate int DirClbk(string fName, StatClass stat, int typeFlag);

                // Import the libc and define the method to represent the native function.
                [DllImport("libSystem.dylib")]
                static extern int ftw(string dirpath, DirClbk cl, int descriptors);

                // Implement the above DirClbk delegate;
                // this one just prints out the filename that is passed to it.
                static int DisplayEntry(string fName, StatClass stat, int typeFlag) {
                        Console.WriteLine(fName);
                        return 0;
                }

                public static void Main(string[] args){
                        // Call the native function.
                        // Note the second parameter which represents the delegate (callback).
                        ftw(".", DisplayEntry, 10);
                }
        }

        // The native callback takes a pointer to a struct. The below class
        // represents that struct in managed code. You can find more information
        // about this in the section on marshalling below.
        [StructLayout(LayoutKind.Sequential)]
        public class StatClass {
                public uint DeviceID;
                public uint InodeNumber;
                public uint Mode;
                public uint HardLinks;
                public uint UserID;
                public uint GroupID;
                public uint SpecialDeviceID;
                public ulong Size;
                public ulong BlockSize;
                public uint Blocks;
                public long TimeLastAccess;
                public long TimeLastModification;
                public long TimeLastStatusChange;
        }
}

```

위의 두 예제에서는 매개 변수를 사용하며, 두 경우 모두 매개 변수가 관리되는 형식으로 지정됩니다. 런타임에서 "올바른 작업"을 수행하고 다른 쪽의 해당 항목으로 처리합니다. 이 프로세스는 고품질 네이티브 interop 코드를 작성하는 데 매우 중요하므로 런타임에서 형식을 _마샬링_하면 어떻게 되는지를 살펴보겠습니다.

## <a name="type-marshalling"></a>형식 마샬링

**마샬링**은 관리되는 경계를 넘어 네이티브로 변환되거나 그 반대로 변환되어야 할 때 형식을 변환하는 프로세스입니다.

마샬링이 필요한 이유는 관리 코드와 비관리 코드의 형식이 서로 다르기 때문입니다. 예를 들어 관리 코드에서는 `String`을 사용하지만 관리되지 않는 환경에서는 문자열이 유니코드("와이드"), 비유니코드, null 종료, ASCII 등일 수 있습니다. 기본적으로 P/Invoke 하위 시스템은 [MSDN](https://msdn.microsoft.com/library/zah6xy75.aspx)에서 확인할 수 있는 기본 동작에 따라 올바른 작업을 수행하려고 합니다. 그러나 추가 제어가 필요한 경우 `MarshalAs` 특성을 사용하여 관리되지 않는 쪽에서 필요한 형식을 지정할 수 있습니다. 예를 들어 문자열을 null 종료 ANSI 문자열로 보내려는 경우 다음과 같이 할 수 있습니다.

```cs
[DllImport("somenativelibrary.dll"]
static extern int MethodA([MarshalAs(UnmanagedType.LPStr) string parameter);

```

### <a name="marshalling-classes-and-structs"></a>클래스 및 구조체 마샬링

형식 마샬링의 또 다른 측면은 관리되지 않는 메서드에 구조체를 전달하는 방법입니다. 예를 들어 관리되지 않는 일부 메서드의 경우 매개 변수로 구조체가 필요합니다. 이러한 경우 관리되는 부분에서 해당 구조체 또는 클래스를 만들어 매개 변수로 사용해야 합니다. 그러나 클래스 정의만으로는 충분하지 않습니다. 클래스의 필드를 관리되지 않는 구조체에 매핑하는 방법도 마샬러에 지정해야 합니다. 이때 `StructLayout` 특성이 사용됩니다.

```cs
[DllImport("kernel32.dll")]
static extern void GetSystemTime(SystemTime systemTime);

[StructLayout(LayoutKind.Sequential)]
class SystemTime {
    public ushort Year;
    public ushort Month;
    public ushort DayOfWeek;
    public ushort Day;
    public ushort Hour;
    public ushort Minute;
    public ushort Second;
    public ushort Milsecond;
}

public static void Main(string[] args) {
    SystemTime st = new SystemTime();
    GetSystemTime(st);
    Console.WriteLine(st.Year);
}

```

위의 예제에서는 `GetSystemTime()` 함수를 호출하는 간단한 예제를 보여 줍니다. 흥미로운 부분은 줄 4에 있습니다\. 특성이 클래스의 필드를 다른 쪽(비관리)의 구조체에 순차적으로 매핑하도록 지정합니다. 즉, 아래와 같이 관리되지 않는 구조체에 대응해야 하므로 필드의 이름 지정은 중요하지 않고 해당 순서만 중요합니다.

```c
typedef struct _SYSTEMTIME {
  WORD wYear;
  WORD wMonth;
  WORD wDayOfWeek;
  WORD wDay;
  WORD wHour;
  WORD wMinute;
  WORD wSecond;
  WORD wMilliseconds;
} SYSTEMTIME, *PSYSTEMTIME*;

```

이전 예제에서 이와 관련된 Linux 및 macOS 예제를 이미 살펴봤습니다. 해당 예제가 아래에 다시 나와 있습니다.

```cs
[StructLayout(LayoutKind.Sequential)]
public class StatClass {
        public uint DeviceID;
        public uint InodeNumber;
        public uint Mode;
        public uint HardLinks;
        public uint UserID;
        public uint GroupID;
        public uint SpecialDeviceID;
        public ulong Size;
        public ulong BlockSize;
        public uint Blocks;
        public long TimeLastAccess;
        public long TimeLastModification;
        public long TimeLastStatusChange;
}

```

`StatClass` 클래스는 UNIX 시스템의 `stat` 시스템 호출에서 반환되는 구조체를 나타냅니다. 지정된 파일에 대한 정보를 나타냅니다. 위의 클래스는 관리 코드의 stat 구조체 표현입니다. 앞서 말했듯이, 클래스의 필드는 네이티브 구조체와 동일한 순서여야 하며(선택한 UNIX 구현에 대한 기본 페이지에서 확인할 수 있음), 동일한 기본 형식을 사용해야 합니다.

## <a name="more-resources"></a>추가 리소스

*   [PInvoke.net wiki](http://www.pinvoke.net)는 일반적인 Win32 API 및 호출 방법에 대한 정보가 포함된 우수한 Wiki입니다.
*   [MSDN의 P/Invoke](https://msdn.microsoft.com/library/zbz07712.aspx)
*   [P/Invoke에 대한 Mono 설명서](http://www.mono-project.com/docs/advanced/pinvoke/)



<!--HONumber=Nov16_HO3-->


