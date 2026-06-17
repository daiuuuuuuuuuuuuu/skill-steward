# skill-keeper · Skill 生态管理器

[![中文](https://img.shields.io/badge/文档-中文-red)](README.zh.md)
[![English](https://img.shields.io/badge/Docs-English-blue)](README.en.md)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

**Claude Code 的 Skill 生态管理工具包**——两个互补的 skill，一个管"用"，一个管"管"。

`skill-packs` 是运行时加载器（只读），`skill-keeper` 是生态协调器（读写）。

---

## 🎯 两个 Skill

### 📖 skill-packs — 运行时加载器

**职责**：发现、搜索、按需加载打包的 skill。只读文件系统，不修改结构。

```
用户："用 frontend-design"      → 搜索 → 匹配 → 加载 → 执行
用户："包里有什么"              → 自动发现 pack → 列出
用户："有没有动画相关的 skill"   → 关键词搜索 → 展示候选
```

### 🔧 skill-keeper — 生态协调器

**职责**：管理整个 skill 生态，包括与 cc-switch 同步、漂移检测、健康检查。

```
skill-keeper list              → 生态地图（全局 + 包 + cc-switch）
skill-keeper diff              → 对比 Claude Code ↔ cc-switch 差异
skill-keeper sync all          → 同步到 cc-switch
skill-keeper health            → 健康评分 /100
skill-keeper move <s> --to global → 迁移 skill
```

---

## 📦 快速安装

```bash
# 克隆仓库
git clone https://github.com/n15893791173-prog/skill-keeper.git
cd skill-keeper

# 安装到 Claude Code
cp -r skills/skill-packs ~/.claude/skills/skill-packs
cp -r skills/skill-keeper ~/.claude/skills/skill-keeper
```

或者用 npx 一键安装：

```bash
npx skills add n15893791173-prog/skill-keeper --skill skill-packs
npx skills add n15893791173-prog/skill-keeper --skill skill-keeper
```

---

## 🧭 边界

```
用户请求
  ├─ "用 / 加载 / 搜索 skill"  →  skill-packs（只读）
  └─ "安装 / 删除 / 移动 / 同步" →  skill-keeper（读写）
```

两个 skill 互相知晓对方存在，必要时互相引导。

---

## 🏗️ 架构

```
~/.claude/
├── skills/              ← 全局 skill（18 个，含 skill-packs & skill-keeper）
├── skill-packs/         ← 打包 skill（34 个，4 个分类）
│   ├── design/  (11)
│   ├── gsap/    (8)
│   ├── tools/   (10)
│   └── si/      (5)
└── .skill-lock          ← skill-keeper 写操作锁

~/.cc-switch/            ← cc-switch 生态（skill-keeper 交互对象）
```

---

## 📄 许可

MIT License

---

## 🏷️ 标签

`claude-code` `claude-code-skill` `skill-management` `skill-keeper` `skill-packs` `ai-agent` `anthropic` `cc-switch` `ecosystem` `devtools` `Claude技能` `技能管理` `技能生态` `AI工具` `开发工具`

---

*Made with ❤️ for the Claude Code community*
