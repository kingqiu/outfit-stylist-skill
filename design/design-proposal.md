# Style Strategist Skill Design Proposal

Date: 2026-06-13
Status: Draft for discussion

## 1. Working Name

Recommended repo/project name: `style-strategist-skill`

Recommended skill name: `style-strategist`

Chinese display name options:

- AI 形象顾问
- 小龙虾形象顾问
- 形象策略师
- 穿搭决策顾问

Recommendation: use `style-strategist` for the Skill name and "AI 形象顾问" as the Chinese public-facing name.

Reason: "style strategist" is broader than outfit generation. It covers personal image, workplace presence, wardrobe decisions, color, silhouette, purchase judgment, and scenario styling.

## 2. Positioning

This Skill is the expert judgment layer before a full wardrobe app.

It helps users turn vague style anxiety into clear, practical decisions:

- What should I wear for this situation?
- Does this item suit me?
- What is wrong with this outfit?
- How do I build a more coherent personal image?
- What should I stop buying?
- How do I reuse what I already own?

The Skill does not default to generating polished images. It produces:

- personal image profile
- scenario outfit recommendation
- outfit critique
- item purchase judgment
- wardrobe audit
- improvement plan
- optional Xiaohongshu-style shareable content brief

## 3. User Model

The Skill should build a lightweight user model first, then enrich it over time.

### Minimum User Model

```yaml
user_profile:
  gender_expression: ""
  age_range: ""
  height_cm: null
  body_notes: []
  skin_tone_notes: []
  hair_makeup_constraints: []
  lifestyle:
    work_context: ""
    common_scenarios: []
    climate_city: ""
    commute: ""
  preferences:
    liked_styles: []
    disliked_styles: []
    comfort_constraints: []
    budget_range: ""
  image_goal:
    target_impression: []
    avoid_impression: []
    role_models: []
```

Avoid overclaiming from photos. When using photos, say "visible cues suggest..." rather than making medical/body judgments.

## 4. Wardrobe Model

MVP does not need a database, but the Skill should use a structured item schema when users provide clothing photos or descriptions.

```yaml
item:
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
```

Later this can become a real wardrobe database.

## 5. Core Modes

### Mode 1: Personal Image Diagnosis

Use when the user says:

- 帮我分析一下我的形象
- 我适合什么风格
- 我想提升穿搭/气质/职场形象
- 帮我建立个人穿搭模型

Workflow:

1. Ask for current context and goal.
2. Build a first-pass profile.
3. Identify current strengths, friction points, and target image.
4. Produce a practical style direction.
5. Give 3 immediate changes and 3 avoid rules.

Output:

- image goal
- style keywords
- color direction
- silhouette direction
- fabric/detail direction
- scenario priorities
- first 7-day action plan

### Mode 2: Scenario Outfit Recommendation

Use when the user says:

- 今天见客户穿什么
- 面试怎么穿
- 周末约会怎么搭
- 用我这几件衣服搭一套

Workflow:

1. Clarify scenario, weather, dress code, and target impression.
2. Inventory available items.
3. Recommend 1 primary outfit and 1 backup.
4. Explain why each piece works.
5. Add detail adjustments: shoes, bag, accessories, hair/makeup, outerwear.
6. Mention risk and last-minute fixes.

Output format:

```markdown
## 推荐方案

**主方案**：...
**目标印象**：...

### 为什么这样搭
- ...

### 细节
- 鞋：
- 包：
- 配饰：
- 发型/妆容：

### 风险与修正
- ...

### 备选方案
- ...
```

### Mode 3: Item Check / Purchase Judgment

Use when the user says:

- 这件衣服适合我吗
- 值不值得买
- 这件怎么搭
- 我买这个会不会闲置

Workflow:

1. Identify item attributes.
2. Match against user profile and current wardrobe.
3. Check use cases and cost-per-wear.
4. Give buy / don't buy / wait / only buy if conditions are met.
5. Suggest 3 pairings if worth buying.
6. Suggest alternatives if not.

Decision scale:

- `buy`: high fit, high use frequency, fills a real gap
- `conditional`: works only under specific styling or fit constraints
- `skip`: duplicate, low use, wrong silhouette/color/formality
- `replace`: buy only if it replaces a worse existing item

### Mode 4: Wardrobe Audit

Use when the user says:

- 帮我整理衣橱
- 我总觉得没衣服穿
- 帮我看看缺什么
- 我想做胶囊衣橱

Workflow:

1. Group items by category, color, formality, season, and scenario.
2. Identify overrepresented categories.
3. Identify gaps.
4. Create 5-10 reusable outfit formulas.
5. Create "keep / alter / donate / replace / buy" list.
6. Prioritize the next 3 upgrades.

Output:

- wardrobe diagnosis
- idle item rescue plan
- missing anchor pieces
- don't-buy list
- next 3 purchases or no-purchase actions

## 6. Style Reasoning Framework

Every recommendation should consider five layers:

1. **Scenario**: What social situation is this for?
2. **Impression**: What should people feel or assume?
3. **Person**: Body, coloring, comfort, lifestyle, personality.
4. **Wardrobe**: What is already available?
5. **Execution**: Fit, proportion, fabric, detail, grooming.

The Skill should avoid single-factor advice:

- not only personal color
- not only body type
- not only trend
- not only "显瘦"
- not only "高级感"

## 7. Safety and Taste Guardrails

Do:

- be practical and kind
- separate visible styling observations from identity judgment
- avoid body shaming
- avoid deterministic claims from limited photos
- respect cultural, gender, religious, professional, and budget constraints
- suggest alterations and styling before purchases
- explain tradeoffs

Do not:

- rank attractiveness
- infer sensitive traits
- diagnose health conditions
- shame body shape, age, skin tone, or budget
- push unnecessary consumption
- promise that one color/shape universally "fixes" the user

Preferred tone:

- honest but not cruel
- concrete rather than mystical
- "here is the style effect" rather than "you are wrong"

## 8. Skill Structure

```text
style-strategist/
├── SKILL.md
├── agents/
│   └── openai.yaml
├── references/
│   ├── user-profile-schema.md
│   ├── wardrobe-schema.md
│   ├── style-reasoning.md
│   ├── scenario-playbooks.md
│   ├── purchase-rubric.md
│   └── safety-guardrails.md
└── templates/
    ├── profile.yaml
    ├── outfit-recommendation.md
    ├── item-check.md
    └── wardrobe-audit.md
```

Keep `SKILL.md` compact. Put detailed examples and rubrics in references.

## 9. MVP Scope

Weekend MVP:

- Create `SKILL.md`
- Create 3 references:
  - `style-reasoning.md`
  - `scenario-playbooks.md`
  - `purchase-rubric.md`
- Create 3 templates:
  - `outfit-recommendation.md`
  - `item-check.md`
  - `wardrobe-audit.md`
- Test with 4 prompts:
  - 职场会议穿搭
  - 约会/社交场景穿搭
  - 单品是否值得买
  - 衣橱不知道缺什么

Defer:

- app UI
- image generation
- virtual try-on
- structured wardrobe database
- shopping links

## 10. Xiaohongshu Validation Angle

The first public validation should be content-first:

1. "AI 形象顾问不是告诉你买什么，而是告诉你为什么总买错"
2. "我把真人形象顾问的问诊流程做成了一个 Skill"
3. "用 4 个问题判断一件衣服值不值得买"
4. "职场人穿搭不是变美，是降低沟通成本"
5. "普通衣橱如何搭出 7 天不重样"

Potential downloadable lead magnet:

- `形象顾问问诊卡`
- `单品购买判断卡`
- `衣橱体检表`

## 11. Key Discussion Decisions

Before implementation, decide:

1. Is the public name "AI 形象顾问" or "小龙虾形象顾问"?
2. Should the Skill support photos from v1, or text-first with photo-aware instructions?
3. Should we make it gender-neutral by default, then add women/men/workplace playbooks later?
4. Should the first public use case be workplace image, everyday outfit, or item purchase judgment?
5. Should we integrate with OpenClaw's skill path immediately, or keep it as a standalone repo first?

## 12. Recommended Build Path

Build it in this order:

1. Design the consultation workflow.
2. Create the Skill skeleton.
3. Encode the four MVP modes.
4. Add a purchase judgment rubric.
5. Test with real user prompts.
6. Package one Xiaohongshu post from the test result.

This keeps the project small enough to finish on a weekend and strong enough to publish.

