<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="e26b6-101">Microsoft Teams 选项卡应用程序有多个选项可以对用户进行身份验证并调用 Microsoft Graph。</span><span class="sxs-lookup"><span data-stu-id="e26b6-101">Microsoft Teams tab applications have multiple options to authenticate the user and call Microsoft Graph.</span></span> <span data-ttu-id="e26b6-102">在此练习中，你将实现一个选项卡，该选项卡[](/microsoftteams/platform/tabs/how-to/authentication/auth-aad-sso)执行单一登录以在客户端上获取身份验证令牌，然后使用服务器上代表流[](/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)交换该令牌，以访问 Microsoft Graph。</span><span class="sxs-lookup"><span data-stu-id="e26b6-102">In this exercise, you'll implement a tab that does [single sign-on](/microsoftteams/platform/tabs/how-to/authentication/auth-aad-sso) to get an auth token on the client, then uses the [on-behalf-of flow](/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow) on the server to exchange that token to get access to Microsoft Graph.</span></span>

<span data-ttu-id="e26b6-103">有关其他备选方法，请参阅以下内容。</span><span class="sxs-lookup"><span data-stu-id="e26b6-103">For other alternatives, see the following.</span></span>

- <span data-ttu-id="e26b6-104">[使用 Microsoft Graph 控件生成 Microsoft Teams Toolkit。](/graph/toolkit/get-started/build-a-microsoft-teams-tab)</span><span class="sxs-lookup"><span data-stu-id="e26b6-104">[Build a Microsoft Teams tab with the Microsoft Graph Toolkit](/graph/toolkit/get-started/build-a-microsoft-teams-tab).</span></span> <span data-ttu-id="e26b6-105">此示例完全在客户端，使用 Microsoft Graph Toolkit处理身份验证和调用 Microsoft Graph。</span><span class="sxs-lookup"><span data-stu-id="e26b6-105">This sample is completely client-side, and uses the Microsoft Graph Toolkit to handle authentication and making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="e26b6-106">[Microsoft Teams 身份验证示例](https://github.com/OfficeDev/microsoft-teams-sample-auth-node)。</span><span class="sxs-lookup"><span data-stu-id="e26b6-106">[Microsoft Teams Authentication Sample](https://github.com/OfficeDev/microsoft-teams-sample-auth-node).</span></span> <span data-ttu-id="e26b6-107">此示例包含多个涉及不同身份验证方案的示例。</span><span class="sxs-lookup"><span data-stu-id="e26b6-107">This sample contains multiple examples covering different authentication scenarios.</span></span>

## <a name="create-the-project"></a><span data-ttu-id="e26b6-108">创建项目</span><span class="sxs-lookup"><span data-stu-id="e26b6-108">Create the project</span></span>

<span data-ttu-id="e26b6-109">首先创建一个ASP.NET核心 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="e26b6-109">Start by creating an ASP.NET Core web app.</span></span>

1. <span data-ttu-id="e26b6-110">在要创建项目的目录中 (CLI) 打开命令行界面。</span><span class="sxs-lookup"><span data-stu-id="e26b6-110">Open your command-line interface (CLI) in a directory where you want to create the project.</span></span> <span data-ttu-id="e26b6-111">运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="e26b6-111">Run the following command.</span></span>

    ```Shell
    dotnet new webapp -o GraphTutorial
    ```

1. <span data-ttu-id="e26b6-112">创建项目后，将当前目录更改为 **GraphTu一l** 目录，并运行 CLI 中的以下命令，验证项目是否正常工作。</span><span class="sxs-lookup"><span data-stu-id="e26b6-112">Once the project is created, verify that it works by changing the current directory to the **GraphTutorial** directory and running the following command in your CLI.</span></span>

    ```Shell
    dotnet run
    ```

1. <span data-ttu-id="e26b6-113">打开浏览器并浏览到 `https://localhost:5001` 。</span><span class="sxs-lookup"><span data-stu-id="e26b6-113">Open your browser and browse to `https://localhost:5001`.</span></span> <span data-ttu-id="e26b6-114">如果一切正常，则应该会看到默认ASP.NET核心页面。</span><span class="sxs-lookup"><span data-stu-id="e26b6-114">If everything is working, you should see a default ASP.NET Core page.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e26b6-115">如果您收到一条警告，指出 **localhost** 的证书不可信，您可以使用 .NET Core CLI 安装和信任开发证书。</span><span class="sxs-lookup"><span data-stu-id="e26b6-115">If you receive a warning that the certificate for **localhost** is un-trusted you can use the .NET Core CLI to install and trust the development certificate.</span></span> <span data-ttu-id="e26b6-116">有关 [特定操作系统的说明，请参阅 ASP.NET Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.1) 中的强制 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="e26b6-116">See [Enforce HTTPS in ASP.NET Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.1) for instructions for specific operating systems.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="e26b6-117">添加 NuGet 程序包</span><span class="sxs-lookup"><span data-stu-id="e26b6-117">Add NuGet packages</span></span>

<span data-ttu-id="e26b6-118">在继续之前，请安装一些稍后将使用的其他 NuGet 程序包。</span><span class="sxs-lookup"><span data-stu-id="e26b6-118">Before moving on, install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="e26b6-119">用于进行身份验证和请求访问令牌的[Microsoft.Identity.Web。](https://www.nuget.org/packages/Microsoft.Identity.Web/)</span><span class="sxs-lookup"><span data-stu-id="e26b6-119">[Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) for authenticating and requesting access tokens.</span></span>
- <span data-ttu-id="e26b6-120">[Microsoft.Identity.Web.MicrosoftGraph，](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph/) 用于添加使用 Microsoft.Identity.Web 配置的 Microsoft Graph 支持。</span><span class="sxs-lookup"><span data-stu-id="e26b6-120">[Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph/) for adding Microsoft Graph support configured with Microsoft.Identity.Web.</span></span>
- <span data-ttu-id="e26b6-121">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) 更新由 Microsoft.Identity.Web.MicrosoftGraph 安装的此程序包的版本。</span><span class="sxs-lookup"><span data-stu-id="e26b6-121">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) to update the version of this package installed by Microsoft.Identity.Web.MicrosoftGraph.</span></span>
- <span data-ttu-id="e26b6-122">[Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) 启用 Newtonsoft JSON 转换器以实现与 Microsoft Graph SDK 的兼容性。</span><span class="sxs-lookup"><span data-stu-id="e26b6-122">[Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) to enable Newtonsoft JSON converters for compatibility with the Microsoft Graph SDK.</span></span>
- <span data-ttu-id="e26b6-123">[TimeZoneConverter，](https://github.com/mj1856/TimeZoneConverter) 用于将 Windows 时区标识符转换为 IANA 标识符。</span><span class="sxs-lookup"><span data-stu-id="e26b6-123">[TimeZoneConverter](https://github.com/mj1856/TimeZoneConverter) for translating Windows time zone identifiers to IANA identifiers.</span></span>

1. <span data-ttu-id="e26b6-124">在 CLI 中运行以下命令以安装依赖项。</span><span class="sxs-lookup"><span data-stu-id="e26b6-124">Run the following commands in your CLI to install the dependencies.</span></span>

    ```Shell
    dotnet add package Microsoft.Identity.Web --version 1.1.0
    dotnet add package Microsoft.Identity.Web.MicrosoftGraph --version 1.1.0
    dotnet add package Microsoft.Graph --version 3.16.0
    dotnet add package Microsoft.AspNetCore.Mvc.NewtonsoftJson
    dotnet add package TimeZoneConverter
    ```

## <a name="design-the-app"></a><span data-ttu-id="e26b6-125">设计应用</span><span class="sxs-lookup"><span data-stu-id="e26b6-125">Design the app</span></span>

<span data-ttu-id="e26b6-126">在此部分中，你将创建应用程序的基本 UI 结构。</span><span class="sxs-lookup"><span data-stu-id="e26b6-126">In this section you will create the basic UI structure of the application.</span></span>

> [!TIP]
> <span data-ttu-id="e26b6-127">可以使用任何文本编辑器编辑本教程的源文件。</span><span class="sxs-lookup"><span data-stu-id="e26b6-127">You can use any text editor to edit the source files for this tutorial.</span></span> <span data-ttu-id="e26b6-128">但是 [，Visual Studio代码](https://code.visualstudio.com/) 提供了其他功能，如调试和 Intellisense。</span><span class="sxs-lookup"><span data-stu-id="e26b6-128">However, [Visual Studio Code](https://code.visualstudio.com/) provides additional features, such as debugging and Intellisense.</span></span>

1. <span data-ttu-id="e26b6-129">打开 **./Pages/Shared/_Layout.cshtml，** 并将其全部内容替换为以下代码以更新应用程序的全局布局。</span><span class="sxs-lookup"><span data-stu-id="e26b6-129">Open **./Pages/Shared/_Layout.cshtml** and replace its entire contents with the following code to update the global layout of the app.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Pages/Shared/_Layout.cshtml" id="LayoutSnippet":::

    <span data-ttu-id="e26b6-130">这将使用 [Fluent UI](https://developer.microsoft.com/fluentui)替换 Bootstrap、添加 Microsoft [Teams SDK](/javascript/api/overview/msteams-client)并简化布局。</span><span class="sxs-lookup"><span data-stu-id="e26b6-130">This replaces Bootstrap with [Fluent UI](https://developer.microsoft.com/fluentui), adds the [Microsoft Teams SDK](/javascript/api/overview/msteams-client), and simplifies the layout.</span></span>

1. <span data-ttu-id="e26b6-131">打开 **./wwwroot/js/site.js** 并添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="e26b6-131">Open **./wwwroot/js/site.js** and add the following code.</span></span>

    :::code language="javascript" source="../demo/GraphTutorial/wwwroot/js/site.js" id="ThemeSupportSnippet":::

    <span data-ttu-id="e26b6-132">这将添加一个简单的主题更改处理程序，以更改深色和高对比度主题的默认文本颜色。</span><span class="sxs-lookup"><span data-stu-id="e26b6-132">This adds a simple theme change handler to change the default text color for dark and high contrast themes.</span></span>

1. <span data-ttu-id="e26b6-133">打开 **./wwwroot/css/site.css，** 并将其内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="e26b6-133">Open **./wwwroot/css/site.css** and replace its contents with the following.</span></span>

    :::code language="css" source="../demo/GraphTutorial/wwwroot/css/site.css" id="CssSnippet":::

1. <span data-ttu-id="e26b6-134">打开 **./Pages/Index.cshtml** 并将其内容替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="e26b6-134">Open **./Pages/Index.cshtml** and replace its contents with the following code.</span></span>

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

1. <span data-ttu-id="e26b6-135">打开 **./Startup.cs** 并 `app.UseHttpsRedirection();` 删除方法中的 `Configure` 行。</span><span class="sxs-lookup"><span data-stu-id="e26b6-135">Open **./Startup.cs** and remove the `app.UseHttpsRedirection();` line in the `Configure` method.</span></span> <span data-ttu-id="e26b6-136">这是 ngrok 隧道正常工作所必需的。</span><span class="sxs-lookup"><span data-stu-id="e26b6-136">This is necessary for ngrok tunneling to work.</span></span>

## <a name="run-ngrok"></a><span data-ttu-id="e26b6-137">运行 ngrok</span><span class="sxs-lookup"><span data-stu-id="e26b6-137">Run ngrok</span></span>

<span data-ttu-id="e26b6-138">Microsoft Teams 不支持应用的本地托管。</span><span class="sxs-lookup"><span data-stu-id="e26b6-138">Microsoft Teams does not support local hosting for apps.</span></span> <span data-ttu-id="e26b6-139">托管应用的服务器必须使用 HTTPS 终结点从云中提供。</span><span class="sxs-lookup"><span data-stu-id="e26b6-139">The server hosting your app must be available from the cloud using HTTPS endpoints.</span></span> <span data-ttu-id="e26b6-140">对于本地调试，可以使用 ngrok 为本地托管的项目创建公用 URL。</span><span class="sxs-lookup"><span data-stu-id="e26b6-140">For debugging locally, you can use ngrok to create a public URL for your locally-hosted project.</span></span>

1. <span data-ttu-id="e26b6-141">打开 CLI 并运行以下命令以启动 ngrok。</span><span class="sxs-lookup"><span data-stu-id="e26b6-141">Open your CLI and run the following command to start ngrok.</span></span>

    ```Shell
    ngrok http 5000
    ```

1. <span data-ttu-id="e26b6-142">ngrok 启动后，复制 HTTPS 转发 URL。</span><span class="sxs-lookup"><span data-stu-id="e26b6-142">Once ngrok starts, copy the HTTPS Forwarding URL.</span></span> <span data-ttu-id="e26b6-143">它应如下所示 `https://50153897dd4d.ngrok.io` 。</span><span class="sxs-lookup"><span data-stu-id="e26b6-143">It should look like `https://50153897dd4d.ngrok.io`.</span></span> <span data-ttu-id="e26b6-144">在稍后的步骤中将需要此值。</span><span class="sxs-lookup"><span data-stu-id="e26b6-144">You'll need this value in later steps.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e26b6-145">如果使用的是 ngrok 的免费版本，则每次重新启动 ngrok 时，转发 URL 都会更改。</span><span class="sxs-lookup"><span data-stu-id="e26b6-145">If you are using the free version of ngrok, the forwarding URL changes every time you restart ngrok.</span></span> <span data-ttu-id="e26b6-146">建议您保持 ngrok 运行，直到完成本教程以保持相同的 URL。</span><span class="sxs-lookup"><span data-stu-id="e26b6-146">It's recommended that you leave ngrok running until you complete this tutorial to keep the same URL.</span></span> <span data-ttu-id="e26b6-147">如果必须重新启动 ngrok，则需要在所使用的任何位置更新 URL，并重新安装 Microsoft Teams 中的应用。</span><span class="sxs-lookup"><span data-stu-id="e26b6-147">If you have to restart ngrok, you'll need to update your URL everywhere that it is used and reinstall the app in Microsoft Teams.</span></span>
