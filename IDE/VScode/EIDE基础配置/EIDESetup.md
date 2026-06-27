# EIDE基础配置

## 安装拓展

默认已经安装了`C/C++`与`C/C++ Extension Pack`。在`VScode`拓展中添加`Embedded IDE`拓展与`Cortex-Debug`拓展，由于该拓展基于`.NET`运行，安装时需同时安装`.NET`框架。重新启动`VScode`。

## 安装工具

选择边栏处`EIDE`图标打开`EIDE`设置，在`操作`栏选择`安装实用工具`，安装以下工具：

`Cppcheck`：实现代码检查。

`GNU Arm Embedded Toolchain`：嵌入式编译工具链。

`OpenOCD Programmer`：开源烧录工具。

`Jlink`：Jlink烧录工具。

`STM32 Cube Programmer CLI`：STM32配置工具。

## 设置拓展

选择边栏处`EIDE`图标打开`EIDE`设置，在`操作`栏选择`打开插件设置`，设置编译器路径（在较新的`Keil`版本下，可能只有`AC6`编译器，若需`AC5`编译器需手动安装）：

`AC5编译器`：在`EIDE.ARM.ARMCC5: Install Directory`（Armcc v5 toolchain 安装目录）中，填写`Keil`下的编译器路径，填写至`ARMCC`（包含ARMCC）为止即可。例如`D:\Keil\keilcore\Keil_v5\ARM\ARMCC`。

`AC6编译器`：在`EIDE.ARM.ARMCC6: Install Directory`（Armcc v6 toolchain 安装目录）中，填写`Keil`下的编译器路径，填写至`ARMCLANG`（包含ARMCLANG）为止即可。例如`D:\Keil\keilcore\Keil_v5\ARM\ARMCLANG`。

另外，`EIDE`拥有单独配置的MCP服务器，用以加强与`Vibe`工具的连接，可根据需求开启。

