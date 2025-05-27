---
title: CMake CTest 및 debugging 설정 
author: ghlee
date: 2025-05-25 22:56:00 +0900
categories: [CMake, CTest]
tags: [CMake]
pin: true

---


1. 최상위 CMakeLists.txt

```shell
cmake_minimum_required(VERSION 3.22)

project(
    CppProjectTemplate
    VERSION 1.0.0
    LANGUAGES C CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

option(ENABLE_TESTING "Enable a Unit Testing Build" ON)

set(LIBRARY_NAME "Library")
set(EXECUTABLE_NAME "Executable")

set(CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake/")
include(AddGitSubmodule)
include(FetchContent)
include(Docs)

FetchContent_Declare(
    nlohmann_json
    GIT_REPOSITORY https://github.com/nlohmann/json
    GIT_TAG v3.11.3
    GIT_SHALLOW TRUE)
FetchContent_MakeAvailable(nlohmann_json)

FetchContent_Declare(
    fmt
    GIT_REPOSITORY https://github.com/fmtlib/fmt
    GIT_TAG 9.1.0
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

if(ENABLE_TESTING)
    FetchContent_Declare(
        Catch2
        GIT_REPOSITORY https://github.com/catchorg/Catch2
        GIT_TAG v3.5.3
        GIT_SHALLOW TRUE)
    FetchContent_MakeAvailable(Catch2)
    list(APPEND CMAKE_MODULE_PATH ${catch2_SOURCE_DIR}/extras)
endif()

add_subdirectory(configured)
add_subdirectory(external)
add_subdirectory(src)
add_subdirectory(app)
if(ENABLE_TESTING)
    include(CTest) # CTest 관련 기능 추가 (예: ctest 명령 사용 가능)
    enable_testing() # CMake에게 "테스트 활성화하겠다"고 알림
    add_subdirectory(tests) # tests/CMakeLists.txt 안에 있는 테스트 정의들을 빌드에 포함시킴
endif()


```

2. tests/CMakeLists.txt

```shell
include(Catch)

set(TEST_MAIN "unit_tests")
set(TEST_SOURCES "main.cc")
set(TEST_INCLUDES "./")

add_executable(${TEST_MAIN} ${TEST_SOURCES})
target_include_directories(${TEST_MAIN} PUBLIC ${TEST_INCLUDES})
target_link_libraries(${TEST_MAIN} PUBLIC ${LIBRARY_NAME} Catch2::Catch2WithMain)

catch_discover_tests(${TEST_MAIN})


```

3. launch.json 파일

```shell
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(gdb) Launch",
            "type": "cppdbg",
            "request": "launch",
            "program": "${command:cmake.launchTargetPath}",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [
                {
                    "name": "PATH",
                    "value": "${env:PATH}:${command:cmake.getLaunchTargetDirectory}"
                }
            ],
            "externalConsole": false, # WSL에서 true로 하면 타임아웃 걸림
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ]
        },
        {
            "name": "(lldb) Launch",
            "type": "cppdbg",
            "request": "launch",
            "program": "${command:cmake.launchTargetPath}",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [
                {
                    "name": "PATH",
                    "value": "${env:PATH}:${command:cmake.getLaunchTargetDirectory}"
                }
            ],
            "externalConsole": false,
            "MIMode": "lldb"
        },
        {
            "name": "(msvc) Launch",
            "type": "cppvsdbg",
            "request": "launch",
            "program": "${command:cmake.launchTargetPath}",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [
                {
                    "name": "PATH",
                    "value": "${env:PATH}:${command:cmake.getLaunchTargetDirectory}"
                }
            ],
            "externalConsole": false
        }
    ]
}

```


- launch.json 파일 주요 포인트

1. "${command:cmake.launchTargetPath}"
이 부분은 CMake Tools 확장 기능이 제공하는 명령입니다.
CMake가 빌드한 실행 파일 경로를 자동으로 가져옵니다.
즉, CMake를 통해 빌드된 결과물을 디버깅하는 것이죠.

2. cppdbg
VS Code의 **C/C++ 디버거 확장(Microsoft C/C++ extension)**이 사용하는 디버깅 타입입니다.
GDB를 사용하여 디버깅을 수행합니다.

3. launch.json
이 설정은 VS Code에서 수동으로 디버깅 구성을 정의한 것입니다.
빌드는 CMake가 하고, 디버깅 실행은 VS Code가 gdb로 합니다.

