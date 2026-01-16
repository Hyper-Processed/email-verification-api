# VerifyFlow - Email Verification API

Real-time email validation API with disposable email detection, SMTP verification, and usage-based pricing.

## Features

- ✅ **Syntax Validation**: RFC 5322 compliant
- ✅ **Domain Verification**: DNS/MX record lookup
- ✅ **SMTP Validation**: Real mailbox verification
- ✅ **Disposable Detection**: 10,000+ disposable domains
- ✅ **Role-Based Detection**: Flag generic addresses
- ✅ **Risk Scoring**: 0-100 risk score for every email
- ✅ **Usage-Based Pricing**: Stripe metered billing
- ✅ **Rate Limiting**: Per-tier limits
- ✅ **Self-Serve**: No sales calls, instant signup

## Tech Stack

- **Frontend**: Next.js 14 (App Router), React, Tailwind CSS
- **Backend**: Next.js API Routes (serverless)
- **Database**: Vercel KV (Redis)
- **Payments**: Stripe (metered billing)
- **Hosting**: Vercel Edge Functions
- **Language**: TypeScript, Node.js 20+

## Getting Started

### Prerequisites

- Node.js 20+
- Stripe account (for payments)
- Vercel account (for KV and deployment)

### Installation

```bash
# Clone the repo
git clone <repo-url>
cd verifyflow

# Install dependencies
npm install

# Set up environment variables
cp .env.example .env.local
# Edit .env.local with your credentials

# Run development server
npm run dev
```

Visit http://localhost:3000

### Load Disposable Domains List

Before first use, load the disposable domains list into Redis:

```bash
curl http://localhost:3000/api/admin/load-disposable-domains
```

## API Usage

### Verify Email

```bash
curl -X POST http://localhost:3000/api/v1/verify \
  -H "Authorization: Bearer sk_live_..." \
  -H "Content-Type: application/json" \
  -d '{"email": "user@example.com"}'
```

Response:

```json
{
  "email": "user@example.com",
  "valid": true,
  "risk_score": 5,
  "risk_level": "low",
  "checks": {
    "syntax": { "valid": true, "message": "Valid email syntax" },
    "domain": { "valid": true, "message": "Domain exists and has MX records" },
    "smtp": { "valid": true, "message": "Mailbox exists" },
    "disposable": { "is_disposable": false, "message": "Not a disposable email domain" },
    "role_based": { "is_role": false, "message": "Not a role-based address" }
  },
  "timestamp": "2026-01-03T10:30:00Z",
  "credits_used": 1
}
```

### Get Account Info

```bash
curl http://localhost:3000/api/v1/account \
  -H "Authorization: Bearer sk_live_..."
```

## Deployment

### Deploy to Vercel

```bash
# Install Vercel CLI
npm i -g vercel

# Link project
vercel link

# Set environment variables
vercel env add STRIPE_SECRET_KEY
vercel env add STRIPE_PUBLISHABLE_KEY
# ... (add all other env vars)

# Deploy
vercel --prod
```

### Set Up Stripe

1. Create products and prices in Stripe Dashboard
2. Copy price IDs to environment variables
3. Create webhook endpoint pointing to `/api/webhooks/stripe`
4. Copy webhook secret to `STRIPE_WEBHOOK_SECRET`

### Set Up Vercel KV

1. Go to Vercel Dashboard → Storage → Create KV Database
2. Link to your project
3. Environment variables auto-populated

## Project Structure

```
verifyflow/
├── app/
│   ├── page.tsx                 # Landing page
│   ├── pricing/page.tsx         # Pricing page
│   ├── layout.tsx               # Root layout
│   ├── globals.css              # Global styles
│   └── api/
│       ├── v1/
│       │   ├── verify/route.ts  # Verify email endpoint
│       │   └── account/route.ts # Account info endpoint
│       ├── checkout/route.ts    # Stripe checkout
│       └── webhooks/
│           └── stripe/route.ts  # Stripe webhooks
├── lib/
│   ├── validation.ts            # Email validation logic
│   ├── disposable.ts            # Disposable email detection
│   └── stripe.ts                # Stripe utilities
├── package.json
├── tsconfig.json
├── next.config.js
└── README.md
```

## Testing

```bash
# Run tests
npm test

# Watch mode
npm run test:watch
```

## License

MIT

## Support

- Documentation: https://verifyflow.com/docs
- Email: support@verifyflow.com
- GitHub: https://github.com/verifyflow/api
