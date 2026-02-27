# Setup Wizard Input Reference

This is the authoritative field reference for the white-label setup wizard.

Access note:
- full installer and source repository access is private
- request access via `support@besc.services`, `safudev0702` (Twitter/X or Telegram), `BESCLLC` (Twitter/X), or `CryptoKingdom (@CCCK78Youtube)` (Twitter/X or Telegram)

Use this document while filling the wizard so setup succeeds on first attempt.

## Conventions

- `Required`: must be provided for successful setup.
- `Format`: exact expected shape.
- `Example`: valid example value.
- `Notes`: behavior and constraints.

## Step 1: Brand

### Project Name
- Required: Yes
- Format: non-empty text
- Example: `MyToken Bridge`
- Notes: shown in UI, metadata, and branding preview.

### Domain
- Required: No (but strongly recommended for production)
- Format: hostname only (no path)
- Example: `bridge.mytoken.io`
- Notes: used by nginx finalize automation.

### Support Email
- Required: No
- Format: valid email
- Example: `support@mytoken.io`

### Bridge Fee (%)
- Required: Yes
- Format: number in `[0, 100)`
- Example: `0.1`
- Notes:
  - `0.1` means `0.1%`
  - applied in external-source fee logic where configured by route/mode.

### Logo URL
- Required: No
- Format: full `http(s)` URL
- Example: `https://cdn.mytoken.io/logo.png`
- Notes: wizard validates URL and shows live preview fallback if load fails.

### Primary Color / Secondary Color / Background Color
- Required: Yes
- Format: hex color
- Example: `#3b82f6`
- Notes: used by theme variables.

### Links: Website / Docs / Support / Twitter
- Required: No
- Format: full `http(s)` URL
- Example: `https://docs.mytoken.io`

### Primary Token Symbol
- Required: Yes
- Format: 1-16 uppercase letters/numbers
- Example: `MYT`
- Notes: maps to backend token lane `BESC` (primary lane).

### Secondary Token Symbol
- Required: Yes
- Format: 1-16 uppercase letters/numbers
- Example: `MYUSD`
- Notes: maps to backend token lane `MONEY` (secondary lane).

## Step 2: Chains

### External Chain Preset
- Required: Yes
- Allowed: `Ethereum`, `BSC`, `Polygon`, `Arbitrum`, `Optimism`, `Solana`, `Custom EVM`
- Notes: determines chain type and default hints.

### External Chain Type
- Required: Yes
- Auto-filled: `evm` or `solana`
- Notes: read-only.

### External Chain Name
- Required: Yes
- Format: text
- Example: `Ethereum`

### External Chain Key
- Required: Yes
- Format: short uppercase code
- Example: `ETH`, `BSC`, `POL`, `SOL`, `EXT`

### External Chain ID (EVM only)
- Required: Yes for EVM
- Format: positive integer
- Example: `1`

### External Native Symbol
- Required: Yes
- Format: uppercase symbol text
- Example: `ETH`

### External RPC URL (EVM only)
- Required: Yes for EVM
- Format: full `http(s)` URL
- Example: `https://mainnet.infura.io/v3/<key>`

### External Explorer URL
- Required: No
- Format: full `http(s)` URL
- Example: `https://etherscan.io`

### Home Side Mode
- Required: Yes
- Allowed: `vault` or `mint`
- Notes:
  - `vault`: external chain lock/release
  - `mint`: external chain burn/mint

### BESC RPC URL
- Required: Yes
- Format: full `http(s)` URL
- Example: `https://rpc.beschyperchain.com`

### BESC Chain ID
- Required: Yes
- Format: positive integer
- Example: `2372`

### BESC Chain Name
- Required: Yes (practically)
- Format: text
- Example: `BESC Hyperchain`

### BESC Explorer URL
- Required: No
- Format: full `http(s)` URL
- Example: `https://explorer.beschyperchain.com`

### BESC Side Mode
- Required: Yes
- Allowed: `vault` or `mint`
- Notes:
  - `vault`: BESC lock/release
  - `mint`: BESC burn/mint

Gas note:
- BESC release queue uses fixed gas price `1000 gwei`.
- External EVM chains use native estimation.

## Step 3: Contracts + Decimals

### External Wrapped Primary Token (EVM only)
- Required: Yes for EVM external
- Format: EVM address (`0x` + 40 hex chars)
- Example: `0x1234...abcd`

### External Wrapped Secondary Token (EVM only)
- Required: Yes for EVM external
- Format: EVM address

### Primary External Decimals (EVM only)
- Required: Yes for EVM external
- Format: integer `0..30`
- Example: `18`

### Secondary External Decimals (EVM only)
- Required: Yes for EVM external
- Format: integer `0..30`

### Mint Controller Address
- Required: No
- Format: EVM address
- Notes: optional legacy/native BESC fallback path.

### BESC Primary Token
- Required: Yes
- Format: EVM address

### BESC Primary Signer
- Required: Yes
- Format: EVM address
- Notes: if BESC side is `vault`, must match signer private key wallet.

### Primary BESC Decimals
- Required: Yes
- Format: integer `0..30`

### BESC Secondary Token
- Required: Yes
- Format: EVM address

### BESC Secondary Signer
- Required: Yes
- Format: EVM address
- Notes: if BESC side is `vault`, must match signer private key wallet.

### Secondary BESC Decimals
- Required: Yes
- Format: integer `0..30`

Critical decimals rule:
- Decimals must match deployed token contracts/mints exactly.
- Non-exact cross-decimal conversions are rejected to prevent over/under mint.

## Step 4: Backend + Infrastructure

### Frontend Backend URL
- Required: Yes
- Format: full `http(s)` URL
- Example: `https://bridge.mytoken.io`
- Notes: same-server domain is recommended.

### Public API Key
- Required: Yes
- Format: text key
- Default: auto-generated during install
- Notes:
  - used by frontend in `x-api-key` header
  - can be rotated before save.

### CORS Origins
- Required: No (recommended)
- Format: comma-separated `http(s)` origins
- Example: `https://bridge.mytoken.io,https://app.mytoken.io`

### Backend Port
- Required: No
- Format: integer `1..65535`
- Example: `3000`

### Form Access Key
- Required: No
- Format: text
- Notes: optional lead form integration key.

### Signer Private Key
- Required: Yes
- Format: `0x` + 64 hex chars
- Notes:
  - bridge execution signer key
  - keep private and never share.

### Postgres Connection String
- Required: Optional in wizard, recommended for production
- Format: `postgres://...` or `postgresql://...`

### Redis URL
- Required: Optional in wizard, required for production reliability
- Format: `redis://...` or `rediss://...`

## Step 5: Solana + ABI

### Enable Solana Route
- Required: context-dependent
- Notes:
  - forced `Yes` if external preset is Solana
  - optional `Yes/No` otherwise.

### Solana RPC URL
- Required: Yes if Solana enabled
- Format: full `http(s)` URL
- Example: `https://api.mainnet-beta.solana.com`

### Solana Primary Mint
- Required: Yes if Solana enabled
- Format: valid Solana address

### Solana Secondary Mint
- Required: Yes if Solana enabled
- Format: valid Solana address

### Primary Solana Decimals
- Required: Yes
- Format: integer `0..30`

### Secondary Solana Decimals
- Required: Yes
- Format: integer `0..30`

### Solana Bridge Wallet
- Required: Yes if Solana enabled
- Format: valid Solana address
- Notes: receives deposits and sends releases.

### Solana Program ID
- Required: No
- Format: valid Solana address when provided

### Solana Bridge Private Key
- Required: Yes if Solana enabled
- Format: one of:
  - base58 secret key
  - JSON keypair array
  - `0x` hex secret bytes
- Notes: must correspond to configured bridge wallet.

### ABI Fields (Primary Wrapped / Secondary Wrapped / Primary Hub / Secondary Hub)
- Required: No
- Format: valid JSON that parses to an ABI array
- Notes: leave blank unless custom ABI override is required.

## Validation Behavior Summary

Client-side wizard validation:
- step-based inline errors
- blocks moving to next step when current step invalid
- blocks save if any global validation errors remain

Backend validation:
- repeats critical validation server-side
- returns explicit error list if payload invalid
- enforces signer alignment checks for BESC vault mode

## Quick Data Collection Checklist (Before Wizard)

Collect before starting:
- brand name, domain, logo URL, support email
- external chain preset and RPC/explorer
- BESC RPC/explorer
- both side execution mode decisions (`vault` vs `mint`)
- token contract addresses (both lanes)
- signer private key and signer addresses
- exact decimals for each lane on each chain
- Redis URL, Postgres URL
- Solana wallet/mints/private key (if Solana route enabled)

If you prepare this list first, wizard completion is typically one pass with no retries.
