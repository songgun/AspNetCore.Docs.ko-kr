---
title: ASP.NET Core Blazor 호스팅 모델
author: guardrex
description: Blazor Weasembomand Blazor 서버 호스팅 모델을 이해 합니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: blazor/hosting-models
ms.openlocfilehash: be67c129af4f071d10719e0bbf121de761dde9f4
ms.sourcegitcommit: 16cf016035f0c9acf3ff0ad874c56f82e013d415
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73033999"
---
# <a name="aspnet-core-blazor-hosting-models"></a>ASP.NET Core Blazor 호스팅 모델

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor는 [WeasembmbBlazor](https://webassembly.org/)(*Blazor server*) ASP.NET Core의 서버 쪽에서 브라우저에서 클라이언트 쪽을 실행 하도록 디자인 된웹 프레임 워크입니다. 호스팅 모델에 관계 없이 앱 및 구성 요소 모델은 *동일*합니다.

이 문서에서 설명 하는 호스팅 모델에 대 한 프로젝트를 만들려면 <xref:blazor/get-started>을 참조 하세요.

## <a name="blazor-webassembly"></a>Blazor WebAssembly

Blazor에 대 한 주 호스팅 모델이 브라우저에서 클라이언트 쪽을 제대로 실행 하 고 있습니다. Blazor 앱, 해당 앱의 종속성 및 .NET 런타임이 브라우저에 다운로드됩니다. 해당 앱은 브라우저 UI 스레드에서 직접 실행됩니다. UI 업데이트 및 이벤트 처리는 동일한 프로세스 내에서 발생 합니다. 응용 프로그램의 자산은 정적 콘텐츠를 클라이언트에 제공할 수 있는 웹 서버 또는 서비스에 정적 파일로 배포 됩니다.

![Blazor Weasemboma: Blazor 앱은 브라우저 내부의 UI 스레드에서 실행 됩니다.](hosting-models/_static/blazor-webassembly.png)

클라이언트 쪽 호스팅 모델을 사용 하 여 Blazor 앱을 만들려면 **Blazor Weasembomapp** 템플릿 ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new))을 사용 합니다.

**Blazor WebAssembly 앱** 템플릿을 선택한 후에는 **ASP.NET Core hosted** 확인란을 선택 하 여 ASP.NET Core 백 엔드를 사용 하도록 앱을 구성 하는 옵션이 있습니다 ([dotnet new blazorwasm--호스팅](/dotnet/core/tools/dotnet-new)). ASP.NET Core 앱은 Blazor 앱을 클라이언트에 제공 합니다. Blazor Weasembomapps는 web API 호출 또는 [SignalR](xref:signalr/introduction)를 사용 하 여 네트워크를 통해 서버와 상호 작용할 수 있습니다.

템플릿에는를 처리 하는 *blazor* 스크립트가 포함 됩니다.

* .NET 런타임, 앱 및 앱의 종속성 다운로드
* 앱을 실행 하기 위한 런타임 초기화

Blazor Weasembom호스팅 모델은 몇 가지 이점을 제공 합니다.

* .NET 서버 쪽 종속성이 없습니다. 클라이언트에 다운로드 한 후 앱이 완전히 작동 합니다.
* 클라이언트 리소스 및 기능은 완전히 활용 됩니다.
* 작업은 서버에서 클라이언트로 오프 로드 됩니다.
* ASP.NET Core 웹 서버는 앱을 호스트 하는 데 필요 하지 않습니다. 서버를 사용 하지 않는 배포 시나리오를 사용할 수 있습니다. 예를 들어 CDN에서 앱을 제공할 수 있습니다.

Blazor Weasembom단점이는 다음과 같습니다.

* 앱은 브라우저의 기능으로 제한 됩니다.
* 가능 클라이언트 하드웨어 및 소프트웨어 (예: Weasembomsupport)는 필수입니다.
* 다운로드 크기가 크고 앱을 로드 하는 데 시간이 오래 걸립니다.
* .NET 런타임 및 도구 지원의 완성도는 낮아집니다. 예를 들어 [.NET Standard](/dotnet/standard/net-standard) 지원 및 디버깅에는 제한 사항이 있습니다.

## <a name="blazor-server"></a>Blazor 서버

Blazor 서버 호스팅 모델을 사용 하면 앱이 ASP.NET Core 앱 내에서 서버에 실행 됩니다. UI 업데이트, 이벤트 처리 및 JavaScript 호출은 [SignalR](xref:signalr/introduction) 연결을 통해 처리됩니다.

![브라우저는 SignalR 연결을 통해 서버의 앱 (ASP.NET Core 앱 내에서 호스트)과 상호 작용 합니다.](hosting-models/_static/blazor-server.png)

Blazor 서버 호스팅 모델을 사용 하 여 Blazor 앱을 만들려면 ASP.NET Core **Blazor Server 앱** 템플릿 ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new))을 사용 합니다. ASP.NET Core 앱은 Blazor Server 앱을 호스팅하고 클라이언트가 연결 하는 SignalR 끝점을 만듭니다.

ASP.NET Core 앱은 추가할 앱의 `Startup` 클래스를 참조 합니다.

* 서버 쪽 서비스.
* 요청 처리 파이프라인에 대 한 앱입니다.

*Blazor* 스크립트&dagger; 클라이언트 연결을 설정 합니다. 필요에 따라 앱 상태를 유지 하 고 복원 하는 것은 앱의 책임입니다 (예: 네트워크 연결이 끊어진 경우).

Blazor 서버 호스팅 모델은 몇 가지 이점을 제공 합니다.

* 다운로드 크기는 Blazor Weasembomapp 보다 훨씬 더 작기 때문에 앱이 훨씬 더 빠르게 로드 됩니다.
* 앱은 .NET Core와 호환 되는 Api의 사용을 포함 하 여 서버 기능을 최대한 활용 합니다.
* 서버의 .NET Core는 앱을 실행 하는 데 사용 되므로 디버깅과 같은 기존 .NET 도구는 정상적으로 작동 합니다.
* 씬 클라이언트가 지원 됩니다. 예를 들어 Blazor Server 앱은 리소스 제한 장치에서 Weasembmbmbsemboma를 지원 하지 않는 브라우저에서 작동 합니다.
* 앱의 구성 요소 코드C# 를 포함 하 여 앱의 .net/code 베이스가 클라이언트에 제공 되지 않습니다.

Blazor 서버 호스팅을 단점이 있습니다.

* 일반적으로 더 높은 대기 시간이 있습니다. 모든 사용자 상호 작용에는 네트워크 홉이 포함 됩니다.
* 오프 라인은 지원 되지 않습니다. 클라이언트 연결에 실패 하면 앱 작동이 중지 됩니다.
* 여러 사용자가 있는 앱의 경우에는 확장성이 어렵습니다. 서버는 여러 클라이언트 연결을 관리 하 고 클라이언트 상태를 처리 해야 합니다.
* 앱을 제공 하려면 ASP.NET Core 서버가 필요 합니다. 서버를 사용 하지 않는 배포 시나리오 (예: CDN에서 앱 제공)를 사용할 수 없습니다.

&dagger;*blazor* 스크립트는 ASP.NET Core 공유 프레임 워크의 포함 리소스에서 제공 됩니다.

### <a name="comparison-to-server-rendered-ui"></a>서버에서 렌더링 된 UI 비교

Blazor 서버 앱을 이해 하는 한 가지 방법은 Razor 뷰나 Razor Pages를 사용 하 여 ASP.NET Core 앱에서 UI를 렌더링 하는 일반적인 모델과의 차이점을 이해 하는 것입니다. 두 모델 모두 Razor 언어를 사용 하 여 HTML 콘텐츠를 설명 하지만 태그가 렌더링 되는 방식에는 크게 차이가 있습니다.

Razor 페이지 또는 뷰가 렌더링 되 면 Razor 코드의 모든 줄은 텍스트 형식으로 HTML을 내보냅니다. 렌더링 후 서버는 생성 된 상태를 포함 하 여 페이지 또는 뷰 인스턴스를 삭제 합니다. 서버 유효성 검사에 실패 하 고 유효성 검사 요약이 표시 되는 경우와 같은 페이지에 대 한 다른 요청이 발생 하는 경우

* 전체 페이지는 HTML 텍스트로 다시 돌아갑니다.
* 페이지가 클라이언트로 전송 됩니다.

Blazor 앱은 *구성 요소*라는 UI의 재사용 가능한 요소로 구성 됩니다. 구성 요소에 C# 는 코드, 태그 및 기타 구성 요소가 포함 됩니다. 구성 요소가 렌더링 되 면 Blazor은 HTML 또는 XML 문서 개체 모델 (DOM)와 유사한 포함 된 구성 요소의 그래프를 생성 합니다. 이 그래프에는 속성 및 필드에 유지 되는 구성 요소 상태가 포함 됩니다. Blazor는 구성 요소 그래프를 평가 하 여 태그의 이진 표현을 생성 합니다. 이진 형식은 다음과 같을 수 있습니다.

* 렌더링 중에 HTML 텍스트로 바뀝니다.
* 정기적으로 렌더링 하는 동안 태그를 효율적으로 업데이트 하는 데 사용 됩니다.

Blazor의 UI 업데이트는 다음에 의해 트리거됩니다.

* 사용자 상호 작용 (예: 단추 선택)
* 타이머와 같은 앱 트리거입니다.

그래프가 계산 되며 UI *diff* (차이)가 계산 됩니다. 이러한 차이는 클라이언트에서 UI를 업데이트 하는 데 필요한 최소한의 DOM 편집 집합입니다. Diff는 클라이언트에 이진 형식으로 전송 되 고 브라우저에서 적용 됩니다.

사용자가 클라이언트에서 다른 구성 요소를 탐색 한 후에 구성 요소가 삭제 됩니다. 사용자가 구성 요소와 상호 작용 하는 동안에는 구성 요소의 상태 (서비스, 리소스)가 서버 메모리에 보관 되어야 합니다. 서버에서 많은 구성 요소의 상태를 동시에 유지 관리할 수 있기 때문에 메모리 소모는 해결 해야 하는 문제입니다. 서버 메모리를 최적으로 사용 하기 위해 Blazor Server 앱을 제작 하는 방법에 대 한 지침은 <xref:security/blazor/server>을 참조 하세요.

### <a name="circuits"></a>배선

Blazor 서버 앱은 [ASP.NET Core SignalR](xref:signalr/introduction)위에 빌드됩니다. 각 클라이언트는 *회로*라는 하나 이상의 SignalR 연결을 통해 서버와 통신 합니다. 회로는 일시적인 네트워크 중단을 허용할 수 있는 SignalR 연결에 대 한 Blazor의 추상화입니다. Blazor client에서 SignalR 연결의 연결이 끊어지면 새 SignalR 연결을 사용 하 여 서버에 다시 연결 하려고 시도 합니다.

Blazor 서버 앱에 연결 된 각 브라우저 화면 (브라우저 탭 또는 iframe)은 SignalR 연결을 사용 합니다. 이는 서버에서 렌더링 되는 일반적인 앱에 비해 또 다른 중요 한 차이점입니다. 서버에서 렌더링 하는 응용 프로그램에서는 여러 브라우저 화면에서 동일한 앱을 여는 것은 일반적으로 서버에서 추가 리소스 요구 사항으로 변환 되지 않습니다. Blazor 서버 앱에서 각 브라우저 화면에는 서버에서 관리 해야 하는 별도의 회로와 개별 구성 요소 상태 인스턴스가 필요 합니다.

Blazor는 브라우저 탭을 닫거나 외부 URL로 이동 하는 *정상적인* 종료를 고려 합니다. 정상적인 종료 시 회로 및 연결 된 리소스가 즉시 해제 됩니다. 클라이언트는 네트워크 중단으로 인해 안정적이 지 않은 방식으로 연결을 끊을 수도 있습니다. Blazor 서버는 클라이언트가 다시 연결할 수 있도록 구성 가능한 간격 동안 연결이 끊어진 회로를 저장 합니다. 자세한 내용은 [동일한 서버에](#reconnection-to-the-same-server) 다시 연결 섹션을 참조 하세요.

### <a name="ui-latency"></a>UI 대기 시간

UI 대기 시간은 시작 된 작업에서 UI를 업데이트 하는 데 걸리는 시간입니다. UI 대기 시간에 대 한 값이 작을수록 앱이 사용자에 게 반응 하는 데 필수적입니다. Blazor 서버 앱에서 각 작업은 서버로 전송 되 고 처리 되며 UI diff가 다시 전송 됩니다. 따라서 UI 대기 시간은 네트워크 대기 시간 및 작업 처리의 서버 대기 시간 합계입니다.

개인 회사 네트워크로 제한 된 lob (기간 업무) 앱의 경우 네트워크 대기 시간으로 인 한 대기 시간에 대 한 사용자의 영향은 일반적으로 imperceptible입니다. 인터넷을 통해 배포 된 앱의 경우 특히 사용자가 지리적으로 광범위 하 게 분산 된 경우 대기 시간이 달라질 수 있습니다.

메모리 사용은 앱 대기 시간에도 영향을 주지 않습니다. 메모리 사용이 증가 하면 가비지 수집 또는 페이징 메모리가 디스크에 자주 발생 하므로 앱 성능이 저하 되 고 결과적으로 UI 대기 시간이 증가 합니다. 자세한 내용은 <xref:security/blazor/server>을 참조하십시오.

Blazor 서버 앱은 네트워크 대기 시간 및 메모리 사용을 줄여 UI 대기 시간을 최소화 하도록 최적화 되어야 합니다. 네트워크 대기 시간을 측정 하는 방법은 <xref:host-and-deploy/blazor/server#measure-network-latency>을 참조 하세요. SignalR 및 Blazor에 대 한 자세한 내용은 다음을 참조 하세요.

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="reconnection-to-the-same-server"></a>동일한 서버에 다시 연결

Blazor 서버 앱은 서버에 대 한 활성 SignalR 연결이 필요 합니다. 연결이 끊어지면 앱이 서버에 다시 연결을 시도 합니다. 클라이언트의 상태가 아직 메모리에 있으면 클라이언트 세션이 상태 손실 없이 다시 시작 됩니다.

클라이언트에서 연결이 끊어진 것을 감지 하면 클라이언트에서 다시 연결을 시도 하는 동안 기본 UI가 표시 됩니다. 다시 연결이 실패 하는 경우 사용자에 게 다시 시도 하는 옵션이 제공 됩니다. UI를 사용자 지정 하려면 *_Host* Razor 페이지에서 `components-reconnect-modal` `id`로 요소를 정의 합니다. 클라이언트는 연결 상태에 따라 다음 CSS 클래스 중 하나를 사용 하 여이 요소를 업데이트 합니다.

* 연결 끊김을 나타내는 UI를 표시 하 고 클라이언트를 다시 연결 하려고 하는 `components-reconnect-show` &ndash; 합니다.
* 클라이언트에 활성 연결이 &ndash; `components-reconnect-hide` UI를 숨깁니다.
* 네트워크 오류로 인해 `components-reconnect-failed` &ndash; 다시 연결 하지 못했습니다. 다시 연결을 시도 하려면 `window.Blazor.reconnect()`을 호출 합니다.
* `components-reconnect-rejected` &ndash; 다시 연결이 거부 되었습니다. 서버에 도달 했지만 연결이 거부 되었으며 서버의 사용자 상태가 사라졌습니다. 앱을 다시 로드 하려면 `location.reload()`을 호출 합니다. 이 연결 상태는 다음과 같은 경우에 발생할 수 있습니다.
  * 회로 (서버 쪽 코드)의 작동이 중단 됩니다.
  * 서버에서 사용자의 상태를 삭제 하기에 충분 한 길이의 클라이언트 연결이 끊겼습니다. 사용자가 상호 작용 하는 구성 요소의 인스턴스가 삭제 됩니다.

### <a name="stateful-reconnection-after-prerendering"></a>렌더링 후 상태 저장 다시 연결

서버에 대 한 클라이언트 연결이 설정 되기 전에 서버에서 UI를 미리 렌더링 하도록 Blazor 서버 앱이 기본적으로 설정 되어 있습니다. 이는 *_Host* Razor 페이지에 설정 되어 있습니다.

```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>(RenderMode.ServerPrerendered))</app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

`RenderMode`은 구성 요소가 있는지 여부를 구성 합니다.

* 는 페이지에 미리 렌더링 된 됩니다.
* 는 페이지에서 정적 HTML로 렌더링 되거나 사용자 에이전트에서 Blazor 앱을 부트스트랩 하는 데 필요한 정보가 포함 되어 있습니다.

| `RenderMode`        | 설명 |
| ------------------- | ----------- |
| `ServerPrerendered` | 구성 요소를 정적 HTML로 렌더링 하 고 Blazor 서버 앱에 대 한 마커를 포함 합니다. 사용자 에이전트가 시작 되 면이 표식은 Blazor 앱을 부트스트랩 하는 데 사용 됩니다. 매개 변수는 지원 되지 않습니다. |
| `Server`            | Blazor 서버 앱에 대 한 마커를 렌더링 합니다. 구성 요소의 출력은 포함 되지 않습니다. 사용자 에이전트가 시작 되 면이 표식은 Blazor 앱을 부트스트랩 하는 데 사용 됩니다. 매개 변수는 지원 되지 않습니다. |
| `Static`            | 구성 요소를 정적 HTML로 렌더링 합니다. 매개 변수가 지원 됩니다. |

정적 HTML 페이지에서 서버 구성 요소를 렌더링 하는 것은 지원 되지 않습니다.

클라이언트는 앱을 미리 렌더링 하는 데 사용 된 상태와 동일한 상태를 사용 하 여 서버에 다시 연결 합니다. 앱의 상태가 아직 메모리에 있는 경우 SignalR 연결이 설정 된 후에 구성 요소 상태가 다시 발생 하지 않습니다.

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Razor 페이지 및 뷰에서 상태 저장 대화형 구성 요소 렌더링

상태 저장 대화형 구성 요소는 Razor 페이지 또는 보기에 추가할 수 있습니다.

페이지 또는 뷰가 렌더링 되는 경우:

* 구성 요소가 페이지 또는 뷰와 미리 렌더링 된 됩니다.
* 렌더링에 사용 되는 초기 구성 요소 상태가 손실 됩니다.
* 새 구성 요소 상태는 SignalR 연결이 설정 될 때 생성 됩니다.

다음 Razor 페이지는 `Counter` 구성 요소를 렌더링 합니다.

```cshtml
<h1>My Razor Page</h1>

@(await Html.RenderComponentAsync<Counter>(RenderMode.ServerPrerendered))
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a>Razor 페이지 및 뷰에서 비 대화형 구성 요소 렌더링

다음 Razor 페이지에서 `MyComponent` 구성 요소는 폼을 사용 하 여 지정 된 초기 값을 사용 하 여 정적으로 렌더링 됩니다.

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

@(await Html.RenderComponentAsync<MyComponent>(RenderMode.Static, 
    new { InitialValue = InitialValue }))

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

`MyComponent` 정적으로 렌더링 되므로 구성 요소는 대화형이 될 수 없습니다.

### <a name="detect-when-the-app-is-prerendering"></a>앱이 사전 렌더링 되는 경우 검색

[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a>Blazor 서버 앱에 대 한 SignalR 클라이언트 구성

경우에 따라 Blazor Server 앱에서 사용 하는 SignalR 클라이언트를 구성 해야 합니다. 예를 들어 SignalR 클라이언트에 대 한 로깅을 구성 하 여 연결 문제를 진단할 수 있습니다.

*Pages/_Host* 파일에서 SignalR client를 구성 하려면 다음을 수행 합니다.

* *Blazor* 스크립트에 대 한 `<script>` 태그에 `autostart="false"` 특성을 추가 합니다.
* `Blazor.start`를 호출 하 고 SignalR builder를 지정 하는 구성 개체를 전달 합니다.

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a>추가 자료

* <xref:blazor/get-started>
* <xref:signalr/introduction>
