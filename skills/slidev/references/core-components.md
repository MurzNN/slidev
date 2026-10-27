---
name: components
description: Ready-to-use components in Slidev
---

# Built-in Components

Ready-to-use components in Slidev.

## Navigation

### Link

Navigate to slide:
```md
<Link to="5">Go to slide 5</Link>
<Link to="intro">Go to intro</Link>  <!-- with routeAlias -->
```

### SlideCurrentNo / SlidesTotal

```md
Slide <SlideCurrentNo /> of <SlidesTotal />
```

### Toc (Table of Contents)

```md
<Toc />
<Toc maxDepth="2" />
<Toc columns="2" />
```

Props:
- `columns` - Number of columns
- `maxDepth` / `minDepth` - Heading depth filter
- `mode` - 'all' | 'onlyCurrentTree' | 'onlySiblings'

### TitleRenderer

Render slide title:
```md
<TitleRenderer no="3" />
```

## Animations

### VClick / VClicks

```md
<VClick>Shows on click</VClick>

<VClicks>

- Item 1
- Item 2

</VClicks>
```

### VAfter

```md
<VClick>First</VClick>
<VAfter>Shows with first</VAfter>
```

### VSwitch

```md
<VSwitch>
  <template #1>State 1</template>
  <template #2>State 2</template>
</VSwitch>
```

## Drawing

### Arrow

```md
<Arrow x1="10" y1="10" x2="100" y2="100" />
<Arrow x1="10" y1="10" x2="100" y2="100" two-way />
```

Props: `x1`, `y1`, `x2`, `y2`, `width`, `color`, `two-way`

### VDragArrow

Draggable arrow:
```md
<VDragArrow />
```

## Layout

### Transform

Scale elements:
```md
<Transform :scale="0.5">
  <LargeContent />
</Transform>
```

Props: `scale`, `origin`

### AutoFitText

Auto-sizing text:
```md
<AutoFitText :max="200" :min="50" modelValue="Hello" />
```

## Media

### SlidevVideo

```md
<SlidevVideo v-click autoplay controls>
  <source src="/video.mp4" type="video/mp4" />
</SlidevVideo>
```

Props: `controls`, `autoplay`, `autoreset`, `poster`, `timestamp`

### Youtube

```md
<Youtube id="dQw4w9WgXcQ" />
<Youtube id="dQw4w9WgXcQ" width="600" height="400" />
```

### Tweet

```md
<Tweet id="1423789844234231808" />
<Tweet id="1423789844234231808" :scale="0.8" />
<Tweet url="https://x.com/antfu7/status/1389604687502995457" />
```

Use either `id` or a full `x.com` or `twitter.com` post URL. When both are present, `id` takes precedence.

## Conditional

### LightOrDark

```md
<LightOrDark>
  <template #dark>Dark mode content</template>
  <template #light>Light mode content</template>
</LightOrDark>
```

### RenderWhen

```md
<RenderWhen context="presenter">
  Only in presenter mode
</RenderWhen>
```

Context values:
- `main` - Main presentation view
- `visible` - Visible slides
- `print` - Print/export mode
- `slide` - Normal slide view
- `overview` - Overview mode
- `presenter` - Presenter mode
- `previewNext` - Next slide preview

## Branding

### PoweredBySlidev

```md
<PoweredBySlidev />
```

## Draggable

### VDrag

```md
<VDrag pos="myElement">
  Draggable content
</VDrag>
```

See [draggable](layout-draggable.md) for details.

## Component Auto-Import

Components from these sources are auto-imported:
1. Built-in components
2. Theme components
3. Addon components
4. `./components/` directory (`*.vue`, `*.ts`, `*.js`, `*.jsx`, `*.tsx`, `*.md`)

No import statements needed for those.

## NPM Vue Components

Arbitrary npm packages are not auto-imported. The package must be Vue 3 and installed in the slides project.

Use one of these:

1. **One slide** — import it in that slide's single `<script setup>` block. The binding name is the tag, and other slides do not see it.

```md
<script setup lang="ts">
import ExampleChart from 'example-vue-component'
</script>

<ExampleChart :value="42" />
```

2. **Every slide, unchanged component** — re-export it from `components/`. The filename becomes the PascalCase tag.

```ts
// components/ExampleChart.ts
export { default } from 'example-vue-component'
```

Named export: `export { ExampleChart as default } from 'example-vue-charts'`.

3. **Every slide, with local markup** — import it from a `.vue` file in `components/`. Files in `components/` are also available in layouts and other components.
4. **Vue plugin, or a global component with no local file** — in `setup/main.ts`, call `app.use(plugin)` or `app.component('ExampleChart', ExampleChart)`. This covers slides, layouts, and global layers.

Import the package CSS from the slide `<script setup>`, from the re-export module, from `styles/index.ts`, or from `setup/main.ts`.

Do not reuse a built-in, theme, or addon component name. Those win over a later file in `components/` and over `app.component`.

`slidev.components` in `vite.config.ts` is passed to `unplugin-vue-components` and replaces the matching field. A custom `resolvers` array drops the icon resolver. A custom `dirs` array drops the built-in and `components/` scan.
