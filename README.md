智能金融投研分析助手

该项目基于 Qwen2.5-7B 模型，使用 vLLM 进行本地部署，并结合 RAG 技术实现了研报分析与数据检索，您可以重点看一下该项目的 README 和 Agent 编排逻辑。

1. 专业的RAG知识库(RAG Pipeline)
多源数据处理：支持PDF研报、财报表格、HTML新闻的自动解析与清洗。
金融专用切分：针对财报结构优化的分块策略，保留表格与上下文的关联。
高精度召回：集成向量检索与关键词检索（BM25），并配合Rerank（重排序）模型，确保检索结果的相关性。

2. 自主决策Agent(Autonomous Agents)
工具调用能力：Agent挂载了包括VectorDB_Search（查库）、Web_Search（联网）、Data_Calculator（计算）等工具。
思维链推理 (CoT)：在回答复杂宏观经济问题时，模型会展示“思考-行动-观察”的推理过程。
结构化输出：直接生成符合研报格式的分析段落，包含风险提示与核心观点。


！！！  31train.ipynb 代码可以跑的起来，但是有关查询股价的精准度有问题，应该是ai的幻觉，和他的历史记忆有关，第一次会使用工具去联网查询，后面就会随便编写数据，

！！！  已经在最新的Untitled.ipynb解决了该问题。

## 🤖 模型服务部署 (Model Serving)
本项目后端采用 **vLLM** 部署 **Qwen2.5-7B-Instruct** 模型，提供兼容 OpenAI 格式的高性能 API 接口，并针对 Agent 场景开启了工具调用（Tool Calling）支持。
### 模型信息
- **基座模型**: [Qwen2.5-7B-Instruct](https://huggingface.co/Qwen/Qwen2.5-7B-Instruct)
- **推理框架**: vLLM (High-throughput and memory-efficient inference engine)
- **显存要求**: 约 16GB+ (推荐 A10, 3090, 4090 或 A100)
- **上下文长度**: 16k (支持长研报分析)
### 启动命令
确保已安装 `vllm`，并下载模型权重至本地目录。使用以下命令启动 API 服务：
```bash
python -m vllm.entrypoints.openai.api_server \
    --model /path/to/your/Qwen2.5-7B-Instruct \  # 替换为你下载的模型路径
    --served-model-name Qwen2.5-7B \
    --max-model-len 16384 \
    --gpu-memory-utilization 0.90 \
    --trust-remote-code \
    --port 8000 \
    --enable-auto-tool-choice \
    --tool-call-parser hermes


一些需要的库安装
akshare==1.18.7
langchain==1.2.0
langchain_community==0.4.1
langchain_core==1.2.6
langchain_huggingface==1.2.0
langchain_openai==1.1.6
langchain_text_splitters==1.1.0
pandas==2.3.3
Requests==2.32.5
yfinance==1.0
openai
tiktoken
