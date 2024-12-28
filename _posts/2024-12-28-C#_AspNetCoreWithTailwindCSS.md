---
title: Asp.Net Core With TailwindCSS
author: ghlee
date: 2024-12-28 22:37:00 +0900
categories: [SW, Svelte]
tags: [C#, WebApp]
pin: true
math: true
mermaid: true
---

## ♧ Asp.Net Core With TailwindCSS

### 1. wwwroot.lib.bootstrap 폴더 삭제

### 2. [**https://tailwindcss.com/docs/installation 참고**](https://tailwindcss.com/docs/installation)

```npm
  - npm install -D tailwindcss
  - npx tailwindcss init
```

### 3. tailwind.config.js 에서 아래내용 적용

```js
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./Views/**/*.{cshtml,razor}",
    "./Pages/**/*.{cshtml,razor}",
    "./wwwroot/js/**/*.js"
  ],
  theme: {
    extend: {}
  },
  plugins: []
};
```

### 4. wwwroot/css/input.css 와 wwwroot/css/output.css 파일 추가

### 5. input.css 파일에서 아래 3줄 추가

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

### 6. Pages/Shared/\_Layout.cshtml 파일에서 아래와 같이 변경

```
// <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.min.css" />
<link rel="stylesheet" href="~/css/output.css" />
<link rel="stylesheet" href="~/css/site.css" asp-append-version="true" />
<link rel="stylesheet" href="~/ProjectManagerWeb.styles.css" asp-append-version="true" />
```

### 6.package.json 파일에서 아래 추가

```json
"scripts": {
	"tw:watch": "npx tailwindcss -i ./wwwroot/css/input.css -o ./wwwroot/css/output.css --watch",
	"tw:dev": "npx tailwindcss -i ./wwwroot/css/input.css -o ./wwwroot/css/output.css",
	"tw:build": "npx tailwindcss -i ./wwwroot/css/input.css -o ./wwwroot/css/output.css --minify"
},
```

### 7. npm run tw:watch 로 사용하면 됨
