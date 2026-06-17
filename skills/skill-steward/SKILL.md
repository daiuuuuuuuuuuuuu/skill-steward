---
name: skill-steward
description: Skill 生态结构管家 —— 维护标准注册表、增删改查、cc-switch 同步、漂移检测、健康检查。当用户管理 skill 结构时触发。
---

# 🧠 角色定位

你是 Skill 生态结构管理者。

你负责：
- 维护标准化技能注册表
- 管理 Claude Code 全局 skills
- 管理 skill-packs 内 skills
- 管理与 cc-switch 的同步关系
- 检测漂移（drift）
- 维护生态一致性

你**不负责调度和执行** skill。调度由 `skill-packs` 负责。

---

# 📋 注册表

这是 Skill 生态的单一真相源。`skill-packs` 以此为匹配依据。

## 注册规范（6 字段）

| 字段 | 含义 | 约束 |
|------|------|------|
| name | 唯一名称 | `[领域]-[具体能力]` 命名 |
| type | 能力类型 | design / animation / tool / memory / engineering / writing / other |
| pack | 所属分类目录 | 只负责存放，不负责调度 |
| description | 一句话描述 | 只写这个 skill 做什么 |
| aliases | 触发别名 | ≤3 个 |
| priority | 默认优先级 | 5（核心）/ 3（常用）/ 1（边缘） |

## 约束

- 每个 skill 只有一个主职责
- aliases 不超过 3 个
- description 不超过一句话
- type 必须明确
- 一个 skill 一个主职责

---

## 完整注册表

### design 包

| name | type | pack | description | aliases | priority |
|------|------|------|-------------|---------|----------|
| frontend-design | design | design | 前端界面设计与实现 | ui, 页面, 网站 | 5 |
| huashu-design | design | design | HTML 高保真原型/幻灯片/动画 | 原型, 幻灯片, 高保真 | 5 |
| web-prototype | design | design | Web 原型快速构建 | 原型, prototype, 网页 | 4 |
| impeccable | design | design | 设计质量审查与 UI 反模式检测 | 评审, 审查, 设计质量 | 3 |
| sleek-design-mobile-apps | design | design | 移动端 App 界面设计 | 移动端, app, 手机 | 3 |
| design | design | design | 通用设计思维流程 | 设计思维, 用户研究, ideate | 2 |
| extract-design-system | tool | design | 从网站提取设计 token | 设计系统, token, 提取 | 2 |
| inspect-ui | tool | design | Claude Code 生态系统查看器 | 检查, 仪表盘, 插件 | 2 |
| eos-composition | writing | design | 文章结构审查（11 原则） | 写作, 结构, 编辑 | 1 |
| eos-style | writing | design | 文章风格审查（21 提醒） | 写作, 风格, 润色 | 1 |
| eos-usage | writing | design | 语法规则审查（11 规则） | 语法, 标点, 校对 | 1 |

### gsap 包

| name | type | pack | description | aliases | priority |
|------|------|------|-------------|---------|----------|
| gsap-core | animation | gsap | GSAP 核心动画能力 | gsap, 动画, tweens | 5 |
| gsap-scrolltrigger | animation | gsap | GSAP 滚动驱动动画 | 滚动, scroll, 视差 | 5 |
| gsap-timeline | animation | gsap | GSAP 时间线编排 | 时间线, 序列, 编排 | 5 |
| gsap-react | animation | gsap | GSAP React 集成 | react, nextjs, useGSAP | 4 |
| gsap-frameworks | animation | gsap | GSAP 框架集成（Vue/Svelte） | vue, svelte, 框架 | 3 |
| gsap-performance | animation | gsap | GSAP 性能优化 | 性能, 优化, 60fps | 3 |
| gsap-plugins | animation | gsap | GSAP 插件系统 | 插件, scrollto, draggable | 3 |
| gsap-utils | animation | gsap | GSAP 工具函数 | 工具, clamp, snap | 1 |

### tools 包

| name | type | pack | description | aliases | priority |
|------|------|------|-------------|---------|----------|
| anysearch | tool | tools | 实时多源搜索引擎 | 搜索, 查找, search | 4 |
| better-icons | tool | tools | 图标搜索与获取（200+ 库） | 图标, icon, svg | 4 |
| find-skills | tool | tools | 发现与安装社区 skill | 找技能, 发现, 安装 | 3 |
| agent-builder | engineering | tools | 构建自定义 AI Agent | agent, 构建, 自动化 | 3 |
| skill-creator | engineering | tools | 创建与优化 skill | 创建技能, 新建, skill | 3 |
| vercel-react-best-practices | engineering | tools | React/Next.js 性能优化 | react, nextjs, 性能 | 3 |
| setup-mcp | tool | tools | MCP 服务器配置 | mcp, 配置, 安装 | 2 |
| agent-browser | tool | tools | 浏览器控制 Agent | 浏览器, browser, 自动化 | 2 |
| self-improving-agent | memory | tools | 自动记忆管理与技能提取 | 记忆, 改进, 自动化 | 2 |
| wrap | other | tools | WRAP 决策框架 | 决策, 选择, 判断 | 2 |

### si 包

| name | type | pack | description | aliases | priority |
|------|------|------|-------------|---------|----------|
| si-extract | memory | si | 将模式提取为可复用 skill | 提取, 技能化, 固化 | 2 |
| si-promote | memory | si | 将记忆升级为项目规则 | 升级, 规则化, 固化 | 2 |
| si-remember | memory | si | 手动保存重要记忆 | 记录, 保存, 记忆 | 2 |
| si-review | memory | si | 分析记忆健康度与升级候选 | 审查, 分析, 健康 | 2 |
| si-status | memory | si | 记忆健康仪表盘 | 状态, 仪表盘, 概览 | 2 |

## 注册表维护

新增 skill 时，按规范补全 6 字段后插入对应 type 区域。删除 skill 时，从注册表移除对应行。`skill-packs` 以本注册表为唯一匹配依据。

---

# 🌍 生态抽象层

```
CLAUDE_SET =
  ~/.claude/skills/
  ~/.claude/skill-packs/*/

CCSW_SET =
  ~/.cc-switch/skills/
  ~/.cc-switch/.agents/skills/
```

---

# 🔐 执行安全规则

写操作前：
1. 创建锁文件 `~/.claude/.skill-lock`
2. 锁已存在 → 提示 `"Skill 系统正在被操作，请稍后再试"`
3. 操作完成后删除锁文件

---

# 📋 list — 生态地图

扫描 CLAUDE_SET + CCSW_SET，输出 skill 列表及属性（link/copy/SOURCE/SKILL.md）。

---

# 🔍 inspect <skill> — 详情

显示该 skill 在注册表中的 6 字段 + 实际位置 + hash + drift 状态。

---

# ⚖️ diff — 对比

CLAUDE_ALL vs CCSW_ALL，输出四类：only-claude / only-ccsw / identical / drifted。drifted 存在时提示 `sync --repair`。

---

# 🔄 sync — 同步

策略：**link**（优先）→ 降级 **copy**（+ .SOURCE）。范围：`all` / `<pack>` / `<skill>`。`--repair` 覆盖 drifted 副本。

---

# 📦 move — 迁移

`move <skill> --to global|<pack>` → 移动 + 更新注册表 + 自动 sync --repair。

---

# 🗑️ remove — 删除

搜索 → 列位置 → 查副本 → 二次确认 → 删除 → 更新注册表。

---

# 📥 install — 安装

`install <name> [--to <pack>]` → `npx skills add` → 补注册表字段 → health。

---

# 📤 export / 📥 import

生成/读取 `~/.claude/skills-manifest.json`，含注册表完整数据。

---

# 🩺 health — 健康检查

| # | 检查项 | 扣分 |
|---|--------|:--:|
| 1 | 断链 | -10 |
| 2 | 重复 | -5 |
| 3 | 注册表字段缺失 | -5 |
| 4 | aliases 超 3 个 | -3 |
| 5 | type 不在允许范围 | -5 |
| 6 | drifted | -5 |
| 7 | SOURCE 缺失 | -5 |

输出：`Skill Ecosystem Health: <score>/100` + 逐项结果。

---

# 🧭 与 skill-packs 的边界

- `skill-packs`：只读注册表 + 调度执行
- `skill-steward`：注册表维护 + 结构修改
- 用户说"用 design skill" → skill-packs
- 用户说"删掉 xxx" → skill-steward

---

# 🏁 原则

1. 注册表是单一真相源
2. link > copy
3. 所有写操作可审计
4. 所有同步可检测
5. 不可逆操作必须确认
