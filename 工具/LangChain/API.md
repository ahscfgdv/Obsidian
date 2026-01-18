## langchain_core

**`langchain_core` 是整个 LangChain 生态系统的基石和底层架构包。**

如果把 LangChain 比作一台电脑，`langchain_community` 是各种外接设备（鼠标、键盘、打印机），那么 **`langchain_core` 就是主板和操作系统内核**。它不包含具体的第三方工具集成，而是定义了所有组件必须遵守的**标准接口**和**基本抽象**。

---
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

### output_parsers

在 LangChain 中，**Output Parsers（输出解析器）** 的核心作用是**将大语言模型（LLM）输出的非结构化“纯文本”，转化为程序可以处理的“结构化数据”**（如 JSON、List、Date 或 Python 对象）。

---

#### 1.`StrOutputParser` (最基础)

这是最简单、最常用的解析器，特别是在 LCEL（LangChain 表达式语言）中。

- **作用：** 剥离掉 `AIMessage` 等包装对象，直接提取模型的**纯文本内容**。
    
- **场景：** 你只需要模型“说人话”，不需要特定的数据格式。
    
- **转换效果：**
    
    - 输入：`AIMessage(content="你好，我是AI。")`
        
    - 输出：`"你好，我是AI。"` (String)
        

#### 2. `PydanticOutputParser` (最强大/工业级)

这是生产环境中最受推崇的解析器。它利用 Python 的 `Pydantic` 库来定义严格的数据结构。

- **作用：** 要求模型必须按照你定义的 Python Class（类）格式返回数据，并会自动验证数据类型是否正确。
    
- **场景：** 你需要提取复杂信息（如姓名、年龄、技能列表），并直接存入数据库或通过 API 返回给前端。
    
- **转换效果：**
    
    - 输入：`"{ 'name': 'Jack', 'age': 25 }"` (Text)
        
    - 输出：`User(name='Jack', age=25)` (Python Object)
        

#### 3. `CommaSeparatedListOutputParser` (列表专用)

- **作用：** 专门用于处理“列出 X 个...”之类的请求，将逗号分隔的字符串自动切分为 Python 列表。
    
- **场景：** 生成关键词、标签、或者推荐列表。
    
- **转换效果：**
    
    - 输入：`"苹果, 香蕉, 橙子"`
        
    - 输出：`['苹果', '香蕉', '橙子']` (List)
        

#### 4. `JsonOutputParser` (通用 JSON)

- **作用：** 指示模型返回标准的 JSON 格式，并将其解析为 Python 的字典（Dict）。
    
- **场景：** 当你不想定义复杂的 Pydantic 类，但又需要键值对（Key-Value）结构的数据时。
    
- **转换效果：**
    
    - 输入：`"```json\n{"answer": 42}\n```"`
        
    - 输出：`{'answer': 42}` (Dict)
        

---

### 代码示例：如何在一个链中使用解析器

解析器通常是 Chain 的**最后一环**。以下是使用 `StrOutputParser` 的极简示例：

Python

```
from langchain_core.output_parsers import StrOutputParser
from langchain_openai import ChatOpenAI
from langchain_core.prompts import PromptTemplate

# 1. 定义模型
model = ChatOpenAI()

# 2. 定义解析器
parser = StrOutputParser()

# 3. 构建链 (Prompt -> Model -> Parser)
# 这里的 "|" 是 LCEL 的管道符号
chain = PromptTemplate.from_template("讲个关于{topic}的笑话") | model | parser

# 4. 调用
result = chain.invoke({"topic": "程序员"})

# result 直接就是字符串，不需要再 .content 了
print(type(result)) # <class 'str'>
print(result)       # "为什么程序员总是分不清万圣节和圣诞节？因为 Oct 31 == Dec 25"
```

**您想看一段 `PydanticOutputParser` 的代码吗？这在做信息提取（比如从简历中提取字段）时非常有用。**
## langchain_openai

### ChatOpenAI

## langchain_community

**`langchain_community` 是 LangChain 生态架构中专门用于存放所有第三方集成（Integrations）的独立 Python 包。**

它旨在将 LangChain 的核心抽象逻辑（`langchain-core`）与具体的外部工具实现解耦，汇集了由开源社区和合作伙伴维护的数以百计的组件——涵盖了各类**大语言模型接口**（如 OpenAI、Hugging Face）、**向量数据库**（如 Chroma、FAISS）、**文档加载器**以及**搜索工具**等，使开发者能够通过标准化的接口，“开箱即用”地将 LangChain 应用连接到现实世界的各种服务与私有数据源中。