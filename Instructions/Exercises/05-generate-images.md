---
lab:
  title: 使用 DALL-E 模型生成图像
---

# 使用 DALL-E 模型生成图像

Azure OpenAI 服务包括名为 DALL-E 的图像生成模型。 可以使用此模型提交描述所需图像的自然语言提示，该模型将根据你提供的内容生成原始图像。

在本练习中，你将使用 DALL-E 版本 3 模型基于自然语言提示生成图像。

该练习大约需要 25 分钟。

## 预配 Azure OpenAI 资源

必须先在 Azure 订阅中预配 Azure OpenAI 资源，然后才能使用 Azure OpenAI 模型生成图像。 资源必须位于支持 DALL-E 模型的区域中。

1. 登录到 Azure 门户，地址为 ****。
2. 请使用以下设置创建 Azure OpenAI 资源：
    - 订阅****：*选择已被批准访问 Azure OpenAI 服务（包括 DALL-E）的 Azure 订阅*
    - **资源组**：*创建或选择资源组*
    - **区域**：*选择“美国东部”**** 或“瑞典中部”*\*****
    - **名称**：所选项的唯一名称**
    - **定价层**：标准版 S0

    > \* DALL-E 3 模型仅在 **美国东部** 和 **瑞典中部** 区域的 Azure OpenAI 服务资源中可用。

3. 等待部署完成。 然后在 Azure 门户中转至部署的 Azure OpenAI 资源。

## 探索 DALL-E 操场中的图像生成

可以使用 Azure OpenAI Studio 中的 DALL-E 操场来试验图像生成。

1. 在 Azure 门户中 Azure OpenAI 资源的“概述”页上，使用“浏览”按钮在新的浏览器选项卡中打开 Azure OpenAI Studio********。或者，直接在 `https://oai.azure.com` 导航到 [Azure OpenAI Studio ](https://oai.azure.com)。
2. 在“操场”部分中，选择“DALL-E”操场。******** 将自动创建名为 *Dalle3* 的 DALL-E 模型部署。
3. 在“提示”框中输入要生成的图像的说明。 例如，`An elephant on a skateboard`。然后选择“生成”并查看生成的图像****。

    ![Azure OpenAI Studio 中的 DALL-E 操场，其中包含生成的图像。](../media/dall-e-playground.png)

4. 修改提示以提供更具体的说明。 例如，`An elephant on a skateboard in the style of Picasso`。 然后生成新图像并查看结果。

    ![Azure OpenAI Studio 中的 DALL-E 操场，其中包含两个生成的图像。](../media/dall-e-playground-new-image.png)

## 使用 REST API 生成图像

Azure OpenAI 服务提供了一个 REST API，可用于提交关于内容生成（包括 DALL-E 模型生成的图像）的提示。

### 准备在 Visual Studio Code 中开发应用

现在，我们来探讨如何构建使用 Azure OpenAI 服务生成图像的自定义应用。 你将使用 Visual Studio Code 开发应用。 应用程序的代码文件已在 GitHub repo 中提供。

> **提示**：如果已克隆 **mslearn-openai** 存储库，请在 Visual Studio Code 中打开它。 否则，请按照以下步骤将其克隆到开发环境中。

1. 启动 Visual Studio Code。
2. 打开面板 (SHIFT+CTRL+P) 并运行“**Git：克隆**”命令，以将 `https://github.com/MicrosoftLearning/mslearn-openai` 存储库克隆到本地文件夹（任意文件夹均可）。
3. 克隆存储库后，在 Visual Studio Code 中打开文件夹。

    > **注意**：如果 Visual Studio Code 显示一条弹出消息，提示你信任打开的代码，请单击弹出窗口中的“是，我信任该作者”选项****。

4. 等待其他文件安装完毕，以支持存储库中的 C# 代码项目。

    > **注意**：如果系统提示你添加生成和调试所需的资产，请选择**以后再说**。

### 配置应用程序

已提供适用于 C# 和 Python 的应用程序。 这两个应用具有相同的功能。 首先，将 Azure OpenAI 资源的终结点和密钥添加到应用的配置文件中。

1. 在 Visual Studio Code 的“资源管理器”窗格中，浏览到“Labfiles/05-image-generation”文件夹，然后根据语言首选项展开“CSharp”文件夹或“Python”文件夹****************。 每个文件夹都包含要集成 Azure OpenAI 功能的应用程序的特定语言文件。
2. 在“资源管理器”窗格中****，在“CSharp”或“Python”文件夹中，打开首选语言的配置文件********

    - **C#** ：appsettings.json
    - **Python**：.env
    
3. 更新配置值，以包含你创建的 Azure OpenAI 资源的 **终结点** 和 **密钥**（在 Azure 门户中 Azure OpenAI 资源的“密钥和终结点”页上提供****）。
4. 保存此配置文件。

### 查看应用程序代码

现在你可以浏览用于调用 REST API 和生成图像的代码。

1. 在“资源管理器”窗格中，选择应用程序的主代码文件****：

    - C#：`Program.cs`
    - Python： `generate-image.py`

2. 查看文件包含的代码，并留意以下关键功能：
    - 该代码会向服务的终结点发出 https 请求，且标头中包括服务的密钥。 这两个值都是从配置文件获取的。
    - 请求包括一些参数，其中图像应基于的提示、要生成的图像数以及所生成图像的大小。
    - 响应包括修订后的提示，即 DALL-E 模型从用户提供的提示中推断得出以使其更具描述性的提示，以及所生成图像的 URL。

### 运行应用

现在你已查看代码，接下来可以运行该代码并生成一些图像。

1. 右键单击包含代码文件的“CSharp”或“Python”文件夹，并打开集成终端。******** 然后输入相应的命令来运行应用程序：

   **C#**
   ```
   dotnet run
   ```
   
   **Python**
   ```
   pip install requests
   python generate-image.py
   ```

3. 出现提示时，输入图像的说明。 例如“A giraffe flying a kite”（放风筝的长颈鹿）。

4. 等待图像生成，系统随后将在中断窗格中显示超链接。 然后选择该超链接以打开新的浏览器选项卡并查看生成的图像。

   > **提示**：如果应用未返回响应，请等待一分钟，然后重试。 新部署的资源最多可能需要 5 分钟才能可用。

5. 关闭包含已生成图像的浏览器选项卡，然后重新运行应用以使用不同的提示生成新图像。

## 清理

使用完 Azure OpenAI 资源后，请记得在位于 `https://portal.azure.com` 的 **Azure 门户** 中删除该资源。
