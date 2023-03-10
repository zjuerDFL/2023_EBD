# 实验4指南

!!! danger "本实验指南尚未发布，内容随时可能发生变化"

<div style="display:none">

请跟随实验指南完成实验，完成文档中所有的`TASK`。`BONUS`部分的内容完成可作为加分，但报告的总分不应超过100分。请下载此指南作为实验报告模版，将填充完成的实验报告导出为PDF格式，并命名为“学号_姓名_lab3.pdf”，上传至学在浙大平台。下载请点击 **<u>[这里](../download.md)</u>** 。

此实验要求实现一个简易的 boot loader ，能够通过串口执行四条最简单的指令：`peek`、`poke`、`load`和`run`。

## 1 硬件连线

本实验中不需要用到开关，只需将103板连接下载器（ST-Link）和串口即可。

`TASK1` ==请拍摄实际的硬件连接图==（5分）

## 2 指令解析

本实验中，boot loader 通过串口接收指令，指令的格式以及简介如下表所示：

| 指令 | 格式 | 说明 |
| :--: | :--: | :--: |
| peek | `peek <addr>` | 读取 addr 位置的数据 |
| poke | `poke <addr> <data>` | 修改 addr 位置的数据为 data |
| load | `load <addr>` | 从PC接收一段二进制数据，保存在 addr 开始的地址中 |
| run | `run <addr>` | 运行 addr 开始的程序 |

请你编写程序，能够解析串口接收到的指令，并将指令分离出命令字和参数，将分离的结果返回，在串口输出。要求仅返回上述四种指令，若输入为其他指令，则返回非法提示信息。

???+ example "示例"
    串口接收到的指令为 `peek 00008000` ，则返回 `INS: peek` 和 `PARA: 00008000` 。

    串口接收到的指令为 `poke 00008000 00000000` ，则返回 `INS: poke` ， `PARA1: 00008000` 和 `PARA2: 00000000` 。

`TASK2` ==请给出实现指令解析的代码==（10分）

`TASK3` ==请自己设计若干测试，烧录上板后运行测试，并给出串口的输出截图==（5分）

## 3 Boot Loader 实现

在实现了指令解析之后，我们可以具体实现 Bootloader 中 `peek`、`poke`、`load` 和 `run` 指令。

### 3.1 peek 指令

指令 `peek <addr>` 即为以一个字为单位，读取内存中 addr 位置的数据（addr是4字节对齐，十六进制的形式，长度为8位十六进制，没有引导字符，例如 `00008000`），并以十六进制的形式输出结果，输出结果为自然序（高位在前）。

此指令的实现较为容易，注意使用指针操作即可。可选的一种方法为使用 `sprintf` 函数，将数据输出到缓冲区的字符串中，再通过串口输出此字符串。

为了方便结果验证，请在完成此部分时，自行在代码中添加若干变量并赋值，在串口开始接收指令之前先将对应的变量地址和值输出到串口，以便验证此部分的代码是否正确。

`TASK4` ==请给出实现 peek 指令的关键代码。==（10分）

`TASK5` ==在烧录后做若干测试，并证明结果正确性，请给出相应的截图。==（5分）

### 3.2 poke 指令

指令 `poke <addr> <data>` 以一个字为单位修改内存中 addr 位置的数据为 data（addr 是 4 字节对齐,十六进制的形式，长度为8位十六进制, data 也是十六进制的形式，长度为8位十六进制，为自然序高位在前）

与 `peek` 指令类似，也只需要通过一些简单的指针操作便可实现。需要注意的是，在测试此指令时，也需要自行添加若干变量并赋值。这样可以后续修改这些变量所在地址的值，方便进行验证。

`TASK6` ==请给出实现 poke 指令的关键代码。==（10分）

`TASK7` ==在烧录后做若干测试，并配合 peek 指令证明结果正确性，请给出相应的截图。==（5分）

`TASK8` ==尝试随意寻找地址，并修改其中的值，可能会发生什么现象？为什么？==（5分）

### 3.3 load 指令

#### 3.3.1 XModem 协议

XModem 协议是一种串口通信中广泛用到的异步文件传输协议。它以以128字节块的形式传输数据，每个块以SOH（0x01）开始，以CRC校验结束。每个块的格式如下：

| 命令字符 | 命令码  | 备注  |
| :-----: | :----: | :--: |
| SOH     | 0x01   | 数据头 |
| STX     | 0x02   |       |
| EOT     | 0x04   | 发送结束 |
| ACK     | 0x06   | 接收成功 |
| NAK     | 0x15   | 接收失败 |
| CAN     | 0x18   | 取消传输 |

XModem 包的格式如下：

```
---------------------------------------------------------------------------
|     Byte1     |    Byte2    |     Byte3      |Byte4~Byte131|  Byte132   |
|-------------------------------------------------------------------------|
|Start Of Header|Packet Number|~(Packet Number)| Packet Data |  Check Sum |
---------------------------------------------------------------------------
```

传输流程如下图所示：

```
------------------------------------------------------------------------------
|               SENDER                |          |          RECIEVER         |
|                                     |  <---    |  NAK                      |
|                                     |          |  Time out after 3 second  |
|                                     |  <---    |  NAK                      |
| SOH|0x01|0xFE|Data[0~127]|CheckSum| |  --->    |                           |
|                                     |  <---    |  ACK                      |
| SOH|0x02|0xFD|Data[0~127]|CheckSum| |  --->    |                           |
|                                     |  <---    |  NAK                      |
| SOH|0x02|0xFD|Data[0~127]|CheckSum| |  --->    |                           |
|                                     |  <---    |  ACK                      |
| SOH|0x03|0xFC|Data[0~127]|CheckSum| |  --->    |                           |
|                                     |  <---    |  ACK                      |
| .                                   |          |  .                        |
| .                                   |          |  .                        |
| .                                   |          |  .                        |
|                                     |  <---    |  ACK                      |
| EOT                                 |  --->    |                           |
|                                     |  <---    |  ACK                      |
------------------------------------------------------------------------------
```

可以参考如上流程，实现 XModem 协议，以便在串口上实现文件传输。此处允许使用开源的 XModem 协议实现，但需要在报告中说明使用的开源代码的出处。

同时，也可以参考官方给出的 **<u>[IAP](https://www.st.com/zh/embedded-software/x-cube-iap-usart.html)</u>** （使用YModem实现）

???+ tip "参考资料"
    [XModem 协议](https://zhuanlan.zhihu.com/p/34464259)

#### 3.3.2 指令实现

指令 `load <addr>` 从串口接收一段二进制数据，保存在 addr 开始的地址中。需要使用 XModem、YModem 或 ZModem 协议进行数据传输。

注意，如果需要将数据保存在 FLASH 中，需要先擦除对应的扇区，然后再写入数据。因为在对 FLASH 进行编程时，只能将1变为0。擦除操作即为将Flash的某个扇区全部写入1。擦除完毕后，对 FLASH 进行编程，即可实现改变 FLASH 中的数据。

当然，本实验中没有要求一定要将数据保存在 FLASH 中，也可以将数据保存在 RAM 中，这样操作会更加简单，但数据会在断电后丢失。

???+ tip "参考资料"
    HAL 库中提供了对 FLASH 的操作函数，可以直接使用。具体的函数请参考 **<u><a href="https://www.st.com/content/ccc/resource/technical/document/user_manual/2f/71/ba/b8/75/54/47/cf/DM00105879.pdf/files/DM00105879.pdf/jcr:content/translations/en.DM00105879.pdf" target="_blank">HAL手册</a></u>**。

    也可参考如下链接：**<u>[STM32中Flash的读写（HAL库）](https://www.stlee.tech/2020/06/17/STM32%E4%B8%ADFlash%E7%9A%84%E8%AF%BB%E5%86%99%EF%BC%88HAL%E5%BA%93%EF%BC%89/)</u>** 

`TASK9` ==请给出实现 load 指令的关键代码。要求对协议实现或者借用开源代码的部分做出适当的解释。==（15分）

`TASK10` ==在烧录后做若干测试，并配合 peek 指令证明结果正确性，请给出相应的截图。烧录的二进制文件可以自己使用 **<u>[ImHex](https://imhex.werwolv.net)</u>** 或其他编辑器创建。==（5分）

> 当然如果你对自己足够有信心，也可以直接将编译好的二进制文件烧录在恰当的地址上，并在此处使用 run 指令进行验证。

### 3.4 run 指令

</div>

!!! note "个人水平有限，如您发现文档中的疏漏欢迎 Issue！"