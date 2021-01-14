# image-builder

## BeagleBone
Checkout this [documents](https://github.com/beagleboard/image-builder/blob/master/readme.md)
## NPi-imx6ull
    todo
## NPi-STM32MP1
    todo

# 一.说明
1. 当前文件是野火由img-builder项目中移植而来，用于编译arm soc的debian或者ubuntu的根文件系统
2. 在当前的编译系统当中主要使用了qemu 和debootstrap工具进行制作的，相关的配置图片如下：  
 ![image-builder的构成](https://github.com/mailonghua/ebf-image-builder/blob/master/PIC/image-builder_analyze.png)
3. image-builder的文件构成:  
  ![文件构成说明](https://github.com/mailonghua/ebf-image-builder/blob/master/PIC/ebf-image-builder_list.png)  
configs目录:主要是Debian文件系统的一些配置项，主要有:预安装软件列表、镜像源地址等。  
docs目录:主要是代理使用说明和debootstrap使用说明。  
publish目录:这个目录下存放着编译debian固件的起始脚本，是整个项目的编译入口。  
scripts目录:存放了一些执行特殊功能的脚本，在编译过程中会被调用。  
target目录:这里面放置的内容比较杂散，有启动参数的说明文档、systemd的启动服务和deb镜像源的密钥等等。  
tools目录:主要是打包脚本，完成把文件系统和uboot打包成img镜像的功能。  
Kernel目录:存放的是前面编译出来的内核安装包(linux-image-4.19.71-imx-r1_1stable_armhf.deb)。  
uboot目录:存放前面编译出来的uboot固件(u-boot-dtb.imx)。  
4. 整体流程就是a.下载相关的固件 b.下载debian相关的文件 c.使用debootstrap进行根文件系统的制作 d.将根文件系统进行挂载(这里需要qemu的虚拟出的arm环境) e.读挂载的文件夹进行相关软件的安装


# 二.野火移植过来后进行修改的内容总结
## 1.涉及的修改和添加的文件
### a. ./publish/seeed-imx-stable.sh，
该文件除了相关变量的检查外主要执行两个脚本./RootStock-NG.sh 和 * ./imxv7_setup_sdcard.sh *  
### b. imxv7_setup_sdcard.sh  
该脚本是有setup_sdcard.sh修改而来，主要修改地方下节分析  
### c. seeed-imx-debian-buster-console-v4.19.conf
配置文件的添加config/seeed-imx-debian-buster-console-v4.19.conf，该文件是指令debootstrap指令的输入参数，主要包含想要包含的软件的下载列表，以及不要安装的软件  
### d.tools/hwpack/imx6ull.conf 
该文件定义了关于bootloader的下载地址以及分区以及kernel的相关参数  
该文件定义了boot_name="u-boot.imx" ,若是定义了该文件那么在imxv7_setup_sdcard.sh中在处理uboot选型时会进行使用
## 2.关键文件修改的细节
### a.imxv7_setup_sdcard.s配置梳理
#### (1).关于uboot的名称的获取
在脚本中有两种方式获取uboot的名称，分别是网上下载和本地获取，为什么从网上获取呢，这是考虑到uboot会升级的情况
在默认的配置当中uboot名称是通过网上获取的，最终获取的名称和本地是存储的uboot名称是相同的u-boot-dtb.imx  
获取的网址：https://turmary.gitee.io/repos/bootloader/latest/bootloader-ng  
### b.imxv7_setup_sdcard.sh相比较setup_sdcard.sh
todo
# 三.移植使用
## 1.uboot和kernel的放置
将编译好的uboot(u-boot-dtb.imx)和kernel(ex:linux-image-4.19.71-imx-xxx.deb)分别放置到ebf-image-builder项目的uboot和Kernel目录中
## 2.修改seeed-imx-stable.sh
todo
## 3.添加相应arm soc的配置文件config/xxx.conf
todo
