# 课前准备

## 1 STM32CubeIDE 安装

> STM32CubeIDE是ST公司提供的一款集成开发环境（IDE），用于开发STM32系列的MCU。此IDE基于Eclipse改造，支持Windows，MacOS以及Linux。Lab1-5均需要在此IDE上完成。

- 下载方式： **<u>[ST公司官网](https://www.st.com/zh/development-tools/stm32cubeide.html)</u>** 
    - 下载需要注册ST公司的账号，可用校园网邮箱免费注册。
- 版本选择：选择最新版本（1.11.2）即可。
- 安装方式：以默认选项安装即可
- 注意事项：IDE安装包中附带ST-Link驱动，若安装程序未自动安装，请手动安装。
- 对于MacOS用户：
    - 解压zip文件后，打开dmg文件，首先双击pkg文件安装ST-Link驱动，然后将app文件拖拽到Applications文件夹中。
    - 安装驱动时，需要在“系统偏好设置-->安全性与隐私”处允许允许这个安装程序。
    - 第一次运行STM32CubeIDE时，需要 **在Applications文件夹中右键点击STM32CubeIDE.app，选择“打开”，然后在弹出的窗口中点击“打开”。** 若直接双击运行，会提示“STM32CubeIDE.app”已损坏，无法打开。这是由于macOS的安全机制导致的，需要手动允许运行一次。
    
## 2 逻辑分析仪 nanoDLA 教程

> nanoDLA 是 MuseLab 推出硬件软件以及上位机均开源的逻辑分析仪。支持最高24Mhz采样率，8通道可同时采样。支持Windows/Linux/Mac/Android平台下使用。Lab5需要使用逻辑分析仪来分析DHT11传感器的数据。

提供的 nanoDLA 为 [nanoDLA-USB-C](https://item.taobao.com/item.htm?spm=2013.1.0.0.58b8333eL0dcfJ&id=642354490902) ，板上接口为 USB-C 的母口，需要自备一根 USB-C 数据线和上位机连接。

> 此教程参考了 nanoDLA 的 [官方文档](https://github.com/wuxx/nanoDLA) 。

### 2.1 上位机软件下载

nanoDLA使用开源的PulseView作为上位机软件。可在其 **<u>[GitHub 页面](https://github.com/wuxx/nanoDLA/tree/master/software)</u>** 进行下载。

安装过程只需按照默认设置连续点击下一步安装即可。

### 2.2 驱动安装

插入nanoDLA，此时会设备管理器中识别出fxlafw设备，如图所示:

<img src="https://github.com/wuxx/nanoDLA/blob/master/doc/usb_device_unknown_v1.2.png?raw=true" alt="usb_device_unknown" style="zoom:100%;" />

在安装PulseView时，会自动安装Zadig，打开Zadig后选择Options->List All Devices，上方选择对应nanoDLA的fx2lafw（USB ID为 1D50:608C），下方在驱动栏选择WinUSB驱动，点击Install Driver安装驱动即可，稍等片刻，即可成功安装驱动。

<img src="https://github.com/wuxx/nanoDLA/blob/master/doc/zadig_install1_v1.2.png?raw=true" alt="zadig_install1" style="zoom:100%;" />

成功安装驱动后，设备可被正常识别，如图所示：

<img src="https://github.com/wuxx/nanoDLA/blob/master/doc/usb_device_default_v1.2.png?raw=true" alt="usb_device_default" style="zoom:75%;" />

!!! note "对于MacOS，无需使用Zadig安装驱动，可跳过此步"

### 2.3 PulseView 使用

将nanoDLA插入PC中，然后打开PulseView，PulseView启动后，会自动搜索并打开fx2lafw设备，如图所示

<img src="https://github.com/wuxx/nanoDLA/blob/master/doc/usb_device_fx2lafw.png?raw=true" alt="usb_device_fx2lafw" style="zoom:60%;" />

PulseView使用上比较简单，在下方菜单栏中配置采样数据大小和采样率，点击左上角run按钮，即可开始采样，对于nanoDLA，最高可配置24Mhz的采样率进行工作，8通道同时进行工作

<img src="https://github.com/wuxx/nanoDLA/blob/master/doc/pulseview2.png?raw=true" alt="pulseview2" style="zoom:68%;" />

<img src="https://github.com/wuxx/nanoDLA/blob/master/doc/pulseview3.png?raw=true" alt="pulseview3" style="zoom:50%;" />

## 3 虚拟示波器教程

> 待补充