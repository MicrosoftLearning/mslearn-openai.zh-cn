---
lab:
  title: 使用 Azure OpenAI 服务实现检索增强生成 (RAG)
---

# 使用 Azure OpenAI 服务实现检索增强生成 (RAG)

通过 Azure OpenAI 服务，可将自己的数据与基础 LLM 的智能配合使用。 可以将模型限制为仅将数据用于相关主题，或将其与预先训练的模型的结果混合。

在本练习的方案中，你将担任在 Margie’s 旅行社工作的软件开发人员职位。 你将了解如何使用生成式 AI 更轻松高效地完成编码任务。 练习中使用的技术可以应用于其他代码文件、编程语言和用例。

该练习大约需要 20 分钟。

## 预配 Azure OpenAI 资源

如果还没有 Azure OpenAI 资源，请在 Azure 订阅中预配 Azure OpenAI 资源。

1. 登录到 Azure 门户，地址为 ****。
2. 请使用以下设置创建 Azure OpenAI 资源：
    - 订阅****：*选择已被批准访问 Azure OpenAI 服务的 Azure 订阅*
    - **资源组**：*创建或选择资源组*
    - 区域****：从以下任何区域中进行随机选择******\*
        - 澳大利亚东部
        - 加拿大东部
        - 美国东部
        - 美国东部 2
        - 法国中部
        - 日本东部
        - 美国中北部
        - 瑞典中部
        - 瑞士北部
        - 英国南部
    - **名称**：所选项的唯一名称**
    - **定价层**：标准版 S0

    > \* Azure OpenAI 资源受区域配额约束。 列出的区域包括本练习中使用的模型类型的默认配额。 在与其他用户共享订阅的情况下，随机选择一个区域可以降低单个区域达到配额限制的风险。 如果稍后在练习中达到配额限制，你可能需要在不同的区域中创建另一个资源。

3. 等待部署完成。 然后在 Azure 门户中转至部署的 Azure OpenAI 资源。

## 部署模型

Azure OpenAI 提供了一个名为 Azure OpenAI Studio 的基于 Web 的门户，可用于部署、管理和探索模型。 你将使用 Azure OpenAI Studio 部署模型，开始探索 Azure OpenAI。

1. 在 Azure OpenAI 资源的“概述”**** 页上，使用“转到 Azure OpenAI Studio”**** 按钮在新的浏览器选项卡中打开 Azure OpenAI Studio。
2. 在 Azure OpenAI Studio 中的“部署”**** 页上，查看现有模型部署。 如果没有模型部署，请使用以下设置创建新的“gpt-35-turbo-16k”**** 模型部署：
    - **模型**：gpt-35-turbo-16k *（必须是此模型才能使用此练习中的功能）*
    - **模型版本**：自动更新为默认值
    - **部署名称**：*所选的唯一名称。稍后将在实验室中使用此名称。*
    - **高级选项**
        - **内容筛选器**：默认
        - **部署类型**：标准
        - **每分钟令牌速率限制**：5K\*
        - **启用动态配额**：已启用

    > \*每分钟 5,000 个令牌的速率限制足以完成此练习，同时也为使用同一订阅的其他人留出容量。

## 在不添加自己的数据的情况下观察正常的聊天行为

在将 Azure OpenAI 连接到数据之前，先观察基本模型如何在没有任何锚定数据的情况下响应查询。

1. 在位于 `https://oai.azure.com` 的 **Azure OpenAI Studio** 中，在“操场”部分中选择“聊天”页。******** “聊天”操场页面由三个主要部分组成****：
    - ****“设置”- 用于设置模型的响应的上下文。
    - ****“聊天会话”- 用于提交聊天消息和查看响应。
    - ****“配置”- 用于配置模型部署的设置。
2. 在“配置”**** 部分中，确保已选择模型部署。
3. 在“设置”区域中，选择默认系统消息模板以设置聊天会话的上下文****。 默认系统消息是 *你是 帮助用户查找信息的 AI 助手*。
4. **** 在“聊天会话”中提交以下查询，并查看响应：

    ```
    I'd like to take a trip to New York. Where should I stay?
    ```

    ```
    What are some facts about New York?
    ```

    尝试询问类似的关于其他地点（将包含在我们的基础数据中，例如伦敦或旧金山）的旅游和住宿的问题。 你可能会得到关于地区或社区的完整响应，以及关于该城市的一些一般事实。

## 在聊天操场中连接你的数据

现在，你将为名为 *Margie's Travel* 的虚构旅行社公司添加一些数据。 然后，你将查看在将 Margie Travel 的宣传册用作锚定数据时，Azure OpenAI 模型将会如何响应。

1. 在新的浏览器选项卡中，从 `https://aka.ms/own-data-brochures` 下载宣传册数据的存档。 将宣传册提取到电脑上的文件夹。
1. 在 Azure OpenAI Studio 的“聊天操场”中的“设置”部分，选择“添加数据”************。
1. 选择“添加数据源”，然后选择“上传文件”********。
1. 需要创建存储帐户和 Azure AI 搜索资源。 在存储资源的下拉列表下，选择“创建新的 Azure Blob 存储资源”，并使用以下设置创建一个存储帐户。 未指定的任何内容将保留为默认值。

    - **订阅**：*Azure 订阅*
    - 资源组****：*选择与 Azure OpenAI 资源相同的资源组*
    - **存储帐户名称**：输入唯一名称
    - 区域****：*选择与 Azure OpenAI 资源相同的区域*
    - **冗余**：本地冗余存储 (LRS)

1. 在创建存储帐户资源时，返回到 Azure OpenAI Studio，并使用以下设置选择“创建新的 Azure AI 搜索资源”****。 未指定的任何内容将保留为默认值。

    - **订阅**：*Azure 订阅*
    - 资源组****：*选择与 Azure OpenAI 资源相同的资源组*
    - **服务名称**：输入唯一名称
    - 位置****：*选择与 Azure OpenAI 资源相同的位置*
    - **定价层**：基本

1. 等待搜索资源部署完毕，然后切换回 Azure AI Studio。
1. 在“添加数据”中，为数据源输入以下值，然后选择“下一步” 。

    - 选择数据源：上传文件
    - 订阅：Azure 订阅
    - **选择 Azure Blob 存储资源**。*使用“刷新”按钮重新填充列表，然后选择所创建的存储资源*****
        - 出现提示时打开 CORS
    - **选择 Azure AI 搜索资源***使用“刷新”按钮重新填充列表，然后选择所创建的搜索资源*****
    - **输入索引名称**：`margiestravel`
    - “向此搜索资源添加矢量搜索”：未勾选
    - **我确认知晓连接到 Azure AI搜索帐户会导致我的帐户产生使用量**：已勾选

1. 在“上传文件”页上，上传下载的 PDF，然后选择“下一步” 。
1. 在“数据管理”页上，从下拉列表中选择“关键字”搜索类型，然后选择“下一步”  。
1. 在“查看并完成”页上，选择“保存并关闭”，随即会添加数据 。 这可能需要几分钟时间，在此期间，你需要让窗口保持打开状态。 完成后，你将看到在“设置”部分中指定的数据源、搜索资源和索引****。

    > **提示**：新建搜索索引与 Azure OpenAI Studio 之间的连接偶尔会用时过长。 如果等待几分钟后仍未连接，请在 Azure 门户中检查 AI 搜索资源。 如果看到已完成的索引，则可以断开 Azure OpenAI Studio 中的数据连接，并通过指定 Azure AI 搜索数据源和选择新索引来重新添加它。

## 与基于你的数据的模型聊天

现在，你已添加数据，请提出与之前相同的问题，查看响应有何不同。

```
I'd like to take a trip to New York. Where should I stay?
```

```
What are some facts about New York?
```

这次你会看到一个非常不同的响应，包括有关某些酒店的详细信息和对 Margie's Travel 的提及，以及对所提供信息的来源的引用。 如果打开响应中列出的 PDF 引用，你将看到与提供的模型相同的酒店。

尝试向它询问基础数据中包含的其他城市，如迪拜、拉斯维加斯、伦敦和旧金山。

> 注意：“添加数据”仍处于预览状态，对于此功能，可能无法始终按预期运行，例如提供对未包含在基础数据中的城市的错误引用 。

## 将应用连接到自己的数据

接下来，我们来探讨如何连接应用以使用自己的数据。

### 准备在 Visual Studio Code 中开发应用

现在，我们来探索如何在使用 Azure OpenAI 服务 SDK 的应用中使用自己的数据。 你将使用 Visual Studio Code 开发应用。 应用程序的代码文件已在 GitHub repo 中提供。

> **提示**：如果已克隆 **mslearn-openai** 存储库，请在 Visual Studio Code 中打开它。 否则，请按照以下步骤将其克隆到开发环境中。

1. 启动 Visual Studio Code。
2. 打开面板 (SHIFT+CTRL+P) 并运行“**Git：克隆**”命令，以将 `https://github.com/MicrosoftLearning/mslearn-openai` 存储库克隆到本地文件夹（任意文件夹均可）。
3. 克隆存储库后，在 Visual Studio Code 中打开文件夹。

    > **注意**：如果 Visual Studio Code 显示一条弹出消息，提示你信任打开的代码，请单击弹出窗口中的“是，我信任该作者”选项****。

4. 等待其他文件安装完毕，以支持存储库中的 C# 代码项目。

    > **注意**：如果系统提示你添加生成和调试所需的资产，请选择**以后再说**。

## 配置应用程序

C# 和 Python 的应用程序都已提供，并且这两个应用具有相同的功能。 首先，你将使用 Azure OpenAI 资源完成要启用的应用程序的一些关键部件。

1. 在 Visual Studio Code 的“资源管理器”窗格中，浏览到“Labfiles/06-use-own-data”文件夹，然后根据语言首选项展开“CSharp”文件夹或“Python”文件夹****************。 每个文件夹都包含要在其中集成 Azure OpenAI 功能的应用的语言特定文件。
2. 右键单击包含代码文件的“CSharp”或“Python”文件夹，并打开集成终端。******** 然后通过运行适用于语言首选项的命令，安装 Azure OpenAI SDK 包：

    **C#：**

    ```
    dotnet add package Azure.AI.OpenAI --version 1.0.0-beta.14
    ```

    **Python**：

    ```
    pip install openai==1.13.3
    ```

3. 在“资源管理器”窗格中****，在“CSharp”或“Python”文件夹中，打开首选语言的配置文件********

    - **C#** ：appsettings.json
    - **Python**：.env
    
4. 更新配置值以包括：
    - 创建的 Azure OpenAI 资源的终结点**** 和密钥****（位于 Azure 门户中 Azure OpenAI 资源的“密钥和终结点”**** 页）
    - 为模型部署指定的 **部署名称**（在 Azure OpenAI Studio 的“部署”页中提供****）。
    - 搜索服务的终结点（Azure 门户中搜索资源概述页上的“URL”**** 值）。
    - 搜索资源的密钥****（位于 Azure 门户中搜索资源的“密钥”**** 页 - 可以使用两个管理密钥中的任何一个）
    - 搜索索引的名称（应为 `margiestravel`）。
1. 保存此配置文件。

### 添加代码以使用 Azure OpenAI 服务

现在，你已准备好使用 Azure OpenAI SDK 来使用已部署的模型。

1. ****************** 在“资源管理器”窗格的“CSharp”和“Python”文件夹中，打开首选语言的代码文件，并将注释“配置数据源”替换为代码以添加 Azure OpenAI SDK 库：

    **C#**：ownData.cs

    ```csharp
    // Configure your data source
    AzureSearchChatExtensionConfiguration ownDataConfig = new()
    {
            SearchEndpoint = new Uri(azureSearchEndpoint),
            Authentication = new OnYourDataApiKeyAuthenticationOptions(azureSearchKey),
            IndexName = azureSearchIndex
    };
    ```

    **Python**：ownData.py

    ```python
    # Configure your data source
    extension_config = dict(dataSources = [  
            { 
                "type": "AzureCognitiveSearch", 
                "parameters": { 
                    "endpoint":azure_search_endpoint, 
                    "key": azure_search_key, 
                    "indexName": azure_search_index,
                }
            }]
        )
    ```

2. 查看代码的其余部分，注意如何在用于提供有关数据源设置信息的请求正文中使用该 *扩展*。

3. 保存代码文件中的更改。

## 运行应用程序

配置应用后，请运行应用以将请求发送到模型并观察响应。 你会注意到，不同选项之间的唯一区别是提示的内容，所有其他参数（如标记计数和温度）对于每个请求保持不变。

1. 在交互式终端窗格中，确保文件夹上下文是首选语言的文件夹。 然后，输入以下命令来创建应用程序。

    - **C#** ：`dotnet run`
    - **Python**：`python ownData.py`

    > **提示**：可以使用 **终端工具栏中的“最大化面板大小** ”图标**^** 查看更多控制台文本。

2. 查看对于提示 `Tell me about London` 的响应，其中应包括答案以及用于锚定提示的数据的一些详细信息（获取自搜索服务）。

    > **提示**：如果想要查看搜索索引中的引文，请将代码文件顶部附件的变量“显示引文”设置为“true”**********。

## 清理

使用完 Azure OpenAI 资源后，请记得在位于 `https://portal.azure.com` 的 **Azure 门户** 中删除该资源。 务必同时包括存储帐户和搜索资源，因为这些内容会产生相对较大的成本。
