# FaceFusion 项目说明

## 项目简介

FaceFusion 是一个业界领先的**人脸处理平台**，支持：

- **换脸（Face Swap）**：将来源人脸替换到目标图片/视频中
- **人脸增强（Face Enhancement）**：提升人脸清晰度与质量
- **批量处理**：支持批量任务队列管理

## 环境依赖

| 依赖 | 安装方式 |
|------|----------|
| ffmpeg | `brew install ffmpeg` |
| Python 环境 | 使用项目自带 `.venv` |

## 运行方式

### 1. Web UI 模式（推荐）

启动后浏览器访问 `http://localhost:7860`：

```bash
python facefusion.py run
```

### 2. 无头模式（命令行直接处理）

```bash
python facefusion.py headless-run \
  --source-paths /path/to/source_face.jpg \
  --target-path /path/to/target.jpg \
  --output-path /path/to/output.jpg
```

### 3. 批量模式

```bash
python facefusion.py batch-run
```

### 4. 任务队列管理

```bash
# 创建任务
python facefusion.py job-create

# 添加步骤
python facefusion.py job-add-step

# 提交任务
python facefusion.py job-submit

# 运行所有队列任务
python facefusion.py job-run-all

# 查看任务列表
python facefusion.py job-list
```

### 5. 强制预下载模型

```bash
python facefusion.py force-download
```

## 完整命令列表

```
python facefusion.py [commands] [options]

commands:
  run               启动 Web UI
  headless-run      无头模式（命令行直接处理）
  batch-run         批量模式
  force-download    预下载所有模型
  benchmark         性能测试
  job-list          查看任务列表
  job-create        创建草稿任务
  job-submit        提交任务到队列
  job-submit-all    提交所有草稿任务
  job-delete        删除任务
  job-delete-all    删除所有任务
  job-add-step      为任务添加步骤
  job-remix-step    复用上一步骤
  job-insert-step   插入步骤
  job-remove-step   移除步骤
  job-run           运行指定队列任务
  job-run-all       运行所有队列任务
  job-retry         重试失败任务
  job-retry-all     重试所有失败任务
```

## 官方文档

https://docs.facefusion.io

---

## 模型选择

### 换脸模型（Face Swapper）- 共 13 个

| 模型 | 最低分辨率 | Mac 适配性 | 说明 |
|------|-----------|-----------|------|
| `inswapper_128_fp16` | 128px | 最轻量 | FP16 量化版，速度最快 |
| `ghost_1_256` | 256px | 中等 | Apache-2.0，质量好，**推荐** |
| `ghost_2_256` / `ghost_3_256` | 256px | 中等 | Ghost 系列改进版 |
| `simswap_256` | 256px | 中等 | 经典稳定 |
| `simswap_unofficial_512` | 512px | 较重 | 高分辨率起点 |
| `blendswap_256` | 256px | 中等 | 非商业许可 |
| `hififace_unofficial_256` | 256px | 较重 | 质量较高 |
| `hyperswap_1a/1b/1c_256` | 256px | 较重 | - |
| `uniface_256` | 256px | 中等 | - |
| `inswapper_128` | 128px | 最轻量 | 标准版（推荐优先用 fp16） |

**Mac 推荐**：`ghost_1_256`（质量与性能最平衡）

### Deep Swapper - 180+ 内置名人脸

内置了 180+ 名各领域知名人物的预制人脸，**不需要提供源人脸图片**，直接按名字选用。

示例：`druuzil/elon_musk_320`、`iperov/scarlett_johansson_224`、`rumateus/margot_robbie_224`

---

## 换脸的两种工作方式

| 方式 | 需要源图片？ | 处理器 | 说明 |
|------|------------|--------|------|
| 自备人脸 | **是**，提供一张源人脸图片 | `face_swapper` | 最通用的方式 |
| 内置名人脸 | **否** | `deep_swapper` | 从内置库选名人脸，直接换 |

---

## 不支持 Prompt 生成人脸

FaceFusion **不支持**通过文字描述（prompt）生成人脸。

- 纯图像处理框架，不集成任何文生图模型（无 Stable Diffusion / DALL-E 等）
- 所有操作基于真实图片/视频输入
- 如需 AI 生成人脸再换脸，需先用其他工具（如 Stable Diffusion）生成图片，再作为源图片传入

---

## Mac 性能优化

执行引擎（Execution Provider）选 **`coreml`**，利用 Apple Silicon Neural Engine 加速，性能显著提升。在 Web UI 设置中切换。

---

## 快速上手（图片换脸）

**Web UI**：
1. Execution Provider → `coreml`
2. Processors → `face_swapper`
3. Model → `ghost_1_256`
4. Source → 上传源人脸图片
5. Target → 上传目标图片
6. 点击 Start

**命令行**：

```bash
python facefusion.py headless-run \
  --source-paths source_face.jpg \
  --target-path target.jpg \
  --output-path output.jpg \
  --processors face_swapper \
  --face-swapper-model ghost_1_256
```
