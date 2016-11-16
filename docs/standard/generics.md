---
title: "제네릭 형식(제네릭) 개요"
description: "제네릭 형식(제네릭) 개요"
keywords: .NET, .NET Core
author: kuhlenh
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: a315b111-8e48-446c-ab19-acb6405894a7
translationtype: Human Translation
ms.sourcegitcommit: 5b1a76c011b95db3ff5c4b4e01556f79c45fb369
ms.openlocfilehash: 5dcb9d10aeded8c5e8956c4b99ba9675311a787a

---

# <a name="generic-types-generics-overview"></a>제네릭 형식(제네릭) 개요

암시적이든 명시적이든 C#에서는 항상 제네릭을 사용합니다. C#에서 LINQ를 사용할 때 IEnumerable<T>로 작업하고 있다는 것을 알아차리셨나요? 또는 Entity Framework를 사용하여 데이터베이스에 통신하기 위한 "제네릭 리포지토리"의 온라인 샘플을 본 적이 있다면 대부분의 메서드가 IQueryable<T>를 반환하는 것을 보셨나요? 이러한 예제에서 **T**가 무엇이고 왜 사용되는지 궁금하게 여겼을 수도 있습니다.

.NET Framework 2.0에서 처음 도입된 제네릭은 C# 언어와 CLR(공용 언어 런타임) 둘 다를 변경했습니다. **제네릭**은 기본적으로 개발자가 실제 데이터 형식에 커밋하지 않고 [형식이 안전한](https://msdn.microsoft.com/library/hbzz1a9a.aspx) 데이터 구조를 정의할 수 있게 해주는 "코드 템플릿"입니다. 예를 들어 `List<T>`는 `List<int>`, `List<string>`, `List<Person>` 등 모든 형식으로 선언하고 사용할 수 있는 [제네릭 컬렉션](https://msdn.microsoft.com/library/System.Collections.Generic.aspx)입니다.

그렇다면 무엇을 말하고 싶은 것일까요? 왜 제네릭이 유용한 것일까요? 이러한 사항을 이해하려면 제네릭을 추가하기 전과 이후의 특정 클래스를 살펴봐야 합니다. `ArrayList`를 살펴보겠습니다. C# 1.0에서는 `ArrayList` 요소가 `object` 형식이었습니다. 즉, 추가된 모든 요소가 `object`로 자동으로 변환되었습니다. 목록에서 요소를 읽는 경우에도 마찬가지입니다. 이 프로세스를 각각 [boxing](https://msdn.microsoft.com/library/yz2be5wk.aspx) 및 unboxing이라고 합니다. boxing 및 unboxing은 성능에 영향을 줍니다. 그러나 이보다 더 중요한 사항은 컴파일 시간에 목록에 있는 데이터의 실제 형식을 알 수 없다는 것입니다. 이 때문에 손상되기 쉬운 일부 코드가 발생합니다. 제네릭은 각 목록 인스턴스에 포함될 데이터 형식과 관련된 추가 정보를 제공하여 이 문제를 해결합니다. 간단히 말해서, `List<int>`에는 정수만 추가하고 `List<Person>`에는 사용자만 추가하면 됩니다.

또한 제네릭은 런타임에 사용하거나 **확인**할 수 있습니다. 즉, 런타임에서 사용 중인 데이터 구조의 형식을 알고 메모리에 보다 효율적으로 저장할 수 있습니다.

아래에는 런타임에 데이터 구조 형식을 알고 있을 경우의 효율성을 보여 주는 작은 프로그램이 있습니다.

```cs
  using System;
  using System.Collections;
  using System.Collections.Generic;
  using System.Diagnostics;

  namespace GenericsExample {
    class Program {
      static void Main(string[] args) {
        //generic list
        List<int> ListGeneric = new List<int> { 5, 9, 1, 4 };
        //non-generic list
        ArrayList ListNonGeneric = new ArrayList { 5, 9, 1, 4 };
        // timer for generic list sort
        Stopwatch s = Stopwatch.StartNew();
        ListGeneric.Sort();
        s.Stop();
        Console.WriteLine($"Generic Sort: {ListGeneric}  \n Time taken: {s.Elapsed.TotalMilliseconds}ms");

        //timer for non-generic list sort
        Stopwatch s2 = Stopwatch.StartNew();
        ListNonGeneric.Sort();
        s2.Stop();
        Console.WriteLine($"Non-Generic Sort: {ListNonGeneric}  \n Time taken: {s2.Elapsed.TotalMilliseconds}ms");
        Console.ReadLine();
      }
    }
  }

```

이 프로그램에서는 다음이 출력됩니다.

```console
Generic Sort: System.Collections.Generic.List\`1[System.Int32] Time taken: 0.0789ms
Non-Generic Sort: System.Collections.ArrayList Time taken: 2.4324ms

```

여기서 알 수 있는 첫 번째 사항은 제네릭 목록의 정렬 속도가 제네릭이 아닌 목록보다 훨씬 빠르다는 것입니다. 제네릭 목록의 형식은 고유 형식([System.Int32])인 반면 제네릭이 아닌 목록의 형식은 일반 형식이라는 것도 발견했을 수 있습니다. 제네릭 `List<int>`는 int 형식임을 런타임에서 알고 있기 때문에 메모리의 기본 정수 배열에 목록 요소를 저장할 수 있는 반면, 제네릭이 아닌 `ArrayList`는 각 목록 요소를 개체로 캐스트해야 메모리의 개체 배열에 저장됩니다. 이 예제에서 알 수 있듯이 추가 캐스팅 작업은 시간이 걸리며 목록 정렬 속도가 느려집니다.

런타임에서 제네릭 형식을 알고 있을 경우의 마지막 이점은 향상된 디버깅 환경입니다. C#에서 제네릭을 디버그하는 경우 데이터 구조의 각 요소가 어떤 형식인지 알고 있습니다. 제네릭이 없었다면 각 요소의 형식을 알 수 없었을 것입니다.

## <a name="further-reading-and-resources"></a>추가 정보 및 리소스

*   [C# 제네릭 소개](https://msdn.microsoft.com/library/ms379564.aspx)
*   [C# 프로그래밍 가이드 - 제네릭](https://msdn.microsoft.com/library/512aeb7t.aspx)



<!--HONumber=Nov16_HO1-->


