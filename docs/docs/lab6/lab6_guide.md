# 实验6指南

!!! danger "本实验指南尚未发布，内容随时可能发生变化"

<div style="display:none">

## 1 ESP32

ESP32 是一系列低成本，低功耗的单片机微控制器，集成了 Wi-Fi 和双模蓝牙。使用 MicroPython 可以有效利用您的 ESP32 板，反之亦然， ESP32 芯片是使用 MicroPython 的绝佳平台。

### 1.1 固件获取

首先您需要下载最新的MicroPython固件二进制文件以载入您的ESP32设备。您可从 **<u>[MicroPython 下载页面](https://micropython.org/download/?port=esp32)</u>** 下载。也可以点击 **<u>[此链接](https://micropython.org/resources/firmware/esp32-20220618-v1.19.1.bin)</u>** 下载最新的适用于 ESP32 的固件二进制文件。

### 1.2 固件烧录（7.5分）

#### 1.2.1 esptool 安装

我们需要借助 esptool 工具来烧录固件。您可以使用 pip 安装 esptool：

```bash
pip3 install esptool
```

它安装了esptool, pyserial, pyaes, ecdsa和six。

- pyserial: 串口
- pyaes: AES
- ecdsa: ECDSA数字签名
- six: py2和py3的差异消除

`TASK1` ==安装完成后，使用 `esptool.py -h` 显示帮助文档，并截图。==（2.5分）

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

`TASK2` ==使用 esptool 查看以上两条设备信息，并将输出结果截图。==（2.5分）

#### 1.2.3 擦除 & 烧录固件

- 擦除：

    ```bash
    esptool.py --chip esp32 --port /dev/tty.SLAB_USBtoUART erase_flash
    ```

- 烧录固件：

    ```bash
    esptool.py --chip esp32 --port /dev/tty.SLAB_USBtoUART --baud 460800 write_flash -z 0x1000 PATH-TO-YOUR-BIN-FILE.bin
    ```

`TASK3` ==使用 esptool 擦除板上原先存在的固件，并烧录新的固件，并将输出结果截图。==（2.5分）

### 1.3 串口 REPL（10分）

#### 1.3.1 连接

使用串口工具连接 ESP32，会进入 REPL 交互界面，可以通过 Python 语句进行交互。

使用 Picocom 连接的命令如下：

```bash
picocom -b 115200 /dev/tty.SLAB_USBtoUART
```

`TASK4` ==使用串口工具连接 ESP32，尝试使用 REPL 运行若干条 Python 语句，并截图。==（2.5分）

#### 1.3.2 连接 Wi-Fi

在 REPL 交互界面中，我们可以使用 `network` 模块连接 Wi-Fi：

```python
import network
wlan = network.WLAN(network.STA_IF)
wlan.active(True)
wlan.connect('ssid', 'password') # 换成自己 Wi-Fi 账户和密码
while not wlan.isconnected():
    pass
print('Wi-Fi connected')
wlan.ifconfig() #看ip地址
```

`TASK5` ==使用上述代码给 ESP32 连接 Wi-Fi，并给出连接成功的截图（需包括 IP 地址）。==（2.5分）

???+ tip "提示"
    由于校网需要使用 Web 进行认证，因此在完成此实验时，建议使用手机热点或其他的 Wi-Fi 进行连接。

    ESP32不支持5GHz信道，若使用手机热点，需要注意是否只选用了5GHz，若是，需要切换到2.4GHz，设备才能检测到网络。

#### 1.3.3 控制 LED

在 REPL 交互界面中，我们可以使用 `machine` 模块控制 LED：

```python
from machine import Pin
led = Pin(2, Pin.OUT)
led.value(1)
led.value(0)
```

`TASK6` ==使用上述代码控制 ESP32 板载 LED 灯，并给出控制成功的截图以及板卡照片。==（2.5分）

#### 1.3.4 远程重启

在 REPL 交互界面中，我们可以使用 `machine` 模块远程重启 ESP32：

```python
from machine import reset
reset()
```

`TASK7` ==使用上述代码远程重启 ESP32，并给出重启成功的截图。==（2.5分）

### 1.4 WebREPL（10分）

??? tip "参考资料"
    - **<u>[通过WiFi连接到REPL](http://wiki.1zlab.com/wiki/micropython-esp32/webrepl/)</u>**

在某些特定的场合和需求下，你可能想要通过无线的方式接入REPL，设想你正在使用MicroPython 和 ESP32 测试一辆自己制作的小车，但是小车的运行不希望被数据线所牵绊。这个时候，你可能就需要通过 WiFi 接入到 REPL 。

WebREPL 允许你通过 WiFi 使用 MicroPython 的 REPL，通过浏览器连接，使用了WebSockt通信协议。

在MicroPython的网站上托管了WebREPL的单页应用，点击如下链接即可访问：**<u>[WebREPL](http://micropython.org/webrepl)</u>**。

#### 1.4.1 配置并连接 WebREPL

- 连接Wi-Fi
  
    使用 **<u>[1.3.2](#132-连接-wi-fi)</u>** 中的代码连接Wi-Fi，并使用 ifconfig() 查看 IP 地址。

    ???+ warning "注意"
        请保证你的电脑（即之后需要使用的 WebREPL Client）和 ESP32（WebREPL Server）在同一局域网中。

- 配置WebREPL

    - 初始化配置

        初次使用WebREPL，我们先要使用webrepl_setup模块来进行初始化设置。在REPL中输入如下代码：

        ```python
        import webrepl_setup
        ```

        按提示设置是否开机自启动WebREPL，设置连接密码（请务必记住），选择是否重启。

        > 若选择重启，请在重启后重新连接 Wi-Fi。

    - 启动WebREPL

        使用以下代码开启 WebREPL：

        ```python
        import webrepl
        webrepl.start()
        ```

        你可以使用 `webrepl.start(password)` 来设置密码。

        启动之后，可以看到如下输出：

        ```bash
        WebREPL daemon started on ws://YOUR-IP-ADDRESS:8266
        ```

        其中 `YOUR-IP-ADDRESS` 为你的 ESP32 的 IP 地址。记住此处 ws:// 开头的链接，后面会用到。

- 使用 WebREPL

    访问 **<u>[http://micropython.org/webrepl](http://micropython.org/webrepl)</u>** ，在左上角输入刚才的链接，点击 `Connect` 后，在命令行中输入你所设置的密码，即可连接到 ESP32。

    !!! warning "注意"
        此网址仅支持通过 HTTP 协议访问，使用 HTTPS 会造成无法正常连接。由于现代浏览器大多会默认开启 HSTS，你可以参考 **<u>[完美解决浏览器输入http被自动跳转至https问题](https://zhuanlan.zhihu.com/p/379637201)</u>** 解决自动跳转到 HTTPS 的问题，也可以采取从本地启动 WebREPL client 的方式。

    另一种方式是通过 clone **<u>[此仓库](https://github.com/micropython/webrepl)</u>** ，进入 webrepl 文件夹，按照如下指令提示，可以在本地启动 WebREPL Client：

    ```
    webrepl_cli.py - Access REPL, perform remote file operations via MicroPython WebREPL protocol
    Arguments:
        [-p password] <host>                            - Access the remote REPL
        [-p password] <host>:<remote_file> <local_file> - Copy remote file to local file
        [-p password] <local_file> <host>:<remote_file> - Copy local file to remote file
    Examples:
        webrepl_cli.py 192.168.4.1
        webrepl_cli.py script.py 192.168.4.1:/another_name.py
        webrepl_cli.py script.py 192.168.4.1:/app/
        webrepl_cli.py -p password 192.168.4.1:/app/script.py .
    ```

    你也可以直接通过打开 `webrepl.html`，和使用在线网址蕾丝的方式进行连接。

    成功连接后，WebREPL 和之前使用串口连接的 REPL 体验基本一致。

`TASK8` ==请给出成功连接 WebREPL 后的截图，需要使用若干条 Python 语句进行测试。==（2.5分）

#### 1.4.2 使用 WebREPL 进行文件传输

WebREPL 除了可以进行 REPL 交互之外，还可以进行文件传输。

通过在 REPL 环境中 `import os`，我们可以使用 `os` 模块进行文件操作。包括 `os.listdir()`、`os.remove()`、`os.rename()`、`os.mkdir()`、`os.rmdir()` 等。

如果需要显示文件内容，可以通过 `open()` 函数打开文件，然后使用 `read()` 函数读取文件内容。

`TASK9` ==请使用 `os` 模块查看 ESP32 的文件系统中的文件，并使用 `open()` 函数打开 `boot.py` 文件，使用 `read()` 显示其内容。请给出相关截图。==（2.5分）

有两个文件在ESP32启动时进行特殊处理：`boot.py` 和 `main.py`。`boot.py` 脚本首先执行（若存在）， 此文件结束后即执行 `main.py` 脚本。您可自行创建文件并把你想开机自启动的代码放进去。

WebREPL 可以很方便的进行文件传输，可以通过网页上的提示直接进行图形化的操作，也可以根据之前本地启动 WebREPL Client 的提示进行相应操作。

`TASK10` ==请将 ESP32 上的 `boot.py` 下载到本地，并验证其内容。请给出相关截图。==（2.5分）

`TASK11` ==请使用 WebREPL 上传 `boot.py` 和 `main.py` 文件，要求实现 ESP32 能够开机自动连接 Wi-Fi，并启动让 LED 灯每隔一秒闪烁的程序。请用 TASK9 类似的方式验证文件已成功正确上传。需要给出实现的代码及相应注释，并将验证得到的结果截图。==（2.5分）

???+ warning "注意"

    - 不要上传空文件，WebREPL 存在相应的 bug

    - 不要传入带中文字符的文件，MicroPython 的文件系统不支持中文字符

### 1.5 mpfshell（5分）

mpfshell 是一个基于 Python 的命令行工具，可以用来与 ESP32 进行交互。它可以用来进行文件传输、执行 Python 脚本、执行 REPL 交互等。

#### 1.5.1 安装 mpfshell

mpfshell 可以通过 pip 进行安装：

```bash
pip3 install mpfshell
```

#### 1.5.2 Bug Fix

要正常使用 mpfshell，需要在安装之后手动修复一些 bug。

打开 PATH-TO-YOUR-PY-ENV / site-packages / mp / conwebsock.py，找到 `def on_message(self, ws, message):` 和 `def on_error(self, ws, error):`，去掉两个函数参数表中的ws，保存时要输入sudo密码。

- 进入mpfshell: `mpfshell`

- 连接ESP32: `open ws://YOUR-IP-ADDRESS,password` 或者 `open /dev/tty.SLAB_USBtoUART`

- 查看文件: `ls`

- 进入 REPL: `repl` ，按 `Ctrl-]` 退出

- 退出mpfshell: `exit`

- 退出连接: `close`

mpfshell 的详细使用方法可以参考 **<u>[mpfshell 文档](https://wiki.sipeed.com/soft/maixpy/zh/get_started/mpfshell-lite/mpfshell-lite-help.html)</u>** 。

`TASK12` ==请给出成功连接 mpfshell 后的截图，要求使用 `ls` 等命令，并进入 REPL 环境测试。==（5分）

### 1.6 RT-Thread MicroPython 插件（5分）

RT-Thread MicroPython 插件是 RT-Thread 官方提供的一个 MicroPython 插件，支持在 ESP32 上运行 MicroPython，实现 MicroPython 的 REPL 交互、文件传输、网络连接等功能。

以下教程摘录自 **<u>[RT-Thread MicroPython 插件文档](https://marketplace.visualstudio.com/items?itemName=RT-Thread.rt-thread-micropython)</u>** 。

#### 1.6.1 准备工作

##### Windows 支持

1. 在 windows 操作系统下使用插件需要将 vscode 的默认终端修改为 powershell，如下图所示：

    <img src="https://oss-club.rt-thread.org/uploads/20210508/35dbdbecd0c62d645f9a76bebce0920f.gif" style="zoom: 67%;" />

> 如果想要使用 MicroPython 自动补全功能（如果暂时不需要自动补全功能，可以跳过后续步骤），还需要进行如下操作：

2. 安装 Python 插件

3. 按照 Python 插件的提示在 PC 上安装 Python3 并加入到系统环境变量中

    <img src="https://oss-club.rt-thread.org/uploads/20210508/333fdc4c024cc705ecc90783e377e353.png" style="zoom: 67%;" />

> 如果在 PC 上已经安装过上述插件和程序，可以跳过此准备步骤。

##### Ubuntu 支持

本插件支持在 ubuntu 18.04 版本下运行，为了避免在 ubuntu 系统下频繁获取串口权限，需要将当前用户加入到 dialout 用户组中，手动输入如下命令即可，`$USERNAME` 是系统当前用户名：

```bash
sudo usermod -aG dialout $USERNAME
```

注意：配置修改后需要 **重启一下操作系统** 使配置生效。

##### Mac 支持

RT-Thread MicroPython 插件支持 Mac 10.15 Catalina 操作系统，直接搜索插件安装即可。

#### 1.6.2 快速上手

##### 创建 MicroPython 工程

MicroPython 开发的第一步是创建 MicroPython 工程，后续所有操作都必须在工程内才能运行。创建一个新的 MicroPython 工程有两种方式，分别是创建一个空白工程和基于 Demo 创建工程，下面展示这两种方式。

##### 创建一个空白 MicroPython 工程

<img src="https://oss-club.rt-thread.org/uploads/20210508/950d7dea11702fcf6e5dd2b9890e7a4c.gif" style="zoom: 67%;" />

##### 创建一个基于 Demo 的 MicroPython 工程

通过该功能可以创建一个基于 demo 的 MicroPython 工程，开发者可以直接运行该 Demo 工程或者在该 Demo 的基础上实现自己想要的功能。

<img src="https://oss-club.rt-thread.org/uploads/20210508/879eb964a6b92e101c9f0f2b49edfbb6.gif" style="zoom: 67%;" />

##### 连接开发板

点击左下角的连接按钮，然后在弹出的设备列表中选择想要连接的设备，即可连接 MicroPython 开发板。

<img src="https://oss-club.rt-thread.org/uploads/20210508/83fadd402f18ad1b1a3d3dc65b498620.gif" style="zoom: 67%;" />

##### 查看示例代码文件

MicroPython 插件提供丰富的示例代码，可在左侧活动栏中查看示例代码和库文件。右键点击示例文件，在下拉菜单中可以将示例文件添加到工程中。

<img src="https://oss-club.rt-thread.org/uploads/20210508/072cea45db8a8ee5ebea3195130318a8.png" style="zoom: 67%;" />

##### 直接在开发板上运行 MicroPython 文件（调试神器）

该功能用于快速调试单个文件，频繁应用在调试代码的过程中。当我们在一个单独的文件中编写测试程序时，使用该功能可以将当前 python 文件下载到开发板的内存中运行，达到快速调试的效果，还可以使用快捷键 `alt + q` 来触发该功能。

<img src="https://oss-club.rt-thread.org/uploads/20210508/bfeb64c05d440f9850e49b8cbe606032.gif" style="zoom: 67%;" />

##### 在开发板上运行 MicroPython 代码片段

如果只是想进行代码量不大的代码调试，而不想将文件下载到开发板上，那么可以使用 代码片段 功能。在编辑器中选中想要运行的代码片段，然后在右键下拉菜单中选择 在设备上执行选中的 MicroPython 代码 选项，即可在 REPL 环境中运行所选代码。

<img src="https://oss-club.rt-thread.org/uploads/20210508/14e1a5c8509ddce884b3bbdafa42d757.gif" style="zoom: 67%;" />

##### 下载文件/文件夹到开发板

如果想要下载单个文件/文件夹到开发板，此时可以使用 **下载单个文件/文件夹到开发板** 的功能。在工程中选中想要下载到开发板上的文件/文件夹，在下拉菜单中使用该功能即可。这里需要注意的是，如果开发板上有同名的文件/文件夹，下载操作将会覆盖这些已有的文件/文件夹。

通过在 repl 中输入 os.listdir() 命令可以查看相应的文件/文件夹是否下载成功，同样在 repl 中还可以使用相应的命令 **删除文件或文件夹** ，命令列表如下所示：

|功能|命令|
|:--:|:--:|
|删除文件|`os.remove("file_to_del")`|
|删除文件夹|`os.rmdir("dir_to_del")`|

<img src="https://oss-club.rt-thread.org/uploads/20210508/982b0a1c18d9990f5f5f3b64b4c237b5.gif" style="zoom: 67%;" />

##### 工程同步功能

点击左下角的同步按钮可以启动工程同步功能。通过该功能可将本地工程中所有目录文件，同步到开发板的文件系统中。该功能推荐在代码调试完成后使用，在调试过程中不必频繁同步工程。

工程同步完成后，可以在 DEVICE FILES LIST 栏目中看到 **设备中的文件列表** 。

<img src="https://oss-club.rt-thread.org/uploads/20210508/952968a08e389d66693f29865ab9f8ed.gif" style="zoom: 67%;" />

##### 基于 MicroPython 的代码智能补全

本插件支持基于 MicroPython 语法的代码智能补全和语法检查，这一强大功能对于开发 MicroPython 代码十分实用。它可以让开发者在编写函数的同时查看 API 参数提示，同时它给出的醒目提示也让开发者更易于查找代码中的错误。

<img src="https://oss-club.rt-thread.org/uploads/20210508/bf264e633145644b4275bce599693422.gif" style="zoom: 67%;" />

???+ warning "注意"
    不要删除工程目录下的 .mpyproject.json 文件，该文件是 MicroPython 工程的配置文件，删除后将无法正常运行 MicroPython 代码程序。

`TASK13` ==请使用此插件实现 ESP32 开机自动连接 Wi-Fi，并让 LED 灯每隔一秒闪烁的程序。（即 TASK11 所实现的工程），并给出你所使用插件界面截图，需要包括工程文件的层级结构以及 VSCode 终端中的 REPL 界面。==（5分）

### 1.7 使用 DHT-11 测量温湿度

在 Lab5 中，我们已经使用过 DHT-11 来进行温湿度的测量。在此实验中，我们无需像之前一样手动发送信号，只需要使用 MicroPython 的库函数 `dht` 即可。

DHT-11 的连线和 Lab5 类似，仅将上位机从 103板，此处不再赘述。

#### 1.7.1 MicroPython DHT 模块

##### 类 DHT11

```python
class DHT11(pin)
```

创建一个与引脚pin相连的DHT11传感器对象。

</div>

!!! note "个人水平有限，如您发现文档中的疏漏欢迎 Issue！"
