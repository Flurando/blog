Title: 给Debian电脑配置Howdy人脸识别
Date: 2025-02-27
Category: 技术
Tags: 中等,emacs,Howdy,桌面,Gnome,人脸识别,命令行,terminal,终端,console,密码,debian

### 起因

人在学校，不方便输入密码，一输入就不是密码了。

于是我就想啊能不能应用其他认证方式。
搜了一下密码狗，非常好，可惜我没有。
指纹登录需要买专门的扫描仪，我也没有。

我这里的选择只剩声纹和人脸识别了吧。
我知道还有其他认证方式，但是你们看我连密码狗都没有怎么可能有那些设备，像弄个谜题代替gdm进门输密码的那种不是我这类连源代码都没编译过几次的人要考虑的问题……

声纹算了，没看到现成的。只有人脸识别的Howdy感觉非常好。

### 警告

本博客只是个人经历，虽然我的电脑上是配置好了，但不代表你按照我的来也能成。不过本着乐于助人的精神，我将把我电脑相关的配置列一下，如果你恰好全部符合，不妨尝试一下。

- 电脑系统：Debian 12 Bookworm  
  (内核用的是Linux 6.1.0 amd64，由于libre内核我的网卡和蓝牙都不支持，也就是启动了没网，所以我默认还是用普通内核)  
- 电脑上存在如下路径：
    + /etc/pam.d/
    + /usr/share/pam-configs/
    + /usr/local/
- 愿意使用命令行

  注意，这次我们几乎全部操作都是命令行操作，没有什么图形界面。不喜欢的现在可以走。

### 步骤

1. 将github上的仓库克隆到本地，目前默认是beta分支，别改。
   运行`git clone https://github.com/boltgolt/howdy.git`得到howdy文件夹。什么？你没有git这个命令？先用`sudo apt install git wget curl`安装吧，顺便弄上wget和curl两个下载工具，以免等下又缺斤少两的。   
2. 安装需要的依赖。
   运行`sudo apt-get update && sudo apt-get install -y python3 python3-pip python3-setuptools python3-wheel cmake make build-essential libpam0g-dev libinih-dev libevdev-dev python3-opencv python3-dev libopencv-dev meson`等它下载完，需要root权限。
   这里要注意，目前Debian Bookworm的meson包是以ninja-build为依赖的，所以不用特别安装，你害怕打上ninja-build也行。github主页上的INIReader就是那个libinih-dev，libevdev就是后面那个不用我多说了吧。
   注意，你要是现在尝试就会发现它就是少一个dlib模块没有，当然了要是想用howdy-gtk可能会遇到elevate之类的也缺，哈哈。我已经放弃elevate了，也就是不用howdy-gtk，那个现在bookworm里根本没有可能会弄坏系统吧————反正也不是要紧功能，算了，这种自己重启用sudo的其实可以去掉，记得`sudo howdy-gtk`之类的就行了嘛（目前已经没有gksudo之类的包了，好像使用一些默认配置让你在sudo图形程序时自动的进入根用户的环境，总之安全），但我懒得碰，主要是不会meson，到时候手动删除肯定很麻烦。
   那么，这个dlib从哪里弄呢，参考[这里](https://github.com/boltgolt/howdy/pull/814)。
   想自己编译的可以试试，我没弄，py2dsc在debian-unstable里，懒得开，像pypi-download嘛我怎么觉得直接`pip download`就可以？反正我直接从[这里下载](https://github.com/DingoBits/howdy/releases/tag/3.0.0-git20230625.c17a834)的[末尾写着deb12的dlib安装包](https://github.com/eatyourbaby/howdy/releases/download/3.0.0-git20230625.c17a834/python3-dlib_19.24.2-1_amd64_deb12.deb)。
   可以运行`wget https://github.com/eatyourbaby/howdy/releases/download/3.0.0-git20230625.c17a834/python3-dlib_19.24.2-1_amd64_deb12.deb`将安装包下载到本地，然后用`sudo apt install python3-dlib_19.24.2-1_amd64_deb12.deb`完成安装。我这里没有遇到任何报错，如果你不幸碰上了，噢哄，gameover。
   到这里如果没有问题，我们就可以开始编译安装了。
3. 按要求编译安装
   首先我们要进入刚才`git clone`的文件夹，直接`cd howdy`。
   接着，一条条输入github主页指导
   
	meson setup build
   	meson compile -C build
   	meson install -C build
   
   用没有报错？没有的话，恭喜你，我们还剩最后几步。
4. 下载dlib所需额外数据
   通过`cd /usr/local/share/dlib-data/`进入相关文件夹
   你应该可以看到里面有一个install脚本和一个readme，我们直接运行这个脚本，输入`./install.sh`。
   待三个文件下载好后它会unpack解开压缩包。
   到这里，我们就完成了howdy的安装。
5. 测试howdy能否正常工作
   用`sudo howdy add`添加你的脸，中间会等你输入这个人脸数据的名称，不重要但你写清楚不是自己看着方便嘛！   
   在摄像头旁边小灯亮起后，请直视摄像头，可以稍微动动，熄灭后如果没有报错就说明成功了，可以用`sudo howdy list`查看。   
   如果add的时候直接报错怎么办，这时候很可能是摄像头没有成功自动识别，你得手动配置，先`cd /usr/local/etc/howdy/`, 然后`sudo nano config.ini`或者`sudo gnome-text-editor config.ini`或者你喜欢的方式，总之我们现在要修改这个config.ini配置文件，而且它需要root权限。
   看见device_path那条没有，不用找，直接用查找与替换之类的功能跳转过去。
   这个就自己在/dev/下面找有没有video加数字的就行，具体可以通过`ls /dev | grep video`来查看，可能会有好几个，一个个尝试吧，我们需要的是add时可以打开摄像头灯光的。
   现在如果你成功add了（可以添加多个，没关系），我们使用`sudo howdy test`来测试，它会打开一个窗口。
   窗口是黑白的，有你的人头在动，应该会有个圈圈在你的脸上，表示它识别出这是人脸。如果圈圈是红色的表示不成功，绿色表示匹配成功，你可以多试试，用Ctrl+C停止（点击关闭窗口没有用）。这是用来帮助你决定要不要`sudo howdy clear`或者`sudo howdy remove`来清空或移除人脸数据并重新`sudo howdy add`。
   确认无误后让我们进入下一步。   
6. 配置pam
   如果不配置pam（Pluggable Authentication Modules），howdy是不会工作的。就好像你购买了一把电子指纹门锁，却不把它装在门上，只是插着电摆在车库的工作台上一样！
   有的人建议直接修改/etc/pam.d/common-auth，或者在/etc/pam.d/sudo里添加规则尝试，都是很好的，只是我最终没有这么做。
   原因是改common-auth会导致每当系统需要问你要密码，你都得先对着镜头等半天，要是光线不好可能几次都卡着，而且在无限循环要求认证的场景下（比如gdm开机后第一次要你输密码登录），你可能就卡死在那里了！而改sudo，你懂的，只有sudo会调用。
   最大的问题在于，即便我们忽略gdm开机吊死在人脸识别的尝试这棵树的情况，无法在gdm原本请求密码时选择桌面环境也是很要命的事情，不是不能选，而是刚出来你就认证成功进桌面了。不像原本问密码，认证模块加载时会等你输入，howdy目前加载并不会让gdm开放选项，等它认证结束选项才出来！来不及！你看，本来挺方便的桌面切换变成只能手动改/var/lib/AccountsService/users/<你的用户名>了。
   不过我也是发现了解决办法，不算解决，算是workaround吧。
   就是我们不要动那个/etc/pam.d/文件夹，我们在/usr/share/pam-configs/文件夹里新建一个howdy文件，纯文本。（这里更好的选择是放在/usr/local/share/pam-configs/下面，但那样还要调pam-auth-update，更麻烦）
   假设你已经在那个/usr/share/pam-configs/文件夹里，不在的话用`cd /usr/share/pam-configs`进，输入下面的命令来生成文件，这里我写了个简单的， 当然你可以自己改，具体我不大懂，照着旁边unix文件写的。
   
       cat > howdy << EOF
       Name: Howdy the face recognization for Linux just like Hello in Windows
       Default: yes
       Priority: 128
       Auth-Type: Primary
       Auth:
		sufficient   /usr/local/lib/x86_64-linux-gnu/security/pam_howdy.so
       EOF
   
   这里有一点要注意，这个到pam_howdy.so的路径你那里可能和我不一样，具体看当时`meson install -C build`的输出把pam_howdy.so下到哪里去了，或者在/usr/local/lib/里找一找有没有类似的，看看。
   生成好后，运行`sudo pam-auth-update`，应该是个tui界面，你按一下Tab键再回车就可以。
7. 亲身尝试   
   现在可以试试，另开一个终端sudo个什么东西，或者注销重进。

我配置好后的效果是，它先让我输密码，不论输入什么都会开启人脸识别。识别成功直接通过，不成功才会检查密码对不对。不过呢，注销后或者刚开机，gdm会强制要求给它正确密码，否则不允许登录，即使人脸识别通过————这可以开启自动登录来绕开，但是怎么说呢，你终归得输入一次密码，因为howdy只是负责认证，我知道你是谁而已，你的密码不给电脑，密钥串什么的都解不开。虽然密钥串在部分高端配置下并不是好的选择，但首次登录输入密码又不费事，何乐而不为呢？更何况，只是刚开机和注销后不能用，锁定屏幕后的解锁还是可以的。

关键是，由于有了输入密码这一步，我们可以在gdm登录页面选择桌面环境。每次开机不乱注销的话只需要输入一次用户密码，可以说足够方便了！

你也是这样吗？是的话恭喜你，配置成功。目前我们只要在第一次要密码时输入，后续都可以随便回车让它人脸识别啦！

### 感想

很可惜，目前这个项目有些年久失修的样子，这次是冒着弄坏系统的风险手动装了一堆东西，dlib，说的就是你！不过还好是弄成了，现在用着还可以。至于我比较关心的guix，压根就没有howdy相关的配置。

还是挺开心的，虽然弄了很久。再怎么说，咱也是用过人脸识别的人，走路都硬气许多！哈哈！