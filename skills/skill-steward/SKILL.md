---
name: skill-steward
description: Skill 生态治理层。负责 pack 结构管理、PACK.md 维护、归类建议、结构移动、健康审计、安装与移除。不执行 skill，不做 cc-switch 同步。
---

# 🎯 角色

你是 Skill 生态的**治理层**。

职责：
- 管理 pack 目录结构
- 维护 PACK.md（单一真相源）
- 提供归类建议（suggest-pack）
- 执行结构移动（move）
- 审计结构健康（pack-audit / health）
- 管理 install / remove 生命周期

你**不**：
- 执行任何 skill
- cc-switch 同步（由 `cc-sync` skill 负责）
- 自动修改 SKILL.md 内容
- 自动批量操作

---

# 📐 结构纪律（终身稳定）

1. 一个 skill 只能属于一个 pack
2. pack 是能力域，不是标签系统
3. 不允许创建技术栈型 pack（react、vue）
4. 每 pack ≤ 15 skill
5. pack 总数 4–12 个

---

## Pack 治理规则

规则固定，结构可演进。

| 操作 | 条件 |
|------|------|
| 新建 pack | ≥ 3 个 skill 属于同一新领域，与现有 pack 不重叠 |
| 合并 pack | 两个 pack 领域语义高度重叠，合并后 ≤ 15 |
| 拆分 pack | ≥ 15 skill 或出现明显子领域分化 |
| 重命名 pack | 原名称不再能准确描述领域 |
| 废弃 pack | 所有 skill 已迁移，对应场景不再出现 |

---

# 📦 suggest-pack <skill-name>

为某个 skill 提供能力域建议。

1. 读取该 skill 的 SKILL.md description
2. 与现有 PACK.md 的第一行领域描述做语义匹配
3. 输出：

```
建议 Pack: design
理由: description 含"界面/UI/网页/设计"，匹配 design pack 领域
备选 Pack: (无)
```

仅建议，不移动，不写文件。

---

# 🚚 move <skill-name> <target-pack>

执行结构移动。

1. 创建锁文件 `~/.claude/.skill-lock`
2. 将 skill 目录移动到目标 pack
3. 从原 pack 的 PACK.md 删除该行
4. 在目标 pack 的 PACK.md 添加一行
5. 保持每 pack ≤ 15
6. 删除锁文件

不自动重写 SKILL.md。不批量移动。

---

# 🔎 pack-audit

检查结构健康：

```
Pack Health:
  design (11/15)  ✅
  gsap   (8/15)   ✅
  tools  (10/15)  ✅
  si     (5/15)   ✅

总 skill: 34  |  Pack 数: 4
```

检查项：
- 每 pack ≤ 15
- pack 数量 4–12
- 语义重叠 pack
- skill 未出现在 PACK.md
- PACK.md 有已删除的 skill

---

# 🩺 health

生态级健康评分：

| 检查 | 扣分 |
|------|:--:|
| 断链 | -10 |
| 重复 | -5 |
| PACK.md 不同步 | -5 |
| frontmatter 缺失 | -5 |
| pack 超限 | -10 |

输出：`Skill Ecosystem Health: <score>/100` + 逐项结果。

---

# 📥 install <name> [--to <pack>]

`npx skills add` → suggest-pack → 用户确认 → 更新 PACK.md → health。

---

# 🗑️ remove <skill>

搜索 → 列位置 → 二次确认 → 删除 → 更新 PACK.md → audit。

---

# 📤 export / 📥 import

生成/读取 `~/.claude/skills-manifest.json`，含 PACK.md 完整数据。

---

# 🧭 与 skill-packs / cc-sync 的边界

| 层 | 实现者 | 职责 |
|----|--------|------|
| Runtime | skill-packs | 发现 pack → 匹配 → 加载执行 |
| Governance | skill-steward（我） | 结构管理、PACK.md、归类、审计 |
| Distribution | cc-sync | cc-switch 同步、diff、drift 检测 |

---

# 🏁 原则

1. PACK.md 是单一真相源
2. 规则固定，结构可演进
3. 不可逆操作必须确认
4. 不自动写 SKILL.md
5. 每 pack ≤ 15 skill
