<!-- markdownlint-disable MD002 MD041 -->

应用清单介绍了应用如何与 Microsoft Teams 集成，并且安装应用是必需的。 在此部分中，你将使用 Microsoft Teams 客户端中的 App Studio 生成清单。

1. 如果你尚未在 Teams 中安装 App Studio， [则立即安装](/microsoftteams/platform/concepts/build-and-test/app-studio-overview)它。

1. 在 Microsoft Teams 中启动 App Studio，然后选择 **清单编辑器**。

1. 选择 **"新建应用"。**

    ![Microsoft Teams 中 App Studio 中清单编辑器的屏幕截图](images/app-studio-01.png)

1. 在 **"应用详细信息** "页上，填写必填字段。

    > [!NOTE]
    > 可以使用"品牌"部分的默认 **图标** 或上传你自己的图标。

1. 在左侧菜单上，选择 **"功能"** 下的 **"选项卡"。**

1. 选择 **"添加****个人"选项卡下的"添加"。**

    ![App Studio 中"选项卡"页面的屏幕截图](images/app-studio-02.png)

1. 按如下方式填写字段，其中是上一节中复制的 `YOUR_NGROK_URL` 转发 URL。 选择 **"完成后** 保存"。

    - **名称：**`Create event`
    - **实体 ID：**`createEventTab`
    - **内容 URL：**`YOUR_NGROK_URL/newevent`

1. 选择 **"添加****个人"选项卡下的"添加"。**

1. 按如下方式填写字段，其中是上一节中复制的 `YOUR_NGROK_URL` 转发 URL。 选择 **"完成后** 保存"。

    - **名称：**`Graph calendar`
    - **实体 ID：**`calendarTab`
    - **内容 URL：**`YOUR_NGROK_URL`

1. 在左侧菜单上，选择"完成 **"下的"域和****权限"。**

1. 通过 **应用注册将 AAD** 应用 ID 设置为应用程序 ID。

1. 通过 **应用注册将** 单一登录字段设置为应用程序 ID URI。

1. 在左侧菜单上，选择"测试 **"，然后分发到"完成****"下**。 选择 **"下载"。**

1. 在名为"清单"的项目的根目录中创建新 **目录**。 将下载的 ZIP 文件的内容提取到此目录。
