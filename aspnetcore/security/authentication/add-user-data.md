---
title: 추가, 다운로드 및 ASP.NET Core 프로젝트에서 Id에 사용자 지정 사용자 데이터를 삭제 합니다.
author: rick-anderson
description: ASP.NET Core 프로젝트에 사용자 지정 사용자 데이터 Id로 추가 하는 방법에 알아봅니다. GDPR 당 데이터를 삭제 합니다.
manager: wpickett
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 6/16/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/authentication/add-user-data
ms.openlocfilehash: 3ebb709cc40f6c2477ac57325d035b9b461e2eaf
ms.sourcegitcommit: 0d6f151e69c159d776ed0142773279e645edbc0a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2018
ms.locfileid: "35414996"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="07cf9-104">추가, 다운로드 및 ASP.NET Core 프로젝트에서 Id에 사용자 지정 사용자 데이터를 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="07cf9-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="07cf9-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="07cf9-106">이 문서에서는 설명 하는 방법:</span><span class="sxs-lookup"><span data-stu-id="07cf9-106">This article shows how to:</span></span>

* <span data-ttu-id="07cf9-107">ASP.NET Core 웹 앱에 사용자 지정 사용자 데이터를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="07cf9-108">사용 하 여 사용자 지정 데이터 모델을 데코레이팅하는 [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute?view=aspnetcore-2.1) 자동으로 다운로드 및 삭제에 사용할 수 있도록 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-108">Decorate the custom user data model with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute?view=aspnetcore-2.1) attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="07cf9-109">충족 사용 하 여 데이터를 다운로드 하 고 삭제할 수 [GDPR](xref:security/gdpr) 요구 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="07cf9-110">Razor 페이지 웹 앱에서 프로젝트 샘플 만들어지지만 ASP.NET Core MVC 웹 응용 프로그램에 대 한 지침 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="07cf9-111">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/authentication/add-user-data/sample)([다운로드 방법](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="07cf9-111">[View or download sample code](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/authentication/add-user-data/sample) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="07cf9-112">전제 조건</span><span class="sxs-lookup"><span data-stu-id="07cf9-112">Prerequisites</span></span>

[!INCLUDE [](~/includes/2.1-SDK.md)]

## <a name="create-a-razor-web-app"></a><span data-ttu-id="07cf9-113">Razor 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="07cf9-113">Create a Razor web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="07cf9-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07cf9-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="07cf9-115">Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="07cf9-116">프로젝트 이름을 **WebApp1** 를 원하는 경우의 네임 스페이스와 일치는 [샘플 다운로드](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/authentication/add-user-data/sample) 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/authentication/add-user-data/sample) code.</span></span>
* <span data-ttu-id="07cf9-117">선택 **ASP.NET Core 웹 응용 프로그램** > **확인**</span><span class="sxs-lookup"><span data-stu-id="07cf9-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="07cf9-118">선택 **ASP.NET Core 2.1** 드롭다운에</span><span class="sxs-lookup"><span data-stu-id="07cf9-118">Select **ASP.NET Core 2.1** in the dropdown</span></span>
* <span data-ttu-id="07cf9-119">선택 **웹 응용 프로그램**  > **확인**</span><span class="sxs-lookup"><span data-stu-id="07cf9-119">Select **Web Application**  > **OK**</span></span>
* <span data-ttu-id="07cf9-120">프로젝트를 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-120">Build and run the project.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="07cf9-121">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="07cf9-121">.NET Core CLI</span></span>](#tab/netcore-cli)

```cli
dotnet new webapp -o WebApp1
```

[!INCLUDE[](~/includes/webapp-alias-notice.md)]

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="07cf9-122">Identity scaffolder 실행</span><span class="sxs-lookup"><span data-stu-id="07cf9-122">Run the Identity scaffolder</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="07cf9-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07cf9-123">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="07cf9-124">**솔루션 탐색기**, 프로젝트를 마우스 오른쪽 단추로 클릭 > **추가** > **스 캐 폴드 된 새 항목**합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-124">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="07cf9-125">왼쪽된 창에서는 **추가 스 캐 폴드** 대화 상자에서 **Identity** > **추가**합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-125">From the left pane of the **Add Scaffold** dialog, select **Identity** > **ADD**.</span></span>
* <span data-ttu-id="07cf9-126">에 **추가 Identity** 대화 상자에서 다음 옵션:</span><span class="sxs-lookup"><span data-stu-id="07cf9-126">In the **ADD Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="07cf9-127">기존 레이아웃 파일을 선택 *~/Pages/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="07cf9-127">Select your existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="07cf9-128">재정의 하려면 다음 파일을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-128">Select the following files to override:</span></span>
    * <span data-ttu-id="07cf9-129">**계정/레지스터**</span><span class="sxs-lookup"><span data-stu-id="07cf9-129">**Account/Register**</span></span>
    * <span data-ttu-id="07cf9-130">**계정/관리/인덱스**</span><span class="sxs-lookup"><span data-stu-id="07cf9-130">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="07cf9-131">선택 된 **+** 단추를 새 **데이터 컨텍스트 클래스가**합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-131">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="07cf9-132">형식을 사용할 수 (**WebApp1.Models.WebApp1Context** 프로젝트의 이름을 **WebApp1**).</span><span class="sxs-lookup"><span data-stu-id="07cf9-132">Accept the type (**WebApp1.Models.WebApp1Context** if you named the project **WebApp1**).</span></span>
  * <span data-ttu-id="07cf9-133">선택 된 **+** 단추를 새 **사용자 클래스**합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-133">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="07cf9-134">형식을 사용할 수 (**WebApp1User** 프로젝트의 이름을 **WebApp1**) > **추가**합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-134">Accept the type (**WebApp1User** if you named the project **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="07cf9-135">선택 **추가**합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-135">Select **ADD**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="07cf9-136">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="07cf9-136">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="07cf9-137">ASP.NET scaffolder 이전에 설치 하지 않은 경우 지금 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-137">If you have not previously installed the ASP.NET scaffolder, install it now:</span></span>

```cli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="07cf9-138">에 대 한 패키지 참조 추가 [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) 프로젝트 (.csproj) 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-138">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="07cf9-139">프로젝트 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-139">Run the following command in the project directory:</span></span>

```cli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="07cf9-140">Identity scaffolder 옵션을 나열 하려면 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-140">Run the following command to list the Identity scaffolder options:</span></span>

```cli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="07cf9-141">프로젝트 폴더에 Identity scaffolder를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-141">In the project folder, run the Identity scaffolder:</span></span>

```cli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

-------------

<span data-ttu-id="07cf9-142">지침에 따라 [마이그레이션, UseAuthentication, 및 레이아웃](xref:security/authentication/scaffold-identity#efm) 다음 단계를 수행 하려면:</span><span class="sxs-lookup"><span data-stu-id="07cf9-142">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="07cf9-143">마이그레이션 만들고 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-143">Create a migration and update the database.</span></span>
* <span data-ttu-id="07cf9-144">`UseAuthentication`를 `Startup.Configure`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-144">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="07cf9-145">추가 `<partial name="_LoginPartial" />` 레이아웃 파일.</span><span class="sxs-lookup"><span data-stu-id="07cf9-145">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="07cf9-146">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-146">Test the app:</span></span>
  * <span data-ttu-id="07cf9-147">사용자 등록</span><span class="sxs-lookup"><span data-stu-id="07cf9-147">Register a user</span></span>
  * <span data-ttu-id="07cf9-148">새 사용자 이름 선택 (옆에 **로그 아웃** 링크)입니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-148">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="07cf9-149">창이 확장 또는 사용자 이름 및 다른 링크 표시 하려면 탐색 모음의 아이콘을 선택 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-149">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="07cf9-150">선택 된 **개인 데이터** 탭 합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-150">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="07cf9-151">선택 된 **다운로드** 단추 및 검사는 *PersonalData.json* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-151">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="07cf9-152">테스트는 **삭제** 단추를 사용자에 로그인을 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-152">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="07cf9-153">Identity db 사용자 지정 사용자 데이터를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-153">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="07cf9-154">업데이트는 `IdentityUser` 사용자 지정 속성을 사용 하 여 클래스를 파생 합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-154">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="07cf9-155">파일의 이름은 프로젝트 WebApp1 명명 *Areas/Identity/Data/WebApp1User.cs*합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-155">If you named your project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="07cf9-156">다음 코드는 파일을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-156">Update the file with the following code:</span></span>

[!code-csharp[Main](add-user-data/sample/Areas/Identity/Data/WebApp1User.cs)]

<span data-ttu-id="07cf9-157">속성으로 데코레이팅되 어는 [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute?view=aspnetcore-2.1) 특성:</span><span class="sxs-lookup"><span data-stu-id="07cf9-157">Properties decorated with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute?view=aspnetcore-2.1) attribute are:</span></span>

* <span data-ttu-id="07cf9-158">삭제는 *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor 페이지 호출 `UserManager.Delete`합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-158">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="07cf9-159">다운로드 한 데이터에 포함 된 *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor 페이지.</span><span class="sxs-lookup"><span data-stu-id="07cf9-159">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="07cf9-160">업데이트 Account/Manage/Index.cshtml 페이지</span><span class="sxs-lookup"><span data-stu-id="07cf9-160">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="07cf9-161">업데이트는 `InputModel` 에 *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* 를 다음으로 코드를 강조 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-161">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

[!code-csharp[Main](add-user-data/sample/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,87-95,120)]

<span data-ttu-id="07cf9-162">업데이트는 *Areas/Identity/Pages/Account/Manage/Index.cshtml* 강조 표시 된 다음 태그로:</span><span class="sxs-lookup"><span data-stu-id="07cf9-162">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-html[Main](add-user-data/sample/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=34-41)]

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="07cf9-163">업데이트 Account/Register.cshtml 페이지</span><span class="sxs-lookup"><span data-stu-id="07cf9-163">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="07cf9-164">업데이트는 `InputModel` 에 *Areas/Identity/Pages/Account/Register.cshtml.cs* 를 다음으로 코드를 강조 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-164">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

[!code-csharp[Main](add-user-data/sample/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=8-16,43,44)]

<span data-ttu-id="07cf9-165">업데이트는 *Areas/Identity/Pages/Account/Register.cshtml* 강조 표시 된 다음 태그로:</span><span class="sxs-lookup"><span data-stu-id="07cf9-165">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-html[Main](add-user-data/sample/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

<span data-ttu-id="07cf9-166">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-166">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="07cf9-167">추가 사용자 지정 사용자 데이터에 대 한 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="07cf9-167">Add a migration for the custom user data</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="07cf9-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07cf9-168">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="07cf9-169">Visual Studio에서 **패키지 관리자 콘솔**:</span><span class="sxs-lookup"><span data-stu-id="07cf9-169">In the Visual Studio **Package Manager Console**:</span></span>

```PMC
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="07cf9-170">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="07cf9-170">.NET Core CLI</span></span>](#tab/netcore-cli)

```cli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

------

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="07cf9-171">테스트 만들기, 보기, 다운로드, 사용자 지정 사용자 데이터를 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-171">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="07cf9-172">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-172">Test the app:</span></span>

* <span data-ttu-id="07cf9-173">새 사용자를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="07cf9-173">Register a new user.</span></span>
* <span data-ttu-id="07cf9-174">사용자 지정 사용자 데이터를 표시는 `/Identity/Account/Manage` 페이지.</span><span class="sxs-lookup"><span data-stu-id="07cf9-174">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="07cf9-175">다운로드 하 고 사용자 개인 데이터를 볼는 `/Identity/Account/Manage/PersonalData` 페이지.</span><span class="sxs-lookup"><span data-stu-id="07cf9-175">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>