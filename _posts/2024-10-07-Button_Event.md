---
title: Button Event
author: ghlee
date: 2024-10-07 23:48:00 +0900
categories: [SW, Svelte]
tags: [Svelte, Sveltekit]
pin: true
math: true
mermaid: true
---

## ♧ Button Event

### ▶ 예제

- 파일: App.svelte

```svelte
<script>
	import Button from './Button.svelte';
</script>

<Button
	on:click|once={(event) => {
		alert(true);
	}}
>
	Button Text
</Button>

<style>
</style>

```

- 파일: Button.svelte

```svelte
<script>
</script>

<button	on:click>
	<slot>Fallback</slot>
</button>

```
