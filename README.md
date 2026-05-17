# DevTools Store

**🔴 Live Demo:** [https://dev-tools-store.vercel.app/](https://dev-tools-store.vercel.app/)

## 🚀 Project Overview
DevTools Store is a polished AI-powered developer product storefront built with Next.js, Stripe, and Supabase. It demonstrates modern checkout flows, subscription billing, webhook-driven order processing, and a lightweight admin dashboard for viewing customers, payments, and subscriptions.

This repository is ideal for showcasing a SaaS + ecommerce starter app with:
- one-off product checkout
- recurring subscription plans
- Stripe Customer Portal support
- Supabase database integration
- webhook event deduplication
- clean Next.js App Router structure

## 🌟 What Makes It Dope
- Fully integrated Stripe Checkout for both one-time and subscription purchases
- Webhook-safe event handling with deduplication via `processed_events`
- Admin interface for customer and billing visibility
- Modular product catalog powered by `lib/products.js`
- Simple, responsive UI built with SCSS
- Ready to extend for custom APIs, auth, and AI product delivery

## 🧱 Tech Stack
- **Next.js 16** (App Router)
- **React 19**
- **Stripe** (`stripe`, `@stripe/stripe-js`)
- **Supabase** (`@supabase/supabase-js`)
- **SASS / SCSS**
- **JavaScript**

## 📁 Key Project Structure
- `app/` — Next.js App Router pages and API routes
- `components/` — UI and layout components
- `lib/stripe.js` — Stripe client/server helpers
- `lib/supabase.js` — Supabase client and security checks
- `lib/products.js` — Products, plans, and seeded customers
- `supabase/schema.sql` — database schema for customers, payments, subscriptions, and webhook tracking
- `styles/` — global and component SCSS styling

## 🛍️ Products & Plans
### One-off Products
The storefront currently offers four premium AI tools:
- **Assignment Writer** — AI academic assignment generator
- **Grammar Guru** — grammar and writing improvement tool
- **Diagram Maker** — professional diagram creation
- **AI Detector & Bypass AI Content** — AI text detection and humanization

### Subscription Plans
Two subscription tiers are available:
- **Starter Plan** — entry-level growth tier
- **Pro Plan** — unlimited access for power users and teams

## 🌐 Routes and Pages
| Route | Description |
|---|---|
| `/` | Home page with hero, product lineup, and pricing options |
| `/admin` | Admin dashboard for customers, payments, and subscriptions |
| `/success` | Checkout success confirmation page |
| `/cancel` | Checkout cancelled page |

## 🔌 API Routes
| Route | Method | Purpose |
|---|---|---|
| `/api/create-checkout-session` | POST | Creates a Stripe Checkout session for product or plan purchase |
| `/api/create-portal-session` | POST | Creates a Stripe Customer Portal session |
| `/api/webhook` | POST | Receives and processes Stripe webhook events |

## ⚙️ Required Environment Variables
Create a `.env.local` file at the repo root with these values:

```env
NEXT_PUBLIC_APP_URL=http://localhost:3000
NEXT_PUBLIC_SUPABASE_URL=<your-supabase-url>
NEXT_PUBLIC_SUPABASE_ANON_KEY=<your-supabase-anon-key>
SUPABASE_SERVICE_ROLE_KEY=<your-supabase-service-role-key>
STRIPE_SECRET_KEY=<your-stripe-secret-key>
STRIPE_WEBHOOK_SECRET=<your-stripe-webhook-secret>
```

> Note: `SUPABASE_SERVICE_ROLE_KEY` must stay server-side only. Do not expose it in client code.

## 🛠️ Local Setup
1. Clone the repository
   ```bash
   git clone <repo-url>
   cd Dev-Tools-Store
   ```
2. Install dependencies
   ```bash
   npm install
   ```
3. Set up Supabase
   - Create a Supabase project
   - Run the SQL in `supabase/schema.sql`
4. Create `.env.local` and add the required keys
5. Start development server
   ```bash
   npm run dev
   ```

## 🧪 Test Stripe Locally
Use Stripe CLI to forward webhook events to your local app:

```bash
stripe listen --forward-to localhost:3000/api/webhook
```

## 💳 Stripe Test Cards
| Card Number | Result |
|---|---|
| `4242 4242 4242 4242` | Successful payment |
| `4000 0000 0000 0002` | Declined payment |

## 📊 Database Schema Summary
The Supabase schema supports:
- `customers` — seeded customer records like Alice and Bob
- `payments` — one-time purchases
- `subscriptions` — recurring subscription state
- `processed_events` — Stripe webhook deduplication and idempotency

## 🚀 Deployment Notes
Recommended deployment target: **Vercel**.
1. Push the repo to GitHub
2. Import the project into Vercel
3. Set the same environment variables in Vercel
4. Configure your Stripe webhook endpoint:
   `https://<your-domain>/api/webhook`
5. Subscribe to webhook events:
   - `checkout.session.completed`
   - `invoice.paid`
   - `invoice.payment_failed`
   - `customer.subscription.deleted`

## 🔒 Security & Production Considerations
- The current admin page is not protected by auth in this starter project
- In production, add authentication using Supabase Auth or Next.js middleware
- Use Stripe webhook signature validation to secure incoming events
- Keep the Supabase service role key strictly server-side

## 🧩 Extending This Project
Great next steps:
- Add proper admin authentication
- Connect AI services to deliver real product output
- Use Stripe Billing portal to manage subscriptions
- Add order history and email receipts
- Add a real product management admin interface

## 📦 NPM Scripts
- `npm run dev` — start development server
- `npm run build` — build for production
- `npm run start` — start production server
- `npm run lint` — run ESLint checks

## 🎯 Summary
DevTools Store is a slick demo store for developer productivity tools, built with modern Stripe billing and Supabase data management. It is designed to be a launchpad for SaaS stores, subscription apps, and marketplace prototypes.
