Title: 如何用EXWM替换Gnome
Date: 2025-01-18
Category: 技术
Tags: 中等,emacs,exwm,桌面

### 折腾的缘由

没事干，折腾折腾。反正网上教程很全，不论是emacs-china，还是system-crafter，抑或是官方github项目界面，讲的都非常清楚，复制粘贴一遍就能用。

### 我的电脑环境

本人用的是Debian 12 Gnome。

电脑上装有emacs和guix。

### 流程

首先得安装exwm

直接`guix install emacs-exwm`

接下来我们要写配置文件，这里建议如果有不明白的地方都严格按照教程来，复制粘贴没错。

具体我就不贴代码了，之后会考虑将我的init.el和.emacs传到github。目前创建了git仓库追踪改动，但是没有同步到github。

虽然我基本是照抄github上exwm官方配置，但后来看到system crafter的配置，用use-package来隔离确实更加高效合理。

总之呢，抄一处先跑起来就行。

注意，用户目录下得有.xinitrc和.xsession，这个.xsession应当是指向.xinitrc的符号链接，通过在自己的用户目录里运行`ln -s .xinitrc .xsession`创建。

配置好后就可以重启了，当然你也可以直接在emacs里运行M-x exwm-ensure或用Crtl-Alt-F？进入tty手动切换。

只要配置没抄错，肯定是可以正常使用的。用gdm之类的小伙伴可别忘了在`/usr/share/xsessions/`文件夹里手动添加桌面配置文件啊！

### 感想

问题自然是有的，目前遇到四点：

1. emacs-wim（在exwm环境下是exwm-wim-mode）默认用Ctrl-\来切换英文和中文输入，默认的很方便，但是每次只能打一个字，词都不行。感觉可用性上不如fcitx5配Rime，可能需要自己配置吧。
2. 对笔记本手势几乎0理解，在gnome下可以左右滑动，exwm完全不支持，遇到大网页想看全似乎只能缩小页面，不好用。而且上下滑居然是反的，我手往下滑他界面也往下，往上他也往上，真难受。我知道肯定有办法调转wheel-up和wheel-down，但是问题在于如何区分来自触摸板和真正鼠标的wheel-up，只有触摸板需要调转呀！
3. 虽然emacs可以跑wayland，但exwm不行，导致我的waydroid用不了，想用得换gnome桌面。
4. 笔记本上的XF86开头的键（一般是最上面的那些小键，可以一键静音啊调亮度啊之类的）都显示未定义，也就是可以收到信号，但没有绑定操作。好处是更加自由，坏处是想弄得一个个上网搜。

当然也有其他坑，只是我习惯用终端操作gpg，没有多显示屏，所以暂时没有遇到。

不过，我觉得exwm还是非常好的，如果能配置好的话肯定比gnome更加好用。

更何况exwm的主开发是国人，这真是国产之光了，厉害。

### 后续

后续呢，建议参照emacs-china或者system-crafter的分享，可以根据自己喜好添加系统托盘之类的东西。

我的建议是至少遵守一下Xft.dpi: 200那个，否则恐怕大多数电脑上打开gui应用字都会非常小！

快尝试一下开机就进emacs的神奇世界吧。