# Model Capability Configuration

Keep styling reasoning separate from image model availability. Image input and image output may be provided by different models.

## Schema

```yaml
model_capabilities:
  image_input:
    enabled: false
    provider: ""
    model: ""
    supported_inputs:
      - clothing_photo
      - outfit_photo
      - multi_item_photo_set
    notes: ""
  image_output:
    enabled: false
    provider: ""
    model: ""
    default_output: outfit_board
    default_aspect_ratio: "9:16"
    fallback_aspect_ratio: "3:4"
    long_image_aspect_ratio: "9:21"
    supported_outputs:
      - outfit_board
      - styling_board
    notes: ""
```

## Runtime Behavior

- If image input is configured, inspect clothing images for category, color, silhouette, texture cues, formality, season, condition, and pairing risks.
- If image input is unavailable, say briefly: "当前环境没有配置图片识别模型。你可以用文字描述这几件衣服，我会继续帮你搭配。" Then continue text-only.
- If image output is configured, generate an outfit board only when requested or clearly useful.
- If image output is unavailable, say briefly: "当前环境没有配置图片生成模型。我先给你一版文字搭配板。" Then provide a text board.
- Never block the whole recommendation because image input or output is unavailable.
- Keep provider names, model names, credentials, and endpoint details outside the skill instructions.

## Text Board Fallback

```markdown
## 搭配板文字版

主色：
廓形：
材质感觉：

上衣：
下装：
鞋：
包/配饰：
外套：

视觉效果：
```
