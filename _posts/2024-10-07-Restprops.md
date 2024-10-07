---
title: RestProps
author: ghlee
date: 2024-10-07 23:55:00 +0900
categories: [SW, Svelte]
tags: [Svelte, Sveltekit]
pin: true
math: true
mermaid: true
---

## ♧ RestProps

> - 설명  
>   Child에서 $$restprops로 Parent에 정의된 값을 사용할 수 있다.

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
	restprops1
	disabled
>
	Button Text
</Button>

```

- 파일: Button.svelte

```svelte
<script>
</script>

<button
	on:click
	{...$$restProps}
>
	{#if $$restProps.restprops1}
		<div>
			restprops1
		</div>
	{/if}
	<slot {isLeftHovered}>Fallback</slot>
</button>

```
