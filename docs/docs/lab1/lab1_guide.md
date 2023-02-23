# 实验1指南

## 1 安装软件

在ST公司官网下载[STM32CubeIDE](https://www.st.com/zh/development-tools/stm32cubeide.html)。这个IDE是基于Eclipse改造的，有Windows、macOS和Linux三个版本。下载时需要注册一个st的帐号，免费的，校网邮箱可用。

下载后，以默认选项安装。

对于macOS，解压zip文件后，打开dmg文件，首先双击pkg文件安装ST-Link驱动，然后将app文件拖拽到Application。安装驱动时，需要在“系统偏好设置-->安全性与隐私”处允许允许这个安装程序。第一次运行STM32CubeIDE时，也需要允许运行一次。

## 2 连线

找出4条杜邦线，要求其中一条红色、一条黑色，另两条其他不同的颜色。

*对特定的信号/电源使用特殊的颜色是工程和实验技术的基础要求。至少要求做到电源使用红色，地使用黑色，并且不将红色和黑色用于其他线路。*

杜邦线的一头连接103板子侧面的标着GND、DCLK、DIO、3.3V的四个插针，另一头接ST-Link的(2)SWCLK、(4)SWDIO、(6)GND、(8)3.3V。在ST-Link的插针处，有缺口的一侧是奇数，另一侧是偶数。

连线的对应关系如下表：

| ST-Link   | STM32F103 | 线的颜色 | 含义 |
| --------- | --------- | ------- | --- |
| (2) SWCLK | DCLK      |         | 时钟 |
| (4) SWDIO | DIO       |         | 数据 |
| (6) GND   | GND       | 红色     | 地   |
| (8) 3.3V  | 3.3V      | 黑色     | 电源 |

<img src="../img/1-1.jpeg" style="zoom: 10%;" />
<img src="../img/1-4.jpeg" style="zoom: 10%;" />
<img src="../img/1-2.jpeg" style="zoom: 10%;" />

右图103上的杜邦线的连接，使得四条线的小开口都朝一个方向，这样不仅是美观，最主要的是可以方便将来有必要的时候，用表笔接触这个小开口来做测量。

> 以这个方式连接，ST-Link是在给103供电。但是ST-Link内的3.3V只能供给大约50mA的电流，所以这只适合于烧录程序和不接其他模块时的简单调试场合。实际工作时，应该使用USB插座或其他外部电源直接给5V或3.3V引脚供电。

在103板上有两个黄色跳线帽，右边分别标着BT0和BT1。这里上下两排各三个插针是两组，上下各一组。两个跳线帽都应该插在左边的两个插针上，如下图所示。这样的话，跳线帽起到把中间的插针和左边的插针连起来的作用。*103板的正确方向是主芯片面朝上，USB插座朝左，引脚的文字正确。*

<img src="../img/1-3.jpeg" style="zoom: 25%;" />

在后面的操作中，如果出现了烧录不进去的情况，BT0这个跳线帽可能要拔下来插到中间和右边的插针上去。

## 3 建立工程

启动Cube IDE，选择“Start new STM32 project”。

<img src="../img/3-1.png" alt="3-1" style="zoom: 33%;" />

如果已经启动过Cube IDE，则在菜单选择“File->New ->STM32 Project“。

在“Target Selection”的“Part Number”，输入“STM32F103C8”，然后在右边的“MCUs/MPUs List”那里，点击出现的“Part No”为“STM32F103C8”的列表项。这样右下方的“Next”才会亮起来。

<img src="../img/3-2.png" alt="3-2" style="zoom:25%;" />

点击“Next”，然后在“STM32 Project”对话框里，输入工程名称，如“blink”。然后就可以直接点击“Finish”了。

<img src="../img/3-3.png" alt="3-3" style="zoom:33%;" />

在随后出现的“Open Associated Perspective?”对话框中点击“Yes”。

<img src="../img/3-4.png" alt="3-4" style="zoom:50%;" />

于是就开始下载安装所需的软件包了。

<img src="../img/3-5.png" alt="3-5" style="zoom:50%;" />

完成时是这样的：

<img src="../img/3-6.png" alt="3-6" style="zoom:33%;" />

##4 芯片配置

接下来，要做一些必要的芯片配置。我们从上往下来。

### 4.1 LED引脚

首先点开“System Core”，点击“GPIO”：

<img src="../img/4-1.png" style="zoom:33%;" />

然后，点击右侧芯片图下方的“+”放大镜按钮，放大到合适的尺寸，点击芯片左上角左侧的“PC13”引脚，在弹出菜单中选择“GPIO_Output”。

<img src="../img/4-2.png" style="zoom:33%;" />

点击中间“Configuration”栏中间的PC13项，在下方的“PC13-TAMPER-RTC Configuration”那里，往下拖到最后，看到“User Label:”，输入"LED"。

<img src="../img/4-3.png" style="zoom:33%;" />

这个“LED”将来会出现在main.h里，它构成了两个宏，在程序中可以直接使用，用来表达这个引脚。

### 4.2 时钟

初次使用也可以不配时钟，那么默认会使用片内RC电路构成的时钟，准确性和稳定度都不够好，尤其是将来要跑串口通信的时候，有可能造成通信失败。

点击左侧栏的“System Core-->RCC”，在中间栏“RCC Mode and Configuration”里，“High Speed Clock(HSE)”选择“Crystal/Ceramic Resnoator”，下面的“Low Speed Clock(LSE)”同样选择。

<img src="../img/4-4.png" style="zoom:33%;" />

可以勾选下面的“Master Clock Output”，这样会在PA8引脚上输出时钟信号供示波器、频率计等用来做硬件调试。一旦做了这个选择，在右侧的芯片图上会出现相应的记号。

<img src="../img/4-5.png" style="zoom:33%;" />

即使选择了外部晶体作为时钟源，默认配置的时钟来源仍然是内部HSI，主频是8MHz。点击上方的“Clock Configuration”。然后在时钟配置图中，在“PLL Source Mux”处选择“HSE”，使得外部晶体成为时钟源，并且使用PLL。

<img src="../img/4-6.png" style="zoom:33%;" />

此时仍然是8MHz。在“*PLLMul”处，选择“X9”，在“System Clock Mux”处选择“PLLCLK”。

这样最右侧一众频率都是72MHz，但是中间“APB1 Prescaler”出现了红色。因为APB1的最高频率只能是36MHz，所以要在“APB1 Prescaler”选择“/2”，这样就把“APB1 peripheral clocks”降到36MHz了。

<img src="../img/4-7.png" style="zoom:33%;" />

时钟图最下面还有一个“MCO source Mux”此时也是红色的，要选择“/2 PLLCLK”。

### 4.3 调试

点击左侧栏的“System Core-->SYS”，在中间栏“SYS Mode and Configuration”里，“Debug”选择“Serial Wire”。做了这个选择，右侧芯片图上也会出现相应的记号。

<img src="../img/4-8.png" style="zoom:33%;" />

这一步很重要，如果忘了做这个选择，一旦烧录程序进板子之后，需要用特殊的流程才能重新做下一次烧录。如果做了这个选择，就可以不用动硬件和连线，直接多次烧录程序了。

<img src="../img/4-9.png" style="zoom:33%;" />

芯片图右上角的SWDIO和SWCLK引脚就是之前连线时接的那两个引脚。

###4.4 产生代码

这样我们第一个实验所需的配置就好了，接下来可以保存配置（blink.ioc文件）。

Ctrl-S或使用菜单/工具条里的保存按钮之后，出现对话框：
“Do you want to generate Code?”，点击“Yes”。

<img src="../img/4-10.png" style="zoom:33%;" />

每次保存ioc文件，都会询问是否要重新产生代码。每次重新产生代码，都会把原有的main.h和main.c覆盖掉。所以如果你已经修改过代码了，需要自己搞定这个事情。如果不需要修改自动产生的代码，只是要加一些代码，可以找main.c中的一些注释，找到`/* USER CODE BEGIN... */`这样的地方，是可以安全地插入代码的。

点击“Yes”之后，还会询问要不要切换到C/C++视图，回答Yes。

这样就出现了main.c了。源码所在的目录如下：

<img src="../img/4-11.png" style="zoom:33%;" />

在main.h会找到这两行：

```
#define LED_Pin GPIO_PIN_13
#define LED_GPIO_Port GPIOC
```

这两个宏分别对应LED所在的引脚和端口。

103的GPIO有A、B、C、D四个端口，每个端口有32个引脚。所以PC13的意思就是C端口的13号引脚。


## 5 编译和下载

打开main.c，找到其中的`main()`函数，里面有一个`while (1)`循环。在这个循环里找到`/* USER CODE BEGIN 3*/`，在这之后加入下面两句：

```
HAL_GPIO_TogglePin(LED_GPIO_Port, LED_Pin);
HAL_Delay(500);
```

*HAL的手册可以在st.com搜索`UM1725`来获得。*

然后就可以编译下载运行了。

第一次编译前，先把ST-Link插上电脑的USB接口。

然后点击工具条里的绿色播放按钮，或菜单选择Run->Run。

第一次运行可能会出现下面的对话框：

<img src="../img/5-1.png" style="zoom:33%;" />

暂时不需要配置，直接点击“Run”。

### 5.1 升级ST-Link固件

第一次运行时，通常Cube IDE会要求检查ST-Link的版本：

<img src="../img/5-2.png" style="zoom:33%;" />

点击“OK”。如果发现需要升级固件，会出现如下对话框：

<img src="../img/5-3.png" style="zoom:33%;" />

点击“Open in update mode“按钮，就会出现“Current Firmware：”的数据，而且下方的”Upgrade“按钮变亮了。点击”Upgrade“，就开始ST-Link的固件升级了。

*ST-Link内部是一个单片机，所以可以（需要）升级程序。*

升级完成，左下角出现“Upgrade successful“，就可以直接关闭这个STLinkUpgrade程序了。

### 5.2 下载运行

回到Cube IDE，再次点击绿色播放按钮，就会再次启动编译下载的过程，直到看到：

```
Download verified successfully 
```

就表明下载成功了。

此时，板上标着`TST`的LED就应该开始闪烁了。

如果之前烧录在板上的程序没有按照4.3的要求设置Debug选项，就会造成这次烧录失败。解决的办法是，拔下BT0的跳线帽，把它插到右边的两个插针上，如下图：

<img src="../img/5-4.jpeg" style="zoom:25%;" />

然后按一下板上的RST按钮，就在黄色跳线的下方。做完这些，再回到Cube IDE上编译下载。下载完成后，将跳线帽插回左边的位置，再按下RST按钮，程序就运行起来了。

只要按照4.3的要求设置了Debug选项，今后就不再需要切换BT0跳线了，也不再需要按RST按钮。

