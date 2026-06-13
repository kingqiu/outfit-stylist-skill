# Agent Compatibility

Design this skill to work across Claude Code, Codex, Hermes Agent, OpenClaw, and similar local agent runtimes.

## Compatibility Principle

Use the open agent skill common denominator:

```text
outfit-stylist/
├── SKILL.md
├── references/
└── assets/
```

Keep `SKILL.md` as the only required entrypoint. It must start with YAML frontmatter containing only broadly portable fields:

```yaml
---
name: outfit-stylist
description: ...
---
```

Do not make `agents/openai.yaml` required for runtime behavior. Treat it as Codex UI metadata only.

## Platform Matrix

| Agent | Expected install location | Compatibility notes |
| --- | --- | --- |
| Claude Code | `~/.claude/skills/outfit-stylist` | Uses filesystem skills with `SKILL.md`; avoid relying on Codex-only `agents/openai.yaml`. |
| Codex | `~/.codex/skills/outfit-stylist` or repo-local during development | Supports `SKILL.md`; `agents/openai.yaml` improves UI display but should not contain essential logic. |
| Hermes Agent | `~/.hermes/skills/outfit-stylist` | Uses progressive disclosure and `SKILL.md`; keep references directly linked from `SKILL.md`. |
| OpenClaw | `~/.openclaw/skills/outfit-stylist` or workspace skill dir | Keep valid YAML frontmatter and avoid hidden setup assumptions; verify with runtime skill list/check commands if available. |

## Portability Rules

- Keep all essential behavior in `SKILL.md` and directly linked `references/*.md`.
- Keep references one level deep; avoid nested instruction chains.
- Do not require shell scripts for normal usage.
- Do not hardcode absolute paths.
- Do not hardcode model providers, API keys, endpoints, or credentials.
- Keep image input and image output model configuration provider-neutral.
- Support separate provider configuration for image input and image output. Examples include MiniMax/DeepSeek/Gemini/OpenAI/custom for image input and GPT-image-2/Gemini banana/Jimeng/custom for image output.
- Treat provider-specific API wiring as an adapter layer owned by the host agent/runtime, not by the portable skill instructions.
- Support both simple API-key providers and signed-request providers such as Jimeng/Volcengine. Store only environment variable names in the skill.
- Use plain Markdown, YAML, and text templates.
- Keep output templates under `assets/templates/` because they are reusable assets, not mandatory runtime logic.
- If adding platform-specific files later, make them optional adapters rather than the source of truth.

## Installation Copy Targets

Manual copy or clone targets:

```bash
# Claude Code
~/.claude/skills/outfit-stylist

# Codex
~/.codex/skills/outfit-stylist

# Hermes Agent
~/.hermes/skills/outfit-stylist

# OpenClaw
~/.openclaw/skills/outfit-stylist
```

After installation, verify that the target directory contains:

```text
SKILL.md
references/
assets/
```

For runtimes with a skill listing or validation command, run it after copying. If a runtime does not discover the skill, first check the install path, exact `SKILL.md` filename, and YAML frontmatter.

## Compatibility Regression Checklist

Before publishing or moving the skill between agents, verify:

- `SKILL.md` exists at the root of the skill directory.
- `name` is `outfit-stylist`.
- `description` clearly contains trigger scenarios.
- No essential instruction exists only in `agents/openai.yaml`.
- All referenced files exist.
- The skill works without image model configuration by using text fallback.
- The skill works without generated images by using text outfit boards.
- User-facing output defaults to Chinese.

## Image Model Adapter Expectations

Each host agent may expose image capabilities differently. The portable skill only requires the host to map local capabilities into `model_capabilities`:

- `image_input.enabled`: whether the host can inspect clothing images.
- `image_input.provider/model`: the configured vision provider, such as MiniMax, DeepSeek-compatible multimodal endpoint, Gemini, OpenAI, or custom.
- `image_output.enabled`: whether the host can generate outfit-board images.
- `image_output.provider/model`: the configured image provider, such as GPT-image-2, Gemini banana family, Jimeng, or custom.
- `auth_type`: the host's authentication adapter, such as `api_key`, `ak_sk`, `oauth`, or `custom`.
- `access_key_env` and `secret_key_env`: optional environment variable names for signed-request providers such as Jimeng/Volcengine.

If a host cannot provide a capability, it should leave that section disabled and rely on the fallback behavior in `references/model-capability-config.md`.

No agent should require `agents/openai.yaml` to understand image model configuration. It is display metadata only.
