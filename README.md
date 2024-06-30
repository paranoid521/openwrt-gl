# 1806_SDK

with github workflow compile.

编译报错：c-stack.c:55:26: error: missing binary operator before token “(“ 55 | #elif HAVE_LIBSIGSEGV &&
https://blog.csdn.net/polaris_zgx/article/details/128559217

**背景介绍**
GL-SF1200 路由器使用了siflower的SF19A28国产芯片，虽然也是mips架构，但由于经过了魔改，不论是mips_24kc还是mipsel_24kc的elf均无法在其上运行。虽然官方在软件仓库提供了大量软件包以供下载，但是有时我们想要安装一些不常见的或者专有的软件包的时候，就会遇到困难。

**解决思路**
好在官方提供了该固件的完整源码，因此我们可以在略微调整构建流程后，利用github actions迅速构建我们自己的软件包。
本文基于官网固件V3.204 - Aug 9, 2021版本，目前最新的3.207版本也可使用，对应源码位于官方仓库openwrt-18.06-siflower分支的commit789db2edb75f4924c6b9227847240d0bfb0d2cd0

**编译流程**
我的仓库已经配置好了全部所需内容，整合了lean openwrt的常用软件包。只需要先在本地运行如下命令配置好需要的软件包

```shell
sudo -E apt-get -y install build-essential asciidoc binutils bzip2 gawk gettext git libncurses5-dev libz-dev patch python3 unzip zlib1g-dev lib32gcc1 libc6-dev-i386 subversion flex uglifyjs gcc-multilib g++-multilib p7zip p7zip-full msmtp libssl-dev texinfo libglib2.0-dev xmlto qemu-utils upx libelf-dev autoconf automake libtool autopoint device-tree-compiler antlr3 gperf swig libtinfo5

git clone --depth=1 -b openwrt-18.06-siflower https://github.com/fumiama/openwrt-gl.git
cd openwrt-gl/openwrt-18.06

git clone --depth=1 https://github.com/fumiama/openwrt-passwall.git ./package/passwall
git clone --depth=1 https://github.com/fumiama/openwrt-packages.git ./package/useful
git clone --depth=1 https://github.com/fumiama/opackages.git ./package/fumiama
git clone --depth=1 https://github.com/fumiama/openwrt-upx.git ./package/fumiama/upx
git clone --depth=1 https://github.com/fumiama/luci-theme-rosy.git ./package/fumiama/luci-theme-rosy
git clone --depth=1 https://github.com/fumiama/lede.git package/leana
mv package/leana/package/lean package/
mv -n package/leana/package/libs/* package/libs/
rm -rf package/leana

cp .config.sf1200 .config
cd include
mv siwifi_version.mk.sf1200 siwifi_version.mk
cd ..

./scripts/feeds update -a
./scripts/feeds install -a

make menuconfig
```



然后fork我的仓库，将生成的.config文件覆盖到openwrt-18.06/.config.sf1200，即可激活自动编译。编译完成后，前往actions下载编译好的软件包即可。另外只建议安装软件包，不建议直接安装编译出的固件，可能会出现各种疑难杂症。

**其他选择**
如果您并不擅长自己编译，这里提供一个编译好的软件包合集，增加的软件包比较有限，基本上是我自己常用的，主要是一些主题和一些你懂的软件，所以如果里面没有您想要的软件包，也请勿私信打扰，谢谢您的配合。

20210929
**安装软件包**
首先您需要在路由器官方管理界面的更多设置->高级功能中选择安装luci，然后使用scp上传软件包filetransfer及其i18n翻译到/tmp，用ssh登录后，使用opkg install命令安装二者，即可在luci中找到文件传输。接下来的软件包就可以方便地通过文件传输一键上传+安装，无需再用ssh。
推荐使用rosy主题美化界面，效果如下
————————————————

                            版权声明：本文为博主原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接和本声明。

原文链接：https://blog.csdn.net/u011570312/article/details/120546354
