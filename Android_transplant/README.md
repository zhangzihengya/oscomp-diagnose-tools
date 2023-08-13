# Android transplant

移植分为两部分：内核模块、用户空间

diagnose-tools/Makefile：

```
deps将编译好的文件放在了lib库里

module是编译内核模块的部分

tools是编译用户空间的部分，需要deps编译好的依赖
```

## 内核模块移植

diagnose-tools/SOURCE/module/Makefile：

```
ifneq ($(findstring aarch64,$(ARCH)),)
	DIAG_EXTRA_CFLAGS += -DDIAG_ARM64		# 编译到安卓手机上时，这里记得加上个ARM64的编译选项，即 DIAG_EXTRA_CFLAGS += -DDIAG_ARM64
endif

	$(MAKE) CFLAGS_MODULE="$(DIAG_EXTRA_CFLAGS)" -C $(KERNEL_BUILD_PATH) M=$(MOD_PATH) modules		# Make的主要部分
	
需要条件编译，指定下载好的编译器（注意：移植到安卓上应该使用clang编译器，这里使用的是gcc）
需要指明要构建目标的内核源码的存放位置（从开源网站下载）
```

<div align='center'>![编译工具和源码路径](https://gitlab.eduxiji.net/202311664111382/project1466467-176202/-/raw/main/images/%E7%BC%96%E8%AF%91%E5%B7%A5%E5%85%B7%E5%92%8C%E6%BA%90%E7%A0%81%E8%B7%AF%E5%BE%84.png?inline=false)</div>

最好把内核源码给编一遍，最后会生成一个符号表：

<div align='center'>![符号表](https://gitlab.eduxiji.net/202311664111382/project1466467-176202/-/raw/main/images/%E7%AC%A6%E5%8F%B7%E8%A1%A8.png?inline=false)</div>

注意：开源代码编译出来的符号表可能和手机上的符号表不太一致，所以找中兴那边要了一个，放在：

<div align='center'>![中兴符号表](https://gitlab.eduxiji.net/202311664111382/project1466467-176202/-/raw/main/images/%E4%B8%AD%E5%85%B4%E7%AC%A6%E5%8F%B7%E8%A1%A8.png?inline=false)</div>

这样的话就可以编译出可以和手机上特定环境相匹配的内核模块，但是我们自己编译的内核模块在安卓手机上始终insmod不成功，经查明，安卓的内核对我们的内核模块有白名单限制，为了解决这个问题，我们寻求了中兴的帮助，他们利用他们的环境帮我们重新编译了手机的内核和内核模块，最终内核模块可以成功地在手机上进行insmod操作。具体操作如下所示：

将安卓手机连接至虚拟机：

<div align='center'>![连接至虚拟机](https://gitlab.eduxiji.net/202311664111382/project1466467-176202/-/raw/main/images/%E8%BF%9E%E6%8E%A5%E8%87%B3%E8%99%9A%E6%8B%9F%E6%9C%BA.png?inline=false)</div>

在虚拟机终端利用 adb push 命令将已经编译好的 diagnose-tools 内核模块部分 diagnose.ko 复制到安卓系统的 /data/local/tmp 文件夹中：

<div align='center'>![内核模块复制到安卓系统](https://gitlab.eduxiji.net/202311664111382/project1466467-176202/-/raw/main/images/%E5%86%85%E6%A0%B8%E6%A8%A1%E5%9D%97%E5%A4%8D%E5%88%B6%E5%88%B0%E5%AE%89%E5%8D%93%E7%B3%BB%E7%BB%9F.png?inline=false)</div>

在虚拟机终端执行指令 adb shell 进入安卓系统终端界面，然后将 /data/local/tmp 文件夹中的 diagnose.ko 进行插入内核：

<div align='center'>![进入安卓系统](https://gitlab.eduxiji.net/202311664111382/project1466467-176202/-/raw/main/images/%E8%BF%9B%E5%85%A5%E5%AE%89%E5%8D%93%E7%B3%BB%E7%BB%9F.png?inline=false)</div>

查看是否已经插入成功，执行指令 ls dev | grep diagnose-tools：

<div align='center'>![插入成功](https://gitlab.eduxiji.net/202311664111382/project1466467-176202/-/raw/main/images/%E6%8F%92%E5%85%A5%E6%88%90%E5%8A%9F.png?inline=false)</div>

可以看到 dev 目录中已经存在 diagnose-tools，说明已经插入成功

## 用户空间移植

目前用户空间的移植还在开发中，敬请期待 ~

