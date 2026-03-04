# Charybdis 键盘设置说明文档

本文档详细介绍如何配置 Charybdis 4x6 无线分体键盘的各项参数，特别是轨迹球相关设置。

## 目录

- [轨迹球参数设置](#轨迹球参数设置)
  - [硬件传感器灵敏度 (CPI/DPI)](#硬件传感器灵敏度-cpidpi)
  - [软件缩放 (移动速度)](#软件缩放-移动速度)
  - [缩放值的工作原理](#缩放值的工作原理)
  - [调整灵敏度的方法](#调整灵敏度的方法)
- [其他配置选项](#其他配置选项)
  - [操作模式切换](#操作模式切换)
  - [ZMK Studio 支持](#zmk-studio-支持)
  - [构建和刷写固件](#构建和刷写固件)
- [故障排除](#故障排除)

## 轨迹球参数设置

### 硬件传感器灵敏度 (CPI/DPI)

轨迹球的硬件灵敏度通过修改 `boards/shields/charybdis/charybdis_right_common.dtsi` 文件中的 CPI 值来调整：

```dts
// 路径: boards/shields/charybdis/charybdis_right_common.dtsi
trackball: trackball@0 {
    compatible = "pixart,pmw3610";
    cpi = <800>;  // 修改此值
    // ...
};
```

**推荐 CPI 值**：
- `400` - 低灵敏度（需要更多物理移动，适合精确操作）
- `800` - 默认值，平衡灵敏度
- `1200` - 高灵敏度（适合快速移动）
- `1600` - 非常高灵敏度（适合大屏幕或快速导航）

### 软件缩放 (移动速度)

软件缩放通过修改 `boards/shields/charybdis/charybdis_trackball_processors.dtsi` 文件来配置，轨迹球有三种不同模式，每种模式有独立的灵敏度设置：

```dts
// 路径: boards/shields/charybdis/charybdis_trackball_processors.dtsi

// 正常光标移动 (BASE 和 POINTER 层)
move {
    layers = <BASE POINTER>;
    input-processors = <&zip_xy_scaler 7 6>;  // 乘数 除数
};

// 精确移动 (SNIPING 层)
snipe {
    layers = <SNIPING>;
    input-processors = <&zip_xy_scaler 1 3>;  // 1/3 速度以提高精度
};

// 滚动模式 (SCROLL 层)
scroll {
    layers = <SCROLL>;
    input-processors = <&zip_xy_scaler 1 10>;  // 调整滚动速度
};
```

### 缩放值的工作原理

缩放器使用公式：`输出 = (输入 × 乘数) / 除数`

**示例**：
- `<&zip_xy_scaler 2 1>` - 加倍移动速度 (输入 × 2 / 1)
- `<&zip_xy_scaler 1 2>` - 减半移动速度 (输入 × 1 / 2)
- `<&zip_xy_scaler 7 6>` - 略快于 1:1 (输入 × 7 / 6)

### 调整灵敏度的方法

| 目标 | 更改方法 | 示例 |
|------|---------|------|
| **更快的光标** | 增加乘数或减少除数 | `7 6` → `8 6` 或 `7 5` |
| **更慢的光标** | 减少乘数或增加除数 | `7 6` → `6 6` 或 `7 7` |
| **更快的滚动** | 减少除数 | `1 10` → `1 5` |
| **更慢的滚动** | 增加除数 | `1 10` → `1 15` |

⚠️ **重要**：乘数和除数的值应 ≤ 16，以避免溢出。

## 其他配置选项

### 操作模式切换

Charybdis 键盘支持两种操作模式：

1. **独立模式**：右侧键盘作为中央设备，直接连接到主机
   - 左侧键盘：外设 (Nice!Nano v2)
   - 右侧键盘：带轨迹球的中央设备 (Nice!Nano v2)
   - 连接方式：左侧 → 右侧 → 主机电脑

2. ** dongle 模式**：专用 dongle 作为中央设备，带有显示屏
   - 左侧键盘：外设 (Nice!Nano v2)
   - 右侧键盘：带轨迹球的外设 (Nice!Nano v2)
   - dongle 选项：
     - **Prospector**：带有自定义 Prospector 显示模块的 Seeeduino XIAO BLE
     - **Nice!Nano**：带有通用 OLED (I2C) 的 Nice!Nano v2
       - **128x32 OLED** (0.91") - 使用 `dongle_nice_32` shield
       - **128x64 OLED** (0.96") - 使用 `dongle_nice_64` shield
   - 连接方式：左侧 → Dongle ← 右侧, Dongle → 主机电脑
   - 配对顺序：先配对左侧键盘，然后配对右侧键盘以获得正确的电池显示

### ZMK Studio 支持

本配置包含对 [ZMK Studio](https://zmk.dev/docs/features/studio) 的支持，允许你交互式配置和测试键盘布局。

**解锁 ZMK Studio**：同时按下右侧三个拇指键：
- **RET** (Return/Enter)
- **SYMBOLS** (按住) / **SPACE** (点击)
- **RAISE** (按住) / **BSPC** (点击)

此组合在 `config/charybdis.keymap` 中定义为 `combo_studio_unlock`，使用键位置 53, 54 和 55。

### 构建和刷写固件

### 重置设置步骤

在以下情况下，你需要先重置设备的设置：

1. **首次刷写固件**：新设备第一次刷写时
2. **更改操作模式**：从独立模式切换到 dongle 模式，或反之
3. **配对问题**：设备无法正确配对时
4. **设置异常**：键盘行为异常时

#### 如何重置设置

1. **进入引导加载程序模式**：
   - 双击设备上的重置按钮
   - 设备将显示为 USB 驱动器

2. **刷写重置固件**：
   - 对于 Nice!Nano 设备：复制 `settings_reset-nice_nano-zmk.uf2` 到 USB 驱动器
   - 对于 Seeeduino XIAO BLE 设备：复制 `settings_reset-xiao_ble-zmk.uf2` 到 USB 驱动器

3. **等待设备重启**：
   - 设备将自动刷写并重启
   - 此时设备的所有设置已被清除

#### 重置后操作

重置完成后，你可以按照正常的刷写流程刷入所需的固件：
- 独立模式：刷入 `charybdis_left-nice_nano-zmk.uf2` 和 `charybdis_right_standalone-nice_nano-zmk.uf2`
- Dongle 模式：刷入相应的 dongle 固件和键盘固件

#### GitHub Actions (自动)

将更改推送到你的仓库，GitHub Actions 将自动为 `build.yaml` 中定义的所有配置构建固件。固件文件将在 Actions 工件中作为 `firmware.zip` 文件提供，包含：
- `charybdis_left-nice_nano-zmk.uf2`
- `charybdis_right_standalone-nice_nano-zmk.uf2`
- `dongle_charybdis_right-nice_nano-zmk.uf2`
- `dongle_prospector prospector_adapter-xiao_ble-zmk.uf2`
- `dongle_nice_32 dongle_display-nice_nano-zmk.uf2`
- `dongle_nice_64 dongle_display-nice_nano-zmk.uf2`
- `tester_pro_micro-nice_nano-zmk.uf2`
- `settings_reset-nice_nano-zmk.uf2`
- `settings_reset-xiao_ble-zmk.uf2`

#### 本地构建 (手动)

对于使用 Docker 的本地构建，请参阅 `manual_build/BUILD_README.md` 获取详细说明。

交互式构建脚本提供以下选项：
1. **charybdis_left** - 左侧键盘（适用于两种模式）
2. **charybdis_right_standalone** - 独立模式的右侧键盘 (Nice!Nano)
3. **dongle_charybdis_right** - dongle 模式的右侧键盘 (Nice!Nano)
4. **dongle_prospector prospector_adapter** - 带有 Prospector 显示屏的 dongle (XIAO BLE)
5. **dongle_nice_32 dongle_display** - 带有 128x32 OLED 的 Nice!Nano dongle
6. **dongle_nice_64 dongle_display** - 带有 128x64 OLED 的 Nice!Nano dongle
7. **tester_pro_micro** - 用于 Pro Micro 兼容板的 GPIO 引脚测试器
8. **settings_reset** - 重置存储的设置

构建的固件文件会自动复制到 `manual_build/artifacts/output/` 目录，文件名具有描述性。

#### 刷写固件

1. 双击板上的重置按钮进入引导加载程序模式
2. 板将显示为 USB 驱动器
3. 将适当的 `.uf2` 文件复制到 USB 驱动器
4. 板将自动刷写并重新启动

**独立模式刷写步骤**：
1. 向**两个**键盘刷写 `settings_reset-nice_nano-zmk.uf2`
2. 向左键盘刷写 `charybdis_left-nice_nano-zmk.uf2`
3. 向右键盘刷写 `charybdis_right_standalone-nice_nano-zmk.uf2`
4. 键盘将自动相互配对

**Dongle 模式刷写步骤**：
1. **刷写设置重置和 dongle 固件**（选择你的 dongle 类型）：
   a) **Prospector Dongle (Seeeduino XIAO BLE)**：
      - 向**两个**键盘刷写 `settings_reset-nice_nano-zmk.uf2`
      - 向**dongle**刷写 `settings_reset-xiao_ble-zmk.uf2`
      - 向 dongle 刷写 `dongle_prospector prospector_adapter-xiao_ble-zmk.uf2`

   b) **Nice!Nano Dongle (Nice!Nano v2)**
      - 向**所有三个**设备（左侧、右侧、dongle）刷写 `settings_reset-nice_nano-zmk.uf2`
      - 向 dongle 刷写适当的 dongle 固件：
        - **128x32 OLED**：`dongle_nice_32 dongle_display-nice_nano-zmk.uf2`
        - **128x64 OLED**：`dongle_nice_64 dongle_display-nice_nano-zmk.uf2`
      - 通过 I2C 将 OLED 显示屏连接到 dongle（SDA→Pin 2, SCL→Pin 3）

2. 向左键盘刷写 `charybdis_left-nice_nano-zmk.uf2`
3. 向右键盘刷写 `dongle_charybdis_right-nice_nano-zmk.uf2`
4. **重要**：先将左侧键盘配对到 dongle，然后再配对右侧键盘

## 故障排除

### 轨迹球不工作
1. 检查轨迹球连接是否正确
2. 确认固件已正确刷写
3. 尝试重置设置并重新刷写固件

### 灵敏度调整后没有效果
1. 确保修改了正确的文件
2. 重新构建并刷写固件
3. 检查缩放值是否在有效范围内（≤ 16）

### 配对问题
1. 确保先重置所有设备的设置
2. 按照正确的配对顺序：独立模式（左侧→右侧），dongle 模式（左侧→dongle→右侧）
3. 如果配对失败，尝试重新进入引导加载程序模式并重新刷写

### 电池显示问题
1. 确保按照正确的顺序配对设备
2. 检查电池电量是否充足
3. 尝试重置设置并重新配对

## 参考文档

- [ZMK 官方文档](https://zmk.dev/docs)
- [ZMK Scaler 文档](https://zmk.dev/docs/keymaps/input-processors/scaler)
- [PMW3610 驱动配置](https://github.com/badjeff/zmk-pmw3610-driver)
- [Prospector 显示模块](https://github.com/carrefinho/prospector)
- [ZMK Dongle Display](https://github.com/englmaxi/zmk-dongle-display)

---

希望本设置说明文档能帮助你成功配置 Charybdis 键盘的各项参数，特别是轨迹球灵敏度设置。如果遇到任何问题，请参考上述故障排除部分或查阅相关文档。