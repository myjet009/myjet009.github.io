---
title: Linux Command
author: ghlee
date: 2025-05-30 00:50:00 +0900
categories: [Linux, Command]
tags: [LinuxCommand]
pin: true

---

### -> gdbserver 동작 확인 및 종료

```shell
확인명령어: ps -ef | grep gdbserver
root       17201   17200  0 02:37 ?        00:00:00 gdbserver :1234 /home/test1/Executable
root       17271    1017  0 02:37 pts/0    00:00:00 grep gdbserver

종료명령어: 
1. pkill gdbserver
2. kill 2431 #pid
```

### -> ls -lh

```shell
옵션    의미
-l      Long format: 권한, 소유자, 크기, 날짜 등을 상세히 보여줌
-h      Human-readable: 파일 크기를 바이트 대신 KB, MB, GB 등 단위로 보기 쉽게 표시

예시
-rwxr-xr-x  1 user user 1.5M May 28 23:10 Executable
-rw-r--r--  1 user user  22K May 28 23:10 Executable.stripped
```
