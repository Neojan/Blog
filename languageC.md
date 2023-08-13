## do while(0)

1. 适合宏控制；
2. 适合模块化代码；
3. 可以用break避免使用goto

## 大端小端

### 数组：从低地址往高地址存放

高低字节：buf =0x12345678, 靠左边是高位，靠右边是低位

小端：低字节存在内存的低地址。`buf[0] = 0x78`。Little Endian : x86、DEC、ARM

大端：低字节存在内存的高地址。同网络字节序，与正常读取顺序一致， `buf[0] = 0x12`。Big Endian : PowerPC、IBM、Sun

## _attribute__

`__attribute__` 可以设置函数属性（Function Attribute）、变量属性（Variable Attribute）和类型属性（Type Attribute）。

### weak weakref

- static __attribute__ ((weakref("test"))) void foo();

- weak表示弱（引用），如果test未定义，也可以编译通过。

- https://www.cnblogs.com/kernel_hcy/archive/2010/01/27/1657411.html

