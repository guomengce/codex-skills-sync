---
name: yuwen-ppt-0615
description: Use when a primary school Chinese teacher provides Chinese lesson materials such as textbook text, after-class questions, teacher reference, teaching design, or an approved PPT outline, and wants a Markdown PPT outline, three coordinated Markdown planning documents, an editable text-layer PPTX, AI-generated no-text background images, or image-plus-text reference prompts.
---

# yuwen-ppt-0615

This skill supports a review-first elementary Chinese PPT workflow.

Default lesson root:

`D:\項目\999 program\yuwen-ppt`

Use the existing grade / volume / unit / lesson folder structure. Do not create duplicate lesson folders with slightly different names unless the user explicitly asks.

## Core Workflow

Follow this workflow by default:

1. The user provides lesson source materials:课文原文、课后题、教参、教学指导与设计, or similar materials.
2. Generate `00.PPT大纲.md` first as a human-reviewable Markdown outline.
3. Stop for user review. Do not generate `01/02/03` until the user approves or asks to continue.
4. After approval, use `00.PPT大纲.md` as the source of truth and generate:
   - `01.PPT文本.md` for Codex internal editable text-layer production
   - `02.背景图提示词.md` for Codex internal no-text background image generation
   - `03.图文合成提示词.md` for the user's browser workflow to add text and reference styling on top of the already-generated `pic_0x.png` backgrounds
5. When the user asks for the next production stage, generate:
   - `04.PPT文本层.pptx` from `01.PPT文本.md` using the pptx skill
   - no-text background images from `02.背景图提示词.md` using image-gen, saved as `pic_01.png`, `pic_02.png`, ...
6. Leave `03.图文合成提示词.md` for the user's browser workflow to generate image-plus-text reference PPTX or reference pages by reusing the exact `pic_0x.png` backgrounds from step 5. Do not let `03` independently redraw backgrounds that `02` already generated.

Do not generate lesson plans, worksheets, teacher scripts, or final merged decks unless the user explicitly asks.

## Output Files

Use Markdown for reviewable planning files:

```text
00.PPT大纲.md
01.PPT文本.md
02.背景图提示词.md
03.图文合成提示词.md
04.PPT文本层.pptx
pic_01.png, pic_02.png, ...
06.提示词复制助手.html
```

Legacy `.txt` files may exist from older runs. Prefer the `.md` files. If the user asks to clean old txt files, delete only obsolete generated txt planning files after the Markdown files are created.

## Stage 0: Generate Review Outline

When the user provides课文原文、课后题、教参、教学设计与指导, generate `00.PPT大纲.md`.

### Outline Principles

- Use the supplied materials as the source of truth.
- Keep the big teaching situation if the user specifies one, such as a科考情境.
- Align the teaching path with the teacher reference and teaching design.
- For课文精讲, prefer顺文推进 unless the source design clearly supports another path.
- Do not reveal later close-reading conclusions too early. First课时 may埋线索 or设悬念, but should not提前完整总结第二课时要精讲的变化线、情节线、课后题答案.
- Preserve the expected lesson scale, such as 2课时约40页, unless the user asks for another size.
- Make each page useful for PPT generation, not a lesson-plan paragraph.

### Visual Style Baseline

All image-related outputs must fit a grade-2 elementary Chinese classroom:

- bright, fresh, warm, clean, child-friendly
- suitable for second-grade students' aesthetics
- clear forms, readable composition, gentle contrast
- no gloomy, dark, gray, oppressive, horror-like, uncanny, dirty, or overly realistic dramatic style
- no cinematic low-key lighting, heavy shadows, stormy atmosphere, or melancholy color grading unless the user explicitly asks

### `00.PPT大纲.md` Format

Use this exact page block format:

```md
# 课题｜PPT大纲

### 第一课时：...

## 第 1 页｜页面标题

**版面视觉**

Describe the visual composition, style, subject placement, and text-safe area in plain language.

**展示文本**

> Only text that should appear on the PPT page.
> Keep line breaks intentional.

---
```

Rules:

- Use `## 第 X 页｜页面标题` for every page.
- Use only two required fields per page: `**版面视觉**` and `**展示文本**`.
- Do not add routine fields such as `类型`、`课时`、`教学重点`、`课堂任务`、`备注`.
- Put student-facing questions and activity wording inside `展示文本` if they are actually shown on the slide.
- Page separators must use `---`.
- Avoid using `【】` as structural markers. Keep Markdown hierarchy clear.
- Do not include interaction commands such as `点击`、`拖拽`、`飞入`、`弹出` unless the user explicitly wants animation planning.

## Stage 1: Generate Three Markdown Planning Documents

After the user approves `00.PPT大纲.md`, generate exactly these files:

1. `01.PPT文本.md`
2. `02.背景图提示词.md`
3. `03.图文合成提示词.md`

Use `00.PPT大纲.md` as the source of truth.

Role split:

- `01.PPT文本.md` and `02.背景图提示词.md` are internal production inputs for Codex. The user normally does not review them.
- `03.图文合成提示词.md` is user-facing. It should be clean, readable, and suitable for browser-based image-plus-text composition on top of already-generated `pic_0x.png` backgrounds.
- To avoid subtle drift between `02` and `03`, `02` is the only stage allowed to generate backgrounds. `03` must not independently redraw or redesign the background; it must reuse the exact `pic_0x.png` image for that page as the composition base.
- After the user confirms the three Markdown prompt files, generate `06.提示词复制助手.html` as a local browser helper for copying long per-page prompts. It must include separate tabs for `02 纯背景版` and `03 图文合成版`, because the user usually generates all no-text background images first and all image-plus-text reference pages afterward.
- The final merged editable PPTX from `01 + 02` and the image-plus-text reference PPTX from `03` must be near-identical in page composition, shared background image, text placement, visual hierarchy, and major layout elements.
- Once the user has used `03.图文合成提示词.md` to generate a reference PPTX, treat that version as frozen. Do not rewrite `02` or `03` layout/background rules afterward unless the user explicitly decides to regenerate the reference PPTX. If a mismatch is discovered, align `01/04/05` to the already-used `03` reference version rather than changing `03`.

Preserve:

- page count
- page order
- page numbers
- page titles
- `展示文本`
- visual intent from `版面视觉`

Do not invent, merge, delete, or renumber slides unless the user explicitly asks.

### Required Text/Image Avoidance Contract

For every slide, define one explicit layout contract and one background generation lock. Reuse both exactly in `01.PPT文本.md`, `02.背景图提示词.md`, and `03.图文合成提示词.md`.

Each page must include:

- `安全区类型`: one of `center`, `left`, `right`, `top`, `bottom`, `split`, `full-clean`
- `文字安全区`: exact editable text area with approximate position and size
- `背景主体区`: where main image subjects, characters, action, highlights, and dense details may appear
- `禁止进入区`: elements that must not enter the text-safe area
- `背景生成锁定`: the exact no-text background composition that `02` must generate once and `03` must later reuse through the resulting `pic_0x.png`
- `版式锁定`: the exact text placement and major page element arrangement that both the merged editable PPTX and the `03` image-plus-text reference PPTX must follow

Rules:

- Never use vague wording such as `自然留白区` alone.
- Specify side, percentage, and what must stay outside the safe area.
- Keep the same text-safe area across `01/02/03`.
- Keep the same background subject placement, natural clean areas, and major layout skeleton across `02` and the later `03` composition workflow.
- If the outline asks for text over a busy image, reserve a naturally clean low-texture area in the background. In `02`, do not name or draw visible text-layer containers; describe them only as blank low-texture reserved areas that PPTX will fill later.
- For image prompts, strongly forbid intrusion into the safe area: no main subject, face, animal body, high contrast object, bright highlight, dense texture, or decorative icon may enter the text-safe area.
- Do not let `03` invent a prettier but different composition. It may add text styling, but it must use the exact `pic_0x.png` background from `02` and keep the same layout skeleton.
- Apply the visual style baseline to both `02` and `03`; the image-plus-text reference PPTX and merged editable PPTX should share the same bright child-friendly style.
- If the user has already generated the reference PPTX from `03`, preserving that visual match takes priority over later prompt-cleanup improvements. Do not change visible layout concepts, text area placement, or major page composition after that point. When cleaning `02`, keep the same positions and composition but convert any text-layer container wording into clean natural blank space.

### `01.PPT文本.md` Format

Purpose: editable PPT text-layer construction spec. `01 + 02` must be able to combine into one editable PPTX whose visible text-layer components fit the no-text background images generated from `02`.

Use this page block:

```md
## 第 X 页｜页面标题

**可编辑展示文本**

> ...

**文字版式**

- 安全区类型：...
- 文字安全区：...
- 页面组件总数：...
- 组件清单：
  - 组件 1：类型=标题文本框；位置=x%, y%；尺寸=w%, h%；样式=字体、字号、颜色、对齐、填充、边框、圆角、透明度；内容=...
  - 组件 2：类型=正文文本框 / 词卡 / 田字格 / 箭头 / 标注 / 评价区等；位置=...；尺寸=...；样式=...；内容=...
- 排布关系：组件之间的上下左右关系、间距、对齐线、分栏或网格规则
- 文本层级：标题 / 正文 / 词卡 / 问题 / 评价等如何分组

**避让规则**

- 背景主体区：...
- 禁止进入区：...
- 背景生成锁定：same as `02`
- 版式锁定：same as `02/03`

---
```

Rules:

- Keep all PPT text editable.
- Preserve exact wording, pinyin, key characters, and student-facing tasks from `00.PPT大纲.md`.
- Split long课文原文 into readable text boxes without paraphrasing.
- Do not write teacher-script language unless it is shown on the slide.
- Do not use the same generic `文本层级` or `组件建议` for every page. `01` must be a page-specific executable text-layer design spec.
- For each page, specify the actual editable components needed for that page, such as title, body text, question area, word cards,田字格, arrows, labels, check marks, or annotation marks, with position, size, hierarchy, color, fill, border, opacity, and alignment.
- Use 16:9 percentage coordinates for every visible editable component: `x%`, `y%`, `w%`, `h%`. Treat the slide origin as top-left, width as 100%, height as 100%.
- State `页面组件总数` and list every editable text-layer component. If a page needs 6 text boxes and 4 word cards, name all 10 components.
- `01` and `02` must be designed as a pair in the same stage: `02` reserves clean low-texture background areas; `01` places editable components exactly on those areas. Do not use a later generated PPTX or pure-image deck as the source of truth.
- `03` must reuse the same text-safe area and component arrangement described in `01`, while rendering the same arrangement as an image-plus-text reference prompt on top of the exact `pic_0x.png` background for the user's browser workflow.
- If `02` has been cleaned to remove background containers, keep `01` responsible for those visible editable components. Do not copy cleaned `02` wording into `01` in a way that loses the visual structure.

### `02.背景图提示词.md` Format

Purpose: no-text background image prompts.

Use this page block:

```md
## 第 X 页｜页面标题

**背景图提示词**

16:9横向背景图。...

**构图与安全区**

- 安全区类型：...
- 文字安全区：...
- 背景主体区：...
- 禁止进入区：...
- 背景生成锁定：...
- 版式锁定：...

**负面要求**

No Chinese characters, pinyin, letters, numbers, punctuation, labels, watermarks, logos, readable book pages, visible instructional layout containers, visible placeholders, borders, frames, or UI text. No main subject, face, animal body, high contrast object, bright highlight, dense texture, or decorative icon may enter the text-safe area. No gloomy, dark, gray, oppressive, horror-like, uncanny, dirty, overly realistic dramatic, low-key cinematic, heavy-shadow, stormy, or melancholy style.

---
```

Rules:

- Prompt for no-text background only.
- Translate requested text labels, signs, or classroom text-layer components into natural clean negative space only. Avoid naming specific container styles in `02` prompts.
- Keep the text-safe area blank, low-contrast, and low-texture, with no visible text-box style.
- Treat this file as the background authority. It is the only stage that may generate the actual background image. `03.图文合成提示词.md` must reuse the resulting `pic_0x.png` image and the same layout skeleton, then add text.
- Every prompt must explicitly request a bright, fresh, warm, clean, child-friendly grade-2 elementary Chinese lesson style.

### `03.图文合成提示词.md` Format

Purpose: image-plus-text composition prompts for the user's browser workflow, using the already-generated `pic_0x.png` background image as the base.

Use this page block:

```md
## 第 X 页｜页面标题

**图文合成提示词**

Use the uploaded `pic_0x.png` for this page as the only background image. Do not redraw, redesign, or move the background composition. Create a polished 16:9 elementary Chinese lesson PPT reference page by placing the page text and visible text-layer styling into the same text-safe area...

**页面文字**

> ...

**版式参考**

- 安全区类型：...
- 文字安全区：...
- 背景主体区：...
- 禁止进入区：...
- 背景生成锁定：copy the same locked background prompt from `02`
- 版式锁定：copy the same text-safe area and major page element arrangement from `02`
- 字体层级与强调：...

---
```

Rules:

- The browser tool may render text in this reference prompt.
- Keep generated text faithful to `展示文本`.
- Use `03` only as visual reference; final PPT text should still come from editable text layers.
- `03` must be background-locked: it must explicitly instruct the browser workflow to use the uploaded `pic_0x.png` image for that page as the sole background, with no redrawing or alternate composition.
- Do not allow the browser prompt to redesign the background, move the main subject, move the text zone, or add different background text-box/card/panel elements compared with `02`.
- Make `03` user-friendly: the user should be able to copy a page prompt, upload the corresponding `pic_0x.png`, and generate an image-plus-text reference page that closely matches the future merged editable PPTX.
- Every browser prompt must explicitly request the same bright, fresh, warm, clean, child-friendly grade-2 elementary Chinese lesson style and forbid dark or gloomy styles.

### `06.提示词复制助手.html` Format

Purpose: local browser helper for one-click copying long prompts.

Rules:

- Generate it only after the user confirms `01.PPT文本.md`, `02.背景图提示词.md`, and `03.图文合成提示词.md`, or explicitly asks for the copy helper.
- Use one static HTML file in the lesson folder. It must work by opening the file directly in a browser; do not require a dev server.
- Do not show the lesson title or课文名 in the page title or visible header. Use a generic title such as `提示词复制助手`.
- Provide two clearly separated tabs:
  - `02 纯背景版`: one card per page, copying the complete page prompt from `02`
  - `03 图文合成版`: one card per page, copying the complete page prompt from `03`
- Each page row must show only `第 X 页｜页面标题` on the left and a one-click copy button on the right. Do not visibly display the prompt body, because the Markdown text has already been confirmed.
- Keep page order and page titles identical to the Markdown files.
- The helper is only a copying UI. It must not change, summarize, rewrite, or merge prompt content.

## Stage 2: Production

### Generate Editable PPTX

When the user asks, use the pptx skill and `01.PPT文本.md` to generate:

`04.PPT文本层.pptx`

Rules:

- Create a 16:9 editable text-layer PPTX.
- Do not insert background images.
- Use page-specific editable text boxes, light panels, word cards,田字格, arrows, labels, annotation marks, and other components required by `01`. Do not use one repeated component template across all pages.
- Follow the exact `文字安全区` in `01.PPT文本.md`.
- For each slide, create exactly the component count and component types specified in `01` unless a PowerPoint limitation requires a documented adjustment.
- Convert `01` percentage coordinates directly into PPTX positions. Keep each component inside its declared `x/y/w/h` box.
- Preserve each component's declared fill color, border color, border width, opacity, font size, font color, bold/regular emphasis, alignment, and spacing as closely as PPTX allows.
- The resulting `04.PPT文本层.pptx` should look incomplete by itself, because backgrounds are absent, but its editable text components must align with the no-text backgrounds generated from `02`.
- Before generating `04`, check whether `01` contains generic repeated component instructions. If it does, revise `01` first into a page-specific executable text-layer spec.
- After generating `04`, create visual thumbnails to verify page-specific component count, placement, hierarchy, and text fit. Regenerate if pages look like one repeated template or if components do not follow `01`.

### Generate No-Text Background Images

When the user asks, use image-gen and `02.背景图提示词.md`.

Rules:

- Generate one no-text background image per slide.
- Save as `pic_01.png`, `pic_02.png`, `pic_03.png`, ...
- Do not overwrite existing `pic_*.png` unless the user explicitly asks.
- Do not generate text, pinyin, labels, watermarks, logos, or page numbers inside images.
- Treat the generated `pic_0x.png` files as the only background standard for later `03` image-plus-text composition prompts and later editable PPTX refinement.

### Refine Editable PPTX Against Reference PPTX

When the user has already generated an image-plus-text reference PPTX from `03`, and wants the editable PPTX to visually match it more closely, create or refine:

`05可编辑.pptx`

Rules:

- `05` must remain genuinely editable: text, text boxes, title groups, word cards, labels, and panel elements must stay as editable PPT objects.
- Never fake the result by replacing a page with one full image that already contains text.
- Use the user's image-plus-text reference PPTX from `03` as the visual standard for layout, spacing, font scale, hierarchy, emphasis colors, and panel arrangement.
- Keep the no-text background images from `02` as the background layer; refine only the editable foreground text-layer objects and visible UI-like page components.
- If needed, refine in batches, such as the first 5 pages, the first period, or a specific module, instead of requiring the whole deck at once.
- Once the user says a page design is locked, do not redesign it later; only make closer alignment edits.

## Response Behavior

- Be practical and classroom-oriented.
- Prefer clear PPT-generation instructions over教案式长段落.
- Keep review files easy to scan in Markdown.
- Stop at review gates unless the user asks to continue.
- If the target lesson folder is uncertain, ask before saving.
