---
title: ASP.NET Core에 대 한 인증 샘플
author: rick-anderson
description: ASP.NET Core 리포지토리의 인증 샘플에 대 한 링크를 제공 합니다.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: d49aef198e926d88f1a6727f84b06f0861c8812d
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187291"
---
# <a name="authentication-samples-for-aspnet-core"></a>ASP.NET Core에 대 한 인증 샘플

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[ASP.NET Core 리포지토리에](https://github.com/aspnet/AspNetCore) 는 *AspNetCore/src/Security/samples* 폴더에 다음 인증 샘플이 포함 되어 있습니다.

* [클레임 변환](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/ClaimsTransformation)
* [쿠키 인증](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/Cookies)
* [사용자 지정 정책 공급자-IAuthorizationPolicyProvider](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider)
* [동적 인증 스키마 및 옵션](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/DynamicSchemes)
* [외부 클레임](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/Identity.ExternalClaims)
* [요청에 따라 쿠키와 다른 인증 체계를 선택 합니다.](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/PathSchemeSelection)
* [정적 파일에 대 한 액세스를 제한 합니다.](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>샘플 실행

* [분기](https://github.com/aspnet/AspNetCore)를 선택 합니다. 예를 들면 `Tag:v3.0.0`과 같습니다.
* [ASP.NET Core 리포지토리](https://github.com/aspnet/AspNetCore)를 복제 하거나 다운로드 합니다.
* ASP.NET Core 리포지토리의 복제본과 일치 하는 [.NET Core SDK](https://www.microsoft.com/net/download/all) 버전을 설치 했는지 확인 합니다.
* *AspNetCore/src/Security/samples* 에서 샘플로 이동 하 여로 `dotnet run`샘플을 실행 합니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[ASP.NET Core 리포지토리에](https://github.com/aspnet/AspNetCore) 는 *AspNetCore/src/Security/samples* 폴더에 다음 인증 샘플이 포함 되어 있습니다.

* [클레임 변환](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [쿠키 인증](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [사용자 지정 정책 공급자-IAuthorizationPolicyProvider](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [동적 인증 스키마 및 옵션](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [외부 클레임](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [요청에 따라 쿠키와 다른 인증 체계를 선택 합니다.](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [정적 파일에 대 한 액세스를 제한 합니다.](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>샘플 실행

* [분기](https://github.com/aspnet/AspNetCore)를 선택 합니다. 예를 들면 `release/2.2`과 같습니다.
* [ASP.NET Core 리포지토리](https://github.com/aspnet/AspNetCore)를 복제 하거나 다운로드 합니다.
* ASP.NET Core 리포지토리의 복제본과 일치 하는 [.NET Core SDK](https://www.microsoft.com/net/download/all) 버전을 설치 했는지 확인 합니다.
* *AspNetCore/src/Security/samples* 에서 샘플로 이동 하 여로 `dotnet run`샘플을 실행 합니다.

::: moniker-end
