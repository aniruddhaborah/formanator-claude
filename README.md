# Formanator

Submit [Forma](https://www.joinforma.com/) benefit claims directly from Claude. Formanator runs as an MCP server that lets Claude read your receipts, analyze them with its native vision, and submit claims — no separate API keys needed.

It also supports fetching receipts automatically from services like Uber via browser automation.

## Setup

### 1. Install

Make sure you have [Node.js](https://nodejs.org/en) installed. For PDF receipt support, also install:

```bash
brew install ghostscript graphicsmagick
```

### 2. Configure Claude Desktop

1. Run `which npx` in a terminal to get the path
2. In Claude Desktop, open **Developer → Open App Config File...**
3. Add the MCP server:

```json
{
  "mcpServers": {
    "formanator": {
      "command": "/path/to/npx",
      "args": ["-y", "formanator", "mcp"]
    }
  }
}
```

4. **Developer → Reload MCP Configuration**

### 3. Log in

In a Claude conversation, just say "log me in to Forma" and provide your email. Claude will:

1. Call `requestMagicLink` to send a login email
2. Ask you to paste the magic link from your inbox
3. Call `loginWithMagicLink` to complete authentication

Your token is stored in `~/.formanatorrc.json`.

## What you can do

### Read and submit receipts

Ask Claude to analyze a receipt:

> "Read the receipt at ~/Downloads/uber-march.png and submit it as a claim"

Claude will use `readReceipt` to see the image directly, extract the amount/merchant/date/description, match it to your available benefits and categories, then submit via `createClaim`.

### Batch process a folder

> "Scan ~/receipts and submit claims for all of them"

Claude will use `scanReceiptDirectory` to list files, then `readReceipt` each one and submit claims.

### Check your benefits and claims

> "What benefits do I have left?"
> "Show me my in-progress claims"

### Fetch receipts from integrations

Formanator can fetch receipts from external services using browser automation.

**Currently supported:** Uber

To set up an integration:

1. Ask Claude to `listIntegrations` to see available providers
2. Provide your session cookies via `configureIntegration`
3. Use `fetchReceipts` to list available receipts
4. Use `downloadReceipt` to pull a receipt and analyze it

> "Fetch my Uber receipts from the last month and submit them all"

**Note:** Integrations require [Playwright](https://playwright.dev/). Install with:

```bash
npm install playwright
npx playwright install chromium
```

## MCP Tools

| Tool | Description |
|------|-------------|
| `requestMagicLink` | Send a Forma login email |
| `loginWithMagicLink` | Complete login with the magic link URL |
| `listBenefitsWithCategories` | List benefits with categories and remaining balances |
| `listClaims` | List claims with optional `in_progress` filter |
| `createClaim` | Submit a claim (amount, merchant, date, description, receipt, benefit, category) |
| `readReceipt` | Return a receipt image for Claude to analyze, with benefits context |
| `scanReceiptDirectory` | List receipt files in a directory |
| `listIntegrations` | Show available receipt-fetching integrations |
| `configureIntegration` | Store authentication cookies for a provider |
| `fetchReceipts` | List receipts from an integration (with date filtering) |
| `downloadReceipt` | Download a receipt from an integration for analysis |

**Supported receipt formats:** JPEG, PNG, HEIC, PDF

## CLI usage

The standalone CLI still works for manual use:

```bash
npx formanator login
npx formanator benefits
npx formanator list-claims
npx formanator submit-claim --receipt-path receipt.jpg --openai-api-key <key>
npx formanator submit-claims-from-directory --directory ./receipts --openai-api-key <key>
```

See `npx formanator --help` for all commands.

## Contributing

Changes are versioned using [Semantic Versioning](https://semver.org/) and released to npm via [`semantic-release`](https://github.com/semantic-release/semantic-release). Commit messages must follow [Angular Commit Message Conventions](https://github.com/angular/angular/blob/master/CONTRIBUTING.md#-commit-message-format).
