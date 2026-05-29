# Windows TaskRoom 同步包說明

**Package**: WINDOWS_TASKROOM_SYNC_PACKAGE_20260528_094629  
**Created**: 2026-05-28  
**Governance**: SP-21 v2.1 Source of Truth Guard  
**Mission Control**: JUKSY_MISSION_CONTROL_CLAUDE_CODE

---

## 為什麼需要這個同步包？

### 技術背景

ccode（Claude Code）的雲端環境運行在 **Linux 遠端執行環境**，無法直接訪問 Windows 本機的文件系統：
- ❌ 無法讀寫 `C:\Users\Jeff\Desktop\TaskRoom_Skill_Packs\`
- ❌ 無法執行 Windows PowerShell 命令
- ❌ 無法檢查 Windows 本機目錄結構

### 解決方案

將所有同步邏輯封裝為一個 **可執行的本機 worker 包**，交給 Windows 本機 agent（Lobster）執行：
1. 雲端 ccode 生成完整的同步邏輯和驗證規則
2. 本機 worker 自動執行 PowerShell 腳本
3. 無需 Jeff 手動參與
4. 本機 worker 自動驗收並報告最終結果

---

## 同步包包含的內容

| 檔案 | 用途 | 執行者 |
|------|------|--------|
| `LOCAL_WORKER_EXECUTE_PROMPT.md` | 給本機 agent 的執行指令 | Windows Lobster Agent |
| `sync_taskroom_mobile_continuity_patch.ps1` | 核心同步腳本（自動執行） | PowerShell (Windows) |
| `SYNC_PACKAGE_README.md` | 本檔，背景說明 | 參考用 |
| `EXPECTED_FINAL_VERDICT.md` | 驗收標準定義 | 參考用 |

---

## 同步的目標內容

此包會同步以下 ccode repo 的最新成果到 Windows TaskRoom：

### 來源 Commits
- `e57dc44`: DISPATCH_TEMPLATE_ROUTED_AND_PATCHED（派工入口強制規則）
- `d627116`: MOBILE_CONTINUITY_README.md（手機端接續指南）

### 同步檔案（7 個）
```
00_MISSION_CONTROL_BOARD/
  ├─ WORKER_DISPATCH_TEMPLATE.md      派工範本強制規則
  ├─ EVIDENCE_LOG.md                  決策過程記錄
  └─ FINAL_VERIFICATION.md            最終驗收報告

00_AGENT_BOOTSTRAP.md                 Agent 派工流程定義
00_CROSS_AGENT_STARTER_PROMPTS_v2.md  跨 Agent 派工規則
00_Skill_Pack_Index.md                Skill 派工強制規則
MOBILE_CONTINUITY_README.md           手機端接續指南
```

### 驗證的必要規則（8 項）
1. ✅ WORKER_DISPATCH_TEMPLATE 強制套用
2. ✅ 繁體中文規則
3. ✅ 禁止日文混用
4. ✅ SP-21 v2.1 Source of Truth Guard
5. ✅ JUKSY_MISSION_CONTROL_CLAUDE_CODE
6. ✅ CURRENT_TASK_LOCK
7. ✅ 不得要求 Jeff 搬運中間結果
8. ✅ STATUS / DONE / NEXT / BLOCKER 格式

---

## 執行流程

### 本機 Lobster Agent 應執行的步驟

1. **讀取本同步包**
   - 位置：`TaskRoom_Outputs/WINDOWS_TASKROOM_SYNC_PACKAGE_20260528_094629/`

2. **執行 PowerShell 腳本**
   ```powershell
   .\sync_taskroom_mobile_continuity_patch.ps1
   ```

3. **腳本自動執行**
   - 檢查 TaskRoom 目錄是否存在
   - 建立備份（若目錄已存在）
   - 複製 7 個同步檔案
   - 驗證 8 項規則
   - 產出驗證報告

4. **驗收報告自動生成**
   - 位置：`C:\Users\Jeff\Desktop\TaskRoom_Skill_Packs\TaskRoom_Outputs\WINDOWS_TASKROOM_SYNC_VERIFICATION_<timestamp>.md`
   - 包含修改清單、備份位置、規則驗證結果

5. **回報最終驗收結果**（三選一）
   - `WINDOWS_TASKROOM_SYNCED_AND_VERIFIED` ← 成功
   - `WINDOWS_TASKROOM_NOT_FOUND` ← 目錄不存在
   - `SYNC_FAILED_NEEDS_FIX` ← 同步失敗

---

## 與傳統 git clone 的區別

| 方式 | 流程 | 需要 Jeff | 適用情況 |
|------|------|----------|---------|
| **傳統 git clone** | 手動 `git clone`、複製文件 | ✅ 需要 | 一般代碼開發 |
| **本同步包** | 本機 agent 自動執行腳本 | ❌ 不需要 | 跨平台治理同步 |

---

## 本機 Worker 的責任

### 必須做
✅ 自動執行 PowerShell 腳本  
✅ 自動備份舊檔案  
✅ 自動複製新檔案  
✅ 自動驗證規則  
✅ 自動產出驗證報告  
✅ 自動回報三種驗收結果其一  

### 禁止做
❌ 要求 Jeff 手動複製檔案  
❌ 要求 Jeff 手動決策「覆蓋或 merge」  
❌ 要求 Jeff 搬運中間結果  
❌ 停下來等待 Jeff 決策  
❌ 產出驗證報告後仍要求 Jeff 判斷成功還是失敗  

---

## 失敗時的處理

若同步失敗（如 TaskRoom 目錄不存在、權限錯誤等），本機 worker 應：

1. **記錄詳細錯誤**
   - 在驗證報告中說明 root cause
   - 例：`ERROR: C:\Users\Jeff\Desktop\TaskRoom_Skill_Packs\ does not exist`

2. **回報適當的 FINAL_VERDICT**
   - 若目錄不存在 → `WINDOWS_TASKROOM_NOT_FOUND`
   - 若其他錯誤 → `SYNC_FAILED_NEEDS_FIX`

3. **提供修復建議**
   - 在驗證報告中說明如何修復

---

## 成功標準

同步包執行成功需要滿足：

| 條件 | 檢查項 |
|------|--------|
| 備份完成 | `99_Archive\backup_before_mobile_continuity_patch_*` 目錄存在 |
| 檔案複製 | 7 個同步檔案全部成功複製 |
| 規則驗證 | 8 項規則全部驗證通過 |
| 報告產出 | `WINDOWS_TASKROOM_SYNC_VERIFICATION_*.md` 存在 |
| 最終狀態 | `FINAL_VERDICT: WINDOWS_TASKROOM_SYNCED_AND_VERIFIED` |

---

## 關鍵資訊

- **同步目標**：`C:\Users\Jeff\Desktop\TaskRoom_Skill_Packs\`
- **備份位置**：`C:\Users\Jeff\Desktop\TaskRoom_Skill_Packs\99_Archive\backup_before_mobile_continuity_patch_<timestamp>\`
- **驗證報告**：`C:\Users\Jeff\Desktop\TaskRoom_Skill_Packs\TaskRoom_Outputs\WINDOWS_TASKROOM_SYNC_VERIFICATION_<timestamp>.md`
- **執行工具**：PowerShell（Windows 內建）
- **所需權限**：管理員（建議）或具有 TaskRoom 目錄的寫權限

---

## 聯繫點

本同步包由雲端 ccode 生成。若有問題：
- **本機 Agent**：檢查 PowerShell 腳本的執行日誌
- **驗證報告**：包含詳細的錯誤信息和修復建議
- **治理框架**：SP-21 v2.1 Source of Truth Guard

---

**Governance**: SP-21 v2.1 Source of Truth Guard  
**Language**: 繁體中文（禁止日文混用）  
**Mission Control**: JUKSY_MISSION_CONTROL_CLAUDE_CODE  
**Lock**: CURRENT_TASK_LOCK enabled
