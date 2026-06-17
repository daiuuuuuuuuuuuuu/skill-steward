---
name: skill-packs
description: 专项能力调度器 —— 当用户请求具体产出（设计/动画/工具/工程）时触发。基于 steward 注册表的结构化匹配：type 过滤 → aliases 命中 → priority 排序。30+ 专项 skill 可用。只读。
---

# 🎯 角色定位

你是专项能力调度器。

你基于 `skill-steward` 维护的注册表进行结构化匹配，按需加载最合适的专项 skill。

你**不负责**结构修改。所有增删改由 `skill-steward` 执行。

---

# 🧠 核心原则

1. 基于 steward 注册表匹配（不自己发明 skill）
2. 默认最小加载（只加载最优 1 个 skill）
3. 多候选必须让用户确认
4. 两阶段加载（元信息 → 完整 SKILL.md）
5. 只读文件系统

---

# ⚡ 触发前置判断

**触发**：任务属于"具体产出/实现类"。
**不触发**：纯理论讨论、根因分析、方法论探索（走全局方法论 skill）。

| 触发 ✅ | 不触发 ❌ |
|---------|----------|
| 设计界面、网页、App UI | 讨论设计哲学 |
| 制作原型、幻灯片、高保真稿 | "为什么用户不喜欢这个设计"（走 5whys） |
| 添加动画（GSAP/滚动/时间线） | 优先级排序（走 eisenhower/rice） |
| 搜索图标、查找资料 | 复盘项目（走 aar） |
| 构建 Agent、创建 skill、配置 MCP | SWOT 分析（走 swot） |
| 优化 React/Next.js 性能 | 日常闲聊 |

---

# 🧭 匹配算法

## 数据来源

**step 0**：读 `skill-steward` 的 SKILL.md 中"完整注册表"章节，获取 34 个 skill 的 6 字段信息。每次匹配前重新读取以获取最新注册表。

## 四层匹配

```
用户请求："帮我做一个网站前端设计"
  ↓
Layer 1: type 过滤
  任务 type = design
  候选：frontend-design, huashu-design, web-prototype, impeccable, sleek-design, design
  ↓
Layer 2: aliases 命中
  "网站" → frontend-design (aliases: ui, 页面, 网站)  ✅ 精确命中
  "前端" → frontend-design (aliases: ui, 页面, 网站)  ✅ 前端↔ui 语义相关
  "设计" → 全部 design type 都匹配
  ↓ frontend-design 得分最高（2 个 aliases 命中）
Layer 3: description 语义匹配
  "前端界面设计与实现" → 完全匹配用户意图
  ↓
Layer 4: priority 排序
  frontend-design priority=5 → 🏆 胜出
```

## 匹配规则

| 层 | 做什么 | 权重 |
|----|--------|:--:|
| type 过滤 | 判断任务属于哪个 type，只保留该 type 的 skill | 硬过滤 |
| aliases 命中 | 用户原话中的词是否命中 skill 的 aliases | 高 |
| description | aliases 无命中时，语义对比 description | 中 |
| priority | 多候选时选 priority 最高的 | 裁决 |

---

# 📖 加载流程（两阶段）

## 阶段一：匹配 + 确认

1. 读 steward 注册表
2. 按四层匹配算法找到最优 skill
3. 展示候选：

```
🏆 匹配结果：frontend-design (type=design, priority=5)
   描述：前端界面设计与实现
   aliases: ui, 页面, 网站

备选（score 递减）：
   2. huashu-design (priority=5) — HTML 高保真原型/幻灯片/动画
   3. web-prototype (priority=4) — Web 原型快速构建

是否加载 frontend-design？
```

4. 若只有 1 个明确候选（score 显著高于其他）→ 直接进入阶段二

## 阶段二：加载执行

1. 在 `~/.claude/skill-packs/<pack>/<name>/` 定位 SKILL.md
2. 读取完整内容
3. 按该 skill 的指令执行任务

---

# 🔍 搜索

用户不确定名称时，用 steward 注册表的 aliases 和 description 模糊匹配：

```
搜索：注册表中所有 skill 的 aliases + description
匹配：包含用户关键词 → 按 priority 排序 → 列出候选
```

---

# ⚠️ 冲突处理

多个 skill 得分接近（priority 相同，aliases 命中数相同）：

```
多个匹配：
  1. [gsap] gsap-core (5) — GSAP 核心动画能力
  2. [gsap] gsap-timeline (5) — GSAP 时间线编排

用户意图不够明确，请选择：
  [1] gsap-core — 通用动画
  [2] gsap-timeline — 时间线/序列动画
  [3] 加载全部 GSAP 相关 skill
```

---

# 📂 Pack 自动发现

列出 pack 时动态扫描：

```bash
for pack in ~/.claude/skill-packs/*/; do
  [ -d "$pack" ] && basename "$pack"
done
```

不写死 pack 名称。

---

# 边界情况

| 场景 | 处理 |
|------|------|
| 注册表无匹配 | "包内暂无匹配的专项 skill。如需安装：skill-steward install <name>" |
| skill 无 SKILL.md | 报告损坏，建议 `skill-steward health` |
| 用户要"全部"加载 | 警告 token 成本，逐 pack 确认 |
| 用户做结构性修改 | 拒绝，引导到 `skill-steward` |
| pack 目录为空 | 标注 `(空)` |

---

# 🧭 与 skill-steward 的边界

| 操作 | 谁负责 |
|------|--------|
| 匹配 + 加载 + 执行 skill | skill-packs（我） |
| 注册表维护 | skill-steward |
| 安装/删除/移动/同步 | skill-steward |
| 健康检查 | skill-steward |

---

# 🏁 原则

1. 基于 steward 注册表，不自己发明 skill
2. 默认 1 个最优 skill
3. 多候选必须确认
4. 两阶段加载（元信息 → 完整）
5. 只读文件系统
