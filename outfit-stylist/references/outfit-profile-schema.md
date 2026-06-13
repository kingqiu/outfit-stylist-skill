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

- the user asks for outfit advice and no basic profile exists
- the user asks for more personalized recommendations
- repeated recommendations would benefit from stable preferences

## Trigger Strategy

Use this decision table:

| Situation | Behavior |
| --- | --- |
| First outfit request and no basic profile exists | Ask a short profile initialization before giving a recommendation if the missing basics affect the answer. |
| Missing only non-critical details | Answer the current outfit request first, then optionally ask for missing details for future use. |
| User explicitly asks to build/personalize profile | Start profile initialization immediately. |
| User asks urgently, such as "马上出门怎么穿" | Give the best answer from available context first; ask profile questions after. |
| User has already declined profile questions | Do not ask again in the same thread unless the user reopens the topic. |
| Current task has enough context | Do not interrupt with profile initialization. |

Basic profile fields that may justify asking before recommendation:

- clothing direction or gender expression when the garment categories depend on it
- age range when formality/maturity signal matters
- height/weight or fit constraints when proportions, length, or fit are central to the request
- comfort limits when the scene involves long walking, heat, cold, heels, tightness, or stiff materials

Do not block an urgent outfit request just because the profile is incomplete.

## Required Intro Before Asking

Before asking profile questions, tell the user:

1. why these questions are needed
2. how many questions will be asked
3. that optional questions can be skipped
4. that the information is only for personalization and is stored locally/on the user's own computer when memory is available; it is not intentionally uploaded to a third party by the skill

Use this tone:

```markdown
为了给你搭得更准，我先问你 6 个很轻的问题；不想答的可以跳过。
这些信息只用来做你的穿衣画像，默认保存在你自己的本地环境/个人电脑里，不会由这个 skill 主动上传给第三方。
```

Ask at most 6 short questions. Height and weight are optional.

Default quick-start questions:

1. 你希望我按什么性别表达/穿衣方向来给建议？例如男装、女装、中性、都可以。
2. 你的年龄段大概是？例如 20s、30s、40s。
3. 身高体重大概多少？不想说可以跳过。
4. 你最常见的穿衣场景是什么？例如通勤、商务、见朋友、约会、户外。
5. 你喜欢/不喜欢什么风格或颜色？
6. 你有哪些舒适雷区？例如硬材质、紧身、高跟、露肤、太热、磨脚。

Store answers only as user-provided preferences. Do not infer or judge body type from height, weight, or photos.

## Relationship To Wardrobe Memory

Use `references/memory-workflow.md` when profile preferences need to interact with saved wardrobe items.

Profile memory answers questions like:

```text
What does the user usually need, prefer, avoid, or feel comfortable wearing?
```

Wardrobe memory answers:

```text
What specific items does the user own, and what are those items good or risky for?
```

Do not store item-specific problems as broad profile rules too quickly. For example, "这条裤子太热" should first become an item note, not "用户不穿长裤".

Only promote feedback to profile memory when the user phrases it as a general rule or confirms it across repeated requests.

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
