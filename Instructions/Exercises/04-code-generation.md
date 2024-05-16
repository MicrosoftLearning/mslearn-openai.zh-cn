---
lab:
  title: 使用 Azure OpenAI 服务生成和改进代码
---

# 使用 Azure OpenAI 服务生成和改进代码

Azure OpenAI 服务模型可以使用自然语言提示为你生成代码、修复已完成代码中的 bug，并提供代码注释。 这些模型还可以解释和简化现有代码，以帮助你了解它的作用以及如何改进它。

在本练习的方案中，你将承担软件开发人员的角色，探索如何使用生成式 AI 更轻松高效地完成编码任务。 练习中使用的技术可以应用于其他代码文件、编程语言和用例。

该练习大约需要 25 分钟。

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

    > \* Azure OpenAI 资源受区域配额约束。 列出的区域包括本练习中使用的模型类型的默认配额。 在与其他用户共享订阅的情况下，随机选择一个区域可以降低单个区域达到配额限制的风险。 如果稍后在练习中达到配额限制，可能需要在不同的区域中创建另一个资源。

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

## 在聊天操场中生成代码

在将其用于应用之前，请检查 Azure OpenAI 如何在聊天操场中生成和解释代码。

1. 在位于 `https://oai.azure.com` 的 **Azure OpenAI Studio** 中，在“操场”部分中选择“聊天”页。******** “聊天”操场页面由三个主要部分组成****：
    - ****“设置”- 用于设置模型的响应的上下文。
    - ****“聊天会话”- 用于提交聊天消息和查看响应。
    - ****“配置”- 用于配置模型部署的设置。
2. 在“配置”**** 部分中，确保已选择模型部署。
3. **** 在“设置”区域中，将系统消息设置为 `You are a programming assistant helping write code` 并应用更改。
4. **** 在“聊天会话”中提交以下查询：

    ```
    Write a function in python that takes a character and a string as input, and returns how many times the character appears in the string
    ```

    模型通常会响应一个函数，并说明该函数的作用和调用方式。

5. 接下来，发送提示 `Do the same thing, but this time write it in C#`。

    模型响应的内容可能与第一次非常相似，但这次是用 C# 编码。 可以再次对其发出请求以获取你选择的其他语言，或获取函数来完成其他任务，例如反转输入字符串。

6. 接下来，我们将探讨如何使用 AI 来理解代码。 以用户消息的形式提交以下提示。

    ```
    What does the following function do?  
    ---  
    def multiply(a, b):  
        result = 0  
        negative = False  
        if a < 0 and b > 0:  
            a = -a  
            negative = True  
        elif a > 0 and b < 0:  
            b = -b  
            negative = True  
        elif a < 0 and b < 0:  
            a = -a  
            b = -b  
        while b > 0:  
            result += a  
            b -= 1      
        if negative:  
            return -result  
        else:  
            return result  
    ```

    模型应描述函数的作用，即使用循环将两个数字相乘。

7. 提交提示 `Can you simplify the function?`。

    该模型应编写函数的简化版本。

8. 提交提示：`Add some comments to the function.`

    模型将注释添加到代码。

## 准备在 Visual Studio Code 中开发应用

现在，我们来探讨如何构建使用 Azure OpenAI 服务生成代码的自定义应用。 你将使用 Visual Studio Code 开发应用。 应用程序的代码文件已在 GitHub repo 中提供。

> **提示**：如果已克隆 **mslearn-openai** 存储库，请在 Visual Studio Code 中打开它。 否则，请按照以下步骤将其克隆到开发环境中。

1. 启动 Visual Studio Code。
2. 打开面板 (SHIFT+CTRL+P) 并运行“**Git：克隆**”命令，以将 `https://github.com/MicrosoftLearning/mslearn-openai` 存储库克隆到本地文件夹（任意文件夹均可）。
3. 克隆存储库后，在 Visual Studio Code 中打开文件夹。

    > **注意**：如果 Visual Studio Code 显示一条弹出消息，提示你信任打开的代码，请单击弹出窗口中的“是，我信任该作者”选项****。

4. 等待其他文件安装完毕，以支持存储库中的 C# 代码项目。

    > **注意**：如果系统提示你添加生成和调试所需的资产，请选择**以后再说**。

## 配置应用程序

已提供适用于 C# 和 Python 的应用程序，以及用于测试汇总情况的示例文本文件。 这两个应用具有相同的功能。 首先，你将使用 Azure OpenAI 资源完成要启用的应用程序的一些关键部件。

1. 在 Visual Studio Code 的“资源管理器”窗格中，浏览到“Labfiles/04-code-generation”文件夹，然后根据语言首选项展开“CSharp”文件夹或“Python”文件夹****************。 每个文件夹都包含要集成 Azure OpenAI 功能的应用程序的特定语言文件。
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

## 添加代码以使用 Azure OpenAI 服务模型

现在，你已准备好使用 Azure OpenAI SDK 来使用已部署的模型。

1. 在“资源管理器”窗格中****，在“CSharp”或“Python”文件夹中，打开首选语言的代码文件********。 ****** 在调用 Azure OpenAI 模型的函数中，在注释“设置请求格式并将其发送到模型”下，添加代码来设置请求格式并将其发送到模型。

    **C#** ：Program.cs

    ```csharp
    // Format and send the request to the model
    var chatCompletionsOptions = new ChatCompletionsOptions()
    {
        Messages =
        {
            new ChatRequestSystemMessage(systemPrompt),
            new ChatRequestUserMessage(userPrompt)
        },
        Temperature = 0.7f,
        MaxTokens = 1000,
        DeploymentName = oaiDeploymentName
    };

    // Get response from Azure OpenAI
    Response<ChatCompletions> response = await client.GetChatCompletionsAsync(chatCompletionsOptions);

    ChatCompletions completions = response.Value;
    string completion = completions.Choices[0].Message.Content;
    ```

    **Python**：code-generation.py

    ```python
    # Format and send the request to the model
    messages =[
        {"role": "system", "content": system_message},
        {"role": "user", "content": user_message},
    ]
    
    # Call the Azure OpenAI model
    response = client.chat.completions.create(
        model=model,
        messages=messages,
        temperature=0.7,
        max_tokens=1000
    )
    ```

4. 保存代码文件中的更改。

## 运行应用程序

现在已完成应用配置，请运行它，尝试为每个用例生成代码。 用例在应用中是有编号的，且能按任何顺序运行。

> **注意**：如果过于频繁地调用模型，部分用户可能会受到速率限制。 如果遇到有关令牌速率限制的错误，请等待一分钟，然后重试。

1. **** 在“资源管理器”窗格中，展开“Labfiles/04-code-generation/sample-code”文件夹，并查看语言的函数和 *go-fish* 应用****。 这些文件将用于应用中的任务。
2. 在交互式终端窗格中，确保文件夹上下文是首选语言的文件夹。 然后，输入以下命令来创建应用程序。

    - **C#** ：`dotnet run`
    - **Python**：`python code-generation.py`

    > **提示**：可以使用 **终端工具栏中的“最大化面板大小** ”图标**^** 查看更多控制台文本。

3. 选择选项“1”以向代码添加注释，并输入以下提示****。 请注意，其中每个任务的响应可能需要几秒钟的时间。

    ```prompt
    Add comments to the following function. Return only the commented code.\n---\n
    ```

    结果将放入 **result/app.txt** 中。 打开该文件，并将其与 **sample-code** 中的函数文件进行比较。

4. 接下来，选择选项“2”以编写同一函数的单元测试，并输入以下提示****。

    ```prompt
    Write four unit tests for the following function.\n---\n
    ```

    结果将会替换 **result/app.txt** 中的内容，并详细说明该函数的四个单元测试。

5. 接下来请选择选项 3 以修复 Go Fish 应用的 bug。 输入以下提示。

    ```prompt
    Fix the code below for an app to play Go Fish with the user. Return only the corrected code.\n---\n
    ```

    结果将会替换 **result/app.txt** 中的内容，并且应该具有非常类似的代码，并更正了一些内容。

    - **C#** ：修正第 30 行和第 59 行的内容
    - **Python**：修正第 18 行和第 31 行的内容

    如果将包含 bug 的行替换为 Azure OpenAI 的响应，则可以运行 **sample-code** 中的 Go Fish 应用。 如果在未修复的情况下运行，该应用将无法正常工作。
    
    > **注意**：请务必注意，即使此 Go Fish 应用的代码已针对某些语法进行了更正，但严格来说这并不是该游戏的准确表示形式。 如果你仔细观察，就会发现在抽牌时不会检查牌组是否为空、在玩家拿到一对牌时不从玩家手中移除对子，以及其他一些需要了解纸牌游戏才能意识到的 bug。 这是一个很好的示例，说明生成式 AI 模型在帮助代码生成方面是多么有用，但也不能完全信任它，需要由开发人员对其生成的内容进行验证。

    如果想要查看 Azure OpenAI 的完整响应，可以将 **printFullResponse** 变量设置为 `True`，然后重新运行应用。

## 清理

使用完 Azure OpenAI 资源后，请记得在位于 `https://portal.azure.com` 的 **Azure 门户** 中删除部署或整个资源。
