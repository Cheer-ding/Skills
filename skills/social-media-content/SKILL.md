---
name: social-media-content
description: Generate ready-to-post social media content summarizing the work or practice from the current session, always including which LLM was used, its verified cost, and the referred skills — then save it as a bilingual (English + Chinese) txt file in the project folder named projectname_socialmedia.txt. Use this skill whenever the user asks to "generate social media content", "write a social media post", "make a post about this", "turn this into a post", "生成社交媒体内容", "写社媒文案", "发帖", or any similar phrasing about creating shareable post content from their current project or practice — even if they don't say "social media" exactly, and even if they only mention one language (still produce both EN and CN unless they explicitly ask otherwise).
---

# Social Media Content Generator

Turn what was just done in this session into ready-to-post social media content — in English and Chinese — and save it as a txt file in the project folder. The value of this skill is consistency: every post follows the same recognizable format the user has approved, so they can publish without editing.

## Step 1 — Gather the four ingredients

All four come from the current conversation and session context. Never invent them.

1. **The practice/topic** — summarize what was just done: what was built, learned, or practiced, the workflow, and anything notable (constraints, tools, results). Pull this from the conversation history, not assumptions.
2. **The LLM** — the model powering the current session, found in the system prompt or environment info (e.g. `z-ai/glm-5.3-flash`). Name it in both friendly form (`GLM 5.3 Flash`) and exact id form.
3. **The cost** — verify it, don't guess. If the model is served on OpenRouter, run:

   ```bash
   curl -s https://openrouter.ai/api/v1/models | python3 -c "
   import json,sys
   data=json.load(sys.stdin)
   for m in data['data']:
       if 'MODEL-ID-SLUG' in m['id']:
           print(json.dumps({k:m[k] for k in ['id','name','pricing','context_length']}, indent=2))
   "
   ```

   The `pricing` values are per single token — multiply by 1,000,000 to present as "per 1M tokens". Report prompt, completion, and `input_cache_read` pricing. If the model is not listed on OpenRouter, look up the provider's published pricing; if nothing verifiable exists, say "pricing not published" plainly rather than inventing numbers.
4. **The referred skills** — which skills from the available skills were actually loaded or used during the session (e.g. `hairline-create`). Name each and add a one-line description of what it does.

## Step 2 — Ask what the user learned

Before writing anything, ask the user:

> **What did you learn from this project? Anything more you want to say about it?**

Their answer becomes the first paragraph of the post, right after the title — in their own voice, first person, lightly polished for posting. This is their reflection, not a summary of the work; keep their meaning intact even if the wording is tidied, and weave in anything extra they wanted to say. Wait for the answer before continuing. If there is nobody to ask (non-interactive run), write the post without it and say plainly in the hand-back that the learning paragraph was skipped.

## Step 3 — Write the English post in this exact format

```
**[Catchy hook naming the practice] [one emoji]**

[What the user learned (from Step 2) — 1-2 sentences right after the title, their voice; include anything extra they wanted to say]

[1-2 sentence intro: what the practice is, what it produces/ships as, why it's interesting]

The workflow is the point:
1️⃣ [Step name]: [one-line description]
2️⃣ [Step name]: [one-line description]
3️⃣ [Step name]: [one-line description]
4️⃣ [Step name]: [one-line description]

**The LLM behind it:** [friendly name] ([exact model id]) via [provider]
**The cost:** $[X] per 1M input tokens, $[Y] per 1M output tokens (cached input just $[Z]/1M) — [one punchy line putting the cost in perspective]

[One closing punchy line about why this combination matters]

#Hashtags #Relevant #To #The #Topic
```

Number of workflow steps matches the actual practice (3-5 is the sweet spot). Hashtags: 5-7, mixing broad (#AI, #LLM) and specific (#TheSkillName, #TheTech).

## Step 4 — Write the Chinese version

Translate the post faithfully but idiomatically — not word-for-word. Keep the structure identical (same hook style, same numbered emoji steps, same bold labels, same closing line). Localize:

- **The LLM behind it** → **背后的 LLM：**
- **The cost** → **成本：**
- Hashtags become Chinese equivalents where natural (#生成艺术, #创意编程, #AIAgent), keep English tags that are proper nouns.
- Prices read as **每百万 token $X** style.

## Step 5 — Add quick facts after each language section

A short record block, useful when the user reposts later:

```
Quick facts behind it:
- Model: `[exact id]` (the LLM powering this session)
- Pricing (verified from [source]): $[X]/M input · $[Y]/M output · $[Z]/M cached · [context length]-token context
- Skill: `[skill-name]` — [one-line description]
```

Chinese version mirrors it:

```
快速事实：
- 模型：`[exact id]`（驱动本次会话的 LLM）
- 定价（已从[来源]核实）：输入 $[X]/百万 token · 输出 $[Y]/百万 token · 缓存 $[Z]/百万 token · 上下文 [N] token
- 技能：`[skill-name]` —— [一句话描述]
```

## Step 6 — Save the file

Save to the project folder as a txt file:

- **Path:** `<project folder>/<projectname>_socialmedia.txt`
- **projectname** = basename of the current working directory (the project being posted about), lowercased, spaces → hyphens. Example: working in `/Users/me/tendedero` → `tendedero_socialmedia.txt`.
- **File layout, in order:**
  1. English post
  2. English quick facts
  3. `---` divider
  4. Chinese post (中文版)
  5. Chinese quick facts
- UTF-8 encoding so Chinese renders correctly everywhere.
- If the working directory is not a project folder, ask the user where to save, or fall back to a topic slug as the projectname.

## Step 7 — Hand back

Show both the English and Chinese versions inline in your reply, then state the file path. Flag anything unverified (e.g. "pricing from provider's site, not OpenRouter").

## Worked example

This is the reference both languages must match in structure and tone. The learning paragraph right after the title is illustrative — in a real run it comes from the user's answer to Step 2, in their own voice.

**English:**

```
**Sketching with code: my latest AI-assisted practice 🖊️**

Biggest takeaway: tight constraints beat a blank canvas — ten rigid drawing rules pushed me toward sharper ideas than total freedom ever has.

I've been using the **hairline** skill (inspired by @lucasmarkes/hairline) to draw isometric line figures — single-stroke rounded solids that answer your pointer — shipped as one self-contained HTML file. No installs, no frameworks.

The workflow is the point:
1️⃣ Concept: one figure, one idea — no labels, geometry speaks
2️⃣ Build: follow 10 strict rules on a fixed rendering kernel
3️⃣ Check: auto-validate + screenshot sheet, fix until it passes
4️⃣ Hand over & adjust: edit only the figure, same bar every iteration

**The LLM behind it:** GLM 5.3 Flash (z-ai/glm-5.3-flash) via OpenRouter
**The cost:** $0.15 per 1M input tokens, $0.50 per 1M output tokens (cached input just $0.03/1M) — a full figure-building session with validation loops costs literal cents. 1M-token context too.

Free-tier pricing, production-grade practice. The skills ecosystem + cheap fast models = a new kind of creative playground.

#AI #GenerativeArt #LLM #CreativeCoding #GLM #OpenRouter #AIagents

---

Quick facts behind it:
- Model: `z-ai/glm-5.3-flash` (the LLM powering this session)
- Pricing (verified from OpenRouter's API): $0.15/M input · $0.50/M output · $0.03/M cached · 1,048,576-token context
- Skill: `hairline-create` — draws one isometric line figure in the Hairline style, validated by build.mjs/look.mjs before hand-off
```

**Chinese:**

```
**用代码画画：我最近的 AI 辅助练习 🖊️**

最大的收获：严格的约束胜过空白画布——十条铁律逼出了比完全自由更锐利的想法。

我一直在用 **hairline** 技能（灵感来自 @lucasmarkes/hairline）绘制等距线稿图——用单一线条勾勒的圆润立体，能回应你的指针——最终交付为一个自包含的 HTML 文件。无需安装，无需框架。

工作流程才是重点：
1️⃣ 概念：一个图，一个想法——不加标签，几何自己说话
2️⃣ 构建：在固定的渲染内核上遵循 10 条严格规则
3️⃣ 检查：自动校验 + 截图对比表，修到通过为止
4️⃣ 交付与调整：只改图形文件，每次迭代同一标准

**背后的 LLM：** GLM 5.3 Flash（z-ai/glm-5.3-flash），经由 OpenRouter
**成本：** 输入每百万 token $0.15，输出每百万 token $0.50（缓存输入仅 $0.03/百万）——一次完整的图形构建加验证循环，成本只要几分钱。还有 1M token 上下文。

免费级的价格，生产级的实践。技能生态 + 便宜的快模型 = 一种全新的创作游乐场。

#AI #生成艺术 #LLM #创意编程 #GLM #OpenRouter #AIAgent

---

快速事实：
- 模型：`z-ai/glm-5.3-flash`（驱动本次会话的 LLM）
- 定价（已从 OpenRouter API 核实）：输入 $0.15/百万 token · 输出 $0.50/百万 token · 缓存 $0.03/百万 token · 上下文 1,048,576 token
- 技能：`hairline-create` —— 绘制单幅 Hairline 风格等距线稿图形，交付前由 build.mjs/look.mjs 校验
```
