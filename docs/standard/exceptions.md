---
title: ".NET의 예외 처리 및 Throw"
description: ".NET에서 예외를 사용하는 방법 이해"
keywords: .NET, .NET Core
author: mairaw
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: bf116df6-0042-46bf-be13-b69864816210
translationtype: Human Translation
ms.sourcegitcommit: 9584699ad7e745ae3cb059b1bb8327301c9a3286
ms.openlocfilehash: 0c73fb0a12092877ff5b54221f4a80693d1d1152

---

# <a name="handling-and-throwing-exceptions-in-net"></a>.NET의 예외 처리 및 Throw

응용 프로그램은 실행 중에 발생하는 오류를 일관된 방식으로 처리할 수 있어야 합니다. .NET에서는 일관된 방식으로 응용 프로그램에 오류를 알리기 위한 모델을 제공합니다. .NET 작업은 예외를 throw하여 오류를 나타냅니다.

## <a name="exceptions"></a>예외

예외란 프로그램 실행 중 발생한 모든 오류 상태 또는 예기치 못한 동작입니다. 예외는 사용 중인 코드 또는 호출한 코드(예: 공유 라이브러리)의 오류, 사용 불가능한 운영 체제 리소스, 런타임에서 발생한 예기치 못한 상황(예: 확인할 수 없는 코드) 등에 의해 throw될 수 있습니다. 사용 중인 응용 프로그램이 이러한 일부 상황으로부터 복구될 수 있지만 복구될 수 없는 경우도 있습니다. 대부분의 응용 프로그램 예외로부터는 복구할 수 있지만 대부분의 런타임 예외로부터는 복구할 수 없습니다.

.NET에서 예외는 [System.Exception](xref:System.Exception) 클래스에서 상속받은 개체입니다. 예외는 문제가 발생한 코드 영역에서 throw됩니다. 예외는 응용 프로그램에서 해당 예외를 처리하거나 프로그램이 종료될 때까지 스택으로 전달됩니다.

## <a name="exceptions-vs-traditional-errorhandling-methods"></a>예외 및 일반적인 오류 처리 방법

일반적으로 언어 오류 처리 모델은 오류를 감지하고 해당 오류 처리기를 찾는 언어 고유의 방식에 의존하거나 운영 체제에서 제공하는 오류 처리 메커니즘에 의존했습니다. .NET에서 예외 처리를 구현하는 방법에는 다음과 같은 이점이 있습니다.

- 예외 throw 및 처리는 .NET 프로그래밍 언어에서 동일하게 작동합니다.

- 예외 처리에 특정한 언어 구문이 필요하지는 않지만, 각 언어에서 고유한 구문을 정의할 수 있도록 허용합니다.

- 프로세스 및 컴퓨터 경계 간에도 예외가 throw될 수 있습니다.

- 예외 처리 코드를 응용 프로그램에 추가하여 프로그램 안정성을 높일 수 있습니다.

예외는 반환 코드 등의 다른 오류 알림 방법에 비해 이점을 제공합니다. 예외가 throw되고 사용자가 처리하지 않을 경우 런타임에서 응용 프로그램을 종료하기 때문에 오류가 발견되지 않습니다. 오류 반환 코드를 검사하지 못한 코드의 결과로 잘못된 값이 시스템 전체에 계속 전파되지 않습니다. 

## <a name="exception-class-and-properties"></a>Exception 클래스 및 속성

@System.Exception 클래스는 예외가 상속받는 기본 클래스입니다. 예를 들어 @System.InvalidCastException 클래스 계층 구조는 다음과 같습니다.

```
Object
  Exception
    SystemException
       InvalidCastException
```

@System.Exception 클래스에는 예외를 보다 쉽게 이해할 수 있도록 하는 다음 속성이 있습니다.

| 속성 이름 | 설명 |
| ------------- | ----------- |
| @System.Exception.Data | 키-값 쌍의 임의의 데이터를 보유하는 @System.Collections.IDictionary. |
| @System.Exception.HelpLink | 예외의 원인에 대한 광범위한 정보를 제공하는 도움말 파일의 URL(또는 URN)을 포함할 수 있습니다. |
| @System.Exception.InnerException | 이 속성을 사용하여 예외 처리 중 일련의 예외를 만들고 유지할 수 있습니다. 이전에 catch된 예외를 포함하는 새 예외를 만드는 데 사용할 수 있습니다. @System.Exception.InnerException 속성의 두 번째 예외에서 원래 예외를 캡처하고 두 번째 예외를 처리하는 코드에서 추가 정보를 검사하도록 허용할 수 있습니다. 예를 들어 부적절한 형식의 인수를 받는 메서드가 있다고 가정해봅시다.  코드에서 인수를 읽으려고 하지만 예외가 throw됩니다. 이 메서드는 예외를 catch하고 @System.FormatException.을 throw합니다. 예외가 throw된 이유를 확인하는 호출자 기능을 개선하기 위해 때로는 메서드가 도우미 루틴에서 throw된 예외를 catch한 다음 발생한 오류를 자세히 나타내는 예외를 throw하는 것이 좋습니다. 보다 의미 있는 새 예외를 만들 수 있으며, 이 경우 내부 예외 참조를 원래 예외로 설정할 수 있습니다. 그런 다음 보다 의미 있는 이 예외를 호출자에게 throw할 수 있습니다. 이 기능을 사용하면 처음 throw된 예외로 끝나는 일련의 연결된 예외를 만들 수 있습니다. |
| @System.Exception.Message | 예외 원인에 대한 정보를 제공합니다.
| @System.Exception.Source | 오류를 발생시키는 응용 프로그램 또는 개체의 이름을 가져오거나 설정합니다. |
| @System.Exception.StackTrace | 오류가 발생한 위치를 확인하는 데 사용할 수 있는 스택 추적을 포함합니다. 디버깅 정보를 사용할 수 있는 경우 스택 추적에는 소스 파일 이름과 프로그램 줄 번호가 포함됩니다. |

@System.Exception에서 상속받는 대부분의 클래스는 추가 멤버를 구현하거나 추가 기능을 제공하지 않습니다. 단순히 @System.Exception.에서 상속받습니다. 따라서 가장 중요한 예외 정보는 예외 클래스, 예외 이름 및 예외에 포함된 정보의 계층 구조에서 확인할 수 있습니다.

@System.Exception,에서 파생된 개체만 throw 및 catch하는 것이 좋지만 @System.Object 클래스에서 파생된 모든 개체를 예외로 throw할 수 있습니다. 일부 언어는 @System.Exception.에서 파생되지 않은 개체의 throw 및 catch를 지원하지 않습니다.

## <a name="common-exceptions"></a>일반적인 예외

다음 표에서는 몇 가지 일반적인 예외 및 예외가 발생할 수 있는 경우의 예를 보여 줍니다.

| 예외 형식 | 기본 형식 | 설명 | 예제 |
| -------------- | --------- | ----------- | ------- |
| @System.Exception | @System.Object | 모든 예외의 기본 클래스. | 없음(이 예외의 파생된 클래스 사용). |
| @System.IndexOutOfRangeException | @System.Exception | 배열이 올바르지 않게 인덱싱된 경우에만 런타임에서 발생됩니다. | 유효 범위를 벗어난 배열 인덱싱: `arr[arr.Length+1]` |
| @System.NullReferenceException | @System.Exception | null 개체가 참조되는 경우에만 런타임에서 발생됩니다. | `object o = null; o.ToString();` |
| @System.InvalidOperationException | @System.Exception | 잘못된 상태에 있는 경우에 메서드에서 발생됩니다. | 기본 컬렉션에서 Item을 제거한 후 `Enumerator.GetNext()` 호출 |
| @System.ArgumentException | @System.Exception | 모든 인수 예외에 대한 기본 클래스. | 없음(이 예외의 파생된 클래스 사용). |
| @System.ArgumentNullException | @System.Exception | 인수에 Null을 허용하지 않는 메서드에서 발생됩니다. | `String s = null; "Calculate".IndexOf (s);` |
| @System.ArgumentOutOfRangeException | @System.Exception | 인수가 지정된 범위에 있는지 확인하는 메서드에서 발생됩니다. | `String s = "string"; s.Substring(s.Length+1);` |

## <a name="how-to-use-the-trycatch-block-to-catch-exceptions"></a>try/catch 블록을 사용하여 예외를 catch하는 방법

예외를 throw할 수 있는 코드 섹션을 `try` 블록에 넣고 예외를 처리하는 코드를 `catch` 블록에 넣습니다. `catch` 블록은 `catch` 키워드로 시작하고 예외 유형 및 수행할 작업으로 이루어진 일련의 문입니다.

다음 코드 예제에서는 `try`/`catch` 블록을 사용하여 가능한 예외를 catch합니다. `Main` 메서드에는 `data.txt`라는 데이터 파일을 열고 파일에서 문자열을 쓰는 @System.IO.StreamReader 문이 포함된 `try` 블록이 있습니다. `try` 블록 뒤에는 `try` 블록에서 발생하는 예외를 catch하는 `catch` 블록이 있습니다.

C#
```
using System;
using System.IO;

public class ProcessFile
{
    public static void Main()
    {
        try
        {
            StreamReader sr = File.OpenText("data.txt");
            Console.WriteLine("The first line of this file is {0}", sr.ReadLine());
            sr.Dispose();
        }
        catch (Exception e)
        {
            Console.WriteLine("An error occurred: '{0}'", e);
        }
    }
}
```

공용 언어 런타임은 catch 블록에서 catch되지 않은 예외를 catch합니다. 런타임의 구성 방법에 따라 디버그 대화 상자가 나타나거나, 프로그램 실행이 중단되고 예외 정보가 포함된 대화 상자가 나타나거나, 오류가 STDERR에 출력됩니다.

> [!NOTE] 
> 거의 모든 코드 줄에서 예외, 특히 @System.OutOfMemoryException.과 같은 공용 언어 런타임에서 throw된 예외가 발생할 수 있습니다. 대부분의 응용 프로그램은 이러한 예외를 처리할 필요가 없지만 다른 사용자가 사용할 라이브러리를 작성하는 경우 이러한 가능성에 유의해야 합니다. Try 블록에서 코드를 설정하는 경우에 대한 제안 사항은 [예외에 대한 모범 사례](#best-practices-for-exceptions)를 참조하세요.
 
## <a name="how-to-use-specific-exceptions-in-a-catch-block"></a>Catch 블록에 특정 예외를 사용하는 방법

앞의 코드 예제에서는 예외를 catch하는 기본 `catch` 문에 대해 설명합니다. 하지만 일반적으로 기본 `catch` 문을 사용하는 대신 특정 형식의 예외를 catch하는 것이 좋은 프로그래밍 방법입니다.

예외가 발생하면 스택 위로 전달되며 각 catch 블록에 예외를 처리할 수 있는 기회가 제공됩니다. catch 문의 순서가 중요합니다. 특정 예외를 대상으로 하는 catch 블록은 일반 예외 catch 블록 앞에 배치합니다. 그러지 않으면 컴파일러에서 오류가 발생할 수 있습니다. 예외 형식을 catch 블록에 지정된 예외 이름과 비교하여 적절한 catch 블록을 확인합니다. 특정 catch 블록이 없는 경우 일반 catch 블록(있는 경우)에서 예외를 catch합니다.

다음 코드 예제에서는 `try`/`catch` 블록을 사용하여 @System.InvalidCastException.을 catch합니다. 샘플에서는 직원 수준(`Emlevel`)이라는 하나의 속성이 있는 `Employee`라는 클래스를 만듭니다. `PromoteEmployee` 메서드는 개체를 사용하고 직원 수준을 증가시킵니다. @System.DateTime 인스턴스가 `PromoteEmployee` 메서드로 전달될 때 @System.InvalidCastException이 발생합니다.

C#
```
using System;

public class Employee
{
    //Create employee level property.
    public int Emlevel
    {
        get
        {
            return(emlevel);
        }
        set
        {
            emlevel = value;
        }
    }

    private int emlevel = 0;
}

public class Ex13
{
    public static void PromoteEmployee(Object emp)
    {
        //Cast object to Employee.
        Employee e = (Employee) emp;
        // Increment employee level.
        e.Emlevel = e.Emlevel + 1;
    }

    public static void Main()
    {
        try
        {
            Object o = new Employee();
            DateTime newyears = new DateTime(2001, 1, 1);
            //Promote the new employee.
            PromoteEmployee(o);
            //Promote DateTime; results in InvalidCastException as newyears is not an employee instance.
            PromoteEmployee(newyears);
        }
        catch (InvalidCastException e)
        {
            Console.WriteLine("Error passing data to PromoteEmployee method. " + e.Message);
        }
    }
}
```

## <a name="how-to-use-finally-blocks"></a>Finally 블록을 사용하는 방법

예외가 발생하면 실행이 중단되고 해당 예외 처리기에 제어가 제공됩니다. 이 경우 대체로 실행될 것으로 예상한 코드 줄을 건너뜁니다. 파일 닫기와 같은 일부 리소스 정리 작업은 예외가 throw된 경우에도 수행해야 합니다. 이 작업을 위해 `finally` 블록을 사용할 수 있습니다. `finally` 블록은 예외가 throw되었는지 여부에 관계없이 항상 실행됩니다.

다음 코드 예제에서는 `try`/`catch` 블록을 사용하여 @System.ArgumentOutOfRangeException.을 catch합니다. `Main` 메서드는 두 개의 배열을 만들고 한 배열을 다른 배열에 복사하려고 합니다. 작업에서 @System.ArgumentOutOfRangeException이 발생하고 콘솔에 오류가 기록됩니다. `finally` 블록은 복사 작업의 결과에 관계없이 실행됩니다.

C#
```
using System;

class ArgumentOutOfRangeExample
{
    public static void Main()
    {
        int[] array1 = {0, 0};
        int[] array2 = {0, 0};

        try
        {
            Array.Copy(array1, array2, -1);
        }
        catch (ArgumentOutOfRangeException e)
        {
            Console.WriteLine("Error: {0}", e);
        }
        finally
        {
            Console.WriteLine("This statement is always executed.");
        }
    }
}
```

## <a name="how-to-explicitly-throw-exceptions"></a>명시적으로 예외를 throw하는 방법

`throw` 문을 사용하여 명시적으로 예외를 throw할 수 있습니다. `throw` 문을 사용하여 catch된 예외를 다시 throw할 수도 있습니다. 디버그 시 자세한 정보를 제공하기 위해 다시 throw되는 예외에 정보를 추가하는 것은 좋은 코딩 방법입니다.

다음 코드 예제에서는 `try`/`catch` 블록을 사용하여 가능한 @System.IO.FileNotFoundException.을 catch합니다. `try` 블록 뒤에는 @System.IO.FileNotFoundException을 catch하고 데이터 파일을 찾을 수 없는 경우 콘솔에 메시지를 쓰는 `catch` 블록이 있습니다. 다음 문은 새 @System.IO.FileNotFoundException을 throw하고 예외에 텍스트 정보를 추가하는 `throw` 문입니다.

C#
```
using System;
using System.IO;

public class ProcessFile
{
   public static void Main()
      {
      FileStream fs = null;
      try   
      {
         //Opens a text tile.
         fs = new FileStream(@"C:\temp\data.txt", FileMode.Open);
         StreamReader sr = new StreamReader(fs);
         string line;

         //A value is read from the file and output to the console.
         line = sr.ReadLine();
         Console.WriteLine(line);
      }
      catch(FileNotFoundException e)
      {
         Console.WriteLine("[Data File Missing] {0}", e);
         throw new FileNotFoundException(@"[data.txt not in c:\temp directory]",e);
      }
      finally
      {
         if (fs != null)
            fs.Dispose();
      }
   }
}
```

## <a name="how-to-create-userdefined-exceptions"></a>사용자 정의 예외를 만드는 방법

.NET에서는 기본 클래스 @System.Exception.에서 최종적으로 파생되는 예외 클래스의 계층 구조를 제공합니다. 그러나 사용자 요구를 충족하는 미리 정의된 예외가 없는 경우 @System.Exception 클래스에서 파생하여 사용자 고유의 예외 클래스를 만들 수 있습니다.

사용자 고유의 예외를 만드는 경우 다음 예제와 같이 사용자 정의 예외의 클래스 이름을 "Exception" 단어로 끝내고 세 가지 공통 생성자를 구현합니다. 예제에서는 `EmployeeListNotFoundException`이라는 새 예외 클래스를 정의합니다. 클래스는 @System.Exception에서 파생되며 세 가지 생성자를 포함합니다.

C#
```
using System;

public class EmployeeListNotFoundException: Exception
{
    public EmployeeListNotFoundException()
    {
    }

    public EmployeeListNotFoundException(string message)
        : base(message)
    {
    }

    public EmployeeListNotFoundException(string message, Exception inner)
        : base(message, inner)
    {
    }
}
```

> [!NOTE]
> 원격을 사용하는 경우 사용자 정의 예외에 대한 메타데이터를 서버(호출 수신자) 및 클라이언트(프록시 개체 또는 호출자)에서 사용할 수 있는지 확인해야 합니다. 자세한 내용은 [예외에 대한 모범 사례](#best-practices-for-exceptions)를 참조하세요.

## <a name="best-practices-for-exceptions"></a>예외에 대한 모범 사례

잘 설계된 응용 프로그램이 응용 프로그램 충돌을 방지하기 위해 예외와 오류를 처리합니다. 이 섹션에서는 예외를 처리하고 만들기 위한 모범 사례를 설명합니다.

### <a name="use-trycatchfinally-blocks"></a>try/catch/finally 블록 사용

잠재적으로 예외를 생성할 수 있는 코드 주변에서 `try`/`catch`/`finally` 블록을 사용합니다. 

`catch` 블록에서 항상 가장 특정한 예외부터 가장 일반적인 예외의 순서로 예외를 지정합니다.

복구 가능 여부에 관계없이 `finally` 블록을 사용하여 리소스를 정리합니다.

### <a name="handle-common-conditions-without-throwing-exceptions"></a>예외를 throw하지 않고 일반적인 조건 처리

발생할 가능성이 높지만 예외를 트리거할 수도 있는 조건의 경우 예외를 방지하는 방식으로 조건을 처리하는 것이 좋습니다. 예를 들어 이미 닫혀 있는 연결을 닫으려고 하면 `InvalidOperationException`이 발생합니다. 닫기 전에 `if` 문을 사용하여 연결 상태를 확인하면 이 예외를 방지할 수 있습니다.

C#
```
if (conn.State != ConnectionState.Closed)
{
    conn.Close();
}
```

닫기 전에 연결 상태를 확인하지 않을 경우 `InvalidOperationException` 예외를 catch할 수 있습니다.

C#
```
try
{
    conn.Close();
}
catch (InvalidOperationException ex)
{
    Console.WriteLine(ex.GetType().FullName);
    Console.WriteLine(ex.Message);
}
```

어떤 방법을 선택할 것인지는 해당 이벤트의 예상 발생 빈도에 따라 달라집니다.

- 이벤트가 그다지 자주 발생하지 않으면(즉, 예상치 못한 파일 끝과 같은 이벤트가 실제로 예외이고 오류를 나타내는 경우) 예외 처리를 사용합니다. 예외 처리를 사용하면 정상적인 조건에서 적은 수의 코드가 실행됩니다.

- 이벤트가 일상적으로 발생하고 정상적인 실행의 일부로 간주될 수 있는 경우 코드에서 오류 조건을 확인합니다. 일반적인 오류 조건을 확인하면 예외가 방지되기 때문에 실행되는 코드가 줄어듭니다.

### <a name="design-classes-so-that-exceptions-can-be-avoided"></a>예외를 방지할 수 있도록 클래스 디자인

클래스는 예외를 트리거하는 호출을 방지할 수 있도록 하는 메서드 또는 속성을 제공할 수 있습니다. 예를 들어, @System.IO.FileStream 클래스는 파일 끝에 도달했는지 확인하는 데 도움이 되는 메서드를 제공합니다. 이러한 메서드를 사용하면 파일의 끝을 지나서 읽을 경우 throw되는 예외를 방지할 수 있습니다. 다음 예제에서는 예외를 트리거하지 않고 파일의 끝까지 읽는 방법을 보여 줍니다.

C#
```
class FileRead
{
    public void ReadAll(FileStream fileToRead)
    {
        // This if statement is optional
        // as it is very unlikely that
        // the stream would ever be null.
        if (fileToRead == null)
        {
            throw new System.ArgumentNullException();
        }

        int b;

        // Set the stream position to the beginning of the file.
        fileToRead.Seek(0, SeekOrigin.Begin);

        // Read each byte to the end of the file.
        for (int i = 0; i < fileToRead.Length; i++)
        {
            b = fileToRead.ReadByte();
            Console.Write(b.ToString());
            // Or do something else with the byte.
        }
    }
}
```

예외를 방지하는 또 다른 방법은 매우 일반적인 오류의 경우 예외를 throw하는 대신 null을 반환하는 것입니다. 매우 흔한 오류 사례는 정상적인 제어 흐름으로 간주할 수 있습니다. 이러한 경우에 null을 반환함으로써, 응용 프로그램의 성능에 미치는 영향을 최소화합니다.

### <a name="throw-exceptions-instead-of-returning-an-error-code"></a>오류 코드를 반환하는 대신 예외 throw

예외는 호출하는 코드에서 반환 코드를 확인하지 않아 오류가 발견되지 않는 것을 방지합니다. 

### <a name="use-the-predefined-net-exception-types"></a>미리 정의된 .NET 예외 형식 사용

새 예외 클래스는 미리 정의된 예외 클래스가 적용되지 않는 경우에만 도입합니다. 예를 들면 다음과 같습니다.

- 개체의 현재 상태에서 속성 집합이나 메서드 호출이 적절하지 않을 경우 @System.InvalidOperationException 예외를 throw합니다.

- 잘못된 매개 변수가 전달되면 @System.ArgumentException 예외 또는 @System.ArgumentException에서 파생된 미리 정의된 클래스 중 하나를 throw합니다.

### <a name="end-exception-class-names-with-the-word-exception"></a>예외 클래스 이름 뒤에 단어 `Exception` 추가

사용자 지정 예외가 필요한 경우 적절한 이름을 지정하고 @System.Exception 클래스에서 파생합니다. 예를 들면 다음과 같습니다.

C#
```
public class MyFileNotFoundException : Exception
{
}
```

### <a name="include-three-constructors-in-custom-exception-classes"></a>사용자 지정 예외 클래스에 세 가지 생성자 포함

사용자 고유의 예외 클래스를 만들 때 최소한 다음 세 가지 일반 생성자를 사용합니다. 즉, 기본 생성자, 문자열 메시지를 사용하는 생성자, 문자열 메시지와 내부 예외를 사용하는 생성자입니다.

- 기본값을 사용하는 @System.Exception.%23ctor,.

- 문자열 메시지를 수락하는 @System.Exception.%23ctor(System.String),.

- 문자열 메시지와 내부 예외를 허용하는 @System.Exception.%23ctor(System.String,System.Exception),.

예를 들어 [방법: 사용자 정의 예외 만들기](#how-to-create-user-defined-exceptions)를 참조하세요.

### <a name="ensure-that-exception-data-is-available-when-code-executes-remotely"></a>코드를 원격으로 실행하는 경우 예외 데이터를 사용할 수 있는지 확인

사용자 정의 예외를 만드는 경우 원격으로 실행하는 코드에서 예외에 대한 메타데이터를 사용할 수 있는지 확인합니다. 

예를 들어 앱 도메인을 구현하는 .NET 런타임에서 앱 도메인 간에 예외가 발생할 수 있습니다. 앱 도메인 A에서 앱 도메인 B를 만들고, 여기서 예외를 throw하는 코드를 실행한다고 가정해봅시다. 앱 도메인 A에서 예외를 정확하게 catch하고 처리하려면 앱 도메인 B에서 throw된 예외를 포함하는 어셈블리를 찾을 수 있어야 합니다. 앱 도메인 B에서 앱 도메인 A의 응용 프로그램 기준 위치가 아니라 해당 응용 프로그램 기준 위치 아래의 어셈블리에 포함된 예외를 throw할 경우 앱 도메인 A는 예외를 찾을 수 없으며 공용 언어 런타임에서 @System.IO.FileNotFoundException 예외를 throw합니다. 이러한 상황을 방지하기 위해 예외 정보가 포함된 어셈블리를 다음과 같은 두 가지 방법으로 배포할 수 있습니다.

- 해당 어셈블리를 두 응용 프로그램 도메인이 공유하는 공통 응용 프로그램 기본 구조에 넣습니다.

    \- 또는 -

- 도메인이 공통 응용 프로그램 기반 구조를 공유하지 않을 경우, 예외 정보가 포함된 어셈블리를 강력한 이름으로 지정한 다음, 이 어셈블리를 전역 어셈블리 캐시에 배포합니다.

### <a name="include-a-localized-description-string-in-every-exception"></a>모든 예외에 지역화된 설명 문자열 포함

사용자에게 표시되는 오류 메시지는 예외 클래스 이름에서 파생된 메시지가 아니라 throw된 예외의 설명 문자열에서 파생된 메시지입니다.

### <a name="use-grammatically-correct-error-messages"></a>문법적으로 올바른 오류 메시지 사용

명확한 문을 작성하고 종료 문장 부호를 포함합니다. 설명 문자열의 각 문장의 뒤에는 마침표가 있어야 합니다. 예를 들어 "로그 테이블이 오버플로되었습니다."는 적절한 설명 문자열입니다.

### <a name="in-custom-exceptions-provide-additional-properties-as-needed"></a>필요에 따라 사용자 지정 예외에서 추가 속성 제공

추가 정보가 유용한 프로그래밍 시나리오에 대해서만 예외에 설명 문자열 이외의 추가 속성을 제공합니다. 예를 들어, @System.IO.FileNotFoundException은 @System.IO.FileNotFoundException.FileName 속성을 제공합니다.

### <a name="place-throw-statements-so-that-the-stack-trace-will-be-helpful"></a>스택 추적이 도움이 되도록 throw 문 포함

스택 추적은 예외가 throw되는 문에서 시작하여 예외를 catch하는 `catch` 문까지 수행됩니다.

### <a name="use-exception-builder-methods"></a>예외 작성기 메서드 사용

클래스는 구현된 여러 위치에서 동일한 예외를 throw하는 것이 일반적입니다. 코드를 많이 사용하지 않으려면 예외를 만들어 반환하는 도우미 메서드를 사용합니다. 예:

C#
```
class FileReader
{
    private string fileName;

    public FileReader(string path)
    {
        fileName = path;
    }

    public byte[] Read(int bytes)
    {
        byte[] results = FileUtils.ReadFromFile(fileName, bytes);
        if (results == null)
        {
            throw NewFileIOException();
        }
        return results;
    }

    FileReaderException NewFileIOException()
    {
        string description = "My NewFileIOException Description";

        return new FileReaderException(description);
    }
}

```

예외의 생성자를 사용하여 예외를 작성하는 것이 더 적합한 경우도 있습니다. 예를 들어 @System.ArgumentException,과 같은 전역 예외 클래스가 있습니다. 

### <a name="clean-up-intermediate-results-when-throwing-an-exception"></a>예외를 throw할 때 중간 결과 정리

호출자가 메서드에서 예외가 throw될 때 의도하지 않은 결과가 발생하지 않는다고 가정할 수 있어야 합니다. 예를 들어 하나의 계좌에서 출금한 후 다른 계좌에 입금하여 돈을 이체하는 코드가 있고 입금을 실행하는 동안 예외가 발생할 경우 출금이 적용되기를 원하지 않을 것입니다.

C#
```
public void TransferFunds(Account from, Account to, decimal amount)
{
    from.Withdrawal(amount);
    // If the deposit fails, the withdrawal shouldn't remain in effect. 
    to.Deposit(amount);
}
```

이 상황을 처리하는 한 가지 방법은 입금 트랜잭션에서 throw된 예외를 catch하고 출금을 롤백하는 것입니다.

C#
```
private static void TransferFunds(Account from, Account to, decimal amount)
{
    string withdrawalTrxID = from.Withdrawal(amount);
    try
    {
        to.Deposit(amount);
    }
    catch
    {
        from.RollbackTransaction(withdrawalTrxID);
        throw
    }
}
```

이 예제에서는 `throw`를 사용하여 원래 예외를 다시 throw하는 방법을 보여 줍니다. 이렇게 하면 호출자가 @System.Exception.InnerException 속성을 검사하지 않아도 문제의 실제 원인을 쉽게 확인할 수 있습니다. 또는 새 예외를 throw하고 원래 예외를 내부 예외로 포함할 수 있습니다.

C#
```
catch (Exception ex)
{
    from.RollbackTransaction(withdrawalTrxID);
    throw new Exception("Withdrawal failed", ex);
}
```

## <a name="see-also"></a>참고 항목

.NET에서 예외가 작동하는 방식을 자세히 알아보려면 [What Every Dev needs to Know About Exceptions in the Runtime](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/exceptions.md)(모든 개발자가 런타임 예외에 대해 알아야 할 사항)을 참조하세요.



<!--HONumber=Nov16_HO1-->


