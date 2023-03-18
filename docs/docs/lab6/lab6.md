# 实验6: 网络温度计

!!! danger "本实验尚未发布，内容随时可能发生变化"

<div style="display:none">

本实验配有[实验指南](lab6_guide.md)。

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

1. 烧录microPython到ESP32；
2. 配置ESP32连接自己的Wi-Fi AP，配置好自己习惯的编程/运行环境；
3. 编写Python程序，读取DHT-11的温度/湿度，直接print输出，远程登录查看；
4. 在嵌入式Linux上编译安装MQTT服务器（broker）；
5. 下载上传micoPython的MQTT库，订阅在broker上，定时发送温度湿度消息；
6. 用手机/PC上的MQTT终端软件（如iOS上的MQTTAnalyzer）查看实时数据。

## 自选扩展内容

无。

## 实验报告要求

1. 画出你所实际实施的连接示意图；
2. 描述所做的实验步骤，给出各步操作的命令和结果；
3. 给出代码并解释。

</div>
