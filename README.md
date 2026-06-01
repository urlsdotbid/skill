# urls.bid AI Agent Skill

This repository contains the official AI Agent Skill for [urls.bid](https://urls.bid) — the crypto-native URL shortener, static web hosting, and paywalled file upload gateway powered by the x402 payment protocol.

AI agents (e.g. Claude Code, Cursor, Codex) can install this skill to programmatically shorten links, create bio profiles, host static websites, and monetize assets directly from any terminal sandbox.

---

## 🛠️ Installation

### Option A: Install as an npm skill (Recommended)
If you have npm installed, add this skill to your global agent skills:
```bash
npx skills add urlsdotbid/skill --skill urls-bid -g
```

### Option B: Quick bash install
Alternatively, you can install the scripts and manifest instantly using our helper script:
```bash
curl -fsSL https://urls.bid/install.sh | bash
```
This installs:
* `SKILL.md` (Agent Manifest) to `~/.claude/skills/urls-bid/SKILL.md`
* `publish.sh` (Static Hosting Helper) to `~/.claude/skills/urls-bid/scripts/publish.sh`
* `drive.sh` (Agent Storage Drive Helper) to `~/.claude/skills/urls-bid/scripts/drive.sh`

---

## 🔒 Security & Privacy

This skill is designed with strict **security-first** principles for autonomous environments:
1. **Local Token Isolation:** Your urls.bid API token is stored securely on your local device under `~/.urlsbid/credentials` with strict `0600` permissions. It is never exposed in client bundles or public repositories.
2. **Attribution Headers:** Agent-initiated requests include the `X-Client: urlsbid/skill` header, allowing servers to accurately measure agent traffic.
3. **Safe Path Sanitization:** The hosting routes enforce rigid protection against path traversal (rejecting any segments containing `..` or absolute prefixes).

---

## 🚀 Core Capabilities

1. **Short & Gated Links:** Shorten URLs and gate redirects using USDC micropayments settled instantly on **Base** or **Solana**.
2. **Static Web Hosting:** Deploy complete static HTML/CSS/JS applications to custom subdomains (`{slug}.urls.bid`). Anonymous uploads expire in 24 hours; authenticated deployments are permanent.
3. **Bio Profiles:** Build customized public profile pages (`/bio/{username}`) summarizing links and monetized files.
4. **Scarcity Promo Links:** Set expiration timers and maximum payment thresholds (payment-caps) for launches or limited drops.
5. **Gated File Storage:** Secure PDFs, ZIPs, or images behind x402 payment requirements.

---

## 📂 Repository Layout

* `SKILL.md` — The main agent instruction manual outlining the complete oRPC and REST API schemas.
* `scripts/publish.sh` — Robust deployment utility utilizing standard bash, `curl`, and `jq` to build and finalize website publishes.
* `scripts/drive.sh` — Agent companion helper for private drive context storing.
* `install.sh` — Cross-platform platform-aware dependency loader.

---

For complete API specifications and OpenAPI definitions, visit our [API Docs](https://api.urls.bid/openapi.json).
