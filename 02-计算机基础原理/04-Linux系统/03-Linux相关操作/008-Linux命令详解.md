---
createTime: 2021-05-08
---
[toc]

## 1、Linux初级命令

### 1.1、ls（List）

> List information about the FILEs (the current directory by default).  Sort entries alphabetically if none of -cftuvSUX nor --sort is specified.
>
> 列出有关文件的信息(默认为当前目录)。如果没有指定-cftuvSUX或——Sort，则按字母顺序排序。

#### 1.1.1、ls-常用参数：

-a  列出指定目录下的所有文件，包括隐藏文件

-c 使用最后一次更改文件状态以进行排序(-t)或长时间打印(-l)的时间

-h 与-l选项一起使用时，请使用单位后缀:Byte、Kilobyte、mete、gb、tb和Petabyte，以便使用以2为基数的大小将数字减少到3或更少

-l 长格式列表。(见下文)。如果输出到终端，则所有文件大小的总和将输出到长清单前面的一行中

-n 以数字形式显示用户和组id，而不是在长(-l)输出中转换为用户或组名。这个选项默认打开-l选项

-o 以长格式列出，但省略组id

-s 显示每个文件实际使用的文件系统块的数量，以512字节为单位，其中部分单元四舍五入为下一个整数值

-t 在按照字典顺序对操作数排序之前，先按修改的时间排序(最近修改的是first)

-u 使用最后一次访问的时间，而不是最后一次修改文件进行排序

### 1.2、pwd（Print Working Directory）

> 打印当前工作目录的完整路径

### 1.3、touch（change file timestamps）

> 修改文件属性，如果指定文件名不存在，则创建新的文件
>
> ==stat [文件名]== 查看此文件属性
>
> ```txt
> [xiang@Xiang linuxStudy]$ stat test1.txt 
>   File: test1.txt
>   Size: 0               Blocks: 0          IO Block: 4096   regular empty file
> Device: fd01h/64769d    Inode: 667949      Links: 1
> Access: (0664/-rw-rw-r--)  Uid: ( 1001/   xiang)   Gid: ( 1001/   xiang)
> Access: 2010-01-01 00:00:00.000000000 +0800
> Modify: 2010-01-01 00:00:00.000000000 +0800
> Change: 2021-04-14 17:51:54.727700419 +0800
>  Birth: -
> ```

#### 1.3.1、touch-常用参数

-a  或--time=atime或--time=access或--time=use 只更改存取时间。

-c  或--no-create 不建立任何文档。

-t 使用指定的日期时间，而非现在的时间。

-m  或--time=mtime或--time=modify 只更改变动时间。

### 1.4、cat&tac (Concatenate FILE(s))

> 将文件或标准输入连接到标准输出

#### 1.4.1、常用参数

-n,-b, --number-nonblank  对非空输出行编号

-A,-e,--show-all  换行符以 `$`  展示

```sh
cat test  #展示文件内容
-A, --show-all      等价于 -vET
-b, --number-nonblank  对非空输出行编号
-e            等价于 -vE

cat -n test  #展示文件内容并且展示行号
     1    -A, --show-all      等价于 -vET
     2    -b, --number-nonblank  对非空输出行编号
     3    -e            等价于 -vE
```

### 1.5、mkdir（Make Directory）

如果目录不存在，则创建目录

##### 1.5.1、常见参数

-p 连续创建目录

-m 指定权限创建   mkdir -m 777 [文件名]

-v 在创建成功后，会输出成功信息，否则输出创建失败信息

--version 当前版本信息

```sh
[xiang@Xiang linuxStudy]$ mkdir -v tmp4
mkdir: created directory 'tmp4'
```

### 1.6、cd （Change Directory）

切换到当前指定目录，没什么好说的，但是使用频率非常高

### 1.7、rm&rmdir（Remove Directory）

> 删除操作，其实底层是让该文件指针不指向改文件内存块，内存上的内容则是存在的，但是恢复会比较麻烦，需要扫描整块内存才能拿到内容

#### 1.7.1、常用参数

-f ，-force 忽略不存在的文件，从来不给提示

-i，-interacive 进行交互式删除 ，指向一个文件，让你确认是否删除

-r，-R，--recursive 将参数重列出的全部目录和子目录递归删除

-d，--dir 删除空目录，也会删除掉文件....

### 1.8、mv（Move）

> 就是移动目录或文件到指定目录下，同时具有重命名功能

#### 1.8.1、常用参数

-b 若覆盖文件，则覆盖前先进行备份，备份的文件会在文件后缀后加一个 `~`

-f，force 强制的意思，如果目标文件已经存在，不会询问而直接覆盖掉

-i，若目标文件已经存在，会询问在覆盖

-n，不回覆盖现有文件

### 1.9、cp（copy）

> cp实用程序将source_file的内容复制到target_file。在第二个大纲格式中，每个命名的source_file的内容都复制到目标target_directory。文件本身的名称不会更改。如果cp检测到尝试将文件复制到自身的尝试，则复制将失败。

-l, --link    链接文件而不复制

-i, --interactive   覆盖前询问(使前面的 -n 选项失效)

-p     等于--preserve=模式,所有权,时间戳

-R, -r, --recursive 复制目录及目录内的所有项目

### 1.10、echo

> Echo 使用程序将任何指定的操作数标准输出，这些操作数由单个空格  '   字符分隔，后跟换行符 （\n）字符

```sh
[xiang@Xiang ~]$ echo \'good\'
'good'
[xiang@Xiang ~]$ echo $PATH
/home/xiang/.local/bin:/home/xiang/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin
```

### 1.11、head&tail

#### 1.11.1、head

head 从头部开始展示文件内容，默认10行

-n 展示前n行

-c 展示前n个字符（中文一个汉字并非一个字符）

#### 1.11.2、tail

tail 命令与 head 完全相反，是从头开始展示文本，但是参数更多，用法更多

-f 循环读取，实时读取，使用读取日志

-s，表示在每次反复的间隔休眠S秒

### 1.12.、more&less

> 每次打开文件不上全部把文件读入内存而是流式读取，不回因为 vi ｜ vim 某个大文件造成系统 oom

#### 1.12.1、常用参数

+n 从第n行开始显示

-s 把连续多个空行显示为一行

-c 从顶部清屏，然后显示

### 1.13、wc

> wc使用程序显示每个输出文件或标准输出（如果未指定文件）中每个输出文件中包含行数，字数和字节数，一行定义为由字符分隔的字符串。最后一个字符将不包括在行数中

-c 统计字节数

-l 统计字数

-m 统计字服数，这个标准不能与 -c  一起使用

--w 统计字数，一个字被定义由空白、跳格或换行符分隔的字符串

-L 打印最长行的长度



### 1.14、date

```txt
%H 小时(以00-23来表示)
%I 小时(以01-12来表示)。
%K 小时(以0-23来表示)。
%l 小时(以0-12来表示)。
%M 分钟(以00-59来表示)。
%P AM或PM
%r 时间(含时分秒，小时以12小时AM/PM来表示)。
%s 总秒数。起算时间为1970-01-01 00:00:00 UTC。
%S 秒(以本地的惯用法来表示)。
%T 时间(含时分秒，小时以24小时制来表示)。
%X 时间(以本地的惯用法来表示)。
%Z 市区。
%a 星期的缩写。
%A 星期的完整名称。
%b 月份英文名的缩写。
%B 月份的完整英文名称。
%c 日期与时间。只输入date指令也会显示同样的结果。
%d 日期(以01-31来表示)
%D 日期(含年月日)。
%j 该年中的第几天。
%m 月份(以01-12来表示)。
%U 该年中的周数
%w 该周的天数，0代表周日，1代表周一，异词类推。
%x 日期(以本地的惯用法来表示)。
%y 年份(以00-99来表示)。
%Y 年份(以四位数来表示)。
%n 在显示时，插入新的一行。
%t 在显示时，插入tab。
MM 月份(必要)
DD 日期(必要)
hh 小时(必要)
mm 分钟(必要)
ss 秒(选择性)
-d<字符串> 显示字符串所指的日期与时间。字符串前后必须加上双引号。
-s<字符串> 根据字符串来设置日期与时间。字符串前后必须加上双引号。
-u 显示GMT。
```

```sh
[xiang@Xiang aobing]$ date
Thu Apr 15 16:06:53 CST 2021
[xiang@Xiang aobing]$ date '+%c'
Thu 15 Apr 2021 04:07:06 PM CST
[xiang@Xiang aobing]$ date '+%D %X'
04/15/21 04:07:19 PM
```

### 1.15、cal

-1 显示一个月的月历

-3 显示系统前一个月，当前月，下一个月的月历

-m 显示星期一为一个星期的第一天
-j  显示在当年中的第几天（一年日期按天算，从1月1号算起，默认显示当前月在一年中的天数）
-y  显示当前年份的日历

```sh
[xiang@Xiang aobing]$ cal -3
     March 2021            April 2021             May 2021      
Su Mo Tu We Th Fr Sa  Su Mo Tu We Th Fr Sa  Su Mo Tu We Th Fr Sa
    1  2  3  4  5  6               1  2  3                     1
 7  8  9 10 11 12 13   4  5  6  7  8  9 10   2  3  4  5  6  7  8
14 15 16 17 18 19 20  11 12 13 14 15 16 17   9 10 11 12 13 14 15
21 22 23 24 25 26 27  18 19 20 21 22 23 24  16 17 18 19 20 21 22
28 29 30 31           25 26 27 28 29 30     23 24 25 26 27 28 29
                                            30 31    
```

### 1.16、which

> which 命令，在PATH变量指定路径，搜索某个系统命令的位置，并且返回第一个搜索结果，也就是说， 使用 which命令，就可以看到某个系统命令是否村存在，以及执行的道理的哪个文件

```sh
xiang@xiaocencen ~ % which mvn
/usr/local/apache-maven-3.5.0/bin/mvn
```

### 1.17、whereis

> Whereis 命令只能用于程序名的搜索

#### 1.17.1、常用参数

-b 定位可执行的文件

-m 定位帮助文件

-s 定位源文件

-u 搜索默认路径下除可执行文件、源代码文件，帮助文档以外的文件

-B 指定搜索可执行文件的路径

-M 指定搜素帮助文件路径

-S  指定搜索源代码文件的路径。

### 1.18、ps

> ps 包含有关具有控制终端的所有进程信息行

#### 1.18.1、常用参数

-a 显示所有程序

-A 显示所有进程

c 显示进程的真实名称

-N 反向选择

f 显示程序间的关系

-H 显示树状结构

r 显示当前终端的进程

T 显示当前终端的所有程序

u 指定用户的所有进程

-au 显示较详细的资讯

-aux 显示所有包含其他使用者的行程

-C<命令> 列出指定命令的状况

--lines<行数> 每页显示的行数

--width<字符数> 每页显示的字符数

### 1.19、kill&killall

> 命令kill将指定的信号发送到指定的进程或进程组。如果未指定信号，则发送TERM信号。TERM信号将杀死不捕获该信号的进程。对于其他过程，可能需要使用KILL（9）信号，因为无法捕获该信号。

-l 信号，若果不加信号的编号参数，则使用“-l”参数会列出全部的信号名称

-a 当处理当前进程时，不限制命令名和进程号的对应关系

-p 指定kill 命令只打印相关进程的进程号，而不发送任何信号

-s 指定发送信号

-u 指定用户

```sh
[xiang@Xiang aobing]$ kill -l
 1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL       5) SIGTRAP
 2) SIGABRT      7) SIGBUS       8) SIGFPE       9) SIGKILL     10) SIGUSR1
1) SIGSEGV     12) SIGUSR2     13) SIGPIPE     14) SIGALRM     15) SIGTERM
2) SIGSTKFLT   17) SIGCHLD     18) SIGCONT     19) SIGSTOP     20) SIGTSTP
3) SIGTTIN     22) SIGTTOU     23) SIGURG      24) SIGXCPU     25) SIGXFSZ
4) SIGVTALRM   27) SIGPROF     28) SIGWINCH    29) SIGIO       30) SIGPWR
5) SIGSYS      34) SIGRTMIN    35) SIGRTMIN+1  36) SIGRTMIN+2  37) SIGRTMIN+3
6) SIGRTMIN+4  39) SIGRTMIN+5  40) SIGRTMIN+6  41) SIGRTMIN+7  42) SIGRTMIN+8
7) SIGRTMIN+9  44) SIGRTMIN+10 45) SIGRTMIN+11 46) SIGRTMIN+12 47) SIGRTMIN+13
8) SIGRTMIN+14 49) SIGRTMIN+15 50) SIGRTMAX-14 51) SIGRTMAX-13 52) SIGRTMAX-12
9) SIGRTMAX-11 54) SIGRTMAX-10 55) SIGRTMAX-9  56) SIGRTMAX-8  57) SIGRTMAX-7
10) SIGRTMAX-6  59) SIGRTMAX-5  60) SIGRTMAX-4  61) SIGRTMAX-3  62) SIGRTMAX-2
11) SIGRTMAX-1  64) SIGRTMAX
```

HUP   1   终端断线
INT   2   中断（同 Ctrl + C）
QUIT   3   退出（同 Ctrl + \）
TERM  15   终止
KILL   9   强制终止
CONT  18   继续（与STOP相反， fg/bg命令）
STOP   19   暂停（同 Ctrl + Z）

kill -9 是我们使用的最多的信号，其实这种方式一点也不优雅，应该使用kill -15信号，大部分程序接收到SIGTERM信号后，会先释放自己的资源，然后再停止。但是也有程序可能接收信号后，做一些其他的事情（如果程序正在等待IO，可能就不会立马做出响应，等到io完成后在结束），也就是说，SIGTERM多半是会被阻塞的。

## 2、Linux进阶命令

### 2.1、find

> find 会列出每个路径递归遍历目录树，根据树中的每个文件计算表达式

#### 2.1.1、find参数格式

```
find [-H | -L | -P] [-EXdsx] [-f path] path ... [expression] find [-H | -L | -P] [-EXdsx] -f path [path ...] [expression]
```

#### 2.1.2、find命令参数

-print：find命令将匹配的文件输出到标准输出。

-exec：find命令对匹配的文件执行该参数所给出的shell命令。相应命令的形式为'command' { } \;，注意{  }和\；之间的空格。

-name  按照文件名查找文件。

-perm  按照文件权限来查找文件。

-prune 使用这一选项可以使find命令不在当前指定的目录中查找，如果同时使用-depth选项，那么-prune将被find命令忽略。

-user  按照文件属主来查找文件。

-group 按照文件所属的组来查找文件。

-mtime -n +n 按照文件的更改时间来查找文件， - n表示文件更改时间距现在n天以内，+ n表示文件更改时间距现在n天以前。find命令还有-atime和-ctime 选项，但它们都和-m time选项。

-nogroup 查找无有效所属组的文件，即该文件所属的组在/etc/groups中不存在。

-nouser  查找无有效属主的文件，即该文件的属主在/etc/passwd中不存在。

-newer file1 ! file2 查找更改时间比文件file1新但比文件file2旧的文件。

-type 查找某一类型的文件，诸如：

- b - 块设备文件。
- d - 目录。
- c - 字符设备文件。
- p - 管道文件。
- l - 符号链接文件。
- f - 普通文件。

-size n：[c] 查找文件长度为n块的文件，带有c时表示文件长度以字节计。-depth：在查找文件时，首先查找当前目录中的文件，然后再在其子目录中查找。

-fstype：查找位于某一类型文件系统中的文件，这些文件系统类型通常可以在配置文件/etc/fstab中找到，该配置文件中包含了本系统中有关文件系统的信息。

-mount：在查找文件时不跨越文件系统mount点。

-follow：如果find命令遇到符号链接文件，就跟踪至链接所指向的文件。

-cpio：对匹配的文件使用cpio命令，将这些文件备份到磁带设备中。

另外,下面三个的区别:

-amin n  查找系统中最后N分钟访问的文件

-atime n 查找系统中最后n*24小时访问的文件

-cmin n  查找系统中最后N分钟被改变文件状态的文件

-ctime n 查找系统中最后n*24小时被改变文件状态的文件

-mmin n  查找系统中最后N分钟被改变文件数据的文件


#### find常用参数示例

```sh
# 查找 /Users 目录下所有以 .log	结尾的文件
xiang@xiaocencen ~ % find /Users  -name "*.log" -print
/Users/oracle/datas/CXEAST20210406.log
/Users/oracle/datas/EAST20210404_1.log
/Users/oracle/datas/EAST20210404.log
/Users/xiang/.jrebel/jrebel.log
/Users/xiang/.jrebel/jrebel-licensing.log
/Users/xiang/.jrebel/jrebel-intellij.log
```

**-exec 参数后面跟的是command，它的终止是以`;`为结束标志的，所以这句命令后面的分号是不可缺少的，考虑到各个系统中分号会有不同的意义，所以前面加反斜杠。**

```sh
[xiang@Xiang aobing]$ find linuxStudy/ -name "*.txt"
linuxStudy/1.txt
linuxStudy/2.txt
linuxStudy/3.txt
# 将查询到的文件，删掉....
[xiang@Xiang aobing]$ find linuxStudy/ -name "*.txt" -exec rm -rf {} \;
[xiang@Xiang aobing]$ find linuxStudy/ -name "*.txt"
```

 -exec 后面可以接任何命令，你可以灵活运用，再结合到前面的-name参数，可以玩出花来。



### 2.2、grep

> grep实用程序搜索任何给定的输入文件，选择与一个或多个模式匹配的行。默认情况下，如果模式中的正则表达式（RE）匹配输入行而没有尾随换行符，则该模式会匹配输入行。空表达式匹配每行。与至少一种模式匹配的每条输入线均写入标准输出

```sh
$grep '20:[1-5][0-9]:' *.log  #匹配当前目录下搜索log日志中，20点的日志
$grep '20:[1-5][0-9]' 1.log 2.log 3.log  #指定在这三个文件中查找
#grep规则是支持正则表达式的
$ps -ef|grep java    #查找所有java进程
$ps -ef|grep java    #-c可以统计查找的个数
$grep '20:[1-5][0-9]:' *.log | grep -v '20:[3-4][0-9]:'   # -v反向选择，相当于过滤
$grep 'ab|bc' *.log  #支持|语法，匹配含有ab或者bc的文本行
```

-a  --text  不要忽略二进制的数据。

-A<显示行数>  --after-context=<显示行数>  #除了显示符合范本样式的那一列之外，并显示该行之后的内容。

-b  --byte-offset  #在显示符合样式的那一行之前，标示出该行第一个字符的编号。

-B<显示行数>  --before-context=<显示行数>  #除了显示符合样式的那一行之外，并显示该行之前的内容。

-c  --count  #计算符合样式的列数。

-C<显示行数>  --context=<显示行数>或-<显示行数>  #除了显示符合样式的那一行之外，并显示该行之前后的内容。

-d <动作>  --directories=<动作>  #当指定要查找的是目录而非文件时，必须使用这项参数，否则grep指令将回报信息并停止动作。

-e<范本样式> --regexp=<范本样式>  #指定字符串做为查找文件内容的样式。

-E  --extended-regexp  #将样式为延伸的普通表示法来使用。

-f<规则文件> --file=<规则文件>  #指定规则文件，其内容含有一个或多个规则样式，让grep查找符合规则条件的文件内容，格式为每行一个规则样式。

-F  --fixed-regexp  #将样式视为固定字符串的列表。

-G  --basic-regexp  #将样式视为普通的表示法来使用。

-h  --no-filename  #在显示符合样式的那一行之前，不标示该行所属的文件名称。

-H  --with-filename  #在显示符合样式的那一行之前，表示该行所属的文件名称。

-i  --ignore-case  #忽略字符大小写的差别。

-l  --file-with-matches  #列出文件内容符合指定的样式的文件名称。

-L  --files-without-match  #列出文件内容不符合指定的样式的文件名称。

-n  --line-number  #在显示符合样式的那一行之前，标示出该行的列数编号。

-q  --quiet或--silent  #不显示任何信息。

-r  --recursive  #此参数的效果和指定“-d recurse”参数相同。

-s  --no-messages  #不显示错误信息。

-v  --revert-match  #显示不包含匹配文本的所有行。

-V  --version  #显示版本信息。

-w  --word-regexp  #只显示全字符合的列。

-x  --line-regexp  #只显示全列符合的列。

-y  此参数的效果和指定“-i”参数相同。



### 2.3、cut

> cut实用程序从每个文件中剪切出每行的选定部分（由列表指定），并将它们写入标准输出。如果未指定文件参数，或者文件参数为单破折号（-），则从标准输入中读取内容。列表指定的项目可以是列位置，也可以是由特殊字符分隔的字段。列编号从1开始。

```sh
$cut -c-10 tmp.txt  #cut tmp.txt文件的前10列
$cut -c3-5 tmp.txt  #cut tmp.txt文件的第3到5列
$cut -c3- tmp.txt  #cut tmp.txt文件的第3到结尾列
```



### 2.4、diff

比较两个文件的不同

-b或--ignore-space-change 不检查空格字符的不同。

-B或--ignore-blank-lines 不检查空白行。

-c 显示全部内文，并标出不同之处。

-C或--context 与执行"-c-"指令相同。

-d或--minimal 使用不同的演算法，以较小的单位来做比较。

-D或ifdef 此参数的输出格式可用于前置处理器巨集。

-e或--ed 此参数的输出格式可用于ed的script文件。

-f或-forward-ed 输出的格式类似ed的script文件，但按照原来文件的顺序来显示不同处。

-H或--speed-large-files 比较大文件时，可加快速度。

-l或--ignore-matching-lines 若两个文件在某几行有所不同，而这几行同时都包含了选项中指定的字符或字符串，则不显示这两个文件的差异。

-i或--ignore-case 不检查大小写的不同。

-l或--paginate 将结果交由pr程序来分页。

-n或--rcs 将比较结果以RCS的格式来显示。

-N或--new-file 在比较目录时，若文件A仅出现在某个目录中，预设会显示：Only in目录：文件A若使用-N参数，则diff会将文件A与一个空白的文件比较。

-p 若比较的文件为C语言的程序码文件时，显示差异所在的函数名称。

-P或--unidirectional-new-file 与-N类似，但只有当第二个目录包含了一个第一个目录所没有的文件时，才会将这个文件与空白的文件做比较。

-q或--brief 仅显示有无差异，不显示详细的信息。

-r或--recursive 比较子目录中的文件。

-s或--report-identical-files 若没有发现任何差异，仍然显示信息。

-S或--starting-file 在比较目录时，从指定的文件开始比较。

-t或--expand-tabs 在输出时，将tab字符展开。

-T或--initial-tab 在每行前面加上tab字符以便对齐。

-u,-U或--unified= 以合并的方式来显示文件内容的不同。

-v或--version 显示版本信息。

-w或--ignore-all-space 忽略全部的空格字符。

-W或--width 在使用-y参数时，指定栏宽。

-x或--exclude 不比较选项中所指定的文件或目录。

-X或--exclude-from 您可以将文件或目录类型存成文本文件，然后在=中指定此文本文件。

-y或--side-by-side 以并列的方式显示文件的异同之处。

```sh
# 比较全文
[xiang@Xiang linuxStudy]$ diff a.txt b.txt -c
*** a.txt       2021-04-15 17:14:24.048498048 +0800
--- b.txt       2021-04-15 17:14:34.542438689 +0800
***************
*** 1,5 ****
  ABC
  AA
! ACD
! A
! ccc
--- 1,5 ----
  ABC
  AA
! CD
! B
! CCC
```

```sh
# 忽略大小写
[xiang@Xiang linuxStudy]$ diff a.txt b.txt -i
3,4c3,4
< ACD
< A
---
> CD
> B
```



### 2.5、tar

> 用来压缩和解压文件。tar本身不具有压缩功能。他是调用压缩功能实现的

```sh
#打包  tar -cvf 包名  文件名
$tar -cvf test.tar test.txt 
#解包  tar -xvf 包名 
$tar -xvf test.tar
#压缩  tar -czvf 包名 文件名
$tar -czvf test.tgz test.txt
#解压  tar -xzvf 包名
$tar -xzvf test.tgz
```



### 2.6、du

> du实用程序显示每个文件自变量以及以每个目录自变量为根的文件层次结构中每个目录的文件系统块使用情况。如果未指定文件，则显示以当前目录为根的层次结构的块使用情况。

```sh
#查看指定文件大小
$du -h filename
#展示该目录下所有文件大小，大小以可读方式展示
$du  -h /
#展示当前目录大小
$du -sh
#展示当前目录下每个目录大小
$du -sh ./
#显示所有文件的大小，以可读方式展示
$du -ah /
```

```sh
[xiang@Xiang aobing]$ du -sh linuxStudy/
736M    linuxStudy/
```

### 2.7、df

> df实用程序显示有关指定文件系统或其中一部分文件的文件系统上的可用磁盘空间量的统计信息。值以每块计数512字节的形式显示。如果未指定文件或文件系统操作数，则将显示所有已挂载文件系统的统计信息（受下面的-t选项约束）。

```sh
# 展示当前磁盘使用情况，以可读的方式展示
[xiang@Xiang aobing]$ df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        900M     0  900M   0% /dev
tmpfs           914M   24K  914M   1% /dev/shm
tmpfs           914M  452K  914M   1% /run
tmpfs           914M     0  914M   0% /sys/fs/cgroup
/dev/vda1        59G  9.4G   48G  17% /
tmpfs           183M     0  183M   0% /run/user/0
tmpfs           183M     0  183M   0% /run/user/1001
```

### 2.8、lsof

> lsof（list open files）是一个列出当前系统打开文件的工具。(在linux环境下，任何事物都以文件的形式存在)

```sh
# 查看相关进程
[xiang@Xiang aobing]$ lsof /bin/bash
COMMAND     PID  USER  FD   TYPE DEVICE SIZE/OFF   NODE NAME
bash    2260663 xiang txt    REG  253,1  1219248 268801 /usr/bin/bash

#查看某个用户打开的文件信息
$lsof -u username
#列出某个程序进程所打开的文件信息
$lsof -c java
#列出除了某个用户外的被打开的文件信息
$lsof -u ^midou
#通过某个进程号显示该进行打开的文件
$lsof -p pid
#列出除了某个进程号，其他进程号所打开的文件信息
$lsof -p ^pid
#列出所有的网络连接
$lsof -i
#列出所有tcp 网络连接信息
$lsof -i tcp
#列出所有udp网络连接信息
$lsof -i udp
#列出谁在某个端口使用情况
$lsof -i :port
#特定的tcp端口
$lsof -i tcp:port 
#特定的udp端口
$lsof -i udp:port
#列出某个用户的所有活跃的网络端口
$lsof -a -u username -i
#根据文件描述符范围列出文件信息
$lsof -d 0-2
```

### 2.9、netstat

> netstat命令以符号形式显示各种与网络相关的数据结构的内容。有多种输出格式，具体取决于显示信息的选项。该命令的第一种形式显示每个协议的活动套接字列表。第二种形式根据选择的选项显示其他网络数据结构之一的内容。使用第三种形式，并指定等待间隔，netstat将在配置的网络接口上连续显示有关数据包流量的信息。第四种形式显示指定协议或地址族的统计信息。如果指定了等待间隔，将显示最近间隔秒的协议信息。第五种形式显示指定协议或地址族的每个接口的统计信息。第六种形式显示mbuf（9）统计信息。第七种形式显示指定地址系列的路由表。第八种形式显示路由统计信息。

```sh
#列出所有端口使用情况
$netstat -a
#显示当前UDP连接状况
$netstat -nu
#显示UDP端口号的使用情况
$netstat -apu
#显示网卡列表
$netstat -i
#显示网络统计信息
$netstat -s
#显示监听的套接口
$netstat -l
#显示所有已建立的有效连接
$netstat -n
#显示关于路由表的信息
$netstat -r
#列出所有 tcp 端口
$netstat -at
#找出程序运行的端口
$netstat -ap | grep ssh
#在 netstat 输出中显示 PID 和进程名称
$netstat -pt
```

### 2.10、ifconfig

```sh
[xiang@Xiang aobing]$ ifconfig -a
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.0.8.3  netmask 255.255.252.0  broadcast 10.0.11.255
        inet6 fe80::5054:ff:fe66:e22d  prefixlen 64  scopeid 0x20<link>
        ether 52:54:00:66:e2:2d  txqueuelen 1000  (Ethernet)
        RX packets 4600623  bytes 1949398239 (1.8 GiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 5350272  bytes 4233981086 (3.9 GiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

> 第一行：连接类型：Ethernet（以太网）HWaddr（硬件mac地址）
>
> 第二行：网卡的IP地址、子网、掩码
>
> 第三行：UP（代表网卡开启状态）RUNNING（代表网卡的网线被接上）MULTICAST（支持组播）MTU:1500（最大传输单元）：1500字节
>
> 第四、五行：接收、发送数据包情况统计
>
> 第七行：接收、发送数据字节数统计信息。

### 2.11、hostname

> 主机名用于显示系统的DNS名称，并显示或设置其主机名或NIS域名

```sh
-v：详细信息模式；
-a：显示主机别名；
-d：显示DNS域名；
-f：显示FQDN名称；
-i：显示主机的ip地址；
-s：显示短主机名称，在第一个点处截断；
-y：显示NIS域名。
#查看主机ip,这个命令我最推荐的一个用法就是查看主机ip，之前我一直用ifconfig
$hostname -i 
```

### 2.12、route

> Route操纵内核的IP路由表。它的主要用途是在使用ifconfig（8）程序对其进行配置后，通过接口设置到特定主机或网络的静态路由。

```sh
#显示当前路由
[xiang@Xiang aobing]$ route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         _gateway        0.0.0.0         UG    100    0        0 eth0
10.0.8.0        0.0.0.0         255.255.252.0   U     100    0        0 eth0
#屏蔽一条路由
$route add -net 224.0.0.0 netmask 240.0.0.0 reject
#删除路由记录
$route del -net 224.0.0.0 netmask 240.0.0.0
#删除和添加设置默认网关
$route del default gw 192.168.0.100
$route add default gw 192.168.0.100
```

### 2.13、vmstat

#### vmstat介绍

> vmstat reports information about processes, memory, paging, block IO, traps, disks and cpu activity.
>
> vmstat报告有关进程，内存，页面调度，块IO，陷阱，磁盘和cpu活动的信息。

#### vmstat参数格式

```
vmstat [options] [delay [count]]
```

#### vmstat命令参数

-a：显示活跃和非活跃内存

-f：显示从系统启动至今的fork数量 。

-m：显示slabinfo

-n：只在开始时显示一次各字段名称。

-s：显示内存相关统计信息及多种系统活动数量。

delay：刷新时间间隔。如果不指定，只显示一条结果。

count：刷新次数。如果不指定刷新次数，但指定了刷新时间间隔，这时刷新次数为无穷。

-d：显示磁盘相关统计信息。

-p：显示指定磁盘分区统计信息

-S：使用指定单位显示。参数有 k 、K 、m 、M ，分别代表1000、1024、1000000、1048576字节（byte）。默认单位为K（1024 bytes）

#### vmstat常用参数示例

```
#显示虚拟内存情况
$ vmstat
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 1  0      0 23764228 507816 36953948    0    0     3     5    0    0  1  0 98  0  0
```

##### 解释

Procs（进程）：

r: 运行队列中进程数量

b: 等待IO的进程数量

Memory（内存）：

swpd: 使用虚拟内存大小

free: 可用内存大小

buff: 用作缓冲的内存大小

cache: 用作缓存的内存大小

Swap：

si: 每秒从交换区写到内存的大小

so: 每秒写入交换区的内存大小

IO：（现在的Linux版本块的大小为1024bytes）

bi: 每秒读取的块数

bo: 每秒写入的块数

系统：

in: 每秒中断数，包括时钟中断。

cs: 每秒上下文切换数。

CPU（以百分比表示）：

us: 用户进程执行时间(user time)

sy: 系统进程执行时间(system time)

id: 空闲时间(包括IO等待时间),中央处理器的空闲时间 。以百分比表示。

wa: 等待IO时间

```
#表示在3秒时间内进行3次采样。将得到一个数据汇总他能够反映真正的系统情况。
$vmstat 3 3
#查看系统fork多少次
$ vmstat -f
    166484246 forks
#查看内存使用的详细信息
$vmstat -s
#查看磁盘的读/写
$vmstat -d
#查看系统的slab信息
$vmstat -m
```

### 2.14、free

#### free介绍

> free  displays the total amount of free and used physical and swap memory in the system, as well as the buffers and caches used by the kernel.
>
> free显示系统中可用和可用的物理内存和交换内存的总量，以及内核使用的缓冲区和高速缓存。

#### free参数格式

```
free [options]
```

#### free命令参数

-b 以Byte为单位显示内存使用情况。

-k 以KB为单位显示内存使用情况。

-m 以MB为单位显示内存使用情况。

-g  以GB为单位显示内存使用情况。

-o 不显示缓冲区调节列。

-s<间隔秒数> 持续观察内存使用状况。

-t 显示内存总和列。

#### free常用参数示例

```
#显示内存使用情况
$ free
              total        used        free      shared  buff/cache   available
Mem:       65808884     4582700    23754736         684    37471448    60913052
$ free -h
              total        used        free      shared  buff/cache   available
Mem:            62G        4.4G         22G        684K         35G         58G
Swap:            0B          0B          0B
```

##### 解释

total:总计物理内存的大小。

used:已使用多大。

free:可用有多少。

Shared:多个进程共享的内存总额。

Buffers/cached:磁盘缓存的大小。

第三行(-/+ buffers/cached):

used:已使用多大。

free:可用有多少。

```
#周期性的查询内存使用信息，5s执行一次
$ free -s 5
```

### 2.15、top

#### top介绍

> The  top  program provides a dynamic real-time view of a running system.  It can display system summary information as well as a list of processes or threads currently being managed by the Linux kernel.  The types of system summary information shown and the types, order and size of information displayed for  processes  are  all  user configurable and that configuration can be made persistent across restarts.
>    The  program  provides a limited interactive interface for process manipulation as well as a much more extensive interface for personal configuration  --  encompassing every aspect of its operation.  And while top is referred to throughout this document, you are free to name the program anything you wish.  That new name, possibly  an alias, will then be reflected on top's display and used when reading and writing a configuration file.
>
> top程序提供正在运行的系统的动态实时视图。它可以显示系统摘要信息以及Linux内核当前正在管理的进程或线程的列表。所显示的系统摘要信息的类型以及为进程显示的信息的类型，顺序和大小都是用户可配置的，并且可以使配置在重新启动后保持不变。
>     该程序为流程操作提供了一个有限的交互式界面，并为个人配置提供了更为广泛的界面-涵盖了其操作的各个方面。尽管在本文档中始终引用top，但是您可以随意为程序命名。然后，该新名称（可能是别名）将反映在顶部的显示屏上，并在读写配置文件时使用。

#### top参数格式

```
top -hv|-bcHiOSs -d secs -n max -u|U user -p pid -o fld -w [cols]
```

#### top命令参数

-b 批处理

-c 显示完整的治命令

-I 忽略失效过程

-s 保密模式

-S 累积模式

-i<时间> 设置间隔时间

-u<用户名> 指定用户名

-p<进程号> 指定进程

-n<次数> 循环显示的次数

#### top常用参数示例

```
#top
$ top
top - 00:56:07 up 149 days, 14:40,  1 user,  load average: 0.00, 0.02, 0.05
Tasks: 254 total,   1 running, 253 sleeping,   0 stopped,   0 zombie
%Cpu(s):  1.4 us,  0.3 sy,  0.0 ni, 98.3 id,  0.1 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem : 65808884 total, 23749772 free,  4586160 used, 37472952 buff/cache
KiB Swap:        0 total,        0 free,        0 used. 60909608 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
24397 dongshan  20   0 17.972g 688312  13728 S   6.2  1.0   7:09.11 java
    1 root      20   0   42140   3684   1476 S   0.0  0.0  23:58.88 systemd
    2 root      20   0       0      0      0 S   0.0  0.0   0:05.47 kthreadd
    3 root      20   0       0      0      0 S   0.0  0.0   0:16.06 ksoftirqd/0
    5 root       0 -20       0      0      0 S   0.0  0.0   0:00.00 kworker/0:0H
    7 root      rt   0       0      0      0 S   0.0  0.0   1:27.00 migration/0
    8 root      20   0       0      0      0 S   0.0  0.0   0:00.00 rcu_bh
    9 root      20   0       0      0      0 S   0.0  0.0   0:00.00 rcuob/0
```

##### 解释

**第一行，任务队列信息，同 uptime 命令的执行结果，具体参数说明情况如下：**

00:56:07 — 当前系统时间

up 149 days, 14:40 — 系统已经运行了149天14小时40分钟（在这期间系统没有重启过的）

1users — 当前有1个用户登录系统

load average: 0.00, 0.02, 0.05 — load average后面的三个数分别是1分钟、5分钟、15分钟的负载情况。

load average数据是每隔5秒钟检查一次活跃的进程数，然后按特定算法计算出的数值。如果这个数除以逻辑CPU的数量，结果高于5的时候就表明系统在超负荷运转了。

**第二行，Tasks — 任务（进程）**

系统现在共有254个进程，其中处于运行中的有1个，253个在休眠（sleep），stoped状态的有0个，zombie状态（僵尸）的有0个。

**第三行，cpu状态信息**

%Cpu(s):  1.4 us,  0.3 sy,  0.0 ni, 98.3 id,  0.1 wa,  0.0 hi,  0.0 si,  0.0 st

1.4 us — 用户空间占用CPU的百分比。

0.3 sy — 内核空间占用CPU的百分比。

0.0 ni — 改变过优先级的进程占用CPU的百分比

98.3 id — 空闲CPU百分比

0.1 wa — IO等待占用CPU的百分比

0.0 hi — 硬中断（Hardware IRQ）占用CPU的百分比

0.0 si — 软中断（Software Interrupts）占用CPU的百分比

**第四行,内存状态**

65808884 total  物理内存总量

23749772 free  使用中的内存总量

4586160 used  空闲内存总量

37472952 buff/cache  缓存的内存量

**第五行，swap交换分区信息**

0 total  交换区总量

0 use  使用的交换区总量

0 free  空闲交换区总量

60909608 avail Mem  可用交换区总量

**第七行以下：各进程（任务）的状态监控**

PID — 进程id

USER — 进程所有者

PR — 进程优先级

NI — nice值。负值表示高优先级，正值表示低优先级

VIRT — 进程使用的虚拟内存总量，单位kb。VIRT=SWAP+RES

RES — 进程使用的、未被换出的物理内存大小，单位kb。RES=CODE+DATA

SHR — 共享内存大小，单位kb

S — 进程状态。D=不可中断的睡眠状态 R=运行 S=睡眠 T=跟踪/停止 Z=僵尸进程

%CPU — 上次更新到现在的CPU时间占用百分比

%MEM — 进程使用的物理内存百分比

TIME+ — 进程使用的CPU时间总计，单位1/100秒

COMMAND — 进程名称（命令名/命令行）

### 2.15、sar

#### sar介绍

> sar（System Activity Reporter系统活动情况报告）是目前 Linux 上最为全面的系统性能分析工具之一，可以从多方面对系统的活动进行报告，包括：文件的读写情况、 系统调用的使用情况、磁盘I/O、CPU效率、内存使用状况、进程活动及IPC有关的活动等。

#### sar参数格式

```
sar [options] [-A] [-o file] t [n]
```

#### sar命令参数

-A：所有报告的总和

-u：输出CPU使用情况的统计信息

-v：输出inode、文件和其他内核表的统计信息

-d：输出每一个块设备的活动信息

-r：输出内存和交换空间的统计信息

-b：显示I/O和传送速率的统计信息

-a：文件读写情况

-c：输出进程统计信息，每秒创建的进程数

-R：输出内存页面的统计信息

-y：终端设备活动情况

-w：输出系统交换活动信息