---
title: CMake && SK-AM62x Remote Debugging
author: ghlee
date: 2025-05-22 23:25:00 +0900
categories: [CMake, SK-AM62x Remote Debugging]
tags: [CMake]
pin: true
---

1. tasks.json

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Build and Copy to AM62X",
          "type": "shell",
          "command": "ssh root@192.168.10.2 'rm -rdf /home/test1' && scp -r ./build/aarch64/app/ root@192.168.10.2:/home/test1",
          "group": 
          {
            "kind": "build",
            "isDefault": true
          },
          "problemMatcher": []
    },
    {
      "label": "🔁 Build & Deploy strip to AM62X",
      "type": "shell",
      "command": "aarch64-oe-linux-strip -o ./build/aarch64/app/Executable.stripped ./build/aarch64/app/Executable && scp ./build/aarch64/app/Executable.stripped root@192.168.10.2:/home/test1/Executable && ssh root@192.168.10.2 'pkill gdbserver; gdbserver :1234 /home/test1/Executable'",
      "problemMatcher": [],
      "group": 
      {
        "kind": "build",
        "isDefault": true
      },
      "options": 
      {
        "env": 
        {
          "PATH": "/home/lgh/ti-processor-sdk-linux-am62xx-evm-11.00.09.04/linux-devkit/sysroots/x86_64-arago-linux/usr/bin/aarch64-oe-linux:${env:PATH}"
        }
      }
    }
  ]
}

```

2. launch.json

```json
{
    "version": "0.2.0",
    "configurations": [
      {
          "name": "🔍 Remote Debug (SK-AM62X)",
          "type": "cppdbg",
          "request": "launch",
          "program": "${workspaceFolder}/build/aarch64/app/Executable",
          "miDebuggerServerAddress": "192.168.10.2:1234",
          "miDebuggerPath": "/home/lgh/ti-processor-sdk-linux-am62xx-evm-11.00.09.04/linux-devkit/sysroots/x86_64-arago-linux/usr/bin/aarch64-oe-linux/aarch64-oe-linux-gdb",
          "cwd": "${workspaceFolder}",
          "environment": [],
          "externalConsole": false,
          "MIMode": "gdb",
          "setupCommands": [
              {
              "description": "Enable pretty-printing",
              "text": "-enable-pretty-printing",
              "ignoreFailures": true
              }
          ]
      },
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
          "externalConsole": false,
          "MIMode": "gdb",
          "setupCommands": [
              {
                  "description": "Enable pretty-printing for gdb",
                  "text": "-enable-pretty-printing",
                  "ignoreFailures": true
              }
          ]
      }
    ]
}
```

3. CMakePresets.json

```json
  "CMAKE_BUILD_TYPE": "Debug" // Release하면 디버깅 안됨...
```


4. 최상위 CMakeLists.txt

```shell
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

추가하면 빌드폴더에 compile_commands.json 파일 생성됨
cat ./build/aarch64/compile_commands.json | grep "\-g" # 이 명령어로 -g flag 있는지 확인

```
