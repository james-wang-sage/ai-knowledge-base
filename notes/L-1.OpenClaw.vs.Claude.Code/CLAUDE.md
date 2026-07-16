# CLAUDE.md — notes/L-1.OpenClaw.vs.Claude.Code

When the user pastes a slide image in this directory's context, do the following without being asked again:

1. Save the image to `images/{slide-title}.png` (strip punctuation/spaces from the title, keep any English words as-is — see existing files for the convention, e.g. `双轴框架认知功能x执行拓扑.png`).
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
     *从 OpenClaw 到 Open Code · 拆解爆款 Agent 的设计密码与工程范式 · 2026-07-10*
     *黄佳 · 讲师*
     ```
3. Update `README.md`'s numbered index with the new note.

Source PDF is `OpenClaw vs Claude Code 设计密码与工程范式解析.pdf` — use `pdftotext -f N -l N -layout` on the matching page to get exact wording instead of guessing from the image alone.
