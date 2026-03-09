# AGENTS.md

## Purpose

This repo is intentionally pinned to the pre-`web3.py` v7 line to preserve backward compatibility with `eth-brownie`.

## Baseline

- Treat `bd7ab2b` (`v6.4.0`) as the compatibility baseline.
- `pyproject.toml` must stay on `web3>=6.2.0,<7.0.0` unless Brownie compatibility is being revisited deliberately.
- Do not merge from current upstream `main` as a shortcut. Backport only isolated fixes.

## Useful commits

- `bd7ab2b` `Set version to v6.4.0`
  - Last tagged release before the repo moved onto the `web3.py` v7 line.
- `05be079` `Bump web3 to 7.7.0 version and Hexbytes to 1.3.0 version`
  - Clear boundary where broad cherry-picks become risky for Brownie compatibility.
- `8146673` `Fix web3 dependency`
  - Still part of the post-v7 line. Useful reference, but not a safe baseline.
- `d9e5333` `Backport tx service API updates to pre-web3-v7 snapshot`
  - Useful reference for Safe transaction service URL changes, but it also pulled in newer API-surface changes. Do not copy it wholesale.
- `678b2f9` `Merge branch 'pre-web3-v7-preserve-ts-api' into wavey_edit`
  - Example of what to avoid: it put the backport on top of a v7 tree instead of preserving the old repo baseline.

## Safe Transaction Service Notes

- Keep the old `v6.4.0` API surface unless a downstream integration explicitly needs more.
- The Safe transaction service now uses base URLs with a path component like `/tx-service/<network>`.
- Plain `urljoin()` breaks those URLs when request paths start with `/api/...`, so use `build_full_url()` from `safe_eth/util/http.py`.
- The main files for tx-service maintenance are:
  - `safe_eth/util/http.py`
  - `safe_eth/safe/api/base_api.py`
  - `safe_eth/safe/api/transaction_service_api/transaction_service_api.py`
  - `safe_eth/safe/tests/api/test_transaction_service_api.py`
  - `safe_eth/util/tests/test_http.py`

## Working Rules

- Prefer manual backports or narrow cherry-picks over merges from upstream.
- Check diffs against `v6.4.0` first when making compatibility-sensitive changes.
- If a change touches dependency constraints, re-check `pyproject.toml` before finishing.
- Be cautious with newer Safe auth/api-key changes. Brownie-related downstream tooling may still handle auth outside of `TransactionServiceApi`.
