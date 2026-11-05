# Authoring configuration reference

[Documentation home](README.md) · [Astro](astro.md) · [Nuxt Content](nuxt-content.md)

All paths below are relative to the **selected website application**, including in a monorepo. These additions are optional: first make the native collections and website rendering work.

For complete examples, inspect the [Nuxt Content reference](https://github.com/ludulicious/marpos-studio-nuxt-reference) ([reference app](https://ludulicious.github.io/marpos-studio-nuxt-reference/)) or the [Astro reference](https://github.com/ludulicious/marpos-studio-astro-reference). The Nuxt reference pairs MDC manifests with Vue renderers, named slots and block tabs, and image-format settings with `ProseImg.vue`.

## Configuration map

| File/location | Purpose | Website responsibility |
| --- | --- | --- |
| Native content collection schema | Fields, required values, defaults, constraints, explicit relationships | Query and render these fields |
| Content-folder `.navigation.yml` | Titles, icons, filters, editing view, link fields, preview fallback | Use equivalent public navigation rules where desired |
| `content/.navigation.yml` | Website-wide image/video library folder references | Render the library records where needed |
| Website-root `studio.preview.json` | Content-file-to-preview-route mappings | Implement those routes |
| Website-root `studio.components.json` | Visual Markdoc/MDC component forms | Register and implement the actual components |
| Website-root `studio.images.json` | Markdown image format choices | Translate saved attributes into responsive styling |
| Document frontmatter `studio.blocks` | Page-specific editing tabs | Implement matching document slots and rendering layout |

## Fields authors can understand

Declare the fields authors should edit, with useful constraints, descriptions, defaults, enums, and nested structures. Avoid making authors type internal identifiers when a declared reference picker is appropriate. `{ src, alt }` image objects receive media controls; require meaningful alternative text where your content policy needs it.

Optional values can remain absent until an author supplies them. Existing undeclared content is preserved, but an explicit schema gives new records a predictable form. A form default does not guarantee the value is written into every existing document; the website should handle missing optional values.

Use the editor metadata API supported by the website's installed dependencies. Nuxt projects may expose `property(...).editor(...)` or `.editor(...)`; JSON Schema represents these hints under `$content.editor`. An editor hint is not an implementation of the website's runtime behavior.

## Folder labels, views, and filters

For example, put this in `content/settings/.navigation.yml`:

```yaml
title: Website settings
icon: i-lucide-settings
studio:
  view: single
  story: false
  previewUrl: /
```

`view: single` opens the one item directly. Zero or multiple items fall back to a list. `view: list` is the ordinary collection browser; `view: media` enables the media workflow described below. `story: false` opens Details without writing tabs and does not delete the body. Folder metadata files are not editable document cards. `.navigation.yaml` and `.navigation.json` are also recognized.

For a posts folder:

```yaml
title: Journal
icon: i-lucide-notebook-pen
studio:
  filters:
    - field: status
      label: Publication status
    - categories
  linkFields:
    - action.to
    - items.*.to
```

Filters use exact property names; dot paths address nested properties. Choices come from saved values and schema enums. Arrays match any element. Date fields offer calendar periods. Filters are shown only when configured. The nearest configured ancestor applies; `filters: []` disables inheritance.

`linkFields` enables existing-route suggestions while permitting custom URLs. `*` addresses an array item or one property segment. The nearest ancestor applies; `linkFields: []` disables inheritance. Schema editor inputs `link` and `internal-link` are alternatives; `internal-link` also validates site-relative URLs. Field names alone do not enable suggestions.

Collections follow declaration order. Ordinary content lists follow numeric filename ordering; media drag ordering uses the record's `order`. The website must implement its own public ordering. Avoid renaming existing files for cosmetic ordering until you have checked their IDs, references, and routes.

## Preview destinations

Put source-to-route mappings in website-root `studio.preview.json`:

```json
{
  "content/pages/home.md": "/",
  "content/faqs/delivery.yml": ["/help#delivery", "/checkout"]
}
```

Paths must be local website paths starting with `/`. Include the final language prefix and anchor. External URLs are excluded. A content field named `previewUrl`, annotated with editor input `internal-link`, can provide a per-record preview override. Folder metadata `studio.previewUrl` provides a fallback when no route is detected. Use explicit mappings when inference is ambiguous, and verify both existing and newly created items.

## Image and video libraries

For built-in website-wide pickers, keep library records under `content/`. Declare ordinary native collections for `photos/*.json` (or YAML) and `videos/*.json`, with one record per asset and matching schemas.

Photo fields: `title`, `image: { src, alt }`, optional `thumbnail: { src, alt }`, `categories` (string array), `credit`, `visible` (boolean), and `order` (number). New uploads must not require developer-only provenance fields; make additional metadata optional or supply appropriate defaults.

Put this in `content/photos/.navigation.yml`:

```yaml
title: Photos
icon: i-lucide-images
studio:
  view: media
  filters: [categories, visible]
  previewUrl: /gallery/
  media:
    directory: images/library
    formats: [jpg, jpeg, png, webp, gif]
    maxBytes: 10485760
    imageAspectRatio: "4:3"
    thumbnailAspectRatio: "16:9"
```

`directory` is relative to `public/`; this example saves images under `public/images/library/` with public URLs `/images/library/...`. Studio enforces a 10 MB ceiling. `imageAspectRatio` optionally locks the full-image crop. `thumbnailAspectRatio` sets the card crop, defaulting to 4:3. An explicitly different ratio enables a separately saved thumbnail; include that optional object in the schema. Render `thumbnail.src` with an `image.src` fallback in website galleries. Animated GIFs cannot use a separate thumbnail-cropping workflow.

For videos, use the same structure with `studio.media.kind: video` and usually `thumbnailAspectRatio: "16:9"`. Video records use `title`, `youtubeId`, optional `image: { src, alt }`, `categories`, `visible`, and `order`. Studio links YouTube videos rather than uploading video files.

Connect the pickers in `content/.navigation.yml`:

```yaml
studio:
  libraries:
    images: photos
    videos: videos
```

Values are folder paths relative to `content/`, not collection IDs. Either library is optional. Hidden items remain selectable for page references; visibility controls public galleries only when the website implements that rule. Implement filtering, sorting, captions/credits, and responsive rendering in your templates.

## Markdown image formats

Website-root `studio.images.json`:

```json
{
  "sizes": [
    { "value": "compact", "label": "Compact" },
    { "value": "normal", "label": "Normal" },
    { "value": "wide", "label": "Wide" }
  ]
}
```

Values are lowercase identifiers beginning with a letter, using letters, digits, and hyphens. Authors can select a configured size or a custom width. Sizes save as attributes such as `{size="compact"}`. Your Markdown pipeline must preserve/translate those attributes, and your website CSS must define their responsive appearance. The reference Astro repository demonstrates rendering helpers; merely adding this JSON does not style the website. Reopen the writing editor after changing formats.

## Visual components

Website-root `studio.components.json`, with an MDC example:

```json
{
  "version": 1,
  "components": [
    {
      "name": "callout",
      "syntax": "mdc",
      "label": "Callout",
      "description": "A short note inside a story.",
      "icon": "i-lucide-info",
      "component": "components/content/Callout.vue",
      "props": {
        "type": "object",
        "properties": {
          "title": { "type": "string", "title": "Heading", "minLength": 1, "default": "Remember" },
          "tone": { "type": "string", "enum": ["info", "warning"], "default": "info" }
        },
        "required": ["title"]
      },
      "slots": {
        "default": { "label": "Content", "format": "markdown", "default": "Write your note." },
        "caption": { "label": "Caption", "format": "markdown" }
      }
    }
  ]
}
```

This enables insertion and property editing through forms. Implement/register `Callout.vue` through the website's normal Nuxt content-component mechanism, including the corresponding slots. The `component` path is informational to Studio; it does not load the implementation into the editor.

For Astro Markdoc, set `syntax: "markdoc"`, use an informational Astro component path, and declare only the `default` slot. Configure the website's Markdoc integration/tag renderer separately. MDX remains source-only; this manifest does not enable visual JSX editing.

Component names must be unique by name/syntax and use lowercase kebab case. Property forms support scalar string, boolean, number, and integer types with JSON Schema constraints, enums, descriptions, and defaults. Slots use `format: "markdown"`; `required: true` keeps a slot present. Dynamic expressions and complex property values retain source fallback. Reopen a story after changing definitions. The manifest is limited to 100 components and must be smaller than 256 KiB.

## Page-specific block tabs

For an MDC page, frontmatter can define tabs for explicit slots and structured fields:

```yaml
studio:
  blocks:
    - id: story
      label: Introduction
      type: markdown
      component: support-sections
      slot: default
    - id: donation
      label: Donate
      type: markdown
      component: support-sections
      slot: donation
    - id: information
      label: Practical information
      type: fields
      field: information
information:
  title: Join us
```

The body must contain exactly one matching top-level component and one matching slot for each Markdown tab:

```md
::support-sections
#default

Introduction with **rich text**.

#donation

## Become a donor

Contact us to learn more.
::
```

Implement the corresponding Vue component with default and `donation` slots. Declare `studio` and `information` in the Nuxt collection schema so queries preserve them; mark optional operational `studio` metadata hidden using the installed editor API. Required fields remain editable even if marked hidden.

`type: fields` points to an existing top-level frontmatter field and moves it from Details into its tab. A Markdown block may also specify `field` to show a structured object above its slot editor. IDs begin with a lowercase letter and contain lowercase letters, digits, and hyphens; `details` and `source` are reserved, while `story` can replace the default Story tab.

Tabs share one document/save/preview flow. They do not rearrange website sections. Structural changes require editing the document and website renderer together. This slot-based convention is for MDC structure; do not apply it to MDX or assume it supplies named Markdoc slots.
