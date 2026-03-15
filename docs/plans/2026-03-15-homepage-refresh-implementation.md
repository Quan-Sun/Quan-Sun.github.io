# Homepage Refresh Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Refresh the personal homepage to improve branding, information hierarchy, and metadata while preserving the current minimalist dark theme.

**Architecture:** Keep the site as a lightweight single-page static website. Extend `index.html` with semantic sections, richer hero content, and stronger metadata, and add a standalone SVG favicon for browser/tab identity.

**Tech Stack:** Plain HTML, inline CSS, SVG, GitHub Pages

---

### Task 1: Define validation targets

**Files:**
- Modify: `index.html`

**Step 1: Write the failing test**

Use content checks for the missing sections and metadata:

```bash
grep -q 'About' index.html
grep -q 'Featured Work' index.html
grep -q 'meta name="description"' index.html
grep -q 'property="og:title"' index.html
```

**Step 2: Run test to verify it fails**

Run: `grep -q 'About' index.html || echo missing`

Expected: the checks report the targets as missing before implementation.

**Step 3: Write minimal implementation**

Add new sections, hero enhancements, and metadata directly in `index.html`.

**Step 4: Run test to verify it passes**

Run the same `grep` commands after the patch.

Expected: all checks succeed.

### Task 2: Add site identity asset

**Files:**
- Create: `favicon.svg`

**Step 1: Write the failing test**

Check that the asset does not exist yet:

```bash
test -f favicon.svg
```

**Step 2: Run test to verify it fails**

Run: `test -f favicon.svg || echo missing`

Expected: the file is absent before implementation.

**Step 3: Write minimal implementation**

Create a compact SVG favicon matching the homepage color palette.

**Step 4: Run test to verify it passes**

Run: `test -f favicon.svg && echo present`

Expected: the favicon exists.

### Task 3: Verify the static page

**Files:**
- Modify: `index.html`
- Create: `favicon.svg`

**Step 1: Write the failing test**

Use repository hygiene checks:

```bash
git diff --check
```

**Step 2: Run test to verify it fails**

Run before the final cleanup if there are whitespace or patch issues.

Expected: no trailing whitespace or malformed diff markers remain.

**Step 3: Write minimal implementation**

Resolve any formatting or patch errors, then run focused content checks.

**Step 4: Run test to verify it passes**

Run: `git diff --check && grep -q 'Featured Work' index.html && grep -q 'rel="icon"' index.html`

Expected: the static page passes the focused validation checks.
