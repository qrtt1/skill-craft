---
name: reflect-skill-release
description: Automate marketplace plugin release - sync versions, commit, push, and update. Trigger ONLY when user explicitly mentions "reflect-skill-release" or "release plugin". Do NOT trigger on generic "release", "push", or "更新" requests.
---

# Reflect Skill Release

自動化 marketplace plugin 的 release 流程。確保 marketplace.json 版號與各 plugin.json 同步，然後 commit、push、更新 marketplace 與 plugin。

## 背景

手動 release 容易遺漏步驟，尤其是 marketplace.json 版號同步。這個 skill 將整個流程自動化。

## Step 1: 偵測變更

掃描所有 plugin.json 與 marketplace.json，比對版號是否一致：

```bash
# 列出所有 plugin.json
find skills/ -path "*/.claude-plugin/plugin.json" -type f
```

對每個 plugin.json，讀取其 version，再與 marketplace.json 中對應 plugin 的 version 比對。

產出差異表：

```
| Plugin | plugin.json | marketplace.json | 狀態 |
|--------|------------|-----------------|------|
| reflect-skill | 0.2.0 | 0.1.0 | 需同步 |
| reflect-work | 0.1.0 | 0.1.0 | 一致 |
```

若全部一致且 git 沒有未 commit 的 skill 變更 → 告知使用者沒有需要 release 的內容，結束。

## Step 2: 檢查未 commit 的變更

```bash
git status --short
```

若有未 commit 的 skill 相關變更，提醒使用者先處理：
- 建議 commit message
- 詢問是否要一起 commit

若有未 commit 的變更但跟 skill 無關 → 提醒但不阻擋。

## Step 3: 同步 marketplace.json 版號

將 marketplace.json 中每個 plugin 的 version 更新為對應 plugin.json 的版號。

顯示即將做的修改，詢問使用者確認後執行。

## Step 4: Commit 與 Push

將 marketplace.json 的變更 commit：

```bash
git add .claude-plugin/marketplace.json
git commit -m "chore: sync marketplace.json versions"
git push
```

若 Step 2 有一起 commit 的變更，合併成一個 commit。

## Step 5: 更新 Marketplace 與 Plugin

依序執行：

```bash
claude plugin marketplace update <marketplace-name>
```

然後對每個版號有變更的 plugin 執行：

```bash
claude plugin update <plugin-name>@<marketplace-name>
```

marketplace name 從 marketplace.json 的 `name` 欄位取得。

## Step 6: 完成摘要

```markdown
# Release 完成

## 版號同步
| Plugin | 版本 |
|--------|------|
| <plugin> | <version> |

## 執行結果
- [x] marketplace.json 版號同步
- [x] commit & push
- [x] marketplace update
- [x] plugin update: <list>
```

## Error Handling

- git push 失敗 → 顯示錯誤，不繼續後續步驟
- marketplace update 失敗 → 顯示錯誤，不繼續 plugin update
- plugin update 失敗 → 顯示錯誤，繼續其他 plugin（不因單一失敗中斷）
- 沒有 remote → 告知使用者需要先設定 remote

## 不在範圍內

- 不決定版號應該是什麼（版號由 skill 開發者在 plugin.json 中管理）
- 不修改 skill 內容
- 不建立新 plugin
