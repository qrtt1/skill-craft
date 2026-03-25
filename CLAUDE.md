# Skill Craft

這是一個 Claude Code Skill Marketplace，提供自省與品質維護的 plugins 與 skills。
專案目標是持續發展與維護高品質的 skill 生態系，並透過內建的 reflect skills 協助：
1. 新增與更新 plugins 及 skills
2. 維護 skill 品質（審查、回顧、發現可自動化的模式）

## 工作環境
- 使用 uv 管理 Python 依賴，執行 Python 指令時使用 `uv run`
- 永遠以 project root 作為工作目錄

## Plugin 目錄結構規則
每個 plugin 子目錄內必須有 `skills/` 這一層，Claude Code 在此路徑下搜尋 skills：

```
skills/<plugin-name>/
├── .claude-plugin/plugin.json
└── skills/
    └── <skill-name>/
        └── SKILL.md
```

## Skill 生命週期
新增或修改 skill 時，依序執行：
1. 建立或修改 SKILL.md（手動撰寫或使用 `skill-creator`）
2. `reflect-skill-review` — 審查 skill（含 model 選擇、sub-agent 適用性、機密保護檢查）
3. `reflect-skill-lint` — 檢查目錄結構與版號一致性
4. `reflect-skill-release` — 同步版號、commit、push、更新 marketplace 與 plugins

## Retrospective
- 工作過程中適時將學到的重點整理進 CLAUDE.md
- 修改 CLAUDE.md 前須獲得使用者同意
- 發現重要規則時，詢問是否加入 CLAUDE.md

## 規則管理
- 發現重要規則時，詢問是否加入 CLAUDE.md
