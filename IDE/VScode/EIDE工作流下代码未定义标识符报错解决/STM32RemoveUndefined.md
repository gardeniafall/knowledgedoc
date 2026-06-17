# EIDE工作流下代码未定义标识符报错解决

在代码可以编译的情况下，编译器对`uint8_t`、`uint16_t`、`GPIOX`报错的原因一般是`C/C++`拓展中的IntelliSense配置编译器默认为`cl.exe`以及未包含所需宏定义导致的。因此针对于此问题修正即可。

### 新建C/C++拓展配置文件

在VScode中使用`Shift + Ctrl + P`快捷键，搜索`>C/C++:编辑配置(UI)`，进入`C/C++`拓展的图形化配置界面，在`配置名称`处选择`添加配置`，添加一个新的`C/C++`拓展配置集。

### 指定编译器路径

接下来以`AC6`编译器为例。`AC6`编译器的路径一般位于`Keil`的 安装路径下，例如:

~~~~~~address
D:\Keil_v5\ARM\ARMCLANG\bin\armclang.exe
~~~~~~

### 指定IntelliSense模式

由于`AC6`编译器采用`ARM-CLANG`，故选择`windows-clang-arm`。

### 指定包含路径

由于默认不会递归搜索指定的路径文件夹下的所有项目，因此需指定其递归搜索：

~~~javascript
${workspaceFolder}/**
~~~

### 指定宏定义

由于`Keil`文件通常会使用宏定义，这里需要进入`Keil`中确认使用的宏定义。在`Keil`工程中选择魔术棒工具，在`C/C++`选项卡中查看已经使用的宏定义，如：

~~~define
USE_PWR_LDO_SUPPLY,USE_HAL_DRIVER,STM32H743xx
~~~

在填入设置时，须在每个逗号处拆开，如：

~~~define
USE_PWR_LDO_SUPPLY
USE_HAL_DRIVER
STM32H743xx
~~~

若使用了微库，需额外定义：

~~~
__MICROLIB
~~~

### 指定C/C++标准

同样在`Keil`工程的`C/C++`选项卡下，`Language/Code Generation`中可以检查所使用的`C/C++`标准，如`C:C99`，`C++:C++11`。

