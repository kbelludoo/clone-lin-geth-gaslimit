# clone-lin-geth-gaslimit

**Class: EXPERIMENTAL.** Scalar LIN clone of go-ethereum v1.14.12 `CalcGasLimit` / `VerifyGaslimit`. This is not a Geth node, not a replacement for Ethereum consensus, and not a claim that LIN executes uint64 values above `MaxGasLimit = 2^63-1`.

- **Upstream algorithm:** [ethereum/go-ethereum](https://github.com/ethereum/go-ethereum) tag `v1.14.12` commit `293a300d64be3d9a1c2cc92c26fcff4089deadcd` `core/block_validator.go` + `consensus/misc/gaslimit.go` (LGPL-3.0)
- **Constants:** `params/protocol_params.go` `GasLimitBoundDivisor=1024`, `MinGasLimit=5000`, `MaxGasLimit=2^63-1`
- **Proofs and harness live in lin-open:** https://github.com/kbelludoo/lin-open (see `examples/geth_gaslimit/` and `python3 test/prove_geth_gaslimit_external.py`)

## Files

| File | What |
|---|---|
| `lin_geth_gaslimit.lin` | LIN kernel (`gl_calc`, `gl_verify`, explicit divisor/min) |
| `NOTICE` | Upstream LGPL-3.0 attribution |

## Canonical vectors (Geth `TestCalcGasLimit`)

| parent | max increase | min decrease |
|---:|---:|---:|
| 20_000_000 | 20_019_530 | 19_980_470 |
| 40_000_000 | 40_039_061 | 39_960_939 |

## What this clone improves versus Geth

1. `params.GasLimitBoundDivisor` / `params.MinGasLimit` become explicit integer arguments.
2. Geth `delta = parent/divisor - 1` wraps uint64 when `parent < divisor` (example: parent=500 yields 499). LIN fail-closes to 0.
3. `VerifyGaslimit` casts `uint64` to `int64` then subtracts. LIN uses a non-negative abs-diff and rejects negatives.

## Not claimed

LIN does not replace go-ethereum. Operand domain is i64 non-negative (the same cap Geth publishes as `MaxGasLimit`). Merkle receipts in lin-open are tamper-evidence plus re-execution, not zk.
