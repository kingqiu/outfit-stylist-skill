# Model Capability Configuration

Keep styling reasoning separate from image model availability. Image input and image output may be provided by different models.

## Schema

```yaml
model_capabilities:
  reasoning:
    enabled: false
    provider: "" # openai | gemini | minimax | zhipu | deepseek | custom
    model: ""
    adapter: "" # openai_compatible | anthropic_compatible | native_api | custom
    endpoint_env: ""
    auth_type: "" # api_key | ak_sk | oauth | custom
    api_key_env: ""
    supports_thinking: null
    supports_tools: null
    notes: ""
  image_input:
    enabled: false
    provider: "" # minimax | deepseek | gemini | openai | custom
    model: ""
    adapter: "" # api | cli | mcp | custom
    endpoint_env: ""
    auth_type: "" # api_key | ak_sk | oauth | custom
    api_key_env: ""
    access_key_env: ""
    secret_key_env: ""
    supported_inputs:
      - clothing_photo
      - outfit_photo
      - multi_item_photo_set
    max_images_per_request: null
    notes: ""
  image_output:
    enabled: false
    provider: "" # openai | gemini | minimax | jimeng | listenhub | custom
    model: ""
    adapter: "" # api | cli | mcp | custom
    endpoint_env: ""
    auth_type: "" # api_key | ak_sk | oauth | custom
    api_key_env: ""
    access_key_env: ""
    secret_key_env: ""
    default_output: outfit_board
    default_aspect_ratio: "9:16"
    fallback_aspect_ratio: "3:4"
    long_image_aspect_ratio: "9:21"
    supported_outputs:
      - outfit_board
      - styling_board
    supports_reference_images: null
    supports_chinese_text_rendering: null
    notes: ""
```

## Required Provider Coverage For V1

The configuration layer must support at least these provider families without making any one of them mandatory:

### Reasoning

The reasoning model handles outfit decisions after user context, wardrobe items, visual inventory, weather, scenario, and constraints have been normalized. It should not be conflated with image input or image output.

| Provider family | Use for | Notes |
| --- | --- | --- |
| `openai` | Default reasoning when available | Optional host default for final recommendation logic. |
| `gemini` | Multimodal-aware reasoning and large-context wardrobe planning | Useful when the same provider is also used for image understanding. |
| `minimax` | Long-context multimodal reasoning and agent workflows | Official MiniMax Chat Completions support `MiniMax-M3` and M2.x model families; M3 supports text, image, video, tools, and thinking-style content. |
| `zhipu` / GLM | Chinese reasoning, agent-style planning, and API-accessible GLM models | Use Zhipu/BigModel chat completions for GLM-4.6, GLM-4.5, GLM-4.7-Flash, GLM-5.x, or configured local availability. |
| `deepseek` | Reasoning over structured clothing inventories | Do not use for raw image inspection unless the host has a separate vision adapter. |
| `custom` | Any compatible chat/reasoning backend | Must accept the structured outfit decision payload and return concise Chinese advice plus image prompt fields. |

### Image Output

| Provider family | Use for | Notes |
| --- | --- | --- |
| `openai` / `gpt-image-2` | Default high-quality outfit board generation where available | Use for `Structured OOTD Styling Card`; keep model name configurable. |
| `gemini` / Nano Banana family | Alternative outfit board generation and reference-image workflows | Official Gemini image model family includes Nano Banana, Nano Banana 2, and Nano Banana Pro; exact model id belongs in config. |
| `minimax` / image generation | Optional image generation backend | Official MiniMax API supports text-to-image and image-to-image generation through `/v1/image_generation`; keep model and provider limits configurable. |
| `jimeng` | ByteDance/Jimeng image generation | Support Volcengine Jimeng/Seedream adapters, especially text-to-image and reference-image outfit boards. |
| `listenhub` | ListenHub image generation skill/API | Compatible host adapter for Google/Gemini and OpenAI/GPT-image-2 image generation; supports prompt, aspect ratio, image size, and reference images. |
| `custom` | Any compatible image generation backend | Must accept a prompt and return an image artifact. |

### Image Input

| Provider family | Use for | Notes |
| --- | --- | --- |
| `minimax` | Clothing/item image understanding | Must return visible garment attributes and uncertainty notes. |
| `deepseek` | Text reasoning or DeepSeek-compatible multimodal endpoints when available | Official DeepSeek API docs currently describe text/reasoning chat models; do not assume native image input unless the host provides a compatible vision endpoint. |
| `gemini` | Multimodal image understanding | Useful for multi-image wardrobe inventory. |
| `openai` | Multimodal image understanding | Optional; keep separate from image output. |
| `custom` | Any compatible vision model | Must return structured item inventory. |

Do not assume image input and image output use the same provider. For example, an installation may use MiniMax for image input and GPT-image-2 or Jimeng for image output.

## Provider Config Template

Use `assets/templates/model-capabilities.yaml` as the base. Installers can set provider-specific values in their local runtime, environment, or agent config.

Required fields per provider:

```yaml
provider: "openai | gemini | jimeng | minimax | deepseek | custom"
model: "provider-specific-model-id"
adapter: "api | cli | mcp | custom"
endpoint_env: "OPTIONAL_ENDPOINT_ENV_NAME"
auth_type: "api_key | ak_sk | oauth | custom"
api_key_env: "OPTIONAL_API_KEY_ENV_NAME"
access_key_env: "OPTIONAL_ACCESS_KEY_ENV_NAME"
secret_key_env: "OPTIONAL_SECRET_KEY_ENV_NAME"
notes: "installation-specific details"
```

Never put actual API keys, secrets, access tokens, or private endpoint URLs inside the skill repository.

## Runtime Resolution Order

At the start of every complete styling request, resolve model capabilities in this order:

```text
1. reasoning
2. image_input, only if the user provided images
3. image_output, unless the user explicitly asked for text only
```

Use `references/end-to-end-workflow.md` for the full request chain.
Use `references/minimal-configuration.md` when explaining installation levels or partial model availability.

Do not skip the image board merely because the user did not explicitly ask for one. A normal styling recommendation is text plus image when image output is available.

Do not block the answer if one layer is unavailable:

- no reasoning model: use the current agent model with the same structured contract
- no image input model: ask for a text description only when needed
- no image output model: provide the explicit text-board fallback

The three layers may use different providers, for example:

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

## Official Provider Notes

These notes reflect official provider documentation checked on 2026-06-13. Because model names and limits change, treat exact model ids as configuration values rather than hardcoded skill logic.

| Provider | Official docs | Skill adapter note |
| --- | --- | --- |
| OpenAI GPT Image | https://developers.openai.com/api/docs/guides/image-generation | Use `gpt-image-2` for image output when available. The Image API supports generation and edits; the Responses API can support conversational image workflows. |
| OpenAI vision | https://developers.openai.com/api/docs/guides/images-vision | Use a vision-capable mainline model for image input if the host config chooses OpenAI for clothing analysis. Images can be provided by URL, base64 data URL, or file id depending on endpoint. |
| Google Gemini image generation | https://ai.google.dev/gemini-api/docs/image-generation | "Nano Banana" is the official Gemini native image-generation family. Prefer `gemini-3-pro-image` for complex, polished outfit boards; use `gemini-3.1-flash-image` or `gemini-2.5-flash-image` for speed/cost. |
| Google Gemini image understanding | https://ai.google.dev/gemini-api/docs/image-understanding | Good default for multi-image wardrobe inspection. Supports inline image data for smaller requests and File API for larger/reused images. |
| MiniMax API | https://platform.minimax.io/docs/api-reference/api-overview | MiniMax exposes language, speech, video, image, music, and file APIs. |
| MiniMax OpenAI-compatible chat | https://platform.minimax.io/docs/api-reference/text-chat-openai | Chat Completions support `MiniMax-M3`, `MiniMax-M2.7`, `MiniMax-M2.7-highspeed`, `MiniMax-M2.5`, and related models. |
| MiniMax Anthropic-compatible chat | https://platform.minimax.io/docs/api-reference/text-chat-anthropic | MiniMax-M3 supports multimodal input, tool use, and thinking content blocks in Anthropic-compatible format. |
| MiniMax text generation guide | https://platform.minimax.io/docs/guides/text-generation | MiniMax-M3 is positioned for long-context agentic reasoning and structured task execution. |
| MiniMax image generation | https://platform.minimax.io/docs/api-reference/image-generation-t2i | MiniMax supports text-to-image through `/v1/image_generation`; image-to-image uses the same image generation family. |
| MiniMax M3 multimodal input | https://platform.minimax.io/docs/api-reference/text-chat-anthropic | `MiniMax-M3` supports text, image, and video input through Anthropic-compatible content blocks. |
| MiniMax image understanding for agents | https://platform.minimaxi.com/docs/token-plan/openclaw | For OpenClaw-style environments, use `mmx vision describe --image <path-or-url>` or MiniMax MCP `understand_image` as the image-input adapter. |
| DeepSeek API | https://api-docs.deepseek.com/ | Official DeepSeek API is OpenAI/Anthropic-compatible for text/reasoning chat. Do not list it as a guaranteed native vision provider. |
| DeepSeek models/pricing | https://api-docs.deepseek.com/quick_start/pricing | Current official model families include `deepseek-v4-flash` and `deepseek-v4-pro`; use them for reasoning over extracted clothing inventories, not raw image inspection unless a vision adapter is separately supplied. |
| Zhipu / BigModel chat completions | https://docs.bigmodel.cn/api-reference/%E6%A8%A1%E5%9E%8B-api/%E5%AF%B9%E8%AF%9D%E8%A1%A5%E5%85%A8 | BigModel chat completions support text, image, audio, video, file inputs, streaming/non-streaming output, tools, and sampling controls. |
| Zhipu HTTP API guide | https://docs.bigmodel.cn/cn/guide/develop/http/introduction | Standard REST API; common endpoint is `https://open.bigmodel.cn/api/paas/v4/chat/completions` with bearer-token authorization. |
| GLM-4.6 | https://docs.bigmodel.cn/cn/guide/models/text/glm-4.6 | Text-output model with improved reasoning, tool use, long context, and Chinese writing quality. |
| GLM-4.5 | https://docs.bigmodel.cn/cn/guide/models/text/glm-4.5 | Agent-oriented GLM model family with thinking and non-thinking modes. |
| ListenHub image skill | https://listenhub.ai/docs/en/skills/image | Agent-facing image generation skill for text prompts and optional reference images. |
| ListenHub image API | https://listenhub.ai/docs/en/openapi/api-reference/image-generation | `POST /v1/images/generation`; supports `provider`, `model`, `prompt`, `imageConfig`, and `referenceImages`. |

## Reasoning Model Adapter Notes

The reasoning adapter should receive a structured outfit decision payload rather than raw, unorganized user text whenever possible:

```yaml
reasoning_input:
  user_profile: {}
  scenario:
    occasion: ""
    date_time: ""
    weather: ""
    formality: ""
  wardrobe_inventory: []
  selected_items: []
  constraints:
    comfort: []
    body_fit: []
    avoid: []
  output_requirements:
    language: "zh-CN"
    mobile_length: "short"
    image_board_required: true
```

It should return:

```yaml
reasoning_output:
  recommendation_summary: ""
  selected_outfit: []
  why_it_works: []
  risk_to_avoid: []
  image_prompt_brief: ""
  fidelity_locks: []
```

Keep the reasoning output compact. The final user-facing answer should usually be a short Chinese text recommendation plus a generated outfit board image.

## MiniMax Reasoning Adapter Notes

Recommended MiniMax reasoning config:

```yaml
provider: "minimax"
model: "MiniMax-M3"
adapter: "openai_compatible | anthropic_compatible"
endpoint_env: "MINIMAX_API_BASE"
auth_type: "api_key"
api_key_env: "MINIMAX_API_KEY"
supports_thinking: true
supports_tools: true
```

Model selection:

- `MiniMax-M3`: preferred default for outfit reasoning when MiniMax is configured; use it for long wardrobe context, multimodal-aware reasoning, and agent-style structured decisions.
- `MiniMax-M2.7` or `MiniMax-M2.7-highspeed`: use when the host prefers lower latency or cost.
- `MiniMax-M2.5` or related M2.x variants: keep as fallback options only when explicitly configured.

Operational notes:

- Use OpenAI-compatible Chat Completions when the host already uses OpenAI-style clients.
- Use Anthropic-compatible Messages when the host uses Claude-style adapters or needs thinking/tool content blocks.
- The reasoning step may be text-only if image input has already produced `wardrobe_inventory`; do not resend raw images unless the MiniMax adapter is intentionally handling both vision and reasoning.

## Zhipu GLM Reasoning Adapter Notes

Recommended Zhipu/GLM reasoning config:

```yaml
provider: "zhipu"
model: "glm-4.6"
adapter: "native_api"
endpoint_env: "ZHIPU_API_BASE"
auth_type: "api_key"
api_key_env: "ZHIPU_API_KEY"
supports_thinking: true
supports_tools: true
```

Default endpoint:

```text
https://open.bigmodel.cn/api/paas/v4/chat/completions
```

Model selection:

- `glm-4.6`: preferred for Chinese styling explanation, long-context reasoning, and tool-aware workflows when available.
- `glm-4.5`: strong agent-oriented fallback with thinking/non-thinking mode support.
- `glm-4.7-flash`: use for lower-cost or faster first-pass recommendations when configured.
- `glm-5.x`: allow newer GLM models through configuration when the host account exposes them.

Operational notes:

- Use bearer-token authorization via `ZHIPU_API_KEY`; never store the key in the skill.
- GLM reasoning is useful for Chinese-first tone, scenario interpretation, and turning structured wardrobe inventory into natural advice.
- If the GLM model supports multimodal input in the host account, it may also handle image input, but keep that capability separate from the reasoning role in `model_capabilities`.

## OpenAI Adapter Notes

Image output:

```yaml
provider: "openai"
model: "gpt-image-2"
adapter: "api"
auth_type: "api_key"
api_key_env: "OPENAI_API_KEY"
supports_reference_images: true
supports_chinese_text_rendering: true
```

Use cases:

- Text-to-image outfit board generation.
- Edit/reference-image workflows when uploaded garments should influence the board.
- Best for high-fidelity boards when the runtime already has OpenAI image generation enabled.

Operational notes:

- Prefer the Image API for a single board from one prompt.
- Prefer the Responses API image tool for multi-turn visual refinement.
- `gpt-image-2` supports configurable size, quality, and output format. Use portrait sizes such as `1024x1536` or higher portrait resolutions when available for mobile boards.
- If reference garments are used, include the skill's fidelity locks and expect higher input cost because image inputs are processed at high fidelity.

## ListenHub Image Adapter Notes

Use ListenHub as an `image_output` adapter when the host environment exposes ListenHub Skills or the ListenHub OpenAPI.

Recommended ListenHub configs:

```yaml
provider: "listenhub"
model: "gemini-3-pro-image | gemini-3.1-flash-image | gpt-image-2"
adapter: "skill | api"
endpoint_env: "LISTENHUB_API_BASE"
auth_type: "api_key"
api_key_env: "LISTENHUB_API_KEY"
underlying_provider: "google | openai"
supports_reference_images: true
supports_chinese_text_rendering: true
```

API contract:

```yaml
method: POST
path: /v1/images/generation
request:
  provider: "google | openai"
  model: ""
  prompt: ""
  imageConfig:
    aspectRatio: "9:16"
    imageSize: "1K | 2K | 4K"
  referenceImages:
    - fileData:
        fileUri: ""
        mimeType: ""
    - inlineData:
        data: ""
        mimeType: ""
response:
  image_data: "base64 image data in model output"
```

Outfit-stylist usage:

- Send the final `Structured OOTD Styling Card` prompt as `prompt`.
- Use `imageConfig.aspectRatio: "9:16"` for default mobile outfit boards when supported.
- Use `imageConfig.imageSize: "2K"` as a practical default; use `4K` only when the user asks for a polished shareable board and the host budget allows it.
- For uploaded garments, pass reference images through `referenceImages` and include fidelity locks in the prompt.
- For GPT-image-2 through ListenHub, set `provider: "openai"` and `model: "gpt-image-2"`.
- For Gemini through ListenHub, set `provider: "google"` and choose the configured Gemini image model.
- If the ListenHub Skill returns inline display output, use it directly; if the OpenAPI returns base64, save or attach it as the generated outfit board artifact.

Reference-image notes:

- ListenHub supports URL references through `fileData`.
- ListenHub API supports base64 references through `inlineData`.
- Keep image reference count within the underlying model limits. The official API notes max 14 references for Gemini models and max 4 for GPT-image-2.

## Gemini Adapter Notes

Image output:

```yaml
provider: "gemini"
model: "gemini-3-pro-image | gemini-3.1-flash-image | gemini-2.5-flash-image"
adapter: "api"
auth_type: "api_key"
api_key_env: "GEMINI_API_KEY"
supports_reference_images: true
supports_chinese_text_rendering: true
```

Model selection:

- `gemini-3-pro-image` / Nano Banana Pro: choose for the most polished outfit board, complex layout, and Chinese text accuracy.
- `gemini-3.1-flash-image` / Nano Banana 2: choose for higher-throughput outfit boards.
- `gemini-2.5-flash-image` / Nano Banana: choose for fast creative drafts.

OpenClaw or host aliases:

- Treat `nano-banana-pro-preview` as Nano Banana Pro / Gemini Pro image generation.
- Treat `nano-banana` or `gemini-2.5-flash-image` as the fast draft family.
- If the host supports both, prefer Nano Banana Pro for this skill's default outfit boards.

Quality note for Nano Banana Pro:

- Use it for the normal polished `Structured OOTD Styling Card` output.
- Send a full visual-spec prompt, not a short mood prompt.
- Use `aspectRatio: "9:16"` and `imageSize: "2K"` as the default.
- Pass uploaded garment images as references when the host supports reference images.
- Keep visible Chinese labels short but explicit; Pro can handle text better than fast draft models, but dense paragraph blocks still reduce quality.
- For correction turns, prefer targeted edits that preserve outfit and item fidelity rather than regenerating the whole board.

Quality note for `gemini-2.5-flash-image`:

- Treat this as a fast draft model, not the highest-fidelity outfit-board model.
- It can collapse structured prompts into a single photorealistic lookbook image if the prompt starts with a lifestyle description.
- Always use the Gemini 2.5 Flash compact hard-layout prompt from `references/outfit-board-prompting.md`.
- Put "structured OOTD styling card / outfit board" in the first line of the prompt.
- Keep Chinese label groups to 5-6 max. If text quality is unstable, generate a numbered board and provide Chinese mapping in the message body.
- Prefer `gemini-3-pro-image` or `gpt-image-2` for polished shareable cards when available.

Image input:

```yaml
provider: "gemini"
model: "gemini-3.5-flash"
adapter: "api"
auth_type: "api_key"
api_key_env: "GEMINI_API_KEY"
```

Operational notes:

- Use `generateContent` for both image understanding and Nano Banana image generation.
- For clothing analysis, send smaller images inline when the total request stays within provider limits; use the File API for larger or repeated wardrobe images.
- Gemini is a strong default for multi-image wardrobe inspection because it accepts multiple image parts in one prompt.

## MiniMax Adapter Notes

Image input:

```yaml
provider: "minimax"
model: "MiniMax-M3 | minimax-vision"
adapter: "api | cli | mcp"
auth_type: "api_key"
api_key_env: "MINIMAX_API_KEY"
endpoint_env: "MINIMAX_API_HOST"
```

Recommended image-input adapters:

- API: `MiniMax-M3` through Anthropic-compatible or OpenAI-compatible multimodal message content.
- CLI: `mmx vision describe --image <path-or-url>`.
- MCP: MiniMax Token Plan MCP `understand_image`.

Use API mode when the host already has MiniMax-M3 configured. Use CLI/MCP mode when the agent environment exposes the dedicated image understanding tool.

Image output:

```yaml
provider: "minimax"
model: "image-01"
adapter: "api"
auth_type: "api_key"
api_key_env: "MINIMAX_API_KEY"
endpoint_env: "MINIMAX_API_HOST"
supports_reference_images: true
supports_chinese_text_rendering: null
```

Operational notes:

- MiniMax-M3 can inspect clothing images directly through multimodal chat input.
- In OpenClaw-style environments, MiniMax CLI/MCP may be simpler and more reliable than hand-rolling image payloads.
- Do not assume older MiniMax M-series text models can inspect images; use M3 or the dedicated vision tool path.

## DeepSeek Adapter Notes

Reasoning/text adapter:

```yaml
provider: "deepseek"
model: "deepseek-v4-flash | deepseek-v4-pro"
adapter: "api"
auth_type: "api_key"
api_key_env: "DEEPSEEK_API_KEY"
endpoint_env: "DEEPSEEK_API_BASE"
supports_raw_image_input: false
```

Use cases:

- Reason over an already-extracted wardrobe inventory.
- Produce structured JSON styling decisions after another vision provider has described the clothing.
- Act as a custom provider only if the host runtime exposes a separate DeepSeek-compatible multimodal endpoint.

Operational notes:

- Official DeepSeek docs expose OpenAI/Anthropic-compatible chat APIs and current `deepseek-v4-*` model families.
- If no separate vision endpoint is configured, do not send raw images to DeepSeek. Use Gemini, OpenAI, MiniMax, or custom vision first, then pass the structured inventory into DeepSeek for reasoning.

## Jimeng / Volcengine Adapter Notes

Jimeng image models use the Volcengine visual endpoint and signed headers. The portable skill should only describe the contract; the host runtime owns signing, polling, downloading, and storage.

Shared adapter details:

```yaml
provider: "jimeng"
endpoint: "https://visual.volcengineapi.com"
auth_type: "ak_sk"
region: "cn-north-1"
service: "cv"
action: "CVSync2AsyncSubmitTask"
version: "2022-08-31"
```

Recommended Jimeng output models:

| Model id | Best use | Key constraints |
| --- | --- | --- |
| `jimeng_t2i_v30` | Text-only outfit board generation when no reference garment image is needed | `req_key: jimeng_t2i_v30`; prompt supports Chinese/English; keep prompts concise, with a hard ceiling around 800 characters; supports `use_pre_llm`, `seed`, `width`, `height`, and URL/base64 result handling. |
| `jimeng_seedream46_cvtob` | Outfit board generation with uploaded clothing references | `req_key: jimeng_seedream46_cvtob`; supports `image_urls` reference input; use this when uploaded garments must be preserved; supports `scale`, `size`, `width`, `height`, and `force_single`. |

Reference-image guardrails for `jimeng_seedream46_cvtob`:

- Uploaded images should be JPEG/PNG and within the provider's file and resolution limits.
- Prefer no more than 6 reference images for stable outfit boards, even if the provider accepts more.
- Use strict fidelity locks in the prompt: category, length, color, silhouette, visible trims, and key pattern details.
- If the original item is a long trouser, coat, vest, Polo, or knit top, the generated board must keep that garment type and length.

Mobile vertical board sizing:

- Prefer `9:16` for phone-first outfit boards when accepted by the adapter.
- If a provider rejects the exact size, keep the board vertical and fall back to the closest supported dimensions rather than changing the template.
- For long image boards, keep the same visual template and extend downward only when the user asks for more detail.

Result handling:

- Some Jimeng responses may return temporary image URLs; host runtimes should download or persist the image artifact because provider URLs may expire.
- If the adapter only returns base64 image data, save it as a local generated artifact before replying to the user.

## Runtime Behavior

- If image input is configured, inspect clothing images for category, color, silhouette, texture cues, formality, season, condition, and pairing risks.
- If image input is unavailable, say briefly: "当前环境没有配置图片识别模型。你可以用文字描述这几件衣服，我会继续帮你搭配。" Then continue text-only.
- If image output is configured, generate an outfit board by default for styling recommendations. Do not wait for the user to explicitly ask for an image.
- If image output is unavailable, the user asks for text only, or the current agent cannot call an image generator, say briefly: "当前环境没有配置图片生成模型。我先给你一版文字搭配板。" Then provide a text board.
- Never block the whole recommendation because image input or output is unavailable.
- Keep provider names, model names, credentials, and endpoint details outside the skill instructions.

## Image Input Output Contract

Image input models should produce this compact inventory before styling:

```yaml
items:
  - id: "image_1"
    category: ""
    length: ""
    color_family: ""
    silhouette: ""
    material_cues: []
    key_details: []
    formality: ""
    season_weight: ""
    uncertainty: ""
```

Image output models should receive:

- provider-aware prompt profile selected from the configured image output model
- final selected outfit
- fidelity locks for uploaded items
- text labels in Chinese
- default template: `Structured OOTD Styling Card`
- negative constraints: no extra people, no mini photos, no drinks/phones/wallets/receipts, no changing uploaded garment type or length
- the complete compiled image prompt, not a short mood prompt or English lookbook summary

Prompt profile selection:

```yaml
image_prompt_profile:
  openai_gpt_image:
    match: ["gpt-image-2", "gpt-image"]
    style: "full structured OOTD board prompt"
  google_nano_banana_pro:
    match: ["nano-banana-pro-preview", "gemini-3-pro-image", "gemini-3-pro-image-preview"]
    style: "visual-spec prompt with composition, typography, exact Chinese text, references"
  google_nano_banana_fast:
    match: ["gemini-2.5-flash-image", "nano-banana"]
    style: "compact hard-layout prompt, explicit not-a-photo guardrail"
  jimeng_seedream:
    match: ["jimeng", "seedream"]
    style: "concise structured prompt with fidelity locks near the end"
  minimax_image:
    match: ["minimax", "image-01"]
    style: "explicit item zones, limited text panels"
  numbered_fallback:
    match: ["weak_chinese_text", "repeated_text_garbling"]
    style: "numbered markers on image, Chinese mapping in message body"
```

If no model name is available, default to the full `Structured OOTD Styling Card` prompt and include the pre-call rejection rule from `references/outfit-board-prompting.md`.

Invalid image-output prompt examples:

```text
Casual weekend dad outfit in light rain...
A man wearing a black knit top and rain jacket...
Fashion photo of a person in...
```

These prompts are invalid because they can produce a standalone model photo. Rewrite them into a structured outfit-board prompt before calling image generation.

## Text Board Fallback

Use this only when image output cannot be generated or the user explicitly asks for text only.

```markdown
## 搭配板文字版

主色：
廓形：
材质感觉：

上衣：
下装：
鞋：
包/配饰：
外套：

视觉效果：
```
