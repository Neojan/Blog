## svn 命令行

### log 
`svn log | head -n X(x 为行数)  日志`
### commit

`svn commit  -m "[CHG]XXX"  `你想要添加的文件相对路径，多个中间用空格隔开  提交，省略文件表示全部

### update

`svn update -r 2105` 更新到某一版本

### checkout

svn导出目录：`svn co 目录路径 指定版本 -r `
### merge

- `svn merge url -c xxxx ./ --dry-run`  将url指定的code的xxxx版本，merge到本地； --dry-run 来模拟merge的操作
- `svn merge url -r xxxx:yyyy ./ `将url指定的code的xxxx版本到yyyy版本，merge到本地（注意：该方式不包括xxxx版本！！）
`svn merge https://192.0.0.183/IBP/VIS/VIS_ARM/Custom/Overseas/2018/PJ_XXX/APPS -c 100463 ./`

### svn添加文件可执行权限

`svn propset svn:executable on filename`, `svn commit`

### 一次性从svn库删除/添加文件

`svn st | awk '{if ($1 == "?") {print $2} }' | xargs svn add`
`svn st | awk '{if ($1 == "!") {print $2}}' | xargs svn rm `

## vim

### 常用vim命令

- yy 拷贝当前行
- nyy 拷贝当前后开始的n行，比如2yy拷贝当前行及其下一行。
- p  在当前光标后粘贴,如果之前使用了yy命令来复制一行，那么就在当前行的下一行粘贴。
- 正常模式下按v（逐字）或V（逐行）进入可视模式，然后用jklh命令移动即可选择某些行或字符，再按y即可复制
- x 删除当前字符
- dd 删除当前行
- 10d 删除当前行开始的10行。
- 撤销（Undo）
- 撤销对整行的操作
-  Ctrl + r 重做（Redo），即撤销的撤销


### 移动

- h 左移一个字符
- l 右移一个字符，这个命令很少用，一般用w代替。
- k 上移一个字符
- j 下移一个字符

以上四个命令可以配合数字使用，比如20j就是向下移动20行，5h就是向左移动5个字符，在Vim中，很多命令都可以配合数字使用，比如删除10个字符10x，在当前位置后插入3个！，`3a！<Esc>`，这里的Esc是必须的，否则命令不生效。

- w 向前移动一个单词（光标停在单词首部），如果已到行尾，则转至下一行行首。此命令快，可以代替l命令。
- b 向后移动一个单词 2b 向后移动2个单词
- gg 移动到文件头。 等于 `[[ `
- G（shift + g） 移动到文件尾。 等于` ]] `
- 冒号+行号，回车，跳到指定行，比如跳到240行就是 :240回车。另一个方法是行号+G，比如230G跳到230行。
- Ctrl + e 向下滚动一行
- Ctrl + y 向上滚动一行
- Ctrl + d 向下滚动半屏
- Ctrl + u 向上滚动半屏
- Ctrl + f 向下滚动一屏
- Ctrl + b 向上滚动一屏

### 替换
`%s/old/new/g` 用old替换new，替换整个文件的所有匹配

### 查找
`/text`查找text，按n健查找下一个，按N健查找前一个。
`?text`查找text，反向查找，按n健查找下一个，按N健查找前一个。

## apt-get源

### https://blog.csdn.net/rchm8519/article/details/48358249

## tar

-c或--create 建立新的备份文件，压缩

-v或--verbose 显示指令执行过程。

-x或--extract或--get 从备份文件中还原文件。解压。

-z或--gzip或--ungzip 通过gzip指令处理备份文件。有gzip属性的。

-f: 使用档案名字，切记，这个参数是最后一个参数，后面只能接档案名。

### 解压

`tar  -zxvf   test.tar.gz`
`tar -zxvf test.tar.gz`
`tar  -xf test.tar.lzma`

### 压缩

`tar  -czvf   test.tar.gz   testDirec`
`tar -cf test.tar testDirec`
`lzma -f test.tar`

### gz

解压1：`gunzip FileName.gz`
解压2：`gzip -d FileName.gz`

## readelf

readelf命令，一般用于查看ELF格式的文件信息，常见的文件如在Linux上的可执行文件，动态库(*.so)或者静态库(*.a) 等包含ELF格式的文件。
-a , --all 显示全部信息,等价于 -h -l -S -s -r -d -V -A -I 。
-h , --file-header 显示 elf 文件开始的文件头信息.
-l , --program-headers , --segments 显示程序头（段头）信息(如果有的话)。
-S , --section-headers , --sections 显示节头信息(如果有的话)。
-g , --section-groups 显示节组信息(如果有的话)。
-t , --section-details 显示节的详细信息( -S 的)。
-s , --syms , --symbols 显示符号表段中的项（如果有的话）。
-e , --headers 显示全部头信息，等价于: -h -l -S
-n , --notes 显示 note 段（内核注释）的信息。
-r , --relocs 显示可重定位段的信息。
-u , --unwind 显示 unwind 段信息。当前只支持 IA64 ELF 的 unwind 段信息。
-d , --dynamic 显示动态段的信息。
-V , --version-info 显示版本段的信息。
-A , --arch-specific 显示 CPU 构架信息。
-D , --use-dynamic 使用动态段中的符号表显示符号，而不是使用符号段。
-x , --hex-dump= 以16进制方式显示指定段内内容。 number 指定段表中段的索引,或字符串指定文件中的段名。
-w[liaprmfFsoR] or –debug-dump[=line,=info,=abbrev,=pubnames,=aranges,=macro,=frames,=frames-interp,=str,=loc,=Ranges] 显示调试段中指定的内容。
-I , --histogram 显示符号的时候，显示 bucket list 长度的柱状图。
-v , --version 显示 readelf 的版本信息。
-H , --help 显示 readelf 所支持的命令行选项。
-W , --wide 宽行输出。

`readelf x.so -a | grep xfunc`
`readelf x -S | grep debug` 查看是否有debug信息

## nm

 `nm -D x.so`
- A    Global absolute 符号。
- a    Local absolute 符号。
- B    Global bss 符号。
- b    Local bss 符号。
- C    Common symbol，未初始化数据段，该符号没有包含于一个普通section中。只有在链接过程中才进行分配。符号的值表示该符号需要的字节数。例如在一个c文件中，定义int test，并且该符号在别的地方会被引用，则该符号类型即为C。否则其类型为B
- D    Global data 符号。
- d    Local data 符号。
- f    源文件名称符号。
- T    Global text 符号。
- t-     Local text 符号。
- U    未定义符号。 absolute符号的值是绝对值，并且在进一步链接过程中不会被改变

`nm -A *.so | grep 函数名`：查看存在符号表的动态库

查看.data 节数据 : `nm --format=sysv yourlib | grep -w .data`

查看.bss 节数据 : `nm --format=sysv yourlib | grep -w .bss`

[nm(1) — Linux manual page](https://www.man7.org/linux/man-pages/man1/nm.1.html)

## gcc

关联头文件：gcc -I../include/ test.c

gcc -static gjobread.c -Wl,-rpath, ../home/lib/xx.a

## iconv

`iconv -f utf-8 -t gbk diff1 -o dif `

## indent 规范代码

indent -kr -i8 main.c

## 查找grep，sed，awk

### grep

查找文件 `grep --color=auto "xxx" . -nr`
`grep --color=auto "xxx" filename`
`grep -o '\"\w\{1,\}\"'`
-o输出匹配字符串而不是一整行

[Linux grep 命令](https://www.runoob.com/linux/linux-comm-grep.html)
### find

find . -name filename 

查找并删除
```shell
`find ./ -name filename -exec rm -rf {}`
`find ./ -name "*.d" | xargs rm -rf ` #查找文件并删除
```

### sed文本替换

查找xxx，并用aaa替换
```shell
sed -i "s/\bold\b/new/g" `grep old -rl . -nr`  #b为边界匹配
```
### awk

```shell
awk '/要筛选的字符串/{print}' 要筛选的文件
awk -F '/' '{print $2}' 以/为分割
```

## 正则表达式

```shell
#判断字符串是否是IP地址
^((2(5[0-5]|[0-4]\d))|[0-1]?\d{1,2})(\.((2(5[0-5]|[0-4]\d))|[0-1]?\d{1,2})){3}$
# 匹配任意字符
(.*)
#匹配 pattern 但不获取匹配结果，也就是说这是一个非获取匹配，不进行存储供以后使用
(?:) 
```



