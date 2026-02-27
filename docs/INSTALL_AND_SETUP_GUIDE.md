# BESC White-Label Bridge: Server Install + Setup Guide

This guide is for token teams deploying the white-label bridge on their own server.

Note:
- full source code access is private
- for access, contact `support@besc.services`, `safudev0702` (Twitter/X or Telegram), `BESCLLC` (Twitter/X), or `CryptoKingdom (@CCCK78Youtube)` (Twitter/X or Telegram)

It covers:
- one-command install
- setup wizard completion
- post-setup finalize
- first production validation

## 1. What You Are Deploying

This white-label bridge deploys:
- frontend bridge UI
- backend API + scanners + queues
- setup wizard

Topology:
- frontend and backend run on the same server
- frontend default port: `3001`
- backend default port: `3000`
- nginx can proxy both under your final domain after finalize

Bridge model:
- one external chain (`ETH/BSC/POL/ARB/OP/custom EVM` or `Solana`)
- internal chain is always `BESC Hyperchain`
- two token lanes:
  - `primary` (backend token code `BESC`)
  - `secondary` (backend token code `MONEY`)

Execution modes:
- `vault` = lock/release
- `mint` = burn/mint

Gas model:
- BESC automated releases use fixed gas price: `1000 gwei`
- external EVM transactions use chain-native gas estimation
- Solana uses native Solana network fees

## 2. Prerequisites

Required:
- Linux server (Ubuntu/Debian recommended)
- Node.js `18+` (Node 20 LTS recommended)
- npm
- internet access for npm package download

Recommended:
- 2 vCPU / 4 GB RAM minimum
- PostgreSQL (production recommended)
- Redis (required for queue/scanner reliability)
- domain pointed to the server

Optional (auto-configured when possible by finalize flow):
- nginx
- certbot

## 3. One-Command Install

From repo root:

```bash
npm run setup:server
```

What this does:
1. installs frontend dependencies
2. installs backend dependencies
3. generates bootstrap defaults (including API key for wizard default)
4. builds frontend
5. initializes DB schema if `PG_CONNECTION_STRING` already exists
6. starts/reloads PM2 apps (unless `SKIP_PM2=1`)
7. prints wizard and setup status URLs

Generated bootstrap key file:
- `besc-bridge-backend/config/bootstrap.defaults.json`
- file is ignored by git and used to prefill wizard API key

If you want to inspect before PM2 start:

```bash
SKIP_PM2=1 npm run setup:server
```

If apt package auto-install should be disabled:

```bash
AUTO_INSTALL_SYSTEM_DEPS=0 npm run setup:server
```

If you need custom ports:

```bash
FRONTEND_PORT=8080 BACKEND_PORT=4000 npm run setup:server
```

## 4. Setup Wizard Flow

After install, open the printed wizard URL and complete all steps.

Important:
- API key is auto-generated during install and auto-filled in wizard.
- You can rotate that key in wizard if desired.
- Decimals must match real deployed token decimals exactly.

Use the field-by-field reference:
- `docs/WIZARD_INPUT_REFERENCE.md`

## 5. Finalize After Wizard Save

After wizard save succeeds, run:

```bash
npm run setup:finalize
```

What finalize does:
1. ensures DB schema exists
2. reloads PM2 with generated runtime env
3. configures nginx from wizard `domain` + `backendUrl` (default enabled)
4. optionally issues SSL certs with certbot
5. saves PM2 process list

Enable automatic SSL issuance:

```bash
CERTBOT_EMAIL=ops@yourdomain.com npm run setup:finalize
```

Skip nginx/certbot:

```bash
CONFIGURE_NGINX=0 npm run setup:finalize
```

## 6. Verify Deployment

Check PM2:

```bash
npm run pm2:status
```

Check backend setup status:

```bash
curl -s http://127.0.0.1:3000/setup/status | jq
```

Expected:
- `"configured": true`

If nginx is configured, verify public domain:
- frontend loads
- wallet connect works
- bridge form validates and submits

## 7. Required Runtime Components

For production quality:
- Redis reachable from backend
- PostgreSQL reachable from backend
- stable RPC endpoints for BESC + external chain (or Solana RPC)

If DB/Redis are missing, bridge reliability degrades:
- queue processing and scanner persistence can fail
- resume/restart behavior becomes unsafe

## 8. First Bridge Validation (Required)

Before public launch, run a controlled dry run:
1. small amount test: external -> BESC (primary)
2. small amount test: BESC -> external (primary)
3. small amount test: external -> BESC (secondary)
4. small amount test: BESC -> external (secondary)
5. if Solana enabled, test both Solana directions for both tokens

For each test:
- verify source tx succeeded
- verify backend accepted initiate/redeem
- verify destination release/mint completed
- verify received raw amount is correct per decimals and configured fee

Use:
- bridge UI status
- backend transaction API (`/transaction/:txHash`)
- PM2 logs

Detailed launch checklist:
- `docs/GO_LIVE_CHECKLIST.md`

## 9. API and Security Notes

- `x-api-key` is required for bridge API calls from frontend.
- In this product, API key is an app-level request gate, not a private secret.
- Protect setup overwrite by setting `SETUP_SECRET` on server and using `x-setup-secret`.
- Never share signer private keys.
- Restrict server access, firewall ports, and keep OS/packages updated.

## 10. Common Failures + Fixes

### Setup wizard cannot save
- Check required fields and format errors in wizard.
- Confirm URLs are valid `http(s)`.
- Confirm EVM addresses are `0x` + 40 hex chars.
- Confirm Solana addresses are valid base58 when Solana route enabled.

### PM2 fails to start
- Verify Node/npm versions.
- Re-run install:
  - `npm run setup:server`
- Check PM2 logs:
  - `npm run pm2:logs`

### API returns Unauthorized / Invalid API key
- Confirm `backend.apiKey` in wizard matches runtime.
- Re-save setup and run:
  - `npm run setup:finalize`

### Bridge rejects amount due decimals
- Token decimals in wizard do not match deployed token contract/mint.
- Correct decimals in wizard, save, finalize, retest.

### Vault mode fails with signer mismatch
- In BESC vault mode, BESC signer addresses must match `SIGNER_PRIVATE_KEY` wallet.
- Fix signer addresses/private key alignment in wizard and re-save.

## 11. Operational Commands

Install/bootstrap:

```bash
npm run setup:server
```

Finalize after wizard:

```bash
npm run setup:finalize
```

PM2 status:

```bash
npm run pm2:status
```

PM2 logs:

```bash
npm run pm2:logs
```
