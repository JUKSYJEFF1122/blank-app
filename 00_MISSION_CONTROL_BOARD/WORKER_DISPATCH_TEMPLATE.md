# WORKER DISPATCH TEMPLATE v1.0

**Mission Control**: JUKSY_MISSION_CONTROL_CLAUDE_CODE  
**Governance**: SP-21 v2.1 Source of Truth Guard  
**Task Lock**: CURRENT_TASK_LOCK (enabled)

---

## 強制繁體中文與治理規則前綴

所有派出的 worker / Cowork 任務必須在 prompt 最前面強制注入以下規則。此規則對所有 Claude Code worker 任務生效。

### 強制注入規則（MANDATORY PREFIX）

```
=== WORKER TASK INITIALIZATION ===

語言與治理規則（非可選）：
• 全程使用繁體中文回覆（Traditional Chinese only）
• 技術名詞可保留英文
• 禁止日文混用（no mid-conversation Japanese)
• 不得宣稱「完全繼承」「保證」或「已套用」未驗證的功能

Mission Control 統籌規則（非可選）：
• 所有 worker 任務必須由 JUKSY_MISSION_CONTROL_CLAUDE_CODE 統籌
• 任務執行必須受 CURRENT_TASK_LOCK 控制
• 不得要求 Jeff 搬運中間結果

回覆格式（FIXED FORMAT）：
STATUS: [current state]
DONE: [completed items]
NEXT: [next steps]
BLOCKER: [any blockers]

ccode 簡稱規則：
• Claude Code 統一簡稱為「ccode」
• 適用於所有文件、commit message、任務描述、狀態報告

不確定性處理：
• 遇到不確定性時，先做合理假設、記錄假設、繼續執行
• 只有 BLOCKED_NEED_DECISION 才通知 Jeff
• 停止宣稱未驗證的規則自動繼承

=== END WORKER TASK INITIALIZATION ===
```

---

## 使用方法

### 方法 1：直接注入 Prompt（推薦）

當派遣新 worker 任務時，**始終** 在用戶指令前加入上述「強制注入規則」區塊：

```
[WORKER TASK INITIALIZATION section]

[User's actual task description]
```

### 方法 2：環境變數傳遞

在 Mission Control dispatch 時設置環境變數：

```bash
export WORKER_PROMPT_PREFIX="[強制注入規則區塊]"
export JUKSY_MISSION_CONTROL="JUKSY_MISSION_CONTROL_CLAUDE_CODE"
export CURRENT_TASK_LOCK="enabled"
export GLOBAL_LANGUAGE="traditional-chinese"
export CLAUDE_CODE_SHORTNAME="ccode"
```

---

## 為什麼需要此 Fallback

| 原因                    | 說明                                          |
|-------------------------|-----------------------------------------------|
| 自動繼承未驗證            | worker 自動繼承 CLAUDE.md/settings.json 的機制未能驗證 |
| 可靠性要求              | 不依賴 ccode 的隱式繼承機制，改用顯式 prompt 注入      |
| 治理保障                | 確保所有 worker 任務一定受 SP-21 v2.1 控制            |
| 不請求 Jeff 驗測        | 不浪費時間追求自動繼承，直接補丁 template             |

---

## 已配置的項目

✅ CLAUDE.md - 專案層級規則文檔（/home/user/blank-app/CLAUDE.md）  
✅ .claude/settings.json - 環境變數配置（permissions、env）  
✅ WORKER_DISPATCH_TEMPLATE.md - worker 任務強制規則（此檔案）  
✅ 00_MISSION_CONTROL_BOARD - Mission Control 治理框架目錄

---

## 驗證檢查清單

- [x] 強制繁體中文規則已納入 prompt 前綴
- [x] 禁止日文混用規則已明確
- [x] ccode 簡稱規則已包含
- [x] SP-21 v2.1 Source of Truth Guard 已明確
- [x] JUKSY_MISSION_CONTROL_CLAUDE_CODE 統籌規則已明確
- [x] CURRENT_TASK_LOCK 控制已明確
- [x] 不得要求 Jeff 搬運中間結果已明確
- [x] STATUS/DONE/NEXT/BLOCKER 固定格式已明確

---

## 最終狀態

**結論**：FAILED_NEEDS_FIX_PATCHED

自動繼承機制未驗證，但 fallback 補丁已完成。未來所有 worker 任務將在 dispatch 時強制注入繁體中文與 SP-21 v2.1 治理規則，確保合規。

**生效日期**：2026-05-27  
**Mission Control**：JUKSY_MISSION_CONTROL_CLAUDE_CODE
