# Styling

Read this before you set a theme or write any `classNames`. It covers theme tokens, fonts, light and dark mode, which classes to use, and when a style change becomes visible.

## How styling works

- **Theme.** A theme is a set of design tokens: colors, typography, backgrounds, spacing and edges. The platform compiles them to CSS. You send tokens, never a stylesheet.
- **Classes.** Every node takes `classNames`: Tailwind utility classes plus the theme-aware classes below. Some components take a second class prop for an inner element, such as `textClassNames` and `imageClassNames`; each component's schema says which element a prop reaches.
- **One-off values.** `styles` holds a property map such as `{ "maxWidth": "30rem" }` for a value no class covers. Prefer `classNames`.
- **Content follows the theme.** Refer to theme colors through classes. A raw hex value in a Page stops tracking the theme and breaks in the other color mode.

## Theme tokens

1. Read the active theme with `get_theme`. It returns every token by family and key.
2. Start a theme with `create_theme` for an empty one, `copy_theme` to fork a preset or an existing theme, or `import_theme` to turn the old site's CSS into a draft.
3. Save tokens with `create_theme_version` and a `settings_patch`, using the same families and keys that `get_theme` showed:

```json
{
	"colors": { "--color-primary-500": "#1f6f5c", "--color-primary-600": "#195a4b" },
	"typography": { "--heading-font-family": "'Fraunces', serif" },
	"edges": { "--radius-container": "0.75rem" }
}
```

4. Publish with `publish_theme_version`, then make it live with `activate_theme`. A theme needs a published version before it can be activated.

**What goes live.** Visitors see the published version of the active theme. Activating a theme changes the site, and so does publishing a new version of the theme that is already active. To prepare a new look on a live site, work on a separate theme made with `create_theme` or `copy_theme`, and activate it at go-live.

**What a patch builds on.** A patch merges onto the theme's published version when it has one, and onto its latest draft only when nothing is published yet. On a published theme a second draft does not build on the first, so send every change in one patch. Tokens are saved as you send them. Each color family (`primary`, `secondary`, `tertiary`, `success`, `warning`, `error`, `surface`) has eleven shades from `50` to `950`, plus contrast tokens that set the text color used on top of each shade. Changing `--color-primary-500` alone leaves the other shades and the contrast tokens as they were. Send the whole scale for any color you replace, and check the text contrast on filled buttons.

## Fonts

Register each family with `add_site_font`. To make it the site's heading or body font, set the theme's `--heading-font-family` or `--base-font-family` token to it, in the format `get_theme` shows. Each registered font also becomes a class named after the family in lowercase with hyphens, such as `font-baloo-2` for Baloo 2, and `font-heading` and `font-body` follow the theme.

Before you rely on a new font, confirm on the live site that it loaded. If it has not, [when styles take effect](#when-styles-take-effect) says how to refresh the site's styles.

## Light and dark mode

A site shows visitors a dark page by default until the owner changes it. A design drawn in light colors therefore needs an owner action: on the **Branding** page (`/_/theme/branding`), **Default color mode** is Dark, Light or Follow visitor's device. The same page shows or hides the light and dark toggle in the site header. Only the owner sets these.

Decide the stance in the plan:

- **One look.** The owner sets the default mode to match the design and hides the toggle. Check the site in that mode.
- **Both modes.** Use the adaptive classes below everywhere, set the dark-mode tokens in the theme, give the logo a dark variant with `set_site_logo`, and check both modes.

## Classes to use

Adaptive classes carry a light shade and a dark shade, and switch with the mode:

| Purpose          | Class                         |
| ---------------- | ----------------------------- |
| Page background  | `bg-surface-50-950`           |
| Raised surface   | `bg-surface-100-900`          |
| Main text        | `text-surface-950-50`         |
| Muted text       | `text-surface-700-300`        |
| Subtle border    | `border-surface-200-800`      |
| Brand action     | `preset-filled-primary-500`   |
| Secondary action | `preset-outlined-primary-500` |
| Card             | `preset-card`                 |

- Use a single shade, such as `bg-primary-500`, for a color that must stay the same in both modes.
- Use `rounded-base` for small elements and `rounded-container` for cards and panels, so corners follow the theme.
- Use `anchor` for a themed text link.
- Avoid `preset-tonal-surface` for cards. It matches the usual page background, so the card has no visible edge. Use `preset-card`.
- Avoid arbitrary color classes such as `bg-[#86C8BC]` and hard-coded `dark:` classes. Put the color in the theme and use its class.
- Build layout with responsive utilities such as `grid grid-cols-1 md:grid-cols-2 gap-6`. Never copy pixel positions or fixed widths from a design export.

## When styles take effect

The site's stylesheet is built from the classes that its content uses, plus the registered fonts. Publishing a Page or a content block updates it a little later. Saving a draft does not.

- A draft that uses a class no published content has used yet can look unstyled in the editor. That is not a design defect.
- On a fresh site within publish scope, publish each exemplar and inspect the live Page.
- Where you may not publish, ask the owner to select **Rebuild styles** on the Pages list (`/_/manage/pages`) before they review drafts.
- Review drafts in the editor. After a rebuild, the editor's styles cover every draft. A draft opened at the Page's public address is styled with published classes only.
- After a publish or a theme change, allow a few seconds, then reload. Confirm that the stylesheet loaded before you diagnose a layout problem. A screenshot without loaded styles proves nothing about the design.

## Renderer behavior

- **Headings.** `level` sets the HTML element and the default size, and a heading without one becomes a level 2. `classNames` lands on the heading element itself and overrides its appearance without changing the level. Use one level-1 heading per Page and keep levels in order.
- **Sections.** `tagName` picks the element, and `backgroundImage` and `backgroundVideo` set a backdrop. A background image must be a path on this site, such as a `/media/` path.
- **Images.** `size` sets the box: the default is a small cropped square, and `full` fills the container's width. The component shows its `src` as given, with no resizing, so set the pixel width in the URL as [authoring](authoring.md#media) describes.
- **Text.** HTML inside a `text` node is cleaned before display, so keep to paragraphs, lists, links and emphasis.

## Finish check

Look at the real published Page at desktop and mobile widths. Confirm that:

- styles and fonts loaded, and no text shows a fallback font;
- the first screen fits its content, and nothing overflows sideways;
- navigation works, including the mobile menu;
- images are sharp at their displayed size, and none is a placeholder;
- text contrast is readable in every mode the site offers;
- keyboard focus is visible, and headings are in order.

Record any difference from the source that you could not close, with its cause.
