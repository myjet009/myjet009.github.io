---
title: VS Code에서 Visual Studio 빌드
author: ghlee
date: 2025-09-21 23:14:00 +0900
categories: [VS Code, Visual Studio]
tags: [VS Code]
pin: true

---

### tasks.json 파일에서 아래와 같이 사용

- Visual Studio 설치 없이, 인스톨러에서 tool만 설치하며 빌드 진행

```json
{
    "version": "2.0.0",
    "tasks": [
      {
        "label": "Build .NET Framework",
        "type": "shell",
        "command": "C:/Program Files (x86)/Microsoft Visual Studio/2022/BuildTools/MSBuild/Current/Bin/MSBuild.exe",
        "args": [
          "${workspaceFolder}/../project.csproj",
          "/p:Configuration=Debug"
        ],
        "group": "build",
        "problemMatcher": "$msCompile"
      }
    ]
  }
```
