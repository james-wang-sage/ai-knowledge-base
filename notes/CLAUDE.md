# CLAUDE.md — notes/

Each subfolder here (e.g. `L01.Loop.Engineering/`, `01.AI编程范式转换/`) is a course-notes directory containing numbered slide notes (`{N}.{slide-title}.md`), an `images/` folder, a source PDF, its own `CLAUDE.md`, `AGENTS.md` (symlink to `CLAUDE.md`), and `README.md` (index).

## When asked to "init" a subfolder

Do these steps for the target subfolder:

1. **Create `CLAUDE.md`** modeled on `L01.Loop.Engineering/CLAUDE.md` — same structure, adapted to the folder:
   - Title: `# CLAUDE.md — notes/{subfolder}`
   - Slide-image workflow: save pasted images to `images/{slide-title}.png` (strip punctuation/spaces from title, keep English as-is), create `{N}.{slide-title}.md` (next sequential number) with the note structure (`# {Title}` → image → `> takeaway` → bullets → optional `##` subsections → closing bold one-liner → footer), then update `README.md`'s index.
   - Footer line: use this course's name, date, and lecturer (look at the folder's PDF/existing notes; ask if unknown).
   - Source PDF line: name the folder's PDF and the `pdftotext -f N -l N -layout` tip.
2. **Create `AGENTS.md`** as a relative symlink: `ln -s CLAUDE.md AGENTS.md` (run inside the subfolder).
3. **Create `README.md`** as the index: `# {course title}`, one intro line naming the source PDF, then a numbered list linking every existing `{N}.*.md` note (empty list if none yet). Add a `## 相关资源` section only if there are known links.

If the subfolder already has some of these files, fill in only what's missing — don't overwrite existing content.
