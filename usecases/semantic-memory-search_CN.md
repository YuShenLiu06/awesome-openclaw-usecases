# 语义记忆搜索

OpenClaw 的内置内存系统将所有内容存储为 markdown 文件 —— 但随着记忆在数周和数月内增长，找到上周二做出的那个决定变得不可能。没有搜索，只是滚动浏览文件。

这个使用案例使用 [memsearch](https://github.com/zilliztech/memsearch) 在 OpenClaw 现有的 markdown 记忆文件之上添加**向量驱动的语义搜索**，这样你可以通过意义即时找到任何过去的记忆，而不仅仅是关键词。

## 功能说明

- 通过单个命令将所有 OpenClaw markdown 记忆文件索引到向量数据库（Milvus）
- 通过意义搜索："我们选择了什么缓存解决方案？"即使没有出现"缓存"一词，也能找到相关记忆
- 混合搜索（密集向量 + BM25 全文）并使用 RRF 重新排名以获得最佳结果
- SHA-256 内容哈希意味着未更改的文件永远不会被重新嵌入 —— 零浪费 API 调用
- 文件监视器在记忆文件更改时自动重新索引，因此索引始终是最新的
- 适用于任何嵌入提供商：OpenAI、Google、Voyage、Ollama 或完全本地（无需 API 密钥）

## 痛点

OpenClaw 的记忆存储为纯 markdown 文件。这对于可移植性和人类可读性很好，但它没有搜索功能。随着你的记忆增长，你不得不通过文件 grep（仅关键词，错过语义匹配）或将整个文件加载到上下文中（在不相关内容上浪费令牌）。你需要一种方式来问"我对 X 做出了什么决定？"并获得确切的相关块，而不管措辞如何。

## 所需技能

- 不需要 OpenClaw 技能 —— memsearch 是一个独立的 Python CLI/库
- Python 3.10+ 带有 pip 或 uv

## 如何设置

1. 安装 memsearch：
```bash
pip install memsearch
```

2. 运行交互式配置向导：
```bash
memsearch config init
```

3. 索引你的 OpenClaw 记忆目录：
```bash
memsearch index ~/path/to/your/memory/
```

4. 通过意义搜索你的记忆：
```bash
memsearch search "我们选择了什么缓存解决方案？"
```

5. 对于实时同步，启动文件监视器 —— 它在每次文件更改时自动索引：
```bash
memsearch watch ~/path/to/your/memory/
```

6. 对于完全本地设置（无 API 密钥），安装本地嵌入提供商：
```bash
pip install "memsearch[local]"
memsearch config set embedding.provider local
memsearch index ~/path/to/your/memory/
```

## 核心见解

- **Markdown 仍然是真实来源。** 向量索引只是一个派生缓存 —— 你可以随时使用 `memsearch index` 重建它。你的记忆文件永远不会被修改。
- **智能去重节省资金。** 每个块由 SHA-256 内容哈希标识。重新运行 `index` 仅嵌入新的或更改的内容，因此你可以尽可能频繁地运行它，而不会浪费嵌入 API 调用。
- **混合搜索胜过纯向量搜索。** 通过倒排融合 (RRF) 将语义相似性（密集向量）与关键词匹配（BM25）相结合，可以捕获基于意义和完全匹配的查询。

## 相关链接

- [memsearch GitHub](https://github.com/zilliztech/memsearch) —— 驱动此使用案例的库
- [memsearch 文档](https://zilliztech.github.io/memsearch/) —— 完整 CLI 参考、Python API 和架构
- [OpenClaw](https://github.com/openclaw/openclaw) —— 激发 memsearch 的记忆架构
- [Milvus](https://milvus.io/) —— 向量数据库后端
