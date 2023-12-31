# MicroPython的ST7789驱动程序

该驱动程序基于[devbis的st7789_mpy驱动程序。](https://github.com/devbis/st7789_mpy) 我修改了原始驱动程序，为我的一个项目添加了以下功能：

- 显示旋转
- 滚动
- 使用从True Type字体转换的位图写入文本
- 使用8位和16位宽位图字体绘制文本
- 使用Hershey矢量字体绘制文本
- 绘制JPG，包括一个慢速模式，用于绘制大于可用RAM的JPG，使用来自http://elm-chan.org/fsw/tjpgd/00index.html的TJpgDec - Tiny JPEG解压缩器R0.01d。
- 使用https://github.com/kikuchan/pngle中的pngle库绘制PNG
- 绘制和旋转多边形和填充多边形
- 跟踪边界
- 自定义初始化功能，以支持st7735、ili9341、ili9342和其他显示器。请参见M5Stack Core、M5Stack Core2、T-DONGLE-S3和Wio_Terminal设备的examples/configs文件夹。

附带了12种基于经典PC文本模式字体的位图字体，26种Hershey矢量字体以及用于不同设备的几个示例程序。

## 显示配置

一些显示器可能使用BGR颜色顺序或反转颜色。 `cfg_helper.py` 程序可用于确定显示器所需的颜色顺序、反转模式、colstart 和 rowstart 值。

### 颜色模式

您可以通过使用 `st7789.RED` 颜色填充显示器并观察实际显示的颜色来测试显示器所需的正确颜色顺序。

- 如果显示的颜色是红色，则设置正确。
- 如果显示的颜色是蓝色，则 `color_order` 应为 `st7789.BGR`。
- 如果显示的颜色是黄色，则 `inversion_mode` 应为 `True`。
- 如果显示的颜色是青色，则 `color_order` 应为 `st7789.BGR` 并且 `inversion_mode` 应为 `True`。

### colstart 和 rowstart

一些显示器的帧缓冲内存比物理显示矩阵要大。在这些情况下，驱动程序必须配置为相对于帧缓冲的第一个物理列和行像素的位置。每个显示的旋转设置可能需要不同的 colstart 和 rowstart 值。

对于常见的 135x240、240x240、170x320 和 240x320 显示器，驱动程序会自动设置 `colstart` 和 `rowstart` 值。如果默认值对您的显示器不起作用，可以使用 `offsets` 方法覆盖这些值。`offsets` 方法应在任何 `rotation` 方法调用之后调用。

#### 128x128 st7735 cfg_helper.py example

```
inversion_mode(False)
color_order = st7789.BGR
for rotation 0 use offset(2, 1)
for rotation 1 use offset(1, 2)
for rotation 2 use offset(2, 3)
for rotation 3 use offset(3, 2)
```

#### 128x160 st7735 cfg_helper.py example

```
inversion_mode(False)
color_order = st7789.RGB
for rotation 0 use offset(0, 0)
for rotation 1 use offset(0, 0)
for rotation 2 use offset(0, 0)
for rotation 3 use offset(0, 0)
```

##  预编译固件文件

固件目录包含了使用 st7789 C 驱动程序和冻结的 Python 字体文件为各种设备预编译的固件。有关字体文件的更多信息，请参阅 fonts 文件夹中的 README.md 文件。

MicroPython 版本为 MicroPython v1.20.0，使用 ESP IDF v4.4.4 编译，使用 CMake 构建。

Directory             | File         | Device
--------------------- | ------------ | ----------------------------------
GENERIC-7789          | firmware.bin | Generic ESP32 devices
GENERIC_SPIRAM-7789   | firmware.bin | Generic ESP32 devices with SPI Ram
GENERIC_C3            | firmware.bin | Generic ESP32-C3 devices
LOLIN_S2_MINI         | firmware.bin | Wemos S2 mini
PYBV11                | firmware.dfu | Pyboard v1.1 (No PNG)
RP2                   | firmware.uf2 | Raspberry Pi Pico RP2040
RP2W                  | firmware.uf2 | Raspberry Pi PicoW RP2040
T-DISPLAY             | firmware.bin | LILYGO® TTGO T-Display
T-Watch-2020          | firmware.bin | LILYGO® T-Watch 2020
WIO_TERMINAL          | firmware.bin | Seeed Wio Terminal


## Additional Modules

Module             | Source
------------------ | -----------------------------------------------------------
axp202c            | https://github.com/lewisxhe/AXP202X_Libraries
focaltouch         | https://gitlab.com/mooond/t-watch2020-esp32-with-micropython

## Video Examples

Example               | Video
--------------------- | -----------------------------------------------------------
PYBV11 hello.py       | https://youtu.be/OtcERmad5ps
PYBV11 scroll.py      | https://youtu.be/ro13rvaLKAc
T-DISPLAY fonts.py    | https://youtu.be/2cnAhEucPD4
T-DISPLAY hello.py    | https://youtu.be/z41Du4GDMSY
T-DISPLAY scroll.py   | https://youtu.be/GQa-RzHLBak
T-DISPLAY roids.py    | https://youtu.be/JV5fPactSPU
TWATCH-2020 draw.py   | https://youtu.be/O_lDBnvH1Sw
TWATCH-2020 hello.py  | https://youtu.be/Bwq39tuMoY4
TWATCH-2020 bitmap.py | https://youtu.be/DgYzgnAW2d8
TWATCH-2020 watch.py  | https://youtu.be/NItKb6umMc4

This is a work in progress.

## Thanks go out to:

- https://github.com/devbis for the original driver this is based on.
- https://github.com/hklang10 for letting me know of the new mp_raise_ValueError().
- https://github.com/aleggon for finding the correct offsets for 240x240
  displays and for discovering issues compiling STM32 ports.

-- Russ

##  概述

这是一个用于处理基于 ST7789 芯片的廉价显示器的 MicroPython 驱动程序。该驱动程序是用 C 语言编写的。提供了适用于 ESP32、带有 SPIRAM 的 ESP32、pyboard1.1 和 Raspberry Pi Pico 设备的固件。


<p align="center">
  <img src="https://raw.githubusercontent.com/russhughes/st7789_mpy/master/docs/ST7789.jpg" alt="ST7789 display photo"/>
</p>


# 在 Ubuntu 20.04.2 中设置 MicroPython 构建环境

如果遇到与 st7789 驱动程序直接无关的任何构建问题，请查看 MicroPython [README.md](https://github.com/micropython/micropython/blob/master/ports/esp32/README.md#setting-up-esp-idf-and-the-build-environment)。建议的 MicroPython 构建说明可能已经更改。

如果您是在新安装的 Ubuntu 或 Windows Subsystem for Linux 上使用，请使用 apt-get 更新和升级 Ubuntu。

```bash
apt-get -y update
sudo apt-get -y upgrade
```

使用 apt-get 安装所需的构建工具。

```bash
sudo apt-get -y install build-essential libffi-dev git pkg-config cmake virtualenv python3-pip python3-virtualenv
```

### 安装兼容的 esp-idf SDK

MicroPython README.md 中提到："ESP-IDF 变化很快，MicroPython 仅支持特定版本。当前，MicroPython 支持 v4.0.2、v4.1.1 和 v4.2，尽管其他 IDF v4 版本也可能有效。" 我在使用 IDF v4.4 时取得了良好的效果。

克隆 esp-idf SDK 存储库 - 这通常需要几分钟。

```bash
-b v4.4 --recursive https://github.com/espressif/esp-idf.git
cd esp-idf/
git pull
```

如果您已经有了 IDF 的副本，可以检出与 MicroPython 兼容的版本，并使用以下命令更新子模块：

```bash
$ git checkout v4.4
$ git submodule update --init --recursive
```

安装 esp-idf SDK。

```bash
./install.sh
```

将 esp-idf export.sh 脚本源化以设置所需的环境变量。您必须源化文件，而不能使用 ./export.sh 运行它。在编译 MicroPython 之前，您需要在这个文件之前源化它。

```bash
export.sh
cd ..
```

克隆 MicroPython 存储库。

```bash
git clone https://github.com/micropython/micropython.git
```

克隆 st7789 驱动程序存储库。

```bash
git clone https://github.com/russhughes/st7789_mpy.git
```

更新 git 子模块并编译 MicroPython 交叉编译器。

```bash
micropython/
git submodule update --init
cd mpy-cross/
make
cd ..
cd ports/esp32
```

将要包含在固件中作为冻结的 Python 模块的任何 .py 文件复制到 ports/esp32 中的 modules 子目录中。请注意，可用的闪存空间有限。如果您收到一条错误消息，指示代码不适合分区，或者如果您的固件持续以错误重新启动，则说明您已超出了此限制。

例如：

```bash
cp ../../../st7789_mpy/fonts/bitmap/vga1_16x16.py modules
cp ../../../st7789_mpy/fonts/truetype/NotoSans_32.py modules
cp ../../../st7789_mpy/fonts/vector/scripts.py modules
```

使用位于 modules 目录中的驱动程序和冻结的 .py 文件构建 MicroPython 固件。如果您没有将任何 .py 文件添加到 modules 目录中，可以省略 FROZEN_MANIFEST 和 FROZEN_MPY_DIR 设置。

```bash
make USER_C_MODULES=../../../../st7789_mpy/st7789/micropython.cmake FROZEN_MANIFEST="" FROZEN_MPY_DIR=$UPYDIR/modules
```

擦除并将固件刷入设备。将 PORT= 设置为 ESP32 的 USB 串行端口。我在 Windows 子系统（WSL2）中无法使 USB 串行端口工作。如果您遇到相同的问题，可以复制 firmware.bin 文件并使用 Windows 的 esptool.py 刷写设备。

```bash
USER_C_MODULES=../../../../st7789_mpy/st7789/micropython.cmake PORT=/dev/ttyUSB0 erase
make USER_C_MODULES=../../../../st7789_mpy/st7789/micropython.cmake PORT=/dev/ttyUSB0 deploy
```

firmware.bin 文件将位于 build-GENERIC 目录中。要使用 python 的 esptool.py 实用程序进行刷写，请使用 pip3 安装 esptool（如果尚未安装）。

```bash
pip3 install esptool
```

将 PORT= 设置为 ESP32 的 USB 串行端口。

```bash
esptool.py --port COM3 erase_flash
esptool.py --chip esp32 --port COM3 write_flash -z 0x1000 firmware.bin
```
## CMake building instructions for MicroPython 1.14 and later

for ESP32:

    $ cd micropython/ports/esp32

And then compile the module with specified USER_C_MODULES dir.

    $ make USER_C_MODULES=../../../../st7789_mpy/st7789/micropython.cmake

for Raspberry Pi PICO:

    $ cd micropython/ports/rp2

And then compile the module with specified USER_C_MODULES dir.

    $ make USER_C_MODULES=../../../st7789_mpy/st7789/micropython.cmake

## Working examples

该模块在ESP32、基于STM32的pyboard v1.1以及树莓派Pico上进行了测试。您需要提供一个 `SPI` 对象和用于屏幕 `dc` 输入的引脚。


```python
"""
ESP32 示例
要使用高于26.6MHz的波特率，您必须使用我的固件或修改 micropython 源代码，通过在 machine_hw_spi_init_internal.c   文件中的 spi_device_interface_config_t 结构体的 .flag 成员中添加 SPI_DEVICE_NO_DUMMY 来增加 SPI 波特率限制。如果不这样做，使用过高波特率将导致 ESP32 崩溃。
"""

import machine
import st7789
spi = machine.SPI(2, baudrate=40000000, polarity=1, sck=machine.Pin(18), mosi=machine.Pin(23))
display = st7789.ST7789(spi, 240, 240, reset=machine.Pin(4, machine.Pin.OUT), dc=machine.Pin(2, machine.Pin.OUT))
display.init()
```

## 方法

- `st7789.ST7789(spi, width, height, dc, reset, cs, backlight, rotations, rotation, custom_init, color_order, inversion, options, buffer_size)`

  ### 必需的位置参数：

  - `spi` SPI 设备
  - `width` 显示器宽度
  - `height` 显示器高度

  ### 必需的关键字参数：

  - `dc` 设置连接到显示器数据/命令选择输入的引脚。 此参数始终是必需的。

  ### 可选的关键字参数：

  - `reset` 设置连接到显示器硬件复位输入的引脚。如果显示器的复位引脚被连接到高电平，那么 `reset` 参数不是必需的。

  - `cs` 设置连接到显示器芯片选择输入的引脚。如果显示器的 CS 引脚被连接到低电平，那么显示器必须是连接到 SPI 端口的唯一设备。显示器将始终是所选设备，`cs` 参数不是必需的。

  - `backlight` 设置连接到显示器背光使能输入的引脚。显示器的背光输入通常可以漂浮或断开连接，因为某些显示器的背光始终处于开启状态，无法关闭。

  - `rotations` 设置方向表。方向表是用于每个 `rotation` 的元组列表，用于设置 MADCTL 寄存器、显示器宽度、显示器高度、start_x 和 start_y 值。

    默认的 `rotations` 包括以下 st7789 和 st7735 显示器大小：

Display | Default Orientation Tables
------- | --------------------------
240x320 | [(0x00, 240, 320,  0,  0), (0x60, 320, 240,  0,  0), (0xc0, 240, 320,  0,  0), (0xa0, 320, 240,  0,  0)]
170x320 |	[(0x00, 170, 320, 35, 0), (0x60, 320, 170, 0, 35), (0xc0, 170, 320, 35, 0), (0xa0, 320, 170, 0, 35)]
240x240 | [(0x00, 240, 240,  0,  0), (0x60, 240, 240,  0,  0), (0xc0, 240, 240,  0, 80), (0xa0, 240, 240, 80,  0)]
135x240 | [(0x00, 135, 240, 52, 40), (0x60, 240, 135, 40, 53), (0xc0, 135, 240, 53, 40), (0xa0, 240, 135, 40, 52)]
128x160 | [(0x00, 128, 160,  0,  0), (0x60, 160, 128,  0,  0), (0xc0, 128, 160,  0,  0), (0xa0, 160, 128,  0,  0)]
128x128 | [(0x00, 128, 128,  2,  1), (0x60, 128, 128,  1,  2), (0xc0, 128, 128,  2,  3), (0xa0, 128, 128,  3,  2)]
 other  | [(0x00, width, height, 0, 0)]

Y您可以定义任意数量的旋转。

- `rotation` 根据方向表设置显示旋转。

  默认方向表为具有LCD平移电缆位于显示器底部的240x320、240x240、134x240、128x160和128x128显示器定义了四个逆时针旋转。默认旋转是纵向（0度）。

  | 索引 | 旋转              |
  | ---- | ----------------- |
  | 0    | 纵向（0度）       |
  | 1    | 横向（90度）      |
  | 2    | 反向纵向（180度） |
  | 3    | 反向横向（270度） |

- 默认的方向表为具有LCD平移电缆位于显示器底部的240x320、240x240、134x240、128x160和128x128显示器定义了四个逆时针旋转。默认旋转是纵向（0度）。

- `custom_init` 包含在显示器 `init()` 期间发送到显示器的显示配置命令列表。列表包含带有字节对象的元组，可以选择性地后跟以毫秒为单位指定的延迟。字节对象的第一个字节包含要发送的命令，后面可以跟随数据字节。请参阅 `examples/configs/t_dongle_s3/tft_config.py` 文件或示例。

- `color_order` 设置驱动程序使用的颜色顺序（st7789.RGB 或 st7789.BGR）。

- `inversion` 如果为 True，则设置显示颜色反转模式，如果为 false，则清除显示颜色反转模式。

- `options` 设置驱动程序选项标志。

    | 选项          | 描述                                                         |
    | ------------- | ------------------------------------------------------------ |
    | st7789.WRAP   | 像素、线条、多边形和Hershey文本将在显示器上水平和垂直两侧环绕。 |
    | st7789.WRAP_H | 像素、线条、多边形和Hershey文本将在显示器上水平环绕。        |
    | st7789.WRAP_V | 像素、线条、多边形和Hershey文本将在显示器上垂直环绕。        |

- `buffer_size` 如果没有指定 `buffer_size`，则会创建并根据需要释放动态分配的缓冲区。如果设置了 `buffer_size`，它必须足够大，以包含使用的最大位图、字体字符和解码的JPG图像（行数*列数*2字节，RGB565表示法中的16位颜色）。动态分配速度较慢，可能会导致堆碎片，因此应启用垃圾收集（GC）。

- `inversion_mode(bool)` 如果为 True，则设置显示颜色反转模式，如果为 False，则清除显示颜色反转模式。

- `madctl(value)` 返回MADCTL寄存器的当前值，如果传递了值，则设置MADCTL寄存器。MADCTL寄存器用于设置显示旋转和颜色顺序。

#### [ MADCTL 常量](https://chat.openai.com/c/3124149c-96b6-4fed-bc6e-50c5efe9dbe8#madctl-constants)

| 常量名称         | 值   | 描述               |
| ---------------- | ---- | ------------------ |
| st7789.MADCTL_MY | 0x80 | 页面地址顺序       |
| st7789_MADCTL_MX | 0x40 | 列地址顺序         |
| st7789_MADCTL_MV | 0x20 | 页面/列顺序        |
| st7789_MADCTL_ML | 0x10 | 行地址顺序         |
| st7789_MADCTL_MH | 0x04 | 显示数据锁存器顺序 |
| st7789_RGB       | 0x00 | RGB颜色顺序        |
| st7789_BGR       | 0x08 | BGR颜色顺序        |

#### [MADCTL examples](#madctl-examples)


     Orientation | MADCTL Values for RGB color order, for BGR color order add 0x08 to the value.
     ----------- | ---------------------------------------------------------------------------------
     <img src="https://raw.githubusercontent.com/russhughes/st7789_mpy/master/docs/madctl_0.png" /> | 0x00
     <img src="https://raw.githubusercontent.com/russhughes/st7789_mpy/master/docs/madctl_y.png" /> | 0x80 ( MADCTL_MY )
     <img src="https://raw.githubusercontent.com/russhughes/st7789_mpy/master/docs/madctl_x.png" /> | 0x40 ( MADCTL_MX )
     <img src="https://raw.githubusercontent.com/russhughes/st7789_mpy/master/docs/madctl_xy.png" /> | 0xC0 ( MADCTL_MX + MADCTL_MY )
     <img src="https://raw.githubusercontent.com/russhughes/st7789_mpy/master/docs/madctl_v.png" /> | 0x20 ( MADCTL_MV )
     <img src="https://raw.githubusercontent.com/russhughes/st7789_mpy/master/docs/madctl_vy.png" /> | 0xA0 ( MADCTL_MV + MADCTL_MY )
     <img src="https://raw.githubusercontent.com/russhughes/st7789_mpy/master/docs/madctl_vx.png" /> | 0x60 ( MADCTL_MV + MADCTL_MX )
     <img src="https://raw.githubusercontent.com/russhughes/st7789_mpy/master/docs/madctl_vxy.png" /> | 0xE0 ( MADCTL_MV + MADCTL_MX + MADCTL_MY )

- `init()`

  必须调用以初始化显示。

- `on()`

  打开背光引脚，如果在初始化期间定义了背光引脚。

- `off()`

  关闭背光引脚，如果在初始化期间定义了背光引脚。

- `sleep_mode(value)`

  如果值为 True，则使显示器进入睡眠模式；如果值为 False，则唤醒。在睡眠期间，显示内容可能无法保留。

- `fill(color)`

  用指定的颜色填充显示器。

- `pixel(x, y, color)`

  将指定的像素设置为给定的颜色。

- `line(x0, y0, x1, y1, color)`

  使用提供的颜色从（`x0`，`y0`）到（`x1`，`y1`）绘制一条线。

- `hline(x, y, length, color)`

  使用提供的颜色和长度以像素为单位绘制一条水平线。与 `vline` 一起使用，这是一个具有较少SPI调用的快速版本。

- `vline(x, y, length, color)`

  使用提供的颜色和长度以像素为单位绘制一条垂直线。

- `rect(x, y, width, height, color)`

  从（`x`，`y`）开始绘制一个具有相应尺寸的矩形。

- `fill_rect(x, y, width, height, color)`

  从（`x`，`y`）坐标开始填充一个具有相应尺寸的矩形。

- `circle(x, y, r, color)`

  在给定颜色下，以（`x`，`y`）坐标为中心绘制半径为 `r` 的圆。

- `fill_circle(x, y, r, color)`

  在给定颜色下，以（`x`，`y`）坐标为中心绘制填充的半径为 `r` 的圆。

- `blit_buffer(buffer, x, y, width, height)`

  将 `bytes()` 或 `bytearray()` 的内容复制到屏幕的内部存储器。注意：数组中的每种颜色都需要2字节。

- `text(font, s, x, y[, fg, bg])`

  使用指定的位图 `font` 和坐标作为文本的左上角，将 `s`（整数、字符串或字节）写入显示器。可选参数 `fg` 和 `bg` 可以设置文本的前景和背景颜色；否则，前景颜色默认为 `WHITE`，背景颜色默认为 `BLACK`。有关示例字体，请参见 `fonts/bitmap` 文件夹中的 `README.md`。

- `write(bitmap_font, s, x, y[, fg, bg, background_tuple, fill_flag])`

  使用指定的比例或等宽位图 `font` 模块，以坐标作为文本的左上角，将 `s` 写入显示器。文本的前景和背景颜色可以通过可选的参数 `fg` 和 `bg` 设置，否则前景颜色默认为 `WHITE`，背景颜色默认为 `BLACK`。

  可以通过提供包含（bitmap_buffer，width，height）的 `background_tuple` 来模拟透明度。这与 `jpg_decode` 方法使用的格式相同。有关示例字体，请参见 `truetype/fonts` 文件夹中的 `README.md`。接受UTF8编码的字符串。

  `font2bitmap` 实用程序从等宽或等宽True Type字体创建兼容的每像素1位位图模块。字符大小、前景、背景颜色和位图模块中的字符可以作为参数指定。使用-h选项获取详细信息。如果在显示初始化期间指定了 `buffer_size`，它必须足够大，以容纳最宽字符（HEIGHT * MAX_WIDTH * 2）。

- `write_len(bitap_font, s)`

  返回使用指定字体打印的字符串的宽度（以像素为单位）。

- `draw(vector_font, s, x, y[, fg, scale])`

  使用指定的Hershey矢量字体，在坐标作为文本的左下角，绘制文本。文本的前景颜色可以通过可选参数 `fg` 设置，否则前景颜色默认为 `WHITE`。文本的大小可以通过指定 `scale` 值来缩放。`scale` 值必须大于0，可以是浮点数或整数值。`scale` 值默认为1.0。请参阅 `vector/fonts` 文件夹中的 `README.md` 以获取示例字体和 `utils` 文件夹中的字体转换程序。

- `draw_len(vector_font, s[, scale])`

  如果使用指定字体绘制，返回字符串的宽度（以像素为单位）。

- `jpg(jpg, x, y [, method])`

  使用给定的 `x` 和 `y` 坐标作为图像的左上角，在显示器上绘制 `jpg`。`jpg` 可以是包含文件名的字符串，也可以是包含JPEG图像数据的缓冲区。

  解码和显示JPG所需的内存可能相当可观，因为全屏320x240的JPG至少需要3100字节的工作区 + 缓冲映像的320 * 240 * 2字节的RAM。如果Jpg图像的缓冲区比可用内存大，可以通过传递 `method` 为 `SLOW` 来绘制该图像。`SLOW` `method` 将使用图像的最小编码单元（MCU，通常是8x8的倍数）逐个绘制图像。默认方法是 `FAST`。

- `jpg_decode(jpg_filename [, x, y, width, height])`

  解码JPG文件并将其作为元组返回，由（buffer，width，height）组成。缓冲区是一个与color565 blit_buffer兼容的字节数组。缓冲区将需要width * height * 2字节的内存。

  如果给定了可选的x，y，width和height参数，则缓冲区将仅包含图像的指定区域。有关示例，请参见 `examples/T-DISPLAY/clock/clock.py` 和 `examples/T-DISPLAY/toasters_jpg/toasters_jpg.py`。

- `png(png_filename, x, y [, mask])`

  在显示器上以给定的 `x` 和 `y` 坐标的左上角绘制PNG文件。如果无法完全适应显示器，则会裁剪PNG。PNG将逐行绘制。由于驱动程序不包含帧缓冲区，因此不支持透明度。为 `mask` 参数提供 `True` 值将防止显示具有零alpha通道值的像素。绘制具有蒙版的PNG比无蒙版的绘制更慢，因为每个可见的线段都是分开绘制的。有关使用蒙版的示例，请参见 examples/png 文件夹中的 alien.py 程序。

- `polygon_center(polygon)`

  将多边形的中心作为元组（x，y）返回。多边形应包含形成闭合凸多边形的（x，y）元组的列表。

- `fill_polygon(polygon, x, y, color[, angle, center_x, center_y])`

  在给定的 `x` 和 `y` 坐标以及给定的颜色中绘制填充的 `polygon`。可以围绕 `center_x` 和 `center_y` 点旋转 `angle` 弧度。多边形应包含形成闭合凸多边形的（x，y）元组的列表。

  请参见 TWATCH-2020 的 `watch.py` 演示以获取示例。

- `polygon(polygon, x, y, color, angle, center_x, center_y)`

  在给定的 `x` 和 `y` 坐标以及给定的颜色中绘制 `polygon`。可以围绕 `center_x` 和 `center_y` 点旋转 `angle` 弧度。多边形应包含形成闭合凸多边形的（x，y）元组的列表。

  请参见 T-Display 的 `roids.py` 以获取示例。

- `bounding({status, as_rect})`

  启用或禁用跟踪自上次清除以来写入到的显示区域。最初，跟踪是禁用的；传递 `True` 值将启用跟踪，传递 `False` 将禁用它。传递 `True` 或 `False` 参数将重置当前的边界矩形为（display_width，display_height，0，0）。

  返回包含（min_x，min_y，max_x，max_y）的四个整数元组，指示自上次清除以来写入到的显示区域。如果 `as_rect` 参数为 `True`，返回的元组将包含（min_x，min_y，width，height）值。

  有关示例，请参见 TWATCH-2020 的 `watch.py` 演示。


- `polygon(polygon, x, y, color, angle, center_x, center_y)`

  在给定的 `x` 和 `y` 坐标处，以指定的 `color` 绘制一个 `polygon`。该多边形可以围绕 `center_x` 和 `center_y` 点旋转 `angle` 弧度。 多边形应该由形成封闭凸多边形的 (x, y) 元组列表组成。
  
  有关示例，请参见 T-Display 中的 `roids.py`。
  
- `bounding({status, as_rect})`

   Bounding 用于启用或禁用跟踪自上次清除以来已写入的显示区域。初始情况下，跟踪被禁用；传递 True 以启用跟踪，传递 False 以禁用跟踪。 传递 True 或 False 参数将重置当前的边界矩形为 (display_width, display_height, 0, 0)。

   返回包含四个整数的元组，表示自上次清除以来已写入的显示区域。

   如果 `as_rect` 参数为 True，则返回的元组将包含 (min_x, min_y, width, height) 的值。

   有关示例，请参见 TWATCH-2020 中的 `watch.py` 演示。

- `bitmap(bitmap, x , y [, index])`

  使用指定的 `x`、`y` 坐标作为位图的左上角绘制 `bitmap`。可选的 `index` 参数提供了一种从包含 `bitmap` 模块的多个位图中选择的方法。 `index` 用于使用模块的 HEIGHT、WIDTH 和 BPP 值计算到所需位图开头的偏移量。
  
  `imgtobitmap.py` 实用程序使用 Pillow Python Imaging 库从图像文件创建与位图兼容的每像素 1 到 8 位的位图模块。
  
  `monofont2bitmap.py` 实用程序从等宽 True Type 字体创建与位图兼容的每像素 1 到 8 位的位图模块。 在 `examples/lib` 文件夹中查看 `inconsolata_16.py`、`inconsolata_32.py` 和 `inconsolata_64.py` 文件，了解示例模块， 并在 `mono_font.py` 程序中查看使用生成的模块的示例。

  字符大小、每像素位数、前景、背景颜色以及要包含在位图模块中的字符可以作为参数指定。使用 -h 选项获取详细信息。 大于 1 的每像素位数设置可用于创建抗锯齿字符，但会增加内存使用。如果在显示初始化期间指定了 buffer_size， 则它必须足够大，以容纳一个字符 (HEIGHT * WIDTH * 2)。
  
- `width()`

   返回显示的当前逻辑宽度。（例如，将 135x240 显示旋转 90 度后，宽度为 240 像素）

- `height()`

   返回显示的当前逻辑高度。（例如，将 135x240 显示旋转 90 度后，高度为 135 像素）

- `rotation(r)`

  设置逻辑显示在逆时针方向旋转。0-纵向（0度），1-横向（90度），2-反向纵向（180度），3-反向横向（270度）
  
- `offset(x_start, y_start)` ST7789 控制器的内存配置为 240x320 显示。当使用较小的显示器，如 240x240 或 135x240 时，需要向 x 和 y 参数添加偏移量，以便像素被写入对应于可见显示的内存区域。在旋转显示时，可能需要调整这些偏移量。

   例如，TTGO-TDisplay 是 135x240，使用以下偏移量。

   | 旋转 | x_start | y_start |
   | ---- | ------- | ------- |
   | 0    | 52      | 40      |
   | 1    | 40      | 53      |
   | 2    | 53      | 40      |
   | 3    | 40      | 52      |

## 滚动

st7789 显示控制器包含一个用于存储显示像素的 240x320 像素帧缓冲区。对于滚动，帧缓冲区包含三个单独的区域：(`tfa`) 顶部固定区域，(`height`) 滚动区域和 (`bfa`) 底部固定区域。`tfa` 是不滚动的像素的上部分。`height` 是帧缓冲区的中间部分，用于滚动。`bfa` 是帧缓冲区的下部分，其中的像素不滚动。这些值控制了滚动整个显示区域或部分显示区域的能力。

对于高度为 320 像素的显示器，将 `tfa` 设置为 0，`height` 设置为 320，`bfa` 设置为 0 将允许滚动整个显示器。您可以将 `tfa` 和 `bfa` 设置为非零值以滚动显示的部分。`tfa` + `height` + `bfa` 应等于 320，否则滚动模式将未定义。

高度小于 320 像素的显示器，`tfa`、`height` 和 `bfa` 将需要进行调整以补偿较小的 LCD 面板。实际值将根据 LCD 面板的配置而变化。例如，滚动整个 135x240 TTGO T-Display 需要 `tfa` 值为 40，`height` 值为 240，`bfa` 值为 40（40+240+40=320），因为 T-Display LCD 显示从帧缓冲区的第 40 行开始的 240 行，留下了未显示的最后 40 行。

其他显示器，如 Waveshare Pico LCD 1.3 英寸 240x240 显示器，则需要将 `tfa` 设置为 0，`height` 设置为 240，`bfa` 设置为 80（0+240+80=320）以滚动整个显示器。Pico LCD 1.3 从帧缓冲区的第 0 行开始显示 240 行，留下了未显示的最后 80 行。

`vscsad` 方法设置 (VSSA) 垂直滚动起始地址。VSSA 设置帧缓冲区中将成为 `tfa` 之后的第一行的行。

    ST7789 datasheet中的警告指出：
    
    垂直滚动起始地址（VSSA）的数值是绝对的（相对于帧内存），它不应进入由垂直滚动定义确定的固定区域，否则在面板上将显示不期望的图像。
    简而言之，这个警告是在建议设置垂直滚动起始地址（VSSA）时，确保指定的值不落在固定区域内，比如顶部固定区域（tfa）和底部固定区域（bfa）。如果VSSA值进入这些固定区域，可能导致面板上显示不期望的图像。因此，在设置VSSA时，需要仔细考虑tfa、height和bfa的值，确保滚动仅发生在帧内存的预期可滚动区域内，以避免滚动时出现意外的图像问题。

- `vscrdef(tfa, height, bfa)`：设置垂直滚动参数。
  - `tfa` 是顶部固定区域的像素数。顶部固定区域是显示帧缓冲区的上部分，不会被滚动。
  - `height` 是要滚动的区域的总高度（以像素为单位）。
  - `bfa` 是底部固定区域的像素数。底部固定区域是显示帧缓冲区的下部分，不会被滚动。
- `vscsad(vssa)`：设置垂直滚动地址。
  - `vssa` 是垂直滚动起始地址，以像素为单位。垂直滚动起始地址是帧缓冲区中将在TFA之后显示的第一行。

-  辅助函数

  - `color565(r, g, b)`
  
    将颜色打包成2字节的RGB565格式。
  
  - `map_bitarray_to_rgb565(bitarray, buffer, width, color=WHITE, bg_color=BLACK)`
  
    将`bitarray`转换为适合进行blit的RGB565颜色`buffer`。`bitarray`中的位1表示一个带有`color`的像素，而位0表示带有`bg_color`的像素。
