# The Drift Rebalancer: paying Swap.io per trade with x402

*A concrete agent use case for [Swap.io](https://swap.io)’s Agent API.*

## The idea

Most trading bots either sit on a paid API key they barely use, or they hammer a free endpoint until it rate-limits them.

The **Drift Rebalancer** does neither. It watches a portfolio. When allocation drift crosses **5%**, it buys **one quote** from Swap.io, builds **one swap**, and otherwise stays silent. Idle time costs nothing.

## How it works (x402 in four beats)

1. The agent calls `POST https://hermes.swap.io/v1/swap/quote`.
2. Hermes answers with **HTTP 402 Payment Required** and the USDC amount (typically a few thousand atomic units — a fraction of a cent).
3. The agent pays that USDC through the **x402** protocol. No API key, no account, no subscription.
4. Hermes returns the ranked quote. The agent can then call `build-tx`, sign locally, and submit.

Failed upstream requests are not charged. A paid `200` carries an on-chain settlement receipt.

## Why x402 beats the alternatives for this use case

| Approach | Cost when idle | Cost when trading | Friction |
|---|---|---|---|
| Monthly API key / seat | Always on | Bundled | Account + key rotation |
| Prepaid credits | Hold inventory | Drawdown | Top-ups, waste |
| **x402 per call** | **$0** | **~$0.003–0.005 per quote** | Wallet with USDC only |

For a rebalancer that might trade a few times a day — or sit quiet for a week — paying only on real quotes is the correct cost curve.

## Why Swap.io

Hermes aggregates Solana swap providers, returns ranked quotes, and can build unsigned transactions the agent signs itself. Discovery is free at `GET https://hermes.swap.io/v1/info`. Docs for agents: [hermes.swap.io/llms.txt](https://hermes.swap.io/llms.txt).

## Watch the pitch

[Video attached / linked from the X post]

## Links

- Swap.io — https://swap.io
- Hermes Agent API — https://hermes.swap.io
- x402 — https://x402.org

*Entry for the Superteam Earn bounty “Make a video about our x402 Feature” by swap.io.*
