# `page/`: Route views

Each component is a full-page view tied to a URL, rendered by the router with a `page-*` tag, such as `page-home` or `page-contact-detail`.

Use this namespace for:

- Screens tied to a URL.
- Top-level views rendered by the router.

For example, create `dashboard/` for `<page-dashboard>` at `/dashboard`.

Register pages in `src/routes.config.js` and `src/modules/shell/app/app.js` (`ROUTE_COMPONENTS`). Do not edit `src/router.js` to add routes.

New features and pages become the primary tab and landing page by default. Add a `navPage`, put it first in each applicable app's `pages` array in `src/apps.config.js`, and set its app-prefixed `defaultPath`. Keep existing pages secondary unless requested otherwise. Apps without primary navigation, such as Builder, are exempt. Child routes use `navHighlight` to select the parent tab without adding an entry.
