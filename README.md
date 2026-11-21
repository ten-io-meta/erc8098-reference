# ERC-8098 reference candidate

Standalone Solidity reference implementation candidate for the draft
[ERC-8098: Non-increasing supply ERC-20](https://github.com/ethereum/ERCs/pull/1390).

**This repository is independent of OECL and ERC-8060.** It contains only
ERC-8098 reference source code, its own Hardhat test suite, locked toolchain,
and its own CI workflow.

## Conformance design

- Initial ERC-20 supply is assigned once in the constructor to a chosen nonzero holder.
- No post-deployment mint interface, privileged minter, owner/administrator, or proxy-upgrade mechanism.
- `burn` and `burnFrom` reduce the canonical ERC-20 `totalSupply()` irreversibly.
- Burns emit canonical `Transfer(from, address(0), amount)` events.
- Canonical balances and supply are inherited from OpenZeppelin ERC-20.
  No token-internal alternative supply representation, synthetic mirrors,
  internal wrappers, or duplicate balance ledgers exist.
- The contract exposes no upgradeable implementation mechanism or hooks.

These properties implement the three central constraints of the **draft**:
irreversible burns, absence of re-mint paths, and single-state supply
representation. External wrappers and third-party representations are
explicitly outside the proposed standard's scope. The draft's discussion
of proof compatibility is **not** a requirement to add zero-knowledge privacy.

## Reproduce

Node 22, Solidity 0.8.20, Hardhat 2.22.19, OpenZeppelin Contracts 4.9.6.

```bash
npm ci
npm run compile
npm run test:conformance
```

The eight tests cover constructor allocation, burn events and supply
accounting, transfer/approval invariance, `burnFrom` authorization,
unauthorized actions and zero-supply behavior, ABI inspection for obvious
admin/mint/upgrade methods, multi-account mixed transactions, and invalid
constructor input.

The lockfile pins the dependencies used by CI. The implementation has no
dependency on TEN.IO collections, ERC-8060 contracts, or OECL.

## Scientific and security limitations

**Passing tests does not mean that an independent audit or exhaustive
formal verification has occurred.** The source is authored by the ERC
proposer as a reference candidate; independent review should precede any
production deployment or universal protocol-compliance assertion.

OECL can independently check out this repository at an exact commit for
experiments; doing so does **not** make its scientific results automatically
conclusive. The lab must still attribute constraints to each participant
and evaluate actual joint execution evidence.
