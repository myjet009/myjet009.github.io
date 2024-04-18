---
title: Rust Result 고급 처리
author: ghlee
date: 2024-04-18 22:30:00 +0900
categories: [SW, Rust]
tags: [Rust]
pin: true
math: true
mermaid: true
---

## ♧ Rust Result 고급 처리
- Result 처리를 고급스럽게 처리를 해보자.
- 동일하게 동작하는 총 4개의 방법을 구현했다.      뒤로 갈 수록 점점 간결해지는 코드이다.    
물론 간결해 질수록 분석시간이 걸릴 수도 있지만, 코드가 점점 더 길어질수록 장점이 더 많을 수 있다.
- 다른 언어에서 `?`는 보통 null을 의미하지만, 러스트에서는 Error 발생 시 `return` 을 의미하다.     
 물론 return을 위해서 return type이 구현되어 있어야 한다.
<br>

### ☞ Sample Code

``` rust

use std::str;
use std::str::Utf8Error;

#[derive(Debug)]
pub struct Request {
    msg: String,
}

impl TryFrom<&[u8]> for Request {
    type Error = ParsingError;

    fn try_from(buf: &[u8]) -> Result<Request, Self::Error> {
        
        // 첫 번째 방법
        match str::from_utf8(buf) {
            Ok(v) => {
                println!("First Ok: {v}");
            }
            Err(e) => return Err(ParsingError::Invalid),
        }

        // 두 번째 방법
        match str::from_utf8(buf).or(Err(ParsingError::Invalid)) {
            Ok(v) => {
                println!("Second Ok: {}", v);
            }
            Err(e) => return Err(e),
        }

        // 세 번째 방법
        let v = str::from_utf8(buf).or(Err(ParsingError::Invalid))?;
        println!("Third Ok: {}", v);

        // 네 번째 방법: 'impl From<Utf8Error> for ParseError' 구현하여 사용할 수 있다.
        let v = str::from_utf8(buf)?;
        println!("4th Ok: {}", v);

        Ok(Self {
            msg: "Ok".to_string(),
        })

        // 번외(error 발생) - Error 타입이 String일 경우, 정상 동작한다.
        //str::from_utf8(buf).map_err(|e| e.to_string())?;
    }
}

pub enum ParsingError {
    Invalid,
}

// Utf8Error 을 ParsingError 로 변경해주는 From Trait 구현
impl From<Utf8Error> for ParsingError {
    fn from(_: Utf8Error) -> Self {
        ParsingError::Invalid
    }
}

fn main() {
    let buf: [u8; 4] = [0xF0, 0x9F, 0x98, 0x81]; //return emoji
    //let buf: [u8; 4] = [0xF0, 0x9F, 98, 0x81]; //return error
    let result = Request::try_from(&buf[..]);

    if let Ok(v) = result {
        println!("Output - {:?}", v);
        return;
    };

    println!("Output Error - {:?}", result);
}

```

``` 
- let buf: [u8; 4] = [0xF0, 0x9F, 0x98, 0x81]
정상 결과: 이모지 출력
`
First Ok: 😁
Second Ok: 😁
Third Ok: 😁
5th Ok: 😁
Output - Request { msg: "Ok" }
`

- let buf: [u8; 4] = [0xF0, 0x9F, 98, 0x81];
에러 결과: 이 배열의 조합으로 문자열 슬라이스를 만들 수가 없다.
`
Output Error - Err(Invalid)
`
```