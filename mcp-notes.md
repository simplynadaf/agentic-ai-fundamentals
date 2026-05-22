# MCP (Model Context Protocol) — Notes

## What is MCP?
A standardized way to connect LLMs/agents to external tools and data sources.

**Analogy:** USB for AI. One standard plug to connect any tool to any agent.

## Problem it Solves
- Before MCP: every tool integration was custom-built, different for each framework
- With MCP: build a tool once, use it with any agent that supports MCP (no vendor lock-in)

## Architecture

```
Agent (MCP Client) ←→ MCP Server ←→ External Service (RDS, S3, APIs, etc.)
```

- **MCP Client** = the agent itself (CrewAI, Kiro, Claude, Cursor). Built into the framework — not deployed separately.
- **MCP Server** = a service you build/host that exposes capabilities to agents.

## What an MCP Server Provides
1. **Tools** — actions the agent can take (run_query, read_file, call_api)
2. **Resources** — data the agent can access (documents, configs)
3. **Prompts** — pre-built prompt templates

## How It Works (RDS Example)
1. You host an RDS MCP server on ECS
2. You configure the agent with the MCP server's endpoint (URL)
3. Agent connects and automatically discovers available tools (run_query, list_tables, etc.)
4. At runtime: agent decides it needs data → calls MCP tool → MCP server queries RDS → returns results to agent

**Key:** You don't tell the agent about RDS. You tell it about the MCP server. The server handles all RDS details internally.

## Configuring an MCP Server

1. **Transport** — How client and server communicate
   - stdio (local, runs as subprocess on same machine)
   - HTTP/SSE (remote, over network)

2. **Tool definitions** — What capabilities you expose
   - Tool name, description, input parameters, output format

3. **Connection/credentials** — For the external service (DB host, API keys, etc.)

4. **Server metadata** — Name, version, description

5. **Client configuration** — Tell the agent where to find the MCP server

## Where to Host MCP Server

**Local (stdio):** Runs on same machine as agent. No hosting needed.

**Remote (HTTP/SSE) on AWS:**

| AWS Service | When to use |
|-------------|-------------|
| Lambda | Lightweight, serverless, auto-scales |
| ECS/Fargate | Long-running, persistent DB connections |
| EC2 | Full control, heavy workloads |
| App Runner | Simple deployment, auto-scales |

For RDS: ECS/Fargate is best (maintains persistent DB connections).

## MCP vs CrewAI Tools
- CrewAI tools (SerperDevTool) = framework-specific, only work in CrewAI
- MCP tools = universal, work with any MCP-compatible client

## Key Benefits
- Build once → works everywhere
- Community can share tools easily
- Agents get more capable without changing their code
- Swap backends (RDS → DynamoDB) without changing agent code — just update the MCP server
