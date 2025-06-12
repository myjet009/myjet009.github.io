---
title: SerialCom_on_WSL
author: ghlee
date: 2025-06-12 10:13:00 +0900
categories: [WSL, Serial]
tags: [WSL]
pin: true

---


1. usbipd로 WSL에 USB 시리얼 장치 전달하기
이 방법은 Windows에 연결된 USB 시리얼 장치를 WSL2 리눅스 환경으로 직접 전달하여, WSL에서 /dev/ttyUSB0, /dev/ttyACM0 등의 실제 디바이스로 사용할 수 있게 해줍니다.

2. usbipd-win 설치 (Windows)
다운로드: https://github.com/dorssel/usbipd-win/releases

설치 후 PowerShell에서 작동 확인:
```sh
usbipd list
```

출력 예시:
```sh
Connected:
BUSID  VID:PID    DEVICE                                                        STATE
1-2    046d:c52b  Logitech USB Input Device, USB 입력 장치                      Not shared
1-3    0408:5425  HP Wide Vision HD Camera, Camera DFU Device                   Not shared
1-4    0bda:2852  Realtek Wireless Bluetooth Adapter                            Not shared
2-1    046d:c548  Logitech USB Input Device, USB 입력 장치                      Not shared
4-2    0403:6001  USB Serial Converter                                          Shared
```

PowerShell을 관리자모드로 실행하여 아래와 같이 해야 State가 shared로 변경됨
```sh
usbipd bind --busid 4-2
```

3. USB 디바이스 WSL에 attach
```sh
usbipd attach --busid 1-5 --wsl
```

4. WSL에서 장치 확인
```sh
dmesg | grep tty
```

예시:
```
[  123.456789] usb 1-5: ch341-uart converter now attached to ttyUSB0
```

5. 테스트
WSL에서:
```sh
screen /dev/ttyUSB0 115200
```

screen 종료:
```sh
- 세션종료: Ctrl + A -> K -> y

- 세선유지 종료: Ctrl + A -> D
  screen -ls   # 확인
  screen -r    # 재접속

- 밖에서 강제종료: killall screen
```
