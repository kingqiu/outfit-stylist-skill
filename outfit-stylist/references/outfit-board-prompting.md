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
1. base template: Structured OOTD Styling Card
2. scenario pack: business / casual / outdoor / social / wardrobe
3. selected outfit details
4. uploaded-item fidelity locks, if any
5. Chinese text fields
6. hard exclusions
7. provider-specific size or reference-image settings
```

Do not start from a freeform aesthetic prompt alone. The board should always be grounded in the final outfit recommendation and any uploaded wardrobe items.

## Scenario Prompt Packs

Choose one primary scenario pack. Add one secondary pack only when the user's situation clearly needs it.

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

## Provider-Specific Prompt Notes

Use these small adjustments only after the main prompt is complete.

- OpenAI GPT Image: keep the prompt explicit about layout, Chinese labels, and reference-item fidelity; use reference images for uploaded garments when available.
- Gemini Nano Banana: ask for cleaner typography and fewer labels when Chinese text density becomes high; use Nano Banana Pro for complex boards.
- Jimeng / Seedream: keep prompts concise when possible; put fidelity locks and Chinese text fields near the end as direct instructions.
- MiniMax image generation: keep item zones explicit and avoid asking for too many small text panels.
- If the provider cannot render Chinese reliably, switch to numbered board mode and provide Chinese callout mapping in text.

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
