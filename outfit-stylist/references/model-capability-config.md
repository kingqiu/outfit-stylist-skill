# Model Capability Configuration

Keep styling reasoning separate from image model availability. Image input and image output may be provided by different models.

## Schema

```yaml
model_capabilities:
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
    provider: "" # openai | gemini | minimax | jimeng | custom
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

### Image Output

| Provider family | Use for | Notes |
| --- | --- | --- |
| `openai` / `gpt-image-2` | Default high-quality outfit board generation where available | Use for `Structured OOTD Styling Card`; keep model name configurable. |
| `gemini` / Nano Banana family | Alternative outfit board generation and reference-image workflows | Official Gemini image model family includes Nano Banana, Nano Banana 2, and Nano Banana Pro; exact model id belongs in config. |
| `minimax` / image generation | Optional image generation backend | Official MiniMax API supports text-to-image and image-to-image generation through `/v1/image_generation`; keep model and provider limits configurable. |
| `jimeng` | ByteDance/Jimeng image generation | Support Volcengine Jimeng/Seedream adapters, especially text-to-image and reference-image outfit boards. |
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

## Official Provider Notes

These notes reflect official provider documentation checked on 2026-06-13. Because model names and limits change, treat exact model ids as configuration values rather than hardcoded skill logic.

| Provider | Official docs | Skill adapter note |
| --- | --- | --- |
| OpenAI GPT Image | https://developers.openai.com/api/docs/guides/image-generation | Use `gpt-image-2` for image output when available. The Image API supports generation and edits; the Responses API can support conversational image workflows. |
| OpenAI vision | https://developers.openai.com/api/docs/guides/images-vision | Use a vision-capable mainline model for image input if the host config chooses OpenAI for clothing analysis. Images can be provided by URL, base64 data URL, or file id depending on endpoint. |
| Google Gemini image generation | https://ai.google.dev/gemini-api/docs/image-generation | "Nano Banana" is the official Gemini native image-generation family. Prefer `gemini-3-pro-image` for complex, polished outfit boards; use `gemini-3.1-flash-image` or `gemini-2.5-flash-image` for speed/cost. |
| Google Gemini image understanding | https://ai.google.dev/gemini-api/docs/image-understanding | Good default for multi-image wardrobe inspection. Supports inline image data for smaller requests and File API for larger/reused images. |
| MiniMax API | https://platform.minimax.io/docs/api-reference/api-overview | MiniMax exposes language, speech, video, image, music, and file APIs. |
| MiniMax image generation | https://platform.minimax.io/docs/api-reference/image-generation-t2i | MiniMax supports text-to-image through `/v1/image_generation`; image-to-image uses the same image generation family. |
| MiniMax M3 multimodal input | https://platform.minimax.io/docs/api-reference/text-chat-anthropic | `MiniMax-M3` supports text, image, and video input through Anthropic-compatible content blocks. |
| MiniMax image understanding for agents | https://platform.minimaxi.com/docs/token-plan/openclaw | For OpenClaw-style environments, use `mmx vision describe --image <path-or-url>` or MiniMax MCP `understand_image` as the image-input adapter. |
| DeepSeek API | https://api-docs.deepseek.com/ | Official DeepSeek API is OpenAI/Anthropic-compatible for text/reasoning chat. Do not list it as a guaranteed native vision provider. |
| DeepSeek models/pricing | https://api-docs.deepseek.com/quick_start/pricing | Current official model families include `deepseek-v4-flash` and `deepseek-v4-pro`; use them for reasoning over extracted clothing inventories, not raw image inspection unless a vision adapter is separately supplied. |

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

- final selected outfit
- fidelity locks for uploaded items
- text labels in Chinese
- default template: `Structured OOTD Styling Card`
- negative constraints: no extra people, no mini photos, no drinks/phones/wallets/receipts, no changing uploaded garment type or length

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
