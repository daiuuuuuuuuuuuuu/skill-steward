# Skill OS · 三层 Skill 操作系统

[![中文](https://img.shields.io/badge/文档-中文-red)](README.zh.md)
[![English](https://img.shields.io/badge/Docs-English-blue)](README.en.md)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

**Claude Code 的 Skill 操作系统**——Runtime · Governance · Distribution 三层架构。

---

## 🏗️ 系统架构

```
┌─────────────────────────────────┐
│   skill-packs（Runtime 层）       │  发现 → 匹配 → 加载 → 执行
├─────────────────────────────────┤
│   skill-steward（Governance 层）  │  结构管理 · PACK.md · 审计
├─────────────────────────────────┤
│   cc-sync（Distribution 层）      │  cc-switch 同步 · diff · repair
└─────────────────────────────────┘
```

## 📦 快速安装

```bash
git clone https://github.com/daiuuuuuuuuuuuuu/skill-steward.git
cd skill-steward
cp -r skills/skill-packs ~/.claude/skills/
cp -r skills/skill-steward ~/.claude/skills/
cp -r skills/cc-sync ~/.claude/skills/
```

## 🧭 三层边界

| 层 | 实现者 | 职责 | 权限 |
|----|--------|------|:--:|
| Runtime | skill-packs | 动态发现 pack → 匹配 → 加载执行 | 只读 |
| Governance | skill-steward | PACK.md 维护 · 归类 · 移动 · 审计 · 安装/移除 | 读写 |
| Distribution | cc-sync | cc-switch 同步 · diff · drift 修复 | 读写 |

## 🏷️ 标签

`claude-code` `skill-os` `skill-management` `skill-packs` `skill-steward` `cc-sync` `ai-agent` `anthropic` `self-hosting` `meta-skill`

---

MIT License
