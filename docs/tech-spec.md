# 技术规格说明书 v3

## 1. 技术栈

| 层级 | 技术 | 说明 |
|------|------|------|
| 结构 | HTML5 | 语义化标签 |
| 表现 | CSS3 | 内联样式表，CSS 变量管理主题色 |
| 逻辑 | Vanilla JavaScript (ES6+) | 无依赖，原生 fetch API |
| AI | DeepSeek API | `api.deepseek.com/v1/chat/completions` |
| 存储 | localStorage | 浏览器本地键值存储 |

## 2. 数据模型

### 2.1 localStorage

**API Key**: `deepseek-api-key` (string)
**评价历史**: `topic-evaluations` (JSON 数组)

```json
[{
  "id": "1717305600000",
  "topic": "用户输入的选题描述",
  "topicUnderstanding": "AI对选题的理解...",
  "backgroundContext": "背景分析...",
  "scores": {
    "emotion":    { "score": 85, "reason": "..." },
    "curiosity":  { "score": 70, "reason": "..." },
    "cognition":  { "score": 90, "reason": "..." },
    "resonance":  { "score": 65, "reason": "..." },
    "complexity": { "score": 80, "reason": "..." },
    "visual":     { "score": 75, "reason": "..." },
    "algorithm":  { "score": 60, "reason": "..." },
    "trend":      { "score": 50, "reason": "..." },
    "audience":   { "score": 55, "reason": "..." },
    "cost":       { "score": 40, "reason": "..." }
  },
  "redLineViolation": false,
  "totalScore": 78,
  "createdAt": "2026-06-02T10:30:00.000Z"
}]
```

### 2.2 权重配置（v3 最终版）

| 维度 | key | 权重 |
|------|-----|:----:|
| 🔥 情绪浓烈程度 | emotion | 30% |
| 🌀 猎奇程度 | curiosity | 16% |
| 🧠 默认认知程度 | cognition | 12% |
| 🌐 共鸣广度 | resonance | 10% |
| 🧩 内容晦涩程度 | complexity | 10% |
| 📸 可视化潜力 | visual | 8% |
| 🤖 平台算法友好度 | algorithm | 5% |
| 📡 流行热点关联度 | trend | 5% |
| 👥 受众规模 | audience | 4% |
| 💰 制作成本 | cost | 参考 |

> 合计：30+16+12+10+10+8+5+5+4 = **100%** ✓

## 3. 计算公式

```
总分 = Σ(维度分数 × 权重%)  （9个加权维度）
红线触发 → 总分 = 0
```

## 4. API 调用

```
POST https://api.deepseek.com/v1/chat/completions
Model: deepseek-chat
temperature: 0.4
max_tokens: 3000
response_format: json_object
```

System prompt 要求 AI 按 4 步流程思考，输出包含 topicUnderstanding、backgroundContext、scores（含 10 个维度）、redLineViolation、overallComment。
