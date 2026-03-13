# Instructions for Claude Code

## Critical Rules

1. **NEVER push to GitHub without explicit user approval**
2. **ALWAYS preview changes locally first**
3. **ALWAYS show git diff before committing**
4. **ALWAYS ask for confirmation before running `git push`**

## Workflow for Managing This Blog

### Starting a Session

When the user wants to work on the blog:

1. Check if hugo server is running: `ps aux | grep hugo`
2. If not running, start it: `hugo server -D` (run in background)
3. Inform user the site is available at http://localhost:1313

### Creating Content

When user wants to create new content:

```bash
# Technical post
hugo new technical/post-name.md

# Poetry
hugo new poetry/poem-name.md

# Stories
hugo new stories/story-name.md

# Bookshelf (book notes)
hugo new bookshelf/book-title.md
```

After creating, use Write or Edit tool to add the content based on user's request.

**For bookshelf posts**, include these frontmatter fields:
- `author` - Book author (displays below title in italics)
- `amazonLink` - Amazon URL (displays as small link icon next to author)
- `description` - Brief summary
- `tags` - Related topics

**Style preferences for bookshelf:**
- Keep writing crisp, to the point, CEO-style (no verbose ornate writing)
- Add Wikipedia links for specialized concepts/people (not common terms)
- Use small link icons, not buttons, for external links

**Style preferences for technical articles:**
- Write from direct experience perspective for senior engineer voice
- Use full sentences, avoid sound bite style (short sentence fragments)
- Avoid "not X, it's Y" or "X, not Y" patterns - write naturally
- Remove redundant information that doesn't add new value
- Avoid LLM-like phrasing and overly formal language
- No em dashes - keep it simple

**IMPORTANT - Validate after creating/editing content:**

Hugo supports hot reload - content changes are automatically detected. After creating or editing content files, validate the build:

```bash
# Test build to catch errors (server keeps running)
hugo --quiet 2>&1
```

If this shows any errors (TOML parsing, template errors, etc.), fix them before proceeding.

**Common TOML frontmatter issues:**
- Apostrophes in single-quoted strings: Use double quotes for strings with apostrophes
- Example: `title = "It's Working"` not `title = 'It's Working'`
- Unescaped special characters in strings
- Missing closing quotes or brackets

**When to restart hugo server:**
- Only restart if hot reload fails or you need to clear cache
- SASS/CSS changes may require restart: `pkill -9 hugo && hugo server -D > /tmp/hugo.log 2>&1 &`
- New frontmatter fields may require restart
- For cache issues: `rm -rf resources/ public/` then restart

### Editing Content

1. Use Read tool to check current content
2. Use Edit or Write tool to make changes
3. Inform user changes are visible immediately at localhost:1313
4. Do NOT commit or push yet

**Iterative editing process for technical articles:**
- Make multiple editing passes
- First pass: structure and content
- Second pass: remove redundancies and check for repeated information
- Third pass: tighten prose and fix style issues
- Validate build with `hugo --quiet 2>&1` after each major change

### Integrating External References

When adding references to articles:

1. Use WebFetch to understand external sources before referencing
2. Integrate references naturally into article body with context and technical depth
3. Don't just cite - add value by explaining what the reference contributes
4. Add "## References" section at bottom with format: `[Title](url) - Source`
5. Example:
   ```markdown
   ## References

   - [Article Title](https://example.com/article) - Source Name
   - [Another Article](https://example.com/other) - Other Source
   ```

### Reviewing Changes

Before any commit:

```bash
# Show what changed
git status

# Show detailed diff
git diff

# If files are staged, show staged diff
git diff --cached
```

Present the changes to the user and ask if they want to commit.

### Committing Changes

Only after user approves the changes:

```bash
git add .
git commit -m "Descriptive message with bullet points of key changes"
```

**DO NOT include "Co-Authored-By" line - user preference.**

**DO NOT push yet.**

### Deploying to GitHub

Only after explicit user approval to deploy:

1. Remind user this will publish to https://viswabalaji.github.io
2. Ask: "Ready to push to GitHub and deploy?"
3. If yes, run: `git push`
4. The pre-push hook will ask for confirmation again
5. Monitor the deployment: `gh run list --limit 1`

### Common Tasks

**Preview locally:**
- Remind user: "View at http://localhost:1313"
- No git operations needed

**Iterate on content:**
- Edit files as needed
- Changes appear instantly in browser
- No commits until user is satisfied

**Check deployment status:**
```bash
gh run list --limit 1
```

**View live site:**
```bash
curl -sI https://viswabalaji.github.io/
```

## File Structure

```
content/
├── technical/    # Technical writing on CS, AI, philosophy, biology
├── poetry/       # Poetry
├── stories/      # Short fiction
├── bookshelf/    # Book summaries and notes
└── about.md      # About page

hugo.toml                        # Site configuration
layouts/_default/single.html     # Custom post layout (with TOC sidebar)
layouts/partials/toc-scroll-spy.html  # TOC scroll highlighting
assets/sass/_custom.scss         # Custom styles
themes/hugo-blog-awesome/        # Theme (don't edit directly)
```

## Important Notes

- The hugo server auto-reloads on file changes
- Draft posts (draft = true) are visible with `hugo server -D`
- Production builds ignore drafts
- GitHub Actions deploys automatically on push to main
- Pre-push hook will prompt for confirmation
- Always use Write/Edit tools for content, not bash echo/cat

## Example Session Flow

1. User: "Add a new technical post about neural networks"
2. Claude: Run `hugo new technical/neural-networks.md`
3. Claude: Write content to the file
4. Claude: "I've created the post. View it at http://localhost:1313/technical/neural-networks/"
5. User: "Change the title"
6. Claude: Edit the file
7. Claude: "Updated. Refresh your browser to see changes."
8. User: "Looks good, let's publish"
9. Claude: Run `git status` and `git diff`
10. Claude: Show changes and ask "Ready to commit and push?"
11. User: "Yes"
12. Claude: Commit and push (after final confirmation)

## Common Issues & Solutions

### Hugo Server & Caching

- **Hugo doesn't pick up frontmatter changes**: Restart the server with `pkill -9 hugo && hugo server -D`
- **CSS changes not compiling**: Clear cache with `rm -rf resources/ public/` then restart
- **Browser not showing changes**: This is almost always browser cache. Tell user to hard refresh (Cmd+Shift+R) or use incognito
- **Old URLs still accessible after file rename**: Full cache clear needed: `pkill -9 hugo && rm -rf resources/ public/ && hugo server -D > /tmp/hugo.log 2>&1 &`
- **SASS calc() errors**: Complex calc expressions may fail in SASS. Simplify or use plain values

### Hugo Template Context

- Use `.Params.field` not `$.Params.field` when inside `{{ if .Params.something }}` blocks
- Use `{{ if .Params.field }}` not `{{ with .Params.field }}` for conditional rendering with multiple fields
- Hugo server needs restart after adding new frontmatter fields to content files

### CSS & Layout

- When adding sidebars, use `position: fixed` with media queries, not grid layouts that split main content
- Check compiled CSS at `/style.min.[hash].css` to verify styles are actually present
- TOC styling: Target `details.toc` not just `.toc` - it's a details element
- Always add dark mode styles when adding new components

### Table of Contents

- TOC requires `[markup.tableOfContents]` config in hugo.toml
- Theme renders TOC via `{{ partial "toc.html" . }}` - must be explicitly included in custom layouts
- TOC scroll spy requires JavaScript for highlighting active sections
- Position TOC sidebar with `position: fixed` and show only on wide screens (>1400px)

## Never Do

- Don't push without asking
- Don't commit without showing changes first
- Don't use bash for file operations when Write/Edit tools exist
- Don't make assumptions about deployment timing
- Don't skip the local preview step
- Don't assume changes are visible - always account for browser/Hugo caching
- Don't use complex calc() expressions in SASS that might fail compilation
- Don't add "Co-Authored-By" lines to commits
- Don't use buttons for links - prefer small icons for external references
- Don't use em dashes in writing - keep it simple
- Don't use sound bite style or "not X, it's Y" patterns - write full sentences
- Don't repeat information that doesn't add new value
