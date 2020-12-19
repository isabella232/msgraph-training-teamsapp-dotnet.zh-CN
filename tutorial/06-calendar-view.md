<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="badbf-101">在此部分中，你将 Microsoft Graph 合并到应用程序中。</span><span class="sxs-lookup"><span data-stu-id="badbf-101">In this section you will incorporate Microsoft Graph into the application.</span></span> <span data-ttu-id="badbf-102">对于此应用程序，你将使用 [适用于 .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) 的 Microsoft Graph 客户端库调用 Microsoft Graph。</span><span class="sxs-lookup"><span data-stu-id="badbf-102">For this application, you will use the [Microsoft Graph Client Library for .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

# <a name="get-a-calendar-view"></a><span data-ttu-id="badbf-103">获取日历视图</span><span class="sxs-lookup"><span data-stu-id="badbf-103">Get a calendar view</span></span>

<span data-ttu-id="badbf-104">日历视图是用户日历中两个时间点之间发生的一组事件。</span><span class="sxs-lookup"><span data-stu-id="badbf-104">A calendar view is a set of events from the user's calendar that occur between two points of time.</span></span> <span data-ttu-id="badbf-105">你将使用它获取当前一周的用户事件。</span><span class="sxs-lookup"><span data-stu-id="badbf-105">You'll use this to get the user's events for the current week.</span></span>

1. <span data-ttu-id="badbf-106">打开 **./Controllers/CalendarController.cs，** 将以下函数添加到 **CalendarController** 类。</span><span class="sxs-lookup"><span data-stu-id="badbf-106">Open **./Controllers/CalendarController.cs** and add the following function to the **CalendarController** class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="GetStartOfWeekSnippet":::

1. <span data-ttu-id="badbf-107">添加以下函数以处理从 Microsoft Graph 调用返回的异常。</span><span class="sxs-lookup"><span data-stu-id="badbf-107">Add the following function to handle exceptions returned from Microsoft Graph calls.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="HandleGraphExceptionSnippet":::

1. <span data-ttu-id="badbf-108">将现有的 `Get` 函数替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="badbf-108">Replace the existing `Get` function with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="GetSnippet" highlight="2,14-57":::

    <span data-ttu-id="badbf-109">查看更改。</span><span class="sxs-lookup"><span data-stu-id="badbf-109">Review the changes.</span></span> <span data-ttu-id="badbf-110">此函数的新版本：</span><span class="sxs-lookup"><span data-stu-id="badbf-110">This new version of the function:</span></span>

    - <span data-ttu-id="badbf-111">返回 `IEnumerable<Event>` 而不是 `string` 。</span><span class="sxs-lookup"><span data-stu-id="badbf-111">Returns `IEnumerable<Event>` instead of `string`.</span></span>
    - <span data-ttu-id="badbf-112">使用 Microsoft Graph 获取用户的邮箱设置。</span><span class="sxs-lookup"><span data-stu-id="badbf-112">Gets the user's mailbox settings using Microsoft Graph.</span></span>
    - <span data-ttu-id="badbf-113">使用用户的时区计算本周的开始和结束。</span><span class="sxs-lookup"><span data-stu-id="badbf-113">Uses the user's time zone to calculate the start and end of the current week.</span></span>
    - <span data-ttu-id="badbf-114">获取日历视图</span><span class="sxs-lookup"><span data-stu-id="badbf-114">Gets a calendar view</span></span>
        - <span data-ttu-id="badbf-115">使用函数包含标头，这将导致返回的事件的开始时间和结束时间转换为 `.Header()` `Prefer: outlook.timezone` 用户的时区。</span><span class="sxs-lookup"><span data-stu-id="badbf-115">Uses the `.Header()` function to include a `Prefer: outlook.timezone` header, which causes the returned events to have their start and end times converted to the user's timezone.</span></span>
        - <span data-ttu-id="badbf-116">使用 `.Top()` 函数最多请求 50 个事件。</span><span class="sxs-lookup"><span data-stu-id="badbf-116">Uses the `.Top()` function to request at most 50 events.</span></span>
        - <span data-ttu-id="badbf-117">使用 `.Select()` 函数仅请求应用使用的字段。</span><span class="sxs-lookup"><span data-stu-id="badbf-117">Uses the `.Select()` function to request just the fields used by the app.</span></span>
        - <span data-ttu-id="badbf-118">使用 `OrderBy()` 函数按开始时间对结果进行排序。</span><span class="sxs-lookup"><span data-stu-id="badbf-118">Uses the `OrderBy()` function to sort the results by the start time.</span></span>

1. <span data-ttu-id="badbf-119">保存更改并重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="badbf-119">Save your changes and restart the app.</span></span> <span data-ttu-id="badbf-120">刷新 Microsoft Teams 中的选项卡。</span><span class="sxs-lookup"><span data-stu-id="badbf-120">Refresh the tab in Microsoft Teams.</span></span> <span data-ttu-id="badbf-121">应用显示事件的 JSON 列表。</span><span class="sxs-lookup"><span data-stu-id="badbf-121">The app displays a JSON listing of the events.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="badbf-122">显示结果</span><span class="sxs-lookup"><span data-stu-id="badbf-122">Display the results</span></span>

<span data-ttu-id="badbf-123">现在，你可以以更用户友好的方式显示事件列表。</span><span class="sxs-lookup"><span data-stu-id="badbf-123">Now you can display the list of events in a more user friendly way.</span></span>

1. <span data-ttu-id="badbf-124">打开 **./Pages/Index.cshtml，** 在标记中添加以下 `<script>` 函数。</span><span class="sxs-lookup"><span data-stu-id="badbf-124">Open **./Pages/Index.cshtml** and add the following functions inside the `<script>` tag.</span></span>

    :::code language="javascript" source="../demo/GraphTutorial/Pages/Index.cshtml" id="RenderHelpersSnippet":::

1. <span data-ttu-id="badbf-125">将现有的 `renderCalendar` 函数替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="badbf-125">Replace the existing `renderCalendar` function with the following.</span></span>

    :::code language="javascript" source="../demo/GraphTutorial/Pages/Index.cshtml" id="RenderCalendarSnippet":::

1. <span data-ttu-id="badbf-126">保存更改并重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="badbf-126">Save your changes and restart the app.</span></span> <span data-ttu-id="badbf-127">刷新 Microsoft Teams 中的选项卡。</span><span class="sxs-lookup"><span data-stu-id="badbf-127">Refresh the tab in Microsoft Teams.</span></span> <span data-ttu-id="badbf-128">应用在用户日历上显示事件。</span><span class="sxs-lookup"><span data-stu-id="badbf-128">The app displays events on the user's calendar.</span></span>

    ![显示用户日历的应用屏幕截图](images/calendar-view.png)
