# pathpida
<br />
<img src="https://aspida.github.io/pathpida/logos/png/logo.png" alt="pathpida" title="pathpida" />
<div align="center">
  <a href="https://www.npmjs.com/package/pathpida">
    <img src="https://img.shields.io/npm/v/pathpida" alt="npm version" />
  </a>
  <a href="https://www.npmjs.com/package/pathpida">
    <img src="https://img.shields.io/npm/dm/pathpida" alt="npm download" />
  </a>
  <a href="https://github.com/aspida/pathpida/actions?query=workflow%3A%22Node.js+CI%22">
    <img src="https://github.com/aspida/pathpida/workflows/Node.js%20CI/badge.svg?branch=master" alt="Node.js CI" />
  </a>
  <a href="https://codecov.io/gh/aspida/pathpida">
    <img src="https://img.shields.io/codecov/c/github/aspida/pathpida.svg" alt="Codecov" />
  </a>
  <a href="https://lgtm.com/projects/g/aspida/pathpida/context:javascript">
    <img src="https://img.shields.io/lgtm/grade/javascript/g/aspida/pathpida.svg" alt="Language grade: JavaScript" />
  </a>
</div>
<br />
<p align="center">TypeScript friendly internal link client for Next.js and Nuxt.js.</p>
<br />
<br />

## Features

- **Type safety**. Automatically generate type definition files for manipulating internal links in Next.js/Nuxt.js.
- **Zero configration**. No configuration required, can be used immediately after installation.
- **Zero runtime**. Lightweight because runtime code is not included in the bundle.

## Table of Contents

- [Install](#Install)
- [Setup - Next.js](#Setup-next)
- [Usage - Next.js](#Usage-next)
- [Define query - Next.js](#Define-query-next)
- [Generate static files path - Next.js](#Generate-static-files-path-next)
- [Setup - Nuxt.js](#Setup-nuxt)
- [Usage - Nuxt.js](#Usage-nuxt)
- [Define query - Nuxt.js](#Define-query-nuxt)
- [Generate static files path - Nuxt.js](#Generate-static-files-path-nuxt)
- [License](#License)

## Install

- Using [npm](https://www.npmjs.com/):

  ```sh
  $ npm install pathpida npm-run-all --save-dev
  ```

- Using [Yarn](https://yarnpkg.com/):

  ```sh
  $ yarn add pathpida npm-run-all --dev
  ```

<a id="Setup-next"></a>

## Setup - Next.js

`package.json`

```json
{
  "scripts": {
    "dev": "run-p dev:*",
    "dev:next": "next dev",
    "dev:path": "pathpida --watch",
    "build": "pathpida && next build"
  }
}
```

<a id="Usage-next"></a>

## Usage - Next.js

```
pages/index.tsx
pages/post/create.tsx
pages/post/[pid].tsx
pages/post/[...slug].tsx

lib/$path.ts or utils/$path.ts // Generated automatically by pathpida
```

or

```
src/pages/index.tsx
src/pages/post/create.tsx
src/pages/post/[pid].tsx
src/pages/post/[...slug].tsx

src/lib/$path.ts or src/utils/$path.ts // Generated automatically by pathpida
```

`pages/index.tsx`

```tsx
import Link from 'next/link'
import { pagesPath } from '../lib/$path'

console.log(pagesPath.post.create.$url()) // { pathname: '/post/create' }
console.log(pagesPath.post._pid(1).$url()) // { pathname: '/post/[pid]', query: { pid: 1 }}
console.log(pagesPath.post._slug(['a', 'b', 'c']).$url()) // { pathname: '/post//[...slug]', query: { slug: ['a', 'b', 'c'] }}

export default () => {
  const onclick = useCallback(() => {
    router.push(pagesPath.post._pid(1).$url())
  }, [])

  return <>
    <Link href={pagesPath.post._slug(['a', 'b', 'c']).$url()} />
    <div onclick={onclick} />
  </>
}
```

<a id="Define-query-next"></a>

## Define query - Next.js

`pages/post/create.tsx`

```tsx
export type Query = {
  userId: number
  name?: string
}

export default () => <div />
```

`pages/post/[pid].tsx`

```tsx
export type OptionalQuery = {
  limit: number
  label?: string
}

export default () => <div />
```

`pages/index.tsx`

```tsx
import Link from 'next/link'
import { pagesPath } from '../lib/$path'

console.log(pagesPath.post.create.$url({ query: { userId: 1 }})) // { pathname: '/post/create', query: { userId: 1 }}
console.log(pagesPath.post.create.$url()) // type error
console.log(pagesPath.post._pid(1).$url()) // { pathname: '/post/[pid]', query: { pid: 1 }}
console.log(pagesPath.post._pid(1).$url({ query: { limit: 10 }, hash: 'sample' })) // { pathname: '/post/[pid]', query: { pid: 1, limit: 10 }, hash: 'sample' }

export default () => {
  const onclick = useCallback(() => {
    router.push(pagesPath.post._pid(1).$url())
  }, [])

  return <>
    <Link href={pagesPath.post._slug(['a', 'b', 'c']).$url()} />
    <div onclick={onclick} />
  </>
}
```

<a id="Generate-static-files-path-next"></a>

## Generate static files path - Next.js

`package.json`

```json
{
  "scripts": {
    "dev": "run-p dev:*",
    "dev:next": "next dev",
    "dev:path": "pathpida --enableStatic --watch",
    "build": "pathpida --enableStatic && next build"
  }
}
```

```
pages/index.tsx
pages/post/create.tsx
pages/post/[pid].tsx
pages/post/[...slug].tsx

public/aa.json
public/bb/cc.png

lib/$path.ts or utils/$path.ts // Generated automatically by pathpida
```

or

```
src/pages/index.tsx
src/pages/post/create.tsx
src/pages/post/[pid].tsx
src/pages/post/[...slug].tsx

public/aa.json
public/bb/cc.png

src/lib/$path.ts or src/utils/$path.ts // Generated automatically by pathpida
```

`pages/index.tsx`

```tsx
import Link from 'next/link'
import { pagesPath, staticPath } from '../lib/$path'

console.log(staticPath.aa_json) // /aa.json

export default () => {
  return <>
    <Link href={pagesPath.post._slug(['a', 'b', 'c']).$url()} />
    <img src={staticPath.bb.cc_png} />
  </>
}
```

<a id="Setup-nuxt"></a>

## Setup - Nuxt.js

`package.json`

```json
{
  "scripts": {
    "dev": "run-p dev:*",
    "dev:nuxt": "nuxt-ts",
    "dev:path": "pathpida --watch",
    "build": "pathpida && nuxt-ts build"
  }
}
```

`nuxt.config.js` or `nuxt.config.ts`

```js
{
  plugins: ['~/plugins/$path'],
  srcDir: 'client', // optional
  router: {
    trailingSlash: true // optional
  }
}
```

<a id="Usage-nuxt"></a>

## Usage - Nuxt.js

```
pages/index.vue
pages/post/create.vue
pages/post/_pid.tsx

plugins/$path.ts // Generated automatically by pathpida
```

`pages/index.vue`

```vue
<template>
  <div>
    <nuxt-link :to="$pagesPath.post._pid(1).$url()" />
    <div @click="onclick" />
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  methods: {
    onclick() {
      this.$router.push(this.$pagesPath.post._pid(1).$url())
    }
  }
})
</script>
```

<a id="Define-query-nuxt"></a>

## Define query - Nuxt.js

`pages/post/create.vue`

```vue
<script lang="ts">
import Vue from 'vue'

export type Query = {
  userId: number
  name?: string
}

export default Vue.extend({
})
</script>
```

`pages/post/_pid.vue`

```vue
<script lang="ts">
import Vue from 'vue'

export type OptionalQuery = {
  limit: number
  label?: string
}

export default Vue.extend({
})
</script>
```

`pages/index.vue`

```vue
<template>
  <div>
    <nuxt-link :to="$pagesPath.post.create.$url({ query: { userId: 1 }})" />
    <div @click="onclick" />
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  methods: {
    onclick() {
      this.$router.push(this.$pagesPath.post._pid(1).$url())
      this.$router.push(this.$pagesPath.post._pid(1).$url({ query: { limit: 10 }, hash: 'sample' }))
    }
  }
})
</script>
```

### :warning: In the case of Nuxt.js, Query/OptionalQuery type must not contain any reference types.

This is because due to typescript restrictions, types exported from `.vue` files cannot be imported in `plugins/$path.ts`.  
If you want to import types from other files, please use [import types](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-9.html#import-types) with absolute paths.

`types/users.ts`

```ts
export type UserId = number
```

`pages/post/create.vue`

```vue
<script lang="ts">
import Vue from 'vue'

export type Query = {
  userId: import('~/types/users').UserId
  name?: string
}

export default Vue.extend({
})
</script>
```

<a id="Generate-static-files-path-nuxt"></a>

## Generate static files path - Nuxt.js

`package.json`

```json
{
  "scripts": {
    "dev": "run-p dev:*",
    "dev:nuxt": "nuxt-ts",
    "dev:path": "pathpida --enableStatic --watch",
    "build": "pathpida --enableStatic && nuxt-ts build"
  }
}
```

```
pages/index.vue
pages/post/create.vue
pages/post/_pid.vue

static/aa.json
static/bb/cc.png

plugins/$path.ts // Generated automatically by pathpida
```

`pages/index.vue`

```vue
<template>
  <div>
    <nuxt-link :to="$pagesPath.post.create.$url({ query: { userId: 1 }})" />
    <img :src="$staticPath.bb.cc_png" />
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({})
</script>
```

## License

pathpida is licensed under a [MIT License](https://github.com/aspida/pathpida/blob/master/LICENSE).
