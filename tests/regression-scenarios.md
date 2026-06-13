# Outfit Stylist Regression Scenarios

Use these fixed scenarios after prompt, workflow, model-config, or voice changes. They are document-level regression tests, not live API tests.

## Evaluation Checklist

Each scenario should verify:

- output defaults to Chinese
- recommendation is concise and mobile-friendly
- one strongest outfit appears first
- one backup or adjustment is included only when useful
- "穿搭雷区" is included for avoid advice
- image board is expected by default when image output is available
- uploaded wardrobe items keep category, length, color, silhouette, and key details
- no invented body judgment, attractiveness ranking, brand claim, or unnecessary purchase push

## T01 Single Item: Gray Polo, Finance Client Visit

Input:

```text
35 岁男性，明天下午跟朋友去金融客户那里做商务拜访。
上传图片：灰色短袖 Polo，领口和袖口有细条纹。
问题：这件 Polo 怎么搭？
```

Expected:

- Use `single-item styling` workflow.
- Recognize the Polo as business-casual but slightly sporty.
- Main outfit should pull formality back through dark trousers, leather shoes, belt/watch, and structured bag if relevant.
- Avoid sneakers, shorts, washed jeans, and loud accessories.
- Generate a business/client visit outfit board when image output is available.

## T02 Multi-Image Wardrobe: Business Social

Input:

```text
我明天有个商务聚会，现在有这些衣服，不知道怎么搭：
图 1 深绿色短袖针织上衣
图 2 深色侧边条纹长裤
图 3 米白无袖马甲
图 4 米白抽绳长裤
图 5 粉色短袖针织上衣
图 6 深棕短袖针织上衣
图 7 黑色短袖针织上衣
帮我组合并说明理由。
```

Expected:

- Use `multi-image wardrobe combination` workflow.
- Classify each item as use / backup / avoid for this occasion.
- Prefer one strongest combination, not three long plans.
- Preserve image 4 as long trousers, never shorts.
- If shoes/bag are not uploaded, label them as suggested additions.
- Generate a wardrobe/multi-image outfit board when image output is available.

## T03 Outfit Diagnosis: Proposed Combination

Input:

```text
这些单品搭在一起去商务聚会合理吗：
深绿色短袖针织上衣 + 深色侧边条纹长裤 + 米白无袖马甲。
```

Expected:

- Use `outfit diagnosis` workflow before replacing the outfit.
- Verdict must be one of: 可以穿 / 可以但要调整 / 不建议这样穿.
- Explain what works and what feels off.
- Suggest 1-2 minimal fixes.
- Generate corrected outfit board when image output is available.

## T04 Outdoor: Summer Hiking With Rain

Input:

```text
男性，45 岁，172cm，160 斤。周末去登山，夏天 32 度，天气预报有短时阵雨。应该如何穿搭？
```

Expected:

- Use `outdoor_sport_travel` plus `high_heat_summer` and `rain_wet_weather` guidance.
- Prioritize breathability, sun/rain protection, anti-slip shoes, and lightweight layers.
- Avoid denim, heavy cotton, leather sole shoes, and non-breathable jackets.
- Keep tone practical and not overly fashion-editorial.
- Generate outdoor function outfit board when image output is available.

## T05 Casual: Gray Polo, Seaside 33C

Input:

```text
上衣还是那件灰色 Polo。明天要跟朋友去海边玩，33 摄氏度，帮我出一个搭配方案。
```

Expected:

- Use `single-item styling` plus `casual_weekend`, `high_heat_summer`, and `sun_uv`.
- Keep Polo recognizable; avoid turning it into a T-shirt.
- Recommend breathable shorts or lightweight trousers only if suitable to the user's stated context; explain heat tradeoff.
- Avoid business trousers, dark heavy layers, and leather formal shoes.
- Generate relaxed seaside board when image output is available.

## T06 Commute: Office Daily With Temperature Swing

Input:

```text
女，30 多岁，明天普通上班通勤，早晚 16 度，中午 25 度，办公室空调比较冷。
我想舒服一点，但不要太随便。
```

Expected:

- Use `commute_office_daily` plus `wind_temperature_swing`.
- Recommend removable layers, office-appropriate shoes, and easy-care fabrics.
- Avoid overly formal client-visit tone.
- Text should be compact, not three full outfits.
- Generate commute outfit board when image output is available.

## T07 Ceremony: Wedding Guest

Input:

```text
下周参加朋友婚礼，室内晚宴，不想太抢眼，但也不能太随便。帮我搭一套。
```

Expected:

- Use `ceremony_wedding_banquet`.
- Respect guest etiquette: polished, not stealing attention, no casual sneakers unless dress code allows.
- Include shoes/bag/accessories because ceremony context needs finishing details.
- Avoid all-white wedding guest looks, excessive sparkle, and uncomfortable shoes.
- Generate ceremony outfit board when image output is available.

## T08 Model Fallback: No Image Output

Input:

```text
男，40 岁，明天客户拜访，25 度。没有配置图片生成模型。
```

Expected:

- Explicitly say image generation is unavailable.
- Provide text-board fallback instead of silently omitting the image.
- Keep the outfit recommendation complete and useful.

## T09 Model Fallback: Images Uploaded, No Image Input

Input:

```text
用户上传 5 张衣服图，但当前环境没有配置图片识别模型。用户问：哪几件能搭？
```

Expected:

- Do not hallucinate image content.
- Ask for a short text description only if needed.
- Give a compact fill-in format: item category, color, silhouette, material, occasion.
- Explain that recommendation can continue after text description.
