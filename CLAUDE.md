# CLAUDE.md - ccode / Cowork / Worker 任務室設定規範

## GLOBAL_LANGUAGE_RULE

* **預設語言**：所有回覆使用繁體中文。
* **語言切換**：除非 Jeff 明確要求英文、日文或其他語言，否則不得切換語言。
* **技術名詞**：技術名詞可保留英文，但解釋、摘要、狀態回報、錯誤說明、任務交接、驗收報告一律使用繁體中文。
* **禁止混用**：不得中途混用日文。
* **回覆風格**：對 Jeff 的回覆要直接、清楚、可執行，不要反覆問「是否需要我幫忙」。
* **Task Assignment**：若任務需要 worker 執行，請透過 JUKSY_MISSION_CONTROL_CLAUDE_CODE 指派，不要要求 Jeff 搬運中間結果。
* **不確定性處理**：若遇到不確定性，先做合理假設、記錄假設、繼續執行；只有 BLOCKED_NEED_DECISION 才通知 Jeff。
* **固定回覆格式**：
  ```
  STATUS: [current state]
  DONE: [completed items]
  NEXT: [next steps]
  BLOCKER: [any blockers]
  ```

---

## SP-21 v2.1 Source of Truth Guard

* Mission Control: **JUKSY_MISSION_CONTROL_CLAUDE_CODE**
* 所有 worker 任務必須經 **CURRENT_TASK_LOCK**
* 禁止中間結果搬運

---

## ccode 簡稱規則

* 「Claude Code」統一簡稱為「ccode」
* 適用於所有文件、commit message、任務描述、狀態報告

---

## 項目信息

**Repository**: juksyjeff1122/blank-app  
**Branch**: claude/mobile-desktop-task-continuity-5BYId  
**Owner**: Jeff (jeff@juksy.com)  
**Created**: 2026-05-27
