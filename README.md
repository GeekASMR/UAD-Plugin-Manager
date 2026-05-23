# UAD 插件管理器 (UAD Plugin Manager)

> 单文件 · 零依赖 · Windows 原生
> 一键隔离 UAD-2 平台上未授权 / 未试用 / 已过期的插件,让 DAW 插件列表只剩你真正能用的产品。

![platform](https://img.shields.io/badge/platform-Windows%2010%2F11%20x64-blue)
![runtime](https://img.shields.io/badge/runtime-Neutralinojs%206.5.0-green)
![license](https://img.shields.io/badge/license-MIT-lightgrey)

## 项目背景

UAD-2 安装后会把全部插件 dll/vst3 都放在系统目录,无论你是否拥有授权,DAW(Studio One / Cubase / REAPER 等)启动时都会扫描全部 ~700 个插件,导致:

- DAW 启动 / 插件浏览器加载缓慢
- 插件列表里堆满 demo / 已过期 / 未试用项目,干扰选择
- UAD Mono 文件夹下尤其混乱(每个未授权插件还会出现一份 mono 入口)

**本工具读取 `UADSystemProfile.txt` 授权清单,智能匹配本地物理插件文件,把未授权的 dll/vst3 重命名为 `.disabled`,DAW 重新扫描后只剩你拥有的插件。**

## 功能特性

- ✅ 读取 UAD 官方应用导出的 `UADSystemProfile.txt` 授权数据
- ✅ 自动扫描三个 UAD 标准安装路径(VST2/VST3,含 Steinberg 副本目录)
- ✅ 智能 token 匹配,正确处理 `Pultec Collection` / `1176 Collection` / `Mix Rack` 等 Bundle 关系
- ✅ 区分 Authorized / In Trial / Not Started / Expired 四种授权状态
- ✅ 一键清理 "Not Started + Expired" 全部未启用 demo
- ✅ 一键恢复所有被隔离的插件
- ✅ VST3 bundle **内层 .vst3 文件同步禁用**(避免 DAW 深度扫描穿透)
- ✅ 多选 / 右键单项隐藏与恢复
- ✅ 中英双语 UI 切换
- ✅ 单 exe,**无需 Python / Node / Visual C++ 运行时**

## 扫描路径

工具默认扫描三个 UAD 安装路径:

| 类型 | 路径 |
|---|---|
| VST2 主目录 | `C:\Program Files\Universal Audio\Powered Plugins\UAD-2 Powered Plugins` |
| VST2 Steinberg 副本 | `C:\Program Files\Steinberg\VstPlugins\Universal Audio` |
| VST3 | `C:\Program Files\Common Files\VST3\Universal Audio` |

## 使用流程

1. 在 UAD 官方控制台 (Console / UAD Meter & Control Panel) 中点击
   **"Save Detailed System Profile"**,生成 `UADSystemProfile.txt`
2. 下载 [Releases](../../releases) 中的 `UAD插件管理器.exe`,双击运行
3. 点左上角 **"载入授权档案"**,选择第 1 步生成的 txt 文件
4. 列表自动加载并标注每个插件的授权状态
5. 选择处理方式:
   - **底部"隐藏未试用/过期插件"**:批量禁用全部未授权 dll
   - **多选 + "🚫 隐藏选中插件"**:精细控制
   - **右键 → 在 DAW 中隐藏 / 恢复**:单项操作
6. **完全关闭并重启 DAW**(Studio One 用户建议先删除 `Vstplugins.settings` 与 `vstservice.json` 强制重扫)

## DAW 缓存清理(首次使用建议)

Studio One 6 用户,首次使用本工具后建议清理插件缓存以确保扫描结果干净:

```cmd
del "%APPDATA%\PreSonus\Studio One 6\x64\Vstplugins.settings"
del "%APPDATA%\PreSonus\Studio One 6\Snapshots\vstservice.json"
```

Cubase / Nuendo:删除 `%APPDATA%\Steinberg\<Cubase x>\Vst2xPlugin Infos *.xml`

REAPER:Options → Preferences → Plug-ins → VST → "Clear cache"

## 工作原理

工具不会修改任何 dll 二进制内容,只通过 **文件名后缀重命名** 实现隐藏:

```
未授权: UAD ADA Flanger.dll              → UAD ADA Flanger.dll.disabled
VST3:   UAD API 2500.vst3\               → UAD API 2500.vst3.disabled\
        Contents\x86_64-win\              ↳ 内层 .vst3 同步重命名为 .disabled
            UAD API 2500.vst3
```

DAW 重新扫描时,后缀不被识别为 VST,插件被自然忽略。需要恢复时反向重命名即可,完全可逆,不破坏任何文件,不修改注册表。

## 风险与免责声明

- 本工具**仅修改文件后缀**,可完全反向恢复;不破坏 UAD 软件本身
- 操作 `C:\Program Files` 下的 dll 需要管理员权限,首次运行可能弹出 UAC
- 本工具不绕过任何 UAD / iLok 授权机制,只是隐藏未授权插件以净化 DAW 列表
- 使用风险由用户自负,使用前建议备份 `C:\Program Files\Universal Audio\` 与 `C:\Program Files\Common Files\VST3\Universal Audio\` 两个目录的目录树

## 致谢

- 基于 [Neutralinojs](https://neutralino.js.org/) 6.5.0 构建
- TrayIcon 来自 [Freepik](https://www.freepik.com) (Flaticon)

## License

[MIT](LICENSE)
