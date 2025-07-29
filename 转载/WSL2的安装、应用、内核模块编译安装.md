#### WSL2的安装、应用、内核模块编译安装

- [WSL安装、升级](#WSL_1)
- [常用命令](#_14)
- [WSL导入导出](#WSL_121)
- [其他 - 图形界面、虚拟化](#___157)
- [内核模块编译安装](#_163)
- - [下载对应源码](#_164)
    - [安装依赖项](#_172)
    - [编译源码及安装](#_178)
    - [参考：](#_246)

## WSL安装、升级

开启WSL参考如下

[win10上linux子系统的开启、升级及使用；](https://blog.csdn.net/helaisun/article/details/80712287)  

更详细的参考，windows官网参考文档，包含wsl安装使用文档。  
[适用于 Linux 的 Windows 子系统文档；](https://learn.microsoft.com/zh-cn/windows/wsl/)

可以通过命令`wsl -l -v`查看WSL版本。

```
# 查看版本
> wsl -l -v
  NAME            STATE           VERSION
* Ubuntu-24.04    Running         2
> wsl --list
适用于 Linux 的 Windows 子系统分发:
Ubuntu-24.04 (默认值)

# 关闭Ubuntu子系统
wsl --terminate Ubuntu
wsl -t Ubuntu
# 关闭WSL
wsl --shutdown
# 启动WSL
wsl
```

具体更多详细的命令可以参考windows官网的文档：[WSL 的基本命令。](https://learn.microsoft.com/zh-cn/windows/wsl/basic-commands)

当然，也可以利用`wsl --help`查看WSL支持的命令。


## WSL导入导出

WSL的导入导出及迁移到其他安装目录的工作，当然也可以打包之后在其他电脑上安装，这样，保证每个WSL环境都和分发版一致，对于团队工作有益。参考：  
[《手把手教你在Windows下用WSL运行Vitis/Vivado/Petalinux》](https://mp.weixin.qq.com/s/aBQcrIpEFl2jCXdzk3ruzA)

1. 第一步，首先查看所有分发版本：
    
    ```
    wsl-l -v
    ```
    
2. 第二步，导出分发版为tar文件到D盘：
    
    ```
    wsl--export Ubuntu-20.04 d:\ubuntu20.04.tar
    ```
    
3. 第三步，注销当前分发版：
    
    ```
    wsl--unregister Ubuntu-20.04
    ```
    
4. 重新导入并安装分发版在d:\ubuntu (注意我们这里都是WSL2啦)；
    
    ```
    wsl--import Ubuntu-20.04 d:\ubuntu2004 d:\ubuntu20.04.tar
    ```
    
5. 设置默认登录用户为安装时用户名 (这里的Username替换成您之前的用户名)；
    
    ```
    Ubuntu2004config --default-user Username
    ```
    
6. 删除tar文件(可选)：
    
    ```
    deld:\ubuntu20.04.tar
    ```
    

有需要扩容的，可以参考微软给的结果：[扩展 WSL 2 虚拟硬盘的大小。](https://learn.microsoft.com/zh-cn/windows/wsl/vhd-size)

## 其他 - 图形界面、虚拟化

参考：  
[Windows 下 Linux子系统 WSL 2 开启 KVM 安装 Ubuntu；](https://www.bilibili.com/video/BV1Xz411e7en/?from=seopage&vd_source=877ddb78285dde4465e4525527e8cc04)  
[WSL2 + Ubuntu + 图形界面安装；](https://www.bilibili.com/read/cv11143517)

## 内核模块编译安装

### 下载对应源码

查看WSL2版本：

```
❯ uname -r
6.6.87.2-microsoft-standard-WSL2
```

版本替换成实际版本

[下载源码](https://codeload.github.com/microsoft/WSL2-Linux-Kernel/tar.gz/refs/tags/linux-msft-wsl-6.6.87.2)

### 安装依赖项

```
sudo apt install build-essential flex bison dwarves libssl-dev libelf-dev cpio
sudo apt-get install bc pkg-config
```

### 编译源码及安装

1. **将下载的代码压缩包拷贝到`/home`目录**
    
    ```
    cp WSL2-Linux-Kernel-linux-msft-wsl-6.6.87.2.tar.gz /home/linuxkernel/
    ```
    
    不能在`/mnt/`下的目录中编译，否则`make modules` 时会报错：
    
    此外还会遇到因为windows系统不区分大小写，导致内核编译出现ipt_ECN.h: No such file or director错误。
    
2. **解压缩**  
    在`/home/linuxkernel/`目录执行
    
    ```
    tar -zxvf WSL2-Linux-Kernel-linux-msft-wsl-6.6.87.2.tar.gz 
    ```
    
    之后进入内核源码目录 `/home/linuxkernel/WSL2-Linux-Kernel-linux-msft-wsl-6.6.87.2`  
    执行
    
3. **编译并查看模块的安装情况**
    
    ```
    make KCONFIG_CONFIG=Microsoft/config-wsl -j8
    make KCONFIG_CONFIG=Microsoft/config-wsl modules_install -j8
    ```
    
4. **回到源码目录`/home/linuxkernel/WSL2-Linux-Kernel-linux-msft-wsl-6.6.87.2`安装headers**
    
    ```
    sudo make headers_install ARCH=x86_64 INSTALL_HDR_PATH=/usr
    ```
    
    安装之后就可以正常导入模块了。
    
    ```
    sudo insmod hello.ko  //插入模块
    sudo rmmode hello // 卸载模块
    modinfo hello.ko // 查看模块信息
    lsmod //查看系统模块
    dmesg // 查看系统日志信息
    ```
    
    ```
    > insmod hello.ko
    > dmesg -c
    [121262.044096] Hello World exit
    ```
    
### 参考：

[WSL升级到最新版本Linux内核headers的方法；](https://zhuanlan.zhihu.com/p/557839637)  
[WSL 运行make提示/lib/modules/xxx/build: No such file or directory. Stop.错误解决办法；](https://blog.csdn.net/weixin_45668903/article/details/128019077#%E9%94%99%E8%AF%AF%E5%8E%9F%E5%9B%A0)  
[wsl2 内核编译；](https://zhuanlan.zhihu.com/p/540597304)  
[如何让WSL2使用自己编译的内核；](https://zhuanlan.zhihu.com/p/324530180)
