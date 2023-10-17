---
lab:
  title: 开始使用 Azure OpenAI
---

# 开始使用 Azure OpenAI 服务

Azure OpenAI 服务将 OpenAI 开发的生成式 AI 模型引入 Azure 平台，使你能够开发功能强大的 AI 解决方案，这些解决方案受益于 Azure 云平台提供的服务的安全性、可伸缩性和集成。 在本练习中，你将了解如何通过将服务预配为 Azure 资源并使用 Azure OpenAI Studio 部署和探索 OpenAI 模型来开始使用 Azure OpenAI。

此练习大约需要 30 分钟。

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

## 部署模型

Azure OpenAI 提供了一个名为 Azure OpenAI Studio 的基于 Web 的门户，可用于部署、管理和探索模型。 你将使用 Azure OpenAI Studio 部署模型，开始探索 Azure OpenAI。

1. 在 Azure OpenAI 资源的“概述”页上，使用“转到 Azure OpenAI Studio”按钮在新的浏览器标签页中打开 Azure OpenAI Studio 。
2. 在 Azure OpenAI Studio 中，使用以下设置创建新部署：
    - 模型：gpt-35-turbo
    - 模型版本：自动更新为默认值
    - 部署名称：my-gpt-model

> 注意：Azure OpenAI 包含多个模型，每个模型针对功能和性能的不同平衡进行优化。 在本练习中，你将使用 GPT-35-Turbo 模型，这是一个很好的常规模型，用于汇总和生成自然语言和代码。 有关 Azure OpenAI 中可用模型的详细信息，请参阅 Azure OpenAI 文档中的[模型](https://learn.microsoft.com/azure/cognitive-services/openai/concepts/models)。

## 在“完成”操场中探索模型

操场是 Azure OpenAI Studio 中的有用接口，可用于试验部署的模型，而无需开发自己的客户端应用程序。

1. 在 Azure OpenAI Studio 中，在“操场”下的左窗格中，选择“完成” 。
2. 在“完成”页中，确保已选择 my-gpt-model 部署，然后在“示例”列表中选择“生成测验”   。

    汇总文本示例包含一个提示，该提示提供一些文本来告诉模型需要哪种类型的响应，并包含一些上下文信息。

3. 在页面底部，注意文本中检测到的标记数。 标记是提示的基本单位 - 实质上是文本中的单词或单词的部分。
4. 使用“生成”按钮将提示提交到模型并检索响应。

    响应由基于提示中的示例的测验组成。

5. 使用“重新生成”按钮重新提交提示，请注意，响应可能与原始响应不同。 生成式 AI 模型可以在每次调用时生成新语言。
6. 使用“查看代码”按钮查看客户端应用程序将用于提交提示的代码。 可以选择首选编程语言。 提示包含提交到模型的文本。 请求将提交到 Azure OpenAI 服务的完成 API。

## 使用聊天操场

聊天操场为 GPT 3.5 及更高版本的模型提供聊天机器人界面。 它使用 ChatCompletions API，而不是旧的完成 API。 

1. 在“操场”部分选择“聊天”页，并确保在右侧配置窗格中选择了 my-gpt-model 模型。  
2. 在“助理设置”部分的“系统消息”框中，将当前文本替换为以下语句：`The system is an AI teacher that helps people learn about AI`。 

3. 在“系统消息”框下方，单击“添加少量示例”，然后在指定的框中输入以下消息和响应： 

    - 用户：`What are different types of artificial intelligence?`
    - 助手：`There are three main types of artificial intelligence: Narrow or Weak AI (such as virtual assistants like Siri or Alexa, image recognition software, and spam filters), General or Strong AI (AI designed to be as intelligent as a human being. This type of AI does not currently exist and is purely theoretical), and Artificial Superintelligence (AI that is more intelligent than any human being and can perform tasks that are beyond human comprehension. This type of AI is also purely theoretical and has not yet been developed).`

    > 注意：使用少量示例为模型提供预期响应类型的示例。 模型将尝试在自己的响应中反映示例的语气和风格。

4. 保存更改以启动新会话并设置聊天系统的行为上下文。
5. 在页面底部的查询框中，输入文本 `What is artificial intelligence?`
6. 使用“发送”按钮提交消息并查看响应。

    > 注意：可能会收到 API 部署尚未就绪的响应。 如果是，请等待几分钟，然后重试。

7. 查看响应，然后提交以下消息以继续对话：`How is it related to machine learning?`
8. 查看响应，指出上一个交互的上下文会保留（以便模型理解“它”是指人工智能）。
9. 使用“查看代码”按钮查看交互的代码。 提示包括系统消息、用户和助手消息的少样本示例，以及到目前为止聊天会话中用户和助手消息的顺序。    

## 探索提示和参数

可以使用提示和参数来最大程度地提高生成所需响应的可能性。

1. 在“参数”窗格中，设置以下参数值：
    - 温度：0
    - 最大长度（标记）：500

2. 提交以下消息

    ```
    Write three multiple choice questions based on the following text.

    Most computer vision solutions are based on machine learning models that can be applied to visual input from cameras, videos, or images.*

    - Image classification involves training a machine learning model to classify images based on their contents. For example, in a traffic monitoring solution you might use an image classification model to classify images based on the type of vehicle they contain, such as taxis, buses, cyclists, and so on.*

    - Object detection machine learning models are trained to classify individual objects within an image, and identify their location with a bounding box. For example, a traffic monitoring solution might use object detection to identify the location of different classes of vehicle.*

    - Semantic segmentation is an advanced machine learning technique in which individual pixels in the image are classified according to the object to which they belong. For example, a traffic monitoring solution might overlay traffic images with "mask" layers to highlight different vehicles using specific colors.
    ```

3. 查看结果，其中应包含教师可用于在提示中对学生进行计算机视觉主题测试的多项选择题。 总响应应小于指定为参数的最大长度。

    观察以下有关你使用的提示和参数的内容：

    - 提示符明确指出，所需的输出应为三个多选题。
    - 参数包括“温度”，它控制响应生成包含随机性元素的程度。 提交中使用的值 0 可最大程度地减小随机性，从而生成稳定、可预测的响应。

## 探索代码生成

除了生成自然语言响应外，还可以使用 GPT 模型生成代码。

1. 在“助手设置”窗格中，选择“空示例”模板以重置系统消息。 
2. 输入系统消息：`You are a Python developer.` 并保存更改。
3. 在“聊天会话”窗格中，选择“清除聊天”以清除聊天历史记录并启动新会话。 
4. 提交以下用户消息：

    ```
    Write a Python function named Multiply that multiplies two numeric parameters.
    ```

5. 查看响应，其中应包含满足提示中要求的示例 Python 代码。

## 清理

使用完 Azure OpenAI 资源后，请记得在 [Azure 门户](https://portal.azure.com)中删除此次部署或整个资源。
