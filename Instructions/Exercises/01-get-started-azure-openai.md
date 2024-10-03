---
lab:
  title: Azure OpenAI 服务入门
---

# Azure OpenAI 服务入门

Azure OpenAI 服务将 OpenAI 开发的生成式 AI 模型引入 Azure 平台，使你能够开发功能强大的 AI 解决方案，这些解决方案受益于 Azure 云平台提供的服务的安全性、可伸缩性和集成。 在本练习中，你将了解如何通过将服务预配为 Azure 资源并使用 Azure AI Studio 部署和探索生成式 AI 模型来开始使用 Azure OpenAI。

在本练习的场景中，你将扮演一名负责实施 AI 代理的软件开发人员，该代理可以使用生成式 AI 帮助营销组织改善发掘客户和宣传新产品的效率。 可将本练习中使用的方法应用于组织中期望使用生成式 AI 模型来帮助员工提高效率和生产力的任何方案。

此练习大约需要 **30** 分钟。

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

Azure 提供了一个名为 **Azure AI Studio** 的基于 Web 的门户，可用于部署、管理和探索模型。 你将通过使用 Azure OpenAI Studio 部署模型，开始探索 Azure OpenAI。

> **备注**：使用 Azure AI Studio 时，可能会显示建议你执行任务的消息框。 可以关闭这些消息框并按照本练习中的步骤进行操作。

1. 在 Azure 门户中的 Azure OpenAI 资源的“**概述**”页上，向下滚动到“**开始**”部分，然后选择转到 **AI Studio** 的按钮。
1. 在 Azure AI Studio 的左侧窗格中，选择“**部署**”页并查看现有模型部署。 如果没有模型部署，请使用以下设置创建新的“gpt-35-turbo-16k”**** 模型部署：
    - **部署名称**：你选择的唯一名称**
    - **模型**：gpt-35-turbo-16k *（如果 16k 模型不可用，请选择 gpt-35-turbo）*
    - **模型版本**：*使用默认版本*
    - **部署类型**：标准
    - **每分钟令牌速率限制**：5K\*
    - **内容筛选器**：默认
    - **启用动态配额**：已禁用

    > \*每分钟 5,000 个令牌的速率限制足以完成此练习，同时也为使用同一订阅的其他人留出容量。

## 使用聊天操场

部署模型后，可以使用它根据自然语言提示生成响应。 Azure AI Studio 中的“*聊天*”操场为 GPT 3.5 及更高版本的模型提供了聊天机器人界面。

> **注意：**“聊天”操场使用 ChatCompletions API，而不是“完成”操场使用的旧版 Completions API********。 提供“完成”操场的目的是为了与旧模型兼容。

1. 在“操场”部分，选择“聊天”页面********。 “**聊天**”操场页面由一排按钮和两个主要面板组成（可能从右到左水平排列，也可能从上到下垂直排列，具体取决于屏幕分辨率）：
    - **配置** - 用于选择部署、定义系统消息并设置用于与部署交互的参数。
    - ****“聊天会话”- 用于提交聊天消息和查看响应。
1. 在“**部署**”下，确保已选择 gpt-35-turbo-16k 模型部署。
1. 查看默认的“**系统消息**”，该消息应是“*你是帮助人们查找信息的 AI 助手*”。 系统消息包含在提交给模型的提示中，并为模型的响应提供上下文；设置有关基于模型的 AI 代理如何与用户交互的期望。
1. 在“聊天会话”面板中，输入用户查询 ****`How can I use generative AI to help me market a new product?`

    > 注意：可能会收到 API 部署尚未就绪的响应。 如果是，请等待几分钟，然后重试。

1. 查看响应，注意模型已生成与提示的查询相关的内聚自然语言答案。
1. 输入用户查询 `What skills do I need if I want to develop a solution to accomplish this?`。
1. 查看响应，注意聊天会话已保留对话上下文（因此“这”被解释为用于营销的生成式 AI 解决方案）。 这种上下文化是通过在每个连续提示提交中包含最近的对话历史记录来实现的，因此发送到模型的第二个查询的提示包含原始查询和响应以及新的用户输入。
1. 在“聊天会话”面板工具栏中，选择“清除聊天”并确认你要重启聊天会话********。
1. 输入查询 `Can you help me find resources to learn those skills?` 并查看响应，该响应应该是有效的自然语言答案，但由于之前的聊天记录已丢失，因此答案可能与查找常规技能资源相关，而不是与构建生成式 AI 营销解决方案所需的特定技能相关。

## 试着使用系统消息、提示和少样本示例

到目前为止，你已根据默认系统消息与模型进行了聊天对话。 你可以自定义系统设置，以便更好地控制模型生成的响应类型。

1. 在主工具栏中，选择“**提示示例**”，然后使用“**营销写作助手**”提示模板。
1. 查看新的系统消息，其中描述了 AI 代理应如何使用模型做出响应。
1. 在“聊天会话”面板中，输入用户查询 ****`Create an advertisement for a new scrubbing brush`。
1. 查看响应，其中应包含毛刷的广告文案。 该文案的内容可能相当宽泛但富有创意。

    在真实场景中，营销专业人员可能已经知道毛刷产品的名称，并且对广告中应强调的关键功能有一些想法。 为了从生成式 AI 模型中获得最有用的结果，用户需要设计提示以包含尽可能多的相关信息。

1. 输入提示 `Revise the advertisement for a scrubbing brush named "Scrubadub 2000", which is made of carbon fiber and reduces cleaning times by half compared to ordinary scrubbing brushes`。
1. 查看响应，其中应考虑到提供的有关毛刷产品的其他信息。

    现在，响应应该更有用，但为了更好地控制模型的输出，可以提供一个或多个少样本示例作为响应的基础**。

1. 在“**系统消息**”文本框中，展开“**添加部分**”的下拉列表，然后选择“**示例**”。 然后在指定的框中键入以下消息和响应：

    **用户**:
    
    ```prompt
    Write an advertisement for the lightweight "Ultramop" mop, which uses patented absorbent materials to clean floors.
    ```
    
    **助手：**
    
    ```prompt
    Welcome to the future of cleaning!
    
    The Ultramop makes light work of even the dirtiest of floors. Thanks to its patented absorbent materials, it ensures a brilliant shine. Just look at these features:
    - Lightweight construction, making it easy to use.
    - High absorbency, enabling you to apply lots of clean soapy water to the floor.
    - Great low price.
    
    Check out this and other products on our website at www.contoso.com.
    ```

1. 使用“应用更改”按钮保存示例并启动新会话****。
1. 在“聊天会话”部分，输入用户查询 ****`Create an advertisement for the Scrubadub 2000 - a new scrubbing brush made of carbon fiber that reduces cleaning time by half`。
1. 查看响应，它应该是“Scrubadub 2000”的新广告，该广告是基于系统设置中提供的“Ultramop”示例建模的。

## 试着使用参数

你已了解系统消息、示例和提示如何帮助优化模型返回的响应。 你还可以使用参数来控制模型行为。

1. 在“配置”面板中，选择“参数”选项卡并设置以下参数值********：
    - 最大响应****：1000
    - **温度**：1

1. 在“聊天会话”部分，使用“清除聊天”按钮重置聊天会话********。 然后输入用户查询 `Create an advertisement for a cleaning sponge` 并查看响应。 生成的广告文案应包含最多 1000 个文本标记，并包含一些创意元素 - 例如，模型可能为海绵创造了一个产品名称，并对其功能做出了一些声明。
1. 使用“清除聊天”按钮再次重置聊天会话，然后重新输入与前面相同的查询 (****`Create an advertisement for a cleaning sponge`) 并查看响应。 该响应可能与之前的响应不同。
1. 在“配置”面板中的“参数”选项卡上，将“温度”参数值更改为 0************。
1. 在“聊天会话”部分，使用“清除聊天”按钮再次重置聊天会话，然后重新输入与前面相同的查询 (********`Create an advertisement for a cleaning sponge`) 并查看响应。 这一次，响应可能不是很有创意。
1. 使用“清除聊天”按钮再次重置聊天会话，然后重新输入与前面相同的查询 (****`Create an advertisement for a cleaning sponge`) 并查看响应；该响应应该与之前的响应非常相似（甚至相同）。

    “温度”参数控制模型在生成响应时的创新程度****。 较小值会导致一致且几乎不会随机变化的响应，而较大值则鼓励模型在其输出中添加创意元素；这可能会影响响应的准确度和真实性。

## 将模型部署到 Web 应用

现在你已经在 Azure AI Studio 操场中探索了生成式 AI 模型的一些功能，接下来可以部署一个 Azure Web 应用来提供基本的 AI 代理界面，用户可以通过该界面与模型聊天。

> **备注**：Azure AI Studio 仍处于预览状态。 对于某些用户，由于工作室模板中的 bug，无法部署到 Web 应用。 如果是这种情况，请跳过本部分。

1. 在“聊天”操场页面右上角的“部署到”菜单中，选择一个新的 Web 应用************。
1. 在“部署到 Web 应用”对话框中，使用以下设置创建新的 Web 应用****：
    - **名称**：唯一名称**
    - **订阅**：*Azure 订阅*
    - 资源组****：*在其中预配了 Azure OpenAI 资源的资源组*
    - **位置**：*在其中预配了 Azure OpenAI 资源的区域*
    - 定价计划****：免费(F1) - 如果此选项不可用，请选择“基本(B1)”**
    - 在 Web 应用中启用聊天历史记录****：未选定<u></u>
    - **** 我确认 Web 应用会在帐户中使用：已选择
1. 部署新的 Web 应用并等待部署完成（可能需要 10 分钟左右）
1. 成功部署 Web 应用后，使用“聊天”操场页面右上角的按钮启动该 Web 应用****。 该应用可能需要几分钟时间才能启动完成。 如果出现提示，请接受权限请求。
1. 在 Web 应用中输入以下聊天消息：

    ```prompt
    Write an advertisement for the new "WonderWipe" cloth that attracts dust particulates and can be used to clean any household surface.
    ```

1. 查看回应。

    > **注意**：你已将模型部署到 Web 应用，但此部署不包括在操场中设置的系统设置和参数；因此响应可能不会反映操场中指定的示例**。 在实际场景中，你可以向应用程序添加逻辑来修改提示，使其包含适合你想要生成的响应类型的上下文数据。 这种自定义操作超出了本入门级练习的范围，但你可以在其他练习和产品文档中了解提示工程技术和 Azure OpenAI API。

1. 在 Web 应用中完成模型试验后，关闭浏览器中的 Web 应用选项卡以返回到 Azure AI Studio。

## 清理

使用完 Azure OpenAI 资源后，请记得在位于 `https://portal.azure.com` 的 **Azure 门户** 中删除部署或整个资源。
