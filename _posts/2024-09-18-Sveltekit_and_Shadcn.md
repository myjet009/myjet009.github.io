---
title: Sveltekit & Tailwind Css & shad-cn
author: ghlee
date: 2024-09-18 23:00:00 +0900
categories: [SW, Svelte]
tags: [Svelte, Tailwind CSS, Shadcn]
pin: true
math: true
mermaid: true
---

## ♧ Sveltekit & Tailwind Css & shad-cn

[**Start a new SvelteKit project**](https://svelte.dev/docs/introduction/)

[**Install Tailwind CSS with SvelteKit**](https://tailwindcss.com/docs/guides/sveltekit/)

[**shadcn-svelte Install**](https://www.shadcn-svelte.com/docs/installation/sveltekit/)

아래 순서로 개발 환경 설치

```cmd
  1: pnpm create svelte@latest my-app
  2: cd my-app
  3: npm install
  4: npm install -D tailwindcss postcss autoprefixer
  5: npx tailwindcss init -p
  6: npm run dev
  7: npx svelte-add@latest tailwindcss
  8: npx shadcn-svelte@latest init
```

### 8번 shadcn-svelte 설치 화면

![Desktop View](/assets/img/shadcn-svelte_install.png){: width="972" height="589" .w-75 .normal}

### app.css 파일에서 Unknown at rule @tailwind 오류 발생시,

PostCSS Lnaguage Support 설치하면 사라짐.

![Desktop View](/assets/img/postcss_language_support.png){: width="972" height="589" .w-75 .normal}
