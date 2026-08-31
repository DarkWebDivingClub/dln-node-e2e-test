# dln-node-e2e

End-to-end scenarios for [`dln-node`](https://github.com/DarkWebDivingClub/dln-node),
against a Bitcoin Core regtest chain.

These drive real node processes over their real control plane — NWC and
NCC over a Nostr relay — with a bitcoind container underneath. Nothing is
mocked.

## Scenarios

- `two_dln_nodes` — two nodes, a channel, an invoice, a Lightning payment,
  with balances asserted on both sides
- `onchain_payment` — an on-chain send, verified against bitcoind rather
  than against the node's own report
- `hold_invoice` — a held HTLC, settled and cancelled

```
cargo run --bin two_dln_nodes
```

Each prints `PASS` or `FAIL` and sets its exit status.

They run with `transport = "none"`, because two nodes with embedded
signers derive the same `node_id` and cannot peer with each other.

## Scope

**The node, on a chain that does not change under it.** Nothing here
knows about the BLAKE2b fork or about the exchange:

| Repo | Covers |
|---|---|
| [`dln-node-knots-e2e`](https://github.com/DarkWebDivingClub/dln-node-knots-e2e) | the node against a BTK chain, including across activation |
| [`diamond-x-e2e`](https://github.com/DarkWebDivingClub/diamond-x-e2e) | the exchange, and the shared harness |

`hold_invoice` sits here rather than with the exchange because a held
HTLC is a node primitive; the exchange is only its first consumer. If it
grows swap-shaped cases it should move.

## Harness

Infrastructure comes from `dln-e2e-harness`, in `diamond-x-e2e`. The
dependency points at the exchange's repository because that is where the
harness lives — with its most demanding consumer — not because these
scenarios have anything to do with the exchange.

## Requirements

Docker. The node under test is built from a local `dln-node` checkout;
set `DLN_NODE_BINARY` to use a different build.

## Licence

GPL-3.0-only.
