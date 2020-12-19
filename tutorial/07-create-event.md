<!-- markdownlint-disable MD002 MD041 -->

在此部分中，您将添加在用户日历上创建事件的能力。

## <a name="create-the-new-event-tab"></a>新建事件选项卡

1. 在 **./Pages** 目录中新建一个名为 **NewEvent.cshtml** 的文件并添加以下代码。

    :::code language="razor" source="../demo/GraphTutorial/Pages/NewEvent.cshtml":::

    这将实现一个简单的表单，并添加 JavaScript 以将表单数据张贴到 Web API。

## <a name="implement-the-web-api"></a>实现 Web API

1. 在项目的根目录中新建一个名为 **Models** 的目录。

1. 在名为 NewEvent.cs 的 **./Models** **目录中** 创建新文件，并添加以下代码。

    :::code language="csharp" source="../demo/GraphTutorial/Models/NewEvent.cs" id="NewEventSnippet":::

1. 打开 **./Controllers/CalendarController.cs，** 在文件顶部 `using` 添加以下语句。

    ```csharp
    using GraphTutorial.Models;
    ```

1. 将以下函数添加到 **CalendarController** 类。

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="PostSnippet":::

    这允许对包含表单字段的 Web API 进行 HTTP POST。

1. 保存所有更改并重新启动应用程序。 在 Microsoft Teams 中刷新应用，然后选择"创建 **事件"** 选项卡。填写表单，然后选择" **创建** "将事件添加到用户的日历。

    !["创建事件"选项卡的屏幕截图](images/create-event.png)
