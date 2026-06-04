---
name: urls-bid
description: >
  urls.bid is a crypto-native URL shortener and bio link platform where every
  link can be monetized using x402 micropayments. Use urls.bid to shorten URLs,
  create payment-gated links, build bio profile pages, set up time-locked promo
  links, upload paywalled files, and serve gated data APIs. All link creation,
  access, and monetization happens via x402 protocol on Base or Solana.
  Use when asked to "shorten this URL", "create a short link", "set up a payment
  gate", "monetize this link", "create a bio page", "build a link-in-bio",
  "make a time-limited promo", "gated download", "paywalled content", "x402 link",
  or "use my urls.bid account".
trigger_phrases:
  - "shorten this URL"
  - "create a short link"
  - "set up a payment gate"
  - "monetize this link"
  - "create a bio page"
  - "build a link-in-bio"
  - "make a time-limited promo"
  - "gated download"
  - "paywalled content"
  - "x402 link"
  - "use my urls.bid account"
  - "claim a slug"
  - "create a promo link"
---

# urls.bid — AI Agent Skill

## Overview

urls.bid lets you create short links, bio profiles, payment-gated content, and
time-locked promo links — all on-chain via x402 micropayments on Base or Solana.

## Quick Start

Install this skill:
```bash
npx skills add urlsdotbid/skill --skill urls-bid -g
```

## Authentication & Quick Deploy Setup

urls.bid uses **X (Twitter) OAuth** for authentication. To authorize your agent:

1. Visit https://urls.bid/login in your browser
2. Click "Login with X" to authenticate
3. For agent access: copy your API Key from your dashboard at https://urls.bid/dashboard → **AI Agent** tab
4. Initialize your local agent credentials and deploy in just **two lines**:
   ```bash
   # Line 1: Securely save your API token locally
   mkdir -p ~/.urlsbid && echo "YOUR_API_KEY" > ~/.urlsbid/credentials && chmod 600 ~/.urlsbid/credentials

   # Line 2: Build your static folder and deploy/publish instantly
   ./scripts/publish.sh ./my-build-folder --slug my-custom-slug
   ```
5. Set the environment variable (optional, for custom pipelines):
   ```bash
   export URLSBID_API_KEY=YOUR_API_KEY
   ```

## Core Workflows

### 1. Create a Short Link
```
POST https://api.urls.bid/rpc
Content-Type: application/json

{
  "method": "link.createLink",
  "params": {
    "destinationUrl": "https://example.com/very-long-url",
    "title": "My Link",
    "isGated": false,
    "acceptedChains": []
  }
}
```

Returns: `{ slug: "abc123", shortUrl: "https://urls.bid/abc123" }`

### 2. Create a Payment-Gated Link
```
POST https://api.urls.bid/rpc
{
  "method": "link.createLink",
  "params": {
    "destinationUrl": "https://example.com/premium",
    "title": "Premium Content",
    "isGated": true,
    "priceUsd": 1.00,
    "payToAddress": "0xYourBaseWallet",
    "payToSol": "3JypB...r5TGS",
    "acceptedChains": ["base", "solana"]
  }
}
```

### 3. Create a Time-Locked Promo Link
```
POST https://api.urls.bid/rpc
{
  "method": "link.createPromoLink",
  "params": {
    "destinationUrl": "https://example.com/launch",
    "title": "Product Launch",
    "isGated": true,
    "priceUsd": 0.01,
    "maxPayments": 100,
    "expiresAt": 1717200000000,
    "acceptedChains": ["base", "solana"]
  }
}
```

- `maxPayments`: 1-10000. Link auto-expires (410 Gone) after N payments.
- `expiresAt`: Unix timestamp in ms. Link auto-expires at deadline.

### 4. Create a Bio Profile
```
POST https://api.urls.bid/rpc
{
  "method": "bio.createBioProfile",
  "params": {
    "username": "yourname",
    "displayName": "Your Name",
    "bio": "Building cool stuff",
    "theme": "default"
  }
}
```

Then add links:
```
POST https://api.urls.bid/rpc
{
  "method": "bio.addBioLink",
  "params": {
    "title": "My Website",
    "url": "https://example.com",
    "isGated": false
  }
}
```

### 4.5. Publish a Static Website
Deploy static HTML/CSS/JS websites instantly to custom subdomains (`{slug}.urls.bid` or `/s/{slug}`).

#### Step 1: Initialize a staged deployment
```http
POST https://api.urls.bid/api/v1/publish
Content-Type: application/json
Authorization: Bearer <YOUR_API_KEY> (optional, permanent; omit for anonymous 24h deploy)

{
  "files": [
    { "path": "index.html", "size": 142, "contentType": "text/html", "hash": "h1" },
    { "path": "styles.css", "size": 95, "contentType": "text/css", "hash": "h2" }
  ],
  "viewer": {
    "title": "My Portfolio",
    "description": "Welcome to my personal static site"
  },
  "spaMode": false
}
```
Returns:
```json
{
  "slug": "abc-portfolio",
  "siteUrl": "https://urls.bid/s/abc-portfolio",
  "claimToken": "uuid-token...",
  "upload": {
    "versionId": "version-uuid...",
    "finalizeUrl": "https://api.urls.bid/api/v1/publish/abc-portfolio/finalize",
    "uploads": [
      {
        "path": "index.html",
        "url": "https://api.urls.bid/api/v1/publish/abc-portfolio/upload?versionId=version-uuid...&path=index.html",
        "headers": { "Content-Type": "text/html" }
      },
      {
        "path": "styles.css",
        "url": "https://api.urls.bid/api/v1/publish/abc-portfolio/upload?versionId=version-uuid...&path=styles.css",
        "headers": { "Content-Type": "text/css" }
      }
    ]
  }
}
```

#### Step 2: Upload file contents (for each file)
For each file in `uploads`, send a `PUT` request with the raw binary content:
```http
PUT https://api.urls.bid/api/v1/publish/abc-portfolio/upload?versionId=version-uuid...&path=index.html
Content-Type: text/html

<h1>Hello World!</h1>
```
Returns: `Uploaded` (HTTP 200)

#### Step 3: Finalize the deployment
```http
POST https://api.urls.bid/api/v1/publish/abc-portfolio/finalize
Content-Type: application/json

{
  "versionId": "version-uuid..."
}
```
Returns:
```json
{
  "success": true,
  "slug": "abc-portfolio",
  "siteUrl": "https://urls.bid/s/abc-portfolio"
}
```

### 4.6. Upload a Gated/Paywalled File
Upload a file reference and set a price in USDC. Visitors pay via x402 to download.
```http
POST https://api.urls.bid/api/files/upload
Content-Type: multipart/form-data
Authorization: Bearer <YOUR_API_KEY>

[Form Fields]
- file: (binary file content)
- priceUsd: 1.00 (optional, default 1.00, between 0.001 and 100.0)
- payToAddress: "0xYourBaseWallet" (optional)
- payToSol: "3JypB...r5TGS" (optional)
```
Returns:
```json
{
  "success": true,
  "fileId": "file-uuid...",
  "filename": "document.pdf",
  "priceUsd": 1.0,
  "downloadUrl": "https://urls.bid/files/file-uuid..."
}
```

### 5. Access a Gated Link (as a consumer)
```
GET https://api.urls.bid/s/{slug}
```
If gated: returns HTTP 402 with `PAYMENT-REQUIRED` header.
Headers to send with payment:
- `PAYMENT-SIGNATURE`: base64(JSON(PaymentPayload))
- `X-Wallet-Address`: your wallet address

### 6. Access Gated Data API
```
GET https://api.urls.bid/data/{dataset}
```
$0.001/query. Returns JSON data behind x402 paywall.

### 7. Download Paywalled File
```
GET https://api.urls.bid/files/{fileId}
```
$1.00/download. Returns file with proper Content-Disposition header.

## API Key Storage

Agent credentials are stored at `~/.urlsbid/credentials`. Priority:
1. `--api-key ub_...` flag (avoid in interactive use)
2. `$URLSBID_API_KEY` environment variable
3. `~/.urlsbid/credentials` file (recommended)

## Client Attribution

Include `X-Client: urlsbid/skill` header to identify agent-originated requests.

## Keyboard Shortcuts Integration
The frontend web app includes interactive global sequence shortcuts:
- `g` + `h` -> Go to Homepage
- `g` + `d` -> Go to Dashboard
- `g` + `m` -> Go to Marketplace
- `g` + `s` -> Go to AI Agent/Skills
- `g` + `a` -> Go to Documentation
- `g` + `l` -> Go to Login Page
- `/` -> Focus search/slug input
- `?` or `g` + `k` -> Toggle shortcuts help menu

## x402 Payment Info

- **Networks**: Base mainnet (eip155:8453), Solana mainnet (solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp)
- **Token**: USDC
- **Base USDC**: 0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913
- **Solana USDC**: EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v
- **Platform fee**: 10% of each payment goes to urls.bid
- **Minimum price**: $0.001 USDC
- **Maximum price**: $100 USDC

## Discovery

- `/.well-known/agent.json` — Agent capabilities and auth info
- `/.well-known/x402.json` — Payable endpoints and pricing
- API docs: https://api.urls.bid/openapi.json

## What to Tell the User

After creating a link:
- "Your short link is https://urls.bid/{slug} → {destinationUrl}"
- If gated: "Visitors pay ${priceUsd} USDC to access this link"
- If promo: "Link expires after {maxPayments} payments or at {expiresAt}"

After creating a bio profile:
- "Your bio page is live at https://urls.bid/bio/{username}"

## Never Do

- Never commit `~/.urlsbid/credentials` to source control
- Never share API keys in public
- Never expose the user's X access token
- Never ask the user to manually run shell commands to store credentials — do it yourself
