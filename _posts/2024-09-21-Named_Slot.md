---
title: Named Slot
author: ghlee
date: 2024-10-02 23:21:00 +0900
categories: [SW, Svelte]
tags: [Svelte, Sveltekit]
pin: true
math: true
mermaid: true
---

## ♧ Named Slot

### ▶ named slot 존재 여부를 확인 후, 처리 방법

- 파일: App.svelte
  - 슬롯 number가 있으면 1 표시, 없으면 no number가 표시된다.

```svelte
<script>
	import Button from './Button.svelte';
</script>

<Button>
	<div slot="number">1</div>
	Button Text
</Button>
```

<br>

- 파일: Button.svelte

```svelte
<script>
</script>

<button>
	{#if $$slots.number}
		<slot name="number" />
	{:else}
		<div>
			no number
		</div>
	{/if}
	<slot>Fallback</slot>
</button>

```

<br>

### ▶ named slot 호출이 안 될 경우, 기본값 사용 방법

- 파일: App.svelte
  - slot 호출 자체를 안 할 경우, default number
  - slot 호출하면, 적용된 값을 표시(아래의 경우: number1)

```svelte
<script>
	import Button from './Button.svelte';
</script>

<Button>
	<div slot="number">number1</div> // 이 라인이 없을 경우, default number 표시됨

	Button Text
</Button>
```

<br>

- 파일: Button.svelte

```svelte
<script>
</script>

<button>
	<slot name="number">default number</slot>
	<slot>Fallback</slot>
</button>

```
