---
title: ASP.NET Core에서 HTTPS 적용
author: rick-anderson
description: ASP.NET Core 웹 앱에서 HTTPS/TLS를 요구 하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2019
uid: security/enforcing-ssl
ms.openlocfilehash: 044e9d594fa037214d80898e3ecc420d80a6f869
ms.sourcegitcommit: 73a451e9a58ac7102f90b608d661d8c23dd9bbaf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72037625"
---
# <a name="enforce-https-in-aspnet-core"></a>ASP.NET Core에서 HTTPS 적용

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

이 문서에서는 다음과 같은 내용을 살펴봅니다.

* 모든 요청에 대해 HTTPS를 요구 합니다.
* 모든 HTTP 요청을 HTTPS로 리디렉션합니다.

API가 없으면 클라이언트가 첫 번째 요청에서 중요 한 데이터를 전송 하지 못할 수 있습니다.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a>API 프로젝트
>
> 중요 한 정보를 수신 하는 웹 Api에 [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) 를 사용 **하지** 마세요. `RequireHttpsAttribute`은 HTTP 상태 코드를 사용 하 여 HTTP에서 HTTPS로 브라우저를 리디렉션합니다. API 클라이언트는 HTTP에서 HTTPS로의 리디렉션을 인식 하거나 준수 하지 않을 수 있습니다. 이러한 클라이언트는 HTTP를 통해 정보를 보낼 수 있습니다. 웹 Api는 다음 중 하나를 수행 해야 합니다.
>
> * HTTP에서 수신 하지 않습니다.
> * 상태 코드 400 (잘못 된 요청)이 포함 된 연결을 닫고 요청을 제공 하지 않습니다.
>
> ## <a name="hsts-and-api-projects"></a>HSTS 및 API 프로젝트
>
> HSTS는 일반적으로 브라우저 전용 명령 이므로 기본 API 프로젝트는 [Hsts](#hsts) 를 포함 하지 않습니다. 전화 또는 데스크톱 앱과 같은 다른 호출자는 지침을 따르지 **않습니다** . 브라우저 내 에서도 HTTP를 통한 API에 대 한 인증 된 단일 호출은 안전 하지 않은 네트워크에서 위험이 있습니다. 안전한 방법은 HTTPS를 통해서만 수신 대기 하 고 응답 하도록 API 프로젝트를 구성 하는 것입니다.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

> [!WARNING]
> ## <a name="api-projects"></a>API 프로젝트
>
> 중요 한 정보를 수신 하는 웹 Api에 [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) 를 사용 **하지** 마세요. `RequireHttpsAttribute`은 HTTP 상태 코드를 사용 하 여 HTTP에서 HTTPS로 브라우저를 리디렉션합니다. API 클라이언트는 HTTP에서 HTTPS로의 리디렉션을 인식 하거나 준수 하지 않을 수 있습니다. 이러한 클라이언트는 HTTP를 통해 정보를 보낼 수 있습니다. 웹 Api는 다음 중 하나를 수행 해야 합니다.
>
> * HTTP에서 수신 하지 않습니다.
> * 상태 코드 400 (잘못 된 요청)이 포함 된 연결을 닫고 요청을 제공 하지 않습니다.

::: moniker-end

## <a name="require-https"></a>HTTPS 필요

프로덕션 ASP.NET Core 웹 앱을 사용 하는 것이 좋습니다.

* Https 리디렉션 미들웨어 (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>)-HTTP 요청을 HTTPS로 리디렉션합니다.
* 클라이언트에 HSTS (HTTP Strict Transport Security Protocol) 헤더를 전송 하는 HSTS 미들웨어 ([Usehsts](#http-strict-transport-security-protocol-hsts))

> [!NOTE]
> 역방향 프록시 구성에 배포 된 앱을 통해 프록시가 연결 보안 (HTTPS)을 처리할 수 있습니다. 프록시가 HTTPS 리디렉션을 처리 하는 경우에는 HTTPS 리디렉션 미들웨어를 사용할 필요가 없습니다. 또한 프록시 서버에서 HSTS 헤더 쓰기를 처리 하는 경우 (예: [IIS 10.0 (1709) 이상에서 기본 hsts 지원](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)) 앱에 Hsts 미들웨어가 필요 하지 않습니다. 자세한 내용은 [프로젝트를 만들 때 HTTPS/HSTS 옵트아웃 (Opt out)](#opt-out-of-httpshsts-on-project-creation)을 참조 하세요.

### <a name="usehttpsredirection"></a>UseHttpsRedirection

다음 코드는 `Startup` 클래스에서 `UseHttpsRedirection`을 호출 합니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=13)]

::: moniker-end

앞에서 강조 표시 된 코드:

* 기본 [HttpsRedirectionOptions](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect))를 사용 합니다.
* @No__t-1 환경 변수 또는 [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature)에 의해 재정의 되지 않는 한 기본 [HttpsRedirectionOptions](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null)를 사용 합니다.

영구 리디렉션이 아닌 임시 리디렉션을 사용 하는 것이 좋습니다. 링크 캐싱은 개발 환경에서 불안정 한 동작을 일으킬 수 있습니다. 앱이 개발 환경에 있지 않은 경우 영구 리디렉션 상태 코드를 보내려면 [프로덕션에서 영구 리디렉션 구성](#configure-permanent-redirects-in-production) 섹션을 참조 하세요. [Hsts](#http-strict-transport-security-protocol-hsts) 를 사용 하 여 보안 리소스 요청만 앱에 전송 되어야 한다는 것을 클라이언트에 알리는 것이 좋습니다 (프로덕션 환경 에서만).

### <a name="port-configuration"></a>포트 구성

미들웨어가 안전 하지 않은 요청을 HTTPS로 리디렉션하는 데 포트를 사용할 수 있어야 합니다. 포트를 사용할 수 없는 경우:

* HTTPS로의 리디렉션이 발생 하지 않습니다.
* 미들웨어는 "리디렉션을 위한 https 포트를 결정 하지 못했습니다." 경고를 기록 합니다.

다음 방법 중 하나를 사용 하 여 HTTPS 포트를 지정 합니다.

* [HttpsRedirectionOptions](#options)를 설정 합니다.

::: moniker range=">= aspnetcore-3.0"

* @No__t-0 [호스트 설정을](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#https_port)설정 합니다.

  * 호스트 구성에서.
  * @No__t-0 환경 변수를 설정 합니다.
  * Appsettings에서 최상위 항목을 추가 *합니다. json*:

    [!code-json[](enforcing-ssl/sample-snapshot/3.x/appsettings.json?highlight=2)]

* [ASPNETCORE_URLS 환경 변수](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#urls)를 사용 하 여 보안 체계가 있는 포트를 표시 합니다. 환경 변수는 서버를 구성 합니다. 미들웨어는 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>을 통해 HTTPS 포트를 간접적으로 검색 합니다. 이 방법은 역방향 프록시 배포에서 작동 하지 않습니다.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

* @No__t-0 [호스트 설정을](xref:fundamentals/host/web-host#https-port)설정 합니다.

  * 호스트 구성에서.
  * @No__t-0 환경 변수를 설정 합니다.
  * Appsettings에서 최상위 항목을 추가 *합니다. json*:

    [!code-json[](enforcing-ssl/sample-snapshot/2.x/appsettings.json?highlight=2)]

* [ASPNETCORE_URLS 환경 변수](xref:fundamentals/host/web-host#server-urls)를 사용 하 여 보안 체계가 있는 포트를 표시 합니다. 환경 변수는 서버를 구성 합니다. 미들웨어는 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>을 통해 HTTPS 포트를 간접적으로 검색 합니다. 이 방법은 역방향 프록시 배포에서 작동 하지 않습니다.

::: moniker-end

* 개발에서 *launchsettings. json*에 HTTPS URL을 설정 합니다. IIS Express 사용 하는 경우 HTTPS를 사용 하도록 설정 합니다.

* [Kestrel](xref:fundamentals/servers/kestrel) 서버 또는 [http.sys](xref:fundamentals/servers/httpsys) 서버의 공용에 지 배포에 대 한 HTTPS URL 끝점을 구성 합니다. 앱에서 **HTTPS 포트** 를 하나만 사용 합니다. 미들웨어는 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>을 통해 포트를 검색 합니다.

> [!NOTE]
> 앱이 역방향 프록시 구성에서 실행 되는 경우 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>을 사용할 수 없습니다. 이 섹션에 설명 된 다른 방법 중 하나를 사용 하 여 포트를 설정 합니다.

### <a name="edge-deployments"></a>Edge 배포 

Kestrel 또는 HTTP.SYS를 공용에 지 서버로 사용 하는 경우 Kestrel 또는 HTTP.SYS를 모두 수신 하도록 구성 해야 합니다.

* 클라이언트가 리디렉션되는 보안 포트 (일반적으로 프로덕션의 경우 443, 개발 중인 경우 5001)
* 안전 하지 않은 포트 (일반적으로 프로덕션의 경우 80, 개발에서 5000)

응용 프로그램이 안전 하지 않은 요청을 받고 클라이언트를 보안 포트로 리디렉션하도록 클라이언트에서 보안 되지 않은 포트에 액세스할 수 있어야 합니다.

자세한 내용은 [Kestrel 끝점 구성](xref:fundamentals/servers/kestrel#endpoint-configuration) 또는 <xref:fundamentals/servers/httpsys>을 참조 하세요.

### <a name="deployment-scenarios"></a>배포 시나리오

클라이언트와 서버 간의 모든 방화벽에는 트래픽에 대 한 통신 포트도 열려 있어야 합니다.

역방향 프록시 구성에서 요청을 전달 하는 경우 HTTPS 리디렉션 미들웨어를 호출 하기 전에 [전달 된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer) 를 사용 합니다. 전달 된 헤더 미들웨어는 `X-Forwarded-Proto` 헤더를 사용 하 여 `Request.Scheme`을 업데이트 합니다. 미들웨어는 리디렉션 Uri 및 기타 보안 정책이 제대로 작동 하도록 허용 합니다. 전달 된 헤더 미들웨어를 사용 하지 않는 경우 백엔드 앱은 올바른 체계를 수신 하지 않고 리디렉션 루프에서 종료 될 수 있습니다. 일반적인 최종 사용자 오류 메시지는 너무 많은 리디렉션이 발생 한 것입니다.

Azure App Service에 배포 하는 경우 [Tutorial의 지침을 따르세요. 기존 사용자 지정 SSL 인증서를 Azure Web Apps에 바인딩](/azure/app-service/app-service-web-tutorial-custom-ssl)을 참조하세요.

### <a name="options"></a>변수

다음 강조 표시 된 코드는 [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) 을 호출 하 여 미들웨어 옵션을 구성 합니다.


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end


@No__t-0을 호출 하는 것은 `HttpsPort` 또는 `RedirectStatusCode`의 값을 변경 하는 데만 필요 합니다.

앞에서 강조 표시 된 코드:

* [HttpsRedirectionOptions](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) 를 기본값 인 <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>로 설정 합니다. @No__t-0 클래스의 필드를 사용 하 여 `RedirectStatusCode`로 지정 합니다.
* HTTPS 포트를 5001으로 설정 합니다. 기본값은 443입니다.

#### <a name="configure-permanent-redirects-in-production"></a>프로덕션 환경에서 영구 리디렉션 구성

미들웨어는 기본적으로 모든 리디렉션을 사용 하 여 [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) 를 보냅니다. 앱이 개발 환경에 있지 않은 경우 영구 리디렉션 상태 코드를 보내려면 비 개발 환경에 대 한 조건부 검사에서 미들웨어 옵션 구성을 래핑합니다.

::: moniker range=">= aspnetcore-3.0"

*Startup.cs*에서 서비스를 구성 하는 경우:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IWebHostEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

*Startup.cs*에서 서비스를 구성 하는 경우:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IHostingEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end


## <a name="https-redirection-middleware-alternative-approach"></a>HTTPS 리디렉션 미들웨어 대체 방법

HTTPS 리디렉션 미들웨어 (`UseHttpsRedirection`)를 사용 하는 대신 URL 재작성 미들웨어 (`AddRedirectToHttps`)를 사용할 수 있습니다. `AddRedirectToHttps`은 리디렉션이 실행 될 때 상태 코드 및 포트를 설정할 수도 있습니다. 자세한 내용은 [URL 재작성 미들웨어](xref:fundamentals/url-rewriting)를 참조 하세요.

추가 리디렉션 규칙을 요구 하지 않고 HTTPS로 리디렉션하는 경우이 항목에서 설명 하는 HTTPS 리디렉션 미들웨어 (`UseHttpsRedirection`)를 사용 하는 것이 좋습니다.

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a>HTTP HSTS (Strict Transport Security Protocol)

[OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project)에 따라 [hsts (HTTP Strict Transport security)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) 는 응답 헤더를 사용 하 여 웹 앱에서 지정 하는 옵트인 (opt in) 보안 기능입니다. [HSTS를 지 원하는 브라우저가](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) 이 헤더를 수신 하는 경우:

* 브라우저는 HTTP를 통한 통신 전송을 방지 하는 도메인에 대 한 구성을 저장 합니다. 브라우저는 HTTPS를 통한 모든 통신을 강제로 수행 합니다.
* 브라우저는 사용자가 신뢰할 수 없거나 유효 하지 않은 인증서를 사용 하지 못하도록 합니다. 브라우저는 사용자가 이러한 인증서를 일시적으로 신뢰할 수 있도록 하는 프롬프트를 사용 하지 않도록 설정 합니다.

HSTS는 클라이언트에 의해 적용 되므로 다음과 같은 몇 가지 제한 사항이 있습니다.

* 클라이언트는 HSTS를 지원 해야 합니다.
* HSTS는 HSTS 정책을 설정 하기 위해 하나 이상의 성공한 HTTPS 요청이 필요 합니다.
* 응용 프로그램은 모든 HTTP 요청을 확인 하 고 HTTP 요청을 리디렉션하거나 거부 해야 합니다.

ASP.NET Core 2.1 이상에서는 `UseHsts` 확장 메서드를 사용 하 여 HSTS를 구현 합니다. 다음 코드는 앱이 [개발 모드가](xref:fundamentals/environments)아닐 때 @no__t를 호출 합니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=10)]

::: moniker-end

`UseHsts`은 브라우저에서 HSTS 설정을 캐시할 수 있으므로 개발에 권장 되지 않습니다. 기본적으로 `UseHsts`은 로컬 루프백 주소를 제외 합니다.

처음으로 HTTPS를 구현 하는 프로덕션 환경의 경우 <xref:System.TimeSpan> 방법 중 하나를 사용 하 여 초기 [MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) 을 작은 값으로 설정 합니다. HTTPS 인프라를 HTTP로 되돌려야 하는 경우에는 값을 하루에 한 번 이상으로 설정 해야 합니다. HTTPS 구성의 유지 가능성을 확신 하는 경우 HSTS 최대 기간 값을 늘립니다. 일반적으로 사용 되는 값은 1 년입니다.

다음 예를 참조하십시오.


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end


* 엄격한 전송 보안 헤더의 미리 로드 매개 변수를 설정 합니다. 프리 로드는 [RFC hsts 사양의](https://tools.ietf.org/html/rfc6797)일부가 아니지만 웹 브라우저가 새로 설치 시 hsts 사이트를 미리 로드 하도록 지원 합니다. 자세한 내용은 [https://hstspreload.org/](https://hstspreload.org/)를 참조하세요.
* 도메인을 호스트 하는 데 HSTS 정책을 적용 하는 [Includesubdomain 도메인](https://tools.ietf.org/html/rfc6797#section-6.1.2)을 사용 하도록 설정 합니다.
* 엄격한 전송 보안 헤더의 최대 보존 기간 매개 변수를 60 일로 명시적으로 설정 합니다. 설정 되지 않은 경우 기본값은 30 일입니다. 자세한 내용은 [최대 사용 기간 지시문](https://tools.ietf.org/html/rfc6797#section-6.1.1) 을 참조 하십시오.
* 제외할 호스트 목록에 `example.com`을 추가 합니다.

`UseHsts`은 다음 루프백 호스트를 제외 합니다.

* `localhost`은: IPv4 루프백 주소입니다.
* `127.0.0.1`은: IPv4 루프백 주소입니다.
* `[::1]`은: IPv6 루프백 주소입니다.

## <a name="opt-out-of-httpshsts-on-project-creation"></a>프로젝트를 만들 때 HTTPS/HSTS 옵트아웃 (Opt out)

네트워크의 공용 가장자리에서 연결 보안이 처리 되는 일부 백 엔드 서비스 시나리오에서는 각 노드에서 연결 보안을 구성할 필요가 없습니다. Visual Studio 또는 [dotnet new](/dotnet/core/tools/dotnet-new) 명령에서 템플릿에서 생성 된 웹 앱은 [HTTPS 리디렉션과](#require-https) [hsts](#http-strict-transport-security-protocol-hsts)를 사용 하도록 설정 합니다. 이러한 시나리오가 필요 하지 않은 배포의 경우 템플릿에서 앱을 만들 때 HTTPS/HSTS를 옵트아웃 (opt out) 할 수 있습니다.

HTTPS/HSTS를 옵트아웃 (opt out) 하려면:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

**HTTPS에 대해 구성** 확인란의 선택을 취소 합니다.

::: moniker range=">= aspnetcore-3.0"

![HTTPS에 대해 구성 확인란을 선택 하지 않은 새 ASP.NET Core 웹 응용 프로그램 대화 상자](enforcing-ssl/_static/out-vs2019.png)

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

![HTTPS에 대해 구성 확인란을 선택 하지 않은 새 ASP.NET Core 웹 응용 프로그램 대화 상자](enforcing-ssl/_static/out.png)

::: moniker-end


# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli) 

`--no-https` 옵션을 사용합니다. 예

```dotnetcli
dotnet new webapp --no-https
```

---

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a>Windows 및 macOS에서 ASP.NET Core HTTPS 개발 인증서 신뢰

.NET Core SDK에는 HTTPS 개발 인증서가 포함 되어 있습니다. 인증서는 첫 실행 환경의 일부로 설치 됩니다. 예를 들어 `dotnet --info`은 다음과 유사한 출력을 생성 합니다.

```text
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

.NET Core SDK를 설치하면 로컬 사용자 인증서 저장소에 ASP.NET Core HTTPS 개발 인증서가 설치됩니다. 인증서가 설치 되었지만 트러스트 되지 않았습니다. 인증서를 신뢰 하려면 1 회 단계를 수행 하 여 dotnet `dev-certs` 도구를 실행 합니다.

```dotnetcli
dotnet dev-certs https --trust
```

다음 명령은 `dev-certs` 도구에 대한 도움말을 제공합니다.

```dotnetcli
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a>Docker 용 개발자 인증서를 설정 하는 방법

이 [GitHub 문제](https://github.com/aspnet/AspNetCore.Docs/issues/6199)를 참조하세요.

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a>Linux 용 Windows 하위 시스템에서 HTTPS 인증서 신뢰

WSL (Linux 용 Windows 하위 시스템)은 HTTPS 자체 서명 된 인증서를 생성 합니다. WSL 인증서를 신뢰 하도록 Windows 인증서 저장소를 구성 하려면:

* 다음 명령을 실행 하 여 WSL 생성 된 인증서를 내보냅니다. `dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`
* WSL 창에서 다음 명령을 실행 합니다. `ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`

  위의 명령은 Linux에서 Windows 신뢰할 수 있는 인증서를 사용 하도록 환경 변수를 설정 합니다.

## <a name="troubleshoot-certificate-problems"></a>인증서 문제 해결

이 섹션에서는 ASP.NET Core HTTPS 개발 인증서를 [설치 하 고 신뢰할 수](#trust)있는 경우에 대 한 도움말을 제공 하지만 인증서를 신뢰할 수 없다는 브라우저 경고가 계속 표시 됩니다.

### <a name="all-platforms---certificate-not-trusted"></a>모든 플랫폼-인증서를 신뢰할 수 없음

다음 명령을 실행 합니다.

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

열려 있는 모든 브라우저 인스턴스를 닫습니다. 앱에 대 한 새 브라우저 창을 엽니다. 인증서 신뢰는 브라우저에 의해 캐시 됩니다.

위의 명령은 대부분의 브라우저 트러스트 문제를 해결 합니다. 브라우저에서 아직 인증서를 신뢰 하지 않는 경우에는 다음 플랫폼 관련 제안 사항을 따르세요.

### <a name="docker---certificate-not-trusted"></a>Docker-인증서를 신뢰할 수 없음

* *C:\Users @ no__t-1USER} \AppData\Roaming\ASP.NET\Https* 폴더를 삭제 합니다.
* 솔루션을 정리 합니다. *bin* 및 *obj* 폴더를 삭제합니다.
* 개발 도구를 다시 시작 합니다. 예를 들어 Visual Studio, Visual Studio Code 또는 Mac용 Visual Studio입니다.

### <a name="windows---certificate-not-trusted"></a>Windows-인증서를 신뢰할 수 없음

* 인증서 저장소의 인증서를 확인 합니다. @No__t-2 및 `Current User > Trusted root certification authorities > Certificates` 모두에서 `ASP.NET Core HTTPS development certificate`의 이름을 가진 `localhost` 인증서가 있어야 합니다.
* 모든 찾은 인증서를 개인 및 신뢰할 수 있는 루트 인증 기관에서 제거 합니다. IIS Express localhost 인증서를 제거 **하지** 마십시오.
* 다음 명령을 실행 합니다.

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

열려 있는 모든 브라우저 인스턴스를 닫습니다. 앱에 대 한 새 브라우저 창을 엽니다.

### <a name="os-x---certificate-not-trusted"></a>OS X-인증서를 신뢰할 수 없음

* 키 집합 액세스를 엽니다.
* 시스템 키 집합을 선택 합니다.
* Localhost 인증서가 있는지 확인 합니다.
* 모든 사용자가 신뢰할 수 있음을 나타내기 위해 아이콘에 `+` 기호가 포함 되어 있는지 확인 합니다.
* 시스템 키 집합에서 인증서를 제거 합니다.
* 다음 명령을 실행 합니다.

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

열려 있는 모든 브라우저 인스턴스를 닫습니다. 앱에 대 한 새 브라우저 창을 엽니다.

## <a name="additional-information"></a>추가 정보

* <xref:host-and-deploy/proxy-load-balancer>
* Apache를 사용 하는 Linux의 [Host ASP.NET Core: HTTPS 구성 @ no__t-0
* Nginx를 사용 하 여 Linux에서 [Host ASP.NET Core: HTTPS 구성 @ no__t-0
* [IIS에서 SSL을 설정하는 방법](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [OWASP HSTS 브라우저 지원](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
