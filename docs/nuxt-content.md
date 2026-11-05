# Nuxt Content integration guide

[Documentation home](README.md) · [AI adaptation prompt](ai-prompts.md#nuxt-content-adapt-an-existing-website)

## Minimum website contract

- Declare `nuxt` and `@nuxt/content` in the website's `package.json` and configure the website to use Nuxt Content 3.
- Put `content.config.ts`, `.mts`, `.js`, or `.mjs` at the selected website root. Default `defineContentConfig` and named `collections` exports are supported.
- Use one local source per collection under `content/`. Include/exclude patterns are supported; collections must not overlap.
- Use Markdown, YAML, or JSON records with explicit supported schemas. Markdown with no frontmatter is also discoverable.
- Keep public media in `public/` and implement the website's rendering routes and queries.

Nuxt Studio is optional. Marpos Studio does not require installing it or migrating content into a separate CMS database.

## Minimal configuration

In `nuxt.config.ts`, keep the website's existing configuration and ensure its modules include `@nuxt/content`. Then define `content.config.ts`:

```ts
import { defineCollection, defineContentConfig } from '@nuxt/content'
import { z } from 'zod'

export default defineContentConfig({
  collections: {
    posts: defineCollection({
      type: 'page',
      source: 'posts/**/*.md',
      schema: z.object({
        title: z.string().min(1),
        description: z.string().max(200).optional(),
        status: z.enum(['draft', 'published']).default('draft'),
        hero: z.object({
          src: z.string(),
          alt: z.string().min(1),
        }).optional(),
      }),
    }),
    settings: defineCollection({
      type: 'data',
      source: 'settings/site.yml',
      schema: z.object({ title: z.string(), contactEmail: z.string().email() }),
    }),
  },
})
```

Use the Zod version/import already supported by your installed Nuxt Content release. Studio supports Zod 3/4 and JSON Schema field descriptions; it cannot infer arbitrary other validators or component-inherited schemas. Declare author-editable fields explicitly, including nested objects and arrays.

Create `content/posts/hello.md`:

```md
---
title: Hello
description: Our first editable post.
status: published
---

This story can be edited visually in Studio.
```

A fixed source such as `settings/site.yml` permits editing that file. A wildcard source permits creating records when Studio can infer a safe destination. Keep unrelated schemas in separate source patterns.

Your Nuxt pages must query and render these documents, usually with `queryCollection()` and `ContentRenderer`. Implement listings/navigation that include new records where intended. Apply any `status`, visibility, or sorting rules in website queries. An unrelated new Markdown file does not automatically become a visible page.

## Reference pickers

Create an authors data collection with a stable identifier field such as `slug`. Annotate the corresponding post field using the editor API supported by your installed Nuxt Content version. For versions exposing the `property` helper:

```ts
import { property } from '@nuxt/content'

// Field inside the posts schema:
author: property(z.string()).editor({
  input: 'reference',
  collection: 'authors',
  valueField: 'slug',
}),
```

Some projects use `z.string().editor(...)` instead. Keep the annotation style that your website actually supports; do not add runtime-invalid APIs just to satisfy an example. The equivalent field metadata is `$content: { editor: { input: 'reference', collection: 'authors', valueField: 'slug' } }` in JSON Schema.

This saves `author: alice`, and the website resolves it explicitly, for example `queryCollection('authors').where('slug', '=', post.author).first()`. Studio checks target existence and duplicate values. `valueField` defaults to `slug`; `path` selects the target's preview path. Nuxt's generated `id` is not synthesized by Studio.

Arrays of annotated strings support multiple references. Array editor metadata can select `selector: 'tags'` (default) or `selector: 'select'` and a `placeholder`. Saving identifier changes through Studio migrates incoming declared local references; external edits and ordinary unannotated strings do not receive that migration.

## Preview routes and languages

Studio uses Nuxt i18n locales/strategies and detectable literal content queries to suggest preview destinations. Custom routing can differ. For shared data or dynamic queries, commit a website-root `studio.preview.json`:

```json
{
  "content/settings/site.yml": "/",
  "content/about.md": ["/about", "/company"],
  "content/nl/home.md": "/nl/"
}
```

Keys are actual content paths relative to the website root. Values are existing website paths, optionally arrays. Include the final language prefix and anchor. Authors can choose **Preview on** for multiple destinations or **Set preview page** when detection is insufficient. Mappings do not create routes or change slugs.

Studio's generated local-preview configuration disables embedded Nuxt Studio/devtools and adjusts framing for authoring without rewriting your source configuration. External services still need to be available.

## Visual content components

Use Nuxt's normal content-component registration for your Vue renderers. Add website-root `studio.components.json` entries with `syntax: "mdc"` to give authors insert/edit forms and editable Markdown slots. See the [component contract](authoring.md#visual-components).

Registered MDC blocks support scalar properties, a default Markdown slot, and named Markdown slots. Expressions and complex/dynamic attributes retain source fallback. Unregistered components remain protected blocks while surrounding text stays editable. The manifest describes editing; it does not register or implement the website renderer.

For pages with several editorial sections, [block tabs](authoring.md#page-specific-block-tabs) can expose named MDC slots and related structured fields as separate editing tabs. The document structure and Vue component still control the actual website layout.

## Validate and hand over

Supported field rules produce editing and publishing feedback. Incomplete content may be saved as a draft; invalid changed content blocks publication. Do not assume every Nuxt runtime refinement or build-time behavior is reproduced by the editor; run the website's normal checks and production build too.

Remote sources, custom source directories, multiple sources within one collection, overlapping collections, unsupported validators, and inherited component fields need adaptation before editing. Use the [authoring reference](authoring.md) to improve the experience and the [handover checklist](verification.md) to prove the result.
