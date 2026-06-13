# End-to-End Workflow

Use this workflow when implementing, revising, or executing a complete outfit recommendation. Keep model roles separate:

```text
reasoning model -> outfit judgment and recommendation logic
image input model -> clothing/photo understanding
image output model -> generated outfit board
```

Do not assume the same provider handles all three.

## Capability Resolution

Before producing the recommendation, resolve the current runtime capability state:

```yaml
capabilities:
  reasoning:
    enabled: true_or_false
    provider: ""
    model: ""
  image_input:
    enabled: true_or_false
    provider: ""
    model: ""
  image_output:
    enabled: true_or_false
    provider: ""
    model: ""
```

Behavior rules:

- If `reasoning.enabled` is true, use it for outfit selection, tradeoff judgment, and the image prompt brief.
- If `reasoning.enabled` is false, use the current agent's language model with the same structured reasoning contract.
- If images are uploaded and `image_input.enabled` is true, inspect images before recommending outfits.
- If images are uploaded and `image_input.enabled` is false, ask the user for a short text description only when the image content is essential; otherwise proceed with visible context and state the limitation briefly.
- If `image_output.enabled` is true, generate an outfit board by default after the text advice.
- If `image_output.enabled` is false, provide the explicit text-board fallback.

## Workflow A: Text-Only Scenario

Use when the user gives occasion, weather, profile, or style needs without clothing photos.

```text
user request
-> identify scenario and missing essentials
-> resolve profile and weather/occasion constraints
-> reasoning payload
-> selected outfit + backup adjustment + avoid section
-> concise Chinese text recommendation
-> outfit-board prompt
-> generated outfit board if image output is available
```

Reasoning payload:

```yaml
mode: scenario_outfit
user_profile: {}
scenario: {}
available_items: []
preferences: []
constraints: []
requested_output:
  text: true
  image_board: true
  language: zh-CN
```

## Workflow B: Single Uploaded Item

Use when the user uploads one garment or accessory and asks how to match it.

```text
uploaded image
-> image input model creates item inventory
-> lock anchor item fidelity
-> combine with user scenario/profile
-> reasoning model chooses supporting pieces
-> concise Chinese text recommendation
-> outfit-board prompt with anchor fidelity locks
-> generated outfit board if image output is available
```

Required inventory:

```yaml
anchor_item:
  id: image_1
  category: ""
  length: ""
  color_family: ""
  silhouette: ""
  material_cues: []
  key_details: []
  formality: ""
  uncertainty: ""
```

Do not replace the anchor item. Added pieces should support the uploaded item and the occasion.

## Workflow C: Multi-Image Wardrobe Combination

Use when the user uploads several clothing images and asks what can be combined.

```text
uploaded image set
-> image input model creates compact wardrobe inventory
-> classify each item as use / backup / avoid for this occasion
-> reasoning model selects one strongest outfit
-> optional backup adjustment
-> concise Chinese text recommendation
-> outfit-board prompt using selected items and fidelity locks
-> generated outfit board if image output is available
```

Inventory and decision contract:

```yaml
wardrobe_inventory:
  - id: image_1
    category: ""
    length: ""
    color_family: ""
    silhouette: ""
    key_details: []
    occasion_fit: ""
    decision: use_or_backup_or_avoid
    reason: ""
selected_outfit:
  top: ""
  bottom: ""
  shoes: ""
  bag: ""
  accessories: ""
  outerwear: ""
missing_suggestions: []
fidelity_locks: []
```

If shoes, bag, belt, or outerwear are not uploaded, label them as suggested additions. Never imply the user uploaded them.

## Workflow D: Outfit Diagnosis

Use when the user asks whether a proposed combination works.

```text
proposed outfit by text or images
-> inventory/parse proposed pieces
-> diagnose before replacing
-> reasoning model returns verdict and minimal fixes
-> concise Chinese diagnosis
-> corrected outfit-board prompt
-> generated outfit board if image output is available
```

Diagnosis verdict must be one of:

```text
可以穿
可以但要调整
不建议这样穿
```

Change only 1-2 pieces when possible. Do not rebuild the whole outfit unless the proposed outfit conflicts with the occasion, weather, comfort, or safety.

## Fallback Paths

### No Reasoning Model

Use the current agent model and preserve the same structured reasoning contract. Do not tell the user this unless it affects quality or configuration.

### No Image Input Model

If images are essential:

```text
当前环境没有配置图片识别模型。你可以简单描述这几件衣服的颜色、版型和材质，我会继续帮你搭配。
```

If the user already provided enough visible/text context, continue and mention uncertainty briefly.

### No Image Output Model

Say this once, then provide the text board:

```text
当前环境没有配置图片生成模型。我先给你一版文字搭配板。
```

Do not silently omit the image when the normal deliverable should include one.

## Final Assembly

Normal final output order:

```text
1. concise Chinese text recommendation
2. generated outfit board image
3. if image generation failed, explicit fallback note + text board
```

Text recommendation should stay mobile-friendly:

- one strongest outfit
- one optional adjustment or backup
- one "穿搭雷区"
- one short reason for the main choice
- avoid long theory unless the user asks for detailed analysis

The outfit-board prompt should include:

- scenario and weather
- user profile essentials
- selected outfit
- one scenario prompt pack from `references/outfit-board-prompting.md`
- anchor or wardrobe fidelity locks
- Chinese labels
- default `Structured OOTD Styling Card` template
- hard exclusions from `references/outfit-board-prompting.md`

Scenario pack mapping:

```yaml
business_client_visit:
  use_for: client meetings, finance customers, interviews, office presentations, business social events
casual_weekend:
  use_for: friend meetups, cafes, city walks, shopping, museum visits
outdoor_sport_travel:
  use_for: hiking, seaside, rainy days, travel, walking-heavy plans
social_date_gathering:
  use_for: meals, dates, parties, gatherings, wedding guest situations
wardrobe_multi_image:
  use_for: uploaded wardrobe sets, existing-clothes combinations, outfit diagnosis with images
```

If two packs seem relevant, choose the one that best protects the user's real-world situation. Example: for "business gathering with uploaded clothes", use `wardrobe_multi_image` plus business labels and risk notes, not two full visual styles.
