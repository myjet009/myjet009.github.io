---
title: Rust Lib에서 여러 파일의 main 함수 개별 실행
author: ghlee
date: 2024-04-17 23:01:00 +0900
categories: [SW, Rust]
tags: [Rust]
pin: true
math: true
mermaid: true
---

## ♧ Rust Lib에서 여러 파일의 main 함수 개별 실행
- git-hub 자료를 받아서 살펴보면 각각의 파일마다 main 함수가 있고 개별로 실행할 수가 있다.
- 나도 rust-playground 말고 vscode에서 샘플 코드를 만들려고 하다보니, 해당 기능이 필요해서 간단히 기록해본다.

<br>

- 간단하다... 아래와 같이 bin이 아닌 lib로 패키지를 생성하면 된다.
``` 
cargo new [package name] --lib
```


- 아래 캡쳐를 보면, test123이라는 패키지의 examples 폴더 안에 error_handle.rs 와  test_handle.rs 파일에서 각 main 함수 위에 `▶Run|Debug` 를 볼 수 있다. 클릭하면 해당 main 함수가 실행된다.
![Desktop View](/assets/img/lib_mains.png){: width="972" height="589" } 

- cli로 실행하는 방법

```
- cargo run --package test123 --example error_handle
- cargo run --package test123 --example test_handle
```