<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="e8aeb-101">在此部分中，您将添加在用户日历上创建事件的能力。</span><span class="sxs-lookup"><span data-stu-id="e8aeb-101">In this section you will add the ability to create events on the user's calendar.</span></span>

## <a name="create-the-new-event-tab"></a><span data-ttu-id="e8aeb-102">新建事件选项卡</span><span class="sxs-lookup"><span data-stu-id="e8aeb-102">Create the new event tab</span></span>

1. <span data-ttu-id="e8aeb-103">在 **./Pages** 目录中新建一个名为 **NewEvent.cshtml** 的文件并添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="e8aeb-103">Create a new file in the **./Pages** directory named **NewEvent.cshtml** and add the following code.</span></span>

    :::code language="razor" source="../demo/GraphTutorial/Pages/NewEvent.cshtml":::

    <span data-ttu-id="e8aeb-104">这将实现一个简单的表单，并添加 JavaScript 以将表单数据张贴到 Web API。</span><span class="sxs-lookup"><span data-stu-id="e8aeb-104">This implements a simple form, and adds JavaScript to post the form data to the Web API.</span></span>

## <a name="implement-the-web-api"></a><span data-ttu-id="e8aeb-105">实现 Web API</span><span class="sxs-lookup"><span data-stu-id="e8aeb-105">Implement the Web API</span></span>

1. <span data-ttu-id="e8aeb-106">在项目的根目录中新建一个名为 **Models** 的目录。</span><span class="sxs-lookup"><span data-stu-id="e8aeb-106">Create a new directory named **Models** in the root of the project.</span></span>

1. <span data-ttu-id="e8aeb-107">在名为 NewEvent.cs 的 **./Models** **目录中** 创建新文件，并添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="e8aeb-107">Create a new file in the **./Models** directory named **NewEvent.cs** and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Models/NewEvent.cs" id="NewEventSnippet":::

1. <span data-ttu-id="e8aeb-108">打开 **./Controllers/CalendarController.cs，** 在文件顶部 `using` 添加以下语句。</span><span class="sxs-lookup"><span data-stu-id="e8aeb-108">Open **./Controllers/CalendarController.cs** and add the following `using` statement at the top of the file.</span></span>

    ```csharp
    using GraphTutorial.Models;
    ```

1. <span data-ttu-id="e8aeb-109">将以下函数添加到 **CalendarController** 类。</span><span class="sxs-lookup"><span data-stu-id="e8aeb-109">Add the following function to the **CalendarController** class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="PostSnippet":::

    <span data-ttu-id="e8aeb-110">这允许对包含表单字段的 Web API 进行 HTTP POST。</span><span class="sxs-lookup"><span data-stu-id="e8aeb-110">This allows an HTTP POST to the Web API with the fields of the form.</span></span>

1. <span data-ttu-id="e8aeb-111">保存所有更改并重新启动应用程序。</span><span class="sxs-lookup"><span data-stu-id="e8aeb-111">Save all of your changes and restart the application.</span></span> <span data-ttu-id="e8aeb-112">在 Microsoft Teams 中刷新应用，然后选择"创建 **事件"** 选项卡。填写表单，然后选择" **创建** "将事件添加到用户的日历。</span><span class="sxs-lookup"><span data-stu-id="e8aeb-112">Refresh the app in Microsoft Teams, and select the **Create event** tab. Fill out the form and select **Create** to add an event to the user's calendar.</span></span>

    !["创建事件"选项卡的屏幕截图](images/create-event.png)
