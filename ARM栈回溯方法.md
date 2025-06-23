#define __save_lr(caller_address) __asm__ __volatile__ ("mov %0,lr\n": "=&r" (caller_address)) 

这行代码是一个 C 语言的宏定义，它使用了内联汇编（inline assembly）来获取函数的调用者地址。这通常用于 ARM 架构的处理器上。

*   `#define __save_lr(caller_address)`：定义了一个名为 `__save_lr` 的宏，它接受一个参数 `caller_address`。
*   `__asm__ __volatile__`：这是 GCC/Clang 编译器提供的关键字，用于在 C/C++ 代码中嵌入汇编指令。`__volatile__` 告诉编译器不要对这段汇编代码进行优化，确保它被执行。
*   `"mov %0,lr\n"`：这是核心的汇编指令。
    *   `mov` 是“移动”指令。
    *   `lr` 是 ARM 处理器中的“链接寄存器”（Link Register）。当一个函数被调用时，`lr` 寄存器会保存返回地址，也就是调用结束后应该回到哪里继续执行。因此，`lr` 的值在很多情况下可以认为是调用者（caller_address）的地址。
    *   `%0` 是一个占位符，代表第一个操作数，也就是 C 变量 `caller_address`。
    *   所以 `mov %0, lr` 的意思就是：将 `lr` 寄存器里的值，移动到与变量 `caller_address` 关联的寄存器中。
*   `: "=&r" (caller_address)`：这部分是用来告诉编译器如何将汇编代码和 C 代码的变量关联起来。
    *   `:` 分隔开了汇编指令和输出操作数。
    *   `"=&r"` 是对操作数的约束。
        *   `=` 表示这是一个输出操作数（汇编代码会修改它）。
        *   `&` 表示这个操作数会被提前修改，所以编译器不应该让它和任何输入操作数使用同一个寄存器。
        *   `r` 表示希望编译器为这个操作数分配一个通用寄存器。
    *   `(caller_address)` 指定了与这个操作数对应的 C 变量是 `caller_address`。


使用：

```c
void* __attribute__((weak)) __wrap_malloc(size_t size)
{
    int caller_address = 0;

    void* addr = __real_malloc(size);
    if (addr)
    {
        __save_lr(caller_address); // 将 lr 寄存器的值（即调用当前函数的指令的地址）存入 caller_address变量中。
        // 保存内存分配地址，大小，调用LR地址
        save_malloc_info(addr, size, &s_stHeapDebugInfo, caller);
    }

    return addr;
}
```
