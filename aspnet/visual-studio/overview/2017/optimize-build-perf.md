---
uid: visual-studio/overview/2017/optimize-build-perf
title: 솔루션에 대 한 빌드 성능 최적화
author: tfitzmac
description: 솔루션에 대 한 빌드 성능 최적화
ms.author: riande
ms.date: 08/22/2018
msc.type: authoredcontent
ms.openlocfilehash: 19f190835e7477e69db470b74edac9e211fd9158
ms.sourcegitcommit: 5a2456cbf429069dc48aaa2823cde14100e4c438
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2018
ms.locfileid: "41909888"
---
# <a name="optimize-build-performance-for-solution"></a><span data-ttu-id="658bb-103">솔루션에 대 한 빌드 성능 최적화</span><span class="sxs-lookup"><span data-stu-id="658bb-103">Optimize build performance for solution</span></span>
<span data-ttu-id="658bb-104">Visual Studio 2017 15.8 나중에 아래에 새 메뉴 항목을 추가 하 고 **빌드 > ASP.NET 컴파일 > 솔루션에 대 한 빌드 성능 최적화**합니다.</span><span class="sxs-lookup"><span data-stu-id="658bb-104">Visual Studio 2017 15.8 and later added a new menu item under **Build > ASP.NET Compilation > Optimize Build Performance for Solution**.</span></span>

![새 메뉴 항목의 스크린샷](optimize-build-perf/_static/optimize-build-performance-for-solution.png)

<span data-ttu-id="658bb-106">ASP.NET에서는 ASP.NET 프로젝트에는 저마다 컴파일러의 복사본 즉 런타임에 해당 뷰를 컴파일합니다.</span><span class="sxs-lookup"><span data-stu-id="658bb-106">ASP.NET compiles its views at runtime, which means your ASP.NET project carries with it a copy of the compiler.</span></span> <span data-ttu-id="658bb-107">그러나 개발자 컴퓨터에서 경우 컴파일러의 복사본에는 Visual Studio의 복사본과 일치 하지 않습니다 빌드 성능이 저하 됩니다 증분 빌드 1 ~ 3 초 순서입니다.</span><span class="sxs-lookup"><span data-stu-id="658bb-107">However, on a developer machine when the copy of the compiler doesn't match Visual Studio's copy, your build performance is impacted on the order of 1-3 seconds per incremental build.</span></span> <span data-ttu-id="658bb-108">이 기능은 Visual Studio의 증분 빌드 속도 일치 하는 컴파일러의 프로젝트의 복사본을 업데이트 됩니다.</span><span class="sxs-lookup"><span data-stu-id="658bb-108">This feature will update your project's copy of the compiler to match Visual Studio's which should speed up incremental builds.</span></span>

<span data-ttu-id="658bb-109">이 ASP.NET 프레임 워크 프로젝트에만 적용 됩니다, 그리고 ASP.NET Core에 적용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="658bb-109">This is applicable to ASP.NET Framework projects only, it does not apply to ASP.NET Core.</span></span>