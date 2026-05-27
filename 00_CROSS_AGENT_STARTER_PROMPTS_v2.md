# Cross-Agent Starter Prompts v2.0

**Mission Control**: JUKSY_MISSION_CONTROL_CLAUDE_CODE  
**Governance**: SP-21 v2.1 Source of Truth Guard  
**Reference**: 00_AGENT_BOOTSTRAP.md  
**Date**: 2026-05-27

---

## 派工規則前置聲明

所有跨 agent 派工必須遵循以下規則。不得直接派發裸 prompt。

### 強制派工入口

```
ALL CROSS-AGENT DISPATCH ROUTES MUST:

1. 優先套用 00_MISSION_CONTROL_BOARD/WORKER_DISPATCH_TEMPLATE.md
2. 在 prompt 最前面注入「WORKER TASK INITIALIZATION」區塊
3. 確保包含以下規則：
   - 繁體中文規則
   - 禁止日文混用
   - SP-21 v2.1 Source of Truth Guard
   - JUKSY_MISSION_CONTROL_CLAUDE_CODE 統籍
   - CURRENT_TASK_LOCK 控制
   - 不得要求 Jeff 搬運中間結果
   - STATUS / DONE / NEXT / BLOCKER 固定格式

4. 狀態門控（Status Gates）：
   - QUEUED → READY: 確認已套用 WORKER_DISPATCH_TEMPLATE
   - READY → DISPATCHED: 確認所有規則項目已完整
```

---

## Starter Prompt 樣板

### 樣板 1: Worker Task Dispatch

```
[載入 WORKER_DISPATCH_TEMPLATE.md]

=== WORKER TASK INITIALIZATION ===

語言與治理規則（非可選）：
• 全程使用繁體中文回覆（Traditional Chinese only）
• 技術名詞可保留英文
• 禁止日文混用（no mid-conversation Japanese）
• 不得宣稱「完全繼承」「保證」或「已套用」未驗證的功能

Mission Control 統籍規則（非可選）：
• 所有 worker 任務必須由 JUKSY_MISSION_CONTROL_CLAUDE_CODE 統籍
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

[實際任務描述]
```

### 樣板 2: Cowork Collaboration Task

```
[載入 WORKER_DISPATCH_TEMPLATE.md]

=== WORKER TASK INITIALIZATION ===

[同上]

=== END WORKER TASK INITIALIZATION ===

[協作任務描述]
```

### 樣板 3: Delegated Task

```
[載入 WORKER_DISPATCH_TEMPLATE.md]

=== WORKER TASK INITIALIZATION ===

[同上]

=== END WORKER TASK INITIALIZATION ===

[代理任務描述]
```

---

## 派工路由映射表

### 派工來源 → 派工目標 → 檢查點

| 派工來源 | 派工目標 | 強制範本 | 檢查點 |
|--------|--------|--------|--------|
| Mission Control | worker-1 | WORKER_DISPATCH_TEMPLATE.md | READY → DISPATCHED |
| Mission Control | Cowork-team-1 | WORKER_DISPATCH_TEMPLATE.md | READY → DISPATCHED |
| ccode Desktop | worker-mobile | WORKER_DISPATCH_TEMPLATE.md | READY → DISPATCHED |
| Skill | delegated-agent | WORKER_DISPATCH_TEMPLATE.md | READY → DISPATCHED |

---

## 派工檢查流程（派工前必須通過）

### Gate 1: 派工前驗證

```
檢查項目：
✓ 是否使用了 WORKER_DISPATCH_TEMPLATE.md？
✓ 是否包含「WORKER TASK INITIALIZATION」區塊？
✓ 是否注入在 prompt 最前面？

若任何項目為 NO，不得進入 READY 狀態。
```

### Gate 2: 派工規則驗證

```
檢查項目：
✓ 繁體中文規則是否完整？
✓ 禁止日文混用是否明確？
✓ SP-21 v2.1 是否明確？
✓ JUKSY_MISSION_CONTROL_CLAUDE_CODE 是否明確？
✓ CURRENT_TASK_LOCK 是否啟用？
✓ 不得要求 Jeff 搬運是否明確？
✓ STATUS/DONE/NEXT/BLOCKER 格式是否說明？

若任何項目缺失，不得派發至 DISPATCHED 狀態。
退回 QUEUED，要求補完。
```

### Gate 3: 派發許可

```
前置條件：
✓ 已通過 Gate 1 和 Gate 2
✓ 狀態已轉移至 READY
✓ 所有檢查項目已勾選

准許狀態：READY → DISPATCHED
操作：派發任務至 worker
記錄：記錄派工時間、檢查結果、版本號
```

---

## 常見派工錯誤與修正

### 錯誤 1: 直接派發裸 prompt（未套用範本）

```
❌ 錯誤做法：
"親愛的 worker，請幫我做 X 任務"

✅ 正確做法：
[WORKER_DISPATCH_TEMPLATE 規則區塊]
親愛的 worker，請幫我做 X 任務
```

### 錯誤 2: 遺漏繁體中文規則

```
❌ 錯誤做法：
派工 prompt 中無繁體中文規則

✅ 正確做法：
在「WORKER TASK INITIALIZATION」區塊中明確：
「全程使用繁體中文回覆」
```

### 錯誤 3: 狀態未經檢查就派發

```
❌ 錯誤做法：
直接從 QUEUED → DISPATCHED

✅ 正確做法：
QUEUED → 檢查 Gate 1 → READY → 檢查 Gate 2 → DISPATCHED
```

---

## 監控與審計

### 派工記錄格式

```
派工紀錄（Dispatch Log Entry）

日期: 2026-05-27
派工 ID: DISPATCH_001
派工源: Mission Control
派工目標: worker-1
任務類型: ccode-mobile-task
範本版本: WORKER_DISPATCH_TEMPLATE v1.0
Gate 1 驗證: ✓ PASS
Gate 2 驗證: ✓ PASS
狀態轉移: READY → DISPATCHED
派工時間: 11:59:00
備註: 跨設備任務同步
```

### 派工審計檢查點

| 檢查點 | 頻率 | 責任單位 |
|--------|------|--------|
| Gate 1 驗證 | 派工前 | Mission Control |
| Gate 2 驗證 | 派工前 | Bootstrap Agent |
| 派工記錄 | 派工時 | Mission Control |
| 執行監控 | 派發後 | Monitoring Agent |

---

## 生效日期與簽名

**配置日期**: 2026-05-27  
**版本**: 2.0  
**生效日期**: 立即生效  
**Mission Control**: JUKSY_MISSION_CONTROL_CLAUDE_CODE  
**參考文件**: 00_AGENT_BOOTSTRAP.md、WORKER_DISPATCH_TEMPLATE.md
