---
title: "project.json으로 패키지 종속성 감소"
description: "project.json으로 패키지 종속성 감소"
keywords: .NET, .NET Core
author: cartermp
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 916251e3-87f9-4eee-81ec-94076215e6fa
translationtype: Human Translation
ms.sourcegitcommit: 62fdb3e60b206728d86220076867eb8fd68af82e
ms.openlocfilehash: caae9f0a32c4378488329fd6d88c1dc7bf96ae9a

---

# <a name="reducing-package-dependencies-with-projectjson"></a>project.json으로 패키지 종속성 감소

이 문서에서는 `project.json` 라이브러리를 작성할 때 패키지 종속성을 줄이는 방법에 대해 알아야 할 내용을 다룹니다. 이 문서를 마칠 때는 필요한 종속성만을 사용하도록 라이브러리를 작성하는 방법을 이해하게 될 것입니다. 

## <a name="why-its-important"></a>중요한 이유

.NET Core는 NuGet 패키지로 구성된 제품입니다.  필수 패키지는 [.NET 표준 라이브러리 메타패키지](https://www.nuget.org/packages/NETStandard.Library)입니다. 이는 다른 패키지들로 구성된 NuGet 패키지로,  .NET Framework, .NET Core 및 Xamarin/Mono 같은 여러 .NET 구현에서 작업할 수 있도록 보장하는 패키지 집합을 제공합니다.

그러나 라이브러리에서 포함된 모든 패키지를 사용하지는 않을 것입니다.  라이브러리를 작성하여 NuGet을 통해 배포할 때, 실제로 사용하는 패키지만으로 종속성을 "잘라내는" 것이 가장 바람직합니다.  이렇게 하면 NuGet 패키지에서 전체적인 공간을 적게 차지합니다.

## <a name="how-to-do-it"></a>방법

현재로서는 패키지 참조를 잘라내는 공식 `dotnet` 명령이 없습니다.  이 작업을 수동으로 수행해야 합니다.  일반적인 프로세스는 다음과 같습니다.

1. `project.json`의 `dependencies` 섹션에서 `NETStandard.Library` 버전 `1.6.0`을 참조합니다.
2. 명령줄에서 `dotnet restore`로 패키지를 복원합니다.
3. `project.lock.json` 파일을 검사하고 `NETSTandard.Library` 섹션을 찾습니다.  파일의 시작 부분에 있습니다.
4. `dependencies` 아래에 나열된 모든 패키지를 복사합니다.
5. `.NETStandard.Library` 참조를 제거하고 복사된 패키지로 교체합니다.
6. 필요 없는 패키지에 대한 참조를 제거합니다.

다음 방법 중 하나를 사용하여 필요 없는 패키지를 알아낼 수 있습니다.

1. 시행착오.  패키지를 제거하고 복원하면서 여전히 컴파일되는지를 알아보고 이 프로세스를 반복합니다.
2. 참조를 피킹하여 실제로 어떤 코드가 사용되는지를 알아보기 위해 [ILSpy](http://ilspy.net) 또는 [.NET Reflector](http://www.red-gate.com/products/dotnet-development/reflector) 같은 도구를 사용합니다.  그런 다음 사용 중인 형식에 해당하지 않는 패키지를 제거할 수 있습니다.

## <a name="example"></a>예제 

제네릭 컬렉션 형식에 추가 기능을 제공한 라이브러리를 작성했다고 가정해 보겠습니다.  그런 라이브러리는 `System.Collections` 같은 패키지에 종속되어야 하지만, `System.Net.Http` 같은 패키지에는 전혀 종속되지 않을 수 있습니다.  따라서 이 라이브러리에 필요한 것으로만 패키지 종속성을 잘라낼 수 있습니다.

이 라이브러리를 잘라내려면 `project.json` 파일로 시작해서 `NETStandard.Library` 버전 `1.6.0`에 대한 참조를 추가합니다.

```json
{
    "version":"1.0.0",
    "dependencies":{
        "NETStandard.Library":"1.6.0"
    },
    "frameworks": {
        "netstandard1.0": {}
     }
}
```

그런 다음 `dotnet restore`로 패키지를 복원하고, `project.lock.json` 파일을 검사하고, `NETSTandard.Library`에 대해 복원된 모든 패키지를 찾습니다.

다음은 `netstandard1.0`을 대상으로 할 때 `project.lock.json` 파일의 관련 섹션이 어떤 모양인지를 보여줍니다.

```json
"NETStandard.Library/1.6.0":{
    "type": "package",
    "dependencies": {
        "Microsoft.NETCore.Platforms": "1.0.1",
        "Microsoft.NETCore.Runtime": "1.0.2",
        "System.Collections": "4.0.11",
        "System.Diagnostics.Debug": "4.0.11",
        "System.Diagnostics.Tools": "4.0.1",
        "System.Globalization": "4.0.11",
        "System.IO": "4.1.0",
        "System.Linq": "4.1.0",
        "System.Net.Primitives": "4.0.11",
        "System.ObjectModel": "4.0.12",
        "System.Reflection": "4.1.0",
        "System.Reflection.Extensions": "4.0.1",
        "System.Reflection.Primitives": "4.0.1",
        "System.Resources.ResourceManager": "4.0.1",
        "System.Runtime": "4.1.0",
        "System.Runtime.Extensions": "4.1.0",
        "System.Text.Encoding": "4.0.11",
        "System.Text.Encoding.Extensions": "4.0.11",
        "System.Text.RegularExpressions": "4.1.0",
        "System.Threading": "4.0.11",
        "System.Threading.Tasks": "4.0.11",
        "System.Xml.ReaderWriter": "4.0.11",
        "System.Xml.XDocument": "4.0.11"
    }
}
```

그런 다음, 라이브러리의 `project.json` 파일에 있는 `dependencies` 섹션으로 패키지 참조를 복사하여 `NETStandard.Library` 참조를 교체합니다.

```json
{
    "version":"1.0.0",
    "dependencies":{
        "Microsoft.NETCore.Platforms": "1.0.1",
        "Microsoft.NETCore.Runtime": "1.0.2",
        "System.Collections": "4.0.11",
        "System.Diagnostics.Debug": "4.0.11",
        "System.Diagnostics.Tools": "4.0.1",
        "System.Globalization": "4.0.11",
        "System.IO": "4.1.0",
        "System.Linq": "4.1.0",
        "System.Net.Primitives": "4.0.11",
        "System.ObjectModel": "4.0.12",
        "System.Reflection": "4.1.0",
        "System.Reflection.Extensions": "4.0.1",
        "System.Reflection.Primitives": "4.0.1",
        "System.Resources.ResourceManager": "4.0.1",
        "System.Runtime": "4.1.0",
        "System.Runtime.Extensions": "4.1.0",
        "System.Text.Encoding": "4.0.11",
        "System.Text.Encoding.Extensions": "4.0.11",
        "System.Text.RegularExpressions": "4.1.0",
        "System.Threading": "4.0.11",
        "System.Threading.Tasks": "4.0.11",
        "System.Xml.ReaderWriter": "4.0.11",
        "System.Xml.XDocument": "4.0.11"
    },
    "frameworks":{
        "netstandard1.0": {}
    }
}
```

패키지가 매우 많지만, 이중 다수는 확실히 컬렉션 형식의 확장에 필요하지 않습니다.  패키지를 수동으로 제거하거나 [ILSpy](http://ilspy.net) 또는 [.NET Reflector](http://www.red-gate.com/products/dotnet-development/reflector)와 같은 도구를 사용하여 코드가 실제로 사용하는 패키지를 식별할 수 있습니다.

잘라낸 패키지의 모양은 다음과 같습니다.

```json
{
    "dependencies":{
        "Microsoft.NETCore.Platforms": "1.0.1",
        "Microsoft.NETCore.Runtime": "1.0.2",
        "System.Collections": "4.0.11",
        "System.Linq": "4.1.0",
        "System.Runtime": "4.1.0",
        "System.Runtime.Extensions": "4.1.0",
        "System.Runtime.Handles": "4.0.1",
        "System.Runtime.InteropServices": "4.1.0",
        "System.Runtime.InteropServices.RuntimeInformation": "4.0.0",
        "System.Threading.Tasks": "4.0.11"
    },
    "frameworks":{
        "netstandard1.0": {}
     }
}
```

이제 `NETStandard.Library` 메타패키지에 의존하는 경우 공간을 덜 차지하게 되었습니다.



<!--HONumber=Nov16_HO1-->


