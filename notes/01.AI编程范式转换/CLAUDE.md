# CLAUDE.md — notes/01.AI编程范式转换

When the user pastes a slide image in this directory's context, do the following without being asked again:

1. Save the image to `images/{slide-title}.png` (strip punctuation/spaces from the title, keep any English words as-is — see `../L01.Loop.Engineering/images/` for the convention, e.g. `双轴框架认知功能x执行拓扑.png`).
2. Create `{N}.{slide-title}.md` (N = next sequential number after the last existing note) following the exact structure of existing notes:
   - `# {Title}` (with full punctuation, e.g. `：` `×`)
   - `![{slide-title}](images/{slide-title}.png)`
   - `> {one-line takeaway from the slide}`
   - bullet list of the slide's key points
   - optional `## {subsection}` for diagrams/tables/sequences on the slide
   - closing bold one-liner takeaway
   - footer:
     ```
     ---
     *AI 编程范式转换 · SDD + Harness Engineering · 2026-04-14*
     *黄佳 · Agent 设计模式作者*
     ```
3. Update `README.md`'s numbered index with the new note.

Source PDF is `课件 第1节：AI编程范式转换.pdf` — use `pdftotext -f N -l N -layout` on the matching page to get exact wording instead of guessing from the image alone.

`资料包01/` holds the course's own handout materials (实操任务 md/pdf + SDD 强化训练) — reference material, don't renumber or restructure it.
