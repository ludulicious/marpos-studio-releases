# Verification, handover, and troubleshooting

[Documentation home](README.md)

## Developer acceptance checklist

Run this on the Studio version and platform your authors will actually use. Use a local checkout or a test repository for changes that should not go live.

1. **Open:** Install website dependencies with the project's package manager and lockfile. Choose **Open local repository**, selecting the app folder for a monorepo. Check that expected collections appear and unsupported sources have been addressed.
2. **Fields:** Open representative content in each collection. Verify required/optional fields, nested data, dates, enums, media, and references. Try a missing required value and confirm the feedback points to the right field.
3. **Create:** Add a record where creation is intended. Save/reopen it and inspect the generated file and identifier. Confirm that the actual website renders the new record and includes it in the intended listing/navigation. Check each language and nested-folder pattern.
4. **Write:** Edit ordinary prose visually. Switch to source and back. Verify custom components, slots, code samples, and unsupported syntax are preserved. For MDX, verify source editing and actual Astro rendering.
5. **Preview:** Preview every content type, including shared settings and new records. Check custom IDs, locale prefixes, anchors, and base-path deployments. Make an unsaved edit, preview it, and discard it; confirm the saved file is restored.
6. **Media:** Upload an image, supply alt text, save/reopen it, and verify both content and public asset paths. Exercise configured image formats, full-image/thumbnail crops, visibility, ordering, and video insertion. Check responsive output on the real website.
7. **Relationships:** Select references and resolve them in the website. In disposable test content, change an identifier through Studio and inspect dependent records. Check deletion feedback for referenced entries and the website's missing-reference behavior.
8. **Local refresh:** Change a schema, route, or Studio metadata file externally. Save/discard active editor changes and refresh local content. Reopen the story for component/image-format definition changes. Confirm the new configuration appears.
9. **Build:** Run the website's existing lint/type/content checks and production build. Check public routes, draft/visibility rules, references, images, and deployment subpaths in the built output. Editor validation is not a production build.
10. **Online workflow:** In a test repository connected through **Add project**, save an online draft, reopen it, review the diff, and publish only an intended test change. Confirm that the normal deployment runs and the live website reflects it. Local-repository mode uses your own Git tools and hides online publishing controls.

Record the installed Studio version/platform, website framework version, commands/results, tested routes, and any manual checks still outstanding. Do not mark Studio interactions as passed based only on a successful website build.

## Deployment and developer updates

Publishing updates GitHub's default branch. Your normal hosting/deployment process determines when changes become live. Configure that process before promising authors automatic website updates.

Developers ship schema, route, component, and Studio-manifest changes through their normal repository workflow. In a GitHub-connected Studio project, authors use **Get latest website** to combine newer website changes with their draft and refresh content structure. In local mode, Studio watches the existing checkout and waits for active edits to be saved/discarded before refreshing.

For optional deployment confirmation, authors can configure environments under **Review & publish → Website updates → Manage environments**. Subsequent online saves include `public/marpos-deployment.json`. Deploy and serve that file beneath the configured website URL, without authentication or redirect, and bypass CDN caching for it. A successful marker check identifies a tracked revision; it does not replace hosting-provider build logs or certify unrelated developer changes that retained an older marker.

## Handover notes for the author

Provide a short website-specific README explaining:

- Which collection edits which part of the website, and which fields are required.
- How to create a page and where it will appear; any navigation step still required.
- How draft/status/visibility fields affect this website.
- Image dimensions, crop behavior, captions/credits, and permitted media sources.
- Which components are available and which content needs developer help.
- How to preview shared content, translated pages, and newly created entries.
- How to review/publish and how long deployment normally takes.
- Who to contact with Studio version, exact error, content path, and steps to reproduce.

Have an intended author complete one real editing task without developer assistance before handover.

## Troubleshooting

| Symptom | Check and action |
| --- | --- |
| Website is not recognized | Check the selected app's `package.json` dependencies and framework-specific content configuration location. Install dependencies for local mode. |
| Astro collection cannot be edited | Replace unsupported custom/remote/`file()` loading for editable content with a supported local `glob()` collection. Check explicit extensions and an allowed base directory. |
| Nuxt collection is read-only or rejected | Check for remote/custom source directories, multiple sources, inherited component schemas, unsupported validators, and overlapping collections. |
| Add content is unavailable | A fixed source only edits its declared file. Use a suitable wildcard source if authors should create entries. |
| Field is missing or uses an unsuitable control | Declare the field explicitly in the native schema; verify editor metadata against the installed dependency API. Refresh and reopen. |
| New content does not appear on the website | Verify routes, query filters, listings/navigation, static path generation, and draft/status logic. Studio does not create website routes. |
| Preview opens the wrong page or a 404 | Check the actual website route and locale; add `studio.preview.json` mappings or a folder fallback. Confirm newly created items too. |
| Preview fails to start | Run the website normally to identify dependency/configuration errors. Check external services and Studio's preview connection settings. Close a process occupying Studio's reserved port. |
| Component has no property form | Check name/syntax and JSON validity in `studio.components.json`, supported scalar properties, and Markdown slots. Reopen the story. Implement the website renderer separately. |
| Image size choice has no visible effect | Verify the Markdown renderer preserves/translates attributes and the website supplies matching CSS. |
| Media picker is empty | Check `content/.navigation.yml` library paths, record collection patterns, schemas, and saved local records. Folder references are relative to `content/`. |
| Hidden records still appear publicly | Implement visibility filtering in the website's queries/templates. Studio's field alone does not filter the website. |
| Save succeeds but publish fails | Review all changed content validation issues and reference targets. Incomplete drafts can be saved; invalid changed content blocks publication. |
| GitHub update is accepted but website looks old | Inspect hosting/deployment results and caching. Acceptance in GitHub and a live deployment are separate events. |

For reproducible integration problems, [open an issue](https://github.com/ludulicious/marpos-studio-releases/issues) with versions, sanitized configuration/content examples, expected/actual behavior, and reproduction steps. Never include credentials or private client content.
