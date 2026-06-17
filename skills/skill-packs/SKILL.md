---
name: skill-packs
description: 专项领域运行时调度器。当任务需要具体专项能力时触发，包括专项执行（生成代码、界面、动画、文档、使用工具）或专项分析（代码审查、UI 审查、性能诊断等）。根据当前 pack 结构动态匹配并只读加载最合适的 skill，不修改任何结构。
---

# 🎯 角色

你是专项能力调度器。

你负责：
- 判断是否为专项领域任务
- 动态发现当前所有 pack
- 读取 PACK.md
- 匹配最合适的 skill
- 加载并执行该 skill

你**不**：
- 修改结构
- 移动 skill
- 调用 steward
- 写任何文件

---

# 🧠 域来自文件系统，不来自 prompt

**域不是写死的**。每次调度时动态发现：

```bash
for f in ~/.claude/skill-packs/*/PACK.md; do
  pack=$(basename $(dirname "$f"))
  domain=$(head -1 "$f" | sed 's/^# //')
  count=$(grep -c "^- " "$f")
  echo "$pack | $domain | $count skill"
done
```

输出即为当前可用的全部 pack。pack 有增删改 → 下次发现自动反映。

---

# ⚡ 触发判断（双路径）

## 路径 A：用户直接请求

触发：任务属于"专项执行"或"专项分析"。

判别原则（不枚举具体领域）：
- 是否要求**生成具体产出**？（代码、界面、动画、文档、Agent 等）
- 是否要求**使用外部工具或能力**？（搜索、图标、MCP 等）
- 是否要求**对已有产出做专门审查或诊断**？

三个问题任一为"是" → 触发。

## 路径 B：方法论 skill 分析中需要专项工具

全局方法论 skill 在执行分析时，若遇到**需要专项工具才能准确回答**的子问题 → 触发。

**规则**：方法论是框架，专项 skill 是工具。框架调用工具，不替代工具。

---

# 🧭 调度逻辑

## Step 0 — 发现 pack

动态扫描 `~/.claude/skill-packs/*/PACK.md`，获取当前所有 pack 的名称和领域描述。

## Step 1 — 判域

将用户意图与 Step 0 发现的 pack 领域描述进行语义匹配，选择最相关的 **1 个** pack。

```
示例：
  用户："帮我设计一个登录页"
  pack 发现：
    design pack    → 视觉与体验
    gsap pack      → 动态交互
    tools pack     → 基础设施工具
    si pack        → 系统元能力
  ↓ 语义匹配
  登录页 → "visual design + experience" → design pack ✅
```

这是语义匹配，不是关键词查找。领域描述来自 PACK.md 的第一行，可随 pack 演化而变。

不确定 → 列出所有 pack 让用户选择。

## Step 2 — 读 PACK.md

只读目标 pack 的 `PACK.md`（≤ 15 行）。

## Step 3 — 匹配

在 PACK.md 中匹配最相关的 **1 个** skill。多个高度相似 → 列出候选让用户确认。

## Step 4 — 加载执行

定位 `~/.claude/skill-packs/<pack>/<skill-name>/SKILL.md`，读取完整内容，按该 skill 的指令执行。

默认只加载 1 个 skill。

---

# 📏 调度纪律

- 域来自文件系统，不来自硬编码表
- 只读目标 pack 的 PACK.md
- 不扫描全部 SKILL.md
- 不维护全局注册表
- 默认加载 1 个最优 skill

系统复杂度 = pack 数量，而不是 skill 数量。

---

# 🧭 与 skill-steward 的边界

| 操作 | 谁负责 |
|------|--------|
| 动态发现 pack + 匹配 + 加载执行 | skill-packs（我） |
| 维护 PACK.md + 结构移动 + 安装 + 同步 | skill-steward |

用户说"删掉这个 skill" → 引导到 `skill-steward remove`
用户说"安装新 skill" → 引导到 `skill-steward install`

---

# 🏁 原则

1. 域来自文件系统（PACK.md），不写死
2. 默认加载 1 个最优 skill
3. 多候选必须确认
4. 方法是框架，专项 skill 是工具
5. 只读文件系统
