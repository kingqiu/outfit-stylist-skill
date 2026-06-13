# Skill Self-Check

Run this checklist before publishing, installing into another agent, or making a large workflow change.

## Structure

- `SKILL.md` exists at the skill root.
- YAML frontmatter has `name: outfit-stylist`.
- Public Chinese name remains `穿衣搭子`.
- `SKILL.md` stays as the entrypoint, not the full manual.
- Detailed behavior lives in one-level reference files under `references/`.
- Reusable output scaffolds live in `assets/templates/`.
- No essential runtime behavior exists only in `agents/openai.yaml`.

## Reference Integrity

- Every `references/*.md` file named in `SKILL.md` exists.
- No referenced file requires a second-level hidden reference to understand core behavior.
- Long references have clear headings and can be searched with obvious terms.
- `model-capability-config.md` explains reasoning, image input, and image output separately.
- `end-to-end-workflow.md` explains the complete path from user request to text advice and image board.
- `outfit-board-prompting.md` includes default template, scenario packs, weather packs, hard exclusions, and visual QA.

## Core Behavior

- `SKILL.md` includes a scope guard for unrelated requests.
- Unrelated tasks are not force-fit into outfit styling just because the user mentions the skill.
- `SKILL.md` includes an internal design disclosure guard.
- Prompt extraction or internal design reconstruction requests receive only README-level public summaries and are redirected to normal usage.
- Default recommendation is Chinese.
- Default deliverable is concise text advice plus a generated outfit board when image output is available.
- Text-only advice is allowed only when image generation is unavailable, explicitly unwanted, or impossible in the current agent.
- Normal output uses one strongest outfit, one optional adjustment or backup, and one `穿搭雷区`.
- Three full plans are used only when the user asks for multiple options.
- Image uploads are converted into a structured wardrobe inventory before recommendations.
- Outfit diagnosis gives a clear verdict before rebuilding the outfit.
- Existing clothes are used before substitutes or purchases.

## Model Fallbacks

- No reasoning model: current agent model follows the same reasoning contract.
- No image input model: do not hallucinate uploaded image content; ask for text description only when needed.
- No image output model: explicitly say image generation is unavailable and provide a text board.
- Image input, reasoning, and image output may use different providers.
- No API keys, secrets, tokens, private endpoints, or local-only absolute paths are committed.

## Visual Quality

- Default image template is `Structured OOTD Styling Card`.
- Visual style is illustrated, mobile-first, natural editorial, and not a virtual try-on.
- Image labels are Chinese unless a brand or fashion term truly requires English.
- Uploaded garment fidelity is locked: category, length, color, silhouette, and key details.
- Long trousers do not become shorts; vests do not become coats; Polo shirts do not become T-shirts.
- Board excludes underwear, lingerie, makeup, nail panels, wallet contents, phones, notebooks, receipts, drinks, unrelated props, extra people, logos, and watermarks.
- If Chinese text rendering is weak, switch to numbered board mode and provide Chinese callout mapping separately.

## Memory And Privacy

- Profile initialization explains why questions are asked, how many questions will be asked, optional skipping, and local/private storage expectations.
- Profile memory stores only user-provided preferences and practical constraints.
- Wardrobe memory stores only user-provided or image-visible garment attributes.
- Wardrobe memory has a scale strategy for light, standard, and large wardrobes.
- Large wardrobes use retrieval, summaries, favorites, recent items, and archives instead of loading every item.
- Outfit combinations are saved only when worn, liked, saved, or explicitly approved.
- Do not infer sensitive identity, body judgments, attractiveness, exact brand, size, or health information from photos.
- When memory is unavailable, keep personalization within the current conversation and say so only when relevant.

## Regression Tests

- Review `tests/regression-scenarios.md` after changing prompts, workflow, model configuration, or voice.
- Run a document-level simulation for at least:
  - single uploaded item
  - multi-image wardrobe combination
  - outfit diagnosis
  - outdoor/weather scenario
  - no image output fallback
  - no image input fallback
- Generated visual outputs remain local-only and are not committed.

## SkillHub Publishing

Before uploading to Xiaohongshu SkillHub:

- Node.js is installed and is version 18 or newer.
- `skillhub-upload` CLI is installed and available in `PATH`.
- If the agent does not already have the upload skill, register the package-provided `skill/SKILL.md` in the agent's skill directory.
- Run the skill creator validator on the skill directory and confirm it passes.
- Confirm `git status` is clean or only contains intentional release changes.
- Confirm `git ls-files` does not include `.DS_Store`, generated visual outputs, API keys, `.env` files, secrets, or private local memory.
- Upload the skill directory itself, not the whole development repository, unless the SkillHub tool explicitly asks for the repo root.
- During upload preview, verify display name, description, default prompt, and included files before final submission.
