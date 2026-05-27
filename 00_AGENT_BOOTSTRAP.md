# Agent Bootstrap Configuration v1.0

**Mission Control**: JUKSY_MISSION_CONTROL_CLAUDE_CODE  
**Governance**: SP-21 v2.1 Source of Truth Guard  
**Date**: 2026-05-27

---

## 派工入口規則（MANDATORY）

所有 ccode / Cowork / worker / delegated task 派工，必須遵循以下規則。

### 派工前置檢查（Pre-Dispatch Gate）

**狀態轉換規則**：

```
QUEUED → READY
  前置檢查：確認 prompt 已套用 WORKER_DISPATCH_TEMPLATE.md
  檢查項目：
    ✓ 是否包含「=== WORKER TASK INITIALIZATION ===」區塊
    ✓ 是否定義了繁體中文規則
    ✓ 是否定義了 SP-21 v2.1 Source of Truth Guard

READY → DISPATCHED
  前置檢查：確認 prompt 已包含所有必要規則
  檢查項目：
    ✓ 繁體中文規則（全程繁體中文、技術名詞英文、禁止日文混用）
    ✓ SP-21 v2.1 Source of Truth Guard
    ✓ JUKSY_MISSION_CONTROL_CLAUDE_CODE 統籍規則
    ✓ CURRENT_TASK_LOCK 控制規則
    ✓ 不得要求 Jeff 搬運中間結果
    ✓ STATUS / DONE / NEXT / BLOCKER 固定格式
    
  若任何項目缺失，退回 QUEUED，要求補完。
```

### 派工模板強制引用

所有 agent bootstrap 流程必須在生成 worker prompt 時強制引用：

```
SOURCE: 00_MISSION_CONTROL_BOARD/WORKER_DISPATCH_TEMPLATE.md
INJECTION_POINT: prompt 最前面
INJECTION_METHOD: 直接注入「WORKER TASK INITIALIZATION」區塊
```

---

## Agent Bootstrap 流程

### Step 1: 接收任務（TASK_RECEIVED）

```
狀態：QUEUED
操作：记录原始任務描述
```

### Step 2: 套用派工範本（APPLY_TEMPLATE）

```
狀態：QUEUED
操作：載入 00_MISSION_CONTROL_BOARD/WORKER_DISPATCH_TEMPLATE.md
操作：在 prompt 最前面注入「WORKER TASK INITIALIZATION」區塊
```

### Step 3: 驗證派工規則（VALIDATE_RULES）

```
狀態：QUEUED → READY（若驗證通過）
檢查項目：
  - 繁體中文規則是否完整
  - SP-21 v2.1 是否明確
  - Mission Control 統籍是否清晰
  - CURRENT_TASK_LOCK 是否啟用
```

### Step 4: 派發任務（DISPATCH_TASK）

```
狀態：READY → DISPATCHED
操作：將驗證通過的 prompt 發送至 worker
記錄：記錄派工時間、版本、驗證結果
```

### Step 5: 監控執行（MONITOR_EXECUTION）

```
狀態：DISPATCHED
操作：監控 worker 回覆是否遵循規則
操作：確認回覆格式為 STATUS/DONE/NEXT/BLOCKER
```

---

## 派工入口清單

### 已配置的派工入口

- ✅ WORKER_DISPATCH_TEMPLATE.md (00_MISSION_CONTROL_BOARD/)
- ✅ 本檔案 (00_AGENT_BOOTSTRAP.md)

### 待配置的派工入口

建立時必須引用此檔案：

- 00_CROSS_AGENT_STARTER_PROMPTS_v2.md
- 00_Mission_Control_Registry.md（若建立）
- 00_Mission_Control_Queue.md（若建立）

---

## 規則生效範圍

| 對象 | 應用範圍 | 檢查點 |
|-----|--------|--------|
| worker task | 所有非同步任務 | READY → DISPATCHED |
| Cowork task | 所有協作任務 | READY → DISPATCHED |
| delegated task | 所有代理任務 | READY → DISPATCHED |
| ccode task | 所有 Claude Code 任務 | READY → DISPATCHED |

---

## 規則執行責任

| 角色 | 責任 | 檢查點 |
|-----|------|--------|
| Mission Control | 主控派工流程 | QUEUED 階段、READY 階段、DISPATCHED 階段 |
| Bootstrap Agent | 套用派工範本 | APPLY_TEMPLATE 階段、VALIDATE_RULES 階段 |
| Jeff | 驗收派工品質 | DISPATCHED 後、執行中 |

---

## 生效日期與簽名

**配置日期**: 2026-05-27  
**生效日期**: 立即生效  
**Mission Control**: JUKSY_MISSION_CONTROL_CLAUDE_CODE  
**治理框架**: SP-21 v2.1 Source of Truth Guard

---

## 附錄：派工檢查清單

派工前必須確認所有項目都已勾選：

```
派工前置檢查清單（Pre-Dispatch Checklist）

[ ] 原始任務已記錄至 QUEUED
[ ] WORKER_DISPATCH_TEMPLATE.md 已載入
[ ] WORKER TASK INITIALIZATION 區塊已注入到 prompt
[ ] 繁體中文規則已完整定義
[ ] 禁止日文混用規則已明確
[ ] SP-21 v2.1 Source of Truth Guard 已明確
[ ] JUKSY_MISSION_CONTROL_CLAUDE_CODE 已明確
[ ] CURRENT_TASK_LOCK 已啟用標記
[ ] 不得要求 Jeff 搬運中間結果已明確
[ ] STATUS / DONE / NEXT / BLOCKER 格式已說明
[ ] 狀態已轉移至 READY
[ ] 準備派發至 DISPATCHED

派發後監控清單（Post-Dispatch Checklist）

[ ] Worker 已開始執行
[ ] Worker 初始回覆符合繁體中文規則
[ ] Worker 回覆格式為 STATUS / DONE / NEXT / BLOCKER
[ ] Worker 無日文混用
[ ] Worker 無要求 Jeff 搬運中間結果
```
