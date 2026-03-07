---
name: blog-edit
description: Create and edit blog posts with spell check, grammar review, flow improvements, and validation. Use when adding new blog content or editing existing posts.
allowed-tools: Bash, Read, Edit, Write, Glob, Grep
---

# Blog Edit Skill

Complete workflow for creating or editing blog posts with quality assurance.

## Workflow

### 1. Create New Post (if applicable)

Run: `hugo new [content-type]/[post-name].md`

Content types:
- `technical/` - Technical writing on CS, AI, philosophy, biology
- `poetry/` - Poetry
- `stories/` - Short fiction
- `bookshelf/` - Book summaries and notes

### 2. Add/Edit Content

- Use Read tool to check current content
- Use Write or Edit tool to add/modify content
- For bookshelf posts, include: `author`, `amazonLink`, `description`, `tags`

### 3. Apply Improvements

**Spelling & Grammar:**
- Fix typos and misspellings
- Correct verb tense consistency
- Add missing articles (a, an, the)
- Fix punctuation and comma usage

**Flow & Clarity:**
- Simplify awkward phrases
- Improve sentence structure
- Remove redundancies
- Ensure parallel structure
- Replace archaic/unclear words with simpler alternatives
- Keep the original voice and vocabulary as much as possible

**Style preferences:**
- Keep writing crisp and to the point (CEO-style, not verbose)
- Use double quotes for TOML strings with apostrophes
- Add Wikipedia links for specialized concepts (not common terms)
- Use small link icons for external links (not buttons)
- No em dashes - keep it simple

### 4. Validate Build

ALWAYS run validation after creating/editing content:

```bash
# Kill server and clean build
pkill -9 hugo && rm -rf resources/ public/

# Validate build
hugo --quiet 2>&1
```

If errors appear:
- Fix TOML parsing errors (usually apostrophes in single-quoted strings)
- Fix template errors
- Rerun validation

After validation passes:
```bash
# Restart server
hugo server -D > /tmp/hugo.log 2>&1 &
```

### 5. Show Changes to User

Run these commands in parallel:
```bash
git status
git diff
```

Present the changes and ask user if ready to commit.

### 6. Commit (after user approval)

```bash
git add .
git commit -m "Descriptive message with bullet points"
```

**IMPORTANT:**
- Do NOT include "Co-Authored-By" line (user preference)
- Do NOT push yet

### 7. Push to GitHub (only after explicit user approval)

1. Remind user: "This will publish to https://viswabalaji.github.io"
2. Ask: "Ready to push to GitHub and deploy?"
3. If yes, run: `git push`
4. Check deployment: `gh run list --limit 1`

## Important Notes

- Changes are visible immediately at http://localhost:1313 after validation
- Always validate Hugo build before committing
- Common TOML errors: apostrophes in single-quoted strings - use double quotes
- Browser cache: tell user to hard refresh (Cmd+Shift+R) if changes don't appear
- Never push to GitHub without explicit user approval
- Never skip the validation step
- Hugo server restarts automatically - don't ask permission for restarts
