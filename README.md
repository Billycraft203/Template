# STM32F103 学习工程模板（Keil MDK）

> 一块“打开即可编译”的 STM32 工程基线：省去从零建工程、配头文件搜索路径、设预处理宏的重复劳动，把时间留给写代码。

![MCU](https://img.shields.io/badge/MCU-STM32F103ZE-1f6feb?style=flat-square)
![IDE](https://img.shields.io/badge/IDE-Keil%20MDK%205-1f6feb?style=flat-square)
![Toolchain](https://img.shields.io/badge/Toolchain-ARMCC%20V5-1f6feb?style=flat-square)
![Build](https://img.shields.io/badge/Build-0%20Error%200%20Warning-2ea44f?style=flat-square)

## 目录

- [STM32F103 学习工程模板（Keil MDK）](#stm32f103-学习工程模板keil-mdk)
  - [目录](#目录)
  - [项目简介](#项目简介)
  - [环境要求](#环境要求)
  - [快速开始](#快速开始)
    - [命令行构建（可选）](#命令行构建可选)
  - [目录结构](#目录结构)
  - [已验证的编译状态](#已验证的编译状态)
  - [编辑器代码提示（可选）](#编辑器代码提示可选)
  - [作为模板使用](#作为模板使用)
    - [每次练习：克隆到新目录](#每次练习克隆到新目录)
    - [正式项目：以模板为起点开新仓库](#正式项目以模板为起点开新仓库)
    - [模板有改进时，同步给已有练习](#模板有改进时同步给已有练习)
    - [维护约定](#维护约定)
  - [板上外设引脚速查](#板上外设引脚速查)
  - [常见问题](#常见问题)
  - [许可与第三方组件](#许可与第三方组件)
  - [作者](#作者)

## 项目简介

本仓库是一个用于 **STM32 入门与嵌入式方向练习** 的 Keil MDK 工程模板，基于 ST 官方的标准外设库（StdPeriph Lib V3.6.0）搭建，硬件参考平台为 **正点原子（ALIENTEK）战舰 V3 开发板（STM32F103ZET6）**。

设计目标是“干净的基线”：

- 已经配好器件、启动文件、库文件、头文件搜索路径与预处理宏，**克隆下来第一次编译就是 0 Error 0 Warning**；
- **不含** ST 官方的 STM32_EVAL 评估板代码（`UTIL` 目录），也不依赖任何开发板专用驱动，因此不会出现“先选评估板”之类的编译报错；
- 练习代码不进本仓库——这里只维护模板本身，练习各自独立成库（见 [作为模板使用](#作为模板使用)）。

适用人群：正在学 STM32 标准库、希望跳过“建工程/配路径”环节，直接写外设代码的同学。若使用其他型号的 F103 板子，请参考 [常见问题](#常见问题) 里的器件与宏修改说明。

## 环境要求

| 项 | 要求 |
| --- | --- |
| 操作系统 | Windows 10 / 11 |
| IDE | Keil MDK 5（含 **ARM Compiler 5**，即 ARMCC） |
| 器件支持包 | `Keil.STM32F1xx_DFP`（Keil Pack Installer 中安装） |
| MCU | STM32F103ZET6（LQFP144，high density） |
| 调试器 | ST-Link / J-Link 均可，推荐 **SWD** 两线接法 |
| 可选编辑器 | Trae / VS Code + C/C++ 扩展（仅用于阅读、跳转、补全，不用于编译） |

> 使用其他型号时至少要改三处：Keil 里的 **Device** 选择、启动文件（`hd` / `md` / `ld`）、预处理宏（`STM32F10X_HD` / `MD` / `LD`）。

## 快速开始

1. **克隆到本地**

   ```bash
   git clone https://github.com/Billycraft203/stm32f103-keil-template.git
   ```

   也可以直接在 GitHub 页面点 **Use this template**（若已开启 Template repository），或下载 ZIP。

2. **用 Keil 打开工程**

   ```
   USER/Template.uvprojx
   ```

3. **确认工程设置**（一般已是模板预置值，无需改动）

   | 位置 | 应为 |
   | --- | --- |
   | Options for Target → Device | `STM32F103ZE` |
   | Options for Target → C/C++ → Define | `USE_STDPERIPH_DRIVER` |
   | Options for Target → C/C++ → Include Paths | `..\CMSIS;..\FWLlib\inc;..\USER` |
   | Options for Target → Debug | 任选你的仿真器，模式选 **SWD** |

4. **编译**：按 `F7`（Build）或 `Rebuild`。首次构建会自动创建 `OBJ/` 输出目录，结果应为 `0 Error(s), 0 Warning(s)`。

5. **下载运行**：仿真器接 `PA13(SWDIO)` / `PA14(SWCLK)` / `GND` / `3V3`，确认板上 **BOOT0 = 0**（从 Flash 启动），然后下载。

6. **开始写代码**：从 `USER/main.c` 起步。板上 LED、蜂鸣器、按键的引脚见 [板上外设引脚速查](#板上外设引脚速查)。

### 命令行构建（可选）

不想开 IDE 时，可以用 Keil 自带命令行做一次全量构建并拿到日志：

```powershell
# 请按本机实际安装位置调整 UV4.exe 路径
Start-Process -FilePath "C:\Keil_v5\UV4\UV4.exe" `
  -ArgumentList '-r','USER\Template.uvprojx','-o','build.log' -Wait
```

`-b` 为增量构建，`-r` 为全量重建；退出码 `0` 表示无错误无警告，`1` 表示有警告，`2` 表示有错误。

## 目录结构

```
Template/
├── USER/                  工程文件与用户代码
│   ├── Template.uvprojx   ← 用 Keil 打开这个
│   ├── main.c             用户程序入口（模板中为空 main）
│   ├── stm32f10x_conf.h   库裁剪配置（决定哪些外设驱动被编译）
│   ├── stm32f10x_it.c     中断服务函数
│   └── DebugConfig/       仿真器调试配置
├── CMSIS/                 内核与启动
│   ├── stm32f10x.h        寄存器定义、外设结构体与位定义
│   ├── system_stm32f10x.c 时钟配置（SystemInit）
│   ├── core_cm3.c/.h      Cortex-M3 内核支持
│   └── startup_stm32f10x_hd.s  启动文件（high density）
├── FWLlib/
│   ├── inc/               标准外设库头文件
│   └── src/               标准外设库源码
├── BSP/                   预留：板级公共代码（引脚宏、延时、GPIO 封装等）
├── .vscode/               编辑器代码提示配置（不参与编译）
├── OBJ/                   编译输出（已被 .gitignore 排除）
├── .gitignore
├── .gitattributes         换行符约定
└── README.md
```

| 目录 | 说明 |
| --- | --- |
| `USER/` | 你的代码写在这里；工程文件也在这里 |
| `CMSIS/` | 尽量不改。器件型号相关的宏由 `stm32f10x.h` 决定 |
| `FWLlib/` | ST 官方库，保持不变；要裁剪驱动改 `USER/stm32f10x_conf.h` 更合适 |
| `BSP/` | 练习中反复出现的公共代码（引脚定义、延时、GPIO 初始化封装）建议沉淀到这里 |
| `OBJ/` | 编译产物，不进版本库；删除后重新编译即可重建 |

## 已验证的编译状态

| 项 | 值 |
| --- | --- |
| 编译器 | ARMCC V5（ARM Compiler 5） |
| 标准外设库 | STM32F10x_StdPeriph_Lib **V3.6.0** |
| 预处理宏 | `USE_STDPERIPH_DRIVER` |
| 头文件搜索路径 | `..\CMSIS;..\FWLlib\inc;..\USER` |
| 启动文件 | `..\CMSIS\startup_stm32f10x_hd.s` |
| 最近一次验证 | `Rebuild All` → `0 Error(s), 0 Warning(s)`；`Code=676  RO-data=320  RW-data=0  ZI-data=1632` |

> **本模板不含 STM32_EVAL 评估板代码**。因此工程里没有定义 `USE_STM3210E_EVAL`，请不要把 `stm32_eval.h` 加回来——否则会立刻撞上 `#error "Please select first the STM32 EVAL board to be used"`。
>
> 如果你改动了 `Define` 或 `Include Paths`，请在 Keil 里做一次 **Rebuild All**，确认 `0 Error 0 Warning` 后再更新本表的数字。

## 编辑器代码提示（可选）

用 Trae / VS Code 阅读代码时，代码补全、跳转、悬停提示依赖 `.vscode/c_cpp_properties.json`，它**只影响编辑器解析，不影响 Keil 编译**。该文件已随仓库提供，若你的环境与作者不同，需要改以下三处：

```jsonc
{
  "includePath": [
    // ...
    "C:/Keil_v5/ARM/ARMCLANG/include"        // ① ARMCLANG 的标准头目录
  ],
  "compilerPath": "C:/Keil_v5/ARM/ARMCLANG/bin/armclang.exe",  // ② 编译器可执行文件
  "compilerArgs": ["--target=arm-arm-none-eabi", "-mcpu=cortex-m3"]  // ③ 目标架构
}
```

几点说明：

- Keil 默认安装在 `C:\Keil_v5`；装在别处就把上面两处路径换成你本机的位置（JSON 里请用正斜杠 `/`）。
- 编辑器里用的是 **armclang（ARM Compiler 6）** 来解析，而工程实际用 **ARMCC V5** 编译。两者 C 标准库头一致，仅作代码提示用，无影响；若你只装了 AC5，可以把这两处换成 `C:/Keil_v5/ARM/ARMCC/include`。
- 工作区根目录请打开到 `Template/` 这一层（不是它的上级目录），否则 `${workspaceFolder}` 变量会指错位置。
- 配置改完后执行 `Ctrl+Shift+P` → `C/C++: Rescan Workspace`；若提示 `无法打开源文件 "stdint.h"`，就是上面①的路径不对。

## 作为模板使用

### 每次练习：克隆到新目录

```bash
git clone https://github.com/Billycraft203/stm32f103-keil-template.git practice-01-blink
cd practice-01-blink
git remote remove origin    # 断开与原模板仓库的联系，避免误 push
```

`remote remove origin` 这一步别省：否则 `origin` 仍指向模板仓库，哪天顺手 `git push` 可能把练习内容推回去。

### 正式项目：以模板为起点开新仓库

把本仓库在 GitHub 上勾选 **Template repository**，之后：

```bash
gh repo create <项目名> --template Billycraft203/stm32f103-keil-template --private --clone
```

它会建好带独立历史与远程的新仓库，不需要再 `git init`。

### 模板有改进时，同步给已有练习

```bash
git -C <练习目录> pull https://github.com/Billycraft203/stm32f103-keil-template.git main
```

### 维护约定

1. **模板保持纯净**：练习代码只留在练习仓库，不要合并回本仓库。
2. **模板级改进欢迎回流**——头文件路径、宏定义、Keil 选项、`BSP/` 里的公共代码。发现这类问题欢迎提 Issue 或 Pull Request。
3. 提交前用 `git status` 确认 `OBJ/`、`Listings/`、`*.uvoptx` 没有被纳入（它们已在 `.gitignore` 里）。

## 板上外设引脚速查

以下为战舰 V3（STM32F103ZET6）常用外设与引脚对应关系，方便上手时对照：

| 外设 | 引脚（封装脚号） | 有效电平 / 说明 |
| --- | --- | --- |
| DS0 红色 LED | PB5（135） | **低电平**点亮（3.3V → 510Ω → LED → 引脚） |
| DS1 绿色 LED | PE5（4） | **低电平**点亮 |
| 蜂鸣器 BEEP（有源，S8050 驱动） | PB8（139） | **高电平**导通；经三极管驱动，不建议兼作他用 |
| 按键 KEY0 / KEY1 / WK_UP | PE4 / PE3 / PA0 | 按下时电平翻转 |
| 24C02（I2C） | PB6（SCL）/ PB7（SDA） | 板上已有 4.7K 上拉 |
| SWD 调试 | PA13（105）/ PA14（109） | SWDIO / SWCLK |
| 主晶振 / RTC 晶振 | Y2 8MHz / PC14、PC15 接 32.768kHz | HSE 经 PLL 倍频至 72MHz |
| 电源指示灯 PWR（蓝） | 无 IO | 上电常亮，不受程序控制 |

> 引脚信息整理自战舰 V3 开发板原理图与 IO 分配表，仅供参考；请以手上板子与官方资料为准。

## 常见问题

**Q1：编译报 `#error "Please select first the STM32 EVAL board to be used"`？**

本模板已移除 STM32_EVAL 评估板代码。请检查是否把 `stm32_eval.h` / `UTIL` 目录加回了工程；并确认 `Options → C/C++ → Define` 里**没有** `USE_STM3210E_EVAL`。

**Q2：编辑器提示 `无法打开源文件 "stdint.h"`（dependency of `stm32f10x.h`）？**

`stdint.h` 是编译器自带的标准头，不在工程里。按 [编辑器代码提示](#编辑器代码提示可选) 一节，把 `includePath` / `compilerPath` 里的 Keil 路径改成你本机的位置即可。

**Q3：Keil 里中文注释乱码，或 GitHub 网页上中文显示成乱码？**

源文件是 **GB2312** 编码而浏览端按 UTF-8 解读就会乱码。建议统一成 UTF-8：Keil → `Edit` → `Configuration` → `Encoding` → 选 `UTF-8`，新建文件默认即 UTF-8。

**Q4：`OBJ/` 或 `Template.uvoptx` 出现在 git 变更里？**

它们本应被 `.gitignore` 排除。若之前误提交过，先 `git rm -r --cached OBJ`（`.uvoptx` 同理）再提交；文件仍留在本地磁盘上。`.uvoptx` 是 Keil 的本地状态（窗口布局、断点），不应进版本库。

**Q5：换成自己的 F103 板子后编译报错或跑不起来？**

按顺序检查三处：

1. Keil 里的 **Device** 是否改成你的具体型号；
2. 启动文件是否匹配容量等级（`startup_stm32f10x_hd.s` / `_md.s` / `_ld.s`，对应大/中/小容量）；
3. `Options → C/C++ → Define` 是否改成对应的 `STM32F10X_HD` / `STM32F10X_MD` / `STM32F10X_LD`（模板默认 `HD`）。

**Q6：下载成功但程序不运行？**

确认 **BOOT0 = 0**（从主 Flash 启动）、SWD 四根线接好、下载后按一次复位。若曾把 `PA13/PA14` 或 `PA15/PB3/PB4` 用作普通 IO，注意 JTAG/SWD 的复用问题——把 SWD 也禁掉后就无法再连仿真器，只能用串口 ISP 救回。

## 许可与第三方组件

- 本仓库自身内容（工程配置、`BSP/`、脚本、文档）：可自由用于学习与二次开发。
- `CMSIS/` 来自 ARM（Cortex-M 内核文件与启动文件）；`FWLlib/` 来自 ST 的 **STM32F10x 标准外设库 V3.6.0**。版权归各自所有者，使用与分发请遵守源文件头部随附的许可声明。
- 板上引脚信息整理自正点原子战舰 V3 开发板公开资料，版权归原厂所有。
- 本模板按“原样”提供，仅用于学习目的，不对使用过程中造成的任何硬件损坏或数据丢失负责。

## 作者

- 张陈星（[@Billycraft203](https://github.com/Billycraft203)）
- 个人网站：[克拉夫特空间](https://www.shijie-smartline.club/)

欢迎同学 fork 使用；模板层面的问题或改进建议，欢迎提 Issue 或 Pull Request。
