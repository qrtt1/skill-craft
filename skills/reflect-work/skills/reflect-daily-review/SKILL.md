---
name: reflect-daily-review
description: Review today's work by scanning git log, conversation context, and docs/daily/ entries. Trigger ONLY when user explicitly mentions "reflect-daily-review" or "回顧今天". Do NOT trigger on generic "review" or "回顧" requests.
---

# Reflect Daily Review

回顧今天的工作，整理出摘要與洞察。從 git log、對話脈絡、docs/daily/ 日誌中收集資訊，產出結構化的每日回顧。

## Prerequisites

1. 在 git repo 中執行

## Step 1: 收集今天的工作資料

並行執行以下收集：

```bash
# 今天的 git commits
git log --oneline --since="$(date +%Y-%m-%d)T00:00:00" --all

# 今天的 git diff 統計（若今天有 commit）
FIRST_COMMIT=$(git log --format=%H --since="$(date +%Y-%m-%d)T00:00:00" --reverse | head -1)
if [ -n "$FIRST_COMMIT" ]; then
  PARENT=$(git rev-parse "$FIRST_COMMIT"^ 2>/dev/null || echo "$FIRST_COMMIT")
  git diff --stat "$PARENT"..HEAD
fi

# 今天的日誌檔案
ls docs/daily/$(date +%Y-%m-%d)-* 2>/dev/null
```

若有日誌檔案，讀取其內容。

## Step 2: 整理工作摘要

從收集到的資料中整理：

1. 主要完成的事項（從 commit messages 和日誌推斷）
2. 變更的範圍（哪些檔案、哪些模組）
3. 未完成或進行中的事項

## Step 3: 產出回顧

格式：

```markdown
# 每日回顧 - YYYY-MM-DD

## 今天完成的事
- <item 1>
- <item 2>

## 變更範圍
- <files/modules changed>

## 進行中 / 未完成
- <pending items>

## 觀察與建議
- <insights, patterns, or suggestions>
```

## Step 4: 詢問後續

問使用者：
1. 是否要將回顧存為日誌（`docs/daily/YYYY-MM-DD-NN-daily-review.md`）
2. 是否有遺漏需要補充

## Error Handling

- 沒有今天的 commits → 告知使用者今天尚無 git 記錄，詢問是否要從對話脈絡回顧
- 不在 git repo → 告知使用者，僅從 docs/daily/ 和對話脈絡回顧
- `docs/daily/` 目錄不存在 → 若使用者選擇儲存回顧，詢問是否建立目錄

## 不在範圍內

- 不自動 commit
- 不修改任何程式碼
- 不產生跨天的回顧（僅限當天）
