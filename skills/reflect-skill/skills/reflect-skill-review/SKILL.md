---
name: reflect-skill-review
description: Review a skill after creation or modification via skill-creator. Evaluates model selection and sub-agent suitability. Trigger ONLY when user explicitly mentions "reflect-skill-review" or "審查 skill". Do NOT trigger on generic "review" requests.
---

# Reflect Skill Review

在使用 `skill-creator` 建立或修改 skill 之後，對 skill 進行審查。評估 model 選擇與是否適合以 sub-agent 執行。

## 使用時機

接在 `skill-creator` skill 之後執行。流程：
1. 用 `skill-creator` 建立/修改 skill
2. 用 `reflect-skill-review` 審查

## Step 1: 確認目標 Skill（主流程，需互動）

讀取要審查的 SKILL.md 檔案。若使用者沒指定，列出最近修改的 skill 讓使用者選擇：

```bash
find skills/ -name "SKILL.md" -newer .git/index -type f 2>/dev/null
```

若沒找到最近修改的，列出所有 skill 讓使用者選。

確認後，讀取 SKILL.md 內容備用。

## Step 2: Dispatch Sub-agent 進行分析

將分析工作委派給 sub-agent。分析不需要對話脈絡，只需讀取檔案內容，適合獨立執行。

使用 Agent tool dispatch，prompt 範本如下：

```
description: "Review skill: <skill-name>"
prompt: |
  你是一個 skill 審查員。請分析以下 SKILL.md 並產出審查報告。

  ## 目標 Skill

  路徑: <skill-path>

  ## 審查項目

  ### 1. 建議 Model

  根據 skill 內容判斷建議的執行 model：

  | Model | 適用情境 |
  |-------|---------|
  | haiku | 流程固定、步驟明確、不需推理判斷（如格式轉換、固定範本套用） |
  | sonnet | 需要邏輯判斷、條件分支、或與使用者互動決策 |
  | opus | 需要理解跨檔架構、做出設計決策、或處理複雜錯誤情境 |

  判斷依據：
  - 有多少條件分支和決策點
  - 是否需要理解程式碼語意
  - 是否需要與使用者多次互動
  - 步驟的複雜度與推理深度

  ### 2. Sub-agent 適用性

  判斷這個 skill 是否適合以 sub-agent 執行：

  適合 sub-agent 的訊號：
  - 所需資訊都在檔案系統中
  - 不依賴對話上下文
  - 不需要使用者即時確認
  - 任務目標明確、可獨立完成

  不適合 sub-agent 的訊號：
  - 需要對話歷史才能理解脈絡
  - 過程中需要使用者多次互動確認
  - 需要跨 skill 協作的上下文

  若 skill 部分適合、部分不適合，指出哪些步驟可以拆給 sub-agent。

  ### 3. 機密保護

  掃描 SKILL.md 內容，確認沒有以下問題：
  - 直接使用 Read tool 讀取可能含機密的設定檔
  - 要求使用者在對話中貼上 token、API key 等
  - 在步驟中輸出機密值到對話

  ## 報告格式

  請嚴格按照以下格式產出：

  # Skill 審查報告: <skill-name>

  ## 建議 Model
  <haiku / sonnet / opus> — <理由>

  ## Sub-agent 適用性
  <適合 / 部分適合 / 不適合> — <理由>

  ## 機密保護
  <通過 / 有風險> — <說明>

  ## 其他建議
  - <改善建議，若有>

  只做分析，不修改任何檔案。
```

Sub-agent 建議使用 sonnet model（需要邏輯判斷但不需跨檔架構理解）。

## Step 3: 呈現報告與詢問是否套用（主流程，需互動）

將 sub-agent 回傳的審查報告呈現給使用者。

若報告中有具體可執行的建議（如在 frontmatter 加上 model 指定），逐條詢問使用者是否要套用修改。

使用者確認後才修改 SKILL.md。

## 為什麼這樣拆分

這個 skill 本身就是 sub-agent 混合模式的示範：

| 步驟 | 執行方式 | 原因 |
|------|---------|------|
| Step 1: 確認目標 | 主流程 | 可能需要詢問使用者選哪個 skill |
| Step 2: 分析審查 | Sub-agent | 純檔案分析，不需對話脈絡 |
| Step 3: 套用修改 | 主流程 | 需要使用者確認才能修改 |

判斷原則：需要互動的留在主流程，純分析的委派給 sub-agent。

## Error Handling

- 找不到 SKILL.md → 告知使用者，詢問路徑
- Skill 內容為空 → 告知使用者，建議先用 skill-creator 建立內容
- Sub-agent 執行失敗 → 顯示錯誤，改為在主流程直接分析

## 不在範圍內

- 不建立新 skill（那是 skill-creator 的工作）
- 不修改 skill 的功能邏輯
- 不自動修改 SKILL.md（需使用者確認）
