# Gridstore — Technical Document

A mobile-first community-sourcing marketplace and accounting system, featuring
Neighborhood Bulk-Sourcing Clubs, Save-to-Buy Packages, and Attention-to-Wallet
Direct Ad Payouts. Built with React 19 + TypeScript + Vite, with an Express
server that also runs the real API routes (Gemini AI, email, TTS, Paystack,
webhooks).

## Tech stack

| Layer | Technology |
| --- | --- |
| Frontend | React 19, TypeScript, Tailwind CSS, Vite 6, lucide-react, motion, recharts |
| Backend | Express 5, Node 22 (TypeScript via tsx) |
| AI | Google GenAI (`@google/genai`), Google Cloud TTS |
| Payments | Paystack, Stripe, USDT TRC-20, corporate bank wire |
| Auth | Firebase Auth (phone), custom PIN-based auth |
| Email | Nodemailer (Gmail SMTP), Resend |
| Storage | localStorage (client), Firestore (declared, optional) |
| Deployment | Vite build → static `dist/` + Express server |

## Architecture

```
Browser
   │
   ▼
┌─────────────────────────────┐
│ Vite build → dist/          │  static frontend (index.html, assets)
└──────────┬──────────────────┘
           │
           ▼
┌─────────────────────────────┐
│ Express server (server.ts)  │  serves dist/ + runs /api/* routes
│   - /api/auth/sendPin       │
│   - /api/gemini/*           │
│   - /api/natlas/voiceSearch │
│   - /api/tts                │
│   - /api/paystack/verify    │
│   - /api/webhooks/*         │
└─────────────────────────────┘
```

## Project structure

```
.
├── App.tsx                    # main app shell + auth flows
├── index.tsx                  # React mount
├── index.html                 # Tailwind CDN + importmap
├── index.css                  # base styles
├── server.ts                  # Express server + API routes (900+ lines)
├── context/
│   └── StoreContext.tsx       # global state (users, stores, orders, wallet, clubs)
├── components/                # reusable UI (NeonButton, BottomNav, etc.)
├── pages/                     # screen components (Wallet, Clubs, StoreFront, etc.)
├── services/                  # AI/voice/TTS service wrappers
├── public/                    # static assets (audio, images)
├── dist/                      # built frontend (gitignored)
├── Dockerfile                 # container image
├── docker-compose.yml         # app + Nginx + certbot
├── wrangler.toml              # Cloudflare Pages + Worker config
├── worker.js                  # Cloudflare Worker (API proxy)
├── gridstore.service          # systemd unit
├── nginx.conf                 # Nginx reverse proxy
└── DEPLOY.md / CLOUDFLARE.md / FREE_HOSTING.md
```

## Key files

- `context/StoreContext.tsx` — global state, auth, wallet, clubs, stores, orders
- `server.ts` — Express server, all `/api/*` routes, Vite dev middleware
- `pages/Wallet.tsx` — funding (Paystack/Stripe by country, USDT, corporate wire)
- `firebase-client.ts` — lazy Firebase init (env vars only, dev fallback)

## Environment variables

See `.env.example`. Required on the backend:

| Variable | Purpose |
| --- | --- |
| `GEMINI_API_KEY` | `/api/gemini/*` routes |
| `VITE_ADMIN_EMAIL` / `VITE_ADMIN_PASS` | admin login (no defaults shipped) |
| `VITE_FIREBASE_API_KEY` etc. | phone auth + Firestore |
| `EMAIL_USER` / `EMAIL_PASS` | Gmail SMTP (or `RESEND_API_KEY`) |
| `GOOGLE_TTS_API_KEY` | voice assistant |
| `PAYSTACK_SECRET_KEY` | payments |

## Scripts

```bash
npm install      # install deps
npm run dev      # start dev server (tsx server.ts)
npm run build    # build frontend → dist/
npm run start    # start production server (node server.ts)
npm run lint     # tsc --noEmit
npm run lint:fix # eslint --fix
```

## Security notes

- No hardcoded admin credentials — admin email/password come from env vars only.
- Firebase config is read from `VITE_FIREBASE_*` env vars; a gitignored local
  `firebase-applet-config.json` is used as a dev-only fallback.
- `.env`, `firebase-applet-config.json`, `migrated_prompt_history/`, `tmp/`,
  and `package-lock.json` are gitignored.

## Deployment

- **Cloudflare Pages + Worker** — `CLOUDFLARE.md` (free)
- **Vercel / Netlify / Render** — `FREE_HOSTING.md` (free tiers)
- **Docker** — `Dockerfile` + `docker-compose.yml`
- **Hetzner VM** — `DEPLOY.md` + `gridstore.service` + `nginx.conf`