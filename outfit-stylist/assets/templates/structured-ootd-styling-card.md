# Structured OOTD Styling Card

Use this as the default V1 outfit-board image template.

```text
Create a vertical mobile-first structured OOTD styling card for [SCENARIO].
Use [USER_PROFILE] and [OUTFIT_PLAN] as the styling basis.
Apply the selected scenario pack: [SCENARIO_PACK].

Visual style:
polished fashion notebook page, warm soft paper-texture background, subtle tape details,
clean editorial layout, central semi-realistic illustrated full-body outfit figure,
surrounding garment cards with short Chinese notes, fabric texture circles, color chips,
thin hand-drawn arrows, quiet magazine scrapbook feel, natural wearable styling.

Default layout:
Use the natural editorial layout. Keep the central figure slightly dominant,
stagger item cards around the figure, allow scene/reason/key/risk notes as small paper notes,
and avoid a perfectly rigid grid. Do not use the regular symmetrical card layout unless the user explicitly asks for a strict comparison view.

Header:
moderately sized title on a light paper strip, slim scene chip, slim reason chip,
or left-side paper notes for scene/reason/key/risk. Use muted colors, no dark filled banner,
and no heavy title box.

Main figure:
one central illustrated full-body figure wearing the complete recommended outfit.
The figure should not imply virtual try-on or exact body/face matching.

Item cards:
outerwear / top / bottom / shoes / bag / accessories or belt.
Each card should include 1-2 short Chinese notes and optional fabric texture circle.
For uploaded wardrobe items, preserve category, length, color, silhouette, and key details.
Do not turn long trousers into shorts or alter the garment category to fit the composition.

Bottom analysis:
color palette / overall effect / proportion or silhouette / risk reminder / suitable scenes.
This should be partial and naturally placed rather than a forced full-width grid. Optional final one-line summary on a light tape strip.

Text:
标题：[TITLE]
场景：[SCENE]
首选理由：[REASON]
上衣：[TOP_NOTE]
下装：[BOTTOM_NOTE]
鞋子：[SHOES_NOTE]
包包：[BAG_NOTE]
配饰：[ACCESSORY_NOTE]
色彩：[COLOR_NOTE]
比例：[PROPORTION_NOTE]
风险提醒：[RISK_NOTE]
适配场景：[SCENE_FIT_NOTE]
总结：[SUMMARY]

Constraints:
All visible advice text must be Chinese except unavoidable fashion terms.
No virtual try-on, brand logos, watermarks, dense paragraphs, tiny text, underwear,
makeup, nail close-ups, wallet contents, phones, notebooks, pens, receipts, drinks,
extra people, friend-group photos, decorative mini photo prints, unrelated scene snapshots,
or unrelated lifestyle objects.
Keep header light, bottom cards delicate, and risk reminders calm.
```

## Assembly Notes

Fill the prompt in this order:

```text
1. base card template
2. one scenario pack from references/outfit-board-prompting.md
3. outfit details
4. fidelity locks for uploaded items
5. Chinese text fields
6. hard exclusions
```

Use only one primary scenario pack unless the request clearly combines two contexts, such as "client visit then dinner". For uploaded wardrobe items, put fidelity locks after outfit details so the image model sees them as final constraints.

Scenario pack options:

- `business_client_visit`
- `casual_weekend`
- `outdoor_sport_travel`
- `social_date_gathering`
- `wardrobe_multi_image`
