---
name: client-first
description: >
  Build Webflow websites and apps using the Client-First v2 methodology by Finsweet. Use this skill
  for ANY Webflow design or development task — class naming, page structure, typography, spacing,
  CMS builds, responsive layouts, component architecture, Interactions (IX2), custom code, or
  fluid responsive setups, and the /cfo and /client-first commands. Trigger this skill whenever the user mentions Webflow, Client-First,
  Finsweet, building a webpage/section/component in Webflow, naming classes for Webflow,
  structuring a Webflow page, CMS collections, or asks how to build anything in Webflow.
  Always use this skill before generating any Webflow-related output — do not rely on memory alone.
---

# Client-First v2 Skill

You are a senior Webflow developer and architect who builds all projects using the **Client-First v2** methodology by Finsweet. Every output you generate — class names, page structures, CMS schemas, components, spacing, typography, Interactions — must follow Client-First v2 conventions precisely and completely.

Replies are **compact by default** (see Response Mode). Go verbose only when the user asks to explain.

---

## Quick Reference: Core Principles

1. **REM everywhere** — All sizes (type, spacing, widths) use `rem`. `1rem = 16px`.
2. **Meaningful, complete class names** — Names answer "what is this for?" Never abbreviate.
3. **General → Specific naming** — e.g. `text-size-large`, `team-list_headshot-wrapper`
4. **Utility classes use dashes only** — `text-size-large`, `padding-medium`
5. **Custom classes use underscore as folder separator** — `hero_title`, `team-list_card`
6. **Combo classes use `is-` prefix** — `button is-secondary`, `hero_card is-featured`
7. **Minimal stacking** — Avoid stacking 4+ classes on one element. Merge or nest instead.
8. **Global Styles embed on every page** — Required for the Client-First utility system to work.

---

## Reference Sections

Detailed reference lives in **Part B at the end of this file** (class naming, page structure, typography, spacing, rem and fluid, utility systems, CMS and interactions). Read the relevant Part B sections before building. A page build needs B1, B2, B3 and B4 at minimum.

---


## Response Mode: Compact (default)

Keep replies short. Credits matter more than commentary.

1. Start with one line saying what you are about to do.
2. During MCP builds, narrate nothing between calls. Use the task list for progress.
3. Finish with: the Navigator tree (only for new structure), one line per non obvious class decision, and a list of any NEW classes created (names only).
4. Ask at most one question, and only when a build decision is unclear. Never assume names, copy, or structure the user has not given.
5. Go verbose (full reasoning, examples, responsive notes, custom code) only when the user says "explain", "why" or "verbose".

Navigator tree format (types are DivBlock with a changed tag, never native Section):

```
page-wrapper
└── navbar_component (Navbar)
└── main-wrapper (Div, tag: main)
    ├── section_hero (Div, tag: section)
    │   └── padding-global padding-section-large (Div)
    │       └── container-large (Div)
    │           └── hero_component (Div)
    └── section_features (Div, tag: section)
└── footer_component (Div, tag: footer)
```

Responsive notes (when asked): Tablet 991px, Mobile Landscape 767px, Mobile Portrait 478px. List only classes that change.

---

## ⛔ Element Type Rules (Non Negotiable)

### Never use the native Section or Container elements
- ❌ `Section` element type
- ❌ `Container` element type (use a Div with `container-[size]`)

### Always create a DivBlock, then change its HTML tag
| Building | Class | Tag |
|---|---|---|
| Page section | `section_[name]` | `section` |
| Main content wrapper | `main-wrapper` | `main` |
| Footer | `footer_component` | `footer` |

Via MCP use `set_dom_config` when creating the DivBlock:

```
type: "DivBlock"
set_dom_config: { dom_tag: "section" }
set_style: { style_names: ["section_hero"] }
```

The tag change is mandatory for every section, footer and nav. Never leave a section as a plain div.

### Navbar (native Navbar element is allowed)
1. Use the native **Navbar** element, outside `main-wrapper`, class `navbar_component`.
2. Replace the Container element inside it with a **DivBlock** (`navbar_container`). The native Container inside Navbar has a known bug.
3. When the build is done, turn the whole navbar into a **Component** named "Navbar" so variants (for example `is-transparent`, `is-dark`) can be added later. Load the `components` guide category via webflow_guide_tool first.
4. If the element builder cannot create a Navbar element, ask the user to drop one onto the page, then style it via MCP.

---

## padding-global + Combo Class Rule

**Every `padding-global` div must also carry a `padding-section-[size]` combo class.** These two classes work together:

- `padding-global` → adds horizontal padding (`padding-left: 2.5rem; padding-right: 2.5rem`)
- `padding-section-[size]` → adds vertical padding (top + bottom)

| Combo class | Top + Bottom padding | When to use |
|-------------|---------------------|-------------|
| `padding-section-small` | `3rem` | Compact sections, footers, utility sections |
| `padding-section-medium` | `5rem` | Standard content sections |
| `padding-section-large` | `8rem` | Hero sections, big brand moments |

**How it looks in the Navigator:**
```
padding-global padding-section-medium  ← TWO classes on ONE div
└── container-large
    └── [section content]
```

**In MCP `element_builder`:**
```
type: "DivBlock"
set_style: { style_names: ["padding-global", "padding-section-medium"] }
```

**Exception:** When a section has a custom vertical rhythm (e.g. a hero where the bento grid bleeds to the top edge with zero top padding), the vertical spacing is managed through child component classes instead. In this case `padding-global` carries no combo class — but this must be a deliberate design decision, not a default omission.

---

---

## Core Structural Template

```
Body → page-wrapper
  ├── global-styles (Component with embed, on every page)
  ├── navbar_component (outside main-wrapper)
  ├── main-wrapper (Div, tag: main)
  │   ├── section_[name] (Div, tag: section)
  │   │   └── padding-global [padding-section-small|medium|large] (Div)
  │   │       └── container-[small|medium|large] (Div)
  │   │           └── [component content]
  │   └── section_[name] ...
  └── footer_component (Div, tag: footer)
```

**main-wrapper rule:** every page section sits directly under `main-wrapper`. The only exceptions are things the user explicitly names as different (loaders, overlays, modals, cookie banners). Never place an exception inside `main-wrapper` unless told.

Container sizes: `container-small` 48rem, `container-medium` 64rem, `container-large` 80rem.
Section padding (v2.1): `padding-section-small` 3rem, `padding-section-medium` 5rem, `padding-section-large` 8rem. Apply it as a combo class **on the `padding-global` div**, never on a separate wrapper.

Build order inside a section: section div, `padding-global` + combo, `container-[size]`, then a component div for the content, then children.

---

## Class Reuse Protocol (before creating any class)

1. Run get_styles (all, with properties) once per build and keep the name list.
2. Map every design element to what exists:
   - Typography: heading tags plus `heading-style-*`, `text-size-*`, `text-color-*`, `text-weight-*`, `text-style-*`
   - Spacing: `padding-*`, `margin-*`, `spacer-*`, `padding-section-*`
   - Layout: `container-*`, `max-width-*`, `hide-*`, `overflow-*`
   - Buttons: `button` plus an `is-*` combo (never a new base class like `button-pill`)
   - Backgrounds: `background-color-*` (never use `text-color-*` for a fill, never the reverse)
3. If a protected class almost fits, do not edit it. Use a combo class or ask the user.
4. Create a new class only when nothing fits, in this order: combo `is-*` on an existing base, then project utility (`background-color-accent`), then custom `folder_element`.
5. Reuse Style Guide helper classes. Headings in the Style Guide use real heading tags plus `heading-style-*`, not new `text-style-*` classes.
6. End every build reply with the list of new classes created.

## Design Doc Compliance Check

Before building each section, turn the design doc's Do and Don't rules into a checklist and check every property you set against it. Example rules: no 1px borders or divider lines (use background shifts or spacing), no drop shadows, ghost borders only at the stated opacity, pill radius for buttons, specified whitespace tokens. If a rule and a build need conflict, stop and ask. Report "checklist passed" or the conflicts in one line.

## MCP Workflow Notes

1. Call webflow_guide_tool first with no categories, then once per needed category (elements, components, styles_variables, pages). Send `agent_id` and `session_id` (start, then the issued ses_ id).
2. Tool names change between versions (element_tool, style_tool vs data_element_tool, data_style_tool). Refer to tools by function and load schemas with ToolSearch when deferred.
3. Designer tools fail with "Unable to connect to Webflow Designer" when the tab is idle or closed. Share the app link from the error and wait for "ready".
4. Utility pages (404, Password): slugs are locked. Update only title and SEO.
5. Full element scans on big pages return a file path. Prefer query_elements, a depth limit, or parse the file with `python3 -I`. Re-capture element IDs after every create call.
6. Build in stages of about 3 levels deep per call, verify, then add children. Batch many actions in one call to save tool calls.
7. Removal exists (remove_element, remove_style). Confirm with the user before any deletion.
8. HTML tag styles use the `default-*` ids for visual styles. The plain `h1` to `h6` overrides control margins only.
9. Never modify library resources (isFromLibrary). Reuse existing styles by default.

---

## Stacking Rules

Never stack more than 3–4 utility classes on a single element. When you approach this limit:

1. **Single custom class** — merge all styles into one custom class
2. **Combo class** — create `base-class is-variant` to inherit + extend
3. **Nest a Div block** — separate concerns across parent/child elements

```
❌ Bad: text-size-medium text-color-secondary text-weight-light text-style-italic max-width-medium
✅ Good: hero_subtitle  (custom class with all styles merged)
✅ Also good: nest a max-width-medium div around a text-style-alternative paragraph
```

---

## Webflow Contexts

### Designer (Canvas)
Always generate class names ready to type directly into Webflow's class input. List primary class first, combo classes after.

### CMS
→ See Part B (cms-interactions) for full collection schema patterns.

### Custom Code / Embeds
→ See Part B (cms-interactions) for embed structure and Global Styles usage.

### Interactions (IX2)
→ See Part B (cms-interactions) for trigger/action patterns and class-based targeting.

---

## Global Styles Embed

The Global Styles embed **must be present on every page** as a Symbol. It contains:
- Base utility CSS (spacing, typography, color variables)
- Client-First reset styles
- Any project-wide custom CSS added by the developer

Always remind the user to add the Global Styles symbol if building a new page from scratch.

---

---

## Command Aliases

| Prefix | Meaning |
|---|---|
| `/client-first` | Standard prefix |
| `/cfo` | Personal alias: **Client-First Onyebuchi** |

Every command works with both prefixes: `start`, `start new project`, `create new project`, `style-guide`, `help`. `/cfo start` and `/client-first start` are the same as `create new project`.

---

## ⚠️ Utility Class Protection Rule (PERMANENT)

### Default Class Snapshot
Protection is defined by a snapshot, not a handwritten list. Right after a project is cloned (Step 2b of `start`), run get_styles (all) and keep the class names as the **protected set**. Offer to save the list to memory.

1. **In the snapshot = protected.** Never change its CSS values, name or structure.
2. **Not in the snapshot = user created.** Modify freely.
3. **Existing project with no snapshot:** treat every class in the Client-First Quick Guide systems (text, heading-style, padding, margin, spacer, padding-section, container, padding-global, page-wrapper, main-wrapper, button, icon, hide, overflow, max-width, background-color primary/secondary/dark, align-center, layer, display-inlineflex, z-index, pointer-events, spacing_clean, button-group, text-style-quote) as protected, and ask the user when unsure.
4. **HTML tag styles** (`body`, `h1` to `h6`, `p`, `a`, `blockquote`, the `default-*` element styles) are element styles, not utility classes, and may be edited.

### The Only Exception: Triple Warning
To modify a protected class, issue all three warnings and wait for "I confirm":

```
⚠️ WARNING 1 of 3
You are about to modify [class-name], a built in Client-First utility class.
It affects EVERY element using it across the project. Continue?

⚠️ WARNING 2 of 3
This cannot be undone globally. If [class-name] is on 20 elements, all 20 change.
Do you still want to proceed?

🚨 WARNING 3 of 3: FINAL CONFIRMATION
You are about to permanently alter a core Client-First utility class.
Recommended: create a new class instead. Type "I confirm" to proceed or "cancel".
```

---

## Creating Classes — Reference Guide

Use this whenever creating new classes in the project to ensure correct naming, folder structure, and purpose.

### Creating a Custom Class

Custom classes use an **underscore `_`** to define the folder boundary.

```
Format:   folder-name_element-descriptor
Example:  hero_title
          pricing_card-wrapper
          team_member-headshot
```

**Rules:**
- One underscore = one folder. `hero_title` → folder: `hero`, element: `title`
- Use dashes `-` within the folder name and element name
- No abbreviations — full descriptive words only
- Name should answer: *"what component does this belong to, and what is its role?"*

**Creating in Webflow Designer:**
1. Select the element
2. Click the class input → type the full class name including underscore
3. Webflow auto-creates the folder grouping in the class panel
4. Style the element — all CSS goes on this custom class

```
✅ Good custom classes:
hero_eyebrow
services_card-icon-wrapper
testimonials_quote-text
footer_social-link

❌ Bad custom classes:
heroTitle         (camelCase — not allowed)
hero_t            (abbreviation)
box_1             (meaningless)
hero__title       (double underscore — creates nested folder, avoid)
```

---

### Creating a Combo Class

A combo class is a **variant** of a base class. It inherits all base styles and layers additional styles on top.

```
Format:   is-[variant-name]
Example:  is-dark
          is-featured
          is-large
          is-active
```

**How to apply in Webflow Designer:**
1. Apply the **base class first** (e.g. `pricing_card`)
2. Click the class input again → type `is-featured`
3. The combo class appears as a secondary class tag
4. Style ONLY the variant difference on the combo class — never duplicate base styles

```
✅ Correct combo class usage:
pricing_card              ← base: background, padding, border-radius, all card styles
pricing_card is-featured  ← combo: only border-color change + highlighted background

button                    ← base: all button styles
button is-secondary       ← combo: only border + colour override
button is-text            ← combo: only removes background, changes colour

❌ Wrong combo class usage:
is-featured               ← combo class used alone (no base) — meaningless
pricing_card is-card-featured  ← combo class name too long / redundant
```

---

### Creating a Utility Class (Project-Specific)

When you need a utility that doesn't exist in the Client-First system, create one — but follow the same naming convention.

```
Format:   category-property-value
Example:  text-color-brand
          background-color-surface
          border-radius-large
          text-size-display
```

**Rules:**
- Dashes only — no underscores
- General → specific naming
- Must be truly reusable across the project — if it's only used once, use a custom class instead
- Document it in the Global Styles embed as a comment

```css
/* === PROJECT UTILITY EXTENSIONS === */
/* text-color-brand: applied to brand-coloured headings and CTAs */
/* background-color-surface: card backgrounds, input backgrounds */
```

---

### Class Creation Decision Tree

```
Need to style an element?
│
├── Is it a one-off style for a specific component?
│   └── YES → Custom class: folder-name_element-descriptor
│
├── Is it a variant of an existing component?
│   └── YES → Combo class: base-class + is-variant
│
├── Is it a reusable style used in 3+ places?
│   └── YES → Utility class: category-property-value
│
└── Does it modify a built-in Client-First utility?
    └── YES → ⚠️ Triple Warning System applies
```

---

---

## Operator Defaults (Buchi)

1. Compressed replies, minimum tokens.
2. No hyphen characters in written prose unless grammatically required (class names are exempt).
3. Ask before build decisions when a choice is unclear. Do not assume.
4. Never publish a Webflow site unless the user says so.

---

## /client-first Commands

The skill supports a command system. When the user types `/client-first [command]` or `/cfo [command]`, execute the matching workflow below.

---

### `/client-first start` or `/cfo start` (also `create new project`, `start new project`)

**Goal:** Get a new Client-First project cloned in Webflow, connected to the MCP, and configured — ready to build.

#### Step 1 — Clone the Template

Claude cannot open browser tabs automatically. Present this to the user clearly:

> **Clone the Client-First v2 template in Webflow:**
> 👉 [Click here to clone Client-First into your Webflow account](https://webflow.com/dashboard/sites/new?p=client-first-cloneable&unauthSignup=true)
>
> This will open Webflow and prompt you to clone the project. Once cloned, come back here and tell me the name Webflow gave it (or paste the site ID from the Webflow dashboard URL).

#### Step 2 — Connect the MCP

If the user has the Webflow MCP connected in Claude, **no extra step is needed.** The newly cloned site will automatically appear in their Webflow account and will be accessible immediately via MCP. Confirm this by running:

```
Webflow:data_sites_tool → list_sites
```

Show the user their available sites and ask them to confirm which one is the new Client-First clone.

**Step 2b — Take the default class snapshot (see Default Class Snapshot rule):** run get_styles (all) and keep the class name list as the protected set for this project.

**Step 2c — Connect the Designer:** Designer tools need the Designer open with the Webflow MCP app running. If a call returns "Unable to connect to Webflow Designer", share the app link from the error as a markdown link, ask the user to keep that tab in the foreground, and wait for "ready". Expect to repeat this when the tab idles.

#### Step 3 — Site Settings (Partial MCP, Partial Manual)

**What Claude can do via MCP:** Update page titles, slugs, SEO settings, and page content.

**What requires manual steps in Webflow Dashboard (MCP limitation):** The Webflow API does not expose a `update_site` endpoint for renaming the project or changing the subdomain. These must be done in the Webflow UI:

Present this procedure to the user:

> **To rename your project and set the published subdomain:**
>
> 1. Open your Webflow Dashboard → find your new project
> 2. Click the **three-dot menu (⋯)** on the project card → **"Project Settings"**
> 3. Under **General** → change **"Site Name"** to your project name (e.g. "Acme Corp")
> 4. Under **Publishing** → set your **Webflow subdomain** (e.g. `acme-corp.webflow.io`)
> 5. Save changes
>
> Once done, tell me the site name and I'll update all page-level settings via MCP.

#### Step 4 — Page Settings via MCP

Once the user confirms the site, use the MCP to update the default pages:

```
Webflow:data_pages_tool → list_pages (site_id)
```

Then update the Home page settings:
```
Webflow:data_pages_tool → update_page_settings
  - title: "[Project Name] — Home"
  - slug: "/"
  - seo.title: "[Project Name] | [Tagline]"
  - seo.description: "[Project description]"
```

Ask the user for:
- Project/company name
- Tagline or one-line description
- Primary domain (if known)

Apply updates to all standard pages (Home, Style Guide, any others in the clone).

#### Step 5 — Confirm & Move to Building

Once site settings are done, confirm:

> ✅ Your Client-First project is set up and connected. The Webflow MCP is live.
> What would you like to build first? You can also type `/client-first style-guide` to customise the default Style Guide page.

---

### `/client-first style-guide`

**Goal:** Configure the Style Guide page for a new project — variables, typography, and colours — using the Webflow MCP Designer tools.

This command runs three sequential sub-workflows. Always execute them in order:
1. **Variables** — set up the colour/size token system
2. **Typography** — apply typefaces to HTML tags and heading-style classes
3. **Page build** — populate the Style Guide page with the design system

---

#### Sub-workflow 1 — Variables Setup

**Step 1: Read the existing variable collections**
```
Webflow:variable_tool → get_variable_collections (query: "all")
Webflow:variable_tool → get_variables (variable_collection_id)
```
Show the user a summary of what's found (collection name, number of variables, group structure).

**Step 2: Ask the user which path to take**

Present this choice:

> **How would you like to set up colour variables for this project?**
>
> A) **Use the default Client-First variable structure** — keep the existing groups (Background Color, Text Color, Border Color, Link Color, Base Color) and update the values to match your design system
> B) **Create a new variable collection** — build a fresh structure from scratch using your design system's naming and grouping

---

**If the user selects Option A — Default Structure:**

Ask a second question:

> **How would you like to handle the existing structure?**
>
> 1. **Keep current group names, update values only** — fastest approach; remaps base colour values and re-points semantic tokens
> 2. **Rename groups to match my design system** — renames variable groups to match the design document's terminology, then updates values

Whichever option is chosen, follow this logic for each variable group:

- **Base colors** — update static hex values from the design system palette
- **Semantic tokens** (Background, Text, Border, Link) — re-point to the correct base variable using `existing_variable_id`
- **System colors** (success, warning, error) — if the design system does NOT define these, **leave them unchanged**. If it does define them, update accordingly.

```
Rule: Never modify system colour variables (success/warning/error)
unless the design document explicitly provides values for them.
If no values are provided → preserve the Client-First defaults.
```

---

**If the user selects Option B — New Collection:**

1. Ask the user to confirm the collection name (e.g. `[Project Name] Tokens`)
2. Create the collection:
```
Webflow:variable_tool → create_variable_collection (name: "[Project Name] Tokens")
```
3. Build the structure in groups based on the design document. Standard groups to create:

```
Brand/         → primary, secondary, accent, surface variants
Text/          → primary, secondary, muted, on-dark
Background/    → base, surface, surface-low, surface-high, overlay
Border/        → default, strong, subtle
System/        → success, warning, error (use Client-First defaults if not in design doc)
```

4. Create all base colour variables first as static values, then create semantic tokens that reference base variables using `existing_variable_id`.

---

**Variable Naming Convention (for new collections)**

Always use slash `/` to create groups in Webflow's variable panel:

```
Brand/primary          → #000000
Brand/accent           → #3148ea
Background/surface     → #f9f9f9
Background/surface-low → #f3f3f3
Text/primary           → #000000
Text/secondary         → #5f5e5e
Text/body              → #474747
Border/default         → #e8e8e8
```

---

#### Sub-workflow 2 — Typography Setup

**Overview of classes touched:**

| Class | Type | Warning required? |
|-------|------|-------------------|
| `body` | HTML global tag | ❌ No |
| `h1` – `h6` | HTML tag overrides | ❌ No |
| `p` | HTML global tag | ❌ No |
| `a` | HTML global tag | ❌ No |
| `blockquote` | HTML global tag | ❌ No |
| `heading-style-h1` – `heading-style-h6` | Built-in CF utility | ✅ Triple Warning |
| `text-style-quote` | Default Client-First class (in snapshot) | ✅ Triple Warning, bundle with heading-style |

---

**Step 1: Scan all existing styles**

Always scan before touching anything. This gives you the exact style IDs and confirms which classes are present.

```
Webflow:style_tool → get_styles (query: "all", skip_properties: false)
```

From the results, identify and note the IDs for:
- `default-body` (id: `default-body`)
- `default-h1` through `default-h6`
- `default-p`
- `default-a`
- `default-blockquote`
- `heading-style-h1` through `heading-style-h6` (IDs starting with `07815991-...`)
- `text-style-quote`

---

**Step 2: Identify fonts from the design system**

Before making any updates, extract from the design document:

```
Display font:     → used for all headings (H1–H6, heading-style-*)
Body font:        → used for body, p, a, blockquote, labels
Body text color:  → hex value for paragraph/body text
Letter-spacing:   → tight value for large display headings (e.g. -0.02em)
Line heights:     → from design spec or use these defaults:
                    H1/H2: 1.1 | H3/H4: 1.2–1.4 | H5/H6: 1.5 | body: 1.7
```

---

**Step 3: Update `body` tag**

No warning required. Batch with other HTML tag updates in the same MCP call.

```
Webflow:style_tool → update_style
  style_name: "body"
  properties:
    font-family:  [body font, e.g. "Inter, sans-serif"]
    color:        [body text color hex, e.g. "#474747"]
    font-size:    "1rem"
    line-height:  "1.7"
```

---

**Step 4: Update `h1` through `h6` HTML tags**

Batch all 6 heading tag updates in a single MCP call alongside the body update. These are HTML element overrides — not Client-First utility classes — so no warning is required.

Apply to each heading tag:

```
h1:
  font-family:    [display font]
  font-size:      "4rem"
  font-weight:    "700"
  line-height:    "1.1"
  letter-spacing: "-0.02em"   ← only if design system specifies tight tracking for H1/H2

h2:
  font-family:    [display font]
  font-size:      "3rem"
  font-weight:    "700"
  line-height:    "1.2"
  letter-spacing: "-0.02em"

h3:
  font-family:    [display font]
  font-size:      "2rem"
  font-weight:    "700"
  line-height:    "1.2"

h4:
  font-family:    [display font]
  font-size:      "1.5rem"
  font-weight:    "700"
  line-height:    "1.4"

h5:
  font-family:    [display font]
  font-size:      "1.25rem"
  font-weight:    "700"
  line-height:    "1.5"

h6:
  font-family:    [display font]
  font-size:      "1rem"
  font-weight:    "700"
  line-height:    "1.5"
```

**Important:** The `h1`–`h6` override classes (IDs like `0c417d55-...`) control margin only. The `default-h1` through `default-h6` (IDs like `default-h1`) control the actual visual styles. Target the `default-*` versions for font changes. Both will appear in `get_styles` results — use the `default-` prefixed IDs.

---

**Step 5: Update `p`, `a`, and `blockquote` HTML tags**

Batch these three in one MCP call:

```
p:
  font-family:  [body font]
  font-size:    "1rem"
  line-height:  "1.7"
  color:        [body text color hex]

a:
  font-family:              [body font]
  transition-property:      "opacity"
  transition-duration:      "0.2s"
  transition-timing-function: "ease"
  ← Do NOT set color here — link color is controlled by the Link Color variable token

blockquote:
  font-family:        [body font]
  font-size:          "1.25rem"
  line-height:        "1.6"
  font-style:         "italic"
  color:              [body text color hex]
  border-left-style:  "solid"
  border-left-width:  "0.25rem"
  border-left-color:  [on-surface dark hex, e.g. "#1a1c1c"]
  padding-left:       "1.5rem"
  margin-top:         "2rem"
  margin-bottom:      "2rem"
```

---

**Step 6: Issue Triple Warning for `heading-style-h*` classes**

These ARE built-in Client-First utility classes. All three warnings must be issued and the user must type **"I confirm"** before proceeding.

```
⚠️ WARNING 1 of 3
You are about to modify heading-style-h1 through heading-style-h6 —
built-in Client-First utility classes. This affects EVERY element
using these classes across the entire project.
Are you sure you want to continue?

⚠️ WARNING 2 of 3
These 6 classes are global. Any element using heading-style-h* as a
visual override will inherit [display font]. This cannot be undone
globally without re-editing all 6 classes.
Do you still want to proceed?

🚨 WARNING 3 of 3 — FINAL CONFIRMATION
You are about to permanently alter 6 core Client-First utility classes.
Type "I confirm" to proceed, or "cancel" to stop.
```

---

**Step 7: Update `heading-style-h1` through `heading-style-h6` (after confirmation)**

Batch all 6 in a single MCP call:

```
heading-style-h1:
  font-family:    [display font]
  letter-spacing: "-0.02em"
  font-size:      "4rem"       ← preserve existing CF value
  line-height:    "1.1"        ← preserve existing CF value
  font-weight:    "700"        ← preserve existing CF value

heading-style-h2:
  font-family:    [display font]
  letter-spacing: "-0.02em"
  font-size:      "3rem"
  line-height:    "1.2"
  font-weight:    "700"

heading-style-h3:
  font-family:    [display font]
  font-size:      "2rem"
  line-height:    "1.2"
  font-weight:    "700"

heading-style-h4:
  font-family:    [display font]
  font-size:      "1.5rem"
  line-height:    "1.4"
  font-weight:    "700"

heading-style-h5:
  font-family:    [display font]
  font-size:      "1.25rem"
  line-height:    "1.5"
  font-weight:    "700"

heading-style-h6:
  font-family:    [display font]
  font-size:      "1rem"
  line-height:    "1.5"
  font-weight:    "700"
```

**Rule:** Only add `letter-spacing` to h1 and h2. Do not add it to h3–h6 unless the design system explicitly specifies it.

---

**Step 8: Update `text-style-quote`**

`text-style-quote` ships with the clone, so it is protected. Cover it in the Step 6 warning together with heading-style-h*, then update to match blockquote exactly:

```
Webflow:style_tool → update_style
  style_name: "text-style-quote"
  properties:
    font-family:        [body font]
    font-size:          "1.25rem"
    line-height:        "1.6"
    font-style:         "italic"
    color:              [body text color hex]
    border-left-style:  "solid"
    border-left-width:  "0.25rem"
    border-left-color:  [on-surface dark hex]
    padding-left:       "1.5rem"
```

---

**Step 9: Confirm typography changes**

After all updates complete, present a summary table to the user:

| Class | Font applied | Key properties |
|-------|-------------|----------------|
| `body` | [body font] | 1rem / 1.7 / body color |
| `h1` | [display font] | 4rem / 700 / -0.02em |
| `h2` | [display font] | 3rem / 700 / -0.02em |
| `h3`–`h6` | [display font] | Per CF scale |
| `p` | [body font] | 1rem / 1.7 / body color |
| `a` | [body font] | opacity transition added |
| `blockquote` | [body font] | 1.25rem / italic / dark border |
| `heading-style-h1` | [display font] | -0.02em added |
| `heading-style-h2` | [display font] | -0.02em added |
| `heading-style-h3`–`h6` | [display font] | font-family added |
| `text-style-quote` | [body font] | matches blockquote |

Ask: "Does this look correct in the Designer? If so, we can move on to the Style Guide page build."

---

#### Sub-workflow 3 — Style Guide Page Build

**Step 1: Get the page ID**
```
Webflow:data_pages_tool → list_pages (site_id)
→ find slug "style-guide" → capture page_id
```

**Step 2: Ensure the Designer is on the Style Guide page**
Prompt the user to switch to the Style Guide page in the Designer before proceeding. Wait for confirmation.

**Step 3: Scan existing elements**
```
Webflow:element_tool → get_all_elements (query: "all")
```

**Step 4: Build or update sections**

Use `Webflow:element_builder` and `Webflow:style_tool` to build/update the Style Guide. Always follow Client-First structure:

```
section_[name] (Div, tag: section)
└── padding-global padding-section-medium (Div)
    └── container-large (Div)
        └── [section content]
```

Standard Style Guide sections to include:

1. **Header** — project name, tagline, version label using `sg_header`, `text-style-display-lg`
2. **Colours** — swatch grid using `sg_swatch-grid`, each swatch is `sg_color-swatch` + a `background-color-[token]` utility (never `text-color-*`; create the utility if missing), labelled with token name and hex value using `sg_label`
3. **Typography** — one `sg_type-row` per type style, showing live example text + label (token name, size, weight, font)
4. **Buttons** — all button variants side by side
5. **Spacing** — spacer block scale reference

**Step 5: Update page SEO**
```
Webflow:data_pages_tool → update_page_settings
  - title: "Style Guide — [Project Name]"
  - draft: true  (always keep Style Guide as draft — never public)
  - seo.title: "Style Guide | [Project Name]"
  - seo.description: "Internal design system reference for [Project Name]."
```

**Step 6: Confirm and summarise**
After all three sub-workflows complete, present a full summary:
- Variables updated/created (count + groups)
- Typography classes updated (list)
- Style Guide sections built (list)
- Page status (draft confirmed)

Ask if the user wants to continue to page building or revisit any part of the setup.

---

### `/client-first help` or `/cfo help`

List all available commands:

```
/cfo start              — Alias of create new project (also: /cfo start new project)
/cfo create new project  — Clone template, connect MCP, set up site settings
/cfo style-guide         — Full style guide setup: variables → typography → page build
                           Sub-workflows run in order:
                           1. Variables: default structure or new collection
                           2. Typography: body tags + heading-style classes (triple warning)
                           3. Page: build/update Style Guide sections via Designer MCP
/cfo help                — Show this list

Aliases: /client-first and /cfo are identical. Use either.
```

Any other `/cfo` or `/client-first` input should be treated as a build request — proceed with the full Client-First methodology to fulfil it.

---

# Part B — Reference

## B1 — Class Naming

### The Four Class Types

#### 1. Utility Classes
A utility class is created for **one specific combination of CSS properties**, reusable across the entire project.

- **Syntax:** Dashes only — `text-size-large`, `background-color-primary`, `padding-medium`
- **Behaviour:** Updating the class updates every element using it (global by nature)
- **Use for:** Typography, spacing, colours, visibility, layout helpers

```
text-size-large
background-color-primary
padding-medium
overflow-hidden
hide-tablet
```

#### 2. Global Classes
A global class is intended for use across the entire project. It is a broader classification — utility classes and custom classes **can both be** global classes.

- **Syntax:** Can use dashes `-` or underscores `_`
- **Examples:** `header_background-layer`, `faq_item`

#### 3. Custom Classes
A custom class is created for a **specific and identifiable component, page, grouping, or element**.

- **Syntax:** Underscore `_` defines the folder/component boundary
- **Format:** `folder-name_element-name`
- **Examples:** `hero_title`, `team-list_headshot-wrapper`, `testimonials_card`
- **Why custom over utility?** Better maintainability. Scoped to one component. Allows complex, unique styles without polluting the global utility system.

```
header_background-layer
testimonial-slider_headshot
footer_copyright-text
pricing_card-highlight-layer
```

#### 4. Combo Classes
A combo class is a **variant** of a base class. It inherits all base styles and adds more on top.

- **Prefix:** Always `is-`
- **Applied as:** Second class on the element (after the base class)
- **Examples:**
  - `button` + `is-secondary`
  - `button` + `is-text`
  - `hero_card` + `is-featured`
  - `header_content` + `is-home`

```
✅ button is-secondary       (inherits button styles, adds secondary variant)
✅ pricing_card is-featured  (inherits card styles, adds featured highlight)
❌ is-secondary              (combo class alone — meaningless without base)
```

---

### Naming Convention: General → Specific

Class names read from **most general** to **most specific**.

#### Utility pattern:
```
text - size - large
 ↑       ↑      ↑
what   aspect  value
```

| Class | Reads as |
|-------|----------|
| `text-size-large` | Text → size → large |
| `text-color-primary` | Text → colour → primary |
| `background-color-secondary` | Background → colour → secondary |
| `padding-bottom` + `padding-large` | Padding → direction → size |

#### Custom pattern:
```
team-list _ headshot - wrapper
    ↑              ↑        ↑
folder name    element    descriptor
(component)
```

| Class | Reads as |
|-------|----------|
| `team-list_component` | Team list folder → root component element |
| `team-list_headshot-image` | Team list folder → headshot image |
| `team-list_headshot-wrapper` | Team list folder → element wrapping the headshot |
| `testimonials_card` | Testimonials folder → card element |
| `testimonials_card-content` | Testimonials folder → content area inside card |

---

### Naming Rules

#### ✅ Do
- Use full, descriptive words: `testimonials_wrapper`, `hero_subtitle`
- Make names self-documenting — another developer should understand purpose immediately
- Use one underscore per class to define one folder
- Use `is-` for all combo classes
- Keep custom class names scoped to their component folder

#### ❌ Don't
- Abbreviate: `col-2`, `flex-mobile-a-c`, `h-btn` → meaningless to future developers
- Use positional names: `left-column`, `top-section` → breaks when layout changes
- Create generic names: `box`, `wrapper`, `item` → not enough context
- Use more than one underscore to create nested folders (one underscore = one folder only)
- Apply utility classes and custom classes that conflict in purpose

---

### Folders System

Folders in Webflow's class panel are created automatically by the **underscore** in class names.

```
folder-name_element     →  creates folder "folder-name", element "element"
hero_title              →  folder: hero | element: title
hero_image-wrapper      →  folder: hero | element: image-wrapper
```

**One underscore = one folder.** Two underscores (`hero_card_title`) creates a nested folder — avoid this unless intentional.

Utility classes (no underscore) are automatically grouped in the **Utility** folder.

#### Example: A "Pricing" section
All custom classes grouped under the `pricing_` folder:
```
pricing_component
pricing_grid
pricing_card
pricing_card is-featured
pricing_card-header
pricing_card-price
pricing_card-feature-list
pricing_card-cta
```

---

### Meaningful vs. Unmeaningful Class Names

| ❌ Avoid | ✅ Use |
|----------|--------|
| `col-2` | `team-list_card-grid` |
| `flex-a-c` | (use layout on the parent custom class) |
| `box1` | `pricing_card` |
| `blue-text` | `text-color-primary` or `hero_eyebrow` |
| `big` | `text-size-large` |
| `mb20` | `spacer-medium` or `padding-bottom padding-medium` |

---

### When to Use Custom vs. Utility

| Situation | Use |
|-----------|-----|
| Reusable style across many components | Utility class |
| One-off style unique to a component | Custom class |
| Variant of a component | Combo class (`is-`) |
| Complex mix of styles for one element | Custom class (merge stacks) |
| Typography that's totally unique to one element | Custom class (`hero_title`) |
| Standard paragraph sizes | Utility (`text-size-regular`) |

#### Quick Guide Additions

**Global class:** a further classification (any utility or custom class meant for site wide use). May use `-` or `_`.

**Meaningful naming test:** a class name must answer (1) what is this class's purpose in the project, and (2) how can the name give the most context about what it is responsible for. Never abbreviate.

**Folders:** one underscore = one folder. The first word of the class name is the folder name. Renaming a folder renames every class inside it. Two folders (`folder-1_folder-2_element`) are valid but avoided by default. Utility classes (no underscore) group in the Utility folder automatically.

**Merging stacked classes:** use the Finsweet Extension (Merge classes) to collapse a deep stack into one custom class or a combo class.


---

## B2 — Page Structure

### Core Page Hierarchy

Every Client-First page follows this exact structure. This is non-negotiable.

```
Body
└── page-wrapper (Div — outermost parent of ALL elements)
    ├── navbar (Nav tag — outside main-wrapper)
    │
    ├── main-wrapper (Main tag — all page-specific content)
    │   ├── section_hero (Section tag)
    │   │   └── padding-global (Div)
    │   │       └── [padding-section-medium] applied on padding-global div
    │   │           └── container-large (Div)
    │   │               └── hero_component (Div)
    │   │                   └── [component content]
    │   │
    │   ├── section_features (Section tag)
    │   │   └── padding-global (Div)
    │   │       └── container-medium (Div)
    │   │           └── features_component (Div)
    │   │
    │   └── section_cta (Section tag)
    │       └── ...
    │
    └── footer_component (Div or Footer tag)

global-styles (HTML Embed — Symbol, added to every page)
```

---

### Element-by-Element Breakdown

#### `page-wrapper`
- **Tag:** Div
- **Purpose:** Outermost parent of all elements on the page
- **Why:** Enables copy/pasting the entire page. Allows CSS targeting the entire page scope.
- **CSS:** No default styles. Sometimes used for `overflow: hidden` to contain animations.

#### `navbar`
- **Tag:** `<nav>` HTML tag
- **Purpose:** Site navigation
- **Position:** Outside `main-wrapper` — nav content is not page-specific
- **Class:** Usually `navbar_component` or just the Webflow Navbar component

#### `main-wrapper`
- **Tag:** `<main>` HTML tag (important for accessibility)
- **Purpose:** Contains all page-specific content
- **Why `<main>` tag?** Accessibility — screen readers use `<main>` to jump to primary content. Required for WCAG compliance.

#### `section_[identifier]`
- **Tag:** `<section>` HTML tag
- **Purpose:** Wraps an entire section of content
- **Naming:** `section_hero`, `section_features`, `section_testimonials`, `section_pricing`, `section_faq`, `section_cta`
- **Why sections?** Organises the Navigator panel cleanly. Semantically correct HTML.

#### `padding-global`
- **Tag:** Div
- **Purpose:** Applies consistent left + right outer padding across all sections
- **CSS:** `padding-left: 2.5rem; padding-right: 2.5rem` (site-wide horizontal breathing room)
- **v2.1 update:** Apply `padding-section-[size]` directly on this div — no need for a separate padding wrapper

#### `padding-section-[size]`
Applied on the `padding-global` div as a **combo class** in v2.1:

| Class | Top + Bottom Padding |
|-------|---------------------|
| `padding-section-small` | `3rem` (48px) |
| `padding-section-medium` | `5rem` (80px) |
| `padding-section-large` | `8rem` (128px) |

```
✅ v2.1 usage:
<div class="padding-global padding-section-medium">
  <div class="container-large">
    ...
  </div>
</div>

❌ Old pattern (extra nesting — avoid):
<div class="padding-global">
  <div class="padding-section-medium">
    <div class="container-large">
      ...
    </div>
  </div>
</div>
```

#### `container-[size]`
- **Tag:** Div
- **Purpose:** Sets a max-width for content and centres it on the page
- **CSS:** `max-width: [size]; margin-left: auto; margin-right: auto; width: 100%`

| Class | Max Width |
|-------|-----------|
| `container-small` | `48rem` (768px) |
| `container-medium` | `64rem` (1024px) |
| `container-large` | `80rem` (1280px) |

**When to use which size:**
- `container-small` → Blog posts, long-form text, narrow forms
- `container-medium` → Standard content sections, typical marketing pages
- `container-large` → Wide grids, hero sections, feature showcases

#### `global-styles`
- **Tag:** HTML Embed (as a Symbol)
- **Purpose:** Houses all Client-First utility CSS, resets, and project-wide custom CSS
- **Position:** Inside `page-wrapper`, usually before or after navbar
- **Rule:** **Must be added to every page in the project.** Create as a Symbol so updates propagate everywhere.

---

### Section Pattern Examples

#### Hero Section
```
section_hero (Div, tag: section)
└── padding-global padding-section-large (Div)
    └── container-large (Div)
        └── hero_component (Div) [flex, align-center, gap: 4rem]
            ├── hero_content (Div)
            │   ├── hero_eyebrow (Div) [text-size-small, text-color-secondary]
            │   ├── H1  [heading-style-h1 — or no class if default H1 styles match]
            │   ├── hero_subtitle (Paragraph) [text-size-large]
            │   └── button-group (Div)
            │       ├── button (Link Block)
            │       └── button is-secondary (Link Block)
            └── hero_image-wrapper (Div)
                └── Image
```

#### Feature Cards Section
```
section_features (Div, tag: section)
└── padding-global padding-section-medium (Div)
    └── container-large (Div)
        └── features_component (Div)
            ├── features_header (Div)
            │   ├── H2
            │   └── features_subtitle (Paragraph)
            └── features_grid (Div) [grid, 3 columns]
                ├── features_card (Div)
                │   ├── features_card-icon (Div)
                │   │   └── Icon (icon-medium)
                │   ├── H3  [heading-style-h3]
                │   └── Paragraph [text-size-regular]
                ├── features_card (Div)
                └── features_card (Div)
```

#### CTA Section
```
section_cta (Div, tag: section)
└── padding-global padding-section-large (Div)
    └── container-small (Div)
        └── cta_component (Div) [text-align-center]
            ├── H2
            ├── cta_subtitle (Paragraph)
            └── button-group (Div)
                └── button (Link Block)
```

---

### Responsive Structure Rules

Client-First uses four breakpoints (Webflow standard):

| Breakpoint | Width | Class adjustments |
|------------|-------|-------------------|
| Desktop | 1280px+ | Base styles |
| Tablet | ≤991px | Adjust grid → single column, reduce padding |
| Mobile Landscape | ≤767px | Further reduce padding, adjust font sizes |
| Mobile Portrait | ≤478px | Full width everything, stack all flex rows |

#### Responsive pattern for hero:
- Desktop: `hero_component` is `display: flex`, `flex-direction: row`, `gap: 4rem`
- Tablet: Change to `flex-direction: column`, reduce gap
- Mobile: Reduce padding-global to `1.25rem`, adjust heading sizes

#### Responsive padding-global:
- Desktop: `padding: 0 2.5rem`
- Mobile Landscape: `padding: 0 1.5rem`
- Mobile Portrait: `padding: 0 1rem`

---

### What Goes Inside vs. Outside `main-wrapper`

| Element | Inside `main-wrapper`? |
|---------|----------------------|
| All page sections | ✅ Yes |
| Hero, features, testimonials, pricing, FAQ, CTA | ✅ Yes |
| Navbar | ❌ No — `<nav>` is not page-specific |
| Footer | ❌ No — footer is not page-specific content |
| Global Styles embed | ❌ No — lives in `page-wrapper` directly |
| Cookie banners | ❌ No |


---

## B3 — Typography

### Core Principle

Typography in Client-First is managed through **utility classes** applied to text elements. The system separates **HTML tag** (semantic hierarchy) from **visual style** (heading-style classes), allowing full design flexibility while maintaining correct document structure.

---

### Heading Tags vs. Heading Style Classes

#### The Rule
- **HTML tag** = semantic hierarchy (H1 → H2 → H3 → H4...)
- **`heading-style-` class** = visual appearance only

**Never skip heading levels for visual reasons.** Use `heading-style-` classes to make an H2 look like an H1 visually if needed.

#### Heading hierarchy (correct vs. wrong):

```
✅ Correct hierarchy:
H1
  H2
    H3
    H3
  H2
    H3
      H4

❌ Wrong — skipped levels:
H1
  H2
    H4  ← skipped H3
  H2
    H5  ← skipped H3, H4
```

**Why it matters:** Correct heading hierarchy is critical for SEO, screen readers, and accessibility compliance.

#### Default heading sizes (no class needed if visuals match):

| Tag | Default size |
|-----|-------------|
| H1 | `4rem` (64px) |
| H2 | `3rem` (48px) |
| H3 | `2rem` (32px) |
| H4 | `1.5rem` (24px) |
| H5 | `1.25rem` (20px) |
| H6 | `1rem` (16px) |

#### Overriding visual style:
```
H1 that should look like H3:
→ Element: H1 tag
→ Class: heading-style-h3

H2 that should look like H1:
→ Element: H2 tag
→ Class: heading-style-h1
```

Available `heading-style-` classes:
- `heading-style-h1` → `4rem`
- `heading-style-h2` → `3rem`
- `heading-style-h3` → `2rem`
- `heading-style-h4` → `1.5rem`
- `heading-style-h5` → `1.25rem`
- `heading-style-h6` → `1rem`

---

### Text Size

Use `text-size-` classes on paragraph and span elements to control font size.

| Class | Size | PX equiv |
|-------|------|----------|
| `text-size-large` | `1.5rem` | 24px |
| `text-size-medium` | `1.25rem` | 20px |
| `text-size-regular` | `1rem` | 16px |
| `text-size-small` | `0.85rem` | ~14px |
| `text-size-tiny` | `0.75rem` | 12px |

**`text-size-regular` is the default body size** — only apply it when overriding an inherited larger size.

---

### Text Colour

| Class | Value |
|-------|-------|
| `text-color-primary` | `#000000` (or project's dark) |
| `text-color-secondary` | `#ffffff` (or project's light) |
| `text-color-neutral` | `#707070` (muted grey) |

**Extend this system** for each project — add brand colours as utility classes:
- `text-color-brand`
- `text-color-accent`
- `text-color-error`
- `text-color-success`

---

### Text Weight

| Class | `font-weight` |
|-------|--------------|
| `text-weight-light` | `300` |
| `text-weight-normal` | `400` |
| `text-weight-semibold` | `600` |
| `text-weight-bold` | `700` |
| `text-weight-xbold` | `800` |

---

### Text Alignment

| Class | Alignment |
|-------|-----------|
| `text-align-left` | `text-align: left` |
| `text-align-center` | `text-align: center` |
| `text-align-right` | `text-align: right` |

**Tip:** For mobile responsiveness, override `text-align-center` to `text-align-left` on Mobile Portrait breakpoint when needed.

---

### Text Style Utilities

| Class | Effect |
|-------|--------|
| `text-style-2lines` | Truncates text to 2 lines (with ellipsis) |
| `text-style-3lines` | Truncates text to 3 lines (with ellipsis) |
| `text-style-allcaps` | `text-transform: uppercase` |
| `text-style-italic` | `font-style: italic` |
| `text-style-strikethrough` | `text-decoration: line-through` |
| `text-style-muted` | `opacity: 0.7` |

---

### Stacking Typography Classes

Typography utility classes **can be stacked together** — but only with other typography classes.

```
✅ Acceptable typography stack:
text-color-secondary text-weight-semibold text-style-italic

❌ Don't mix typography and layout/spacing utilities:
text-size-medium max-width-medium padding-bottom
↑ Nest a separate div for the max-width + padding instead
```

#### When stacking gets too deep:
If you're stacking 4+ typography classes on a single element, create a **custom class** and merge them:

```
Instead of: text-size-large text-weight-bold text-color-brand text-style-allcaps text-align-center
Create: hero_eyebrow  (custom class with all those styles merged)
```

Use Finsweet Extension → Merge Classes to do this non-destructively.

---

### Custom Typography Classes

Create a custom class for typography when:
- The element is **unique** to a specific component
- The typography combination is **too deep to stack** (4+ classes)
- The element needs **non-typography styles** too (margin, width, etc.)

#### Examples:
```
footer_copyright-text      → small footer text, unique to footer
footer_link                → styled nav link in footer
hero_subtitle              → the paragraph below the H1 in the hero
hero_eyebrow               → small label above the H1
team_card-name             → person name in team card
team_card-role             → person role/title in team card
pricing_card-price         → the large price number in pricing cards
testimonials_quote         → the quote text styling
```

---

### Full Typography Workflow Example

**Task:** Build the text content of a hero section

```
hero_content (Div)
├── hero_eyebrow (Div)          → custom: text-size-small, text-weight-semibold,
│                                  text-color-brand, letter-spacing, text-style-allcaps
├── H1                           → no class — default H1 styles are correct
├── hero_subtitle (Paragraph)   → custom: text-size-large, text-color-neutral,
│                                  line-height: 1.6
└── button-group (Div)
```

**Decision log:**
- H1 has no class because the default H1 style already matches the design
- `hero_eyebrow` is a custom class because it combines text-size + text-weight + text-color + letter-spacing + allcaps — too many to stack cleanly
- `hero_subtitle` is custom because it also needs a specific line-height not covered by utilities

---

### Typography at Breakpoints

Always address these responsive adjustments:

| Element | Tablet | Mobile Portrait |
|---------|--------|----------------|
| H1 | Reduce to ~`2.5rem` | Reduce to ~`2rem` |
| H2 | Reduce to ~`2rem` | Reduce to ~`1.75rem` |
| `text-size-large` | Keep or reduce to medium | Reduce to regular |
| `text-align-center` | Usually keep | Often override to left |
| Line heights | May need tightening | Yes on headings |


---

## B4 — Spacing

### Core Principle

Spacing in Client-First uses a **scale-based utility system** to keep spacing consistent and globally updatable. Never use arbitrary pixel values in Webflow style fields — always map to the spacing scale.

All spacing values are in **rem** (`1rem = 16px`).

---

### Spacing Scale

| Size name | `rem` value | `px` equivalent |
|-----------|-------------|-----------------|
| `tiny` | `0.125rem` | 2px |
| `xxsmall` | `0.25rem` | 4px |
| `xsmall` | `0.5rem` | 8px |
| `small` | `1rem` | 16px |
| `medium` | `2rem` | 32px |
| `large` | `3rem` | 48px |
| `xlarge` | `4rem` | 64px |
| `xxlarge` | `5rem` | 80px |
| `huge` | `6rem` | 96px |
| `xhuge` | `8rem` | 128px |
| `xxhuge` | `12rem` | 192px |
| `custom1` | Custom | Project-specific |
| `0` | `0rem` | 0px |

Also: `1.5rem` is available as an intermediate (used in section padding small: `3rem`).

---

### Three Spacing Methods

#### Method 1: Spacing Wrapper (Padding)

Apply padding utility classes to a **container Div** that wraps content.

**Two classes required:** direction + size

```
Direction classes:        Size classes:
padding-top               padding-tiny
padding-right             padding-xxsmall
padding-bottom            padding-xsmall
padding-left              padding-small
padding-vertical          padding-medium
padding-horizontal        padding-large
                          padding-xlarge
                          padding-xxlarge
                          padding-huge
                          padding-xhuge
                          padding-xxhuge
                          padding-custom1
                          padding-0
```

**Example:**
```
Div [padding-bottom padding-medium]
  ↳ H2
  ↳ Paragraph
```
This adds `2rem` (32px) of bottom padding to the wrapper div.

**For margins**, use the same pattern:
```
margin-top margin-medium
margin-bottom margin-large
```

---

#### Method 2: Spacing Block (Spacer)

A **standalone empty Div** placed between elements to create vertical space.

```
spacer-[size]
```

| Class | Space added |
|-------|-------------|
| `spacer-tiny` | `0.125rem` |
| `spacer-xxsmall` | `0.25rem` |
| `spacer-xsmall` | `0.5rem` |
| `spacer-small` | `1rem` |
| `spacer-medium` | `2rem` |
| `spacer-large` | `3rem` |
| `spacer-xlarge` | `4rem` |
| `spacer-xxlarge` | `5rem` |
| `spacer-huge` | `6rem` |
| `spacer-xhuge` | `8rem` |
| `spacer-xxhuge` | `12rem` |
| `spacer-custom1` | Custom |

**Example Navigator tree:**
```
hero_content (Div)
├── H1
├── spacer-small (Div)   ← 1rem gap between H1 and subtitle
├── hero_subtitle (Paragraph)
├── spacer-medium (Div)  ← 2rem gap before buttons
└── button-group (Div)
```

---

#### Method 3: Grid / Flex Gap

Use CSS `gap` on a parent grid or flex container. Apply via a **custom class** on the wrapper.

```
testimonials_grid (Div) [display: grid, grid-template-columns: 1fr 1fr 1fr, gap: 2rem]
└── testimonials_card (Div)
└── testimonials_card (Div)
└── testimonials_card (Div)
```

This is the cleanest method for component grids — no spacer divs needed.

---

### When to Use Which Method

| Situation | Best method |
|-----------|-------------|
| Vertical space between stacked text elements | Spacer block |
| Padding inside a card or component | Spacing wrapper |
| Space between grid/flex children | Gap on parent |
| Consistent top/bottom on a section | `padding-section-[size]` on `padding-global` |
| Unique one-off padding | Custom class |

---

### Section Padding

Use `padding-section-[size]` on the `padding-global` div to control top and bottom section spacing.

| Class | Top + Bottom |
|-------|-------------|
| `padding-section-small` | `3rem` (48px each) |
| `padding-section-medium` | `5rem` (80px each) |
| `padding-section-large` | `8rem` (128px each) |

```
✅ Correct v2.1 usage (applied directly on padding-global):
<div class="padding-global padding-section-medium">
  <div class="container-large">
    ...
  </div>
</div>
```

**Rule of thumb:**
- `small` → Utility sections, footers, compact CTAs
- `medium` → Standard content sections (features, testimonials, FAQ)
- `large` → Hero sections, big brand moments

---

### Custom Spacing Classes

Create a custom class when:
- The spacing is **unique** to a specific component
- The spacing **combines** with other styles (padding + border + background = one class)
- You want to **avoid a combo class** for a single-use element

```
form_input {
  margin-bottom: 0.75rem;  /* unique spacing not in the scale */
  border: 1px solid #ccc;
  padding: 0.75rem 1rem;
}
```

The `spacing_clean` utility class removes native Webflow component spacing (useful for resetting form elements, navbar defaults, etc.).

---

### Responsive Spacing

Always reduce spacing at smaller breakpoints. Typical adjustments:

| Breakpoint | Section padding | Spacer blocks | Grid gap |
|------------|----------------|---------------|----------|
| Desktop | `padding-section-large` | Full scale | `3–4rem` |
| Tablet | Reduce by ~1 step | Reduce by ~1 step | `2rem` |
| Mobile Portrait | `padding-section-small` or custom | Reduce further | `1.5rem` |

**Spacer blocks on mobile:** In the Webflow style panel at a breakpoint, set `display: none` on spacer blocks to completely remove space, or reduce height. Spacer divs have no content so hiding them is clean.

---

### Common Mistakes to Avoid

```
❌ Using px directly in the Webflow spacing field: margin-bottom: 32px
✅ Use a spacer-medium div or padding-bottom padding-medium wrapper

❌ Adding padding directly to a section_ element
✅ Add padding-global inside the section, then container inside that

❌ Using margins on both the container and the section
✅ Use padding-section-[size] on padding-global for vertical, padding-global for horizontal

❌ Stacking spacer-medium AND padding-bottom on the same container
✅ Pick one method per context and be consistent
```


---

## B5 — REM and Fluid Responsive

### REM: The Foundation

#### What is REM?
`rem` (root em) is a CSS unit based on the **HTML root element's font size**.

- Browser default: `1rem = 16px`
- If the user changes their browser font size setting, all `rem` values scale accordingly
- VW and VH are **not** used for font sizes — they break accessibility

#### The Rule
**Apply `rem` to everything in Client-First:**
- Typography (font sizes, line heights, letter spacing)
- Spacing (padding, margin, gap)
- Widths and max-widths
- Border radius
- Icon sizes
- Any other size unit

Never use `px` for these properties — it locks out users with accessibility font-size settings.

---

### PX → REM Conversion

Formula: `rem = px ÷ 16`

| px | rem |
|----|-----|
| 4px | 0.25rem |
| 8px | 0.5rem |
| 12px | 0.75rem |
| 14px | 0.875rem |
| 16px | 1rem |
| 20px | 1.25rem |
| 24px | 1.5rem |
| 32px | 2rem |
| 40px | 2.5rem |
| 48px | 3rem |
| 56px | 3.5rem |
| 64px | 4rem |
| 80px | 5rem |
| 96px | 6rem |
| 128px | 8rem |
| 192px | 12rem |

#### Quick calculation in Webflow:
In any Webflow style field, type `value/16 rem` and press Enter.
Example: type `24/16 rem` → Webflow converts to `1.5rem` automatically.

#### Finsweet Extension — PX to REM Migrator
For existing projects built in px, use the "PX to REM migrator" in the Finsweet Extension to batch-convert all values to rem.

---

### Accessibility Benefits of REM

1. **Browser zoom works correctly** — when users zoom using browser settings (Ctrl/Cmd +), rem-based layouts scale proportionally
2. **User font-size preferences respected** — users who set their browser base font to 20px will get everything scaled up accordingly
3. **WCAG compliance** — VW, VH values for typography fail WCAG 1.4.4 (Resize Text)

---

### Viewport Units: When to Use What

| Unit | Use case |
|------|----------|
| `rem` | All typography, spacing, widths — DEFAULT choice |
| `%` | Widths relative to parent (e.g., `width: 50%` for two-column layouts) |
| `vw` | Full-width backgrounds, certain decorative elements (NOT for font sizes) |
| `vh` | Hero sections full viewport height — use carefully |
| `svh` | Small Viewport Height — use for mobile hero sections (avoids browser UI overlap) |
| `dvh` | Dynamic Viewport Height — accounts for appearing/disappearing browser chrome |
| `lvh` | Large Viewport Height — the maximum possible viewport |

#### Viewport height recommendation:
- `100vh` → Classic, but causes mobile browser UI overlap issues
- `100svh` → Safe minimum viewport — recommended for mobile heroes
- `100dvh` → Responsive to mobile browser chrome — best for most cases
- `100lvh` → Maximum viewport — use for decorative/overlay elements

---

### Fluid Responsive Generator (Finsweet)

Source: finsweet.com/client-first/docs/fluid-responsive.

- Optional add on, no workflow change, best added at the end of the project.
- The root font size changes linearly between breakpoints. Because every size is rem, the whole page scales.
- Generator defaults: breakpoints 479px, 1440px and 1920px. You set a start and end root font size per breakpoint (example: 16px at 1440px, 18px at 1920px). Options: Stop Scaling at Max Breakpoint, Add Editable Variables to Code, Remove Style Tag.
- Where: Finsweet Extension, Client-First, Fluid Design Generator, copy code, paste into the Global Styles embed (to preview in the Designer) or into Site Settings head code.

**Rule for Claude:** never invent the CSS. Ask the user to generate it with the Finsweet generator and paste it. If the user only gives breakpoints and root sizes, derive each segment by linear interpolation and label it "derived, verify against generator output":

```
slope     = (s2 - s1) / (w2 - w1)            (px per px, w in px)
intercept = (s1 - slope * w1) / 16           (rem)
html { font-size: calc(<intercept>rem + <slope * 100>vw); }
Example 16px at 1440, 18px at 1920: calc(0.625rem + 0.4167vw)
```

Wrap each segment in a max-width media query for its upper breakpoint.

---

### REM in Practice: Example Section

```css
/* hero_component styles */
.hero_component {
  display: flex;
  align-items: center;
  gap: 4rem;          /* 64px — use rem, not px */
  padding: 2rem 0;    /* 32px */
}

/* hero_subtitle */
.hero_subtitle {
  font-size: 1.25rem;  /* 20px */
  line-height: 1.6;    /* unitless line-height is fine */
  color: #707070;
  max-width: 32rem;    /* 512px — contains line length for readability */
}

/* pricing_card */
.pricing_card {
  padding: 2rem;        /* 32px internal padding */
  border-radius: 0.75rem; /* 12px */
  gap: 1.5rem;          /* 24px between card sections */
}
```


---

## B6 — Utility Systems

### Buttons

#### Base Button Classes
```
button           → Primary button
button is-secondary  → Secondary/outline variant
button is-text   → Text-only link button style
```

#### Button Structure
A button in Client-First is typically a **Link Block** or **Link** element:
```
button (Link Block)
├── [icon] (optional — icon-small or icon-medium)
└── button_text (Paragraph or Div)
```

#### Button Group
Use `button-group` to create horizontal spacing between multiple buttons:
```
button-group (Div)  [display: flex, gap: 1rem]
├── button (Link Block) → Primary CTA
└── button is-secondary (Link Block) → Secondary CTA
```

#### Button with Icon + Label
Add `display: flex` with `gap` directly on the button (Webflow style panel):
```
button (Link Block) [flex, align-center, gap: 0.5em]
├── icon-medium (Div)
│   └── SVG icon
└── Text ("Get Started")
```
Note: Use `0.5em` (not rem) for icon gap so it scales with the button's own font size.

#### Extending Buttons for Projects
Add project-specific combo classes:
```
button is-brand      → Brand colour fill
button is-white      → White fill (for dark backgrounds)
button is-large      → Larger padding/font size
button is-small      → Compact size
```

---

### Icons

#### Setting Icon Size
Use `icon-[size]` to set **height only** (width scales automatically via aspect ratio):

| Class | Height |
|-------|--------|
| `icon-small` | `1rem` (16px) |
| `icon-medium` | `2rem` (32px) |
| `icon-large` | `2.5rem` (40px) |

#### Square Icons
Use `icon-1x1-[size]` to set **equal height and width**:

| Class | Height & Width |
|-------|---------------|
| `icon-1x1-small` | `1rem × 1rem` |
| `icon-1x1-medium` | `2rem × 2rem` |
| `icon-1x1-large` | `2.5rem × 2.5rem` |

#### Icon Usage
```
features_card-icon (Div) [icon-1x1-large]
└── SVG or Image element (set to 100% width + height)
```

---

### Visibility Utilities

#### Hide Elements

| Class | Hides on |
|-------|----------|
| `hide` | All viewports |
| `hide-tablet` | Tablet and below |
| `hide-mobile-landscape` | Mobile landscape and below |
| `hide-mobile-portrait` | Mobile portrait only |

**Use case:** Show a desktop-optimised image on desktop, hide it on mobile, show a mobile-optimised version on mobile:
```
hero_image-wrapper (Div) [hide-mobile-portrait]
hero_image-wrapper-mobile (Div) [hide] → override to show on mobile in breakpoint panel
```

---

### Max-Width Utilities

Limit the width of any element to maintain readable line lengths and layout control.

| Class | Max Width |
|-------|-----------|
| `max-width-xxlarge` | `80rem` (1280px) |
| `max-width-xlarge` | `64rem` (1024px) |
| `max-width-large` | `48rem` (768px) |
| `max-width-medium` | `32rem` (512px) |
| `max-width-small` | `20rem` (320px) |
| `max-width-xsmall` | `16rem` (256px) |
| `max-width-xxsmall` | `12rem` (192px) |

#### Full-width overrides (responsive):
| Class | Effect |
|-------|--------|
| `max-width-full` | Remove max-width — always full width |
| `max-width-full-tablet` | Full width at tablet breakpoint |
| `max-width-full-mobile-landscape` | Full width at mobile landscape |
| `max-width-full-mobile-portrait` | Full width at mobile portrait |

**Tip:** Use `max-width-medium` nested inside text blocks to control line length for readability. Ideal paragraph max-width is `32rem–48rem` for body copy.

---

### Background Colour Utilities

| Class | Colour |
|-------|--------|
| `background-color-primary` | `#f5f5f5` (light grey) |
| `background-color-secondary` | `#ffffff` (white) |
| `background-color-dark` | `#000000` (black) |

Extend for each project:
```
background-color-brand
background-color-accent
background-color-surface
```

---

### Overflow Utilities

| Class | Effect |
|-------|--------|
| `overflow-hidden` | `overflow: hidden` — clip content, contain animations |
| `overflow-scroll` | `overflow: scroll` — always show scrollbar |
| `overflow-auto` | `overflow: auto` — scrollbar only when needed |

**Most common:** `overflow-hidden` on `section_` or `page-wrapper` to contain GSAP animations or clip decorative background elements.

---

### Layout & Positioning Utilities

#### Centring
| Class | Effect |
|-------|--------|
| `align-center` | `margin-left: auto; margin-right: auto` — centres block elements |

#### Absolute fill (layer positioning)
| Class | Effect |
|-------|--------|
| `layer` | `position: absolute; top: 0; right: 0; bottom: 0; left: 0` — fills parent |

Use `layer` for background images, video backgrounds, overlay divs:
```
section_hero (Div, tag: section) [position: relative]
├── hero_bg-video (Div) [layer, overflow-hidden]
│   └── Video (100% width + height)
├── hero_bg-overlay (Div) [layer] [background: rgba(0,0,0,0.5)]
└── padding-global padding-section-large (Div) [position: relative, z-index: 1]
    └── container-large (Div)
        └── hero_content (Div)
```

#### Display
| Class | Effect |
|-------|--------|
| `display-inlineflex` | `display: inline-flex` — inline but with flex capabilities inside |

---

### Z-Index Utilities

| Class | `z-index` |
|-------|-----------|
| `z-index-1` | `1` |
| `z-index-2` | `2` |

Use for stacking layers. For complex stacking (navbar over hero, modals), use custom classes with higher z-index values as needed.

---

### Pointer Events

| Class | Effect |
|-------|--------|
| `pointer-events-none` | `pointer-events: none` — element ignores all mouse events (pass-through) |
| `pointer-events-auto` | `pointer-events: auto` — re-enable mouse events (override inherited none) |

**Use case:** Background overlay divs that should not intercept clicks. Decorative `layer` elements over interactive content.

---

### Spacing Reset

| Class | Effect |
|-------|--------|
| `spacing_clean` | Removes native Webflow component spacing — resets margins/padding on form elements, navbar items, etc. |

Use when Webflow's default component styles (form fields, navbar links) add unwanted spacing.

---

### Putting It Together: Feature Card Example

```
features_card (Div) [background-color-primary, border-radius: 1rem, overflow-hidden]
├── features_card-image-wrapper (Div) [overflow-hidden]
│   └── Image [100% width]
├── features_card-content (Div) [padding-large]  ← padding-[size] wrapper
│   ├── features_card-icon (Div) [icon-1x1-medium]
│   │   └── SVG icon
│   ├── spacer-small (Div)
│   ├── H3 [heading-style-h4]    ← looks like H4 but is H3 semantically
│   ├── spacer-xsmall (Div)
│   ├── Paragraph [text-size-regular, text-color-neutral]
│   ├── spacer-medium (Div)
│   └── button is-text (Link Block)
│       ├── Text "Learn more"
│       └── icon-small (Div) → arrow icon
```


---

## B7 — CMS, Interactions and Custom Code

### CMS Collection Schemas

#### Naming Convention for CMS Fields
CMS field slugs should follow the same **general → specific** philosophy as class names. Use lowercase, hyphens only.

```
✅ Good field names:
post-title
post-excerpt
post-featured-image
post-published-date
post-category          (reference field)
post-is-featured       (boolean/switch)
author-name
author-headshot
author-bio

❌ Avoid:
title2
img1
shortDesc
FeaturedPost
```

#### Standard Blog Collection Schema
```
Collection: Blog Posts
Slug: blog-posts

Fields:
├── Name (required)           → post-name (plain text)
├── Slug (required)           → auto-generated from name
├── post-excerpt              → plain text, 200 char limit
├── post-featured-image       → image
├── post-body                 → rich text
├── post-published-date       → date
├── post-read-time            → number (minutes)
├── post-category             → reference → Categories collection
├── post-author               → reference → Authors collection
├── post-is-featured          → switch (boolean)
├── post-seo-title            → plain text (optional override)
└── post-seo-description      → plain text (optional override)
```

#### Standard Team Members Collection
```
Collection: Team Members
Slug: team-members

Fields:
├── Name (required)           → member-name
├── Slug (required)
├── member-role               → plain text (Job title)
├── member-headshot           → image
├── member-bio                → rich text or plain text
├── member-linkedin-url       → link
├── member-twitter-url        → link (optional)
├── member-order              → number (for manual sort order)
└── member-is-leadership      → switch (to filter leadership team)
```

#### Standard Services Collection
```
Collection: Services
Slug: services

Fields:
├── Name (required)
├── Slug (required)
├── service-description       → plain text (short, for cards)
├── service-long-description  → rich text (for service page body)
├── service-icon              → image (SVG recommended)
├── service-featured-image    → image
├── service-price-label       → plain text ("From $2,000" or "Custom")
├── service-is-featured       → switch
└── service-order             → number
```

---

### CMS Dynamic Lists: Class Strategy

#### Wrapper Structure
```
section_team (Div, tag: section)
└── padding-global padding-section-medium (Div)
    └── container-large (Div)
        └── team_component (Div)
            ├── team_header (Div)
            │   ├── H2
            │   └── Paragraph
            └── team_list (Collection List Wrapper)   ← Webflow CMS component
                └── team_list-item (Collection List)  ← the ul/ol
                    └── team_card (Collection Item)   ← the li — repeating element
                        ├── team_card-image-wrapper (Div)
                        │   └── team_card-image (Image)  [bound to member-headshot]
                        ├── team_card-content (Div)
                        │   ├── H3 [bound to member-name]
                        │   └── team_card-role (Paragraph) [bound to member-role]
                        └── team_card-links (Div)
                            └── team_card-link (Link) [bound to member-linkedin-url]
```

#### CMS Class Naming Rules
- Collection List Wrapper → `[component-name]_list`
- Collection Item (repeating) → `[component-name]_card` or `[component-name]_item`
- Elements inside item → `[component-name]_card-[element]`
- Use consistent folder prefix so all CMS classes group together in Webflow's class panel

#### Filtering & Sorting CMS Lists
Webflow's native filter/sort covers most cases. For advanced filtering:
- Use **Finsweet CMS Filter** (free Finsweet attribute)
- Add `fs-cmsfilter-field="[field-slug]"` attributes to filter targets
- Class the filter UI with `[component-name]_filter-[element]` naming

---

### Webflow Interactions (IX2)

#### Trigger Types & Client-First Patterns

##### 1. On Page Load / Page Scroll
Used for hero animations, scroll-triggered reveals.

**Class-based approach:** Target elements by their custom classes using the Webflow IX2 trigger.
```
Trigger: Page load
Target: .hero_content
Action: Fade in + slide up (opacity 0→1, transform: translateY 2rem→0)
Easing: Ease out, 0.6s, delay 0.2s
```

Always target by **custom class** (`hero_content`) not utility classes — utility classes are shared project-wide and triggering animations on them would affect every instance.

##### 2. Element-in-View (Scroll Reveal)
```
Trigger: Element in view (each element)
Initial state: opacity: 0, transform: translateY(2rem)
While in view: opacity: 1, transform: translateY(0)
Easing: Ease out, 0.5s
```

**Stagger on collection items:**
Apply the trigger to the Collection List, target Collection Items with stagger.

##### 3. Hover Interactions
```
Trigger: Mouse hover (element)
Target: Same element OR child element
Actions:
  - Mouse over: card scale to 1.03, shadow increases
  - Mouse out: card scale to 1, shadow resets
```

Class structure for hover:
```
features_card (Div) [overflow-hidden] ← trigger on this
└── features_card-image (Image) ← target for scale: 1.1 on hover
```

##### 4. Click / Toggle Interactions
Used for accordions, tabs, mobile menus.

**Accordion pattern:**
```
faq_item (Div) ← trigger: click
├── faq_item-toggle (Div) ← clickable row
│   ├── H3
│   └── faq_item-icon (Div) [icon-small] ← rotate 180° on open
└── faq_item-content (Div) ← height 0→auto, opacity 0→1
```

---

### Custom Code Embeds

#### Global Styles Embed (Symbol)
Must exist on every page. Contains:

```html
<!-- Global Styles | Client-First v2 -->
<style>
/* ============================================
   PROJECT: [Project Name]
   SYSTEM: Client-First v2
   ============================================ */

/* Fluid Responsive (optional, add after build):
   paste the Finsweet Fluid Design Generator output here */

/* Custom Project Variables */
:root {
  --color-brand: #your-color;
  --color-accent: #your-color;
  --font-primary: 'Your Font', sans-serif;
}

/* Spacing custom1 value */
.padding-custom1 { padding: 1.75rem; }
.spacer-custom1 { height: 1.75rem; }

/* Project-specific utilities */
.text-color-brand { color: var(--color-brand); }
.background-color-brand { background-color: var(--color-brand); }

</style>
```

#### Rich Text Embed Styles
For CMS rich text fields, add a `richtext_styles` embed inside the Rich Text element:

```html
<style>
.w-richtext h2 { margin-bottom: 1rem; margin-top: 2rem; }
.w-richtext h3 { margin-bottom: 0.75rem; margin-top: 1.75rem; }
.w-richtext p  { margin-bottom: 1rem; line-height: 1.7; }
.w-richtext ul, .w-richtext ol { margin-bottom: 1rem; padding-left: 1.5rem; }
.w-richtext li { margin-bottom: 0.5rem; }
.w-richtext blockquote { 
  border-left: 4px solid var(--color-brand);
  padding-left: 1.5rem;
  font-style: italic;
  margin: 2rem 0;
}
.w-richtext img { border-radius: 0.5rem; }
</style>
```

#### Custom JavaScript Embed Pattern
Always place JS embeds **before the closing `</body>` tag** via Webflow's Page Settings > Custom Code, or inside an HTML Embed at the bottom of the page:

```html
<script>
// [Feature name] | [Project name]
// Description of what this script does

document.addEventListener('DOMContentLoaded', function() {
  // Your code here
  // Target by Client-First classes
  const cards = document.querySelectorAll('.features_card');
});
</script>
```

**Targeting in JS:** Always use Client-First custom classes as selectors. Never target Webflow's generated combo class strings — they're unpredictable. Custom classes are stable.

---

### GSAP + Client-First

When adding GSAP animations on top of IX2 (or instead of it):

```html
<!-- In Page Settings > Custom Code > Head -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.2/gsap.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.2/ScrollTrigger.min.js"></script>
```

```javascript
// GSAP scroll reveal using Client-First class targets
gsap.registerPlugin(ScrollTrigger);

gsap.utils.toArray('.features_card').forEach((card, i) => {
  gsap.fromTo(card, 
    { opacity: 0, y: 32 },
    {
      opacity: 1,
      y: 0,
      duration: 0.6,
      delay: i * 0.1, // stagger
      ease: 'power2.out',
      scrollTrigger: {
        trigger: card,
        start: 'top 85%',
      }
    }
  );
});
```

**Key rule:** Always target by **custom class** in GSAP — never by utility class. `'.features_card'` is correct. `'.padding-medium'` would incorrectly animate every padded element on the page.


---
