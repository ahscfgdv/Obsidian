agent和llm的区别
- agent可以更好的处理context window和prompt，相对于直接使用llm

agent构建了更特殊的系统提示词，
- 包含agent可以调用的工具，如读写文件等，
- 之前的记忆，
- 用户对于agent的各种设定

openclaw和claude code的主要区别：
- 多了接入社交媒体的接口，便于使用。
- claude code可能让人意会为只能编写代码其实是通用的agent

openclaw的特殊能力
- 通过spawn分出子openclaw处理单独的独立任务，将结果返回给父任务，可以节省context window
- 心跳机制：每隔一段时间就发送固定的prompt到llm
- Cron Job：可以执行定时任务，

通过这些agent进行vibe coding的缺点，编写prompt时只注意功能的实现，而忽视后续的维护迭代等等。agent缺乏工程化的能力，而在细节的编码上绝对bu'shu