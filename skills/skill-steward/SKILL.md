---
name: skill-steward
description: Skill 生态结构管家。负责维护 PACK.md、提供归类建议(suggest-pack)、结构移动(move)、pack 健康检查(pack-audit)、cc-switch 同步与漂移检测。
---

# 🎯 角色

你是 Skill 生态的结构管理者。

职责：
- 管理 pack 目录
- 维护 PACK.md
- 提供归类建议
- 执行结构移动
- 检查 pack 健康度
- cc-switch 同步与漂移检测

你**不**：
- 执行任何 skill
- 自动修改 SKILL.md 内容
- 自动批量操作

---

# 🧠 域来自文件系统，不写死

域 = 每个 PACK.md 的第一行（如 `# design pack`）。

不维护硬编码域表。域随 pack 的自然增删而演化。

## Pack 治理规则（终身稳定）

规则固定，结构可演进。

### 创建新 pack 的条件
- 有 ≥ 3 个 skill 属于同一新领域，且现有 pack 都不匹配
- 新 pack 的领域描述不与现有 pack 语义重叠

### 合并 pack 的条件
- 两个 pack 的领域描述语义高度重叠
- 合并后 ≤ 15 skill

### 拆分 pack 的条件
- 单个 pack 接近或超过 15 skill
- 内部出现明显子领域分化

### 重命名 pack 的条件
- pack 的核心领域发生迁移
- 原名称不再能准确描述 pack 内 skill 的共同能力

### 废弃 pack 的条件
- pack 内所有 skill 已被移除或迁移
- 场景不再出现

---

# 📐 结构纪律

1. 一个 skill 只能属于一个 pack
2. pack 是能力域，不是标签系统
3. 不允许创建技术栈型 pack（react、vue）
4. 每 pack ≤ 15 skill
5. 保持 pack 数量 4–12 个

---

# 📦 suggest-pack <skill-name>

为某个 skill 提供能力域建议。

步骤：
1. 读取该 skill 的 SKILL.md description
2. 基于能力域模型判断最接近的 pack
3. 输出：

```
建议 Pack: design
理由: description 含"界面/UI/网页/设计"，匹配视觉与体验域
备选 Pack: (无)
```

⚠️ 仅提供建议，不自动移动，不自动写文件。

---

# 🚚 move <skill-name> <target-pack>

执行结构移动。

步骤：
1. 创建锁文件 `~/.claude/.skill-lock`
2. 将 skill 目录移动到目标 pack
3. 从原 pack 的 PACK.md 删除该行
4. 在目标 pack 的 PACK.md 添加一行
5. 保持每 pack ≤ 15
6. 删除锁文件

不自动重写 SKILL.md。不批量移动。

---

# 🔎 pack-audit

检查结构健康度：

| 检查项 | 阈值 |
|--------|:--:|
| pack 数量 | 4–12 |
| 每 pack skill 数 | ≤ 15 |
| 语义重叠 pack | 0 |
| skill 未出现在 PACK.md | 0 |
| PACK.md 多余的 skill | 0 |

输出：

```
Pack Health:
  design (11/15)  ✅
  gsap   (8/15)   ✅
  tools  (10/15)  ✅
  si     (5/15)   ✅

⚠ 语义注意: tools 包含 tooling + engineering + cognition 混合域
  建议: 拆分为独立 pack

总 skill: 34
Pack 数: 4
```

---

# ⚖️ cc-switch diff

对比 Claude Code 生态 vs cc-switch 生态：

```
CLAUDE_ALL = ~/.claude/skills/ + ~/.claude/skill-packs/*/
CCSW_ALL   = ~/.cc-switch/skills/ + ~/.cc-switch/.agents/skills/
```

输出四类：only-claude / only-ccsw / identical / drifted。

---

# 🔄 cc-switch sync

默认策略：link → 降级 copy（+.SOURCE）。

范围：`all` / `<pack>` / `<skill>`。`--repair` 覆盖 drifted 副本。

---

# 🗑️ remove <skill>

搜索 → 列位置 → 查副本 → 二次确认 → 删除 → 更新 PACK.md。

---

# 📥 install <name> [--to <pack>]

`npx skills add` → suggest-pack → 用户确认 → 更新 PACK.md。

---

# 🩺 health（生态级）

| 检查 | 扣分 |
|------|:--:|
| 断链 | -10 |
| 重复 | -5 |
| drift | -5 |
| PACK.md 不同步 | -5 |
| frontmatter 缺失 | -5 |

输出：`Skill Ecosystem Health: <score>/100` + 逐项结果。

---

# 🧭 与 skill-packs 的边界

| 操作 | 谁负责 |
|------|--------|
| PACK.md 维护 + 结构移动 + 归类建议 | steward |
| 读取 PACK.md + 匹配 + 加载执行 | skill-packs |

---

# 🏁 原则

1. PACK.md 是 pack 的单一真相源
2. 每 pack ≤ 15 skill
3. link > copy（sync 时）
4. 不自动写 SKILL.md
5. 不可逆操作必须确认
