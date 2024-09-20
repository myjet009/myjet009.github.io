---
title: Svelte 반응형 문장의 함정 '$:'
author: ghlee
date: 2024-09-20 23:40:00 +0900
categories: [SW, Svelte]
tags: [Svelte, Sveltekit]
pin: true
math: true
mermaid: true
---

## ♧ Svelte 반응형 문장의 함정 - $:

1-1. 정상동작

```javascript
<script>
  let count1 = 0;
  let count2 = 0;

  $: string = `The count total is ${count1 + count2}.`; // count1, count2가 보여야 함...

  function increment1() {
    count1 += 1;
  }

  function increment2() {
    count2 += 1;
  }
</script>

<button on:click={increment1}>Clicks {count1}</button>
<button on:click={increment2}>Clicks {count2}</button>
<h3>{string}</h3>
```

2-2. 미동작

```javascript
<script>
  let count1 = 0;
  let count2 = 0;

  function getTotal() {
    return count1 + count2;
  }

  $: string = `The count total is ${getTotal()}.`; // <- 변화없음 - 함수만으로는 추론할 수 없음

  function increment1() {
    count1 += 1;
  }

  function increment2() {
    count2 += 1;
  }
</script>

<button on:click={increment1}>Clicks {count1}</button>
<button on:click={increment2}>Clicks {count2}</button>
<h3>{string}</h3>
```

2-1. count2 동작안함

```javascript
<script>
  let count1 = 0;
  let count2 = 0;

  function setCount2(x) {
    return count2 = x;
  }

  $: string = `Count2 is ${count2}.`;
  $: setCount2(count1); // count2보다 아래에 있어서 동작 안 함

  function increment1() {
    count1 += 1;
  }
</script>

<button on:click={increment1}>Clicks {count1}</button>
<h3>{string}</h3>
```

2-2. count2 동작

```javascript
<script>
  let count1 = 0;
  let count2 = 0;

  function setCount2(x) {
    return count2 = x;
  }

  $: setCount2(count1); // count1 위치가 count2 위에 있어야 count2가 동작함
  $: string = `Count2 is ${count2}.`;

  function increment1() {
    count1 += 1;
  }
</script>

<button on:click={increment1}>Clicks {count1}</button>
<h3>{string}</h3>
```
