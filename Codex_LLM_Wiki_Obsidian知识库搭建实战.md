# Codex + LLM Wiki + Obsidian 知识库搭建完整指南

> **说明**: 基于 ComfyUI / LoRA 工作流 → Codex CLI → LLM API → Obsidian笔记，构建个人知识管理系统  
> **创建日期**: 2026-06-11  
> **标签**: #Codex #LLMWiki #Obsidian #知识库搭建 #ComfyUI #工作流

---

## 📋 目录

1. [系统架构总览](#1-系统架构总览)
2. [工具选型与安装](#2-工具选型与安装)
3. [自动化脚本模板](#3-自动化脚本模板)
4. [代码范例](#4-代码范例)
5. [最佳实践](#5-最佳实践)

---

## 1. 系统架构总览

```
┌──────────────────────────────────────────────────────────────┐
│                    知识管理全景架构                            │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  🎨 图像生成层                                              │
│     ComfyUI ─┐                                               │
│     IP-Adapter├────▶ LORA模型库 ──▶ Prompt模板库             │
│     ControlNet┘                     (Q版/3D/国潮风格)         │
│                              ▼                               │
│  🤖 AI 代理层                                              │
│     Codex CLI / Desktop ────▶ Web抓取 + 网页解析              │
│     ├── 自动化: 读取网页 → 提取要点 → 生成结构化笔记          │
│     ├── LoRA分析: 预览图识别 → 提示词优化 → 风格参数推荐      │
│     └── 批量处理: 多页面抓取 + 格式统一 + 标签管理            │
│                              ▼                               │
│  📚 LLM Wiki 层                                              │
│     GitHub wiki + Notion API ──▶ 结构化知识图谱               │
│     ├── 技术文档归档 (ComfyUI节点/LoRA手册/模型对比)           │
│     ├── 学习总结归档 (课程笔记/Claude Code/工作流模板)         │
│     └── Prompt仓库 (正/负面提示词/参数配置表)                 │
│                              ▼                               │
│  📝 Obsidian 知识库层                                        │
│     /Users/km/Desktop/kang/obsidian/knowledge_base/           │
│     ├── 按主题分文件夹 (图像风格/LORA模型/AI工具使用指南)       │
│     ├── 统一 Markdown 格式 (YAML Frontmatter + Tags)          │
│     └── Link 互链 (Mermaid / Wikilinks / Backlinks)           │
└──────────────────────────────────────────────────────────────┘
```

---

## 2. 工具选型与安装

### 2.1 核心组件清单

| 组件 | 用途 | 获取方式 |
|------|------|---------|
| **ComfyUI** | AI图像生成 (LoRA管理/节点编排) | GitHub: `comfyanonymous/ComfyUI` |
| **Codex CLI / Desktop** | 网页抓取 + LLM学习归档 | https://github.com/openai/codex |
| **Obsidian** | 个人知识库 (Markdown笔记) | https://obsidian.md |
| **Git + GitHub Wiki** | 团队/版本化 wiki | `git clone git@github.com/user/repo.wiki.git` |
| **Python (requests/fvcore)** | 自动化脚本/数据批处理 | `pip install requests beautifulsoup4 pymarkdown` |

### 2.2 ComfyUI 安装与配置

```bash
# 1. ComfyUI 基础安装
cd ~/projects
git clone https://github.com/comfyanonymous/ComfyUI.git
cd ComfyUI
python -m pip install -r requirements.txt

# 2. 模型目录结构
mkdir -p models/loras models/checkpoints models/controlnet \
         models/ip-adapters models/vae

# 3. 安装关键插件 (通过 ComfyUI Manager)
ComfyUI manager → Install → 搜索:
├── ComfyUI-Impact-Pack          # 面部识别与分割
├── ComfyUI-Advanced-ControlNet  # 多ControlNet支持
├── ComfyUI_IP-Adapter_plus      # IP-Adapter支持
└── ComfyUI-Custom-Scripts       # 批量处理辅助

# 4. Model 文件放置位置参考
~/ComfyUI/models/loras/           ← LoRA模型 (.safetensors)
~/ComfyUI/models/checkpoints/     ← Stable Diffusion基座模型
~/ComfyUI/models/controlnet/      ← ControlNet预训练权重
```

### 2.3 环境变量配置

```bash
# ~/.zshrc 或 ~/.bash_profile
export COMFYUI_HOME="$HOME/projects/ComfyUI"
export MODEL_DIR="$COMFYUI_HOME/models"
export OUTPUT_DIR="$COMFYUI_HOME/output"
export OBSIDIAN_KB="/Users/km/Desktop/kang/obsidian/knowledge_base"

# ComfyUI API 端口
export COMFYUI_API_PORT=8188

# CUDA (如果可用)
export TORCH_CUDA_ARCH_LIST="8.6;8.9"  # RTX 30xx/4xxx
```

### 2.4 Obsidian 知识管理设置

| 配置项 | 推荐值 |
|--------|--------|
| **根路径** | `/Users/km/Desktop/kang/obsidian/knowledge_base/` |
| **文件夹分类策略** | 01_LoRA模型 / 02_工具指南 / 03_AI技术笔记 / 04_Prompt模板 |
| **文件命名规范** | `主题_日期_简要描述.md` (例如: `IP_Q版LoRA分析_2026-06-11.md`) |
| **Obsidian插件** | Templater, Dataview, Excalidraw, QuickAdd |

---

## 3. 自动化脚本模板

### 3.1 网页内容抓取 + Obsidian 笔记生成

```python
#!/usr/bin/env python
"""auto_knowledge.py - 从网页自动抓取并生成Obsidian笔记"""

import requests
import re
from pathlib import Path
from datetime import datetime
import yaml

def extract_webpage_content(url):
    """提取URL内容并解析关键信息"""
    headers = {
        "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7)"
    }
    response = requests.get(url, headers=headers, timeout=30)
    html = response.text
    
    # 提取标题
    title_match = re.search(r'<title>(.*?)</title>', html)
    title = title_match.group(1) if title_match else "未命名页面"
    
    # 提取关键词
    keywords_match = re.search(
        r'meta\s+name="keywords"\s+content="(.*?)"', 
        html, re.IGNORECASE
    )
    tags = [k.strip() for k in keywords_match.group(1).split(',')] if keywords_match else []
    
    # 提取描述
    desc_match = re.search(
        r'meta\s+name="description"\s+content="(.*?)"', 
        html, re.IGNORECASE
    )
    description = desc_match.group(1) if desc_match else ""
    
    return title, tags, description

def generate_obsidian_note(title, tags, content_raw, output_dir):
    """生成 Obsidian Markdown 笔记"""
    now = datetime.now()
    filename = f"{title[:20]}_{now.strftime('%Y-%m-%d')}.md"
    
    note_content = f"""---
title: "{title}"
date: {now.strftime('%Y-%m-%d')}
tags:
""" + "\n".join([f"  - #{tag}" for tag in tags]) + """
source_url: "<url>"
status: research

---
# [Title]

## 📋 摘要

> {description}

## 🎯 核心要点

1. Point One
2. Point Two  
3. Point Three

## 🔧 技术细节

### Sub Topic A

```json
// relevant code/config
```

### Sub Topic B

- Sub-point detail
- Another sub-point

## 💡 行动项

- [ ] Action Item
- [ ] Follow-up research needed

---
> Generated by Codex Auto-Note Bot
"""
    
    output_path = Path(output_dir) / filename
    output_path.write_text(note_content, encoding="utf-8")
    return str(output_path), title

# =========================================
# 使用示例 (替换 URL):
# =========================================
if __name__ == "__main__":
    TARGET_URL = "..."
    OUTPUT_DIR = "/Users/km/Desktop/kang/obsidian/knowledge_base/"
    
    filename, name = generate_obsidian_note(
        extract_webpage_content(TARGET_URL),
        OUTPUT_DIR
    )
    print(f"✅ Note saved: {filename}")
```

### 3.2 ComfyUI LoRA文件管理 + 自动生成分析笔记

```python
#!/usr/bin/env python
"""lora_analyzer.py - 分析本地LoRA模型并生成知识库笔记"""

import os
import json
from pathlib import Path
from datetime import datetime

def scan_lora_directory(directory):
    """扫描 LoRA 目录并生成模型概览"""
    loras = []
    path = Path(directory)
    
    for f in path.rglob("*.safetensors"):
        size_mb = f.stat().st_size / (1024 * 1024)
        stem = f.stem[:50]  # 截断过长名称
        loras.append({
            "name": stem,
            "path": str(f),
            "size_mb": round(size_mb, 1),
            "ext": "safetensors",
            "created": datetime.fromtimestamp(
                f.stat().st_ctime
            ).strftime("%Y-%m-%d"),
        })
    
    return loras

def generate_lora_note(lora_entry):
    """生成 LoRA 模型分析笔记"""
    now = datetime.now()
    
    template = '''---
title: "{name}"
date: {date}
tags: [LORA, 3DRender, IPStyle]
model_size_mb: {size}
source: ComfyUI Models
status: research
---

# {name} LoRA 模型分析

## 📋 基本信息

| 属性 | 值 |
|------|----|
| **文件大小** | {size} MB |
| **存储路径** | `[Path]` |
| **创建日期** | {date} |

## 🎨 风格特征 (待确认)

- Q版卡通 / 3D渲染 / IP设计
- 国潮元素 + 传统装饰
- 高细节纹理表现

## ⚙️ ComfyUI 推荐配置

| 参数 | 值 |
|------|----|
| **LoRA强度** | `0.6~0.8` |
| **Steps** | 25~30 (SDXL) / 20 (Flux) |
| **CFG Scale** | 4.5~6.5 |
| **Sampler** | dpmpp_2m_sde_gpu |

## 💡 使用建议

1. 搭配 LoRA混合策略：风格 LoRA + 服饰/场景 LoRA
2. 注意底座匹配 (SDXL vs SD1.5 vs Flux)
3. img2img 精调 (denoise≈0.4) 可提升细节

---
> Auto-generated by Codex LORA Analyzer
'''
    
    return template.format(
        name=lora_entry["name"],
        date=now.strftime("%Y-%m-%d"),
        size=lora_entry["size_mb"],
        path=lora_entry["path"],
    )

if __name__ == "__main__":
    LORA_DIR = "/Users/km/Desktop/kang/software/comfyui/loras-人物-服饰-风格小模型/"
    OUTPUT_DIR = "/Users/km/Desktop/kang/obsidian/knowledge_base/"
    
    loras = scan_lora_directory(LORA_DIR)
    print(f"Found {len(loras)} LoRA files")
    
    for entry in loras:
        note = generate_lora_note(entry)
        filename = f"LORA_{entry['name'][:10]}_{datetime.now().strftime('%Y-%m-%d')}.md"
        Path(OUTPUT_DIR, filename).write_text(note, encoding="utf-8")
        print(f"✅ Generated: {filename}")
```

### 3.3 Codex CLI 提示词模板库生成器

```python
#!/usr/bin/env python
"""prompt_library_generator.py - 自动收集优质提示词到 Obsidian"""

from pathlib import Path
import json, yaml
from datetime import datetime

def build_prompt_template(prompt_type="q_version"):
    """为不同类型的Prompt生成标准化模板"""
    
    templates = {
        "q_version": {
            "正面": "(masterpiece, best quality:1.2), Q version style, chibi character, 3D render",
            "负面": "lowres, bad anatomy, worst quality, deformed, ugly...",
            "适用LoRA": ["IP_Q版_3D风格", "国风插画"],
        }
    }
    
    return templates.get(prompt_type, templates["q_version"])

def export_to_obsidian(templates_dict, output_file):
    """导出为 Obsidian Markdown"""
    content = '''---
title: "Prompt 提示词模板库"
date: {date}
tags: [PromptLibrary, ComfyUI, AIImage]
status: reference
---

# Prompt Library - {category}

## 🎯 {category} Templates

| Type | Template |
|------|----------|
''' + "\n".join(
f"| **{k}** | `{v}` |" for k,v in templates_dict.items()
)

    Path(output_file).write_text(content, encoding="utf-8")
    return output_file

# ==================================================
if __name__ == "__main__":
    OUTPUT_DIR = "/Users/km/Desktop/kang/obsidian/knowledge_base/"
    filename = export_to_obsidian(
        build_prompt_template("q_version"),
        f"{OUTPUT_DIR}/PromptLibrary_Q版_3D风格.md"
    )
    print(f"✅ Prompt Library: {filename}")
```

---

## 4. 代码范例

### 4.1 ComfyUI API 调用 (REST)

```bash
#!/bin/bash
# 通过 REST API 批量生成图片并保存结果

COMFYUI_URL="http://localhost:8188"

# 提交工作流到 ComfyUI
curl -X "$COMFYUI_URL/prompt" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": {
      "1": {
        "class_type": "CheckpointLoaderSimple",
        "inputs": {
          "ckpt_name": "sd_xl_base_1.0.safetensors"
        }
      },
      "2": {
        "class_type": "LoraLoader",
        "inputs": {
          "model": [1, 0],
          "clip": [1, 1],
          "lora_name": "IP_Q版_3D_十二生肖_v1.safetensors",
          "strength_model": 0.7,
          "strength_clip": 0.7
        }
      }
    }
  }'

echo "✅ Workflow submitted to ComfyUI"
```

### 4.2 Obsidian Dataview Query

在 Obsidian 中使用 **Dataview** 插件进行高级检索：

````markdown
```dataview
TABLE model_size_mb as "大小", status as "状态"
FROM #LORA AND "01_LoRA模型"
SORT date DESC
LIMIT 20
```
````

### 4.3 ComfyUI JSON Workflow Export

```json
{
  "nodes": [
    {
      "id": 1,
      "type": "CheckpointLoaderSimple",
      "inputs": {"ckpt_name": "sd_xl_base_1.0.safetensors"}
    },
    {
      "id": 2,
      "type": "LoraLoader",
      "inputs": {
        "model": [1, 0],
        "clip": [1, 1],
        "lora_name": "IP_Q版_3D_十二生肖_v1.safetensors",
        "strength_model": 0.7,
        "strength_clip": 0.7
      }
    },
    {
      "id": 5,
      "type": "KSamplerAdvanced",
      "inputs": {
        "model": [2, 0],
        "positive": [3, 0],
        "negative": [4, 0],
        "sampler": "dpmpp_2m_sde_gpu",
        "scheduler": "karras",
        "steps": 30,
        "denoise": 1.0
      }
    },
    {
      "id": 8,
      "type": "SaveImage",
      "inputs": {
        "images": [7, 0],
        "filename_prefix": "q_version_zodiac_3d"
      }
    }
  ]
}
```

---

## 5. 最佳实践

### 5.1 ComfyUI LoRA 管理最佳实践

| 项目 | 建议 |
|------|-----|
| **文件夹按风格分类** | `models/loras/q_version/`, `models/loras/3d_render/` |
| **LoRA使用策略** | 优先单个，其次双 LoRA(0.4+0.3)，避免三层叠加 |
| **Prompt优化方法** | 固定正/负面提示词 → 调LoRA强度 → 换采样器 |
| **工作流备份** | 每个优秀 workflow 导出为 `.json` + 命名 `best_practice_*.json` |
| **质量检查清单** | 分辨率 ≥1024，无坏手/变形，光影自然，色彩协调 |

### 5.2 Codex 自动化最佳实践

```markdown
### Codex CLI 指令模板

- "抓取 URL 内容并总结要点"  
- "分析 LoRA 模型文件并生成 ComfyUI 参数推荐表"
- "把教程笔记整理为 Obsidian Markdown + YAML Frontmatter"
- "批量处理文件夹中的所有 .safetensors 并生成知识库条目"
```

### 5.3 Obsidian 知识库组织方法

````markdown
**文件夹结构建议**:
```
knowledge_base/
├── 01_LoRA模型/          ← LoRA相关笔记
│   ├── IP_Q版LoRA分析_2026-06-11.md
│   └── LORA_国风3D风格_2026-06-10.md
├── 02_工具指南/          ← 软件/工具使用教程
│   ├── ComfyUI工作流搭建.md
│   ├── CodexCLI操作指南.md
│   └── ControlNet进阶用法.md
├── 03_AI技术笔记/        ← AI论文/技术解读
│   ├── StableDiffusion原理.md
│   └── IP-Adapter原理与实战.md
└── 04_Prompt模板/        ← Prompt库
    ├── Q版IP提示词模板.md
    └── ComfyUI节点参数速查表.md
```

**Obsidian 链接语法**:
```markdown
[[相关笔记标题]]      <!-- Wikilink -->
![Alt](./path/to/img.png)       <!-- 本地图片引用 -->
[#标签名]                        <!-- Dataview 查询触发 -->
```

### 5.4 知识库维护建议

| 任务 | 频率 | 操作 |
|------|------|------|
| **新增模型笔记** | 实时/每次导入 | `Codex CLI` → 自动抓取+生成Markdown |
| **清理失效链接** | Weekly | Dataview + `datamodel:missing` |
| **Prompt迭代记录** | Daily | 记录"参数变更对比"到对应 Prompt 页 |
| **ComfyUI 工作流归档** | Daily | `.json` → `04_ComfyUI_Workflows/` |
| **知识图谱可视化** | Weekly | Excalidraw / Graph View |

---

## 📊 系统架构速查表

| 层级 | 工具 | 输入 | 输出 |
|------|------|------|-----|
| 🎨 图像生成 | ComfyUI + LoRA | Prompt + 模型文件 | .PNG/.WEBP |
| 🤖 AI代理 | Codex CLI | URL/Page内容 | 结构化笔记 |
| 📚 wiki/文档 | Git/GitHub | 技术文档/笔记.md | HTML wiki Pages |
| 📝 个人知识库 | Obsidian | Markdown笔记 + 图片 | Graph View / Full-text Search |

---

> ✅ **下一步**:  
> 1. 安装并配置 ComfyUI (按上述目录结构)  
> 2. 将 `01_LoRA模型/` 等文件夹在 Obsidian 中建立  
> 3. 使用 Codex CLI 批量导入现有 LoRA 到 wiki  
> 4. 定期更新 Prompt 库并关联 ComfiUI workflow

