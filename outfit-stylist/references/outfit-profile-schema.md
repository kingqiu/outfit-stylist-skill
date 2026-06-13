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

## Initialization Flow

Use a lightweight profile initialization when:

- the user is using the skill for the first time
- the user asks for more personalized recommendations
- repeated recommendations would benefit from stable preferences

Do not block an urgent outfit request just because the profile is incomplete. If the user asks "明天怎么穿", answer the current request first, then optionally ask for missing profile details for future use.

Ask at most 6 short questions. Height and weight are optional.

Default quick-start questions:

1. 你希望我按什么性别表达/穿衣方向来给建议？例如男装、女装、中性、都可以。
2. 你的年龄段大概是？例如 20s、30s、40s。
3. 身高体重大概多少？不想说可以跳过。
4. 你最常见的穿衣场景是什么？例如通勤、商务、见朋友、约会、户外。
5. 你喜欢/不喜欢什么风格或颜色？
6. 你有哪些舒适雷区？例如硬材质、紧身、高跟、露肤、太热、磨脚。

Store answers only as user-provided preferences. Do not infer or judge body type from height, weight, or photos.

## Minimal Profile Summary

After initialization, summarize in 3-5 lines:

```markdown
我先按这个画像记：
- 穿衣方向：
- 常见场景：
- 喜欢：
- 避免：
- 舒适雷区：
```

Then continue with normal recommendations.
