# Linux启动流程总的来说可以分成三个阶段

![image](https://github.com/Neojan/Blog/assets/13540636/488ebafa-9253-4f8a-9221-5db21fd3457f)

  

# Linux启动流程图

![image](https://github.com/Neojan/Blog/assets/13540636/3be602da-80a2-4ce7-95ef-9cb5631271f3)


# 第一步：上电

> Ø在 x86 系统中，将 1M 空间最上面的 0xF0000 到 0xFFFFF 这 64K 映射给 ROM。
> 
> Ø当电脑刚加电的时候，会做一些重置的工作，将 CS 设置为 0xFFFF，将 IP 设置为 0x0000，所以第一条指令就会指向 0xFFFF0，正是在 ROM 的范围内。
> 
> Ø在这里，有一个 JMP 命令会跳到 ROM 中做初始化工作的代码，于是，BIOS 开始进行初始化的工作

  
![image](https://github.com/Neojan/Blog/assets/13540636/fe506909-03c0-40a2-ba04-a7c32d34361d)


  

# 第二步：BIOS启动

> 固件初始化：计算机开机后，UEFI固件会进行初始化，包括硬件初始化、自检和加载UEFI固件驱动程序等。
> 
>   
> 
> 启动设备选择：UEFI固件会检测并识别可启动的设备，如硬盘、光盘、USB设备等。它会根据预设的启动顺序或用户设置的启动选项，选择一个可启动的设备作为启动介质。
> 
>   
> 
> UEFI固件驱动程序加载：UEFI固件会加载设备上的UEFI固件驱动程序，这些驱动程序负责与硬件设备进行交互，以便后续的启动过程能够正常进行。
> 
>   
> 
> UEFI应用程序加载：UEFI固件会加载位于启动介质上的UEFI应用程序，如引导加载程序（Bootloader）或操作系统的引导管理器。这些应用程序通常位于EFI系统分区中，以.efi文件格式存在。
> 
>   
> 
> 引导加载程序执行：加载的引导加载程序会接管控制权，负责加载操作系统内核或其他引导组件。常见的引导加载程序有UEFI Shell、GRUB、rEFInd等。

![image](https://github.com/Neojan/Blog/assets/13540636/936ce946-7a25-4413-b14f-a6ed386c949f)

  

# 第三步：Linux启动

![image](https://github.com/Neojan/Blog/assets/13540636/f579f623-ccb9-457d-a61b-68abdd7f77f5)


  

# Linux启动-引导

> Ø我们可以通过BIOS界面选择硬盘启动项进入OS，那BIOS是怎么发现这个硬盘里有OS？
> 
> Ø答案就是MBR（Master Boot Record），
> 
> ØMBR是放在硬盘的第一个扇区，一共512字节，
> 
> Ø可以分成两部分：
> 
> Ø主引导记录：安装启动引导程序的地方，446字节，
> 
> Ø分区表：记录整个硬盘分区的的状态此外，64字节

  

# Linux启动-引导EBR/VBR

> Ø找到MBR后下一步做啥？
> 
>   
> 
> Ø(1)如果查找分区表时发现操作系统装在主分区，然后执行已载入的MBR中的boot loader代码，加载该激活主分区的VBR中的boot loader，至此，控制权就交给了VBR的boot loader
> 
>   
> 
> Ø(2)如果操作系统不是装在主分区，那么肯定是装在逻辑分区中，所以查找完主分区表后会继续查找扩展分区表，直到找到EBR所在的分区，然后MBR中的boot loader将控制权交给该EBR的boot loader
> 
>   

![image](https://github.com/Neojan/Blog/assets/13540636/a83f30aa-3f98-4f9c-80c3-4a5ad0ae6be2)


  

# Linux启动-引导GRUB2介绍

> ØGNU GRUB（GRand Unified Bootloader简称“GRUB”）是一个来自GNU项目的多操作系统启动程序。GRUB是多启动规范的实现，它允许用户可以在计算机内同时拥有多个操作系统，并在计算机启动时选择希望运行的操作系统。GRUB可用于选择操作系统分区上的不同内核，也可用于向这些内核传递启动参数。
> 
>   
> 
> Ø生成配置文件：grub2-mkconfig -o /boot/grub2/grub.cfg
> 
> Ø安装：grub2-install /dev/sda

  

# Linux启动-引导GRUB2加载

> ØGrub2 第一个安装的就是 boot.img，BIOS 完成任务后，会将 boot.img 从硬盘加载到内存中的 0x7c00 来运行。boot.img会加载 core.img。如果从硬盘启动的话，这个扇区里面是 diskboot.img，diskboot.img 的任务就是将 core.img 的其他部分加载进来，先是解压缩程序 lzma_decompress.img，再往下是 kernel.img，最后是各个模块 module 对应的映像。这里需要注意，它不是 Linux 的内核，而是 grub 的内核。
> 
>   
> 
> Ø随着我们加载的东西越来越大，实模式这 1M 的地址空间实在放不下了，所以在真正的解压缩之前，lzma_decompress.img 做了一个重要的决定，就是调用 real_to_prot，切换到保护模式，这样就能在更大的寻址空间里面，加载更多的东西。

  

![image](https://github.com/Neojan/Blog/assets/13540636/5a54b75b-52ea-4618-be7c-b4399f42a47f)


  

# Linux启动-0/1号进程

> Øset_task_stack_end_magic(&init_task)。这里面有一个参数 init_task，它的定义是 struct task_struct init_task = INIT_TASK(init_task)。
> 
> Ø它是系统创建的第一个进程，我们称为 0 号进程。这是唯一一个没有通过 fork 或者 kernel_thread 产生的进程，是进程列表的第一个。
> 
>   
> 
> Ø1.trap_init()中断初始化
> 
> Ø2.mm_init()内存初始化
> 
> Ø3.sched_init()调度策略初始化
> 
> Ø4.vfs_caches_init()基于内存文件系统rootfs初始化
> 
> Ø5.start_kernel()->rest_init()其他方面的初始化
> 
> Ørest_init 的第一大工作是，用 kernel_thread(kernel_init, NULL, CLONE_FS) 创建第二个进程，这个是 1 号进程。1 号进程对于操作系统来讲，有“划时代”的意义。

  

# Linux启动-ramdisk

> Øinit 程序是在文件系统上的，文件系统一定是在一个存储设备上的，例如硬盘。Linux 访问存储设备，要有驱动才能访问。如果存储系统数目很有限，那驱动可以直接放到内核里面，但是文件系统的格式有很多，全都放进内核那内核就太大了。
> 
> Ø这该怎么办呢？
> 
> Ø我们只好先弄一个基于内存的文件系统。内存访问是不需要驱动的，这个就是 ramdisk。这个时候，ramdisk 是根文件系统。
> 
> Ø运行 ramdisk 上的 /init，等它运行完了就已经在用户态了。/init 这个程序会先根据存储系统的类型加载驱动，有了驱动就可以设置真正的根文件系统了。有了真正的根文件系统，ramdisk 上的 /init 会启动文件系统上的 init。

  

# Linux启动-init介绍

> Ø前面0/1进程都属于内核线程，ps pid=1的是init进程
> 
> Ø if (ramdisk_execute_command) {
> 
> Øret = run_init_process(ramdisk_execute_command);
> 
> Ø...... }
> 
> Ø......
> 
> Øif (!try_to_run_init_process("/sbin/init") ||
> 
> Ø!try_to_run_init_process("/etc/init") ||
> 
> Ø !try_to_run_init_process("/bin/init") ||
> 
> Ø!try_to_run_init_process("/bin/sh")) return 0
> 
> Ø它会尝试运行 ramdisk 的“/init”，或者普通文件系统上的“/sbin/init”“/etc/init”“/bin/init”“/bin/sh”。不同版本的 Linux 会选择不同的文件启动，但是只要有一个起来了就可以。
> 
>   
> 
> ØInit类型：
> 
> ØSysV:CentOS 5之前, 配置文件/etc/inittab
> 
> ØUpstart:CentOS 6，配置文件/etc/inittab,/etc/init/*.conf
> 
> ØSystemd:CnetOS7, 配置文件/usr/lib/system/syste,/etc/systemd/system

  

# Linux启动-运行级别

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/icRxcMBeJfc8YwNQMe2NmzibaFRagn26Tibnk1n8I2iajm9QsALl9IISamTrCI34kNlJ4vRL54Cl2vQk0VbbDgxLkw/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

  

  

# Linux启动-fstab

> Ø任何硬件设备连接后，操作系统使用硬件，即需要挂载。windows只不过是自动“挂载”了，linux需要手动自己搞。在Linux系统下，例如每次挂载/dev/sdb1（例如U盘设备文件）需要手动使用命令mount。当然，每次重启，开启时，硬盘一般也是被自动挂载的，而自动挂载的信息，就记录在/etc/fstab文件中。
> 
> Ø 系统每次启动都会读取/etc/fstab中的配置内容，自动挂载该文件中被记录的设备和分区。
> 
> Ø 第一列：设备文件或UUID或label（三者的区别看下面）
> 
> Ø 第二列：设备的挂载点（空目录）
> 
> Ø 第三列：该分区文件系统的格式（可以使用特殊的参数auto，自动识别分区的分区格式）
> 
> Ø 第四列：文件系统的参数，设置格式的选项
> 
> Ø 第五列：dump备份的设置（0表示不进行dump备份，1代表每天进行dump备份，2代表不定日期的进行dump备份）
> 
> Ø 第六列：磁盘检查设置（其实是一个检查顺序，0代表不检查，1代表第一个检查，2后续.一般根目录是1，数字相同则同时检查）

  

# Linux启动-用户登录

> 一般来说：用户登录方式有三种
> 
> 1.命令行登录
> 
> 2.ssh登录
> 
> 3.图形登录
> 
>   
> 
> Linux是多任务多用户的操作系统，它允许多人同时在线工作。但每个人都必须要输入用户名和密码才能验证身份并最终登录。但登陆时是以图形界面的方式给用户使用，还是以纯命令行模式给用户使用呢？这是终端决定的，也就是说在登录前需要先加载终端。
> 
>   
> 
> 现代Linux上，console终端已经和原始的意义不太一样了，其设备映射在/dev/console上，所有内核输出的信息都输出到console终端，而其他用户程序输出的信息则输出到虚拟终端或伪终端。
> 
>   
> 
> 总结下：
> 
> /dev/console：控制台终端
> 
> /dev/ttyN：虚拟终端，ctrl+alt+f[1-6]切换的就是虚拟终端
> 
> /dev/ttySN：串行终端
> 
> /dev/pts/N：伪终端，ssh等工具连接过去的活着图形终端下开启的命令行终端就是伪终端。

  

# Linux启动-用户切换

> Linux预设提供了六个命令窗口终端机让我们来登录。
> 
>   
> 
> 默认我们登录的就是第一个窗口，也就是tty1，这个六个窗口分别为tty1,tty2 … tty6，你可以按下Ctrl + Alt + F1 ~ F6 来切换它们
> 
> Ø
> 
> Ø针对我的VM Virtual BOX ctrl+alt + F1是图形终端， ctrl+alt + F2~F6命令行终端

  

# Linux启动流程思维导图

![image](https://github.com/Neojan/Blog/assets/13540636/5acbfad7-1e53-46d7-9535-383ebb8e9e39)

