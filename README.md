# ai-video-py

## 1. 项目定位

ai-video-py 是 AI 视频工厂系统中的 **智能内容生成与策略调度层（Intelligence Layer）**。

该项目不负责视频渲染、不负责高并发执行，而是负责“内容决策”。

---

## 2. 核心职责

本项目的唯一目标：

> 将“信息/热点/主题”转化为“可执行的视频生成任务描述”。

具体职责包括：

### 2.1 选题生成（Topic Generation）

- 从热点数据源生成视频选题
- 输出可用于传播的短视频主题
- 计算爆款概率（viral_score）

### 2.2 脚本生成（Script Generation）

- 使用大语言模型生成短视频脚本
- 控制时长（通常 15s / 30s / 60s）
- 保证结构清晰（hook → content → ending）

### 2.3 分镜拆解（Scene Planning）

- 将脚本拆解为 scene 列表
- 每个 scene 对应 3~8 秒视频片段
- 生成 AI 视频 prompt（关键）

### 2.4 Prompt优化（Prompt Engineering）

- 将自然语言转为 AI 视频生成 prompt
- 适配不同模型：
    - Kling
    - Pika
    - Runway

### 2.5 内容策略优化（Content Strategy）

- 根据历史数据优化选题方向
- 提供 A/B 测试脚本版本
- 预测爆款概率

---

## 3. 不负责的内容（重要）

本项目 **禁止承担以下职责**：

- ❌ 视频生成
- ❌ FFmpeg剪辑
- ❌ 并发任务执行
- ❌ 队列消费
- ❌ 文件上传/下载

这些由 `ai-video-go` 负责

---

## 4. 输入输出模型

### 输入

- 热点数据
- 用户配置
- 内容模板

### 输出（核心结构）

```json
{
  "topic": "...",
  "title": "...",
  "script": "...",
  "scenes": [
    {
      "index": 1,
      "prompt": "...",
      "duration": 5
    }
  ],
  "tags": [
    "ai",
    "future"
  ],
  "viral_score": 0.87
}
```

## 5. 技术栈

- Python 3.10+
- FastAPI（API服务）
- OpenAI / Claude / Gemini（LLM）
- Redis（缓存/队列辅助）
- Pydantic（数据结构）

## 6. 架构角色

ai-video-py = AI大脑（Brain Layer）

它只做三件事：

1. 想（选题）
2. 写（脚本）
3. 拆（分镜）

## 8. 设计原则

- Stateless（无状态）
- 快速生成（Latency优先）
- 可批量（Batch-first）
- 可重试（Idempotent output）

⸻

## 9. 典型调用链

1. Go系统请求生成任务
2. Python生成 script + scenes
3. 返回结构化 JSON
4. Go进入执行队列

