# Outfit Stylist Skill Design Proposal

Date: 2026-06-13
Status: Draft for discussion

## 1. Working Name

Recommended repo/project name: `outfit-stylist-skill`

Recommended skill name: `outfit-stylist`

Chinese display name:

- AI 穿搭顾问

Recommendation: use `outfit-stylist` as the Skill name and "AI 穿搭顾问" as the Chinese public-facing name.

Reason: `outfit-stylist` is more concrete than `style-strategist` and easier to understand for a Skill focused on practical daily outfit recommendations. "AI 穿搭顾问" is clearer than "AI 时尚造型师" for ordinary users who need help deciding what to wear.

## 2. Positioning

This Skill is an outfit decision assistant for ordinary people in daily life.

It helps users turn concrete dressing needs into practical outfit plans:

- What should I wear for this situation?
- What should I wear tomorrow for this event?
- Given today's weather, what should I wear?
- How should I style this top, jacket, dress, pair of shoes, or accessory?
- Which of these uploaded clothing photos can be combined into outfits?
- How do I use my existing wardrobe instead of buying more?

The Skill should not start from heavy personal image diagnosis. It should start from the user's immediate scenario, basic profile, available clothing, comfort constraints, and desired impression.

The Skill produces:

- scenario outfit recommendation
- 2-3 complete outfit plans
- single-item styling advice
- multi-image outfit combinations
- wardrobe-based outfit suggestions
- optional outfit board image plus short text explanation
- optional lightweight profile updates after user feedback

Personal image analysis is an auxiliary mode, not the core v1 entry point.

## 3. Foundational Outfit Model

### First Principle

Outfit styling is not just arranging attractive garments. It is helping a person appear in a concrete life situation through clothing that coordinates body, comfort, environment, social meaning, aesthetic order, and self-expression.

Operational definition:

```text
Good outfit advice helps the user appear in the world in a way that is true, comfortable, readable, actionable, appropriate, and visually coherent.
```

### Four-Layer Model

Use this model as the reasoning spine for every recommendation.

```text
Layer 1: Philosophical Core
truth / ease / readability / agency

Layer 2: Root Functions of Dress
protection / boundary / role / social order / expression

Layer 3: Aesthetic and Cultural Judgment
propriety / harmony / degree / form / qi / context / flavor

Layer 4: Practical Inputs
person / weather / occasion / clothes / intent
```

### Layer 1: Philosophical Core

Ask these final-check questions before recommending an outfit:

- **Truth**: Does this outfit feel like a plausible version of the user, or does it force the user to impersonate someone else?
- **Ease**: Can the user physically and psychologically wear it without constant self-monitoring?
- **Readability**: Will people in the scene understand the intended signal?
- **Agency**: Does it help the user act, communicate, move, and complete the day?

### Layer 2: Root Functions of Dress

Every outfit must serve five human functions:

- **Protection**: Handle temperature, weather, movement, exposure, friction, and physical comfort.
- **Boundary**: Manage how the body is seen through fit, coverage, transparency, structure, softness, and distance.
- **Role**: Help the user enter the role of worker, host, guest, date, traveler, interviewer, friend, or performer.
- **Social order**: Respect the implicit norms of the scene, including profession, city, culture, age group, hierarchy, and relationship.
- **Expression**: Give the user a personal note without making the outfit unreadable or impractical.

### Layer 3: Aesthetic and Cultural Judgment

Use both Western and Eastern aesthetic logic.

- **Propriety / 礼**: Does the outfit respect the occasion, relationship, and role?
- **Harmony / 和**: Do body, garment, environment, color, texture, and mood work together?
- **Degree / 度**: Is the level of formality, trendiness, exposure, decoration, and effort appropriate?
- **Form / 形**: Are proportion, line, silhouette, visual weight, and rhythm coherent?
- **Qi / 气**: What state does the outfit create: relaxed, sharp, gentle, dignified, bright, calm, intimate, authoritative?
- **Context / 境**: Does it fit weather, city, venue, time of day, movement, and cultural surroundings?
- **Flavor / 味**: Does it have a memorable but controlled personal note?

### Layer 4: Practical Inputs

Collect only what is needed to solve the current outfit problem:

- **Person**: gender expression, age range, height, weight, comfort constraints, style preferences.
- **Weather**: temperature, rain/snow, wind, humidity, indoor/outdoor transitions.
- **Occasion**: event, people involved, dress code, relationship, activity length, mobility needs.
- **Clothes**: available garments from text descriptions or uploaded photos.
- **Intent**: target impression and impressions to avoid.

### Conflict Priority

When constraints conflict, use this priority order:

```text
safety and body comfort
> occasion propriety
> user truth and confidence
> aesthetic coherence
> trend relevance
```

Trend is a reference layer, not the decision layer. Borrow from current trends only when they improve freshness without damaging comfort, propriety, truth, or execution.

### Execution Mechanisms

The Skill should include these mechanisms in recommendations:

- **Risk check**: identify where a look may be too casual, too formal, too cold, too hot, too revealing, too stiff, too trend-driven, too hard to walk in, or too difficult to maintain.
- **Confidence grading**: provide a safe option, an upgraded option, and an expressive option when appropriate.
- **Wardrobe-first logic**: use existing clothes first, suggest substitutes second, and recommend purchases only when they fill a repeated real gap.
- **Feedback learning**: update the user's profile when they say something feels good, uncomfortable, too formal, too casual, too mature, too cute, too stiff, or unlike them.
- **Visual output rule**: default to concise text outfit plans; when images are provided or requested, create an outfit board / styling board rather than defaulting to virtual try-on.

## 4. User Model

The Skill should build a lightweight outfit profile first, then enrich it over time through feedback.

### Minimum Outfit Profile

```yaml
outfit_profile:
  gender_expression: ""
  age_range: ""
  height_cm: null
  weight_kg: null
  city_or_climate: ""
  common_scenarios: []
  comfort_profile:
    preferred_materials: []
    avoided_materials: []
    fit_preferences: []
    shoe_constraints: []
    exposure_limits: []
  lifestyle:
    work_context: ""
    commute: ""
  preferences:
    liked_styles: []
    disliked_styles: []
    color_preferences: []
    budget_range: ""
  intent_profile:
    target_impression: []
    avoid_impression: []
```

Avoid overclaiming from photos. When using photos, describe visible clothing and styling cues rather than making medical, attractiveness, or identity judgments.

## 5. Wardrobe Model

MVP does not need a database, but the Skill should use a structured item schema when users provide clothing photos or descriptions.

```yaml
item:
  name: ""
  category: ""
  color: ""
  silhouette: ""
  material: ""
  pattern: ""
  formality: ""
  season: []
  fit_notes: []
  condition: ""
  pairing_potential: []
  risks: []
```

Later this can become a real wardrobe database.

## 6. Core Modes

### Mode 1: Scenario Outfit Recommendation

Use when the user says:

- 明天见客户穿什么
- 面试怎么穿
- 周末见朋友怎么搭
- 今天降温/下雨/很热，怎么穿
- 给我 2-3 套明天可穿的方案

Workflow:

1. Clarify occasion, people involved, dress code, weather, activity length, and target impression.
2. Check the user's lightweight outfit profile and comfort constraints.
3. Ask what clothing is available if not already known.
4. Recommend 2-3 complete outfits.
5. Rank the options: safest, best overall, and more expressive when useful.
6. Explain why the first option is preferred.
7. Add risks, last-minute fixes, and substitutions.

Output:

- 2-3 complete looks
- top/bottom/shoes/outerwear/bag/accessory details
- target impression
- ranking
- reasoning
- risk and adjustment notes

### Mode 2: Single-Item Styling

Use when the user says:

- 这件上衣怎么搭
- 这双鞋配什么裤子
- 这条裙子适合什么外套
- 我上传一件衣服，帮我配一套
- 这件衣服怎么穿才不显老/不太正式/不太随便

Workflow:

1. Identify the item category, color, material, silhouette, formality, season, and styling risks.
2. Ask or infer the intended occasion.
3. Suggest 2-3 styling directions around the item.
4. Specify pants/skirt, shoes, bag, accessories, and outerwear.
5. Name what not to pair with it.
6. If the item is from a purchase consideration, add a short buy/skip/conditional judgment.

Output:

- item reading
- 2-3 pairing formulas
- best use cases
- avoid pairings
- optional purchase judgment

### Mode 3: Multi-Image Outfit Combination

Use when the user says:

- 我上传几张衣服图，帮我组合几套
- 这几件里面哪些可以搭一起
- 从这些衣服里选明天能穿的
- 这几套哪套更合适
- 帮我从图里挑一套

Workflow:

1. Identify each visible item by category, color, silhouette, material cues, formality, and season.
2. Group compatible items by occasion and aesthetic logic.
3. Build 2-3 outfit combinations.
4. Mark best combination, backup combination, and not-recommended combinations.
5. Explain color, proportion, texture, and formality reasons.
6. Add missing pieces if the images do not include shoes, outerwear, or accessories.

Output:

- item inventory from images
- best outfit combination
- 1-2 backup combinations
- not-recommended pairings
- missing-piece suggestions
- optional outfit board

### Mode 4: Wardrobe-Based Outfit Planning

Use when the user says:

- 用我已有衣服搭几套
- 我只有这些衣服，明天怎么穿
- 我总觉得没衣服穿
- 帮我从衣橱里搭一周
- 这些衣服怎么提高利用率

Workflow:

1. Group available items by category, color, formality, season, and scenario.
2. Build reusable outfit formulas from existing items first.
3. Identify missing anchor pieces only if repeated outfit gaps appear.
4. Suggest 3-7 outfits depending on the request.
5. Add no-purchase fixes: styling, layering, alterations, shoe swaps, color balancing.
6. Suggest purchases only when they unlock multiple existing items.

Output:

- existing-item outfit plan
- repeated formulas
- idle item rescue
- no-purchase improvements
- optional gap list
- restrained buy list only when justified

### Auxiliary Mode: Lightweight Image/Profile Diagnosis

Use only when the user explicitly asks for longer-term style analysis, profile setup, or recurring personalization. Keep it practical and avoid turning v1 into a heavy image-diagnosis workflow.

## 7. Recommendation Reasoning Flow

Every recommendation should follow this practical reasoning order:

1. Understand the user's immediate occasion and intent.
2. Check weather, mobility, duration, and indoor/outdoor transitions.
3. Check profile constraints: body comfort, materials, fit, shoes, exposure, and confidence.
4. Identify available garments from descriptions or uploaded images.
5. Generate 2-3 complete outfit options.
6. Rank the options by fit to scenario, comfort, execution, and aesthetic coherence.
7. Explain the tradeoffs briefly.
8. Add risks, last-minute fixes, and optional substitutions.
9. If visual output is requested, create an outfit board or styling board.

The Skill should avoid single-factor advice:

- not only personal color
- not only body type
- not only trend
- not only "显瘦"
- not only "高级感"
- not only "博主同款"

## 8. Safety and Taste Guardrails

Do:

- be practical and kind
- separate visible styling observations from identity judgment
- avoid body shaming
- avoid deterministic claims from limited photos
- respect cultural, gender, religious, professional, and budget constraints
- suggest alterations and styling before purchases
- explain tradeoffs

Do not:

- rank attractiveness
- infer sensitive traits
- diagnose health conditions
- shame body shape, age, skin tone, or budget
- push unnecessary consumption
- promise that one color/shape universally "fixes" the user

Preferred tone:

- honest but not cruel
- concrete rather than mystical
- "here is the style effect" rather than "you are wrong"

## 9. Skill Structure

```text
outfit-stylist/
├── SKILL.md
├── agents/
│   └── openai.yaml
├── references/
│   ├── agent-compatibility.md
│   ├── foundational-model.md
│   ├── model-capability-config.md
│   ├── outfit-profile-schema.md
│   ├── wardrobe-schema.md
│   ├── scenario-playbooks.md
│   ├── image-input-guidelines.md
│   ├── outfit-board-prompting.md
│   ├── recommendation-format.md
│   ├── stylist-voice.md
│   ├── mobile-output-guidelines.md
│   ├── trend-reference-guidelines.md
│   └── safety-guardrails.md
└── assets/
    └── templates/
        ├── profile.yaml
        ├── model-capabilities.yaml
        ├── scenario-outfit.md
        ├── item-styling.md
        ├── multi-image-combination.md
        └── wardrobe-outfit.md
```

Keep `SKILL.md` compact. Put detailed examples, schemas, model configuration rules, output formats, and playbooks in references.

## 10. MVP Scope

Weekend MVP:

- Create `SKILL.md`
- Create 13 references:
  - `agent-compatibility.md`
  - `foundational-model.md`
  - `model-capability-config.md`
  - `outfit-profile-schema.md`
  - `wardrobe-schema.md`
  - `scenario-playbooks.md`
  - `image-input-guidelines.md`
  - `outfit-board-prompting.md`
  - `recommendation-format.md`
  - `stylist-voice.md`
  - `mobile-output-guidelines.md`
  - `trend-reference-guidelines.md`
  - `safety-guardrails.md`
- Create 6 templates:
  - `profile.yaml`
  - `model-capabilities.yaml`
  - `scenario-outfit.md`
  - `item-styling.md`
  - `multi-image-combination.md`
  - `wardrobe-outfit.md`
- Test with 5 prompts:
  - 明天见客户，帮我搭 2-3 套
  - 周末见朋友，天气降温，怎么穿
  - 我上传一件上衣，帮我配裤子、鞋、包
  - 我上传几张衣服图，从里面组合几套
  - 我只有这些衣服，明天通勤怎么搭

V1 capability decisions:

- The Skill should be portable across Claude Code, Codex, Hermes Agent, OpenClaw, and similar agent runtimes.
- The cross-agent source of truth is `SKILL.md` plus directly linked `references/` and `assets/`.
- `agents/openai.yaml` is a Codex UI enhancement, not a required runtime dependency.
- Image input is a core capability, but not a required user input. The Skill must support both text-only requests and image-assisted requests.
- Image input should support one-item styling and multi-image outfit combination from v1.
- Image output should default to outfit boards / styling boards when requested.
- V1 should not attempt default real-person virtual try-on.
- Image recognition and image generation may use different backend models. The Skill must treat them as separately configurable capabilities.
- If installed in another environment, image model configuration should be externalized rather than hardcoded into the Skill instructions.

Defer:

- full wardrobe database
- virtual try-on
- shopping crawler
- mobile app UI
- monetization system
- heavy long-term image diagnosis

## 11. Output Shape

Professional styling services commonly combine a client/profile read, occasion context, curated outfit options, stylist notes, and follow-up feedback. The Skill should translate this into a compact mobile-first recommendation card rather than a long essay.

Default text output:

```markdown
## 今日建议

**首选**：方案 1
**理由**：一句话说明为什么最稳。

### 方案 1
- 上衣：
- 下装：
- 鞋：
- 外套：
- 包/配饰：
- 适合感觉：
- 注意：

### 方案 2
- ...

### 方案 3
- ...

## 快速判断
- 最稳：
- 更有风格：
- 不建议：
```

Visual output:

- Use outfit boards / styling boards when the user asks for image output.
- Treat deconstructed OOTD outfit boards as the preferred default visual style for v1.
- Use compact boards when text readability and fast phone scanning matter more than editorial feel.
- Avoid implying exact virtual try-on unless the user explicitly requests try-on and the available tools can support it.
- Pair every visual with short reasoning, not long theory.
- Treat outfit-board prompt quality as a core part of the Skill, not a cosmetic afterthought.
- Refine image prompts after the first Skill version with real visual QA.

Outfit board image requirements:

- Use a vertical mobile-first canvas by default.
- Prefer common mobile-friendly proportions such as `9:16` or `3:4`; use a longer vertical image when one screen cannot fit the content.
- Keep the visual width suitable for phone viewing, such as 1080px-wide output when the image backend supports explicit dimensions.
- Use Chinese for all visible outfit advice, titles, callouts, styling notes, and labels.
- Use English only when a brand name, fabric term, style term, or fashion-industry phrase is better left in English.
- Keep text large, sparse, and readable; do not fill the board with dense paragraphs.
- Show a complete outfit: top, bottom, shoes, bag, accessories, and optional outerwear.
- Use annotation lines, detail close-ups, fabric swatches, color chips, and concise styling callouts.
- Prefer a warm deconstructed OOTD collage feel: central outfit, surrounding garment panels, paper texture, hand-drawn arrows, fabric swatches, and color chips.
- Prefer a semi-realistic illustrated fashion figure by default, rather than a photorealistic model, so the board feels like styling advice instead of virtual try-on or advertising.
- Keep garment and accessory cutouts realistic enough to show fabric, color, silhouette, and styling details.
- Do not include underwear, bras, panties, lingerie, nail close-ups, manicure panels, makeup items, skincare, wallet contents, phones, notebooks, pens, receipts, cards, drinks, or unrelated lifestyle objects.
- Avoid making the image look like a product ad unless the user asks for a commercial look.

Base outfit-board prompt pattern:

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
one clear hero outfit, 3-5 small detail panels, no clutter, no tiny text.

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
```

Reference inspiration:

```text
Fashion blueprint sheet, fashion editorial view with detailed outfit annotations,
styling callouts, jewelry breakdowns, fabric texture descriptions, tailoring notes,
accessory close-ups, cinematic natural light, professional fashion concept sheet.
```

Mobile output constraints:

- Put the best recommendation first.
- Keep the first screen useful even if the user does not scroll.
- Prefer 2-3 outfit cards over long paragraphs.
- Keep each outfit explanation to 1-2 short sentences.
- Use concise labels: `首选`, `备选`, `更有风格`, `风险`, `替换`.
- Avoid theory names in user-facing output unless the user asks why the model thinks that way.
- Target a complete recommendation that fits within one to two phone screens when possible.

## 12. Model Capability Configuration

The Skill should separate styling reasoning from model capability configuration.

### Capability Types

```yaml
model_capabilities:
  image_input:
    enabled: false
    provider: ""
    model: ""
    supported_inputs:
      - clothing_photo
      - outfit_photo
      - multi_item_photo_set
    notes: ""
  image_output:
    enabled: false
    provider: ""
    model: ""
    default_output: outfit_board
    default_aspect_ratio: "9:16"
    fallback_aspect_ratio: "3:4"
    long_image_aspect_ratio: "9:21"
    supported_outputs:
      - outfit_board
      - styling_board
    notes: ""
```

### Runtime Behavior

- If image input is configured, use it to identify garment category, color, silhouette, texture cues, formality, season, condition, and pairing risks.
- If image input is not configured and the user uploads or references images, explain briefly that image recognition is not configured in this environment and ask the user to describe the visible garments. Continue with text-only styling.
- If image output is configured, generate an outfit board only when the user asks for visual output or when the task clearly benefits from it.
- If image output is not configured and the user asks for an image, explain briefly that visual generation is not configured in this environment, then provide a compact text-based outfit board description.
- Never block the entire styling recommendation just because image input or output is unavailable.
- Keep configuration provider-neutral. The current Codex environment may use built-in image capabilities, but installed Skill environments must be able to configure separate image input and image output models.
- When the image model supports text rendering, explicitly require Chinese labels. When it does not reliably support text rendering, generate the visual board with numbered callouts and provide the exact Chinese text separately.

### Fallback Text Board

When visual output is unavailable, use this structure:

```markdown
## 搭配板文字版

主色：...
廓形：...
材质感觉：...

上衣：...
下装：...
鞋：...
包/配饰：...
外套：...

视觉效果：...
```

## 13. Simulated Test Scenarios

Use these before real user testing:

### Scenario 1: Business Meeting, Text-Only

User:

> 女，32 岁，165cm，55kg，上海。明天 18-24 度，有点风，下午第一次见一个偏传统行业客户。平时喜欢简洁、舒服，不喜欢太硬的西装，也不穿高跟。衣橱里有白衬衫、浅蓝衬衫、米色针织开衫、黑色直筒裤、深蓝牛仔裤、乐福鞋、小白鞋、黑色托特包。帮我搭 2-3 套。

Expected Skill behavior:

- Prioritize propriety and comfort.
- Avoid stiff formalwear because of comfort profile.
- Recommend soft-structured business casual options.
- Explain which option is safest for a first client meeting.

### Scenario 2: Weekend Social, Weather Constraint

User:

> 男，28 岁，178cm，70kg，杭州。周六和朋友吃饭、逛展，12-18 度，可能下小雨。我想看起来干净、有点审美，但不要太用力。已有灰色卫衣、白 T、黑色夹克、卡其裤、深色牛仔裤、运动鞋、切尔西靴。

Expected Skill behavior:

- Balance casual, weather, and "not too hard" intent.
- Include rain-aware shoe/outerwear choices.
- Offer safe and slightly more expressive options.

### Scenario 3: Single Uploaded Item

User:

> 我上传了一件浅绿色短袖针织上衣，帮我配裤子、鞋和包。场景是周日下午咖啡店见朋友，天气 25 度。

Expected Skill behavior:

- Read visible item attributes from image.
- Suggest 2-3 pairings around color, proportion, texture, and casualness.
- Explain what colors or silhouettes to avoid.
- Offer outfit board if visual output is requested.

### Scenario 4: Multi-Image Combination

User:

> 我上传 8 张衣服图，里面有 3 件上衣、2 条裤子、1 条半裙、2 双鞋。帮我从里面组合 3 套，标出哪套最适合明天通勤。

Expected Skill behavior:

- Inventory visible items.
- Combine compatible pieces.
- Mark best, backup, and not-recommended pairings.
- Explain proportion, color, formality, and weather assumptions.

### Scenario 5: Comfort Conflict

User:

> 我明天要参加一个半正式晚餐，但我不喜欢硬邦邦的材质，穿亚麻、棉、针织会舒服很多。怎么穿能得体，但不要像穿制服？

Expected Skill behavior:

- Treat comfort as a high-priority constraint.
- Use soft materials to achieve formality through color, silhouette, shoes, and accessories.
- Explain the tradeoff between structure and ease.
- Avoid forcing stiff jackets as the only formal solution.

## 14. Xiaohongshu Validation Angle

The first public validation should be content-first:

1. "明天怎么穿，不是审美题，是决策题"
2. "我把穿搭顾问的判断流程做成了一个 Skill"
3. "给我一件上衣，AI 怎么配出 3 套可穿方案"
4. "普通人穿搭最重要的不是惊艳，是得体、舒服、有状态"
5. "用已有衣服搭出 7 天不重样"

Potential downloadable lead magnet:

- `明日穿搭决策卡`
- `单品搭配判断卡`
- `衣橱组合表`

## 15. Key Discussion Decisions

Before implementation, decide:

1. Should current trend lookup be included in v1, or treated as optional only when user asks for trend relevance?
2. Should we integrate with OpenClaw's skill path immediately, or keep it as a standalone repo first?

Decided:

- Skill name: `outfit-stylist`
- Chinese public name: "AI 穿搭顾问"
- V1 supports image input as a core optional path.
- V1 visual output defaults to outfit boards / styling boards, not real-person virtual try-on.
- Image input and image output are separately configurable model capabilities.
- If image models are not configured, the Skill must provide clear prompts and text-only fallbacks.
- User-facing recommendations should be mobile-first and concise enough for one to two phone screens when possible.

## 16. Recommended Build Path

Build it in this order:

1. Finalize the consultation workflow for the four outfit-first modes.
2. Create the Skill skeleton.
3. Encode the foundational model and conflict priority.
4. Add profile, wardrobe, image-input, and model-capability schemas.
5. Add mobile-first output templates.
6. Test with real user prompts and at least one image-based prompt.
7. Test unavailable-image-model fallbacks.
8. Generate and visually QA at least one outfit board prompt.
9. Refine the outfit-board prompt template for Chinese readability, phone composition, and styling accuracy.
10. Package one Xiaohongshu post from the test result.

This keeps the project small enough to finish on a weekend and strong enough to publish.
