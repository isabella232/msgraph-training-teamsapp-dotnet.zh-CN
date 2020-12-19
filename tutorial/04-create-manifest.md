<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="b6c5c-101">应用清单介绍了应用如何与 Microsoft Teams 集成，并且安装应用是必需的。</span><span class="sxs-lookup"><span data-stu-id="b6c5c-101">The app manifest describes how the app integrates with Microsoft Teams and is required to install apps.</span></span> <span data-ttu-id="b6c5c-102">在此部分中，你将使用 Microsoft Teams 客户端中的 App Studio 生成清单。</span><span class="sxs-lookup"><span data-stu-id="b6c5c-102">In this section you'll use App Studio in the Microsoft Teams client to generate a manifest.</span></span>

1. <span data-ttu-id="b6c5c-103">如果你尚未在 Teams 中安装 App Studio， [则立即安装](/microsoftteams/platform/concepts/build-and-test/app-studio-overview)它。</span><span class="sxs-lookup"><span data-stu-id="b6c5c-103">If you do not already have App Studio installed in Teams, [install it now](/microsoftteams/platform/concepts/build-and-test/app-studio-overview).</span></span>

1. <span data-ttu-id="b6c5c-104">在 Microsoft Teams 中启动 App Studio，然后选择 **清单编辑器**。</span><span class="sxs-lookup"><span data-stu-id="b6c5c-104">Launch App Studio in Microsoft Teams and select the **Manifest editor**.</span></span>

1. <span data-ttu-id="b6c5c-105">选择 **"新建应用"。**</span><span class="sxs-lookup"><span data-stu-id="b6c5c-105">Select **Create a new app**.</span></span>

    ![Microsoft Teams 中 App Studio 中清单编辑器的屏幕截图](images/app-studio-01.png)

1. <span data-ttu-id="b6c5c-107">在 **"应用详细信息** "页上，填写必填字段。</span><span class="sxs-lookup"><span data-stu-id="b6c5c-107">On the **App details** page, fill in the required fields.</span></span>

    > [!NOTE]
    > <span data-ttu-id="b6c5c-108">可以使用"品牌"部分的默认 **图标** 或上传你自己的图标。</span><span class="sxs-lookup"><span data-stu-id="b6c5c-108">You can use the default icons in the **Branding** section or upload your own.</span></span>

1. <span data-ttu-id="b6c5c-109">在左侧菜单上，选择 **"功能"** 下的 **"选项卡"。**</span><span class="sxs-lookup"><span data-stu-id="b6c5c-109">On the left-hand menu, select **Tabs** under **Capabilities**.</span></span>

1. <span data-ttu-id="b6c5c-110">选择 **"添加\*\*\*\*个人"选项卡下的"添加"。**</span><span class="sxs-lookup"><span data-stu-id="b6c5c-110">Select **Add** under **Add a personal tab**.</span></span>

    ![App Studio 中"选项卡"页面的屏幕截图](images/app-studio-02.png)

1. <span data-ttu-id="b6c5c-112">按如下方式填写字段，其中是上一节中复制的 `YOUR_NGROK_URL` 转发 URL。</span><span class="sxs-lookup"><span data-stu-id="b6c5c-112">Fill in the fields as follows, where `YOUR_NGROK_URL` is the forwarding URL you copied in the previous section.</span></span> <span data-ttu-id="b6c5c-113">选择 **"完成后** 保存"。</span><span class="sxs-lookup"><span data-stu-id="b6c5c-113">Select **Save** when done.</span></span>

    - <span data-ttu-id="b6c5c-114">**名称：**`Create event`</span><span class="sxs-lookup"><span data-stu-id="b6c5c-114">**Name:** `Create event`</span></span>
    - <span data-ttu-id="b6c5c-115">**实体 ID：**`createEventTab`</span><span class="sxs-lookup"><span data-stu-id="b6c5c-115">**Entity ID:** `createEventTab`</span></span>
    - <span data-ttu-id="b6c5c-116">**内容 URL：**`YOUR_NGROK_URL/newevent`</span><span class="sxs-lookup"><span data-stu-id="b6c5c-116">**Content URL:** `YOUR_NGROK_URL/newevent`</span></span>

1. <span data-ttu-id="b6c5c-117">选择 **"添加\*\*\*\*个人"选项卡下的"添加"。**</span><span class="sxs-lookup"><span data-stu-id="b6c5c-117">Select **Add** under **Add a personal tab**.</span></span>

1. <span data-ttu-id="b6c5c-118">按如下方式填写字段，其中是上一节中复制的 `YOUR_NGROK_URL` 转发 URL。</span><span class="sxs-lookup"><span data-stu-id="b6c5c-118">Fill in the fields as follows, where `YOUR_NGROK_URL` is the forwarding URL you copied in the previous section.</span></span> <span data-ttu-id="b6c5c-119">选择 **"完成后** 保存"。</span><span class="sxs-lookup"><span data-stu-id="b6c5c-119">Select **Save** when done.</span></span>

    - <span data-ttu-id="b6c5c-120">**名称：**`Graph calendar`</span><span class="sxs-lookup"><span data-stu-id="b6c5c-120">**Name:** `Graph calendar`</span></span>
    - <span data-ttu-id="b6c5c-121">**实体 ID：**`calendarTab`</span><span class="sxs-lookup"><span data-stu-id="b6c5c-121">**Entity ID:** `calendarTab`</span></span>
    - <span data-ttu-id="b6c5c-122">**内容 URL：**`YOUR_NGROK_URL`</span><span class="sxs-lookup"><span data-stu-id="b6c5c-122">**Content URL:** `YOUR_NGROK_URL`</span></span>

1. <span data-ttu-id="b6c5c-123">在左侧菜单上，选择"完成 **"下的"域和\*\*\*\*权限"。**</span><span class="sxs-lookup"><span data-stu-id="b6c5c-123">On the left-hand menu, select **Domains and permissions** under **Finish**.</span></span>

1. <span data-ttu-id="b6c5c-124">通过 **应用注册将 AAD** 应用 ID 设置为应用程序 ID。</span><span class="sxs-lookup"><span data-stu-id="b6c5c-124">Set the **AAD App ID** to the application ID from your app registration.</span></span>

1. <span data-ttu-id="b6c5c-125">通过 **应用注册将** 单一登录字段设置为应用程序 ID URI。</span><span class="sxs-lookup"><span data-stu-id="b6c5c-125">Set the **Single-Sign-On** field to the application ID URI from your app registration.</span></span>

1. <span data-ttu-id="b6c5c-126">在左侧菜单上，选择"测试 **"，然后分发到"完成\*\*\*\*"下**。</span><span class="sxs-lookup"><span data-stu-id="b6c5c-126">On the left-hand menu, select **Test and distribute** under **Finish**.</span></span> <span data-ttu-id="b6c5c-127">选择 **"下载"。**</span><span class="sxs-lookup"><span data-stu-id="b6c5c-127">Select **Download**.</span></span>

1. <span data-ttu-id="b6c5c-128">在名为"清单"的项目的根目录中创建新 **目录**。</span><span class="sxs-lookup"><span data-stu-id="b6c5c-128">Create a new directory in the root of the project named **Manifest**.</span></span> <span data-ttu-id="b6c5c-129">将下载的 ZIP 文件的内容提取到此目录。</span><span class="sxs-lookup"><span data-stu-id="b6c5c-129">Extract the contents of the downloaded ZIP file to this directory.</span></span>
