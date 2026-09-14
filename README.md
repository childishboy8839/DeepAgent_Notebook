# Deep Agents 学习项目

这是跟随 DataWhale《Deep Agents 实战》课程完成的学习项目。

## 学习目标

- 理解 LangChain、LangGraph 与 Deep Agents 的关系
- 掌握工具调用、任务规划、文件系统和上下文管理
- 实践子 Agent、Skills、长期记忆、MCP 与人机协作
- 逐步构建一个可运行、可测试、可迭代的 Agent 项目

## 环境准备

建议使用 Python 3.11+。

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

复制 `.env.example` 为 `.env`，填写模型服务的 API Key。不要把真实密钥提交到 Git。

## 课程章节

代码按章节放在 `chapters/` 下：

1. 第一个 Deep Agent
2. 虚拟文件系统与上下文工程
3. 任务规划与分解
4. 子 Agent 与上下文隔离
5. Skills、记忆与安全控制
6. MCP、Streaming 与进阶编排

每完成一章，单独提交一次，提交信息建议使用：

```text
chapter-01: create first deep agent
```

## 参考资料

- [DataWhale Deep Agents 实战](https://github.com/datawhalechina/deepagents-in-action)
- [Deep Agents 官方文档](https://docs.langchain.com/oss/python/deepagents/overview)
