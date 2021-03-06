---
title: ASP.NET Core Blazor 지원 플랫폼
author: guardrex
description: ASP.NET Core Blazor에 대해 지원 되는 플랫폼에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: blazor/supported-platforms
ms.openlocfilehash: 4e86bd6967a747a59c99a515c1c838cc2c21770f
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391226"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>ASP.NET Core Blazor 지원 플랫폼

작성자: [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a>브라우저 요구 사항

### <a name="blazor-webassembly"></a>Blazor WebAssembly

| 브라우저                          | Version               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | 현재               |
| Mozilla Firefox                  | 현재               |
| Google Chrome (Android 포함) | 현재               |
| Safari (iOS 포함)            | 현재               |
| Microsoft Internet Explorer      | 지원 되지 않음 @ no__t-0 |

&dagger; Microsoft Internet Explorer는 [Weasembmbc를](https://webassembly.org)지원 하지 않습니다.

### <a name="blazor-server"></a>Blazor 서버

| 브라우저                          | Version    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | 현재    |
| Mozilla Firefox                  | 현재    |
| Google Chrome (Android 포함) | 현재    |
| Safari (iOS 포함)            | 현재    |
| Microsoft Internet Explorer      | 11 @ no__t-0 |

&dagger; 추가 polyfills 필요 합니다. 예를 들어 [Polyfill.io](https://polyfill.io/v3/) 번들을 통해 약속을 추가할 수 있습니다.

## <a name="additional-resources"></a>추가 자료

* <xref:blazor/hosting-models>
