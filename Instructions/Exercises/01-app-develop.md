---
lab:
  title: 使用 Azure OpenAI 服务开发应用程序
---

# 使用 Azure OpenAI 服务开发应用程序

借助 Azure OpenAI 服务，开发人员可以通过使用 REST API 或特定语言 SDK 创建擅长理解人类自然语言的聊天机器人和其他应用程序。 使用这些语言模型时，开发人员如何塑造其提示会极大地影响生成式 AI 模型的响应方式。 Azure OpenAI 模型能够以清晰简洁的方式定制内容并设置内容格式。 在本练习中，你将学习如何将应用程序连接到 Azure OpenAI，并了解类似内容的不同提示如何帮助 AI 模型做出响应，以更好地满足你的要求。

在本练习的应用场景中，你将担任野生动物市场营销活动的软件开发人员角色。 你正在探索如何使用生成式 AI 来改进广告电子邮件和对可能适用于团队的文章进行分类。 练习中使用的提示工程技术同样适用于各种用例。

该练习大约需要 **30** 分钟。

## 预配 Azure OpenAI 资源

如果还没有 Azure OpenAI 资源，请在 Azure 订阅中预配 Azure OpenAI 资源。

1. 登录到 Azure 门户，地址为 ****。

1. 请使用以下设置创建 Azure OpenAI 资源：
    - 订阅****：*选择已被批准访问 Azure OpenAI 服务的 Azure 订阅*
    - **资源组**：*创建或选择资源组*
    - 区域****：从以下任何区域中进行随机选择******\*
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

接下来，你将从 CLI 部署 Azure OpenAI 模型资源。 参考此示例，将下列变量替换为上述自己的值：

```dotnetcli
az cognitiveservices account deployment create \
   -g *Your resource group* \
   -n *Name of your OpenAI service* \
   --deployment-name gpt-35-turbo \
   --model-name gpt-35-turbo \
   --model-version 0125  \
   --model-format OpenAI \
   --sku-name "Standard" \
   --sku-capacity 5
```

    > \* Sku-capacity is measured in thousands of tokens per minute. A rate limit of 5,000 tokens per minute is more than adequate to complete this exercise while leaving capacity for other people using the same subscription.

> [!NOTE]
> 如果看到有关 net7.0 框架不受支持的警告，则可以在本练习中忽略它们。

## 配置应用程序

C# 和 Python 的应用程序都已提供，并且这两个应用具有相同的功能。 首先，你将通过异步 API 调用使用 Azure OpenAI 资源完成要启用的应用程序的一些关键部件。

1. 在 Visual Studio Code 的“**资源管理器**”窗格中，浏览到“**Labfiles/01-app-develop**”文件夹，然后根据语言首选项展开“**CSharp**”文件夹或“**Python**”文件夹。 每个文件夹都包含要集成 Azure OpenAI 功能的应用程序的特定语言文件。
2. 右键单击包含代码文件的“CSharp”或“Python”文件夹，并打开集成终端。******** 然后通过运行适用于语言首选项的命令，安装 Azure OpenAI SDK 包：

    **C#：**

    ```
    dotnet add package Azure.AI.OpenAI --version 2.0.0
    ```

    **Python**：

    ```
    pip install openai==1.54.3
    ```

3. 在“资源管理器”窗格中****，在“CSharp”或“Python”文件夹中，打开首选语言的配置文件********

    - **C#** ：appsettings.json
    - **Python**：.env
    
4. 更新配置值以包括：
    - 创建的 Azure OpenAI 资源的终结点**** 和密钥****（位于 Azure 门户中 Azure OpenAI 资源的“密钥和终结点”**** 页）
    - 为模型部署指定的**部署名称**。
5. 保存此配置文件。

## 添加代码以使用 Azure OpenAI 服务

现在，你已准备好使用 Azure OpenAI SDK 来使用已部署的模型。

1. ****************** 在“资源管理器”窗格的“CSharp”和“Python”文件夹中，打开首选语言的代码文件，并将注释“添加 Azure OpenAI 包”替换为代码以添加 Azure OpenAI SDK 库：

    **C#** ：Program.cs

    ```csharp
    // Add Azure OpenAI packages
    using Azure.AI.OpenAI;
    using OpenAI.Chat;
    ```

    **Python**：application.py

    ```python
    # Add Azure OpenAI package
    from openai import AsyncAzureOpenAI
    ```

2. 在代码文件中，找到注释“配置 Azure OpenAI 客户端”******，并添加代码以配置 Azure OpenAI 客户端：

    **C#** ：Program.cs

    ```csharp
    // Configure the Azure OpenAI client
       AzureOpenAIClient azureClient = new (new Uri(oaiEndpoint), new ApiKeyCredential(oaiKey));
        ChatClient chatClient = azureClient.GetChatClient(oaiDeploymentName);
        ChatCompletion completion = chatClient.CompleteChat(
        [
        new SystemChatMessage(systemMessage),
        new UserChatMessage(userMessage),
        ]);
    ```

    **Python**：application.py

    ```python
    # Configure the Azure OpenAI client
    client = AsyncAzureOpenAI(
        azure_endpoint = azure_oai_endpoint, 
        api_key=azure_oai_key,  
        api_version="2024-02-15-preview"
        )
    ```

3. 在调用 Azure OpenAI 模型的函数中，位于注释***从 Azure OpenAI 获取响应***下方，添加代码以格式化并向模型发送请求。

    **C#** ：Program.cs

    ```csharp
    // Get response from Azure OpenAI
    Console.WriteLine($"{completion.Role}: {completion.Content[0].Text}");

    ```

    **Python**：application.py

    ```python
    # Get response from Azure OpenAI
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
    - **Python**：`python application.py`

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

    > **提示**：你可能会发现 VM 中的自动键入不能很好地处理多行提示。 如果问题出在这里，请复制整个提示，然后将其粘贴到 Visual Studio Code 中。

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

    **Python**：application.py

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
