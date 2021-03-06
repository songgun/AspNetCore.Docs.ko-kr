---
title: HTTP 처리기 및 모듈을 ASP.NET Core 미들웨어로 마이그레이션
author: rick-anderson
description: ''
ms.author: riande
ms.date: 12/07/2016
uid: migration/http-modules
ms.openlocfilehash: bdf27ccb742d4bc05bac71e6c96d71c38dcb4b62
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975486"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a>HTTP 처리기 및 모듈을 ASP.NET Core 미들웨어로 마이그레이션

이 문서에서는 기존 ASP.NET [HTTP 모듈 및 처리기를 system.webserver에서](/iis/configuration/system.webserver/) ASP.NET Core [미들웨어](xref:fundamentals/middleware/index)로 마이그레이션하는 방법을 보여 줍니다.

## <a name="modules-and-handlers-revisited"></a>모듈 및 처리기를 검토 합니다.

미들웨어 ASP.NET Core를 진행 하기 전에 먼저 HTTP 모듈 및 처리기가 작동 하는 방법을 살펴보겠습니다.

![모듈 처리기](http-modules/_static/moduleshandlers.png)

**처리기는 다음과 같습니다.**

* [IHttpHandler](/dotnet/api/system.web.ihttphandler) 를 구현 하는 클래스

* 지정 된 파일 이름 또는 확장명이 있는 요청을 처리 하는 데 사용 됩니다 (예: *. report).*

* Web.config에 [구성 됨](/iis/configuration/system.webserver/handlers/)

**모듈은 다음과 같습니다.**

* [IHttpModule](/dotnet/api/system.web.ihttpmodule) 를 구현 하는 클래스

* 모든 요청에 대해 호출 됩니다.

* 단기 회로 가능 (요청 추가 처리 중지)

* HTTP 응답에 추가 하거나 직접 만들 수 있습니다.

* Web.config에 [구성 됨](/iis/configuration/system.webserver/modules/)

**모듈에서 들어오는 요청을 처리 하는 순서는 다음에 의해 결정 됩니다.**

1. [응용 프로그램 수명 주기](https://msdn.microsoft.com/library/ms227673.aspx)는 ASP.NET에서 발생 하는 시리즈 이벤트입니다. [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. 각 모듈은 하나 이상의 이벤트에 대 한 처리기를 만들 수 있습니다.

2. 동일한 이벤트의 경우 *web.config*에서 구성 된 순서입니다.

모듈 외에도 *Global.asax.cs* 파일에 수명 주기 이벤트에 대 한 처리기를 추가할 수 있습니다. 이러한 처리기는 구성 된 모듈의 처리기 다음에 실행 됩니다.

## <a name="from-handlers-and-modules-to-middleware"></a>처리기 및 모듈에서 미들웨어로

**미들웨어는 HTTP 모듈 및 처리기 보다 간단 합니다.**

* 모듈, 처리기, *Global.asax.cs*, *web.config* (IIS 구성 제외) 및 응용 프로그램 수명 주기가 사라졌습니다.

* 미들웨어에서 모듈 및 처리기의 역할을 모두 가져왔습니다.

* 미들웨어는 Web.config가 아닌 코드를 사용 하 여 구성 됩니다 *.*

* [파이프라인 분기](xref:fundamentals/middleware/index#use-run-and-map) 를 사용 하 여 URL 뿐만 아니라 요청 헤더, 쿼리 문자열 등에 따라 특정 미들웨어에 요청을 보낼 수 있습니다.

**미들웨어는 모듈과 매우 유사 합니다.**

* 모든 요청에 대해 원금에서 호출 됩니다.

* 요청을 [다음 미들웨어로 전달 하지 않고](#http-modules-shortcircuiting-middleware) 요청을 단기 회로 할 수 있습니다.

* 자체 HTTP 응답을 만들 수 있습니다.

**미들웨어와 모듈은 다른 순서로 처리 됩니다.**

* 미들웨어의 순서는 요청 파이프라인에 삽입 되는 순서를 기반으로 하며, 모듈 순서는 주로 [응용 프로그램 수명 주기](https://msdn.microsoft.com/library/ms227673.aspx) 이벤트를 기반으로 합니다.

* 응답에 대 한 미들웨어의 순서는 요청의 반대 이며, 모듈 순서는 요청 및 응답에 대해 동일 합니다.

* [IApplicationBuilder를 사용 하 여 미들웨어 파이프라인 만들기](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) 를 참조 하세요.

![미들웨어](http-modules/_static/middleware.png)

위의 이미지에서 인증 미들웨어는 요청을 단기 short-circuit 합니다.

## <a name="migrating-module-code-to-middleware"></a>미들웨어로 모듈 코드 마이그레이션

기존 HTTP 모듈은 다음과 유사 하 게 표시 됩니다.

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

[미들웨어](xref:fundamentals/middleware/index) 페이지에 표시 된 것 처럼 ASP.NET Core 미들웨어는를 가져와서 `Invoke` `HttpContext` 을 `Task`반환 하는 메서드를 노출 하는 클래스입니다. 새 미들웨어는 다음과 같습니다.

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

위의 미들웨어 템플릿은 [미들웨어 작성](xref:fundamentals/middleware/write)에 대 한 섹션에서 가져온 것입니다.

*MyMiddlewareExtensions* helper 클래스를 사용 하면 `Startup` 클래스에서 미들웨어를 더 쉽게 구성할 수 있습니다. 메서드 `UseMyMiddleware` 는 미들웨어 클래스를 요청 파이프라인에 추가 합니다. 미들웨어에 필요한 서비스는 미들웨어의 생성자에 삽입 됩니다.

<a name="http-modules-shortcircuiting-middleware"></a>

사용자에 게 권한이 부여 되지 않은 경우와 같이 모듈에서 요청을 종료할 수 있습니다.

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

미들웨어는 파이프라인의 다음 미들웨어에서 `Invoke` 를 호출 하지 않고이를 처리 합니다. 이전 미들웨어는 응답이 파이프라인을 통해 다시 사용 될 때 계속 호출 되기 때문에 요청을 완전히 종료 하지는 않습니다.

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

모듈의 기능을 새로운 미들웨어로 마이그레이션하면 ASP.NET Core에서 `HttpContext` 클래스가 크게 변경 되어 코드가 컴파일되지 않을 수 있습니다. [나중에](#migrating-to-the-new-httpcontext)새 ASP.NET Core HttpContext로 마이그레이션하는 방법을 확인할 수 있습니다.

## <a name="migrating-module-insertion-into-the-request-pipeline"></a>모듈 삽입을 요청 파이프라인으로 마이그레이션

일반적으로 HTTP 모듈은 *web.config*를 사용 하 여 요청 파이프라인에 추가 됩니다.

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

`Startup` 클래스의 요청 파이프라인에 [새 미들웨어를 추가](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) 하 여이를 변환 합니다.

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

새 미들웨어를 삽입 하는 파이프라인의 정확한 위치는 모듈 (`BeginRequest`, `EndRequest`등)로 처리 되는 이벤트와 *web.config*의 모듈 목록에서 해당 순서로 처리 되는 이벤트에 따라 달라 집니다.

앞에서 설명한 것 처럼 ASP.NET Core에는 응용 프로그램 수명 주기가 없으며 미들웨어에서 응답이 처리 되는 순서는 모듈에서 사용 하는 순서와 다릅니다. 이렇게 하면 순서 결정을 더 어려울 수 있습니다.

정렬이 문제가 되는 경우 독립적으로 정렬할 수 있는 여러 미들웨어 구성 요소로 모듈을 분할할 수 있습니다.

## <a name="migrating-handler-code-to-middleware"></a>미들웨어로 처리기 코드 마이그레이션

HTTP 처리기는 다음과 같습니다.

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

ASP.NET Core 프로젝트에서 다음과 유사한 미들웨어로 변환 합니다.

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

이 미들웨어는 모듈에 해당 하는 미들웨어와 매우 비슷합니다. 여기에는에 대 `_next.Invoke(context)`한 호출이 없다는 점만 다릅니다. 이는 처리기가 요청 파이프라인의 끝에 있기 때문에 의미가 있으므로 호출할 다음 미들웨어가 없습니다.

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a>요청 파이프라인으로 처리기 삽입 마이그레이션

HTTP 처리기 구성은 *web.config* 에서 수행 되며 다음과 같습니다.

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

모듈에서 변환 된 미들웨어와 마찬가지로 `Startup` 클래스의 요청 파이프라인에 새 처리기 미들웨어를 추가 하 여이를 변환할 수 있습니다. 이 방법의 문제는 새 처리기 미들웨어에 모든 요청을 전송 한다는 것입니다. 그러나 지정 된 확장을 포함 하는 요청만 미들웨어에 도달 하려고 합니다. 그러면 HTTP 처리기와 동일한 기능을 제공 합니다.

한 가지 해결 방법은 `MapWhen` 확장 메서드를 사용 하 여 지정 된 확장명을 가진 요청의 파이프라인을 분기 하는 것입니다. 다른 미들웨어를 추가 하는 `Configure` 것과 같은 방법으로이 작업을 수행 합니다.

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

`MapWhen`는 다음 매개 변수를 사용 합니다.

1. 을 사용 `HttpContext` 하 고 요청을 분기 `true` 아래로 이동 해야 하는 경우를 반환 하는 람다입니다. 즉, 확장 뿐만 아니라 요청 헤더, 쿼리 문자열 매개 변수 등에서 요청을 분기할 수 있습니다.

2. 을 사용 `IApplicationBuilder` 하 고 분기에 대 한 모든 미들웨어를 추가 하는 람다입니다. 즉, 처리기 미들웨어 앞의 분기에 추가 미들웨어를 추가할 수 있습니다.

모든 요청에서 분기가 호출 되기 전에 파이프라인에 추가 되는 미들웨어 분기는 영향을 주지 않습니다.

## <a name="loading-middleware-options-using-the-options-pattern"></a>옵션 패턴을 사용 하 여 미들웨어 옵션 로드

일부 모듈 및 처리기는 *web.config*에 저장 되는 구성 옵션을 포함 합니다. 그러나 ASP.NET Core에서는 *web.config*대신 새 구성 모델이 사용 됩니다.

새 [구성 시스템](xref:fundamentals/configuration/index) 은이를 해결 하는 다음과 같은 옵션을 제공 합니다.

* [다음 섹션](#loading-middleware-options-through-direct-injection)에 표시 된 것 처럼 미들웨어에 직접 옵션을 삽입 합니다.

* [옵션 패턴](xref:fundamentals/configuration/options)을 사용 합니다.

1. 미들웨어 옵션을 포함 하는 클래스를 만듭니다. 예를 들면 다음과 같습니다.

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. 옵션 값 저장

   구성 시스템을 사용 하 여 원하는 위치에 옵션 값을 저장할 수 있습니다. 그러나 대부분의 사이트에서는 *appsettings*를 사용 하기 때문에 다음 방법을 사용 합니다.

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   여기서 *MyMiddlewareOptionsSection* 는 섹션 이름입니다. 옵션 클래스의 이름과 같을 필요는 없습니다.

3. 옵션 클래스와 옵션 값 연결

    옵션 패턴은 ASP.NET Core의 종속성 주입 프레임 워크를 사용 하 여 옵션 유형 (예 `MyMiddlewareOptions`:)을 `MyMiddlewareOptions` 실제 옵션을 포함 하는 개체와 연결 합니다.

    클래스를 `Startup` 업데이트 합니다.

   1. *Appsettings*를 사용 하는 경우 `Startup` 생성자의 구성 작성기에 추가 합니다.

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. 옵션 서비스를 구성 합니다.

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. 옵션과 옵션 클래스를 연결 합니다.

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. 미들웨어 생성자에 옵션을 삽입 합니다. 이는 컨트롤러에 옵션을 삽입 하는 것과 유사 합니다.

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   에 미들웨어 `IApplicationBuilder` 를 추가 하는 [usemiddleware](#http-modules-usemiddleware) 확장 메서드는 종속성 주입을 담당 합니다.

   이는 `IOptions` 개체로 제한 되지 않습니다. 미들웨어에 필요한 다른 모든 개체를 이러한 방식으로 삽입할 수 있습니다.

## <a name="loading-middleware-options-through-direct-injection"></a>직접 주입을 통해 미들웨어 옵션 로드

옵션 패턴에는 옵션 값과 소비자 간의 느슨한 결합을 만드는 이점이 있습니다. 옵션 클래스를 실제 옵션 값과 연결한 후에는 다른 모든 클래스가 종속성 주입 프레임 워크를 통해 옵션에 대 한 액세스 권한을 얻을 수 있습니다. 옵션 값 주위에 전달할 필요가 없습니다.

이는 다른 옵션을 사용 하 여 동일한 미들웨어를 두 번 사용 하려는 경우에도 중단 됩니다. 예를 들어 서로 다른 역할을 허용 하는 여러 분기에서 사용 되는 권한 부여 미들웨어가 있습니다. 두 가지 옵션 개체를 하나의 옵션 클래스에 연결할 수 없습니다.

이 솔루션은 `Startup` 클래스의 실제 옵션 값을 사용 하 여 옵션 개체를 가져온 다음 미들웨어의 각 인스턴스에 직접 전달 하는 것입니다.

1. Appsettings에 두 번째 키를 추가 *합니다.*

   *Appsettings* 파일에 두 번째 옵션 집합을 추가 하려면 새 키를 사용 하 여 고유 하 게 식별 합니다.

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. 옵션 값을 검색 하 여 미들웨어에 전달 합니다. 파이프라인에 미들웨어를 추가 하는 확장메서드는옵션값에전달할논리적인위치입니다.`Use...` 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. 미들웨어가 options 매개 변수를 사용할 수 있도록 합니다. 옵션 매개 변수를 사용 `Use...` 하 여에 `UseMiddleware`전달 하는 확장 메서드의 오버 로드를 제공 합니다. 매개 `UseMiddleware` 변수를 사용 하 여를 호출 하면 미들웨어 개체를 인스턴스화할 때 미들웨어 생성자에 매개 변수를 전달 합니다.

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   `OptionsWrapper` 개체의 옵션 개체를 래핑하는 방법을 확인 합니다. 이는 `IOptions`미들웨어 생성자에서 예상한 대로을 구현 합니다.

## <a name="migrating-to-the-new-httpcontext"></a>새 HttpContext로 마이그레이션

미들웨어의 `Invoke` 메서드는 다음 형식의 `HttpContext`매개 변수를 사용 한다는 것을 앞에서 살펴보았습니다.

```csharp
public async Task Invoke(HttpContext context)
```

`HttpContext`ASP.NET Core에서 대폭 변경 되었습니다. 이 섹션에서는 [system.web](/dotnet/api/system.web.httpcontext) 의 가장 일반적으로 사용 되는 속성을 새 `Microsoft.AspNetCore.Http.HttpContext`로 변환 하는 방법을 보여 줍니다.

### <a name="httpcontext"></a>HttpContext

**HttpContext 항목** 은 다음과 같이 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

**고유한 요청 ID (System.web. HttpContext)**

각 요청에 대 한 고유 id를 제공 합니다. 로그에를 포함 하는 것이 매우 유용 합니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a>HttpContext.Request

**HttpMethod** 는 다음과 같이 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

**HttpContext. QueryString** 은 다음과 같이 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

**RawUrl** 및:로 변환 합니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

**HttpContext. IsSecureConnection** 은 다음과 같이 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

**HttpContext. UserHostAddress** 는 다음과 같이 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

**HttpContext. 쿠키** 는 다음과 같이 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

**RouteData** 는 다음과 같이 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

**HttpContext. 헤더** 는 다음과 같이 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

**UserAgent** 는 다음과 같이 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

**HttpContext** 참조 페이지는 다음으로 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

**HttpContext. ContentType** 은 다음과 같이 변환 합니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

**HttpContext. Form** 은 다음과 같이 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> 콘텐츠 하위 형식이 *x-www-form-urlencoded* 또는 *양식 데이터*인 경우에만 양식 값을 읽습니다.

**InputStream** 는 다음과 같이 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> 이 코드는 파이프라인의 끝에 있는 처리기 형식 미들웨어 에서만 사용 합니다.
>
>위에서 설명한 대로 요청 당 한 번만 원시 본문을 읽을 수 있습니다. 첫 번째 읽은 후 본문을 읽으려고 시도 하는 미들웨어는 빈 본문을 읽습니다.
>
>이는 버퍼에서 수행 되기 때문에 이전에 표시 된 대로 폼을 읽는 경우에는 적용 되지 않습니다.

### <a name="httpcontextresponse"></a>HttpContext.Response

**Httpcontext. Status** 및 **httpcontext 설명** 으로 변환 합니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

**Httpcontext. ContentEncoding** 및 Httpcontext. **ContentType** 은 다음으로 변환 합니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

또한 직접 Httpcontext.current에 대 한 **ContentType** 은 다음과 같이 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

**HttpContext. 출력** 은 다음과 같이 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

**HttpContext.Response.TransmitFile**

파일을 처리 하는 방법은 [여기](../fundamentals/request-features.md#middleware-and-request-features)에 설명 되어 있습니다.

**HttpContext.Response.Headers**

응답 본문에 쓴 후에 응답 헤더를 설정 하는 경우 응답 헤더를 보내는 것은 복잡 하므로 전송 되지 않습니다.

해결 방법은 응답에 쓰기를 시작 하기 직전에 호출 되는 콜백 메서드를 설정 하는 것입니다. 미들웨어에서 `Invoke` 메서드를 시작할 때이 작업을 수행 하는 것이 가장 좋습니다. 이 콜백 메서드는 응답 헤더를 설정 합니다.

다음 코드에서는 라는 `SetHeaders`콜백 메서드를 설정 합니다.

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

콜백 `SetHeaders` 메서드는 다음과 같습니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

**HttpContext.Response.Cookies**

쿠키는 *집합 쿠키* 응답 헤더의 브라우저로 이동 합니다. 따라서 쿠키를 보내려면 응답 헤더를 보내는 데 사용 되는 것과 동일한 콜백이 필요 합니다.

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

콜백 `SetCookies` 메서드는 다음과 같습니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a>추가 자료

* [HTTP 처리기 및 HTTP 모듈 개요](/iis/configuration/system.webserver/)
* [구성](xref:fundamentals/configuration/index)
* [애플리케이션 시작](xref:fundamentals/startup)
* [미들웨어](xref:fundamentals/middleware/index)
