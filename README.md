# 📘 高性价比使用 MiniMax 通用攻略（2026-04-12 修订版 v3.0）

> 适用于：自动化搭建、AI 产品开发、代码生成、配置编写等技术任务  
> **定位升级：AI Output Protocol + 工程化实践指南**

**如果这份攻略帮你省了积分和时间，请点右上角 Star ⭐ 支持一下，让更多人看到。**

---

## ⚡ 快速开始（30 秒）

### 1. 复制技能包作为第一条消息发送给 MiniMax

```text
# AI Output Protocol Skill

## 角色定义
你是 **代码生成器**，只输出代码块。

## 输出规则
- 每次回复只输出一个代码块
- 代码块前后禁止任何解释文字
- 如果信息不足，回复：❌ 缺少信息：请提供 [字段]

## 启动确认
收到本协议后，请回复：✅ 技能已加载
```

### 2. 等待确认

MiniMax 应回复：`✅ 技能已加载`

### 3. 使用标准格式提交任务

```text
【任务类型】fix | generate | refactor

【错误现象】
（粘贴原始错误日志）

【数据结构】
（粘贴 JSON 示例，禁止省略）

【当前代码】
（最小可复现片段）

【输出要求】
只输出完整 index.js
```

👉 **缺少任一项，成功率显著下降**

---

## 🧨 关键教训（来自真实项目 `ai-builders-digest`）

| ❌ 错误做法 | ✅ 正确做法 |
|------------|------------|
| “飞书收不到消息，帮我看看” | 提供数据源 JSON + 错误日志 |
| 接受“删除 state 文件”建议 | 打印数据结构，发现解析错误 |
| 让 AI 诊断“为什么失败” | 自己用 `curl` 测试 webhook |
| 相信“已修复成功”但无代码 | 只接受代码块输出 |

**结论**：AI 不擅长诊断，只擅长按规格输出代码。

---

## 💰 成本模型（积分消耗对比）

| 输入质量 | 平均对话轮数 | 积分消耗 | 成功率 |
|----------|--------------|----------|--------|
| 模糊提问（“帮我看下”） | 5–8 轮 | 高 | <20% |
| 有错误日志，无数据结构 | 3–5 轮 | 中 | ~50% |
| **标准化输入（本协议）** | **1–2 轮** | **低** | **>90%** |

👉 **准备输入的时间，抵得上 5 轮无效对话。**

---

## 🚦 危险信号（出现立即终止）

- “仓库可能不存在” / “定时任务可能被禁用”  
- “建议删除 state-feed.json”  
- 输出长篇分析、表格、列表  
- “我已修复成功” 但无代码块  

**连续 2 轮 → 终止对话，精简输入重试**

---

## ✅ 适用场景与禁用场景

| ✅ 适合 MiniMax | ❌ 不适合 MiniMax |
|----------------|------------------|
| 生成脚本 / 配置文件 | 诊断“为什么我的系统不工作” |
| 根据 JSON 结构改写解析逻辑 | 排查网络、权限、环境变量 |
| 代码语言转换（JS → Python） | 操作 GitHub 仓库或云服务 |
| 生成单元测试 | 提供架构设计建议 |

---

## 🧪 协议仓库健康自检（进阶）

> **让规则文件本身保持可校验，从源头避免积分浪费。**

如果你的规则仓库（如 `minimax-output-protocol`）存在乱码、JSON 格式错误或测试失效，发给 MiniMax 的指令可能无效。

### ✅ 健康自检清单

| 项目 | 快速验证命令 |
|------|-------------|
| **合法 JSON** | `python -m json.tool config/rules.json` |
| **最小测试套件** | `pytest -q` |
| **pytest 配置** | 存在 `pytest.ini` |
| **忽略缓存** | 存在 `.gitignore` |

### 🧪 推荐测试内容（`tests/validate_repo.py`）

```python
def test_rules_json_valid():
    import json
    json.loads(open("config/rules.json").read())

def test_skill_md_has_key_phrases():
    skill = open("skill.md").read()
    assert "✅ 技能已加载" in skill
    assert "只输出代码块" in skill
```

### 🔁 可选：GitHub Actions 自动检查

```yaml
name: Health Check
on: [push]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: pip install pytest
      - run: pytest -q
      - run: python -m json.tool config/rules.json
```

---

## 📌 最终原则

> **不要为“分析”付费，只为“代码”付费。**

**每次提问前问自己：**  
👉 我是否已手动验证问题范围？  
👉 我是否提供了 JSON 数据结构？  
👉 我是否明确要求只输出代码？

三个 YES 再提问。

---

## 🤝 贡献与反馈

- 发现新坑？欢迎提 Issue / PR  
- 觉得有用？**Star ⭐ 支持一下**

---

## 📄 License

MIT © [atweb3](https://github.com/atweb3)

---

## 🇬🇧 English Version (Quick Reference)

### ⚡ Quick Start

1. **Send this as first message**:

```text
# AI Output Protocol Skill
You are a **Code Generator**. Output ONLY code blocks. No explanations. If info missing, reply: ❌ MISSING: please provide [field]
Acknowledge with: ✅ Skill loaded
```

2. Wait for `✅ Skill loaded`
3. **Use standard format**:

```text
[TASK] fix | generate

[ERROR]
(paste log)

[DATA STRUCTURE]
(paste JSON)

[CURRENT CODE]
(paste minimal snippet)

[OUTPUT REQUIREMENT]
e.g., full index.js
```

### 💰 Cost Model

| Input Quality | Rounds | Cost | Success |
|---------------|--------|------|---------|
| Vague | 5–8 | High | <20% |
| Log only | 3–5 | Medium | ~50% |
| **Standardized** | **1–2** | **Low** | **>90%** |

---

**文档版本**：3.0（协议化 + 输入标准化 + 成本模型 + 健康自检）  
**适用平台**：MiniMax 对话式 AI  
**基于真实项目**：AI Builders Digest 完整复盘 + 仓库优化实践
```
