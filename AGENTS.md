# Agent guidelines: Design System 2 Starter Kit

Local Salesforce UI starter using LWC, Vite, SLDS, Lightning Base Components, synthetic shadow DOM, and client-side routing.

## Code placement and navigation

- Route views: `src/modules/page/<name>/`, tagged `page-*`. Add routes in `src/routes.config.js`; import and register components in `src/modules/shell/app/app.js` (`ROUTE_COMPONENTS`). Do not edit `src/router.js` to add routes.
- New features and pages become the primary tab and initial landing page without confirmation. Assign a `navPage`, put its id first in every applicable app's `pages` array in `src/apps.config.js`, and set each app's `defaultPath` to the app-prefixed route. Keep existing pages as secondary tabs unless the user requests otherwise. Apps without primary navigation, such as Builder, are exempt.
- Reusable UI and SLDS blueprints: `src/modules/ui/<name>/`, tagged `ui-*`.
- App shell: `src/modules/shell/<name>/`, tagged `shell-*`, for the root app, layout, theme, and docked panels. Put route views in `page/` and reusable components in `ui/`.
- Do not add components under `src/modules/lightning/` or `src/build/lightning-icon/shims/`, except the checked-in icon overrides.

## UI implementation

For all UI work, read and follow `node_modules/@salesforce/afv-skills/skills/design-systems-slds-apply/SKILL.md` first. Re-read it when iterating on presentation, including markup, CSS, layout, icons, and component selection.

- Use Lightning Base Components whenever available, such as `lightning-card`, `lightning-button`, and `lightning-icon`. Hand-roll a blueprint only when no base component covers the case, and explain when you do.
- Keep LWCs small and focused on one responsibility.
- Do not use `!important` or inline `style` attributes. Use utility classes or component CSS.
- Modals must extend `lightning/modal`. Follow `src/modules/ui/demoModal/` for header, body, and footer slots; open with `MyModal.open({ size, label })`. Do not use raw `slds-modal` markup.
- Use `lightning-input`, `lightning-combobox`, `lightning-radio-group`, `lightning-textarea`, or `lightning-select` for all form inputs. Do not use raw `<input>`, `<select>`, or `<textarea>`. Choose the correct type, such as `type="email"`, and use `read-only` for data display.

Lint every changed HTML or CSS file before completing the task:

```bash
npx @salesforce-ux/slds-linter@latest lint <path-to-changed-file>
```

Fix reported issues where possible and briefly report any remaining issues.

## Skills

SLDS and LWC skills ship in `@salesforce/afv-skills`. Paths below are relative to `node_modules/@salesforce/afv-skills/skills/` unless otherwise shown.

| Skill path | Use |
|------------|-----|
| `design-systems-slds-apply/SKILL.md` | Required first read for all UI work: blueprints, hooks, utilities, icons, and base component selection. |
| `experience-lwc-generate/SKILL.md` | Consult when stuck on LWC behavior, including `@api`, `@wire`, lifecycle, events, or template expressions. See `references/lwc-best-practices.md` and `references/template-anti-patterns.md`. Not routine UI work. |
| `design-systems-slds2-migrate/SKILL.md` | SLDS 1 to 2 migration, linter fixes, and hook/token replacements. |
| `design-systems-slds-validate/SKILL.md` | Audits and scorecards only, not implementation or fixes. |
| `.agent/skills/repo-setup/SKILL.md` (repo-relative) | Saving or backing up work: host detection, prerequisites, repository creation, and initial push. |
| `.agent/skills/first-time-deploy/SKILL.md` (repo-relative) | First GitHub Pages deployment or sharing a published link. For repeat deployments, run `npm run deploy`. |

## GitHub Pages

Publish only through `npm run deploy`: it runs `build:gh-pages` with hash routing and pushes `dist/` to `gh-pages` on `origin`. `npm run dev` and `npm run build` retain path routing. GitHub Pages must use **Deploy from a branch → gh-pages**; see [README.md](README.md).

When asked about another push target, mention `gh-pages` options `-o <remote>` and `-r <url>`.
