---
title: ".NET Core RID(런타임 식별자) 카탈로그"
description: ".NET Core RID(런타임 식별자) 카탈로그"
keywords: .NET, .NET Core
author: blackdwarf
manager: wpickett
ms.date: 08/22/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: b2032f5d-771f-48d9-917c-587d9509035c
translationtype: Human Translation
ms.sourcegitcommit: 682b27c575e63c3a9efeba8fe14bad6288d02664
ms.openlocfilehash: be3ada8eb885d341c03a004d16fb523281f12870

---

# <a name="net-core-runtime-identifier-rid-catalog"></a>.NET Core RID(런타임 식별자) 카탈로그

## <a name="what-are-rids"></a>RID란?
RID는 *Runtime IDentifier(런타임 식별자)*의 약어입니다. RID는 응용 프로그램 또는 자산(즉, 어셈블리)이 실행될 대상 운영 체제를 식별하기 위해 사용됩니다. RID의 예를 들면 "ubuntu.14.04-x64", "win7-x64", "osx.10.11-x64"와 같습니다. 기본 종속성이 있는 패키지의 경우 RID는 패키지를 복원할 수 있는 플랫폼을 지정합니다. 

RID는 실제로 불투명한 문자열이라는 점에 유의해야 합니다. 즉, RID는 사용하는 작업에 대해 정확히 일치해야만 작동합니다. 예를 들어, Ubuntu 14.04의 간단한 복제본인 [Elementary OS](https://elementary.io/)의 경우를 고려해 보겠습니다. .NET Core와 CLI는 Ubuntu의 14.04 버전에서 작동하지만, 수정 없이 Elementary OS에서 사용하려고 하면 패키지에 대한 복원 작업이 모두 실패합니다. 그 이유는 Elementary OS를 플랫폼으로 지정하는 RID가 현재 없기 때문입니다. 

구체적인 운영 체제를 나타내는 RID는 일반적으로 `[os].[version]-[arch]`의 패턴을 따릅니다. 각각은 다음과 같습니다.
- `[os]` - 운영 체제 모니커입니다(예: `ubuntu`).
- `[version]` - 점(`.`) 형태로 구분된 버전 번호 형식의 운영 체제 버전입니다(예: `15.10`). 자산이 해당 버전으로 표시되는 운영 체제 플랫폼 API를 대상으로 지정하도록 할 만큼 충분히 정확합니다.
  - 이것은 마케팅 버전이어서는 **안 됩니다**. 마케팅 버전은 종종 다양한 플랫폼 API 노출 영역이 있는 운영 체제의 여러 개별 버전을 나타내기 때문입니다.
- `[arch]` - 프로세서 아키텍처입니다(예: `x86`, `x64`, `arm`, `arm64` 등).

RID 그래프는 `runtime.json`라는 파일의 `Microsoft.NETCore.Platforms`라는 패키지에서 정의됩니다. [CoreFX 리포지토리](https://github.com/dotnet/corefx/blob/master/pkg/Microsoft.NETCore.Platforms/runtime.json)에서 이를 볼 수 있습니다. 이 파일을 사용하는 경우 일부 RID에 `"#import"` 문이 있는 것을 볼 수 있습니다. 이러한 문은 호환성 문입니다. 즉, 가져온 RID를 포함하는 RID는 해당 RID용 패키지 복원을 위한 대상이 될 수 있습니다. 약간 복잡하지만 예를 살펴보겠습니다. macOS를 살펴보겠습니다.

```json
"osx.10.11-x64": {
    "#import": [ "osx.10.11", "osx.10.10-x64" ]
}
```
위의 RID는 `osx.10.11-x64`가 `osx.10.10-x64`를 가져오도록 지정합니다. 즉, 패키지를 복원할 때 NuGet은 `osx.10.11-x64`에 `osx.10.10-x64`가 필요함을 지정하는 패키지를 복원할 수 있습니다.

다음은 약간 더 큰 RID 그래프의 예제입니다.  

- `win10-arm`
  - `win10`
  - `win81-arm`
    - `win81`
    - `win8-arm`
      - `win8`
        - `win7`
          - `win`
            - `any`

모든 RID는 결국 루트 `any` RID에 다시 매핑됩니다.

비록 사용하기 쉬워 보이지만, RID로 작업할 때 유의해야 할 몇 가지 특별한 사항이 있습니다.

* RID는 **불투명 문자열**이며 블랙박스로 취급해야 합니다.
    * RID를 프로그래밍 방식으로 구성하지 않아야 합니다.
* 플랫폼에 대해 이미 정의되어 있고 이 문서에서 보여준 RID를 사용해야 합니다.
* RID는 구체적이어야 하므로 실제 RID 값에서 어떤 것도 가정해서는 안 됩니다. 특정 플랫폼에 어떤 RID가 필요한지를 확인하려면 이 문서를 참조하세요.

## <a name="using-rids"></a>RID 사용
RID를 사용하려면 어떤 RID가 있는지를 알아야 합니다. 새 RID가 정기적으로 플랫폼에 추가됩니다. 최신 버전을 보려면 CoreFX 리포지토리에서 [runtime.json](https://github.com/dotnet/corefx/blob/master/pkg/Microsoft.NETCore.Platforms/runtime.json) 파일을 확인하세요.

> [!NOTE]
> 이 정보를 좀 더 대화형 방식으로 얻을 수 있도록 작업하고 있습니다. 그렇게 되면 이 페이지도 해당 도구 및/또는 사용 방법 문서를 안내하도록 업데이트될 것입니다. 

## <a name="windows-rids"></a>Windows RID

* Windows 7 / Windows Server 2008 R2
    * `win7-x64`
    * `win7-x86`
* Windows 8 / Windows Server 2012
    * `win8-x64`
    * `win8-x86`
    * `win8-arm`
* Windows 8.1 / Windows Server 2012 R2
    * `win81-x64`
    * `win81-x86`
    * `win81-arm`
* Windows 10 / Windows Server 2016
    * `win10-x64`
    * `win10-x86`
    * `win10-arm`
    * `win10-arm64`

## <a name="linux-rids"></a>Linux RID

* Red Hat Enterprise Linux
    * `rhel.7.0-x64`
    * `rhel.7.1-x64`
    * `rhel.7.2-x64`
* Ubuntu
    * `ubuntu.14.04-x64`
    * `ubuntu.14.10-x64`
    * `ubuntu.15.04-x64`
    * `ubuntu.15.10-x64`
    * `ubuntu.16.04-x64`
    * `ubuntu.16.10-x64`
* CentOS
    * `centos.7-x64`
* Debian
    * `debian.8-x64`
* Fedora
    * `fedora.23-x64`
    * `fedora.24-x64`
* OpenSUSE
    * `opensuse.13.2-x64`
    * `opensuse.42.1-x64`
* Oracle Linux
    * `ol.7-x64`
    * `ol.7.0-x64`
    * `ol.7.1-x64`
    * `ol.7.2-x64`
* 현재 지원되는 Ubuntu 파생 버전 
    * `linuxmint.17-x64`
    * `linuxmint.17.1-x64`
    * `linuxmint.17.2-x64`
    * `linuxmint.17.3-x64`
    * `linuxmint.18-x64`

## <a name="os-x-rids"></a>OS X RID

* `osx.10.10-x64`
* `osx.10.11-x64`
* `osx.10.12-x64`



<!--HONumber=Nov16_HO1-->


