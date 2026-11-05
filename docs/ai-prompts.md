# AI prompts for website integration

[Documentation home](README.md) · [Astro guide](astro.md) · [Nuxt Content guide](nuxt-content.md)

Open your **website repository** in your AI coding assistant, then copy the appropriate prompt below. Studio source access is unnecessary. Replace bracketed context with your website's details where known; the assistant should inspect the repository for the rest.

Reference implementations: [Astro source](https://github.com/ludulicious/marpos-studio-astro-reference) and [Nuxt Content source](https://github.com/ludulicious/marpos-studio-nuxt-reference) ([reference app](https://ludulicious.github.io/marpos-studio-nuxt-reference/)). Each framework prompt below tells the assistant what to inspect.

These prompts request website changes, checks, and a handover report. They do not authorize publishing or deployment. If the assistant cannot access a linked document, give it that document's text or a local copy of this public documentation before it invents configuration.

## Astro: adapt an existing website

```text
Adapt this existing Astro website so a nontechnical author can use it comfortably
in Marpos Studio. Work in this website repository; Studio source is not available.

Context:
- Website/app folder: [discover it, or specify a folder]
- Author-editable areas: [describe them, or infer candidates and state assumptions]
- Installed Studio version/platform: [if known]
- Routes, content identities, design, and deployment behavior should be preserved.

Read the public integration documentation before editing:
https://github.com/ludulicious/marpos-studio-releases/blob/main/docs/README.md
https://github.com/ludulicious/marpos-studio-releases/blob/main/docs/astro.md
https://github.com/ludulicious/marpos-studio-releases/blob/main/docs/authoring.md
https://github.com/ludulicious/marpos-studio-releases/blob/main/docs/verification.md

Inspect this working Astro reference repository, including its README:
https://github.com/ludulicious/marpos-studio-astro-reference
Look at src/content.config.ts, content/ and its .navigation.yml files,
studio.preview.json, studio.components.json, routes/templates, Markdoc
configuration, and Markdown/media rendering helpers. Clone/run it separately
using its own README if you need to inspect the rendered reference site.
Treat it as a source of integration patterns, not a template to copy wholesale.

First inspect this site's installed Astro version, integrations, schemas,
loaders, content, routing, references, media, package manager, and deployment.
Report concrete compatibility blockers separately from optional improvements,
with the affected files and intended changes. Then implement the supported,
focused changes needed for the author-editable areas.

Requirements:
1. Use native local glob() collections with explicit supported extensions and
   resolvable schemas. Keep existing collection IDs, record IDs, URLs, and
   incoming references stable. Do not migrate remote/custom-loader content or
   rename/move records without first identifying the consequences and obtaining
   agreement on a migration when needed.
2. Expose meaningful content fields with constraints, helpful descriptions,
   defaults, enums, nested image/alt data, and native reference() relationships.
   Move author-editable hardcoded text into suitable collections only where
   necessary, and update templates to render it without changing the design.
3. Ensure new records actually render and appear in intended website listings.
   Implement the site's draft/visibility and ordering rules in its queries/routes.
4. Prefer Markdown for visual writing. Keep existing MDX source intact and
   explain its source-only editing. Use registered Markdoc blocks for visual
   component forms only where the site's integrations support them. Never
   convert MDX/Markdoc or add integrations merely to match the reference.
5. Add useful navigation labels, editing views, filters, link suggestions,
   and reliable preview mappings for custom routes/shared data where needed.
6. If useful for this site's authors, configure content/ photo/video libraries,
   public media, thumbnail schemas, and image formats. Implement their actual
   renderer/CSS behavior, alt text, visibility, and sorting. If visual components
   are needed, add studio.components.json and matching website tag renderers.
   The manifest alone does not register an Astro renderer.
7. Preserve the package manager/lockfile and deployment base-path behavior.
   Do not upgrade framework dependencies unless required and explained.

Run the existing relevant checks and production build. Verify representative
routes, references, new-entry rendering, and media output. Add a short
website-specific author/developer handover guide describing collections,
creation, preview destinations, components, media, and publication behavior.

Report changed files, checks actually run/results, remaining limitations, and
the Studio interactions still needing manual verification from the public
checklist. If you cannot operate Studio, say so; do not claim those checks passed.
Do not commit, push, publish, deploy, or edit the reference repository.
```

## Nuxt Content: adapt an existing website

```text
Adapt this existing Nuxt Content website so a nontechnical author can use it
comfortably in Marpos Studio. Work in this website repository; Studio source
is not available.

Context:
- Website/app folder: [discover it, or specify a folder]
- Author-editable areas: [describe them, or infer candidates and state assumptions]
- Installed Studio version/platform: [if known]
- Preserve existing URLs, languages, content identities, design, and deployment.

Read the public integration documentation before editing:
https://github.com/ludulicious/marpos-studio-releases/blob/main/docs/README.md
https://github.com/ludulicious/marpos-studio-releases/blob/main/docs/nuxt-content.md
https://github.com/ludulicious/marpos-studio-releases/blob/main/docs/authoring.md
https://github.com/ludulicious/marpos-studio-releases/blob/main/docs/verification.md

Inspect this working Nuxt Content reference repository, including its README:
https://github.com/ludulicious/marpos-studio-nuxt-reference
Explore the rendered reference app:
https://ludulicious.github.io/marpos-studio-nuxt-reference/
Look at content.config.ts, content/ and its .navigation.yml files,
studio.preview.json, studio.components.json, studio.images.json,
app/pages/, app/composables/useReferenceContent.ts,
app/components/content/Callout.vue, app/components/content/StorySections.vue,
and app/components/prose/ProseImg.vue. Trace the schemas and editor metadata
through saved content to actual rendering, including named MDC slots, block tabs,
reference resolution, media, and image sizes. Clone/run the reference separately
using its README if the hosted app cannot be accessed. Treat it as integration
patterns, not a required design or a reason to upgrade this site's dependencies.
Keep deployment base paths separate from Studio's website-root preview mappings.

Inspect installed Nuxt/Nuxt Content versions, collection configuration, source
patterns, schemas/editor APIs, content components, queries/routes, i18n, media,
package manager/workspaces, and deployment. The documented contract targets
Nuxt Content 3; if this site uses another major version, report that migration
requirement and obtain agreement before a major-version migration. Nuxt Studio
is optional and must not be installed solely for Marpos Studio.

Report concrete blockers separately from optional authoring improvements, with
affected files and intended changes. Then implement supported, focused changes:
1. Use a root content.config.* with explicit page/data collections, one supported
   local source per collection under content/, and non-overlapping patterns.
   Preserve identifiers, routes, locale prefixes, and existing relationships.
   Do not migrate remote/custom sources without agreeing on the migration.
2. Declare author-editable fields explicitly with supported schemas, constraints,
   defaults, enums, nested data, and helpful descriptions. Replace inherited
   component-field assumptions with explicit fields where appropriate.
3. Add reference, link, and media editor metadata through APIs supported by the
   installed Nuxt Content/Zod versions. Do not blindly paste .editor()/property()
   APIs that this site does not support. For references, use stable existing
   valueField identifiers and implement matching website queries.
4. Ensure routes/templates render edited and newly created records and include
   them in the intended navigation/listings. Implement draft/status/visibility
   and sorting in the website; do not assume Studio field names implement them.
5. Add useful folder labels, filters, editing views, link suggestions, and
   studio.preview.json mappings where route inference is insufficient.
   Test shared content, custom routes, new records, and each language.
6. Where useful, register visual MDC component forms in studio.components.json
   and implement matching Vue content components with editable Markdown slots.
   Use scalar props and preserve unsupported dynamic expressions/source syntax.
   Add studio.blocks tabs only when the actual page's MDC structure and renderer
   match; preserve the operational frontmatter in the collection schema.
7. Where useful, configure content/ photo/video libraries, public media,
   thumbnails, and image formats. Implement responsive rendering/CSS, alt text,
   credits, visibility, and ordering in the website. Avoid requiring developer-
   only metadata for ordinary new uploads.
8. Preserve the package manager/lockfile, workspace layout, and deployment.
   Do not introduce unnecessary framework upgrades or unrelated redesigns.

Run existing relevant checks and the production build. Verify representative
rendered routes, relationships, new-entry behavior, locales, and media output.
Write a website-specific author/developer handover guide explaining collections,
creation, preview destinations, components, media, and publication behavior.

Report changed files, checks actually run/results, remaining limitations, and
Studio interactions still needing manual verification from the public checklist.
If you cannot operate Studio, state that instead of claiming those checks passed.
Do not commit, push, publish, deploy, or edit the reference repository.
```

## Review the authoring experience

Use this follow-up once basic integration works, in either framework:

```text
Review this website's Marpos Studio authoring experience against:
https://github.com/ludulicious/marpos-studio-releases/blob/main/docs/authoring.md
https://github.com/ludulicious/marpos-studio-releases/blob/main/docs/verification.md

Trace an author's tasks: edit the home page, create a post, select a related
entry, upload/crop an image, insert a supported component, preview a translated
or shared item, and prepare a publication. Skip tasks that do not apply and
explain why.

For each applicable task, inspect the schema, configuration, saved content, and
actual website rendering. Identify missing controls, confusing identifiers,
required technical fields, unreliable previews, and values the website ignores.
Prioritize blockers, then high-impact improvements. Make focused reversible
improvements consistent with the existing framework and design. Run relevant
website checks and build, and update the handover instructions.

Separate observed Studio behavior from code-based expectations. List desktop
interactions still needing verification. Do not invent settings, claim a build
proves the desktop workflow, or commit/push/publish/deploy.
```
