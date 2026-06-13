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
2. Group items by compatibility: color, proportion, texture, formality, season.
3. Build 2-3 complete outfits.
4. Mark best, backup, and not-recommended combinations.
5. Add missing items such as shoes, bag, outerwear, or accessories.

## Photo Uncertainty

Use language like:

- "图片里看起来像..."
- "如果实物更偏厚/更亮，可以这样调整..."
- "我不能确定材质，但视觉上更接近..."

Avoid claiming exact brand, size, body measurement, identity, attractiveness, or medical/body judgments.
