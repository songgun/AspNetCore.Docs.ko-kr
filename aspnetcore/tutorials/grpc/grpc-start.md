---
title: ASP.NET Core에서 .NET Core gRPC 클라이언트 및 서버 만들기
author: juntaoluo
description: 이 자습서는 ASP.NET Core에서 gRPC 서비스 및 gRPC 클라이언트를 만드는 방법을 보여줍니다. gRPC 서비스 프로젝트를 만들고, proto 파일을 편집하고, 이중 스트리밍 호출을 추가하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/10/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 61324cdd5b574ea8a12a1be5846a25c311ab4499
ms.sourcegitcommit: 7d3c6565dda6241eb13f9a8e1e1fd89b1cfe4d18
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72259674"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a>자습서: ASP.NET Core에서 gRPC 클라이언트 및 서버 만들기

작성자: [John Luo](https://github.com/juntaoluo)

이 자습서에서는 .NET Core [gRPC](https://grpc.io/docs/guides/) 클라이언트와 ASP.NET Core gRPC 서버를 만드는 방법을 보여줍니다.

끝으로 gRPC Greeter 서비스와 통신하는 gRPC 클라이언트를 갖게 됩니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

이 자습서에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * gRPC 서버를 만듭니다.
> * gRPC 클라이언트를 만듭니다.
> * gRPC Greeter 서비스를 사용하여 gRPC 클라이언트 서비스를 테스트합니다.

## <a name="prerequisites"></a>전제 조건

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a>gRPC 서비스 만들기

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio를 시작하고 **새 프로젝트 만들기**를 선택합니다. 또는 Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.
* **새 프로젝트 만들기** 대화 상자에서 **gRPC 서비스**를 선택한 후 **다음**을 선택합니다.

  ![**새 프로젝트 만들기** 대화 상자](~/tutorials/grpc/grpc-start/static/cnp.png)

* 프로젝트 이름을 **GrpcGreeter**로 지정합니다. 코드를 복사하여 붙여넣을 때 네임스페이스가 일치하도록 프로젝트 이름을 *GrpcGreeter*로 지정해야 합니다.
* **만들기**를 선택합니다.
* **새 gRPC 서비스 만들기** 대화 상자에서 다음과 같이 합니다.
  * **gRPC 서비스** 템플릿이 선택됩니다.
  * **만들기**를 선택합니다.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.
* 프로젝트를 포함할 폴더로 디렉터리를 변경(`cd`)합니다.
* 다음 명령을 실행합니다.

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * `dotnet new` 명령은 *GrpcGreeter* 폴더에 새 gRPC 서비스를 만듭니다.
  * `code` 명령은 Visual Studio Code의 새 인스턴스에서 *GrpcGreeter* 폴더를 엽니다.

  다음과 같은 대화 상자가 표시됩니다. **‘GrpcGreeter’에서 빌드 및 디버그에 필요한 자산이 누락되었습니다.  Add them?** 라는 대화 상자가 나타납니다.
* **예**를 선택합니다.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

터미널에서 다음 명령을 실행합니다.

```dotnetcli
dotnet new grpc -o GrpcGreeter
cd GrpcGreeter
```

이전 명령은 [.NET Core CLI](/dotnet/core/tools/dotnet)를 사용하여 gRPC 서비스를 만듭니다.

### <a name="open-the-project"></a>프로젝트 열기

Visual Studio에서 **파일** > **열기**를 선택한 다음 *GrpcGreeter.csproj* 파일을 선택합니다.

---

### <a name="run-the-service"></a>서비스 실행

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* `Ctrl+F5`를 눌러 디버거 없이 gRPC 서비스를 실행합니다.

  Visual Studio는 명령 프롬프트에서 서비스를 실행합니다.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* `dotnet run`을 사용하여 명령줄에서 gRPC Greeter 프로젝트 *GrpcGreeter*를 실행합니다.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* `dotnet run`을 사용하여 명령줄에서 gRPC Greeter 프로젝트 *GrpcGreeter*를 실행합니다.

---

로그에는 `https://localhost:5001`에서 수신하는 서비스가 표시됩니다.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> gRPC 템플릿은 [TLS(전송 계층 보안)](https://tools.ietf.org/html/rfc5246)를 사용하도록 구성됩니다. gRPC 클라이언트는 HTTPS를 사용하여 서버를 호출해야 합니다.
>
> macOS는 TLS를 사용하는 ASP.NET Core gRPC를 지원하지 않습니다. macOS에서 gRPC 서비스를 성공적으로 실행하려면 추가 구성이 필요합니다. 자세한 내용은 [macOS에서 ASP.NET Core gRPC 앱을 시작할 수 없음](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)을 참조하세요.

### <a name="examine-the-project-files"></a>프로젝트 파일 검사

*GrpcGreeter* 프로젝트 파일:

* *greet.proto* &ndash; *Protos/greet.proto* 파일은 `Greeter` gRPC를 정의하고 gRPC 서버 자산 생성에 사용됩니다. 자세한 내용은 [gRPC 소개](xref:grpc/index)를 참조하세요.
* *Services* 폴더: `Greeter` 서비스의 구현을 포함합니다.
* *appSettings.json* &ndash; Kestrel에서 사용하는 프로토콜과 같은 구성 데이터를 포함합니다. 자세한 내용은 <xref:fundamentals/configuration/index>을 참조하세요.
* *Program.cs* &ndash; gRPC 서비스의 진입점을 포함합니다. 자세한 내용은 <xref:fundamentals/host/generic-host>을 참조하세요.
* *Startup.cs* &ndash; 앱 동작을 구성하는 코드를 포함합니다. 자세한 내용은 [앱 시작](xref:fundamentals/startup)을 참조하세요.

## <a name="create-the-grpc-client-in-a-net-console-app"></a>.NET 콘솔 앱에서 gRPC 클라이언트 만들기

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio의 두 번째 인스턴스를 열고 **새 프로젝트 만들기**를 선택합니다.
* **새 프로젝트 만들기** 대화 상자에서 **콘솔 앱(.NET Core)** 을 선택한 후 **다음**을 선택합니다.
* **이름** 텍스트 상자에 **GrpcGreeterClient**를 입력하고 **만들기**를 선택합니다.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.
* 프로젝트를 포함할 폴더로 디렉터리를 변경(`cd`)합니다.
* 다음 명령을 실행합니다.

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[Mac용 Visual Studio를 사용하여 macOS에서 완전한 .NET Core 솔루션 빌드](/dotnet/core/tutorials/using-on-mac-vs-full-solution)의 지침에 따라 *GrpcGreeterClient*라는 이름의 콘솔 앱을 만듭니다.

---

### <a name="add-required-packages"></a>필수 패키지 추가

gRPC 클라이언트 프로젝트에는 다음 패키지가 필요합니다.

* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client)는 .NET Core 클라이언트를 포함합니다.
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/)는 C#용 protobuf 메시지 API를 포함합니다.
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)는 protobuf 파일에 대한 C# 도구 지원을 포함합니다. 도구 패키지는 런타임에 필요하지 않으므로 종속성은 `PrivateAssets="All"`로 표시됩니다.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

PMC(패키지 관리자 콘솔) 또는 NuGet 패키지 관리를 사용하여 패키지를 설치합니다.

#### <a name="pmc-option-to-install-packages"></a>패키지 설치를 위한 PMC 옵션

* Visual Studio에서 **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 선택합니다.
* **패키지 관리자 콘솔** 창에서 `cd GrpcGreeterClient`를 실행하여 디렉터리를 *GrpcGreeterClient.csproj* 파일이 있는 폴더로 변경합니다.
* 다음 명령을 실행합니다.

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a>패키지 설치를 위한 NuGet 패키지 관리 옵션

* **솔루션 탐색기** > **NuGet 패키지 관리**에서 프로젝트를 마우스 오른쪽 단추로 클릭
* **찾아보기** 탭을 선택합니다.
* 검색 상자에 **Grpc.Net.Client**를 입력합니다.
* **찾아보기** 탭에서 **Grpc.Net.Client** 패키지를 선택하고 **설치**를 선택합니다.
* `Google.Protobuf` 및 `Grpc.Tools`에 대해 반복하세요.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

**통합 터미널**에서 다음 명령을 실행합니다.

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* **Solution Pad** > **패키지 추가**에서 **패키지** 폴더를 마우스 오른쪽 단추로 클릭합니다.
* 검색 상자에 **Grpc.Net.Client**를 입력합니다.
* 결과 창에서 **Grpc.Net.Client** 패키지를 선택하고 **패키지 추가**를 선택합니다.
* `Google.Protobuf` 및 `Grpc.Tools`에 대해 반복하세요.

---

### <a name="add-greetproto"></a>greet.proto 추가

* gRPC 클라이언트 프로젝트에서 *Protos* 폴더를 만듭니다.
* *Protos\greet.proto* 파일을 gRPC Greeter 서비스에서 gRPC 클라이언트 프로젝트로 복사합니다.
* *GrpcGreeterClient.csproj* 프로젝트 파일을 편집합니다.

  # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

  프로젝트를 마우스 오른쪽 단추로 클릭하고 **프로젝트 파일 편집**을 선택합니다.

  # <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  *GrpcGreeterClient.csproj* 파일을 선택합니다.

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

  프로젝트를 마우스 오른쪽 단추로 클릭하고 **도구** > **파일 편집**을 선택합니다.

  ---

* *greet.proto* 파일을 참조하는 `<Protobuf>` 요소를 사용하여 항목 그룹을 추가합니다.

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a>Greeter 클라이언트 만들기

프로젝트를 빌드하여 `GrpcGreeter` 네임스페이스에 유형을 만듭니다. `GrpcGreeter` 유형은 빌드 프로세스에 의해 자동으로 생성됩니다.

gRPC 클라이언트 *Program.cs* 파일을 다음 코드로 업데이트합니다.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

*Program.cs*는 gRPC 클라이언트의 진입점 및 논리를 포함합니다.

Greeter 클라이언트는 다음에 의해 생성됩니다.

* gRPC 서비스에 대한 연결을 만들기 위한 정보가 포함된 `HttpClient` 인스턴스화.
* `HttpClient`를 사용하여 gRPC 채널 및 Greeter 클라이언트를 구성합니다.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

Greeter 클라이언트가 비동기 `SayHello` 메서드를 호출합니다. `SayHello` 호출의 결과가 표시됩니다.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>gRPC Greeter 서비스를 사용하여 gRPC 클라이언트 테스트

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Greeter 서비스에서 `Ctrl+F5`를 눌러 디버거 없이 서버를 시작합니다.
* `GrpcGreeterClient` 프로젝트에서 `Ctrl+F5`를 눌러 디버거 없이 클라이언트를 시작합니다.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Greeter 서비스를 시작합니다.
* 클라이언트를 시작합니다.


# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* Greeter 서비스를 시작합니다.
* 클라이언트를 시작합니다.

---

클라이언트가 이름 *GreeterClient*가 포함된 메시지와 함께 인사말을 서비스에 보냅니다. 서비스는 "Hello GreeterClient"라는 메시지를 응답으로 보냅니다. "Hello GreeterClient" 응답이 명령 프롬프트에 표시됩니다.

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

gRPC 서비스는 명령 프롬프트에 기록된 로그에 성공한 호출의 세부 정보를 기록합니다.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

> [!NOTE]
> 이 문서의 코드에는 gRPC 서비스 보호를 위해 ASP.NET Core HTTPS 개발 인증서가 필요합니다. 클라이언트가 `The remote certificate is invalid according to the validation procedure.`라는 메시지와 함께 실패하는 경우에는 개발 인증서를 신뢰할 수 없습니다. 이 문제의 해결 지침은 [Windows 및 macOS에서의 ASP.NET Core HTTPS 개발 인증서 신뢰](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)를 참조하세요.

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a>다음 단계

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
