# STM32F103 工程模板（战舰 V3 / STM32F103ZET6）

Keil MDK 工程模板，作为各类练习与正式项目的起手基线。**本仓库只承载模板，不承载练习代码。**

## 一、已验证的可编译状态

| 项 | 值 |
| --- | --- |
| 目标器件 | STM32F103ZE（LQFP144，high density） |
| 编译器 | ARMCC V5（AC5） |
| 标准外设库 | STM32F10x_StdPeriph_Lib V3.6.0 |
| 预处理宏 | `USE_STDPERIPH_DRIVER` |
| 头文件搜索路径 | `..\CMSIS;..\FWLlib\inc;..\USER` |
| 启动文件 | `..\CMSIS\startup_stm32f10x_hd.s` |
| 最近一次验证 | Rebuild All：`0 Error(s), 0 Warning(s)`，`Code=676  RO-data=320  RW-data=0  ZI-data=1632` |

> 注意：本模板**不含** ST 官方的 STM32_EVAL 板级代码（`UTIL` 目录已移除）。因此没有定义 `USE_STM3210E_EVAL`，也不要把 `stm32_eval.h` 加回来——否则会立刻撞上 `#error "Please select first the STM32 EVAL board..."`。
>
> 改动 Define 或 IncludePath 之后，请在 Keil 里 **Rebuild All** 再确认一次 `0 Error 0 Warning`，然后更新上表的数字。

## 二、目录结构

| 目录 | 内容 |
| --- | --- |
| `USER/` | `Template.uvprojx` 工程文件、`main.c`（当前是空的 `main()`）、`stm32f10x_it.c`（中断服务函数） |
| `CMSIS/` | 内核文件、`system_stm32f10x.c`（时钟配置）、启动文件、`stm32f10x.h` |
| `FWLlib/` | 标准外设库：`inc/` 头文件、`src/` 源码 |
| `BSP/` | 预留：板级公共代码（引脚宏、延时、GPIO 封装等），练到重复时往里沉淀 |
| `OBJ/` | 编译输出，**已被 `.gitignore` 排除，不进版本库** |

## 三、怎么用这个模板

### 每次练习：克隆到新目录，并断掉 origin

```powershell
git clone "<本模板仓库路径>" "<新练习目录>"
git -C "<新练习目录>" remote remove origin
```

`remote remove origin` 这一步别省：否则 `origin` 指向模板仓库，哪天顺手 `git push` 就可能把练习内容推回来。

本地克隆默认走硬链接，很快；`OBJ/` 不会跟过来，Keil 首次构建时会自动创建。

### 正式项目：走 GitHub Template repository

把本仓库推到 GitHub 并勾选 **Template repository**，之后：

```powershell
gh repo create <项目名> --template <账号>/<模板仓库名> --private --clone
```

它会直接建好带首次提交历史和独立远程的仓库，不需要再 `git init`。

### 模板有改进时，怎么同步给已有练习

```powershell
git -C "<练习目录>" pull "<本模板仓库路径>" main
```

## 四、维护约定

1. **模板保持纯净**：练习代码只留在练习仓库，不要往这里合并练习代码。
2. **模板级改进必须回流到这里**——头文件路径、宏定义、Keil 选项、`BSP/` 里的公共代码。不回流的代价就是下一个练习重新踩坑。
3. 提交前用 `git status` 确认 `OBJ/`、`Listings/`、`*.uvoptx` 都没被纳入（它们已在 `.gitignore` 中）。

## 五、板上外设引脚速查（战舰 V3）

| 外设 | 引脚（封装脚号） | 有效电平 |
| --- | --- | --- |
| DS0 红色 LED | PB5（135） | 低电平点亮 |
| DS1 绿色 LED | PE5（4） | 低电平点亮 |
| 蜂鸣器 BEEP（有源，S8050 驱动） | PB8（139） | 高电平导通 |
| 按键 KEY0 / KEY1 / WK_UP | PE4 / PE3 / PA0 | 按下时电平翻转 |
| 24C02 的 IIC | PB6 / PB7 | 板上已有 4.7K 上拉 |
| SWD 调试 | PA13（105）/ PA14（109） | — |
| RTC 晶振 / 主晶振 | PC14 / PC15（32.768kHz）、Y2（8MHz） | — |
