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
  last_used: ""
  user_feedback: []
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
