---
title: ASP.NET Core의 Link 태그 도우미
author: rick-anderson
ms.author: riande
description: ASP.NET Core Link 태그 도우미 특성 및 HTML 링크 태그의 동작을 확장할 때 각 특성이 담당하는 역할을 확인합니다.
ms.custom: mvc
ms.date: 09/24/2019
uid: mvc/views/tag-helpers/builtin-th/link-tag-helper
ms.openlocfilehash: e1e2e58b4ab9087e1f9de5b5c03b587feb88f1b9
ms.sourcegitcommit: fae6f0e253f9d62d8f39de5884d2ba2b4b2a6050
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256485"
---
# <a name="link-tag-helper-in-aspnet-core"></a>ASP.NET Core의 Link 태그 도우미

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

[Link 태그 도우미](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)는 기본 또는 대체(fallback) CSS 파일에 대한 링크를 생성합니다. 일반적으로 기본 CSS 파일은 [콘텐츠 배달 네트워크](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn)(CDN)에 있습니다.

[!INCLUDE[](~/includes/cdn.md)]

Link 태그 도우미를 사용하면 CSS 파일에 CDN을 지정하고 CDN을 사용할 수 없는 경우 대체(fallback) 항목을 지정할 수 있습니다. Link 태그 도우미는 로컬 호스팅의 견고성을 사용하여 CDN의 성능 이점을 제공합니다.

다음 Razor 표시는 ASP.NET Core 웹앱 템플릿을 사용하여 만든 레이아웃 파일의 `head` 요소를 보여줍니다.

[!code-html[](link-tag-helper/sample/_Layout.cshtml?name=snippet)]

다음은 위의 코드에서 렌더링된 HTML입니다(비개발 환경에서).

[!code-csharp[](link-tag-helper/sample/HtmlPage1.html)]

위의 코드에서 Link 태그 도우미는 `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` 요소를 생성하고 요청된 *bootstrap.min.css* 파일을 확인하는 데 사용되는 다음 JavaScript를 CDN에서 사용할 수 있습니다. 이 경우, 태그 도우미가 CDN CSS 파일을 사용하여 `<link />` 요소를 생성하도록 CSS 파일을 사용할 수 있었습니다.

## <a name="commonly-used-link-tag-helper-attributes"></a>일반적으로 사용되는 Link 태그 도우미 특성

모든 Link 태그 도우미 특성, 속성 및 메서드는 [Link 태그 도우미](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)를 참조하세요.

### <a name="href"></a>href

링크된 리소스의 기본 설정 주소입니다. 주소는 모든 경우에 생성된 HTML로 간주되어 전달됩니다.

### <a name="asp-fallback-href"></a>asp-fallback-href

기본 URL에 오류가 발생할 경우 대체(fallback)할 CSS 스타일시트의 URL입니다.

### <a name="asp-fallback-test-class"></a>asp-fallback-test-class

대체(fallback) 테스트에 사용할 스타일시트에 정의된 클래스 이름입니다. 자세한 내용은 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>을 참조하세요.

### <a name="asp-fallback-test-property"></a>asp-fallback-test-property

대체(fallback) 테스트에 사용할 CSS 속성 이름입니다. 자세한 내용은 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>을 참조하세요.

### <a name="asp-fallback-test-value"></a>asp-fallback-test-value

대체(fallback) 테스트에 사용할 CSS 속성 값입니다. 자세한 내용은 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>을 참조하세요.

### <a name="asp-fallback-test-value"></a>asp-fallback-test-value

대체(fallback) 테스트에 사용할 CSS 속성 값입니다. 자세한 내용은 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
