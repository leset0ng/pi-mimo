# @lesetong/pi-mimo

Pi extension for Xiaomi MiMo AI models. Auto-discovers and registers all available models from the MiMo API.

Supports **multiple regions** (CN / SGP / AMS) and **both OpenAI-compatible and Anthropic-compatible protocols**.

## Install

```bash
pi install npm:@lesetong/pi-mimo
```

## Setup

### Option 1: Environment variables

```bash
export MIMO_API_KEY="tp-xxxxx"
# Optional: choose region and protocol
export MIMO_BASE_URL="https://token-plan-sgp.xiaomimimo.com/v1"
export MIMO_API="openai-completions"   # or "anthropic-messages"
```

### Option 2: pi auth.json (recommended)

Store everything in `~/.pi/agent/auth.json` — no env vars needed:

```json
{
  "mimo": {
    "type": "api_key",
    "key": "tp-xxxxx",
    "baseUrl": "https://token-plan-sgp.xiaomimimo.com/v1",
    "api": "openai-completions"
  }
}
```

Or append with one command:

```bash
cat <<'EOF' >> ~/.pi/agent/auth.json
, "mimo": { "type": "api_key", "key": "tp-xxxxx", "baseUrl": "https://token-plan-sgp.xiaomimimo.com/v1", "api": "openai-completions" }
EOF
```

> **Note:** Make sure the resulting JSON is valid (no duplicate keys, proper commas).

## Region & Protocol Matrix

| Region | OpenAI-compatible | Anthropic-compatible |
|--------|-------------------|----------------------|
| China (CN) | `https://token-plan-cn.xiaomimimo.com/v1` | `https://token-plan-cn.xiaomimimo.com/anthropic` |
| Singapore (SGP) | `https://token-plan-sgp.xiaomimimo.com/v1` | `https://token-plan-sgp.xiaomimimo.com/anthropic` |
| Europe (AMS) | `https://token-plan-ams.xiaomimimo.com/v1` | `https://token-plan-ams.xiaomimimo.com/anthropic` |

Default: **Singapore OpenAI-compatible** (`https://token-plan-sgp.xiaomimimo.com/v1`).

## Usage

Start pi normally — MiMo models appear under the `mimo` provider:

```bash
pi
```

Select a MiMo model with `/model` or use `mimo/<model-id>` directly.

## Development

Test locally without publishing:

```bash
# With env variables
MIMO_API_KEY="tp-xxxxx" MIMO_BASE_URL="https://token-plan-cn.xiaomimimo.com/v1" pi -e ./extensions/index.ts

# With auth.json (ensure ~/.pi/agent/auth.json contains the mimo key)
pi -e ./extensions/index.ts
```

## How it works

On startup, the extension:
1. Reads config from `MIMO_API_KEY` / `MIMO_BASE_URL` / `MIMO_API` env vars, or `~/.pi/agent/auth.json`
2. Fetches models from `<baseUrl>/models` (falls back to OpenAI path for Anthropic endpoints)
3. Enriches with platform metadata from `https://platform.xiaomimimo.com/api/v1/models`
4. Filters out non-coding models (TTS, image-gen, embeddings, etc.)
5. Registers them under the `mimo` provider

## License

MIT
