# mp-developer

Mercado Pago full-product integration toolkit for Claude Code. Hybrid architecture: 12 product skills provide stable integration intelligence (flows, decision trees, gotchas), while the MCP server provides live API data (endpoints, payloads, code snippets).

## Quick Start

After installing the plugin, just ask Claude about any Mercado Pago product:

> "Integrate Checkout Pro in Node.js for Argentina"
> "Review my webhook implementation"
> "How do I handle payment status in Brazil?"

The agent detects the country and product, activates the right skill, and guides the integration. No setup required.

**Optional: Connect the MCP server** for enhanced live API data (exact endpoints, up-to-date payloads, code snippets). Run `/mp-connect` or open `/mcp` in Claude Code and authenticate via OAuth.

## Products Covered

| Product | Skill | Description |
|---------|-------|-------------|
| Checkout Pro | `mp-checkout-online` | Redirect-based payment with preferences |
| Checkout Bricks | `mp-checkout-online` | Embeddable UI components (Payment Brick, Card Brick, etc.) |
| Payments API | `mp-checkout-online` | Direct server-to-server payment with card tokenization |
| 3D Secure | `mp-checkout-online` | Additional cardholder authentication |
| Cross-Border Payments | `mp-checkout-online` | Accept payments from other countries |
| Webhooks | `mp-notifications` | HMAC-signed webhook handling, IPN |
| QR Attended / Dynamic | `mp-instore` | In-store QR code payments |
| Point | `mp-instore` | Physical card reader devices |
| Orders | `mp-orders` | Orders, OU + QR |
| Subscriptions | `mp-subscriptions` | Recurring billing, plans, preapprovals |
| Wallet Connect | `mp-wallet` | Link user wallets, debt payments, massive links |
| Money Out | `mp-money-out` | Disbursements, bank transfers |
| Marketplace | `mp-marketplace` | Multi-seller platforms, splits, OAuth |
| Security | `mp-security` | Tokenization, Supertoken, Vault, PCI |
| Specialized | `mp-specialized` | Insurance, Yape (PE), Fintoc (CL) |
| Reporting | `mp-reporting` | Settlement reports, reconciliation |
| SDKs | `mp-sdks` | SDK selection, setup, migration, platform compatibility |

## Architecture

```
Skills contain (STABLE — rarely changes):
├── What products are covered
├── When to use this skill vs another
├── End-to-end integration flows (step by step)
├── Decision trees between variants
├── Prerequisites and configuration
├── Country availability
├── Non-obvious gotchas and common errors
├── Product-specific security checklist
└── Instructions on WHAT to fetch from MCP and WHERE in docs

MCP provides (DYNAMIC — always up to date):
├── Exact endpoints and URLs
├── Request/response payloads and schemas
├── Code snippets per SDK/language
├── Test data
└── Error codes and messages
```

## Agent: `mp-integration-expert`

A lightweight router that detects the target country and product, then delegates to the right skill + MCP. Covers all 7 countries: Argentina, Brazil, Mexico, Chile, Colombia, Peru, Uruguay.

## Commands

| Command | Description |
|---------|-------------|
| `/mp-connect` | Connect to your Mercado Pago account via OAuth |
| `/mp-review [scope]` | Review your MP integration. Scopes: `security`, `webhooks`, `checkout`, `qr`, `subscriptions`, `marketplace`, `errors`, `full` |
| `/mp-setup [lang] [product]` | Scaffold a new integration. Products: `checkout-pro`, `bricks`, `checkout-api`, `orders`, `qr`, `point`, `subscriptions`, `marketplace` |

## Hook: Credential Leak Prevention

Automatically scans code being written for hardcoded Mercado Pago credentials:
- Access tokens (`APP_USR-*` for production and test users, `TEST-*` legacy)
- Client secrets
- Bearer authorization headers
- Webhook signing secrets

Blocks writes containing credentials and suggests using environment variables instead. Also blocks reading `.env` files to prevent credential exposure (`.env.example` remains readable).

## MCP: Mercado Pago API

Connects Claude Code to the official Mercado Pago MCP server (`mcp.mercadopago.com`) via HTTP transport, providing live access to payment APIs, documentation, and developer tools. Authentication is handled via OAuth — run `/mp-connect` for setup instructions.

## Configuration

See [PLUGIN_SETTINGS.md](./PLUGIN_SETTINGS.md) for per-project configuration options (e.g., disabling the credential hook).

## Resources

- [Mercado Pago Developer Docs](https://www.mercadopago.com.ar/developers/en/docs)
- [API Reference](https://www.mercadopago.com.ar/developers/en/reference)
- [SDKs](https://www.mercadopago.com.ar/developers/en/docs/sdks-library/landing)
- [Credentials Dashboard](https://www.mercadopago.com.ar/developers/panel/app)
