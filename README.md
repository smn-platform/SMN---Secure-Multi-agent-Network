# SMN — Secure Multi-agent Network

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Python 3.11+](https://img.shields.io/badge/python-3.11+-blue.svg)](https://www.python.org/downloads/)

**Deploy, govern, and scale AI agents safely.**

SMN is the governance and execution layer for production AI agent systems. It wraps every agent action in identity checks, policy enforcement, cost controls, output guardrails, and an immutable audit trail.

> SMN is to AI agents what Cloudflare is to web traffic — the control layer between intent and execution.

---

## Features

- **Agent Runtime** — ReAct loop with 5-gate governance pipeline
- **YAML Policy Engine** — Declarative policies with default templates
- **Immutable Audit Trail** — Hash-chained, tamper-evident audit log with verification
- **Identity & Permissions** — Scoped API keys with SHA-256 hashed storage
- **Output Guardrails** — PII detection, content filtering, JSON schema validation
- **LLM Reliability** — Retries, fallback, circuit breaker via LiteLLM
- **SSE Streaming** — Real-time Server-Sent Events for task execution
- **CLI** — `smn serve`, `smn agent`, `smn policy`, `smn audit`
- **REST API** — Full CRUD for agents, tasks, policies, and audit
- **OpenTelemetry** — Built-in observability instrumentation
- **SQLite Backend** — Zero-config local development
- **Compliance Frameworks** — EU AI Act, NIST AI RMF templates included

## Quick Start

```bash
# Install
pip install smn

# Start the server
smn serve

# Bootstrap a tenant + API key
curl -X POST http://localhost:8000/api/v1/auth/bootstrap \
  -H "Content-Type: application/json" \
  -d '{"tenant_name": "my-org"}'

# Register an agent
curl -X POST http://localhost:8000/api/v1/agents \
  -H "X-API-Key: smn_YOUR_KEY_HERE" \
  -H "Content-Type: application/json" \
  -d '{"name": "analyst", "model": "gpt-4o"}'
```

## SDKs

| SDK | Install | Docs |
|-----|---------|------|
| **Python** | `pip install smn-client` | [sdks/python/README.md](sdks/python/README.md) |
| **TypeScript** | `npm install @smn/sdk` | [sdks/typescript/](sdks/typescript/) |

### Python SDK

```python
from smn_client import SMNClient

client = SMNClient(api_key="smn_...", base_url="http://localhost:8000")

# Create an agent
agent = client.agents.create(name="analyst", model="gpt-4o")

# Execute a task
task = client.tasks.create(agent_id=agent.id, input_text="Summarise Q4 earnings")
print(task.output_text)
```

### TypeScript SDK

```typescript
import { SMNClient } from '@smn/sdk';

const client = new SMNClient({ apiKey: 'smn_...', baseUrl: 'http://localhost:8000' });

const agent = await client.agents.create({ name: 'analyst', model: 'gpt-4o' });
const task = await client.tasks.create({ agent_id: agent.id, input_text: 'Summarise Q4 earnings' });
console.log(task.output_text);
```

## API Endpoints

| Area | Endpoints | Purpose |
|------|-----------|---------|
| Health | `GET /health` | Service status |
| Auth | `POST /auth/bootstrap`, `POST/GET/DELETE /auth/keys` | Tenant + API keys |
| Agents | `POST/GET/PATCH/DELETE /agents` | Agent lifecycle |
| Tasks | `POST/GET /tasks` | Task execution |
| Streaming | `POST /stream` | Real-time SSE |
| Policies | `POST/GET /policies`, `GET /frameworks` | Policy management |
| Audit | `GET /audit`, `GET /audit/verify` | Tamper-evident logs |

All endpoints prefixed with `/api/v1`. Interactive docs at `/docs`.

## Project Structure

```
src/smn/
├── api/            # REST API routes
├── core/           # Agent runtime, policy engine, audit, guardrails
├── connectors/     # HTTP, LLM, database, email, storage, webhook
├── governance/     # Compliance framework definitions
├── middleware/      # Request ID, idempotency
└── static/         # Favicon, assets
```

## Configuration

SMN reads from environment variables (prefix `SMN_`):

| Variable | Default | Description |
|----------|---------|-------------|
| `SMN_HOST` | `0.0.0.0` | Server bind address |
| `SMN_PORT` | `8000` | Server port |
| `SMN_DATABASE_URL` | `sqlite+aiosqlite:///./smn.db` | Database connection |
| `SMN_DEBUG` | `false` | Enable debug mode |

## Development

```bash
# Clone and install
git clone https://github.com/smn-platform/SMN---Secure-Multi-agent-Network.git
cd smn
pip install -e ".[dev]"

# Run tests
pytest

# Start dev server
smn serve
```

## SMN Pro

Need multi-tenant management, SSO, Stripe billing, PostgreSQL, async task queues, multi-agent orchestration, or compliance packs?

**[SMN Pro] (Soon!)** adds enterprise features on top of this open-source core.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## Security

See [SECURITY.md](SECURITY.md) for our security policy and how to report vulnerabilities.

## License

MIT — see [LICENSE](LICENSE).

---

Built by [Freddie Ley](freddie.ley@icloud.com)
