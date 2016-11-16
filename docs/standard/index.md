---
title: ".NET 입문서"
description: ".NET 입문서"
keywords: .NET, .NET Core
author: richlander
manager: wpickett
ms.date: 10/05/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: bbfe6465-329d-4982-869d-472e7ef85d93
translationtype: Human Translation
ms.sourcegitcommit: be774ae1291def36baafffbf2f717cf98565cd60
ms.openlocfilehash: fba870a93784b579da1065a07d82974951ac7e28

---

# <a name="net-primer"></a>.NET 입문서

> 간단한 .NET Core 응용 프로그램을 만드는 방법을 알아보려면 [".NET Core 시작" 자습서](../core/getting-started.md)를 확인해 보세요. 첫 번째 앱을 만들고 실행하는 데 몇 분밖에 걸리지 않습니다.

.NET은 범용 개발 플랫폼입니다. 범용 솔루션이 사용되는 모든 종류의 앱 유형이나 작업에 사용할 수 있습니다. 자동 메모리 관리, 최신 프로그래밍 언어 등 고품질 응용 프로그램을 효율적으로 쉽게 빌드할 수 있게 해주며 대부분의 개발자에게 유용한 몇 가지 주요 기능이 있습니다. .NET은 기본 메모리 및 API에 대한 하위 수준 액세스를 제공하는 동시에 다양한 편의 기능이 있는 상위 수준의 프로그래밍 환경을 지원합니다.

플랫폼의 기본 사항을 지정하는 개방형 [.NET 표준](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/dotnet-standards.md)에 따라 여러 .NET 구현을 사용할 수 있습니다. 응용 프로그램 종류(예: 데스크톱, 모바일, 게임, 클라우드)에 따라 개별적으로 최적화되며 많은 칩(예: x86/x64, ARM)과 운영 체제(예: Windows, Linux, iOS, Android, macOS)를 지원합니다. 오픈 소스도 환경의 OSI 승인 라이선스로 여러 .NET 구현과 많은 라이브러리를 사용할 수 있는 .NET 에코시스템의 중요한 부분입니다.

Microsoft 및 타사에서 제공하는 모든 .NET 버전을 확인하려면 [.NET 구현 개요](../about/products.md) 문서를 참조하세요.

이 입문서는 .NET 플랫폼의 주요 개념 중 일부를 이해하는 데 도움이 되며 지정된 각 항목에 대한 추가 리소스 링크를 제공합니다. 입문서를 마칠 때쯤에는 .NET 플랫폼의 중요한 용어와 개념을 인식하고 더 많은 지식을 얻는 방법을 알 수 있을 것입니다. 

## <a name="a-stroll-through-net"></a>.NET 살펴보기

완성도가 높은 모든 고급 응용 프로그램 개발 프레임워크와 마찬가지로 .NET에는 개발자의 작업을 더 쉽게 하고 코드 작성을 더 강력하고 표현력 있게 만들기 위한 여러 가지 강력한 기능이 있습니다. 이 섹션에서는 가장 중요한 기능의 기본 사항을 간략하게 설명하고 필요한 경우 자세한 토론에 대한 포인터를 제공합니다. 이 살펴보기를 마치면 GitHub 리포지토리의 샘플과 다른 코드를 읽고 그 내용을 이해할 수 있을 것입니다.

*   [프로그래밍 언어](#programming-languages)
*   [자동 메모리 관리](#automatic-memory-management)
*   [형식 안전성](#type-safety)
*   [대리자 및 람다 식](#delegates-and-lambdas)
*   [제네릭 형식(제네릭)](#generic-types-generics)
*   [LINQ(Language-Integrated Query)](#language-integrated-query-linq)
*   [비동기 프로그래밍](#async-programming)
*   [기본 상호 운용성](#native-interoperability)
*   [안전하지 않은 코드](#unsafe-code)

### <a name="programming-languages"></a>프로그래밍 언어

개발자는 응용 프로그램을 만들기 위해 .NET을 지원하는 모든 프로그래밍 언어를 선택할 수 있습니다. .NET은 언어 독립성과 상호 운용성을 제공하므로 개발된 언어에 관계없이 다른 .NET 응용 프로그램 및 구성 요소와 상호 작용할 수 있습니다.

.NET 플랫폼용 응용 프로그램을 개발할 수 있는 언어는 [CLI(공용 언어 인프라) 사양](https://www.visualstudio.com/en-us/mt639507)을 준수합니다.

.NET에서 지원하는 Microsoft 언어에는 C#, F# 및 Visual Basic이 포함됩니다. 

* C#은 C 스타일 언어의 표현력과 우아함은 그대로 유지하면서 간단하고 형식이 안전한 개체 지향 언어입니다. C 및 이와 비슷한 언어에 익숙한 사용자라면 누구나 거의 문제 없이 C#을 사용할 수 있을 것입니다.

* F#은 일반적인 개체 지향 및 명령형 프로그래밍도 지원하는 플랫폼 간 기능 우선 프로그래밍 언어입니다.

* Visual Basic은 .NET에서 실행되는 다양한 응용 프로그램을 빌드하는 데 사용할 수 있는 배우기 쉬운 언어입니다.

> [!NOTE]
> 현재 릴리스의 .NET Core에서는 C#만 모든 Microsoft 도구에서 완전히 지원됩니다.  F#은 .NET Core SDK에서 지원되지만 Visual Studio 도구가 아직 없습니다.  SDK 및 Visual Studio 도구에 대한 Visual Basic 지원도 곧 제공될 예정입니다.

### <a name="automatic-memory-management"></a>자동 메모리 관리

가비지 수집은 가장 잘 알려진 .NET 기능입니다. GC(가비지 수집기)에 자세한 정보를 제공하는 메커니즘이 있지만 개발자가 적극적으로 메모리를 관리할 필요는 없습니다. C#에는 특정 형식 측면에서 메모리를 할당하는 `new` 키워드 및 개체의 사용 범위를 제공하는 `using` 키워드가 포함되어 있습니다. GC는 메모리 관리에 대해 지연 방식으로 작동하며 메모리의 즉각적인 수집보다 응용 프로그램 처리량을 우선합니다.

다음 두 줄에서는 모두 메모리를 할당합니다.

```cs
var title = ".NET Primer";
var list = new List<string>;

```

할당 취소는 가비지 수집기에서 예약 실행을 통해 메모리를 회수할 때 자동으로 수행되기 때문에 메모리 할당을 취소하는 유사 키워드는 없습니다.

메서드 변수는 일반적으로 메서드가 완료될 때 범위를 벗어나며, 이때 메서드 변수를 수집할 수 있습니다. 그러나 `using` 문을 사용하여 특정 개체가 메서드 종료보다 더 빨리 범위를 벗어남을 GC에 알릴 수 있습니다.

```cs
using(FileStream stream = GetFileStream(context))
{
    //operations on the stream
}

```

`using` 블록이 완료되면 GC는 위 예제의 `stream` 개체를 수집하고 해당 메모리를 회수할 수 있음을 알게 됩니다.

가비지 수집기를 통해 사용할 수 있는, 덜 눈에 띄지만 훨씬 광범위한 기능 중 하나는 메모리 안전성입니다. 고정 메모리 안전성은 매우 간단합니다. 프로그램이 할당되고 해제되지 않은 메모리에만 액세스하면 메모리가 안전합니다. 현수 포인터는 항상 버그이며 추적하기 어려운 경우가 많습니다.

.NET 런타임에서는 메모리 안전성에 대한 약속을 지키기 위해 GC에서 기본적으로 제공되지 않는 추가 서비스를 제공합니다. 프로그램이 배열의 끝에서 인덱싱하거나 개체의 끝에서 가상 필드에 액세스하지 않도록 합니다.

다음 예제에서는 메모리 안전성의 결과로 예외가 throw됩니다.

```cs
int[] numbers = new int[42];
int number = numbers[42]; // will throw (indexes are 0-based)

```

### <a name="type-safety"></a>형식 안전성

개체는 형식에 따라 할당됩니다. 지정된 개체에 허용되는 유일한 작업과 개체가 사용하는 메모리는 해당 형식의 것입니다. `Dog` 형식에는 `Jump` 및 `WagTail` 메서드가 있을 수 있지만 `SumTotal` 메서드가 있을 가능성은 낮습니다. 프로그램은 지정된 형식의 선언된 메서드만 호출할 수 있습니다. 다른 모든 호출에서는 컴파일 시간 오류 또는 런타임 예외가 발생합니다(동적 기능 또는 `object`를 사용하는 경우).

.NET 언어는 기본 및 파생 클래스의 계층 구조로 이루어진 개체 지향 언어입니다. .NET 런타임에서는 개체 계층 구조에 맞는 개체 캐스트 및 호출만 허용됩니다. .NET 언어에서 정의된 모든 형식은 기본 `object` 형식에서 파생됩니다.

```cs
Dog dog = Dog.AdoptDog(); // Returns a Dog type
Pet pet = (Pet)dog; // Dog derives from Pet
pet.ActCute();
Car car = (Car)dog; // will throw - no relationship between Car and Dog
object temp = (object)dog; // legal - a Dog is an object
car = (Car)temp; // will throw - the runtime isn't fooled
car.Accelerate() // the dog won't like this, nor will the program get this far

```

또한 형식 안전성은 접근자 키워드의 충실도를 보장하여 캡슐화 적용을 지원하는 데 사용됩니다. 접근자 키워드는 다른 코드에서 지정된 형식의 멤버에 대한 액세스를 제어하는 아티팩트입니다. 일반적으로 형식 내에서 해당 동작을 관리하는 데 사용하는 다양한 종류의 데이터에 사용됩니다.

```cs
Dog dog = Dog._nextDogToBeAdopted; // will throw - this is a private field

```

일부 .NET 언어에서는 **형식 유추**를 지원합니다. 형식 유추는 컴파일러가 오른쪽에 있는 식에서 왼쪽에 있는 식의 형식을 유추함을 의미합니다. 형식 안전성이 손상되거나 무시되는 것은 아닙니다. 결과 형식은** **강력한 형식이며 수반되는 모든 특성을 포함합니다. 형식 유추를 도입하기 위해 이전 예제의 처음 두 줄을 다시 작성해 보겠습니다. 예제의 나머지 부분은 완전히 동일합니다.

```cs
  var dog = Dog.AdoptDog();
  var pet = (Pet)dog;
  pet.ActCute();
  Car car = (Car)dog; // will throw - no relationship between Car and Dog
  object temp = (object)dog; // legal - a Dog is an object
  car = (Car)temp; // will throw - the runtime isn't fooled
  car.Accelerate() // the dog won't like this, nor will the program get this far

```

### <a name="delegates-and-lambdas"></a>대리자 및 람다 식

대리자는 C++의 함수 포인터와 유사하지만 형식이 안전하다는 큰 차이점이 있습니다. 대리자는 CLR 형식 시스템 내에서 일종의 연결이 끊긴 메서드입니다. 일반 메서드는 클래스에 연결되고 정적 또는 인스턴스 호출 규칙을 통해서만 직접 호출할 수 있습니다.

대리자는 특히 LINQ의 토대가 되는 람다 식을 통해 .NET 환경의 다양한 API 및 위치에서 사용됩니다.

[대리자 및 람다 식](delegates-lambdas.md) 문서에서 자세히 알아보세요.

### <a name="generic-types-generics"></a>제네릭 형식(제네릭)

일반적으로 "제네릭"이라고도 불리는 제네릭 형식은 .NET Framework 2.0에서 추가된 기능입니다. 간단히 말해, 제네릭은 프로그래머가 해당 클래스를 디자인할 때 "형식 매개 변수"를 도입할 수 있게 합니다. 이렇게 하면 클라이언트 코드(형식의 사용자)에서 형식 매개 변수 대신 사용할 형식을 정확하게 지정할 수 있습니다.

제네릭은 프로그래머가 제네릭 데이터 구조를 구현할 수 있도록 돕기 위해 추가되었습니다. 제네릭이 도입되기 전에는, 가령 _List_ 형식을 제네릭으로 만들기 위해 _object_ 형식인 요소로 작업해야 했습니다. 이 경우 미묘한 런타임 오류가 발생할 수 있다는 점 외에도 다양한 성능 및 의미 체계 문제가 있습니다. 후자의 경우 가장 심각한 문제는 예를 들어 데이터 구조에 정수와 문자열이 둘 다 포함되어 있을 때 목록의 멤버로 작업하면 _InvalidCastException_이 throw되는 것입니다.

아래 샘플에서는 @System.Collections.Generic.List%601 형식 인스턴스를 사용하여 실행 중인 기본 프로그램을 보여 줍니다.

```cs
using System;
using System.Collections.Generic;

namespace GenericsSampleShort {
    public static void Main(string[] args){
        // List<string> is the client way of specifying the actual type for the type parameter T
        List<string> listOfStrings = new List<string> { "First", "Second", "Third" };

        // listOfStrings can accept only strings, both on read and write.
        listOfStrings.Add("Fourth");

        // Below will throw a compile-time error, since the type parameter
        // specifies this list as containing only strings.
        listOfStrings.Add(1);

    }
}

```

자세한 내용은 [제네릭 형식(제네릭) 개요](generics.md) 문서를 참조하세요.

### <a name="async-programming"></a>비동기 프로그래밍

비동기 프로그래밍은 런타임, 프레임워크 라이브러리 및 .NET 언어 구문의 비동기 지원을 포함하는 .NET 내의 최고급 개념입니다. 내부적으로 운영 체제를 활용하여 I/O 바인딩된 작업을 최대한 효율적으로 수행하는 개체(예: `Task`)를 기반으로 합니다.

.NET의 비동기 프로그래밍에 대해 자세히 알아보려면 [비동기 개요](async.md)부터 살펴보세요.

### <a name="language-integrated-query-linq"></a>LINQ(Language-Integrated Query)

LINQ는 데이터에 적용할 간단하고 선언적인 코드를 작성할 수 있게 해주는 C# 및 VB에 대한 강력한 기능 집합입니다. 데이터는 다양한 형식(예: 메모리 내 개체, SQL 데이터베이스 또는 XML 문서)일 수 있지만 작성하는 LINQ 코드는 일반적으로 각 데이터 소스마다 다르게 표시되지 않습니다.

자세히 알아보고 몇 가지 샘플을 확인하려면 [LINQ(Language-Integrated Query)](using-linq.md)를 참조하세요.

### <a name="native-interoperability"></a>기본 상호 운용성

현재 사용 중인 운영 체제마다 다양한 프로그래밍 태스크에 대해 많은 플랫폼 지원을 제공합니다. .NET에서는 해당 API를 활용하는 여러 가지 방법을 제공합니다. 총체적으로 이러한 지원을 "기본 상호 운용성"이라고 하며, 이 섹션에서는 관리되는 .NET 코드에서 기본 API에 액세스하는 방법을 살펴보겠습니다.

기본 상호 운용성은 대체로 "플랫폼 호출" 또는 줄여서 P/Invoke를 통해 수행됩니다. .NET Core의 이러한 지원은 Linux 및 Windows 플랫폼에서 사용할 수 있습니다. 기본 상호 운용성을 수행하는 다른 Windows 전용 방법은 "COM interop"라고 하며, 관리 코드에서 [COM 구성 요소](https://msdn.microsoft.com/library/bwa2bx93.aspx)로 작업하는 데 사용됩니다. P/Invoke 인프라를 기반으로 하지만 약간 다른 방식으로 작동합니다.

Java 및 Objective-C에 대한 Mono(및 Xamarin)의 상호 운용성 지원은 대부분 비슷하게 작성되었습니다. 즉, 동일한 원칙을 사용합니다.

[기본 상호 운용성](native-interop.md) 문서에서 자세히 알아보세요.

### <a name="unsafe-code"></a>안전하지 않은 코드

CLR을 사용하면 기본 메모리에 액세스하고 `unsafe` 코드를 통해 포인터 산술 연산을 수행할 수 있습니다. 이러한 작업은 특정 알고리즘 및 시스템 상호 운용성에 필요합니다. 강력하기는 하지만, 시스템 API와의 상호 운용하거나 가장 효율적인 알고리즘을 구현하는 데 필요한 경우가 아니면 안전하지 않은 코드는 사용하지 않는 것이 좋습니다. 안전하지 않은 코드는 환경에 따라 다르게 실행될 수도 있고 가비지 수집기 및 형식 안전성의 이점을 잃을 수도 있습니다. 안전하지 않은 코드를 최대한 제한 및 중앙 집중화하고 해당 코드를 철저히 테스트하는 것이 좋습니다.

[StringBuilder 클래스](https://github.com/dotnet/coreclr/blob/master/src/mscorlib/src/System/Text/StringBuilder.cs#L327)의 `ToString()` 메서드는 `unsafe` 코드를 통해 메모리 청크를 직접 이동하여 알고리즘을 효율적으로 구현할 수 있는 방법을 보여 줍니다.

```cs
public override String ToString() {
          Contract.Ensures(Contract.Result<String>() != null);

          VerifyClassInvariant();

          if (Length == 0)
              return String.Empty;

          string ret = string.FastAllocateString(Length);
          StringBuilder chunk = this;
          unsafe {
              fixed (char* destinationPtr = ret)
              {
                  do
                  {
                      if (chunk.m_ChunkLength > 0)
                      {
                          // Copy these into local variables so that they are stable even in the presence of ----s (hackers might do this)
                          char[] sourceArray = chunk.m_ChunkChars;
                          int chunkOffset = chunk.m_ChunkOffset;
                          int chunkLength = chunk.m_ChunkLength;

                          // Check that we will not overrun our boundaries.
                          if ((uint)(chunkLength + chunkOffset) <= ret.Length && (uint)chunkLength <= (uint)sourceArray.Length)
                          {
                              fixed (char* sourcePtr = sourceArray)
                                  string.wstrcpy(destinationPtr + chunkOffset, sourcePtr, chunkLength);
                          }
                          else
                          {
                              throw new ArgumentOutOfRangeException("chunkLength", Environment.GetResourceString("ArgumentOutOfRange_Index"));
                          }
                      }
                      chunk = chunk.m_ChunkPrevious;
                  } while (chunk != null);
              }
          }
          return ret;
      }

```

## <a name="notes"></a>노트

".NET 런타임" 용어는 CLR, Mono, IL2CPP 등의 여러 .NET 구현을 수용하기 위해 문서 전체에서 사용됩니다. 더 구체적인 이름은 필요한 경우에만 사용됩니다.

이 문서는 이전 기록이 아니라 현재 상태의 .NET 플랫폼을 설명하기 위한 것입니다. 강조해서 토론할 만큼 중요하기만 하면 .NET 기능이 항상 제공되었는지 또는 최근에 도입되었는지는 중요하지 않습니다.



<!--HONumber=Nov16_HO1-->


