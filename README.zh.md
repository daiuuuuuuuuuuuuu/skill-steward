# skill-keeper · Skill 生态管理器

**两个 Claude Code skill，一套 Skill 生态管理方案。**

---

## 🎯 包含两个 Skill

### 📖 skill-packs — 运行时加载器（只读）

发现、搜索、按需加载打包的非全局 skill。不修改任何结构。

| 用户说 | 行为 |
|--------|------|
| "用 frontend-design" | 搜索 → 匹配 → 加载 SKILL.md → 执行 |
| "包里有什么" | 自动发现所有 pack → 列出 skill 名和描述 |
| "有没有动画相关的 skill" | 关键词搜索 description → 展示候选 |
| "帮我设计个登录页" | 智能匹配最相关的设计 skill |

### 🔧 skill-keeper — 生态协调器（读写）

管理全局 skill、打包 skill、与 cc-switch 的同步、漂移检测、健康检查。

| 命令 | 行为 |
|------|------|
| `list` | 生成完整生态地图 |
| `inspect <skill>` | 查看详情（位置/hash/drift/副本） |
| `diff` | 对比 Claude Code ↔ cc-switch 差异 |
| `sync [all\|pack\|skill]` | 同步到 cc-switch（link 优先，降级 copy） |
| `move <skill> --to global\|<pack>` | 迁移 skill |
| `remove <skill>` | 删除（二次确认） |
| `health` | 健康评分 /100 |
| `install <name>` | 从 marketplace 安装 |
| `export` / `import` | 生态快照备份/恢复 |

---

## 📦 安装

```bash
git clone https://github.com/n15893791173-prog/skill-keeper.git
cd skill-keeper
cp -r skills/skill-packs ~/.claude/skills/skill-packs
cp -r skills/skill-keeper ~/.claude/skills/skill-keeper
```

或：

```bash
npx skills add n15893791173-prog/skill-keeper --skill skill-packs
npx skills add n15893791173-prog/skill-keeper --skill skill-keeper
```

---

## 🧭 两个 Skill 的边界

- **skill-packs**：负责"用"——列出、搜索、加载执行
- **skill-keeper**：负责"管"——增删改查、同步、健康检查

结构性修改只能通过 skill-keeper 进行。

---

## 🏷️ 标签

`claude-code` `claude-code-skill` `skill-management` `skill-keeper` `skill-packs` `ai-agent` `anthropic` `cc-switch` `Claude技能` `技能管理` `技能生态` `AI工具`

---

MIT License
