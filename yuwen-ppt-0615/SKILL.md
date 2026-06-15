---
name: yuwen-ppt-0615
description: Use when a primary school Chinese teacher wants to generate Chinese lesson PPT planning documents first, then later generate lesson plans, worksheets, and teacher-only classroom scripts from the final PPTX.
---

# yuwen-ppt-0615

This skill is for a split workflow:

1. Generate three PPT planning `.txt` documents first.
2. Let the user make the final PPT manually.
3. After the final PPTX exists, read that PPTX and generate the teaching documents that match the real slide flow.

Do not default to generating a finished PPTX deck.

## Core Role

This skill helps the user with:

- Chinese lesson PPT planning
- text/image coordination
- editable text-layer planning
- background image prompting
- image-plus-text reference prompting
- final teaching document generation after the PPT is finished

This skill does **not** assume it should produce the final beautiful PPT automatically.

## Output Folder

Save outputs to:

`D:\语文PPT\<册次>\<课文名>\`

Example:

`D:\语文PPT\二下\中国美食\`

## Stage 1: Generate Three PPT Planning Documents

When the user provides lesson materials and wants PPT planning, generate exactly these three `.txt` files:

1. `课文名-PPTX文本层制作稿.txt`
2. `课文名-背景图片提示词稿.txt`
3. `课文名-图文参考图提示词稿.txt`

Do not generate PPTX yet unless the user explicitly asks for it after review.

### Document 1: PPTX Text-Layer Draft

Purpose:

- plan the editable text layer for a later PPTX

For each slide, include:

- page number
- page core title
- the exact text that should appear on the PPT
- text layout instructions
- optional interaction or animation prompt

Rules:

- keep text very concise
- use original wording, pinyin, or key characters only when necessary
- do not write lesson-plan paragraphs inside slides
- number slides continuously
- each page must work independently
- always think in a 16:9 slide
- all text areas must be editable
- prefer translucent text panels over heavy boxes

### Document 2: Background Image Prompt Draft

Purpose:

- help the user generate no-text background images in a browser image AI

For each slide, include:

- page number
- page core title
- image generation prompt
- composition requirements
- forbidden items

Rules:

- explicitly say `16:9横向背景图`
- do not ask the image AI to generate text, pinyin, labels, or watermarks
- reserve text-safe areas for the PPT text layer
- do not force all pages to use the same text position
- composition must change according to the page content

### Document 3: Full Image With Text Reference Prompt Draft

Purpose:

- help the user generate beautiful image-plus-text reference pages
- these are for visual reference, not final editable teaching files

For each slide, include:

- page number
- page core title
- image-plus-text prompt
- page text
- layout reference requirements
- forbidden items

Rules:

- page numbers and titles must match Document 1 and Document 2
- image AI may render text in this document
- keep page text aligned with the text-layer draft as much as possible
- use this document to guide later text-box style refinement

## Stage 2: Prevent Text/Image Conflict

Every page must be designed as one coordinated layout.

Do not plan text first and image second as separate things.

For each page:

1. decide what the visual subject is
2. decide where the safest text area is
3. make the text-layer draft and image prompt describe the same spatial plan

Rules:

- do not lock all titles to the right side
- title position, font feeling, color, panel style, and size must adapt to the page
- background prompts must describe where to leave clean space
- text zones must avoid faces, strong highlights, dense textures, and key objects
- prefer translucent panels, soft paper tags, thin labels, and natural negative space
- avoid heavy solid text boxes unless the page truly needs a framed task area

## Stage 3: PPTX Generation Principle

Do not automatically generate a full PPT.

Only after user confirmation, and only if the user asks, use:

`课文名-PPTX文本层制作稿.txt`

to generate an editable text-layer PPTX.

Rules:

- do not insert background images
- only generate text boxes and text
- keep text boxes light and editable
- assume the user will add background images later

## Stage 4: Using a Pure Image PPT

The user may separately create a pure image PPT with image and text already fused together.

If requested, help in one of these two ways:

1. refine a small number of editable PPT pages by visually matching a pure image reference page
2. build a reusable text-box style library from screenshots of different pure-image text styles

Do not recommend refining all 36 pages one by one unless the user explicitly wants that tradeoff.

## Stage 5: After the Final PPT Is Finished

Once the user finishes the actual PPT and provides the final PPTX, read that PPTX first.

Then generate:

1. `课文名-教案.txt`
2. `课文名-学习单.txt`
3. `课文名-逐字稿.txt`

These documents must match the real final PPT flow, not an older outline.

### Lesson Plan

Use a standard practical structure:

1. 教材分析
2. 学情分析
3. 教学目标
4. 教学重难点
5. 教学准备
6. 课时安排
7. 教学过程
8. 板书设计
9. 作业设计
10. 教学反思

Rules:

- the teaching process must match the final PPT page flow
- do not create a separate teaching flow unrelated to the finished slides

### Worksheet

The worksheet is a classroom handout, not a generic workbook page.

It may include:

- preview tasks
- character and word recognition
- polyphonic character sorting
- circling and underlining tasks
- tables
- retelling support
- classroom practice
- imagination or extension writing
- self-evaluation

Rules:

- every worksheet section must support actual classroom tasks

### Teacher Script

The teacher script must be generated from the final PPTX.

Rules:

- write only the teacher's spoken language
- do not write student answers
- the language should sound like a real teacher in class
- natural and plain is better than decorative or literary
- transitions between pages must feel natural
- life examples are allowed only when they help the current teaching point
- after any example, return quickly to the text, task, or student action
- do not pad time with empty talk
- each lesson should be able to support a full 40-minute class
- class time should be supported by reading, circling, brief pauses, practice, retelling, and speaking tasks

Preferred transition style:

- `刚才我们已经……`
- `接下来我们把目光放回课文……`
- `读到这里先停一下……`
- `这个地方很有意思……`
- `这个问题先放一放，后面你会发现……`

Avoid robotic transitions like:

- `现在看第X页……`
- `这一页讲的是……`
- `下一页我们来看……`

## Stage 6: Fixed Workflow

Follow this workflow by default:

1. the user provides lesson materials
2. generate the three PPT planning `.txt` files
3. stop and wait for confirmation
4. the user finishes the real PPT manually
5. the user provides the final PPTX
6. read the final PPTX
7. generate the lesson plan, worksheet, and teacher script
8. save all files under `D:\语文PPT\<册次>\<课文名>\`

## Response Behavior

When using this skill:

- be practical
- prefer classroom usefulness over ornate wording
- avoid rigid templates
- keep structure stable but let layout, text position, and visual planning change from lesson to lesson
- if the user asks only for the first-stage documents, do not jump ahead to lesson plan or script generation
- if the user gives a final PPTX, use that PPTX as the source of truth for the second-stage teaching documents
