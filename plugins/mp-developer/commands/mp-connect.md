---
description: Connect Claude Code to your Mercado Pago account via OAuth
license: Apache-2.0
copyright: "Copyright (c) 2026 Mercado Pago (MercadoLibre S.R.L.)"
allowed-tools: [Bash]
---

# /mp-connect

Connect Claude Code to Mercado Pago MCP Server using OAuth. No Access Token is required — authentication is handled by Mercado Pago via browser redirect.

## Instructions

The Mercado Pago MCP server is registered automatically via the plugin's `.mcp.json` (HTTP transport). The only step is completing the OAuth flow so Claude Code can get a session token.

### Pre-check: Is MCP already connected?

Before running setup, check if the server is already authenticated:

1. Try `ListMcpResourcesTool` with `server: "mercadopago"`, or check if any `mcp__mercadopago__*` tools are available
2. If connected → inform the user: "The Mercado Pago MCP server is already connected. You're all set! If you want to re-authenticate, say so and I'll guide you."
3. Only continue with setup if the user explicitly wants to reconnect

### Step 1 — Open the MCP panel

Ask the user to run `/mcp` in Claude Code. The `mercadopago` server will appear in the list with a **"needs authentication"** link below its name.

### Step 2 — Complete OAuth

Ask the user to click the **"needs authentication"** link. This will:

1. Open a browser window redirecting to Mercado Pago
2. Ask the user to select their **country**
3. Show the permissions being granted and ask the user to **authorize the connection**
4. Redirect back to Claude Code automatically

Claude Code stores the OAuth session token internally — it is never accessible to the model.

### Step 3 — Verify the connection

After the user completes OAuth, verify using MCP tools — NOT REST APIs:

1. Try `ListMcpResourcesTool` with `server: "mercadopago"`, or check if `mcp__mercadopago__*` tools are now available
2. If tools are available → report: "Mercado Pago MCP server is connected and ready."
3. If tools are NOT available → report: "The MCP server doesn't appear to be connected yet. Try running `/mcp` again and clicking the authentication link."

**Do NOT validate by calling REST APIs** (like `curl` to `/v1/payment_methods`). The goal is to verify the MCP connection, not the API credentials.

### If the browser redirect doesn't open automatically

If the user clicks the link but nothing opens, ask them to run this command to register the server manually and retry:

```bash
claude mcp add --transport http mercadopago https://mcp.mercadopago.com/mcp
```

Then run `/mcp` again and click **"needs authentication"**.

### Migrating from v1 (keychain setup)

If you previously configured the plugin using an Access Token stored in the OS keychain, follow these steps to migrate:

**1. Remove the old keychain entry (optional but recommended):**

```bash
# macOS
security delete-generic-password \
  -a "access_token" \
  -s "mercadopago-claude-plugin"

# Linux
secret-tool clear service "mercadopago-claude-plugin" account "access_token"
```

**2. Complete the OAuth flow** following the steps above (run `/mcp` → click "needs authentication").

You do not need to edit any config file — the plugin's `.mcp.json` was already updated to use HTTP transport.

---

### Security notes

- The OAuth token is managed entirely by Claude Code — the model cannot read it
- No Access Token is stored in `.env` files or the OS keychain
- If the user asks to configure the token manually, explain that OAuth is the supported and more secure method
