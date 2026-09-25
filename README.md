# Orbit Governance Action (binary)

Precompiled GitHub Action for **Orbit AI Governance** evidence collection.

This public Action repo contains only:
- `action.yml` — Action interface
- `bin/orbit-governance-action` — Linux binary (PyInstaller)

Source, prompts, and build tooling stay in the private `orbit-governance-action` repo. Rebuild the binary there, then copy `bin/orbit-governance-action` into this repo and push.

Remote: https://github.com/aahamed-dm/orbit-governance-action-binary  
Default branch: **master**

## Install in an agent repo

1. Create **`.github/workflows/orbit-governance.yml`** at the root of the agent repo.
2. Add the four secrets below (Settings → Secrets and variables → Actions).
3. Edit the `branches:` lists as needed.

| Secret | Purpose |
|--------|---------|
| `ORBIT_GOVERNANCE_KEY` | Bearer key forwarded to Orbit via MCP |
| `AI_GATEWAY_URL` | LiteLLM / OpenAI-compatible base URL |
| `AI_GATEWAY_API_KEY` | Gateway API key |
| `AI_GATEWAY_MODEL` | Model id accepted by the gateway |

```yaml
name: Orbit governance evidence

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  workflow_dispatch:

jobs:
  evidence:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      - name: Orbit governance scan
        uses: aahamed-dm/orbit-governance-action-binary@v1
        with:
          orbit_governance_key: ${{ secrets.ORBIT_GOVERNANCE_KEY }}
          ai_gateway_url: ${{ secrets.AI_GATEWAY_URL }}
          ai_gateway_api_key: ${{ secrets.AI_GATEWAY_API_KEY }}
          ai_gateway_model: ${{ secrets.AI_GATEWAY_MODEL }}
```

Prefer `@v1` or an exact tag like `@v0.3.0`. Use `@master` only before the first release tag.

**Runner:** Linux only (`ubuntu-latest`).

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `orbit_governance_key` | yes | — | Orbit governance Bearer key |
| `ai_gateway_url` | yes | — | Customer AI gateway base URL |
| `ai_gateway_api_key` | yes | — | Customer gateway key |
| `ai_gateway_model` | yes | — | Model id |
| `fail_on_required` | no | `true` | Fail job if any Required control fails |
| `source` | no | `ci` | `ci` or `local` |

## Maintainer release flow

1. In the **private source** repo, build the Linux binary (PyInstaller / Docker).
2. Copy the binary to `bin/orbit-governance-action` in **this** repo.
3. Commit and push only `action.yml` + `bin/orbit-governance-action` (+ this README if needed).
4. Tag:

```bash
git tag -a v0.3.0 -m "binary action release"
git push origin v0.3.0
git tag -f -a v1 -m "v1 line"
git push origin v1 --force
```
