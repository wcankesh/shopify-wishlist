# Shopify Wishlist App (Wishlist Club Parity)

This project targets MVP feature parity with Wishlist Club using a modern, free‑tier friendly stack.

## Stack (Free‑tier friendly)
- Hosting: Vercel (Hobby)
- Database: Neon Postgres (Free)
- Queue/Cache: Upstash Redis (Free)
- Email: Resend (Free tier) or Postmark (trial)
- Framework: Next.js 14 (App Router, TypeScript)
- Shopify: App Bridge + Polaris, OAuth/Billing APIs, Theme App Extensions (OS 2.0), App Proxy
- ORM: Prisma
- Jobs: BullMQ workers (Redis)

## Features (MVP)
- OS 2.0 blocks (PDP + collection) and App Embed (header icon/badge)
- Storefront JS (guest + logged‑in), toasts, badge count, merge on login
- Wishlist page via App Proxy (SSR), public share URL, social/email share
- Back‑in‑stock + price‑drop email alerts (daily batch)
- Admin app (settings, branding, translations, analytics)
- Webhooks for products/variants/inventory/price; uninstall; GDPR

## Environments
- Local dev: Node 20+, PNPM, Vercel CLI optional
- Production: Vercel project + env vars

## Env Vars (.env.local)
Copy this example and fill values:

```
# Shopify
SHOPIFY_API_KEY=
SHOPIFY_API_SECRET=
SHOPIFY_SCOPES=read_products,read_customers,write_customers,read_inventory
SHOPIFY_APP_URL=https://<vercel-app-url>
ENCRYPTION_KEY=<32-byte-hex>

# Database (Neon)
DATABASE_URL=postgresql://<user>:<pass>@<host>/<db>?sslmode=require

# Redis (Upstash)
REDIS_URL=rediss://:<password>@<host>:<port>

# Email (Resend or Postmark)
EMAIL_PROVIDER=resend
RESEND_API_KEY=
# or Postmark
POSTMARK_TOKEN=
FROM_EMAIL=notifications@yourdomain.com

# App Proxy
APP_PROXY_SIGNATURE_SECRET=<auto from Shopify>
APP_PROXY_SUBPATH=wishlist

# Misc
NEXTAUTH_SECRET=<32-byte-hex-or-random>
SENTRY_DSN=
```

## Free‑tier Setup
1) Vercel
   - Create a new project (Next.js). Link GitHub repo (when ready).
   - Add the env vars above to Project Settings → Environment Variables.
2) Neon (Postgres)
   - Create a free database; copy connection string to DATABASE_URL.
3) Upstash Redis
   - Create a free Redis database; copy URL to REDIS_URL.
4) Resend (Email)
   - Create account; add domain (or use on‑domain address later); get RESEND_API_KEY; set FROM_EMAIL.
5) Shopify Partner
   - Create a Partner account; create a Public app; set App URL to your Vercel URL; configure App Proxy /apps/wishlist → /api/proxy/wishlist.
   - Set required scopes; install on a dev store for testing.

## Monorepo Structure (planned)
```
shopify-wishlist/
  apps/
    web/                # Next.js app (Admin, App Proxy, Webhooks, APIs)
      app/
      components/
      lib/
      prisma/
      public/
    # packages below are published locally via turborepo workspaces
  packages/
    storefront-js/      # Embeddable storefront script (vanilla TS)
    email-templates/    # MJML/React Email templates
    shared/             # zod schemas, shared types
```

## Roadmap (2 weeks)
- Week 1: init repo, DB schema + migrations, OAuth shell, App Proxy skeleton, storefront APIs + script, theme blocks, wishlist page.
- Week 2: email sender + scheduler, analytics, i18n, multi-currency polish, QA across OS 2.0 themes, docs + submission.

## Notes
- Keep storefront script ≤12kb gzipped and async.
- Use HMAC verification for App Proxy and webhooks.
- Queue daily alert jobs in Redis with BullMQ; rate‑limit email sends.
- Ensure GDPR flows: uninstall purge, redact, data request.
