# Polymarket CLI Skill

A comprehensive Claude skill for interacting with the Polymarket CLI - a Rust-based command-line interface for Polymarket prediction markets on Polygon blockchain.

## Overview

The Polymarket CLI enables users to:
- Browse prediction markets, events, and tags
- Place orders and manage trades
- Query on-chain data and positions
- Manage wallets and contract approvals
- Bridge assets across chains
- Interact with conditional token framework (CTF)

**Binary**: `polymarket`
**Version**: 0.1.4+
**Config**: `~/.config/polymarket/config.json`

## Global Flags

Available on ALL commands:

```bash
-o, --output <format>        # Output format: 'table' (default) or 'json'
--private-key <key>          # Override wallet private key (0x...)
--signature-type <type>      # Override signature type (eoa, proxy, gnosis-safe)
--help                       # Show help for command
```

## Authentication

Commands are divided into:
- **Read-only**: No authentication required
- **Authenticated**: Require wallet setup

### Setting Up Wallet

**Option 1: Guided Setup**
```bash
polymarket setup
```

**Option 2: Create New Wallet**
```bash
polymarket wallet create [--force] [--signature-type <type>]
```

**Option 3: Import Existing Key**
```bash
polymarket wallet import <private_key> [--force] [--signature-type <type>]
```

**Option 4: Environment Variable**
```bash
export POLYMARKET_PRIVATE_KEY="0x..."
export POLYMARKET_SIGNATURE_TYPE="proxy"  # or eoa, gnosis-safe
```

**Option 5: CLI Flag**
```bash
polymarket --private-key 0x... <command>
```

**Signature Types**:
- `proxy` (default): Polymarket proxy wallet
- `eoa`: Externally owned account (direct wallet)
- `gnosis-safe`: Gnosis Safe multisig

---

## 1. Markets Commands

Browse and search prediction markets.

### 1.1 List Markets

```bash
polymarket markets list [OPTIONS]
```

**Parameters**:
- `--active <bool>` - Filter by active status (true/false)
- `--closed <bool>` - Filter by closed status (true/false)
- `--limit <n>` - Number of results (default: 20)
- `--offset <n>` - Skip first n results (pagination)
- `--order <field>` - Sort field (created_at, end_date, volume, liquidity)
- `--ascending <bool>` - Sort direction (default: false)

**Output**: List of markets with question, outcomes, prices, volume, liquidity, end date

**Example**:
```bash
# Get top 10 active markets by volume
polymarket markets list --active true --limit 10 --order volume

# Get markets as JSON for processing
polymarket -o json markets list --limit 100
```

### 1.2 Get Market by ID

```bash
polymarket markets get <id>
```

**Parameters**:
- `<id>` - Market ID (numeric) or slug (string)

**Output**: Detailed market information including:
- Question and description
- Outcomes with token IDs
- Current prices
- Volume and liquidity metrics
- Start/end dates
- Resolution status

**Example**:
```bash
# By numeric ID
polymarket markets get 12345

# By slug
polymarket markets get "will-bitcoin-reach-100k-in-2026"
```

### 1.3 Search Markets

```bash
polymarket markets search <query> [--limit <n>]
```

**Parameters**:
- `<query>` - Search string (required)
- `--limit <n>` - Max results (default: 20)

**Output**: Markets matching search query

**Example**:
```bash
polymarket markets search "election" --limit 50
polymarket markets search "crypto bitcoin" --limit 10
```

### 1.4 Get Market Tags

```bash
polymarket markets tags <id>
```

**Parameters**:
- `<id>` - Market ID or slug

**Output**: Tags associated with the market

**Example**:
```bash
polymarket markets tags 12345
```

---

## 2. Events Commands

Events group related markets together (e.g., "2024 US Presidential Election").

### 2.1 List Events

```bash
polymarket events list [OPTIONS]
```

**Parameters**:
- `--active <bool>` - Filter by active status
- `--closed <bool>` - Filter by closed status
- `--tag <slug>` - Filter by tag slug
- `--limit <n>` - Number of results (default: 20)
- `--offset <n>` - Skip first n results
- `--order <field>` - Sort field (created_at, end_date, volume, liquidity)
- `--ascending <bool>` - Sort direction

**Output**: List of events with title, markets count, volume, liquidity

**Example**:
```bash
# Get political events
polymarket events list --tag politics --limit 20

# Get active events sorted by volume
polymarket events list --active true --order volume
```

### 2.2 Get Event by ID

```bash
polymarket events get <id>
```

**Parameters**:
- `<id>` - Event ID or slug

**Output**: Detailed event information with all associated markets

**Example**:
```bash
polymarket events get 456
polymarket events get "us-presidential-election-2024"
```

### 2.3 Get Event Tags

```bash
polymarket events tags <id>
```

**Parameters**:
- `<id>` - Event ID or slug

**Output**: Tags associated with the event

---

## 3. Tags Commands

Tags categorize markets and events (e.g., "politics", "crypto", "sports").

### 3.1 List Tags

```bash
polymarket tags list [OPTIONS]
```

**Parameters**:
- `--limit <n>` - Number of results
- `--offset <n>` - Skip first n results
- `--ascending <bool>` - Sort alphabetically

**Output**: All available tags with ID, slug, label

**Example**:
```bash
polymarket tags list --limit 100
```

### 3.2 Get Tag by ID

```bash
polymarket tags get <id>
```

**Parameters**:
- `<id>` - Tag ID or slug

**Output**: Tag details including related markets/events count

**Example**:
```bash
polymarket tags get politics
polymarket tags get 5
```

### 3.3 Get Related Tag IDs

```bash
polymarket tags related <id> [--omit-empty <bool>]
```

**Parameters**:
- `<id>` - Tag ID or slug
- `--omit-empty <bool>` - Exclude tags with no items

**Output**: Related tag IDs and their relationships

### 3.4 Get Related Tags

```bash
polymarket tags related-tags <id> [--omit-empty <bool>]
```

**Parameters**:
- `<id>` - Tag ID or slug
- `--omit-empty <bool>` - Exclude empty tags

**Output**: Full tag objects for related tags

---

## 4. Series Commands

Series are ordered sequences of related events.

### 4.1 List Series

```bash
polymarket series list [OPTIONS]
```

**Parameters**:
- `--limit <n>` - Number of results
- `--offset <n>` - Skip first n results
- `--order <field>` - Sort field
- `--ascending <bool>` - Sort direction
- `--closed <bool>` - Filter by closed status

**Output**: Available series

### 4.2 Get Series by ID

```bash
polymarket series get <id>
```

**Parameters**:
- `<id>` - Series ID

**Output**: Series details with ordered events

---

## 5. Comments Commands

User comments on markets, events, and other entities.

### 5.1 List Comments

```bash
polymarket comments list --entity-type <type> --entity-id <id> [OPTIONS]
```

**Parameters**:
- `--entity-type <type>` - Entity type (market, event, etc.)
- `--entity-id <id>` - Entity ID
- `--limit <n>` - Number of results
- `--offset <n>` - Skip first n results
- `--order <field>` - Sort field
- `--ascending <bool>` - Sort direction

**Output**: Comments for the entity

### 5.2 Get Comment by ID

```bash
polymarket comments get <id>
```

**Parameters**:
- `<id>` - Comment ID

**Output**: Single comment with content and metadata

### 5.3 Get Comments by User

```bash
polymarket comments by-user <address> [OPTIONS]
```

**Parameters**:
- `<address>` - Wallet address (0x...)
- `--limit <n>` - Number of results
- `--offset <n>` - Skip first n results

**Output**: All comments by the user

**Example**:
```bash
polymarket comments by-user 0x1234... --limit 50
```

---

## 6. Profiles Commands

Public profile information for traders.

### 6.1 Get Profile

```bash
polymarket profiles get <address>
```

**Parameters**:
- `<address>` - Wallet address (0x...)

**Output**: Public profile including:
- Username
- Avatar
- Trading statistics
- Social links

**Example**:
```bash
polymarket profiles get 0x1234567890abcdef1234567890abcdef12345678
```

---

## 7. Sports Commands

Sports-specific metadata and market types.

### 7.1 List Sports

```bash
polymarket sports list
```

**Output**: Supported sports and leagues

### 7.2 List Market Types

```bash
polymarket sports market-types
```

**Output**: Valid sports market types (moneyline, spread, over/under, etc.)

### 7.3 List Teams

```bash
polymarket sports teams [OPTIONS]
```

**Parameters**:
- `--limit <n>` - Number of results
- `--offset <n>` - Skip first n results
- `--order <field>` - Sort field
- `--ascending <bool>` - Sort direction
- `--league <name>` - Filter by league

**Output**: Sports teams and their metadata

---

## 8. Approve Commands

Manage ERC20 token approvals for trading contracts.

⚠️ **REQUIRES WALLET** - Sends on-chain transactions

### 8.1 Check Approvals

```bash
polymarket approve check [address]
```

**Parameters**:
- `[address]` - Optional wallet address (defaults to configured wallet)

**Output**: Current approval status for:
- USDC (collateral token)
- CTF Exchange (trading contract)
- Neg Risk CTF Exchange
- Neg Risk Adapter

**Example**:
```bash
# Check your approvals
polymarket approve check

# Check another address
polymarket approve check 0x1234...
```

### 8.2 Set Approvals

```bash
polymarket approve set
```

**Action**: Sends transactions to approve all required contracts for trading

**Output**: Transaction hashes and confirmation status

**Note**: Must be run before first trade. Grants unlimited approval (standard for DEX UX).

---

## 9. CLOB Commands

Central Limit Order Book - Trading, orders, balances, rewards.

### 9.1 Health & Status

#### Check API Health
```bash
polymarket clob ok
```

**Output**: API status (OK/Error)

#### Get Server Time
```bash
polymarket clob time
```

**Output**: Server timestamp

#### Check Geoblock Status
```bash
polymarket clob geoblock
```

**Output**: Whether your region is blocked

### 9.2 Pricing (Read-Only)

#### Get Price
```bash
polymarket clob price <token_id> [--side <buy|sell>]
```

**Parameters**:
- `<token_id>` - Token ID (numeric string)
- `--side <buy|sell>` - Optional: specific side price

**Output**: Current price for the token

**Example**:
```bash
polymarket clob price 123456789
polymarket clob price 123456789 --side buy
```

#### Get Batch Prices
```bash
polymarket clob batch-prices <token_ids> [--side <buy|sell>]
```

**Parameters**:
- `<token_ids>` - Comma-separated token IDs

**Example**:
```bash
polymarket clob batch-prices 12345,67890,11111
```

#### Get Midpoint Price
```bash
polymarket clob midpoint <token_id>
```

**Output**: Midpoint between bid and ask

#### Get Batch Midpoints
```bash
polymarket clob midpoints <token_ids>
```

#### Get Spread
```bash
polymarket clob spread <token_id> [--side <buy|sell>]
```

**Output**: Bid-ask spread

#### Get Batch Spreads
```bash
polymarket clob spreads <token_ids>
```

### 9.3 Order Book (Read-Only)

#### Get Order Book
```bash
polymarket clob book <token_id>
```

**Output**: Full order book with bids and asks

**Example**:
```bash
polymarket clob book 123456789
```

#### Get Multiple Order Books
```bash
polymarket clob books <token_ids>
```

### 9.4 Trade History (Read-Only)

#### Get Last Trade
```bash
polymarket clob last-trade <token_id>
```

**Output**: Most recent trade price and size

#### Get Last Trades
```bash
polymarket clob last-trades <token_ids>
```

### 9.5 Market Information (Read-Only)

#### Get CLOB Market
```bash
polymarket clob market <condition_id>
```

**Parameters**:
- `<condition_id>` - 32-byte hex condition ID (0x...)

**Output**: Market metadata from CLOB API

#### List All Markets
```bash
polymarket clob markets
```

**Output**: All tradeable markets on CLOB

#### Get Price History
```bash
polymarket clob price-history <token_id> [OPTIONS]
```

**Parameters**:
- `<token_id>` - Token ID
- `--interval <period>` - Time interval: 1m, 1h, 6h, 1d, 1w, max
- `--fidelity <n>` - Data point density

**Output**: Historical price data

**Example**:
```bash
polymarket clob price-history 123456789 --interval 1h
```

#### Get Tick Size
```bash
polymarket clob tick-size <token_id>
```

**Output**: Minimum price increment

#### Get Fee Rate
```bash
polymarket clob fee-rate <token_id>
```

**Output**: Trading fee percentage

#### Get Neg-Risk Info
```bash
polymarket clob neg-risk <token_id>
```

**Output**: Negative risk configuration

### 9.6 Order Management (Authenticated)

⚠️ **REQUIRES WALLET**

#### Create Limit Order
```bash
polymarket clob create-order --token <id> --side <buy|sell> --price <price> --size <amount> [OPTIONS]
```

**Parameters**:
- `--token <id>` - Token ID (required)
- `--side <buy|sell>` - Order side (required)
- `--price <price>` - Limit price 0.01-0.99 (required)
- `--size <amount>` - Order size in tokens (required)
- `--order-type <type>` - GTC (good-til-cancel) or FOK (fill-or-kill)
- `--post-only <bool>` - Only add liquidity (no taker trades)

**Output**: Order ID and confirmation

**Example**:
```bash
# Buy 100 shares at $0.55
polymarket clob create-order --token 123456789 --side buy --price 0.55 --size 100

# Post-only sell order
polymarket clob create-order --token 123456789 --side sell --price 0.60 --size 50 --post-only true
```

#### Create Market Order
```bash
polymarket clob market-order --token <id> --side <buy|sell> --amount <size>
```

**Parameters**:
- `--token <id>` - Token ID
- `--side <buy|sell>` - Order side
- `--amount <size>` - Amount to trade

**Output**: Fill details and average price

#### Post Multiple Orders
```bash
polymarket clob post-orders --tokens <ids> --side <buy|sell> --prices <prices> --sizes <amounts>
```

**Parameters**:
- `--tokens <ids>` - Comma-separated token IDs
- `--side <buy|sell>` - Order side (same for all)
- `--prices <prices>` - Comma-separated prices
- `--sizes <amounts>` - Comma-separated sizes

**Example**:
```bash
polymarket clob post-orders --tokens 111,222,333 --side buy --prices 0.50,0.55,0.60 --sizes 10,20,30
```

#### Cancel Single Order
```bash
polymarket clob cancel <order_id>
```

**Parameters**:
- `<order_id>` - Order ID to cancel

**Output**: Cancellation confirmation

#### Cancel Multiple Orders
```bash
polymarket clob cancel-orders <order_ids>
```

**Parameters**:
- `<order_ids>` - Comma-separated order IDs

#### Cancel Market Orders
```bash
polymarket clob cancel-market --market <condition_id>
```

**Parameters**:
- `--market <condition_id>` - Cancel all orders in this market

#### Cancel All Orders
```bash
polymarket clob cancel-all
```

**Output**: Number of orders cancelled

### 9.7 Order Viewing (Authenticated)

⚠️ **REQUIRES WALLET**

#### View Your Orders
```bash
polymarket clob orders [--market <condition_id>]
```

**Parameters**:
- `--market <condition_id>` - Optional: filter by market

**Output**: All your open orders

#### Get Order Details
```bash
polymarket clob order <order_id>
```

**Parameters**:
- `<order_id>` - Order ID

**Output**: Full order details

#### View Your Trades
```bash
polymarket clob trades
```

**Output**: Your recent trade history

### 9.8 Balance Management (Authenticated)

⚠️ **REQUIRES WALLET**

#### Check Balance
```bash
polymarket clob balance [OPTIONS]
```

**Parameters**:
- `--asset-type <type>` - Asset type: collateral or conditional
- `--token <id>` - Optional: specific token ID (for conditional)

**Output**: Your balances

**Example**:
```bash
# Check USDC balance
polymarket clob balance --asset-type collateral

# Check specific outcome token
polymarket clob balance --asset-type conditional --token 123456789
```

#### Update Balance Cache
```bash
polymarket clob update-balance --asset-type <type>
```

**Parameters**:
- `--asset-type <type>` - collateral or conditional

**Action**: Refreshes cached balance from blockchain

### 9.9 Rewards & Earnings (Authenticated)

⚠️ **REQUIRES WALLET**

#### Get Rewards
```bash
polymarket clob rewards [--date <date>]
```

**Parameters**:
- `--date <date>` - Optional: specific date (YYYY-MM-DD)

**Output**: Trading rewards summary

#### Get Earnings
```bash
polymarket clob earnings [--date <date>]
```

**Output**: Trading earnings summary

#### Get Earnings by Market
```bash
polymarket clob earnings-markets [--date <date>]
```

**Output**: Earnings broken down by market

#### Get Reward Percentages
```bash
polymarket clob reward-percentages
```

**Output**: Current reward rates

#### Get Current Rewards
```bash
polymarket clob current-rewards
```

**Output**: Your current unclaimed rewards

#### Get Market Reward
```bash
polymarket clob market-reward --market <condition_id>
```

**Parameters**:
- `--market <condition_id>` - Market condition ID

**Output**: Rewards for specific market

#### Check Order Scoring
```bash
polymarket clob order-scoring --order-id <id>
```

**Parameters**:
- `--order-id <id>` - Order ID

**Output**: Whether order earns rewards

#### Check Multiple Orders Scoring
```bash
polymarket clob orders-scoring --order-ids <ids>
```

**Parameters**:
- `--order-ids <ids>` - Comma-separated order IDs

### 9.10 API Key Management (Authenticated)

⚠️ **REQUIRES WALLET**

#### List API Keys
```bash
polymarket clob api-keys
```

**Output**: Your active API keys

#### Create API Key
```bash
polymarket clob create-api-key
```

**Output**: New API key (save securely)

#### Delete API Key
```bash
polymarket clob delete-api-key
```

**Action**: Revokes an API key

### 9.11 Account (Authenticated)

⚠️ **REQUIRES WALLET**

#### Get Account Status
```bash
polymarket clob account-status
```

**Output**: Account verification and trading status

#### Get Notifications
```bash
polymarket clob notifications
```

**Output**: Your notifications

#### Delete Notifications
```bash
polymarket clob delete-notifications
```

**Action**: Clear notifications

---

## 10. CTF Commands

Conditional Token Framework - Split, merge, and redeem outcome tokens.

⚠️ **REQUIRES WALLET** - Sends on-chain transactions

### 10.1 Split Collateral

```bash
polymarket ctf split --condition <id> --amount <amount> [OPTIONS]
```

**Parameters**:
- `--condition <id>` - Condition ID (0x...)
- `--amount <amount>` - Amount to split
- `--collateral <address>` - Collateral token (default: USDC)
- `--partition <partition>` - Partition array
- `--parent-collection <id>` - Parent collection ID

**Action**: Splits collateral into outcome tokens

**Example**:
```bash
# Split 100 USDC into outcome tokens
polymarket ctf split --condition 0xabcd... --amount 100
```

### 10.2 Merge Tokens

```bash
polymarket ctf merge --condition <id> --amount <amount> [OPTIONS]
```

**Parameters**:
- `--condition <id>` - Condition ID
- `--amount <amount>` - Amount to merge
- `--collateral <address>` - Collateral token
- `--partition <partition>` - Partition array
- `--parent-collection <id>` - Parent collection ID

**Action**: Merges outcome tokens back to collateral

**Example**:
```bash
polymarket ctf merge --condition 0xabcd... --amount 50
```

### 10.3 Redeem Winning Tokens

```bash
polymarket ctf redeem --condition <id> [OPTIONS]
```

**Parameters**:
- `--condition <id>` - Condition ID
- `--collateral <address>` - Collateral token
- `--index-sets <sets>` - Index sets to redeem
- `--parent-collection <id>` - Parent collection ID

**Action**: Redeems winning tokens after market resolution

**Example**:
```bash
polymarket ctf redeem --condition 0xabcd...
```

### 10.4 Redeem Neg-Risk

```bash
polymarket ctf redeem-neg-risk --condition <id> --amounts <amounts>
```

**Parameters**:
- `--condition <id>` - Condition ID
- `--amounts <amounts>` - Comma-separated amounts

**Action**: Redeems negative-risk positions

### 10.5 Calculate IDs

#### Calculate Condition ID
```bash
polymarket ctf condition-id --oracle <address> --question <id> --outcomes <n>
```

**Parameters**:
- `--oracle <address>` - Oracle address
- `--question <id>` - Question ID
- `--outcomes <n>` - Number of outcomes

**Output**: Calculated condition ID

#### Calculate Collection ID
```bash
polymarket ctf collection-id --condition <id> --index-set <set>
```

**Parameters**:
- `--condition <id>` - Condition ID
- `--index-set <set>` - Index set

**Output**: Calculated collection ID

#### Calculate Position ID
```bash
polymarket ctf position-id --collection <id>
```

**Parameters**:
- `--collection <id>` - Collection ID

**Output**: Calculated position ID (token ID)

---

## 11. Data Commands

On-chain data queries for positions, trades, and analytics.

### 11.1 Positions

#### Get Open Positions
```bash
polymarket data positions <address> [OPTIONS]
```

**Parameters**:
- `<address>` - Wallet address
- `--limit <n>` - Number of results
- `--offset <n>` - Skip first n results

**Output**: All open positions with current value

**Example**:
```bash
polymarket data positions 0x1234... --limit 50
```

#### Get Closed Positions
```bash
polymarket data closed-positions <address> [OPTIONS]
```

**Parameters**:
- `<address>` - Wallet address
- `--limit <n>` - Number of results
- `--offset <n>` - Skip first n results

**Output**: Historical closed positions with P&L

### 11.2 Portfolio Value

```bash
polymarket data value <address>
```

**Parameters**:
- `<address>` - Wallet address

**Output**: Total portfolio value in USD

### 11.3 Trading Statistics

#### Markets Traded Count
```bash
polymarket data traded <address>
```

**Parameters**:
- `<address>` - Wallet address

**Output**: Number of unique markets traded

#### Trade History
```bash
polymarket data trades <address> [OPTIONS]
```

**Parameters**:
- `<address>` - Wallet address
- `--limit <n>` - Number of results
- `--offset <n>` - Skip first n results

**Output**: Detailed trade history

### 11.4 On-Chain Activity

```bash
polymarket data activity <address> [OPTIONS]
```

**Parameters**:
- `<address>` - Wallet address
- `--limit <n>` - Number of results
- `--offset <n>` - Skip first n results

**Output**: All on-chain transactions (splits, merges, redeems, transfers)

### 11.5 Market Analytics

#### Token Holders
```bash
polymarket data holders <condition_id> [OPTIONS]
```

**Parameters**:
- `<condition_id>` - Condition ID (0x...)
- `--limit <n>` - Number of results
- `--offset <n>` - Skip first n results

**Output**: All addresses holding positions in the market

#### Open Interest
```bash
polymarket data open-interest <condition_id> [OPTIONS]
```

**Parameters**:
- `<condition_id>` - Condition ID
- `--limit <n>` - Number of results
- `--offset <n>` - Skip first n results

**Output**: Total open interest per outcome

#### Event Volume
```bash
polymarket data volume <event_id> [OPTIONS]
```

**Parameters**:
- `<event_id>` - Event ID
- `--period <period>` - Time period
- `--limit <n>` - Number of results
- `--offset <n>` - Skip first n results

**Output**: Trading volume over time

### 11.6 Leaderboards

#### Trader Leaderboard
```bash
polymarket data leaderboard [OPTIONS]
```

**Parameters**:
- `--period <period>` - Time period: month, week, day
- `--order-by <metric>` - Metric: pnl, volume
- `--limit <n>` - Number of results (default: 100)

**Output**: Top traders by P&L or volume

**Example**:
```bash
polymarket data leaderboard --period month --order-by pnl --limit 20
```

#### Builder Leaderboard
```bash
polymarket data builder-leaderboard [OPTIONS]
```

**Parameters**:
- `--period <period>` - Time period
- `--limit <n>` - Number of results

**Output**: Top market builders

#### Builder Volume
```bash
polymarket data builder-volume [OPTIONS]
```

**Parameters**:
- `--period <period>` - Time period
- `--limit <n>` - Number of results

**Output**: Volume by builder

---

## 12. Bridge Commands

Cross-chain asset deposits to Polygon.

### 12.1 Get Deposit Address

```bash
polymarket bridge deposit <address>
```

**Parameters**:
- `<address>` - Your Polygon wallet address

**Output**: Deposit addresses for:
- EVM chains (Ethereum, Base, Arbitrum, etc.)
- Solana
- Bitcoin

**Example**:
```bash
polymarket bridge deposit 0x1234...
```

**Usage**: Send USDC to the provided address to bridge to Polygon

### 12.2 List Supported Assets

```bash
polymarket bridge supported-assets
```

**Output**: All supported chains and tokens for bridging

### 12.3 Check Deposit Status

```bash
polymarket bridge status <address>
```

**Parameters**:
- `<address>` - Your wallet address

**Output**: Pending and completed deposits

---

## 13. Wallet Commands

Wallet creation, management, and configuration.

### 13.1 Create Wallet

```bash
polymarket wallet create [OPTIONS]
```

**Parameters**:
- `--force` - Overwrite existing wallet
- `--signature-type <type>` - Signature type (proxy, eoa, gnosis-safe)

**Action**: Generates new random private key and saves to config

**Output**: New wallet address

**Example**:
```bash
polymarket wallet create
polymarket wallet create --signature-type eoa
```

### 13.2 Import Wallet

```bash
polymarket wallet import <private_key> [OPTIONS]
```

**Parameters**:
- `<private_key>` - Private key to import (0x... or raw hex)
- `--force` - Overwrite existing wallet
- `--signature-type <type>` - Signature type

**Action**: Imports existing key and saves to config

**Example**:
```bash
polymarket wallet import 0xabcdef123456... --signature-type proxy
```

### 13.3 Show Wallet Address

```bash
polymarket wallet address
```

**Output**: Current wallet address

### 13.4 Show Wallet Info

```bash
polymarket wallet show
```

**Output**:
- Wallet address
- Config file path
- Key source (config/env/flag)
- Signature type

### 13.5 Reset Configuration

```bash
polymarket wallet reset [--force]
```

**Parameters**:
- `--force` - Skip confirmation prompt

**Action**: Deletes config file and all saved settings

**Warning**: Cannot be undone

---

## 14. Utility Commands

### 14.1 Setup Wizard

```bash
polymarket setup
```

**Action**: Interactive guided setup for first-time users
- Creates or imports wallet
- Sets signature type
- Explains approval process

### 14.2 Interactive Shell

```bash
polymarket shell
```

**Action**: Launches interactive REPL shell
- Run commands without "polymarket" prefix
- Command history
- Tab completion
- Type `exit` or `quit` to leave

**Example**:
```bash
$ polymarket shell
polymarket> markets list --limit 5
polymarket> clob price 123456789
polymarket> exit
```

### 14.3 API Status

```bash
polymarket status
```

**Output**: Health status of all APIs:
- Gamma API (markets, events)
- CLOB API (trading)
- Data API (on-chain data)
- Bridge API

### 14.4 Upgrade

```bash
polymarket upgrade
```

**Action**: Checks for and installs latest version

### 14.5 Version

```bash
polymarket --version
```

**Output**: Current version number

---

## Output Formats

All commands support two output formats:

### Table Format (Default)

Human-readable formatted tables:

```bash
polymarket markets list --limit 3

┌────────┬───────────────────────────────┬────────────┬──────────┬────────────┐
│ ID     │ Question                      │ Volume     │ Liquidity│ End Date   │
├────────┼───────────────────────────────┼────────────┼──────────┼────────────┤
│ 123456 │ Will Bitcoin reach $100k?     │ $1.2M      │ $45K     │ 2026-12-31 │
│ 123457 │ Will Trump win 2024?          │ $5.8M      │ $120K    │ 2024-11-05 │
│ 123458 │ Will ETH flip BTC?            │ $890K      │ $30K     │ 2027-01-01 │
└────────┴───────────────────────────────┴────────────┴──────────┴────────────┘
```

### JSON Format

Machine-readable JSON for scripting:

```bash
polymarket -o json markets list --limit 2

[
  {
    "id": "123456",
    "question": "Will Bitcoin reach $100k in 2026?",
    "outcomes": ["Yes", "No"],
    "volume": "1234567.89",
    "liquidity": "45000.00",
    "end_date": "2026-12-31T23:59:59Z"
  },
  {
    "id": "123457",
    "question": "Will Trump win 2024 election?",
    "outcomes": ["Yes", "No"],
    "volume": "5876543.21",
    "liquidity": "120000.00",
    "end_date": "2024-11-05T23:59:59Z"
  }
]
```

## Scripting Examples

### Example 1: Find High-Volume Markets

```bash
# Get top 50 markets by volume in JSON
polymarket -o json markets list --limit 50 --order volume | \
  jq '.[] | select(.volume | tonumber > 1000000) | {question, volume, id}'
```

### Example 2: Monitor Your Positions

```bash
# Get your open positions and total value
WALLET="0x1234..."
polymarket -o json data positions $WALLET | jq '.[].market'
polymarket data value $WALLET
```

### Example 3: Check Prices for Multiple Tokens

```bash
# Get current prices for watchlist
TOKENS="123456,234567,345678"
polymarket -o json clob batch-prices $TOKENS | \
  jq '.[] | {token_id, price}'
```

### Example 4: Place Multiple Orders

```bash
# Place orders on multiple outcomes
polymarket clob post-orders \
  --tokens 111111,222222,333333 \
  --side buy \
  --prices 0.45,0.50,0.55 \
  --sizes 100,150,200
```

### Example 5: Daily Portfolio Check

```bash
#!/bin/bash
# Daily portfolio snapshot

WALLET="0x1234..."

echo "=== Portfolio Value ==="
polymarket data value $WALLET

echo -e "\n=== Open Positions ==="
polymarket data positions $WALLET --limit 20

echo -e "\n=== Open Orders ==="
polymarket clob orders

echo -e "\n=== Today's Trades ==="
polymarket clob trades
```

### Example 6: Market Research

```bash
# Research a topic
polymarket markets search "AI artificial intelligence" --limit 20 | \
  tee markets.txt

# Get details on specific market
polymarket -o json markets get 123456 > market_details.json

# Check order book depth
polymarket clob book 123456789
```

---

## Common Workflows

### Workflow 1: First-Time Setup

```bash
# 1. Run setup wizard
polymarket setup

# 2. Or manually create wallet
polymarket wallet create --signature-type proxy

# 3. Check wallet
polymarket wallet show

# 4. Approve contracts (required before first trade)
polymarket approve set

# 5. Check approvals
polymarket approve check

# 6. Add funds (bridge USDC to Polygon)
polymarket bridge deposit $(polymarket wallet address)
```

### Workflow 2: Browse Markets

```bash
# List active markets
polymarket markets list --active true --limit 20

# Search specific topic
polymarket markets search "election"

# Get market details
polymarket markets get 123456

# Check order book
polymarket clob book <token_id>

# Check price history
polymarket clob price-history <token_id> --interval 1h
```

### Workflow 3: Place Trade

```bash
# 1. Find market
polymarket markets search "bitcoin"

# 2. Get market details (find token IDs for outcomes)
polymarket markets get 123456

# 3. Check current price
polymarket clob price 123456789

# 4. Check order book depth
polymarket clob book 123456789

# 5. Place limit order
polymarket clob create-order \
  --token 123456789 \
  --side buy \
  --price 0.55 \
  --size 100

# 6. Check order status
polymarket clob orders

# 7. Cancel if needed
polymarket clob cancel <order_id>
```

### Workflow 4: Monitor Portfolio

```bash
# Check total value
polymarket data value $(polymarket wallet address)

# View open positions
polymarket data positions $(polymarket wallet address)

# Check USDC balance
polymarket clob balance --asset-type collateral

# View recent trades
polymarket clob trades

# Check rewards
polymarket clob current-rewards
```

### Workflow 5: Close Position

```bash
# 1. Check current positions
polymarket data positions $(polymarket wallet address)

# 2. Get token ID from position

# 3. Check current price
polymarket clob price <token_id>

# 4. Place sell order
polymarket clob create-order \
  --token <token_id> \
  --side sell \
  --price 0.65 \
  --size 50

# OR market sell for instant fill
polymarket clob market-order \
  --token <token_id> \
  --side sell \
  --amount 50
```

### Workflow 6: After Market Resolution

```bash
# 1. Check closed positions
polymarket data closed-positions $(polymarket wallet address)

# 2. Redeem winning tokens
polymarket ctf redeem --condition <condition_id>

# 3. Check updated balance
polymarket clob balance --asset-type collateral
```

---

## Error Handling

### Common Errors

**"No wallet configured"**
- Run `polymarket setup` or `polymarket wallet create`
- Or use `--private-key` flag

**"Insufficient balance"**
- Check balance: `polymarket clob balance --asset-type collateral`
- Bridge funds: `polymarket bridge deposit <address>`

**"Not approved"**
- Run `polymarket approve set`
- Check status: `polymarket approve check`

**"Invalid token ID"**
- Token IDs are numeric strings (e.g., "123456789")
- Get from market details: `polymarket markets get <market_id>`

**"Price out of range"**
- Prices must be between 0.01 and 0.99
- Respect tick size: `polymarket clob tick-size <token_id>`

**"Order size too small"**
- Check minimum order size for market
- Typically $10-20 minimum

**"Geoblock"**
- Your region may be restricted
- Check: `polymarket clob geoblock`

---

## Advanced Features

### API Key Authentication

For programmatic access with API keys:

```bash
# Create API key
polymarket clob create-api-key

# List keys
polymarket clob api-keys

# Use in scripts with SDK
# (keys stored by CLOB, not in CLI config)
```

### Signature Types

**Proxy Wallet (Default)**:
- Polymarket's account abstraction
- Gas-efficient
- Recommended for most users

**EOA (Externally Owned Account)**:
- Direct wallet control
- Standard Ethereum wallet
- Higher gas costs

**Gnosis Safe**:
- Multisig wallet
- Enterprise/team trading
- Requires Safe setup

### Neg-Risk Markets

Special market type with different redemption:

```bash
# Check if market is neg-risk
polymarket clob neg-risk <token_id>

# Redeem neg-risk position
polymarket ctf redeem-neg-risk --condition <id> --amounts 100,0
```

### Conditional Token Math

```bash
# Calculate IDs for custom conditions
polymarket ctf condition-id \
  --oracle 0xOracle... \
  --question 0xQuestion... \
  --outcomes 2

# Calculate position ID
polymarket ctf collection-id --condition 0x... --index-set 1
polymarket ctf position-id --collection 0x...
```

---

## Tips & Best Practices

### Trading Tips

1. **Always check order book first** before placing orders
2. **Use limit orders** for better prices (avoid market orders unless urgent)
3. **Enable post-only** when adding liquidity to earn maker rebates
4. **Monitor tick size** - some markets have larger minimum increments
5. **Check fee rates** before large trades
6. **Set price alerts** by checking periodically with JSON output

### Portfolio Management

1. **Check positions daily**: `polymarket data positions $(polymarket wallet address)`
2. **Monitor total value**: `polymarket data value $(polymarket wallet address)`
3. **Track rewards**: `polymarket clob rewards`
4. **Review trades**: `polymarket clob trades` to analyze performance
5. **Export to JSON** for external analysis

### Security

1. **Protect private keys** - never share or commit to git
2. **Use environment variables** for scripts: `export POLYMARKET_PRIVATE_KEY=0x...`
3. **Review approvals** periodically: `polymarket approve check`
4. **Keep CLI updated**: `polymarket upgrade`
5. **Backup config** at `~/.config/polymarket/config.json`

### Automation

1. **Use JSON output** for all scripts: `-o json`
2. **Combine with jq** for parsing: `polymarket -o json markets list | jq`
3. **Check errors** with `$?` exit codes
4. **Log trades** to files for records
5. **Use shell mode** for interactive sessions: `polymarket shell`

---

## Configuration Reference

### Config File Location

`~/.config/polymarket/config.json`

### Config Structure

```json
{
  "private_key": "0x...",
  "chain_id": 137,
  "signature_type": "proxy"
}
```

### Environment Variables

```bash
POLYMARKET_PRIVATE_KEY=0x...           # Private key
POLYMARKET_SIGNATURE_TYPE=proxy        # Signature type
```

### Priority Order (Authentication)

1. CLI flag `--private-key`
2. Environment variable `POLYMARKET_PRIVATE_KEY`
3. Config file `~/.config/polymarket/config.json`

### Priority Order (Signature Type)

1. CLI flag `--signature-type`
2. Environment variable `POLYMARKET_SIGNATURE_TYPE`
3. Config file
4. Default: `proxy`

---

## Resources

### Official Links

- **Repository**: https://github.com/Polymarket/polymarket-cli
- **Polymarket**: https://polymarket.com
- **Documentation**: README.md in repository
- **API Docs**: https://docs.polymarket.com

### Network Details

- **Chain**: Polygon (Chain ID 137)
- **RPC**: https://polygon.drpc.org
- **Collateral**: USDC (0x2791Bca1f2de4661ED88A30C99A7a9449Aa84174)

### Installation

**Homebrew (macOS/Linux)**:
```bash
brew tap Polymarket/polymarket-cli
brew install polymarket-cli
```

**Shell Script**:
```bash
curl -fsSL https://raw.githubusercontent.com/Polymarket/polymarket-cli/main/install.sh | bash
```

**From Source**:
```bash
git clone https://github.com/Polymarket/polymarket-cli.git
cd polymarket-cli
cargo install --path .
```

### Getting Help

```bash
# General help
polymarket --help

# Command-specific help
polymarket markets --help
polymarket clob create-order --help

# Version
polymarket --version

# API status
polymarket status
```

---

## Summary

The Polymarket CLI provides comprehensive access to:
- **15 command groups** with 80+ subcommands
- **Market browsing** and search
- **Trading** via CLOB (limit orders, market orders, cancellations)
- **Portfolio tracking** (positions, value, P&L)
- **On-chain operations** (split, merge, redeem tokens)
- **Cross-chain bridging** (deposit assets)
- **Rewards & earnings** tracking
- **Dual output formats** (table, JSON)
- **Interactive shell** mode
- **Flexible authentication** (config, env var, flag)

Perfect for traders, market makers, researchers, and automated strategies.
