## Mac 修改 host 文件

### 一、三方工具

- 工具一：[Hosts-1.2.pkg.zip](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.macx.cn%2Fforum.php%3Fmod%3Dattachment%26aid%3DMzk2NzcwfDg0M2NlYTlifDE1NjI4OTkwMDZ8MHwyMDU3NDI3)

  安装后，在系统偏好设置底部会增加一个 Hosts 快捷入口，然后进行可视化操作即可

  注意：修改后没有即可生效，可以使用终端命令手动清除 DNS 缓存

  ```
  dscacheutil -flushcache
  ```

- 工具二：[iHosts-/etc/hosts编辑器](https://apps.apple.com/cn/app/ihosts-etc-hosts-%E7%BC%96%E8%BE%91%E5%99%A8/id1102004240?mt=12)

  App Store 直接下载安装，支持分组与切换

### 二、终端命令

1. 执行命令`sudo vi /etc/hosts`
2. 输入本机密码即可打开 hosts 文件
3. 扩展：`i`插入、`:w`或`:q`保存退出、`q!`不保存退出

### 三、修改文件

- 进入 Finder 使用 shift + commond + G 查找`/etc/hosts`，确认前往
- 复制 hosts 文件值桌面，修改后保存并将原先 hosts 文件替换掉即可

