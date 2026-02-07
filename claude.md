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
```

After creating, use Write or Edit tool to add the content based on user's request.

### Editing Content

1. Use Read tool to check current content
2. Use Edit or Write tool to make changes
3. Inform user changes are visible immediately at localhost:1313
4. Do NOT commit or push yet

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
git commit -m "Descriptive message

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"
```

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
└── about.md      # About page

hugo.toml         # Site configuration
themes/PaperMod/  # Theme (don't edit)
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

## Never Do

- Don't push without asking
- Don't commit without showing changes first
- Don't use bash for file operations when Write/Edit tools exist
- Don't make assumptions about deployment timing
- Don't skip the local preview step
