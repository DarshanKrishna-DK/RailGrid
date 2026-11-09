<h1 align="center">RailGrid</h1>

<p align="center">
  <img src="https://img.shields.io/badge/React-3178C6?style=for-the-badge" alt="React" />
  <img src="https://img.shields.io/badge/TypeScript-7C3AED?style=for-the-badge" alt="TypeScript" />
  <img src="https://img.shields.io/badge/Algorand-0F766E?style=for-the-badge" alt="Algorand" />
  <img src="https://img.shields.io/badge/USDC-334155?style=for-the-badge" alt="USDC" />
  <img src="https://img.shields.io/badge/x402-A16207?style=for-the-badge" alt="x402" />
</p>

GPU compute you pay for by the second. Pick a node, get a cost estimate, run your job, and settle in USDC on Algorand. Every payment leaves an on-chain receipt.

Built for the [x402 Build Challenge](https://github.com/DarshanKrishna-DK/RailGrid) at India DevRetreat (June 2026).

## What this actually does

Most cloud GPU setups want a credit card, a monthly plan, and a login before you can even see pricing. RailGrid skips that.

You connect a Pera Wallet, browse available GPU nodes (each priced per second), and call the estimator. That costs a tiny USDC fee through the x402 protocol - HTTP 402, pay, retry, get your data. You see the exact cost and runtime before committing to the full job.

Submit the job, pay the capped amount, and the network runs your task. When it finishes, the receipt shows up in your wallet history.

No account system. No subscription. Just per-second billing with blockchain verification.

## How x402 fits in

x402 brings payments into normal HTTP requests. The flow is simple:

1. Client hits a protected endpoint
2. Server responds with `402 Payment Required`
3. Client signs a payment and retries with `Payment-Signature`
4. Server validates and returns the resource

RailGrid uses this for estimates, job runs, and provider registration. Micropayments work because Algorand settles in seconds and USDC fees stay low.

## Project layout

```
RailGrid/
├── railgrid-frontend/     React + Vite UI (wallet, providers, estimator, job runner)
└── x402-Project/
    ├── x402-demo-server/  Hono API with x402 payment middleware
    └── X402-Usecase/      AlgoKit starter kit and reference docs
```

The frontend talks to the demo server on port 4021. The `x402-Project` folder also has hackathon docs, architecture notes, and the original AlgoKit usecase template if you want to dig deeper.

## Run it locally

**Prerequisites**

- Node.js 18+
- [Pera Wallet](https://perawallet.app) on Algorand TestNet with a bit of USDC

**1. Start the API**

```bash
cd x402-Project/x402-demo-server
cp .env.example .env
npm install
npm run dev
```

Set `AVM_ADDRESS` in `.env` to your wallet address. The server listens on `http://localhost:4021`.

**2. Start the frontend**

```bash
cd railgrid-frontend
npm install
npm run dev
```

Open `http://localhost:5173`, connect your wallet, and try browsing nodes or running an estimate.

## Environment variables

| Variable | Where | Purpose |
|----------|-------|---------|
| `AVM_ADDRESS` | `x402-demo-server/.env` | Wallet that receives x402 payments |
| `FACILITATOR_URL` | `x402-demo-server/.env` | x402 facilitator endpoint |
| `PORT` | `x402-demo-server/.env` | API port (default `4021`) |
| `VITE_API_BASE` | `railgrid-frontend` | Backend URL for production builds |

## Deploy

The frontend deploys to Vercel from this repo. Set `VITE_API_BASE` to your hosted API URL so the UI can reach the x402 server.

The demo server is a long-running Node process (Hono + tsx). Host it separately - Railway, Render, a VPS, whatever you prefer. Vercel handles the static React app; the API needs its own home.

## Tech stack

- **Frontend:** React 19, Vite, Tailwind, Framer Motion, Pera Wallet, `@x402/core`
- **Backend:** Hono, `@x402/hono`, `@x402/avm`, TypeScript
- **Chain:** Algorand TestNet, USDC

## License

MIT
