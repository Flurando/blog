Title: 昨天gnome大崩溃原因部分找到
Date: 2024-12-10
Category: 技术
Tags: common-lisp guix bug 环境变量 lisp

###后续

我今天打开电脑，正准备大干一场，没想到天公不作美，恰逢连夜暴雨。

又出现昨天的问题了，我！幸运的是我急中生智，细细思索，终于发现一丝端倪————我昨天“解决”问题后唯一做的操作就是guix install gcl sbcl，完成后写了篇博客就睡觉了————这说明guix install gcl sbcl弄坏了我的gnome桌面环境。

问题来了，这是怎么弄坏的？我guix pull啊guix install很多次了没出过岔子，gcl下过也没有问题，难道是sbcl吗？

我按Ctrl Alt F3进入tty3，输入账户密码登录后果断运行guix remove gcl，然后sudo reboot。没用。接着我又guix remove sbcl，sudo reboot，好了。说真的我挺难过的，不是心疼电脑开关好几次，而是难过sbcl用不了。

查过lisp相关文章的都知道，目前比较好用的lisp方言就是common lisp和scheme两个，这个不解释，事实就这样。

####Common Lisp世界里标配开发方式是什么？

参考portable，这是一个打包好的移动开发环境，里面包含了SBCL（steel bank common lisp，最快的开源实现），emacs（拓展性最强的编辑器），quicklisp（common lisp最好的包管理器）和slime（用于emacs和common lisp间最经典的插件，提供丰富的编辑功能）。这里另外三个没有多少异议，除了slime可以用它的fork项目sly代替以外。

说到实现，选择可就很多了，商业的有Allegro和Lispworks左右护法，开源的有ABCL，SBCL，CLASP等等，还有基于java虚拟机的CCL（这个在lisp社区相当于go），编译成虚拟机字节码的Clisp，还有在Gnu名字下挂着的古老实现GCL（以前是ATCL）。

这么多实现如何选择呢？

简单来说就是商业实现的话个人选lispworks，企业看金主；开源选SBCL和CCL一起。

商业的就不细说了，开源选SBCL是因为它编译能达到的速度是最快的，但是编译速度很慢，很多人会搭配快速编译的CCL使用。相当于开发时用CCL快速迭代，发布用SBCL压榨性能。

这时候可能有人会问了，我就不信这个邪！我就要用其他实现！哈哈，我就是这么想的，所以我下载了兼容性最好的clisp。但是我也想有编译成机器码的能力，所以我把目光转向了GCL————gnu官方的common lisp实现，想想，gnu官方，一个字，“牛”。

我一看介绍，好家伙历史悠久，功能齐全。好，马上下载！结果下好一试，好嘛，用它也能用，但是slime连不上，quicklisp也用不了。我心里纳闷啊，gnu的孩子怎么这么逊，太不应该了！我去看slime文档，没有提到支持gcl，那就是不支持的意思；我又去看quicklisp的官网，在quicklisp.lisp里倒是看到大大的export gcl字样，说明维护者是有意支持gcl的，但我往下翻却没有gcl的身影……我有些不好的预感，去看gcl的主页，没有任何论坛，唯一的报bug或传patch的通道还要注册开发帐号，完全不是2024年gnu软件包的样子！我一看commit log，好家伙，几十年来好像就一个人在维护，上次活跃提交是在2010年，沉默10多年，最近2024跨年前后有两次提交，但都是些边缘问题。我又去看issue tracker，好家伙，从2006年开始到2024年一堆紧急bug没人修，好多无法编译的报告让我感觉这东西自己能运行就很不错了，指望它连slime用quicklisp不大现实，可能是搭配信件邮递来合作编程的。

总之呢，年久失修，已经几乎无法使用，虽然我看clisp和sbcl这些也是严重缺少修缮，但这些蒸汽机至少还能按照人们说的“Common Lisp就这么用”的方法动起来，gcl已经无法用于实际生产，只能在gnu的博物馆里里等待来年了。

####那scheme呢？

这和今天文章主题没有关系，过！

###接前文

所以，选择其实不多，主要就是sbcl。但通过guix下载的sbcl又会破坏gnome，这就很难受了。

我面前就3个选择

1. 不用gnome————这个不可能，至少目前我没工夫去折腾。
2. 不用sbcl————也不可能，如果希望最后能得到机器码的话。
3. 用portable-IDE————可以，但我电脑上已经有了emacs，slime和quicklisp，只为了sbcl下移动环境得不偿失。

最后一提，我在guix的issue tracker上看到一模一样的问题了，序号是45360，2020年的，到现在都没解决。

我有时间会再试试用发行版包管理器安装sbcl，别无选择了！