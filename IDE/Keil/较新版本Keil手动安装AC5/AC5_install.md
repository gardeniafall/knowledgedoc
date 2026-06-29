# 较新版本Keil手动安装AC5

## 获取安装包

进入`ARM`官方网站

```url
https://developer.arm.com/Tools%20and%20Software/Arm%20Compiler%20for%20Embedded
```

选择`Download Lagacy Versions`，向下翻页至`Guidelines for old projects`，选择`[Arm Compiler 5.06 update 7 (build 960)](https://developer.arm.com/downloads/view/ACOMP5) `，下载`AC5`安装包。

## 安装AC5

在`Keil`安装目录下，找到`ARM`文件夹，在其中新建用于安装`AC5`的安装目录，建议为`ARMCC`。**必须安装在此目录下**，否则将会报错找不到`licence`。运行安装程序，直接安装即可。

## 在Keil中配置AC5

进入`Keil`，选择魔术棒工具右侧的三个矩形，进入设置项。切换至`Folders/Extensions`选项卡。点击`Use Arm Complier`处最后的三个点，选择`Add another ARM Compiler Version to List`，选择刚刚安装的编译器路径，点击确定，点击OK。即可在魔术棒工具中选择`AC5`编译器。

