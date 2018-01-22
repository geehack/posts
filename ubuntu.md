### 初始化

### Emacs只运行在字符界面

默认Ubuntu下用`apt install emacs`安装的是带图形界面的emacs，启动的时候会弹出emacs图形窗口。

解决方法：

在你的用户主目录下`.bashrc`文件中，添加`alias emacs='emacs -nw'`，执行`source ~/.bashrc`即可。



### 修改Ubuntu键位

#### 方法1
在`~/.xinputrc`中加入：`setxkbmap -option "ctrl:swapcaps"`，从而实现登录你的用户后`Ctrl`和`Caps`交换，而不影响其他账户。


#### 方法2
使用gnome-tweak-tool

```bash
sudo apt install gnome-tweak-tool #安装
gnome-tweak-tool #启动
```

定位到`Keyboad&Mouse` > `Ctrl key position` 选中`Swap Ctrl and Caps Lock`

### 修改鼠标滚轮自然滚动
`settings` > `Devices` > `Mouse & TOuchpad` 找到`Mouse`设置部分，选中`Natural Scrolling`

### 电源管理

- 下载电源管理工具包 `sudo apt install pm-utils`
- 睡眠 `sudo pm-suspend`



### 主题

#### Numix

``` bash
sudo add-apt-repository ppa:numix/ppa
sudo apt-get update
sudo apt-get install numix-gtk-theme numix-icon-theme-circle
```



### 软件
- `Terminator`


#### Idea生成快捷图标
在桌面上建立`idea.desktop`文件，编辑

``` ini
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

``` bash
$ chmod +x idea.desktop
```

双击图标，就可以直接启动`IntelliJ IDEA`了
