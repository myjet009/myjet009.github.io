---
title: Svelte 프로젝트에 SCSS 및 prependData 적용하기
author: ghlee
date: 2024-09-22 23:45:00 +0900
categories: [SW, Svelte]
tags: [Svelte, Sveltekit, Scss]
pin: true
math: true
mermaid: true
---

## ♧ Svelte 프로젝트에 SCSS 및 prependData 적용하기

### 1. svelte preprocess 설치

```
npm i -D sass

npm i -D svelte-preprocess
```

svelte.config.js 파일에서, 아래와 같이 -는 주석처리, +는 추가 하면된다.

```javascript

- //import { vitePreprocess } from '@sveltejs/vite-plugin-svelte';
+ import { sveltePreprocess } from 'svelte-preprocess';

- // preprocess: vitePreprocess(),

+ preprocess: sveltePreprocess({
+     scss: {
+     },
+ }),
```

아래는 적용 예시
파일: svelte.config.js

```javascript
import adapter from "@sveltejs/adapter-auto";
//import { vitePreprocess } from '@sveltejs/vite-plugin-svelte';
import { sveltePreprocess } from "svelte-preprocess";

/** @type {import('@sveltejs/kit').Config} */
const config = {
  // 	// Consult https://kit.svelte.dev/docs/integrations#preprocessors
  // 	// for more information about preprocessors
  // 	preprocess: vitePreprocess(),

  // svelte-preprocess를 사용하여 SCSS를 처리할 수 있도록 설정
  preprocess: sveltePreprocess({
    scss: {
      // SCSS 파일에서 사용할 전역 변수를 정의하거나 추가적으로 설정할 수 있습니다.
      // prependData: `@import 'src/styles/variables.scss';` // 예시로 변수를 사용할 경우
    }
    // 기타 필요한 설정 추가
  }),

  kit: {
    // adapter-auto only supports some environments, see https://kit.svelte.dev/docs/adapter-auto for a list.
    // If your environment is not supported, or you settled on a specific environment, switch out the adapter.
    // See https://kit.svelte.dev/docs/adapters for more information about adapters.
    adapter: adapter()
  }
};

export default config;
```

<br>

### 2. scss에 prependData 적용하기

#### 2-1. scss에 prependData 적용하기 (사용 전)

파일: 'src/styles/variables.scss' 아래와 같이 작성

```scss
$color: blue;
```

파일: Button.svelte  
@use "../styles/variables.scss";를 각 파일의 style에 적용해야 한다.

```javascript
<button><slot>Slot Text</slot></button>

<style lang="scss">
  button {
    @use "../styles/variables.scss";
    border: none;
    background-color: blue;
    color: white;
    padding: 15px 20px;
    font-weight: bold;
    border-radius: 5px;
    cursor: pointer;

    &:hover {
      background-color: variables.$color;
    }
  }
</style>
```

#### 2-2. scss에 prependData 적용하기 (사용 후)

파일: svelte.config.js

```javascript
  // svelte-preprocess를 사용하여 SCSS를 처리할 수 있도록 설정
  preprocess: sveltePreprocess({
    scss: {
      // SCSS 파일에서 사용할 전역 변수를 정의하거나 추가적으로 설정할 수 있습니다.
      prependData: `@import 'src/styles/variables.scss';` // 예시로 변수를 사용할 경우
    }
    // 기타 필요한 설정 추가
  }),
```

파일: 'src/styles/variables.scss' 아래와 같이 작성

```scss
$color: blue;
```

파일: Button.svelte  
@use "../styles/variables.scss"; 를 삭제해도 전역으로 적용된다.

```javascript
<button><slot>Slot Text</slot></button>

<style lang="scss">
  button {
    border: none;
    background-color: blue;
    color: white;
    padding: 15px 20px;
    font-weight: bold;
    border-radius: 5px;
    cursor: pointer;

    &:hover {
      background-color: variables.$color;
    }
  }
</style>
```
