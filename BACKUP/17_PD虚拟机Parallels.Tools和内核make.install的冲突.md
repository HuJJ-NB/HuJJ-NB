# [PD虚拟机Parallels Tools和内核make install的冲突](https://github.com/HuJJ-NB/gitblog/issues/17)

一开始发现错误是每次`make`都会出现一个报错，但是重新`make`就好了。部分错误信息大致如下：

``` shell
scripts/selinux/mdp/mdp.c:2
    #include <generic/autoconf.h>
             ^～～～～～～～～～～^
```

复现一次需要一个小时，不太清楚细节了，大意为找不到这个头文件。

之后发现无法`make prepare`，不确定做了什么改动，重新`make`同样报错。使用`make clean`无法重置，但`make mrproper`可以。

之后跟踪发现，`make install`前，`./include/generic/autoconf.h`文件存在，之后便不存在了。

观察这个阶段的报错，发现`Error: xxxxxxxx`和`parallels tools`有关，什么二进制找不到什么的，然后还有什么构建失败。根据错误信息查看了`/var/log/parallels-tools-install.log`，里面说编译错误的文件路径根本不存在，难以跟踪。

没有研究`make install`的具体过程，可以肯定安装`parallels tools`之后，构建内核时试图编译`parallels tools`，必然会导致问题发生。解决方案大致为两种：不安装`parallels tools`；避免构建内核时编译`parallels tools`。

细致研究`make install`的流程之后应该会有其他更优雅的办法，目前暂且存疑。
