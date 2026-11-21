# The Conversated ecosystem

Read this before planning. It maps what a Conversated site contains, which tool handles which job, and what only the owner can do in the admin. Rosters and schemas change: the tools the connected server lists and each tool's live schema outrank this page.

## What a site contains

- **Site.** One website plus its admin. Features come in modules, such as content, CRM, marketing, automations and hospitality, that are switched on per site. A disabled module's tools and components are absent or refuse, so discover them instead of assuming.
- **Addresses.** The platform gives every site an address of its own, the **Access Domain**, such as `<your-site>.conversated.ai`. That host serves the public site, the admin under `/_/`, and the MCP connection, so it is the site's own host in this skill. The owner can connect a **custom domain** to the same site. The first one verified becomes the primary domain, the address used for search engines and sharing.
- **Pages and versions.** A Page is a stable identity with a slug. What visitors read lives in versions: save a draft, then publish that exact version. Blog posts are Pages of type `post`.
- **Renderable content.** Page content is a JSON tree of components, each a `type` with its props inline. You compose with components; you do not hand-write a Page's HTML, CSS or scripts. A source section becomes the closest component; a section with no match is a gap to resolve in the plan.
- **Publish gate.** A version whose blocks are missing required fields is stopped at publish. Saves report the gate, so fix the fields before asking to publish.
- **Content blocks.** Reusable named blocks. The site header and footer are content blocks bound to the site layout.
- **Theme and brand.** A theme is a set of tokens for palette, typography and radius that the platform compiles to CSS. Fonts are registered on the brand. Content refers to tokens through utility classes instead of raw color values. A site shows visitors a dark page by default until the owner changes its default color mode.
- **Media library.** Imported images are stored, optimized and described by AI. Importing spends from the AI wallet. The library holds images and video; it has no place for documents such as PDFs.
- **Embeds.** A third-party widget goes on a Page through the `iframe` component, which takes a URL, or the `embed` component, which takes the vendor's embed code. Confirm both with `list_components`. Site snippets in the admin are site-wide scripts, not Page content.
- **Site metadata.** Site-wide SEO defaults, organization details, social links and the machine-readable introductions.
- **Redirects.** A site-level map from old paths to new ones. It takes effect as soon as it is saved.
- **Forms, leads and CRM.** The Form component captures each submission as a Lead and matches it to a Contact. Automations react to submissions.
- **Module data.** Some of what visitors see is not Page content. Rental units sync from a property-management system and render through live components; contacts live in the CRM. Rebuilding these as static Pages creates content that goes stale. Decide from the site: when `list_units` returns units, or the owner uses a property-management system, listings arrive by sync. Otherwise rooms, services and similar offerings are ordinary Pages.
- **Design Plan.** The platform's digest of an uploaded design: a tracked list of fonts, colors, chrome and pages to apply. It proposes; an executor such as you applies it.
- **AI wallet.** Site-level funds for AI that the platform runs. Your own tokens are separate, and only the owner can see the balance.

## Which tool does which job

| Job                              | Tools                                                                                                                                                        | Notes                                                                                                       |
| -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------- |
| Confirm the site                 | `get_site_info`                                                                                                                                              | Returns the site's public overview                                                                          |
| See what exists                  | `list_pages_admin`, `get_page_for_editing`, `read_page_content`, `list_content_blocks`, `get_site_layout`                                                    | The composed view of `read_page_content` includes chrome                                                    |
| Discover components              | `list_components`, `get_component_schema`                                                                                                                    | Confirm prop names before composing                                                                         |
| Check for synced rental units    | `list_units`                                                                                                                                                 | Present only when the hospitality module is on                                                              |
| Build Pages                      | `create_page`, `save_page_draft`, `list_page_versions`, `diff_page_versions`, `restore_page_version`                                                         | `create_page` makes an unpublished Page; pass its content inline                                            |
| Start from a template            | `list_page_templates`, `create_page_from_template`                                                                                                           |                                                                                                             |
| Rename or re-scope a Page        | `update_page_settings`                                                                                                                                       | A slug change records its own redirect; see [authoring](authoring.md#slugs-and-redirects)                   |
| Publish                          | `publish_page_version`, `schedule_page_version`, `cancel_page_schedule`                                                                                      | Publishes one exact version. Incomplete blocks stop the first call: fix them, or get the owner's decision   |
| Header, footer and shared blocks | `create_content_block`, `save_content_block_draft`, `publish_content_block_version`, `set_site_header_block`, `set_site_footer_block`                        | A binding renders the block's published version on every Page                                               |
| Theme and fonts                  | `list_themes`, `get_theme`, `create_theme`, `copy_theme`, `create_theme_version`, `import_theme`, `publish_theme_version`, `activate_theme`, `add_site_font` | Three separate acts. Activating changes the live look, and so does publishing a version of the active theme |
| Brand reference                  | `get_brand_identity`, `get_brand_voice`                                                                                                                      |                                                                                                             |
| Logo and site metadata           | `extract_logo_from_website`, `import_inline_svg`, `set_site_logo`, `update_site_metadata`                                                                    | Both setters apply at once. `update_site_metadata` merges by default; keep it that way                      |
| Media                            | `list_media_folders`, `list_media_assets`, `image_search`, `import_media_from_urls`, `rename_media_assets`                                                   | Search the library before importing                                                                         |
| Redirects                        | `list_redirects`, `upsert_redirects`, `delete_redirects`                                                                                                     | For old paths that no Page serves. Run `upsert_redirects` with `dry_run` first                              |
| Read the source website          | `probe_url`, `web_map`, `web_crawl`, `web_extract`, `get_research_result`, `list_research_history`                                                           | Crawled and extracted content is saved to the site; `probe_url` and the `web_map` URL list are not          |
| Work a Design Plan               | `list_design_imports`, `get_design_plan`, `complete_design_plan_item`                                                                                        |                                                                                                             |
| Look at the result               | `capture_page_screenshot`                                                                                                                                    | Accepts only URLs on the connection's own host, loaded as an anonymous visitor, so drafts do not show       |
| Follow background work           | `list_active_workflows`                                                                                                                                      | Imports return before they finish. A job leaves the list when it ends or fails, so confirm the result       |
| Automations, email and surveys   | `list_flow_capabilities`, `create_flow`, `add_flow_step`, `publish_flow`, the email template tools, the survey tools                                         | When the module is enabled and the plan calls for them                                                      |
| Report a gap                     | `submit_feature_request`, `submit_bug_report`, `submit_feedback`, `check_request_status`                                                                     |                                                                                                             |

Leave alone the tools that serve the platform's own chat: `watch_workflow` and the tools that render in-chat controls, such as `render_media_picker` and `render_upload_drop_target`. Do not generate images; new imagery comes from the owner.

## How to navigate well

1. **Read before you write.** Take IDs, slugs and block ids from list and read tools. Never construct them.
2. **Trust schemas over memory.** Call `get_component_schema` for each component type you use, and read each tool's own input schema for limits and options.
3. **Draft, read back, then publish the exact version.** A saved draft is not live, and a published Page is not verified until you look at it.
4. **Know the mechanics before you write.** [Authoring](authoring.md) holds save, patch, publish, rename, redirect, shared block and media behavior. [Styling](styling.md) holds theme tokens, classes, color modes and renderer behavior. This page only tells you where things are.
5. **Let the platform finish.** New styles appear a little after a publish. Confirm the stylesheet loaded before diagnosing a layout defect.
6. **Hand the owner full links.** Tools return paths such as `editor_url`. Put the Access Domain's origin in front before giving one to the owner.
7. **Treat a gap as a gap.** Use the closest supported composition, note the difference in the plan, and file `submit_feature_request`. Never approximate with content that misstates the source.

## What only the owner can do in the admin

These need the owner in the admin. Put each one in the plan as an owner action with its location.

| Need                                                 | Where in the admin                                                                                                  |
| ---------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| Connect a custom domain                              | **Settings > Domains** (`/_/settings/domains`), plus DNS records at their domain provider                           |
| Upload a design export, or download the design guide | **Pages > Import > Design** (`/_/manage/pages/import/design`)                                                       |
| Run the WordPress or Webflow importer                | **Pages > Import** (`/_/manage/pages/import/wordpress`, or its Webflow tab), which links to the connection settings |
| Analytics and marketing tags, other vendor scripts   | **Settings > Integrations**: Google Tag Manager and the other built-in tags, or **Site snippets**                   |
| Property-management and other integrations           | **Settings > Integrations** (`/_/settings/integrations`)                                                            |
| AI wallet, budget and billing                        | **Billing > AI Wallet & Budget**                                                                                    |
| MCP keys and connected apps                          | **AI & LLMs > MCP** (`/_/ai/mcp`)                                                                                   |
| Review a draft theme or Page visually                | **Settings > Themes**, and each Page's Design editor                                                                |
| Default light or dark mode, and the header's toggle  | **Branding** (`/_/theme/branding`): **Default color mode**                                                          |
| Rebuild the site's styles so that drafts show styled | **Rebuild styles** on the Pages list (`/_/manage/pages`)                                                            |
