# Recommendation Format

Use a compact styling-card format, but do not sound like a form. Default to one strongest recommendation, one optional adjustment/backup, and a "穿搭雷区" section. Do not default to three full plans unless the user asks for multiple options.

Default deliverable: concise text advice plus a generated outfit-board image. Do not stop at text unless image generation is unavailable, the user asks for text only, or the current agent cannot call image generation.

## Scenario Outfit

```markdown
## 今日建议

**我建议你优先选这套。**  
一句话说明这套解决什么问题。

### 首选穿法
上衣 + 下装 + 鞋 + 外套/包/配饰。

这套的取舍是：...

### 小调整
...

### 如果想更...
...

## 穿搭雷区
- ...
```

## Multiple Outfit Options

Use this only when the user asks for multiple plans, such as "给我 2-3 套".

```markdown
## 今日建议

**我建议你优先选方案 1。**

### 方案 1｜首选
...

### 方案 2｜备选
...

## 穿搭雷区
- ...
```

## Single-Item Styling

```markdown
## 这件单品的穿法

图片里这件...看起来...。  
放到你的场景里，它可以...，但需要...

### 首选穿法
这套的作用是...

穿法：...
小调整：...

### 如果想更...
只调整一个关键件，让它...

穿法：...

## 穿搭雷区
- ...
```

Use "方案一/方案二/方案三" only when the user explicitly asks for multiple full plans. In normal single-item styling, the third section should usually be "穿搭雷区", not another outfit.

## Multi-Image Combination

```markdown
## 图片衣物组合

**这几件可以优先放在一起：...**  
原因：...

### 可搭组合
1. **首选**：...  
   适合：... 风险：...
2. **备选**：...  
   适合：... 风险：...
3. **更有风格**：...  
   适合：... 风险：...

### 不建议组合
- ...

### 缺的关键件
- ...
```

## Tone

- Explain tradeoffs, not judgments.
- Prefer "这套更稳" over "这套最好看".
- Prefer "可能显得过于随意" over "不高级".
- Avoid long theory unless requested.
- Avoid pure category rows unless the user explicitly asks for a checklist.
- Use item-list templates as internal scaffolding, not the final voice.
- Use "穿搭雷区" for avoid advice; it is more useful than a weak third option.
- Prefer "我建议你选择..." over "我会让你穿...".

## Image Companion

After the text recommendation, generate or attach the outfit-board image using the default `Structured OOTD Styling Card` template. If it cannot be generated, add a short fallback note before the text board so the missing image is explicit.
