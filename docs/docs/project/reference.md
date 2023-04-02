# 参考资料

## SX1278 使用教程

### 硬件连线

SX1278模块有8个引脚，这8个引脚都需要连接到STM32F103，建议VCC和GND相应采用红色和黑色线，其他6根线采用6个不同的颜色。

SX1278与103的连线如下：

SX1278 | 103
-------|------
GND | GND
VCC | 3.3V
RST | B0
SCK | A5
MISO | A6
MOSI | A7
NSS | A4
DIO0 | A1

### ioc配置

SX1278通过SPI与103连接，因此首先需要开启103的SPI。在Connectivity下开启SPI1，选择速度为4.5Mbps或更低，软件NSS。

其他GPIO引脚如下配置：

引脚 | 输入输出 | 建议命名
----|--------|--------
A1 | 上拉输入 | SX_D0
A4 | 推挽输出 | SX_NSS
B0 | 推挽输出 | SX_RST

### 库

在 **<u>[https://github.com/wdomski/SX1278](https://github.com/wdomski/SX1278)</u>** 下载用于STM32的SX1278库。

将“SX1278-master”目录放在Cube IDE工程的Drivers目录下，鼠标右键点击IDE右侧栏中的“SX1278-master/driver”目录（即含有SX1278.h等头文件的目录），选择“Add/Remove Include Path”，将其加入编译时的`-I`选项中。

### 代码

在main.c中，做以下几件事情：

1. `#include "sx1278.h"`
2. 在`main()`中加入本地变量

    ```c
    SX1278_hw_t sx1278_hw = {
        {SX_RST_Pin, SX_RST_GPIO_Port},     // rst
        {SX_D0_Pin, SX_D0_GPIO_Port},       // dio0
        {SX_NSS_Pin, SX_NSS_GPIO_Port},     // nss
        &hspi1
    };
    SX1278_t sx1278 = {
        &sx1278_hw,         // SX1278_hw_t *hw;
    };
    char buffer[512];

    int message =0;
    int message_length;
    ```

3. 加入如下初始化代码：

    ```c
    printf("Configuring LoRa module\r\n");
    SX1278_init(&sx1278, 434000000, SX1278_POWER_11DBM, SX1278_LORA_SF_7,
            SX1278_LORA_BW_125KHZ, SX1278_LORA_CR_4_5, SX1278_LORA_CRC_EN, 10);
    printf("Done configuring LoRaModule\r\n");
    int ret;
    #ifdef RXM
    ret = SX1278_LoRaEntryRx(&sx1278, 16, 2000);
    #else
    ret = SX1278_LoRaEntryTx(&sx1278, 16, 2000);
    #endif
    ```

    *这里的宏`RXM`用来控制编译产生用于发射还是接收的代码，有`RXM`的是接收代码*

4. 在循环中加入以下代码：

    ```c
    #ifdef RXM
        printf("Slave ...\r\n");
        HAL_Delay(800);
        printf("Receiving package...\r\n");

        ret = SX1278_LoRaRxPacket(&sx1278);
        printf("Received: %d\r\n", ret);
        if (ret > 0) {
            SX1278_read(&sx1278, (uint8_t*) buffer, ret);
            printf("Content (%d): %s\r\n", ret, buffer);
        }
        printf("Package received ...\r\n");
    #else
        printf("Master ...\r\n");
        HAL_Delay(1000);
        printf("Sending package...\r\n");

        message_length = sprintf(buffer, "Hello %d", message);
        ret = SX1278_LoRaEntryTx(&sx1278, message_length, 2000);
        printf("Entry: %d\r\n", ret);

        printf("Sending %s\r\n", buffer);
        ret = SX1278_LoRaTxPacket(&sx1278, (uint8_t*) buffer,
                    message_length, 2000);
        message += 1;

        printf("Transmission: %d\r\n", ret);
        printf("Package sent...\r\n");
    #endif
    ```

### 测试

用两套板子搭好，分别烧入发射和接收的代码，在两块103的串口输出中，会看到每1s左右发射和接收一个带着流水号的报文。
