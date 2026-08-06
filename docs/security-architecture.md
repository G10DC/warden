# Sentinel Security & Infrastructure Architecture

The Sentinel security architecture segregates runtime protection into clear operational boundaries:

1. **`warden` (Ingress & Prompt Boundary)**:
   Filters untrusted input payloads, prevents prompt injection, and maintains frozen execution policy bounds via `checkTextRules`.

2. **`sentinel-egress-guard` (Egress Network Boundary)**:
   Monitors outbound network calls, blocks non-allowlisted domains, and prevents API token or secret leaks.

3. **`pulse-sentinel` (Infrastructure Daemon Monitor)**:
   Continuously monitors local LLM daemons (Unsloth Studio, Ollama), background data pipelines, and system health status.

## Architecture Overview
```
[ External Data / User Prompt ] ---> [ warden: Ingress Filter ]
                                              │
                                              ▼
                                   [ Agent Execution Engine ]
                                              │
                                              ▼
[ Allowed Endpoints ] <--- [ sentinel-egress-guard: Egress Firewall ]
```
