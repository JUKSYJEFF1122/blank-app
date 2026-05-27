# EVIDENCE LOG - Worker Dispatch Template Fallback Patch

**Date**: 2026-05-27  
**Mission Control**: JUKSY_MISSION_CONTROL_CLAUDE_CODE  
**Decision**: FAILED_NEEDS_FIX_PATCHED  
**Branch**: claude/mobile-desktop-task-continuity-5BYId

---

## 背景

### 原始目標
實現繁體中文語言配置與 SP-21 v2.1 Source of Truth Guard 治理框架，確保 ccode desktop ↔ mobile 跨設備任務室同步時，所有 worker/Cowork 任務預設使用繁體中文。

### 已配置的項目
1. ✅ **CLAUDE.md** - 專案層級規則文檔
   - 包含 GLOBAL_LANGUAGE_RULE（完整 19 行）
   - 包含 SP-21 v2.1 Source of Truth Guard
   - 包含 ccode 簡稱規則
   - 檔案位置：/home/user/blank-app/CLAUDE.md
   - 狀態：已建立、已提交

2. ✅ **.claude/settings.json** - 項目級 ccode 配置
   - 官方支援欄位已驗證：`$schema`、`permissions`、`env`
   - 環境變數已配置：JUKSY_MISSION_CONTROL、CURRENT_TASK_LOCK、GLOBAL_LANGUAGE、CLAUDE_CODE_SHORTNAME
   - 檔案位置：/home/user/blank-app/.claude/settings.json
   - 狀態：已建立、已提交、官方欄位已驗證

### 未驗證的項目
❌ **worker 自動繼承** - ccode 未執行「最小 worker/Cowork 語言繼承實測」
- CLAUDE.md 和 settings.json 的存在不保證新 worker 任務自動遵循規則
- ccode 連續重複輸出同一份補驗收報告，未執行實際測試
- 驗證需要：建立新測試 worker 任務，檢查其回覆是否預設使用繁體中文

---

## 決策轉變

### 為什麼改策略

| 原策略 | 現策略 | 原因 |
|-------|-------|------|
| 依賴 ccode 自動繼承 CLAUDE.md | 在 dispatch template 強制注入規則 | 自動繼承機制無法驗證，浪費時間 |
| 追求驗證並升級到 PASS_VERIFIED | 接受 FAILED_NEEDS_FIX_PATCHED | 更務實、更可靠的治理方案 |
| 要求 ccode 執行 worker 實測 | 直接補丁 dispatch template | 不依賴 ccode 的自動機制 |

### 關鍵認知

> 自動繼承未驗證 → 不再浪費時間 → 直接改 dispatch template → 每次派工 prompt 強制帶規則

---

## 補丁實作

### 新增檔案

✅ **00_MISSION_CONTROL_BOARD/WORKER_DISPATCH_TEMPLATE.md**
- 位置：/home/user/blank-app/00_MISSION_CONTROL_BOARD/WORKER_DISPATCH_TEMPLATE.md
- 用途：定義所有 worker 任務的強制前綴規則
- 內容：WORKER TASK INITIALIZATION 區塊，包含
  - 強制繁體中文規則
  - 禁止日文混用
  - ccode 簡稱規則
  - SP-21 v2.1 Source of Truth Guard
  - JUKSY_MISSION_CONTROL_CLAUDE_CODE 統籌規則
  - CURRENT_TASK_LOCK 控制
  - 固定回覆格式（STATUS/DONE/NEXT/BLOCKER）
  - 不得要求 Jeff 搬運中間結果

### 補丁生效方式

方法 1：**Prompt 直接注入**（推薦）
```
[WORKER_DISPATCH_TEMPLATE 規則區塊]

[實際任務描述]
```

方法 2：**環境變數傳遞**
```bash
export WORKER_PROMPT_PREFIX="[規則區塊]"
```

---

## 驗證檢查

### 官方支援欄位驗證

| 欄位 | 位置 | 官方支援 | 實際驗證 | 狀態 |
|-----|------|--------|--------|------|
| `$schema` | .claude/settings.json | ✅ | ✅ | VERIFIED |
| `permissions` | .claude/settings.json | ✅ | ✅ | VERIFIED |
| `env` | .claude/settings.json | ✅ | ✅ | VERIFIED |
| GLOBAL_LANGUAGE_RULE | CLAUDE.md | 📄 | ✅ | DOCUMENTED |
| ccode 簡稱規則 | CLAUDE.md | 📄 | ✅ | DOCUMENTED |
| worker dispatch 強制規則 | WORKER_DISPATCH_TEMPLATE.md | 📄 | ✅ | IMPLEMENTED |

### 無法驗證的項目

- ❓ 新建 worker 任務是否自動繼承 CLAUDE.md（**因 ccode 不執行測試**）
- ❓ Cowork 任務室是否遵循相同繼承機制（**因 ccode 不執行測試**）

---

## 最終狀態

### 結論

**FAILED_NEEDS_FIX_PATCHED**

自動繼承機制未驗證 → 但 fallback 補丁已完成 → 未來 worker prompt 會被強制插入繁體中文與 SP-21 v2.1 治理規則

### 已完成工作清單

| 項目 | 狀態 | 檔案 |
|-----|------|------|
| CLAUDE.md 專案規則 | ✅ 已配置 | /home/user/blank-app/CLAUDE.md |
| .claude/settings.json env | ✅ 已配置 | /home/user/blank-app/.claude/settings.json |
| ccode 簡稱規則 | ✅ 已配置 | CLAUDE.md + WORKER_DISPATCH_TEMPLATE.md |
| worker dispatch fallback | ✅ 已完成 | /home/user/blank-app/00_MISSION_CONTROL_BOARD/WORKER_DISPATCH_TEMPLATE.md |
| worker 自動繼承測試 | ❌ 未執行 | N/A |
| Cowork 自動繼承測試 | ❌ 未執行 | N/A |

### 治理效力

即使 worker 未能自動繼承 CLAUDE.md，所有透過此 dispatch template 派遣的任務都會被強制注入：

✅ 繁體中文規則  
✅ SP-21 v2.1 Source of Truth Guard  
✅ JUKSY_MISSION_CONTROL_CLAUDE_CODE 統籌  
✅ CURRENT_TASK_LOCK 控制  
✅ ccode 簡稱規則  
✅ 固定回覆格式  

---

## 日誌簽名

**Decision Maker**: Jeff (jeff@juksy.com)  
**Implementation Date**: 2026-05-27  
**Branch**: claude/mobile-desktop-task-continuity-5BYId  
**Mission Control**: JUKSY_MISSION_CONTROL_CLAUDE_CODE  
**Final Verdict**: FAILED_NEEDS_FIX_PATCHED ✅ COMPLETE
