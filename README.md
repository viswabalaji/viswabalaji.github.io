# Personal Blog

A Hugo-based blog with technical writing, poetry, and short stories.

Live site: https://viswabalaji.github.io

## Local Development

### Start the local server
```bash
hugo server -D
```

Then open http://localhost:1313 in your browser.

Changes you make to files will automatically reload in the browser.

### Create new content
```bash
# Technical post
hugo new technical/my-post.md

# Poem
hugo new poetry/my-poem.md

# Story
hugo new stories/my-story.md
```

### Build for production
```bash
hugo
```

This creates static files in the `public/` directory.

## Deployment

The site deploys automatically via GitHub Actions when you push to the `main` branch.

**Note:** A pre-push hook will ask for confirmation before pushing to prevent accidental deployments.

### Deploy workflow
1. Make changes locally
2. Preview with `hugo server`
3. Review with `git status` and `git diff`
4. Commit: `git add . && git commit -m "Your message"`
5. Push (requires confirmation): `git push`

## Structure

- `content/technical/` - Technical writing
- `content/poetry/` - Poems
- `content/stories/` - Short stories
- `content/about.md` - About page
- `hugo.toml` - Site configuration
- `themes/PaperMod/` - Theme (don't edit directly)

## Configuration

Edit `hugo.toml` to customize:
- Site title and description
- Menu items
- Social links
- Theme settings

See [WORKFLOW.md](WORKFLOW.md) for detailed workflow instructions.
