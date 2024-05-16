---
lab:
  title: 在应用中使用 Azure OpenAI SDK
---

# 在应用中使用 Azure OpenAI API

借助 Azure OpenAI 服务，开发人员可以创建擅长理解自然人类语言的聊天机器人、语言模型和其他应用程序。 可以通过 Azure OpenAI 获取经过预先训练的 AI 模型，以及一套用于自定义和微调这些模型的 API 和工具，以满足应用程序的特定要求。 在本练习中，你将了解如何在 Azure OpenAI 中部署模型，并将其用于自己的应用程序中。

在本练习的方案中，你将担任软件开发人员的职位，负责实现可以使用生成式 AI 来帮助提供徒步旅行建议的应用。 练习中使用的技术可以应用于任何想要使用 Azure OpenAI API 的应用。

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

Azure OpenAI 提供了一个名为 Azure OpenAI Studio 的基于 Web 的门户，可用于部署、管理和探索模型。 你将使用 Azure OpenAI Studio 部署模型，开始探索 Azure OpenAI。

1. 在 Azure OpenAI 资源的“概述”**** 页上，使用“转到 Azure OpenAI Studio”**** 按钮在新的浏览器选项卡中打开 Azure OpenAI Studio。
2. 在 Azure OpenAI Studio 中的“部署”**** 页上，查看现有模型部署。 如果没有模型部署，请使用以下设置创建新的“gpt-35-turbo-16k”**** 模型部署：
    - **模型**：gpt-35-turbo-16k *（如果 16k 模型不可用，请选择 gpt-35-turbo）*
    - **模型版本**：自动更新为默认值
    - **部署名称**：*所选的唯一名称。稍后将在实验室中使用此名称。*
    - **高级选项**
        - **内容筛选器**：默认
        - **部署类型**：标准
        - **每分钟令牌速率限制**：5K\*
        - **启用动态配额**：已启用

    > \*每分钟 5,000 个令牌的速率限制足以完成此练习，同时也为使用同一订阅的其他人留出容量。

## 准备在 Visual Studio Code 中开发应用

你将使用 Visual Studio Code 开发 Azure OpenAI 应用。 你的应用的代码文件已在 GitHub 存储库中提供。

> **提示**：如果已克隆 **mslearn-openai** 存储库，请在 Visual Studio Code 中打开它。 否则，请按照以下步骤将其克隆到开发环境中。

1. 启动 Visual Studio Code。
2. 打开面板 (SHIFT+CTRL+P) 并运行“**Git：克隆**”命令，以将 `https://github.com/MicrosoftLearning/mslearn-openai` 存储库克隆到本地文件夹（任意文件夹均可）。
3. 克隆存储库后，在 Visual Studio Code 中打开文件夹。

    > **注意**：如果 Visual Studio Code 显示一条弹出消息，提示你信任打开的代码，请单击弹出窗口中的“是，我信任该作者”选项****。

4. 等待其他文件安装完毕，以支持存储库中的 C# 代码项目。

    > **注意**：如果系统提示你添加生成和调试所需的资产，请选择**以后再说**。

## 配置应用程序

已提供适用于 C# 和 Python 的应用程序。 这两个应用具有相同的功能。 首先，你将使用 Azure OpenAI 资源完成要启用的应用程序的一些关键部件。

1. 在 Visual Studio Code 的“资源管理器”窗格中，浏览到“Labfiles/02-azure-openai-api”文件夹，然后根据语言首选项展开“CSharp”文件夹或“Python”文件夹****************。 每个文件夹都包含要在其中集成 Azure OpenAI 功能的应用的语言特定文件。
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
5. 保存此配置文件。

## 添加代码以使用 Azure OpenAI 服务

现在，你已准备好使用 Azure OpenAI SDK 来使用已部署的模型。

1. ****************** 在“资源管理器”窗格的“CSharp”和“Python”文件夹中，打开首选语言的代码文件，并将注释“添加 Azure OpenAI 包”替换为代码以添加 Azure OpenAI SDK 库：

    **C#** ：Program.cs

    ```csharp
    // Add Azure OpenAI package
    using Azure.AI.OpenAI;
    ```
    
    **Python**：test-openai-model.py
    
    ```python
    # Add Azure OpenAI package
    from openai import AzureOpenAI
    ```

1. 在语言的应用程序代码中，将注释“初始化 Azure OpenAI 客户端...”替换为以下代码来初始化客户端并定义系统消息******。

    **C#** ：Program.cs

    ```csharp
    // Initialize the Azure OpenAI client
    OpenAIClient client = new OpenAIClient(new Uri(oaiEndpoint), new AzureKeyCredential(oaiKey));
    
    // System message to provide context to the model
    string systemMessage = "I am a hiking enthusiast named Forest who helps people discover hikes in their area. If no area is specified, I will default to near Rainier National Park. I will then provide three suggestions for nearby hikes that vary in length. I will also share an interesting fact about the local nature on the hikes when making a recommendation.";
    ```

    **Python**：test-openai-model.py

    ```python
    # Initialize the Azure OpenAI client
    client = AzureOpenAI(
            azure_endpoint = azure_oai_endpoint, 
            api_key=azure_oai_key,  
            api_version="2024-02-15-preview"
            )
    
    # Create a system message
    system_message = """I am a hiking enthusiast named Forest who helps people discover hikes in their area. 
        If no area is specified, I will default to near Rainier National Park. 
        I will then provide three suggestions for nearby hikes that vary in length. 
        I will also share an interesting fact about the local nature on the hikes when making a recommendation.
        """
    ```

1. ****** 将注释“添加代码以发送请求...”替换为生成请求所需的代码；指定模型的各种参数，例如 `messages` 和 `temperature`。

    **C#** ：Program.cs

    ```csharp
    // Add code to send request...
    // Build completion options object
    ChatCompletionsOptions chatCompletionsOptions = new ChatCompletionsOptions()
    {
        Messages =
        {
            new ChatRequestSystemMessage(systemMessage),
            new ChatRequestUserMessage(inputText),
        },
        MaxTokens = 400,
        Temperature = 0.7f,
        DeploymentName = oaiDeploymentName
    };

    // Send request to Azure OpenAI model
    ChatCompletions response = client.GetChatCompletions(chatCompletionsOptions);

    // Print the response
    string completion = response.Choices[0].Message.Content;
    Console.WriteLine("Response: " + completion + "\n");
    ```

    **Python**：test-openai-model.py

    ```python
    # Add code to send request...
    # Send request to Azure OpenAI model
    response = client.chat.completions.create(
        model=azure_oai_deployment,
        temperature=0.7,
        max_tokens=400,
        messages=[
            {"role": "system", "content": system_message},
            {"role": "user", "content": input_text}
        ]
    )
    generated_text = response.choices[0].message.content

    # Print the response
    print("Response: " + generated_text + "\n")
    ```

1. 保存对文件所做的更改。

## 测试应用程序

配置应用后，请运行应用以将请求发送到模型并观察响应。

1. 在交互式终端窗格中，确保文件夹上下文是首选语言的文件夹。 然后，输入以下命令来创建应用程序。

    - **C#** ：`dotnet run`
    - **Python**：`python test-openai-model.py`

    > **提示**：可以使用 **终端工具栏中的“最大化面板大小** ”图标**^** 查看更多控制台文本。

1. 当系统提示时，输入文本 `What hike should I do near Rainier?`。
1. 观察输出，并注意响应会遵循添加到 *消息* 数组的系统消息中提供的准则。
1. 提供提示 `Where should I hike near Boise? I'm looking for something of easy difficulty, between 2 to 3 miles, with moderate elevation gain.` 并观察输出。
1. 在首选语言的代码文件中，将请求中的 *温度* 参数值更改为 **1.0** 并保存该文件。
1. 使用上述提示再次运行应用程序，并观察输出。

由于随机性增加，提高温度通常会导致响应发生变化，即使提供相同的文本也是如此。 可以多次运行应用程序，以查看输出可能会发生怎样的变化。 尝试在使用相同输入的情况下使用不同的 Temperature 值来获取输出。

## 维护对话历史记录

在大多数实际应用程序中，引用对话前面部分的能力有助于与 AI 代理进行更真实的交互。 Azure OpenAI API 在设计上是无状态的，但通过在提示中提供对话历史记录，AI 模型能够引用过去的消息。

1. 再次运行应用并提供提示 `Where is a good hike near Boise?`。
1. 观察输出，然后提示 `How difficult is the second hike you suggested?`。
1. 来自模型的响应可能指示无法理解你引用的徒步旅行。 要解决此问题，我们可以支持模型获取过去的对话消息作为参考。
1. 在应用程序中，我们需要将上一个输入和相应添加到将来要发送的提示。 在 **系统消息** 的定义下，添加以下代码。

    **C#** ：Program.cs

    ```csharp
    // Initialize messages list
    var messagesList = new List<ChatRequestMessage>()
    {
        new ChatRequestSystemMessage(systemMessage),
    };
    ```

    **Python**：test-openai-model.py

    ```python
    # Initialize messages array
    messages_array = [{"role": "system", "content": system_message}]
    ```

1. ****** 在注释“添加代码以发送请求...”下，使用以下代码将注释中的所有代码替换为 **while** 循环的末尾，然后保存该文件。 代码大致相同，但现在使用消息数组来存储对话历史记录。

    **C#** ：Program.cs

    ```csharp
    // Add code to send request...
    // Build completion options object
    messagesList.Add(new ChatRequestUserMessage(inputText));

    ChatCompletionsOptions chatCompletionsOptions = new ChatCompletionsOptions()
    {
        MaxTokens = 1200,
        Temperature = 0.7f,
        DeploymentName = oaiDeploymentName
    };

    // Add messages to the completion options
    foreach (ChatRequestMessage chatMessage in messagesList)
    {
        chatCompletionsOptions.Messages.Add(chatMessage);
    }

    // Send request to Azure OpenAI model
    ChatCompletions response = client.GetChatCompletions(chatCompletionsOptions);

    // Return the response
    string completion = response.Choices[0].Message.Content;

    // Add generated text to messages list
    messagesList.Add(new ChatRequestAssistantMessage(completion));

    Console.WriteLine("Response: " + completion + "\n");
    ```

    **Python**：test-openai-model.py

    ```python
    # Add code to send request...
    # Send request to Azure OpenAI model
    messages_array.append({"role": "user", "content": input_text})

    response = client.chat.completions.create(
        model=azure_oai_deployment,
        temperature=0.7,
        max_tokens=1200,
        messages=messages_array
    )
    generated_text = response.choices[0].message.content
    # Add generated text to messages array
    messages_array.append({"role": "assistant", "content": generated_text})

    # Print generated text
    print("Summary: " + generated_text + "\n")
    ```

1. 保存文件。 在添加的代码中，请注意，我们现在将上一个输入和响应追加到提示数组，这会支持理解对话的历史记录。
1. 在终端窗格中，输入以下命令来运行应用程序。

    - **C#** ：`dotnet run`
    - **Python**：`python test-openai-model.py`

1. 再次运行应用并提供提示 `Where is a good hike near Boise?`。
1. 观察输出，然后提示 `How difficult is the second hike you suggested?`。
1. 你可能会收到关于模型建议的第二个徒步旅行的响应，这提供了一个更真实的对话。 可以提出引用先前答案的其他后续问题，每次历史记录都会提供上下文以便模型给出回答。

    > **提示**：令牌计数仅设置为 1200，因此，如果对话持续时间太长，应用程序将耗尽可用令牌，从而导致提示不完整。 在生产用途中，将历史记录长度限制为最新的输入和响应将有助于控制所需令牌的数量。

## 清理

使用完 Azure OpenAI 资源后，请记得在位于 `https://portal.azure.com` 的 **Azure 门户** 中删除部署或整个资源。
