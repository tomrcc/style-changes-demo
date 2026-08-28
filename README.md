# Astro Minimal Starter

A minimal starter template for building an Astro site with [CloudCannon](https://cloudcannon.com/) using **Editable Regions** for visual editing.

See a [demo site](https://tiny-jackal.cloudvent.net/).

## Features

- Visual editing with [Editable Regions](https://cloudcannon.com/documentation/developer-guides/set-up-visual-editing/an-overview-of-editable-regions/) (text, image, array, source, and component regions)
- Page building with reusable components
- Blog with pagination and tags
- [Tailwind CSS v4](https://tailwindcss.com/) with CSS-first configuration
- SEO controls
- Pagefind search

## Getting Started

Click `Use this template` to make your own copy of the repository.

### Local Development

1. Clone the repository to your local machine.

2. Start the development server.

```bash
npm install
npm run dev
```

## CloudCannon Setup

This site is pre-configured for CloudCannon. Connect your repository and CloudCannon will detect the configuration in `.cloudcannon/initial-site-settings.json` and build your site automatically. The editing experience is defined in `cloudcannon.config.yml`, which you can modify to control your editors' experience.

### Editable Regions

This starter demonstrates several types of Editable Region:

- **Text** (`data-editable="text"`) for editing front matter text values inline
- **Image** (`data-editable="image"`) for editing front matter image values
- **Array** (`data-editable="array"`) for page-building with reorderable content blocks
- **Source** (`data-editable="source"`) for making standalone `.astro` pages editable
- **Component** (`<editable-component>`) for live re-rendering of Astro components

Components that need live re-rendering are registered in `src/scripts/register-components.ts` and loaded conditionally when the site is open in CloudCannon's Visual Editor.

#### Source Editables

The About page (`src/content/pages/about.astro`) demonstrates **source editables** — a pattern where content lives directly in an Astro template rather than in Markdown front matter. Source editable regions use `data-editable="source"`, `data-path="path/to/file.astro"`, and `data-key` attributes. CloudCannon writes changes straight back to the `.astro` file.

This is useful for standalone pages (like About or Contact) where a developer wants full control over the markup while still giving editors visual editing access — **and where page building with components is *not* desired**. No accompanying Markdown file or front matter schema is needed. A thin routing wrapper in `src/pages/about.astro` handles Astro's file-based routing.

### Components

Four page-building components are included:

- **Hero** — heading, subheading, image, and optional button
- **LeftRight** — side-by-side text and image, with optional flip and button
- **TextBlock** — heading and rich text content
- **StyledSection** — text, image and button, with editor-facing controls for
  typography, color, spacing and layout

### Data-driven styling

`StyledSection` demonstrates exposing style controls to editors. Font family,
text sizes and alignment are `select` Inputs, colors are `color` Inputs with a
brand `palette`, and spacing uses `range` sliders — all stored in a single
`style` object in front matter and applied at render time.

Two techniques keep this working with Tailwind v4, which purges class names it
cannot find in your source:

- **Lookup maps of complete class strings** for finite token sets. A composed
  `` `text-${size}` `` would be purged; `{ lg: "text-3xl md:text-4xl" }` is not.
- **CSS custom properties via an inline `style` attribute** for arbitrary values
  like picked colors and slider pixels, referenced through literal utilities
  such as `bg-[var(--sx-accent)]`.

Because style fields drive computed classes rather than text, they need the
component re-render path — an inline `data-editable="text"` region updates DOM
text only and cannot re-run an expression.

Adding a styled block takes four edits:

1. the component in `src/components/`
2. `componentMap` in `src/layouts/Page.astro`
3. `registerAstroComponent` in `src/scripts/register-components.ts`
4. a `_structures.content_blocks` value plus matching `_inputs` in `cloudcannon.config.yml`

Declare style Inputs with dot-scoped keys (`style.font_family`) — CloudCannon
matches `_inputs` keys by name at any nesting depth, so bare names can collide.

See [Style Controls](src/content/blog/style-controls.mdx) for the full write-up,
including the recommended image upload configuration.

### Content

- **Pages** are in `src/content/pages/` as Markdown with structured front matter, and support a component-based page-building workflow. Developers can also add standalone pages paired with a routing file in `src/pages/` (like `src/content/pages/about.astro`), and decide which parts of those pages are editable in CloudCannon.
- **Blog posts** are in `src/content/blog/` as MDX files
- **Data** files (site settings, navigation) are in `data/`

## Project Structure

```
├── .cloudcannon/          # CloudCannon schemas and postbuild
├── cloudcannon.config.yml # CloudCannon configuration
├── data/                  # Site-wide data files
├── public/                # Static assets
└── src/
    ├── assets/            # Images processed by astro:assets
    ├── components/        # Astro components
    ├── content/           # Content collections (pages, blog)
    ├── layouts/           # Page layouts
    ├── pages/             # Astro page routes
    ├── scripts/           # Component registration for visual editing
    └── styles/            # Global CSS (Tailwind v4)
```
