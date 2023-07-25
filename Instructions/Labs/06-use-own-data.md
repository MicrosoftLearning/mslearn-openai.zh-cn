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
    - **资源组**：使用你所选择的名称创建新资源组。
    - 区域：任选一个可用的区域。
    - 名称：所选项的唯一名称。
    - 定价层：标准版 S0
3. 等待部署完成。 然后，在 Azure 门户中转至部署的 Azure OpenAI 资源。

## 部署模型

若要与 Azure OpenAI 聊天，必须先通过 Azure OpenAI Studio 部署一个要使用的模型。 部署后，我们将模型与操场一起使用，并使用我们的数据为其响应提供基础。

1. 在 Azure OpenAI 资源的“概述”页上，使用“浏览”按钮在新的浏览器选项卡中打开 Azure OpenAI Studio。或者直接导航到 [Azure OpenAI Studio](https://oai.azure.com/?azure-portal=true) 。
2. 在 Azure OpenAI Studio 中，使用以下设置创建新部署：
    - 模型名称：gpt-35-turbo
    - 模型版本：使用默认版本
    - 部署名称：text-turbo

## 在不添加自己的数据的情况下观察正常的聊天行为

在将 Azure OpenAI 连接到你的数据之前，先观察基础模型如何在没有任何基础数据的情况下响应查询。

1. 导航到“聊天”操场，确保在“配置”窗格中选择了你部署的 `gpt-35-turbo` 模型（如果你只有一个部署的模型，这应该是默认模型） 。
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
1. 需要创建存储帐户和 Azure 认知搜索资源。 在存储资源的下拉列表下，选择“创建新的 Azure Blob 存储资源”，并使用以下设置创建一个存储帐户。 未指定的任何内容将保留为默认值。

    - 订阅：与 Azure OpenAI 资源相同的订阅
    - 资源组：与 Azure OpenAI 资源相同的资源组
    - 存储帐户名称：输入全局唯一的名称
    - 区域：与 Azure OpenAI 资源相同的区域
    -               冗余：本地冗余存储 (LRS)

1. 创建资源后，返回到 Azure OpenAI Studio，选择“创建新的 Azure 认知搜索资源”（使用以下设置）。 未指定的任何内容将保留为默认值。

    - 订阅：与 Azure OpenAI 资源相同的订阅
    - 资源组：与 Azure OpenAI 资源相同的资源组
    - 服务名称：输入全局唯一的名称
    - 位置：与 Azure OpenAI 资源相同的位置
    - 定价层：基本

1. 等待搜索资源部署完毕，然后切换回 Azure AI Studio 并刷新页面。
1. 在“添加数据”中，为数据源输入以下值。

    - 选择数据源：上传文件
    - 选择 Azure Blob 存储资源：选择创建的存储资源
        - 出现提示时打开 CORS
    - 选择 Azure 认知搜索资源：选择创建的搜索资源
    - 输入索引名称：margiestravel
    - 选中该复选框

1. 在“上传文件”页上，上传下载的 PDF。
1. 选择“保存并关闭”，这将添加你的数据。 这可能需要几分钟时间，在此期间，你需要让窗口保持打开状态。 完成后，你将看到“助手设置”窗格中指定的数据源、搜索资源和索引。

## 与基于你的数据的模型聊天

现在，你已添加数据，请提出与之前相同的问题，查看响应有何不同。

```code
I'd like to take a trip to New York. Where should I stay?
```

```code
What are some facts about New York?
```

这次你会看到一个非常不同的响应，包括有关某些酒店的详细信息和对 Margie's Travel 的提及，以及对所提供信息的来源的引用。 如果打开响应中列出的 PDF 引用，你将看到与提供的模型相同的酒店。

尝试向它询问基础数据中包含的其他城市，如迪拜、拉斯维加斯、伦敦和旧金山。

> 注意：“添加数据”仍处于预览状态，对于此功能，可能无法始终按预期运行，例如提供对未包含在基础数据中的城市的错误引用 。

## 清理

Azure OpenAI 资源使用完毕后，请记得在 [Azure 门户](https://portal.azure.com/?azure-portal=true)中删除该资源。 务必同时包括存储帐户和搜索资源，因为这些内容会产生相对较大的成本。
