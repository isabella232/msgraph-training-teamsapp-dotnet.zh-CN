<!-- markdownlint-disable MD002 MD041 -->

在此练习中，你将扩展上一练习中的应用程序，以支持使用 Azure AD 的单一登录身份验证。 这是获取调用 Microsoft Graph API 所需的 OAuth 访问令牌所必需的。 在此步骤中，您将配置 [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) 库。

> [!IMPORTANT]
> 若要避免在源中存储应用程序 ID 和密码，将使用 [.NET 密码管理器](/aspnet/core/security/app-secrets) 存储这些值。 密码管理器仅供开发使用，生产应用应使用受信任的密码管理器来存储密码。

1. 打开 **./appsettings.js，** 并将其内容替换为以下内容。

    :::code language="json" source="../demo/GraphTutorial/appsettings.example.json" highlight="2-8":::

1. 在 **GraphTu一l.csproj** 所在的目录中打开 CLI，然后运行以下命令，使用 Azure 门户中的应用程序 ID 和应用程序密码进行代用。 `YOUR_APP_ID` `YOUR_APP_SECRET`

    ```Shell
    dotnet user-secrets init
    dotnet user-secrets set "AzureAd:ClientId" "YOUR_APP_ID"
    dotnet user-secrets set "AzureAd:ClientSecret" "YOUR_APP_SECRET"
    ```

## <a name="implement-sign-in"></a>实现登录

首先，在应用的 JavaScript 代码中实现单一登录。 你将使用 [Microsoft Teams JavaScript SDK](/javascript/api/overview/msteams-client) 获取访问令牌，该令牌允许在 Teams 客户端中运行的 JavaScript 代码对将稍后实现的 Web API 进行 AJAX 调用。

1. 打开 **./Pages/Index.cshtml，** 在标记中添加以下 `<script>` 代码。

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

    这将调用 `microsoftTeams.authentication.getAuthToken` 以登录 Teams 的用户身份以静默方式进行身份验证。 通常不会涉及到任何 UI 提示，除非用户必须同意。 然后，代码在选项卡中显示令牌。

1. 在 CLI 中运行以下命令，保存更改并启动应用程序。

    ```Shell
    dotnet run
    ```

    > [!IMPORTANT]
    > 如果已重新启动 ngrok 且 ngrok URL 已更改，请务必在测试之前在下列位置 **更新** ngrok 值。
    >
    > - 应用注册中的重定向 URI
    > - 应用注册中的应用程序 ID URI
    > - `contentUrl` in manifest.json
    > - `validDomains` in manifest.json
    > - `resource` in manifest.json

1. 创建 ZIP 文件 **，manifest.js、color.png****和** outline.png。 ****

1. 在 Microsoft Teams中，选择左侧栏中的应用，选择"上传自定义应用"，然后选择"为我或 **我的团队上载"。**

    ![Microsoft Teams 中"上传自定义应用"链接的屏幕截图](images/upload-custom-app.png)

1. 浏览到之前创建的 ZIP 文件，然后选择"**打开"。**

1. 查看应用程序信息，然后选择"添加 **"。**

1. 应用程序将在 Teams 中打开并显示访问令牌。

如果复制令牌，可以将其粘贴到[jwt.ms。](https://jwt.ms) 确认声明 (访问群体) 应用程序 ID，并且声明 (范围) 是创建的 `aud` `scp` API `access_as_user` 范围。 这意味着此令牌不会授予对 Microsoft Graph 的直接访问权！ 相反，您即将实现的 Web API 将需要使用代表流交换此[](/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)令牌，以获得将用于 Microsoft Graph 调用的令牌。

## <a name="configure-authentication-in-the-aspnet-core-app"></a>在核心应用中ASP.NET身份验证

首先，将 Microsoft Identity 平台服务添加到应用程序。

1. 打开 **./Startup.cs** 文件，将以下 `using` 语句添加到文件顶部。

    ```csharp
    using Microsoft.Identity.Web;
    ```

1. 在函数中的行之前 `app.UseAuthorization();` 添加以下 `Configure` 行。

    ```csharp
    app.UseAuthentication();
    ```

1. 在函数中的行后 `endpoints.MapRazorPages();` 添加以下 `Configure` 行。

    ```csharp
    endpoints.MapControllers();
    ```

1. 将现有的 `ConfigureServices` 函数替换为以下内容。

    :::code language="csharp" source="../demo/GraphTutorial/Startup.cs" id="ConfigureServicesSnippet":::

    此代码将应用程序配置为允许对 Web API 的调用基于标头中的 JWT 承载令牌 `Authorization` 进行身份验证。 它还添加了令牌获取服务，这些服务可以通过代表流交换该令牌。

## <a name="create-the-web-api-controller"></a>创建 Web API 控制器

1. 在名为 Controllers 的项目的根目录中创建新 **目录**。

1. 在名为 CalendarController.cs 的 **./Controllers** **目录中** 创建新文件，并添加以下代码。

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

    这将实现 Web API `GET /calendar` () 可以从 Teams 选项卡调用。现在，它只是尝试交换 Graph 令牌的记名令牌。 用户首次加载选项卡时，此操作将失败，因为他们尚未同意允许应用代表他们访问 Microsoft Graph。

1. 打开 **./Pages/Index.cshtml，** 将 `successCallback` 函数替换为以下内容。

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

    这将调用 Web API 并显示响应。

1. 保存更改并重新启动该应用。 刷新 Microsoft Teams 中的选项卡。 页面应显示 `consent_required` 。

1. 查看 CLI 中的日志输出。 请注意两点。

    - 类似这样的条目 `Authenticated user: MeganB@contoso.com` 。 Web API 已基于随 API 请求发送的令牌对用户进行身份验证。
    - 类似这样的条目 `AADSTS65001: The user or administrator has not consented to use the application with ID...` 。 这是预期操作，因为尚未提示用户同意请求的 Microsoft Graph 权限范围。

## <a name="implement-consent-prompt"></a>实现同意提示

由于 Web API 无法提示用户，因此 Teams 选项卡将需要实现提示。 这将只需要为每个用户执行一次。 用户同意后，他们无需重新登录，除非他们明确撤销对您的应用程序的访问权限。

1. 在名为 Authenticate.cshtml.cs 的 **./Pages** **目录中** 创建新文件，并添加以下代码。

    :::code language="csharp" source="../demo/GraphTutorial/Pages/Authenticate.cshtml.cs" id="AuthenticateModelSnippet":::

1. 在 **./Pages** 目录中新建一个名为 **Authenticate.cshtml** 的文件并添加以下代码。

    :::code language="razor" source="../demo/GraphTutorial/Pages/Authenticate.cshtml":::

1. 在 **./Pages** 目录中新建一个名为 **AuthComplete.cshtml** 的文件并添加以下代码。

    :::code language="razor" source="../demo/GraphTutorial/Pages/AuthComplete.cshtml":::

1. 打开 **./Pages/Index.cshtml，** 在标记中添加以下 `<script>` 函数。

    :::code language="javascript" source="../demo/GraphTutorial/Pages/Index.cshtml" id="LoadUserCalendarSnippet":::

1. 在标记中添加以下函数以显示 Web `<script>` API 的成功结果。

    ```javascript
    function renderCalendar(events) {
      $('#tab-container').empty();

      $('<pre/>').append($('<code/>', {
        text: JSON.stringify(events, null, 2),
        style: 'word-break: break-all;'
      })).appendTo('#tab-container');
    }
    ```

1. 将现有 `successCallback` 代码替换为以下代码。

    ```javascript
    successCallback: (token) => {
      loadUserCalendar(token, (events) => {
        renderCalendar(events);
      });
    }
    ```

1. 保存更改并重新启动该应用。 刷新 Microsoft Teams 中的选项卡。 选项卡应显示 `{ "status": "OK" }` 。

1. 查看日志输出。 你应该会看到 `Access token for Graph` 该条目。 如果分析该令牌，你会注意到它包含在appsettings.js **中配置的** Microsoft Graph 作用域。

## <a name="storing-and-refreshing-tokens"></a>存储和刷新令牌

此时，应用程序具有访问令牌，该令牌在 API 调用 `Authorization` 标头中发送。 这是允许应用代表用户访问 Microsoft Graph 的令牌。

但是，此令牌是短期的。 令牌在颁发后一小时过期。 此时刷新令牌将变得有用。 刷新令牌允许应用请求新的访问令牌，而无需用户重新登录。

由于应用使用的是 Microsoft.Identity.Web 库，因此不需要实现任何令牌存储或刷新逻辑。

应用使用内存中令牌缓存，这足以满足应用重启时无需保留令牌的应用。 生产应用可能会改为使用 Microsoft.Identity.Web [库中](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization) 的分布式缓存选项。

`GetAccessTokenForUserAsync`此方法会处理令牌到期并刷新。 它首先检查缓存的令牌，如果它未过期，它将返回它。 如果已过期，它将使用缓存的刷新令牌获取新的刷新令牌。

控制器通过依赖关系注入获取的 **GraphServiceClient** 已使用用于你的身份验证提供程序 `GetAccessTokenForUserAsync` 进行预配置。
