# VS_Code下Arduino_STM32环境搭建

## Step 1：配置 Arduino IDE

- 复制 Arduino_STM32-master 到 Arduino-1.8.19\hardware 下面
- 打开 Arduino-1.8.19\arduino.exe ，安装 Arduino SAM Boards （32-bits ARM Cortex-M3）

## Step 2：安装 VS Code 

- 安装 VSCodeUserSetup-64-1.79.2.exe

- 打开 VS Code，安装 Arduino 插件

- File → Preferences → Settings → Edit in settings.json

  ```
  "arduino.path": "D:\\ZZH_software_2023\\arduino-1.8.19",
  "C_Cpp.intelliSenseEngineFallback": "disabled",
  "C_Cpp.intelliSenseEngine": "Tag Parser",
  "workbench.colorTheme": "Monokai"  
  ```

  并添加库路径:

  ```
  "C_Cpp.default.includePath": [
      "C:\\Users\\006\\Documents\\Arduino\\libraries\\**",  //include subfolders
  ],
  ```

## Step 3：上传固件

- 安装 flash_loader_demo_v2.8.0
- STM32F103C8T6 核心板，boot0 → 1, boot1 → 0, Rx → PA9, Tx → PA10
- 打开 STMFlashLoader Demo.exe，将 STM32duino_bootloader-master/binaries/generic_boot20_pc13 上传到开发板
- boot0 → 0, boot1 → 0

## Step 4：测试

- 使用Micro USB 连接核心板和电脑
- 打开 Arduino，打开 stm32f103c8t6_demo.ino
- Upload
- LED 闪烁，打开串口输出 a=0