# Clawdbot 完整配置指南：從安裝到 Claude Code 中轉

![](https://upload.maynor1024.live/file/1769478880951_image-20260127095432442.png)

## 目錄

- [簡介](#簡介)
- [系統需求](#系統需求)
- [安裝步驟](#安裝步驟)
- [配置 Claude Code API 中轉](#配置-claude-code-api-中轉)
- [驗證與測試](#驗證與測試)
- [常見踩雷點](#常見踩雷點)
- [常見問題 FAQ](#常見問題-faq)
- [常用指令](#常用指令)

---

## 簡介

Clawdbot 是一個開源的本地 AI 助手，支援透過通訊軟體（Telegram、WhatsApp、Discord 等）或 Web UI 與 AI 模型互動。

**核心特點：**
- 🦞 完全開源，資料本地儲存
- 💬 支援多種訊息平台
- 🌐 提供 Web 控制面板
- 🔧 可自訂 AI 模型和 API 端點

---

## 系統需求

### 必要條件

| 項目 | 需求 |
|------|------|
| 作業系統 | macOS / Linux / Windows |
| Node.js | 22.0.0 或更高版本 |
| 套件管理器 | pnpm（推薦）或 npm |

### 可選條件

- Xcode（僅 macOS，如需建置原生應用程式）
- 訊息平台帳號（Telegram Bot Token、Discord Bot 等）

---

## 安裝步驟

### 1. 升級 Node.js 版本

Clawdbot 要求 Node.js 22+，使用 nvm 管理版本：

```bash
# 安裝 Node.js 22
nvm install 22

# 設定為預設版本
nvm use 22
nvm alias default 22

# 驗證版本
node --version  # 應顯示 v22.x.x
```

### 2. 選擇安裝方式

**方式一：npm 安裝（推薦）**

```bash
npm install -g clawdbot
```

**方式二：一鍵安裝腳本**

```bash
curl -fsSL https://clawd.bot/install.sh | bash
```

**方式三：從原始碼安裝**

```bash
# 複製儲存庫
git clone https://github.com/clawdbot/clawdbot.git
cd clawdbot

# 安裝依賴
pnpm install

# 建置
pnpm build

# 連結 CLI
npm link
```

### 3. 初始化配置

安裝完成後執行配置精靈：

```bash
clawdbot onboard
```

**配置精靈流程：**

#### 步驟 1：安全確認

```
◇  Security ───────────────────────────────────────────────────────╮
│  Clawdbot agents can run commands, read/write files, and act     │
│  through any tools you enable.                                   │
│  Please read: https://docs.clawd.bot/security                    │
├──────────────────────────────────────────────────────────────────╯

◇  I understand this is powerful and inherently risky. Continue?
│  Yes
```

#### 步驟 2：選擇 AI 後端

```
◇  Model/auth provider
│  Anthropic

◆  Anthropic auth method
│  ● Anthropic token (paste setup-token) ← 推薦 Claude Max 用戶
│  ○ Anthropic token (Claude Code CLI)
│  ○ Anthropic API key
```

**認證方式比較：**

| 認證方式 | 適用場景 | 優點 | 缺點 |
|---------|---------|------|------|
| setup-token | Claude Max/Pro 訂閱用戶 | 無需額外付費 | 需要額外步驟產生 token |
| Claude Code CLI | 已配置 Claude Code 的用戶 | 自動讀取憑證 | 可能找不到憑證檔案 |
| API Key | API 用量付費用戶 | 最直接 | 需要獨立付費 |

#### 步驟 3：產生 setup-token（Claude Max 用戶）

在另一個終端機執行：

```bash
claude setup-token
```

複製產生的 token，貼上到配置精靈中：

```
◇  Paste Anthropic setup-token
│  sk-ant-oat01-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx...

◇  Token name (blank = default)
│  default

◇  Default model
│  Keep current (default: anthropic/claude-opus-4-5)
```

> **提示：** Claude Max 訂閱用戶不需要單獨購買 API Key，使用 setup-token 即可共用訂閱額度。

#### 步驟 4：配置訊息平台（可選）

```
◇  Channel status ────────────────────────────╮
│  Telegram: not configured                   │
│  WhatsApp: not configured                   │
│  Discord: not configured                    │
│  ...共支援 12+ 平台                          │
├─────────────────────────────────────────────╯

◇  Select channel (QuickStart)
│  Telegram (Bot API)
```

**獲取 Telegram Bot Token：**

1. 在 Telegram 中搜尋 @BotFather
2. 發送 `/newbot`
3. 按提示輸入 Bot 名稱和使用者名稱
4. 複製 Bot Token

#### 步驟 5：完成配置

```
◇  Telegram: ok (@YourBotName) (1416ms)
    Agents: main (default)
    Heartbeat interval: 1h (main)

◇  Control UI ─────────────────────────────────────────────────────╮
│  Web UI: http://127.0.0.1:18789/                                 │
│  Web UI (with token): http://127.0.0.1:18789/?token=your-token   │
│  Gateway WS: ws://127.0.0.1:18789                                │
├──────────────────────────────────────────────────────────────────╯

└  Onboarding complete.
```

#### 步驟 6：配對驗證（如使用 Telegram）

去 Telegram 給你的 Bot 發送訊息，會收到配對碼：

```
Clawdbot: access not configured.

Your Telegram user id: 1234567890
Pairing code: ABC12345

Ask the bot owner to approve with:
clawdbot pairing approve telegram <code>
```

在終端機核准配對：

```bash
clawdbot pairing approve telegram ABC12345
```

---

## 配置 Claude Code API 中轉

### 1. 獲取 API 憑證

從 Claude Code API 中轉服務獲取：
- **API Base URL**: `https://code.claude-opus.top/api`
- **API Key**: `cr_xxxxxxxxxxxxx`

**推薦服務：**
- 購買連結：https://maynorai.tqfk.xyz/item/7
- 推薦中轉 API：https://apipro.maynor1024.live/

### 2. 修改設定檔

> **⚠️ 重要提示：** Clawdbot 不支援透過環境變數 `ANTHROPIC_BASE_URL` 來設定自訂 API 端點。必須透過設定檔的 `models.providers` 來配置。

#### 步驟 1：備份設定檔

```bash
cp ~/.clawdbot/clawdbot.json ~/.clawdbot/clawdbot.json.bak
```

#### 步驟 2：編輯設定檔

```bash
nano ~/.clawdbot/clawdbot.json
```

在設定檔中新增 `models` 部分：

```json
{
  "models": {
    "providers": {
      "anthropic": {
        "baseUrl": "https://code.claude-opus.top/api",
        "apiKey": "cr_你的API金鑰",
        "api": "anthropic-messages",
        "models": []
      }
    }
  }
}
```

**關鍵配置說明：**

| 欄位 | 說明 | 必要 |
|------|------|------|
| baseUrl | 自訂 API 端點 | ✅ |
| apiKey | 你的 API 金鑰 | ✅ |
| api | 必須設定為 `anthropic-messages` | ✅ |
| models | 必須包含此欄位，可以為空陣列 `[]` | ✅ |

**完整配置範例：**

```json
{
  "meta": {
    "lastTouchedVersion": "2026.1.25",
    "lastTouchedAt": "2026-01-27T01:05:21.233Z"
  },
  "models": {
    "providers": {
      "anthropic": {
        "baseUrl": "https://code.claude-opus.top/api",
        "apiKey": "cr_你的API金鑰",
        "api": "anthropic-messages",
        "models": []
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "anthropic/claude-sonnet-4-5"
      },
      "workspace": "/Users/你的使用者名稱/clawd",
      "maxConcurrent": 4
    }
  },
  "gateway": {
    "port": 18789,
    "mode": "local",
    "bind": "loopback",
    "auth": {
      "mode": "token",
      "token": "你的gateway_token"
    }
  },
  "channels": {
    "telegram": {
      "enabled": false
    }
  }
}
```

#### 步驟 3：驗證配置格式

```bash
# 使用 jq 驗證 JSON 格式
cat ~/.clawdbot/clawdbot.json | jq '.models'

# 應輸出：
# {
#   "providers": {
#     "anthropic": {
#       "baseUrl": "https://code.claude-opus.top/api",
#       "apiKey": "cr_...",
#       "api": "anthropic-messages",
#       "models": []
#     }
#   }
# }
```

### 3. 重啟 Gateway 服務

```bash
clawdbot gateway restart
```

### 4. 驗證配置生效

```bash
# 檢查 Gateway 狀態
clawdbot channels status

# 應顯示：
# Gateway reachable.
```

---

## 驗證與測試

### 1. 檢查 Gateway 狀態

```bash
clawdbot channels status
```

**正常輸出：**
```
Gateway reachable.
- Telegram default: disabled, configured, stopped
```

### 2. 訪問 Web UI

開啟瀏覽器訪問：

```
http://127.0.0.1:18789/?token=你的token
```

**Web UI 功能：**
- 💬 Chat: 直接與 AI 對話
- 📊 Overview: 查看系統狀態
- 🔌 Channels: 管理訊息通道
- ⚙️ Config: 修改配置

### 3. 發送測試訊息

在 Web UI 的 Chat 介面：

1. 輸入訊息：`Hello, can you hear me?`
2. 點擊 Send 按鈕
3. 等待 AI 回覆

**預期結果：**
- 狀態顯示 "Health OK"
- 收到 AI 的回覆訊息
- 右上角顯示 token 使用情況

### 4. 查看日誌

如果遇到問題，檢查日誌：

```bash
# Gateway 主日誌
tail -f ~/.clawdbot/logs/gateway.log

# 錯誤日誌
tail -f ~/.clawdbot/logs/gateway.err.log

# 詳細除錯日誌
tail -f /tmp/clawdbot/clawdbot-$(date +%Y-%m-%d).log
```

---

## 常見踩雷點

### ❌ 踩雷 1：環境變數配置無效

**錯誤做法：**
```bash
# 在 LaunchAgent 中設定環境變數（無效！）
<key>ANTHROPIC_BASE_URL</key>
<string>https://code.claude-opus.top/api</string>
```

**問題原因：** Clawdbot 不支援透過 `ANTHROPIC_BASE_URL` 環境變數來設定自訂 API 端點。

**✅ 正確做法：** 在 `~/.clawdbot/clawdbot.json` 設定檔中新增：
```json
{
  "models": {
    "providers": {
      "anthropic": {
        "baseUrl": "https://code.claude-opus.top/api",
        "apiKey": "cr_你的金鑰",
        "api": "anthropic-messages",
        "models": []
      }
    }
  }
}
```

### ❌ 踩雷 2：缺少 models 欄位

**錯誤配置：**
```json
{
  "models": {
    "providers": {
      "anthropic": {
        "baseUrl": "https://code.claude-opus.top/api",
        "apiKey": "cr_xxx",
        "api": "anthropic-messages"
        // 缺少 models 欄位！
      }
    }
  }
}
```

**錯誤訊息：**
```
Invalid config at ~/.clawdbot/clawdbot.json:
- models.providers.anthropic.models: Invalid input: expected array
```

**✅ 正確做法：** 必須包含 `models` 欄位，即使是空陣列：
```json
{
  "models": {
    "providers": {
      "anthropic": {
        "baseUrl": "https://code.claude-opus.top/api",
        "apiKey": "cr_xxx",
        "api": "anthropic-messages",
        "models": []  // 必須有這一行！
      }
    }
  }
}
```

### ❌ 踩雷 3：Telegram 連線失敗導致 Gateway 不穩定

**症狀：**
- Gateway 不斷重啟
- 日誌顯示 `TypeError: fetch failed`
- Web UI 無法連線

**✅ 解決方案：** 臨時禁用 Telegram：
```bash
clawdbot config set channels.telegram.enabled false
clawdbot gateway restart
```

### ❌ 踩雷 4：Node.js 版本過低

**錯誤訊息：**
```
clawdbot requires Node >=22.0.0.
Detected: node 20.19.0
```

**✅ 解決方案：**
```bash
nvm install 22
nvm use 22
nvm alias default 22
node --version  # 應顯示 v22.x.x
```

### ❌ 踩雷 5：忘記重啟 Gateway

**問題：** 修改配置後沒有重啟 Gateway，配置不生效。

**✅ 解決方案：**
```bash
# 每次修改配置後都要重啟
clawdbot gateway restart

# 驗證配置生效
clawdbot channels status
```

---

## 常見問題 FAQ

### Q1: Gateway 無法連線

**症狀：** Web UI 顯示 "disconnected (1006): no reason"

**解決方案：**
```bash
# 1. 檢查 Gateway 是否執行
ps aux | grep clawdbot-gateway

# 2. 檢查連接埠佔用
lsof -i :18789

# 3. 檢查設定檔是否有效
cat ~/.clawdbot/clawdbot.json | jq '.'

# 4. 重啟 Gateway
clawdbot gateway restart

# 5. 查看錯誤日誌
tail -50 ~/.clawdbot/logs/gateway.err.log
```

### Q2: API 呼叫失敗

**症狀：** 日誌顯示 "TypeError: fetch failed"

**解決方案：**
```bash
# 1. 測試 API 端點可存取性
curl -s https://code.claude-opus.top/api/v1/messages \
  -H "x-api-key: 你的API金鑰" \
  -H "anthropic-version: 2023-06-01" \
  -H "content-type: application/json" \
  -d '{"model":"claude-sonnet-4-5","max_tokens":100,"messages":[{"role":"user","content":"hi"}]}'

# 2. 驗證設定檔
cat ~/.clawdbot/clawdbot.json | jq '.models.providers.anthropic'

# 3. 確保包含所有必要欄位
# - baseUrl
# - apiKey
# - api: "anthropic-messages"
# - models: []

# 4. 重啟 Gateway
clawdbot gateway restart
```

### Q3: Assistant 不回覆訊息

**症狀：**
- Web UI 發送訊息後無回應
- 日誌顯示請求完成但耗時很短（< 1秒）
- 沒有錯誤訊息

**解決方案：**
1. 確認使用設定檔方式（見 "配置 Claude Code API 中轉" 章節）
2. 檢查設定檔格式正確
3. 重啟 Gateway
4. 在 Web UI 中發送測試訊息

### Q4: 如何查看詳細日誌？

```bash
# Gateway 主日誌
tail -f ~/.clawdbot/logs/gateway.log

# 錯誤日誌
tail -f ~/.clawdbot/logs/gateway.err.log

# 詳細除錯日誌（JSON 格式）
tail -f /tmp/clawdbot/clawdbot-$(date +%Y-%m-%d).log

# 過濾錯誤資訊
tail -f /tmp/clawdbot/clawdbot-$(date +%Y-%m-%d).log | grep -i "error\|fail"
```

### Q5: 如何完全重設配置？

```bash
# 1. 備份目前配置
cp -r ~/.clawdbot ~/.clawdbot.backup

# 2. 停止 Gateway
clawdbot gateway stop

# 3. 刪除配置
rm -rf ~/.clawdbot

# 4. 重新執行配置精靈
clawdbot onboard
```

---

## 常用指令

### Gateway 管理

```bash
# 查看狀態
clawdbot channels status

# 深度檢查
clawdbot channels status --deep

# 重啟 Gateway
clawdbot gateway restart

# 停止 Gateway
launchctl unload ~/Library/LaunchAgents/com.clawdbot.gateway.plist

# 啟動 Gateway
launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/com.clawdbot.gateway.plist
```

### 配置管理

```bash
# 互動式配置
clawdbot configure

# 配置特定部分
clawdbot configure --section gateway
clawdbot configure --section channels
clawdbot configure --section model

# 設定配置項
clawdbot config set gateway.mode local
clawdbot config set channels.telegram.enabled false

# 查看配置
cat ~/.clawdbot/clawdbot.json
```

### 日誌查看

```bash
# Gateway 主日誌
tail -f ~/.clawdbot/logs/gateway.log

# 錯誤日誌
tail -f ~/.clawdbot/logs/gateway.err.log

# 詳細日誌（JSON 格式）
tail -f /tmp/clawdbot/clawdbot-$(date +%Y-%m-%d).log
```

### 診斷工具

```bash
# 執行診斷
clawdbot doctor

# 自動修復問題
clawdbot doctor --fix

# 安全稽核
clawdbot security audit --deep
```

### Web UI

```bash
# 開啟 Web UI（瀏覽器）
clawdbot dashboard

# 獲取帶 token 的 URL（不開啟瀏覽器）
clawdbot dashboard --no-open
```

### TUI（終端介面）

```bash
# 啟動 TUI
clawdbot tui

# TUI 快速鍵
# Ctrl+C: 退出
# Enter: 發送訊息
# Shift+Enter: 換行
```

### 更新

```bash
# 從原始碼更新
cd /path/to/clawdbot
git pull --rebase
pnpm install
pnpm build
clawdbot gateway restart

# 從 npm 更新（全域安裝）
npm install -g clawdbot@latest
```

---

## 設定檔位置

```
~/.clawdbot/
├── clawdbot.json              # 主設定檔
├── credentials/               # API 憑證
├── sessions/                  # 工作階段資料
├── logs/                      # 日志檔案
│   ├── gateway.log           # Gateway 主日志
│   └── gateway.err.log       # 錯誤日志
└── agents/                    # Agent 工作空間
    └── main/
        └── sessions/          # 工作階段儲存

~/Library/LaunchAgents/        # macOS 服務配置
└── com.clawdbot.gateway.plist

/tmp/clawdbot/                 # 臨時日誌
└── clawdbot-YYYY-MM-DD.log   # 詳細除錯日誌
```

---

## 安全建議

### 1. 保護 API Key

- ❌ 不要將 API Key 提交到 Git
- ✅ 定期輪換 API Key
- ✅ 使用設定檔而非環境變數

### 2. Gateway Token

- ❌ 不要分享 Web UI 的 token URL
- ✅ 定期更換 Gateway token

### 3. 網路安全

- ✅ Gateway 預設只監聽 localhost
- ✅ 如需遠端存取，使用 Tailscale 或 VPN
- ❌ 不要直接暴露到公網

### 4. 資料備份

```bash
# 備份配置和會話
tar -czf clawdbot-backup-$(date +%Y%m%d).tar.gz ~/.clawdbot/
```

---

## 參考資源

- **官方文件**: https://docs.clawd.bot/
- **GitHub 儲存庫**: https://github.com/clawdbot/clawdbot
- **官方網站**: https://clawd.bot/
- **社群討論**: GitHub Issues

---

## 總結

透過本教學，你應該已經：

✅ 成功安裝 Clawdbot  
✅ 正確配置 Claude Code API 中轉（透過設定檔）  
✅ 啟動 Gateway 服務  
✅ 透過 Web UI 與 AI 對話  
✅ 了解常用指令和故障排除  
✅ 避開了所有常見踩雷點

### 關鍵要點

1. **API 配置方式：** 必須使用設定檔 `~/.clawdbot/clawdbot.json` 中的 `models.providers`
2. **必要欄位：** `baseUrl`、`apiKey`、`api`、`models` 四個欄位缺一不可
3. **配置後重啟：** 每次修改配置後必須執行 `clawdbot gateway restart`
4. **Telegram 問題：** 如遇連線問題，先禁用 Telegram 使用 Web UI
5. **Node.js 版本：** 必須 22.0.0 或更高

### 快速參考

```bash
# 查看狀態
clawdbot channels status

# 重啟 Gateway
clawdbot gateway restart

# 查看日誌
tail -f ~/.clawdbot/logs/gateway.log

# 開啟 Web UI
clawdbot dashboard

# 設定檔位置
~/.clawdbot/clawdbot.json
```

祝使用愉快！🦞

---

**教學版本：** v2.0 (2026-01-27)  
**更新內容：** 修正 API 配置方式，新增完整踩雷指南  
**適用版本：** Clawdbot 2026.1.25+
