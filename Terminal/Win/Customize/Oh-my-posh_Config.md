# Windows 下配置 Oh My Posh

Oh My Posh 是一个跨 Shell 的提示符渲染器。本篇以 **Windows Terminal + PowerShell** 为例，完成安装、字体配置、主题启用、个性化和故障排查。

> 文中的命令适用于 PowerShell。若在 WSL 中使用 Oh My Posh，应进入 WSL 后按 Linux 方式单独安装，不要直接复用 Windows 版本。

## 一、准备环境

建议准备以下组件：

- Windows Terminal
- PowerShell 7（推荐，命令为 `pwsh`；系统自带的 Windows PowerShell 5.1 也可以使用）
- `winget`

检查当前环境：

```powershell
$PSVersionTable.PSVersion
winget --version
```

如果没有 `winget`，可从 Microsoft Store 安装或更新“应用安装程序（App Installer）”。

## 二、安装 Oh My Posh

使用官方推荐的 `winget` 包：

```powershell
winget install JanDeDobbeleer.OhMyPosh --source winget
```

安装后关闭并重新打开终端，让 `PATH` 和相关环境变量生效，然后验证：

```powershell
oh-my-posh version
Get-Command oh-my-posh
```

## 三、安装并启用 Nerd Font

Oh My Posh 主题通常包含图标和 Powerline 符号，普通字体会显示方框或乱码。安装一个 Nerd Font，例如 Meslo：

```powershell
oh-my-posh font install meslo
```

然后在 Windows Terminal 中设置字体：

1. 打开“设置”。
2. 进入“配置文件” -> “默认值” -> “外观”。
3. 将“字体”设置为 `MesloLGM Nerd Font`。
4. 保存设置并新建一个终端标签页。

也可以直接编辑 Windows Terminal 的 `settings.json`，在 `profiles.defaults` 中加入：

```json
{
  "profiles": {
    "defaults": {
      "font": {
        "face": "MesloLGM Nerd Font"
      }
    }
  }
}
```

若在 VS Code 集成终端中使用，还需在 VS Code 的 `settings.json` 中配置：

```json
{
  "terminal.integrated.fontFamily": "MesloLGM Nerd Font"
}
```

## 四、临时预览主题

`winget` 安装通常会同时设置 `$env:POSH_THEMES_PATH`，其中包含内置主题。先确认主题目录：

```powershell
$env:POSH_THEMES_PATH
Get-ChildItem -Path $env:POSH_THEMES_PATH -Filter *.omp.json |
    Select-Object -ExpandProperty BaseName
```

在当前 PowerShell 会话中预览主题：

```powershell
$theme = Join-Path $env:POSH_THEMES_PATH 'jandedobbeleer.omp.json'
oh-my-posh init pwsh --config $theme | Invoke-Expression
```

这一步只影响当前窗口。关闭窗口后会恢复原提示符，适合先挑选主题。

## 五、写入 PowerShell 配置文件

PowerShell 启动时会执行 `$PROFILE`。先查看实际路径并在文件不存在时创建它：

```powershell
$PROFILE

if (-not (Test-Path -LiteralPath $PROFILE)) {
    New-Item -ItemType File -Path $PROFILE -Force | Out-Null
}

notepad $PROFILE
```

将以下内容添加到 `$PROFILE`：

```powershell
$theme = Join-Path $env:POSH_THEMES_PATH 'jandedobbeleer.omp.json'
oh-my-posh init pwsh --config $theme | Invoke-Expression
```

保存后重新打开 PowerShell，或在当前会话立即加载：

```powershell
. $PROFILE
```

> Windows PowerShell 5.1 与 PowerShell 7 的 `$PROFILE` 路径通常不同。在哪个 Shell 中执行 `$PROFILE`，修改的就是哪个 Shell 的配置文件。

### 最简配置

如果不指定主题，可以让 Oh My Posh 使用默认配置：

```powershell
oh-my-posh init pwsh | Invoke-Expression
```

## 六、保存并修改自己的主题

不要直接修改 `$env:POSH_THEMES_PATH` 中的文件，软件升级时这些文件可能被覆盖。把选中的主题复制到用户配置目录：

```powershell
$configDir = Join-Path $HOME '.config\oh-my-posh'
New-Item -ItemType Directory -Path $configDir -Force | Out-Null

$source = Join-Path $env:POSH_THEMES_PATH 'jandedobbeleer.omp.json'
$target = Join-Path $configDir 'mytheme.omp.json'
Copy-Item -LiteralPath $source -Destination $target
notepad $target
```

再把 `$PROFILE` 中的初始化语句改为：

```powershell
$theme = Join-Path $HOME '.config\oh-my-posh\mytheme.omp.json'
oh-my-posh init pwsh --config $theme | Invoke-Expression
```

修改主题后，先验证 JSON 语法，再重新加载配置：

```powershell
$theme = Join-Path $HOME '.config\oh-my-posh\mytheme.omp.json'
Get-Content -LiteralPath $theme -Raw | ConvertFrom-Json | Out-Null
. $PROFILE
```

主题文件的主要结构如下：

```json
{
  "$schema": "https://raw.githubusercontent.com/JanDeDobbeleer/oh-my-posh/main/themes/schema.json",
  "version": 3,
  "final_space": true,
  "blocks": [
    {
      "type": "prompt",
      "alignment": "left",
      "segments": [
        {
          "type": "path",
          "style": "powerline",
          "foreground": "#ffffff",
          "background": "#0077c2",
          "template": " {{ .Path }} ",
          "properties": {
            "style": "folder"
          }
        },
        {
          "type": "git",
          "style": "powerline",
          "foreground": "#193549",
          "background": "#fffb38",
          "template": " {{ .HEAD }}{{ if .Working.Changed }} ●{{ end }} ",
          "properties": {
            "fetch_status": true
          }
        }
      ]
    }
  ]
}
```

常用字段：

| 字段 | 作用 |
| --- | --- |
| `blocks` | 提示符块列表，可分别控制左侧、右侧和换行提示符 |
| `segments` | 一个块中要显示的片段，例如路径、Git、执行时间和退出码 |
| `type` | 片段类型，例如 `path`、`git`、`status`、`executiontime` |
| `style` | 显示样式，例如 `plain`、`powerline`、`diamond` |
| `template` | 使用 Go template 语法定义显示内容 |
| `foreground` / `background` | 前景色和背景色，支持十六进制颜色 |
| `properties` | 当前片段的专用行为配置 |

完整字段和片段说明以官方配置文档为准，不同片段支持的 `properties` 并不相同。

## 七、更新与卸载

更新：

```powershell
winget upgrade JanDeDobbeleer.OhMyPosh --source winget
```

卸载：

```powershell
winget uninstall JanDeDobbeleer.OhMyPosh
```

卸载程序不会自动删除手动加入 `$PROFILE` 的初始化语句。卸载后应同时移除对应语句，避免每次启动 PowerShell 都报错。

## 八、常见问题

### 1. `oh-my-posh` 无法识别或无法运行

先重新打开 Windows Terminal，再检查命令解析结果和安装状态：

```powershell
Get-Command oh-my-posh -All
where.exe oh-my-posh
winget list --id JanDeDobbeleer.OhMyPosh
```

如果 `Get-Command` 只指向 `WindowsApps` 中无法执行的占位程序，或者 `winget list` 中没有该包，可重新安装：

```powershell
winget install JanDeDobbeleer.OhMyPosh --source winget --force
```

### 2. 图标显示为方框、问号或错位

- 确认安装的是 Nerd Font，而不是名称相近的普通字体。
- 确认字体配置在当前 Windows Terminal 配置文件或“默认值”中。
- VS Code、Windows Terminal 和其他终端需要分别设置字体。
- 设置完成后新建标签页；已经打开的标签页可能不会立即更新字体。

### 3. 新开终端后主题没有生效

确认当前 Shell 和配置文件：

```powershell
$PSVersionTable.PSEdition
$PROFILE
Test-Path -LiteralPath $PROFILE
Get-Content -LiteralPath $PROFILE
```

Windows Terminal 的配置文件如果启动的是 `powershell.exe`，就不会自动读取 PowerShell 7（`pwsh.exe`）的 `$PROFILE`，反之亦然。

### 4. 提示“无法加载配置文件，因为系统禁止运行脚本”

查看执行策略：

```powershell
Get-ExecutionPolicy -List
```

通常只需为当前用户启用本地脚本，不要修改整个系统或使用 `Unrestricted`：

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

如果设备由组织策略管理，应遵循管理员策略，不要强行绕过。

### 5. `$env:POSH_THEMES_PATH` 为空

先重新打开终端并确认安装正常。也可以直接使用自己保存的主题文件，并在 `$PROFILE` 中通过绝对路径初始化；这样不依赖该环境变量。

### 6. PowerShell 启动速度变慢

使用调试输出检查各片段耗时：

```powershell
$theme = Join-Path $HOME '.config\oh-my-posh\mytheme.omp.json'
oh-my-posh debug --config $theme
```

Git 状态、云平台状态等片段可能访问大量文件或外部命令。根据调试结果减少不需要的片段，或关闭对应片段中代价较高的属性。

## 九、参考资料

- [Oh My Posh 官方文档](https://ohmyposh.dev/docs/)
- [Windows 安装说明](https://ohmyposh.dev/docs/installation/windows)
- [字体安装说明](https://ohmyposh.dev/docs/installation/fonts)
- [PowerShell 提示符初始化](https://ohmyposh.dev/docs/installation/prompt)
- [主题列表](https://ohmyposh.dev/docs/themes)
- [配置与片段说明](https://ohmyposh.dev/docs/configuration/general)
- [Oh My Posh GitHub 仓库](https://github.com/JanDeDobbeleer/oh-my-posh)
