# Profile And Wardrobe Memory Workflow

Use this when a runtime supports memory, local files, or user-scoped storage. If memory is unavailable, keep the same structure mentally for the current conversation.

## Memory Layers

Keep profile memory and wardrobe memory separate:

```text
profile memory -> who the user is dressing for and what they prefer
wardrobe memory -> what clothes/items the user owns
styling history -> what recommendations worked or were rejected
```

Do not merge these into a single vague "style" note. The skill needs practical constraints more than personality labels.

## Profile Memory

Store only user-provided or explicitly confirmed information:

```yaml
profile_memory:
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
  preferences:
    liked_styles: []
    disliked_styles: []
    color_preferences: []
    target_impression: []
    avoid_impression: []
```

Update profile memory when the user says durable preferences, such as:

- "我不穿高跟"
- "我不喜欢太紧"
- "硬材质我不舒服"
- "我经常见金融客户"
- "我想看起来专业但不要有压迫感"

Do not store one-off context as profile memory unless it repeats or the user says it is typical.

## Wardrobe Memory

Store wardrobe items only when the user asks to record them, repeatedly uses them, or provides item photos/descriptions for future reuse.

```yaml
wardrobe_memory:
  items:
    - id: ""
      name: ""
      category: ""
      color: ""
      silhouette: ""
      material: ""
      pattern: ""
      formality: ""
      season: []
      fit_notes: []
      comfort_notes: []
      pairing_potential: []
      risks: []
      image_reference: ""
      source: user_text_or_image
      confidence: low_medium_high
      last_used: ""
      user_feedback: []
```

For photo-derived items, keep uncertainty:

```text
图片里看起来像...
如果实物更厚/更薄/更亮，下次可以调整。
```

Never claim exact brand, authenticity, size, price, or material unless the user provided it.

## Linking Profile And Wardrobe

When making a recommendation from memory:

```text
current occasion/weather
-> relevant profile constraints
-> candidate wardrobe items
-> filter by comfort and occasion
-> select outfit
-> store feedback if user responds
```

Examples:

- If profile says "不穿高跟", filter high heels out before ranking outfits.
- If profile says "硬挺材质不舒服", prefer soft structure and only suggest stiff pieces when the occasion truly requires them.
- If wardrobe item has `risks: too_casual`, avoid it for client visits unless balanced by formal shoes/bag.

## Feedback Loop

User feedback should update memory carefully:

| User feedback | Memory action |
| --- | --- |
| "这套我喜欢" | Add style/pairing preference if it seems durable. |
| "这条裤子太热" | Add item comfort note and seasonal risk. |
| "这双鞋磨脚" | Add shoe constraint and item risk. |
| "客户场合这样太随意" | Adjust occasion fit for similar items. |
| "我其实不喜欢这种颜色" | Add color dislike to profile. |

Do not overgeneralize from one event. Prefer item-specific notes first, then profile-level rules if the user confirms.

## Privacy Rules

- Explain profile initialization before asking questions.
- Store only personalization-relevant clothing information.
- Do not store sensitive identity, body judgments, attractiveness ratings, medical information, exact addresses, or financial information.
- Do not upload memory to third-party services from the skill itself.
- If a host runtime syncs memory externally, that is outside the portable skill; tell the user to check their host settings if relevant.

## When To Ask Before Saving

Ask before creating or updating longer-term memory when:

- the user has not opted into memory
- the item photo includes personal/sensitive background details
- the note is a durable preference rather than current-task context
- the memory destination is unclear

Use a short prompt:

```markdown
这条偏长期偏好我可以记到你的穿衣画像里：以后少推荐硬挺材质。要记吗？
```

Do not ask for every small temporary observation.
