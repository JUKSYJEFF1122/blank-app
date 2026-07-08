# 銜接說明 — 採購自動化系統開發 V3（雲端 session ↔ 本機 Windows session）

> 本文件由雲端 Claude Code 遠端執行環境（此 `blank-app` repo）於 2026-07-08 建立，
> 目的是讓即將在公司 Windows 電腦開啟的新 V3 本機 Claude Code session，
> 能快速接上進度，不用重新摸索環境落差。

## 為什麼會有這份文件

原始交接任務（採購自動化系統開發 V3 / 模組 G 上實彈）預期的工作目錄是
`C:\Users\Jeff\Desktop\TaskRoom_Skill_Packs`（本機 Windows 路徑），
但任務被送進了這個雲端遠端 session。雲端 session 只掛載得到
`JUKSYJEFF1122/blank-app` 這個 GitHub repo，**完全看不到本機 C 槽**，
因此無法讀取 HANDOFF.md、progress.md、price_engine 程式碼、
`.superpowers\sdd\`、`TaskRoom_Outputs\`、`secrets\.env` 等任何本機檔案。

嘗試以 GitHub App 整合權限新建 private repo `procurement-automation-v3`
時，收到 `403 Resource not accessible by integration`——目前這個 GitHub
App 安裝的權限範圍不含「建立新 repo」，需要 Jeff 到 GitHub App 設定
（或 claude.ai 的 GitHub 整合管理頁）手動開通 Repository creation 權限
才能由雲端 session 直接建立。在權限開通前，先以既有、已有 push 權限的
`JUKSYJEFF1122/blank-app` 作為暫時銜接站。

## 給新 V3 本機 session 的接手指引

1. **不要重做已完成項**：先讀 `TaskRoom_Outputs\ROOM_HANDOFFS\採購自動化系統開發_V2\HANDOFF.md`
   與 `.superpowers\sdd\progress.md`，八模組 A/A2/B/C/D/E/F/G 已在 master、
   543 tests 綠、皆經 Hermes 第三方驗收，不要重跑 audit。
2. **主線**：模組 G（Jordan 鑑定卡撿漏狙擊）上實彈，卡在 BLOCKED_ON_JEFF
   清單（eBay API 金鑰、eBay 登入態、card_targets.json、HUB_BOT_TOKEN、
   售票平台登入態、cards.json、蝦皮/露天登入態）。缺哪個就先跟 Jeff 要，
   不要用假資料填充或跳過安全降級邏輯。
3. **治理鐵規不變**：SP-42（規格書先過 Jeff 才寫 code）、SP-56（驗證閘門+Proof）、
   SP-58（Hermes 第三方驗收，逐一核對引用行號屬實）、SP-68（交易/付款/搶購/開卡/
   不可逆/對外發佈永遠 human-in-loop）、SP-52（UI 完成前無頭渲染+截圖）。
4. **模組 G 兩紅線**：絕不買錯卡（嚴格卡別比對）、絕不自動付款
   （競標只到出價、固定價撿漏備到付款前由 Jeff 人工付款）。
5. **若要與此雲端 session 協同**：把 `price_engine/`、`TaskRoom_Outputs/`
   等程式碼／文件 push 到本 repo（或等 Jeff 開通權限後改用專用的
   `procurement-automation-v3` private repo）。push 前務必確認
   `secrets/`、`*.env`、`ebay_session.json`、`card_targets.json`、
   `cards.json`、`watch_artists.json`、`HUB_BOT_TOKEN*` 等機敏檔案
   已被 `.gitignore` 排除（本次已預先加入排除規則，見 `.gitignore`）——
   金鑰/憑證一律留在本機 `secrets\.env` 金庫，不進版控、不貼對話。

## 目前分支

`claude/procurement-v3-module-g-golive-d3dqp1`（已建立，尚無實際程式碼，
僅有 Streamlit 範本 + 本銜接文件）。
