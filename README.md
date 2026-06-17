# Skill OS · Claude Code 技能操作系统

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Skills](https://img.shields.io/badge/skills-3%20system%20%2B%2034%20packed-blue)](.)

**三个 skill，一个完整的 Skill 操作系统。** Runtime · Governance · Distribution 三层架构，零外部依赖，文件系统即通信协议。

---

## 为什么需要这个

Claude Code 的 skill 数量从 5 到 50 到 200，全局目录会越来越臃肿，每次会话启动加载所有 description，调度越来越容易误选。社区方案是 MCP 服务器、Python 包、Go CLI——都需要外部依赖。

Skill OS 换了一条路：**用 skill 管理 skill**。三个 SKILL.md 就是全部基础设施。安装是 `cp -r`，升级是 `git pull`。

---

## 🏗️ 架构

```
用户请求
  │
  ├─ "帮我做一个登录页" ──→ skill-packs（Runtime）
  │                        动态发现 pack → 语义判域 → 匹配最优 skill → 加载执行
  │                        每次只读取目标 pack 的 PACK.md（≤15 行）
  │
  ├─ "把这个 skill 移到 design 包" ──→ skill-steward（Governance）
  │                                   suggest-pack · move · audit · health · bootstrap
  │                                   PACK.md 是单一真相源，所有写操作经过这里
  │
  └─ "同步到 cc-switch" ──→ cc-sync（Distribution）
                            diff · sync · repair · link/copy 策略
```

三层互不调用、互不依赖、各自通过文件系统自举。

---

## 🧠 设计亮点

### 1. 域来自文件系统，不写死

```
传统做法：prompt 里硬编码 "design | animation | engineering | ..."
Skill OS：PACK.md 第一行就是领域描述，skill-packs 每次调度动态发现

加一个 pack → mkdir + PACK.md → 自动识别 ✅
删一个 pack → rm -rf → 自动消失 ✅
零代码变更。
```

### 2. 复杂度与 skill 数量解耦

skill-packs 每次只读**一个** PACK.md。200 个 skill 分布在 12 个 pack 里，每次调度只加载 ≤15 行。系统性能不随 skill 总数线性下降。

### 3. 框架与工具分层（双路径桥接）

```
方法论 skill（5whys/aar/premortem）→ 分析框架
专项 skill（frontend-design/gsap-core）→ 执行工具

框架遇到需要专项工具的子问题 → skill-packs 自动加载
工具不替代框架，框架不重复造轮子
```

### 4. 规则固定，结构可演进

```
创建 pack 的条件、合并的条件、拆分的条件 → 写死在 steward 里
域名称可以变、pack 数量可以变、skill 分布可以变
但"什么时候该变"的判断逻辑永远不变
```

### 5. bootstrap-packs：一次性结构建模

50+ skill 时自动语义聚类，按"主要工作对象"分组，生成候选 pack 结构。人工确认后固化，之后进入治理模式。不持续自动重算——**结构稳定优先于分类最优**。

### 6. 自举（Self-hosting）

社区里独一无二：用 skill 管理 skill。没有 Go 二进制、没有 Python 包、没有 MCP 服务器、没有 JSON 配置文件、没有向量数据库。三个 SKILL.md + 四个 PACK.md = 全部基础设施。

---

## 📦 安装

```bash
git clone https://github.com/daiuuuuuuuuuuuuu/skill-steward.git
cd skill-steward
cp -r skills/skill-packs ~/.claude/skills/
cp -r skills/skill-steward ~/.claude/skills/
cp -r skills/cc-sync ~/.claude/skills/
```

---

## 🧭 三层职责

| 层 | skill | 触发条件 | 能力 | 权限 |
|----|-------|---------|------|:--:|
| Runtime | `skill-packs` | 专项执行或专项分析 | 动态发现 pack → 语义判域 → 匹配加载 | 只读 |
| Governance | `skill-steward` | 增删移重组或健康审计 | PACK.md 维护 · 归类 · 移动 · bootstrap · health | 读写 |
| Distribution | `cc-sync` | 同步/对比/修复漂移 | diff · sync · repair · link/copy | 读写 |

---

## 🏷️ 标签

`claude-code` `skill-os` `skill-management` `self-hosting` `meta-skill` `ai-agent` `anthropic` `cc-switch` `ecosystem`

---

*Made for the long term. Three files, no dependencies, one philosophy.*
