# Source intake

Read the branch that matches what the owner brought. Every branch ends with the same two records: the coverage table and the asset inventory.

## Ask the owner once

Some intake depends on the owner. Collect these needs while you read the source, then send one request:

- **A design export upload,** when they have a project in Claude Design, Anthropic's design tool, and no Design Plan exists yet.
- **The importer choice,** when the source is WordPress or Webflow. See [the built-in importers](#the-built-in-importers).
- **What a crawl cannot see:** where forms deliver, analytics and marketing tags, logged-in or gated areas, site search, checkout or booking flows, listings fed from another system, downloadable files, pages hidden from search engines, and other languages.
- **Files and facts you could not reach:** brand files, original images, and copy for placeholder sections.

Continue the parts of intake that do not depend on the answers. Write the plan after they arrive.

## A design with a Design Plan

A Design Plan exists when the owner uploaded a design export in the admin. Check with `list_design_imports`.

If there is none and the owner has a Claude Design project, ask them to export the full **"all assets" .zip** from Claude Design, upload it under **Pages > Import > Design** (`/_/manage/pages/import/design`), and select **Build design plan**. Building the plan spends a small amount of the site's AI credit. The upload is an owner action.

Then work from the plan:

1. Call `get_design_plan` with the `import_batch_id` for this design. It returns outstanding items by default. Larger items arrive as **Steps**, child rows that carry a `parent_id`: one per color, one per page region.
2. Fold the items into the migration plan. Reference each Design Plan item id from a ledger row, so there is one ledger and the Design Plan stays the progress view the owner sees in their admin.
3. Apply each item with the tools for its kind:

| Item kind        | Tools                                                                                        |
| ---------------- | -------------------------------------------------------------------------------------------- |
| Font             | `add_site_font`                                                                              |
| Theme palette    | `create_theme_version`, then `publish_theme_version` and `activate_theme`                    |
| Header or footer | A content block saved and published, then `set_site_header_block` or `set_site_footer_block` |
| Page             | `create_page` or `save_page_draft`, then `publish_page_version`                              |

Registering a font, activating a theme, binding a block and publishing a Page are live changes. They follow the publish scope and the plan's go-live order. Saving a theme version changes nothing visitors see. Publishing one changes nothing either, unless that theme is already the active one, in which case it goes live at once.

The lead marks an item with `complete_design_plan_item` when its change has reached the state the approved scope allows: live under a publish scope, saved as a draft under drafts only. Under drafts only, tell the owner that "done" in their Design Plan means drafted. Mark Steps one at a time. Completing a parent also completes its remaining Steps, so mark a parent only when all of its Steps are truly done.

What the plan gives you, and what it leaves to you:

- A page Step describes a region: its extracted headings, copy, links and image references, plus candidate component types. It never names props. Resolve the real shape with `get_component_schema`.
- Applying the palette sets the base shade of each color. Plan the other shades as a theme decision.
- Colors the platform could not place arrive as Steps that carry the raw color and where the design used it. Those are owner decisions; put them in the approval round with your recommendation.
- Header and footer Steps for logo, navigation links and call to action are content requirements to verify. Binding a header is still one action.
- Fonts already on the brand are not proposed again. Check each proposed color against the theme before you apply it.
- The owner, and the platform's built-in assistant (the Concierge), can work the same plan. Confirm you are the only executor, or split the items, before you start.

## A design supplied as files

Use this branch for a Figma export, an HTML and CSS mockup, screenshots, a PDF, another builder's export, or a Claude Design export the owner prefers not to upload.

1. List every screen and treat each as a source page.
2. Extract the design system: font families and weights, each color with where it is used, radius and spacing habits, the logo, and the header and footer structure.
3. Break each screen into regions and record headings, copy, links and image references per region.
4. Flag placeholder copy and stock imagery. The plan must say where the real content comes from.

Design exports are often fixed-width and absolutely positioned. Never copy pixel positions or sizes. Rebuild each region as responsive components with theme tokens, and judge the result by how it reads at desktop and mobile widths.

For an owner who is still designing in Claude Design, mention the **Download design guide** button on the same Import screen. Adding that bundle to their Claude Design project makes later designs use the site's brand and components.

## An existing website

1. **Find every page.** Start with `probe_url` for a first look; it reports whether the site is WordPress and saves nothing. Then read the sitemaps, navigation, footer, internal links and pagination. `web_map` returns a URL list; keep that list in your working files. `web_crawl` and `web_extract` save page content to the Conversated site, where `get_research_result` reads it back, so nothing is crawled twice. Each tool caps how much it processes per call: read the limits in its schema and page through a large site. The session's own browser or fetch tools work as well.
2. **Record each page** in the coverage table with its purpose, title, description, headings, body copy, links, images and forms. Separate canonical URLs from query-string variants and from redirects the old site already serves. Note the URL pattern of posts.
3. **Capture the look** when the move is meant to be faithful: fonts, colors with their usage, spacing, logo and the distinct layout types. `extract_logo_from_website` ranks logo candidates. `import_theme` can turn a CSS extract from the old site into a draft theme to refine. Take reference screenshots of each layout type with the session's own browser; `capture_page_screenshot` cannot capture another site.
4. **Group pages into layout types.** Pages that share a structure share an exemplar and a worker packet.
5. **Name what plugins produced.** Sliders, galleries, plugin forms and shortcodes reach you as rendered HTML or as raw shortcode text. Map each to a component or record a gap. Never paste a shortcode or a plugin's markup into a Page.

### The built-in importers

For a WordPress site with a public REST API, or a Webflow site with an API token, the platform has its own importer. It brings published pages, posts or CMS items in as Pages for the owner to review and publish, and can be run again for changed content. The owner runs it under **Pages > Import** (`/_/manage/pages/import/wordpress`, or the Webflow tab there), which links to its connection settings.

Recommend it when there are many posts and faithful bulk transfer matters more than bespoke layout. Conversion can use platform AI, so raise any AI wallet cost together with the choice.

Because the owner runs it and it creates Pages, settle it in the intake request. If they use it, they run it before you write the plan. Take those rows of the coverage table from `list_pages_admin`, and plan your work on them as review: restyle to the design contract, complete metadata, check slugs and add redirects.

## A new design on an existing site

The design governs look and structure. The existing site governs content, URLs and search standing.

- Build the coverage table from the site, then map each page to a design screen or layout type.
- A design screen with no source page needs content from the owner. A source page with no design screen needs a layout decision. Put both in the approval round.
- Keep existing slugs unless the owner chooses otherwise.
- When the design also has a Design Plan, take fonts, colors, header and footer from the plan. Treat each of its page items as a layout type, not as a Page to create: its copy is the design's sample text. Build the real Pages from the site's content in that layout, and mark a page item once the Pages of its layout type have reached the approved state.

## Records every branch produces

Coverage table:

| Source URL or screen | Purpose | Layout type | Destination slug | Destination type | Page and version ids | Status | Redirect and evidence |
| -------------------- | ------- | ----------- | ---------------- | ---------------- | -------------------- | ------ | --------------------- |

Destination type is one of: Page, post, module data, merged into another Page, redirect only, or dropped. Dropping or merging a page needs the owner's approval.

Asset inventory:

| Source URL | Kind | Dimensions | Used on | Alt text | Import, hotlink or replace | State |
| ---------- | ---- | ---------- | ------- | -------- | -------------------------- | ----- |

Check an image's real dimensions before planning it for a full-width placement. Mark every asset served from the source domain: a hotlink to it stops working when that domain moves to the new site.
