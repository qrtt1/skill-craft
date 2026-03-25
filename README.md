# Skill Craft

Claude Code Skill Marketplace，提供自省與品質維護的 plugins 與 skills。用自身的 skills 來維護這個 marketplace。

這不是一個拿來直接用的 marketplace，而是建立自己 skill 生態系的起點。內建的 reflect skills 是「核心」，幫助你在日常工作中發現重複模式、審查品質、自動化 release 流程。隨著使用，你會不斷從工作中提煉出新的 skills，讓這個 marketplace 長出屬於你自己的工作流程。

## 快速開始

### 安裝 marketplace

```bash
claude plugin marketplace add qrtt1/skill-craft
```

### 安裝 plugin

```bash
claude plugin install reflect-skill@skill-craft
claude plugin install reflect-work@skill-craft
```

> 點選 "Use this template" 建立你自己的 marketplace repo，安裝指令中的 `qrtt1/skill-craft` 請替換為你的 `<owner>/<repo>`。

## 包含的 Plugins

### reflect-skill — Skill 品質維護

- reflect-skill-review
  - 觸發："reflect-skill-review" 或 "審查 skill"
  - 審查 skill（model 選擇、sub-agent 適用性、機密保護）
- reflect-skill-candidate
  - 觸發："reflect-skill-candidate" 或 "找 skill 候選"
  - 分析工作模式，找出可以變成 skill 的重複流程
- reflect-skill-lint
  - 觸發："reflect-skill-lint" 或 "檢查結構"
  - 檢查 marketplace 目錄結構是否符合規範
- reflect-skill-release
  - 觸發："reflect-skill-release" 或 "release plugin"
  - 自動化版號同步、commit、push、更新 marketplace 與 plugins

### reflect-work — 個人工作回顧

- reflect-daily-review
  - 觸發："reflect-daily-review" 或 "回顧今天"
  - 回顧今天的工作，產出結構化摘要
- reflect-retrospective
  - 觸發："reflect-retrospective" 或 "回顧規則"
  - 回顧累積知識，建議更新 CLAUDE.md

## Skill 生命週期

新增或修改 skill 時，依序執行：
1. 建立或修改 SKILL.md（手動撰寫或使用 `skill-creator`）
2. `reflect-skill-review` — 審查 skill
3. `reflect-skill-lint` — 檢查目錄結構與版號一致性
4. `reflect-skill-release` — 同步版號、commit、push、更新 marketplace 與 plugins

## License

MIT
