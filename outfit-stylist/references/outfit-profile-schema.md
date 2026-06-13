# Outfit Profile Schema

Use this for lightweight personalization. Ask only for missing fields needed for the current task.

```yaml
outfit_profile:
  gender_expression: ""
  age_range: ""
  height_cm: null
  weight_kg: null
  city_or_climate: ""
  common_scenarios: []
  comfort_profile:
    preferred_materials: []
    avoided_materials: []
    fit_preferences: []
    shoe_constraints: []
    exposure_limits: []
  lifestyle:
    work_context: ""
    commute: ""
  preferences:
    liked_styles: []
    disliked_styles: []
    color_preferences: []
    budget_range: ""
  intent_profile:
    target_impression: []
    avoid_impression: []
```

## Learning Rules

Update or remember profile cues when the user says:

- "我不穿高跟"
- "硬挺材质我不舒服"
- "我喜欢亚麻/棉/针织"
- "这套太正式/太随便/太成熟/太甜"
- "我不喜欢露腰/紧身/大 logo"
- "我想看起来专业但不要有压迫感"

Do not infer sensitive identity or body judgments from photos.
