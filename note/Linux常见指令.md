# 一、增：新建目录和文件

在操作之前先要理解Linux的**文件系统的树状目录：**所有文件和目录以根目录`/`为起点，形成层级树状结构，普通文件和空目录是叶子节点，目录是分支节点（包含子目录或文件）

```bash
[root@VM-0-11-centos ~]# tree -L 1 /
/
|-- bin -> usr/bin
|-- boot
|-- data
|-- dev
|-- etc
|-- home
|-- lib -> usr/lib
|-- lib64 -> usr/lib64
|-- lost+found
|-- media
|-- mnt
|-- opt
|-- proc
|-- root
|-- run
|-- sbin -> usr/sbin
|-- srv
|-- sys
|-- tmp
|-- usr
`-- var

21 directories, 0 files
[root@VM-0-11-centos ~]# 
```

用这个方式就可以查看Linux根目录下的一级目录，如下图所示：

![目录树](E:\CODE\图\画图截图\目录树.png)

## 1. 创建目录：mkdir

**目录的递归创建：**若需要创建多级嵌套目录(如“`a/b/c`)直接使用`mkdir a/b/c`会报错（因父目录`a`和`a/b`不存在），需要通过`-p`选项自动创建所有缺失的父目录。

**语法和选项**

>  `mkdir [选项] 目录名...`

| **选项** | **说明**         |
| -------- | ---------------- |
| `-p`     | 递归创建多级目录 |
| 无选项   | 仅创建单级空目录 |

**案例**

```bash
# 1. 创建单级目录
[root@VM-0-11-centos ~]# mkdir path1
[root@VM-0-11-centos ~]# ls
path1

# 2. 递归创建多级目录
[root@VM-0-11-centos ~]# mkdir -p path1/path2/path3
[root@VM-0-11-centos ~]# tree
.
`-- path1
    `-- path2
        `-- path3

3 directories, 0 files
```

## 2.创建文件：touch

**概念铺垫：文件 = 文件属性 + 文件内容**

这是一个文件属性的具体信息：

![文件属性](E:\CODE\图\画图截图\文件属性.png)

对于**文件的时间属性：**Linux有三个关键时间戳，`touch`指令的核心作用是修改这些时间戳，如果文件不存在自动创建空文件

**时间戳：**是一种**时间存储标准**，指从「1970 年 1 月 1 日 00:00:00 UTC」（Unix 纪元时间）到当前时间的「总秒数」（不含闰秒），比如 `1730000000` 代表某个具体时间。

```bash
[root@VM-0-11-centos ~]# stat path1
  File: ‘path1’
  Size: 4096      	Blocks: 8          IO Block: 4096   directory
Device: fd01h/64769d	Inode: 919083      Links: 3
Access: (0755/drwxr-xr-x)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2025-11-30 15:56:58.553850382 +0800			# 访问时间
Modify: 2025-11-30 15:56:56.327782460 +0800			# 修改时间
Change: 2025-11-30 15:56:56.327782460 +0800			# 变化时间
 Birth: -

```

**语法：**

> `touch [选项] 文件名...`

| **选项** | **说明**                                                     |
| -------- | ------------------------------------------------------------ |
| `-a`     | 仅修改文件的访问时间（atime）                                |
| `-m`     | 仅修改文件的修改时间（mtime）                                |
| 无选项   | 若文件不存在则创建空文件，若存在则更新atime和mtime为当前时间 |

```bash
[root@VM-0-11-centos path1]# touch code.c
[root@VM-0-11-centos path1]# ls -l
total 4
-rw-r--r-- 1 root root    0 Nov 30 16:15 code.c
-rw-r--r-- 1 root root    0 Nov 30 16:14 code.txt
drwxr-xr-x 3 root root 4096 Nov 30 15:56 path2
[root@VM-0-11-centos path1]# touch code.txt
[root@VM-0-11-centos path1]# ls -l
total 4
-rw-r--r-- 1 root root    0 Nov 30 16:15 code.c
-rw-r--r-- 1 root root    0 Nov 30 16:15 code.txt
drwxr-xr-x 3 root root 4096 Nov 30 15:56 path2
```

## 3. 文件写入与读取：echo printf cat tac

这里需要一个新的认知：**Linux下一切皆文件！！！**

类比C++：

> `cout`：在显示器上打印文本，即向显示器文件写入数据
>
> `cin`：键盘接收输入数据，即从键盘文件读取数据

而**重定向：**

1. 输出重定向：`>`表示**覆盖写入**，`>>`表示**追加写入**
2. 输入重定向：`<`表示**读文件当输入**

**案例：（请看详细注释）**

```bash
# 向显示器写入文本
[root@VM-0-11-centos path1]# echo "123456"
123456
# echo和printf的唯一区别就是一个有换行一个没有
[root@VM-0-11-centos path1]# printf "132456"
132456[root@VM-0-11-centos path1]# 
# >输出重定向 echo原本要将"hello Linux"输出到显示屏文件 但是写入到了文件中
# 若文件存在 则删除原先内容 从头开始写入
# 若文件不存在 则新建文件并写入
132456[root@VM-0-11-centos path1]# tree
.
|-- code.c
|-- code.txt
`-- path2
    `-- path3

2 directories, 2 files
[root@VM-0-11-centos path1]# pwd
/root/path1
[root@VM-0-11-centos path1]# echo "hello Linux" > code.txt
[root@VM-0-11-centos path1]# tree
.
|-- code.c
|-- code.txt
`-- path2
    `-- path3

2 directories, 2 files
[root@VM-0-11-centos path1]# cat code.txt
hello Linux

# 原文件存在 删除原来的内容 从头写入
[root@VM-0-11-centos path1]# echo "123456" > code.txt
[root@VM-0-11-centos path1]# cat code.txt
123456
# 追加写入
[root@VM-0-11-centos path1]# echo "7897978798" >> code.txt
[root@VM-0-11-centos path1]# cat code.txt
123456
7897978798
# 带行号显示文件内容
[root@VM-0-11-centos path1]# cat -n code.txt
     1	123456
     2	7897978798
# 逆序显示
[root@VM-0-11-centos path1]# tac code.txt
7897978798
123456

[root@VM-0-11-centos path1]# > code.txt
[root@VM-0-11-centos path1]# cat code.txt
[root@VM-0-11-centos path1]# > show.txt
[root@VM-0-11-centos path1]# tree
.
|-- code.c
|-- code.txt
|-- path2
|   `-- path3
`-- show.txt

2 directories, 3 files
[root@VM-0-11-centos path1]# 

```

这里就可以总结出`>`新的用法：

> * 若原文件存在，清空文件
> * 若原文件不存在，新建文件

****

# 二、 删：删除目录和文件

## 1. rm

这里需要注意：Linux系统并没有回收站，文件删除就真的找不到了，所以删除文件之前要三思

* **文件与目录的删除差异**：`rm`默认仅能删除文件，删除目录需加`-r`选项（递归删除目录内所有内容）；若需强制删除（跳过确认提示），需加`-f`选项（强制删除，忽略不存在的文件和权限提示）。
* **安全提示**：`rm -rf /`是极其危险的指令（会删除根目录下所有文件，导致系统崩溃），绝对禁止使用。

**语法：**`rm [选项] 文件名/目录名...`

| **选项** | **说明**                                   |
| -------- | ------------------------------------------ |
| `-r`     | 递归删除目录（包含目录内所有文件和子目录） |
| `-f`     | 强制删除（不做任何提示）                   |

**案例：**

```bash
[root@VM-0-11-centos path1]# tree
.
|-- code.c
|-- code.txt
|-- path2
|   `-- path3
`-- show.txt

2 directories, 3 files
# 删除单个文件 会有提示
[root@VM-0-11-centos path1]# rm code.c
rm: remove regular empty file ‘code.c’? y
[root@VM-0-11-centos path1]# ls
code.txt  path2  show.txt
# 强制删除单个文件
[root@VM-0-11-centos path1]# rm -f show.txt
[root@VM-0-11-centos path1]# ls
code.txt  path2
# 强制递归删除目录
[root@VM-0-11-centos path1]# rm -rf path2
[root@VM-0-11-centos path1]# ls
code.txt
```

## 2. rmdir

仅删除**空目录**，没有`rm -r`灵活

**语法： `rmdir [选项] 目录名...`**

```bash
[root@VM-0-11-centos ~]# mkdir -p show1/show2/show3
[root@VM-0-11-centos ~]# tree
.
|-- path1
|   `-- code.txt
`-- show1
    `-- show2
        `-- show3

4 directories, 1 file
[root@VM-0-11-centos ~]# cd /root/show1/show2
[root@VM-0-11-centos show2]# pwd
/root/show1/show2
[root@VM-0-11-centos show2]# > code.txt | > code.c
[root@VM-0-11-centos show2]# ls
code.c  code.txt  show3

[root@VM-0-11-centos ~]# rmdir -p show1/show2/show3
# 很鸡肋 别用
rmdir: failed to remove directory ‘show1/show2’: Directory not empty
[root@VM-0-11-centos ~]# 

```



****

# 三、 查：获取文件/目录信息

这个操作涵盖了**查看目录内容、文件内容、文件属性、系统信息**等场景，需要结合**路径概念**和**管道操作**

## 1.相关概念

### 家目录

家目录是 Linux 为每个用户分配的「专属私人文件夹」，核心作用是让用户存放自己的文件、配置和数据，且每个用户默认只能修改自己家目录的内容（保证隐私和安全）。

> * **普通用户**：家目录统一放在 `/home` 目录下，名字和用户名一致，比如用户 `whb` 的家目录是 `/home/whb`。
> * **root 用户（超级管理员）**：家目录单独在 `/root`（不跟普通用户混放，因为权限最高）。
> * `cd ~`：快速进入家目录

###  路径：定位文件/目录的唯一方式

> * **绝对路径：**从根目录`/`开始的的完整路径，如：`/root/path1/code.txt`，唯一性强，不受当前目录的影响，一般用于配置文件和脚本
> * **相对路径：**相对于当前所处位置的路径（比如说我当前在`/root/path1`,则`/path1/code.txt`等价于`/root/path1/code.txt`），便捷性高，常用于命令行交互
> * **特殊符号：** `./`当前目录 `../`上级目录 `~`当前用户的家目录（如`~`对普通用户是`/home/用户名`，对 root 是`/root`）

**为什么路径是定位文件/目录的唯一方式？**

![路径唯一](E:\CODE\图\画图截图\路径唯一.png)

对于多叉树：

> * **任何一个叶子节点有且仅有一个父节点**
> * **任何一个节点存在0个或多个子节点**

我们从`/local`开始，一路找父节点：`local->/user->/`

而从根目录`/`开始，顺着这个条路径走：`/->/user->local`

会发现**路径是唯一的**，所以可以定位一个文件

### 管道操作`|`

`|`这也是文件，将前一个命令作为后一个命令的输入

## 2. ls 查看目录内容

**概念**

* **文件属性解析：** `ls -l`显示文件的详细信息

![文件属性](E:\CODE\图\画图截图\文件属性.png)

> 1. 字段首位：`d`代表目录 `-`代表普通文件
> 2. 权限（`rwxr-xr-x`）:每三个为一组，分别属于拥有者u、所属组g、其他用户o）每组`r`为读权限、`w`为写权限、`x`为执行权限
> 3. 链接数（`3`）：文件的硬链接数量
> 4. 拥有者（`root`）：文件的归属用户
> 5. 所属组（`root`）：文件归属的用户组，这里就可以理解一个项目由一个项目组负责，这个项目属于项目组的所有用户
> 6. 大小（`4096`）：文件大小，单位：字节
> 7. 修改时间（`Nov 30 15:56`）：文件的最后修改时间
> 8. 文件名（`path1`）：文件或目录名称

**语法：`ls [选项] [目录/文件]`**

| **常用选项** | **说明**                                      |
| ------------ | --------------------------------------------- |
| `-l`         | 长格式显示，包含文件详细属性                  |
| `-a`         | 显示所有文件，包含隐藏文件（以`.`开头的文件） |
| `-d`         | 仅显示目录本身，不显示目录内的内容            |
| `-t`         | 按修改时间排序，最新修改的在最前面            |
| `-R`         | 递归显示目录内容                              |

这些选项可以组合使用

```bash
[root@VM-0-11-centos path1]# pwd
/root/path1
[root@VM-0-11-centos path1]# ls
code.txt
[root@VM-0-11-centos path1]# ls -al
total 8
drwxr-xr-x   2 root root 4096 Nov 30 16:57 .
dr-xr-x---. 10 root root 4096 Nov 30 17:03 ..
-rw-r--r--   1 root root    0 Nov 30 16:49 code.txt
[root@VM-0-11-centos path1]# ls -alt
total 8
dr-xr-x---. 10 root root 4096 Nov 30 17:03 ..
drwxr-xr-x   2 root root 4096 Nov 30 16:57 .
-rw-r--r--   1 root root    0 Nov 30 16:49 code.txt
```

## 3. 查看文件内容： cat/more/less/head/tail

**文件查看工具的核心差异：**

* `cat`：一次性显示所有内容（适合小文件，大文件会刷屏）
* `more/less`：分页显示（适合大文件），`more`只支持向下翻页，`less`支持上下翻页和搜索
* `head/tail`：显示文件开头/结尾（默认10行，可指定行数）

**各种指令语法和选项**

| **指令** | **语法**             | **核心选项**                                                 | **功能说明**                       |
| -------- | -------------------- | ------------------------------------------------------------ | ---------------------------------- |
| `cat`    | `cat [选项] [文件]`  | `-n`:带行号显示 `-b`: 仅显示非空行 `-s` ：把多个空行压缩成一个空行显示 | 一次性显示文件所有内容             |
| `more`   | `more [选项] [文件]` | `-n`：每页显示n行，这里的n是具体数字  `-q`：退出查看         | 分页显示，只能向下翻页，按空格翻页 |
| `less`   | `less [选项] [文件]` | `-N`：每页显示n行，这里的n是具体数字  `-q`：退出查看 `/字符串`：向下查找 `?字符串`：向上查找 | 分页显示，支持上下翻页、搜索       |
| `head`   | `head [选项] [文件]` | `-n`：显示前n行，这里的n是具体数字                           | 显示文件前n行                      |
| `tail`   | `tail [选项] [文件]` | `-n`：显示后n行，这里的n是具体数字 `-f`：实时跟踪文件更新    | 显示文件后n行，支持实时监控        |

**案例**

```bash
[root@VM-0-11-centos path1]# ls
1000lines.txt  10lines.txt  code.txt
# 按行号显示文件
[root@VM-0-11-centos path1]# cat -n 10lines.txt
     1	第1行：这是用for循环生成的内容
     2	第2行：这是用for循环生成的内容
     3	第3行：这是用for循环生成的内容
     4	第4行：这是用for循环生成的内容
     5	第5行：这是用for循环生成的内容
     6	第6行：这是用for循环生成的内容
     7	第7行：这是用for循环生成的内容
     8	第8行：这是用for循环生成的内容
     9	第9行：这是用for循环生成的内容
    10	第10行：这是用for循环生成的内容
# 逆序显示文件
[root@VM-0-11-centos path1]# tac 10lines.txt
第10行：这是用for循环生成的内容
第9行：这是用for循环生成的内容
第8行：这是用for循环生成的内容
第7行：这是用for循环生成的内容
第6行：这是用for循环生成的内容
第5行：这是用for循环生成的内容
第4行：这是用for循环生成的内容
第3行：这是用for循环生成的内容
第2行：这是用for循环生成的内容
第1行：这是用for循环生成的内容
# 每6行为一页显示 这里我按了两次空格 翻了两页
[root@VM-0-11-centos path1]# more -6 1000lines.txt
第1行：这是用while循环生成的内容
第2行：这是用while循环生成的内容
第3行：这是用while循环生成的内容
第4行：这是用while循环生成的内容
第5行：这是用while循环生成的内容
第6行：这是用while循环生成的内容
第7行：这是用while循环生成的内容
第8行：这是用while循环生成的内容
第9行：这是用while循环生成的内容
第10行：这是用while循环生成的内容
第11行：这是用while循环生成的内容
第12行：这是用while循环生成的内容
第13行：这是用while循环生成的内容
第14行：这是用while循环生成的内容
第15行：这是用while循环生成的内容
第16行：这是用while循环生成的内容
第17行：这是用while循环生成的内容
第18行：这是用while循环生成的内容
# 会跳转到新的页面显示 /字符串会向下查找 ?字符串会向上查找 q退出
[root@VM-0-11-centos path1]# less -6 1000lines.txt
# 显示[285,300]行 
# 也可以用这个公式 head -末尾行 文件名 | tail -$(末尾行 - 初始行 + 1)
[root@VM-0-11-centos path1]# head -300 1000lines.txt | tail -16
第285行：这是用while循环生成的内容
第286行：这是用while循环生成的内容
第287行：这是用while循环生成的内容
第288行：这是用while循环生成的内容
第289行：这是用while循环生成的内容
第290行：这是用while循环生成的内容
第291行：这是用while循环生成的内容
第292行：这是用while循环生成的内容
第293行：这是用while循环生成的内容
第294行：这是用while循环生成的内容
第295行：这是用while循环生成的内容
第296行：这是用while循环生成的内容
第297行：这是用while循环生成的内容
第298行：这是用while循环生成的内容
第299行：这是用while循环生成的内容
第300行：这是用while循环生成的内容
```

## 4. 辅助查询指令 find which

这里先引入`*`，这是一个通配符，用于**快速匹配一个或多个文件 / 目录名**的特殊符号，核心作用是 ***替代具体文件名的部分字符**，避免手动输入冗长的文件名，大幅提升操作效率

**`*`：匹配任意数量的任意字符（包括 0 个） **

* **作用**：最常用的通配符，代表 “任意长度的任意字符”（可以是字母、数字、符号，长度不限）。
* **语法**：`*` 可放在文件名的任意位置（开头、中间、结尾）。
* **实操案例**

```bash
[root@VM-0-11-centos path1]# tree
.
|-- 1000lines.txt
|-- 10lines.txt
|-- code.txt
|-- hello.c
`-- hello.png

0 directories, 5 files
# 显示所有以.txt为后缀的文件
[root@VM-0-11-centos path1]# ls *.txt
1000lines.txt  10lines.txt  code.txt
# 显示所有以hello开头的文件
[root@VM-0-11-centos path1]# ls hello*
hello.c  hello.png
# 显示所有包含o的文件
[root@VM-0-11-centos path1]# ls *o*
code.txt  hello.c  hello.png
# 删除所有以.c为后缀的文件
[root@VM-0-11-centos path1]# rm -rf *.c
[root@VM-0-11-centos path1]# tree
.
|-- 1000lines.txt
|-- 10lines.txt
|-- code.txt
`-- hello.png

0 directories, 4 files
```

**核心差异：**

* `find`：`find [路径] [选项]`按照文件名、文件大小、文件路径查找，可以递归遍历目录树
* `which`：`which [命令]`查找命令的路径



```bash
# 查找这个路径下的所有以.txt为后缀的文件
[root@VM-0-11-centos ~]# find /root/path1 "*.txt"
/root/path1
/root/path1/10lines.txt
/root/path1/code.txt
/root/path1/1000lines.txt
# 查找命令所在路径
[root@VM-0-11-centos ~]# which ls
alias ls='ls --color=auto'
	/usr/bin/ls
[root@VM-0-11-centos ~]# which ls -al
/usr/bin/which: invalid option -- 'l'
alias ls='ls --color=auto'
	/usr/bin/ls
```

