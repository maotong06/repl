# @vue/repl

Vue SFC REPL as a Vue 3 component.

## Basic Usage

**Note: `@vue/repl` >= 2 now supports Monaco Editor, but also requires explicitly passing in the editor to be used for tree-shaking.**

### With CodeMirror Editor

Basic editing experience with no intellisense. Lighter weight, fewer network requests, better for embedding use cases.

```vue
<script setup>
import { Repl } from '@vue/repl'
import CodeMirror from '@vue/repl/codemirror-editor'
// import '@vue/repl/style.css'
// ^ no longer needed after 3.0
</script>

<template>
  <Repl :editor="CodeMirror" />
</template>
```

### With Monaco Editor

With Volar support, autocomplete, type inference, and semantic highlighting. Heavier bundle, loads dts files from CDN, better for standalone use cases.

```vue
<script setup>
import { Repl } from '@vue/repl'
import Monaco from '@vue/repl/monaco-editor'
// import '@vue/repl/style.css'
// ^ no longer needed after 3.0
</script>

<template>
  <Repl :editor="Monaco" />
</template>
```

## Advanced Usage

Customize the behavior of the REPL by manually initializing the store.

```vue
<script setup>
import { watchEffect, ref } from 'vue'
import { Repl, useStore, useVueImportMap } from '@vue/repl'
import Monaco from '@vue/repl/monaco-editor'

// retrieve some configuration options from the URL
const query = new URLSearchParams(location.search)

const { importMap: builtinImportMap, vueVersion } = useVueImportMap({
  // specify the default URL to import Vue runtime from in the sandbox
  // default is the CDN link from jsdelivr.com with version matching Vue's version
  // from peerDependency
  runtimeDev: 'cdn link to vue.runtime.esm-browser.js',
  runtimeProd: 'cdn link to vue.runtime.esm-browser.prod.js',
  serverRenderer: 'cdn link to server-renderer.esm-browser.js',
})

const store = useStore(
  {
    // pre-set import map
    builtinImportMap,
    vueVersion,
    // starts on the output pane (mobile only) if the URL has a showOutput query
    showOutput: ref(query.has('showOutput')),
    // starts on a different tab on the output pane if the URL has a outputMode query
    // and default to the "preview" tab
    outputMode: ref(query.get('outputMode') || 'preview'),
  },
  // initialize repl with previously serialized state
  location.hash,
)

// persist state to URL hash
watchEffect(() => history.replaceState({}, '', store.serialize()))

// use a specific version of Vue
vueVersion.value = '3.2.8'
</script>

<template>
  <Repl :store="store" :editor="Monaco" :showCompileOutput="true" />
</template>
```
