# 天琴 RAG 系统 (Tianqin RAG System)

天琴 RAG 系统是面向天琴引力波探测项目的智能知识库管理平台，采用检索增强生成（RAG）技术，为引力波探测研究提供智能文档处理和检索能力。

## 项目背景

天琴计划是中国空间引力波探测重大项目，旨在通过空间激光干涉仪探测引力波。本项目为天琴计划研究团队提供：

- 科研文献的智能管理和检索
- 基于知识库的 AI 辅助研究
- 多团队协作和知识共享
- 敏感科研数据的安全管理

## 核心技术栈

- **后端**: Spring Boot 3.4.2, Java 17, MySQL 8.0, Redis, Elasticsearch 8.10.0, Kafka, MinIO
- **前端**: Vue 3 + TypeScript, Vite, Naive UI, Pinia, UnoCSS
- **AI**: DeepSeek API / Ollama + Embedding 模型
- **实时通信**: WebSocket

## 核心功能

### 📚 科研文献管理
- 支持 PDF、Word、TXT 等多种格式文档上传
- 文件分片上传和断点续传
- 基于组织标签的文档权限管理
- 公开/私有文档分类

### 🔍 智能检索 (RAG)
- 文档语义分块和向量化
- ElasticSearch 语义搜索 + 关键词搜索
- 基于检索结果的 AI 生成回答
- 多轮对话支持

### 🏢 多团队协作
- 组织标签实现多租户架构
- 团队成员权限管理
- 跨团队知识共享

### 💬 实时交互
- WebSocket 实时通信
- 流式 AI 响应
- 对话历史管理

## 快速开始

### 环境要求
- Java 17
- Maven 3.8.6+
- Node.js 18.20.0+
- pnpm 8.7.0+
- Docker（推荐用于运行依赖服务）

### 启动依赖服务
```bash
cd docs && docker-compose up -d
```

### 启动后端
```bash
mvn spring-boot:run
```

### 启动前端
```bash
cd frontend && pnpm install && pnpm dev
```

## 项目结构

```
.
├── src/main/java/com/yizhaoqi/smartpai/    # 后端代码
├── frontend/                                # 前端代码
├── docs/                                    # Docker 配置和文档
└── README.md
```

## 贡献指南

欢迎为天琴 RAG 系统贡献代码！请遵循以下步骤：

1. Fork 本仓库
2. 创建特性分支 (`git checkout -b feature/amazing-feature`)
3. 提交更改 (`git commit -m 'Add amazing feature'`)
4. 推送到分支 (`git push origin feature/amazing-feature`)
5. 创建 Pull Request

## 许可证

MIT License

## 联系方式

- 项目地址: https://github.com/sleeve5/TianQin-RAG.git
- 问题反馈: https://github.com/sleeve5/TianQin-RAG/issues

---

*本项目服务于天琴引力波探测计划，助力中国空间引力波探测研究。*
