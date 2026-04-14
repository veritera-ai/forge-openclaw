# Forge + OpenClaw

[![npm](https://img.shields.io/npm/v/@veritera.ai/forge-openclaw)](https://www.npmjs.com/package/@veritera.ai/forge-openclaw)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://github.com/veritera-ai/forge-openclaw/blob/main/LICENSE)

Verification skill for [OpenClaw](https://openclaw.dev) agents. Every tool call is verified through [Forge](https://forge.veritera.ai) before execution — content-blind, sub-15ms, cryptographically signed.

## Install

```bash
npm install @veritera.ai/forge-openclaw
```

## Quick Start

```typescript
import { ForgeSkill } from "@veritera.ai/forge-openclaw";

const skill = new ForgeSkill({
  apiKey: process.env.FORGE_API_KEY!,
  policy: "production",
});

// Verify a tool call
const result = await skill.verify({ name: "send_email", arguments: { to: "user@example.com" } });
// result.verified = true | false

// Or use as an interceptor — blocked actions never reach your tools
const intercepted = await skill.intercept(toolCall);
if (!intercepted) {
  console.log("Action blocked by Forge policy");
}
```

## Configuration

```typescript
const skill = new ForgeSkill({
  apiKey: "vt_live_...",          // Required — get your key at forge.veritera.ai
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
2. Forge verifies the action against your policy — without seeing the tool's input or output
3. If approved, the tool executes normally
4. If denied, the action is blocked before execution

Forge never sees your prompts, code, data, or outputs. Policies are enforced outside the agent. Every decision is independently verifiable with Ed25519 signatures.

## Links

- [Forge Documentation](https://forge.veritera.ai/docs)
- [Python SDK](https://github.com/veritera-ai/forge-python) | [TypeScript SDK](https://github.com/veritera-ai/forge-js)
- [All Framework Integrations](https://github.com/veritera-ai/forge-python#framework-integrations)

## License

MIT — see [LICENSE](./LICENSE)
