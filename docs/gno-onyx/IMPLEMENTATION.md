# Onyx GNOT transfer prototype

## Status

**Blocked before implementation.** This branch contains no Gno runtime code and no wallet, signing, or broadcast changes. The pinned historical baseline cannot complete its documented build on the available host toolchain.

Scope authority: `n0sn0de/keplr-wallet#1`.

## Historical baseline

- Repository: `n0sn0de/keplr-wallet`
- Baseline: Osmosis Keplr `master` at `4c7e774b0b1e6864dd98f4a76e39a0825b3eb52d`
- Historical release: `v0.11.29`, December 2022
- Feature branch: `feat/gno-onyx-transfers`
- Baseline source remains unchanged apart from this blocker documentation.

This is a historical-source prototype boundary. No newer Keplr release or unrelated branch was substituted.

## Buildability receipt

Commands were run from the repository root with the checked-in Yarn 1.19.0 release (`.yarn/releases/yarn-1.19.0.cjs`).

| Check | Result | Evidence |
|---|---|---|
| `node --version` | Observed | `v26.7.0` |
| `yarn --version` | Host command unavailable | `yarn: command not found`; the checked-in Yarn release is usable through Node |
| `protoc --version` | Blocked | `protoc: command not found` |
| `node .yarn/releases/yarn-1.19.0.cjs install --frozen-lockfile` | Failed | `deasync@0.1.23` native build invokes `node-gyp@5.1.1`; Node 26 fails with `TypeError: Cannot assign to read only property 'cflags'` |
| `node .yarn/releases/yarn-1.19.0.cjs install --frozen-lockfile --ignore-scripts` | Passed only as a diagnostic fallback | Dependencies linked, but native lifecycle scripts were intentionally skipped; this is not an authoritative baseline install |
| `node .yarn/releases/yarn-1.19.0.cjs bootstrap` | Passed after the fallback install | Lerna 3.22.1 completed root bootstrap |
| `node .yarn/releases/yarn-1.19.0.cjs build` | Failed | `@keplr-wallet/proto-types` invokes `protoc`; the executable is absent |
| Existing tests | Not run | The required baseline build did not complete |
| Chromium extension build/exercise | Not run | No valid production artifact was produced |

The repository README and `packages/proto-types/proto-types-gen/scripts/proto-gen.mjs` both establish `protoc` as a required build prerequisite. The build is therefore **not established** on this host.

## Required operator action

Provide an approved historical-compatible Node toolchain that can install the pinned lockfile, and make `protoc` available to the build without modifying the system from this task. Then rerun the baseline install, bootstrap, build, and existing test commands before any Gno implementation begins.

No workaround that skips native package scripts or replaces the pinned dependency graph should be treated as baseline proof.

## Intended implementation boundary (not implemented)

If the baseline becomes buildable, the smallest proposed vertical slices are:

1. Derive and validate the Gno address from the existing wallet-controlled public key using the verified Gno rule; never infer it from a prefix substitution.
2. Add an Onyx-only network descriptor for ASCII chain ID `onyx-1`, with the final network check immediately before signing and broadcast.
3. Add account number, sequence, native GNOT balance, and fee reads through a bounded direct RPC adapter.
4. Parse exact integer amounts and fees without floating-point conversion, with explicit balance and fee reconciliation.
5. Build the Gno-compatible sign bytes and transaction envelope behind the existing Keplr permission, approval, and key-custody path.
6. Render recipient, amount, fee, memo, account number, sequence, and network in the approval review; reject cancellation and malformed or mismatched wallet results.
7. Add a bounded broadcast and confirmation query path, but do not execute runtime broadcast during this phase.
8. Fail closed for unsupported account types and every non-Onyx network; preserve existing Cosmos message validation and regressions.

Excluded: GnoConnect, realms, custom tokens, swaps, staking, mobile, Ledger, other hardware wallets, new key stores, seed export, mainnet signing, and upstream submission.

## Change map

No production source, dependency manifest, lockfile, generated artifact, or existing test was changed. This branch currently adds documentation only:

- `docs/gno-onyx/IMPLEMENTATION.md` — blocked implementation and build receipt.
- `docs/gno-onyx/UPSTREAM-PROPOSAL-DRAFT.md` — unposted proposal draft.

## Provenance and licensing

- Baseline repository code is Apache-2.0 according to the pinned repository metadata.
- No Gno SDK dependency was added.
- The Adena fork was not copied, translated, adapted, or imported; its GPL-covered implementation remains outside this change.
- No new key-management or signing dependency was introduced.

## Testnet and runtime evidence

**Pending.** No extension artifact was produced or exercised. No account was created or imported. No wallet material was read or generated. No signing, broadcast, faucet action, or testnet transaction was performed. Runtime Onyx evidence must be collected by the parent only after the candidate builds and passes independent review.

## Reproduction

From the checked-out pinned repository:

```sh
node .yarn/releases/yarn-1.19.0.cjs install --frozen-lockfile
node .yarn/releases/yarn-1.19.0.cjs bootstrap
node .yarn/releases/yarn-1.19.0.cjs build
node .yarn/releases/yarn-1.19.0.cjs test
```

The first command currently stops at the historical `deasync` native build under Node 26.7.0. If an operator uses `--ignore-scripts` only to continue diagnosis, the build then stops at the missing `protoc` executable in `@keplr-wallet/proto-types`.

## Readiness statement

This branch is not a reviewed-ready implementation candidate. It is a precise, reviewable setup-blocker report at the authorized historical baseline. Do not install it into a browser or use it for signing until the baseline toolchain is repaired and the planned production-path tests, build, exact-head review, and parent-controlled browser QA are complete.
