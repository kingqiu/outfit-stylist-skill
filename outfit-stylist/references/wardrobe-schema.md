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
  condition: ""
  pairing_potential: []
  risks: []
  image_reference: ""
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
