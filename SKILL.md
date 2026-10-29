# skill：给 AI 智能体配一本"领域工作手册"。

通俗理解：MCP 让 AI "会用手"(调用外部工具、系统、数据)， skill 则让 AI "会按流程干活"(读懂专业 SOP、遵守领域规范、按步骤交付)。 两者互补，常组合使用：skill 指导 AI 何时、怎样去调用 MCP 工具。

## 组成形式：

* 一个 skill = 一个目录，常见位置 ~/.codebuddy/skills/<skill-name>/

* 入口文件 SKILL.md (必须)，结构为：

   * YAML frontmatter：写在文件顶部 --- 之间的元数据，声明 name / description 等

   * Markdown 正文：领域知识、工作步骤、最佳实践、示例

* 可选附件：

   * scripts/：可直接执行的脚本 (如环境检查、数据准备) 

   * templates/：代码或文档模板

   * references/：补充参考资料

* 目录名就是 skill 的调用名。

## 加载/发现方式：

* 自动加载：Agent 将每个 Skill 的 `name` 和 `description` 在对话初始时提供给 LLM。LLM 根据用户输入判断当前任务是否命中某个 Skill 的描述场景；若命中，LLM 通过工具调用请求 Agent 将该 Skill 的完整 `SKILL.md` 内容注入上下文。

* 手动调用：用户输入 /<skill-name> 主动指定加载该 skill；

* 触发条件：description 写得越具体、越贴近关键词，越容易被准确匹配。

与 Agent 的"元数据同步"(对位 MCP 握手)：

* Agent 编列可用 skill 列表，读取 frontmatter 得到：

   * name：skill 的唯一标识

   * description：能力描述与适用场景 (决定是否自动加载) 

   * allowed-tools：允许该 skill 调用的工具范围

   * disable-model-invocation：是否禁止模型自动触发 (仅允许手动调用) 

* 加载时把 SKILL.md 正文作为系统提示扩展注入本次会话；

* 可选子代理执行：把 skill 作为子任务在隔离上下文中运行，避免污染主对话。

## 使用过程：

* 用户发起问题；

* Agent 根据 description 判断是否需要加载某个 skill；

* 命中则把 SKILL.md 注入上下文，AI 按手册中的步骤与规范执行；

* 过程中可结合内置工具 (读写文件、执行命令) 和 MCP 工具完成具体操作；

* 执行完毕，按 skill 要求交付结果 (代码、报告、提交等)。

## 与 MCP 的对比：

* MCP：提供"工具能力"，解决"AI 能做什么事"(读库、调 API、查浏览器)；

* skill：提供"领域知识 + 工作流"，解决"AI 怎么做才专业"(规范、步骤、模板)；

* 关系：skill 是"剧本"，MCP 是"演员手里的道具"，组合起来才能交付专业产出。


