# 组队大程

!!! danger "组队大程尚未发布，内容随时可能发生变化"

<div style="display:none">

## 项目简介

一个无线自组网实验系统

分四个大组，每个大组完成一个网络。每个组要提自己的在这个网络上的应用。不要求4个网络在无线上能互通（做到有加分），但是要求4个网络通过互联网网关能实现消息的互通。

- 无线自组网

    网络的物理层采用LoRa调制解调芯片，使用433MHz频段，10dBm功率，点对点大约能实现100m左右的通信。我们只使用LoRa的无线通信功能，不使用LoRaWAN的网络功能。

- LoRa

    在物理层之上，实现链路层、传输层和应用层。链路层一般至少实现MAC、heard list和存储转发，传输层一般至少实现用户认证、网络注册和注销。通过转发，能覆盖更大的范围。

    应用层则各组可以自己思考和设计。如文字聊天、目标跟踪、定点数据传输。
    传输层还要实现无线网到互联网的网关，能和互联网上的服务器交换数据，如将应用数据发送到互联网服务器上。这个网关还要实现各组之间的互联互通。
    各组自己决定自己的应用、人员的分工、技术路线和架构。
    每个月做一次阶段性验收。
    M1: 方案验收；
    M2: 物理层验收，能实现点对点通信；
    M3: 链路层和传输层验收，能实现存储转发；
    M4: 整体验收，包括互联互通。

## 参考资料

- [安信可官网](https://docs.ai-thinker.com/lora)

- [Ra-01SC 规格书](https://docs.ai-thinker.com/_media/ble/docs/ra-01sc规格书20210413.pdf)

- [LoRa官方LLCC68驱动](https://github.com/Lora-net/llcc68_driver)
    
    - 此官方驱动为了适配各种不同的MCU，将HAL库的实现留给用户完成。

- [Ra-02 LoRa module (SX1278) library for STM32 (ARM processors) using HAL drivers](https://github.com/SMotlaq/LoRa)

    - 此库使用 STM32 的 HAL 库实现了 SX1278 的驱动。作者同时制作了使用 STM32CubeMX + Keil 进行开发的[视频教程](https://www.youtube.com/watch?v=jMGnSQyCUqw)。（STM32CubeMX + Keil 可用STM32CubeIDE代替）

- [Ra-01SC/Ra-01SCH STM32F103C8T6单片机 示例 demo](https://docs.ai-thinker.com/_media/llcc68driver-v.0.0.1.zip)

    - 此例程基于 Keil ，可以参考 [Keil 迁移至 STM32CubeIDE 教程](https://blog.csdn.net/qq_37529330/article/details/128951852) 将其移植到CubeIDE中进行开发。

- [SX1262/SX1268/LLCC68 Low Power Long Range Transceiver driver for esp-idf](https://github.com/nopnop2002/esp-idf-sx126x)

    - 此库使用ESP系列MCU作为上位机，使用ESP-IDF开发框架，实现了SX1262/SX1268/LLCC68的驱动。

!!! note "个人水平有限，如您发现文档中的疏漏欢迎 Issue！"

</div>