# STM32F407_Explorer_ThreadX
在 STM32CubeIDE 中针对 STM32F407 移植 ThreadX

### 前言

在 Microsoft 开源ThreadX后，网上有人在Keil MDK、IAR上移植成功过，但既然有ST官方提供的开发工具，何必使用盗版破解软件。

工程文件在 [STM32F407_Explorer_ThreadX](https://github.com/wangyuew/STM32F407_Explorer_ThreadX.git)

参照网上现有资料，移植过程并不复杂，通过提交历史记录关键修改。



### 背景

2019年4月18日，Microsoft 收购了 Express Logic，后者开发了用于物联网和边缘计算的实时操作系统 ThreadX 全家桶，并于2020年5月17日开源。

相较于 FreeRTOS 等国内使用较多的 RTOS 而言，除了后发（FreeRTOS 早期即开源，在 2017 年被亚马逊收购并修改为 MIT授权）的劣势，ThreadX 具有2方面的优势：

1. 拥有丰富的多行业认证。FreeRTOS 的改进版（由第三方重写） SafeRTOS 才提供部分行业认证。
2. 拥有丰富的中间件，如网络、文件系统、GUI、USB等，集成时能提供一致的体验。如果使用 FreeRTOS，需手动集成FatFs、LwIP等第三方中间件。



### 开发环境

* STM32CubeIDE v1.3.1
* STM32F4 Cube v1.25.0
* ThreadX v6.0



### 移植时的注意事项

1. STM32F407为**cortex-m4**内核，所以使用`ports/cortex_m4`目录中的移植文件。
2. Project配置中增加ThreadX头文件路径。`Project Properties --> C/C++ Build --> Settings --> Tools Settings --> MCU GCC Compiler --> Include paths`。
3. 注释掉STM32CubeIDE默认代码中的`PendSV_Handler()`和`SysTick_Handler()`，避免重复定义。为避免重新生成代码时注释被优化，将`#if 0`放置在`DebugMon_Handler()`的用户段0，将`#endif`放置在`SysTick_Handler()`的用户段1。
4.  在 `STM32F407ZETX_FLASH.ld` 中的 `._user_heap_stack` 段定义 `__RAM_segment_used_end__`，其表示未使用RAM空间的起始地址，用于做动态分配。
5. 在 `startup_stm32f407zetx.`s 中定义全局变量 `_vectors`，其在 `tx_initialize_low_level_sample.S` 中被调用。
6. 删除 `tx_vector_table_sample.S`。该示例启动文件与默认启动文件 `startup_stm32f407zetx.s` 功能重复。
7. 修改时钟频率。



### 参考资料

[The Top 10 Pitfalls of Embedded Open Source Software](https://rtos.com/wp-content/uploads/2018/01/EL_Pitfalls_of_Open_Source_Software.pdf)

[首发从搭建环境开始，教你如何在小熊派上跑起来ThreadX操作系统！](https://www.codenong.com/cs106388532/)

[小熊派带你初探最近叱咤风云的ThreadX全家桶](https://www.21ic.com/article/766878.html)

[ThreadX操作系统在MDK中的移植方法](https://blog.csdn.net/Mculover666/article/details/106607238)

[基于STM32H7的ThreadX内核GCC版，MDK和IAR个版本都整理完毕](http://www.armbbs.cn/forum.php?mod=viewthread&tid=98089)

[ThreadX内核移植中__RAM_segment_used_end__的作用](http://www.armbbs.cn/forum.php?mod=viewthread&tid=98114&fromuid=58)