<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="53246-101">在此练习中，你将使用 Azure Active Directory 管理中心创建新的 Azure AD Web 应用程序注册。</span><span class="sxs-lookup"><span data-stu-id="53246-101">In this exercise, you will create a new Azure AD web application registration using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="53246-102">打开浏览器，并转到 [Azure Active Directory 管理中心](https://aad.portal.azure.com)。</span><span class="sxs-lookup"><span data-stu-id="53246-102">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="53246-103">使用 **个人帐户**（亦称为“Microsoft 帐户”）或 **工作或学校帐户** 登录。</span><span class="sxs-lookup"><span data-stu-id="53246-103">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="53246-104">选择左侧导航栏中的“**Azure Active Directory**”，再选择“**管理**”下的“**应用注册**”。</span><span class="sxs-lookup"><span data-stu-id="53246-104">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="53246-105">应用注册的屏幕截图</span><span class="sxs-lookup"><span data-stu-id="53246-105">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

1. <span data-ttu-id="53246-106">选择“新注册”。</span><span class="sxs-lookup"><span data-stu-id="53246-106">Select **New registration**.</span></span> <span data-ttu-id="53246-107">在 **"注册应用程序"** 页上，按如下所示设置值，其中是上一节中复制的 `YOUR_NGROK_URL` ngrok 转发 URL。</span><span class="sxs-lookup"><span data-stu-id="53246-107">On the **Register an application** page, set the values as follows, where `YOUR_NGROK_URL` is the ngrok forwarding URL you copied in the previous section.</span></span>

    - <span data-ttu-id="53246-108">将“名称”设置为“`Teams Graph Tutorial`”。</span><span class="sxs-lookup"><span data-stu-id="53246-108">Set **Name** to `Teams Graph Tutorial`.</span></span>
    - <span data-ttu-id="53246-109">将“受支持的帐户类型”设置为“任何组织目录中的帐户和个人 Microsoft 帐户”。</span><span class="sxs-lookup"><span data-stu-id="53246-109">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="53246-110">在“重定向 URI”下，将第一个下拉列表设置为“`Web`”，并将值设置为“`YOUR_NGROK_URL/authcomplete`”。</span><span class="sxs-lookup"><span data-stu-id="53246-110">Under **Redirect URI**, set the first drop-down to `Web` and set the value to `YOUR_NGROK_URL/authcomplete`.</span></span>

    !["注册应用程序"页的屏幕截图](./images/aad-register-an-app.png)

1. <span data-ttu-id="53246-112">选择“**注册**”。</span><span class="sxs-lookup"><span data-stu-id="53246-112">Select **Register**.</span></span> <span data-ttu-id="53246-113">在 **"Teams Graph** 教程"页上，复制应用程序 (客户端) **ID** 并保存它，下一步中将需要该值。</span><span class="sxs-lookup"><span data-stu-id="53246-113">On the **Teams Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![新应用注册的应用程序 ID 屏幕截图](./images/aad-application-id.png)

1. <span data-ttu-id="53246-115">选择“管理”下的“身份验证”。</span><span class="sxs-lookup"><span data-stu-id="53246-115">Select **Authentication** under **Manage**.</span></span> <span data-ttu-id="53246-116">找到 **隐式授予** 部分并启用 **访问令牌\*\*\*\*和 ID 令牌**。</span><span class="sxs-lookup"><span data-stu-id="53246-116">Locate the **Implicit grant** section and enable **Access tokens** and **ID tokens**.</span></span> <span data-ttu-id="53246-117">选择“**保存**”。</span><span class="sxs-lookup"><span data-stu-id="53246-117">Select **Save**.</span></span>

1. <span data-ttu-id="53246-118">选择“管理”下的“证书和密码”。</span><span class="sxs-lookup"><span data-stu-id="53246-118">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="53246-119">选择“新客户端密码”按钮。</span><span class="sxs-lookup"><span data-stu-id="53246-119">Select the **New client secret** button.</span></span> <span data-ttu-id="53246-120">在 Description 中 **输入** 值，然后选择"到期"选项之 **一，然后选择**"**添加"。**</span><span class="sxs-lookup"><span data-stu-id="53246-120">Enter a value in **Description** and select one of the options for **Expires** and select **Add**.</span></span>

1. <span data-ttu-id="53246-121">离开此页前，先复制客户端密码值。</span><span class="sxs-lookup"><span data-stu-id="53246-121">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="53246-122">将在下一步中用到它。</span><span class="sxs-lookup"><span data-stu-id="53246-122">You will need it in the next step.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="53246-123">此客户端密码不会再次显示，所以请务必现在就复制它。</span><span class="sxs-lookup"><span data-stu-id="53246-123">This client secret is never shown again, so make sure you copy it now.</span></span>

1. <span data-ttu-id="53246-124">在 **"管理"下\*\*\*\*选择** API 权限，然后选择 **"添加权限"。**</span><span class="sxs-lookup"><span data-stu-id="53246-124">Select **API permissions** under **Manage**, then select **Add a permission**.</span></span>

1. <span data-ttu-id="53246-125">选择 **Microsoft Graph，** 然后选择 **委派权限**。</span><span class="sxs-lookup"><span data-stu-id="53246-125">Select **Microsoft Graph**, then **Delegated permissions**.</span></span>

1. <span data-ttu-id="53246-126">选择以下权限，然后选择"**添加权限"。**</span><span class="sxs-lookup"><span data-stu-id="53246-126">Select the following permissions, then select **Add permissions**.</span></span>

    - <span data-ttu-id="53246-127">**Calendars.ReadWrite** - 这将允许应用读取和写入用户的日历。</span><span class="sxs-lookup"><span data-stu-id="53246-127">**Calendars.ReadWrite** - this will allow the app to read and write to the user's calendar.</span></span>
    - <span data-ttu-id="53246-128">**MailboxSettings.Read** - 这将允许应用从用户的邮箱设置获取用户的时区、日期格式和时间格式。</span><span class="sxs-lookup"><span data-stu-id="53246-128">**MailboxSettings.Read** - this will allow the app to get the user's time zone, date format, and time format from their mailbox settings.</span></span>

    ![已配置权限的屏幕截图](images/aad-configured-permissions.png)

## <a name="configure-teams-single-sign-on"></a><span data-ttu-id="53246-130">配置 Teams 单一登录</span><span class="sxs-lookup"><span data-stu-id="53246-130">Configure Teams single sign-on</span></span>

<span data-ttu-id="53246-131">在此部分中，你将更新应用注册以支持 Teams [中的单一登录](/microsoftteams/platform/tabs/how-to/authentication/auth-aad-sso)。</span><span class="sxs-lookup"><span data-stu-id="53246-131">In this section you'll update the app registration to support [single sign-on in Teams](/microsoftteams/platform/tabs/how-to/authentication/auth-aad-sso).</span></span>

1. <span data-ttu-id="53246-132">选择 **"公开 API"。**</span><span class="sxs-lookup"><span data-stu-id="53246-132">Select **Expose an API**.</span></span> <span data-ttu-id="53246-133">选择 **应用程序** **ID URI 旁边的"设置"链接**。</span><span class="sxs-lookup"><span data-stu-id="53246-133">Select the **Set** link next to **Application ID URI**.</span></span> <span data-ttu-id="53246-134">插入您的 ngrok forwarding URL 域名， (双正斜杠和 GUID 之间的末尾) 带正斜杠"/"。</span><span class="sxs-lookup"><span data-stu-id="53246-134">Insert your ngrok forwarding URL domain name (with a forward slash "/" appended to the end) between the double forward slashes and the GUID.</span></span> <span data-ttu-id="53246-135">整个 ID 应类似于： `api://50153897dd4d.ngrok.io/ae7d8088-3422-4c8c-a351-6ded0f21d615` 。</span><span class="sxs-lookup"><span data-stu-id="53246-135">The entire ID should look similar to: `api://50153897dd4d.ngrok.io/ae7d8088-3422-4c8c-a351-6ded0f21d615`.</span></span>

1. <span data-ttu-id="53246-136">在此 **API 定义的"范围**"部分，选择 **"添加范围"。**</span><span class="sxs-lookup"><span data-stu-id="53246-136">In the **Scopes defined by this API** section, select **Add a scope**.</span></span> <span data-ttu-id="53246-137">按如下所示填写字段，然后选择"**添加范围"。**</span><span class="sxs-lookup"><span data-stu-id="53246-137">Fill in the fields as follows and select **Add scope**.</span></span>

    - <span data-ttu-id="53246-138">**范围名称：**`access_as_user`</span><span class="sxs-lookup"><span data-stu-id="53246-138">**Scope name:** `access_as_user`</span></span>
    - <span data-ttu-id="53246-139">**谁可以同意？：管理员和用户**</span><span class="sxs-lookup"><span data-stu-id="53246-139">**Who can consent?: Admins and users**</span></span>
    - <span data-ttu-id="53246-140">**管理员同意显示名称：**`Access the app as the user`</span><span class="sxs-lookup"><span data-stu-id="53246-140">**Admin consent display name:** `Access the app as the user`</span></span>
    - <span data-ttu-id="53246-141">**管理员同意说明：**`Allows Teams to call the app's web APIs as the current user.`</span><span class="sxs-lookup"><span data-stu-id="53246-141">**Admin consent description:** `Allows Teams to call the app's web APIs as the current user.`</span></span>
    - <span data-ttu-id="53246-142">**用户同意显示名称：**`Access the app as you`</span><span class="sxs-lookup"><span data-stu-id="53246-142">**User consent display name:** `Access the app as you`</span></span>
    - <span data-ttu-id="53246-143">**用户同意说明：**`Allows Teams to call the app's web APIs as you.`</span><span class="sxs-lookup"><span data-stu-id="53246-143">**User consent description:** `Allows Teams to call the app's web APIs as you.`</span></span>
    - <span data-ttu-id="53246-144">**状态：已启用**</span><span class="sxs-lookup"><span data-stu-id="53246-144">**State: Enabled**</span></span>

    !["添加范围"表单的屏幕截图](images/aad-add-scope.png)

1. <span data-ttu-id="53246-146">在"**授权客户端应用程序**"部分，选择 **"添加客户端应用程序"。**</span><span class="sxs-lookup"><span data-stu-id="53246-146">In the **Authorized client applications** section, select **Add a client application**.</span></span> <span data-ttu-id="53246-147">输入以下列表中的客户端 ID，在"授权范围"下启用范围，然后选择 **"添加应用程序"。**</span><span class="sxs-lookup"><span data-stu-id="53246-147">Enter a client ID from the following list, enable the scope under **Authorized scopes**, and select **Add application**.</span></span> <span data-ttu-id="53246-148">对列表中的每个客户端 ID 重复此过程。</span><span class="sxs-lookup"><span data-stu-id="53246-148">Repeat this process for each of the client IDs in the list.</span></span>

    - <span data-ttu-id="53246-149">`1fec8e78-bce4-4aaf-ab1b-5451cc387264` (Teams 移动/桌面应用程序) </span><span class="sxs-lookup"><span data-stu-id="53246-149">`1fec8e78-bce4-4aaf-ab1b-5451cc387264` (Teams mobile/desktop application)</span></span>
    - <span data-ttu-id="53246-150">`5e3ce6c0-2b1f-4285-8d4b-75ee78787346` (Teams Web 应用程序) </span><span class="sxs-lookup"><span data-stu-id="53246-150">`5e3ce6c0-2b1f-4285-8d4b-75ee78787346` (Teams web application)</span></span>
