# Model Context Protocol: Advanced Topics

## Sampling

Sampling lets an MCP **server** ask the **client** (e.g., Claude Code) to run an LLM inference call on its behalf.

**Why it matters:** Servers don't need their own API keys or model access. The client owns the model connection and acts as a proxy.

**Example:** A `research` tool fetches Wikipedia pages, then uses sampling to ask the model to summarize them — all within a single tool call, without the server ever touching the LLM directly.

**Benefits:**
- **Reduces complexity:** The server doesn't need to integrate with LLMs directly
- **Perfect for public servers:** The client pays for token usage, not the server

## Roots

Roots let the **client** declare which directories are in scope for the server, identified by `file://` URIs.

Roots are not pushed on connection — the server requests them when needed, and the client notifies the server if the list changes.

**Roots are advisory, not enforced** — the protocol has no sandboxing mechanism. It's up to the server to respect the declared boundaries. A poorly written or malicious server could ignore them entirely.

## Logging and Progress Notifications

MCP defines two types of server-to-client **notifications** — one-way messages that require no response.

**Logging** lets a server emit structured log messages to the MCP client. Think of it like a backend service sending logs to a log aggregator, except the aggregator is the MCP client.

```python
await ctx.info("Searching on Wikipedia...")
```

**Progress notifications** are for long-running tool calls. The server sends incremental updates so the client can render a progress bar or status indicator.

```python
await ctx.report_progress(10, 100)
```

Both are fire-and-forget and implemented as callback functions on the MCP client. These notifications are optional: the client decides how to present them (if at all). Use case: improved user experience.

## Transport and Communication

MCP messages use **JSON-RPC 2.0**.

**Call tool request:**
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "tools/call",
  "params": {
    "name": "get_weather",
    "arguments": {
      "location": "Kyiv, Ukraine",
      "units": "metric"
    }
  }
}
```

**Call tool result:**
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "content": [
      {
        "type": "text",
        "text": "The current weather in Kyiv is 22°C and cloudy."
      }
    ],
    "isError": false
  }
}
```

### Streamable HTTP

MCP is bidirectional, but plain HTTP is request-response only — Streamable HTTP adds a workaround:

- `GET /mcp` opens a persistent SSE stream — the server uses it to push requests and notifications to the client
- `POST /mcp` sends a client request; the response can be an SSE stream

`mcp-session-id` ties subsequent requests to a session.

> **Note:** Progress notifications are sent over the `GET /mcp` SSE stream; logging goes over the `POST /mcp` one.

### Python SDK Options

- `stateless_http=True` — no session ID; disables server-to-client requests, sampling, and progress reports. Useful when server replicas sit behind a load balancer.
- `json_response=True` — `POST /mcp` returns the final result directly, not as an SSE stream.
