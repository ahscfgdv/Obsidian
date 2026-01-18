## langchain_core

### prompts

* FewShotPromptTemplate：FewShotPromptTemplate 的作用是构建包含少量示例（即“少样本”）的提示词，通过让大模型“看例子学习”，引导其按照指定的格式、风格或逻辑生成更准确的回复。
  **返回类型:** 纯文本
* FewShotChatMessagePromptTemplate
  **返回类型:** 消息对象列表

### messages
* HumanMessage **（人类消息）：** 它是**用户输入**，代表你（或最终用户）向 AI 提出的具体问题、指令或对话内容。
* SystemMessage **（系统消息）：** 它是**幕后导演**，用于设定 AI 的“人设”、行为准则或背景信息（例如：“你是一个资深的 Python 程序员”）。
* AIMessage **（AI 消息）：** 它是**模型输出**，代表 AI 根据上述上下文生成的回答或反馈。
## langchain_openai

### ChatOpenAI

## langchain_community

**`langchain_community` 是 LangChain 生态架构中专门用于存放所有第三方集成（Integrations）的独立 Python 包。**

它旨在将 LangChain 的核心抽象逻辑（`langchain-core`）与具体的外部工具实现解耦，汇集了由开源社区和合作伙伴维护的数以百计的组件——涵盖了各类**大语言模型接口**（如 OpenAI、Hugging Face）、**向量数据库**（如 Chroma、FAISS）、**文档加载器**以及**搜索工具**等，使开发者能够通过标准化的接口，“开箱即用”地将 LangChain 应用连接到现实世界的各种服务与私有数据源中。