# `data/`: Plain JavaScript modules

Use this namespace for shared helpers, fixtures, and static datasets that do not render UI. Import modules as `data/<name>`; they are not LWCs and have no `data-*` tag.

For example, add `contacts/contacts.js` and import it from a page or UI component:

```js
import { getAllContacts } from 'data/contacts';
```

Put custom elements in `ui/` and route views in `page/`.
