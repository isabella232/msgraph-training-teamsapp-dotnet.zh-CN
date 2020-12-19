<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="ae071-101">在此练习中，你将扩展上一练习中的应用程序，以支持使用 Azure AD 的单一登录身份验证。</span><span class="sxs-lookup"><span data-stu-id="ae071-101">In this exercise you will extend the application from the previous exercise to support single sign-on authentication with Azure AD.</span></span> <span data-ttu-id="ae071-102">这是获取调用 Microsoft Graph API 所需的 OAuth 访问令牌所必需的。</span><span class="sxs-lookup"><span data-stu-id="ae071-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph API.</span></span> <span data-ttu-id="ae071-103">在此步骤中，您将配置 [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) 库。</span><span class="sxs-lookup"><span data-stu-id="ae071-103">In this step you will configure the [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) library.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ae071-104">若要避免在源中存储应用程序 ID 和密码，将使用 [.NET 密码管理器](/aspnet/core/security/app-secrets) 存储这些值。</span><span class="sxs-lookup"><span data-stu-id="ae071-104">To avoid storing the application ID and secret in source, you will use the [.NET Secret Manager](/aspnet/core/security/app-secrets) to store these values.</span></span> <span data-ttu-id="ae071-105">密码管理器仅供开发使用，生产应用应使用受信任的密码管理器来存储密码。</span><span class="sxs-lookup"><span data-stu-id="ae071-105">The Secret Manager is for development purposes only, production apps should use a trusted secret manager for storing secrets.</span></span>

1. <span data-ttu-id="ae071-106">打开 **./appsettings.js，** 并将其内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="ae071-106">Open **./appsettings.json** and replace its contents with the following.</span></span>

    :::code language="json" source="../demo/GraphTutorial/appsettings.example.json" highlight="2-8":::

1. <span data-ttu-id="ae071-107">在 **GraphTu一l.csproj** 所在的目录中打开 CLI，然后运行以下命令，使用 Azure 门户中的应用程序 ID 和应用程序密码进行代用。 `YOUR_APP_ID` `YOUR_APP_SECRET`</span><span class="sxs-lookup"><span data-stu-id="ae071-107">Open your CLI in the directory where **GraphTutorial.csproj** is located, and run the following commands, substituting `YOUR_APP_ID` with your application ID from the Azure portal, and `YOUR_APP_SECRET` with your application secret.</span></span>

    ```Shell
    dotnet user-secrets init
    dotnet user-secrets set "AzureAd:ClientId" "YOUR_APP_ID"
    dotnet user-secrets set "AzureAd:ClientSecret" "YOUR_APP_SECRET"
    ```

## <a name="implement-sign-in"></a><span data-ttu-id="ae071-108">实现登录</span><span class="sxs-lookup"><span data-stu-id="ae071-108">Implement sign-in</span></span>

<span data-ttu-id="ae071-109">首先，在应用的 JavaScript 代码中实现单一登录。</span><span class="sxs-lookup"><span data-stu-id="ae071-109">First, implement single sign-on in the app's JavaScript code.</span></span> <span data-ttu-id="ae071-110">你将使用 [Microsoft Teams JavaScript SDK](/javascript/api/overview/msteams-client) 获取访问令牌，该令牌允许在 Teams 客户端中运行的 JavaScript 代码对将稍后实现的 Web API 进行 AJAX 调用。</span><span class="sxs-lookup"><span data-stu-id="ae071-110">You will use the [Microsoft Teams JavaScript SDK](/javascript/api/overview/msteams-client) to get an access token which allows the JavaScript code running in the Teams client to make AJAX calls to Web API you will implement later.</span></span>

1. <span data-ttu-id="ae071-111">打开 **./Pages/Index.cshtml，** 在标记中添加以下 `<script>` 代码。</span><span class="sxs-lookup"><span data-stu-id="ae071-111">Open **./Pages/Index.cshtml** and add the following code inside the `<script>` tag.</span></span>

    ```javascript
    (function () {
      if (microsoftTeams) {
        microsoftTeams.initialize();

        microsoftTeams.authentication.getAuthToken({
          successCallback: (token) => {
            // TEMPORARY: Display the access token for debugging
            $('#tab-container').empty();

            $('<code/>', {
              text: token,
              style: 'word-break: break-all;'
            }).appendTo('#tab-container');
          },
          failureCallback: (error) => {
            renderError(error);
          }
        });
      }
    })();

    function renderError(error) {
      $('#tab-container').empty();

      $('<h1/>', {
        text: 'Error'
      }).appendTo('#tab-container');

      $('<code/>', {
        text: JSON.stringify(error, Object.getOwnPropertyNames(error)),
        style: 'word-break: break-all;'
      }).appendTo('#tab-container');
    }
    ```

    <span data-ttu-id="ae071-112">这将调用 `microsoftTeams.authentication.getAuthToken` 以登录 Teams 的用户身份以静默方式进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="ae071-112">This calls the `microsoftTeams.authentication.getAuthToken` to silently authenticate as the user that is signed in to Teams.</span></span> <span data-ttu-id="ae071-113">通常不会涉及到任何 UI 提示，除非用户必须同意。</span><span class="sxs-lookup"><span data-stu-id="ae071-113">There is typically not any UI prompts involved, unless the user has to consent.</span></span> <span data-ttu-id="ae071-114">然后，代码在选项卡中显示令牌。</span><span class="sxs-lookup"><span data-stu-id="ae071-114">The code then displays the token in the tab.</span></span>

1. <span data-ttu-id="ae071-115">在 CLI 中运行以下命令，保存更改并启动应用程序。</span><span class="sxs-lookup"><span data-stu-id="ae071-115">Save your changes and start your application by running the following command in your CLI.</span></span>

    ```Shell
    dotnet run
    ```

    > [!IMPORTANT]
    > <span data-ttu-id="ae071-116">如果已重新启动 ngrok 且 ngrok URL 已更改，请务必在测试之前在下列位置 **更新** ngrok 值。</span><span class="sxs-lookup"><span data-stu-id="ae071-116">If you have restarted ngrok and your ngrok URL has changed, be sure to update the ngrok value in the following place **before** you test.</span></span>
    >
    > - <span data-ttu-id="ae071-117">应用注册中的重定向 URI</span><span class="sxs-lookup"><span data-stu-id="ae071-117">The redirect URI in your app registration</span></span>
    > - <span data-ttu-id="ae071-118">应用注册中的应用程序 ID URI</span><span class="sxs-lookup"><span data-stu-id="ae071-118">The application ID URI in your app registration</span></span>
    > - <span data-ttu-id="ae071-119">`contentUrl` in manifest.json</span><span class="sxs-lookup"><span data-stu-id="ae071-119">`contentUrl` in manifest.json</span></span>
    > - <span data-ttu-id="ae071-120">`validDomains` in manifest.json</span><span class="sxs-lookup"><span data-stu-id="ae071-120">`validDomains` in manifest.json</span></span>
    > - <span data-ttu-id="ae071-121">`resource` in manifest.json</span><span class="sxs-lookup"><span data-stu-id="ae071-121">`resource` in manifest.json</span></span>

1. <span data-ttu-id="ae071-122">创建 ZIP 文件 **，manifest.js、color.png\*\*\*\*和** outline.png。 \*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="ae071-122">Create a ZIP file with **manifest.json**, **color.png**, and **outline.png**.</span></span>

1. <span data-ttu-id="ae071-123">在 Microsoft Teams中，选择左侧栏中的应用，选择"上传自定义应用"，然后选择"为我或 **我的团队上载"。**</span><span class="sxs-lookup"><span data-stu-id="ae071-123">In Microsoft Teams, select **Apps** in the left-hand bar, select **Upload a custom app**, then select **Upload for me or my teams**.</span></span>

    ![Microsoft Teams 中"上传自定义应用"链接的屏幕截图](images/upload-custom-app.png)

1. <span data-ttu-id="ae071-125">浏览到之前创建的 ZIP 文件，然后选择"**打开"。**</span><span class="sxs-lookup"><span data-stu-id="ae071-125">Browse to the ZIP file you created previously and select **Open**.</span></span>

1. <span data-ttu-id="ae071-126">查看应用程序信息，然后选择"添加 **"。**</span><span class="sxs-lookup"><span data-stu-id="ae071-126">Review the application information and select **Add**.</span></span>

1. <span data-ttu-id="ae071-127">应用程序将在 Teams 中打开并显示访问令牌。</span><span class="sxs-lookup"><span data-stu-id="ae071-127">The application opens in Teams and displays an access token.</span></span>

<span data-ttu-id="ae071-128">如果复制令牌，可以将其粘贴到[jwt.ms。](https://jwt.ms)</span><span class="sxs-lookup"><span data-stu-id="ae071-128">If you copy the token, you can paste it into [jwt.ms](https://jwt.ms).</span></span> <span data-ttu-id="ae071-129">确认声明 (访问群体) 应用程序 ID，并且声明 (范围) 是创建的 `aud` `scp` API `access_as_user` 范围。</span><span class="sxs-lookup"><span data-stu-id="ae071-129">Verify that the audience (the `aud` claim) is your application ID, and the only scope (the `scp` claim) is the `access_as_user` API scope you created.</span></span> <span data-ttu-id="ae071-130">这意味着此令牌不会授予对 Microsoft Graph 的直接访问权！</span><span class="sxs-lookup"><span data-stu-id="ae071-130">That means that this token does not grant direct access to Microsoft Graph!</span></span> <span data-ttu-id="ae071-131">相反，您即将实现的 Web API 将需要使用代表流交换此[](/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)令牌，以获得将用于 Microsoft Graph 调用的令牌。</span><span class="sxs-lookup"><span data-stu-id="ae071-131">Instead, the Web API you will implement soon will need to exchange this token using the [on-behalf-of flow](/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow) to get a token that will work with Microsoft Graph calls.</span></span>

## <a name="configure-authentication-in-the-aspnet-core-app"></a><span data-ttu-id="ae071-132">在核心应用中ASP.NET身份验证</span><span class="sxs-lookup"><span data-stu-id="ae071-132">Configure authentication in the ASP.NET Core app</span></span>

<span data-ttu-id="ae071-133">首先，将 Microsoft Identity 平台服务添加到应用程序。</span><span class="sxs-lookup"><span data-stu-id="ae071-133">Start by adding the Microsoft Identity platform services to the application.</span></span>

1. <span data-ttu-id="ae071-134">打开 **./Startup.cs** 文件，将以下 `using` 语句添加到文件顶部。</span><span class="sxs-lookup"><span data-stu-id="ae071-134">Open the **./Startup.cs** file and add the following `using` statement to the top of the file.</span></span>

    ```csharp
    using Microsoft.Identity.Web;
    ```

1. <span data-ttu-id="ae071-135">在函数中的行之前 `app.UseAuthorization();` 添加以下 `Configure` 行。</span><span class="sxs-lookup"><span data-stu-id="ae071-135">Add the following line just before the `app.UseAuthorization();` line in the `Configure` function.</span></span>

    ```csharp
    app.UseAuthentication();
    ```

1. <span data-ttu-id="ae071-136">在函数中的行后 `endpoints.MapRazorPages();` 添加以下 `Configure` 行。</span><span class="sxs-lookup"><span data-stu-id="ae071-136">Add the following line just after the `endpoints.MapRazorPages();` line in the `Configure` function.</span></span>

    ```csharp
    endpoints.MapControllers();
    ```

1. <span data-ttu-id="ae071-137">将现有的 `ConfigureServices` 函数替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="ae071-137">Replace the existing `ConfigureServices` function with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Startup.cs" id="ConfigureServicesSnippet":::

    <span data-ttu-id="ae071-138">此代码将应用程序配置为允许对 Web API 的调用基于标头中的 JWT 承载令牌 `Authorization` 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="ae071-138">This code configures the application to allow calls to Web APIs to be authenticated based on the JWT bearer token in the `Authorization` header.</span></span> <span data-ttu-id="ae071-139">它还添加了令牌获取服务，这些服务可以通过代表流交换该令牌。</span><span class="sxs-lookup"><span data-stu-id="ae071-139">It also adds the token acquisition services that can exchange that token via the on-behalf-of flow.</span></span>

## <a name="create-the-web-api-controller"></a><span data-ttu-id="ae071-140">创建 Web API 控制器</span><span class="sxs-lookup"><span data-stu-id="ae071-140">Create the Web API controller</span></span>

1. <span data-ttu-id="ae071-141">在名为 Controllers 的项目的根目录中创建新 **目录**。</span><span class="sxs-lookup"><span data-stu-id="ae071-141">Create a new directory in the root of the project named **Controllers**.</span></span>

1. <span data-ttu-id="ae071-142">在名为 CalendarController.cs 的 **./Controllers** **目录中** 创建新文件，并添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="ae071-142">Create a new file in the **./Controllers** directory named **CalendarController.cs** and add the following code.</span></span>

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Net;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    using Microsoft.AspNetCore.Http;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Identity.Web;
    using Microsoft.Identity.Web.Resource;
    using Microsoft.Graph;
    using TimeZoneConverter;

    namespace GraphTutorial.Controllers
    {
        [ApiController]
        [Route("[controller]")]
        [Authorize]
        public class CalendarController : ControllerBase
        {
            private static readonly string[] apiScopes = new[] { "access_as_user" };

            private readonly GraphServiceClient _graphClient;
            private readonly ITokenAcquisition _tokenAcquisition;
            private readonly ILogger<CalendarController> _logger;

            public CalendarController(ITokenAcquisition tokenAcquisition, GraphServiceClient graphClient, ILogger<CalendarController> logger)
            {
                _tokenAcquisition = tokenAcquisition;
                _graphClient = graphClient;
                _logger = logger;
            }

            [HttpGet]
            public async Task<string> Get()
            {
                // This verifies that the access_as_user scope is
                // present in the bearer token, throws if not
                HttpContext.VerifyUserHasAnyAcceptedScope(apiScopes);

                // To verify that the identity libraries have authenticated
                // based on the token, log the user's name
                _logger.LogInformation($"Authenticated user: {User.GetDisplayName()}");

                try
                {
                    // TEMPORARY
                    // Get a Graph token via OBO flow
                    var token = await _tokenAcquisition
                        .GetAccessTokenForUserAsync(new[]{
                            "User.Read",
                            "MailboxSettings.Read",
                            "Calendars.ReadWrite" });

                    // Log the token
                    _logger.LogInformation($"Access token for Graph: {token}");
                    return "{ \"status\": \"OK\" }";
                }
                catch (MicrosoftIdentityWebChallengeUserException ex)
                {
                    _logger.LogError(ex, "Consent required");
                    // This exception indicates consent is required.
                    // Return a 403 with "consent_required" in the body
                    // to signal to the tab it needs to prompt for consent
                    HttpContext.Response.ContentType = "text/plain";
                    HttpContext.Response.StatusCode = (int)HttpStatusCode.Forbidden;
                    await HttpContext.Response.WriteAsync("consent_required");
                    return null;
                }
                catch (Exception ex)
                {
                    _logger.LogError(ex, "Error occurred");
                    return null;
                }
            }
        }
    }
    ```

    <span data-ttu-id="ae071-143">这将实现 Web API `GET /calendar` () 可以从 Teams 选项卡调用。现在，它只是尝试交换 Graph 令牌的记名令牌。</span><span class="sxs-lookup"><span data-stu-id="ae071-143">This implements a Web API (`GET /calendar`) that can be called from the Teams tab. For now it simply tries to exchange the bearer token for a Graph token.</span></span> <span data-ttu-id="ae071-144">用户首次加载选项卡时，此操作将失败，因为他们尚未同意允许应用代表他们访问 Microsoft Graph。</span><span class="sxs-lookup"><span data-stu-id="ae071-144">The first time a user loads the tab, this will fail because they have not yet consented to allow the app access to Microsoft Graph on their behalf.</span></span>

1. <span data-ttu-id="ae071-145">打开 **./Pages/Index.cshtml，** 将 `successCallback` 函数替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="ae071-145">Open **./Pages/Index.cshtml** and replace the `successCallback` function with the following.</span></span>

    ```javascript
    successCallback: (token) => {
      // TEMPORARY: Call the Web API
      fetch('/calendar', {
        headers: {
          'Authorization': `Bearer ${token}`
        }
      }).then(response => {
        response.text()
          .then(body => {
            $('#tab-container').empty();
            $('<code/>', {
              text: body
            }).appendTo('#tab-container');
          });
      }).catch(error => {
        console.error(error);
        renderError(error);
      });
    }
    ```

    <span data-ttu-id="ae071-146">这将调用 Web API 并显示响应。</span><span class="sxs-lookup"><span data-stu-id="ae071-146">This will call the Web API and display the response.</span></span>

1. <span data-ttu-id="ae071-147">保存更改并重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="ae071-147">Save your changes and restart the app.</span></span> <span data-ttu-id="ae071-148">刷新 Microsoft Teams 中的选项卡。</span><span class="sxs-lookup"><span data-stu-id="ae071-148">Refresh the tab in Microsoft Teams.</span></span> <span data-ttu-id="ae071-149">页面应显示 `consent_required` 。</span><span class="sxs-lookup"><span data-stu-id="ae071-149">The page should display `consent_required`.</span></span>

1. <span data-ttu-id="ae071-150">查看 CLI 中的日志输出。</span><span class="sxs-lookup"><span data-stu-id="ae071-150">Review the log output in your CLI.</span></span> <span data-ttu-id="ae071-151">请注意两点。</span><span class="sxs-lookup"><span data-stu-id="ae071-151">Notice two things.</span></span>

    - <span data-ttu-id="ae071-152">类似这样的条目 `Authenticated user: MeganB@contoso.com` 。</span><span class="sxs-lookup"><span data-stu-id="ae071-152">An entry like `Authenticated user: MeganB@contoso.com`.</span></span> <span data-ttu-id="ae071-153">Web API 已基于随 API 请求发送的令牌对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="ae071-153">The Web API has authenticated the user based on the token sent with the API request.</span></span>
    - <span data-ttu-id="ae071-154">类似这样的条目 `AADSTS65001: The user or administrator has not consented to use the application with ID...` 。</span><span class="sxs-lookup"><span data-stu-id="ae071-154">An entry like `AADSTS65001: The user or administrator has not consented to use the application with ID...`.</span></span> <span data-ttu-id="ae071-155">这是预期操作，因为尚未提示用户同意请求的 Microsoft Graph 权限范围。</span><span class="sxs-lookup"><span data-stu-id="ae071-155">This is expected, since the user has not yet been prompted to consent for the requested Microsoft Graph permission scopes.</span></span>

## <a name="implement-consent-prompt"></a><span data-ttu-id="ae071-156">实现同意提示</span><span class="sxs-lookup"><span data-stu-id="ae071-156">Implement consent prompt</span></span>

<span data-ttu-id="ae071-157">由于 Web API 无法提示用户，因此 Teams 选项卡将需要实现提示。</span><span class="sxs-lookup"><span data-stu-id="ae071-157">Because the Web API cannot prompt the user, the Teams tab will need to implement a prompt.</span></span> <span data-ttu-id="ae071-158">这将只需要为每个用户执行一次。</span><span class="sxs-lookup"><span data-stu-id="ae071-158">This will only need to be done once for each user.</span></span> <span data-ttu-id="ae071-159">用户同意后，他们无需重新登录，除非他们明确撤销对您的应用程序的访问权限。</span><span class="sxs-lookup"><span data-stu-id="ae071-159">Once a user consents, they do not need to reconsent unless they explicitly revoke access to your application.</span></span>

1. <span data-ttu-id="ae071-160">在名为 Authenticate.cshtml.cs 的 **./Pages** **目录中** 创建新文件，并添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="ae071-160">Create a new file in the **./Pages** directory named **Authenticate.cshtml.cs** and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Pages/Authenticate.cshtml.cs" id="AuthenticateModelSnippet":::

1. <span data-ttu-id="ae071-161">在 **./Pages** 目录中新建一个名为 **Authenticate.cshtml** 的文件并添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="ae071-161">Create a new file in the **./Pages** directory named **Authenticate.cshtml** and add the following code.</span></span>

    :::code language="razor" source="../demo/GraphTutorial/Pages/Authenticate.cshtml":::

1. <span data-ttu-id="ae071-162">在 **./Pages** 目录中新建一个名为 **AuthComplete.cshtml** 的文件并添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="ae071-162">Create a new file in the **./Pages** directory named **AuthComplete.cshtml** and add the following code.</span></span>

    :::code language="razor" source="../demo/GraphTutorial/Pages/AuthComplete.cshtml":::

1. <span data-ttu-id="ae071-163">打开 **./Pages/Index.cshtml，** 在标记中添加以下 `<script>` 函数。</span><span class="sxs-lookup"><span data-stu-id="ae071-163">Open **./Pages/Index.cshtml** and add the following functions inside the `<script>` tag.</span></span>

    :::code language="javascript" source="../demo/GraphTutorial/Pages/Index.cshtml" id="LoadUserCalendarSnippet":::

1. <span data-ttu-id="ae071-164">在标记中添加以下函数以显示 Web `<script>` API 的成功结果。</span><span class="sxs-lookup"><span data-stu-id="ae071-164">Add the following function inside the `<script>` tag to display a successful result from the Web API.</span></span>

    ```javascript
    function renderCalendar(events) {
      $('#tab-container').empty();

      $('<pre/>').append($('<code/>', {
        text: JSON.stringify(events, null, 2),
        style: 'word-break: break-all;'
      })).appendTo('#tab-container');
    }
    ```

1. <span data-ttu-id="ae071-165">将现有 `successCallback` 代码替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="ae071-165">Replace the existing `successCallback` with the following code.</span></span>

    ```javascript
    successCallback: (token) => {
      loadUserCalendar(token, (events) => {
        renderCalendar(events);
      });
    }
    ```

1. <span data-ttu-id="ae071-166">保存更改并重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="ae071-166">Save your changes and restart the app.</span></span> <span data-ttu-id="ae071-167">刷新 Microsoft Teams 中的选项卡。</span><span class="sxs-lookup"><span data-stu-id="ae071-167">Refresh the tab in Microsoft Teams.</span></span> <span data-ttu-id="ae071-168">选项卡应显示 `{ "status": "OK" }` 。</span><span class="sxs-lookup"><span data-stu-id="ae071-168">The tab should display `{ "status": "OK" }`.</span></span>

1. <span data-ttu-id="ae071-169">查看日志输出。</span><span class="sxs-lookup"><span data-stu-id="ae071-169">Review the log output.</span></span> <span data-ttu-id="ae071-170">你应该会看到 `Access token for Graph` 该条目。</span><span class="sxs-lookup"><span data-stu-id="ae071-170">You should see the `Access token for Graph` entry.</span></span> <span data-ttu-id="ae071-171">如果分析该令牌，你会注意到它包含在appsettings.js **中配置的** Microsoft Graph 作用域。</span><span class="sxs-lookup"><span data-stu-id="ae071-171">If you parse that token, you'll notice that it contains the Microsoft Graph scopes configured in **appsettings.json**.</span></span>

## <a name="storing-and-refreshing-tokens"></a><span data-ttu-id="ae071-172">存储和刷新令牌</span><span class="sxs-lookup"><span data-stu-id="ae071-172">Storing and refreshing tokens</span></span>

<span data-ttu-id="ae071-173">此时，应用程序具有访问令牌，该令牌在 API 调用 `Authorization` 标头中发送。</span><span class="sxs-lookup"><span data-stu-id="ae071-173">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="ae071-174">这是允许应用代表用户访问 Microsoft Graph 的令牌。</span><span class="sxs-lookup"><span data-stu-id="ae071-174">This is the token that allows the app to access Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="ae071-175">但是，此令牌是短期的。</span><span class="sxs-lookup"><span data-stu-id="ae071-175">However, this token is short-lived.</span></span> <span data-ttu-id="ae071-176">令牌在颁发后一小时过期。</span><span class="sxs-lookup"><span data-stu-id="ae071-176">The token expires an hour after it is issued.</span></span> <span data-ttu-id="ae071-177">此时刷新令牌将变得有用。</span><span class="sxs-lookup"><span data-stu-id="ae071-177">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="ae071-178">刷新令牌允许应用请求新的访问令牌，而无需用户重新登录。</span><span class="sxs-lookup"><span data-stu-id="ae071-178">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="ae071-179">由于应用使用的是 Microsoft.Identity.Web 库，因此不需要实现任何令牌存储或刷新逻辑。</span><span class="sxs-lookup"><span data-stu-id="ae071-179">Because the app is using the Microsoft.Identity.Web library, you do not have to implement any token storage or refresh logic.</span></span>

<span data-ttu-id="ae071-180">应用使用内存中令牌缓存，这足以满足应用重启时无需保留令牌的应用。</span><span class="sxs-lookup"><span data-stu-id="ae071-180">The app uses the in-memory token cache, which is sufficient for apps that do not need to persist tokens when the app restarts.</span></span> <span data-ttu-id="ae071-181">生产应用可能会改为使用 Microsoft.Identity.Web [库中](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization) 的分布式缓存选项。</span><span class="sxs-lookup"><span data-stu-id="ae071-181">Production apps may instead use the [distributed cache options](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization) in the Microsoft.Identity.Web library.</span></span>

<span data-ttu-id="ae071-182">`GetAccessTokenForUserAsync`此方法会处理令牌到期并刷新。</span><span class="sxs-lookup"><span data-stu-id="ae071-182">The `GetAccessTokenForUserAsync` method handles token expiration and refresh for you.</span></span> <span data-ttu-id="ae071-183">它首先检查缓存的令牌，如果它未过期，它将返回它。</span><span class="sxs-lookup"><span data-stu-id="ae071-183">It first checks the cached token, and if it is not expired, it returns it.</span></span> <span data-ttu-id="ae071-184">如果已过期，它将使用缓存的刷新令牌获取新的刷新令牌。</span><span class="sxs-lookup"><span data-stu-id="ae071-184">If it is expired, it uses the cached refresh token to obtain a new one.</span></span>

<span data-ttu-id="ae071-185">控制器通过依赖关系注入获取的 **GraphServiceClient** 已使用用于你的身份验证提供程序 `GetAccessTokenForUserAsync` 进行预配置。</span><span class="sxs-lookup"><span data-stu-id="ae071-185">The **GraphServiceClient** that controllers get via dependency injection is pre-configured with an authentication provider that uses `GetAccessTokenForUserAsync` for you.</span></span>
