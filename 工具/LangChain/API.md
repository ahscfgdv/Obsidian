## langchain_core

**`langchain_core` 是整个 LangChain 生态系统的基石和底层架构包。**

如果把 LangChain 比作一台电脑，`langchain_community` 是各种外接设备（鼠标、键盘、打印机），那么 **`langchain_core` 就是主板和操作系统内核**。它不包含具体的第三方工具集成，而是定义了所有组件必须遵守的**标准接口**和**基本抽象**。
### prompts

* **FewShotPromptTemplate：** 的作用是构建包含少量示例（即“少样本”）的提示词，通过让大模型“看例子学习”，引导其按照指定的格式、风格或逻辑生成更准确的回复。
  **返回类型:** 纯文本
* **FewShotChatMessagePromptTemplate**
  **返回类型:** 消息对象列表
*  **PromptTemplate:** 带有填空题的标准公文模版”。它的核心作用是将静态的文本结构与动态的用户输入分离开来，让提示词的生成变得标准化、可复用且易于管理。
	* **partial:**  方法的主要作用是“预填充”提示词模版中的部分变量，并返回一个新的、待完成的提示词模版对象。

### messages
* HumanMessage **（人类消息）：** 它是**用户输入**，代表你（或最终用户）向 AI 提出的具体问题、指令或对话内容。
* SystemMessage **（系统消息）：** 它是**幕后导演**，用于设定 AI 的“人设”、行为准则或背景信息（例如：“你是一个资深的 Python 程序员”）。
* AIMessage **（AI 消息）：** 它是**模型输出**，代表 AI 根据上述上下文生成的回答或反馈。

### example_selectors

**SemanticSimilarityExampleSelector:**  是 LangChain 中用于 Few-Shot（少样本）提示工程的一个智能组件，旨在根据语义相似度动态筛选最佳示例。

它通过 Embedding 模型将用户的**当前输入**和预设的**示例库**转化为向量，并计算它们之间的距离（通常是余弦相似度），从而自动从大量样本中检索出与当前问题**最相关**的 K 个示例插入到 Prompt 中。这种机制不仅能显著节省 Token（无需把所有例子都塞进去），还能让大模型参考最贴切的案例进行推理，从而大幅提升输出的准确性和风格一致性。
## langchain_openai

### ChatOpenAI

## langchain_community

**`langchain_community` 是 LangChain 生态架构中专门用于存放所有第三方集成（Integrations）的独立 Python 包。**

它旨在将 LangChain 的核心抽象逻辑（`langchain-core`）与具体的外部工具实现解耦，汇集了由开源社区和合作伙伴维护的数以百计的组件——涵盖了各类**大语言模型接口**（如 OpenAI、Hugging Face）、**向量数据库**（如 Chroma、FAISS）、**文档加载器**以及**搜索工具**等，使开发者能够通过标准化的接口，“开箱即用”地将 LangChain 应用连接到现实世界的各种服务与私有数据源中。