# Regression Run: 2026-06-13

Scope:

- Fixed scenarios from `tests/regression-scenarios.md`
- Document-level simulation only
- No live API calls
- No generated image files

## Environment Assumptions

```yaml
reasoning:
  enabled: simulated
image_input:
  enabled: simulated_when_inventory_is_given
image_output:
  enabled: simulated
```

For fallback cases, the relevant model layer is treated as unavailable.

## Results Summary

| Test | Scenario | Result | Notes |
| --- | --- | --- | --- |
| T01 | Single gray Polo, finance client visit | Pass | Correctly uses single-item workflow and business pack. |
| T02 | Multi-image wardrobe, business social | Pass | Requires fidelity locks; long trousers must remain long trousers. |
| T03 | Outfit diagnosis | Pass | Diagnosis-first behavior is clear. |
| T04 | Summer hiking with rain | Pass | Outdoor + heat + rain pack combination works. |
| T05 | Seaside 33C with gray Polo | Pass | Casual + heat + sun pack combination works. |
| T06 | Office commute with temperature swing | Pass | New commute pack covers this well. |
| T07 | Wedding guest | Pass | New ceremony pack covers etiquette and risk notes. |
| T08 | No image output fallback | Pass | Explicit fallback wording is defined. |
| T09 | No image input fallback | Pass | Avoids hallucinating uploaded images. |

## Detailed Notes

### T01 Single Item: Gray Polo, Finance Client Visit

Expected path:

```text
single uploaded item
-> anchor inventory
-> business_client_visit pack
-> dark trousers + leather shoes + structured accessories
-> text advice + outfit board
```

Pass criteria met:

- Polo is treated as business-casual, not formal.
- Formality is corrected through trousers, shoes, and accessories.
- Avoid section should call out sneakers, shorts, washed jeans, and overly sporty styling.
- Board prompt must preserve Polo collar, short sleeves, gray color, and trim.

Residual risk:

- Image model may still over-formalize into a dress shirt if the anchor item is not strongly locked.

### T02 Multi-Image Wardrobe: Business Social

Expected path:

```text
multi-image inventory
-> classify use / backup / avoid
-> wardrobe_multi_image + business labels
-> one strongest combination
-> board with selected items only
```

Pass criteria met:

- Missing shoes/bag can be suggested but must be labeled as additions.
- Avoided items should not appear in the central outfit.
- Long trousers remain long trousers.

Residual risk:

- The image model may simplify side stripe trousers into plain trousers unless key details are included in fidelity locks.

### T03 Outfit Diagnosis

Expected path:

```text
proposed combination
-> verdict
-> what works
-> what feels off
-> minimal fixes
-> corrected board
```

Pass criteria met:

- Diagnosis happens before replacement.
- Verdict vocabulary is constrained.
- Fix should change only 1-2 pieces unless the outfit is unsuitable for the occasion.

Residual risk:

- The text template must avoid sounding overly negative when verdict is "可以但要调整".

### T04 Outdoor: Summer Hiking With Rain

Expected packs:

```text
outdoor_sport_travel
high_heat_summer
rain_wet_weather
```

Pass criteria met:

- Breathability, anti-slip, rain layer, and sun/heat management are covered.
- Outfit does not become a pure gear catalogue.
- Avoid note covers denim, heavy cotton, leather sole shoes, and non-breathable outer layers.

Residual risk:

- Too many weather notes can crowd the board; use at most 6 callout groups.

### T05 Casual: Gray Polo, Seaside 33C

Expected packs:

```text
casual_weekend
high_heat_summer
sun_uv
```

Pass criteria met:

- Polo remains the anchor.
- Output shifts away from business tone.
- Heat and sun risks are included.

Residual risk:

- If shorts are recommended, the text should account for user's age, comfort, and venue expectations rather than defaulting to beachwear.

### T06 Commute: Office Daily With Temperature Swing

Expected packs:

```text
commute_office_daily
wind_temperature_swing
```

Pass criteria met:

- Removable layer and office AC are covered.
- Tone is daily polished, not client-meeting formal.
- Shoes and bag are practical.

Residual risk:

- If user gives no gender expression or wardrobe, ask only when garment categories materially depend on it.

### T07 Ceremony: Wedding Guest

Expected pack:

```text
ceremony_wedding_banquet
```

Pass criteria met:

- Etiquette boundaries are explicit.
- Board includes finishing details: shoes, bag, accessories.
- Avoid notes cover all-white guest looks, excessive shine, and uncomfortable shoes.

Residual risk:

- Cultural wedding color rules vary; ask if the user mentions region, family custom, or dress code uncertainty.

### T08 No Image Output

Expected behavior:

```text
当前环境没有配置图片生成模型。我先给你一版文字搭配板。
```

Pass criteria met:

- Missing image is explicit.
- Text-board fallback remains complete.

Residual risk:

- Agent should not apologize repeatedly or over-explain model configuration to the user.

### T09 No Image Input

Expected behavior:

```text
当前环境没有配置图片识别模型。你可以简单描述这几件衣服的颜色、版型和材质，我会继续帮你搭配。
```

Pass criteria met:

- No hallucinated image content.
- User receives a compact fill-in path.

Residual risk:

- If the host agent can visually inspect images outside configured model capabilities, it should still preserve the same inventory contract.

## Overall Findings

The current workflow covers the V1 core:

- text scenario recommendation
- single-item styling
- multi-image wardrobe combination
- outfit diagnosis
- model fallbacks
- scenario and weather prompt packs

Most remaining risk is visual-generation fidelity, not recommendation structure. Future live tests should focus on:

- uploaded item category preservation
- long trousers not becoming shorts
- Chinese text readability
- avoiding unrelated props
- keeping board layout natural rather than rigid
