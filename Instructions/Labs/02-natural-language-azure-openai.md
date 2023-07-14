---
lab:
  title: 将 Azure OpenAI 集成到应用中
---

# 将 Azure OpenAI 集成到应用中

借助 Azure OpenAI 服务，开发人员可以创建擅长理解自然人类语言的聊天机器人、语言模型和其他应用程序。 可以通过 Azure OpenAI 获取经过预先训练的 AI 模型，以及一套用于自定义和微调这些模型的 API 和工具，以满足应用程序的特定要求。 在本练习中，你将了解如何在 Azure OpenAI 中部署模型，并在自己的应用程序中使用模型来汇总文本。

该练习大约需要 30 分钟。

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

1. 在 Azure OpenAI 资源的“概述”页上，使用“浏览”按钮在新的浏览器选项卡中打开 Azure OpenAI Studio 。
2. 在 Azure OpenAI Studio 中，使用以下设置创建新部署：
    - **模型名称**：gpt-35-turbo
    - **部署名称**：text-turbo

> **注意**：针对功能和性能间不同的权衡情况，每个 Azure OpenAI 模型都会得到相应的优化。 在本练习中，我们将使用 GPT-3 模型系列中的 3.5 Turbo 模型系列，该系列高度支持语言理解 。 本练习仅使用单个模型，但部署和使用其他部署的模型的方式是相同的。

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
   cd azure-openai/Labfiles/02-nlp-azure-openai
    ```

已提供适用于 C# 和 Python 的应用程序，以及用于测试汇总情况的示例文本文件。 这两个应用具有相同的功能。

打开内置的代码编辑器，并观察要使用模型进行汇总的文本文件（位于 `text-files/sample-text.txt`）。 运行以下命令，在代码编辑器中打开实验室文件。

```bash
code .
```

## 配置应用程序

在本练习中，你将使用 Azure OpenAI 资源完成应用程序的一些关键部分以进行启用。

1. 在代码编辑器中，根据语言首选项展开 CSharp 或 Python 文件夹 。

2. 打开所选语言的配置文件

    - C#：`appsettings.json`
    - Python： `.env`
    
3. 更新配置值，以包括创建的 Azure OpenAI 资源的终结点和密钥值，以及部署的模型名称（`text-turbo`） 。 保存文件。

4. 导航到首选语言的文件夹并安装必要的包

    **C#**

    ```bash
   cd CSharp
   dotnet add package Azure.AI.OpenAI --prerelease
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

5. 打开首选语言对应的应用程序代码，并添加生成请求所需的代码，该代码指定模型的各种参数，例如 `prompt` 和 `temperature`。

    **C#**

    ```csharp
   // Initialize the Azure OpenAI client
   OpenAIClient client = new OpenAIClient(new Uri(oaiEndpoint), new AzureKeyCredential(oaiKey));

   // Build completion options object
   ChatCompletionsOptions chatCompletionsOptions = new ChatCompletionsOptions()
   {
       Messages =
       {
          new ChatMessage(ChatRole.System, "You are a helpful assistant. Summarize the following text in 60 words or less."),
          new ChatMessage(ChatRole.User, text),
       },
       MaxTokens = 120,
       Temperature = 0.7f,
   };

   // Send request to Azure OpenAI model
   ChatCompletions response = client.GetChatCompletions(
       deploymentOrModelName: oaiModelName, 
       chatCompletionsOptions);
   string completion = response.Choices[0].Message.Content;

   Console.WriteLine("Summary: " + completion + "\n");
    ```

    **Python**

    ```python
   # Set OpenAI configuration settings
   openai.api_type = "azure"
   openai.api_base = azure_oai_endpoint
   openai.api_version = "2023-03-15-preview"
   openai.api_key = azure_oai_key

   # Send request to Azure OpenAI model
   print("Sending request for summary to Azure OpenAI endpoint...\n\n")
   response = openai.ChatCompletion.create(
       engine=azure_oai_model,
       temperature=0.7,
       max_tokens=120,
       messages=[
          {"role": "system", "content": "You are a helpful assistant. Summarize the following text in 60 words or less."},
           {"role": "user", "content": text}
       ]
   )

   print("Summary: " + response.choices[0].message.content + "\n")
    ```

## 运行应用程序

配置应用后，请运行应用以将请求发送到模型并观察响应。

1. 在 Cloud Shell Bash 终端中，导航到首选语言的文件夹。
1. 运行应用程序。

    - **C#** ：`dotnet run`
    - **Python**：`python test-openai-model.py`

1. 观察示例文本文件的汇总情况。
1. 导航到首选语言的代码文件，并将 Temperature 值更改为 `1`。 保存文件。
1. 再次运行应用程序，并观察输出。

由于随机性增加，提高 Temperature 通常会导致摘要发生变化，即使提供相同的文本也是如此。 可以多次运行应用程序，以查看输出可能会发生怎样的变化。 尝试在使用相同输入的情况下使用不同的 Temperature 值来获取输出。

## 清理

使用完 Azure OpenAI 资源后，请记得在 [Azure 门户](https://portal.azure.com?azure-portal=true)中删除此次部署或整个资源。
