# Prepare your website for Marpos Studio

This documentation is for developers who own an Astro or Nuxt Content website and want other people to edit it comfortably in Marpos Studio. Everything described here belongs in your website repository; Studio source access is unnecessary.

Documentation baseline: **5 November 2026**. Use the release notes for your installed platform/version, and complete the verification checklist on that version before handing the website over. These guides describe the current integration contract; they do not establish a minimum Studio version for every feature.

## Choose your starting point

| Your website | Start here | Ask an AI coding assistant |
| --- | --- | --- |
| Astro with native content collections | [Astro guide](astro.md) | [Astro prompt](ai-prompts.md#astro-adapt-an-existing-website) |
| Nuxt with Nuxt Content 3 | [Nuxt Content guide](nuxt-content.md) | [Nuxt prompt](ai-prompts.md#nuxt-content-adapt-an-existing-website) |
| Already editable, but awkward for authors | [Authoring configuration](authoring.md) | [Authoring review prompt](ai-prompts.md#review-the-authoring-experience) |
| Ready to hand over | [Verification and troubleshooting](verification.md) | Use its checks as acceptance criteria |

Working reference integrations are available for both frameworks:

- **Astro:** [source repository](https://github.com/ludulicious/marpos-studio-astro-reference).
- **Nuxt Content:** [reference app](https://ludulicious.github.io/marpos-studio-nuxt-reference/) and [source repository](https://github.com/ludulicious/marpos-studio-nuxt-reference).

You and your AI assistant can inspect the repositories and run them locally using their README instructions. Both include setup and acceptance steps. They demonstrate integration patterns rather than a required website design.

## What Studio does, and what your website must do

Studio discovers supported local collections, turns schemas into forms, edits content and public media, and previews changes using the actual website. It can save online drafts and publish through GitHub. Your website retains responsibility for rendering, routing, navigation, filtering, sorting, and deployment.

For example:

- Adding a post in Studio creates a content file. Your website must already render new posts and include them in its listings where appropriate.
- A `visible` or `status` field affects the live website only if your queries/templates respect it. Its name does not implement publication rules.
- Image format choices need matching website rendering and CSS.
- A preview mapping points to an existing route. It does not create a page.
- Shared authors, categories, and related entries need website queries that resolve their identifiers.

Put author-editable words and data in collections. Text hardcoded in `.astro` or `.vue` templates does not become editable merely by opening the website in Studio.

## Get it working first

1. Follow your framework guide to expose supported collections with explicit schemas and non-overlapping local sources.
2. Ensure an existing content item and a newly created item render through the website's normal routes.
3. Install the website's dependencies, then choose **Open local repository** in Studio. Select the website folder when using a monorepo.
4. Edit, preview, save, reopen, and verify the resulting file. Local projects write directly to your working directory; use your normal Git tools for commits and pushes.

For GitHub editing, the author signs in, chooses **Add project**, selects a repository they can write to, and selects the website folder. Studio downloads its own checkout and installs dependencies. Keep the package manager declaration and lockfile reproducible. Monorepos can use workspace installation, but editable/publishable content is scoped to the selected website.

Studio's local preview uses its own managed development server. Do not hardcode the port of your usual development server into content or preview configuration. External services used by the website must be reachable; configure supported service settings under **Website → Preview connections** when needed.

## Then make it pleasant

Use [authoring configuration](authoring.md) to add useful folder names, descriptions and sensible defaults, reference pickers, media libraries, filters, predictable preview destinations, and visual component forms. Add only controls whose saved values your website actually uses.

Framework configuration, component implementations, and Studio manifests are developer-owned files. Ship those through your normal development workflow. Authors edit the content they expose; Studio's content publishing flow is not a general website-code deployment tool.

Finish with the [handover checklist](verification.md), including a real build and an author walkthrough. A working form alone does not prove a working integration.
