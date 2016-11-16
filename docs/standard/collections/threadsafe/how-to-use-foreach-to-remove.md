---
title: "방법: ForEach를 사용하여 BlockingCollection 항목 제거"
description: "방법: ForEach를 사용하여 BlockingCollection 항목 제거"
keywords: .NET, .NET Core
author: mairaw
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: f3db5825-b5c9-4e8b-80bc-e11760d9523e
translationtype: Human Translation
ms.sourcegitcommit: c15f2da15c6448cf1c36dea2d5fd53e734bb6608
ms.openlocfilehash: 618b5c7c1b8219f4e9225277064ff3c498ff3382

---

# <a name="how-to-use-foreach-to-remove-items-in-a-blockingcollection"></a>방법: ForEach를 사용하여 BlockingCollection 항목 제거

`Take` 및 `TryTake` 메서드를 사용하여 [BlockingCollection&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.BlockingCollection-1)에서 항목을 가져오는 것 외에도 `foreach` 루프를 사용하여 추가 작업이 완료되고 컬렉션이 빌 때까지 항목을 제거할 수 있습니다. 일반적인 `foreach` 루프와 달리 이 열거자는 항목을 제거하여 소스 컬렉션을 수정하기 때문에 이 방식을 열거형 변경 또는 열거형 소비라고 합니다.

## <a name="example"></a>예제

다음 예제에서는 `foreach` 루프를 사용하여 `BlockingCollection<T>`의 항목을 모두 제거하는 방법을 보여 줍니다. 

```csharp
using System;
using System.Collections.Concurrent;
using System.Diagnostics;
using System.Threading;
using System.Threading.Tasks;

class Example
{
   // Limit the collection size to 2000 items at any given time.
   // Set itemsToProduce to > 500 to hit the limit.
   const int upperLimit = 1000;

   // Adjust this number to see how it impacts the producing-consuming pattern.
   const int itemsToProduce = 100;

   static BlockingCollection<long> collection = new BlockingCollection<long>(upperLimit);

   // Variables for diagnostic output only.
   static Stopwatch sw = new Stopwatch();
   static int totalAdditions = 0;

   // Counter for synchronizing producers.
   static int producersStillRunning = 2;

   static void Main()
   {
       // Start the stopwatch.
       sw.Start();

       // Queue the Producer threads. Store in an array
       // for use with ContinueWhenAll
       Task[] producers = new Task[2];
       producers[0] = Task.Run(() => RunProducer("A", 0));
       producers[1] = Task.Run(() => RunProducer("B", itemsToProduce));

       // Create a cleanup task that will call CompleteAdding after
       // all producers are done adding items.
       Task cleanup = Task.Factory.ContinueWhenAll(producers, (p) => collection.CompleteAdding());

       // Queue the Consumer thread. Put this call
       // before Parallel.Invoke to begin consuming as soon as
       // the producers add items.
       Task.Run(() => RunConsumer());

       // Keep the console window open while the
       // consumer thread completes its output.
       Console.ReadKey(true);
   }

   static void RunProducer(string ID, int start)
   {

       int additions = 0;
       for (int i = start; i < start + itemsToProduce; i++)
       {
           // The data that is added to the collection.
           long ticks = sw.ElapsedTicks;

           // Display additions and subtractions.
           Console.WriteLine("{0} adding tick value {1}. item# {2}", ID, ticks, i);

           if(!collection.IsAddingCompleted)
               collection.Add(ticks);

           // Counter for demonstration purposes only.
           additions++;

           // Uncomment this line to
           // slow down the producer threads     ing.
           Thread.SpinWait(100000);
       }

       Interlocked.Add(ref totalAdditions, additions);
       Console.WriteLine("{0} is done adding: {1} items", ID, additions);
   }

   static void RunConsumer()
   {
       // GetConsumingEnumerable returns the enumerator for the
       // underlying collection.
       int subtractions = 0;
       foreach (var item in collection.GetConsumingEnumerable())
       {
           Console.WriteLine("Consuming tick value {0} : item# {1} : current count = {2}",
                   item.ToString("D18"), subtractions++, collection.Count);
       }

       Console.WriteLine("Total added: {0} Total consumed: {1} Current count: {2} ",
                           totalAdditions, subtractions, collection.Count);
       sw.Stop();

       Console.WriteLine("Press any key to exit");
   }
}

```

이 예제에서는 소비 스레드의 `BlockingCollection<T>.GetConsumingEnumerable` 메서드에 `foreach` 루프를 사용하는데, 이 루프는 컬렉션을 열거하면서 해당 컬렉션의 각 항목을 제거합니다. `BlockingCollection<T>`는 항상 컬렉션에 있는 항목의 최대 수를 제한합니다. 이런 방식으로 컬렉션을 열거하는 경우 제공되는 항목이 없거나 컬렉션이 비었을 때 소비자 스레드를 차단합니다. 이 예제에서는 항목이 소비되는 것보다 더 빠르게 공급자 스레드에서 항목을 추가하기 때문에 차단이 발생하지 않습니다. 

공급자 스레드를 통한 항목 추가 순서와 항목 열거 순서는 같지 않을 수 있습니다.

컬렉션을 수정하지 않고 열거하려면 `GetConsumingEnumerable` 메서드 없이 `foreach`만 사용하면 됩니다. 그러나 이와 같이 열거한 결과는 특정 시점의 컬렉션을 나타내는 스냅숏에 불과하다는 사실을 이해할 필요가 있습니다. 루프를 실행하는 동시에 다른 스레드를 통해 항목을 추가하거나 제거하고 있는 경우에는 루프에서 컬렉션의 실제 상태를 나타내지 않을 수 있습니다.

## <a name="see-also"></a>참고 항목

[System.Collections.Concurrent](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent)

[BlockingCollection 개요](blockingcollection-overview.md)



<!--HONumber=Nov16_HO1-->


