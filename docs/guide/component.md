---
outline: deep
---

# Components in Slides

One of the most powerful features of Slidev is the ability to use Vue components directly in your slides. This allows you to create interactive and dynamic content with ease.

## Using Components {#use}

With the help of [`unplugin-vue-components`](https://github.com/unplugin/unplugin-vue-components), Slidev allows you to use Vue components directly in your slides without importing them manually:

```md
# My Slide

<MyComponent :count="4"/>
```

The components come from:

- Built-in components. See [Built-in Components](../builtin/components) for reference.
- Provided by the theme and addons. See <LinkInline link="guide/theme-addon" />.
- Custom components in the `components` directory. See the next section.
- npm packages, after you install and import them. See [Importing from npm Packages](#npm).

## Writing Components {#write}

To create a custom component, simply create a new Vue file in the `components` directory:

```bash
your-slidev/
  ├── ...
  ├── slides.md
  └── components/
      ├── ...
      └── MyComponent.vue
```

Refer to the [Vue documentation](https://vuejs.org/guide/essentials/component-basics.html) for how to write Vue components.

You can also <LinkInline link="guide/write-addon" /> to reuse and share your components with others.

## Importing from npm Packages {#npm}

Auto-import covers built-in components, components from the theme and addons, and files in the local `components` directory. A Vue component published as an npm package becomes available after you install it in the slides project and import it.

Install a package that provides Vue 3 components. Slidev renders the presentation with Vue 3.

::: info
`example-vue-component` and `ExampleChart` in the samples below are placeholders. Use the package name, import path, and stylesheet path from the library you install.
:::

### Install {#npm-install}

Run the install from the project that contains `slides.md`, so Vite can resolve the package:

::: code-group

```bash [pnpm]
pnpm add example-vue-component
```

```bash [npm]
npm install example-vue-component
```

```bash [yarn]
yarn add example-vue-component
```

```bash [bun]
bun add example-vue-component
```

:::

Restart the dev server after installing so Vite picks up the new dependency.

### Import on one slide {#npm-slide}

Each slide is compiled as its own Vue single-file component. A `<script setup>` block on that slide can import from `node_modules`, and the imported binding is the component tag. The import applies to that slide only.

```md
# Chart

<ExampleChart :value="42" />

<script setup lang="ts">
import ExampleChart from 'example-vue-component'
</script>
```

Named exports work the same way. The local binding name is the tag:

```md
<script setup lang="ts">
import { ExampleChart } from 'example-vue-charts'
</script>

<ExampleChart />
```

A slide has one `<script setup>` block. When the slide already defines state or other imports there, add the component import to that block.

The same `import` works in any local Vue file, including files in `components/` and `layouts/`.

### Reuse it from `components/` {#npm-reuse}

Put a file in the `components` directory when more than one slide uses the library. Slidev auto-imports that file into slides, layouts, and other components.

To render the library component unchanged, including its props, events, and slots, re-export it from a `.ts` file. The file name becomes the tag: `components/ExampleChart.ts` is `<ExampleChart />` (`example-chart.ts` resolves to the same tag).

```ts
// components/ExampleChart.ts
export { default } from 'example-vue-component'
```

```md
# Any slide

<ExampleChart :value="42" />
```

For a named export, re-export that binding as the default:

```ts
// components/ExampleChart.ts
export { ExampleChart as default } from 'example-vue-charts'
```

Write a `.vue` file instead when you want local defaults or extra markup around the library component:

```vue
<!-- components/RevenueChart.vue -->
<script setup lang="ts">
import ExampleChart from 'example-vue-component'
</script>

<template>
  <ExampleChart :value="42" />
</template>
```

```md
<RevenueChart />
```

Give the file its own component name. If that name matches a built-in component, or a component from the theme or an addon, Slidev keeps the existing component and ignores the file in `components/`.

### Register it on the app {#npm-global}

A library that ships a Vue plugin is installed from `setup/main.ts`. The same file can register one component with `app.component`, which makes it available in slides, layouts, and global layers without adding a file under `components/`.

```ts
// setup/main.ts
import { defineAppSetup } from '@slidev/types'
import ExampleChart from 'example-vue-component'

export default defineAppSetup(({ app }) => {
  app.component('ExampleChart', ExampleChart)
})
```

```ts
// setup/main.ts
import { defineAppSetup } from '@slidev/types'
import ExamplePlugin from 'example-vue-component'

export default defineAppSetup(({ app }) => {
  app.use(ExamplePlugin)
})
```

See [Configure Vue App](../custom/config-vue) and <LinkInline link="features/global-layers" />.

Use a name that does not match a built-in component, a theme or addon component, or a file in `components/`. Those components are imported into the slide directly, so they take precedence over `app.component` for the same name.

### Styles {#npm-styles}

When the package documents a stylesheet, import that file next to the component. From a slide:

```md
<script setup lang="ts">
import ExampleChart from 'example-vue-component'
import 'example-vue-component/style.css'
</script>
```

Import it from the re-export module to load the CSS with the component:

```ts
// components/ExampleChart.ts
import 'example-vue-component/style.css'

export { default } from 'example-vue-component'
```

Import it once for the whole deck from [`styles/index.ts`](../custom/directory-structure#style) (or `style.css`) or from `setup/main.ts`:

```ts
// styles/index.ts
import 'example-vue-component/style.css'
```

### Resolvers {#npm-resolvers}

Some libraries publish an [`unplugin-vue-components`](https://github.com/unplugin/unplugin-vue-components) resolver so tags are imported on demand. Slidev forwards [`slidev.components`](../custom/config-vite#configure-internal-plugins) in `vite.config.ts` to that plugin.

Values you set there replace Slidev's value for the same field:

- A custom `resolvers` list replaces the built-in icon resolver, so icon components such as `<carbon-logo-github />` stop resolving. See <LinkInline link="features/icons" />.
- A custom `dirs` list replaces the scan of built-in components, theme and addon components, and your `components/` directory.

Prefer a slide import, a file in `components/`, or `app.component` for a typical presentation. Use a resolver when the library requires one and you are ready to supply the full `resolvers` list yourself.
