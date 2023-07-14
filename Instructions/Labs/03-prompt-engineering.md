---
lab:
  title: 在应用中利用提示工程
---

# 在应用中利用提示工程

借助 Azure OpenAI 服务，开发人员可以创建擅长理解自然人类语言的聊天机器人、语言模型和其他应用程序。 可以通过 Azure OpenAI 获取经过预先训练的 AI 模型，以及一套用于自定义和微调这些模型的 API 和工具，以满足应用程序的特定要求。 在本练习中，你将了解如何在 Azure OpenAI 中部署模型，并在自己的应用程序中使用模型来汇总文本。

使用 Azure OpenAI 服务时，开发人员如何塑造其提示会极大地影响生成式 AI 模型的响应方式。 Azure OpenAI 模型能够以清晰简洁的方式定制内容并设置内容格式。 在本练习中，你将了解针对类似内容的不同提示如何帮助塑造 AI 模型的响应，以更好地满足你的要求。

假设你正在尝试发送新的野生动物救援信息，并希望从生成式 AI 模型获得帮助。

该练习大约需要 25 分钟。

## 准备工作

你将需要使用已被批准访问 Azure OpenAI 服务的 Azure 订阅。

- 若要注册免费的 Azure 订阅，请访问 [https://azure.microsoft.com/free](https://azure.microsoft.com/free)。
- 若要请求访问 Azure OpenAI 服务，请访问 [https://aka.ms/oaiapply](https://aka.ms/oaiapply)。

## 预配 Azure OpenAI 资源

必须先在 Azure 订阅中预配 Azure OpenAI 资源，然后才能使用 Azure OpenAI 模型。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 请使用以下设置创建 Azure OpenAI 资源：
    - 订阅：已被批准访问 Azure OpenAI 服务的 Azure 订阅。
    - **资源组**：使用你所选择的名称创建新资源组。
    - 区域：任选一个可用的区域。
    - 名称：所选项的唯一名称。
    - 定价层：标准版 S0
3. 等待部署完成。 然后在 Azure 门户中转至部署的 Azure OpenAI 资源。
4. 导航到“密钥和终结点”页，并将其保存到文本文件中，以供稍后使用。

## 部署模型

若要使用 Azure OpenAI API，必须先部署一个通过 Azure OpenAI Studio 使用的模型。 部署好模型后，我们将在应用中引用该模型。

1. 在 Azure OpenAI 资源的“概述”页上，使用“浏览”按钮在新的浏览器选项卡中打开 Azure OpenAI Studio。或者直接导航到 [Azure OpenAI Studio](https://oai.azure.com/?azure-portal=true) 。
2. 在 Azure OpenAI Studio 中，使用以下设置创建新部署：
    - 模型名称：gpt-35-turbo
    - 部署名称：text-turbo

> 注意：针对功能和性能间不同的权衡情况，每个 Azure OpenAI 模型都会得到相应的优化。 在本练习中，我们将使用 GPT-3 模型系列中的 3.5 Turbo 模型系列，该系列高度支持语言理解 。 本练习仅使用单个模型，但部署和使用其他部署的模型的方式是相同的。

## 在聊天操场中应用提示工程

在使用应用之前，请了解提示工程如何改进操场中的模型响应。 在第一个示例中，假设你尝试编写一个具有有趣名称的动物的 Python 应用。

1. 在 [Azure OpenAI Studio](https://oai.azure.com/?azure-portal=true) 中导航到左窗格中的聊天操场。
1. 在顶部的“助理设置”部分中，输入 `You are a helpful AI assistant` 作为系统消息。
1. 在“聊天会话”部分中输入以下提示，然后按 Enter。

    ```code
   1. Create a list of animals
   2. Create a list of whimsical names for those animals
   3. Combine them randomly into a list of 25 animal and name pairs
    ```

1. 模型可能会用一个答案进行响应以满足提示，拆分为编号列表。 这是一个很好的响应，但不是我们想要的。
1. 接下来，更新系统消息以包含说明 `You are an AI assistant helping write python code. Complete the app based on provided comments`。 单击“保存更改”
1. 将说明的格式设置为 python 注释。 将以下提示发送到模型。

    ```code
   # 1. Create a list of animals
   # 2. Create a list of whimsical names for those animals
   # 3. Combine them randomly into a list of 25 animal and name pairs
    ```

1. 模型应使用完整的 python 代码正确响应，执行注释所请求的内容。
1. 接下来，我们将看到尝试对文章进行分类时出现少量提示的影响。 返回到系统消息，然后再次输入 `You are a helpful AI assistant` 并保存更改。 这将创建新的聊天会话。
1. 将以下提示发送到模型。

    ```code
   Severe drought likely in California

   Millions of California residents are bracing for less water and dry lawns as drought threatens to leave a large swath of the region with a growing water shortage.
   
   In a remarkable indication of drought severity, officials in Southern California have declared a first-of-its-kind action limiting outdoor water use to one day a week for nearly 8 million residents.
   
   Much remains to be determined about how daily life will change as people adjust to a drier normal. But officials are warning the situation is dire and could lead to even more severe limits later in the year.
    ```

1. 响应可能是关于加州干旱的一些信息。 虽然这不是一个糟糕的响应，但它不是我们所需的分类。
1. 在系统消息附近的“助理设置”部分中，选择“添加示例”按钮 。 添加下面的示例。

    **用户：**

    ```code
   New York Baseballers Wins Big Against Chicago
   
   New York Baseballers mounted a big 5-0 shutout against the Chicago Cyclones last night, solidifying their win with a 3 run homerun late in the bottom of the 7th inning.
   
   Pitcher Mario Rogers threw 96 pitches with only two hits for New York, marking his best performance this year.
   
   The Chicago Cyclones' two hits came in the 2nd and the 5th innings, but were unable to get the runner home to score.
    ```

    **助手：**

    ```code
   Sports
    ```

1. 添加另一个包含以下文本的示例。

    **用户：**

    ```code
   Joyous moments at the Oscars

   The Oscars this past week where quite something!
   
   Though a certain scandal might have stolen the show, this year's Academy Awards were full of moments that filled us with joy and even moved us to tears.
   These actors and actresses delivered some truly emotional performances, along with some great laughs, to get us through the winter.
   
   From Robin Kline's history-making win to a full performance by none other than Casey Jensen herself, don't miss tomorrows rerun of all the festivities.
    ```

    **助手：**

    ```code
   Entertainment
    ```

1. 将已更改内容保存到助手设置，并发送关于加州干旱的相同提示，为了方便起见再次在此处提供。

    ```code
   Severe drought likely in California

   Millions of California residents are bracing for less water and dry lawns as drought threatens to leave a large swath of the region with a growing water shortage.
   
   In a remarkable indication of drought severity, officials in Southern California have declared a first-of-its-kind action limiting outdoor water use to one day a week for nearly 8 million residents.
   
   Much remains to be determined about how daily life will change as people adjust to a drier normal. But officials are warning the situation is dire and could lead to even more severe limits later in the year.
    ```

1. 这一次，即使没有说明，模型也应该使用适当的分类做出响应。

## 在 Cloud Shell 中设置应用程序

为了演示如何与 Azure OpenAI 模型集成，我们将使用在 Azure 上的 Cloud Shell 中运行的简短命令行应用程序。 打开新的浏览器选项卡以使用 Cloud Shell。

1. 在 [Azure 门户](https://portal.azure.com?azure-portal=true)中，选择页面顶部搜索框右侧的 [>_] (Cloud Shell) 按钮。 Cloud Shell 窗格将在门户底部将打开。

    ![屏幕截图显示单击顶部搜索框右侧的图标启动 Cloud Shell。](../media/cloudshell-launch-portal.png#lightbox)

2. 首次打开 Cloud Shell 时，系统可能会提示你选择要使用的 shell 类型（Bash 或 PowerShell）。 选择 Bash。 如果未看到此选项，请跳过该步骤。  

3. 如果系统提示你为 Cloud Shell 创建存储，请确保已指定订阅，然后选择“创建存储”。 等待存储创建完毕，此过程大约需要一分钟。

4. 请确保 Cloud Shell 窗格左上角指示的 shell 类型已切换到 Bash。 如果是 PowerShell，请使用下拉菜单切换到 Bash。

5. 终端启动后，输入以下命令以下载示例应用程序并将其保存到名为“`azure-openai`”的文件夹中。

    ```bash
   rm -r azure-openai -f
   git clone https://github.com/MicrosoftLearning/mslearn-openai azure-openai
    ```

6. 文件将下载到名为“azure-openai”的文件夹中。 使用以下命令导航到本练习的实验室文件。

    ```bash
   cd azure-openai/Labfiles/03-prompt-engineering
    ```

    提供了适用于 C# 和 Python 的应用程序，以及提供提示的文本文件。 这两个应用具有相同的功能。

    打开内置代码编辑器，可以看到你将在 `prompts` 中使用的提示文件。 运行以下命令，在代码编辑器中打开实验室文件。

    ```bash
   code .
    ```

## 配置应用程序

在本练习中，你将使用 Azure OpenAI 资源完成应用程序的一些关键部分以进行启用。

1. 在代码编辑器中，根据语言首选项展开 CSharp 或 Python 文件夹 。

2. 打开所选语言的配置文件。

    - C#：`appsettings.json`
    - Python： `.env`
    
3. 更新配置值，以包括创建的 Azure OpenAI 资源的终结点和密钥值，以及部署的模型名称（`text-turbo`） 。 保存文件。

4. 导航到首选语言的文件夹并安装必要的包。

    **C#**

    ```bash
   cd CSharp
   dotnet add package Azure.AI.OpenAI --version 1.0.0-beta.5
    ```

    **Python**

    ```bash
   cd Python
   pip install python-dotenv
   pip install openai
    ```

5. 导航到首选语言文件夹、选择代码文件并添加必要的库。

    **C#**

    ```csharp
   // Add Azure OpenAI package
   using Azure.AI.OpenAI;
    ```

    **Python**

    ```python
   # Add OpenAI import
   import openai
    ```

5. 打开针对你的语言的应用程序代码，并添加配置客户端所需的代码。

    **C#**

    ```csharp
   // Initialize the Azure OpenAI client
   OpenAIClient client = new OpenAIClient(new Uri(oaiEndpoint), new AzureKeyCredential(oaiKey));
    ```

    **Python**

    ```python
   # Set OpenAI configuration settings
   openai.api_type = "azure"
   openai.api_base = azure_oai_endpoint
   openai.api_version = "2023-03-15-preview"
   openai.api_key = azure_oai_key
    ```

6. 在调用 Azure OpenAI 模型的函数中，添加代码以设置格式并将请求发送到模型。

    **C#**

    ```csharp
   // Create chat completion options
   var chatCompletionsOptions = new ChatCompletionsOptions()
   {
       Messages =
       {
          new ChatMessage(ChatRole.System, systemPrompt),
          new ChatMessage(ChatRole.User, userPrompt)
       },
       Temperature = 0.7f,
       MaxTokens = 800,
   };

   // Get response from Azure OpenAI
   Response<ChatCompletions> response = await client.GetChatCompletionsAsync(
       oaiModelName,
       chatCompletionsOptions
   );

   ChatCompletions completions = response.Value;
   string completion = completions.Choices[0].Message.Content;
    ```

    **Python**

    ```python
   # Build the messages array
   messages =[
       {"role": "system", "content": system_message},
       {"role": "user", "content": user_message},
   ]

   # Call the Azure OpenAI model
   response = openai.ChatCompletion.create(
       engine=model,
       messages=messages,
       temperature=0.7,
       max_tokens=800
   )
    ```

## 运行应用程序

配置应用后，请运行应用以将请求发送到模型并观察响应。 你会注意到，不同选项之间的唯一区别是提示的内容，所有其他参数（如标记计数和温度）对于每个请求保持不变。

每个提示都显示在控制台中，因为它发送供你查看提示的差异如何产生不同的响应。

1. 在 Cloud Shell Bash 终端中，导航到首选语言的文件夹。
1. 运行应用程序，并展开终端以占用大部分浏览器窗口。

    - **C#** ：`dotnet run`
    - **Python**：`python prompt-engineering.py`

1. 对于最基本的提示，请选择选项 1。
1. 观察提示输入和生成的输出。 AI 模型可能会对野生动物救援生成良好的通用介绍。
1. 接下来，选择选项 2 以提示它提供简介电子邮件，以及有关野生动物救援的一些详细信息。
1. 观察提示输入和生成的输出。 这一次，你可能会看到包含特定动物的电子邮件格式，以及捐款呼吁。
1. 接下来，选择选项 3 以请求与上述类似的电子邮件，但其中包含包括其他动物的格式化表。
1. 观察提示输入和生成的输出。 这一次，你可能会看到一封类似的电子邮件，其中包含采用特定格式的文本（在本例中是末尾的表格），演示生成式 AI 模型在请求时如何设置输出格式。
1. 接下来，选择选项 4 以请求类似的电子邮件，但这次在系统邮件中指定不同的语气。
1. 观察提示输入和生成的输出。 这一次，你可能会看到采用类似格式的电子邮件，但语气不那么随意。 你甚至可能会看到笑话！

由于随机性增加，提高温度通常会导致响应发生变化，即使提供相同的提示也是如此。 可以使用不同的温度或 top_p 值多次运行它，以查看它如何影响对同一提示的响应。

如果想要查看 Azure OpenAI 的完整响应，可以将 `printFullResponse` 变量设置为 `True`，然后重新运行应用。

## 清理

使用完 Azure OpenAI 资源后，请记得在 [Azure 门户](https://portal.azure.com/?azure-portal=true)中删除此次部署或整个资源。
