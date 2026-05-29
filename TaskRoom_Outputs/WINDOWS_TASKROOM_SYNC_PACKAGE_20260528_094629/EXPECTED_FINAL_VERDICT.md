# 預期驗收結果 / Expected Final Verdict

**Package**: WINDOWS_TASKROOM_SYNC_PACKAGE_20260528_094629  
**Governance**: SP-21 v2.1 Source of Truth Guard  
**Mission Control**: JUKSY_MISSION_CONTROL_CLAUDE_CODE

---

## 本機 Worker 必須回報的三種驗收結果

本機 agent（Lobster）執行完 PowerShell 腳本後，**必須**回報以下三種其一，**不允許其他回答方式**。

---

## 驗收結果定義

### 1️⃣ `WINDOWS_TASKROOM_SYNCED_AND_VERIFIED`

**適用情況**：同步完全成功

**必要條件**（全部滿足）：
- ✅ TaskRoom 根目錄存在：`C:\Users\Jeff\Desktop\TaskRoom_Skill_Packs\`
- ✅ 7 個同步檔案全部成功複製
- ✅ 備份目錄建立：`99_Archive\backup_before_mobile_continuity_patch_<timestamp>\`
- ✅ 8 項必要規則全部驗證通過：
  1. WORKER_DISPATCH_TEMPLATE 強制套用
  2. 繁體中文規則
  3. 禁止日文混用
  4. SP-21 v2.1 Source of Truth Guard
  5. JUKSY_MISSION_CONTROL_CLAUDE_CODE
  6. CURRENT_TASK_LOCK
  7. 不得要求 Jeff 搬運中間結果
  8. STATUS / DONE / NEXT / BLOCKER 格式
- ✅ 驗證報告成功產出

**回報格式**：
```
STATUS: 同步完成
DONE:
  ✓ TaskRoom 目錄驗證
  ✓ 備份建立完成
  ✓ 7 個檔案全部複製成功
  ✓ 8 項規則全部驗證通過
  ✓ 驗證報告產出
SYNC_PATH: C:\Users\Jeff\Desktop\TaskRoom_Skill_Packs\
VERIFICATION_REPORT: C:\Users\Jeff\Desktop\TaskRoom_Skill_Packs\TaskRoom_Outputs\WINDOWS_TASKROOM_SYNC_VERIFICATION_<timestamp>.md
FINAL_VERDICT: WINDOWS_TASKROOM_SYNCED_AND_VERIFIED
```

---

### 2️⃣ `WINDOWS_TASKROOM_NOT_FOUND`

**適用情況**：TaskRoom 目錄不存在，且無法自動建立

**可能的原因**：
- ❌ `C:\Users\Jeff\Desktop\TaskRoom_Skill_Packs\` 路徑不存在
- ❌ 權限不足，無法建立目錄
- ❌ 磁盤空間不足
- ❌ 路徑格式錯誤或無效

**本機 Agent 應執行**：
1. 嘗試建立目錄結構
2. 若建立失敗，記錄詳細的錯誤信息
3. 產出驗證報告（說明為何無法建立）
4. 回報本驗收結果

**回報格式**：
```
STATUS: TaskRoom 目錄不存在
DONE:
  ✓ 嘗試定位 TaskRoom 目錄
  ✗ 目錄不存在：C:\Users\Jeff\Desktop\TaskRoom_Skill_Packs\
  ✗ 嘗試自動建立失敗（原因：<詳細說明>）
SYNC_PATH: N/A
VERIFICATION_REPORT: <驗證報告位置>
ERROR_DETAILS: <root cause 分析>
SUGGESTED_FIX: <修復建議，例：手動建立目錄或檢查路徑>
FINAL_VERDICT: WINDOWS_TASKROOM_NOT_FOUND
```

---

### 3️⃣ `SYNC_FAILED_NEEDS_FIX`

**適用情況**：同步過程中發生錯誤，需要修復

**可能的原因**：
- ❌ 檔案複製失敗（如權限不足、來源檔案不存在）
- ❌ 規則驗證失敗
- ❌ 驗證報告產出失敗
- ❌ 其他非預期的錯誤

**本機 Agent 應執行**：
1. 記錄詳細的錯誤信息（包括堆棧跟蹤或 PowerShell 錯誤）
2. 分析 root cause
3. 產出驗證報告（包含所有失敗信息）
4. 提供修復建議
5. 回報本驗收結果

**回報格式**：
```
STATUS: 同步失敗，需修復
DONE:
  ✓ TaskRoom 目錄驗證：成功
  ✓ 備份建立：成功
  ✓ 檔案複製：部分失敗（共 <N> 個檔案失敗）
  ✗ 規則驗證：失敗
  ✗ 驗證報告：產出失敗

FAILED_FILES:
  - <檔案 1>: <失敗原因>
  - <檔案 2>: <失敗原因>

VERIFICATION_REPORT: <驗證報告位置>
ERROR_ANALYSIS: <詳細的根本原因分析>
SUGGESTED_FIX:
  1. <修復步驟 1>
  2. <修復步驟 2>
  3. <修復步驟 3>

FINAL_VERDICT: SYNC_FAILED_NEEDS_FIX
```

---

## 驗收過程中禁止的行為

❌ **禁止**回報「等待 Jeff 決策」  
❌ **禁止**要求「Jeff 手動複製檔案」  
❌ **禁止**要求「Jeff 確認是否覆蓋」  
❌ **禁止**回報「同步包含不清楚的內容」  
❌ **禁止**停留在中間狀態而不做最終判決  
❌ **禁止**回報除上述三種外的驗收結果  

---

## 驗收標準矩陣

| 條件 | 結果 1 | 結果 2 | 結果 3 |
|------|--------|--------|--------|
| TaskRoom 目錄存在 | ✅ YES | ❌ NO | ✅ YES |
| 備份成功建立 | ✅ YES | N/A | ✅ YES |
| 7 個檔案全部複製 | ✅ YES | N/A | ❌ PARTIAL |
| 8 項規則全部驗證 | ✅ YES | N/A | ❌ PARTIAL |
| 驗證報告成功產出 | ✅ YES | ✅ YES | ✅ YES |
| **最終驗收** | **SYNCED_AND_VERIFIED** | **NOT_FOUND** | **FAILED_NEEDS_FIX** |

---

## 驗證報告內容期望

無論回報哪種驗收結果，驗證報告都應包含：

```markdown
# Windows TaskRoom 同步驗證報告

**生成時間**: <時間戳>
**Package**: WINDOWS_TASKROOM_SYNC_PACKAGE_20260528_094629
**Timestamp**: <執行時間戳>

---

## 同步狀態
- **狀態**: <CREATED / EXISTS / FAILED>
- **TaskRoom Root**: C:\Users\Jeff\Desktop\TaskRoom_Skill_Packs\
- **備份位置**: <備份目錄路徑>

---

## 修改檔案清單
| 檔案 | 狀態 |
| --- | --- |
| <檔案 1> | <SYNCED / FAILED / SKIPPED> |
| <檔案 2> | <SYNCED / FAILED / SKIPPED> |
...

---

## 規則驗證結果
- ✅ / ❌ WORKER_DISPATCH_TEMPLATE: <VERIFIED / NOT_FOUND>
- ✅ / ❌ 繁體中文: <VERIFIED / NOT_FOUND>
...

---

## 最終驗收結果
**FINAL_VERDICT**: <三選一>

---

## 錯誤分析（若有失敗）
<詳細說明>

## 修復建議（若有失敗）
<步驟清單>
```

---

## 本機 Agent 的回報責任

本機 Lobster Agent 執行後應確保：

1. ✅ 清楚說明哪些檔案成功、哪些失敗
2. ✅ 提供詳細的錯誤分析（不只是「複製失敗」）
3. ✅ 提供可執行的修復建議
4. ✅ 回報確切的三種驗收結果其一
5. ✅ 驗證報告包含上述所有信息

---

## 何時聯繫 Jeff

本 worker 任務**禁止**要求 Jeff 參與決策。僅在下列情況下「被動」通知 Jeff（由驗證報告自動完成）：
- 驗收完成後的驗證報告中記錄結果
- 若驗收為 `NOT_FOUND` 或 `FAILED_NEEDS_FIX`，報告中說明原因

**絕不能**：
- 停下來問「Jeff 你現在在嗎？」
- 要求「Jeff 幫我檢查路徑」
- 等待「Jeff 決定是否覆蓋」

---

## 成功案例

✅ **範例：完全成功**
```
STATUS: 同步完成
DONE:
  ✓ TaskRoom 目錄驗證
  ✓ 備份建立完成
  ✓ 7 個檔案全部複製成功
  ✓ 8 項規則全部驗證通過
  ✓ 驗證報告產出完成
SYNC_PATH: C:\Users\Jeff\Desktop\TaskRoom_Skill_Packs\
VERIFICATION_REPORT: C:\Users\Jeff\Desktop\TaskRoom_Skill_Packs\TaskRoom_Outputs\WINDOWS_TASKROOM_SYNC_VERIFICATION_20260528_150000.md
FINAL_VERDICT: WINDOWS_TASKROOM_SYNCED_AND_VERIFIED
```

---

## 失敗案例

❌ **範例：目錄不存在**
```
STATUS: TaskRoom 目錄不存在
DONE:
  ✓ 嘗試定位 TaskRoom 目錄
  ✗ 目錄不存在：C:\Users\Jeff\Desktop\TaskRoom_Skill_Packs\
  ✗ 嘗試自動建立失敗（權限不足）
SYNC_PATH: N/A
ERROR_DETAILS: Access denied when creating directory
SUGGESTED_FIX: Please create C:\Users\Jeff\Desktop\TaskRoom_Skill_Packs\ manually or check permissions
FINAL_VERDICT: WINDOWS_TASKROOM_NOT_FOUND
```

---

**Governance**: SP-21 v2.1 Source of Truth Guard  
**Language**: 繁體中文（禁止日文混用）  
**Mission Control**: JUKSY_MISSION_CONTROL_CLAUDE_CODE  
**Lock**: CURRENT_TASK_LOCK enabled
