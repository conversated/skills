# Authoring mechanics

Read this before you create or change Pages, shared blocks, media or redirects. It covers how the tools behave across calls, which a single tool description does not show. Where this page and a tool's live schema disagree, the schema wins.

## The content tree

Page and block content is an array of nodes. A node has a `type`, an `id`, and that component's props beside them. Children go in `content`. Nothing goes under `params`.

```json
[
	{
		"id": "<uuid>",
		"type": "section",
		"tagName": "section",
		"classNames": "mx-auto flex w-full max-w-screen-lg flex-col gap-6 px-4 py-16",
		"content": [
			{ "id": "<uuid>", "type": "heading", "level": "1", "text": "Lakefront cabins" },
			{
				"id": "<uuid>",
				"type": "text",
				"text": "<p>Twelve cabins, open May to October. <a href=\"/cabins\">See them all</a>.</p>"
			},
			{
				"id": "<uuid>",
				"type": "image",
				"src": "/media/w_1600,c_limit,q_auto,f_auto/<image-path>",
				"alt": "Cabin deck at sunset",
				"size": "full"
			},
			{
				"id": "<uuid>",
				"type": "button-link",
				"text": "Check availability",
				"link": "/availability"
			}
		]
	}
]
```

- **Ids.** Give every new node its own id, a fresh UUID. Later patches address nodes by id, so reuse the ids that a read returns.
- **Props.** `get_component_schema` is the authority for each type. Names differ between components: a button's label is `text` and its destination is `link`, a heading's `level` is a string from `"1"` to `"6"`, and an image needs `alt`. A required prop left empty stops publication.
- **Defaults that surprise.** An image with no `size` renders as a small cropped square, so set `size` to `full` for anything meant to fill its container. An image with no `src` shows a placeholder picture. A heading with no `level` becomes a level 2.
- **Body copy.** The `text` component takes HTML in its `text` prop: paragraphs, lists, links and emphasis.

## Create and save drafts

- **Create with content.** `create_page` makes an unpublished Page and its first draft. Pass `content` and `metadata` in the same call. Set `type` to `post` for a blog post.
- **Slugs** are stored lowercase with a leading slash. A slug that another Page holds is refused, and paths under `/_/` are reserved for the admin.
- **Each save is a new draft version.** `save_page_draft` returns its `version_id`. A Page keeps one current draft and archives the one before.
- **Patch is the default.** It merges the nodes you send into the current version by id, and keeps every node and field you leave out. Send only what changes. Place a new or moved node with `insert_before` or `insert_after` and a sibling's id. A patch the server cannot match safely is refused without saving, and the refusal gives the reason.
- **Replace deletes.** `merge_strategy: "replace"` saves exactly the tree and metadata you send and drops everything else. It is the only way to remove a node. Build it from a complete current copy read with `read_page_content`.
- **Never save an empty content array.**
- **Pages without ids.** Some Pages, such as imported ones, hold nodes without ids. Save once with `metadata` and no `content` to give every node an id, then read the full tree with `read_page_content` before you patch.
- **Read the save result.** It carries the version id, a summary of the saved top-level nodes, an editor path, and a `publish_gate` entry when a node is missing a required field. A draft always saves, so the gate is the only sign that publication will stop.

### Metadata

Keep a Page's search and sharing metadata in its drafts, through `metadata` on `create_page` and `save_page_draft`: `title`, `description`, `canonical`, `robots`, and the `og_*` and `twitter_*` fields. Under patch, the keys you send merge into the current ones.

`update_page_settings` changes a Page's name, slug and visibility. Set metadata through the drafts, not through this tool.

Never put an email address in a public author or byline field such as `author` or `article_author`. Imported sites often carry one. Use the person's display name or the brand name, or leave the field out.

Keep each Page's `visibility` as it is unless the owner asked for a change.

## Posts

A post is a Page created with `type: "post"`.

- **Date.** To keep a post's original date, put the source's publication time in the draft's metadata as `article_published_time`, an ISO date-time. Publication uses it as the post's published date. An unreadable value is ignored, and a first publication is then dated at that moment.
- **URL.** A slug can have several segments, such as `/2024/05/harvest-notes`, so the old URL pattern can usually be kept. A request with or without a trailing slash reaches the same Page.
- **Listing.** `/blog` lists published posts by itself, twelve to a page, unless a published Page exists at `/blog`. The `blog-summary-collection` component places a post listing on any Page. `/feed` serves the feed.
- **What does not carry over.** The listing has no category, tag or author archives, and posts have no comments. Plan each old archive URL as a redirect or a hand-built Page, and tell the owner that comments are left behind.

## Review a draft

A draft is visible only to a signed-in member of the site's team. The save returns `preview_url`, an editor path on the site's own host. To look at a draft, open that editor path in a browser where the owner is already signed in to the admin, or ask the owner to open it. Never ask for the owner's password.

`capture_page_screenshot` loads a URL without signing in, so it shows published, public Pages only. It cannot show a draft, even through the draft's preview link. To check a draft's values, read its saved content back with `read_page_content`. A draft that uses new classes can look unstyled until it is published; [styling](styling.md#when-styles-take-effect) explains how to review it.

## Publish

`publish_page_version` takes the Page id and the exact version id. It replaces the previously published version and returns `live_url` when the site has a primary domain.

- **The publish gate.** If the version has nodes missing required fields, the first call publishes nothing and returns those nodes with a `publish_incomplete_token`. Fix the fields, save, and publish the new version. Publishing incomplete is the owner's decision alone. Only then, call again with `publish_incomplete: true` and that token as `token`. The token stops working as soon as the content changes.
- **Permission.** "Not authorized to publish pages on this site" means the connection may not publish. Record an owner action instead of looking for another route.
- **Scheduling is publishing.** `schedule_page_version` passes the same gate and needs the same approval from the owner. Take a migration live with `publish_page_version` instead.
- **Order.** On a site that already has visitors, publish a Page before anything sends them to it: navigation, links from other Pages, redirects. On a fresh site the order is free, because nobody is following those links yet.

## Slugs and redirects

- **Renaming a Page.** `update_page_settings` with a new `slug` records a permanent redirect from the old slug by itself, unless another Page or a built-in route still serves that path. Renaming a Page that has never been published records the redirect too, so get a draft's slug right when you create it.
- **Old paths from the source site.** Add them with `upsert_redirects`: up to 200 rows per call, each with a `source_path` on this site and a `target_path` that is a path or a full `https://` URL. Run it with `dry_run: true` first and read the rejected rows.
- **How a request matches.** A request matches a `source_path` exactly, or with its trailing slash added or removed. Case matters, so enter each old path in the case the old site used. Page slugs are different: they are lowercase, and a request in any case reaches the Page.
- **What it rejects.** A source that a live Page or built-in route already serves, a redirect to itself, and a loop. A target that is itself redirected is accepted with a chain warning; point the row at the final path instead.
- **Query strings.** A `source_path` cannot include one. Old URLs that differ only by query string cannot each be redirected, so list them as unresolved.
- **File-like paths.** A path that ends in a file extension for an image, font, script, stylesheet, video or PDF, such as `/wp-content/uploads/guide.pdf`, redirects only on the domains listed in the result's `edge_sync`, and only when its status is `synced`. Otherwise, record those paths as a gap. Paths ending in `.html` or `.php` redirect like any other path.
- **A Page wins its path.** Publishing a Page, or renaming a published Page onto a path, retires any redirect that had that path as its source.
- **Where redirects work.** They apply at once, on the site's own host. Visitors of the old domain are redirected only after that domain points at this site.
- **Testing.** Request each old path on the site's own host with the session's own HTTP client or browser, and read the status and the final address. For a long list, check every path with a script and open a sample by hand.

## Header, footer and shared blocks

1. Read `get_site_layout` and `list_content_blocks` to see what is bound and what exists.
2. Create the block with `create_content_block`: a `code` such as `site-header`, a name, `slot` set to `header` or `footer`, and its content.
3. Save changes with `save_content_block_draft`. It takes the complete content every time, because blocks have no patch mode.
4. Publish the exact version with `publish_content_block_version`. It has the same publish gate as Pages.
5. Bind it with `set_site_header_block` or `set_site_footer_block` and the block's `code`. A binding shows the block's published version on every public Page. Publish before you bind, because a bound block with no published version leaves its slot empty. Passing `null` returns the site to the platform's default header or footer.

To prepare new chrome on a live site without changing what visitors see, build a new block under a new code and bind it at go-live. Editing and publishing the block that is already bound changes the live site at once.

## Site metadata, logo and fonts

- **Site metadata.** Send only the keys you are changing to `update_site_metadata`. It merges by default and returns the complete merged metadata. Never use `metadata_strategy: "replace"`, which discards every key you omit.
- **Logo.** Import the file to the media library with `intent` set to `logo`, `icon` or `favicon`, then call `set_site_logo` with the asset's `image_id`, the `variant`, and `mode` for the light or dark version. Use `import_inline_svg` when the logo exists only as SVG markup.
- **Fonts.** `add_site_font` registers a family by name with the stylesheet URL that loads it. [Styling](styling.md#fonts) covers how content and the theme use it.

Site metadata and the logo apply immediately. Confirm on the live site that a new font loaded; [styling](styling.md#fonts) says what to do if it has not.

## Media

1. **Search first.** `list_media_assets` and `image_search` show what the library already holds.
2. **Import.** `import_media_from_urls` takes image URLs, or objects with `url`, `title`, `alt` and `caption`. A URL that was already imported is skipped by default. Pass `skip_enhancement: true` for images whose name or identity matters, such as staff portraits, so that an AI caption does not replace it.
3. **Wait.** The call returns as soon as the import is queued. `list_active_workflows` shows the background jobs still running on the site. A job leaves that list when it ends, whether or not it succeeded.
4. **Find the results.** `list_media_assets` returns each asset with the `source_url` it came from. Match on that. It is the only proof that an import landed, so confirm an asset exists before a Page refers to it. An import can fail, for example when the AI wallet is empty.
5. **Use the right size.** The `url` that `list_media_assets` returns is a 500-pixel preview, and the image component shows whatever `src` it is given. Keep the path from `/media/` onward and change its first segment to fit the placement: `/media/w_1600,c_limit,q_auto,f_auto/<image-path>` for a full-width image, a smaller `w_` value for cards. A path that starts at `/media/` works on whichever domain serves the site. A `url` with no `/media/` segment belongs to an asset stored elsewhere; use it as it is.

Imports spend the site's AI wallet. When it is empty, or the owner has not approved the spend, carry on with hotlinks: an image component accepts a full URL on another site. A background image does not: it must be a path on this site, such as a `/media/` path. Use an imported image there, or an image component instead.

**Files that are not images.** The media library holds images and video, so documents such as PDFs need another home. List every download in the asset inventory and make each one an owner decision: a file host the owner controls, with the Page linking to it. A file left on the source domain stops working when that domain moves.
