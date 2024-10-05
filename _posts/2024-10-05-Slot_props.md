---
title: Slot Props
author: ghlee
date: 2024-10-05 23:49:00 +0900
categories: [SW, Svelte]
tags: [Svelte, Sveltekit]
pin: true
math: true
mermaid: true
---

## ♧ Slot Props

> - 설명  
>   Slot Props는 Child의 필드를 Parent에 전달하기 위하여 사용한다.  
>   변수 뿐만 아니라 함수도 전달할 수 있다.

### ▶ 사용법1: Svelte example and more

- 파일: App.svelte

```svelte
<script>
	let name = 'world';
	import Outer from "./Outer.svelte"
	import Inner from "./Inner.svelte"
</script>

<h1>Hello {name}!</h1>

<Outer>
	// outer에 있는 slot을 Inner에 적용할 수도 있는 듯 하다?
	<Inner slot="inner" let:alert let:click on:click={alert}>
		{#if click > 5}
			<div>
				clicked more than 5
			</div>
		{/if}
	</Inner>
</Outer>
```

- 파일: Outer.svelte

```svelte
<script>
	let click = 0;

	function alert(){
		click += 1;
		console.log("alert")
	}
</script>

<slot name="inner" {click} {alert}></slot> // alret 함수와 click 변수를 Parent에 전달
```

- 파일: Inner.svelte

```svelte
<p on:click>Click me!</p>
<slot/>
```

<br>

### ▶ 사용법2

> - 설명  
>   Button의 isHovered를 parent에서 사용

- 파일: App.svelte

```svelte
<script>
	let name = 'world';
	import Button from "./Button.svelte"
</script>

<h1>Hello {name}!</h1>

<Button let:isHovered>
	{#if isHovered}
		<div>
			isHovered
		</div>
	{/if}
	Button!
</Button>
```

- 파일: Button.svelte

```svelte
<script>
	let isHovered = false;
</script>

<button on:mouseenter={()=>{isHovered = true}} on:mouseleave={()=>{isHovered = false}}>
	<slot {isHovered}>test</slot>
</button>
```
