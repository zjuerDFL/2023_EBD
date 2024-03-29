# 实验6: 网络温度计

<!-- !!! danger "本实验尚未发布，内容随时可能发生变化" -->

<!-- <div style="display:none"> -->

本实验需要使用 ESP32 开发板以及嵌入式 Linux 开发板 RK3586，利用 DHT-11 温湿度传感器实现网络温度计。

本实验配有 **<u><font size=6>[实验指南](lab6_guide.md)</font></u>** 。

## 实验目的 

1. 掌握microPython的应用程序编写；
2. 理解如何直接操纵GPIO；
3. 掌握MQTT的消息发布和订阅；
4. 掌握支持Linux的嵌入式板卡的基本使用。

## 实验器材

### 硬件

- ESP32 1块；
- DHT-11 温湿度传感器1个；
- 面包板1块；
- 杜邦线、面包线若干；
- RK3568板。

### 软件

- ESP32烧录程序：esptool
- 串口终端软件，如picocom、putty等；
- ESP32远程shell：mpfshell

## 实验步骤

> 此为实验报告的整体步骤，具体的实验步骤请参考 **<u>[实验指南](lab6_guide.md)</u>** 。

1. 烧录microPython到ESP32；
2. 配置ESP32连接自己的Wi-Fi AP，配置好自己习惯的编程/运行环境；
3. 编写Python程序，读取DHT-11的温度/湿度，直接print输出，远程登录查看；
4. 在嵌入式Linux上编译安装MQTT服务器（broker）；
5. 下载上传micoPython的MQTT库，订阅在broker上，定时发送温度湿度消息；
6. 用手机/PC上的MQTT终端软件（如iOS上的MQTTAnalyzer）查看实时数据。

## 自选扩展内容

无。

## 实验报告要求

> 此为实验报告的整体要求，具体的实验步骤请参考 **<u>[实验指南](lab6_guide.md)</u>** 。

1. 画出你所实际实施的连接示意图；
2. 描述所做的实验步骤，给出各步操作的命令和结果；
3. 给出代码并解释。

## 验收要求

1. 使用 ESP32 配合 DHT-11 测量温湿度，直接 print 输出，并在远程终端查看；

2. 使用 ESP32 配合 DHT-11 测量温湿度，通过 MQTT 协议将数据发送到嵌入式 Linux 上的 MQTT broker，在手机 / PC 上通过 MQTT 终端软件查看数据；

    > 验收时可提前到实验室为 3568 板上电启动并配置好网络，验收时将 3568 板放在实验室自己位置上即可。或者保证能够访问到 3568 板的情况下，也可将其放在寝室，不一定要将器材带到实验室。

3. 回答实验相关问题。

<!-- </div> -->
