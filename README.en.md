# skill-steward · Skill Ecosystem Manager

**Two Claude Code skills, one complete skill ecosystem management solution.**

---

## 🎯 Two Skills

### 📖 skill-packs — Runtime Loader (Read-Only)

Discovers, searches, and loads packed non-global skills on demand. Never modifies structure.

| User says | Behavior |
|-----------|----------|
| "Use frontend-design" | Search → match → load SKILL.md → execute |
| "What's in the packs" | Auto-discover all packs → list skill names & descriptions |
| "Any animation skills?" | Keyword search descriptions → show candidates |
| "Design a login page" | Smart match the most relevant design skill |

### 🔧 skill-steward — Ecosystem Coordinator (Read-Write)

Manages global skills, packed skills, cc-switch sync, drift detection, and health checks.

| Command | Behavior |
|---------|----------|
| `list` | Generate full ecosystem map |
| `inspect <skill>` | Show details (location/hash/drift/copies) |
| `diff` | Compare Claude Code ↔ cc-switch |
| `sync [all\|pack\|skill]` | Sync to cc-switch (link first, fallback copy) |
| `move <skill> --to global\|<pack>` | Migrate a skill |
| `remove <skill>` | Delete with double confirmation |
| `health` | Health score /100 |
| `install <name>` | Install from marketplace |
| `export` / `import` | Ecosystem snapshot backup/restore |

---

## 📦 Install

```bash
git clone https://github.com/daiuuuuuuuuuuuuu/skill-steward.git
cd skill-steward
cp -r skills/skill-packs ~/.claude/skills/skill-packs
cp -r skills/skill-steward ~/.claude/skills/skill-steward
```

Or one-liner:

```bash
npx skills add daiuuuuuuuuuuuuu/skill-steward --skill skill-packs
npx skills add daiuuuuuuuuuuuuu/skill-steward --skill skill-steward
```

---

## 🧭 Boundary

- **skill-packs**: "Use" — list, search, load, execute
- **skill-steward**: "Manage" — CRUD, sync, health checks

All structural modifications go through skill-steward.

---

## 🏷️ Tags

`claude-code` `claude-code-skill` `skill-management` `skill-steward` `skill-packs` `ai-agent` `anthropic` `cc-switch` `ecosystem` `devtools`

---

MIT License
