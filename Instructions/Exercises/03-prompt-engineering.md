---
lab:
  title: 在应用中利用提示工程
---

# 在应用中利用提示工程

使用 Azure OpenAI 服务时，开发人员如何塑造其提示会极大地影响生成式 AI 模型的响应方式。 Azure OpenAI 模型能够以清晰简洁的方式定制内容并设置内容格式。 在本练习中，你将了解针对类似内容的不同提示如何帮助塑造 AI 模型的响应，以更好地满足你的要求。

在本练习的方案中，你将担任从事野生动物营销活动的软件开发人员职位。 你正在探索如何使用生成式 AI 来改进广告电子邮件和对可能适用于团队的文章进行分类。 练习中使用的提示工程技术同样适用于各种用例。

该练习大约需要 **30** 分钟。

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

Azure 提供了一个名为 **Azure AI Studio** 的基于 Web 的门户，可用于部署、管理和探索模型。 你将通过使用 Azure OpenAI Studio 部署模型，开始探索 Azure OpenAI。

> **备注**：使用 Azure AI Studio 时，可能会显示建议你执行任务的消息框。 可以关闭这些消息框并按照本练习中的步骤进行操作。

1. 在 Azure 门户中的 Azure OpenAI 资源的“**概述**”页上，向下滚动到“**开始**”部分，然后选择转到 **AI Studio** 的按钮。
1. 在 Azure AI Studio 的左侧窗格中，选择“**部署**”页并查看现有模型部署。 如果没有模型部署，请使用以下设置创建新的“gpt-35-turbo-16k”**** 模型部署：
    - **部署名称**：你选择的唯一名称**
    - **模型**：gpt-35-turbo-16k *（如果 16k 模型不可用，请选择 gpt-35-turbo）*
    - **模型版本**：*使用默认版本*
    - **部署类型**：标准
    - **每分钟令牌速率限制**：5K\*
    - **内容筛选器**：默认
    - **启用动态配额**：已禁用

    > \*每分钟 5,000 个令牌的速率限制足以完成此练习，同时也为使用同一订阅的其他人留出容量。

## 了解提示工程技术

我们首先在聊天操场中探索一些提示工程技术。

1. 在“操场”部分，选择“聊天”页面********。 “**聊天**”操场页面由一排按钮和两个主要面板组成（可能从右到左水平排列，也可能从上到下垂直排列，具体取决于屏幕分辨率）：
    - **配置** - 用于选择部署、定义系统消息并设置用于与部署交互的参数。
    - ****“聊天会话”- 用于提交聊天消息和查看响应。
2. 在“**部署**”下，确保已选择 gpt-35-turbo-16k 模型部署。
1. 查看默认的“**系统消息**”，该消息应是“*你是帮助人们查找信息的 AI 助手*”。
4. **** 在“聊天会话”中提交以下查询：

    ```prompt
    What kind of article is this?
    ---
    Severe drought likely in California
    
    Millions of California residents are bracing for less water and dry lawns as drought threatens to leave a large swath of the region with a growing water shortage.
    
    In a remarkable indication of drought severity, officials in Southern California have declared a first-of-its-kind action limiting outdoor water use to one day a week for nearly 8 million residents.
    
    Much remains to be determined about how daily life will change as people adjust to a drier normal. But officials are warning the situation is dire and could lead to even more severe limits later in the year.
    ```

    响应提供了文章的说明。 但假设你需要更具体的文章分类格式。

5. 在“**设置**”部分中，将系统消息更改为 `You are a news aggregator that categorizes news articles.`

6. 在新系统消息下，选择“**添加部分**”按钮，然后选择“**示例**”。 然后，添加以下示例。

    **用户**:
    
    ```prompt
    What kind of article is this?
    ---
    New York Baseballers Wins Big Against Chicago
    
    New York Baseballers mounted a big 5-0 shutout against the Chicago Cyclones last night, solidifying their win with a 3 run homerun late in the bottom of the 7th inning.
    
    Pitcher Mario Rogers threw 96 pitches with only two hits for New York, marking his best performance this year.
    
    The Chicago Cyclones' two hits came in the 2nd and the 5th innings but were unable to get the runner home to score.
    ```
    
    **助手：**
    
    ```prompt
    Sports
      ```

7. 添加另一个包含以下文本的示例。

    **用户：**
    
    ```prompt
    Categorize this article:
    ---
    Joyous moments at the Oscars
    
    The Oscars this past week where quite something!
    
    Though a certain scandal might have stolen the show, this year's Academy Awards were full of moments that filled us with joy and even moved us to tears.
    These actors and actresses delivered some truly emotional performances, along with some great laughs, to get us through the winter.
    
    From Robin Kline's history-making win to a full performance by none other than Casey Jensen herself, don't miss tomorrows rerun of all the festivities.
    ```
    
    **助手：**
    
    ```prompt
    Entertainment
    ```

8. 使用“**配置**”部分顶部的“**应用更改**”按钮保存所做的更改。

9. **** 在“聊天会话”部分中，重新提交以下提示：

    ```prompt
    What kind of article is this?
    ---
    Severe drought likely in California
    
    Millions of California residents are bracing for less water and dry lawns as drought threatens to leave a large swath of the region with a growing water shortage.
    
    In a remarkable indication of drought severity, officials in Southern California have declared a first-of-its-kind action limiting outdoor water use to one day a week for nearly 8 million residents.
    
    Much remains to be determined about how daily life will change as people adjust to a drier normal. But officials are warning the situation is dire and could lead to even more severe limits later in the year.
    ```

    更具体的系统消息和预期查询和响应的一些示例的组合可以生成格式一致的结果。

10. 将系统消息更改回默认模板，它应是不包含示例的 `You are an AI assistant that helps people find information.`。 然后应用更改。

11. **** 在“聊天会话”部分中，提交以下提示：

    ```prompt
    # 1. Create a list of animals
    # 2. Create a list of whimsical names for those animals
    # 3. Combine them randomly into a list of 25 animal and name pairs
    ```

    模型可能会用一个答案进行响应以满足提示，拆分为编号列表。 这是一个适当的响应，但假设你实际上是希望模型编写执行所描述任务的 Python 程序，该怎么办？

12. 将系统消息更改为 `You are a coding assistant helping write python code.` 并应用更改。
13. 将以下提示重新提交到模型：

    ```
    # 1. Create a list of animals
    # 2. Create a list of whimsical names for those animals
    # 3. Combine them randomly into a list of 25 animal and name pairs
    ```

    模型应使用 python 代码正确响应，以执行注释所请求的操作。

## 准备在 Visual Studio Code 中开发应用

现在，我们来探讨如何在使用 Azure OpenAI 服务 SDK 的应用中使用提示工程。 你将使用 Visual Studio Code 开发应用。 应用程序的代码文件已在 GitHub repo 中提供。

> **提示**：如果已克隆 **mslearn-openai** 存储库，请在 Visual Studio Code 中打开它。 否则，请按照以下步骤将其克隆到开发环境中。

1. 启动 Visual Studio Code。
2. 打开面板 (SHIFT+CTRL+P) 并运行“**Git：克隆**”命令，以将 `https://github.com/MicrosoftLearning/mslearn-openai` 存储库克隆到本地文件夹（任意文件夹均可）。
3. 克隆存储库后，在 Visual Studio Code 中打开文件夹。

    > **注意**：如果 Visual Studio Code 显示一条弹出消息，提示你信任打开的代码，请单击弹出窗口中的“是，我信任该作者”选项****。

4. 等待其他文件安装完毕，以支持存储库中的 C# 代码项目。

    > **注意**：如果系统提示你添加生成和调试所需的资产，请选择**以后再说**。

## 配置应用程序

C# 和 Python 的应用程序都已提供，并且这两个应用具有相同的功能。 首先，你将通过异步 API 调用使用 Azure OpenAI 资源完成要启用的应用程序的一些关键部件。

1. 在 Visual Studio Code 的“资源管理器”窗格中，浏览到“Labfiles/03-prompt-engineering”文件夹，然后根据语言首选项展开“CSharp”文件夹或“Python”文件夹****************。 每个文件夹都包含要集成 Azure OpenAI 功能的应用程序的特定语言文件。
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
    - 为模型部署指定的**部署名称**（可在 Azure AI Studio 的“**部署**”页中找到）。
5. 保存此配置文件。

## 添加代码以使用 Azure OpenAI 服务

现在，你已准备好使用 Azure OpenAI SDK 来使用已部署的模型。

1. ****************** 在“资源管理器”窗格的“CSharp”和“Python”文件夹中，打开首选语言的代码文件，并将注释“添加 Azure OpenAI 包”替换为代码以添加 Azure OpenAI SDK 库：

    **C#** ：Program.cs

    ```csharp
    // Add Azure OpenAI package
    using Azure.AI.OpenAI;
    ```

    **Python**：prompt-engineering.py

    ```python
    # Add Azure OpenAI package
    from openai import AsyncAzureOpenAI
    ```

2. 在代码文件中，找到注释“配置 Azure OpenAI 客户端”******，并添加代码以配置 Azure OpenAI 客户端：

    **C#** ：Program.cs

    ```csharp
    // Configure the Azure OpenAI client
    OpenAIClient client = new OpenAIClient(new Uri(oaiEndpoint), new AzureKeyCredential(oaiKey));
    ```

    **Python**：prompt-engineering.py

    ```python
    # Configure the Azure OpenAI client
    client = AsyncAzureOpenAI(
        azure_endpoint = azure_oai_endpoint, 
        api_key=azure_oai_key,  
        api_version="2024-02-15-preview"
        )
    ```

3. ****** 在调用 Azure OpenAI 模型的函数中，在注释“设置请求格式并将其发送到模型”下，添加代码来设置请求格式并将其发送到模型。

    **C#** ：Program.cs

    ```csharp
    // Format and send the request to the model
    var chatCompletionsOptions = new ChatCompletionsOptions()
    {
        Messages =
        {
            new ChatRequestSystemMessage(systemMessage),
            new ChatRequestUserMessage(userMessage)
        },
        Temperature = 0.7f,
        MaxTokens = 800,
        DeploymentName = oaiDeploymentName
    };
    
    // Get response from Azure OpenAI
    Response<ChatCompletions> response = await client.GetChatCompletionsAsync(chatCompletionsOptions);
    ```

    **Python**：prompt-engineering.py

    ```python
    # Format and send the request to the model
    messages =[
        {"role": "system", "content": system_message},
        {"role": "user", "content": user_message},
    ]
    
    print("\nSending request to Azure OpenAI model...\n")

    # Call the Azure OpenAI model
    response = await client.chat.completions.create(
        model=model,
        messages=messages,
        temperature=0.7,
        max_tokens=800
    )
    ```

4. 保存代码文件中的更改。

## 运行应用程序

配置应用后，请运行应用以将请求发送到模型并观察响应。 你会注意到，不同选项之间的唯一区别是提示的内容，所有其他参数（如标记计数和温度）对于每个请求保持不变。

1. 在首选语言的文件夹中，在 Visual Studio Code 中打开 `system.txt`。 对于每个交互，你将在此文件中输入“**系统消息**”并将其保存。 每次迭代都会先暂停，以便更改系统消息。
1. 在交互式终端窗格中，确保文件夹上下文是首选语言的文件夹。 然后，输入以下命令来创建应用程序。

    - **C#** ：`dotnet run`
    - **Python**：`python prompt-engineering.py`

    > **提示**：可以使用 **终端工具栏中的“最大化面板大小** ”图标**^** 查看更多控制台文本。

1. 对于第一次迭代，请输入以下提示：

    **系统消息**

    ```prompt
    You are an AI assistant
    ```

    **用户消息：**

    ```prompt
    Write an intro for a new wildlife Rescue
    ```

1. 观察输出。 AI 模型可能会对野生动物救援生成良好的通用介绍。
1. 接下来，输入以下提示，以指定响应的格式：

    **系统消息**

    ```prompt
    You are an AI assistant helping to write emails
    ```

    **用户消息：**

    ```prompt
    Write a promotional email for a new wildlife rescue, including the following: 
    - Rescue name is Contoso 
    - It specializes in elephants 
    - Call for donations to be given at our website
    ```

1. 观察输出。 这一次，你可能会看到包含特定动物的电子邮件格式，以及捐款呼吁。
1. 接下来，输入以下提示，以额外指定内容：

    **系统消息**

    ```prompt
    You are an AI assistant helping to write emails
    ```

    **用户消息：**

    ```prompt
    Write a promotional email for a new wildlife rescue, including the following: 
    - Rescue name is Contoso 
    - It specializes in elephants, as well as zebras and giraffes 
    - Call for donations to be given at our website 
    \n Include a list of the current animals we have at our rescue after the signature, in the form of a table. These animals include elephants, zebras, gorillas, lizards, and jackrabbits.
    ```

1. 观察输出，并查看电子邮件是如何根据明确的说明更改的。
1. 接下来，输入以下提示，我们会在此处将有关语气的详细信息添加到系统消息：

    **系统消息**

    ```prompt
    You are an AI assistant that helps write promotional emails to generate interest in a new business. Your tone is light, chit-chat oriented and you always include at least two jokes.
    ```

    **用户消息：**

    ```prompt
    Write a promotional email for a new wildlife rescue, including the following: 
    - Rescue name is Contoso 
    - It specializes in elephants, as well as zebras and giraffes 
    - Call for donations to be given at our website 
    \n Include a list of the current animals we have at our rescue after the signature, in the form of a table. These animals include elephants, zebras, gorillas, lizards, and jackrabbits.
    ```

1. 观察输出。 这一次，你可能会看到采用类似格式的电子邮件，但语气要随意得多。 你甚至可能会看到笑话！
1. 对于最终迭代，我们将偏离电子邮件生成并探索 *锚定上下文*。 你在此处提供了简单的系统消息，并更改应用，以在用户提示开始时提供锚定上下文。 然后，应用将追加用户输入，并从锚定上下文中提取信息以回答用户提示。
1. 打开文件 `grounding.txt` 并快速阅读要插入的锚定上下文。
1. 在应用中的注释“设置请求格式并将其发送到模型”之后和任何现有代码之前******，添加以下从 `grounding.txt` 读取文本的代码片段，以使用锚定文本增强用户提示。

    **C#** ：Program.cs

    ```csharp
    // Format and send the request to the model
    Console.WriteLine("\nAdding grounding context from grounding.txt");
    string groundingText = System.IO.File.ReadAllText("grounding.txt");
    userMessage = groundingText + userMessage;
    ```

    **Python**：prompt-engineering.py

    ```python
    # Format and send the request to the model
    print("\nAdding grounding context from grounding.txt")
    grounding_text = open(file="grounding.txt", encoding="utf8").read().strip()
    user_message = grounding_text + user_message
    ```

1. 保存文件并重新运行应用。
1. 输入以下提示（仍然输入 **系统消息** 并保存在 `system.txt` 中）。

    **系统消息**

    ```prompt
    You're an AI assistant who helps people find information. You'll provide answers from the text provided in the prompt, and respond concisely.
    ```

    **用户消息：**

    ```prompt
    What animal is the favorite of children at Contoso?
    ```

> **提示**：如果想要查看 Azure OpenAI 的完整响应，可以将 **printFullResponse** 变量设置为 `True`，然后重新运行应用。

## 清理

使用完 Azure OpenAI 资源后，请记得在位于 `https://portal.azure.com` 的 **Azure 门户** 中删除部署或整个资源。
