---
title: "방법: ConcurrentBag을 사용하여 개체 풀 만들기"
description: "방법: ConcurrentBag을 사용하여 개체 풀 만들기"
keywords: .NET, .NET Core
author: mairaw
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 87a6ada1-ee27-423d-b587-82e7cb45361b
translationtype: Human Translation
ms.sourcegitcommit: 2f8dede4c6f679466e8441b29cf778dc46059196
ms.openlocfilehash: cf16330afe6b8415c3480322069e88132bef1082

---

# <a name="how-to-create-an-object-pool-by-using-a-concurrentbag"></a>방법: ConcurrentBag을 사용하여 개체 풀 만들기

이 예제에서는 CurrentBag을 사용하여 개체 풀을 구현하는 방법을 보여 줍니다. 클래스에 여러 인스턴스가 필요하고 클래스를 만들거나 삭제하는 데 비용이 많이 드는 경우 개체 풀을 사용하면 응용 프로그램 성능을 향상시킬 수 있습니다. 클라이언트 프로그램에서 새 개체를 요청하면 먼저 개체 풀이 이미 풀에 만들어져 반환된 개체를 제공하려고 시도합니다. 제공될 개체가 없는 경우에만 새 개체가 만들어집니다. 

[ConcurrentBag&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentBag-1)는 특히 한 스레드에서 항목의 추가와 제거를 모두 수행할 때 추가하고 제거하는 속도가 빠르기 때문에 개체를 저장하는 데 사용됩니다. 이 예제는 [ConcurrentQueue&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentQueue-1) 및 [ConcurrentStack&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentStack-1)와 같이 모음 데이터 구조에서 구현하는 [IProducerConsumerCollection&lt;T&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.IProducerConsumerCollection-1)을 기반으로 추가로 확장할 수 있습니다.

## <a name="example"></a>예제

```csharp
using System;
using System.Collections.Concurrent;
using System.Threading;
using System.Threading.Tasks;


namespace ObjectPoolExample
{
    public class ObjectPool<T>
    {
        private ConcurrentBag<T> _objects;
        private Func<T> _objectGenerator;

        public ObjectPool(Func<T> objectGenerator)
        {
            if (objectGenerator == null) throw new ArgumentNullException("objectGenerator");

            _objects = new ConcurrentBag<T>();
            _objectGenerator = objectGenerator;
        }

        public T GetObject()
        {
            T item;
            return _objects.TryTake(out item) ? item : _objectGenerator();
        }

        public void PutObject(T item)
        {
            _objects.Add(item);
        }
    }

    class Program
    {
       static void Main(string[] args)
        {
            CancellationTokenSource cts = new CancellationTokenSource();

            // Create an opportunity for the user to cancel.
            Task.Run(() =>
                {
                    if (Console.ReadKey().KeyChar == 'c' || Console.ReadKey().KeyChar == 'C')
                        cts.Cancel();
                });

            ObjectPool<MyClass> pool = new ObjectPool<MyClass> (() => new MyClass());            

            // Create a high demand for MyClass objects.
            Parallel.For(0, 1000000, (i, loopState) =>
                {
                    MyClass mc = pool.GetObject();
                    Console.CursorLeft = 0;
                    // This is the bottleneck in our application. All threads in this loop
                    // must serialize their access to the static Console class.
                    Console.WriteLine("{0:####.####}", mc.GetValue(i));                 

                    pool.PutObject(mc);
                    if (cts.Token.IsCancellationRequested)
                        loopState.Stop();                 
                });
            Console.WriteLine("Press the Enter key to exit.");
            Console.ReadLine();
            cts.Dispose();
        }
    }

    // A toy class that requires some resources to create.
    // You can experiment here to measure the performance of the
    // object pool vs. ordinary instantiation.
    class MyClass
    {
        public int[] Nums {get; set;}
        public double GetValue(long i)
        {
            return Math.Sqrt(Nums[i]);
        }
        public MyClass()
        {
            Nums = new int[1000000];
            Random rand = new Random();
            for (int i = 0; i < Nums.Length; i++)
                Nums[i] = rand.Next();
        }
    }   
}
```

## <a name="see-also"></a>참고 항목

[System.Collections.Concurrent](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent)

[스레드로부터 안전한 컬렉션](index.md)





<!--HONumber=Nov16_HO1-->


