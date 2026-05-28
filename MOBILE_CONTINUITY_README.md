# 手機端任務接續指南

## 當前狀態

- **手機可見範圍**：Claude Desktop mobile App → Code 頁面 → 特定 repo / branch / review
- **完整任務室**：PC ccode desktop 的任務室（MISSION_CONTROL_BOARD 等目錄）**不會完整同步到手機**
- **同步機制**：只有 GitHub repo / branch / review 相關內容會自動同步到手機 Code 頁面

## 手機端的三個工作入口

### 1. **Code 頁面**（自動同步）
- 位置：Claude Desktop mobile App → Code tab
- 可見內容：
  - Repository: `juksyjeff1122/blank-app`
  - Branch: `claude/mobile-desktop-task-continuity-5BYId`
  - Pull Request review（如果存在）
  - 最新 commit: `e57dc44` 及後續 commits
- 使用場景：查看/編輯與 GitHub PR 相關的代碼變更
- 注意：PC 本機的 ccode session 和完整任務室清單**不會出現**在手機 Code 頁面

### 2. **Dispatch 頁面**（另行進入）
- 位置：Claude Desktop mobile App → Dispatch tab（或左側菜單）
- 用途：派工新的 worker 任務到其他 Agent
- 要求：所有派工任務必須套用 **WORKER_DISPATCH_TEMPLATE.md** 規則
- 模板位置（PC 端查看）：`/home/user/blank-app/00_MISSION_CONTROL_BOARD/WORKER_DISPATCH_TEMPLATE.md`

### 3. **Cowork 頁面**（另行進入）
- 位置：Claude Desktop mobile App → Cowork tab（或左側菜單）
- 用途：多人協作任務
- 要求：同樣遵循 GLOBAL_LANGUAGE_RULE（繁體中文）和 SP-21 v2.1 governance

## 從手機接續 PC 任務的正確方式

### 場景 1：在手機 Code 頁面繼續 PC 的代碼相關任務
1. 打開 Claude Desktop mobile App
2. 進入 **Code** tab
3. 你會看到 `juksyjeff1122/blank-app` 的最新代碼和 commits
4. 在 mobile Code 環境中編輯/查看文件，或提出新的代碼變更
5. 所有變更會自動同步到遠端分支

**重要**：你不需要從 PC 手動搬運任何中間結果或會話狀態到手機。手機會自動讀取最新的 GitHub repo/branch 狀態。

### 場景 2：在手機派工新任務（Dispatch）
1. 進入手機的 **Dispatch** tab
2. 建立新的 worker 任務派工
3. 在派工 prompt 中**強制注入** WORKER_DISPATCH_TEMPLATE 的規則：
   - 繁體中文回覆
   - 禁止日文混用
   - 套用 SP-21 v2.1 governance
   - 使用 STATUS / DONE / NEXT / BLOCKER 固定格式
4. 派工確認後，worker Agent 會在遠端執行

參考模板：**PC 端 `/home/user/blank-app/00_MISSION_CONTROL_BOARD/WORKER_DISPATCH_TEMPLATE.md`**

### 場景 3：在手機 Cowork 進行多人協作
1. 進入手機的 **Cowork** tab
2. 遵循相同的語言規則和派工規則
3. Cowork 任務也不需要 Jeff 手動搬運中間結果

## 無需手動搬運的理由

ccode 的**雲同步**是應用層級的自動功能：
- GitHub repo/branch 的更新會自動同步到手機 Code 頁面
- Dispatch/Cowork 的派工狀態會自動在各端同步
- **你只需要在手機或 PC 上任選一處工作，其他端會自動看到最新狀態**

因此，你**不需要**：
- 手動複製代碼到手機
- 手動記錄會話狀態
- 手動更新任務進度
- 手動傳遞文件或日誌

## PC 任務室的特殊說明

以下內容**只在 PC ccode 可見**，手機端無法直接訪問，但不影響日常工作：
- `/home/user/blank-app/CLAUDE.md` - 項目規則（已套用到所有派工）
- `/home/user/blank-app/.claude/settings.json` - 項目級配置（已自動生效）
- `/home/user/blank-app/00_MISSION_CONTROL_BOARD/` - 治理文檔（供參考，不需手動同步）
- `/home/user/blank-app/00_AGENT_BOOTSTRAP.md` - Agent 派工規則（已固化）

如果手機端需要查看這些文檔，請在 GitHub 網頁版查看相同分支 `claude/mobile-desktop-task-continuity-5BYId`。

## Mission Control 當前狀態（2026-05-28）

| 項目 | 狀態 | 詳情 |
|------|------|------|
| Windows TaskRoom 同步 | ✅ SYNCED_AND_VERIFIED | Lobster 於 20260528_180153 完成 7/7 檔案 + 8/8 規則驗證 |
| Fallback Patch | ✅ IMPLEMENTED | WORKER_DISPATCH_TEMPLATE 強制注入已生效 |
| 三層派工路由 | ✅ CONFIGURED | Bootstrap → Cross-Agent → Skill 全鏈完整 |
| 手機端接續驗收 | 🔄 IN_PROGRESS | 本文件更新中 |
| ccode Skill 派工實測 | ⏳ QUEUED | 13 Skills 中選 1 項實測完整流程 |

## 關鍵狀態確認

| 項目 | 狀態 | 備註 |
|------|------|------|
| 最新 commit | b49c080 | 已推送到遠端分支 |
| 手機 Code 可見性 | ✅ 自動同步 | juksyjeff1122/blank-app 最新代碼可見 |
| 手機 Dispatch 可見性 | ✅ 另行進入 | Dispatch tab 可派工新任務 |
| 手機 Cowork 可見性 | ✅ 另行進入 | Cowork tab 可進行協作任務 |
| PC 任務室同步到手機 | ❌ 不同步 | PC 本機任務室僅在 PC 端可見 |
| Windows TaskRoom 同步 | ✅ VERIFIED | C:\Users\Jeff\Desktop\TaskRoom_Skill_Packs\ 同步完成 |
| 治理規則生效範圍 | ✅ 全端 | CLAUDE.md 規則對所有派工生效 |

## 下次從手機建立任務的檢查清單

```
□ 確認在手機的 Dispatch/Cowork tab（不是 Code tab）
□ 任務 prompt 最前面注入 WORKER_DISPATCH_TEMPLATE 規則
□ 使用繁體中文，禁止日文混用
□ 套用 SP-21 v2.1 governance：
  □ JUKSY_MISSION_CONTROL_CLAUDE_CODE 標識
  □ CURRENT_TASK_LOCK 啟用
  □ 禁止要求 Jeff 搬運中間結果
□ 輸出格式：STATUS / DONE / NEXT / BLOCKER
□ 派工確認後，不需手動搬運任何結果到 PC
```

## 常見問題

**Q: 我在手機 Code 看到的代碼是最新的嗎？**
A: 是的。任何 PC 推送到 `claude/mobile-desktop-task-continuity-5BYId` 的代碼會自動同步到手機。

**Q: 我在手機修改代碼，PC 會自動看到嗎？**
A: 是的。雲同步是雙向的，任何端的變更都會自動同步。

**Q: PC 的任務室（MISSION_CONTROL_BOARD 等）為什麼手機看不到？**
A: PC ccode 的本機任務室只存在於 PC 環境，不是 GitHub repo 的一部分，所以不會同步到手機。如需查看，請在 GitHub 網頁版查看本分支的相關文檔。

**Q: 我應該在手機或 PC 上工作？**
A: 根據當前任務選擇最方便的設備。無論在哪裡工作，其他設備都會自動看到最新的 repo 狀態和派工結果。

---

**最後更新**：2026-05-28  
**分支**：claude/mobile-desktop-task-continuity-5BYId  
**治理框架**：SP-21 v2.1 Source of Truth Guard
