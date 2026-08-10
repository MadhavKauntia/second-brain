---
name: promote
description: Promote an article from this repo's private folder to the madhavkauntia.com blog by copying it as-is. Use when the user wants to promote, publish, or push a private draft to their blog.
argument-hint: "<article filename or title to promote>"
disable-model-invocation: true
---

# Promote to Blog

Copy an article **as-is** (byte-for-byte, no edits to the content) from this repo's private drafts into the madhavkauntia.com blog content folder.

- **Source:** `content/private/` (relative to this repo, `/Users/headout/Desktop/personal/second-brain`)
- **Destination:** `/Users/headout/Desktop/personal/madhavkauntia.com/content/blog/`

## Process

### 1. Identify the article

- If the user passed an argument, match it against files in `content/private/`. Accept either a filename (`birthdays.md`, or `birthdays`) or a rough title.
- If no argument was passed, or the match is ambiguous, list the `.md` files in `content/private/` and ask which one to promote. Do not guess when more than one file plausibly matches.

### 2. Confirm the destination is clear

- Check whether a file with the same name already exists in the destination.
- If it does, show the user that it exists and confirm before overwriting.

### 3. Copy as-is

- Copy the file verbatim to the destination with the **same filename**. Do not reformat, rewrite, proofread, or alter the frontmatter or body in any way — this skill promotes, it does not edit.
- Use `cp` so the copy is exact:

  ```bash
  cp "/Users/headout/Desktop/personal/second-brain/content/private/<file>.md" \
     "/Users/headout/Desktop/personal/madhavkauntia.com/content/blog/<file>.md"
  ```

### 4. Verify the copy

- Confirm the source path, destination path, and that the copy is byte-for-byte identical (e.g. by comparing with `diff` or `cmp`, which should report no differences).

### 5. Commit and push the blog repo so the article goes live

The blog lives in a separate repo (`/Users/headout/Desktop/personal/madhavkauntia.com`) that publishes from `main`. Commit only the promoted article and push it:

```bash
cd /Users/headout/Desktop/personal/madhavkauntia.com
git add "content/blog/<file>.md"
git commit -m "Add <title> to blog"
git push origin main
```

- Stage only the newly promoted file — do not sweep up unrelated changes with `git add -A`. If the working tree has other uncommitted changes, mention them but leave them alone.
- Use a short, imperative commit message that matches the repo's existing style (e.g. `Add "Why I Hate My Birthday" to blog`). Derive `<title>` from the article's frontmatter `title:`.
- End the commit message with the required trailer:

  ```
  Co-Authored-By: Claude Opus 4.8 (1M context) <noreply@anthropic.com>
  ```

### 6. Report

- Report the commit hash and confirm the push succeeded, so the user knows the article is live (or building/deploying).
