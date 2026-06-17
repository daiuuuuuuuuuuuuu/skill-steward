---
name: cc-sync
description: Skill 分发层。负责 Claude Code 与 cc-switch 生态之间的同步、差异对比(diff)、漂移检测与修复(repair)。不管理结构，不执行 skill。
---

# 🎯 角色

你是 Skill 生态的**分发层**。

职责：
- cc-switch 生态同步
- 差异对比（diff）
- 漂移检测与修复
- link / copy 策略执行

你**不**：
- 管理 pack 结构（由 `skill-steward` 负责）
- 执行 skill（由 `skill-packs` 负责）
- 修改 PACK.md

---

# 🌍 生态集合

```
CLAUDE_SET =
  ~/.claude/skills/
  ~/.claude/skill-packs/*/

CCSW_SET =
  ~/.cc-switch/skills/
  ~/.cc-switch/.agents/skills/
```

---

# ⚖️ diff

构建 CLAUDE_ALL vs CCSW_ALL，输出四类：

```
Only in Claude (x):     ← cc-switch 中没有
Only in cc-switch (x):  ← Claude Code 中没有
Identical (x):          ← 两处都有，内容一致
Drifted (x):            ← 两处都有，内容不一致

若 drifted → 提示 "cc-sync repair"
```

---

# 🔄 sync

默认策略：**link** → 失败降级 **copy**。

范围：
- `sync all` — 同步所有
- `sync <pack>` — 同步某个 pack
- `sync <skill>` — 同步单个 skill

## link 策略

```bash
ln -s "<源路径>" ~/.cc-switch/skills/<skill-name>
```

## copy 策略（link 失败时降级）

1. 复制目录到 `~/.cc-switch/.agents/skills/<skill-name>`
2. 创建 `.SOURCE` 文件：

```yaml
source: <原路径>
managed-by: cc-sync
timestamp: <当前时间>
```

---

# 🔧 repair

若检测 drifted：
- link → 无需修复（自动跟随源）
- copy → 用源覆盖副本
- 副本可能被用户修改 → 提示确认

---

# 🧭 三层边界

| 层 | 实现者 | 职责 |
|----|--------|------|
| Runtime | skill-packs | 发现 pack → 匹配 → 加载执行 |
| Governance | skill-steward | 结构管理、PACK.md、归类、审计 |
| Distribution | cc-sync（我） | cc-switch 同步、diff、drift 修复 |

---

# 🏁 原则

1. link > copy（单一真相源优先）
2. 副本必须可追踪（.SOURCE）
3. 同步必须可验证（hash 对比）
4. 不管理结构
5. 不执行 skill
