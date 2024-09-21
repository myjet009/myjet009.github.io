---
title: 슬롯을 사용하여 자식 컴포넌트에 접근
author: ghlee
date: 2024-09-21 23:53:00 +0900
categories: [SW, Svelte]
tags: [Svelte, Sveltekit]
pin: true
math: true
mermaid: true
---

## ♧ 슬롯을 사용하여 자식 컴포넌트에 접근

파일: App.svelte

```svelte
<script>
	import Button from './Button.svelte';
</script>

<Button>Text</Button> // Text가 없을 경우, Slot Text 문구가 표시됨...
```

파일: Button.svelte

```svelte
<button><slot>Slot Text</slot></button>

<style>
	button {
		border: none;
		background-color: blue;
		color: white;
		padding: 15px 20px;
		font-weight: bold;
		border-radius: 5px;
		cursor: pointer;
	}
</style>

```
