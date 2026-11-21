# Migration plan

Read this when writing the plan in step 4. Work through the checklist first, then save the plan from the skeleton below. Every item gets an answer in the plan, or "not applicable" with the reason.

## Checklist

### 1. Target and scope

- [ ] Target site, connection name, and whether the site is fresh or live.
- [ ] Sources: URLs, design files, Design Plan import id.
- [ ] Mode: a faithful move, a new design on existing content, or a design only.
- [ ] What may change, such as copy edits or merged pages, and what must stay verbatim.
- [ ] What is out of scope, stated plainly.
- [ ] Publish scope: drafts only, publish on the site's current address, or publish and move the domain.

### 2. Coverage and URLs

- [ ] The coverage table is complete. Every source page is kept, merged, dropped or handled as module data.
- [ ] Existing paths are kept where possible. Every changed or dropped path has a redirect target. A renamed Page gets its redirect from the rename itself.
- [ ] Posts: URL pattern, dates, authors, categories, the index page and pagination. The post exemplar confirms how the destination expresses the source's URL pattern before the batch is built.
- [ ] URLs that are not pages: downloads, directly linked images, feeds, query-string variants. A URL that differs only by its query string may not be redirectable; list it as unresolved.
- [ ] Redirects the old site already serves that must carry over.

### 3. Design system

- [ ] Fonts: families, weights, the stylesheet that loads each, and license status for any font that is not free to use.
- [ ] Palette to theme tokens: which color takes which role, the light and dark stance, and which shades are set or left at defaults.
- [ ] Color mode: one look or both, the default mode the owner must set on the Branding page, and whether the header keeps its light and dark toggle. A site shows dark by default.
- [ ] Radius, spacing and type-scale conventions.
- [ ] Logo, icon, favicon and default social image.
- [ ] A new theme or a change to the active one, and when it is activated. A published change to the active theme goes live at once, so a live site gets a separate theme.

### 4. Shared chrome

- [ ] Header: navigation items and hierarchy, call to action, mobile menu.
- [ ] Footer: columns, legal links, social links, contact details.
- [ ] Site-wide elements: announcement bars, consent banner, chat widget.

### 5. Layout types and components

- [ ] Each layout type lists its pages, its exemplar, and a section-by-section component mapping checked against live schemas.
- [ ] Every section with no matching component has a chosen resolution.
- [ ] Interactive pieces such as sliders, tabs, accordions, maps, video and search have named equivalents.

### 6. Content rules

- [ ] Which copy is carried verbatim and which may be edited. Legal pages stay verbatim.
- [ ] Every claim traces to the source or the owner.
- [ ] Placeholder copy or stock images in a design, and where the real content comes from.
- [ ] Heading structure with one top-level heading per Page, and the source of alt text.

### 7. Media

- [ ] The asset inventory is complete, with real dimensions checked against each placement.
- [ ] Import, hotlink or replace for each asset, and the owner's confirmation that they may use the images.
- [ ] Wallet use stated as a count of images to import. The owner reads the balance and prices under Billing; you cannot.
- [ ] Every asset served from the source domain is imported before the domain moves.
- [ ] Where video and embeds will live.
- [ ] Downloads such as PDFs: the media library holds images and video only, so each one needs a file host the owner provides and a decision recorded here.
- [ ] New imagery comes from the owner.

### 8. Search and machine metadata

- [ ] Per-Page title, description and social image, carried from the source.
- [ ] Site-level defaults, organization details and structured data.
- [ ] Pages that must stay private or out of search results, and each Page's visibility.
- [ ] The machine-readable introductions, written from facts.

### 9. Forms and lead capture

- [ ] Each source form: its fields, required fields and where submissions go today.
- [ ] Its Form component: field mapping to the CRM, consent checkbox, notification address.
- [ ] Follow-ups that need an automation.
- [ ] A test approach that creates no junk leads.

### 10. Integrations, tracking and module data

- [ ] Analytics, tags, pixels and consent tooling on the source, with the owner action that re-adds each.
- [ ] Third-party embeds and widgets: booking, scheduling, maps, reviews, chat.
- [ ] Module data on the source, such as listings, products, events, members, contacts and subscribers: what is in scope, how it arrives, and what is left out.

### 11. Accessibility and responsive behavior

- [ ] Contrast, visible focus, keyboard reach, reduced motion and mobile navigation.
- [ ] Known defects on the source, and whether each is fixed or carried over.

### 12. Cutover and rollback

- [ ] The go-live order: prepared foundation applied (fonts, theme activation, chrome binding, logo, metadata), then Page publication, then redirects.
- [ ] On a live site, the state recorded before any change (published version ids, active theme, chrome bindings, existing redirects) and the way back for each.
- [ ] On a live site, how the owner reviews the new look before go-live, given that only a signed-in member of the site's team can see a draft and that drafts preview in the current theme and chrome.
- [ ] Search engines before the domain moves: a published Page is open to them at once on the site's own host. The only switch is `robots` in each Page's metadata, and lifting it later means saving and publishing every Page again. Choose between accepting a short overlap and that cost, as an owner decision.
- [ ] Before the domain moves: no asset hotlinked from the source domain, old paths tested on the site's own host, and the DNS steps in the owner's hands.
- [ ] Domain: who changes DNS and when, and that only web records change so email keeps working.
- [ ] After the domain moves: redirect and Page checks on the real domain, the certificate, the sitemap, and the owner's search-console resubmission.
- [ ] How long the old site stays available, and how a later session resumes cutover from this plan.

### 13. Verification

- [ ] The evidence required per Page and per layout type, and which browser capability the session has, including whether it can view drafts in a browser where the owner is signed in.
- [ ] Who verifies, and what the owner will review to accept the work.

### 14. Execution

- [ ] Work packets, their order, what runs in parallel, the model tier per packet and the worker count.
- [ ] Expected use of the AI wallet as counts, the costliest steps for workers, and the conditions that stop spending.
- [ ] The lead's own model tier, and any request to use a costlier model for workers or the final review.
- [ ] What happens to a blocked item.

### 15. Decisions and risks

- [ ] Every question for the owner, each with a recommended default.
- [ ] Unknowns, unreachable sources and platform gaps.

## Plan skeleton

```markdown
# Migration plan: <site>

Status: draft | approved by <name> on <date> | executing | complete
Target: <hostname>, connection <name>, fresh | live
Sources: <URLs, design files, Design Plan import id>
Mode: faithful move | new design on existing content | design only
Publish scope: drafts only | publish on current address | publish and move the domain

## Decisions for the owner

| #   | Decision | Recommended default | Answer |
| --- | -------- | ------------------- | ------ |

## 1. Target and scope

## 2. Coverage and URLs

## 3. Design system

## 4. Shared chrome

## 5. Layout types and components

## 6. Content rules

## 7. Media

## 8. Search and machine metadata

## 9. Forms and lead capture

## 10. Integrations, tracking and module data

## 11. Accessibility and responsive behavior

## 12. Cutover and rollback

## 13. Verification

## 14. Execution

## 15. Risks and unknowns

## Ledger

| #   | Item | Kind | Assignee | Tier | Depends on | Status | Evidence |
| --- | ---- | ---- | -------- | ---- | ---------- | ------ | -------- |
```

Ledger conventions:

- **One row per unit of work, not per Page.** A Page's own state lives in its coverage row. The ledger holds each work packet and every item that is not a Page, and a packet row is done when all of its coverage rows are.
- **Kind** is foundation, exemplar, Page packet, form, redirect, module data, go-live, cutover, verification or owner action.
- **Assignee** is the lead, a worker packet, or the site owner.
- **Status** is pending, in progress, done, skipped with a reason, or blocked with the owner action that unblocks it.
- **Evidence** is a version id, a URL, a screenshot path or a check result. A row without evidence is not done.

## The approval round

Send one message that contains:

1. a short summary of what will exist when you finish;
2. the decisions table, each row with your recommended default;
3. the publish scope you are asking for;
4. expected use of the AI wallet as counts, and the worker count and tiers;
5. owner actions and when each is needed.

Record the answers in the plan before executing. A later change of scope gets its own recorded approval.
