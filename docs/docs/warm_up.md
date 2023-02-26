# 课前准备

## STM32CubeIDE 安装

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
    