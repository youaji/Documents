## 简介

- https://github.com/Genymobile/scrcpy

- 同类产品：[Vysor](https://www.vysor.io/)、[Total Control](http://tc.sigma-rt.com.cn/)

- 无需 root
- 电脑可完全操控手机
- 条件：adb、android 版本 >= 5.0

## 安装

- Mac「使用 homebrew 安装」
  1. 安装 scrcpy：`brew install scrcpy`
  2. 安装 adb：`brew cask install android-platform-tools`
  3. 执行`scrcpy`启动
- Windows 可直接使用安装文件

## 操作手册

| 功能                                       | 命令                                                         | 快捷键                                   |
| ------------------------------------------ | ------------------------------------------------------------ | ---------------------------------------- |
| 限制分辨率                                 | `scrcpy -m 1024`                                             |                                          |
| 修改视频码率                               | `scrcpy -b 4M`「默认 8M」                                    |                                          |
| 裁剪画面                                   | `scrcpy -c 1224:1440:0:0`<br />分辨率1224*1440，偏移坐标（0，0） |                                          |
| 多设备切换                                 | `scrcpy -s 设备 ID`<br />`adb devices` 查看设备 ID           |                                          |
| 窗口置顶                                   | `scrcpy -T`                                                  |                                          |
| 显示触摸点击                               | `scrcpy -t`                                                  |                                          |
| 文件传输默认路径                           | `scrcpy --push-target /目录/`<br />文件拖拽至 scrcpy 传输的保存位置 |                                          |
| 只读模式（仅显示不控制）                   | `scrcpy -n`                                                  |                                          |
| 屏幕录像<br />投屏并录屏                   | `scrcpy -r 视频文件名.mp4`                                   |                                          |
| 同步传输声音                               | 借助 [usbaudio](https://github.com/rom1v/usbaudio) 实现，仅支持 Linux |                                          |
| 设置窗口标题                               | `scrcpy --window-title '标题‘`                               |                                          |
| 屏幕录像「禁用电脑显示」<br />不投屏只录屏 | `scrcpy -Nr 文件名.mkv`                                      |                                          |
| 关闭手机屏幕「投屏不熄灭」                 | `scrcpy -S`                                                  | `command + O`                            |
| 全屏显示                                   | `scrcpy -f`                                                  | `commond + F`                            |
| 窗口调整为 1：1                            |                                                              | `commond + G`                            |
| 窗口调整为紧凑「恢复上面操作」             |                                                              | `commond + X`<br />双击黑色背景          |
| HOME 键                                    |                                                              | `control + H`<br />鼠标中键              |
| BACK 键                                    |                                                              | `commond + B`<br />鼠标右键              |
| 任务管理键「切换 APP」                     |                                                              | `commond + S`                            |
| 菜单键                                     |                                                              | `commond + M`                            |
| 音量 +                                     |                                                              | `commond + ↑`                            |
| 音量 -                                     |                                                              | `commond + ↓`                            |
| 电源键                                     |                                                              | `commond + P`                            |
| 点亮屏幕                                   |                                                              | 鼠标右键                                 |
| 启用/禁用 FPS 计数器「stdout」             |                                                              | `commond + I`                            |
| 传输文件/安装 apk                          |                                                              | 拖拽文件                                 |
| 展开通知面板                               |                                                              | `command + N`                            |
| 收起通知面板                               |                                                              | `command + shift + N`                    |
| 复制设备剪切板到计算机                     |                                                              | `command + C`                            |
| 复制计算机剪切板到设备                     |                                                              | `commond + V`<br />`command + shift + V` |

## 无线连接

| 功能                                        | 命令                                                         |
| ------------------------------------------- | ------------------------------------------------------------ |
| 查询设备 IP 地址                            | 设备 → 关于手机 → 状态                                       |
| 启用 adb tcp/ip 连接                        | `adb tcpid 4444`<br />4444 为端口号                          |
| 拔掉数据线                                  |                                                              |
| 通过 wifi 连接                              | `adb connect ipAddress:port`<br />ipAddress：设备 IP 地址<br />port：启动时配置的 端口号 |
| 重启 scrcpy                                 |                                                              |
| wifi 传输导致慢、卡顿，调整码率和分辨率优化 | `scrcpy -b 3M -m 800`<br />码率限制为 3 Mbps，分辨率限制为 800 |
| 切换回 usb                                  | `adb usb`                                                    |

