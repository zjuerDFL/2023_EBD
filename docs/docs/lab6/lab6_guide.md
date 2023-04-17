# 实验6指南

!!! danger "本实验指南尚未发布，内容随时可能发生变化"

<div style="display:none">

## 1 ESP32

ESP32 是一系列低成本，低功耗的单片机微控制器，集成了 Wi-Fi 和双模蓝牙。使用 MicroPython 可以有效利用您的 ESP32 板，反之亦然， ESP32 芯片是使用 MicroPython 的绝佳平台。

### 1.1 固件获取

首先您需要下载最新的MicroPython固件二进制文件以载入您的ESP32设备。您可从 **<u>[MicroPython 下载页面](https://micropython.org/download/?port=esp32)</u>** 下载。也可以点击 **<u>[此链接](https://micropython.org/resources/firmware/esp32-20220618-v1.19.1.bin)</u>** 下载最新的适用于 ESP32 的固件二进制文件。

### 1.2 固件烧录

#### 1.2.1 esptool 安装

我们需要借助 esptool 工具来烧录固件。您可以使用 pip 安装 esptool：

```bash
pip3 install esptool
```

它安装了esptool, pyserial, pyaes, ecdsa和six。

- pyserial:串口
- pyaes:AES
- ecdsa:ECDSA数字签名
- six:py2和py3的差异消除

`TASK1` ==安装完成后，使用 `esptool.py -h` 显示帮助文档，并截图。==（5分）

#### 1.2.2 查看信息

将 ESP32 连接到电脑上，并确定对应的设备号（如 `/dev/tty.SLAB_USBtoUART` ），然后使用 esptool 查看设备信息：

- 查看 `chip_id`：

    ```bash
    esptool.py --chip esp32 -p /dev/tty.SLAB_USBtoUART -b 115200 chip_id
    ```

- 查看 `flash_id`：

    ```bash
    esptool.py --chip esp32 -p /dev/tty.SLAB_USBtoUART -b 115200 flash_id
    ```

`TASK2` ==使用 esptool 查看以上两条设备信息，并将输出结果截图。==（5分）

#### 1.2.3 擦除 & 烧录固件

</div>

!!! note "个人水平有限，如您发现文档中的疏漏欢迎 Issue！"
