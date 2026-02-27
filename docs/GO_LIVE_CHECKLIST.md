# Go-Live Checklist (Token Team)

Use this checklist before sharing bridge access publicly.

Access note:
- if your team does not have private repository access yet, contact `support@besc.services`, `safudev0702` (Twitter/X or Telegram), `BESCLLC` (Twitter/X), or `CryptoKingdom (@CCCK78Youtube)` (Twitter/X or Telegram).

## A. Server + Process Readiness

- [ ] `npm run setup:server` completed successfully
- [ ] wizard completed and saved successfully
- [ ] `npm run setup:finalize` completed successfully
- [ ] `npm run pm2:status` shows backend + frontend online
- [ ] domain resolves to server IP
- [ ] HTTPS is active (if certbot enabled)
- [ ] firewall allows expected public ports only (`80/443`)

## B. Setup Configuration Integrity

- [ ] external chain preset matches intended chain
- [ ] BESC RPC and external RPC are healthy
- [ ] BESC chain id and external chain id are correct
- [ ] both token lane contract addresses are correct
- [ ] signer private key is valid and funded for gas
- [ ] in BESC vault mode, BESC signer addresses match signer wallet
- [ ] decimals are verified against deployed contracts/mints exactly
- [ ] `homeSideMode` and `bescSideMode` match contract capabilities

## C. Data Layer Reliability

- [ ] Redis reachable from backend
- [ ] PostgreSQL reachable from backend
- [ ] DB schema initialized
- [ ] queue/scanner jobs persist correctly after restart

## D. Security + Access

- [ ] API key present and working for frontend/backend calls
- [ ] setup overwrite protection enabled with `SETUP_SECRET` (recommended)
- [ ] signer private keys are not in git or plaintext docs
- [ ] server SSH access restricted and keys managed

## E. Functional Test Matrix

Run all tests with small amounts first.

Primary token:
- [ ] external -> BESC succeeds
- [ ] BESC -> external succeeds

Secondary token:
- [ ] external -> BESC succeeds
- [ ] BESC -> external succeeds

If Solana enabled:
- [ ] Solana -> BESC (primary) succeeds
- [ ] BESC -> Solana (primary) succeeds
- [ ] Solana -> BESC (secondary) succeeds
- [ ] BESC -> Solana (secondary) succeeds

For each test:
- [ ] source tx confirmed
- [ ] backend accepted initiate/redeem
- [ ] queue processed completion
- [ ] destination amount and fee are correct
- [ ] no over/under mint behavior

## F. Observability

- [ ] backend logs readable (`npm run pm2:logs`)
- [ ] transaction status endpoint responds for completed tx
- [ ] no recurring scanner or queue errors

## G. Failure Drills

Validate expected behavior:
- [ ] invalid API key rejected
- [ ] invalid decimals input blocked in wizard
- [ ] invalid address input blocked in wizard
- [ ] non-exact decimal conversion attempt rejected safely
- [ ] restart PM2 and confirm bridge resumes cleanly

## H. Launch Decision

Go live only when:
- all sections above are complete
- all routes tested for enabled chains/modes
- no unresolved errors remain in PM2 logs

If any item fails, fix and rerun checklist before public release.
