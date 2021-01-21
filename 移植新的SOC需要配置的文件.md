# 移植新的SOC需要配置文件
## 跟文件系统制作的
1. debootstrap 的输入参数
debootstrap工具是用来制作根文件系统的工具
如在野火的imu6x中执行的指令(从log中查看)
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
