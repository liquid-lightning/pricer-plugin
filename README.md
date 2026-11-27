# pricer-plugin

`pricetiers-plugin` is a Core Lightning plugin that quotes prices for asset
pairs. Given an asset and an amount, it returns low, medium and high
price-ceiling tiers and the amount of the other asset each tier implies.

It is a pricing policy and nothing else. Asset ids are opaque strings to it:
it matches them and never interprets them. The
[swap plugin](https://github.com/liquid-lightning/swap-plugin) calls its
`pricetiers` RPC method to price a cross-asset swap, and any plugin that
implements the same method can replace it.

> **Status: MVP, not production ready.** This is open-source code for
> testnets. It has not been audited and must not be used with funds that
> matter.

## Build

```
cargo build --release
```

The binary is `target/release/pricetiers-plugin`. Rust 1.85 or later is
needed. The crate is a repository of its own and builds against the published
`cln-plugin` crate; `Cargo.toml` shows how to build against a CLN checkout
instead.

## Run

```
lightningd --plugin=/path/to/pricetiers-plugin \
    --pricetiers-pair=<from_asset>:<to_asset>:<rate_ppm>:<fee_base_msat>
```

| Option | Meaning |
|---|---|
| `pricetiers-pair` | A quotable pair, as `from_asset:to_asset:rate_ppm:fee_base_msat`. `rate_ppm` is how many msat of `from_asset` are required per 1,000,000 msat of `to_asset` at the reference price. Repeatable. |

The plugin is dynamic, so it can be started and stopped with
`lightning-cli plugin`.

## RPC

`pricetiers to_asset to_amount_msat` finds the configured pair with that
`to_asset` and returns:

| Field | Meaning |
|---|---|
| `from_asset` | The asset the payer would pay in |
| `rate_ppm`, `fee_base_msat` | The pair's configured rate and base fee |
| `tiers` | The low, medium and high price ceilings and the `from_amount_msat` each implies |

## Tests

```
cargo test
```

## Related repositories

- [Core Lightning fork](https://github.com/MarkCherepovskyi/lightning) with the asset-channel core changes
- [swap-plugin](https://github.com/liquid-lightning/swap-plugin), which calls this plugin

## Licence

MIT, see [LICENSE](LICENSE).
