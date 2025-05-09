---
lab:
  title: 使用 Azure OpenAI 服务实现检索增强生成 (RAG)
  status: new
---

# 使用 Azure OpenAI 服务实现检索增强生成 (RAG)

通过 Azure OpenAI 服务，可将自己的数据与基础 LLM 的智能配合使用。 可以将模型限制为仅将数据用于相关主题，或将其与预先训练的模型的结果混合。

在本练习的方案中，你将担任在 Margie’s 旅行社工作的软件开发人员职位。 将了解如何使用 Azure AI 搜索为自己的数据编制索引，并将其与 Azure OpenAI 结合使用来增强提示。

该练习大约需要 **30** 分钟。

## 预配 Azure 资源

若要完成本练习，需要：

- Azure OpenAI 资源。
- Azure AI 搜索资源。
- Azure 存储帐户资源。

1. 登录到 Azure 门户，地址为 ****。
2. 请使用以下设置创建 Azure OpenAI 资源：
    - 订阅****：*选择已被批准访问 Azure OpenAI 服务的 Azure 订阅*
    - **资源组**：*创建或选择资源组*
    - 区域****：从以下任何区域中进行随机选择******\*
        - 美国东部
        - 美国东部 2
        - 美国中北部
        - 美国中南部
        - 瑞典中部
        - 美国西部
        - 美国西部 3
    - **名称**：所选项的唯一名称**
    - **定价层**：标准版 S0

    > \* Azure OpenAI 资源受区域配额约束。 列出的区域包括本练习中使用的模型类型的默认配额。 在与其他用户共享订阅的情况下，随机选择一个区域可以降低单个区域达到配额限制的风险。 如果稍后在练习中达到配额限制，你可能需要在不同的区域中创建另一个资源。

3. 预配 Azure OpenAI 资源时，使用以下设置创建“**Azure AI 搜索**”资源：
    - **订阅**：*在其中预配 Azure OpenAI 资源的订阅*
    - 资源组****：*在其中预配了 Azure OpenAI 资源的资源组*
    - **服务名称**：*所选项的唯一名称*
    - **位置**：*在其中预配 Azure OpenAI 资源的区域*
    - 定价层：基本
4. 预配 Azure AI 搜索资源时，使用以下设置创建“**存储帐户**”资源：
    - **订阅**：*在其中预配 Azure OpenAI 资源的订阅*
    - 资源组****：*在其中预配了 Azure OpenAI 资源的资源组*
    - **存储帐户名称**：*所选项的唯一名称*
    - **区域**：*在其中预配 Azure OpenAI 资源的区域*
    - **主服务**：Azure Blob 存储或 Azure Data Lake Storage Gen 2
    - **性能**：标准
    - 冗余：本地冗余存储 (LRS)
5. 在 Azure 订阅中成功部署这三个资源后，请在 Azure 门户中查看这些资源并收集以下信息（稍后在练习中需要这些信息）：
    - 创建的 Azure OpenAI 资源的终结点**** 和密钥****（位于 Azure 门户中 Azure OpenAI 资源的“密钥和终结点”**** 页）
    - Azure AI 搜索服务的终结点（Azure 门户中 Azure AI 搜索资源概述页上的 **URL** 值）。
    - Azure AI 搜索资源的**主管理密钥**（可在 Azure 门户中 Azure AI 搜索资源的“**密钥**”页中找到）。

## 上传数据

将使用自己的数据来设置与生成 式AI 模型一起使用的提示。 在本练习中，数据由虚构的 *Margies Travel* 公司提供的旅行手册集合组成。

1. 在新的浏览器选项卡中，从 `https://aka.ms/own-data-brochures` 下载宣传册数据的存档。 将宣传册提取到电脑上的文件夹。
1. 在 Azure 门户中，导航到存储帐户并查看“**存储浏览器**”页。
1. 选择“**Blob 容器**”，然后添加名为 `margies-travel` 的新容器。
1. 选择“**margies-travel**”容器，然后将之前提取的 .pdf 手册上传到 Blob 容器的根文件夹。

## 部署 AI 模型

在本练习中，将使用两个 AI 模型：

- 文本嵌入模型，用于*矢量化*手册中的文本，以便有效地编制索引，用于设置提示。
- GPT 模型，应用程序可以使用 GPT 模型来生成基于数据的提示的响应。

## 部署模型

接下来，你将从 Cloud Shell 部署 Azure OpenAI 模型。

1. 使用页面顶部搜索栏右侧的 **[\>_]** 按钮在 Azure 门户中创建新的 Cloud Shell，选择 ***Bash*** 环境。 Cloud Shell 在 Azure 门户底部的窗格中提供命令行接口。

    > **备注**：如果以前创建了使用 *PowerShell* 环境的 Cloud Shell，请将其切换到 ***Bash***。

```dotnetcli
az cognitiveservices account deployment create \
   -g <your_resource_group> \
   -n <your_OpenAI_resource> \
   --deployment-name text-embedding-ada-002 \
   --model-name text-embedding-ada-002 \
   --model-version "2"  \
   --model-format OpenAI \
   --sku-name "Standard" \
   --sku-capacity 5
```

> **备注**：SKU 容量以每分钟多少千个令牌进行度量。 每分钟 5,000 个令牌的速率限制足以完成此练习，同时也为使用同一订阅的其他用户留出容量。

部署文本嵌入模型后，使用以下设置新建 **GPT-4o** 模型部署：

```dotnetcli
az cognitiveservices account deployment create \
   -g <your_resource_group> \
   -n <your_OpenAI_resource> \
   --deployment-name gpt-4o \
   --model-name gpt-4o \
   --model-version "2024-05-13" \
   --model-format OpenAI \
   --sku-name "Standard" \
   --sku-capacity 5
```

## 创建索引

若要在提示中轻松使用自己的数据，请使用 Azure AI 搜索为数据编制索引。 将使用之前在索引过程中部署的文本嵌入模型来*矢量化*文本数据（这会导致索引中的每个文本标记由数字矢量表示，使其与生成式 AI 模型表示文本的方式兼容）

1. 在 Azure 门户中，导航到 Azure AI 搜索资源。
1. 在“概述”**** 页上，选择“导入和矢量化数据”****。
1. 在“**设置数据连接**”页中，选择“**Azure Blob 存储**”并使用以下设置配置数据源：
    - **订阅**：在其中预配存储帐户的 Azure 订阅。
    - **Blob 存储帐户**：之前创建的存储帐户。
    - **Blob 容器**：margies-travel
    - **Blob 文件夹**：*留空*
    - **启用删除跟踪**：未选定
    - **使用托管标识进行身份验证**：未选定
1. 在“**矢量化文本**”页上，选择以下设置：
    - **种类**：Azure OpenAI
    - **订阅**：在其中预配 Azure OpenAI 服务的 Azure 订阅。
    - **Azure OpenAI 服务**：Azure OpenAI 服务资源
    - **模型部署**：text-embedding-ada-002
    - **身份验证类型**：API 密钥
    - **我确认连接到 Azure OpenAI 服务将产生额外的帐户费用**：已选定
1. 在下一页上，请**不要**选择使用 AI 技能矢量化图像或提取数据的选项。
1. 在下一页上，启用语义排名并计划索引器运行一次。
1. 在最后一页上，将“**对象名称前缀**”设置为 `margies-index`，然后创建索引。

## 准备在 Visual Studio Code 中开发应用

现在，我们来探索如何在使用 Azure OpenAI 服务 SDK 的应用中使用自己的数据。 你将使用 Visual Studio Code 开发应用。 应用程序的代码文件已在 GitHub repo 中提供。

> **提示**：如果已克隆 **mslearn-openai** 存储库，请在 Visual Studio Code 中打开它。 否则，请按照以下步骤将其克隆到开发环境中。

1. 启动 Visual Studio Code。
2. 打开面板（SHIFT+CTRL+P 或**视图** > **命令面板...**）并运行 **Git: Clone** 命令将`https://github.com/MicrosoftLearning/mslearn-openai`存储库克隆到本地文件夹（任意文件夹均可）。
3. 克隆存储库后，在 Visual Studio Code 中打开文件夹。

    > **注意**：如果 Visual Studio Code 显示一条弹出消息，提示你信任打开的代码，请单击弹出窗口中的“是，我信任该作者”选项。

4. 等待其他文件安装完毕，以支持存储库中的 C# 代码项目。

    > **注意**：如果系统提示你添加生成和调试所需的资产，请选择**以后再说**。

## 配置应用程序

C# 和 Python 的应用程序都已提供，并且这两个应用具有相同的功能。 首先，你将使用 Azure OpenAI 资源完成要启用的应用程序的一些关键部件。

1. 在 Visual Studio Code 中，于“**资源管理器**”窗格中，导航到 **Labfiles/02-use-own-data** 文件夹，并根据您的语言首选项展开 **CSharp** 或 **Python** 文件夹。 每个文件夹都包含要在其中集成 Azure OpenAI 功能的应用的语言特定文件。
2. 右键单击包含代码文件的“CSharp”或“Python”文件夹，并打开集成终端。******** 然后通过运行适用于语言首选项的命令，安装 Azure OpenAI SDK 包：

    **C#：**

    ```powershell
    dotnet add package Azure.AI.OpenAI --version 2.1.0
    dotnet add package Azure.Search.Documents --version 11.6.0
    ```

    **Python**：

    ```powershell
    pip install openai==1.65.2
    ```

3. 在“资源管理器”窗格中****，在“CSharp”或“Python”文件夹中，打开首选语言的配置文件********

    - **C#** ：appsettings.json
    - **Python**：.env

4. 更新配置值以包括：
    - 创建的 Azure OpenAI 资源的终结点**** 和密钥****（位于 Azure 门户中 Azure OpenAI 资源的“密钥和终结点”**** 页）
    - 为 GPT-4o 模型部署指定的**部署名称**（应为`gpt-4o`）。
    - 搜索服务的终结点（Azure 门户中搜索资源概述页上的“URL”**** 值）。
    - 搜索资源的密钥****（位于 Azure 门户中搜索资源的“密钥”**** 页 - 可以使用两个管理密钥中的任何一个）
    - 搜索索引的名称（应为 `margies-index`）。
5. 保存此配置文件。

### 添加代码以使用 Azure OpenAI 服务

现在，你已准备好使用 Azure OpenAI SDK 来使用已部署的模型。

1. 在“**资源管理器**”窗格的 **CSharp** 或 **Python** 文件夹中，打开首选语言的代码文件，并将注释“***配置数据源***”替换为索引的代码，作为聊天补全的数据源：

    **C#**：ownData.cs

    ```csharp
    // Configure your data source
    // Extension methods to use data sources with options are subject to SDK surface changes. Suppress the warning to acknowledge this and use the subject-to-change AddDataSource method.
    #pragma warning disable AOAI001
    
    ChatCompletionOptions chatCompletionsOptions = new ChatCompletionOptions()
    {
        MaxOutputTokenCount = 600,
        Temperature = 0.9f,
    };
    
    chatCompletionsOptions.AddDataSource(new AzureSearchChatDataSource()
    {
        Endpoint = new Uri(azureSearchEndpoint),
        IndexName = azureSearchIndex,
        Authentication = DataSourceAuthentication.FromApiKey(azureSearchKey),
    });
    ```

    **Python**：ownData.py

    ```python
    # Configure your data source
    text = input('\nEnter a question:\n')
    
    completion = client.chat.completions.create(
        model=deployment,
        messages=[
            {
                "role": "user",
                "content": text,
            },
        ],
        extra_body={
            "data_sources":[
                {
                    "type": "azure_search",
                    "parameters": {
                        "endpoint": os.environ["AZURE_SEARCH_ENDPOINT"],
                        "index_name": os.environ["AZURE_SEARCH_INDEX"],
                        "authentication": {
                            "type": "api_key",
                            "key": os.environ["AZURE_SEARCH_KEY"],
                        }
                    }
                }
            ],
        }
    )
    ```

1. 保存代码文件中的更改。

## 运行应用程序

配置应用后，请运行应用以将请求发送到模型并观察响应。 你会注意到，不同选项之间的唯一区别是提示的内容，所有其他参数（如标记计数和温度）对于每个请求保持不变。

1. 在交互式终端窗格中，确保文件夹上下文是首选语言的文件夹。 然后，输入以下命令来创建应用程序。

    - **C#** ：`dotnet run`
    - **Python**：`python ownData.py`

    > **提示**：可以使用 **终端工具栏中的“最大化面板大小** ”图标**^** 查看更多控制台文本。

2. 查看对于提示 `Tell me about London` 的响应，其中应包括答案以及用于锚定提示的数据的一些详细信息（获取自搜索服务）。

    > **提示**：如果想要查看搜索索引中的引文，请将代码文件顶部附件的变量“显示引文”设置为“true”**********。

## 清理

使用完 Azure OpenAI 资源后，请记得在位于 `https://portal.azure.com` 的 **Azure 门户**中删除该资源。 务必同时包括存储帐户和搜索资源，因为这些内容会产生相对较大的成本。
