﻿# CHARYBDIS 4X6 无线分体键盘 ZEPHYR 4.1 的 ZMK 配置

此配置支持两种模式：

- **独立模式**：右侧键盘作为中心，直接连接到主机
- **接收器模式**：带有显示屏的专用接收器作为中心，两个键盘都连接到它

## 目录

- [CHARYBDIS 4X6 无线分体键盘 ZEPHYR 4.1 的 ZMK 配置](#charybdis-4x6-无线分体键盘-zephyr-41-的-zmk-配置)
  - [目录](#目录)
  - [BOM (材料清单)](#bom-材料清单)
    - [接收器模式的附加组件](#接收器模式的附加组件)
      - [选项 1：Prospector 接收器 (Seeeduino XIAO BLE)](#选项-1-prospector-接收器-seeduino-xiao-ble)
      - [选项 2：Nice!Nano 接收器 (Nice!Nano v2)](#选项-2-nicenano-接收器-nicenano-v2)
      - [选项 3：YADS Prospector 接收器 (Seeeduino XIAO BLE)](#选项-3-yads-prospector-接收器-seeduino-xiao-ble)
  - [Tester Pro Micro 扩展板](#tester-pro-micro-扩展板)
  - [仓库结构](#仓库结构)
    - [关键文件说明](#关键文件说明)
      - [共享配置文件（整合）](#共享配置文件整合)
      - [特定于扩展板的文件](#特定于扩展板的文件)
  - [操作模式](#操作模式)
    - [独立模式](#独立模式)
    - [接收器模式](#接收器模式)
    - [接收器显示功能](#接收器显示功能)
      - [Prospector 接收器 (Seeeduino XIAO BLE)](#prospector-接收器-seeduino-xiao-ble)
      - [YADS Prospector 接收器 (Seeeduino XIAO BLE)](#yads-prospector-接收器-seeduino-xiao-ble)
      - [Nice!Nano 接收器 (Nice!Nano v2)](#nicenano-接收器-nicenano-v2)
  - [West.yml 配置](#westyml-配置)
    - [Remotes 部分](#remotes-部分)
    - [Projects 部分](#projects-部分)
    - [Self 部分](#self-部分)
  - [按键映射](#按键映射)
  - [轨迹球灵敏度配置](#轨迹球灵敏度配置)
    - [硬件传感器灵敏度 (CPI/DPI)](#硬件传感器灵敏度-cpidpi)
    - [软件缩放 (移动速度)](#软件缩放-移动速度)
    - [缩放值如何工作](#缩放值如何工作)
    - [参考文档](#参考文档)
  - [ZMK Studio 支持](#zmk-studio-支持)
    - [物理布局定义](#物理布局定义)
    - [启用/禁用 ZMK Studio](#启用禁用-zmk-studio)
    - [Studio 解锁](#studio-解锁)
  - [构建固件](#构建固件)
    - [GitHub Actions (自动)](#github-actions-自动)
    - [本地构建 (手动)](#本地构建-手动)
  - [刷写固件](#刷写固件)
    - [如何刷写](#如何刷写)
    - [刷写 (独立模式)](#刷写-独立模式)
      - [刷写清单 (先重置设置)](#刷写清单-先重置设置)
    - [刷写 (接收器模式)](#刷写-接收器模式)
      - [首次或更改模式时：先重置设置](#首次或更改模式时先重置设置)
    - [Tester Pro Micro (GPIO 测试)](#tester-pro-micro-gpio-测试)
      - [用于测试 Pro Micro 兼容板](#用于测试-pro-micro-兼容板)

## BOM (材料清单)

请参阅完整的 [材料清单](/docs/bom/readme.md)，包括电子元件、PCB、制造文件（可直接上传到 PCBWay/JLCPCB 的 gerber 文件）和 3D 打印文件。

### 接收器模式的附加组件

#### 选项 1：Prospector 接收器 (Seeeduino XIAO BLE)

- 1x Seeeduino XIAO BLE (nRF52840) - 接收器中心
- 1x [Prospector 显示模块](https://github.com/carrefinho/prospector) - 自定义 OLED 显示屏

#### 选项 2：Nice!Nano 接收器 (Nice!Nano v2)

- 1x Nice!Nano v2 (nRF52840) - 接收器中心
- 1x OLED 显示屏 (SSD1306, I2C) - 通用 OLED 模块
  - **128x32** (0.91" OLED) - 使用 `dongle_nice_32` 扩展板
  - **128x64** (0.96" OLED) - 使用 `dongle_nice_64` 扩展板
- 使用 [zmk-dongle-display](https://github.com/englmaxi/zmk-dongle-display) 模块

#### 选项 3：YADS Prospector 接收器 (Seeeduino XIAO BLE)

- 1x Seeeduino XIAO BLE (nRF52840) - 接收器中心
- 1x [Prospector 显示模块](https://github.com/carrefinho/prospector) - 自定义 OLED 显示屏
- 使用 [zmk-dongle-screen](https://github.com/janpfischer/zmk-dongle-screen) 模块 (YADS) *(当前已禁用 - 等待 Zephyr 4.1 兼容性)*
- Prospector 硬件的替代固件，具有不同功能

![无线键盘](/docs/picture/wireless-charybdis.png)

## Tester Pro Micro 扩展板

此仓库包含一个 **ZMK Tester 扩展板** (`tester_pro_micro`)，用于故障排除和测试 Pro Micro 兼容板（Nice!Nano、Seeeduino XIAO 等）。测试扩展板将所有 18 个可用的 GPIO 引脚（D0-D10、D14-D16、D18-D21）映射到虚拟"按键"，当触发时输出"PIN X"，帮助您在组装键盘之前验证所有 GPIO 引脚是否正常工作。

**使用方法：**

1. 将 `tester_pro_micro-nice_nano-zmk.uf2` 刷写到您的控制器（参见 [构建固件](#构建固件)）
2. 通过 USB 将板连接到您的计算机
3. 打开文本编辑器
4. 将开关或电线从任何 GPIO 引脚连接到 GND 并触发它
5. 板将输出 "PIN X"，其中 X 是引脚编号（例如 "PIN 14"）

测试器以仅 USB 模式运行（无 BLE），并包括两个用于 ZMK Studio 可视化的物理布局。

## 仓库结构

```text
zmk-config-charybdis/
├── boards/                          # 基于模块的扩展板（推荐 Zephyr 4.1+ 布局）
│   └── shields/
│       ├── charybdis/               # Charybdis 扩展板配置
│       │   ├── charybdis.dtsi                        # 通用设备树（键盘布局，kscan）
│       │   ├── charybdis_layers.h                    # 共享层定义
│       │   ├── charybdis_trackball_processors.dtsi   # 共享轨迹球处理配置
│       │   ├── charybdis_right_common.dtsi           # 右侧键盘共享硬件配置
│       │   ├── charybdis_left.conf                   # 左侧 Kconfig 选项（空）
│       │   ├── charybdis_left.overlay                # 左侧设备树覆盖
│       │   ├── charybdis_right_standalone.conf       # 右侧 Kconfig（独立模式）
│       │   ├── charybdis_right_standalone.overlay    # 右侧覆盖（独立模式）
│       │   ├── dongle_charybdis_right.conf           # 符号链接 → charybdis_right_standalone.conf
│       │   ├── dongle_charybdis_right.overlay        # 右侧覆盖（接收器模式）
│       │   ├── dongle_common.dtsi                    # 所有变体的基础共享接收器配置
│       │   ├── dongle_nice_common.dtsi               # Nice!Nano 平台通用配置
│       │   ├── dongle_prospector_common.dtsi         # Prospector 平台通用配置
│       │   ├── dongle_prospector.conf                # Prospector 接收器 Kconfig 选项
│       │   ├── dongle_prospector.overlay             # Prospector 接收器设备树覆盖
│       │   ├── dongle_nice_32.conf                   # Nice!Nano 接收器 32px Kconfig 选项
│       │   ├── dongle_nice_32.overlay                # Nice!Nano 接收器 32px 设备树覆盖
│       │   ├── dongle_nice_64.conf                   # Nice!Nano 接收器 64px Kconfig 选项
│       │   ├── dongle_nice_64.overlay                # Nice!Nano 接收器 64px 设备树覆盖
│       │   ├── Kconfig.defconfig                     # 扩展板 Kconfig 定义
│       │   └── Kconfig.shield                        # 扩展板 Kconfig 选项
│       └── tester_pro_micro/         # Pro Micro GPIO 测试扩展板
│           ├── Kconfig.shield                        # 扩展板标识符
│           ├── Kconfig.defconfig                     # 扩展板默认值（仅 USB，无 BLE）
│           ├── tester_pro_micro.zmk.yml              # 扩展板元数据
│           ├── tester_pro_micro.overlay              # GPIO 引脚定义（18 个引脚）
│           ├── tester_pro_micro.keymap               # 引脚测试宏
│           └── tester_pro_micro-layouts.dtsi         # 物理布局（引脚分布 + 单行）
├── config/                          # 主要 ZMK 配置目录（按键映射 + west 清单）
│   ├── charybdis.conf               # 全局 ZMK 配置
│   ├── charybdis.keymap             # 按键映射定义文件
│   ├── charybdis.zmk.yml            # ZMK 构建配置
│   ├── info.json                    # 仓库元数据
│   └── west.yml                     # West 清单（见下面的 West.yml 部分）
├── manual_build/                    # 本地构建脚本
│   ├── build.py                     # 交互式构建脚本
│   └── BUILD_README.md              # 构建说明
├── docs/                            # 文档
│   ├── bom/                         # 材料清单
│   │   ├── stl/                     # 3D 打印文件
│   │   │   ├── charybdis_left_base.stl
│   │   │   ├── charybdis_left_button.stl
│   │   │   ├── charybdis_left_case.stl
│   │   │   ├── scylla_right_base.stl
│   │   │   ├── scylla_right_button.stl
│   │   │   └── scylla_right_case.stl
│   │   └── readme.md
│   ├── keymap/                      # 按键映射文档
│   │   ├── config.yaml              # 按键映射绘制器配置
│   │   ├── keymap.yaml              # 基础按键映射定义
│   │   ├── keymap.svg               # 生成：可视化按键映射（由 render.sh 创建）
│   │   └── render.sh                # 解析按键映射并生成 SVG 的脚本
│   └── picture/                     # 图片
│       └── wireless-charybdis.png
├── build.yaml                       # GitHub Actions 构建配置
├── zephyr/
│   └── module.yml                   # Zephyr 模块标记（启用发现 boards/shields/）
└── readme.md                        # 此文件
```

### 关键文件说明

#### 共享配置文件（整合）

- **`charybdis_layers.h`**：层定义（BASE、POINTER、LOWER、RAISE、SYMBOLS、SCROLL、SNIPING），用于所有扩展板
- **`charybdis_trackball_processors.dtsi`**：共享轨迹球输入处理配置（狙击/滚动/移动模式）
- **`charybdis_right_common.dtsi`**：两种右侧键盘变体的通用硬件配置（GPIO、SPI、轨迹球设备）
- **`dongle_charybdis_right.conf`**：指向 `charybdis_right_standalone.conf` 的符号链接（相同的硬件配置）

#### 特定于扩展板的文件

- **`config/charybdis.keymap`**：定义所有按键层、行为和绑定
- **`boards/shields/charybdis/charybdis.dtsi`**：共享设备树定义（键盘矩阵、kscan、物理布局）
- **`charybdis_left.overlay`**：左侧配置（两种模式相同）
- **`charybdis_right_standalone.overlay`**：**独立模式**的右侧（本地处理轨迹球）
- **`dongle_charybdis_right.overlay`**：**接收器模式**的右侧（将轨迹球转发到接收器）
- **`dongle_common.dtsi`**：所有接收器变体的基础共享配置（矩阵、输入分割、物理布局）
- **`dongle_nice_common.dtsi`**：Nice!Nano 平台特定的通用配置（KSCAN、I2C）
- **`dongle_prospector_common.dtsi`**：Prospector 平台特定的通用配置（KSCAN）
- **`dongle_prospector.overlay`**：Prospector 接收器配置（从右侧外设接收轨迹球）
- **`dongle_nice_32.overlay`**：带 128x32 OLED 显示屏的 Nice!Nano 接收器
- **`dongle_nice_64.overlay`**：带 128x64 OLED 显示屏的 Nice!Nano 接收器
- **`config/west.yml`**：定义外部依赖项（见下面的 West.yml 部分）

## 操作模式

### 独立模式

在独立模式下，右侧键盘充当中央设备：

- **左侧键盘**：外设（Nice!Nano v2）
- **右侧键盘**：带轨迹球的中央设备（Nice!Nano v2）
- **连接**：左侧 → 右侧 → 主机计算机

### 接收器模式

在接收器模式下，专用接收器充当带显示屏的中央设备：

- **左侧键盘**：外设（Nice!Nano v2）
- **右侧键盘**：带轨迹球的外设（Nice!Nano v2）
- **接收器选项**：
  - **Prospector**：带自定义 Prospector 显示模块的 Seeeduino XIAO BLE
  - **Nice!Nano**：带通用 OLED（I2C）的 Nice!Nano v2
    - **128x32 OLED** (0.91") - 使用 `dongle_nice_32` 扩展板
    - **128x64 OLED** (0.96") - 使用 `dongle_nice_64` 扩展板
- **连接**：左侧 → 接收器 ← 右侧，接收器 → 主机计算机
- **配对顺序**：先配对左侧键盘，然后配对右侧键盘以获得正确的电池显示
- **电源**：USB 供电（不需要睡眠模式）

### 接收器显示功能

#### Prospector 接收器 (Seeeduino XIAO BLE)

- 活动层指示器，带层名称
- 两个外设的分割电池状态
- 外设连接状态指示器
- Caps Word 指示器
- 固定亮度（50%），无环境光传感器

#### YADS Prospector 接收器 (Seeeduino XIAO BLE)

- **显示屏**：Prospector 1.69" IPS LCD
- **功能**：使用 YADS（Yet Another Dongle Screen）固件
- **电池**：中央和外设的详细电池状态
- **WPM**：每分钟字数图表/指示器
- **亮度**：可通过键盘控制的亮度调节
- **系统**：连接状态、层指示、修饰键
- **睡眠**：支持深度睡眠以节省电量
- **状态**：当前已禁用，等待 Zephyr 4.1 兼容性修复 ([issue #29](https://github.com/janpfischer/zmk-dongle-screen/issues/29))

#### Nice!Nano 接收器 (Nice!Nano v2)

根据 OLED 显示屏尺寸，有两种变体可用：

**128x32 OLED (dongle_nice_32)：**

- **显示屏**：128x32 OLED (SSD1306) 通过 I2C（0.91" 模块）
- **活动层名称**，居中对齐并支持滚动
- **外设电池电量**（左侧和右侧键盘）
- **HID 指示器**（CAPS、NUM、SCROLL 锁定）
- **输出状态**（USB/BLE 连接）
- **活动修饰键显示**（Shift、Ctrl、Alt、GUI）
- **显示超时**：5 分钟（可配置）
- **针对 32px 高度优化**：禁用 Bongo cat，修饰键可选

**128x64 OLED (dongle_nice_64)：**

- **显示屏**：128x64 OLED (SSD1306) 通过 I2C（0.96" 模块）
- **活动层名称**，居中对齐并支持滚动
- **外设电池电量**（左侧和右侧键盘）
- **HID 指示器**（CAPS、NUM、SCROLL 锁定）
- **输出状态**（USB/BLE 连接）
- **活动修饰键显示**（Shift、Ctrl、Alt、GUI）
- **启用 Bongo cat**（有更多垂直空间可用）
- **显示超时**：5 分钟（可配置）

**接线（Nice!Nano 到 OLED）：**

- VCC → 3.3V
- GND → GND
- SDA → 引脚 2
- SCL → 引脚 3

## West.yml 配置

`config/west.yml` 文件定义了此配置中使用的 ZMK 固件依赖项和外部模块。

### Remotes 部分

```yaml
remotes:
  - name: zmkfirmware
    url-base: https://github.com/zmkfirmware
  - name: badjeff
    url-base: https://github.com/badjeff
  - name: carrefinho
    url-base: https://github.com/carrefinho
  - name: englmaxi
    url-base: https://github.com/englmaxi
  # - name: janpfischer  # disabled - Zephyr 4.1 issues
  #   url-base: https://github.com/janpfischer
```

- **`zmkfirmware`**：主要 ZMK 固件仓库，包含核心 ZMK 应用代码
- **`badjeff`**：包含用于 Charybdis 轨迹球的 PMW3610 轨迹球驱动程序的仓库。有关完整配置选项，请参阅 [zmk-pmw3610-driver](https://github.com/badjeff/zmk-pmw3610-driver)。
- **`carrefinho`**：包含接收器 Prospector 显示模块的仓库。有关显示配置选项，请参阅 [prospector-zmk-module](https://github.com/carrefinho/prospector-zmk-module)。
- **`englmaxi`**：包含 OLED 接收器显示模块的仓库。请参阅 [zmk-dongle-display](https://github.com/englmaxi/zmk-dongle-display)。
- **`janpfischer`**：*(已禁用)* 包含 YADS（Yet Another Dongle Screen）模块的仓库。请参阅 [zmk-dongle-screen](https://github.com/janpfischer/zmk-dongle-screen)。

### Projects 部分

```yaml
projects:
  - name: zmk
    remote: zmkfirmware
    revision: main
    import: app/west.yml
  - name: zmk-pmw3610-driver
    remote: badjeff
    revision: zmk-0.4
  - name: prospector-zmk-module
    remote: carrefinho
    revision: core/zephyr-4-1
  - name: zmk-dongle-display
    remote: englmaxi
    revision: main
  # - name: zmk-dongle-screen  # disabled - Zephyr 4.1 issues
  #   remote: janpfischer
  #   revision: upgrade-4.1
```

- **`zmk`**：
  - **用途**：主要 ZMK 固件应用
  - **来源**：`zmkfirmware` 远程
  - **版本**：`main` 分支
  - **导入**：包括 ZMK 仓库中 `app/west.yml` 的额外依赖项

- **`zmk-pmw3610-driver`**：
  - **用途**：ZMK 的 PMW3610 轨迹球传感器驱动程序
  - **来源**：`badjeff` 远程
  - **版本**：`zmk-0.4` 分支（PMW3610-alt 兼容 + Zephyr 4.1 更新）
  - **注意**：此驱动程序为 Charybdis 右侧使用的 PMW3610 轨迹球传感器提供设备树绑定和驱动代码

- **`prospector-zmk-module`**：
  - **用途**：用于带 ZMK Studio 支持的 Seeeduino XIAO BLE 接收器的自定义 OLED 显示模块
  - **来源**：`carrefinho` 远程
  - **版本**：`core/zephyr-4-1` 分支
  - **注意**：为接收器模式提供 `prospector_adapter` 扩展板，包括层显示、电池状态和连接指示器的小部件

- **`zmk-dongle-display`**：
  - **用途**：用于 Nice!Nano 接收器的通用 OLED 显示模块（128x32/128x64 显示屏）
  - **来源**：`englmaxi` 远程
  - **版本**：`main` 分支
  - **注意**：为通用 I2C OLED 显示屏（SSD1306）提供 `dongle_display` 扩展板。支持 128x32 和 128x64 显示屏，带有可配置的小部件。对于 32px 显示屏使用 `dongle_nice_32` 扩展板，对于 64px 显示屏使用 `dongle_nice_64` 扩展板。

- **`zmk-dongle-screen`**：*(已禁用)*
  - **用途**：用于带 ST7789V 显示屏的 Prospector 接收器的 YADS（Yet Another Dongle Screen）模块
  - **来源**：`janpfischer` 远程
  - **版本**：`upgrade-4.1` 分支
  - **状态**：当前已禁用，等待 Zephyr 4.1 兼容性修复 ([issue #29](https://github.com/janpfischer/zmk-dongle-screen/issues/29))
  - **注意**：为基于 ST7789V 的显示屏提供 `dongle_screen` 扩展板。功能包括 WPM 小部件、环境光传感器支持、通过键盘控制亮度和可自定义的状态栏。

### Self 部分

```yaml
self:
  path: config
```

- **`path: config`**：告诉 west 此仓库的配置文件位于 `config/` 目录中

## 按键映射

可以在 [/config/charybdis.keymap](/config/charybdis.keymap) 更新并使用 [render.sh](/docs/keymap/render.sh) 渲染

使用 [Keymap Drawer](https://github.com/caksoylar/keymap-drawer-web/) 生成

![按键映射](/docs/keymap/keymap.svg)

## 轨迹球灵敏度配置

轨迹球灵敏度可以在硬件和软件级别进行调整。

### 硬件传感器灵敏度 (CPI/DPI)

PMW3610 轨迹球传感器 CPI（每英寸计数）在 [`boards/shields/charybdis/charybdis_right_common.dtsi`](/boards/shields/charybdis/charybdis_right_common.dtsi) 中配置：

```dts
trackball: trackball@0 {
    compatible = "pixart,pmw3610";
    cpi = <800>;  // 更改此值
    // ...
};
```

**常见 CPI 值：**

- `400` - 低灵敏度（需要更多物理移动）
- `800` - 默认，平衡灵敏度
- `1200` - 高灵敏度
- `1600` - 非常高灵敏度

### 软件缩放 (移动速度)

软件缩放在 [`boards/shields/charybdis/charybdis_trackball_processors.dtsi`](/boards/shields/charybdis/charybdis_trackball_processors.dtsi) 中按层配置。轨迹球有三种不同模式，具有独立的灵敏度设置：

```dts
// 正常光标移动（BASE 和 POINTER 层）
move {
    layers = <BASE POINTER>;
    input-processors = <&zip_xy_scaler 7 6>;  // 乘数 除数
};

// 精确移动（SNIPING 层）
snipe {
    layers = <SNIPING>;
    input-processors = <&zip_xy_scaler 1 3>;  // 1/3 速度以提高精度
};

// 滚动模式（SCROLL 层）
scroll {
    layers = <SCROLL>;
    input-processors = <&zip_xy_scaler 1 10>;  // 调整滚动速度
};
```

### 缩放值如何工作

缩放器使用公式：`输出 = (输入 × 乘数) / 除数`

**示例：**

- `<&zip_xy_scaler 2 1>` - 使移动速度加倍（输入 × 2 / 1）
- `<&zip_xy_scaler 1 2>` - 使移动速度减半（输入 × 1 / 2）
- `<&zip_xy_scaler 7 6>` - 略快于 1:1（输入 × 7 / 6）

**调整灵敏度：**

| 目标 | 更改 | 示例 |
|------|------|------|
| **更快的光标** | 增加乘数或减少除数 | `7 6` → `8 6` 或 `7 5` |
| **更慢的光标** | 减少乘数或增加除数 | `7 6` → `6 6` 或 `7 7` |
| **更快的滚动** | 减少除数 | `1 10` → `1 5` |
| **更慢的滚动** | 增加除数 | `1 10` → `1 15` |

⚠️ **重要：** 为避免溢出，乘数和除数均使用 ≤ 16 的值。

### 参考文档

有关输入处理器的更多详细信息，请参阅：

- [ZMK 缩放器文档](https://zmk.dev/docs/keymaps/input-processors/scaler)
- [PMW3610 驱动程序配置](https://github.com/badjeff/zmk-pmw3610-driver)

## ZMK Studio 支持

此配置包括对 [ZMK Studio](https://zmk.dev/docs/features/studio) 的支持，允许您交互式配置和测试键盘布局。

### 物理布局定义

ZMK Studio 的物理布局在 [`boards/shields/charybdis/charybdis.dtsi`](/boards/shields/charybdis/charybdis.dtsi) 中的 `charybdis_6col_layout` 部分定义。这定义了 ZMK Studio 中视觉表示所需的物理按键位置、大小和旋转。

### 启用/禁用 ZMK Studio

ZMK Studio 支持通过 [`build.yaml`](/build.yaml) 中的构建配置默认启用。

**独立模式** - 右侧键盘具有 ZMK Studio：

```yaml
- board: nice_nano
  shield: charybdis_right_standalone
  snippet: studio-rpc-usb-uart
  cmake-args: -DCONFIG_ZMK_STUDIO=y
```

**接收器模式** - 多个接收器具有 ZMK Studio：

**Prospector 接收器：**

```yaml
- board: xiao_ble
  shield: dongle_prospector prospector_adapter
  snippet: studio-rpc-usb-uart
  cmake-args: -DCONFIG_ZMK_STUDIO=y
```

**Nice!Nano 接收器（32px 和 64px）：**

```yaml
- board: nice_nano
  shield: dongle_nice_32 dongle_display  # 或 dongle_nice_64
  snippet: studio-rpc-usb-uart
  cmake-args: -DCONFIG_ZMK_STUDIO=y
```

要禁用 ZMK Studio 支持，请在相应的构建配置中注释掉 `snippet` 和 `cmake-args` 行。

### Studio 解锁

要解锁 ZMK Studio 进行配置，请同时按下所有三个右侧拇指键：

- **RET**（返回/ Enter）
- **SYMBOLS**（按住）/ **SPACE**（轻触）
- **RAISE**（按住）/ **BSPC**（轻触）

此组合在 [`config/charybdis.keymap`](/config/charybdis.keymap) 中定义为 `combo_studio_unlock`，使用按键位置 53、54 和 55。

## 构建固件

### GitHub Actions (自动)

将更改推送到您的仓库，GitHub Actions 将自动为 [`build.yaml`](/build.yaml) 中定义的所有配置构建固件。固件文件将在 Actions 工件中作为包含以下内容的 `firmware.zip` 文件提供：

- `charybdis_left-nice_nano-zmk.uf2`
- `charybdis_right_standalone-nice_nano-zmk.uf2`
- `dongle_charybdis_right-nice_nano-zmk.uf2`
- `dongle_prospector prospector_adapter-xiao_ble-zmk.uf2`
- `dongle_nice_32 dongle_display-nice_nano-zmk.uf2`
- `dongle_nice_64 dongle_display-nice_nano-zmk.uf2`
- `tester_pro_micro-nice_nano-zmk.uf2`
- `settings_reset-nice_nano-zmk.uf2`
- `settings_reset-xiao_ble-zmk.uf2`

### 本地构建 (手动)

有关使用 Docker 进行本地构建的详细说明，请参阅 [`manual_build/BUILD_README.md`](/manual_build/BUILD_README.md)。

交互式构建脚本提供以下选项：

1. **charybdis_left** - 左侧键盘（适用于两种模式）
2. **charybdis_right_standalone** - 独立模式的右侧键盘（Nice!Nano）
3. **dongle_charybdis_right** - 接收器模式的右侧键盘（Nice!Nano）
4. **dongle_prospector prospector_adapter** - 带 Prospector 显示屏的接收器（XIAO BLE）
5. **dongle_nice_32 dongle_display** - 带 128x32 OLED 的 Nice!Nano 接收器
6. **dongle_nice_64 dongle_display** - 带 128x64 OLED 的 Nice!Nano 接收器
7. **tester_pro_micro** - Pro Micro 兼容板的 GPIO 引脚测试器
8. **settings_reset** - 重置存储的设置

注意：本地构建使用 `manual_build/west-workspace/` 下的专用工作区，其行为应与 CI 相同。如果特定配置在本地失败，建议在 GitHub Actions 中构建，然后在依赖项/工作区稳定后在本地迭代。

构建的固件文件会自动复制到 `manual_build/artifacts/output/`，并带有描述性名称。

## 刷写固件

### 如何刷写

1. 双击板上的重置按钮进入引导加载程序模式
2. 板将显示为 USB 驱动器
3. 将相应的 `.uf2` 文件复制到 USB 驱动器
4. 板将自动刷写并重新启动

### 刷写 (独立模式)

#### 刷写清单 (先重置设置)

1. 将 `settings_reset-nice_nano-zmk.uf2` 刷写到**两个**键盘
2. 将 `charybdis_left-nice_nano-zmk.uf2` 刷写到左侧键盘
3. 将 `charybdis_right_standalone-nice_nano-zmk.uf2` 刷写到右侧键盘
4. 键盘将自动相互配对

### 刷写 (接收器模式)

#### 首次或更改模式时：先重置设置

1. **刷写设置重置和接收器固件**（选择您的接收器类型）：

   a) **Prospector 接收器 (Seeeduino XIAO BLE)**：
      - 将 `settings_reset-nice_nano-zmk.uf2` 刷写到**两个**键盘
      - 将 `settings_reset-xiao_ble-zmk.uf2` 刷写到**接收器**
      - 将 `dongle_prospector prospector_adapter-xiao_ble-zmk.uf2` 刷写到接收器

   b) **YADS Prospector 接收器 (Seeeduino XIAO BLE)**：
      - 将 `settings_reset-nice_nano-zmk.uf2` 刷写到**两个**键盘
      - 将 `settings_reset-xiao_ble-zmk.uf2` 刷写到**接收器**
      - 将 `dongle_yads_prospector dongle_screen-xiao_ble-zmk.uf2` 刷写到接收器

   c) **Nice!Nano 接收器 (Nice!Nano v2)**
      - 将 `settings_reset-nice_nano-zmk.uf2` 刷写到**所有三个**设备（左侧、右侧、接收器）
      - 将相应的接收器固件刷写到接收器：
        - **128x32 OLED**：`dongle_nice_32 dongle_display-nice_nano-zmk.uf2`
        - **128x64 OLED**：`dongle_nice_64 dongle_display-nice_nano-zmk.uf2`
      - 通过 I2C 将 OLED 显示屏连接到接收器（SDA→引脚 2，SCL→引脚 3）

2. 将 `charybdis_left-nice_nano-zmk.uf2` 刷写到左侧键盘
3. 将 `dongle_charybdis_right-nice_nano-zmk.uf2` 刷写到右侧键盘
4. **重要**：先将左侧键盘配对到接收器，然后将右侧键盘配对

### Tester Pro Micro (GPIO 测试)

#### 用于测试 Pro Micro 兼容板

1. 将 `tester_pro_micro-nice_nano-zmk.uf2`（或您的板变体）刷写到控制器
2. 通过 USB 将板连接到您的计算机
3. 打开文本编辑器或终端
4. 将开关或电线从任何 GPIO 引脚连接到 GND 并触发它
5. 板将输出 "PIN X"，其中 X 是引脚编号（例如 "PIN 14"）
6. 测试所有您要验证的引脚

**注意**：测试器固件禁用蓝牙并以仅 USB 模式运行以简化操作。