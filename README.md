# LangGraph Demo

基于 [LangGraph](https://github.com/langchain-ai/langgraph) 的学习示例仓库，用一系列可运行的
Jupyter Notebook 演示 LangGraph 的核心概念：图的构建、状态（State）定义以及 Reducer 的使用。

## 特性

- 使用 `StateGraph` 构建「节点 + 边」的工作流图。
- 对比三种 State 定义方式：`TypedDict`、`dataclass`、`pydantic.BaseModel`。
- 演示 `Annotated[类型, reducer]` 状态归约（如列表追加、覆盖等）。
- 静态分支与动态分支：`add_conditional_edges`、`Command(goto=...)`、`Send`（map-reduce）。
- `Send` 配合工具（`@tool`）调用的 map-reduce 示例。
- Agent Loop：LLM 与工具的循环调用，含 `ToolNode`、`tools_condition` 等动态派发方案。
- 步数治理：`recursion_limit`（super-step 计数与并行折算）、业务级步数预算与优雅降级、checkpointer 断点续跑。
- 失败治理：节点级 `RetryPolicy`（指数退避、`retry_on` 白名单、多套策略的匹配顺序）、`timeout` 超时与 `error_handler` 降级。
- 节点缓存：`cache_policy` 复用模型调用与检索结果，含缓存键、TTL、手动失效与常见陷阱。
- 持久化与记忆：`PostgresSaver` 按 `thread_id` 隔离会话状态，`PostgresStore` 跨会话保存用户偏好。
- 失败恢复：`get_state()` 定位断点、`invoke(None, config)` 续跑、时间旅行回放与分叉。
- 中断（human-in-the-loop）：动态 `interrupt()` 与静态 `interrupt_before` / `interrupt_after`，含并行中断与审批模式。
- 每个示例均可直接运行，并在 Notebook 中渲染出图结构。

## 目录结构

```
langgraph_demo/
├── chapter01/
│   ├── 01-graph.ipynb                        # StateGraph 基础：节点、边、执行
│   ├── 02-Typedict&dataclass&Pydantic.ipynb  # 三种 State 定义方式对比
│   ├── 03-StateReducer.ipynb                 # State Reducer 归约机制
│   ├── 04-节点并行执行.ipynb                 # 并行执行与其异常处理
│   ├── 05-MultiSchema.ipynb                  # 多状态图与私有状态
│   └── 06-预定义状态.ipynb                   # 预定义状态与流式输出
├── chapter02/
│   ├── 01-add_sequence.ipynb                 # add_sequence 顺序添加节点
│   ├── 02-静态分支&动态分支.ipynb            # 静态边与动态路由（条件边/Command/Send）
│   ├── 03-延迟执行.ipynb                     # 延迟执行
│   ├── 04-fan-in.ipynb                       # 动态扇入（fan-in）
│   ├── 05-AgentLoop.ipynb                    # Agent Loop：工具调用循环的三种实现 + 步数限制
│   ├── 06-重试机制.ipynb                     # 节点级重试、超时与降级处理
│   └── 07-节点缓存.ipynb                     # 节点级缓存策略、缓存键与失效陷阱
├── chapter03/
│   ├── 01-持久化.ipynb                       # checkpointer 短期记忆与 store 长期记忆
│   ├── 02-失败后回复运行.ipynb               # 失败后断点续跑、时间旅行与 replay/fork 分叉
│   └── 03-中断.ipynb                         # 动态中断、并行中断、审批模式与静态中断
├── pyproject.toml                            # 项目依赖与 Python 版本约束
├── uv.lock                                   # uv 锁定的依赖版本
├── docker-compose-pg.yaml                    # 本地 Postgres 编排（chapter03/01 持久化使用）
├── .env.template                             # 环境变量模板
├── LICENSE                                    # MIT 开源协议
└── README.md
```

## 环境要求

- **Python 3.13**（`>=3.13,<3.14`，见 `pyproject.toml`）
- [uv](https://docs.astral.sh/uv/) 包管理器
- 本仓库示例基于以下版本编写（`pyproject.toml` 中已固定）：

  | 包 | 版本 |
  | --- | --- |
  | `langgraph` | 1.2.11 |
  | `langchain` | 1.3.18 |
  | `langchain-core` | 1.6.0 |
  | `langchain-deepseek` | 1.0.1 |

  > ⚠️ 升级 `langgraph` 时注意版本约束：`langgraph>=1.2.11` 需要 `langchain>=1.3.18`，
  > 而 `langchain 1.2.x` 的上限是 `langgraph<1.2.0`，两者不能共存。

## 快速开始

```bash
# 1. 安装依赖（uv 会自动创建 .venv 并下载匹配的 Python 版本）
uv sync

# 2. 根据模板创建 .env 并填入真实值
cp .env.template .env

# 3. 启动 Jupyter
uv run jupyter lab
```

> `chapter03/01-持久化.ipynb` 需要一个可连接的 Postgres。其余章节不依赖数据库，
> 直接运行即可。

## 环境变量

部分示例会调用大模型，需要在项目根目录的 `.env` 中配置：

| 变量 | 说明 |
| --- | --- |
| `DEEPSEEK_API_BASE` | DeepSeek API 的基础地址 |
| `DEEPSEEK_API_KEY` | DeepSeek API 密钥 |
| `OPENROUTER_API_KEY` | OpenRouter API 密钥（`chapter02/05-AgentLoop.ipynb` 使用） |
| `OPENROUTER_API_BASE` | OpenRouter API 基础地址（可选，默认官方地址） |
| `POSTGRES_CONN_STRING` | Postgres 连接串（`chapter03/01-持久化.ipynb` 使用），形如 `postgresql://用户:密码@127.0.0.1:5432/库名` |
| `DB_USER` / `DB_PASSWORD` / `DB_NAME` | `docker-compose-pg.yaml` 启动 Postgres 时读取的账号与库名 |

> ⚠️ 请勿将真实密钥提交到仓库。`.env` 应加入 `.gitignore`；若它已被跟踪，
> 用 `git rm --cached .env` 取消跟踪，并轮换已泄露的密钥。

## 章节内容

| Notebook | 内容 |
| --- | --- |
| `01-graph.ipynb` | 用 `StateGraph` 定义状态、添加节点与边，并运行图 |
| `02-Typedict&dataclass&Pydantic.ipynb` | `TypedDict` / `dataclass` / `Pydantic` 三种 State 定义方式的示例与优缺点对比 |
| `03-StateReducer.ipynb` | 通过 `Annotated` 为状态字段指定 Reducer（归约函数） |
| `04-节点并行执行.ipynb` | 节点并行（fan-out/fan-in）的异常场景与处理方法 |
| `05-MultiSchema.ipynb` | 多状态图（input/output schema）与私有状态 |
| `06-预定义状态.ipynb` | 预定义状态、多 schema 与流式输出（含思考内容） |
| `chapter02/01-add_sequence.ipynb` | 用 `StateGraph.add_sequence` 顺序添加并串联节点 |
| `chapter02/02-静态分支&动态分支.ipynb` | 静态分支与动态分支（`add_conditional_edges` / `Command` / `Send`）的区别与用法，含 `Send` + 工具调用示例 |
| `chapter02/03-延迟执行.ipynb` | 延迟执行 |
| `chapter02/04-fan-in.ipynb` | 动态扇入（fan-in）：多分支汇聚、superstep 与 reducer 合并 |
| `chapter02/05-AgentLoop.ipynb` | Agent Loop：LLM 与工具的循环调用，含静态工具节点、`Send` 动态派发、`ToolNode` + `tools_condition` 三种实现；以及步数限制（`recursion_limit` 与 super-step 计数、chunk 数 ≠ 步数、断点续跑、业务级步数预算与优雅降级、`remaining_steps` 托管字段） |
| `chapter02/06-重试机制.ipynb` | 节点级失败治理：`RetryPolicy`（`max_attempts`、指数退避与 jitter）、`retry_on` 白名单与多套策略的匹配顺序、重试与 state/步数的关系、`error_handler` 降级、`TimeoutPolicy` 超时、`set_node_defaults` 全图默认策略、`ToolNode` 工具级重试 |
| `chapter02/07-节点缓存.ipynb` | 节点缓存：`cache_policy` 与 `compile(cache=...)` 的配合、默认缓存键为何易 miss、自定义 `key_func`、`CachePolicy(ttl=...)` 与 `clear_cache` 手动失效、命中缓存时是否执行节点体、缓存后端选型，以及与 checkpointer / retry 的关系 |
| `chapter03/01-持久化.ipynb` | 持久化三种模式（checkpointer / store / 长期记忆）的分工；`PostgresSaver`、`PostgresStore` 单例封装与 `setup()`；`thread_id` 决定会话隔离；用 store + `system_prompt` 实现跨会话记住用户偏好 |
| `chapter03/02-失败后回复运行.ipynb` | 节点抛异常时的真实行为；`get_state()` 定位断点（`next` / `pending_writes`）；修好后 `invoke(None, config)` 续跑且已完成节点不重跑；并行分支中成功的一半不白跑；时间旅行回到历史 `checkpoint_id`，以及 replay 与 fork 的区别 |
| `chapter03/03-中断.ipynb` | 动态中断：节点内 `interrupt()` 收集输入、`Command(resume=...)` 恢复、多节点并行中断按 `id` 批量恢复、审批模式用 `Command(goto=...)` 分流；静态中断：`interrupt_before` / `interrupt_after` 在调用时指定断点、用 `get_state().next` 查断点、`update_state` 人工修正后放行、`"*"` 逐节点单步调试 |

## License

本项目基于 [MIT License](LICENSE) 开源。
