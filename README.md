# arb-sepolia-faucet

Small **Node.js** service that sends a little **Arbitrum Sepolia** native ETH to a given address. Intended for demos and QA — **testnet only**.

### RPC must be Arbitrum Sepolia (chain ID `421614`)

**Not** Ethereum Sepolia (`11155111`). If you use an Alchemy URL, it must be **arb-sepolia**, not **eth-sepolia**:

| Wrong (Ethereum Sepolia) | Right (Arbitrum Sepolia) |
|--------------------------|---------------------------|
| `https://eth-sepolia.g.alchemy.com/v2/...` | `https://arb-sepolia.g.alchemy.com/v2/...` |

If you omit `ARBITRUM_SEPOLIA_RPC_URL`, the service uses the public rollup RPC (`https://sepolia-rollup.arbitrum.io/rpc`). The server **exits on startup** if the RPC reports any other `chainId`.

Fund the faucet wallet with **Arbitrum Sepolia** ETH (e.g. [Chainlink Arbitrum Sepolia faucet](https://faucets.chain.link/arbitrum-sepolia)), not L1 Sepolia.

## API

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/health` or `/` | `{ ok: true, service, chain }` |
| `POST` | `/drip` | Body: `{ "address": "0x…" }` → `{ success, txHash, explorerUrl }` |

Errors return JSON `{ error, retryAfterMs? }` with appropriate HTTP status.

## Setup

```bash
cp .env.example .env
# Edit .env — set FAUCET_PRIVATE_KEY (fund on **Arbitrum Sepolia**, not Ethereum Sepolia)
npm install
npm start
```

## Environment

| Variable | Required | Default |
|----------|----------|---------|
| `FAUCET_PRIVATE_KEY` | Yes | — |
| `ARBITRUM_SEPOLIA_RPC_URL` | No | `https://sepolia-rollup.arbitrum.io/rpc` (must resolve to **chain 421614**) |
| `PORT` | No | `8787` |
| `DRIP_ETH` | No | `0.0004` |
| `COOLDOWN_MS` | No | `86400000` (24h per recipient address **and** per client IP) |
| `FAUCET_CORS_ORIGIN` | No | `*` (set to your frontend origin in production) |

## Frontend

Point your app at the deployed base URL (no trailing path). Example:

```env
VITE_TESTNET_FAUCET_URL=https://your-service.up.railway.app
```

The client should `POST` to `{baseUrl}/drip` with `{ "address": "<checksum or any valid 0x address>" }`.

Cool-downs are stored **in memory** and reset on process restart.

## Deploy (e.g. Railway)

1. New project from this repo.
2. Set `FAUCET_PRIVATE_KEY` in the platform’s secrets (wallet funded on **Arbitrum Sepolia**).
3. If you set `ARBITRUM_SEPOLIA_RPC_URL`, use an **Arbitrum Sepolia** endpoint only — or remove it to use the default public RPC.
4. Optional: set `FAUCET_CORS_ORIGIN` to your production site.
5. Start command: `npm start` (uses `PORT` from the host if provided).

## License

MIT
