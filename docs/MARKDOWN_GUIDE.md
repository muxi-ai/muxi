# Writing documentation with human-readable markdown

## Overview


This guide explains how to write and maintain documentation using **plain markdown** that stays easy to read at source while supporting rich layouts and interactivity.

We use **GitHub-flavored markdown (GFM)** with a few lightweight, intuitive extensions for:

- ✅ Callouts
- ✅ Tabs
- ✅ Cards
- ✅ Columns
- ✅ Toggles
- ✅ Boxed sidebars
- ✅ Bookmarks
- ✅ Buttons
- ✅ Steps

Everything renders beautifully without breaking readability for humans or editors.

---

## Frontmatter

Each markdown file can include optional YAML frontmatter at the top to control metadata and behavior.

```yaml
---
title: Your Page Title
description: A brief description of this page
searchable: false
section: plugins
doc-type: doc
hide-toc: true
youtube-video: dQw4w9WgXcQ
---
```

### Standard fields

- **`title`** *(optional)*: The page title displayed in navigation and search results
- **`description`** *(optional)*: A summary shown in search results and metadata

### Custom fields

- **`searchable`** *(optional, defaults to `true`)*: Set to `false` to exclude this page from search indexing
- **`section`** *(optional)*: Categorizes the page for filtered search (e.g., `core`, `plugins`, `theme`)
- **`doc-type`** *(optional)*: Controls the page layout theme
  - `home` – for landing or overview pages
  - `doc` – for standard documentation pages
- **`hide-toc`** *(optional)*: Set to `true` to hide the "On this page" sidebar navigation
- **`youtube-video`** *(optional)*: YouTube video ID to embed a mini player in the sidebar (e.g., `dQw4w9WgXcQ`)

### Example

```yaml
---
title: Getting Started with Tabs
description: Learn how to create tabbed content in your documentation
section: core
doc-type: doc
youtube-video: abc123xyz
---
```

---

## 1. Callouts

GitHub-flavored callouts work natively using the `> [!TYPE]` pattern.

```md
> [!NOTE]
> This endpoint requires authentication via headers.
>
> Example: `TGX-API-KEY`
```

### Supported types

- `[!NOTE]` – default callout style
- `[!INFO]` – informational callout
- `[!TIP]` – helpful tips and suggestions
- `[!IMPORTANT]` – important information to note
- `[!WARNING]` – warnings about potential issues
- `[!CAUTION]` – cautionary notices
- `[!DANGER]` – critical warnings about dangerous actions

Each will render with its own visual style.

---

## 2. Tabs

Tabs group related content (for example, code snippets in multiple languages).

```md
[[tabs]]

[[tab python]]
‘‘‘python
print("Hello, World!")
‘‘‘
[[/tab]]

[[tab javascript]]
‘‘‘js
console.log("Hello, World!")
‘‘‘
[[/tab]]

[[/tabs]]
```

**Rules:**

- text after `"tab"` defines the tab’s title.
- Spaces are converted to `-` for the tab’s internal ID.
- If no label is provided, tabs default to `tab-1`, `tab-2`, etc.
- If **all tabs** contain only fenced code blocks, the container renders with
  `<div class="tab-group">`.

---

## 3. Cards

Cards highlight small pieces of information, previews, or summaries.

```md
(link/to/)[[card]]
#### Chat window
The main area where users talk to your bot.
[[/card]]
```

Each `[[card]]` block can include **any valid markdown** (headings, images, lists, etc.).

---

## 4. Columns

Columns group cards or blocks into a responsive grid layout.

```md
:::: cols=3

[[card]] card 1 [[/card]]
[[card]] card 2 [[/card]]
[[card]] card 3 [[/card]]

::::
```

Set `cols=2`, `cols=3`, etc. to control layout.

---

## 5. Toggles

Toggles render as collapsible `<details>` elements for optional or advanced content.

```md
[[toggle Advanced options]]
You can include **any content** here — text, lists, code, etc.
[[/toggle]]
```

Renders as:

```html
<details>
  <summary>Advanced options</summary>
  <p>You can include any content here...</p>
</details>
```

---

## 6. Boxed sidebars

Boxed sections float beside the main content for tips, reminders, or notes.

```md
[[boxed float-right]]
You can add diagrams, notes, or additional context here.
[[/boxed]]
```

### Float options
- `float-right` (default)
- `float-left`

Renders as:

```html
<div class="boxed float-right">
  <p>You can add diagrams, notes, or additional context here.</p>
</div>
```

---

## 7. Bookmarks

Bookmarks create a clean, icon-enhanced list of linked articles or related reading.

```md
[+] [test article 1]({base_url}/docs/some-article)
[+] [test article 2]({base_url}/docs/some-article)
[+] [test article 3]({base_url}/docs/some-article)
```

Each line renders with a small bookmark icon and the link text, for example:

📑 **test article 1**
📑 **test article 2**
📑 **test article 3**

---

## 8. Buttons

Buttons create styled call-to-action links.

```md
[>] [Get Started](quickstart.md)
[>>] [View Examples](examples/README.md)
```

### Button styles

- `[>]` – primary button (`class="btn"`)
- `[>>]` – secondary button (`class="btn-secondary"`)

Renders as:

```html
<a href="quickstart.md" class="btn">Get Started</a>
<a href="examples/README.md" class="btn-secondary">View Examples</a>
```

**Graceful degradation:** In standard markdown renderers (GitHub, VS Code), the prefix appears as text and the link remains clickable.

---

## 9. Steps

Steps create numbered, sequential instructions for tutorials and guides.

```md
[[steps]]

[[step Create your account]]

Visit our website and click the **Sign Up** button. Fill in your:

- Email address
- Password
- Display name

[[/step]]

[[step Verify your email]]

Check your inbox for a verification email. Click the link to confirm your email address.

> [!TIP]
> Check your spam folder if you don't see the email.

[[/step]]

[[step Complete your profile]]

Add more information to your profile:

1. Upload a profile picture
2. Add your bio
3. Set your preferences

[[/step]]

[[/steps]]
```

**Rules:**

- Steps are automatically numbered (Step 1, Step 2, etc.)
- Text after `[[step ...]]` becomes the step title (optional)
- Each step supports **full markdown** (lists, code blocks, callouts, etc.)
- Steps render as `<ul class="steps">` with `<li class="step">` items

Renders as:

```html
<ul class="steps">
  <li class="step">
    <h3 class="step-title">Step 1</h3>
    <p>Visit our website and click the <strong>Sign Up</strong> button...</p>
  </li>
  <li class="step">
    <h3 class="step-title">Step 2</h3>
    <p>Check your inbox...</p>
  </li>
</ul>
```

---

## Writing conventions

- Keep all custom tags (`[[...]]`, `::::`, etc.) **flush-left** — avoid indenting them inside lists or code.
- Inside custom blocks, you can write normal markdown (headings, lists, tables, etc.).
- Use blank lines between structural blocks for readability.
- Keep labels lowercase for consistency, but they’re case-insensitive.

---

## Example: combining multiple elements

```md
> [!TIP]
> You can combine **callouts** with cards, tabs, or toggles for richer docs.

[[tabs]]

[[tab label="python"]]
‘‘‘python
print("Hello")
‘‘‘
[[/tab]]

[[tab label="javascript"]]
‘‘‘js
console.log("Hello")
‘‘‘
[[/tab]]

[[/tabs]]

[[toggle More examples]]
:::: cols=2
[[card]]**Feature 1**: fast setup[[/card]]
[[card]]**Feature 2**: human-readable markdown[[/card]]
::::
[[/toggle]]
```

---

## Final notes

- These patterns are designed for **clarity and compatibility** — markdown remains human-readable in raw form.
- No build-time magic is required — all features can be parsed via a simple line-based preprocessor.
- Always preview rendered docs before committing to ensure structure looks right.
