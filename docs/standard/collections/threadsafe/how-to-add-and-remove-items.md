---
title: "방법: ConcurrentDictionary에서 항목 추가 및 제거"
description: "방법: ConcurrentDictionary에서 항목 추가 및 제거"
keywords: .NET, .NET Core
author: mairaw
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: b7c04a5f-a8e6-42ae-8c84-0e1ae18896eb
translationtype: Human Translation
ms.sourcegitcommit: c15f2da15c6448cf1c36dea2d5fd53e734bb6608
ms.openlocfilehash: 90ad4f1f0266d948937ed462be15a4d4948ce7f8

---

# <a name="how-to-add-and-remove-items-from-a-concurrentdictionary"></a>방법: ConcurrentDictionary에서 항목 추가 및 제거

이 예제에서는 [System.Collections.Concurrent.ConcurrentDictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentDictionary-2)의 항목을 추가, 검색, 업데이트 및 제거하는 방법을 보여 줍니다. 이 컬렉션 클래스는 스레드로부터 안전하게 구현됩니다. 여러 스레드에서 컬렉션에 동시에 액세스할 수 있을 때는 언제든지 이 클래스를 사용하는 것이 좋습니다.

`ConcurrentDictionary<TKey, TValue>`에서는 추가하거나 제거하려고 시도하기 전에 키의 존재 여부를 먼저 확인하는 코드가 필요하지 않게 하는 편리한 몇 가지 메서드를 제공합니다. 다음 표에서는 이처럼 편리한 메서드를 나열하고 해당 메서드가 사용되는 경우에 대해 설명합니다.

메서드 | 사용 시기
------ | -----------
`AddOrUpdate` | 지정된 키의 새 값을 추가하려는 경우 및 이미 존재하는 키의 값을 바꾸려는 경우
`GetOrAdd` | 지정된 키의 기존 값을 검색하려는 경우 및 존재하지 않는 키/값 쌍을 지정하려는 경우
`TryAdd`, `TryGetValue`, `TryUpdate`, `TryRemove` | 키/값 쌍 추가, 가져오기, 업데이트 또는 제거를 수행하려는 경우 및 어떤 이유로 인해 이러한 시도가 실패하거나 이미 키가 존재할 때 다른 작업을 대신 수행하려는 경우

## <a name="example"></a>예제

```csharp
namespace DictionaryHowTo
{
    using System;
    using System.Collections.Concurrent;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;

    // The type of the Value to store in the dictionary:
    class CityInfo : IEqualityComparer<CityInfo>
    {
        public string Name { get; set; }
        public DateTime lastQueryDate { get; set; }
        public decimal Longitude { get; set; }
        public decimal Latitude { get; set; }
        public int[] RecentHighTemperatures { get; set; }

        public CityInfo(string name, decimal longitude, decimal latitude, int[] temps)
        {
            Name = name;
            lastQueryDate = DateTime.Now;
            Longitude = longitude;
            Latitude = latitude;
            RecentHighTemperatures = temps;
        }

        public CityInfo()
        {
        }

        public CityInfo(string key)
        {
            Name = key;
            // MaxValue means "not initialized"
            Longitude = Decimal.MaxValue;
            Latitude = Decimal.MaxValue;
            lastQueryDate = DateTime.Now;
            RecentHighTemperatures = new int[] { 0 };

        }
        public bool Equals(CityInfo x, CityInfo y)
        {
            return x.Name == y.Name && x.Longitude == y.Longitude && x.Latitude == y.Latitude;
        }

        public int GetHashCode(CityInfo obj)
        {
            CityInfo ci = (CityInfo)obj;
            return ci.Name.GetHashCode();
        }
    }

    class Program
    {
        // Create a new concurrent dictionary.
        static ConcurrentDictionary<string, CityInfo> cities = new ConcurrentDictionary<string, CityInfo>();

        static void Main(string[] args)
        {
            CityInfo[] data = 
            {
                new CityInfo(){ Name = "Boston", Latitude = 42.358769M, Longitude = -71.057806M, RecentHighTemperatures = new int[] {56, 51, 52, 58, 65, 56,53}},
                new CityInfo(){ Name = "Miami", Latitude = 25.780833M, Longitude = -80.195556M, RecentHighTemperatures = new int[] {86,87,88,87,85,85,86}},
                new CityInfo(){ Name = "Los Angeles", Latitude = 34.05M, Longitude = -118.25M, RecentHighTemperatures =   new int[] {67,68,69,73,79,78,78}},
                new CityInfo(){ Name = "Seattle", Latitude = 47.609722M, Longitude =  -122.333056M, RecentHighTemperatures =   new int[] {49,50,53,47,52,52,51}},
                new CityInfo(){ Name = "Toronto", Latitude = 43.716589M, Longitude = -79.340686M, RecentHighTemperatures =   new int[] {53,57, 51,52,56,55,50}},
                new CityInfo(){ Name = "Mexico City", Latitude = 19.432736M, Longitude = -99.133253M, RecentHighTemperatures =   new int[] {72,68,73,77,76,74,73}},
                new CityInfo(){ Name = "Rio de Janiero", Latitude = -22.908333M, Longitude = -43.196389M, RecentHighTemperatures =   new int[] {72,68,73,82,84,78,84}},
                new CityInfo(){ Name = "Quito", Latitude = -0.25M, Longitude = -78.583333M, RecentHighTemperatures =   new int[] {71,69,70,66,65,64,61}}
            };

            // Add some key/value pairs from multiple threads.
            Task[] tasks = new Task[2];

            tasks[0] = Task.Run(() =>
            {
                for (int i = 0; i < 2; i++)
                {
                    if (cities.TryAdd(data[i].Name, data[i]))
                        Console.WriteLine("Added {0} on thread {1}", data[i],
                            Thread.CurrentThread.ManagedThreadId);
                    else 
                        Console.WriteLine("Could not add {0}", data[i]);
                }
            });

            tasks[1] = Task.Run(() =>
            {
                for (int i = 2; i < data.Length; i++)
                {
                    if (cities.TryAdd(data[i].Name, data[i]))
                        Console.WriteLine("Added {0} on thread {1}", data[i],
                            Thread.CurrentThread.ManagedThreadId);
                    else
                        Console.WriteLine("Could not add {0}", data[i]);
                }
            });

            // Output results so far.
            Task.WaitAll(tasks);

            // Enumerate collection from the app main thread.
            // Note that ConcurrentDictionary is the one concurrent collection
            // that does not support thread-safe enumeration.
            foreach (var city in cities)
            {
                Console.WriteLine("{0} has been added.", city.Key);
            }

            AddOrUpdateWithoutRetrieving();
            RetrieveValueOrAdd();
            RetrieveAndUpdateOrAdd();  

            Console.WriteLine("Press any key.");
            Console.ReadKey();
        }

        // This method shows how to add key-value pairs to the dictionary
        // in scenarios where the key might already exist.
        private static void AddOrUpdateWithoutRetrieving()
        {
            // Sometime later. We receive new data from some source.
            CityInfo ci = new CityInfo() { Name = "Toronto",
                                            Latitude = 43.716589M,
                                            Longitude = -79.340686M,
                                            RecentHighTemperatures = new int[] { 54, 59, 67, 82, 87, 55, -14 } };

            // Try to add data. If it doesn't exist, the object ci is added. If it does
            // already exist, update existingVal according to the custom logic in the 
            // delegate.
            cities.AddOrUpdate(ci.Name, ci,
                (key, existingVal) =>
                {
                    // If this delegate is invoked, then the key already exists.
                    // Here we make sure the city really is the same city we already have.
                    // (Support for multiple cities of the same name is left as an exercise for the reader.)
                    if (ci != existingVal)
                        throw new ArgumentException("Duplicate city names are not allowed: {0}.", ci.Name);

                    // The only updatable fields are the temerature array and lastQueryDate.
                    existingVal.lastQueryDate = DateTime.Now;
                    existingVal.RecentHighTemperatures = ci.RecentHighTemperatures;
                    return existingVal;
                });

            // Verify that the dictionary contains the new or updated data.
            Console.Write("Most recent high temperatures for {0} are: ", cities[ci.Name].Name);
            int[] temps = cities[ci.Name].RecentHighTemperatures;
            foreach (var temp in temps) Console.Write("{0}, ", temp);
            Console.WriteLine();
        }

        // This method shows how to use data and ensure that it has been
        // added to the dictionary.
        private static void RetrieveValueOrAdd()
        {
            string searchKey = "Caracas";
            CityInfo retrievedValue = null;

            try
            {
                retrievedValue = cities.GetOrAdd(searchKey, GetDataForCity(searchKey));
            }
            catch (ArgumentException e)
            {
                Console.WriteLine(e.Message);
            }

            // Use the data.
            if (retrievedValue != null)
            {
                Console.Write("Most recent high temperatures for {0} are: ", retrievedValue.Name);
                int[] temps = cities[retrievedValue.Name].RecentHighTemperatures;
                foreach (var temp in temps) Console.Write("{0}, ", temp);
            }
            Console.WriteLine();
        }




        // This method shows how to retrieve a value from the dictionary,
        // when you expect that the key/value pair already exists,
        // and then possibly update the dictionary with a new value for the key.
        private static void RetrieveAndUpdateOrAdd()
        {
            CityInfo retrievedValue;
            string searchKey = "Buenos Aires";

            if (cities.TryGetValue(searchKey, out retrievedValue))
            {
                // use the data
                Console.Write("Most recent high temperatures for {0} are: ", retrievedValue.Name);
                int[] temps = retrievedValue.RecentHighTemperatures;
                foreach (var temp in temps) Console.Write("{0}, ", temp);

                // Make a copy of the data. Our object will update its lastQueryDate automatically.
                CityInfo newValue = new CityInfo(retrievedValue.Name,
                                                retrievedValue.Longitude,
                                                retrievedValue.Latitude,
                                                retrievedValue.RecentHighTemperatures);

                // Replace the old value with the new value.
                if (!cities.TryUpdate(searchKey, retrievedValue, newValue))
                {
                    //The data was not updated. Log error, throw exception, etc.
                    Console.WriteLine("Could not update {0}", retrievedValue.Name);
                }
            }
            else
            {
                // Add the new key and value. Here we call a method to retrieve
                // the data. Another option is to add a default value here and 
                // update with real data later on some other thread.
                CityInfo newValue = GetDataForCity(searchKey);
                if( cities.TryAdd(searchKey, newValue))
                {
                    // use the data
                    Console.Write("Most recent high temperatures for {0} are: ", newValue.Name);
                    int[] temps = newValue.RecentHighTemperatures;
                    foreach (var temp in temps) Console.Write("{0}, ", temp);
                }
                else
                    Console.WriteLine("Unable to add data for {0}", searchKey);
            }
        }

        //Assume this method knows how to find long/lat/temp info for any specified city.
        static CityInfo GetDataForCity(string name)
        {
            // Real implementation left as exercise for the reader.
            if (String.CompareOrdinal(name, "Caracas") == 0)
                return new CityInfo() { Name = "Caracas", 
                                        Longitude = 10.5M, 
                                        Latitude = -66.916667M,
                                        RecentHighTemperatures = new int[] { 91, 89, 91, 91, 87, 90, 91 } };
            else if (String.CompareOrdinal(name, "Buenos Aires") == 0)
                return new CityInfo() { Name = "Buenos Aires", 
                                        Longitude = -34.61M, 
                                        Latitude = -58.369997M, 
                                        RecentHighTemperatures = new int[] { 80, 86, 89, 91, 84, 86, 88 } };
            else
                throw new ArgumentException("Cannot find any data for {0}", name);
        }
    }
}

```
[ConcurrentDictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentDictionary-2)는 다중 스레드 시나리오에 맞게 만들어졌습니다. 이 컬렉션에서 항목을 추가하거나 제거하기 위해 코드에 잠금을 사용할 필요는 없습니다. 그러나 동일한 키에 새 값을 지정하여 한 스레드에서 값을 검색하고 다른 스레드에서 컬렉션을 바로 업데이트하는 것은 언제든지 가능합니다.

또한 `ConcurrentDictionary<TKey, TValue>`의 모든 메서드가 스레드로부터 안전하지만 모든 메서드, 특히 `GetOrAdd` 및 `AddOrUpdate`가 원자성 메서드인 것은 아닙니다. 이러한 메서드에 전달되는 사용자 대리자는 사전의 내부 잠금 밖에서 호출됩니다. 알려지지 않은 코드에서 모든 스레드를 차단하지 않도록 하기 위해 이렇게 합니다. 따라서 다음과 같은 이벤트 시퀀스가 발생할 수 있습니다.

1. threadA에서 `GetOrAdd`를 호출하고, 항목을 찾지 못한 다음, valueFactory 대리자를 호출하여 Add에 새 항목을 만듭니다.

2. threadB에서 `GetOrAdd`를 동시에 호출하고, 해당 valueFactory 대리자가 호출된 다음 threadA보다 먼저 내부 잠금에 도달하여 threadB의 새 키-값 쌍이 사전에 추가됩니다.

3. threadA의 사용자 대리자가 완료되고 스레드가 잠금에 도달하지만 이제는 해당 항목이 이미 존재하는 것으로 표시됩니다.

4. threadA에서 "Get"을 수행하고 이전에 threadB에서 추가한 데이터를 반환합니다.

따라서 `GetOrAdd`에서 반환한 데이터가 스레드의 valueFactory에서 만든 것과 동일한 데이터라고 보장되지 않습니다. `AddOrUpdate`를 호출할 때에도 비슷한 이벤트 시퀀스가 발생할 수 있습니다. 

## <a name="see-also"></a>참고 항목

[System.Collections.Concurrent](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent)

[스레드로부터 안전한 컬렉션](index.md)




<!--HONumber=Nov16_HO1-->


