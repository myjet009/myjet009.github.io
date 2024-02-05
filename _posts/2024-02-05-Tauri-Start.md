---
title: Tauri 시작
author: ghlee
date: 2024-02-05 22:21:00 +0900
categories: [새로운, SW]
tags: [Tauri]
pin: true
math: true
mermaid: true
image:
  path: /assets/img/tauri.png
  lqip: data:image/webp;
  alt: Welcome to Tauri!
---

# [**Tauri**](https://tauri.app/ko/) 를 시작해 봅시다.
- 개발환경: Windows 10
- 준비 - 사전 설치
  - Rust
  - Microsoft Visual Studio C++ Build Tools
    - Rust 설치할 때, 같이 설치한 기억이 있다.
  - 더 있을 수 있으나 Pass → 구글 검색 gogo!!


### [**Create Tauri App**](https://www.npmjs.com/package/create-tauri-app?activeTab=readme) 
![Desktop View](/assets/img/create_tauri_app.png){: width="972" height="589" } 


#### 1. Tauri app 생성 시작
```
npm create tauri-app@latest
```
![Desktop View](/assets/img/create_tauri_app_start.png){:.w-75 .normal}
- 녹색 표시 선택  
- **cmd 창에서 실행**
  - gitbash 에서 실행 시, os error 발생 


#### 2. npm install
```
cd tauri-app-test
npm install
```

#### 3. npm run tauri dev
```
npm run tauri dev
```
- 최초 빌드 실행 시, 시간 걸림
- DeskTop App 실행된 것을 확인할 수 있다


#### 4. tailwindcss 설치
```
npx svelte-add@latest add tailwindcss
```
- **`no such file of directory` 에러 발생할 경우, 해당 경로에 npm 폴더 생성하면 된다.**


#### 5. 기본 준비 완료
- 일단 준비만 해놓고...