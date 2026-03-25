# Marketplace 結構學習筆記

日期：2026-03-25

## 學到的事

### Claude Code Plugin Marketplace 的三層結構

1. Marketplace（`.claude-plugin/marketplace.json`）
   - 定義 marketplace 名稱、owner、包含的 plugins 清單
   - 每個 plugin 項目有 name、description、version、source、category

2. Plugin（`skills/<plugin-name>/.claude-plugin/plugin.json`）
   - 定義單一 plugin 的 name、version、description、author
   - 版號管理：major（重大變更）、minor（增減 skill）、patch（修改行為）

3. Skill（`skills/<plugin-name>/skills/<skill-name>/SKILL.md`）
   - 必須放在 `skills/` 子目錄下，不能直接放在 plugin root
   - 使用 frontmatter 定義 name 和 description（觸發條件寫在 description 裡）
   - 內容是 markdown 格式的步驟說明

### 關鍵目錄規則

```
skills/<plugin-name>/
├── .claude-plugin/plugin.json    ← 必須有這個
└── skills/                       ← 必須有這一層
    └── <skill-name>/
        └── SKILL.md              ← Claude Code 只認這個路徑
```

少了 `skills/` 這一層，skill 就不會被發現。

### 從 twjug-lite-infra 學到的好規則

- 只查不改原則：沒有明確指示不做變更
- 日記機制：學到就記，不等階段性完成
- Plugin 更新流程：改版號 → push → update marketplace → update plugin
- Skill 機密保護：不用 Read tool 讀機密檔、不在對話中暴露 token
- Skill 審查原則：根據複雜度建議 model（haiku/sonnet/opus）和是否適合 fork

## 決策

- 以自省（reflection）為主題建立 example plugin，因為這類 skill 不需要外部 infra 就能運作
- 三個自省 skills：daily-review、skill-candidate、retrospective
