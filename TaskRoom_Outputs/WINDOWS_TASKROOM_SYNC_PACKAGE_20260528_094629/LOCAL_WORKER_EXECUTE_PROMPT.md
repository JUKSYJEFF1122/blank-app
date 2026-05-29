# LOCAL_WORKER_EXECUTE_PROMPT

## WORKER TASK INITIALIZATION
**Mission Control**: JUKSY_MISSION_CONTROL_CLAUDE_CODE  
**Task Lock**: CURRENT_TASK_LOCK enabled  
**Governance**: SP-21 v2.1 Source of Truth Guard  
**Language**: 繁體中文（禁止日文混用）  

---

## 任務：Windows TaskRoom 同步執行

**目標**：將 ccode 雲端 repo 的最新成果（commit e57dc44 + d627116）同步到本機 Windows TaskRoom 正式目錄

**執行者**：Lobster / Windows 本機 agent  
**執行環境**：Windows PowerShell（系統管理員）  
**不需 Jeff 參與**：此 worker 任務需要本機 agent **完全自動執行**，禁止要求 Jeff 手動複製或決策

---

## 執行流程（一鍵自動化）

### 步驟 1：讀取同步包
本同步包位置：
```
TaskRoom_Outputs/WINDOWS_TASKROOM_SYNC_PACKAGE_20260528_094629/
```

包含文件：
- `LOCAL_WORKER_EXECUTE_PROMPT.md`（本檔）
- `sync_taskroom_mobile_continuity_patch.ps1`（主執行腳本）
- `SYNC_PACKAGE_README.md`（背景說明）
- `EXPECTED_FINAL_VERDICT.md`（驗收標準）

### 步驟 2：執行 PowerShell 腳本
在 Windows 本機執行：
```powershell
# 以系統管理員身份開啟 PowerShell，然後執行：
cd C:\path\to\ccode\repo\TaskRoom_Outputs\WINDOWS_TASKROOM_SYNC_PACKAGE_20260528_094629\
Set-ExecutionPolicy -ExecutionPolicy Bypass -Scope Process -Force
.\sync_taskroom_mobile_continuity_patch.ps1
```

### 步驟 3：驗證結果
腳本執行完畢後，自動產生驗證報告：
```
C:\Users\Jeff\Desktop\TaskRoom_Skill_Packs\TaskRoom_Outputs\WINDOWS_TASKROOM_SYNC_VERIFICATION_<timestamp>.md
```

檢查報告中的最終狀態。

### 步驟 4：回報驗收
根據驗證報告的結果，回報以下三種其一：
- `WINDOWS_TASKROOM_SYNCED_AND_VERIFIED` ← 同步成功
- `WINDOWS_TASKROOM_NOT_FOUND` ← TaskRoom 目錄不存在
- `SYNC_FAILED_NEEDS_FIX` ← 同步失敗，需修復

---

## 腳本自動執行的主要功能

1. ✅ 檢查 `C:\Users\Jeff\Desktop\TaskRoom_Skill_Packs\` 是否存在
2. ✅ 若存在，自動建立備份到 `99_Archive\backup_before_mobile_continuity_patch_<timestamp>\`
3. ✅ 從本同步包複製以下檔案到 TaskRoom root：
   - `00_MISSION_CONTROL_BOARD\WORKER_DISPATCH_TEMPLATE.md`
   - `00_MISSION_CONTROL_BOARD\EVIDENCE_LOG.md`
   - `00_MISSION_CONTROL_BOARD\FINAL_VERIFICATION.md`
   - `00_AGENT_BOOTSTRAP.md`
   - `00_CROSS_AGENT_STARTER_PROMPTS_v2.md`
   - `00_Skill_Pack_Index.md`
   - `MOBILE_CONTINUITY_README.md`
4. ✅ 若 root files 已存在，自動 merge 缺漏區塊（不粗暴覆蓋）
5. ✅ 驗證新同步內容是否包含所有必要規則：
   - WORKER_DISPATCH_TEMPLATE 強制套用
   - 繁體中文規則
   - 禁止日文混用
   - SP-21 v2.1 Source of Truth Guard
   - JUKSY_MISSION_CONTROL_CLAUDE_CODE
   - CURRENT_TASK_LOCK
   - 不得要求 Jeff 搬運中間結果
   - STATUS / DONE / NEXT / BLOCKER 格式
6. ✅ 輸出詳細驗證報告（包含修改檔案清單、備份路徑、grep 驗證結果）

---

## 關鍵規則（本 worker 必須遵守）

- 禁止要求 Jeff 手動決策「應該同步哪些檔案」
- 禁止要求 Jeff 手動複製檔案
- 禁止要求 Jeff 搬運中間結果
- 腳本執行中遇到問題，必須在驗證報告中說明 root cause，而不是停下來等待
- 若 TaskRoom 目錄不存在，腳本應自動建立該目錄結構（或清楚報告為何無法建立）
- 最終必須回報三種驗收結果其一，不允許「等待 Jeff 決策」

---

## 成功指標

| 檢查項 | 合格標準 |
|--------|---------|
| 備份完成 | `99_Archive\backup_before_mobile_continuity_patch_*` 目錄存在 |
| 檔案同步 | 7 個同步檔案全部複製到 TaskRoom root |
| 規則驗證 | grep 搜尋確認 8 項規則全部存在 |
| 驗證報告 | `WINDOWS_TASKROOM_SYNC_VERIFICATION_*.md` 產出 |
| 最終狀態 | 回報 `WINDOWS_TASKROOM_SYNCED_AND_VERIFIED` |

---

## 失敗處理

若任何步驟失敗（如目錄不存在、檔案複製失敗、權限錯誤等），腳本應：
1. 記錄詳細的錯誤信息
2. 回報適當的 FINAL_VERDICT（`WINDOWS_TASKROOM_NOT_FOUND` 或 `SYNC_FAILED_NEEDS_FIX`）
3. 在驗證報告中提供 root cause 分析和修復建議

---

## 不需要 Jeff 做什麼

❌ 不需要手動複製檔案  
❌ 不需要手動決策備份策略  
❌ 不需要手動驗證同步結果  
❌ 不需要搬運中間結果  
❌ 不需要決定「是覆蓋還是 merge」  

---

## 執行後預期輸出

```
STATUS: Windows TaskRoom 同步中 → 同步完成
DONE:
  ✓ 備份舊檔案
  ✓ 複製 7 個同步檔案
  ✓ 驗證 8 項規則
  ✓ 產出驗證報告
FINAL_VERDICT: WINDOWS_TASKROOM_SYNCED_AND_VERIFIED
VERIFICATION_REPORT: C:\Users\Jeff\Desktop\TaskRoom_Skill_Packs\TaskRoom_Outputs\WINDOWS_TASKROOM_SYNC_VERIFICATION_*.md
```

---

**回傳時請使用固定格式**：
```
STATUS: [current state]
DONE: [completed items]
MOBILE_VISIBLE: [確認手機端可見的 commit]
NOT_VISIBLE: [確認手機端不可見的部分]
SYNC_PATH: [同步到的正式路徑]
VERIFICATION_REPORT: [驗證報告完整路徑]
FINAL_VERDICT: [WINDOWS_TASKROOM_SYNCED_AND_VERIFIED / WINDOWS_TASKROOM_NOT_FOUND / SYNC_FAILED_NEEDS_FIX]
```

---

**Mission Control Signature**: JUKSY_MISSION_CONTROL_CLAUDE_CODE  
**Governance**: SP-21 v2.1 Source of Truth Guard  
**Created**: 2026-05-28  
**Package**: WINDOWS_TASKROOM_SYNC_PACKAGE_20260528_094629
