# IP · Q版· 3D 十二生肖 LoRA 模型详解

> **来源**: `/Users/km/Desktop/kang/software/comfyui/loras-人物-服饰-风格小模型/`  
> **标签**: #ComfyUI #LoRA #Q版 #3DRender #国潮IP #十二生肖 #2DStyleLearning

---

## 📋 模型概览

| 属性 | 说明 |
|------|------|
| **模型名称** | IP · Q版 · 3D 十二生肖_v1 |
| **文件类型** | `.safetensors` (约 150 MB) |
| **预览图** | `IP_Q版_3D_十二生肖_v1.webp` (764×1146 px, VP8 WebP) |
| **风格定位** | 3D渲染 + 国潮IP + Q版卡通 + 十二生肖主题 |
| **推荐底座** | SDXL 1.0 / Flux.1 (dev/schnell) |

---

## 🎨 核心视觉特征

通过该 LoRA 预览图分析，它的风格特点：

- **Q版比例**: 二到三头身，大头娃娃感
- **3D渲染质感**: 类似于 Blender/C4D 出来的立体感
- **国潮IP设计**: 中国传统生肖元素 + 现代潮流风格
- **可爱风格**: 大眼睛、圆润身体、表情俏皮
- **精细细节**: 材质表现出众，光影质感真实

---

## ⚙️ ComfyUI 工作流搭建

### 基础文生图流程 (txt2img)

```
┌──────────────┐     ┌───────────────┐     ┌───────────────┐
│ Load Checkpoint│──▶│ CLIP Encode POS│──▶│ KSampler      │
│ SDXL / Flux    │     │ prompt text   │     │ dpmpp_2m_sde  │
└──────┬───────┘     └───────────────┘     └───────┬───────┘
       │                                            │
┌──────┴───────┐     ┌───────────────┐     ┌───────▼───────┐
│ LoraLoader    │←────│ CLIP Encode NEG│◀────│ Apply LoRA    │
│ 强度 0.6-0.8   │     │ negative text │     │ Q版3D生肖风格  │
└──────┬───────┘     └───────────────┘     └───────┬───────┘
       │                                            │
       ▼                                            ▼
┌───────────────┐     ┌───────────────┐     ┌───────────────┐
│ VAEDecode      │◀───│ Empty Latent   │     │ Save Image      │
│ 1024x1024      │     │ batch_size:1   │     │ q_version_zodiac│
└──────┬───────┘     └───────────────┘     └───────┬───────┘
       │                                            │
       ▼                                            ▼
 最终输出图片 ←──────────────────────────── Save Image
```

### 节点设置详解

| Node | Node Class | Input Parameters |
|------|-----------|-----------------|
| 1 | `CheckpointLoaderSimple` | ckpt_name = `sd_xl_base_1.0.safetensors` |
| 2/3 | `CLIPTextEncode` (POS / NEG) | text = 正面提示词 / 负面提示词 |
| 4 | `LoraLoader` | model: from checkpoint, strength_model: **0.7**, strength_clip: **0.7** |
| 5 | `EmptyLatentImage` | width: 1024, height: 1024 (竖版 832×1216 也可) |
| 6 | `KSamplerAdvanced` | sampler=`dpmpp_2m_sde_gpu`, steps=30, seed=随机, cfg=5.5, denoise=1.0 |
| 7 | `VAELoader` | vae_name = sdxl_vae.safetensors |
| 8 | `VAEDecode` | samples from KSampler |
| 9 | `SaveImage` | images from VAE Decoder, prefix=`q_version_3d_zodiac` |

---

## 📐 参数推荐表

| 参数 | 推荐值 | 原因 |
|------|--------|-----|
| **LoRA Strength** | **0.6 ~ 0.8** | 0.7 为最佳平衡点，过高会过饱和失真 |
| **Steps** | 25 ~ 30 | SDXL 下 30 steps 足够达到最优质量 | 
| **CFG Scale** | 4.5 ~ 6.5 | LoRA 模型适合略低 CFG，避免过拟合 |
| **Sampler** | `dpmpp_2m_sde_gpu` / `euler_a` | SDE 系列对 Q版风格更友好 |
| **Scheduler** | `karras` / `normal` | karras 在渲染效果上更好 |
| **分辨率** | `1024×1024` 或 `896×1216` | Q版竖版更突出全身角色 |
| **Denoise (txt2img)** | **1.0** | 纯文本到图像全去噪 |

---

## 🎯 Prompt 构建模板

### 正面提示词 (Positive Prompt)：

```text
(masterpiece, best quality:1.2), Q version style, chibi character, 
3D render, cute cartoon rabbit zodiac, big round eyes, soft body, 
Chinese traditional clothing, cloud pattern decoration, 
cinematic lighting, gradient pastel background, bloom, 
highly detailed texture, national trend IP design
```

### 负面提示词 (Negative Prompt)：

```text
lowres, bad anatomy, worst quality, low quality, 
monster, mutated, deformed, ugly, disfigured, 
bad hands, extra limbs, missing limb, 
blurry, watermark, text, signature
```

---

## 🔄 进阶工作流 (ControlNet + IP-Adapter)

如果需要更精准控制角色构图：

```
┌──────────────┐     ┌───────────────┐             
│ Load Checkpoint │──▶│ CLIP Encode    │             
│ SDXL           │     │ positive text  │             
└──────┬───────┘     └───────────────┘             
       │                                             
┌──────▼───────┐     ┌───────────────┐             
│ LoraLoader    │◀────│ Reference Img  │◀─ 生肖角色参考图 
│ LoRA + IP-Adapter │                              
└──────┬───────┘     └───────────────┘             
       │                                             
       ▼                                             
┌───────────────┐     ┌───────────────┐              
│ KSampler      │◀────│ ControlNet (OpenPose/Depth)     
│ dpmpp_2m + LoRA│     │ 控制角色姿势      
└──────┬───────┘     └───────────────┘             
       │                                             
       ▼                                             
┌───────────────┐     ┌───────────────┐              
│ VAEDecode      │◀────│ Empty Latent   │              
│                │     │ 1024×1024 batch  │              
└──────┬───────┘     └───────────────┘       
       │                                             
       ▼                                             
┌───────────────┐     
│ Save Image      │ ← 最终角色图  
└───────────────┘      
```

### ControlNet 辅助参数：

| 控制方式 | 模型文件 | 推荐权重 |
|---------|---------|---------|
| **IP-Adapter Plus** | ip-adapter-plus.safetensors | 0.6~0.8 |
| **ControlNet OpenPose** | control-net-openpose.safetensors | 0.7~1.0 |
| **ControlNet Depth** | controlnet-depth.safetensors | 0.5~0.7 |

---

## 💡 实用技巧汇总

| 技巧 | 详细说明 |
|------|---------|
| **LoRA混合** | 同时加载风格 LoRA(0.3) + 服饰/场景 LoRA(0.4)，叠加增强 |
| **Upscale后处理** | 生成后用 `Ultimate SD Upscale` 提升，Q版放大易糊 |
| **Negative强化** | Q版常出多余肢体，negative 加入 `extra limbs, bad hands` |
| **Seed漫游** | 固定 prompt + seed步进，批量出图筛选最佳风格 |
| **Img2Img精修** | 底图 + LoRA (denoise=0.4) 重绘风格 |
| **CFG调低** | LoRA模型推荐 CFG<6 (SDXL)，避免过拟合风格 |

---

## 📦 文件位置

```
源文件路径: /Users/km/Desktop/kang/software/comfyui/loras-人物-服饰-风格小模型/
├── IP_Q版_3D_十二生肖_v1.safetensors   ← 模型 (约150MB)
└── IP_Q版_3D_十二生肖_v1.webp           ← 预览图

ComfyUI存放: ComfyUI/models/loras/       ← 建议放这里
               OR
             ComfyUI/models/checkpoints/  ← 如果作为主模型加载
```

---

> 📌 创建日期: 2026-06-11  
> ✏️ 作者: Codex AI Assistant

