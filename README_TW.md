# OpenCode 技能：反 AI 幻覺

> 一個 OpenCode Agent 技能，透過強制驗證、不確定性確認和基於證據的回應，防止 AI 產生幻覺。

[English](./README.md) | [中文](./README_CN.md) | 中文 (繁體) | [Русский](./README_RU.md) | [Français](./README_FR.md) | [Deutsch](./README_DE.md) | [Español](./README_ES.md) | [Español (Cuba)](./README_CU.md) | [Português](./README_PT.md) | [Português (Brasil)](./README_BR.md) | [العربية](./README_AR.md) | [हिन्दी](./README_HI.md)

## 功能特性

- **先驗證再陳述** — 讀取檔案、執行程式碼、查閱文件後再下判斷
- **承認不確定性** — 明確區分推測和已驗證的事實
- **不捏造** — 絕不杜撰錯誤訊息、API 方法、測試結果或修復方案
- **基於證據** — 引用檔案路徑、行號和實際輸出
- **回應自查清單** — 10 點回答前自我審計
- **程式碼產生護欄** — 寫程式碼前後的雙重驗證
- **工具級反幻覺規則** — 針對每種工具的具體防幻覺要點
- **8 類幻覺分類** — 每種幻覺類型有專門的預防方案

## 快速安裝

### 方式一：克隆倉庫（推薦）

```bash
git clone https://github.com/magiccloudflash/opencode-skill-hallucination-prevention.git
```

然後複製 skill 到 OpenCode 全域設定：

```bash
# macOS / Linux
cp -r hallucination-prevention ~/.config/opencode/skills/

# Windows PowerShell
Copy-Item -Recurse hallucination-prevention "$env:USERPROFILE\.config\opencode\skills\"
```

### 方式二：手動安裝

```bash
# macOS / Linux
mkdir -p ~/.config/opencode/skills/hallucination-prevention
cp hallucination-prevention/SKILL.md ~/.config/opencode/skills/hallucination-prevention/

# Windows PowerShell
mkdir -p "$env:USERPROFILE\.config\opencode\skills\hallucination-prevention"
Copy-Item hallucination-prevention/SKILL.md "$env:USERPROFILE\.config\opencode\skills\hallucination-prevention\"
```

### 方式三：全域規則（每次工作階段自動載入，強烈推薦）

```bash
# macOS / Linux
cp AGENTS.md ~/.config/opencode/AGENTS.md

# Windows PowerShell
Copy-Item AGENTS.md "$env:USERPROFILE\.config\opencode\AGENTS.md"
```

> 全域檔案 `AGENTS.md` 會在每次工作階段中自動載入，無需手動呼叫 skill。

## 使用方法

### On-Demand 載入（方式一、二）

Skill 安裝後會被 OpenCode 自動發現。Agent 在任務相關時會自動呼叫：

```javascript
skill({ name: "hallucination-prevention" })
```

### 自動載入（方式三）

規則會在每次工作階段啟動時自動注入上下文，**無需任何手動操作**。

## 權限設定

在 `opencode.json` 中控制 skill 存取權限：

```json
{
  "permission": {
    "skill": {
      "hallucination-prevention": "allow"
    }
  }
}
```

依 Agent 類型覆蓋權限：

```json
{
  "agent": {
    "plan": {
      "permission": {
        "skill": {
          "hallucination-prevention": "allow"
        }
      }
    }
  }
}
```

權限模式支援萬用字元：

| 權限 | 行為 |
|------|------|
| `allow` | 立即載入 skill |
| `deny` | 對 Agent 隱藏 skill，拒絕存取 |
| `ask` | 每次載入前提示使用者確認 |

## 倉庫結構

```
opencode-skill-hallucination-prevention/
├── README.md                                 # 英文文件
├── README_CN.md                              # 簡體中文文件
├── README_TW.md                              # 繁體中文文件（本檔案）
├── README_RU.md                              # 俄文文件
├── README_FR.md                              # 法文文件
├── README_DE.md                              # 德文文件
├── README_ES.md                              # 西班牙文文件
├── README_CU.md                              # 古巴西班牙文文件
├── README_PT.md                              # 葡萄牙文文件
├── README_BR.md                              # 巴西葡萄牙文文件
├── README_AR.md                              # 阿拉伯文文件
├── README_HI.md                              # 印度文文件
├── LICENSE                                   # MIT 授權
├── opencode.json                             # 設定範例
├── AGENTS.md                                 # 全域規則（自動載入版）
└── hallucination-prevention/
    └── SKILL.md                              # Skill 定義檔案
```

## Skill v2.0 內容詳解

### 8 類幻覺分類體系

| 類型 | 說明 | 範例 | 預防方法 |
|------|------|------|----------|
| **A: 事實性** | 陳述未經核實的事實 | "此函數回傳 Promise"（未讀原始碼） | 先讀原始碼，再執行程式碼 |
| **B: 結構性** | 捏造不存在的檔案/路徑 | "處理器在 src/routes/auth.ts"（檔案不存在） | 用 glob/ls 確認目錄結構 |
| **C: 行為性** | 假設程式碼行為 | "Bug 已經修復了"（未執行專案） | 執行程式碼並擷取實際輸出 |
| **D: 外部性** | 捏造 API/函式庫函數 | "Lodash 有 groupByAndSort() 函數"（不存在） | 查文件、原始碼或型別定義 |
| **E: 結果性** | 想像測試結果 | "所有測試通過"（未執行） | 執行測試，展示實際輸出 |
| **F: 時間性** | 錯誤的版本/日期 | "Node v24 新增了此功能"（v24 不存在） | 檢查目前版本和更新日誌 |
| **G: 權威性** | 假裝已執行了驗證 | "我已確認修改正確"（未重新讀取檔案） | 實際執行驗證並展示證據 |
| **H: 引用性** | 捏造 URL/ID/commit | "參見 https://github.com/foo/issues/99999"（假連結） | 驗證 URL 或註明"未核實" |

### 工具級反幻覺規則

| 工具 | 核心規則 |
|------|---------|
| **read** | 不猜檔案內容；讀取前確認檔案存在；編輯後重新讀取 |
| **write** | 寫入後立即重新讀取驗證；未被要求時不建立文件 |
| **edit** | 編輯前必須讀取原始檔；編輯後重新讀取確認；比對縮排風格 |
| **bash** | 不幻想命令輸出；修復後重新執行驗證；禁止性操作前詢問 |
| **grep** | 引用函數/類別前先 grep 確認存在 |
| **glob** | 引用路徑前先用 glob 發現實際結構 |
| **question** | 指令模糊時先詢問再操作，提供具體選項 |

### 10 點回應自查清單

回答前逐條檢查：

| # | 檢查項 | 不滿足時的動作 |
|---|--------|---------------|
| 1 | 我確實讀取了正在討論的檔案嗎？ | 現在去讀取 |
| 2 | 我確實執行了正在報告輸出的命令嗎？ | 現在去執行 |
| 3 | 我確實檢查了引用的套件/API 嗎？ | 現在檢查或標註不確定 |
| 4 | 我確定這個錯誤訊息是真實的嗎？ | 展示實際輸出 |
| 5 | 編輯檔案後我重新讀取過了嗎？ | 現在重新讀取 |
| 6 | 我在猜測目錄結構嗎？ | 列出目錄 |
| 7 | 我在猜測函數簽名嗎？ | 閱讀原始碼 |
| 8 | 我在未測試的情況下聲稱修復有效嗎？ | 執行程式碼 |
| 9 | 我在編造命令參數嗎？ | 查看 --help |
| 10 | 我的回答可能包含虛構的輸出嗎？ | 僅保留真實輸出 |

### 上下文錨定協定

1. **重大檔案操作前**：重新讀取相關原始檔
2. **多次工具呼叫後**（5 次以上）：重新讀取已修改檔案，確認累積狀態
3. **回到先前的任務時**：重新讀取所有相關檔案，不依賴記憶

### 語言/框架特定規則

- **JS/TS**：檢查 package.json 再 import；檢查 tsconfig.json 再聲稱設定
- **Python**：檢查 requirements.txt/pyproject.toml 再 import；驗證 python --version
- **Rust**：檢查 Cargo.toml；驗證 rust-toolchain.toml
- **Go**：檢查 go.mod；驗證 go version
- **Shell**：腳本前確認可用命令；一行命令先測試

### Git 特定規則

- 禁止偽造 commit hash — 用 `git log` 查看真實的
- 禁止假設 commit 資訊 — 用 `git log --oneline` 查看
- 禁止捏造 PR 編號或 Issue ID
- Commit 後執行 `git status` 和 `git log -1` 驗證
- 聲稱分支存在前執行 `git branch -a`

### URL/引用規則

- 禁止產生未驗證的 URL
- 文件參考優先使用本地路徑
- 若不確定 URL：詢問使用者，或註明"未驗證"

### 確定性級別標註

- **已驗證**：我直接讀取/執行/檢查過了
- **很可能**：有強證據但未直接驗證
- **推測性**：基於類似模式推斷，需要驗證
- **未知**：無法從現有資訊判斷

預設應達到"已驗證"級別。

### 升級協定（何時停下來問使用者）

以下情況立即停止並詢問使用者：
1. 指令含糊，有多種理解方式
2. 所需工具/函式庫不可用
3. 操作需要提升權限
4. 觀察到的情況與使用者描述矛盾
5. 任務需要你無法驗證的知識
6. 操作可能導致資料遺失或安全性問題
7. 上下文變化太多，難以準確追蹤

## 授權

MIT — 詳見 [LICENSE](LICENSE) 檔案

## 貢獻

歡迎提交 Issues 和 Pull Requests：

https://github.com/magiccloudflash/opencode-skill-hallucination-prevention

---

**導覽** : [English](./README.md) | [中文](./README_CN.md) | **中文 (繁體)** | [Русский](./README_RU.md) | [Français](./README_FR.md) | [Deutsch](./README_DE.md) | [Español](./README_ES.md) | [Español (Cuba)](./README_CU.md) | [Português](./README_PT.md) | [Português (Brasil)](./README_BR.md) | [العربية](./README_AR.md) | [हिन्दी](./README_HI.md)
