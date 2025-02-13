Title: exwm如何与gnome和谐共存
Date: 2025-2-13
Category: 技术
Tags: exwm,gnome,桌面环境,中等,emacs

### 事情没有结束

大家还记得上次那篇如何安装exwm的文章吗？

我用着就特别难受。不是不喜欢exwm，而是很多事还是gnome方便————可是之前那样配置导致在gnome下emacs也会运行exwm，虽然可以手动选择不替换，但明显变长的加载时间怎么能忍？

### 问题根源

当时我的exwm加载放在主配置里，只要开emacs就会运行，严重影响体验。我尝试了手动用独立配置，开机先开守护进程（daemon），但是也非常麻烦。

### 下定决心

在痛苦的迫使下，我再次打开了system-crafter的网站仔细查看。

解决方案应运而生————抄！

### 过程

主要办法是，把exwm配置分离出来由.desktop文件单独加载

这样主配置就不会受影响了，非常聪明。

这里难点在于use-package的使用。我鼓起勇气查阅了文档，才发现其实不难。配合quelpa、quelpa-use-package和system-package三个包，可以说非常方便！（也有麻烦的地方，就是每次出现新包use-package都会自动拉取包目录，而Melpa又常常连不上……）

这里给个例子：

```lisp
;; Magit, the git interface for emacs
(use-package magit
  :ensure t
  :ensure-system-package git)
```

这个代码片段来自我的emacs配置，可以说，用上use-package确实清晰很多。具体能做什么，可以去看我emacs配置的init.el文件。

值得注意的两个点：

- 可以`:custom`的可自定义变量可以通过`C-h v`来查看。
- 不清楚就用`:config`在模块加载后修改，不完美但一般是安全的。

### 后记

感觉没啥好说的，因为主要就是看看system-crafter和一些文档，抄啊抄。没有什么自己原创的东西。

不过quelpa有一点很不好，"git://"链接是支持的，但是文档里没说————可能写的人觉得显然，但是对大多数人来说链接的scheme只能是http或ftp之类的吧！