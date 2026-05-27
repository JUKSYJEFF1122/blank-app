# Skill Pack Index v1.0

**Mission Control**: JUKSY_MISSION_CONTROL_CLAUDE_CODE  
**Governance**: SP-21 v2.1 Source of Truth Guard  
**Date**: 2026-05-27

---

## Skill 派工規則聲明

所有 ccode Skill 派工必須遵循以下規則。Skill 不得直接派發裸 prompt。

### Skill 派工強制規則

```
ALL SKILL DISPATCH MUST:

1. 優先套用 00_MISSION_CONTROL_BOARD/WORKER_DISPATCH_TEMPLATE.md
2. 在 Skill prompt 最前面注入「WORKER TASK INITIALIZATION」區塊
3. 遵循所有派工規則：
   - 繁體中文規則
   - 禁止日文混用
   - SP-21 v2.1 Source of Truth Guard
   - JUKSY_MISSION_CONTROL_CLAUDE_CODE 統籍
   - CURRENT_TASK_LOCK 控制
   - 不得要求 Jeff 搬運中間結果
   - STATUS / DONE / NEXT / BLOCKER 固定格式

4. Skill 執行時必須受 Mission Control 統籍
```

---

## ccode Skill 清單

| Skill 名稱 | 派工用途 | 派工入口 | 檢查點 |
|-----------|--------|--------|--------|
| session-start-hook | 啟動 SessionStart 鉤子 | WORKER_DISPATCH_TEMPLATE.md | READY → DISPATCHED |
| update-config | 修改 ccode 配置 | WORKER_DISPATCH_TEMPLATE.md | READY → DISPATCHED |
| keybindings-help | 自訂鍵盤快捷鍵 | WORKER_DISPATCH_TEMPLATE.md | READY → DISPATCHED |
| verify | 驗證代碼變更 | WORKER_DISPATCH_TEMPLATE.md | READY → DISPATCHED |
| code-review | 程式碼審查 | WORKER_DISPATCH_TEMPLATE.md | READY → DISPATCHED |
| simplify | 簡化代碼 | WORKER_DISPATCH_TEMPLATE.md | READY → DISPATCHED |
| fewer-permission-prompts | 減少權限提示 | WORKER_DISPATCH_TEMPLATE.md | READY → DISPATCHED |
| loop | 週期性執行命令 | WORKER_DISPATCH_TEMPLATE.md | READY → DISPATCHED |
| claude-api | 構建 Claude API 應用 | WORKER_DISPATCH_TEMPLATE.md | READY → DISPATCHED |
| run | 啟動應用程式 | WORKER_DISPATCH_TEMPLATE.md | READY → DISPATCHED |
| init | 初始化 CLAUDE.md | WORKER_DISPATCH_TEMPLATE.md | READY → DISPATCHED |
| review | 審查拉取請求 | WORKER_DISPATCH_TEMPLATE.md | READY → DISPATCHED |
| security-review | 安全審查 | WORKER_DISPATCH_TEMPLATE.md | READY → DISPATCHED |

---

## Skill 派工樣板

### 標準 Skill 派工格式

```
[載入 WORKER_DISPATCH_TEMPLATE.md]

=== WORKER TASK INITIALIZATION ===

[WORKER_DISPATCH_TEMPLATE 中的所有規則]

=== END WORKER TASK INITIALIZATION ===

[Skill 實際執行指令]

範例：
/update-config language traditional-chinese
/verify
/code-review --fix
```

---

## Skill 派工前置檢查清單

派工前必須確認：

```
Skill 派工檢查清單（Pre-Dispatch Checklist）

[ ] Skill 名稱已識別
[ ] WORKER_DISPATCH_TEMPLATE.md 已加載
[ ] WORKER TASK INITIALIZATION 區塊已注入
[ ] 繁體中文規則已說明
[ ] 禁止日文混用規則已說明
[ ] SP-21 v2.1 已說明
[ ] JUKSY_MISSION_CONTROL_CLAUDE_CODE 已說明
[ ] CURRENT_TASK_LOCK 已說明
[ ] 不得要求 Jeff 搬運規則已說明
[ ] STATUS / DONE / NEXT / BLOCKER 格式已說明
[ ] 狀態轉移至 READY
[ ] 準備派發至 DISPATCHED
```

---

## Skill 執行監控

### Skill 執行狀態轉移

```
SKILL_QUEUED
  ↓
SKILL_READY (通過 Gate 1 和 Gate 2)
  ↓
SKILL_DISPATCHED (派發至 ccode 執行)
  ↓
SKILL_EXECUTING (執行中)
  ↓
SKILL_COMPLETED (執行完成)
  ↓
SKILL_VERIFIED (驗證完成)
```

### Skill 執行記錄

```
Skill 執行紀錄（Skill Execution Log）

日期: 2026-05-27
Skill ID: SKILL_UPDATE_CONFIG_001
Skill 名稱: update-config
派工源: Mission Control
參數: language=traditional-chinese
Gate 1 驗證: ✓ PASS
Gate 2 驗證: ✓ PASS
狀態轉移: READY → DISPATCHED
派發時間: 11:59:00
執行時間: 11:59:05
完成時間: 11:59:10
結果: ✓ SUCCESS
備註: ccode 配置更新完成
```

---

## 派工路由規則

### Skill 派工不得繞過的規則

```
派工路由（Routing Rules）

if task.type == "SKILL":
  1. 載入 WORKER_DISPATCH_TEMPLATE.md
  2. 注入 WORKER TASK INITIALIZATION 區塊
  3. 檢查所有必需規則項目
  4. 驗證狀態轉移 READY → DISPATCHED
  5. 記錄派工日誌
  6. 派發至 ccode Skill executor
else:
  不得派發
```

---

## 禁止事項

### Skill 派工嚴禁：

```
❌ 直接派發裸 Skill 命令
❌ 跳過 WORKER_DISPATCH_TEMPLATE.md 規則
❌ 省略繁體中文規則
❌ 遺漏 SP-21 v2.1 聲明
❌ 不記錄派工日誌
❌ 忽視狀態門控檢查
❌ 派發時未標記 JUKSY_MISSION_CONTROL_CLAUDE_CODE 統籍
❌ 允許日文混用
❌ 要求 Jeff 搬運中間結果
```

---

## 生效日期與簽名

**配置日期**: 2026-05-27  
**版本**: 1.0  
**生效日期**: 立即生效  
**Mission Control**: JUKSY_MISSION_CONTROL_CLAUDE_CODE  
**參考文件**: WORKER_DISPATCH_TEMPLATE.md、00_AGENT_BOOTSTRAP.md、00_CROSS_AGENT_STARTER_PROMPTS_v2.md
