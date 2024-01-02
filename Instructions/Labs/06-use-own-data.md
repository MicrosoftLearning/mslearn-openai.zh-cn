---
lab:
  title: 通过 Azure OpenAI 使用自己的数据
---

# 通过 Azure OpenAI 使用自己的数据

通过 Azure OpenAI 服务，可将自己的数据与基础 LLM 的智能配合使用。 可以将模型限制为仅将数据用于相关主题，或将其与预先训练的模型的结果混合。

该练习大约需要 20 分钟。

## 开始之前

你将需要使用已被批准访问 Azure OpenAI 服务的 Azure 订阅。 

- 若要注册免费的 Azure 订阅，请访问 [https://azure.microsoft.com/free](https://azure.microsoft.com/free)。
- 若要请求访问 Azure OpenAI 服务，请访问 [https://aka.ms/oaiapply](https://aka.ms/oaiapply)。

## 预配 Azure OpenAI 资源

必须先在 Azure 订阅中预配 Azure OpenAI 资源，然后才能使用 Azure OpenAI 模型。

1. 登录到 [Azure 门户](https://portal.azure.com?azure-portal=true)。
2. 请使用以下设置创建 Azure OpenAI 资源：
    - 订阅：已被批准访问 Azure OpenAI 服务的 Azure 订阅。
    - 资源组：选择现有的资源组，或者用你选择的名称新建一个。
    - 区域：任选一个可用的区域。
    - 名称：所选项的唯一名称。
    - 定价层：标准版 S0
3. 等待部署完成。 然后在 Azure 门户中转至部署的 Azure OpenAI 资源。

## 部署模型

若要与 Azure OpenAI 聊天，必须先通过 Azure OpenAI Studio 部署一个要使用的模型。 部署后，我们将模型与操场一起使用，并使用我们的数据为其响应提供基础。

1. 在 Azure OpenAI 资源的“概述”页上，使用“浏览”按钮在新的浏览器选项卡中打开 Azure OpenAI Studio。或者直接导航到 [Azure OpenAI Studio](https://oai.azure.com/?azure-portal=true) 。
2. 在 Azure OpenAI Studio 中的“部署”**** 页上，查看现有模型部署。 如果没有模型部署，请使用以下设置创建新的“gpt-35-turbo-16k”**** 模型部署：
    - **模型**：gpt-35-turbo-16k
    - **模型版本**：自动更新为默认值
    - **部署名称**：你选择的唯一名称**
    - **高级选项**
        - **内容筛选器**：默认
        - **每分钟令牌速率限制**：5K\*
        - **启用动态配额**：已启用

    > \*每分钟 5,000 个令牌的速率限制足以完成此练习，同时也为使用同一订阅的其他人留出容量。

> **备注**：在某些区域中，新的模型部署界面不显示“模型版本”**** 选项。 在这种情况下，请不要担心，无需设置此选项并继续

## 在不添加自己的数据的情况下观察正常的聊天行为

在将 Azure OpenAI 连接到你的数据之前，先观察基础模型如何在没有任何基础数据的情况下响应查询。

1. 导航到“聊天”**** 操场，确保在“配置”**** 窗格中选择了你部署的模型（如果你只有一个部署的模型，这应该是默认模型）。
1. 输入以下提示并观察输出。

    ```code
    I'd like to take a trip to New York. Where should I stay?
    ```

    ```code
    What are some facts about New York?
    ```

1. 尝试询问类似的关于其他地点（将包含在我们的基础数据中，例如伦敦或旧金山）的旅游和住宿的问题。 你可能会得到关于地区或社区的完整响应，以及关于该城市的一些一般事实。

## 在聊天操场中连接你的数据

接下来，在聊天操场中添加你的数据，查看它如何以你的数据为基础进行响应

1. 从 GitHub [下载要使用的数据](https://aka.ms/own-data-brochures)。 解压缩所提供的 `.zip` 中的 PDF。
1. 导航到“聊天”操场，在“助手设置”窗格中选择“添加数据”。
1. 选择“添加数据源”，从下拉列表中选择“上传文件”。
1. 需要创建存储帐户和 Azure AI 搜索资源。 在存储资源的下拉列表下，选择“创建新的 Azure Blob 存储资源”，并使用以下设置创建一个存储帐户。 未指定的任何内容将保留为默认值。

    - 订阅：与 Azure OpenAI 资源相同的订阅
    - 资源组：与 Azure OpenAI 资源相同的资源组
    - 存储帐户名称：输入全局唯一的名称
    - 区域：与 Azure OpenAI 资源相同的区域
    - 冗余：本地冗余存储 (LRS)

1. 创建资源后，返回到 Azure OpenAI Studio，选择“创建新的 Azure AI 搜索资源”****（使用以下设置）。 未指定的任何内容将保留为默认值。

    - 订阅：与 Azure OpenAI 资源相同的订阅
    - 资源组：与 Azure OpenAI 资源相同的资源组
    - 服务名称：输入全局唯一的名称
    - 位置：与 Azure OpenAI 资源相同的位置
    - 定价层：基本

1. 等待搜索资源部署完毕，然后切换回 Azure AI Studio 并刷新页面。
1. 在“添加数据”中，为数据源输入以下值，然后选择“下一步” 。

    - 选择数据源：上传文件
    - 选择 Azure Blob 存储资源：选择创建的存储资源
        - 出现提示时打开 CORS
    - **选择 Azure AI 搜索资源**：选择创建的搜索资源**
    - 输入索引名称：margiestravel
    - “向此搜索资源添加矢量搜索”：未勾选
    - **我确认知晓连接到 Azure AI搜索帐户会导致我的帐户产生使用量**：已勾选

1. 在“上传文件”页上，上传下载的 PDF，然后选择“下一步” 。
1. 在“数据管理”页上，从下拉列表中选择“关键字”搜索类型，然后选择“下一步”  。
1. 在“查看并完成”页上，选择“保存并关闭”，随即会添加数据 。 这可能需要几分钟时间，在此期间，你需要让窗口保持打开状态。 完成后，你将看到“助手设置”窗格中指定的数据源、搜索资源和索引。

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

接下来，了解如何连接应用以使用自己的数据。

### 在 Cloud Shell 中设置应用程序

为了演示如何将 Azure OpenAI 应用连接到自己的数据，我们将使用 Azure Cloud Shell 中运行的简短命令行应用程序。 打开新的浏览器选项卡以使用 Cloud Shell。

1. 在 [Azure 门户](https://portal.azure.com?azure-portal=true)中，选择页面顶部搜索框右侧的 [>_] (Cloud Shell) 按钮。 Cloud Shell 窗格将在门户底部将打开。

    ![屏幕截图显示单击顶部搜索框右侧的图标启动 Cloud Shell。](../media/cloudshell-launch-portal.png#lightbox)

2. 首次打开 Cloud Shell 时，系统可能会提示你选择要使用的 shell 类型（Bash 或 PowerShell）。 选择 Bash。 如果未看到此选项，请跳过该步骤。  

3. 如果系统提示为 Cloud Shell 创建存储，请选择“显示高级设置”，然后选择以下设置：
    - **订阅**：你的订阅
    - Cloud Shell 区域：选择任何可用区域
    - 显示 VNET 隔离设置：未选中
    - 资源组：使用预配了 Azure OpenAI 资源的现有资源组
    - 存储帐户：新建具有唯一名称的存储帐户
    - 文件共享：新建具有唯一名称的文件共享

    等待存储创建完毕，此过程大约需要一分钟。

    > **注意**：如果已在 Azure 订阅中设置了 Cloud Shell，则可能需要使用 ⚙️ 菜单中的“重置用户设置”选项，以确保已安装最新版本的 Python 和 .NET Framework。

4. 请确保 Cloud Shell 窗格左上角指示的 shell 类型为 Bash。 如果是 PowerShell，请使用下拉菜单切换到 Bash。

5. 终端启动后，输入以下命令以下载示例应用程序并将其保存到名为“`azure-openai`”的文件夹中。

    ```bash
    rm -r azure-openai -f
    git clone https://github.com/MicrosoftLearning/mslearn-openai azure-openai
    ```

6. 文件将下载到名为“azure-openai”的文件夹中。 使用以下命令导航到本练习的实验室文件。

    ```bash
    cd azure-openai/Labfiles/06-use-own-data
    ```

7. 运行以下命令打开内置代码编辑器：

    ```bash
    code .
    ```

    > **提示**：有关使用其在 Azure Cloud Shell 环境中处理文件的更多详细信息，请参阅 [Azure Cloud Shell 代码编辑器文档](https://learn.microsoft.com/azure/cloud-shell/using-cloud-shell-editor)。

## 配置应用程序

在本练习中，你将使用 Azure OpenAI 资源完成应用程序的一些关键部分以进行启用。 已提供适用于 C# 和 Python 的应用程序。 这两个应用具有相同的功能。

1. 在代码编辑器中，根据语言首选项展开 CSharp 或 Python 文件夹 。

2. 打开所选语言的配置文件。

    - C#：`appsettings.json`
    - Python： `.env`
    
3. 更新配置值以包括：
    - 创建的 Azure OpenAI 资源的终结点**** 和密钥****（位于 Azure 门户中 Azure OpenAI 资源的“密钥和终结点”**** 页）
    - 为模型部署指定的名称（位于 Azure OpenAI Studio中的“部署”**** 页）。
    - 搜索服务的终结点（Azure 门户中搜索资源概述页上的“URL”**** 值）。
    - 搜索资源的密钥****（位于 Azure 门户中搜索资源的“密钥”**** 页 - 可以使用两个管理密钥中的任何一个）
    - 搜索索引的名称（应为 `margiestravel`）。
    
4. 保存更新的配置文件。

5. 在控制台窗格中，输入以下命令以导航到首选语言对应的文件夹并安装必要的包。

    **C#**

    ```bash
    cd CSharp
    dotnet add package Azure.AI.OpenAI --version 1.0.0-beta.9
    ```

    **Python**

    ```bash
    cd Python
    pip install python-dotenv
    pip install openai==1.2.0
    ```

6. 在代码编辑器中，导航到首选语言文件夹，选择代码文件，然后添加必要的库。

    **C#**：OwnData.cs

    ```csharp
    // Add Azure OpenAI package
    using Azure.AI.OpenAI;
    ```

    **Python**：ownData.py

    ```python
    # Add OpenAI import
    from openai import AzureOpenAI
    ```

7. 查看代码文件，尤其是完成 API 调用参数时使用搜索值的位置。

## 运行应用程序

配置应用后，请运行应用以将请求发送到模型并观察响应。 你会注意到，响应现在是基于你的数据，类似于工作室体验。

1. 在 Cloud Shell Bash 终端中，导航到首选语言的文件夹。
1. 展开终端以占据大部分浏览器窗口，然后运行应用程序。

    - **C#** ：`dotnet run`
    - **Python**：`python ownData.py`

1. 提交提示 `Tell me about London`，应会看到响应引用了你的数据。

## 清理

Azure OpenAI 资源使用完毕后，请记得在 [Azure 门户](https://portal.azure.com/?azure-portal=true)中删除该资源。 务必同时包括存储帐户和搜索资源，因为这些内容会产生相对较大的成本。
