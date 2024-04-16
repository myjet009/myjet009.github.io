---
title: Rust TryFrom trait
author: ghlee
date: 2024-04-16 22:46:00 +0900
categories: [SW, Rust]
tags: [Rust]
pin: true
math: true
mermaid: true
---


## ♧ Rust TryFrom trait 관련 링크
### [**# Rust doc 예제 for TryFrom trait**](https://doc.rust-lang.org/rust-by-example/conversion.html)
### [**# Rust doc for TryFrom trait**](https://doc.rust-lang.org/std/convert/trait.TryFrom.html)

<br>

## ☞ Rust TryFrom trait
- 특정 타입을 다른 타입으로 변경 시 사용하는 `trait`이다
    - From trait 의 확장이라고도 할 수 있다.
    - From tarit 과 다른점은 Result를 리턴 한다는 것 → 실패 시, 에러처리가 가능하다.
    - 직접 Error 타입을 설정할 있다.

<br>

- example
    - u8 배열을 Request 구조체로 변경하는 예제이다.
    - 수명과 예외처리에 대해서는 일단 넘어가자.

``` rust
use std::str;

#[derive(Debug)]
pub struct Request<'a> {
    msg : &'a str,
}

impl<'a> TryFrom<&'a [u8]> for Request<'a> {
    type Error = String;

    fn try_from(buf: &'a [u8]) -> Result<Request<'a>, Self::Error> {
        let msg: &str = str::from_utf8(buf).map_err(|e| e.to_string())?;

        Ok(Self {
            msg
        })
    }
}

fn main() {
    let buf:[u8; 3] = [0x31; 3];
    let result = Request::try_from(&buf[..]);

    println!("Output - {:?}", result);
}


결과
Output - Ok(Request { msg: "111" })
```
