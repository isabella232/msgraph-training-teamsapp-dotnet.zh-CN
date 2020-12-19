<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="507a4-101">本教程指导你如何使用 ASP.NET Core 和 Microsoft Graph API 生成 Microsoft Teams 应用，以检索用户的日历信息。</span><span class="sxs-lookup"><span data-stu-id="507a4-101">This tutorial teaches you how to build a Microsoft Teams app using ASP.NET Core and the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="507a4-102">如果你更喜欢仅下载已完成的教程，可以下载或克隆 [GitHub 存储库](https://github.com/microsoftgraph/msgraph-training-teamsapp-dotnet)。</span><span class="sxs-lookup"><span data-stu-id="507a4-102">If you prefer to just download the completed tutorial, you can download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-teamsapp-dotnet).</span></span> <span data-ttu-id="507a4-103">有关使用应用 ID 和密码配置应用的说明，请参阅演示文件夹中的自述文件。</span><span class="sxs-lookup"><span data-stu-id="507a4-103">See the README file in the **demo** folder for instructions on configuring the app with an app ID and secret.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="507a4-104">先决条件</span><span class="sxs-lookup"><span data-stu-id="507a4-104">Prerequisites</span></span>

<span data-ttu-id="507a4-105">在开始本教程之前，应在开发计算机上安装以下内容。</span><span class="sxs-lookup"><span data-stu-id="507a4-105">Before you start this tutorial, you should have the following installed on your development machine.</span></span>

- <span data-ttu-id="507a4-106">[.NET Core SDK](https://dotnet.microsoft.com/download)。</span><span class="sxs-lookup"><span data-stu-id="507a4-106">[.NET Core SDK](https://dotnet.microsoft.com/download).</span></span>
- [<span data-ttu-id="507a4-107">ngrok</span><span class="sxs-lookup"><span data-stu-id="507a4-107">ngrok</span></span>](https://ngrok.com/)

<span data-ttu-id="507a4-108">你还应该拥有具有邮箱的个人 Microsoft 帐户Outlook.com或 Microsoft 工作或学校帐户。</span><span class="sxs-lookup"><span data-stu-id="507a4-108">You should also have either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span> <span data-ttu-id="507a4-109">如果你没有 Microsoft 帐户，有几个选项可以获取免费帐户：</span><span class="sxs-lookup"><span data-stu-id="507a4-109">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="507a4-110">你可以 [注册新的个人 Microsoft 帐户](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)。</span><span class="sxs-lookup"><span data-stu-id="507a4-110">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="507a4-111">你可以 [注册 Office 365 开发人员计划](https://developer.microsoft.com/office/dev-program) ，获取免费的 Office 365 订阅。</span><span class="sxs-lookup"><span data-stu-id="507a4-111">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

> [!NOTE]
> <span data-ttu-id="507a4-112">本教程是使用 .NET Core SDK 版本 3.1.402 编写的。</span><span class="sxs-lookup"><span data-stu-id="507a4-112">This tutorial was written with .NET Core SDK version 3.1.402.</span></span> <span data-ttu-id="507a4-113">本指南中的步骤可能与其他版本一起运行，但尚未经过测试。</span><span class="sxs-lookup"><span data-stu-id="507a4-113">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="feedback"></a><span data-ttu-id="507a4-114">反馈</span><span class="sxs-lookup"><span data-stu-id="507a4-114">Feedback</span></span>

<span data-ttu-id="507a4-115">请在 GitHub 存储库中提供有关本教程 [的任何反馈](https://github.com/microsoftgraph/msgraph-training-teamsapp-dotnet)。</span><span class="sxs-lookup"><span data-stu-id="507a4-115">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-teamsapp-dotnet).</span></span>
