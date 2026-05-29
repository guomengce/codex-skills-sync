---
name: pptx-match-image-design
description: Adjust an editable PPTX to visually match a non-editable full-page image reference PPT. Use when the user has two outputs for the same lesson or presentation: (1) an editable PowerPoint with text boxes/shapes that can be modified, and (2) beautiful pure-image slides or full-page design reference images that are not editable. The skill compares each editable slide against its corresponding image reference, then edits text layout, fonts, hierarchy, transparent/outlined text boxes, labels, numbered items, spacing, and component positions so the editable PPT closely matches the reference while preserving editability.
---

# PPTX Match Image Design

## Skill Sync Rule

If this skill or another skill in the sync repository is modified, automatically run the following from the sync repository after edits are complete:

```text
git add .
git commit -m "update skills"
git push
```

Only do this when skill files themselves changed. Do not commit ordinary lesson outputs such as `.txt`, `.pptx`, images, or temporary files from lesson folders.

## Goal

Transform an editable PPTX from a rough text-layer deck into a polished editable deck by using a pure-image PPT or page images as the visual reference.

The final PPTX should remain editable: text must stay as PowerPoint text, boxes and lines should remain editable shapes, and videos/placeholders should remain replaceable. The reference image is a design target, not a layer to paste over the slide.

## Expected Inputs

Use this skill when the user provides or points to:

- Editable PPTX: the deck to modify.
- Visual reference: either a pure-image PPTX, exported slide images, or full-page design reference images.
- Optional source TXT files: text-box outline, background prompt file, full-page design prompt file.

If the files are stored in the user's lesson folder, look there first. For the user's elementary Chinese workflow, the common folder root is:

```text
D:\項目\999 program\yuwen-ppt
```

When the user says only a lesson name, automatically search under this root. For example, if the user says `火烧云`, use:

```text
D:\項目\999 program\yuwen-ppt\火烧云
```

Do not ask the user to re-upload files if the lesson folder exists.

## Lesson Folder Search Rules

1. Extract the lesson title from the user request, preferring text inside Chinese book-title brackets such as `《火烧云》`.
2. Search `D:\項目\999 program\yuwen-ppt` for a folder whose name contains the lesson title.
3. If exactly one folder matches, use it.
4. If multiple folders match, choose the shortest and closest folder name; if still ambiguous, ask the user to choose.
5. If no folder matches, tell the user the folder was not found and ask them to confirm the lesson folder name.
6. In the lesson folder, look for both PPT files:
   - Editable PPTX: a file name containing words such as `可编辑`, `透明文字层`, `透明文本框`, `文本框设计版`, or similar.
   - Image/reference PPTX: a file name containing words such as `纯图`, `整页设计`, `图片版`, `参考`, `设计图`, or similar.
7. If there are multiple candidates, prefer the newest file. Avoid choosing files whose names contain `旧`, `备份`, `backup`, or `old` unless no better candidate exists.
8. Save the revised editable PPTX back into the same lesson folder.

For a lesson such as `火烧云`, likely files include:

```text
课文名-透明文本框设计版.pptx
课文名-透明文字层PPT-无背景无占位.pptx
课文名-整页设计图.pptx
课文名-纯图设计版.pptx
课文名-文本+图片大纲.txt
```

Prefer the newest editable PPTX and the newest image/reference PPTX unless the user specifies exact files.

## Core Principle

Match the reference image's visual design while preserving editability.

Do:

- Move and resize editable text boxes to align with the reference.
- Adjust font family, font size, boldness, color, line spacing, and alignment.
- Add editable transparent text-box components: rounded outline boxes, dashed borders, labels, numbered circles, divider lines, arrows, simple icons, and video placeholders.
- Rebuild lists as designed components when the reference uses learning-tip boxes, numbered question strips, reading-flow diagrams, or writing-guidance cards.
- Keep background images or generated background layers separate from editable text whenever the target workflow needs background/text compositing.

Do not:

- Flatten the editable slide into a screenshot.
- Paste the pure reference image over the whole slide as a shortcut.
- Leave visible helper labels such as “background image area” or “replace with image”.
- Add large opaque rectangles that hide the background unless the reference design clearly uses them and the user accepts less background visibility.
- Change lesson content beyond the approved text.

## Workflow

1. Identify file pairs.
   - Locate the editable PPTX.
   - Locate the corresponding image-reference PPTX or slide images.
   - Confirm slide counts match. If not, align by title and page order, and report mismatches.

2. Create slide images for comparison.
   - Render or export thumbnails/images for the editable PPTX and reference PPTX when tools are available.
   - If rendering is unavailable, inspect PPTX XML and use provided screenshots/images as reference.

3. Analyze the reference design per slide.
   For each slide, extract or infer:
   - Main text safe area and background-heavy area.
   - Title position, size, font style, color, and weight.
   - Body text grouping: raw list, numbered strips, table, cards, flowchart, quote box, writing card, video placeholder.
   - Component geometry: x/y position, width, height, border style, corner radius, line color, fill transparency, icon/label placement.
   - Visual rhythm: spacing, alignment, line breaks, emphasis colors.

4. Edit the editable PPTX.
   - Use the `pptx` skill's OOXML editing workflow or a reliable PPTX generation/editing approach.
   - Preserve editable text runs and editable shapes.
   - Apply slide-by-slide changes based on the reference.
   - For Chinese elementary lesson decks, prefer a clean classroom style:
     - Title: large, often KaiTi/楷体-like or bold black style.
     - Body: readable KaiTi/Microsoft YaHei/SimHei style.
     - Lists: designed as transparent learning-tip boxes or numbered strips.
     - Writing pages: video box + writing-point cards.

5. Validate.
   - Ensure text is not cut off.
   - Ensure objects do not overlap unexpectedly.
   - Ensure no full-slide reference image is covering editable text.
   - Ensure key text remains selectable/editable.
   - Export or generate thumbnails if available, then compare with the reference.

6. Save the revised PPTX.
   Use the stable output name and overwrite the previous generated editable PPTX if it exists:

```text
课文名-可编辑PPT.pptx
```

Do not create parallel files such as `修正版`, `新版`, `-2`, or date-stamped variants unless the user explicitly asks for archival versions. If a temporary working copy is needed during editing, delete it after the final PPTX is written.

## Matching Heuristics

When the reference has a designed list:

- Convert plain bullet lists into numbered rows or learning-tip boxes.
- Use transparent or very light fill only if it does not hide the background.
- Use round numbered badges for 1/2/3.
- Use thin dividers between items.
- Align text baselines and row heights evenly.

When the reference has a title:

- Make the title visually dominant.
- Use larger type and stronger weight than body text.
- Keep title close to the reference's position and width.
- Avoid tiny top-left titles unless the reference uses them intentionally.

When the reference has writing-guidance pages:

- Keep the stroke-order video placeholder as an editable rectangle or media placeholder.
- Put 3-4 characters per slide.
- Give each character its own editable writing-point card.
- Make character labels large and clear.

When the reference has a reading-flow diagram:

- Build editable circles, arrows, and short text labels.
- Keep the flow direction and spacing similar to the image reference.

## Output Response

After finishing, respond with:

- Output PPTX path.
- Which editable PPTX and visual reference were used.
- What was changed, briefly.
- Any limitations, especially if visual rendering/thumbnail validation was unavailable.

Keep the response concise.
