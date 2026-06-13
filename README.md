# outfit-stylist / 穿衣搭子

一句话简介：根据场合、天气、个人偏好和已有衣物，给出简洁实用的穿搭建议，并在图片生成可用时输出一张中文搭配板。

## 这个 Skill 是什么

`outfit-stylist` 是一个面向日常生活的 AI 穿衣搭配 skill。它不是“形象评判”工具，而是帮助用户更快决定：

- 明天某个场合应该怎么穿
- 一件单品可以怎么搭
- 多张衣服照片里哪些可以组合
- 一套搭配是否合适、哪里需要调整
- 已有衣橱里哪些衣服适合某个天气和场合

中文显示名：**穿衣搭子**

## 常见使用场景

- **场景穿搭**：明天客户拜访、通勤、约会、聚会、婚礼、登山、海边、高铁出差等。
- **单品搭配**：上传一件 Polo、衬衫、针织衫、外套、裤子、鞋或包，让它给出完整搭配。
- **多图组合**：上传多件衣服，判断哪些适合放在一起，哪些不适合当前场合。
- **搭配诊断**：告诉它你准备这样穿，让它判断是否合理，并给出最小修改建议。
- **衣橱记忆**：在支持本地记忆的 agent 环境中，记录用户偏好、单品和穿搭反馈。
- **搭配板生成**：在图片生成模型可用时，输出竖版中文 OOTD 搭配板。

## 默认输出形式

正常情况下，skill 会输出：

1. 一段简洁中文穿搭建议
2. 一套首选搭配
3. 一个小调整或备选
4. 一个“穿搭雷区”
5. 一张竖版中文搭配板图片

如果当前环境没有配置图片生成模型，会明确说明并提供文字搭配板。

## 安装方式

把 `outfit-stylist/` 目录复制到你的 agent skills 目录。

常见路径示例：

```bash
# Codex
mkdir -p ~/.codex/skills
cp -R outfit-stylist ~/.codex/skills/outfit-stylist

# Claude Code
mkdir -p ~/.claude/skills
cp -R outfit-stylist ~/.claude/skills/outfit-stylist

# OpenClaw
OC_WS="${OPENCLAW_WORKSPACE:-$HOME/.openclaw/workspace}"
mkdir -p "$OC_WS/skills"
cp -R outfit-stylist "$OC_WS/skills/outfit-stylist"

# 通用 agents 目录
mkdir -p ~/.agents/skills
cp -R outfit-stylist ~/.agents/skills/outfit-stylist
```

安装后确认目录里至少包含：

```text
outfit-stylist/
├── SKILL.md
├── agents/
├── assets/
└── references/
```

## 模型配置

这个 skill 把模型能力分成三层：

- **reasoning**：负责穿搭判断和推荐逻辑
- **image_input**：负责识别用户上传的衣服/搭配图片
- **image_output**：负责生成 OOTD 搭配板图片

三层可以使用不同模型，也可以都不配置。不配置时，skill 仍可提供文字穿搭建议。

配置模板在：

```text
outfit-stylist/assets/templates/model-capabilities.yaml
```

当前设计支持的模型/供应商类型包括：

- OpenAI GPT Image / vision-capable models
- Google Gemini Nano Banana 系列
- 即梦 / Seedream
- ListenHub Image Skill / OpenAPI
- MiniMax
- DeepSeek / DeepSeek-compatible endpoints
- 智谱 GLM
- Custom provider

不要把 API key、access key、secret、token 或私有 endpoint 写进仓库。

### ListenHub 图片生成兼容

如果你的 agent 环境支持 ListenHub Image Skill 或 ListenHub OpenAPI，可以把它配置为 `image_output` 适配器。

常见配置方式：

```yaml
image_output:
  enabled: true
  provider: "listenhub"
  underlying_provider: "google" # google | openai
  model: "gemini-3-pro-image" # or gemini-3.1-flash-image / gpt-image-2
  adapter: "skill_or_api"
  endpoint_env: "LISTENHUB_API_BASE"
  api_key_env: "LISTENHUB_API_KEY"
  default_aspect_ratio: "9:16"
  default_image_size: "2K"
```

使用 ListenHub 时，`outfit-stylist` 仍然负责生成穿搭建议和搭配板提示词；ListenHub 负责实际图片生成。

## 使用示例

```text
使用 $outfit-stylist：
男，35 岁，明天下午要去金融客户那里做商务拜访。
我有一件灰色 Polo，帮我搭一套，不要太正式但要稳。
```

```text
使用 $outfit-stylist：
我上传了几件衣服，明天有商务聚会。
帮我从这些衣服里组合一套，并说明为什么。
```

```text
使用 $outfit-stylist：
周末登山，32 度，有短时阵雨。
我应该怎么穿？
```

## 注意事项

- 这个 skill 给的是穿搭建议，不是医疗、法律、金融或身份判断。
- 它不会评价用户的吸引力、身材好坏、年龄感或肤色优劣。
- 图片识别结果可能存在不确定性，尤其是材质、厚薄、真实颜色和尺码。
- 如果要长期保存用户画像或衣橱信息，应使用本地或用户可控的记忆环境。
- skill 本身不会主动上传用户画像、衣橱记忆或模型密钥到第三方。
- 生成图片是搭配板，不是真人虚拟试穿。

## 发布与测试

发布前建议检查：

- `outfit-stylist/SKILL.md` 能通过 skill validator
- Git 中没有 `.DS_Store`、测试图、密钥、`.env` 或私有记忆
- `tests/regression-scenarios.md` 中的核心场景仍然成立
- 图片输出仍然遵守衣物保真和中文标签要求

小红书 SkillHub 上传时，推荐上传目录：

```text
outfit-stylist/
```

不要上传整个开发仓库，除非上传工具明确要求。
