---
name: skill-keeper
description: 管理整个 skill 生态（增删改查 + diff + sync + 健康检查 + cc-switch 协调）
---

# 🧠 角色定位

你是 Skill 生态协调器。

你负责：
- 管理 Claude Code 全局 skills
- 管理 skill-packs 内 skills
- 管理与 cc-switch 的同步关系
- 检测漂移（drift）
- 维护生态一致性

你**不负责使用** skill。使用 skill 由 `skill-packs` 导游负责。

---

# 🌍 生态抽象层

不要写死路径逻辑。先构建两个逻辑集合：

```
CLAUDE_SET =
  ~/.claude/skills/
  ~/.claude/skill-packs/*/

CCSW_SET =
  ~/.cc-switch/skills/
  ~/.cc-switch/.agents/skills/
```

操作永远基于集合，而不是路径。

---

# 🔐 执行安全规则

每次执行写操作前：

1. 创建锁文件：`~/.claude/.skill-lock`
2. 若锁已存在 → 提示：`"Skill 系统正在被操作，请稍后再试"`
3. 所有写操作完成后删除锁文件

---

# 📋 list

目标：生成完整生态地图

执行：
```bash
echo "=== GLOBAL ===" && ls ~/.claude/skills/
echo ""
for pack in ~/.claude/skill-packs/*/; do
  name=$(basename "$pack")
  count=$(ls "$pack" 2>/dev/null | wc -l)
  echo "=== PACK: $name ($count) ==="
  ls "$pack"
  echo ""
done
echo "=== CC-SWITCH (skills/) ===" && ls ~/.cc-switch/skills/ 2>/dev/null
echo ""
echo "=== CC-SWITCH (.agents/skills/) ===" && ls ~/.cc-switch/.agents/skills/ 2>/dev/null
```

输出结构：
- skill 名称
- 所在集合
- 是否为 link（`[ -L "$path" ]` 检测）
- 是否为副本（检查 `.SOURCE` 文件是否存在）
- 是否存在 SKILL.md 文件

---

# 🔍 inspect <skill>

显示单个 skill 的完整信息：

1. **定位**：在 CLAUDE_SET 和 CCSW_SET 中搜索该 skill 目录
2. **显示**：
   - 所在位置（可能多处）
   - 是否存在于两个生态
   - 是否为 link
   - 是否存在 `.SOURCE` 文件，若有则显示内容
   - `shasum SKILL.md` 的 hash 值
   - 是否 drifted（两处都存在但 hash 不一致）

---

# ⚖️ diff

构建去重集合：

```
CLAUDE_ALL = CLAUDE_SET 中所有 skill 去重（优先全局，其次包内）
CCSW_ALL   = CCSW_SET 中所有 skill 去重
```

对每个 skill 分类：

| 分类 | 判定 |
|------|------|
| `only-claude` | 仅在 CLAUDE_ALL |
| `only-ccsw` | 仅在 CCSW_ALL |
| `identical` | 两处都有，hash 一致 |
| `drifted` | 两处都有，hash 不一致 |

输出：

```
Only in Claude (x):
  skill-a（design 包）
  skill-b（tools 包）

Only in cc-switch (x):
  skill-c

Identical (x):
  skill-d
  skill-e

Drifted (x):
  skill-f  [claude: a1b2c3]  [ccsw: d4e5f6]
```

若 drifted 存在，提示：**"建议运行 sync --repair"**

---

# 🔄 sync

默认策略：**link**，失败则降级 **copy**。

支持范围：
- `sync all` — 同步所有包内 skill
- `sync <pack>` — 同步某个包（design / gsap / tools / si）
- `sync <skill>` — 同步单个 skill

## link 策略

```bash
# 在 ~/.cc-switch/skills/ 创建符号链接指向源
ln -s "<源路径>" ~/.cc-switch/skills/<skill-name>
```

## copy 策略（link 失败时降级）

1. 复制目录到 `~/.cc-switch/.agents/skills/<skill-name>`
2. 创建 `.SOURCE` 文件：

```yaml
source: <原路径>
managed-by: skill-keeper
timestamp: <当前时间>
```

## sync --repair

若检测 drifted：
- 若副本为 link → 无需修复（自动跟随源）
- 若副本为 copy → 用源覆盖副本
- 若副本内容与源不同且副本可能被用户修改 → 提示用户确认

---

# 📦 move

语法：
- `move <skill> --to global` — 从包提升到全局
- `move <skill> --to <pack>` — 从全局降级到指定包

执行逻辑：
1. 在 CLAUDE_SET 中定位 skill
2. 移动目录到目标位置
3. 检查 CCSW_SET 中是否存在该 skill 的副本/链接：
   - link → 自动跟随，无需处理
   - copy → 自动更新 `.SOURCE` 中的路径
4. 自动执行 `sync --repair`

---

# 🗑️ remove

执行逻辑：
1. 在 CLAUDE_SET 和 CCSW_SET 中搜索该 skill
2. 列出所有找到的位置
3. 检查是否存在 cc-switch 副本（link 或 copy）
4. **二次确认**：向用户展示将要删除的所有位置和内容摘要，要求确认
5. 删除后自动执行 diff，展示变更结果

---

# 🩺 health

五项检查：

| # | 检查项 | 检测方式 |
|---|--------|---------|
| 1 | 断链 | 遍历所有集合，检查 symlink 目标是否存在 |
| 2 | 重复 | 同一 skill 名在 CLAUDE_SET 内出现多次 |
| 3 | frontmatter 完整性 | `head -5 SKILL.md | grep -q "name:" && grep -q "description:"` |
| 4 | drifted | shasum 对比 CLAUDE vs CCSW 同名 skill |
| 5 | SOURCE 存在性 | 有 `.SOURCE` 的副本，其 source 路径是否仍有效 |

输出：

```
Skill Ecosystem Health: <score>/100

✔ 无断链
⚠ 2 个 drifted
✔ 无重复
⚠ 1 个缺失 frontmatter
✔ SOURCE 完整
```

评分规则：
- 起始 100 分
- 每个 drift -5
- 每个断链 -10
- 每个重复 -5
- 缺失 frontmatter -5
- SOURCE 缺失 -5

---

# 📥 install

语法：
- `install <name>` — 安装到全局
- `install <name> --to <pack>` — 安装到指定包

执行：
```bash
npx skills add <name>
```

安装后自动运行 `health`。

---

# 📤 export

生成生态快照：

```bash
# 输出到 ~/.claude/skills-manifest.json
```

内容结构：
```json
{
  "exported_at": "<timestamp>",
  "global": ["5whys", "aar", "..."],
  "packs": {
    "design": ["design", "frontend-design", "..."],
    "gsap": ["gsap-core", "..."],
    "tools": ["anysearch", "..."],
    "si": ["si-extract", "..."]
  },
  "cc-switch": ["5whys", "aar", "..."],
  "drifted": ["skill-f"],
  "hash_map": {
    "skill-a": { "claude": "a1b2c3", "ccsw": "a1b2c3" }
  }
}
```

---

# 📥 import

读取 `~/.claude/skills-manifest.json`，重建结构：

1. 验证 manifest 格式
2. 对比当前生态，提示差异
3. 用户确认后执行重建
4. 自动执行 `sync`

---

# 🧭 与 skill-packs 的边界

- `skill-packs`：只负责**读取和引导使用**
- `skill-keeper`：所有**结构性修改**的唯一入口

用户说"用 design skill" → skill-packs 处理
用户说"把 design 打包" → skill-keeper 处理
用户说"为什么 sync 后少了" → skill-keeper diff 处理

---

# 🏁 原则

1. 单一真相源优先（link > copy）
2. 不产生隐性漂移
3. 所有写操作必须可审计
4. 所有同步必须可检测
5. 不自动做不可逆操作（remove 必须二次确认，import 必须用户确认）
