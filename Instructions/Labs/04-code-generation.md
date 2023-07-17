---
lab:
  title: 使用 Azure OpenAI 服务生成和改进代码
---

# 使用 Azure OpenAI 服务生成和改进代码

Azure OpenAI 服务模型可以使用自然语言提示为你生成代码、修复已完成代码中的 bug，并提供代码注释。 这些模型还可以解释和简化现有代码，以帮助你了解它的作用以及如何改进它。

该练习大约需要 25 分钟。

## 开始之前

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

若要使用 Azure OpenAI API 来生成代码，必须先部署一个通过 Azure OpenAI Studio 使用的模型。 部署后，我们将使用该模型和操场，并在应用中引用该模型。

1. 在 Azure OpenAI 资源的“概述”页上，使用“浏览”按钮在新的浏览器选项卡中打开 Azure OpenAI Studio。或者直接导航到 [Azure OpenAI Studio](https://oai.azure.com/?azure-portal=true) 。
2. 在 Azure OpenAI Studio 中，使用以下设置创建新部署：
    - 模型：gpt-35-turbo
    - 模型版本：使用默认版本
    - **部署名称**：35turbo

> **注意**：针对功能和性能间不同的权衡情况，每个 Azure OpenAI 模型都会得到相应的优化。 在本练习中，我们将使用 GPT-3 模型系列中的 3.5 Turbo 模型系列，该系列高度支持两种语言和代码理解 。

## 在聊天操场中生成代码

在将其用于应用之前，请检查 Azure OpenAI 如何在聊天操场中生成和解释代码。

1. 在 [Azure OpenAI Studio](https://oai.azure.com/?azure-portal=true) 中导航到左窗格中的聊天操场。
1. 在顶部的“助手设置”部分中，选择“默认”系统消息模板 。
1. 在“聊天会话”部分中输入以下提示，然后按 Enter。

    ```code
   Write a function in python that takes a character and string as input, and returns how many times that character appears in the string
    ```

1. 模型通常会响应一个函数，并说明该函数的作用和调用方式。
1. 接下来，发送提示 `Do the same thing, but this time write it in C#`。
1. 观察输出。 模型响应的内容可能与第一次非常相似，但这次是用 C# 编码。 可以再次对其发出请求以获取你选择的其他语言，或获取函数来完成其他任务，例如反转输入字符串。
1. 接下来，让我们通过这个用 Ruby 编写的随机函数示例来探索如何使用 AI 来理解代码。 以用户消息的形式发送以下提示。

    ```code
    What does the following function do?  
    ---  
    def random_func(n)
      start = [0, 1]
      (n - 2).times do
        start << start[-1] + start[-2]
      end
      start.shuffle.each do |num|
        puts num
      end
    end
    ```

1. 观察输出，该输出说明函数在自然语言中执行的操作。 尝试要求模型以你熟悉的语言重写该函数。

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
   cd azure-openai/Labfiles/04-code-generation
    ```

    已提供适用于 C# 和 Python 的应用程序，以及我们将在本实验室中使用的示例代码。

    打开内置代码编辑器，可以看到我们将在 `sample-code` 中使用的代码文件。 运行以下命令，在代码编辑器中打开实验室文件。

    ```bash
   code .
    ```

## 配置应用程序

在本练习中，你将使用 Azure OpenAI 资源完成应用程序的一些关键部分以进行启用。

1. 在代码编辑器中，展开首选语言的语言文件夹。

2. 打开所选语言的配置文件。

    - **C#** ：`appsettings.json`
    - **Python**：`.env`

3. 更新配置值，以包括创建的 Azure OpenAI 资源的终结点和密钥值，以及部署名称（`35turbo`） 。 保存文件。

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

5. 在此文件夹中选择所选语言对应的代码文件，并添加必要的库。

    **C#**

    `Program.cs`

    ```csharp
   // Add Azure OpenAI package
   using Azure.AI.OpenAI;
    ```

    **Python**

    `code-generation.py`

    ```python
   # Add OpenAI import
   import openai
    ```

6. 添加配置客户端所需的代码。

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
   openai.api_version = "2023-05-15"
   openai.api_key = azure_oai_key
    ```

7. 在调用 Azure OpenAI 模型的函数中，添加代码以设置格式并将请求发送到模型。

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
        MaxTokens = 1000,
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
       max_tokens=1000
   )
    ```

## 运行应用程序

现在已完成应用配置，请运行它，尝试为每个用例生成代码。 用例在应用中是有编号的，且能按任何顺序运行。

> **注意**：如果过于频繁地调用模型，部分用户可能会受到速率限制。 如果遇到有关令牌速率限制的错误，请等待一分钟，然后重试。

1. 在代码编辑器中展开 `sample-code` 文件夹，并简要观察适用于所选语言的函数和应用。 这些文件将用于应用中的任务。
1. 在 Cloud Shell Bash 终端中，导航到首选语言的文件夹。
1. 运行应用。

    - **C#** ：`dotnet run`
    - **Python**：`python code-generation.py`

1. 选择选项 1 以向代码添加注释。 请注意，其中每个任务的响应可能需要几秒钟的时间。
1. 结果将放入 `result/app.txt`。 打开该文件，并将其与 `sample-code` 中的函数文件进行比较。
1. 接下来请选择选项 2，为同一函数编写单元测试。
1. 结果将替换 `result/app.txt` 中的内容，并详细说明该函数的四个单元测试。
1. 接下来请选择选项 3 以修复 Go Fish 应用的 bug。
1. 结果将替换 `result/app.txt` 中的内容，且应包含非常相似的代码，并更正了一些内容。

    - **C#** ：修正第 30 行和第 59 行的内容
    - **Python**：修正第 18 行和第 31 行的内容

如果使用 Azure OpenAI 的响应替换存在 bug 的代码行，`sample-code` 中的 Go Fish 应用就能运行。 如果在未修复的情况下运行，该应用将无法正常工作。

请务必注意，即使此 Go Fish 应用的代码已针对某些语法进行了更正，但这并不是该游戏严格的准确表示形式。 如果你仔细观察，就会发现在抽牌时不会检查牌组是否为空、在玩家拿到一对牌时不从玩家手中移除对子，以及其他一些需要了解纸牌游戏才能意识到的 bug。 这是一个很好的示例，说明生成式 AI 模型在帮助代码生成方面是多么有用，但也不能完全信任它，需要由开发人员对其生成的内容进行验证。

如果想要查看 Azure OpenAI 的完整响应，可以将 `printFullResponse` 变量设置为 `True`，然后重新运行应用。

## 清理

使用完 Azure OpenAI 资源后，请记得在 [Azure 门户](https://portal.azure.com/?azure-portal=true)中删除此次部署或整个资源。
