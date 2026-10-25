# Draft only: isolated Onyx GNOT support on historical Keplr

> **Not posted.** This file is prepared for parent review only. It is not an upstream issue, pull request, or release claim.

## Proposal

Add a narrowly isolated Chromium-extension integration for Onyx (`onyx-1`) native GNOT transfers on the pinned historical Osmosis Keplr baseline.

The integration would reuse the existing Keplr permission, approval, key custody, and signing boundaries. It would derive and validate the Gno address from the wallet-controlled public key, query GNOT balance and account sequence, build exact Gno/TM2-compatible sign bytes and transaction envelopes, show an explicit human-readable review, broadcast only through a bounded Onyx endpoint, and query confirmation.

## Guardrails

- Onyx-only; no mainnet signing or broadcast.
- Native GNOT only; no realms, GnoConnect, custom tokens, swaps, staking, mobile, Ledger, or other hardware wallets.
- Exact integer amount and fee handling; no floating-point conversion.
- Network and recipient validation immediately before signing and broadcast.
- Unsupported account types fail closed.
- Existing Cosmos message validation and signing behavior remain unchanged.
- No new key store, signer, custody service, or seed-export path.
- No runtime broadcast in the implementation phase; parent-controlled testnet evidence is a separate gate.

## Current status

The historical baseline is not buildable on the available host yet. The standard frozen-lockfile install fails while compiling the pinned `deasync` dependency under Node 26.7.0. Continuing with skipped lifecycle scripts reaches a second blocker: the proto-types build requires `protoc`, which is not available on PATH.

No implementation or testnet evidence should be inferred from this draft. The proposal should be reconsidered only after the pinned baseline install, bootstrap, build, existing tests, production extension build, and exact-head review all pass.

## Source and license boundary

The work must use original Apache-compatible code and independently verified official Gno SDK interfaces. The Adena reference fork is behavior/interface reference only; its GPL implementation must not be copied, translated, or adapted.

## Acceptance evidence required before any upstream discussion

1. Reproducible build of the exact pinned baseline and feature branch.
2. RED/GREEN production-path tests for address derivation, address validation, integer precision, account/sequence, sign bytes, envelope, network binding, fee handling, cancellation, rejection, and non-Gno regressions.
3. Actual extension build and inert-signer browser exercise showing human-readable approval details without broadcasting.
4. Independent exact-head security review.
5. Parent-controlled isolated Onyx testnet evidence with public receipts and balance-plus-fee reconciliation.
6. Documentation of mainnet/Onyx runtime-version divergence and every unsupported feature.

Until those gates are met, this file remains a local draft and must not be submitted to Osmosis, Chainapsis, Onbloc, or any other upstream project.
