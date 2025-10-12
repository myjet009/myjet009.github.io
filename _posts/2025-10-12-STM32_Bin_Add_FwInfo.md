---
title: STM323 Bin Add FwInfo
author: ghlee
date: 2025-10-12 22:53:00 +0900
categories: [ST]
tags: [ST]
pin: true

---

## 목차
- [BIN 파일 헤더 추가 방법 (16바이트 헤더)](#bin-파일-헤더-추가-방법-16바이트-헤더)
  - [✅ 1. Python 스크립트로 처리 (가장 간단하고 이식성 높음)](#-1-python-스크립트로-처리-가장-간단하고-이식성-높음)
  - [✅ 2. C/C++ 유틸리티로 처리 (펌웨어 개발자 친화적)](#-2-cc-유틸리티로-처리-펌웨어-개발자-친화적)
  - [✅ 3. 빌드 시스템 통합 (STM32CubeIDE / CMake)](#-3-빌드-시스템-통합-stm32cubeide--cmake)
    - [STM32CubeIDE 설정](#stm32cubeide-설정)
    - [CMake 예시](#cmake-예시)
  - [✅ 정리 비교](#-정리-비교)
  - [✅ 4. C# 유틸리티 (Windows 환경용)](#-4-c-유틸리티-windows-환경용)
    - [✅ 사용 방법](#-사용-방법)

---

# BIN 파일 헤더 추가 방법 (16바이트 헤더)

펌웨어 업데이트나 부트로더 설계 시, 기존 bin 파일 맨 앞에 **16바이트 헤더**를 추가하는 것은 일반적인 패턴입니다.  
헤더 구성은 다음과 같습니다:

| 항목 | 크기 | 설명 |
|------|------|------|
| Length | 4바이트 | 전체 데이터 길이 |
| CRC32 | 4바이트 | 원본 데이터 CRC |
| Reserved | 8바이트 | 예약 (0으로 채움) |

---

## ✅ 1. Python 스크립트로 처리 (가장 간단하고 이식성 높음)

```python
import struct
import zlib

in_file = "app.bin"
out_file = "app_with_header.bin"

with open(in_file, "rb") as f:
    data = f.read()

length = len(data)
crc32_val = zlib.crc32(data) & 0xFFFFFFFF

header = struct.pack("<II8s", length, crc32_val, b'\x00' * 8)

with open(out_file, "wb") as f:
    f.write(header + data)

print(f"Header added: len={length}, crc32=0x{crc32_val:08X}")
```

**장점**
- 간단하고 플랫폼 독립적
- 자동화 빌드 스크립트에 넣기 쉬움
- CRC 계산 신뢰도 높음

**단점**
- Python 런타임 필요

---

## ✅ 2. C/C++ 유틸리티로 처리 (펌웨어 개발자 친화적)

```c
#include <stdio.h>
#include <stdint.h>
#include <stdlib.h>

uint32_t crc32_table[256];

void crc32_init(void) {
    uint32_t polynomial = 0xEDB88320;
    for (uint32_t i = 0; i < 256; i++) {
        uint32_t c = i;
        for (size_t j = 0; j < 8; j++) {
            c = (c & 1) ? polynomial ^ (c >> 1) : (c >> 1);
        }
        crc32_table[i] = c;
    }
}

uint32_t crc32_compute(const uint8_t *buf, size_t len) {
    uint32_t c = 0xFFFFFFFF;
    for (size_t i = 0; i < len; i++) {
        c = crc32_table[(c ^ buf[i]) & 0xFF] ^ (c >> 8);
    }
    return c ^ 0xFFFFFFFF;
}

int main(int argc, char *argv[]) {
    if (argc < 3) {
        printf("Usage: %s input.bin output.bin\n", argv[0]);
        return -1;
    }

    FILE *fin = fopen(argv[1], "rb");
    if (!fin) return -1;

    fseek(fin, 0, SEEK_END);
    size_t size = ftell(fin);
    rewind(fin);

    uint8_t *data = malloc(size);
    fread(data, 1, size, fin);
    fclose(fin);

    crc32_init();
    uint32_t crc = crc32_compute(data, size);

    FILE *fout = fopen(argv[2], "wb");
    fwrite(&size, 4, 1, fout);
    fwrite(&crc, 4, 1, fout);
    uint8_t reserved[8] = {0};
    fwrite(reserved, 1, 8, fout);
    fwrite(data, 1, size, fout);
    fclose(fout);

    free(data);
    printf("Header added. len=%zu, crc=0x%08X\n", size, crc);
    return 0;
}
```

**장점**
- 독립 실행 파일로 배포 가능
- 빌드 후 자동 실행 가능
- 완벽한 바이너리 호환성

**단점**
- 환경별 빌드 필요

---

## ✅ 3. 빌드 시스템 통합 (STM32CubeIDE / CMake)

### STM32CubeIDE 설정
1. Project Properties → C/C++ Build → Settings → Build Steps
2. Post-build steps에 아래 추가:

```bash
python3 ${ProjDirPath}/tools/add_header.py ${BuildArtifactFileName} ${BuildArtifactFileBaseName}_with_header.bin
```

### CMake 예시
```cmake
add_custom_command(TARGET app POST_BUILD
    COMMAND python3 ${CMAKE_SOURCE_DIR}/tools/add_header.py
        ${CMAKE_BINARY_DIR}/app.bin
        ${CMAKE_BINARY_DIR}/app_with_header.bin
)
```

---

## ✅ 정리 비교

| 방법 | 구현 언어 | 자동화 용이성 | 이식성 | 추천도 |
|------|------------|----------------|----------|--------|
| Python 스크립트 | Python | ★★★★★ | ★★★★★ | ✅ 가장 추천 |
| C/C++ 유틸리티 | C | ★★★★☆ | ★★★☆☆ | 펌웨어 친화적 |
| 빌드 시스템 통합 | 혼합 | ★★★★★ | ★★★★☆ | 유지보수성 높음 |

---

필요 시 **STM32CubeIDE용 Post-build 자동화 스크립트** 또는 **CMake 완성 예시** 버전으로 확장 가능합니다.


---

## ✅ 4. C# 유틸리티 (Windows 환경용)

아래는 동일한 기능을 수행하는 **C# 콘솔 프로그램 예제**입니다.  
입력 BIN 파일 앞에 16바이트 헤더(`[Length][CRC32][Reserved(8B)]`)를 추가합니다.

```csharp
using System;
using System.IO;
using System.IO.Compression;
using System.Linq;

namespace BinHeaderTool
{
    class Program
    {
        static void Main(string[] args)
        {
            if (args.Length < 2)
            {
                Console.WriteLine("Usage: BinHeaderTool <input.bin> <output.bin>");
                return;
            }

            string inputPath = args[0];
            string outputPath = args[1];

            if (!File.Exists(inputPath))
            {
                Console.WriteLine($"Error: File not found - {inputPath}");
                return;
            }

            try
            {
                byte[] data = File.ReadAllBytes(inputPath);
                uint length = (uint)data.Length;
                uint crc = ComputeCrc32(data);

                using (FileStream fs = new FileStream(outputPath, FileMode.Create, FileAccess.Write))
                using (BinaryWriter writer = new BinaryWriter(fs))
                {
                    // 16-byte header
                    writer.Write(length);                 // 4B Length
                    writer.Write(crc);                    // 4B CRC32
                    writer.Write(new byte[8]);            // 8B Reserved (zero)
                    writer.Write(data);                   // Original firmware
                }

                Console.WriteLine($"Header added successfully!");
                Console.WriteLine($"Length : {length} bytes");
                Console.WriteLine($"CRC32  : 0x{crc:X8}");
                Console.WriteLine($"Output  : {outputPath}");
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Error: {ex.Message}");
            }
        }

        /// <summary>
        /// 표준 IEEE CRC-32 (0xEDB88320) 계산
        /// </summary>
        static uint ComputeCrc32(byte[] data)
        {
            uint[] table = GenerateCrc32Table();
            uint crc = 0xFFFFFFFF;

            foreach (byte b in data)
            {
                crc = table[(crc ^ b) & 0xFF] ^ (crc >> 8);
            }

            return crc ^ 0xFFFFFFFF;
        }

        static uint[] GenerateCrc32Table()
        {
            const uint polynomial = 0xEDB88320;
            uint[] table = new uint[256];

            for (uint i = 0; i < 256; i++)
            {
                uint c = i;
                for (int j = 0; j < 8; j++)
                {
                    if ((c & 1) != 0)
                        c = polynomial ^ (c >> 1);
                    else
                        c >>= 1;
                }
                table[i] = c;
            }

            return table;
        }
    }
}
```

### ✅ 사용 방법

1. 위 코드를 Visual Studio에서 **콘솔 앱(.NET 6 이상)**으로 새로 만들고 복사합니다.
2. 빌드하면 `BinHeaderTool.exe` 실행 파일 생성.
3. 명령줄에서 실행:

```bash
BinHeaderTool.exe app.bin app_with_header.bin
```

출력 예시:

```
