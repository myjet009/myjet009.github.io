---
title: STM32F CMAKE bin, hex 생성
author: ghlee
date: 2025-07-03 23:17:00 +0900
categories: [CMake, ST]
tags: [CMake]
pin: true

---

▶ STM32 CMAKE bin, hex 설정
root 폴더에 있는 CMakeLists.txt 에러 아래 추가
참고: GitHub - MaJerle/stm32-cube-cmake-vscode: STM32, VSCode and CMake detailed tutorial

```bash
# Execute post-build to print size
add_custom_command(TARGET ${CMAKE_PROJECT_NAME} POST_BUILD
    COMMAND ${CMAKE_SIZE} $<TARGET_FILE:${CMAKE_PROJECT_NAME}>
)

# Convert output to hex and binary
add_custom_command(TARGET ${CMAKE_PROJECT_NAME} POST_BUILD
    COMMAND ${CMAKE_OBJCOPY} -O ihex $<TARGET_FILE:${CMAKE_PROJECT_NAME}> ${CMAKE_PROJECT_NAME}.hex
)

# Convert to bin file -> add conditional check?
add_custom_command(TARGET ${CMAKE_PROJECT_NAME} POST_BUILD
    COMMAND ${CMAKE_OBJCOPY} -O binary $<TARGET_FILE:${CMAKE_PROJECT_NAME}> ${CMAKE_PROJECT_NAME}.bin
)
```
