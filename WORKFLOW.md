# Blog Workflow Guide

## Important Rules

**NEVER push to GitHub without explicit user approval.**

## Local Development Workflow

### 1. Start Local Server

To preview changes locally:
```bash
hugo server -D
```

This starts a development server at `http://localhost:1313` with:
- Live reload (changes appear immediately)
- Draft posts visible (with `-D` flag)
- Fast incremental builds

### 2. Make Changes

Create or edit content:
```bash
# Technical writing
hugo new technical/my-post.md

# Poetry
hugo new poetry/my-poem.md

# Stories
hugo new stories/my-story.md
```

Edit the files in your text editor, save, and see changes instantly in the browser.

### 3. Review Changes

Check what changed:
```bash
git status
git diff
```

Build the production site:
```bash
hugo
```

### 4. Deploy to GitHub (Only After Approval)

**DO NOT run these commands without explicit user approval:**
```bash
git add .
git commit -m "Your commit message"
git push
```

## Quick Commands

```bash
# Start local preview
hugo server

# Start with drafts visible
hugo server -D

# Build production site
hugo

# Check what will be committed
git status
git diff

# View local changes before committing
git diff --cached
```

## File Structure

```
content/
├── technical/     # Technical writing
├── poetry/        # Poems
├── stories/       # Short stories
└── about.md       # About page
```

## Notes

- All local changes are visible immediately with `hugo server`
- The `-D` flag shows draft posts (draft = true)
- Production builds ignore drafts unless `draft = false`
- GitHub deployment happens automatically ONLY after git push
