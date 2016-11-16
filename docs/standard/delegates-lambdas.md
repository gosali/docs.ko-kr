---
title: "대리자 및 람다 식"
description: "대리자 및 람다 식"
keywords: .NET, .NET Core
author: richlander
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: fe2e4b4c-6483-4106-a4b4-a33e2e306591
translationtype: Human Translation
ms.sourcegitcommit: 9cf6022fc910bc5418c03c0fa81d9432d85be3b0
ms.openlocfilehash: 0d1dfc333f16acad44b1e276b75ff3c65a77e5aa

---

# <a name="delegates-and-lambdas"></a>대리자 및 람다 식

대리자는 특정 메서드 시그니처를 지정하는 형식을 정의합니다. 이 시그니처를 충족하는 메서드(정적 또는 인스턴스)를 해당 형식의 변수에 할당한 다음 직접 호출하거나(적절한 인수 사용) 다른 메서드에 인수로 전달한 다음 호출할 수 있습니다. 다음 예제에서는 대리자 사용을 보여 줍니다.

```cs
public class Program
{

  public delegate string Reverse(string s);

  static string ReverseString(string s)
  {
      return new string(s.Reverse().ToArray());
  }

  static void Main(string[] args)
  {
      Reverse rev = ReverseString;

      Console.WriteLine(rev("a string"));
  }
}

```

*   줄 4에서는 특정 시그니처의 대리자 형식(이 경우 문자열 매개 변수를 사용하고 문자열 매개 변수를 반환하는 메서드)을 만듭니다.
*   줄 6에서는 정확히 동일한 시그니처가 있는 메서드를 제공하여 대리자 구현을 정의합니다.
*   줄 13에서는 `Reverse` 대리자를 준수하는 형식에 메서드가 할당됩니다.
*   마지막으로, 줄 15에서는 대리자를 호출하고 반대로 만들 문자열을 전달합니다.

개발 프로세스를 간소화하기 위해 .NET에는 프로그래머가 새 형식을 만들 필요 없이 다시 사용할 수 있는 대리자 형식 집합이 포함되어 있습니다. `Func<>`, `Action<>` 및 `Predicate<>`이며, 새 대리자 형식을 정의할 필요 없이 .NET API의 여러 위치에서 사용할 수 있습니다. 물론, 세 가지 형식 간에는 대체로 의도된 사용 방법과 관계가 있는 몇 가지 차이점이 있으며 해당 시그니처에서 확인할 수 있습니다.

*   `Action<>`은 대리자의 인수를 사용하여 작업을 수행해야 할 때 사용됩니다.
*   `Func<>`는 일반적으로 변환을 수행해야 할 때 사용됩니다. 즉, 대리자의 인수를 다른 결과로 변환해야 할 때 사용됩니다. 대표적인 예로 프로젝션이 있습니다.
*   `Predicate<>`는 인수가 대리자의 조건을 충족하는지 확인해야 할 때 사용됩니다. `Func<T, bool>`로 작성할 수도 있습니다.

이제 위의 예제를 가져와서 사용자 지정 형식 대신 `Func<>` 대리자를 사용하여 다시 작성할 수 있습니다. 프로그램은 동일하게 실행됩니다.

```cs
public class Program
{

  static string ReverseString(string s)
  {
      return new string(s.Reverse().ToArray());
  }

  static void Main(string[] args)
  {
      Func<string, string> rev = ReverseString;

      Console.WriteLine(rev("a string"));
  }
}

```

이 간단한 예제에서는 Main() 메서드 외부에서 메서드를 정의하는 것이 불필요해 보입니다. .NET Framework 2.0에서 **익명 대리자** 개념이 도입된 것은 이 때문입니다. 익명 대리자 지원을 사용하면 추가 형식이나 메서드를 지정할 필요 없이 "인라인" 대리자를 만들 수 있습니다. 필요한 위치에 대리자 정의를 인라인으로 추가하기만 하면 됩니다.

예를 들어 이번에는 익명 대리자를 사용하여 짝수 목록만 필터링한 다음 콘솔에 출력하겠습니다.

```cs
public class Program
{

  public static void Main(string[] args)
  {
    List<int> list = new List<int>();

    for (int i = 1; i <= 100; i++)
    {
        list.Add(i);
    }

    List<int> result = list.FindAll(
      delegate(int no)
      {
          return (no%2 == 0);
      }
    );

    foreach (var item in result)
    {
        Console.WriteLine(item);
    }
  }
}

```

강조 표시된 줄에 유의하세요. 보시는 것처럼 대리자 본문은 다른 모든 대리자와 마찬가지로 단순히 식 집합일 뿐입니다. 그러나 별도의 정의가 아니라 `List<T>` 형식의 `FindAll()` 메서드 호출에서 _임시로_ 도입했습니다.

그러나 이 방법의 경우에도 제거할 수 있는 많은 코드가 있습니다. 이때 **람다 식**이 유용합니다.

람다 식 또는 줄여서 "람다"는 처음에 C# 3.0에서 LINQ(Language-Integrated Query)의 핵심 구성 요소 중 하나로 도입되었습니다. 람다 식은 단지 대리자 사용에 더 편리한 구문일 뿐입니다. 시그니처와 메서드 본문을 선언하지만 대리자에 할당되지 않은 경우 고유한 공식 ID가 없습니다. 대리자와 달리 이벤트 등록의 왼쪽 항으로 또는 다양한 Linq 절과 메서드에서 직접 할당할 수 있습니다.

람다 식은 대리자를 지정하는 또 다른 방법이므로 익명 대리자 대신 람다 식을 사용하도록 위의 샘플을 다시 작성할 수 있어야 합니다.

```cs
public class Program
{

  public static void Main(string[] args)
  {
    List<int> list = new List<int>();

    for (int i = 1; i <= 100; i++)
    {
        list.Add(i);
    }

    List<int> result = list.FindAll(i => i % 2 == 0);

    foreach (var item in result)
    {
        Console.WriteLine(item);
    }
  }
}

```

강조 표시된 줄을 살펴보면 람다 식이 어떻게 표시되는지 확인할 수 있습니다. 대리자 사용에 **매우** 편리한 구문일 뿐이므로 이면에서 수행되는 동작은 익명 대리자의 경우와 비슷합니다.

다시 말해, 람다는 단순히 대리자이므로 다음 코드 조각과 같이 문제 없이 이벤트 처리기로 사용할 수 있습니다.

```cs
public MainWindow()
{
    InitializeComponent();

    Loaded += (o, e) =>
    {
        this.Title = "Loaded";
    };
}

```

## <a name="further-reading-and-resources"></a>추가 정보 및 리소스

*   [대리자](https://msdn.microsoft.com/library/ms173171.aspx)
*   [익명 함수](https://msdn.microsoft.com/library/bb882516.aspx)
*   [람다 식](https://msdn.microsoft.com/library/bb397687.aspx)



<!--HONumber=Nov16_HO1-->


