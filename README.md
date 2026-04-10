# 天琴RAG智能知识库平台 (Tianqin RAG System)

天琴RAG智能知识库平台是面向天琴引力波探测项目的智能知识库管理平台，采用检索增强生成（RAG）技术，为引力波探测研究提供智能文档处理和检索能力。

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
├── src/main/java/com/sleeve/tianqinrag/    # 后端代码
├── frontend/                                # 前端代码
├── docs/                                    # Docker 配置和文档
└── README.md
```

## 系统架构

### 整体架构图

```
┌─────────────────────────────────────────────────────────────────┐
│                        前端 Vue 3 + TypeScript                    │
│   ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────────────┐ │
│   │ 知识库   │  │  聊天    │  │ 用户管理  │  │   组织标签管理   │ │
│   └──────────┘  └──────────┘  └──────────┘  └──────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼ HTTP/WebSocket
┌─────────────────────────────────────────────────────────────────┐
│                      后端 Spring Boot 3.4.2                       │
│  ┌────────────────────────────────────────────────────────────┐ │
│  │                     Controller Layer                        │ │
│  │  ChatController │ UploadController │ DocumentController    │ │
│  └────────────────────────────────────────────────────────────┘ │
│  ┌────────────────────────────────────────────────────────────┐ │
│  │                      Service Layer                           │ │
│  │  ChatHandler │ ParseService │ HybridSearchService          │ │
│  │  VectorizationService │ ElasticsearchService               │ │
│  └────────────────────────────────────────────────────────────┘ │
│  ┌────────────────────────────────────────────────────────────┐ │
│  │                    Repository Layer                          │ │
│  │  JPA Repositories │ Elasticsearch │ Redis                  │ │
│  └────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
                                │
        ┌───────────────────────┼───────────────────────┐
        ▼                       ▼                       ▼
┌───────────────┐     ┌───────────────┐     ┌───────────────┐
│    MySQL 8    │     │     Redis     │     │Elasticsearch  │
│  (主数据存储)  │     │  (缓存/会话)   │     │   (向量搜索)   │
└───────────────┘     └───────────────┘     └───────────────┘
        │                       │                       │
        └───────────────────────┼───────────────────────┘
                                ▼
┌───────────────┐     ┌───────────────┐     ┌───────────────┐
│  MinIO (OSS)  │     │    Kafka      │     │  DeepSeek API │
│  (文件存储)   │     │ (异步处理)     │     │   (AI 生成)   │
└───────────────┘     └───────────────┘     └───────────────┘
```

### 后端架构

后端采用经典的分层架构设计：

```
src/main/java/com/sleeve/tianqinrag/
├── controller/          # 控制层：处理 HTTP 请求和 WebSocket 消息
│   ├── ChatController       # 聊天控制器（WebSocket）
│   ├── UploadController     # 文件上传控制器
│   ├── DocumentController   # 文档管理控制器
│   ├── SearchController     # 搜索控制器
│   ├── ConversationController # 对话历史控制器
│   ├── AuthController       # 认证控制器
│   ├── UserController       # 用户管理控制器
│   └── AdminController      # 管理员控制器
├── service/             # 业务层：核心业务逻辑处理
│   ├── ChatHandler           # 聊天处理器（流式响应）
│   ├── ParseService          # 文档解析服务（流式分块）
│   ├── HybridSearchService   # 混合搜索服务（向量+关键词）
│   ├── VectorizationService  # 向量化服务（Embedding）
│   ├── ElasticsearchService  # ES 搜索服务
│   ├── DocumentService       # 文档管理服务
│   ├── ConversationService   # 对话管理服务
│   ├── UploadService         # 上传服务
│   └── OrgTagCacheService    # 组织标签缓存服务
├── repository/          # 数据访问层：数据库和缓存操作
│   ├── UserRepository        # 用户 JPA 仓库
│   ├── DocumentVectorRepository # 向量存储仓库
│   ├── ChunkInfoRepository    # 分块信息仓库
│   ├── FileUploadRepository   # 文件上传记录仓库
│   ├── ConversationRepository # 对话记录仓库
│   ├── OrganizationTagRepository # 组织标签仓库
│   └── RedisRepository       # Redis 缓存操作
├── client/              # 外部 API 客户端
│   ├── DeepSeekClient        # DeepSeek AI API 客户端
│   └── EmbeddingClient       # Embedding 模型客户端
├── config/              # 配置层：Spring 配置类
│   ├── SecurityConfig        # Spring Security 配置
│   ├── JwtAuthenticationFilter # JWT 认证过滤器
│   ├── OrgTagAuthorizationFilter # 组织标签授权过滤器
│   ├── EsConfig              # Elasticsearch 配置
│   ├── KafkaConfig           # Kafka 配置
│   ├── MinioConfig           # MinIO 对象存储配置
│   └── WebSocketConfig       # WebSocket 配置
├── model/               # 数据模型：JPA 实体
│   ├── User                  # 用户实体
│   ├── OrganizationTag       # 组织标签实体
│   ├── FileUpload            # 文件上传记录实体
│   ├── ChunkInfo             # 分块信息实体
│   ├── DocumentVector        # 文档向量实体
│   └── Conversation          # 对话记录实体
├── entity/              # 实体类：Elasticsearch 文档等
├── consumer/           # Kafka 消费者：异步文件处理
│   └── FileProcessingConsumer # 文件处理消息消费者
├── handler/             # WebSocket 处理器
│   └── ChatWebSocketHandler   # 聊天 WebSocket 处理器
└── utils/              # 工具类
    ├── JwtUtils             # JWT 工具类
    ├── LogUtils             # 日志工具类
    └── PasswordUtil         # 密码工具类
```

### 前端架构

前端采用 Vue 3 + TypeScript + Pinia + Vue Router 构建：

```
frontend/src/
├── views/                   # 页面视图
│   ├── _builtin/             # 内置页面（登录、404、500）
│   ├── chat/                 # 聊天页面
│   ├── chat-history/         # 对话历史页面
│   ├── knowledge-base/       # 知识库管理页面
│   ├── org-tag/              # 组织标签管理页面
│   ├── user/                 # 用户管理页面
│   └── personal-center/      # 个人中心页面
├── components/               # 公共组件
│   ├── common/               # 通用组件
│   └── custom/               # 定制组件
├── store/                    # Pinia 状态管理
│   └── modules/
│       ├── auth/             # 认证状态
│       ├── chat/             # 聊天状态
│       ├── knowledge-base/   # 知识库状态
│       ├── route/            # 路由状态
│       ├── tab/              # 多标签页状态
│       └── theme/            # 主题状态
├── service/                  # 网络服务
│   ├── api/                  # API 接口定义
│   └── request/              # 请求封装
├── router/                   # 路由配置
│   ├── guard/                # 路由守卫
│   ├── routes/              # 路由定义
│   └── elegant/              # 优雅路由
├── layouts/                 # 布局组件
│   ├── base-layout/          # 基础布局
│   └── modules/              # 布局模块
│       ├── global-header/   # 全局头部
│       ├── global-menu/     # 全局菜单
│       ├── global-sider/    # 全局侧边栏
│       └── global-tab/      # 全局标签页
├── locales/                  # 国际化
└── styles/                   # 样式文件
```

### 核心数据流

#### 1. 文档上传与处理流程

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│  前端分片    │───▶│ MinIO 存储  │───▶│ Kafka 消息   │───▶│ 异步消费者   │
│  (断点续传)  │    │  (分块存储)  │    │  (触发处理)  │    │  (FileProc) │
└─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘
                                                                   │
                                                                   ▼
┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│  ES 索引    │◀───│ 向量化存储   │◀───│ 分块解析    │◀───│ Tika 解析    │
│  (全文搜索)  │    │ (向量数据)   │    │ (HanLP)     │    │ (流式处理)   │
└─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘
```

#### 2. RAG 智能问答流程

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│  用户提问    │───▶│ 混合搜索    │───▶│ 向量检索    │───▶│ 关键词检索   │
│  (WebSocket)│    │ (Hybrid)     │    │ (ES ANN)    │    │ (ES BM25)   │
└─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘
                                                                   │
                                                                   ▼
┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│  流式响应    │◀───│ DeepSeek   │◀───│ Prompt 构建 │◀───│ 检索结果    │
│  (SSE)      │    │ API         │    │ (RAG Prompt)│    │ (Top-K)     │
└─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘
```

### 安全架构

#### JWT 认证流程

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│  用户登录    │───▶│ 验证凭证    │───▶│ 生成 JWT    │───▶│ 返回 Token   │
│             │    │ (UserService)│   │ (JwtUtils)  │    │             │
└─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘
                                                                   │
                           后续请求                                  ▼
┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│  请求携带   │───▶│ 过滤器解析  │───▶│ 验证签名    │───▶│ 权限校验    │
│  JWT Token  │    │ (JwtAuth)   │    │ & 过期检查  │    │ (OrgTag)    │
└─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘
```

#### 组织标签权限模型

```
┌─────────────────────────────────────────────────────────────────┐
│                      组织标签层级结构                              │
│                                                                 │
│                    ┌──────────────┐                             │
│                    │   项目组 A    │ (root)                      │
│                    └──────┬───────┘                             │
│              ┌────────────┼────────────┐                        │
│              ▼            ▼            ▼                        │
│     ┌────────────┐ ┌────────────┐ ┌────────────┐               │
│     │  子项目 A1  │ │  子项目 A2  │ │  子项目 A3  │               │
│     └──────┬─────┘ └──────┬─────┘ └────────────┘               │
│            │              │                                     │
│            ▼              ▼                                     │
│     ┌────────────┐ ┌────────────┐                               │
│     │  研发组    │ │  测试组    │                                │
│     └────────────┘ └────────────┘                               │
└─────────────────────────────────────────────────────────────────┘

权限规则：
• 用户可访问：自己的文档 + 公开文档 + 所属组织的文档
• 组织继承：子组织自动继承父组织的访问权限
```

### 基础设施

| 服务 | 用途 | 端口 |
|------|------|------|
| MySQL 8 | 主数据库，存储用户、文档、对话等核心数据 | 3306 |
| Redis | 缓存服务，存储 JWT Token、会话数据、组织标签缓存 | 6379 |
| Elasticsearch 8.10 | 向量数据库，支持语义搜索和关键词搜索 | 9200 |
| Kafka | 异步消息队列，触发文档处理任务 | 9092 |
| MinIO | 对象存储，存储上传的原始文件 | 19000 |
| DeepSeek API | AI 大模型，提供 RAG 生成能力 | - |

### 技术选型理由

| 组件 | 选型 | 理由 |
|------|------|------|
| **Spring Boot** | 3.4.2 | 成熟的微服务框架，生态完善 |
| **Vue 3** | Composition API | 更好的 TypeScript 支持和逻辑复用 |
| **Pinia** | 状态管理 | 官方推荐，比 Vuex 更轻量简洁 |
| **Elasticsearch** | 8.10 | 同时支持向量搜索和全文搜索 |
| **Kafka** | 异步处理 | 解耦文件处理流程，支持高并发 |
| **MinIO** | 对象存储 | S3 兼容，自托管保证数据安全 |
| **Tika** | 文档解析 | 支持 1400+ 文档格式 |

## 贡献指南

欢迎为天琴RAG智能知识库平台贡献代码！请遵循以下步骤：

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
