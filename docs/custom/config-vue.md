# Configure Vue App

<Environment type="client" />

Slidev uses [Vue 3](https://v3.vuejs.org/) to render the application on the client side. You can extend the app to add custom plugins or configurations.

Create `./setup/main.ts` with the following content:

```ts twoslash [setup/main.ts]
/* eslint-disable import/first */
import type { Plugin } from 'vue'

declare const YourPlugin: Plugin
// ---cut---
import { defineAppSetup } from '@slidev/types'

export default defineAppSetup(({ app, router }) => {
  // Vue App
  app.use(YourPlugin)
})
```

This can also be used as the main entrance of your Slidev app to do some initializations before the app starts.

To register a component from an npm package with `app.component`, or to install a Vue plugin that provides components, see [Importing from npm Packages](../guide/component#npm).

Learn more: [Vue Application API](https://v3.vuejs.org/api/application-api.html#component).
