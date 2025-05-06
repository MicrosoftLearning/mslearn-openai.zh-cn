---
lab:
  title: 使用 AI 生成图像
  description: 了解如何使用 DALL-E OpenAI 模型生成图像。
  status: new
---

# 使用 AI 生成图像

在本练习中，你将使用 OpenAI DALL-E 生成式 AI 模型生成图像。 你将使用 Azure AI Foundry 和 Azure OpenAI 服务开发应用。

此练习大约需要 **30** 分钟。

## 创建 Azure AI Foundry 项目

让我们首先创建 Azure AI Foundry 项目。

1. 在 Web 浏览器中打开 [Azure AI Foundry 门户](https://ai.azure.com)，网址为：`https://ai.azure.com`，然后使用 Azure 凭据登录。 关闭首次登录时打开的任何使用技巧或快速入门窗格，如有必要，请使用左上角的 **Azure AI Foundry** 徽标导航到主页，如下图所示：

    ![Azure AI Foundry 门户的屏幕截图。](../media/ai-foundry-home.png)

1. 在主页中，选择“**+ 创建项目**”。
1. 在**创建项目**向导中，输入项目的有效名，如果出现建议使用现有中心的提示，请选择新建中心的选项。 然后查看将自动创建的 Azure 资源以支持中心和项目。
1. 选择“**自定义**”并为中心指定以下设置：
    - **中心名称**：*中心的有效名称*
    - **订阅**：Azure 订阅
    - **资源组**：*创建或选择资源组*
    - **位置**：选择“**帮助我选择**”，然后在“位置帮助程序”窗口中选择“**dalle**”，并使用推荐的区域\*
    - **连接 Azure AI 服务或 Azure OpenAI**：*新建 AI 服务资源*
    - **连接 Azure AI 搜索**：跳过连接

    > \* Azure OpenAI 资源受区域配额约束。 如果稍后在练习中达到配额限制，你可能需要在不同的区域中创建另一个资源。

1. 选择“**下一步**”查看配置。 然后，选择“**创建**”并等待该进程完成。
1. 创建项目后，关闭显示的所有使用技巧，并查看 Azure AI Foundry 门户中的项目页面，如下图所示：

    ![Azure AI Foundry 门户中 Azure AI 项目详细信息的屏幕截图。](../media/ai-foundry-project.png)

## 部署 DALL-E 模型

现在，你已准备好部署 DALL-E 模型以支持图像生成。

1. 在 Azure AI Foundry 项目页右上角的工具栏中，使用“**预览功能**”图标启用“**将模型部署到 Azure AI 模型推理服务**”功能。 此功能可确保模型部署可供 Azure AI 推理服务使用，你可在应用程序代码中使用该服务。
1. 在项目左侧窗格的“**我的资产**”部分中，选择“**模型 + 终结点**”页。
1. 在“**模型 + 终结点**”页的“**模型部署**”选项卡中，在“**+ 部署模型**”菜单中，选择“**部署基础模型**”。
1. 在列表中搜索 **dall-e-3** 模型，然后选择并确认。
1. 如果出现提示，请同意许可协议，然后在部署详细信息中选择“**自定义**”并使用以下设置部署模型：
    - **部署名**：*有效的模型部署名*
    - **部署类型**：标准
    - **部署详细信息**：*使用默认设置*
1. 等待部署预配状态为“**完成**”。

## 在操场中测试模型

在创建客户端应用程序之前，让我们在操场中测试 DALL-E 模型。

1. 在部署的 DALL-E 模型的页面中，选择“**在操场中打开**”（或在“**操场**”页中，打开“**图像操场**”）。
1. 确保已选择 DALL-E 模型部署。 然后，在“**提示**”框中输入提示，如 `Create an image of an robot eating spaghetti`。
1. 查看操场中生成的图像：

    ![图像操场的屏幕截图，其中包含生成的图像。](../media/images-playground.png)

1. 输入跟进提示，例如 `Show the robot in a restaurant` 并查看生成的图像。
1. 继续测试新的提示以优化图像，直到对图像感到满意。

## 创建客户端应用程序

模型似乎在操场上工作。 现在可以使用 Azure OpenAI SDK 在客户端应用程序中使用它。

> **提示**：可以选择使用 Python 或 Microsoft C# 开发解决方案。 按照所选语言的相应部分中的说明进行操作。

### 准备应用程序配置

1. 在 Azure AI Foundry 门户中，查看项目的“**概述**”页。
1. 在“**项目详细信息**”区域中，记下**项目连接字符串**。 你将使用此连接字符串连接到客户端应用程序中的项目。
1. 打开新的浏览器选项卡（使 Azure AI Foundry 门户在现有选项卡中保持打开状态）。 然后在新选项卡中，浏览到 [Azure 门户](https://portal.azure.com)，网址为：`https://portal.azure.com`；如果出现提示，请使用 Azure 凭据登录。
1. 使用页面顶部搜索栏右侧的 **[\>_]** 按钮在 Azure 门户中创建新的 Cloud Shell，选择 ***PowerShell*** 环境。 Cloud Shell 在 Azure 门户底部的窗格中提供命令行接口。

    > **备注**：如果以前创建了使用 *Bash* 环境的 Cloud Shell，请将其切换到 ***PowerShell***。

5. 在 Cloud Shell 工具栏的“**设置**”菜单中，选择“**转到经典版本**”（这是使用代码编辑器所必需的）。

    **<font color="red">在继续作之前，请确保已切换到 Cloud Shell 的经典版本。</font>**

1. 在 Cloud Shell 窗格中，输入以下命令以克隆包含此练习代码文件的 GitHub 存储库（键入命令，或将其复制到剪贴板后，在命令行中右键单击并粘贴为纯文本）：

    ```
    rm -r mslearn-openai -f
    git clone https://github.com/microsoftlearning/mslearn-openai mslearn-openai
    ```

    > **提示**：将命令粘贴到 cloudshell 中时，输出可能会占用大量屏幕缓冲区。 可以通过输入 `cls` 命令来清除屏幕，以便更轻松地专注于每项任务。

1. 克隆存储库后，根据所选编程语言（Python 或 C#）导航到包含应用程序代码文件的特定语言文件夹：  

    **Python**

    ```
   cd mslearn-openai/Labfiles/03-image-generation/Python
    ```

    **C#**

    ```
   cd mslearn-openai/Labfiles/03-image-generation/CSharp
    ```

1. 在 Cloud Shell 命令行窗格中，输入以下命令安装将使用的库：

    **Python**

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install python-dotenv azure-identity azure-ai-projects openai requests
    ```

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --prerelease
   dotnet add package Azure.AI.OpenAI
    ```

1. 输入以下命令以编辑已提供的配置文件：

    **Python**

    ```
   code .env
    ```

    **C#**

    ```
   code appsettings.json
    ```

    该文件已在代码编辑器中打开。

1. 在代码文件中，将 **your_project_endpoint** 占位符替换为项目的连接字符串（从 Azure AI Foundry 门户中的项目“**概述**”页复制），并将 **your_model_deployment** 占位符替换为分配给 dall-e-3 模型部署的名称。
1. 替换占位符后，使用 **Ctrl+S** 命令保存更改，然后使用 **Ctrl+Q** 命令关闭代码编辑器，同时使 Cloud Shell 命令行保持打开状态。

### 写入代码以连接到项目并与模型聊天

> **提示**：添加代码时，请务必保持正确的缩进。

1. 输入以下命令以编辑已提供的代码文件：

    **Python**

    ```
   code dalle-client.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. 在代码文件中，请注意在文件顶部添加的现有语句，以导入必要的 SDK 命名空间。 然后，在注释“**添加引用**”下，添加以下代码以引用之前安装的库中的命名空间：

    **Python**

    ```python
   # Add references
   from dotenv import load_dotenv
   from azure.identity import DefaultAzureCredential
   from azure.ai.projects import AIProjectClient
   from openai import AzureOpenAI
   import requests
    ```

    **C#**

    ```csharp
   // Add references
   using Azure.Identity;
   using Azure.AI.Projects;
   using Azure.AI.OpenAI;
   using OpenAI.Images;
    ```

1. 在 **main** 函数的注释“**获取配置设置**”下，请注意，代码将加载配置文件中定义的项目连接字符串和模型部署名称值。
1. 在注释“**初始化项目客户端**”下，添加以下代码，以使用当前登录所使用的 Azure 凭据连接到 Azure AI Foundry 项目：

    **Python**

    ```python
   # Initialize the project client
   project_client = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential())
    ```

    **C#**

    ```csharp
   // Initialize the project client
   var projectClient = new AIProjectClient(project_connection,
                        new DefaultAzureCredential());
    ```

1. 在注释“**获取 OpenAI 客户端**”下，添加以下代码，以创建与模型聊天的客户端对象：

    **Python**

    ```python
   # Get an OpenAI client
   openai_client = project_client.inference.get_azure_openai_client(api_version="2024-06-01")

    ```

    **C#**

    ```csharp
   // Get an OpenAI client
   ConnectionResponse connection = projectClient.GetConnectionsClient().GetDefaultConnection(ConnectionType.AzureOpenAI, withCredential: true);

   var connectionProperties = connection.Properties as ConnectionPropertiesApiKeyAuth;

   AzureOpenAIClient openAIClient = new(
        new Uri(connectionProperties.Target),
        new AzureKeyCredential(connectionProperties.Credentials.Key));

   ImageClient openAIimageClient = openAIClient.GetImageClient(model_deployment);

    ```

1. 请注意，代码包含一个循环，允许用户输入提示，直到输入“退出”。 然后，在循环部分的注释“**获取图像**”下，添加以下代码，以提交提示，并从模型中获取生成图像的 URL：

    **Python**

    ```python
   # Generate an image
   result = openai_client.images.generate(
        model=model_deployment,
        prompt=input_text,
        n=1
    )

    json_response = json.loads(result.model_dump_json())
    image_url = json_response["data"][0]["url"] 
    ```

    **C#**

    ```csharp
   // Generate an image
   var imageGeneration = await openAIimageClient.GenerateImageAsync(
            input_text,
            new ImageGenerationOptions()
            {
                Size = GeneratedImageSize.W1024xH1024
            }
   );
   imageUrl= imageGeneration.Value.ImageUri;
    ```

1. 请注意，**main** 函数其余部分的代码将图像 URL 和文件名传递给提供的函数，该函数下载生成的图像并将其保存为.png文件。

1. 使用 **Ctrl+S** 命令保存对代码文件的更改，然后使用 **Ctrl+Q** 命令关闭代码编辑器，同时保持 Cloud Shell 命令行处于打开状态。

### 运行客户端应用程序

1. 在 Cloud Shell 命令行窗格中，输入以下命令以运行应用：

    **Python**

    ```
   python dalle-client.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. 出现提示时，输入图像请求，例如 `Create an image of a robot eating pizza`。 一两分钟后，应用应确认图像已保存。
1. 再尝试一些提示。 完成后，输入`quit`退出程序。

    > **备注**：在此简单应用中，我们尚未实现用于保留对话历史记录的逻辑；因此模型会将每个提示视为一个新请求，且没有上一提示的上下文。

1. 要下载和查看应用生成的图像，请使用 Cloud Shell **下载**命令 - 指定生成的 .png 文件：

    ```
   download ./images/image_1.png
    ```

    下载命令会在浏览器右下角创建一个弹出链接，可以选择此链接下载并打开该文件。

## 总结

在本练习中，你使用 Azure AI Foundry 和 Azure OpenAI SDK 创建客户端应用程序，使用 DALL-E 模型生成图像。

## 清理

如果已完成对 DALL-E 的探索，则应删除在本练习中创建的资源，以避免产生不必要的 Azure 成本。

1. 返回到包含 Azure 门户的浏览器选项卡（或在新的浏览器选项卡中重新打开 [Azure 门户](https://portal.azure.com)，网址为：`https://portal.azure.com`），查看已在其中部署本练习中使用的资源的资源组内容。
1. 在工具栏中，选择“删除资源组”****。
1. 输入资源组名称，并确认要删除该资源组。