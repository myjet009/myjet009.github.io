---
title: STM32F BootLoader Jump to RTOS App
author: ghlee
date: 2025-07-03 23:17:00 +0900
categories: [ST]
tags: [ST]
pin: true

---

▶ BootLoader Jump to RTOS App 설정

- systick 초기화 - rtos에서 사용
- 주변장치 Deinit()
- SCB->VTOR 레지스터를 운영 앱의 백터 테이블 주소로 설정
- MSP(Main Stack Pointer) 재설정

```c
void jump_to_application(uint32_t app_address)
{
    void (*app_reset_handler)(void);
    // SysTick 비활성화
    SysTick->CTRL = 0;
    SysTick->LOAD = 0;
    SysTick->VAL = 0;
    NVIC_DisableIRQ(SysTick_IRQn);

    // 백터 테이블 설정
    SCB->VTOR = app_address;

    // 스택 포인터 설정
    __set_MSP(*(volatile uint32_t *)app_address);

    // 리셋 핸들러 주소
    app_reset_handler = (void (*)(void))(*(volatile uint32_t *)(app_address+4));

    // 어플리캐이션 실행
    app_reset_handler();
}
```
