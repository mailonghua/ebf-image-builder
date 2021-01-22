# 移植新的SOC需要配置文件
## 0.kernel和uboot的文件copy
 将编译好的uboot文件放置到image-builder/uboot下
 将编号的kernel文件放到image-builder/Kernel/下
## 1.根文件系统制作的<添加debootstrap的参数文件>
1. debootstrap 的输入参数

debootstrap工具是用来制作根文件系统的工具
如在野火的imu6x中执行的指令(从log中查看)
scripts/debootstrap.sh：
```shell
sudo debootstrap --no-merged-usr #表示不创建根文件系统下/{bin,sbin,lib}到usr的软连接
                 --arch=armhf     #设置当前的硬件的特性是硬件浮点还是armel，软件浮点(较老的soc)
                 --include=apt-transport-https,ca-certificates,connman,curl,dosfstools,gnupg,gnupg2,ifupdown,initramfs-tools,isc-dhcp-client,nano,net-tools,netcat,rsync,sudo,systemd,systemd-sysv,udhcpd,parted,mtd-utils,mmc-utils,rng-tools,haveged,wpasupplicant,udhcpc,openssh-server --exclude=aptitude,aptitude-common,groff-base,info,install-info,man-db,manpages,manpages-dev,tasksel,tasksel-data   
                                  #include中表示要下载的packages
                  --components=main,contrib,non-free    #使用存档中的软件包
                  --no-check-gpg   #禁用检查检索到的Release文件的gpg签名。
                  --foreign buster #仅执行引导的初始解压缩阶段
                  /opt/project/my_space/debian_transplant/ebf-image-builder/ignore/tmp.vZkZWfi4xa   #安装的路径 
                  http://mirrors.tuna.tsinghua.edu.cn/debian/    #镜像地址，也就是构建的debian是从网上clone下来的，然后根据参数进行修改
```
该段的程序主要是使用debootstrap构造debian 的根文件系统，那么debootstrap该主要的格式如下：

debootstrap --arch <ARCH> <VERSION> <DIRECTORY>  <MIRROR>
- ARCH
目標系統的 CPU 架構，常用的有 i386、amd64、armel、armhf 等。

- VERSION
Debian 的版本，你可以使用目前的穩定版本 wheezy ，或是 永遠的測試版 sid ，當然你也可以選擇更不穩定的 testing ，詳細版本名 稱請見 Debian 官網。

- DIRECTORY
安裝的目錄，這個根據自己的需求設定即可

- MIRROR
下載 Debian 套件的伺服器，通常選擇該使用者區域內的服務器，当前选用的是清华大学的服务器 http://mirrors.tuna.tsinghua.edu.cn/debian/

2. debootstrap的配置文件
    在image-builder/configs/xxx.conf下，如野火开发板的配置，根据soc进行修改调整seeed-imx-debian-buster-console-v4.19.conf  
3.当前需编译需要使用的debootstrap配置文件
    配置文件的指定，在seeed-imx-stable.sh文件中在执行build_and_upload_image函数前,如下
   ```shell
    config_name="seeed-imx-debian-buster-console-v4.19"
    build_and_upload_image  #调用脚本RootStock-NG.sh脚本继续进行处理
    ```    
        
## 2.内核的安装--修改安装的文件名称 <修改kernle的名称>
文件路径script/chroot.sh中
a.kernel的安装 L719行,如下
```shell
 install_pkgs () {                                                    
                                                                      
     if [ -f "/linux-image-4.19.71-imx-r1_1stable_armhf.deb" ] ; then 
         dpkg -i "/linux-image-4.19.71-imx-r1_1stable_armhf.deb"      
         rm -f /linux-image-4.19.71-imx-r1_1stable_armhf.deb          
     fi                                                              
```  
b.  LINE:1245 sudo cp "${OIB_DIR}/Kernel/linux-image-4.19.71-imx-r1_1stable_armhf.deb" "${tempdir}/linux-image-4.19.71-imx-r1_1stable_armhf.deb"                                           
其中linux-image-4.19.71-imx-r1_1stable_armhf.deb，安装的包名，就是在Kernel文件夹下存储的kernel文件名称为linux-image-4.19.71-imx-r1_1stable_armhf.deb
若是想要替换kernel文件需要对应修改这里.
说明：(1)中脚本是在我们要制作的根文件系统里执行的
        如何在我们制作的根文件系统中执行这个脚本呢？先通过chroot_mount函数，将根文件系统中的/proc   /dev/pts 进行挂载，挂载成虚拟文件系统proc和devpts形式
        然后执行sudo chroot "${tempdir}" /bin/bash -e chroot_script.sh 通过chroot切换根文件路径，然后执行脚本chroot_script
        这样就能在我们制作的根文件系统里进行软件的安装了
        可以参考[debootstrip](https://github.com/KingBing/blog-src/blob/master/%E4%BD%BF%E7%94%A8%20debootstrap%20%E5%BB%BA%E7%AB%8B%E5%AE%8C%E6%95%B4%E7%9A%84%20Debian%20%E7%B3%BB%E7%B5%B1.org)



