---
title: Cmake 라이브러리 상호 의존
author: ghlee
date: 2025-05-22 23:25:00 +0900
categories: [CMake]
tags: [CMake]
pin: true

---

## 폴더 구조
```t
project_root/
├── CMakeLists.txt
└── src/
    ├── libA/  # libA 가 libB 를 include 함
    │   ├── libA.cpp
    │   ├── libA.h
    │   └── CMakeLists.txt
    └── libB/
        ├── libB.cpp
        ├── libB.h
        └── CMakeLists.txt
```

## 각 디렉토리별 CMake 설정
- src/libB/CMakeLists.txt 
```shell
add_library(libB STATIC libB.cpp)
target_include_directories(libB PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
```
→ PUBLIC으로 설정하면, libB를 사용하는 다른 라이브러리도 libB.h를 include할 수 있습니다.


- src/libA/CMakeLists.txt 
```shell
add_library(libA STATIC libA.cpp)
target_include_directories(libA PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
target_link_libraries(libA PUBLIC libB)  # libA가 libB에 의존
```
→ target_link_libraries(libA PUBLIC libB)를 사용하면:
  libA.cpp가 #include "libB.h" 가능
  libA를 사용하는 다른 라이브러리/실행파일도 libB를 자동으로 링크함


- root CMakeLists.txt   
```
cmake_minimum_required(VERSION 3.16)
project(MultiLibExample)

add_subdirectory(src/libB)
add_subdirectory(src/libA)

# 실행 파일 만들고 libA, libB 연결
add_executable(my_app main.cpp)
target_link_libraries(my_app PRIVATE libA)
```

→ libA만 링크하면, libB는 자동으로 링크됨 (PUBLIC 덕분)



- 코드 예시: libA.cpp 
```
#include "libA.h"
#include "libB.h"  // OK: CMake가 include path 설정함

void funcA() {
    funcB();
}
```