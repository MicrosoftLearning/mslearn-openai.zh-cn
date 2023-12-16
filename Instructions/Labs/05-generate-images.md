---
lab:
  title: 使用 DALL-E 模型生成图像
---

# 使用 DALL-E 模型生成图像

Azure OpenAI 服务包括名为 DALL-E 的图像生成模型。 可以使用此模型提交描述所需图像的自然语言提示，该模型将根据你提供的内容生成原始图像。

该练习大约需要 25 分钟。

## 准备工作

需要使用一个已被批准访问 Azure OpenAI 服务（包括 DALL-E）的 Azure 订阅。 如果以前已申请访问 Azure openAI 服务，则可能需要通过提交另一个申请来获取对 DALL-E 的访问权限。

- 若要注册免费的 Azure 订阅，请访问 [https://azure.microsoft.com/free](https://azure.microsoft.com/free)。
- 若要请求访问 Azure OpenAI 服务，请访问 [https://aka.ms/oaiapply](https://aka.ms/oaiapply)。

## 预配 Azure OpenAI 资源

必须先在 Azure 订阅中预配 Azure OpenAI 资源，然后才能使用 Azure OpenAI 模型。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 请使用以下设置创建 Azure OpenAI 资源：
    - 订阅：已被批准访问 Azure OpenAI 服务的 Azure 订阅。
    - 资源组：选择现有的资源组，或者用你选择的名称新建一个。
    - **区域**：选择“EastUS”**** 作为区域
    - 名称：所选项的唯一名称。
    - 定价层：标准版 S0
3. 等待部署完成。 然后在 Azure 门户中转至部署的 Azure OpenAI 资源。
4. 导航到“密钥和终结点”页。 在此页面中，你可以检索服务的唯一终结点和身份验证密钥（稍后会需要这些信息）。

## 探索 DALL-E 操场中的图像生成

可以使用 Azure OpenAI Studio 中的 DALL-E 操场来试验图像生成。

1. 在 Azure 门户中 Azure OpenAI 资源的“概述”页上，使用“浏览”按钮在新的浏览器选项卡中打开 Azure OpenAI Studio。或者直接导航到 [Azure OpenAI Studio](https://oai.azure.com/?azure-portal=true) 。
2. 选择“DALL-E 操场”。
3. 在“提示”框中输入要生成的图像的说明。 例如“An elephant on a skateboard”（滑板上的大象）。 然后选择“生成”并查看生成的图像。

    ![Azure OpenAI Studio 中的 DALL-E 操场，其中包含生成的图像。](../media/dall-e-playground.png)

4. 修改提示以提供更具体的说明。 例如“An elephant on a skateboard in the style of Picasso”（毕加索风格的滑板上的大象）。 然后生成新图像并查看结果。

    ![Azure OpenAI Studio 中的 DALL-E 操场，其中包含两个生成的图像。](../media/dall-e-playground-new-image.png)

## 使用 REST API 生成图像

Azure OpenAI 服务提供了一个 REST API，可用于提交关于内容生成（包括 DALL-E 模型生成的图像）的提示。

### 准备应用环境

在本练习中，你将使用简单的 Python 或 Microsoft C# 应用通过调用 REST API 生成图像。 你将在 Azure 门户的 Cloud Shell 控制台界面中运行代码。

1. 在 [Azure 门户](https://portal.azure.com?azure-portal=true)中，选择页面顶部搜索框右侧的 [>_] (Cloud Shell) 按钮。 Cloud Shell 窗格将在门户底部将打开。 

    ![屏幕截图显示单击顶部搜索框右侧的图标启动 Cloud Shell。](../media/cloudshell-launch-portal.png#lightbox)

2. 首次打开 Cloud Shell 时，系统可能会提示你选择要使用的 shell 类型（Bash 或 PowerShell）。 选择 Bash。 如果未看到此选项，请跳过该步骤。  

3. 如果系统提示为 Cloud Shell 创建存储，请选择“显示高级设置”，然后选择以下设置：
    - **订阅**：你的订阅
    - Cloud Shell 区域：选择任何可用区域
    - **显示 VNET 隔离设置**：未选中
    - 资源组：使用预配了 Azure OpenAI 资源的现有资源组
    - 存储帐户：新建具有唯一名称的存储帐户
    - 文件共享：新建具有唯一名称的文件共享

    等待存储创建完毕，此过程大约需要一分钟。

    > **注意**：如果已在 Azure 订阅中设置了 Cloud Shell，则可能需要使用 ⚙️ 菜单中的“重置用户设置”选项，以确保已安装最新版本的 Python 和 .NET Framework。

4. 请确保 Cloud Shell 窗格左上角指示的 shell 类型为 Bash。 如果是 PowerShell，请使用下拉菜单切换到 Bash。

5. 终端启动后，输入以下命令下载要使用的应用程序代码。

    ```bash
    rm -r azure-openai -f
    git clone https://github.com/MicrosoftLearning/mslearn-openai azure-openai
    ```

    文件将下载到名为“azure-openai”的文件夹中。 已提供适用于 C# 和 Python 的应用程序。 这两个应用具有相同的功能。

6. 通过运行相应的命令，导航到要使用的语言的文件夹。

    **Python**

    ```bash
    cd azure-openai/Labfiles/05-image-generation/Python
    ```

    **C#**

    ```bash
    cd azure-openai/Labfiles/05-image-generation/CSharp
    ```

7. 使用以下命令打开内置的代码编辑器，并查看要使用的代码文件。

    ```bash
    code .
    ```

    > **提示**：有关使用其在 Azure Cloud Shell 环境中处理文件的更多详细信息，请参阅 [Azure Cloud Shell 代码编辑器文档](https://learn.microsoft.com/azure/cloud-shell/using-cloud-shell-editor)。

### 配置应用程序

应用程序使用配置文件来存储连接到 Azure OpenAI 服务帐户所需的详细信息。

1. 在代码编辑器中，根据语言首选项选择应用的配置文件。

    - C#：`appsettings.json`
    - Python： `.env`
    
2. 更新配置值以包括 Azure OpenAI 服务的 Endpoint 和 Key1 值，然后保存文件 。

    > **提示**：可以调整 Cloud Shell 窗格顶部的拆分情况以查看 Azure 门户，并从 Azure OpenAI 服务的“密钥和终结点”页获取终结点和密钥值。

3. 如果使用 Python，则还需要安装用于读取配置文件的 python-dotenv 包 。 在控制台提示窗格中，确保当前文件夹为 ~/azure-openai/Labfiles/05-image-generation/Python。 然后输入此命令：

    ```bash
    pip install python-dotenv
    ```

### 查看应用程序代码

现在你可以浏览用于调用 REST API 和生成图像的代码。

1. 在代码编辑器窗格中，选择应用程序的 main 代码文件：

    - C#：`Program.cs`
    - Python： `generate-image.py`

2. 查看文件包含的代码，并留意以下关键功能：
    - 该代码向服务的终结点发出 https 请求，且标头中包括服务的密钥。 这两个值都是从配置文件获取的。
    - 该过程由<u>两</u>个 REST 请求组成：一个用于发起图像生成请求，另一个用于检索结果。
    初始请求包括以下数据：
        - 用户提供的提示，描述的是要生成的图像
        - 要生成的图像数（本例中为 1）
        - 要生成的图像的分辨率（大小）。
    - 来自初始请求的响应标头，包含 operation-location 值，该值用于后续回叫以获取结果。
    - 代码轮询回叫 URL，直到图像生成任务的状态为成功，然后提取并显示生成的图像的 URL。

### 运行应用

现在你已查看代码，接下来可以运行该代码并生成一些图像。

1. 在控制台提示窗格中，输入相应的命令以运行应用程序：

    **Python**

    ```bash
    python generate-image.py
    ```

    **C#**

    ```bash
    dotnet run
    ```

2. 出现提示时，输入图像的说明。 例如“A giraffe flying a kite”（放风筝的长颈鹿）。

3. 等待图像生成，系统随后将在控制台窗格中显示超链接。 然后选择该超链接以打开新的浏览器选项卡并查看生成的图像。

4. 关闭包含已生成图像的选项卡，然后重新运行应用以使用不同的提示生成新图像。

## 清理

使用完 Azure OpenAI 资源后，请记得在 [Azure 门户](https://portal.azure.com/?azure-portal=true)中删除资源。
