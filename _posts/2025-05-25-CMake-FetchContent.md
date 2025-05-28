---
title: CMake FetchContent
author: ghlee
date: 2025-05-25 14:25:00 +0900
categories: [CMake, FetchContent]
tags: [CMake]
pin: true

---


1. 최상위 CMakeLists.txt

```shell
cmake_minimum_required(VERSION 3.22)

project(CppProjectTemplate VERSION 1.0.0 LANGUAGES C CXX)

set(CMAKE_CXX_STANDARD          17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS        OFF)

set(LIBRARY_NAME Library)
set(EXECUTABLE_NAME Executable)

set(CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake/")
include(AddGitSubmodule)
include(FetchContent)

FetchContent_Declare(
    nlohmann_json                               #프로젝트명 - 아래 page의 CMakeLists.txt 파일에 있음.
    GIT_REPOSITORY https://github.com/nlohmann/json
    GIT_TAG v3.11.3                             #Release Tag 확인
    GIT_SHALLOW TRUE)
FetchContent_MakeAvailable(nlohmann_json)

FetchContent_Declare(
    fmt
    GIT_REPOSITORY https://github.com/fmtlib/fmt
    GIT_TAG 10.2.1
    GIT_SHALLOW TRUE)
FetchContent_MakeAvailable(fmt)

FetchContent_Declare(
    spdlog
    GIT_REPOSITORY https://github.com/gabime/spdlog
    GIT_TAG v1.13.0
    GIT_SHALLOW TRUE)
FetchContent_MakeAvailable(spdlog)

FetchContent_Declare(
    cxxopts
    GIT_REPOSITORY https://github.com/jarro2783/cxxopts
    GIT_TAG v3.1.1
    GIT_SHALLOW TRUE)
FetchContent_MakeAvailable(cxxopts)

FetchContent_Declare(
    Catch2
    GIT_REPOSITORY https://github.com/catchorg/Catch2
    GIT_TAG v2.13.9
    GIT_SHALLOW TRUE)
FetchContent_MakeAvailable(Catch2)

add_subdirectory(configured)
add_subdirectory(external)
add_subdirectory(src)
add_subdirectory(app)

```

2. 사용하고자 하는 파일

```shell
set(EXE_SOURCES "main.cpp")
set(EXE_INCLUDES "./")

add_executable(${EXECUTABLE_NAME} ${EXE_SOURCES})
target_include_directories(${EXECUTABLE_NAME} PUBLIC ${EXE_INCLUDES})
target_link_libraries(
  ${EXECUTABLE_NAME} PUBLIC
    ${LIBRARY_NAME}
    nlohmann_json::nlohmann_json
    fmt::fmt
    spdlog::spdlog
    cxxopts::cxxopts)


```

3. 이후 cmake .. & cmake --build .

4. Ninja 설치때문인지 모르겠지만, 되던 빌드와 디버깅이 안되더는 경우가 발생
   → build 폴더 삭제 후, 재시도 시 정상동작 확인
