---
title: CMake CrossCompile
author: ghlee
date: 2025-05-27 23:34:00 +0900
categories: [CMake, CrossComplie]
tags: [CMake]
pin: true

---


4. aarch64-cross-toolchain.cmake

```shell

# aarch64-toolchain.cmake

set(CMAKE_SYSTEM_NAME Linux)
set(CMAKE_SYSTEM_PROCESSOR aarch64)

# 툴체인 경로: 환경에 맞게 수정
set(TOOLCHAIN_DIR "$ENV{HOME}/ti-processor-sdk-linux-am62xx-evm-11.00.09.04/linux-devkit/sysroots/x86_64-arago-linux/usr/bin/aarch64-oe-linux")

set(CMAKE_C_COMPILER   ${TOOLCHAIN_DIR}/aarch64-oe-linux-gcc)
set(CMAKE_CXX_COMPILER ${TOOLCHAIN_DIR}/aarch64-oe-linux-g++)

# sysroot 경로 설정 (필요 시)
set(CMAKE_SYSROOT "$ENV{HOME}/ti-processor-sdk-linux-am62xx-evm-11.00.09.04/linux-devkit/sysroots/aarch64-oe-linux")

set(CMAKE_FIND_ROOT_PATH ${CMAKE_SYSROOT})
set(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)
set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)
```

5. x86-64-native-toolchain.cmake

```shell

set(CMAKE_SYSTEM_NAME Linux)
set(CMAKE_SYSTEM_PROCESSOR x86_64)

set(CMAKE_C_COMPILER gcc)
set(CMAKE_CXX_COMPILER g++)

set(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)
set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)
set(CMAKE_FIND_ROOT_PATH_MODE_PACKAGE ONLY)
```



1. CMakePresets.json 사용 없이, 아래처럼 수동으로 실행가능

```
mkdir build
cd build
cmake .. -DCMAKE_TOOLCHAIN_FILE=../Toolchains/aarch64-cross-toolchain.cmake
make -j4
```

2. CMakePresets.json 추가 프로젝트 구조

```
.
├── CMakeLists.txt
├── CMakePresets.json
├── README.md
├── Toolchains
│   └── aarch64-cross-toolchain.cmake 
├── inc
│   └── lib.h
└── src
    └── lib.cc

```


3. CMakePresets.json

```json
{
  "version": 3,
  "cmakeMinimumRequired": {
    "major": 3,
    "minor": 19
  },
  "configurePresets": [
    {
      "name": "aarch64",
      "description": "Cross-compile for AARCH64",
      "generator": "Unix Makefiles",
      "binaryDir": "${sourceDir}/build/aarch64",
      "toolchainFile": "${sourceDir}/Toolchains/aarch64-cross-toolchain.cmake",
      "cacheVariables": {
        "CMAKE_BUILD_TYPE": "Release"
      }
    },
    {
      "name": "native",
      "description": "GCC",
      "generator": "Unix Makefiles",
      "binaryDir": "${sourceDir}/build/gcc",
      "toolchainFile": "${sourceDir}/Toolchains/x86-64-native-toolchain.cmake",
      "cacheVariables": {
        "CMAKE_BUILD_TYPE": "Release"
      }
    }
  ],
    "buildPresets": [
    {
      "name": "aarch64",
      "configurePreset": "aarch64"
    },
    {
      "name": "native",
      "configurePreset": "native"
    }
  ]
}

```



6. 선택하여 실행가능

```
cmake --preset native
cmake --build --preset native
```

```
cmake --preset aarch64
cmake --build --preset aarch64
```

7. vscode에서 preset 쉽게 사용법
참고 사이트 : https://github.com/microsoft/vscode-cmake-tools/blob/HEAD/docs/cmake-presets.md


8. Ninja 설치 후, 재적용

```
sudo apt update
sudo apt install ninja-build
ninja --version

아래처럼 변경
"generator": "Ninja"
```
