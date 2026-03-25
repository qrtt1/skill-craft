---
name: reflect-skill-lint
description: Lint marketplace structure - verify plugin directories, plugin.json, SKILL.md paths, and marketplace.json registration. Trigger ONLY when user explicitly mentions "reflect-skill-lint" or "檢查結構". Do NOT trigger on generic "lint", "check", or "檢查" requests.
---

# Reflect Skill Lint

檢查 marketplace 的目錄結構是否正確。驗證每個 plugin 和 skill 的檔案配置符合 Claude Code 的規範。

## Step 1: 掃描結構

收集所有需要的資訊：

```bash
# 所有 plugin.json
find skills/ -path "*/.claude-plugin/plugin.json" -type f

# 所有 SKILL.md
find skills/ -name "SKILL.md" -type f

# marketplace.json
cat .claude-plugin/marketplace.json
```

## Step 2: 逐項檢查

對每個 plugin 目錄執行以下檢查：

| 檢查項目 | 規則 |
|---------|------|
| plugin.json 存在 | `skills/<plugin-name>/.claude-plugin/plugin.json` 必須存在 |
| plugin.json 格式 | 必須有 name、version、description 欄位 |
| skills/ 目錄存在 | `skills/<plugin-name>/skills/` 必須存在 |
| SKILL.md 路徑 | 每個 skill 必須在 `skills/<plugin-name>/skills/<skill-name>/SKILL.md` |
| SKILL.md frontmatter | 必須有 name 和 description 欄位 |
| marketplace.json 註冊 | 每個有 plugin.json 的 plugin 都必須在 marketplace.json 中有對應項目 |
| 版號同步 | marketplace.json 中的版號應與 plugin.json 一致 |

## Step 3: 產出報告

```markdown
# 結構檢查報告

## 通過
- [x] <check description>

## 問題
- [ ] <plugin-name>: <問題描述>

## 摘要
N 個 plugin, M 個 skill, X 個問題
```

若全部通過 → 顯示摘要即可，不需要逐條列出。
若有問題 → 列出所有問題，並建議修正方式。

## Error Handling

- marketplace.json 不存在 → 告知使用者需要先建立
- skills/ 目錄不存在 → 告知使用者目錄結構尚未初始化

## 不在範圍內

- 不自動修正問題（只報告）
- 不檢查 SKILL.md 的內容品質（那是 reflect-skill-review 的工作）
- 不檢查 git 狀態
