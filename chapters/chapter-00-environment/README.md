# 第 0 章：环境配置笔记

> 记录时间：2026-09-14  
> 学习路线：DataWhale《Deep Agents 实战》

本章记录从空项目开始配置 Deep Agents 学习环境的过程，包括最终方案、版本信息和遇到的问题。

## 1. 工具之间的关系

```text
Python
└── 运行 Deep Agents 代码

uv
├── 安装和管理 Python 版本
├── 创建项目虚拟环境
└── 安装 Python 依赖

AgentSeek
└── 创建课程模板、执行 task、doctor、dev 等生命周期命令

Deep Agents
└── 真正学习的 Agent Harness
```

AgentSeek 不是 Deep Agents 本身，也不是 LangChain 官方维护的框架。Deep Agents 由 LangChain 官方维护；AgentSeek 是独立的模板与生命周期工具，DataWhale 使用它来提供各章节实验模板。

## 2. 最终采用的环境方案

本机原本有 Anaconda 和多个 Conda 环境。最初曾创建过一个名为 `deepagent` 的 Conda 环境，后来为了严格跟随课程，确认并删除了它。

最终采用 DataWhale 推荐的 `uv` 方案：

```text
系统工具层
├── uv：C:\Users\27038\.local\bin
└── AgentSeek：通过 uv tool 安装

项目层
└── D:\Learning_Agent\learn_deepagent\.venv
    ├── Python 3.13.15
    ├── deepagents 0.7.13
    ├── langchain-openai 1.6.2
    └── langsmith 0.12.4
```

项目目录中的 `.venv` 已加入 `.gitignore`，不会上传到 GitHub。

## 3. 前置环境检查

曾检查到的版本如下：

```text
Anaconda Python：3.11.5
uv：未安装
AgentSeek：未安装
Node.js：24.13.0
npm：11.6.2
```

DataWhale 的 AgentSeek 模板要求 Python 3.12 或 3.13。虽然系统中还有 Python 3.14，但为了避免模板兼容性问题，本项目使用 uv 管理的 Python 3.13。

Node.js 和 npm 已经满足前端运行需要，因此没有重复安装。

## 4. 安装 uv

Windows PowerShell 安装命令：

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

安装位置：

```text
C:\Users\27038\.local\bin
```

如果当前 PowerShell 没有刷新 PATH，可以临时执行：

```powershell
$env:Path = "$env:USERPROFILE\.local\bin;$env:Path"
uv --version
```

## 5. 安装 AgentSeek

课程命令是：

```powershell
uv tool install --upgrade agentseek
```

第一次执行时遇到版本解析错误：默认 Python 是 Anaconda 3.11，而 AgentSeek 需要 Python 3.12～3.13。

最终使用明确的 Python 3.13 安装：

```powershell
uv python install 3.13
uv tool install --python 3.13 --upgrade agentseek
```

验证结果：

```text
uv 0.12.13
AGENTSEEK v0.1.4
```

课程页面曾使用 AgentSeek 0.1.2，但课程也说明版本会继续更新；本次安装得到 0.1.4。

## 6. 创建项目虚拟环境并安装依赖

在当前学习项目根目录执行：

```powershell
cd D:\Learning_Agent\learn_deepagent
uv venv --python 3.13 .venv
uv pip install --python .venv\Scripts\python.exe `
  "deepagents>=0.7,<0.8" langchain-openai langsmith python-dotenv
```

也可以先激活环境，再使用 `uv pip install`：

```powershell
.\.venv\Scripts\Activate.ps1
uv pip install "deepagents>=0.7,<0.8" langchain-openai langsmith python-dotenv
```

验证安装：

```powershell
python --version
python -c "import deepagents, langchain_openai, langsmith; print('依赖正常')"
```

当前依赖已记录在项目根目录的 `requirements.txt` 中。以后可以使用下面的命令重建类似环境：

```powershell
uv venv --python 3.13 .venv
uv pip install --python .venv\Scripts\python.exe -r requirements.txt
```

## 7. AgentSeek 启动器问题与排查

### 问题一：AgentSeek 默认使用了 Python 3.11

错误信息的核心是：

```text
the current Python version (3.11.5) does not satisfy Python>=3.12,<3.14
```

解决方法是显式指定：

```powershell
uv tool install --python 3.13 --upgrade agentseek
```

### 问题二：`uv python install 3.12` 出现目标目录警告

尝试安装 Python 3.12 时出现了 managed installation target directory 警告。改用 Python 3.13 后，AgentSeek 成功安装并能运行。

### 问题三：`uv trampoline failed to canonicalize script path`

直接执行：

```powershell
agentseek version
```

曾出现：

```text
error: uv trampoline failed to canonicalize script path
```

这不是 AgentSeek 包未安装。下面的命令可以成功运行并显示版本，证明包和 Python 环境是正常的：

```powershell
uv tool run --from agentseek --python 3.13 agentseek version
```

目前稳定的绕过方式是使用 `uv tool run` 执行 AgentSeek：

```powershell
uv tool run --from agentseek --python 3.13 agentseek create deepagents/research --checkout main --no-input
```

### 问题四：PowerShell 路径变量 typo

错误写法：

```powershell
$uvBin = "$env:USERPROFILE.local\bin"
```

正确写法：

```powershell
$uvBin = "$env:USERPROFILE\.local\bin"
```

这里的 `.local` 前面必须有反斜杠。

## 8. 不要混用环境管理器

以下两种方式都可以运行 Python，但同一个项目最好只选一种：

```text
Conda 环境
└── conda create / pip install

uv 项目环境
└── uv venv / uv sync / uv pip install
```

本项目决定使用 uv。因此：

- 不把 `deepagents` 安装到 Anaconda base 环境；
- 不把 AgentSeek 安装到项目 `.venv`；
- 不在同一个模板项目中同时使用 Conda 环境和 `uv sync`；
- 使用 `uv run` 或激活 `.venv` 后的 `python` 运行项目代码。

## 9. API Key 与 LangSmith

`langsmith` 包已经安装，但是否记录 Trace 由环境变量决定。真实 Key 只写入本地 `.env`，不要提交到 Git，也不要发到聊天中。

常见配置形式：

```text
LANGSMITH_TRACING=false
LANGSMITH_API_KEY=
LANGSMITH_PROJECT=deepagents-course
```

开始学习 Trace 时，再把 `LANGSMITH_TRACING` 改为 `true` 并填写本地 Key。

模型服务的 Key 也只写入 `.env`。项目已经忽略 `.env`，而 `.env.example` 只保存变量名称和示例说明。

## 10. 下一步

环境已经准备好。接下来创建 DataWhale 的研究模板：

```powershell
cd D:\Learning_Agent\learn_deepagent
uv tool run --from agentseek --python 3.13 agentseek create deepagents/research --checkout main --no-input
```

进入生成目录后，按模板自己的生命周期配置执行：

```powershell
uv tool run --from agentseek --python 3.13 agentseek info
uv tool run --from agentseek --python 3.13 agentseek task --list
uv tool run --from agentseek --python 3.13 agentseek task sync
uv tool run --from agentseek --python 3.13 agentseek doctor
```

模板项目可能拥有独立的 `.venv`。当前根目录的 `.venv` 用于第 2 章的基础代码实验；模板自己的环境由模板配置管理。
