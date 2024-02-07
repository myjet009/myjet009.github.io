---
title: Rust Debugging in VS Code 
author: ghlee
date: 2024-02-07 21:46:00 +0900
categories: [SW, Rust]
tags: [Rust]
pin: true
math: true
mermaid: true
---

### ☞ Rust 설치는 [**여기 클릭**](../Rust-Start)

### ☞ Build
- 프로젝트 생성 후, 별도 설정없이 `ctrl+shift+b` 를 누르면 아래와 같이 빌드를 선택해주면 빌드가 된다.

![Desktop View](/assets/img/build_in_vscode.png)

### ☞ Debugging 
- `ctrl+shift+p` 검색창에서 `cargo`를 치고 검색된 LLDB:... 를 선택하면 자동으로 debugging을 위한 json 형식의 텍스트가 생성된다.

![Desktop View](/assets/img/debug_in_vscode.png)


 - 아래는 생성된 json 텍스트
 - .vscode/launch.json 파일에 내용 붙여넣기 (폴더/파일 없을 시 생성)
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "type": "lldb",
            "request": "launch",
            "name": "Debug executable 'test111'",
            "cargo": {
                "args": [
                    "build",
                    "--bin=test111",
                    "--package=test111"
                ],
                "filter": {
                    "name": "test111",
                    "kind": "bin"
                }
            },
            "args": [],
            "cwd": "${workspaceFolder}"
        },
        {
            "type": "lldb",
            "request": "launch",
            "name": "Debug unit tests in executable 'test111'",
            "cargo": {
                "args": [
                    "test",
                    "--no-run",
                    "--bin=test111",
                    "--package=test111"
                ],
                "filter": {
                    "name": "test111",
                    "kind": "bin"
                }
            },
            "args": [],
            "cwd": "${workspaceFolder}"
        }
    ]
}
```

- 작업 완료 후, F5 를 누르면 아래와 같이 사용 가능

![Desktop View](/assets/img/debug_run_in_vscode.png)

<br><br>

### 기본적인 설치 및 개발환경 작업은 완료했으니... 