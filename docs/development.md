# Development reference

Technical details for extending and publishing the starter kit. For getting started, see [README.md](../README.md).

## Project structure

| Path | Purpose |
|------|---------|
| `src/modules/page/` | Route views, tagged `page-*` |
| `src/modules/ui/` | Reusable components and SLDS blueprints, tagged `ui-*` |
| `src/modules/shell/` | Root app, global layout, theme, and docked panels, tagged `shell-*` |
| `src/modules/data/` | Plain JavaScript helpers and fixtures, imported as `data/<name>` |
| `src/routes.config.js` | Route definitions and navigation metadata |
| `src/apps.config.js` | Apps, page order, URL prefixes, and landing routes |
| `src/router.js` | Route matching and navigation |
| `src/index.js`, `src/bootstrap.js` | Synthetic shadow initialization and app mounting |
| `src/build/slds-loader.js` | Stylesheet loading and theme initialization |
| `src/build/slds/slds1-url.js` | Lazy SLDS 1 stylesheet URL |
| `src/build/lightning-icon/shims/` | Checked-in icon template overrides |
| `src/build/shim/` | LWC/package compatibility shims |
| `src/build/generated/` | Generated icon modules; do not edit |
| `scripts/prebuild-icons.mjs` | Icon generation, run by npm scripts |
| `public/images/` | App-owned assets, including `salesforce.svg` |
| `vite.config.js` | Build, module aliases, and dev server configuration |

Put feature pages in `page/`, reusable components in `ui/`, and shell wiring in `shell/`. Do not add custom components under `src/modules/lightning/` or unrelated files under `src/build/lightning-icon/shims/`.

## Add a page or component

1. Create `src/modules/page/<name>/`, such as `dashboard/` for `<page-dashboard>`.
2. Add a route to `src/routes.config.js`:

   ```js
   { path: '/dashboard', component: 'page-dashboard', title: 'Dashboard', navPage: 'dashboard', navLabel: 'Dashboard' }
   ```

3. Import the component in `src/modules/shell/app/app.js` and register it in `ROUTE_COMPONENTS`. No changes to `src/router.js` are needed.
4. For a new feature or page, make its `navPage` first in each applicable app's `pages` array in `src/apps.config.js`, and set that app's `defaultPath` to the app-prefixed route (for example, `/app/dashboard`). Keep existing pages as secondary tabs unless requested otherwise. Apps without primary navigation, such as Builder, are exempt.

For child routes, use `navHighlight: '<parentNavPage>'` instead of `navPage` to highlight the parent without adding a tab.

For reusable components, create `src/modules/ui/<name>/` and use the corresponding tag, such as `<ui-card>`. Follow [AGENTS.md](../AGENTS.md) and any additional editor rules, such as `.cursor/rules`, when present.

## Routing and apps

Routes use logical paths without app prefixes. Each entry has `path`, `component`, and `title`, plus optional `navPage`, `navLabel`, `navPath`, and `navHighlight` fields.

- `title` is a string or a function of route parameters.
- `:id` defines a path parameter. Read parameters with `getCurrentRoute()` from `src/router.js`.
- `navPage` creates a navigation entry; `navLabel` supplies its label. `navPath` supplies a concrete navigation target for dynamic routes.
- `navHighlight` selects an existing parent entry, such as `contacts` for `/contacts/:id`.
- Call `navigate(path)` from the router. The shell renders the matching component without reloading the page.

Each app in `src/apps.config.js` declares its `pages` using route `navPage` ids. Pages can appear in multiple apps. Edit this array to change navigation; add an app with a unique `id`, `pathPrefix`, and `defaultPath` to create another app.

## Components and styles

Prefer Lightning Base Components, then SLDS utilities, then styling hooks. For UI work, first read `node_modules/@salesforce/afv-skills/skills/design-systems-slds-apply/SKILL.md`. See [AGENTS.md](../AGENTS.md) for required form elements, linting, and styling constraints.

For modals, extend `LightningModal` from `lightning/modal`. Follow `src/modules/ui/demoModal/`: compose `lightning-modal-header`, `lightning-modal-body`, and `lightning-modal-footer`, then open with `MyModal.open({ size, label })`. Do not use raw SLDS modal markup.

### Themes

SLDS 2 is the default. `src/bootstrap.js` awaits `initSldsFromStorage()` from `src/build/slds-loader.js` before mounting LWC, restoring the saved theme on first paint.

The loader injects `<link rel="stylesheet" data-slds="...">` elements and switches sheets through `media="all"` or `media="not all"`. Vite resolves stylesheet URLs from `new URL(..., import.meta.url)`, emits hashed CSS in `dist/assets/`, and rewrites nested `url(...)` assets:

- `@salesforce-ux/design-system-2`: `dist/css/bundled/slds2.cosmos.css` for SLDS 2/Cosmos.
- `@salesforce-ux/design-system`: `assets/styles/salesforce-lightning-design-system.min.css` for classic SLDS.

SLDS 1 loads lazily through `src/build/slds/slds1-url.js` when selected or required by a saved preference.

### Shadow DOM

Synthetic shadow is the default, matching Salesforce behavior and allowing global SLDS styles and DOM inspection across components. In the browser console, `document.querySelector('shell-app').shadowRoot` returns `null` for synthetic shadow and a `ShadowRoot` for native shadow. Native shadow blocks global styles and DOM queries across its boundary.

To use native shadow, set `disableSyntheticShadowSupport: true` in the LWC plugin options in `vite.config.js`.

### Icons

`scripts/prebuild-icons.mjs` generates templates from `lightning-base-components` into `src/build/generated/`. Vite aliases `lightning/iconSvgTemplates*` to the overrides under `src/build/lightning-icon/shims/`.

## GitHub Pages

Publish with `npm run deploy`. It runs `build:gh-pages` with hash routing, then pushes `dist/` to `gh-pages` on `origin` using [gh-pages](https://www.npmjs.com/package/gh-pages). This repository does not use GitHub Actions for Pages.

For first-time setup:

1. Configure Git push access to `origin`.
2. Run `npm run deploy` to create the remote `gh-pages` branch.
3. In GitHub **Settings → Pages → Build and deployment**, select **Deploy from a branch**, branch **gh-pages**, folder **/ (root)**, then save.

Find the published URL in **Settings → Pages** once publishing finishes. Run `npm run deploy` for subsequent updates. Project skills [repo-setup](../.agent/skills/repo-setup/SKILL.md) and [first-time-deploy](../.agent/skills/first-time-deploy/SKILL.md) guide repository creation and initial publishing.

`npm run dev` and `npm run build` use path URLs such as `/settings`. Pages builds use hash URLs such as `…/your-repo/#/settings`, allowing refreshes and deep links on static hosting. Path-only bookmarks such as `…/your-repo/settings` still return 404 on Pages.

To preview locally, run either `npm run build` for path routing or `npm run build:gh-pages` for Pages routing, then `npm run preview`.

For another target, publish a Pages build with `npx gh-pages -d dist -o <remote-name>` or `npx gh-pages -d dist -r https://github.com/org/repo.git`.

## Tooling and references

- `vite` and `vite-plugin-lwc` provide builds, hot reload, and LWC support. `lwc` and `@lwc/synthetic-shadow` provide the component runtime.
- `mcp.json` configures the [Salesforce DX MCP server](https://www.npmjs.com/package/@salesforce/mcp). Compatible editors, including Claude Code, Cursor, and VS Code with Copilot, can launch it through `npx` on demand without additional setup.
- `@salesforce/afv-skills` supplies SLDS skills from [forcedotcom/sf-skills](https://github.com/forcedotcom/sf-skills). Project skills live in `.agent/skills/<skill-id>/`.
- [Lightning Design System](https://lightningdesignsystem.com)
- [Lightning Web Components documentation](https://developer.salesforce.com/docs/component-library/documentation/en/lwc) and [component library](https://developer.salesforce.com/docs/component-library/overview/components)
- [LWC OSS](https://github.com/salesforce/lwc) for local framework behavior.
