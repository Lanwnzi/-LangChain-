<div align='center'>
    <img src="https://github.com/user-attachments/assets/eb9b3b09-e2bf-4c9d-95a0-5c2d9712723d" alt="alt text" width="70%">
</div>

<div align="center">

<p align="center">
  <img src="https://img.shields.io/badge/python-3.12+-blue.svg?style=for-the-badge&logo=python&logoColor=white" alt="Python Version" />
  <img src="https://img.shields.io/badge/LangChain-1.0.3-important.svg?style=for-the-badge" alt="LangChain" />
  <img src="https://img.shields.io/badge/LangGraph-1.0.9-purple.svg?style=for-the-badge" alt="LangGraph" />
  <img src="https://img.shields.io/badge/vue-3.4+-4FC08D.svg?style=for-the-badge&logo=vue.js&logoColor=white" alt="Vue Version" />
  <img src="https://img.shields.io/badge/fastapi-0.121+-009688.svg?style=for-the-badge&logo=fastapi&logoColor=white" alt="FastAPI" />
  <img src="https://img.shields.io/badge/license-MIT-green.svg?style=for-the-badge" alt="License" />
</p>

<p align="center">
  <b>🛠️ 基于 LangChain + LangGraph 的智能硬件售后知识助手</b>
</p>

<p align="center">
  多 Agent 协同 • 混合检索 • MCP 工具集成 • AgentSkill 技能封装 • 记忆召回
</p>

</div>

---

## 项目背景

面向智能硬件企业内部客服辅助场景。客服每天面对大量重复性问题——产品参数、蓝牙连接失败、保修政策、故障排查，需要频繁切换多个系统查询资料，响应慢、培训成本高。

本系统基于 LangChain + LangGraph 构建多 Agent 售后知识助手，将产品知识库检索、故障排查技能和 MCP 外部工具统一封装，由主 Agent 根据客服问题自动路由与协同调度。

## 核心能力

- **多 Agent 协同 + 混合检索**：主 Agent 统一接收客服问题，内部按意图分流子 Agent——售后知识文档走混合检索（Qwen3-Embedding-0.6B + BM25 + BGE-Reranker-v2-m3），硬件型号相关问题路由到对应独立 SkillAgent，对外表现为单一入口
- **AgentSkill 技能封装**：不同耳机型号的故障排查与保修知识封装为独立子 Agent，拥有独立 LangGraph 图、System Prompt 和工具集，严格隔离避免知识混用；新增产品型号仅需一条数据库记录
- **上下文记忆召回**：基于 ChromaDB 做对话摘要向量化与语义召回，替代全量历史拼接。自建测试集上响应时延降 30%，输入 Token 降 40%，Top-3 命中率 0.95
- **MCP 协议集成**：支持 SSE 和 STDIO 两种传输协议的外部系统接入（订单查询、保修状态等），无需切换系统
- **全链路可观测**：通过继承 AgentMiddleware 重写三个钩子，在 LLM 调用和工具执行节点注入 SSE 事件；同步接入 Langfuse 全链路追踪，自动采集 Token 消耗与调用链拓扑

## 技术栈

| 层 | 技术 |
|------|------|
| 后端框架 | Python 3.12+, FastAPI, Uvicorn |
| Agent 编排 | LangChain 1.0.3, LangGraph 1.0.9 |
| 数据库 | MySQL 8.0+ (SQLModel + SQLAlchemy + aiomysql) |
| 缓存 | Redis 7.0+ |
| 向量数据库 | ChromaDB / Milvus |
| 搜索引擎 | Elasticsearch（可选） |
| LLM 接口 | OpenAI 兼容 API（ChatOpenAI） |
| 外部工具协议 | MCP 1.20 (SSE / STDIO) |
| 检索 | Qwen3-Embedding-0.6B + BM25 + BGE-Reranker-v2-m3 |
| 可观测 | Langfuse |
| 前端 | Vue 3 + TypeScript + Vite + Element Plus |

## 快速开始

### 前置条件

- Python 3.12+
- MySQL 8.0+（运行中，端口 3306）
- Redis 7.0+（运行中，端口 6379）

### 启动后端

```bash
# 安装依赖
pip install -r requirements.txt

# 修改配置文件 src/backend/agentchat/config.yaml，填入 LLM API key 等参数

# 启动
cd src/backend
uvicorn agentchat.main:app --port 7860 --reload
```

### 启动前端

```bash
cd src/frontend
npm install
npm run dev
```

### Docker 启动

```bash
docker-compose -f docs/docker/docker-compose.yml up -d
```

## 项目结构

```
AgentChat/
├── pyproject.toml
├── requirements.txt
├── src/
│   ├── backend/agentchat/
│   │   ├── main.py              # FastAPI 入口
│   │   ├── config.yaml          # 主配置
│   │   ├── api/                 # API 路由层
│   │   ├── core/agents/         # Agent 编排核心
│   │   │   ├── general_agent.py # 主 Agent
│   │   │   ├── skill_agent.py   # 技能 Agent
│   │   │   ├── mcp_agent.py     # MCP Agent
│   │   │   └── ...
│   │   ├── database/            # 数据模型与 DAO
│   │   ├── services/            # 业务服务层
│   │   │   ├── rag/             # RAG 检索
│   │   │   ├── mcp/             # MCP 协议
│   │   │   └── memory/          # 记忆系统
│   │   ├── tools/               # 内置工具
│   │   └── prompts/             # 提示词模板
│   └── frontend/                # Vue 3 前端
└── docs/
```

## License

[MIT License](LICENSE)
