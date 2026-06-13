# Image Input Guidelines

Use image input as a core optional path. Never require images if the user can describe clothing.

## One-Item Styling

When the user uploads one item:

1. Translate the image into a styling judgment, not a detection list.
2. Briefly mention category, color, silhouette, material cues, formality, and visible design details only when they affect pairing.
3. Ask the occasion only if it is missing and materially changes the answer.
4. Give one strongest outfit, one light adjustment/backup, and one "穿搭雷区" by default.
5. Name what not to pair with it.

## Single-Item Judgment Pattern

Use this internal order, then write it naturally:

```text
visible item -> style signal -> occasion fit -> pairing strategy -> avoid risk
```

Example:

```text
图片里这件灰色 Polo 本身是偏稳的商务休闲，领口和袖口的细条纹会带一点运动感。
明天下午去金融客户那里，它可以穿，但下半身和鞋要把商务感收回来，避免整套看起来像周末休闲。
```

Do not write like this in final output:

```text
类别：Polo
颜色：灰色
风格：商务休闲
风险：偏休闲
```

Use item labels only when they make the answer easier to scan.

## Single-Item Output Default

For normal user requests, keep the answer short:

```markdown
## 这件 Polo 可以这样搭

一句图像判断 + 场景判断。

### 首选穿法
一句说明这套解决什么问题。

穿法：...
小调整：...

### 如果想更正式一点
...

## 穿搭雷区
- ...
```

## Multi-Image Combination

When the user uploads multiple items:

1. Create a compact inventory.
2. Preserve item identity: category, length, color family, key silhouette, and visible design details.
3. Mark each item as `use`, `backup`, or `avoid for this occasion`.
4. Group items by compatibility: color, proportion, texture, formality, season.
5. Build one strongest outfit first, then one backup only if useful.
6. Explain why unsuitable uploaded items are not used.
7. Add missing items such as shoes, bag, outerwear, or accessories, but label them as suggestions rather than uploaded items.
8. Generate the outfit board from the selected outfit and fidelity locks.

## Multi-Image Selection Logic

Use occasion fit before color matching.

```text
occasion requirement
> item fidelity
> formality fit
> comfort/weather fit
> color and proportion harmony
> novelty or style interest
```

For each uploaded item, decide:

- `use`: belongs in the main outfit.
- `backup`: works if the user wants a different mood.
- `avoid for this occasion`: not wrong generally, but sends the wrong signal now.

Do not create three full options by default. For most multi-image requests, provide:

- one main combination
- one backup adjustment
- one avoid section

When shoes, bag, belt, or outerwear are not among uploaded images, say "建议补" or "如果你有" instead of implying the user uploaded them.

## Multi-Image Visual Prompt Checklist

Before image generation, include:

- selected uploaded item ids and fidelity locks
- backup item ids, if shown
- avoided item ids and why they should not appear in the central outfit
- missing suggested pieces clearly marked as suggested additions
- explicit rule: central outfit may include only selected uploaded items plus clearly suggested missing pieces
- explicit rule: avoid unrelated background props such as drinks, phones, notebooks, receipts, wallets, mini photos, or extra people

## Outfit Diagnosis

Use this when the user provides or implies a proposed outfit combination and asks whether it is reasonable, such as:

- "这几件搭在一起合理吗？"
- "我想这样穿，你帮我看看。"
- "这套哪里不对？"
- "帮我诊断一下这套搭配。"

Do not immediately replace the outfit. First diagnose the proposed combination.

Diagnosis order:

```text
proposed items
-> occasion fit
-> formality consistency
-> color relationship
-> proportion and silhouette
-> material/season comfort
-> one or two minimal fixes
```

Default output:

- clear verdict: `可以穿 / 可以但要调整 / 不建议这样穿`
- what works
- what feels off
- minimal fix: change 1-2 pieces only
- avoid note
- outfit-board image of the corrected version when image output is available

If the user has not specified the intended occasion, ask only when it materially changes the diagnosis. Otherwise give a general diagnosis and state the assumption.

For uploaded items, preserve item fidelity. If the fix changes an uploaded item, say exactly which uploaded item is being swapped out and why.

## Wardrobe Item Fidelity

When the user uploads clothing images, selected items must stay visually faithful in recommendations and generated outfit boards.

Lock these attributes before prompting image generation:

```text
item id -> category -> length -> color -> silhouette -> key details
```

Examples:

- "图 4：米白色长裤，抽绳腰，直筒/锥形长裤" must not become shorts.
- "图 3：米白无袖马甲" must not become a long coat or shirt.
- "图 2：深色侧边条纹长裤" must not become plain black trousers if used.

Do not alter uploaded items to better fit weather, style, or layout. If the original item is unsuitable, recommend a different uploaded item or say a missing substitute is needed.

## Photo Uncertainty

Use language like:

- "图片里看起来像..."
- "如果实物更偏厚/更亮，可以这样调整..."
- "我不能确定材质，但视觉上更接近..."

Avoid claiming exact brand, size, body measurement, identity, attractiveness, or medical/body judgments.
