---
title: "Agent Skills 清单"
created: 2026-06-28
updated: 2026-06-28
tags: [skills, agent, claude-code]
sources: ["https://github.com/yongkangliang/knowledge_base"]
---

# Agent Skills 清单

Claude Code / Hermes Agent 可用的技能（Skills）完整列表。

## 基础技能

| 技能 | 触发条件 |
|------|----------|
| `init` | 初始化新的 CLAUDE.md 文件 |
| `review` | 审查 pull request |
| `verify` | 验证代码变更是否生效 |
| `run` | 启动项目应用查看变更效果 |

## 代码质量

| 技能 | 触发条件 |
|------|----------|
| `code-review` | 审查当前 diff 的正确性和优化空间 |
| `simplify` | 审查代码的可复用性、简洁性和效率 |
| `security-review` | 审查待处理变更的安全性 |

## 配置与管理

| 技能 | 触发条件 |
|------|----------|
| `update-config` | 配置 settings.json、权限、环境变量、hooks |
| `keybindings-help` | 自定义键盘快捷键 |
| `setup-cowork` | 引导式 Cowork 插件配置 |
| `consolidate-memory` | 记忆文件合并去重 |

## 定时任务

| 技能 | 触发条件 |
|------|----------|
| `schedule` | 创建或更新定时任务 |
| `loop` | 周期性运行任务（如 /loop 5m） |

## 文档处理

| 技能 | 触发条件 |
|------|----------|
| `anthropic-skills:docx` | 创建/读取/编辑 Word 文档 (.docx) |
| `anthropic-skills:pdf` | 创建/合并/拆分/加密/填充 PDF |
| `anthropic-skills:pdf-reading` | 读取/提取 PDF 内容 |
| `anthropic-skills:pptx` | 创建/读取/编辑 PowerPoint 演示文稿 |
| `anthropic-skills:xlsx` | 创建/读取/编辑电子表格 |

## 前端与设计

| 技能 | 触发条件 |
|------|----------|
| `anthropic-skills:frontend-design` | UI 视觉设计方向指导 |

## AI 生成

| 技能 | 触发条件 |
|------|----------|
| `wanvideo` | 使用 WanVideo 进行视频生成 |
| `deep-research` | 深度研究、多源验证、事实核查报告 |
| `anthropic-skills:agnes-image-gen` | 文本生成图片 |
| `anthropic-skills:agnes-video-gen` | 文本/图片生成视频 |

## Claude 生态

| 技能 | 触发条件 |
|------|----------|
| `claude-api` | Claude API / Anthropic SDK 参考（模型ID、定价、参数等） |
