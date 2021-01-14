# image-builder

## BeagleBone
Checkout this [documents](https://github.com/beagleboard/image-builder/blob/master/readme.md)
## NPi-imx6ull
    todo
## NPi-STM32MP1
    todo

# 一.说明
1.当前文件是野火由img-builder项目中移植而来，用于编译arm soc的debian或者ubuntu的根文件系统
2.在当前的编译系统当中主要使用了qemu 和debootstrap工具进行制作的，相关的配置图片如下：
3.整体流程就是a.下载相关的固件 b.下载debian相关的文件 c.使用debootstrap进行根文件系统的制作 d.将根文件系统进行挂载(这里需要qemu的虚拟出的arm环境) e.读挂载的文件夹进行相关软件的安装


# 二.野火移植过来后进行修改的内容总结
## 1.涉及的修改和添加的文件
a. ./publish/seeed-imx-stable.sh，该文件除了相关变量的检查外主要执行两个脚本./RootStock-NG.sh 和 * ./imxv7_setup_sdcard.sh *
b. imxv7_setup_sdcard.sh  该脚本是有setup_sdcard.sh修改而来，主要修改地方下节分析
c. 配置文件的添加config/seeed-imx-debian-buster-console-v4.19.conf，该文件是指令debootstrap指令的输入参数，主要包含想要包含的软件的下载列表，以及不要安装的软件
## 2.关键文件修改的细节
### imxv7_setup_sdcard.sh相比较setup_sdcard.sh
todo
# 三.移植使用
## 1.uboot和kernel的放置
将编译好的uboot(u-boot-dtb.imx)和kernel(ex:linux-image-4.19.71-imx-xxx.deb)分别放置到ebf-image-builder项目的uboot和Kernel目录中
## 2.修改seeed-imx-stable.sh
todo
## 3.添加相应arm soc的配置文件config/xxx.conf
todo
