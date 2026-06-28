---
title: "Hermes Skills Overview"
created: "2026-06-28"
updated: "2026-06-28"
tags: [hermes, skills, agent, tools, automation]
---

# Hermes Agent Skills 总览

当前 Hermes Agent v0.15.1 共注册 **34 个 Skill 目录**，其中 **8 个完整配置（含 SKILL.md）**，其余为已预留分类但尚未填充内容的骨架目录。

## 一、完整 Skill（已配置 SKILL.md）

| Skill | 分类 | 描述 | 附加文件 |
|-------|------|------|---------|
| `apikey-image-gen` | 图像生成 | 通过 Hermes Web UI 使用 fun-codex provider 生成/编辑图像 | — |
| `codex-app-troubleshooting` | 开发工具 | 故障排查 Codex CLI/Desktop App 的模型路由、配置层、LocalStorage 冲突等问题 | references/, scripts/ |
| `grok-image-to-video` | 视频生成 | 使用 xAI Grok Imagine 将本地图片动画化为短视频 | — |
| `hermes-config` | 配置管理 | Profile 配置、provider 设置、模型路由、Web UI 生命周期管理 | references/, scripts/ |
| `hyperframes` | 视频创作 | 使用 HTML/CSS/JS 创建 AI 视频作品，支持多场景、GSAP 动画、渲染 MP4 | references/ |
| `markdown-viewer` | 可视化 | 在 Markdown 中创建丰富的图表、数据可视化、技术架构视图 | — |
| `remotion` | 视频创作 | 使用 Remotion + React 创建可编辑的 AI 视频项目 | — |
| `yuanbao` | 社交 | 元宝（企业微信）群组：@提及用户、查询信息/成员 | — |

## 二、骨架目录（待填充 SKILL.md）

这些目录已创建但缺少 SKILL.md 文件，属于预留分类：

### Agent 相关
- `autonomous-ai-agents` — 自治 AI 代理/多代理工作流
- `hermes-agent-tools` — Hermes Agent 工具配置

### 开发工具
- `mcp` — Model Context Protocol 相关
- `software-development` — 软件开发工作流
- `github` — GitHub 操作
- `codex-app-troubleshooting` 已有完整内容

### 创意与多媒体
- `creative` — 创意内容生成（ASCII art、手绘风格图表等）
- `media` — 媒体内容（YouTube 转录、GIF 搜索、音乐生成、音频可视化）
- `image-analysis` — 图片分析
- `diagramming` — 图表绘制
- `gifs` — GIF 搜索/下载

### 数据与搜索
- `data-science` — 数据科学工作流
- `search` — 搜索相关
- `research` — 研究辅助

### 本地与推理
- `local-llm-tools` — 本地 LLM 工具
- `inference-sh` — 模型推理脚本
- `mlops` — ML Ops（评估、服务、量化等）

### 社交与沟通
- `social-media` — 社交媒体
- `email` — 邮件处理
- `note-taking` — 笔记记录
- `domain` — 域名相关

### 硬件/娱乐
- `smart-home` — 智能家居
- `gaming` — 游戏相关
- `apple` — Apple 生态（CapCut/剪映、macOS 计算机使用等）

### 信息收集
- `red-teaming` — 红队测试
- `devops` — DevOps 工作流

## 三、嵌套 Skill（子目录结构）

部分 Skill 目录包含子 Skill：

```
media/          ── 多媒体
  └── heartmula/   ── AI 音乐生成（Suno 风格）
  └── songsee/     ── 音频频谱分析
  └── spotify/     ── Spotify 播放/搜索/管理
  └── gif-search/  ── Tenor GIF 搜索
  └── agnes-video-v2.0/     ── Agnes 视频生成
  └── agnes-image-2.1-flash/ ── Agnes 图像生成
  └── agnes-media-plugins/   ── Agnes 媒体注册
  └── heartmula/
  └── jianying/               ── 剪映相关

autonomous-ai-agents/ ── 自治代理
  └── hermes-agent/   ── Hermes Agent 自身配置/扩展
  └── hermes-profile-provider-setup/ ── Provider 配置

productivity/ ── 生产力工具
  └── airtable/ ── Airtable API
  └── google-workspace/ ── Gmail/Calendar/Drive/Docs
  └── linear/ ── 项目管理
  └── notion/ ── Notion API
  └── maps/ ── 地图/地理编码
  └── nano-pdf/ ── PDF 编辑
  └── ocr-and-documents/ ── OCR/文档提取
  └── teams-meeting-pipeline/ ── Teams 会议摘要
  └── powerpoint/ ── PPT 创建

software-development/ ── 开发
  └── codex-desktop-local-storage-fix/ ── Codex LocalStorage 修复

data-science/ ── 数据科学
  └── jupyter-live-kernel/ ── Jupyter 实时内核

mlops/ ── ML Ops
  └── evaluating-llms-harness/ ── 模型评估
  └── weights-and-biases/ ── W&B 实验追踪
  ├── inference/ ── 推理相关
  │   └── llama-cpp/ ── GGUF 推理
  │   └── obliteratus/ ── 模型安全
  ├── models/ ── 模型架构
  │   └── segment-anything-model/ ── SAM 图像分割
  └── research/ ── ML 研究
      └── dspy/ ── DSPy 框架

creative/ ── 创意
  └── humanizer/ ── 文本人性化
  └── ideation/ ── 创意生成
  └── pretext/ ── 浏览器 Demo

search/ ── 搜索
  └── aihot/ ── AI 热点资讯查询

mcp/ ── MCP 协议
  └── native-mcp/ ── MCP 客户端/服务配置

hermes-agent-tools/ ── 工具配置
  └── hermes-provider-config/ ── 自定义 LLM API 端点配置

note-taking/ ── 笔记
  └── obsidian/ ── Obsidian 笔记操作

apple/ ── Apple 生态
  ├── capcut-editing/ ── CapCut/剪映自动化
  └── macos-computer-use/ ── macOS 桌面自动化

social-media/ ── 社交媒体
  └── xurl/ ── X/Twitter CLI 操作

email/ ── 邮件
  └── himalaya/ ── Himalaya CLI 邮件管理

devops/ ── DevOps
  └── webhook-subscriptions/ ── Webhook 订阅

local-llm-tools/ ── 本地 LLM
  ├── inference-provider-config/ ── 推理 provider 配置
  └── ollama-model-management/ ── Ollama 模型管理

red-teaming/ ── 红队
  └── godmode/ ── LLM 越狱测试

smart-home/ ── 智能家居
  └── openhue/ ── Philips Hue 灯控

## 四、Skill 使用建议

略

## 五、同步信息

- 本文件由 Hermes Agent 自动生成
- 知识库：`~/.hermes/wiki/` → GitHub: `yongkangliang/knowledge_base`
- Hermes 版本：v0.15.1
