# Outfit Stylist Simulated Scenario Test

Date: 2026-06-13
Skill: `outfit-stylist`
Status: first self-test after initial skill build

## Test Assumptions

- User-facing output defaults to Chinese.
- This is a document-level simulation, not a live model invocation.
- Image input/output are treated as unavailable unless the prompt gives enough clothing details in text.
- The goal is to test recommendation structure, mobile readability, fallback behavior, and whether the skill avoids heavy image diagnosis.
- Generated visual outputs are local-only test artifacts and should not be committed to GitHub.

## Evaluation Criteria

- Best option appears first.
- Output is compact enough for phone reading.
- Recommendation is concrete: top, bottom, shoes, outerwear, bag/accessory where relevant.
- Reasoning is short and practical.
- Risk or adjustment note is present.
- Existing clothes are used before purchases.
- Image-model failure does not block the workflow.

## Scenario 1: Business Meeting, Text-Only

Prompt:

> 女，32 岁，165cm，55kg，上海。明天 18-24 度，有点风，下午第一次见一个偏传统行业客户。平时喜欢简洁、舒服，不喜欢太硬的西装，也不穿高跟。衣橱里有白衬衫、浅蓝衬衫、米色针织开衫、黑色直筒裤、深蓝牛仔裤、乐福鞋、小白鞋、黑色托特包。帮我搭 2-3 套。

Simulated output:

```markdown
## 今日建议

**首选**：方案 1
**理由**：第一次见传统行业客户，浅蓝衬衫 + 黑裤 + 乐福鞋最稳，专业但不硬。

### 方案 1｜柔和商务
- 上衣：浅蓝衬衫
- 下装：黑色直筒裤
- 鞋：乐福鞋
- 外套：米色针织开衫
- 包/配饰：黑色托特包，银色小耳钉或细项链
- 适合感觉：可靠、干净、亲和
- 注意：衬衫下摆要处理利落，避免开衫太松显随意。

### 方案 2｜更正式一点
- 上衣：白衬衫
- 下装：黑色直筒裤
- 鞋：乐福鞋
- 外套：米色针织开衫
- 包/配饰：黑色托特包
- 适合感觉：清爽、端正
- 注意：白衬衫容易显正式，妆发和配饰可以柔和一点。

### 方案 3｜不推荐给首次客户
- 上衣：白衬衫
- 下装：深蓝牛仔裤
- 鞋：小白鞋
- 适合感觉：日常通勤
- 注意：对传统行业首次会面偏随意，除非对方氛围很轻松。

## 快速判断
- 最稳：方案 1
- 更有风格：方案 2 加一条细项链
- 不建议：牛仔裤 + 小白鞋作为首选
```

QA:

- Pass: best option first, uses existing clothes, respects comfort, avoids hard blazer.
- Watch: output slightly long but still phone-readable.

## Scenario 2: Weekend Social, Weather Constraint

Prompt:

> 男，28 岁，178cm，70kg，杭州。周六和朋友吃饭、逛展，12-18 度，可能下小雨。我想看起来干净、有点审美，但不要太用力。已有灰色卫衣、白 T、黑色夹克、卡其裤、深色牛仔裤、运动鞋、切尔西靴。

Simulated output:

```markdown
## 今日建议

**首选**：黑色夹克 + 白 T + 卡其裤 + 切尔西靴
**理由**：逛展和吃饭都适合，雨天比运动鞋更稳，也不会太用力。

### 方案 1｜干净轻熟
- 上衣：白 T
- 下装：卡其裤
- 鞋：切尔西靴
- 外套：黑色夹克
- 适合感觉：清爽、有一点审美
- 注意：裤脚别堆太多，雨天更利落。

### 方案 2｜更松弛
- 上衣：灰色卫衣
- 下装：深色牛仔裤
- 鞋：切尔西靴
- 外套：黑色夹克
- 适合感觉：轻松、干净
- 注意：卫衣不要太厚，否则室内吃饭会热。

### 方案 3｜最休闲
- 上衣：白 T + 灰色卫衣
- 下装：深色牛仔裤
- 鞋：运动鞋
- 外套：黑色夹克
- 注意：小雨天运动鞋容易显脏，除非防水性好。

## 快速判断
- 最稳：方案 1
- 更舒服：方案 2
- 不建议：浅色或麂皮鞋
```

QA:

- Pass: rain-aware, not over-styled, phone-friendly.
- Watch: should ask if jacket is water-resistant only if user needs precision.

## Scenario 3: Single Uploaded Item

Prompt:

> 我上传了一件浅绿色短袖针织上衣，帮我配裤子、鞋和包。场景是周日下午咖啡店见朋友，天气 25 度。

Simulated output:

```markdown
## 这件单品的穿法

**单品判断**：浅绿色短袖针织偏温柔、轻松，适合做上半身亮点；下装要干净，不要再堆太多颜色。

### 搭法 1｜清爽咖啡店
- 配：米白直筒裤或浅卡其裤
- 鞋/包：奶油色平底鞋 + 小号米色包
- 适合：温柔、干净、不费力
- 注意：裤子别太软塌，针织 + 软裤容易没精神。

### 搭法 2｜更利落
- 配：深蓝直筒牛仔裤
- 鞋/包：棕色乐福鞋 + 棕色小包
- 适合：轻法式、日常见朋友
- 注意：牛仔裤不要破洞，避免破坏清爽感。

### 搭法 3｜更有风格
- 配：白色 A 字半裙
- 鞋/包：银色/浅色凉鞋 + 小挎包
- 适合：更柔和、更有照片感
- 注意：裙长到膝下更稳，太短会偏甜。

## 不建议
- 不建议配高饱和红/紫下装，容易抢掉浅绿色的清透感。
```

QA:

- Pass: item attributes translated into styling formulas; avoid pairings included.
- Watch: if actual image color differs, response should add uncertainty phrase.

## Scenario 4: Multi-Image Combination, Image Input Unavailable

Prompt:

> 我上传 8 张衣服图，里面有 3 件上衣、2 条裤子、1 条半裙、2 双鞋。帮我从里面组合 3 套，标出哪套最适合明天通勤。

Simulated output:

```markdown
当前环境没有配置图片识别模型。你可以用文字描述这 8 件衣服的颜色、款式和材质，我会继续帮你组合。

为了最快出结果，你可以这样发：
- 上衣 1/2/3：
- 裤子 1/2：
- 半裙：
- 鞋 1/2：
- 明天天气和通勤方式：

收到后我会给你：
1. 最适合通勤的一套
2. 一套更舒服的备选
3. 一套更有风格的组合
并标出不建议搭在一起的单品。
```

QA:

- Pass: no hallucinated image content; fallback is clear and actionable.
- Issue: Need a separate positive test with mock image inventory to test actual multi-image combination logic.

## Scenario 5: Comfort Conflict

Prompt:

> 我明天要参加一个半正式晚餐，但我不喜欢硬邦邦的材质，穿亚麻、棉、针织会舒服很多。怎么穿能得体，但不要像穿制服？

Simulated output:

```markdown
## 今日建议

**首选**：软结构外套/针织开衫 + 垂坠长裤 + 乐福鞋
**理由**：用颜色和轮廓做得体感，不靠硬挺材质强撑正式。

### 方案 1｜柔和半正式
- 上衣：细针织上衣或棉质衬衫
- 下装：深色垂坠直筒裤
- 鞋：乐福鞋或低跟穆勒鞋
- 外套：软结构外套、针织外套或亚麻混纺外套
- 包/配饰：小号皮质包，金属耳饰
- 适合感觉：得体、放松、有质感
- 注意：全身软材质时，鞋包要稍微利落。

### 方案 2｜更轻松
- 上衣：棉麻衬衫
- 下装：高腰阔腿裤
- 鞋：简洁皮鞋/凉鞋
- 适合感觉：自然、松弛
- 注意：衬衫最好塞一半或加腰带，避免像度假装。

## 快速判断
- 最稳：方案 1
- 更舒服：方案 2
- 不建议：硬挺西装、很紧的裤子、容易扎皮肤的面料
```

QA:

- Pass: comfort priority works; formality achieved through silhouette, color, shoes, accessories.
- Watch: if user has actual items, should switch from abstract formulas to wardrobe-specific combinations.

## Scenario 4B: Multi-Image Combination, Mock Inventory Available

Prompt:

> 图片识别结果如下：上衣 A 白色短袖衬衫，上衣 B 黑色针织短袖，上衣 C 浅蓝牛津纺衬衫；裤子 A 黑色直筒裤，裤子 B 浅色牛仔裤；半裙 A 深灰 A 字中长裙；鞋 A 黑色乐福鞋，鞋 B 白色运动鞋。明天 20-26 度，普通通勤，想精神一点但不要太正式。帮我组合 3 套。

Simulated output:

```markdown
## 图片衣物组合

**最佳组合**：浅蓝牛津纺衬衫 + 黑色直筒裤 + 黑色乐福鞋。通勤最稳，精神、干净，也不会像正式会议装。

### 可搭组合
1. **首选通勤**：浅蓝衬衫 + 黑色直筒裤 + 黑色乐福鞋  
   适合：精神、可靠。风险：衬衫太皱会显疲惫，出门前简单熨一下。

2. **更轻松**：白色短袖衬衫 + 浅色牛仔裤 + 黑色乐福鞋  
   适合：普通办公室、轻松通勤。风险：牛仔裤如果水洗感太强，会偏休闲。

3. **更有风格**：黑色针织短袖 + 深灰 A 字中长裙 + 黑色乐福鞋  
   适合：想要更利落、有一点女性化轮廓。风险：全身偏深，包可以用浅色提亮。

### 不建议组合
- 浅蓝衬衫 + 深灰半裙 + 白色运动鞋：上半身通勤、下半身偏学生感，信号有点散。

### 缺的关键件
- 一只浅色或中性色通勤包，可以让第 3 套不显沉。
```

QA:

- Pass: actual multi-image combination logic works with inventory.
- Pass: marks best, alternatives, not-recommended pairing, and missing piece.
- Watch: if the user asks for "一个屏幕看完", reduce to best + one backup.

## Summary

Overall result: pass for first self-test.

Strengths:

- The skill keeps recommendations practical and compact.
- It puts the best option first.
- It respects comfort and avoids forcing standard formalwear.
- It handles unavailable image recognition without hallucinating.
- It uses Chinese by default.

Issues to fix or improve:

1. Add a clearer rule for uncertainty language when a single uploaded item is described by image.
2. Consider tightening normal 3-look outputs; some responses may exceed the one-screen ideal.
3. Add an explicit "visual board prompt test" after text output stabilizes.

Recommended next iteration:

- Tighten mobile output for 3-look recommendations.
- Then test an outfit-board prompt generated from Scenario 1 or Scenario 3.

## Default Output Structure Iteration

Issue found after single-image test:

- Default output should not expand into `方案 1 / 方案 2 / 方案 3`.
- Three full plans are too much for phone reading unless the user explicitly asks.
- The third plan is often more useful as a negative example or avoid section.

Updated default:

```markdown
## 今日建议

**我建议你优先选这套。**
一句话说明判断。

### 首选穿法
具体组合。

### 小调整
1-2 个可执行调整。

### 如果想更...
一个轻量替换或备选方向。

## 穿搭雷区
- 避免什么，以及为什么。
```

Decision:

- Default output = 首选穿法 + 小调整 + 如果想更... + 穿搭雷区.
- Multiple full plans only when the user asks for 2-3 options or detailed analysis.

## Single-Image Polo Test

Prompt:

> 35 岁男性，明天下午跟朋友约了去一个金融客户那里做商务拜访，基于一张灰色短袖 Polo 图片做搭配建议。

Output shape that matched user expectation:

```markdown
## 今日建议

**我建议你把这件灰色 Polo 往“干净商务休闲”方向搭。**  
它本身带一点运动休闲感，所以去金融客户那里，正式感要靠裤子、鞋、包和外套补回来。

### 首选穿法
灰色 Polo + 深色西裤/挺括休闲裤 + 黑色乐福鞋 + 黑色皮带 + 深色商务包。  
如果客户环境偏正式，加一件藏青色轻薄西装外套，进办公室前穿上会更完整。

### 小调整
Polo 衣摆保持平整，建议扎进裤子或半扎；鞋不要用运动鞋，包也别用运动双肩包。

### 穿搭雷区
灰色 Polo + 牛仔裤 + 运动鞋。  
不是不好看，而是去金融客户那里会显得太松。
```

Visual output:

- `tests/visual-outputs/scenario-3-male-gray-polo-finance-visit-ootd.png`

Decision:

- This is the preferred default text length and section structure.
- Use image boards as optional companion output when requested or when testing image mode.

## Copy Voice Iteration

Issue found after review:

- Earlier recommendation copy was structurally correct but sounded too mechanical.
- It exposed the model's category structure instead of sounding like a human stylist making a practical judgment.
- The user experience should be "a stylist has thought about my situation", not "a system filled outfit fields".

Updated direction:

- Add `references/stylist-voice.md`.
- Update templates so they start with a human judgment before item lists.
- Use item lists as scaffolding, not the final voice.

Rewritten Scenario 1 sample:

```markdown
## 今日建议

**我建议你优先选方案 1。**  
第一次见传统行业客户，重点不是穿得很强势，而是让对方觉得你稳、清楚、好沟通。这套把正式感放在裤子和鞋上，把亲和感放在颜色和材质上。

### 方案 1｜首选
浅蓝衬衫 + 黑色直筒裤 + 米色针织开衫 + 黑色乐福鞋 + 黑色托特包。

浅蓝比白衬衫柔和一点，黑裤和乐福鞋把商务感收住，米色针织替代硬西装，让你看起来专业但不紧绷。  
小调整：衬衫下摆处理利落，开衫别太松；银色小耳钉或细项链就够了。

### 方案 2｜如果客户更正式
白衬衫 + 黑色直筒裤 + 乐福鞋 + 黑色托特包。

这套会更端正，但距离感也更强。可以用柔和妆发或细配饰把它放松一点，避免像制服。

### 方案 3｜只在氛围轻松时用
浅蓝衬衫 + 深蓝牛仔裤 + 乐福鞋。

这套更像日常通勤，我不放首选，因为第一次见传统客户时可能略随意。

## 快速判断
- 最稳：方案 1
- 更正式：方案 2
- 不建议：小白鞋 + 牛仔裤作为第一次见客户主方案
```

QA:

- Pass: sounds more like a human stylist.
- Pass: connects recommendation to situation and social signal.
- Pass: still compact enough for mobile if limited to 2-3 options.
- Watch: keep the first paragraph short; human voice cannot become long essay.
