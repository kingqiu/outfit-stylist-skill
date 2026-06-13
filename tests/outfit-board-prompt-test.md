# Outfit Board Prompt Test

Date: 2026-06-13
Skill: `outfit-stylist`
Scenario: first client meeting, soft business outfit
Mode: built-in image generation preview
Saved output: `tests/visual-outputs/scenario-1-business-outfit-board-v1.png`
Output size: 941 x 1672 px, close to 9:16

Note: visual outputs are local-only test artifacts and are ignored by Git. Filenames are kept here for local comparison, but generated images should not be committed.

## Source Outfit

- Scene: Shanghai, first meeting with a traditional-industry client, 18-24°C, light wind.
- User preference: simple, comfortable, no stiff blazer, no heels.
- Outfit: light blue shirt, black straight-leg trousers, black loafers, beige knit cardigan, black tote, small silver jewelry.

## Prompt Tested

```text
Create a vertical mobile-first fashion outfit board for a first client meeting in Shanghai, 18-24°C, light wind. Use a wearable soft business casual outfit: light blue button-up shirt, black straight-leg trousers, loafers, beige knit cardigan, black tote bag, small silver earrings or thin silver necklace.

Visual style: professional fashion editorial styling board, clean magazine layout, outfit blueprint sheet, refined but wearable, modern East Asian / Korean street-fashion editorial sensibility, polished natural light, quiet premium look, no product-ad feeling.

Layout: vertical 9:16 portrait composition, phone-friendly width, spacious blocks, one clear hero outfit on a stylish adult woman model shown as half-body or full-body fashion editorial view, plus 4 small detail panels for shirt, trousers, loafers, tote/accessories. Use neat annotation lines, fabric texture callouts, color palette chips, tailoring and proportion notes. No clutter, no tiny text.

Required visible Chinese text on image, large and readable:
标题：柔和商务穿搭
场景：第一次见客户
首选理由：专业但不硬
搭配要点：浅蓝衬衫 / 黑色直筒裤 / 米色针织
风险提醒：开衫别太松

Outfit details: light blue button-up shirt; black straight-leg trousers; black loafers; black tote bag; small silver earrings or thin silver necklace; beige knit cardigan.

Constraints: All styling advice text must be in Chinese except unavoidable fashion terms. Do not generate virtual try-on. Do not add body-shaming, attractiveness ranking, identity assumptions, brand logos, watermark, or dense paragraphs. Keep text crisp and readable.
```

## Result QA

Pass:

- Vertical fashion board direction is correct.
- Canvas is mobile-friendly: 941 x 1672 px.
- Outfit is accurate enough: light blue shirt, beige cardigan, black trousers, black loafers, black tote.
- Chinese text is mostly readable.
- Styling board looks professional and useful.
- It does not look like real-person virtual try-on.
- Detail panels are helpful.

Issues:

- The model added too many extra explanation panels.
- Some callout text became denser than ideal for a phone screen.
- The board is closer to a long infographic than a compact styling card.
- Chinese rendering is good enough in this run, but still needs a fallback rule for models with weaker text rendering.
- This output is good for a detailed/shareable board, but too dense as the default board.

Prompt refinement made:

- Add hard limits for visible text.
- Ask the model not to invent large extra explanation panels.
- Limit callout groups to 6 unless the user asks for a long detailed board.
- Split prompt guidance into compact board, detailed board, and numbered board modes.
- Add a visual QA checklist to `references/outfit-board-prompting.md`.

## Next Visual Test

Run a second prompt with stricter text limits:

- one hero outfit
- 4 detail panels
- 5 Chinese labels max
- no extra information panels
- optional numbered-callout fallback

## Compact Board V2

Saved output: `tests/visual-outputs/scenario-1-business-outfit-board-v2-compact.png`
Output size: 941 x 1672 px, close to 9:16

Prompt adjustment:

```text
Create a COMPACT vertical mobile-first fashion outfit board...
Use exactly these 5 label groups and no other text panels:
1. 标题：柔和商务
2. 场景：第一次见客户
3. 首选理由：专业但不硬
4. 搭配要点：浅蓝 / 黑裤 / 米色针织
5. 风险提醒：开衫别太松
```

QA:

- Pass: text density is much better for default mobile output.
- Pass: 5 label groups are large and readable.
- Pass: 4 detail panels are enough for shirt, trousers, loafers, tote/accessories.
- Pass: outfit remains accurate and wearable.
- Pass: no extra paragraph blocks.
- Watch: numbered label cards are visually acceptable, but may feel slightly mechanical for more editorial styles.

Decision:

- Use deconstructed OOTD board mode as the preferred default visual style after user feedback.
- Use compact board mode when text control and fast phone scanning matter most.
- Use detailed board mode only when the user asks for a shareable long image, Xiaohongshu-style board, or detailed styling breakdown.
- Keep numbered cards as an acceptable fallback when Chinese text control matters more than editorial feel.

## User Reference Style Feedback

User preferred a deconstructed OOTD collage-board style similar to the provided Xiaohongshu references, but with strict exclusions.

Borrow:

- central outfit + surrounding item breakdowns
- paper texture
- hand-drawn arrows
- clothing/fabric/accessory cutout zones
- warmer editorial collage feeling

Exclude:

- underwear/lingerie
- nail close-ups/manicure
- makeup and skincare items
- wallet contents, phones, notebooks, pens, receipts, cards
- unrelated small lifestyle objects

Prompt update:

- Added `Deconstructed OOTD Board Prompt` to `references/outfit-board-prompting.md`.
- Added hard exclusions and QA checks.
- Added `tests/reference-style-notes.md`.

## Deconstructed OOTD V3

Saved output: `tests/visual-outputs/scenario-1-business-outfit-board-v3-deconstructed-ootd.png`
Output size: 945 x 1665 px, close to 9:16

Prompt direction:

```text
deconstructed OOTD styling sheet, soft warm paper-texture background,
editorial collage layout, central full-body outfit figure,
surrounding cutout panels for clothing items,
hand-drawn arrow annotations, fabric swatches, color chips.

Hard exclusions:
No underwear, no bras, no panties, no lingerie, no nail close-ups,
no manicure panels, no makeup items, no wallet contents, no phones,
no notebooks, no pens, no drinks, no unrelated lifestyle objects.
```

QA:

- Pass: closest to the desired visual direction so far.
- Pass: paper texture, hand-drawn arrows, central figure, and surrounding garment panels work well.
- Pass: no underwear, nail details, makeup, or unrelated lifestyle objects appeared.
- Pass: focus stays on clothes, bag, shoes, accessory, fabric, color, and silhouette.
- Watch: some Chinese text still has model-generated awkwardness; for production-critical boards, prefer fewer labels or numbered callouts with exact Chinese text outside the image.

Decision:

- Make `deconstructed OOTD board` the preferred default visual style.
- Keep `compact board` as a stricter backup when text readability is the highest priority.

## Illustrated OOTD V4

Saved output: `tests/visual-outputs/scenario-1-business-outfit-board-v4-illustrated-ootd.png`
Output size: 941 x 1672 px, close to 9:16

Prompt direction:

```text
central semi-realistic illustrated fashion figure,
clean Korean/Japanese magazine illustration style,
natural adult proportions, soft facial detail,
not photorealistic, not anime, not childish,
outfit accuracy prioritized over face detail
```

QA:

- Pass: closer to the user-provided reference mood than the photorealistic version.
- Pass: feels like styling guidance rather than a simulated try-on photo.
- Pass: clothing, bag, shoes, earrings, materials, and color palette are clearly represented.
- Pass: no underwear, nail details, makeup, wallet contents, phone, notebook, pen, or unrelated lifestyle objects.
- Pass: Chinese labels are more integrated with the board style.
- Watch: some item renderings become slightly idealized; keep garment cutouts realistic enough for fabric and silhouette.

Decision:

- Default figure style should be semi-realistic fashion illustration.
- Use photorealistic model output only when the user explicitly asks for editorial photography or realistic lookbook style.
- For this skill, illustrated OOTD is a better default because it reduces false try-on expectations and keeps attention on styling logic.

## Detailed Illustrated OOTD V5

Saved output: `tests/visual-outputs/scenario-1-business-outfit-board-v5-detailed-illustrated-ootd.png`
Output size: 864 x 1821 px, long vertical mobile image

Prompt direction:

```text
DETAILED vertical mobile-first deconstructed OOTD outfit board,
7-9 surrounding panels, fabric swatches, color palette,
proportion notes, occasion fit, risk reminder,
semi-realistic illustrated fashion figure.
```

QA:

- Pass: richer than v4 and suitable for a detailed/shareable styling board.
- Pass: visual hierarchy remains clear despite more panels.
- Pass: includes garment panels, fabric close-ups, color palette, proportion, risk, and occasion fit.
- Pass: still avoids underwear, manicure, makeup, wallet/phone/notebook/pen, and unrelated lifestyle objects.
- Pass: Chinese text is readable enough for a long phone image.
- Watch: detailed board is too much for default chat output; reserve for user-requested long image or shareable card.

Decision:

## Single-Item Business Board V2

Saved output: `tests/visual-outputs/scenario-3-male-gray-polo-finance-visit-ootd-v2.png`
Output size: 885 x 1778 px, long vertical mobile image

Prompt direction:

```text
Single uploaded item as anchor: gray short-sleeve Polo with subtle collar/sleeve trim.
Business/client context: financial client visit, quiet layout, muted palette.
Show the Polo as a cutout and integrated into the central outfit.
Complete outfit: dark tailored trousers, black leather shoes, black belt, structured dark business bag, optional navy lightweight blazer.
```

QA:

- Pass: uploaded item remains visually recognizable as the outfit anchor.
- Pass: menswear business-casual direction is clear.
- Pass: no underwear, makeup, nail details, wallet contents, phone, notebook, pen, receipts, or unrelated lifestyle objects.
- Pass: Chinese labels are mostly readable and useful.
- Pass: outfit items are complete: Polo, trousers, shoes, belt, bag, optional blazer, fabric and color references.
- Watch: bottom summary cards make the board feel slightly like a UI panel instead of a fashion annotation sheet.

Prompt refinement made:

- Added "no bottom summary cards, checkbox cards, icon tiles, UI panels, rating boxes, or dashboard-like blocks".
- Require labels to feel like fashion annotation notes attached to garments, not app interface components.

## Single-Item Business Board V3

Saved output: `tests/visual-outputs/scenario-3-male-gray-polo-finance-visit-ootd-v3.png`
Output size: 885 x 1777 px, long vertical mobile image

QA:

- Pass: removed the boxed footer cards from V2.
- Pass: board feels more like a deconstructed OOTD sheet.
- Pass: anchor Polo, trousers, blazer, bag, belt, shoes, texture swatches, and color palette are clear.
- Pass: Chinese labels are readable.
- Watch: model still placed reason/key/risk as a bottom text footer, although without boxes.

Prompt refinement made:

- Added label placement rules.
- Reason should sit beside trousers/shoes; risk should sit beside the risky substitution or shoes.
- Do not place reason/key/risk as a separate footer section.

## Single-Item Business Board V4

Saved output: `tests/visual-outputs/scenario-3-male-gray-polo-finance-visit-ootd-v4.png`
Output size: 887 x 1774 px, long vertical mobile image

QA:

- Pass: no footer cards or bottom summary section.
- Pass: labels are integrated near garments and accessories.
- Pass: anchor Polo appears both as a cutout and on the central outfit figure.
- Pass: outfit is complete and business-appropriate: Polo, tailored trousers, leather shoes, belt, business bag, optional blazer.
- Pass: Chinese labels are large and readable.
- Pass: board avoids underwear, makeup, nail details, phones, wallet contents, notebooks, pens, receipts, and unrelated lifestyle objects.
- Watch: "正式感靠裤鞋" became "正式感靠裤鞋" / "正式感靠裤鞋" style phrasing in some generations; prefer the more natural label "裤鞋拉正式感" in future prompts.

Decision:

- V4 is usable but not the preferred visual direction.
- It is too much like a loose product breakdown; it lacks the stronger information hierarchy of the best structured OOTD card.
- Future prompts should use the structured OOTD styling card layout: top title/scene/reason, central figure, surrounding garment cards, compact bottom analysis strip.
- Use natural short callouts such as `裤鞋拉正式感`, `Polo 做主角`, `别配运动鞋`.

## Gold Standard Reference Update

User selected the strongest generated visual so far: the female soft-business OOTD card for first client meeting.

Why it is better:

- Clear information hierarchy: title, scene, reason, outfit, item cards, bottom analysis, one-line summary.
- The central figure is integrated with the styling logic instead of floating among isolated products.
- Garment cards have enough structure to be readable but still feel like a fashion notebook page.
- Bottom modules for color, proportion, risk, and suitable scenes are helpful, not clutter.
- It feels like a professional styling recommendation rather than a product collage or app UI.

Decision:

- Make `structured OOTD styling card` the preferred default image layout.
- Allow a compact bottom analysis strip when it looks like a paper styling note, not a dashboard.
- Do not over-remove structure; the goal is readable styling hierarchy, not a sparse collage.

## Single-Item Business Board V5 Structured Card

Saved output: `tests/visual-outputs/scenario-3-male-gray-polo-finance-visit-ootd-v5-structured-card.png`
Output size: 864 x 1821 px, long vertical mobile image

Prompt direction:

```text
Use the selected soft-business OOTD card only as an information-hierarchy reference:
top title, scene/reason chips, central illustrated outfit figure, surrounding garment cards,
fabric texture circles, bottom analysis strip, final one-line summary.
Do not copy the reference image's person, outfit, pose, exact typography, exact layout, or text.
```

QA:

- Pass: strongest menswear board so far; much closer to the selected gold-standard style.
- Pass: information hierarchy is clear from top to bottom.
- Pass: central figure and item cards support the same outfit instead of feeling like loose product cutouts.
- Pass: bottom analysis strip is useful: color, proportion, risk, and suitable scenes.
- Pass: uploaded Polo remains the anchor and appears in both garment card and central outfit.
- Pass: no underwear, makeup, nail details, wallet contents, phones, notebooks, pens, receipts, or unrelated lifestyle objects.
- Watch: the red "no sneakers" risk icon is slightly heavy; future prompts should keep risk reminders calmer and more editorial.

Decision:

- V5 structured card becomes the preferred default image direction for single-item business styling.
- Keep the full card hierarchy; do not strip the board down into sparse callouts.
- Add a risk-visual constraint: avoid oversized warning/prohibition icons unless the issue is safety-critical.

## Single-Item Business Board V6 Lower-Half Polish

Saved output: `tests/visual-outputs/scenario-3-male-gray-polo-finance-visit-ootd-v6-lower-polish.png`
Output size: 863 x 1823 px, long vertical mobile image

Prompt direction:

```text
Use the selected female soft-business OOTD card as a refinement reference for the lower half:
delicate bottom analysis cards, airy spacing, soft paper texture, subtle tape,
refined shoes, elegant trouser hem, small calm icons, and balanced final summary.
```

QA:

- Pass: lower half is more polished than V5.
- Pass: trouser hem and black leather shoes are cleaner and less bulky.
- Pass: bottom analysis cards are lighter and more balanced.
- Pass: risk reminder no longer uses a heavy red prohibition symbol.
- Pass: full structured card hierarchy remains intact.
- Watch: title is slightly large; bottom icons still feel a bit tool-like compared with the softer gold-standard reference.

Decision:

- V6 is the current preferred menswear single-item business board.
- Keep the lower-half polish constraints in future prompts.
- Further refinement should make icons more editorial and reduce oversized title weight slightly.

## Single-Item Business Board V7 Light Header

Saved output: `tests/visual-outputs/scenario-3-male-gray-polo-finance-visit-ootd-v7-light-header.png`
Output size: 863 x 1823 px, long vertical mobile image

Prompt direction:

```text
Use the selected female soft-business OOTD card as a quality reference for light header balance:
moderately sized paper-note title, slim scene/reason strips, muted colors, no dark filled banner,
airy top spacing, while preserving the V6 lower-half polish.
```

QA:

- Pass: header is much lighter than V6 and closer to the selected gold-standard reference.
- Pass: title no longer dominates the model.
- Pass: scene and reason chips feel like paper notes rather than banners.
- Pass: structured card hierarchy and lower analysis strip remain intact.
- Watch: trouser hem exposes slightly too much ankle for a business-client menswear scene.

Decision:

- Use V7's header direction as the preferred header treatment.
- Combine it with a menswear fit constraint: avoid cropped trousers or excessive ankle exposure in business scenes.

## Template Abstraction

The selected female soft-business card and the refined male Polo card share the same underlying image template.

Template name:

- `Structured OOTD Styling Card`
- Chinese name: `结构化 OOTD 穿搭卡`

Use as:

- Default V1 image-output template.
- Works across gender, occasion, style direction, and input mode.
- Future image styles can be added as separate templates, but this is the default until another template is intentionally selected.

Reusable structure:

1. light header: title, scene chip, reason chip
2. central illustrated full-body outfit figure
3. surrounding garment cards with short Chinese notes
4. fabric or texture circles attached to item cards
5. bottom analysis strip: color, proportion, risk, suitable scenes
6. optional final one-line summary

Template asset:

- `outfit-stylist/assets/templates/structured-ootd-styling-card.md`

## Natural Editorial Variant

The user later pointed out that an earlier male Polo board felt stronger than the stricter grid version because it was more natural and less stiff.

Why it works:

- The central figure feels more alive and less trapped by a grid.
- Scene, reason, key points, and risk notes sit naturally on the left like paper notes.
- Item cards are staggered around the figure, which gives the page more editorial movement.
- The background scene adds atmosphere without becoming a lifestyle prop dump.
- Bottom analysis is lighter and partial, so the card does not become a dashboard.

Decision:

- Keep `Structured OOTD Styling Card` as the default template.
- Replace the earlier regular grid-card layout with the `natural editorial layout`.
- Do not use the regular symmetrical card layout as the default because it feels too stiff.
- Use a stricter grid only if the user explicitly asks for an organized comparison view.

- Default visual recommendation: structured OOTD styling card with natural editorial layout.
- Detailed visual recommendation: use the same natural editorial foundation, then add more garment/detail notes only when the user asks for a detailed board, shareable image, or social-card-like output.

## Scenario 4: Same Polo, Seaside 33C

Prompt:

> 上衣还是刚刚给的灰色 Polo，明天要跟朋友到海边玩，温度 33°C，帮我出一个搭配方案。

Saved output: `tests/visual-outputs/scenario-4-male-gray-polo-seaside-33c-ootd-v1.png`
Output size: 941 x 1672 px, close to 9:16

Text recommendation tested:

```markdown
## 今日建议

**我建议你把这件灰色 Polo 往“清爽海边休闲”方向搭。**  
33°C 去海边，重点不是再增加造型感，而是降温、防晒、走路舒服，同时别显得太随便。

### 首选穿法
灰色 Polo + 浅卡其短裤 + 皮质凉鞋/干净运动凉鞋 + 帆布托特包 + 墨镜和浅色帽子。

Polo 可以自然放下来，或者只做一点前塞；短裤选膝上到膝盖附近，面料要轻薄但别太软塌。  
如果会进咖啡店或坐车空调比较冷，可以带一件白色或米色薄亚麻衬衫，当防晒外搭。

### 小调整
鞋子如果要走沙滩，就选凉鞋；如果主要是海边餐厅和拍照，可以换成干净小白鞋，但别选厚重款。

## 穿搭雷区
- 不建议配深色长裤、黑皮鞋或商务包。这件 Polo 本身偏稳，再这样搭会太闷，也不适合 33°C 的海边。
```

Visual QA:

- Pass: natural editorial layout works better than the regular grid for a relaxed seaside scene.
- Pass: anchor gray Polo remains recognizable.
- Pass: outfit logic matches heat and seaside activity: light shorts, sandals, tote, sunglasses, hat, linen overshirt.
- Pass: Chinese labels are readable and mostly natural.
- Pass: no underwear, makeup, nail details, wallet contents, phones, notebooks, pens, receipts, or unrelated clutter.
- Watch: model added small extra scene photos/people near the bottom; future prompts should avoid extra people or photo inserts unless requested.
- Watch: bottom suitable-scene area can be calmer and less scrapbook-like for default outputs.

Decision:

- Natural editorial layout generalizes well beyond business scenarios.
- Add future prompt constraint: no extra people, no friend-group photo inserts, no decorative mini photos unless the user asks for a social-card style.
