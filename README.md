# EYDII + OpenClaw

[![npm](https://img.shields.io/npm/v/@veritera.ai/eydii-openclaw)](https://www.npmjs.com/package/@veritera.ai/eydii-openclaw)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://github.com/veritera-ai/eydii-openclaw/blob/main/LICENSE)

Verification skill for [OpenClaw](https://openclaw.dev) agents. Every tool call is verified through [EYDII](https://id.veritera.ai) before execution — content-blind, sub-15ms, cryptographically signed.

## Install

```bash
npm install @veritera.ai/eydii-openclaw
```

## Quick Start

```typescript
import { EydiiSkill } from "@veritera.ai/eydii-openclaw";

const skill = new EydiiSkill({
  apiKey: process.env.EYDII_API_KEY!,
  policy: "production",
});

// Verify a tool call
const result = await skill.verify({ name: "send_email", arguments: { to: "user@example.com" } });
// result.verified = true | false

// Or use as an interceptor — blocked actions never reach your tools
const intercepted = await skill.intercept(toolCall);
if (!intercepted) {
  console.log("Action blocked by EYDII policy");
}
```

## Configuration

```typescript
const skill = new EydiiSkill({
  apiKey: "vt_live_...",          // Required — get your key at id.veritera.ai
  policy: "production",           // Policy name to evaluate against
  agentId: "my-agent",            // Agent identifier (default: "openclaw-agent")
  skipActions: ["read_file"],     // Actions that skip verification
  debug: true,                    // Enable debug logging
  onBlocked: (action, reason) => {
    console.log(`Blocked: ${action} — ${reason}`);
  },
  onVerified: (action, result) => {
    console.log(`Verified: ${action} in ${result.latencyMs}ms`);
  },
});
```

## Middleware

Use as Express/Connect middleware for custom integrations:

```typescript
import express from "express";

const app = express();
app.use("/agent/action", skill.middleware());
```

## How It Works

1. Your agent calls a tool
2. EYDII verifies the action against your policy — without seeing the tool's input or output
3. If approved, the tool executes normally
4. If denied, the action is blocked before execution

EYDII never sees your prompts, code, data, or outputs. Policies are enforced outside the agent. Every decision is independently verifiable with Ed25519 signatures.

## Links

- [EYDII Documentation](https://id.veritera.ai/docs)
- [Python SDK](https://github.com/veritera-ai/eydii-python) | [TypeScript SDK](https://github.com/veritera-ai/eydii-js)
- [All Framework Integrations](https://github.com/veritera-ai/eydii-python#framework-integrations)

## License

MIT — see [LICENSE](./LICENSE)
