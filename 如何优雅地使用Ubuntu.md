我们需要优雅地使用ubuntu

<!--more-->

# INIT 初始化

## Emacs只运行在字符界面
默认Ubuntu下用`apt install emacs`安装的是带图形界面的emacs，启动的时候会弹出emacs图形窗口。

解决方法：

在你的用户主目录下 =.bashrc= 文件中，添加 alias emacs='emacs -nw' ，执行 ~source ~/.bashrc~ 即可。



## 修改Ubuntu键位

### 方法1: 使用ubuntu键位配置
在~/.xinputrc中加入： setxkbmap -option "ctrl:swapcaps" ，从而实现登录你的用户后`Ctrl`和`Caps`交换，而不影响其他账户。


### 方法2: 使用gnome-tweak-tool

install gnome-tweak-tool

``` bash
sudo apt install gnome-tweak-tool #安装
gnome-tweaks #启动
```

定位到 `Keyboad&Mouse` > `Additional Layout Options` > `Ctrl key position` 选中 `Swap Ctrl and Caps Lock`

## 修改鼠标滚轮自然滚动

`settings` > `Devices` > `Mouse & TOuchpad` 找到 `Mouse` 设置部分，选中 `Natural Scrolling`



## 安装中州韵输入法

中州韵输入法官网： `http://rime.im`

`apt install ibus-rime`


## 电源管理

- 下载电源管理工具包 `sudo apt install pm-utils`
- 睡眠 `sudo pm-suspend`



# THEME 主题

## Numix icon theme

### install
```bash
sudo add-apt-repository ppa:numix/ppa
sudo apt-get update
sudo apt-get install numix-gtk-theme numix-icon-theme-circle numix-icon-theme-bevel numix-icon-theme-square
```


# SOFTWARE
- snap :: 新的软件管理工具，snap格式会把一个软件依赖的所有库都打入一个包内，非常干净
- appimage :: 类似mac的软件，一个镜像一个软件
  1. 只要对Appimage文件赋予执行权限即可
  2. 第一次执行会提示是否集成，会添加桌面图标到 ~$HOME/.local/share/applications/~ 中
  3. 会在 ~$HOME/.config/~ 下创建应用名字的目录
  4. 解压已有AppImage文件: ~xxx.AppImage --appimage-extract~
  5. 压缩已解压的AppImage目录: 下载appimagetool-x86_64.AppImage, 执行 ~appimagetool-x86_64.AppImage 解压后的文件夹~



- list
  - `Terminator`


## WPS :: 文字表格处理

1. 解决打开WPS时出现的系统缺失字体问题
https://pan.baidu.com/s/1eS6xIzo 下载 wps_symbol_fonts.zip

把下载下来的字体包内6个字体复制到 ~$HOME/.fonts/~ 目录下或 ~/usr/share/fonts~ 目录下即可

重启wps


- Terminator :: 多功能终端工具
- KeePassXC :: 密码管理工具
- uget :: 下载工具

** Keepassxc :: 密码管理工具

官方网站 https://keepassxc.org/



```bash
sudo add-apt-repository ppa:phoerious/keepassxc
sudo apt update
sudo apt install keepassxc
```




# PROXY 代理

## shadowsocks

`apt install shadowsocks-libev`


config file ~/.shadowsocks.json content:

```json
{
  "server":"x.x.x.x",
  "server_port":10155,
  "local_address": "127.0.0.1",
  "local_port":1080,
  "password":"vZBoklA",
  "timeout":300,
  "method":"chacha20",
  "fast_open": false
}
```

run: `ss-local -c ~/.shadowsocks.json`

now, ss will open a sock5 proxy port


## PROXYCHAINS
对应用定向代理的工具

```bash
sudo yum install -y gcc git
apt install git
git clone https://github.com/rofl0r/proxychains-ng.git && cd proxychains-ng
./configure --prefix=/usr --sysconfdir=/etc && make && sudo make install && sudo make install-config
sudo sed -i 's/^socks4\(.*\)9050/socks5  127.0.0.1 8016/g' /etc/proxychains.conf
```


# TROUBLESHOOTING

## Idea生成快捷图标
在桌面上建立`idea.desktop`文件，编辑

```ini
[Desktop Entry]
Name=IntelliJ IDEA
Comment=IntelliJ IDEA
Exec=/home/longsky/Application/idea-IU-163.7743.44/bin/idea.sh
Icon=/home/longsky/Application/idea-IU-163.7743.44/bin/idea.png
Terminal=false
Type=Application
Categories=Developer;
```

赋予此文件执行权限

```bash
chmod +x idea.desktop
```

双击图标，就可以直接启动`IntelliJ IDEA`了


## 安装virtualbox卡死
安装virtualbox有可能出现错误，更新apt也可能出现virtualbox不能运行的问题

系统提示：
dpkg was interrupted, you must manually run 'sudo dpkg --configure -a' to correct the problem

当输入 `sudo dpkg --configure -a` 的时候

到下面提示

Loading new virtualbox-5.2.10 DKMS files...
Building for 4.15.0-22-generic
Building initial module for 4.15.0-22-generic

然后系统就一直挂起，无法C-c强行终止，只能重启，重启后dpkg依然不能运行。又提示执行上面命令

### 解决方案：恢复dpkg系统

```bash
dpkg --remove --force-remove-reinstreq virtualbox-qt
dpkg --remove --force-remove-reinstreq virtualbox
dpkg --remove --force-remove-reinstreq virtualbox-dkms
dpkg --configure -a
```

### 解决方案：重装virtualbox
1. 执行: `apt install dkms`
2. `Disabling secure boot in my BIOS`
3. `apt install virtualbox`

## 大版本升级
```bash
sudo apt update
sudo do-release-upgrade
```