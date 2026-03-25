---
name: reflect-retrospective
description: Review accumulated knowledge and suggest updates to CLAUDE.md rules. Trigger ONLY when user explicitly mentions "reflect-retrospective" or "回顧規則". Do NOT trigger on generic "retrospective" or "回顧" requests.
---

# Reflect Retrospective

回顧累積的工作知識，檢視 CLAUDE.md 是否需要更新。從日誌、commit 歷史、和工作模式中提煉出應該被規則化的知識。

## Prerequisites

1. 在 git repo 中執行
2. CLAUDE.md 存在

## Step 1: 收集資料

```bash
# 讀取現有 CLAUDE.md
cat CLAUDE.md

# 所有日誌檔案
ls -1 docs/daily/ 2>/dev/null

# 近期 commit 歷史
git log --oneline -30
```

讀取最近的日誌檔案（最多 15 篇）。

## Step 2: 比對現有規則與實際行為

分析以下面向：

| 面向 | 檢查內容 |
|------|---------|
| 缺失規則 | 日誌中反覆提到但 CLAUDE.md 沒有的知識 |
| 過時規則 | CLAUDE.md 中的規則已不符合實際做法 |
| 模糊規則 | 規則描述不夠具體，導致每次都要重新判斷 |
| 矛盾規則 | 規則之間有衝突 |

## Step 3: 產出建議

格式：

```markdown
# CLAUDE.md 回顧建議

## 建議新增的規則
### 1. <規則名稱>
- 來源: <從哪篇日誌或哪個 commit 觀察到>
- 建議內容: <規則文字>
- 理由: <為什麼需要這條規則>

## 建議修改的規則
### 1. <現有規則名稱>
- 現狀: <目前的規則文字>
- 建議: <修改後的規則文字>
- 理由: <為什麼要改>

## 建議移除的規則
### 1. <規則名稱>
- 理由: <為什麼不再需要>

## 無需變更
<確認哪些規則仍然適用且描述正確>
```

## Step 4: 互動確認

逐條詢問使用者：
1. 同意 → 直接修改 CLAUDE.md
2. 修改 → 調整後再確認
3. 跳過 → 不修改

修改 CLAUDE.md 前必須獲得使用者同意（這本身就是規則之一）。

## Step 5: 記錄

將本次回顧結果存為日誌：
`docs/daily/YYYY-MM-DD-NN-retrospective.md`

## Error Handling

- CLAUDE.md 不存在 → 建議使用者先建立，或提供初始版本
- 日誌太少 → 僅從 commit 歷史和 CLAUDE.md 本身分析
- `docs/daily/` 目錄不存在 → 在 Step 5 儲存時詢問使用者是否建立目錄

## 不在範圍內

- 不修改程式碼
- 不修改 skill 定義
- 不自動修改 CLAUDE.md（必須經過使用者確認）
