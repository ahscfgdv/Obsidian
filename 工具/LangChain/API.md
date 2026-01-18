## langchain_core

### prompts

* FewShotPromptTemplate：FewShotPromptTemplate 的作用是构建包含少量示例（即“少样本”）的提示词，通过让大模型“看例子学习”，引导其按照指定的格式、风格或逻辑生成更准确的回复。
* 

### messages
* HumanMessage **（人类消息）：** 它是**用户输入**，代表你（或最终用户）向 AI 提出的具体问题、指令或对话内容。
* SystemMessage **（系统消息）：** 它是**幕后导演**，用于设定 AI 的“人设”、行为准则或背景信息（例如：“你是一个资深的 Python 程序员”）。
* AIMessage **（AI 消息）：** 它是**模型输出**，代表 AI 根据上述上下文生成的回答或反馈。
## langchain_openai

### ChatOpenAI