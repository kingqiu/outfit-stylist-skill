# Minimal Configuration Guide

The skill should remain useful even with no external model configuration. Extra models improve image handling and visual output, but they are not required for basic advice.

## Capability Levels

| Level | Configured layers | What works | User-facing behavior |
| --- | --- | --- | --- |
| 0 | none | Text-only outfit advice from the current agent model | No image understanding or image board; use text fallback. |
| 1 | reasoning only | Better structured outfit decisions and Chinese recommendation logic | Text advice improves; image uploads and image boards still need fallback if unavailable. |
| 2 | image input only | Uploaded clothing photos can be understood and converted into wardrobe inventory | Can recommend from photos, but visual board falls back to text board. |
| 3 | image output only | Generated outfit boards from text descriptions | Can make boards, but uploaded photos require user text description or current-agent visual ability. |
| 4 | reasoning + image input | Strong wardrobe/photo-based recommendations | Can inspect photos and reason well; board still text fallback if image output unavailable. |
| 5 | reasoning + image output | Strong text-to-board recommendations | Great for scenario prompts and described clothes; uploaded photos need text description unless current agent can inspect them. |
| 6 | image input + image output | Photo-to-board workflow | Can inspect items and generate boards; reasoning uses current agent if no dedicated reasoning model. |
| 7 | reasoning + image input + image output | Full V1 experience | Photo/text input, structured reasoning, concise advice, and outfit-board image. |

## Recommended Minimums

For a basic installation:

```yaml
model_capabilities:
  reasoning:
    enabled: false
  image_input:
    enabled: false
  image_output:
    enabled: false
```

This still supports:

- occasion/weather-based advice
- wardrobe advice from text descriptions
- profile initialization
- text outfit boards

For a practical visual installation:

```yaml
model_capabilities:
  reasoning:
    enabled: true
  image_input:
    enabled: true
  image_output:
    enabled: true
```

The providers can be mixed. Example:

```yaml
reasoning:
  provider: "zhipu"
  model: "glm-4.6"
image_input:
  provider: "minimax"
  model: "MiniMax-M3"
image_output:
  provider: "jimeng"
  model: "jimeng_seedream46_cvtob"
```

## Setup Rules

- Use `assets/templates/model-capabilities.yaml` as the editable base.
- Store only environment variable names in skill files.
- Never commit API keys, access keys, tokens, private endpoints, or user memory.
- If a configured provider is unavailable at runtime, downgrade gracefully instead of failing the whole recommendation.
- If image output is unavailable, explicitly say so and provide a text-board fallback.
- If image input is unavailable, do not hallucinate uploaded images.

## Common Installation Profiles

### Text-First

Best for users who mainly ask "what should I wear tomorrow?"

```yaml
reasoning.enabled: true
image_input.enabled: false
image_output.enabled: false
```

### Photo Understanding Only

Best for users who upload wardrobe photos but do not need generated boards.

```yaml
reasoning.enabled: true
image_input.enabled: true
image_output.enabled: false
```

### Visual Board Only

Best for users who describe clothes in text and want generated outfit boards.

```yaml
reasoning.enabled: true
image_input.enabled: false
image_output.enabled: true
```

### Full Styling Assistant

Best for V1 target behavior.

```yaml
reasoning.enabled: true
image_input.enabled: true
image_output.enabled: true
```

## User-Facing Fallback Phrases

No image input:

```text
当前环境没有配置图片识别模型。你可以简单描述这几件衣服的颜色、版型和材质，我会继续帮你搭配。
```

No image output:

```text
当前环境没有配置图片生成模型。我先给你一版文字搭配板。
```

No dedicated reasoning model:

```text
No user-facing message is needed. Use the current agent model with the same structured reasoning contract.
```
