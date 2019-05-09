warez
=====

- Chocolatey windows下的命令行包管理工具 https://chocolatey.org

chocolatey
----------

### 安装chocolatey

```powershell
@powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```

### 安装软件

`choco install -y 7zip everything git jdk8`


common warez
------------
- `7zip` `everything` `git` `jdk8` `python3` `putty` `vagrant` `virtualbox` `vim` `emacs` `wox`
- `IntelliJ IDEA`
- `有道词典`
- `GVim`
- `editplus`
- `visio`

shortcuts
----------
- 关闭应用 `ALT + F4`
- 锁定窗口 `WIN + l`


best practice
-------------
- `vbox + vagrant + putty` 作为linux文档环境连接后跑`tmux + zsh + emacs`
- 软连接: 文件连接`mklink <dest> <source>` 目录连接`mklink /d <dest> <source>`
- WIN10设置多个屏幕不同壁纸 :: 在桌面右键菜单 > 个性化界面 > "选择图片" 栏的图片上鼠标右键点击，会有个显示器列表，选择不同显示器，即可把当前图片设置为指定显示器壁纸


### windows下git输入错误密码时，无法更改密码，显示fatal: Authentication failed
打开运行窗口（WIN+r）, 输入 `rundll32.exe keymgr.dll,KRShowKeyMgr` 打开密码管理器，在里面找到对应的git密码修改或者删除


### CAP to CTRL

cap-to-ctrl.reg file

``` reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Keyboard Layout]
"Scancode Map"=hex:00,00,00,00,00,00,00,00,02,00,00,00,1d,00,3a,00,00,00,00,00
```


PUTTY
------

### RSA密钥转PPK（putty密钥）
1. 运行 `puttygen.exe` 点击 `Conversions` 菜单项中的 `Import key`, 选择在linux下生成的id_rsa文件
2. 然后点击Save provate Key按钮就可以转换成ppk格式
