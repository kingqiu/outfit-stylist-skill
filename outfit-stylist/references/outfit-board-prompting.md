# Outfit Board Prompting

Outfit board quality is core to this skill. Use vertical, mobile-first composition and Chinese labels.

## Image Requirements

- Default aspect ratio: `9:16`.
- Use `3:4` for simpler boards.
- Use long vertical, such as `9:21`, when one mobile screen cannot fit the content.
- Use phone-friendly width, such as 1080px, if the backend supports explicit dimensions.
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

- **Deconstructed OOTD board**: preferred default visual style. One central outfit figure or outfit flat-lay, surrounding garment/accessory cutouts, hand-drawn arrows, paper texture, light editorial collage. Best for normal outfit-board output.
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

## Deconstructed OOTD Board Prompt

Use this as the preferred default visual direction. It may borrow the feel of deconstructed OOTD collage sheets, but do not copy any specific reference image.

```text
Create a vertical mobile-first deconstructed OOTD outfit board for [SCENARIO].
Use [USER_PROFILE] and [OUTFIT_PLAN] as the styling basis.

Visual style:
deconstructed OOTD styling sheet, soft paper-texture background, editorial collage layout,
central semi-realistic illustrated fashion figure or clean outfit flat-lay, surrounding cutout panels for clothing items,
hand-drawn arrow annotations, light sketch lines, fabric swatches, color chips,
natural wearable styling, refined but not commercial, warm magazine scrapbook feel.
The person should look illustrated, not photorealistic: semi-realistic fashion illustration,
clean Korean/Japanese magazine illustration style, natural adult proportions, not anime, not childish.

Core item zones only:
outerwear / top / bottom / shoes / bag / accessories / fabric detail / color palette.

Layout:
vertical 9:16 phone-friendly composition, one clear central outfit, 4-6 item cutout panels,
large readable Chinese labels, no dense text, no tiny text, no extra explanation blocks.

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

Hard exclusions:
No underwear, bras, panties, lingerie, sleepwear, nail close-ups, manicure panels, makeup items,
lipstick, compact powder, skincare, wallet contents, ID cards, bank cards, receipts, phones,
notebooks, pens, drinks, unrelated lifestyle objects, brand logos, watermarks, or body/beauty ranking.
Focus only on clothing, bag, shoes, accessories, fabric, color, silhouette, and proportion.

Constraints:
All styling advice text must be in Chinese, except unavoidable brand or fashion terms.
Do not generate virtual try-on unless explicitly requested.
Do not copy any specific reference image composition exactly.
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
