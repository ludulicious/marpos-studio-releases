# Astro integration guide

[Documentation home](README.md) · [AI adaptation prompt](ai-prompts.md#astro-adapt-an-existing-website)

## Minimum website contract

- Declare `astro` in the website's `package.json` dependencies or devDependencies.
- Export `collections` from `src/content.config.ts`, `.mts`, `.js`, or `.mjs`. A root `content.config.*` is also recognized.
- Use native local `glob()` loaders with explicit extensions. Supported content: `.md`, `.mdx`, `.mdoc`, `.yml`, `.yaml`, and `.json`.
- Keep collection bases in ordinary directories inside the selected website, such as `content/`, `src/content/`, or `src/data/`.
- Provide schemas whose imports resolve from the installed website. Keep content sources non-overlapping.
- Keep uploaded public media under `public/` and store public URLs such as `/images/example.jpg` in content.
- Implement the website routes and templates that consume these collections.

No Nuxt dependency, Nuxt Content configuration, or extra Studio website module is required.

## Minimal collection

For the current Astro API, put this in `src/content.config.ts`:

```ts
import { defineCollection } from 'astro:content'
import { z } from 'astro/zod'
import { glob } from 'astro/loaders'

export const collections = {
  posts: defineCollection({
    loader: glob({ base: './content/posts', pattern: '**/*.md' }),
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
}
```

Use the Zod import supported by your installed Astro version; older projects may export `z` from `astro:content`. Do not upgrade a working website solely to copy an import from an example.

Create `content/posts/hello.md`:

```md
---
title: Hello
description: Our first editable post.
status: published
---

This story can be edited visually in Studio.
```

This enables discovery and fields. Your Astro route must still load and render posts. For static output, generate paths for new entries using the website's normal `getStaticPaths()` implementation. Apply your draft/status policy in both listings and routes; a schema default does not implement that policy.

## References and validation

Declare an `authors` collection with one local JSON/YAML document per author, then use Astro's native reference schema:

```ts
import { reference } from 'astro:content'

// Fields inside the posts schema:
author: reference('authors'),
relatedPosts: z.array(reference('posts')).optional(),
```

Studio offers pickers and stores identifier strings. Astro resolves those references when your website renders them. IDs follow Astro's slug behavior or your `generateId` callback. Keep callbacks deterministic, and verify nested paths and filename prefixes against actual rendered routes.

Saving identifier changes through Studio updates incoming declared local references as a recoverable operation. External file edits and relationships represented only in application code do not receive that migration. Deleting referenced entries can leave broken references after explicit confirmation; design templates to handle missing entries.

Studio renders forms from JSON Schema and also validates Astro frontmatter with the website's original schema, including asynchronous refinements. Drafts can retain incomplete values; validation blocks publishing. Validation does not write transformed/defaulted output back into the content and does not replace an Astro build. Image helpers validate source strings without executing Astro's asset pipeline.

## Choose a story format for your authors

| Format | Studio editing | Website setup |
| --- | --- | --- |
| Markdown `.md` | Visual and source editing | Normal Astro Markdown rendering |
| MDX `.mdx` | Source editing for the body; structured frontmatter fields | Install/configure the website's MDX integration |
| Markdoc `.mdoc` | Visual Markdown bodies with protected syntax; registered block tags get property forms | Install/configure Markdoc and register the website's tag renderers |
| JSON/YAML | Structured fields | Query and render the data in templates |

Mixed glob patterns such as `**/*.{md,mdx,mdoc}` are supported. The first declared extension determines the creation default. Use Markdown for ordinary writing; reserve MDX for content whose authors can work with imports, JSX, and expressions. Complex Markdoc expressions and control flow remain protected/source content.

## Make preview destinations explicit where needed

Astro content IDs do not define public URLs. Studio can infer a collection prefix from a single dynamic `.astro` page that directly queries it with `getCollection()` or `getEntry()`. Custom IDs, shared content, and custom routing may require a website-root `studio.preview.json`:

```json
{
  "content/posts/hello.md": "/journal/hello/",
  "content/authors/alice.json": "/team/alice/"
}
```

Use existing local website routes, including language prefixes and anchors where appropriate. When mapped YAML/JSON items all share one destination, new items can inherit it. Test new entries too. Authors can use **Set preview page** for unresolved items, but committed mappings make the initial experience more predictable across computers.

## Improve authoring

Follow the [shared authoring reference](authoring.md) for `.navigation.yml`, media libraries, filters, image formats, and `studio.components.json`.

Astro supports content bases outside `content/`, but the built-in website-wide media-library folder references are relative to `content/`. If you want those pickers, use the documented `content/photos` and `content/videos` arrangement. Ordinary Astro collection editing does not require moving all existing content.

## Working reference

Inspect [marpos-studio-astro-reference](https://github.com/ludulicious/marpos-studio-astro-reference), especially:

- [`src/content.config.ts`](https://github.com/ludulicious/marpos-studio-astro-reference/blob/main/src/content.config.ts): schemas, local loaders, references, and stable generated IDs.
- [`content/`](https://github.com/ludulicious/marpos-studio-astro-reference/tree/main/content): example documents and navigation/media configuration.
- [`src/pages/`](https://github.com/ludulicious/marpos-studio-astro-reference/tree/main/src/pages): the rendering side of the contract.
- [`studio.preview.json`](https://github.com/ludulicious/marpos-studio-astro-reference/blob/main/studio.preview.json): source-to-route mappings.
- [`studio.components.json`](https://github.com/ludulicious/marpos-studio-astro-reference/blob/main/studio.components.json): visual Markdoc property forms.
- Its README, Markdoc configuration, and Markdown rendering helpers: how saved authoring values become visible website behavior.

Run the reference using its README instructions to inspect the actual site. Reuse the integration patterns while preserving your website's design, URLs, and content identities.

## Current limits

Custom/remote loaders and `file()` loaders are not editable or valid local reference targets. TOML is unsupported. Whole-app bases (`.`), `public/` content bases, hidden/dependency/build directories, traversal, and symbolic-link content paths are outside the supported editing contract. Keep schema imports resolvable and deterministic.

Astro 6 has been verified with the reference site's Vue islands; the launcher recognizes Astro 5 and 6 CLI layouts. This is not a compatibility guarantee for every dependency combination. Your dependencies must run on Studio's managed Node runtime. Test your installed Studio version with the [verification checklist](verification.md).
