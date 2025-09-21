---
title: STM32F HardFault Handler
author: ghlee
date: 2025-07-03 23:17:00 +0900
categories: [ST]
tags: [ST]
pin: true

---

▶Hard Fault 발생 시 호출 스택 확인

```c
void HardFault_Handler(void)
{
    __asm volatiole (
        "TST lr, #4 \n"                     // EXC_RETURN 값 확인 (MSP or PSP 사용 여부)
        "ITE EQ \n"                        
        "MRSEQ r0, MSP \n"            // MSP 사용 시 R0에 저장
        "MRSNE r0, PSP \n"             // PSP 사용 시 R0에 저장
        "B hard_fault_handler_c \n"
    );
}
```

```c
void hard_fault_handler_c(uint32_t *stack_address) 
{
    uint32_t stacked_r0 = stack_address[0];
    uint32_t stacked_r1 = stack_address[1];
    uint32_t stacked_r2 = stack_address[2];
    uint32_t stacked_r3 = stack_address[3];
    uint32_t stacked_r12 = stack_address[4];
    uint32_t stacked_lr = stack_address[5];
    uint32_t stacked_pc = stack_address[6];
    uint32_t stacked_xpsr = stack_address[7];
    printf("\n[HardFault]\n");
    printf("R0 = 0x%08lX\n", stacked_r0);
    printf("R1 = 0x%08lX\n", stacked_r1);
    printf("R2 = 0x%08lX\n", stacked_r2);
    printf("R3 = 0x%08lX\n", stacked_r3);
    printf("R12 = 0x%08lX\n", stacked_r12);
    printf("LR = 0x%08lX (이전 함수 호출 지점)\n", stacked_lr);       // Link Register - 이전 함수 호출 지점(복귀 주소)을 저장하는 레지스터: LR값(이전 함수 호출 지점)
    printf("PC = 0x%08lX (충돌한 코드 주소)\n", stacked_pc);     // Program Counter -  현재 실행중인 명령어의 주소를 가리키는 레지스터: HardFault 발생 시 PC값
    printf("xPSR = 0x%08lX\n", stacked_xpsr);

    while(1);
}
```
---------------
분석방법 map파일을 확인하거나 GDB로 디스어셈블

