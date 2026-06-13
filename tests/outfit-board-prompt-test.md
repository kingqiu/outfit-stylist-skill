# Outfit Board Prompt Test

Date: 2026-06-13
Skill: `outfit-stylist`
Scenario: first client meeting, soft business outfit
Mode: built-in image generation preview
Saved output: `tests/visual-outputs/scenario-1-business-outfit-board-v1.png`
Output size: 941 x 1672 px, close to 9:16

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

- Default visual recommendation: v4-style illustrated deconstructed OOTD board.
- Detailed visual recommendation: v5-style long board, only when the user asks for a detailed board, shareable image, or social-card-like output.
