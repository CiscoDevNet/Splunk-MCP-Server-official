# Splunk-MCP-Server-official

[http://splunkbase.splunk.com/app/7931](http://splunkbase.splunk.com/app/7931)

# Splunk MCP Server

**Splunkbase App ID:** [7931](https://splunkbase.splunk.com/app/7931)  
**Built by:** Splunk LLC  
**Latest Version:** 1.0.1 (February 7, 2026)  
**Status:** Splunk Supported · Beta  
**Category:** Artificial Intelligence, IT Operations  
**Rating:** ⭐ 5/5 (7 reviews) · #8 in Artificial Intelligence  
**Downloads:** 5,029+

---

## Overview

The Model Context Protocol (MCP) is an open standard and framework that enables seamless, secure, and standardized two-way communication between AI applications (like large language models) and external data sources or tools. It acts as a universal adapter allowing AI systems to access, execute, and integrate functionalities from diverse systems through a common protocol, simplifying data sharing and tool interoperability without custom coding for each integration.

Splunk's MCP server leverages this to provide a standardized, secure, and scalable interface to connect AI assistants, agents, and other intelligent systems with data in the Splunk platform for both **Enterprise** and **Cloud** customers (currently in beta).

---

## Compatibility

- **Platforms:** Splunk Enterprise, Splunk Cloud Platform  
- **Platform Versions:** 8.0, 8.1, 8.2, 9.0, 9.1, 9.2, 9.3, 9.4, 10.0, 10.1, 10.2

---

## Key Features

**Universal Connectivity** — Seamlessly connects AI agents and tools to Splunk data resources in a secure and efficient manner.

**Enterprise-Grade Security** — Includes built-in authentication, authorization, and Role-Based Access Control (RBAC). Respects existing Splunk authentication and access controls, preventing unauthorized data exposure. Supports auditing, logging, and input validation to monitor for malicious payloads or command injection attempts.

**Rapid Deployment** — Offers a plug-and-play solution, eliminating the need for custom integrations.

---

## Core Capabilities

- **Explore the Data** — Navigate and interact with Splunk data effortlessly.
- **Discover Knowledge Objects** — Identify and access relevant saved searches, lookups, and other knowledge assets.
- **Execute Searches** — Run powerful Splunk queries to extract insights and drive intelligent workflows.
- **Leverage AI capabilities from Splunk's AI Assistant** — SPL search generation from natural language, search optimization, search explanation, retrieve MLTK models and algorithms.

---

## MCP Server Type & Features

### Deploy Type

`HTTP/SSE` — The server is hosted within your Splunk instance and exposed over HTTP. Clients connect via the Splunk management port (default `8089`) at the endpoint `/services/mcp`. It is **not** a locally-spawned `stdio` process.

Example initialization response confirming the transport:
```bash
curl -k \
  -H "Authorization: Bearer <YOUR_TOKEN>" \
  -H "Content-Type: application/json" \
  -X POST \
  -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"client":"curl","version":"0.1"}}' \
  https://<YOUR_SPLUNK_HOST>:8089/services/mcp
```
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "protocolVersion": "2025-03-26",
    "capabilities": { "tools": {} },
    "serverInfo": { "name": "Splunk_MCP_Server", "version": "0.2.2" }
  }
}
```

### Features / Capabilities

**`Tools` only** — The server exposes Splunk functionality exclusively as callable MCP Tools. The `capabilities` field in the initialization response confirms `{"tools": {}}` with no `resources` or `prompts` declared.

---

## Available Tools

| Tool | Description |
|------|-------------|
| `generate_spl` | Generate SPL from a natural language query (AI-powered) |
| `run_splunk_query` | Run a SPL query and return results (logs, aggregations, indexed data) |
| `get_splunk_info` | Get basic info about the Splunk instance (version, server name) |
| `get_indexes` | List available Splunk indexes (includes `splunk_server`) |
| `get_index_info` | Get detailed metadata for a specific index |
| `get_saved_searches` | List saved searches / knowledge objects |
| *(additional tools)* | Viewable via the built-in tools UI added in v1.0.0 |

> Safety guardrails are in place to prevent destructive operations.

---

## Client Configuration Examples

### Claude Desktop / Claude Code

Uses `mcp-remote` as a local proxy to inject the Authorization header:

```json
{
  "mcpServers": {
    "splunk-mcp-server": {
      "command": "npx",
      "args": [
        "-y",
        "mcp-remote",
        "https://<SPLUNK_HOST>:8089/services/mcp",
        "--header",
        "Authorization: Bearer <YOUR_TOKEN>"
      ],
      "env": {
        "NODE_TLS_REJECT_UNAUTHORIZED": "0"
      }
    }
  }
}
```

> Set `NODE_TLS_REJECT_UNAUTHORIZED: "0"` if using self-signed certificates.

### Cursor IDE

```json
{
  "mcpServers": {
    "splunk-mcp-server": {
      "url": "https://<SPLUNK_HOST>:8089/services/mcp",
      "headers": {
        "Authorization": "Bearer <YOUR_TOKEN>"
      }
    }
  }
}
```

---

## Authorization & RBAC

Roles must be configured in `authorize.conf` to grant users access to MCP tools:

```ini
[role_mcp_user]
mcp_tool_admin = enabled
mcp_tool_execute = enabled
```

- `sc_admin` role can manage MCP capabilities (added in v1.0.0).
- All AI interactions respect existing Splunk RBAC — users can only access data their Splunk role permits.

---

## SSL Configuration

In `mcp.conf`, you can configure SSL verification:

```ini
[mcp]
ssl_verify = true   # Set to false for self-signed certificates
```

---

## Sample Use Cases

**Security Operations** — A SOC analyst queries: *"Show me all failed login attempts from external IPs in the last hour."* The MCP server executes the SPL search and returns a concise report.

**DevOps Efficiency** — A DevOps engineer asks: *"What's the performance trend of my Kubernetes cluster logs in Splunk?"* The server pulls and summarizes the data.

**Business Insights** — A product manager queries: *"What's driving customer churn in Q2?"* The server combines Splunk data with external sources for a comprehensive analysis.

**Multi-App Insights** — Connect a Confluence MCP server (containing Splunk system knowledge) with the Splunk MCP server for richer, more accurate insights.

---

## Version History (Selected)

| Date | Version | Changes |
|------|---------|---------|
| Feb 7, 2026 | 1.0.1 | Bug fix for invalid hostname in certificate errors |
| Oct 22, 2025 | 1.0.0 | `sc_admin` role support; `ssl_verify` config; built-in tools UI; updated `get_indexes` and `get_index_info` with `splunk_server` |
| Sep 8, 2025 | — | Initial public availability |

---

## Resources

- [Splunkbase App Page](https://splunkbase.splunk.com/app/7931)
- [Official Configuration Docs](https://help.splunk.com/en/splunk-cloud-platform/mcp-server-for-splunk-platform/configure-the-server)
- [Splunk Blog: Unlock the Power of Splunk Cloud Platform with the MCP Server](https://www.splunk.com/en_us/blog/artificial-intelligence/unlock-the-power-of-splunk-cloud-platform-with-the-mcp-server.html)
- [Splunk Community Discussion](https://community.splunk.com/t5/All-Apps-and-Add-ons/Is-it-possible-to-use-the-Splunk-MCP-Server-APP-on-premises/m-p/753371)
- [Licensing: Splunk General Terms](https://www.splunk.com/en_us/legal/splunk-general-terms.html)

---

*© 2005–2026 Splunk LLC. All rights reserved.*
