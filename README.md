# FlowchartAI MCP

Official connection configuration for [FlowchartAI](https://flowchartai.org/mcp), a hosted remote MCP server for creating and editing native flowcharts.

- **Remote endpoint:** `https://flowchartai.org/api/mcp`
- **Transport:** Streamable HTTP
- **Authentication:** OAuth with PKCE, or a scoped bearer API key
- **Official Registry:** `org.flowchartai/flowchartai` version `1.0.0`
- **Documentation:** https://flowchartai.org/developers/docs
- **Account and key management:** https://flowchartai.org/developers
- **Contact:** support@flowchartai.org

## Connect with OAuth

Add the remote URL to an MCP client that supports Streamable HTTP and OAuth. Sign in to FlowchartAI, inspect the requested permissions and approve the connection. Connections can be revoked in the developer dashboard.

```json
{
  "mcpServers": {
    "flowchartai": {
      "type": "http",
      "url": "https://flowchartai.org/api/mcp"
    }
  }
}
```

`mcp.json` is the Cursor connection configuration; `.mcp.json` and `.claude-plugin/plugin.json` provide the Claude Code plugin configuration. Client-specific setup is documented on the product website. These are configuration files, not a local server package. There is no npm install command or Docker image to run.

## API key alternative

Create a scoped key in the [developer dashboard](https://flowchartai.org/developers) and configure your client's secret store to send `Authorization: Bearer <YOUR_API_KEY>`. Never commit a real key into these files. API keys do not create website sessions.

## Tools

| Tool | Purpose |
| --- | --- |
| `get_capabilities` | Read formats, execution limits and charging rules |
| `get_account` | Read allowance, scopes and developer budgets |
| `create_flowchart` | Generate and save a native diagram from text or an image |
| `edit_flowchart` | Edit an owned diagram at its expected version |
| `list_flowcharts` | List saved diagrams |
| `get_flowchart` | Read a diagram and its website editor URL |
| `save_flowchart` | Save a native diagram without an AI generation charge |
| `list_flowchart_versions` | Read saved version history |
| `export_flowchart` | Export editable draw.io XML or JSON |
| `list_tasks` | Read durable operation receipts |
| `get_task` | Read one task and its result |

Saved diagrams open in the existing FlowchartAI web editor. PNG and SVG rendering is available there; server-side MCP exports provide draw.io XML and JSON.

## Usage and charging

A FlowchartAI account is required. AI generation and edits use the existing account allowance or Credits, with per-task spending caps and key/account daily budgets. There is no separate MCP subscription. Read capabilities and account information before spending, obtain the user's authorized `max_credits`, and keep a stable idempotency key for retries. A cap below the server estimate returns `max_credits_too_low` and `required_credits` without generating. Do not raise a cap without the user's approval.

After an uncertain response, read the task receipt or repeat the exact request with the same idempotency key. Do not create another task to recover the same result. Read a diagram's current version before editing it. Platform-funded repairs are not a second user charge.

## Repository scope and validation

This MIT-licensed repository contains public connection metadata, manifests, documentation and a product icon. It does not implement or self-host the backend. The application source is maintained separately in [tanchaowen84/flowchartai](https://github.com/tanchaowen84/flowchartai).

The remote service was verified with official MCP SDK 1.32.1: 11 tools, real OAuth/PKCE, account read, rotating refresh, scoped access and revocation. Hosted ChatGPT/Claude web-client account acceptance is separate and was not completed in that verification session.

Manifest checks:

```sh
claude plugin validate .
```

Cursor manifests were checked locally with the official [cursor/plugin-template](https://github.com/cursor/plugin-template) validator. The upstream validation script is kept outside this connection repository. Its optional hooks warning is expected because this plugin contains no hooks. Claude plugin validation requires a recent Claude Code version; the local 1.0.51 CLI did not support that command.
