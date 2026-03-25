---
name: reflect-skill-candidate
description: Analyze recent work to identify repetitive patterns that could become reusable skills. Trigger ONLY when user explicitly mentions "reflect-skill-candidate" or "找 skill 候選". Do NOT trigger on generic "skill" or "分析" requests.
---

# Reflect Skill Candidate

檢視近期工作內容，找出重複出現的模式或流程，建議哪些可以轉化為可重用的 skill。

## Prerequisites

1. 在 git repo 中執行
2. 有一定的工作歷史（至少幾次 commit 或幾篇日誌）

## Step 1: 收集工作模式

並行執行以下收集：

```bash
# 近期 commit 歷史（最近 7 天或 50 筆）
git log --oneline --since="7 days ago" -50

# 日誌檔案
ls -1 docs/daily/ 2>/dev/null | tail -20

# CLAUDE.md 中的現有規則
cat CLAUDE.md
```

讀取最近的日誌檔案內容（最多 10 篇）。

## Step 2: 分析重複模式

從收集到的資料中，尋找以下訊號：

| 訊號 | 說明 |
|------|------|
| 重複的指令序列 | 同樣的 bash 指令組合出現多次 |
| 重複的工作流程 | 類似的步驟反覆執行（如：收集→分析→輸出） |
| 重複的決策點 | 每次都要做的判斷（如：要不要 commit、要不要建新檔） |
| 規則化的行為 | 已在 CLAUDE.md 中明文規定但尚未自動化的流程 |
| 跨專案通用性 | 不限於特定專案的通用操作模式 |

## Step 3: 產出候選清單

對每個候選，產出：

```markdown
## Skill 候選 #N: <名稱>

觸發時機: <什麼情境下會用到>
重複頻率: <高/中/低>
自動化程度: <完全自動 / 需要互動 / 半自動>
跨專案通用性: <高/中/低>

### 大致流程
1. <step 1>
2. <step 2>
3. ...

### 為什麼值得變成 skill
<理由>
```

## Step 4: 排序與建議

按以下優先順序排序候選：
1. 重複頻率高 + 跨專案通用性高 → 最優先
2. 重複頻率高 + 專案特定 → 次優先
3. 重複頻率低但流程複雜 → 可考慮

詢問使用者：
1. 哪些候選要進一步發展成 skill
2. 是否有遺漏的模式

## Step 5: 記錄

若使用者選定候選，將分析結果存為日誌：
`docs/daily/YYYY-MM-DD-NN-skill-candidates.md`

## Error Handling

- 工作歷史太少（< 3 筆 commit 且無日誌）→ 告知使用者資料不足，建議累積更多工作後再分析
- 找不到明顯模式 → 如實告知，不硬湊

## 不在範圍內

- 不自動建立 skill（僅分析與建議）
- 不修改現有 skill
- 不執行 skill-creator（使用者決定後自行觸發）
