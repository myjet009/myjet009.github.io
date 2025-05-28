---
title: CMake scp && rsync
author: ghlee
date: 2025-05-28 23:34:00 +0900
categories: [CMake, SCP && RSYNC]
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
      "command": "ssh root@192.168.10.2 'rm -rdf /home/test1' && scp -r ./build/aarch64/app/ root@192.168.10.2:/home/test1/",
      //"command": "scp -r ./build/aarch64/app root@192.168.10.2:/home/test1",
      "group": {
      "kind": "build",
      "isDefault": true
      },
      "problemMatcher": []
    },
    {
      "label": "Sync to AM62X",
      "type": "shell",
      // "options": {
      //  "env": {
      //  "PATH": "/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin"
      //  }
      // },
      "command": "rsync -avz --delete ./build/aarch64/app/ root@192.168.10.2:/home/test1/",
      // no internet
      /*
      항목                             내용
      rsync -avz                      archive mode, verbose, 압축
      --delete                        대상 경로에 불필요한 파일이 있으면 삭제 (선택)
      ./build/aarch64/app/            로컬 파일 경로
      root@192.168.10.2:/home/test1/  보드의 경로
      */
      "problemMatcher": [],

      "group": {
      "kind": "build",
      "isDefault": true
      }
    }
  ]
}

```
