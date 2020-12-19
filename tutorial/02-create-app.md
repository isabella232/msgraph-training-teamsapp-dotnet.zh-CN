<!-- markdownlint-disable MD002 MD041 -->

Microsoft Teams 选项卡应用程序有多个选项可以对用户进行身份验证并调用 Microsoft Graph。 在此练习中，你将实现一个选项卡，该选项卡[](/microsoftteams/platform/tabs/how-to/authentication/auth-aad-sso)执行单一登录以在客户端上获取身份验证令牌，然后使用服务器上代表流[](/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)交换该令牌，以访问 Microsoft Graph。

有关其他备选方法，请参阅以下内容。

- [使用 Microsoft Graph 控件生成 Microsoft Teams Toolkit。](/graph/toolkit/get-started/build-a-microsoft-teams-tab) 此示例完全在客户端，使用 Microsoft Graph Toolkit处理身份验证和调用 Microsoft Graph。
- [Microsoft Teams 身份验证示例](https://github.com/OfficeDev/microsoft-teams-sample-auth-node)。 此示例包含多个涉及不同身份验证方案的示例。

## <a name="create-the-project"></a>创建项目

首先创建一个ASP.NET核心 Web 应用。

1. 在要创建项目的目录中 (CLI) 打开命令行界面。 运行以下命令。

    ```Shell
    dotnet new webapp -o GraphTutorial
    ```

1. 创建项目后，将当前目录更改为 **GraphTu一l** 目录，并运行 CLI 中的以下命令，验证项目是否正常工作。

    ```Shell
    dotnet run
    ```

1. 打开浏览器并浏览到 `https://localhost:5001` 。 如果一切正常，则应该会看到默认ASP.NET核心页面。

> [!IMPORTANT]
> 如果您收到一条警告，指出 **localhost** 的证书不可信，您可以使用 .NET Core CLI 安装和信任开发证书。 有关 [特定操作系统的说明，请参阅 ASP.NET Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.1) 中的强制 HTTPS。

## <a name="add-nuget-packages"></a>添加 NuGet 程序包

在继续之前，请安装一些稍后将使用的其他 NuGet 程序包。

- 用于进行身份验证和请求访问令牌的[Microsoft.Identity.Web。](https://www.nuget.org/packages/Microsoft.Identity.Web/)
- [Microsoft.Identity.Web.MicrosoftGraph，](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph/) 用于添加使用 Microsoft.Identity.Web 配置的 Microsoft Graph 支持。
- [Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) 更新由 Microsoft.Identity.Web.MicrosoftGraph 安装的此程序包的版本。
- [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) 启用 Newtonsoft JSON 转换器以实现与 Microsoft Graph SDK 的兼容性。
- [TimeZoneConverter，](https://github.com/mj1856/TimeZoneConverter) 用于将 Windows 时区标识符转换为 IANA 标识符。

1. 在 CLI 中运行以下命令以安装依赖项。

    ```Shell
    dotnet add package Microsoft.Identity.Web --version 1.1.0
    dotnet add package Microsoft.Identity.Web.MicrosoftGraph --version 1.1.0
    dotnet add package Microsoft.Graph --version 3.16.0
    dotnet add package Microsoft.AspNetCore.Mvc.NewtonsoftJson
    dotnet add package TimeZoneConverter
    ```

## <a name="design-the-app"></a>设计应用

在此部分中，你将创建应用程序的基本 UI 结构。

> [!TIP]
> 可以使用任何文本编辑器编辑本教程的源文件。 但是 [，Visual Studio代码](https://code.visualstudio.com/) 提供了其他功能，如调试和 Intellisense。

1. 打开 **./Pages/Shared/_Layout.cshtml，** 并将其全部内容替换为以下代码以更新应用程序的全局布局。

    :::code language="cshtml" source="../demo/GraphTutorial/Pages/Shared/_Layout.cshtml" id="LayoutSnippet":::

    这将使用 [Fluent UI](https://developer.microsoft.com/fluentui)替换 Bootstrap、添加 Microsoft [Teams SDK](/javascript/api/overview/msteams-client)并简化布局。

1. 打开 **./wwwroot/js/site.js** 并添加以下代码。

    :::code language="javascript" source="../demo/GraphTutorial/wwwroot/js/site.js" id="ThemeSupportSnippet":::

    这将添加一个简单的主题更改处理程序，以更改深色和高对比度主题的默认文本颜色。

1. 打开 **./wwwroot/css/site.css，** 并将其内容替换为以下内容。

    :::code language="css" source="../demo/GraphTutorial/wwwroot/css/site.css" id="CssSnippet":::

1. 打开 **./Pages/Index.cshtml** 并将其内容替换为以下代码。

    ```cshtml
    @page
    @model IndexModel
    @{
      ViewData["Title"] = "Home page";
    }

    <div id="tab-container">
      <h1 class="ms-fontSize-24 ms-fontWeight-semibold">Loading...</h1>
    </div>

    @section Scripts
    {
      <script>
      </script>
    }
    ```

1. 打开 **./Startup.cs** 并 `app.UseHttpsRedirection();` 删除方法中的 `Configure` 行。 这是 ngrok 隧道正常工作所必需的。

## <a name="run-ngrok"></a>运行 ngrok

Microsoft Teams 不支持应用的本地托管。 托管应用的服务器必须使用 HTTPS 终结点从云中提供。 对于本地调试，可以使用 ngrok 为本地托管的项目创建公用 URL。

1. 打开 CLI 并运行以下命令以启动 ngrok。

    ```Shell
    ngrok http 5000
    ```

1. ngrok 启动后，复制 HTTPS 转发 URL。 它应如下所示 `https://50153897dd4d.ngrok.io` 。 在稍后的步骤中将需要此值。

> [!IMPORTANT]
> 如果使用的是 ngrok 的免费版本，则每次重新启动 ngrok 时，转发 URL 都会更改。 建议您保持 ngrok 运行，直到完成本教程以保持相同的 URL。 如果必须重新启动 ngrok，则需要在所使用的任何位置更新 URL，并重新安装 Microsoft Teams 中的应用。
