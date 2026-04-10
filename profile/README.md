# OpenJWC - 私有化部署教务资讯系统

> 一个完整的私有化部署教务资讯系统，集成AI对话、多平台客户端、智能爬虫和可视化管理面板

## 项目简介

OpenJWC是一个面向高校的私有化部署教务资讯系统，旨在为学生提供及时、准确的教务信息获取服务。系统采用现代化的微服务架构，支持多终端访问，并集成了先进的AI对话功能，为用户提供智能化的资讯查询和交互体验。

### 核心特性

- **智能信息爬取**: 自动化爬取教务官网资讯，确保信息及时更新
- **AI智能对话**: 基于大语言模型的智能问答系统，支持上下文理解
- **多平台支持**: Android移动客户端、Web管理面板、QQ机器人等多终端
- **现代化界面**: Material Design 3风格的移动端，响应式Web管理界面
- **私有化部署**: 支持本地部署，数据完全自主可控
- **高性能架构**: 异步处理、容器化部署
- **数据持久化**: SQLite关系数据库 + ChromaDB向量数据库双存储

## 系统架构

OpenJWC采用分布式微服务架构，包含以下核心组件：

```
┌─────────────────────────────────────────────────────────────┐
│                    OpenJWC-Server                           │
│                 (Docker Orchestration)                      │
└──────────────┬──────────────────────────────────────────────┘
               │
               ├─> backend (OpenJWC-webapi Python/FastAPI)
               │    ├─> SQLite (关系数据库)
               │    ├─> ChromaDB (向量数据库)
               │    ├─> DeepSeek/智谱AI (大语言模型)
               │    └─> crawler_wrapper (爬虫调度)
               │
               ├─> crawler_worker (定时爬虫服务)
               │    └─> JwcCrawler (Rust爬虫引擎)
               │
               └─> frontend (Web管理面板)
                    └─> React + TypeScript + TailwindCSS

┌─────────────────────────────────────────────────────────────┐
│                    客户端应用                               │
└─────────────────────────────────────────────────────────────┘
               │
               ├─> OpenJWCClient (Android/Kotlin)
               │    └─> Jetpack Compose + Material Design 3
               │
               └─> openjwc-qqbot (Python/NoneBot)
                    └─> QQ群消息推送
```

## 技术栈

### 后端服务

- **编程语言**: Python 3.12+
- **Web框架**: FastAPI + Uvicorn
- **数据库**: SQLite + ChromaDB (向量数据库)
- **AI服务**: DeepSeek API、智谱AI API
- **认证授权**: JWT + bcrypt密码加密
- **HTTP客户端**: httpx (异步HTTP请求)

### 爬虫引擎

- **编程语言**: Rust
- **Web框架**: reqwest (异步HTTP客户端)
- **HTML解析**: scraper (HTML解析库)
- **并发处理**: rayon (并行处理)

### Web管理面板

- **前端框架**: React 18 + TypeScript
- **构建工具**: Vite 5
- **UI框架**: TailwindCSS + Material Design
- **状态管理**: Redux Toolkit + Redux Persist
- **图表库**: ECharts
- **表单处理**: React Hook Form + Zod验证

### Android客户端

- **编程语言**: Kotlin
- **UI框架**: Jetpack Compose + Material Design 3
- **架构模式**: MVVM (Model-View-ViewModel)
- **网络请求**: OkHttp3 + Retrofit
- **本地存储**: Room Database + DataStore
- **依赖注入**: KSP (Kotlin Symbol Processing)

### QQ机器人

- **编程语言**: Python
- **机器人框架**: NoneBot2 (异步框架)
- **通信协议**: OneBot协议
- **WebSocket通信**: 异步WebSocket连接

### 部署编排

- **容器化**: Docker + Docker Compose
- **反向代理**: Nginx
- **服务编排**: docker-compose.yml多容器编排

## 快速开始

### 环境要求

- Docker 20.10+
- Docker Compose 2.0+
- Python 3.12+ (用于后端开发)
- Node.js 18+ (用于Web前端开发)
- Android Studio (用于Android客户端开发)

### 一键部署

1. **克隆服务器部署仓库**

   ```bash
   git clone https://github.com/OpenJWC/OpenJWC-Server.git
   cd OpenJWC-Server
   ```

2. **启动服务**

   ```bash
   docker-compose up -d
   ```

3. **访问管理面板**
   打开浏览器访问: `http://your-server-ip:8000`

详细部署指南请参考 [快速开始.md](快速开始.md)

## 文档导航

- [技术架构.md](技术架构.md) - 系统架构设计和技术实现细节
- [API文档.md](API文档.md) - 完整的API接口文档
- [项目介绍.md](项目介绍.md) - 项目详细介绍和功能说明
- [用户手册.md](用户手册.md) - 各端用户使用指南
- [快速开始.md](快速开始.md) - 快速部署和配置指南

## 核心功能

### 学生用户功能

- **实时资讯**: 浏览最新的教务通知、公告、考试安排等
- **智能搜索**: AI驱动的自然语言搜索，快速找到相关信息
- 智能解读：指定资讯作为附件添加至上下文，方便获取精准解读。

### 管理员功能

- **系统监控**: 实时监控系统运行状态、性能指标
- **用户管理**: 管理用户注册、API密钥分配、设备限制
- **内容管理**: 审核和管理爬取的资讯内容
- **配置管理**: 可视化配置系统参数、AI服务、爬虫策略

### AI对话功能

- **知识问答**: 基于教务资讯知识库的智能问答
- **上下文理解**: 支持多轮对话，理解上下文语境
- **内容关联**: 可将具体资讯内容与对话关联，提供精准解答

## 安全特性

- **数据加密**: 敏感数据加密存储和传输
- **访问控制**: 基于JWT的身份认证和权限管理
- **API密钥**: 客户端API密钥认证，支持设备数量限制
- **SQL注入防护**: 使用参数化查询，防止SQL注入攻击
- **CORS配置**: 合理的跨域资源共享策略

## 项目亮点

1. **技术先进性**: 采用最新的技术栈和最佳实践
2. **架构设计**: 微服务架构，模块化设计，易于扩展和维护
3. **用户体验**: 现代化UI设计，流畅的用户交互体验
4. **AI集成**: 深度集成大语言模型，提供智能化服务
5. **开源生态**: 完全开源，便于社区贡献和二次开发
6. **私有化部署**: 支持本地部署，数据安全和隐私保护
7. **多终端支持**: 覆盖Android、Web、QQ等多个平台

## 项目数据

- **代码仓库**: monorepo结构，包含多个子模块
- **代码行数**: 总计约17,000行代码
- **支持平台**: Android、Web、QQ Bot
- **数据库**: SQLite + ChromaDB (向量数据库)
- **API接口**: 30+ RESTful API接口
- **并发支持**: 异步架构，支持高并发访问

## 贡献指南

我们欢迎任何形式的贡献，包括但不限于：

- Bug修复
- 新功能开发
- 文档改进
- UI/UX优化
- 性能优化

## 联系方式

- **项目主页**: [OpenJWC GitHub Organization](https://github.com/OpenJWC)
- **问题反馈**: [GitHub Issues](https://github.com/OpenJWC/OpenJWC-Server/issues)
- **技术讨论**: 欢迎在Issues中提出技术问题

## 开源协议

本项目采用 [MIT License](开源声明.md) 开源协议，详细信息请查看开源声明文档。

## 致谢

感谢以下开源项目和技术社区的支持：

- [FastAPI](https://fastapi.tiangolo.com/) - 现代化的Python Web框架
- [React](https://react.dev/) - 用于构建用户界面的JavaScript库
- [Jetpack Compose](https://developer.android.com/jetpack/compose) - Android现代UI工具包
- [ChromaDB](https://www.trychroma.com/) - 开源向量数据库
- [DeepSeek](https://www.deepseek.com/) - AI大语言模型服务
- [NoneBot2](https://nonebot.dev/) - 异步Python机器人框架

**OpenJWC - 让教务资讯获取更智能、更便捷！**
