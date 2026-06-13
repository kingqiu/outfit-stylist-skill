# Wardrobe Schema

Use this when the user provides clothing descriptions or photos.

```yaml
item:
  id: ""
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
  condition: ""
  pairing_potential: []
  risks: []
  image_reference: ""
  source: ""
  confidence: ""
  tags: []
  favorite: false
  archived: false
  last_used: ""
  user_feedback: []
```

Optional wardrobe-level summaries:

```yaml
wardrobe_summary:
  category_counts:
    tops: 0
    bottoms: 0
    shoes: 0
    outerwear: 0
    bags: 0
    accessories: 0
  dominant_colors: []
  missing_basics: []
  high_use_items: []
  occasion_ready_sets: []
  frequent_risks: []
  last_updated: ""
```

Optional outfit combination memory:

```yaml
outfit:
  id: ""
  scenario: ""
  season: []
  weather_tags: []
  formality: ""
  item_ids: []
  why_it_worked: ""
  user_feedback: ""
  repeatability: ""
  last_worn: ""
  avoid_repeating_until: ""
  archived: false
```

## Item Reading

For each garment, identify only visible or user-provided attributes:

- category: shirt, knit, blazer, trousers, jeans, skirt, dress, coat, shoes, bag, accessory
- color: include brightness, warmth, saturation when useful
- silhouette: cropped, relaxed, slim, straight, wide-leg, oversized, structured, soft
- material cues: linen-like, cotton, knit, denim, wool, leather, satin, synthetic sheen
- formality: casual, smart casual, business casual, formal
- season: spring, summer, autumn, winter, transitional
- risks: wrinkles, too sheer, too stiff, too casual, hard to walk, visually heavy

Use existing items first before suggesting purchases.

## Memory Use

Use `references/memory-workflow.md` when saving or reusing wardrobe items across sessions.

Wardrobe item memory should stay practical:

- what the item is
- when it works
- what it pairs with
- what risks it has
- what feedback the user gave after wearing it

Do not infer exact brand, authenticity, size, price, material, or body fit from photos unless the user provides it.

When generating outfit boards from wardrobe memory, pass these as fidelity locks:

```text
item id -> category -> length -> color -> silhouette -> key details
```

If memory confidence is low, use cautious language and ask the user to confirm before relying on the item for a high-stakes occasion.

## Retrieval Fields

Use these fields to retrieve items before reasoning:

- `category`
- `season`
- `formality`
- `tags`
- `pairing_potential`
- `risks`
- `comfort_notes`
- `favorite`
- `archived`
- `last_used`
- `user_feedback`

Recommended tags:

```text
commute, client_visit, business_social, casual_weekend, date, ceremony,
outdoor, travel, high_heat, cold, rain, sun, walkable, wrinkle_resistant,
soft_material, structured, breathable, reliable_basic, statement_piece
```

Do not retrieve archived items unless the user asks to review the full wardrobe.

## Candidate Set Limits

For normal outfit recommendations, retrieve a small candidate set instead of loading the whole wardrobe:

```yaml
candidate_set:
  tops: 3-8
  bottoms: 3-8
  shoes: 2-5
  outerwear: 0-4
  bags_accessories: 0-5
```

For large wardrobes, retrieve in batches and summarize. Do not put 200+ item records into the prompt.

## Outfit Memory Use

Use outfit memory to avoid starting from scratch:

- retrieve successful outfits for similar scenarios
- avoid outfits the user disliked
- avoid repeating the same look too soon unless it is marked as a reliable uniform
- reuse item pairings that received positive feedback

Save outfit combinations only when the user wore, liked, saved, or explicitly approved them. Do not store every generated suggestion as history.
