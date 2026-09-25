# LangGraph Demo

基于 [LangGraph](https://github.com/langchain-ai/langgraph) 的学习示例仓库，用一系列可运行的
Jupyter Notebook 演示 LangGraph 的核心概念：图的构建、状态（State）定义以及 Reducer 的使用。

## 特性

- 使用 `StateGraph` 构建「节点 + 边」的工作流图。
- 对比三种 State 定义方式：`TypedDict`、`dataclass`、`pydantic.BaseModel`。
- 演示 `Annotated[类型, reducer]` 状态归约（如列表追加、覆盖等）。
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
│   └── 02-静态分支&动态分支.ipynb            # 静态边与动态路由（条件边/Command/Send）
├── pyproject.toml                            # 项目依赖与 Python 版本约束
├── uv.lock                                   # uv 锁定的依赖版本
├── .python-version                            # 指定 Python 3.13
├── env.template                               # 环境变量模板
├── LICENSE                                    # MIT 开源协议
└── README.md
```

## 环境要求

- **Python 3.13**（`>=3.13,<3.14`，见 `pyproject.toml` 与 `.python-version`）
- [uv](https://docs.astral.sh/uv/) 包管理器

## 快速开始

```bash
# 1. 安装依赖（uv 会自动创建 .venv 并下载匹配的 Python 版本）
uv sync

# 2. 根据模板创建 .env 并填入真实值
cp .env.template .env

# 3. 启动 Jupyter
uv run jupyter lab
```

## 环境变量

部分示例会调用大模型，需要在项目根目录的 `.env` 中配置：

| 变量 | 说明 |
| --- | --- |
| `DEEPSEEK_API_BASE` | DeepSeek API 的基础地址 |
| `DEEPSEEK_API_KEY` | DeepSeek API 密钥 |

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
| `chapter02/02-静态分支&动态分支.ipynb` | 静态分支与动态分支（`add_conditional_edges` / `Command` / `Send`）的区别与用法 |

## License

本项目基于 [MIT License](LICENSE) 开源。
