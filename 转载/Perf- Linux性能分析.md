## 安装

```shell
sudo apt install linux-tools-common linux-tools-generic linux-tools-`uname -r`
```

## 使用

首先我们将程序编译成可执行文件，编译时记得加上 -g 参数，这样 perf 才能获取到函数名。
```shell
$ gcc sample.c -g -o sample
```

使用 perf 的 record 命令来记录程序的运行情况。
```shell
$ sudo perf record -g ./sample sleep 10
or
$ sudo perf record -a --call-graph dwarf -p `ps aux | grep "sample" | grep -v grep | cut -c 9-15` -d 1 -b

-a：表示对所有CPU采样
--call-graph dward：表示分析调用栈的关系
-p：表示分析指定的进程
```

运行上面的命令后，将会生成一个 perf.data 的文件，此文件记录了 sample 程序运行时的采样数据。

使用 perf 的 report 命令分析程序的运行情况。
```shell
$ sudo perf report -g
or 
$ sudo perf report -i perf.data > perf.txt
```

## perf 实现原理

来思考一下，如果让我们来设计一个统计程序中各个函数占用 CPU 时间的方案，应该如何设计？最简单的方案就是：在各个函数的开始记录当前时间，然后在函数执行结束后，使用当前时间减去函数开始执行时的时间，得到函数的执行时间总时长。如下伪代码：

```c
void func1()
{
    ...
}

void func2()
{
    ...
}

int main(int argc, char *argv[])
{
    int start_time, total_time;
  
    start_time = now();
    func1();
    total_time = now() - start_time;
    printf("func1() spent %d\n", total_time);

    start_time = now();
    func2();
    total_time = now() - start_time;
    printf("func2() spent %d\n", total_time);
}
```

虽然上述方式可以统计程序中各个函数的耗时情况，但却存在很多问题：

- 代码入侵度高。由于要对每个函数进行耗时记录，所以必须在调用函数前和调用函数后加入统计代码。
- 统计函数耗时，并不能反映该函数的真实 CPU 使用率。比如函数内部调用了导致进程休眠的系统调用（如sleep），这时函数实际上是不使用CPU的，但函数的耗时却统计了休眠的时间。
- 对性能影响较大。由于程序中所有函数都加入统计代码，所以对性能的影响是非常大的。

所以我们需要一个系统，它能够避免上述问题：

- 零代码入侵。
- 能够真实反映函数的 CPU 使用率。
- 对性能影响较小。

perf 就是为了解决上述问题而生的，我们先来介绍一下 perf 的原理。

### 采样

为了减小对程序性能的影响，perf 并不会在每个函数加入统计代码，取而代之的统计方式是：采样。

采样的原理是：设置一个定时器，当定时器触发时，查看当前进程正在执行的函数，然后记录下来。

### 排序

如果程序有成千上万的函数，那么采样出来的数据可能非常多，这个时候就需要对采样的数据进行排序。

为了对采样数据进行排序，perf 使用红黑树这种数据结构
