---
title: ASP.NET Core Id가 없는 Facebook, Google 및 외부 공급자 인증
author: rick-anderson
description: ASP.NET Core Id 없이 Facebook, Google, Twitter 등의 계정 사용자 인증을 사용 하는 방법에 대 한 설명입니다.
ms.author: riande
ms.date: 09/25/2019
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: 54dd93a13b2f7ed09c2c305f529d5f4610567184
ms.sourcegitcommit: 6d26ab647ede4f8e57465e29b03be5cb130fc872
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2019
ms.locfileid: "71999892"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a>ASP.NET Core Id 없이 소셜 로그인 공급자 인증 사용

::: moniker range=">= aspnetcore-3.0"

<xref:security/authentication/social/index>은 사용자가 외부 인증 공급자의 자격 증명으로 OAuth 2.0를 사용 하 여 로그인 할 수 있게 하는 방법을 설명 합니다. 이 항목에서 설명 하는 접근 방식에는 인증 공급자로 Id ASP.NET Core 포함 되어 있습니다.

이 샘플에서는 ASP.NET Core Id **없이** 외부 인증 공급자를 사용 하는 방법을 보여 줍니다. 이는 ASP.NET Core Id의 모든 기능이 필요 하지는 않지만 여전히 신뢰할 수 있는 외부 인증 공급자와 통합 해야 하는 앱에 유용 합니다.

이 샘플에서는 사용자를 인증 하는 데 [Google 인증](xref:security/authentication/google-logins) 을 사용 합니다. Google 인증을 사용 하면 로그인 프로세스를 관리 하는 복잡 한 여러 가지 복잡성이 Google으로 이동 됩니다. 다른 외부 인증 공급자와 통합 하려면 다음 항목을 참조 하십시오.

* [Facebook 인증](xref:security/authentication/facebook-logins)
* [Microsoft 인증](xref:security/authentication/microsoft-logins)
* [Twitter 인증](xref:security/authentication/twitter-logins)
* [기타 공급자](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Configuration

@No__t-0 메서드에서 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> 및 <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> 메서드를 사용 하 여 앱의 인증 체계를 구성 합니다.

[!code-csharp[](social-without-identity/3.0sample/Startup.cs?name=snippet1)]

@No__t-0에 대 한 호출은 앱의 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>을 설정 합니다. @No__t-0은 다음 `HttpContext` 인증 확장 메서드에서 사용 하는 기본 체계입니다.

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

앱의 `DefaultScheme`을 [CookieAuthenticationDefaults](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("쿠키")로 설정 하면 해당 확장 메서드에 대 한 기본 스키마로 쿠키를 사용 하도록 앱을 구성 합니다. 앱의 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme>을 [GoogleDefaults](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google")로 설정 하면 Google을 `ChallengeAsync`에 대 한 호출에 대 한 기본 체계로 사용 하도록 앱이 구성 됩니다. `DefaultChallengeScheme`는 `DefaultScheme`를 재정의합니다. 설정 시 `DefaultScheme`을 재정의 하는 추가 속성은 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>을 참조 하세요.

@No__t-0에서 `UseAuthentication`을 호출 하 고 `UseAuthorization`를 호출 하 여 `HttpContext.User` 속성을 설정 하 고 요청에 대 한 권한 부여 미들웨어를 실행 합니다. @No__t-2를 호출 하기 전에 `UseAuthentication` 및 `UseAuthorization` 메서드를 호출 합니다.

[!code-csharp[](social-without-identity/3.0sample/Startup.cs?name=snippet2)]

인증 체계 및 쿠키 인증에 대 한 자세한 내용은 <xref:security/authentication/cookie>을 참조 하세요.

## <a name="apply-authorization"></a>권한 부여 적용

컨트롤러, 작업 또는 페이지에 `AuthorizeAttribute` 특성을 적용 하 여 앱의 인증 구성을 테스트 합니다. 다음 코드는 인증 된 사용자에 대 한 *개인 정보* 페이지에 대 한 액세스를 제한 합니다.

[!code-csharp[](social-without-identity/3.0sample/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>로그아웃

현재 사용자를 로그 아웃 하 고 쿠키를 삭제 하려면 [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)를 호출 합니다. 다음 코드는 *인덱스* 페이지에 `Logout` 페이지 처리기를 추가 합니다.

[!code-csharp[](social-without-identity/3.0sample/Pages/Index.cshtml.cs?name=snippet&highlight=14-18)]

@No__t-0에 대 한 호출은 인증 체계를 지정 하지 않습니다. @No__t-1의 `DefaultScheme` 인 앱은 대체로 사용 됩니다.

## <a name="additional-resources"></a>추가 자료

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<xref:security/authentication/social/index>은 사용자가 외부 인증 공급자의 자격 증명으로 OAuth 2.0를 사용 하 여 로그인 할 수 있게 하는 방법을 설명 합니다. 이 항목에서 설명 하는 접근 방식에는 인증 공급자로 Id ASP.NET Core 포함 되어 있습니다.

이 샘플에서는 ASP.NET Core Id **없이** 외부 인증 공급자를 사용 하는 방법을 보여 줍니다. 이는 ASP.NET Core Id의 모든 기능이 필요 하지는 않지만 여전히 신뢰할 수 있는 외부 인증 공급자와 통합 해야 하는 앱에 유용 합니다.

이 샘플에서는 사용자를 인증 하는 데 [Google 인증](xref:security/authentication/google-logins) 을 사용 합니다. Google 인증을 사용 하면 로그인 프로세스를 관리 하는 복잡 한 여러 가지 복잡성이 Google으로 이동 됩니다. 다른 외부 인증 공급자와 통합 하려면 다음 항목을 참조 하십시오.

* [Facebook 인증](xref:security/authentication/facebook-logins)
* [Microsoft 인증](xref:security/authentication/microsoft-logins)
* [Twitter 인증](xref:security/authentication/twitter-logins)
* [기타 공급자](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Configuration

@No__t-0 메서드에서 `AddAuthentication`, `AddCookie` 및 `AddGoogle` 메서드를 사용 하 여 앱의 인증 체계를 구성 합니다.

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet1)]

[Addauthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) 에 대 한 호출은 앱의 [defaultscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)을 설정 합니다. @No__t-0은 다음 `HttpContext` 인증 확장 메서드에서 사용 하는 기본 체계입니다.

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

앱의 `DefaultScheme`을 [CookieAuthenticationDefaults](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("쿠키")로 설정 하면 해당 확장 메서드에 대 한 기본 스키마로 쿠키를 사용 하도록 앱을 구성 합니다. 앱의 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme>을 [GoogleDefaults](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google")로 설정 하면 Google을 `ChallengeAsync`에 대 한 호출에 대 한 기본 체계로 사용 하도록 앱이 구성 됩니다. `DefaultChallengeScheme`는 `DefaultScheme`를 재정의합니다. 설정 시 `DefaultScheme`을 재정의 하는 추가 속성은 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>을 참조 하세요.

@No__t-0 메서드에서는 `UseAuthentication` 메서드를 호출 하 여 `HttpContext.User` 속성을 설정 하는 인증 미들웨어를 호출 합니다. @No__t-1 또는 `UseMvc`를 호출 하기 전에 `UseAuthentication` 메서드를 호출 합니다.

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet2)]

인증 체계 및 쿠키 인증에 대 한 자세한 내용은 <xref:security/authentication/cookie>을 참조 하세요.

## <a name="apply-authorization"></a>권한 부여 적용

컨트롤러, 작업 또는 페이지에 `AuthorizeAttribute` 특성을 적용 하 여 앱의 인증 구성을 테스트 합니다. 다음 코드는 인증 된 사용자에 대 한 *개인 정보* 페이지에 대 한 액세스를 제한 합니다.

[!code-csharp[](social-without-identity/sample/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>로그아웃

현재 사용자를 로그 아웃 하 고 쿠키를 삭제 하려면 [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)를 호출 합니다. 다음 코드는 *인덱스* 페이지에 `Logout` 페이지 처리기를 추가 합니다.

[!code-csharp[](social-without-identity/sample/Pages/Index.cshtml.cs?name=snippet&highlight=7-11)]

@No__t-0에 대 한 호출은 인증 체계를 지정 하지 않습니다. @No__t-1의 `DefaultScheme` 인 앱은 대체로 사용 됩니다.

## <a name="additional-resources"></a>추가 자료

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
