# Outfit Board Prompting

Outfit board quality is core to this skill. Use vertical, mobile-first composition and Chinese labels.

## Image Requirements

- Default aspect ratio: `9:16`.
- Use `3:4` for simpler boards.
- Use long vertical, such as `9:21`, when one mobile screen cannot fit the content.
- Use phone-friendly width, such as 1080px, if the backend supports explicit dimensions.
- Default visual output should be an outfit board, not a portrait, selfie, product ad, or virtual try-on.
- Use Chinese for all visible advice, titles, callouts, styling notes, and labels.
- Use English only for unavoidable brand names or fashion terms.
- Prefer readable labels over dense paragraphs.
- Include top, bottom, shoes, bag, accessories, and optional outerwear.
- Use color chips, fabric swatches, detail panels, annotation lines, and short callouts.
- Limit visible text. Do not let the image model invent large extra explanation panels.
- Each callout should be no more than 12 Chinese characters per line and no more than 2 lines.
- Use at most 6 callout groups unless the user asks for a long detailed board.

## Board Density Modes

Choose one density mode before prompting:

- **Structured OOTD styling card**: preferred default visual style. Clear top header, central illustrated outfit figure, surrounding garment cards, short item notes, fabric circles, and a compact bottom analysis strip for color/proportion/risk/occasion. Best for normal outfit-board output.
- **Deconstructed OOTD board**: use when the user wants a looser collage feeling. One central outfit figure or outfit flat-lay, surrounding garment/accessory cutouts, hand-drawn arrows, paper texture, light editorial collage.
- **Compact board**: use when text control and fast phone scanning matter most. One hero outfit, 3-4 detail panels, 5 labels max, no extra text panels.
- **Detailed board**: use only when the user asks for a shareable long image, Xiaohongshu-style card, or detailed styling board. One hero outfit, 4-6 detail panels, color chips, short styling notes, still no dense paragraphs.
- **Numbered board**: use when Chinese text rendering is unreliable. Image contains numbered markers only; provide Chinese text separately.

## Figure Style

Prefer an illustrated fashion figure over a photorealistic model for the default board.

Why:

- It feels more like styling advice than a simulated personal photo.
- It reduces false expectations of virtual try-on or exact body/face matching.
- It makes the board more reusable across genders, ages, and body types.
- It pairs better with hand-drawn arrows, paper texture, and item cutouts.
- It avoids over-focusing on the model's face or attractiveness.

Default figure style:

```text
semi-realistic fashion illustration, clean Korean/Japanese magazine illustration style,
natural adult proportions, soft facial detail, tasteful expression, not anime, not childish,
not photorealistic, not a celebrity portrait, outfit accuracy prioritized over face detail
```

Use photorealistic figures only when the user explicitly asks for editorial photography, realistic lookbook, or model-style output. Even then, do not imply true virtual try-on.

## Context Tuning

- Business/client visit: cleaner spacing, quieter pose, muted color palette, fewer decorative elements, no playful stickers.
- Outdoor/sport: prioritize function panels such as sun, rain, grip, breathability, and temperature layering.
- Social/date: allow warmer color chips and softer accessories, but keep labels brief.
- Single uploaded item: treat the uploaded item as the anchor. Keep it recognizable and make every added piece support it.

## Prompt Assembly Order

Build the image prompt in this order so every model receives the same essential constraints:

```text
0. resolve image_output provider/model and select a prompt profile
1. base template: Structured OOTD Styling Card
2. primary scenario pack: business / commute / travel / ceremony / family / campus / fitness / casual / outdoor / social / wardrobe
3. optional weather function pack: heat / cold / rain / wind / sun
4. selected outfit details
5. uploaded-item fidelity locks, if any
6. Chinese text fields
7. hard exclusions
8. provider-specific prompt profile, size, and reference-image settings
```

Do not start from a freeform aesthetic prompt alone. The board should always be grounded in the final outfit recommendation and any uploaded wardrobe items.

## Provider-Aware Prompt Profile Selection

Before writing the final text-to-image prompt, inspect the configured `image_output.provider` and `image_output.model`. Then choose one prompt profile:

| Profile | Use When | Prompt Shape |
| --- | --- | --- |
| `openai_gpt_image` | `gpt-image-2` or OpenAI-compatible GPT Image | Full structured prompt with detailed layout, Chinese labels, and reference-item fidelity locks. |
| `google_nano_banana_pro` | `nano-banana-pro-preview`, `gemini-3-pro-image`, `gemini-3-pro-image-preview` | Visual-spec prompt. Treat the prompt like a design brief with composition, typography, layout, exact Chinese snippets, and reference behavior. |
| `google_nano_banana_fast` | `gemini-2.5-flash-image`, `nano-banana`, fast/preview Gemini image models | Compact hard-layout prompt. Start with the artifact type and explicit "not a single photo" constraint. Keep labels to 5-6 groups. |
| `jimeng_seedream` | Jimeng / Seedream / Volcengine image models | Concise prompt with layout first, selected outfit second, fidelity locks near the end, and short Chinese labels. |
| `minimax_image` | MiniMax image generation | Explicit item zones, fewer text panels, strong negative constraints against standalone portraits. |
| `numbered_fallback` | Any model with weak Chinese rendering or repeated garbled text | Generate a numbered outfit board with markers 1-6 and provide exact Chinese mapping in the message body. |

Selection rules:

- If the model name contains `pro` and belongs to Google/Gemini/Nano Banana, prefer `google_nano_banana_pro`.
- If the model name contains `2.5-flash`, `flash-image`, or plain `nano-banana`, prefer `google_nano_banana_fast`.
- If the model repeatedly outputs a standalone person photo, downgrade to `google_nano_banana_fast` hard-layout style even when the model is stronger.
- If the model repeatedly garbles Chinese labels, switch to `numbered_fallback`.
- If a user uploads garment images and the model supports references, pass references and include fidelity locks. If references cannot be passed, describe the anchor item with stricter category/color/length/silhouette locks.

The model profile affects only prompt compilation, not the styling decision. The same selected outfit should be preserved across profiles.

## Image Prompt Compiler Contract

The reasoning model must compile a complete outfit-board prompt before any image model call. Do not let the host agent or reasoning model reduce the prompt to a plain English lookbook sentence.

The compiled prompt must include all of these fields:

```yaml
image_prompt_contract:
  output_type: "structured_ootd_styling_card"
  forbidden_output_type: "single photorealistic model photo, studio portrait, catalogue lookbook, virtual try-on"
  aspect_ratio: "9:16"
  visual_template: "natural editorial Structured OOTD Styling Card"
  figure_style: "semi-realistic fashion illustration or illustrated flat-lay, not photorealistic"
  required_layout:
    - top Chinese title / scene / reason area
    - one central complete outfit figure or outfit flat-lay
    - surrounding item cards for top, bottom, shoes, bag, accessories, outerwear when relevant
    - fabric/color/detail callouts
    - compact bottom analysis strip or paper-note summary
  selected_outfit: {}
  fidelity_locks: []
  chinese_text_fields: {}
  hard_exclusions: []
  provider_notes: []
```

Pre-call rejection rule:

- If the image prompt reads like "a person wearing..." or "fashion photo of..." and does not explicitly request an OOTD styling card / outfit board layout, reject it and rewrite it.
- If the prompt does not mention surrounding item cards, Chinese labels, and bottom analysis/paper notes, reject it and rewrite it.
- If the prompt asks for a photorealistic person, studio background, catalogue lookbook, or virtual try-on without explicit user request, reject it and rewrite it.
- If uploaded-item fidelity locks are missing for user-provided garment images, reject it and rewrite it.

Use this short self-check before calling image generation:

```text
Does this prompt force an outfit-board infographic rather than a single person photo?
Does it force Chinese labels or numbered markers?
Does it include the exact selected outfit and uploaded-item fidelity locks?
Does it forbid studio portrait / catalogue / virtual try-on output?
```

Only call the image model after all answers are yes.

## Scenario Prompt Packs

Choose one primary scenario pack. Add one optional weather function pack when weather materially changes comfort, safety, or item choice.

### Business / Client Visit

Use for customer meetings, finance clients, interviews, office presentations, formal dinners, or business social events.

```text
Business styling pack:
quiet professional tone, clean spacing, restrained palette, polished but not stiff,
subtle paper notes for scene/reason/risk, calm confident posture,
no playful stickers, no streetwear exaggeration, no nightlife props.
Prioritize garment fit, trouser length, shoe polish, bag structure, and refined accessories.
Risk reminder should focus on avoiding too casual, too sporty, too loud, or too tight.
Menswear: use full-length or slight-break trousers in business scenes; avoid cropped hems and excessive ankle exposure.
Womenswear: keep neckline, hemline, and accessories occasion-appropriate; avoid party styling unless requested.
```

Chinese label suggestions:

```text
标题：稳妥商务 OOTD / 柔和商务 OOTD / 客户拜访 OOTD
首选理由：专业但不硬 / 干净稳妥 / 有分寸感
风险提醒：别配运动鞋 / 避免太休闲 / 别太抢眼
```

### Commute / Office Daily

Use for ordinary workdays, commuting, shared offices, hybrid work, internal meetings, office-to-dinner transitions, or "tomorrow to work" requests.

```text
Commute office pack:
practical daily polish, comfortable for transit and sitting, office temperature awareness,
clean but not client-meeting formal, repeatable wardrobe feeling, low-maintenance fabrics.
The outfit should look intentional in the office and comfortable during commute.
Prioritize wrinkle resistance, walkable shoes, layers for air conditioning, bag practicality, and quiet color coordination.
Risk reminder should focus on looking too casual, overheating on commute, freezing in office AC, or shoes that are uncomfortable for walking.
```

Chinese label suggestions:

```text
标题：通勤日常 OOTD / 办公室 OOTD / 上班轻正式
首选理由：舒服又得体 / 通勤不累 / 办公室刚好
风险提醒：别太像周末 / 注意空调温差 / 鞋要能走
```

### Business Travel / Transit

Use for flights, high-speed rail, business trips, arriving then meeting people, light packing, or day-to-night travel plans.

```text
Business travel pack:
travel-ready but presentable, wrinkle-resistant layers, comfortable waistband or stretch,
compact bag logic, shoes that handle walking and still look polished.
Show one optional layer or jacket for temperature shifts, and keep the board tidy rather than tourist-like.
Avoid luggage piles, boarding passes, passports, maps, phones, tickets, coffee cups, or travel props unless explicitly requested.
Risk reminder should focus on wrinkling, uncomfortable waist/shoes, overpacking, or arriving underdressed.
```

Chinese label suggestions:

```text
标题：差旅轻装 OOTD / 高铁出差 OOTD / 到达即见人
首选理由：久坐不皱 / 到站能见人 / 一套多场景
风险提醒：别穿易皱面料 / 鞋别太新 / 外套别太厚
```

### Ceremony / Wedding / Banquet

Use for weddings, banquets, formal ceremonies, annual meetings, gala dinners, guest-of-honor situations, or family formal occasions.

```text
Ceremony banquet pack:
formal and respectful, polished occasionwear, clean lines, refined texture,
avoid stealing attention from hosts or the couple, avoid overly casual shoes or bags.
Use elegant item cards for jacket/dress/suit/shoes/bag/accessory, with restrained shine if appropriate.
Risk reminder should focus on dress-code mismatch, overexposure, too much sparkle, all-white wedding guest looks, funeral-like heaviness, or shoes that cannot stand/walk for long.
```

Chinese label suggestions:

```text
标题：婚礼宾客 OOTD / 宴会得体 OOTD / 正式场合 OOTD
首选理由：得体不抢眼 / 正式有分寸 / 体面耐看
风险提醒：别抢主角 / 鞋要能久站 / 避免过度闪
```

### Family / Parent-Child

Use for family gatherings, meeting parents, parent-child outings, school pickup, parks, errands with children, or relaxed family photos.

```text
Family parent-child pack:
warm approachable tone, easy movement, washable or forgiving fabrics,
not too precious, not too sloppy, friendly color balance.
Prioritize comfort, movement range, pockets/bag usefulness, stain tolerance, and approachable styling.
Avoid fragile fabrics, sharp accessories, impractical shoes, overly formal styling, or seductive/event-heavy styling.
Risk reminder should focus on being too stiff, hard to move, easy to stain, or too casual for family elders.
```

Chinese label suggestions:

```text
标题：家庭聚会 OOTD / 亲子出门 OOTD / 见家人穿搭
首选理由：亲和不随便 / 好活动 / 干净耐看
风险提醒：别穿太娇贵 / 鞋要好走 / 避免太硬
```

### Campus / Interview / Academic

Use for school, university, academic events, student presentations, first day of school, interviews, lectures, or research meetings.

```text
Campus academic pack:
fresh, respectful, intelligent but not overly corporate,
age-appropriate polish, neat layers, clean shoes, simple bag.
Prioritize approachability, clarity, comfort for sitting/walking, and not looking either too casual or too business-heavy.
Risk reminder should focus on looking immature, too formal for campus, too sloppy, or too trend-driven.
```

Chinese label suggestions:

```text
标题：校园得体 OOTD / 面试清爽 OOTD / 学术场合 OOTD
首选理由：清爽可信 / 年轻但稳 / 专业不老气
风险提醒：别太成熟 / 避免太街头 / 鞋面要干净
```

### Fitness / Sport-to-Daily

Use for gym, yoga, running, light sport, gym-to-cafe, workout-to-errands, or运动后还要见人.

```text
Fitness transition pack:
sport functional base with daily-ready finishing pieces,
clean layering, breathable fabrics, tidy sneakers, compact bag.
The board should not look like a pure gym gear catalogue unless the user asks for training outfit only.
Prioritize sweat management, coverage after exercise, shoe function, and a simple outer layer for daily spaces.
Avoid gym clutter, towels, bottles, headphones, phones, locker-room props, or body/fitness ranking.
Risk reminder should focus on sweat marks, see-through fabric, poor support, or looking too gym-only for the next stop.
```

Chinese label suggestions:

```text
标题：运动后也体面 / 健身通勤 OOTD / 轻运动穿搭
首选理由：能动也能见人 / 清爽不狼狈 / 功能不邋遢
风险提醒：注意汗渍 / 外搭要带 / 别太健身房
```

### Casual / Weekend

Use for friend meetups, cafes, city walks, shopping, museum visits, short trips, or relaxed non-business plans.

```text
Casual styling pack:
easy wearable mood, relaxed but intentional proportions, breathable fabrics,
slightly warmer color chips, light hand-drawn arrows, natural movement in the figure.
Keep item cards focused on clothes, shoes, bag, and accessories; do not add phones, drinks, notebooks, wallet contents, cosmetics, or random lifestyle objects.
Risk reminder should focus on avoiding sloppy, over-layered, too hot, too cold, or mismatched formality.
```

Chinese label suggestions:

```text
标题：周末轻松 OOTD / 城市休闲 OOTD / 朋友见面 OOTD
首选理由：舒服但有型 / 轻松不随便 / 清爽耐看
风险提醒：别堆太多层 / 避免像运动装 / 鞋子别太笨重
```

### Outdoor / Sport / Travel

Use for hiking, seaside, rainy days, walking-heavy travel, commuting in bad weather, or outdoor activity.

```text
Outdoor function pack:
functional but tidy outfit board, clear weather notes, breathable fabric callouts,
sun/rain/wind/grip/layering panels only when relevant, practical footwear emphasis,
lightweight bag or water-resistant layer when needed.
The outfit should still look like a complete wearable look, not a gear catalogue.
Avoid decorative adventure props, mountain posters, water bottles, maps, phones, tickets, or unrelated travel objects unless the user explicitly asks.
Risk reminder should focus on overheating, rain exposure, poor grip, chafing, or impractical shoes.
```

Chinese label suggestions:

```text
标题：轻户外 OOTD / 海边清爽 OOTD / 阵雨通勤 OOTD
首选理由：透气好走 / 防晒防雨 / 轻便不狼狈
风险提醒：鞋底要防滑 / 别穿厚重棉 / 雨天避开浅麂皮
```

### Social / Date / Gathering

Use for meals, dates, parties, gatherings, weddings as guest, or situations where warmth and approachability matter.

```text
Social styling pack:
warmer emotional tone, softer color relationships, approachable silhouette,
slightly more attention to accessories, bag, texture, and face-near color.
Keep the figure tasteful and adult; no seductive posing, no lingerie, no beauty ranking, no makeup/nail panels.
Risk reminder should focus on avoiding overdressing, underdressing, overexposure, or too many focal points.
```

Chinese label suggestions:

```text
标题：聚会得体 OOTD / 晚餐约会 OOTD / 轻松社交 OOTD
首选理由：亲和但精致 / 有亮点不夸张 / 松弛有分寸
风险提醒：别太用力 / 露肤别过多 / 亮点只留一个
```

### Wardrobe / Multi-Image

Use when the user uploaded several wardrobe items and wants a combination.

```text
Wardrobe fidelity pack:
show only selected uploaded items in the central outfit,
place backup items only in small backup cards if needed,
do not include avoided uploaded items in the central look,
mark missing shoes, bag, belt, or outerwear as suggested additions.
Preserve uploaded item category, length, color, silhouette, and visible key details.
If a reference item is a long trouser, keep it as long trousers. If it is a vest, keep it as a vest. If it is a Polo, keep the collar and short-sleeve Polo structure.
Do not improve, shorten, recolor, or replace uploaded items just to make the layout prettier.
```

Chinese label suggestions:

```text
标题：衣橱组合 OOTD / 现有衣服这样搭 / 商务聚会组合
首选理由：现有单品最稳组合 / 颜色和场合都成立 / 保留单品特点
风险提醒：未选单品别混入 / 长裤不能变短裤 / 休闲件别抢主场
```

## Weather Function Packs

Use at most one primary weather pack unless the user gives extreme or mixed weather. Weather packs should change fabric, layers, shoes, and risk notes without changing the social tone of the primary scenario pack.

### High Heat / Summer

Use for high temperature, humidity, heatwave, strong indoor/outdoor temperature contrast, or summer outdoor plans.

```text
High heat function pack:
breathable fabrics, lighter color weight when appropriate, sweat-aware layering,
looser fit around body, minimal heavy accessories, walkable breathable shoes.
Show fabric callouts such as cotton blend, linen blend, quick-dry, thin knit, or airy weave only when they match the outfit.
Risk reminder should focus on overheating, sweat marks, clingy fabric, heavy dark layers, or non-breathable shoes.
```

Chinese label suggestions:

```text
温度提示：高温透气
风险提醒：避免闷热 / 注意汗渍 / 少叠厚层
```

### Cold / Winter

Use for cold days, wind chill, indoor-outdoor temperature gaps, winter commuting, or formal cold-weather events.

```text
Cold function pack:
warm layered structure, coat or jacket logic, scarf/gloves only if needed,
keep silhouette clean rather than bulky, preserve mobility and footwear warmth.
Show warmth through layers and fabric texture, not by adding random winter props.
Risk reminder should focus on looking bulky, ankle exposure, thin shoes, or overheating indoors.
```

Chinese label suggestions:

```text
温度提示：保暖分层
风险提醒：别露脚踝 / 避免臃肿 / 室内可脱层
```

### Rain / Wet Weather

Use for rain, drizzle, short showers, wet pavement, typhoon rain, or commute in rainy season.

```text
Rain function pack:
water-resistant outer layer, darker or washable lower-half choices,
grippy shoes, quick-dry or less absorbent fabric, compact umbrella/rain layer only as clothing-related note.
Do not add puddles, umbrellas as large props, weather icons everywhere, or dramatic storm backgrounds.
Risk reminder should focus on slippery soles, suede/leather damage, sheer wet fabric, long hems dragging, or light trousers showing stains.
```

Chinese label suggestions:

```text
天气提示：短时阵雨 / 梅雨通勤 / 湿地防滑
风险提醒：鞋底防滑 / 避开麂皮 / 裤脚别拖地
```

### Wind / Temperature Swing

Use for windy days, spring/fall temperature swings, seaside wind, mountain wind, or day-to-night cooling.

```text
Wind and swing function pack:
secure layers, wind-blocking light outerwear, avoid flyaway or overly loose pieces,
easy-to-remove layers for changing temperature.
Risk reminder should focus on wind exposure, unstable hems, overly light outerwear, or being too cold after sunset.
```

Chinese label suggestions:

```text
天气提示：防风分层
风险提醒：外套要挡风 / 傍晚会冷 / 裙摆别太飘
```

### Sun / UV

Use for strong sun, seaside, outdoor walking, exposed commute, summer travel, or long daytime activity.

```text
Sun function pack:
sun-aware coverage, breathable long sleeves or light outer layer when useful,
hat/sunglasses only when appropriate to the scenario, lighter reflective palette if it fits.
Risk reminder should focus on sun exposure, heat absorption, uncomfortable straps, or footwear unsuitable for long sun-exposed walking.
```

Chinese label suggestions:

```text
天气提示：防晒清爽
风险提醒：注意暴晒 / 少穿吸热黑 / 鞋要耐走
```

## Provider-Specific Prompt Notes

Use these small adjustments only after the main prompt is complete.

- OpenAI GPT Image: keep the prompt explicit about layout, Chinese labels, and reference-item fidelity; use reference images for uploaded garments when available.
- Gemini Nano Banana: ask for cleaner typography and fewer labels when Chinese text density becomes high; use Nano Banana Pro for complex boards.
- Gemini Nano Banana Pro / Gemini 3 Pro Image: use the Pro visual-spec prompt below. It is the preferred Google model for polished outfit boards, complex layout, Chinese text, reference garments, and iterative refinements.
- Gemini 2.5 Flash Image / Nano Banana fast draft: use the compact hard-layout prompt below. It is more likely than Pro models to collapse into a normal person photo if the prompt starts with a lifestyle sentence. Put "structured OOTD styling card / outfit board" in the first sentence, keep the layout list near the top, and avoid starting with "casual man/woman wearing...".
- Jimeng / Seedream: keep prompts concise when possible; put fidelity locks and Chinese text fields near the end as direct instructions.
- MiniMax image generation: keep item zones explicit and avoid asking for too many small text panels.
- ListenHub Image: use the same structured outfit-board prompt; set `aspectRatio` to `9:16` when available, pass uploaded garments as `referenceImages`, and save/attach base64 output when using the OpenAPI.
- If the provider cannot render Chinese reliably, switch to numbered board mode and provide Chinese callout mapping in text.

### Gemini Nano Banana Pro Visual-Spec Prompt

Use this when the configured image output model is `nano-banana-pro-preview`, `gemini-3-pro-image`, `gemini-3-pro-image-preview`, or a host alias for Google Nano Banana Pro.

Nano Banana Pro is better suited than Flash models for complex posters, outfit boards, multilingual text, diagrams, reference images, and iterative editing. Give it a precise visual specification instead of a short keyword prompt.

Prompting rules:

- Start with a strong creation verb and the exact artifact type.
- Specify subject, composition, layout, style, typography, and reference-image behavior.
- Put exact visible Chinese text in quotes.
- Use positive instructions first; keep negative constraints as a final guardrail.
- Use `9:16` and `2K` by default; use `4K` only for polished shareable exports when the host budget allows it.
- If uploaded garments are available, pass them as reference images and repeat concise fidelity locks in the prompt.
- For follow-up correction, ask for a targeted edit to the existing board instead of regenerating from scratch.

```text
Create a vertical 9:16 professional Chinese OOTD styling card / outfit board.
This is an illustrated fashion notebook infographic, not a single model photo.

Primary goal:
Turn the final outfit recommendation into a readable mobile outfit board with Chinese labels.

Composition:
- warm paper-texture background, refined fashion notebook mood
- moderately sized Chinese title on a light paper strip
- one central semi-realistic illustrated full-body outfit figure
- staggered surrounding item cards for 上衣 / 下装 / 鞋子 / 包包 / 配饰 / 外套
- fabric texture circles and color chips
- thin hand-drawn arrows connecting item cards to the outfit
- compact bottom analysis strip for 色彩 / 比例 / 风险提醒 / 适配场景
- asymmetric editorial layout, not a rigid dashboard grid

Typography:
Use clear, legible Simplified Chinese text. Use these exact text snippets:
"标题：[TITLE]"
"场景：[SCENE]"
"首选理由：[REASON]"
"搭配要点：[KEY_POINTS]"
"风险提醒：[RISK]"
Keep every label short and readable on a phone.

Outfit details:
上衣：[TOP]
下装：[BOTTOM]
鞋子：[SHOES]
包包：[BAG]
配饰：[ACCESSORIES]
外套：[OUTERWEAR]

Reference garment fidelity:
[FIDELITY_LOCKS]
If a garment comes from a reference image, preserve its category, length, color family, silhouette, and key visible details. Do not shorten, recolor, or change the garment type.

Style:
semi-realistic fashion illustration, natural adult proportions, clean Korean/Japanese magazine illustration style, refined but wearable, outfit accuracy prioritized over facial detail.

Final guardrails:
Do not create a photorealistic studio portrait, catalogue lookbook, product ad, or virtual try-on.
Do not add extra people, lifestyle props, phones, drinks, notebooks, wallets, receipts, cosmetics, nail panels, underwear, brand logos, watermarks, or dense paragraph blocks.
```

If the first result is close but the layout is off, use edit prompts such as:

```text
Keep the same outfit and garment details. Convert this into a clearer OOTD styling card: add surrounding item cards, Chinese labels, fabric circles, color chips, arrows, and a compact bottom analysis strip. Do not change garment category, length, color, or silhouette.
```

### Gemini 2.5 Flash Compact Hard-Layout Prompt

Use this when the configured image output model is `gemini-2.5-flash-image`, `nano-banana`, or another fast/low-instruction-following image model.

```text
CREATE A VERTICAL 9:16 STRUCTURED OOTD STYLING CARD / OUTFIT BOARD.
Do NOT create a single photorealistic man/woman photo.
Do NOT create a studio portrait, catalogue lookbook, product ad, or virtual try-on.

Visual format:
warm paper fashion notebook page, semi-realistic fashion illustration, natural editorial OOTD board.
One central illustrated full-body outfit figure or outfit flat-lay.
Surrounding item cards: 上衣 / 下装 / 鞋子 / 包包 / 配饰 / 外套.
Add fabric texture circles, color chips, thin arrows, and a small bottom analysis strip.
All visible advice text should be Chinese and short. If Chinese rendering becomes unreliable, use numbered markers 1-6 and leave clean label spaces.

Required Chinese fields:
标题：[TITLE]
场景：[SCENE]
首选理由：[REASON]
搭配要点：[KEY_POINTS]
风险提醒：[RISK]

Selected outfit:
上衣：[TOP]
下装：[BOTTOM]
鞋子：[SHOES]
包包：[BAG]
配饰：[ACCESSORIES]
外套：[OUTERWEAR]

Uploaded garment fidelity locks:
[FIDELITY_LOCKS]

Hard exclusions:
no standalone person photo, no photorealistic studio model, no white-background catalogue shot,
no virtual try-on, no extra people, no lifestyle props, no phone, no wallet, no receipts,
no drinks, no notebooks, no makeup, no manicure, no underwear, no brand logos,
do not change uploaded garment category, length, color, silhouette, or key details.
```

For Gemini 2.5 Flash, the prompt should begin with the hard layout instruction above, not with the outfit mood or the user's scenario.

## Default Template: Structured OOTD Styling Card

Use this as the default image-output template for V1, regardless of gender, style direction, or occasion, unless the user explicitly asks for another visual format.

Template purpose:

- turn a styling recommendation into a readable mobile outfit card
- show the complete look on a central illustrated figure
- explain the outfit through surrounding item cards
- summarize color, proportion, risk, and suitable scenes at the bottom

Template slots:

```text
Header:
- title
- scene chip
- reason chip

Main figure:
- central semi-realistic illustrated full-body outfit figure
- natural adult proportions
- outfit reflects the final recommendation, not a virtual try-on claim

Item cards:
- outerwear when relevant
- top
- bottom
- shoes
- bag
- accessories or belt
- each card uses 1-2 short Chinese notes
- fabric or texture circle when useful

Bottom analysis strip:
- color palette
- proportion/silhouette
- risk reminder
- suitable scenes
- optional one-line summary
```

Default layout:

- **Natural editorial layout**: use this as the V1 default. Central figure is slightly dominant, item cards are staggered around the figure, scene/reason/key/risk notes may live as left-side paper notes, and bottom analysis can be partial rather than perfectly symmetrical. This feels more natural and less rigid.

Avoid the regular symmetrical card-grid layout as the default. It looks too stiff for this skill's visual personality. Use a stricter grid only if the user explicitly asks for a very organized comparison view.

Template adaptation:

- Gender: adapt figure, garment categories, shoes, bag, and accessory choices; keep the same hierarchy.
- Occasion: adapt scene chip, reason chip, risk reminder, and suitable scenes.
- Weather/activity: add functional notes such as rain, wind, breathability, warmth, grip, or layering inside item cards or bottom analysis.
- Single uploaded item: make the uploaded item the anchor item card and show it integrated into the central outfit.
- Wardrobe/multi-image: use the selected outfit as the central look and put chosen wardrobe items into item cards.
- Default boards should not add extra people, friend-group photos, decorative mini photos, or unrelated scene snapshots unless the user asks for a social-card style.
- For uploaded wardrobe items, preserve category, length, color, silhouette, and key details. Do not turn long trousers into shorts, shirts into jackets, vests into coats, or one color family into another.
- For multi-image boards, do not include avoided uploaded items in the central outfit. If showing backup items, put them in a small backup card only.

## Gold Standard Visual Style

Preferred default boards should feel like a polished fashion notebook page:

- warm paper background with light tape or hand-drawn divider details
- light-to-medium title weight; avoid a top-heavy header
- scene, reason, key points, and risk can be shown as small paper notes on one side instead of always as top chips
- central semi-realistic illustrated full-body outfit figure
- surrounding item cards for outerwear/top/bottom/shoes/bag/accessories, each with 1-2 short Chinese notes
- fabric texture circles attached to relevant item cards
- thin hand-drawn arrows connecting items to the central outfit
- optional compact bottom analysis strip for color, proportion, risk reminder, and suitable scenes
- final one-line summary at the bottom if space allows
- risk reminder should be visually calm; avoid oversized warning signs, heavy red prohibition symbols, or alarm-like graphics unless safety is the main issue
- lower-half polish matters: keep shoes, trouser hem, bottom cards, icons, and final summary refined, airy, and balanced
- bottom analysis cards should be light and editorial, with thin borders, generous padding, small icons, and consistent alignment
- header should be light and editorial: avoid oversized dark titles, heavy title boxes, thick bars, or top-heavy composition
- top scene/reason chips should feel like small paper notes, not large banners
- menswear business boards should avoid cropped trouser hems that expose too much ankle; use a clean full-length or slight-break trouser line

This structured page is better than a loose object collage when the goal is an actionable outfit recommendation. However, avoid making the structure feel too rigid: stagger cards, vary card sizes, and let the central figure breathe.

Avoid copying any specific reference image exactly. Borrow the information hierarchy, not the exact person, pose, clothing, text, or layout.

## Structured OOTD Styling Card Prompt

Use this as the preferred default visual direction. It may borrow the information hierarchy of polished OOTD styling cards, but do not copy any specific reference image.

```text
Create a vertical mobile-first structured OOTD styling card for [SCENARIO].
Use [USER_PROFILE] and [OUTFIT_PLAN] as the styling basis.

Visual style:
polished fashion notebook page, warm soft paper-texture background, light tape details,
clean editorial layout, central semi-realistic illustrated full-body fashion figure,
surrounding garment cards with short Chinese notes, fabric texture circles, color chips,
thin hand-drawn arrows, quiet magazine scrapbook feel, natural wearable styling.
The person should look illustrated, not photorealistic: semi-realistic fashion illustration,
clean Korean/Japanese magazine illustration style, natural adult proportions, not anime, not childish.

Core item zones only:
outerwear / top / bottom / shoes / bag / accessories / fabric detail / color palette / proportion / risk / suitable scenes.

Layout:
vertical 9:16 phone-friendly composition.
Use the natural editorial layout by default. Do not use the regular symmetrical card-grid layout.
Top area: light-to-medium title on a paper strip, not too heavy.
Left note area when useful: scene, reason, key points, and risk as small stacked paper notes.
Middle area: one clear central outfit figure, with staggered surrounding garment cards, fabric texture circles, and arrows.
Bottom area: optional compact analysis area for color, overall effect, proportion, or suitable scenes; it may be partial, not a forced full-width grid.
Large readable Chinese labels, no dense text, no tiny text.
Bottom analysis is allowed, but it must look like a fashion notebook analysis strip, not app UI.
Do not add checkbox cards, icon tiles, rating boxes, dashboard blocks, or product-ad panels.
Keep risk reminders calm and editorial; do not use oversized red prohibition signs.
Lower-half refinement: trouser hem and shoes should look elegant and proportional; bottom analysis blocks should be delicate, evenly spaced, and not visually heavy.
Header refinement: title should be moderately sized with breathing room; scene and reason chips should be slim and soft, with muted colors and no heavy dark filled boxes.
Menswear fit refinement: avoid short cropped trousers or excessive ankle exposure in business scenes; prefer clean full-length trousers with a slight break over polished leather shoes.
Naturalness refinement: avoid perfectly rigid symmetry and evenly boxed card grids. Use subtle card overlap, tape, varied card sizes, left-side notes, and asymmetric spacing to create a more human editorial page.
Scene refinement: a soft background atmosphere is fine, but do not add extra people, friend photos, decorative mini photo prints, drinks, bottles, bar shelves, dining props, phones, notebooks, wallets, receipts, or unrelated lifestyle snapshots in default boards.
Wardrobe fidelity: if an item comes from the user's uploaded images, preserve its category, length, color, silhouette, and key details exactly enough to be recognizable. Do not modify uploaded garments for convenience or aesthetics.

Required Chinese text on image:
标题：[中文标题]
场景：[中文场景]
首选理由：[一句中文理由]
搭配要点：[2-3 个中文短标签]
风险提醒：[1 个中文短句]

Label placement:
- Put the title and scene near the top.
- Put item labels directly beside the related garment or accessory.
- Put the reason beside the items that create the effect, such as trousers and shoes.
- Put the risk reminder beside the risky substitution, such as sneakers or overly casual pieces.
- If using a bottom analysis strip, keep it compact and visually integrated with the paper notebook style.

Outfit details:
[TOP]
[BOTTOM]
[SHOES]
[BAG]
[ACCESSORIES]
[OUTERWEAR]
[ANCHOR_ITEM if user uploaded one item]
[FIDELITY_LOCKS for uploaded wardrobe items: category, length, color, silhouette, key details]

Hard exclusions:
No underwear, bras, panties, lingerie, sleepwear, nail close-ups, manicure panels, makeup items,
lipstick, compact powder, skincare, wallet contents, ID cards, bank cards, receipts, phones,
notebooks, pens, drinks, unrelated lifestyle objects, brand logos, watermarks, body/beauty ranking,
or changing uploaded garment categories and lengths.
Focus only on clothing, bag, shoes, accessories, fabric, color, silhouette, and proportion.

Constraints:
All styling advice text must be in Chinese, except unavoidable brand or fashion terms.
Do not generate virtual try-on unless explicitly requested.
Do not copy any specific reference image composition exactly.
```

## Item-Based Board Add-On

Use this when the user uploads one clothing item and asks how to match it.

```text
The uploaded item is the anchor piece. Keep it visually recognizable and make all other pieces support it.
Do not replace the anchor item with a different garment.
Show the anchor piece once as a clear cutout and once integrated into the central outfit figure or flat-lay.
If exact brand/logo details are visible, avoid copying logos; preserve only general color, collar, silhouette, and trim details.
The board should explain how to complete the outfit, not advertise the uploaded item.
For business or client-facing scenes, make the board quieter: fewer props, clean spacing, no playful stickers, no exaggerated pose.
Use the structured OOTD styling card layout by default: top header, central figure, surrounding garment cards, and compact bottom analysis strip.
```

## Compact Board Prompt

```text
Create a vertical mobile-first fashion outfit board for [SCENARIO].
Use [USER_PROFILE] and [OUTFIT_PLAN] as the styling basis.

Visual style:
professional fashion editorial styling board, clean magazine layout, outfit blueprint sheet,
full or half-body fashion view when appropriate, neat annotation lines, fabric texture callouts,
color palette chips, accessory close-ups, tailoring and proportion notes, polished natural light,
modern East Asian / Korean street-fashion editorial sensibility when suitable, refined but wearable.

Layout:
vertical portrait composition, phone-friendly width, readable Chinese typography, spacious blocks,
one clear hero outfit, 3-4 small detail panels, no clutter, no tiny text.
Do not add extra long text panels beyond the required Chinese labels.
Keep every label short, large, and readable on a phone.

Required Chinese text on image:
标题：[中文标题]
场景：[中文场景]
首选理由：[一句中文理由]
搭配要点：[2-3 个中文短标签]
风险提醒：[1 个中文短句]

Outfit details:
[TOP]
[BOTTOM]
[SHOES]
[BAG]
[ACCESSORIES]
[OUTERWEAR]

Constraints:
All styling advice text must be in Chinese, except unavoidable brand or fashion terms.
Do not generate virtual try-on unless explicitly requested.
Do not add body-shaming, attractiveness ranking, or identity assumptions.
Do not add dense paragraphs, tiny labels, invented brand logos, or unrelated fashion advice.
```

## Detailed Board Prompt Add-On

Use only when a more shareable or long-card style is appropriate:

```text
Detailed board mode: include color chips, 4-6 detail panels, fabric texture notes, proportion notes, and accessory close-ups.
Keep all text in short Chinese labels. No paragraph blocks. The board may be slightly longer vertically, but must remain phone-readable.
```

## If Chinese Text Rendering Is Weak

Ask the model for numbered callouts only:

```text
Create a clean vertical outfit board with numbered callout markers 1-6, no long embedded text.
Leave clean label spaces beside each marker. Do not render paragraphs.
```

Then provide the Chinese callout mapping separately in text.

## Visual QA Checklist

After generating a board, check:

- Is the canvas vertical and phone-friendly?
- Is the primary outfit clear in the first glance?
- Are Chinese labels readable and mostly correct?
- Are there too many text panels?
- Are all core items represented: top, bottom, shoes, bag/accessory, outerwear if relevant?
- Does it focus on clothes, bag, shoes, accessories, fabric, color, silhouette, and proportion?
- Does it avoid underwear, nail details, makeup, wallet contents, phone, notebook, pen, receipts, and unrelated lifestyle objects?
- Does the image avoid virtual try-on claims?
- Does it avoid brand logos, attractiveness ranking, and body judgment?
- If Chinese text is garbled or too dense, switch to numbered callouts and provide Chinese text separately.

## Inspiration Terms

Use sparingly and adapt to the user:

```text
deconstructed OOTD styling sheet, paper texture background, hand-drawn arrow annotations,
editorial collage layout, garment cutout panels, fabric texture circles, color chips,
fashion blueprint sheet, detailed outfit annotations, tailoring notes, accessory close-ups
```
