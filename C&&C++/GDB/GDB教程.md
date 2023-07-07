---
number headings: auto, first-level 1, max 6, _.1.1.
---

# GDB教程

## 1. GDB是什么

从现在开始，我将系统教大家学习使用 GDB，本节先解决第一个问题，即 GDB 是什么。

要知道，哪怕是开发经验再丰富的程序员，编写的程序也避免不了出错。程序中的错误主要分为 2 类，分别为语法错误和逻辑错误：

- 程序中的语法错误几乎都可以由编译器诊断出来，很容易就能发现并解决；
- 逻辑错误指的是代码思路或者设计上的缺陷，程序出现逻辑错误的症状是：代码能够编译通过，没有语法错误，但是运行结果不对。对于这类错误，只能靠我们自己去发现和纠正。  

也就是说，程序中出现的语法错误可以借助编译器解决；但逻辑错误则只能靠自己解决。实际场景中解决逻辑错误最高效的方法，就是借助调试工具对程序进行调试。

所谓调试（Debug），就是让代码一步一步慢慢执行，跟踪程序的运行过程。比如，可以让程序停在某个地方，查看当前所有变量的值，或者内存中的数据；也可以让程序一次只执行一条或者几条语句，看看程序到底执行了哪些代码。

也就是说，通过调试程序，我们可以监控程序执行的每一个细节，包括变量的值、函数的调用过程、内存中数据、线程的调度等，从而发现隐藏的错误或者低效的代码。

> 对于初学者来说，学习调试可以增加编程的功力，能让我们更加了解自己的程序，比如变量是什么时候赋值的、内存是什么时候分配的，从而弥补学习的纰漏。<font color='red'>调试是每个程序员必须掌握的基本技能，没有选择的余地！</font>

就好像编译程序需要借助专业的编译器，调试程序也需要借助专业的辅助工具，即调试器（Debugger）。表 1 罗列了当下最流行的几款调试器：
<center>表 1 常用的调试器</center>  

| 特 点                                                        | 调试器名称      |
| ------------------------------------------------------------ | --------------- |
| Remote Debugger 是 VC/VS 自带的调试器，与整个IDE无缝衔接，使用非常方便。 | Remote Debugger |
| 大名鼎鼎的 Windows 下的调试器，它的功能甚至超越了 Remote Debugger，它还有一个命令行版本（cdb.exe），但是这个命令行版本的调试器指令比较复杂，不建议初学者使用。 | WinDbg          |
| XCode 自带的调试器，Mac OS X 下开发必备调试器。              | LLDB            |
| Linux 下使用最多的一款调试器，也有 Windows 的移植版。        | GDB             |

本教程讲解的就是 GDB 调试器。

GDB 全称“GNU symbolic debugger”，从名称上不难看出，它诞生于 GNU 计划（同时诞生的还有 GCC、Emacs 等），是 Linux 下常用的程序调试器。发展至今，GDB 已经迭代了诸多个版本，当下的 GDB 支持调试多种编程语言编写的程序，包括 C、C++、Go、Objective-C、OpenCL、Ada 等。实际场景中，GDB 更常用来调试 C 和 C++ 程序。

> Windows 操作系统中，人们更习惯使用一些已经集成好的开发环境（IDE），如 VS、VC、Dev-C++ 等，它们的内部已经嵌套了相应的调试器。

![GDB的吉祥物：弓箭鱼](images/1-2002122135363V.gif)
<center>图 1 GDB 的吉祥物：弓箭鱼</center>

总的来说，借助 GDB 调试器可以实现以下几个功能：
1. 程序启动时，可以按照我们自定义的要求运行程序，例如设置参数和环境变量；
2. 可使被调试程序在指定代码处暂停运行，并查看当前程序的运行状态（例如当前变量的值，函数的执行结果等），即支持断点调试；
3. 程序执行过程中，可以改变某个变量的值，还可以改变代码的执行顺序，从而尝试修改程序中出现的逻辑错误。

> 后续章节会做以上功能做详细的讲解，这里简单了解一下即可，不必深究。

正如从事 Windows C/C++ 开发的一定要熟悉 Visual Studio、从事 Java 开发的要熟悉 Eclipse 或 IntelliJ IDEA、从事 Android 开发的要熟悉 Android Studio、从事 iOS 开发的要熟悉 XCode 一样，从事 Linux C/C++ 开发要熟悉 GDB。

另外，虽然 Linux 系统下读者编写 C/C++ 代码的 IDE 可以自由选择，但调试生成的 C/C++ 程序一定是直接或者间接使用 GDB。可以毫不夸张地说，我所做那些 C/C++ 项目的开发和调试包括故障排查都是利用 GDB 完成的，调试是开发流程中一个非常重要的环节，因此对于从事 Linux C/C++ 的开发人员熟练使用 GDB 调试是一项基本要求。

“工欲善其事、必先利其器”，作为一名合格的软件开发者，至少得熟悉一种软件开发工具和调试器， 而对于 Linux C/C++ 后台开发，舍 GDB 其谁。

那么，GDB 如何安装，又该怎样使用，需要记住哪些指令呢？别急，我会后续的文章中给大家做详细的讲解。

## 2. GDB下载和安装教程

基于 Linux 系统的免费、开源，衍生出了多个不同的 Linux 版本，比如 Redhat、CentOS、Ubuntu、Debian 等。这些 Linux 发行版中，有些默认安装有 GDB 调试器，但有些默认不安装。

判断当前 Linux 发行版是否安装有 GDB 的方法也很简单，就是在命令行窗口中执行 gdb -v 命令。以本机安装的 CentOS 系统为例：
```
[root@bogon ~]# gdb -v`
bash: gdb: command not found`
```

如上所示，执行结果为“command not found”，表明当前系统中未安装 GDB 调试器。反之，若执行结果为：
```
[root@bogon ~]# gdb -v`
GNU gdb (GDB) Red Hat Enterprise Linux (7.2-92.el6)`
Copyright (C) 2010 Free Software Foundation, Inc.`
....... <-省略部分信息`
```

则表明当前系统安装了 GDB 调试器。

对于尚未安装 GDB 的 Linux 发行版，安装方法通常有以下 2 种：

1. 直接调用该操作系统内拥有的 GDB 安装包，使用包管理器进行安装。此安装方式的好处是速度快，但通常情况下安装的并非 GDB 的最新版本；
2. 前往 GDB 官网下载源码包，在本机编译安装。此安装方式的好处是可以任意选择 GDB 的版本，但由于安装过程需要编译源码，因此安装速度较慢。

> 注意，不同的 Linux 发行版，管理包的工具也不同。根据维护团体（商业公司维护和社区组织维护）的不同，可以将众多 Linux 发行版分为 2 个系列，分别为 RedHat 系列和 Debian 系列。其中 RedHat 系列代表 Linux 发行版有 RedHat、CentOS、Fedora 等，使用 yum 作为包管理器；Debian 系列有 Debian、Ubuntu 等，使用 apt 作为包管理器。

### 2.1. 快速安装GDB

对于 RedHat 系列的 Linux 发行版，通过在命令行窗口中执行`sudo yum -y install gdb`指令，即可实现 GDB 调试器的安装。这里以 CentOS 为例，执行该指令的过程为：
```
[root@bogon ~]# gdb -v
bash: gdb: command not found                        <--当前系统中没有GDB 
[root@bogon ~]# sudo yum -y install gdb               <--安装 GDB
Loaded plugins: fastestmirror, refresh-packagekit, security
Loading mirror speeds from cached hostfile
......  <-省略部分过程
Installed:
 gdb.x86_64 0:7.2-92.el6                           

Complete!
[root@bogon ~]# gdb -v
GNU gdb (GDB) Red Hat Enterprise Linux (7.2-92.el6)        <--安装成功
```

可以看到，GDB 安装成功。

对于 Debian 系列的 Linux 发行版，通过执行`sudo apt -y install gdb`指令，即可实现 GDB 的安装。感兴趣的读者可自行验证，这里不再过多赘述。

> 注意，使用 yum 或者 apt 更多情况需要借助网络下载所需使用的安装包，这也就意味着，如果读者所用系统没有网络环境，很有可能安装失败。

### 2.2. 源码安装GDB

和使用 yum（apt）自动安装 GDB 不同，手动安装需提前到 GDB 官网下载相应的源码包，读者可直接点击 [GDB源码包](http://ftp.gnu.org/gnu/gdb/)进行下载。值得一提的是，每个 GDB 版本都提供有 tar.gz 和 tar.xz 这 2 种格式的压缩包，这里以 tar.gz 格式为例教大家安装 GDB。

> 注意，在安装 GDB 之前，读者必须保证当前操作系统中有可以使用的编译器，比如最常用的 GCC 编译器（应同时支持 gcc 和 g++ 指令），有关 GCC 编译器的下载和安装，读者可阅读《[GCC下载和安装](http://c.biancheng.net/view/7933.html)》一文。另外，源码安装 GDB 需要用到 Makefile 相关的知识，读者可完全遵循以下步骤“照猫画虎”地安装 GDB。对 Makefile 感兴趣的读者，可前往《[Makefile教程](http://c.biancheng.net/makefile/)》做系统了解。

本节下载的 GDB 源码包为 gdb-9.2-tar.gz，接下来以 CentOS 系统为例（也同样适用于其它 Linux 发行版），给大家演示整个安装过程：
1) 找到 gdb-9.2-tar.gz 文件，笔者将下载好的 gdb-9.2-tat.gz 放置在 /usr/local/src 目录下：
```
[root@bogon ~]# cd /usr/local/src
[root@bogon src]# ls
gdb-9.2.tar.gz
```

2) 使用 tar 命令解压该文件，执行命令如下：
```
[root@bogon ~]# tar -zxvf gdb-9.2.tar.gz
--省略解压过程的输出结果
[root@bogon src]# ls
gdb-9.2 gdb-9.2.tar.gz
```

此步骤会得到 gdb-9.2.tar.gz 相应的解压文件 gdb-9.2 。

3) 进入 gdb-9.2 目录文件，创建一个 gdb_build_9.2 目录并进入，为后续下载并放置安装GDB 所需的依赖项做准备：
```
[root@bogon src]# cd gdb-9.2
[root@bogon gdb-9.2]# mkdir gdb-build-9.2
[root@bogon src]# cd gdb-build-9.2
```

4) 在此基础上，继续执行如下指令：
```
[root@bogon gdb-build-9.2]# ../configure
......  <--省略众多输出
configure: creating ./config.status
config.status: creating Makefile
```

5) 执行 make 指令编译整个 GDB 源码文件，此过程可能会花费很长时间，读者耐心等待即可：
```
[root@bogon gdb-build-9.2]# make
...... <-- 省略编译过程产生的输出结果
```

> 注意，如果编译过程中出现错误，极有可能是所用的 GCC 编译器版本过低导致的，可尝试升级 GCC 版本后再重新执行 make 命令。

6) 确定整个编译过程没有出错之后，执行`sudo make install`指令（其中使用 sudo 指令是为了避免操作权限不够而导致安装失败），正式开始安装 GDB 调试器：
```
[root@bogon gdb-build-9.2]# sudo make install
...... <-- 省略输出结果
```

以上过程全部成功执行，则表示 GDB 安装成功。通过再次执行 gdb -v 指令，可验证其是否被成功安装。
```
[root@bogon gdb-build-9.2]# gdb -v
GNU gdb (GDB) 9.2
Copyright (C) 2020 Free Software Foundation, Inc.
...... <-- 省略部分输出
```

## 3. Windows平台安装GDB调试器

前面介绍了如何将 GDB 调试器安装到 Linux 发行版系统中，其实它还能在 Windows 平台上使用。和前者不同，GDB 调试器无法直接安装到 Windows 平台上，如果想在 Windows 系统中使用 GDB 调试器，需要一个中间媒介，常用的就是 MinGW。  

MinGw 全称 Minimalist GNU for Windows，应用于 Windows 平台，可以为我们提供一个功能有限的 Linux 系统环境以使用一些 GNU 工具，比如 GCC、GDB、gawk 等。也就是说，如果我们想将 GDB 安装到 Windows 平台，必须提前在该平台上打造出一个虚拟的 Linux 环境，MinGW 就可以帮我们完成这项工作。  

由此，在 Windows 平台上安装 GDB 调试器的过程，分为如下 2 个步骤：

1. 下载并安装 MinGW；
2. 借助 MinGW 安装 GDB 调试器。
  

有关 MinGW 的下载和安装，我已经在《[MinGW下载和安装教程](http://c.biancheng.net/view/8077.html)》一节中做了详细的讲解，这里不再重复赘述（安装好的 MinGW 如图 1 所示）。本节要重点介绍的，就是当安装完成 MinGW 之后，如何安装 GDB 调试器。  
![MinGW安装完成](images/1-200QP95301W5.gif)

<center>图 1 MinGW 安装完成 MinGW安装GDB调试器</center>

### 3.1. MinGW安装GDB调试器

使用 MinGW 安装 GDB 调试器的方法有 2 种，一种是利用 MinGW 的在线安装功能，由 MinGW 帮我们下载 GDB 并安装；另一种是由我们手动到 MinGW 官网上下载 GDB 安装包（本质是一个压缩包），然后将其解压到 MinGW 安装目录中的适当位置，由此完成 GDB 调试器的安装。

 1、在线安装GDB调试器

首先介绍较简单的第一种安装方法：  
1) 打开图 1 所示的 MinGW，在右侧框中找到 mingw32-gdb-bin，并勾选它，如图 2 所示：  
![在线安装GDB调试器](images/1-200QQ05950K4.gif)
<center>图 2 在线安装 GDB 调试器</center>

2) 菜单栏中依次选择`Installation -> Apply Changes`，会弹出如下对话框：  
![开始安装GDB调试器](images/1-200QQ1001AP.gif)
<center>图 3 开始安装 GDB 调试器</center>

选择 "Apply"，MinGW 就会自动下载安装 GDB 调试器所需的包。此过程可能需要一段时间，读者耐心等待即可。看在如下对话框，证明 GDB 安装成功：    
![GDB调试器成功安装](images/1-200QQ100412I.gif)
<center>图 4 GDB调试器成功安装</center>
> 注意，在线安装过程中，GDB 安装包可能会下载失败。这种情况下，可以采用第 2 种安装方法。

 2、手动安装GDB调试器

当 MinGW 提示 GDB 调试器安装包下载失败时，我们可以自行打开 [MinGW 官网](https://sourceforge.net/projects/mingw/files/MinGW/Extension/gdb/)（如图 5 所示），下载所需版本的 GDB 调试器。  
![进入GDB安装包下载界面](images/1-200QQ101111S.gif)
<center>图 5 MinGW 官网下载 GDB 调试器安装包
</center>

本节，我们选择安装 7.6.1 版本的 GDB 调试器，点击`gdb-7.6.1-1`，然后找到`gdb-7.6.1-1-mingw32-bin.tar.lzma`，这是一个安装 GDB 的压缩包，直接点击即可开始下载：    
![下载GDB安装包](images/1-200QQ101422I.gif)
<center>图 6 下载 GDB 安装包</center>

下载完成后，读者会得到`gdb-7.6.1-1-mingw32-bin.tar.lzma`压缩包，其内部包含一个`gdb-7.6.1-1-mingw32-bin.tar`的压缩包。所以，我们需要对下载的压缩包进行 2 次压缩，最终得到 bin 和 share 这 2 个文件夹。  

将 bin 文件夹内的 2 个可执行文件 gdb.exe 和 gdbserver.exe 拷贝到 MinGW 安装目录中的 bin 文件夹内（本机 MinGW 安装目录为 E:\MinGW），即可完成 GDB 的安装。

3、修改PATH环境变量

注意，无论采用以上哪种安装方式，最终都需要将 MinGW 的 bin 文件夹所在路径添加到 PATH 环境变量中。以本机为例，我将 MinGW 安装到 E:\MinGW 路径下，因此需要将 E:\MinGW\bin 添加至 PATH 环境变量中，如图 7 所示：   
![设置PATH环境变量](images/1-200QQ102251Y.gif)
<center>图 7 设置 PATH 环境变量</center>

> PATH环境变量打开方法是：右击计算机（我的电脑） -> 属性 -> 高级系统设置 -> 环境变量，建议读者修改当前用户的 PATH 环境变量。

由此，GDB 调试器就算安装成功了。打开命令行窗口，输入`gdb -v`命令，如果输入如下信息：  
 ![GDB调试器安装成功的示意图](images/1-200QQ10254163.gif)
<center>图 8 GDB 调试器安装成功的示意图</center>

就证明 GDB 调试器安装成功，我们可以像在 Linux 系统中使用 GDB 那样，在 Windows 平台上使用 GDB 调试器。  

## 4. Sublime Text配置GDB调试环境

作为当下一款非常流行的文本编辑器，Sublime Text 界面简洁、功能强大（提供有众多的插件）、还支持跨平台使用（包括 Mac OS X、Linux 和 Windows）。更值得一提的是在程序员眼中，Sublime Text 不仅仅是一个文本编辑器，更是一款好用的代码编辑器。

《[Sublime Text运行C和C++程序](http://c.biancheng.net/view/8094.html)》一节中，给大家详细介绍了怎样使 Sublime Text 具备编译、运行 C、C++ 程序的能力。在此基础上，本节给大家讲解：如何为 Sublime Text 配备 GDB 调试环境，从而可以直接调试在 Sublime Text 中编写的程序？

> 为 Sublime Text 配备 GDB 调试环境，其本质是令 Sublime Text 具备调用 GDB 调试器的能力。也就是说，当我们需要调试在 Sublime Text 中编写的代码时，无需手动打开 GDB 调试器，Sublime Text 可以自行调用 GDB 调试器，并以当前文件作为调试对象打开 GDB，等待我们输入 GDB 调试指令。

注意，要想为 Sublime Text 配备 GDB 调试环境，则必须确认当前系统环境中已经安装好了 GDB 调试器。以 Windows 平台为例，打开命令行窗口并执行 gdb -v 命令，如果打印出 GDB 调试器的相关信息（如图 1 所示），则表明当前系统中已经安装好了 GDB。
![Windows平台成功安装了GDB调试器](images/1-200QQ10254163-1688632849008-17.gif)
<center>图 1 Windows平台成功安装了 GDB 调试器</center>

> 如果当前系统环境未安装 GDB，Windows 平台的读者可阅读《[Windows平台安装GDB调试器](http://c.biancheng.net/view/8296.html)》一节；Linux 平台的读者可阅读《[GDB下载和安装教程](http://c.biancheng.net/view/8130.html)》一节。

### 4.1. Windows平台配置Sublime Text

在已安装好 Sublime Text 编辑器的基础上，为其配置 GDB 调试环境的过程如下（以调试 C 语言程序为例）：
1) 在 Sublime Text 的菜单栏中依次选择`Tools -> Build System -> New Build System`，此时会生成如图 2 所示的临时文件：
![img](images/2-200QQGI0B3.gif)
<center>图 2 Sublime Text配置文件</center>

2) 将图 2 所示文件中的内容全部删除，然后将以下内容拷贝到文件中：
```
{
  "cmd": ["gcc","-g","${file}","-o", "${file_path}/${file_base_name}"],
  "file_regex":"^(..[^:]*):([0-9]+):?([0-9]+)?:? (.*)$",
  "working_dir":"${file_path}",
  "selector": "source.c",
  "variants":
  [
    {
      "name": "GDB_C",
      "cmd": ["cmd", "/c", "gcc","-g", "${file}", "-o", "${file_path}/${file_base_name}", "&&", "start", "cmd", "/c", "gdb", "${file_path}/${file_base_name}"]
    }
  ]
}
```

> 如果读者想调试 C++ 程序，可直接将其中的 gcc 全部改成 g++ 即可。当然，这样配置的 Sublime Text 仅能调试普通的 C、C++ 程序，<font color='red'>那些引入非标准库或者第三方库的程序，需要在 2 个 "cmd" 行中的 gcc 或者 g++ 命令中注明要使用的系统库</font>。

拷贝完成后，按 Ctrl+S 组合键保存此文件，对于调试 C 语言程序的配置文件，我将其重命名为 gdb_c.sublime-build。

由此，Sublime Text 就具备了调用 GDB 调试 C、C++ 程序的能力。以调试简单的 C 语言程序为例：
```
#include <stdio.h>
int main ()
{
    char * url="http://c.biancheng.net/gdb/";
    printf("%s",url);
    return 0;
}
```

读者可以将此示例程序拷贝到 Sublime Text 中，并为其命名以 .c 为后缀名的文件。在此基础上，在菜单栏中选择 Tools -> Build System -> gdb_c（即以我们自定义的方式编译指定文件），然后选择 Tools -> Build System，此时 Sublime Text 会弹出如下图所示的选项框：
![img](images/2-200QZ93931628.gif)
<center>图 3 选择具体的调试选项</center>

可以看到，正如 gdb_c.sublime-build 文件配置的那样，这里显示了 2 个调试选项，第一个用于仅生成可供 GDB 调试的可执行文件，第二个选项不仅会生成可调式的执行文件，还会自动调用 GDB 并启动调试。例如，我们选择第二个选项，此时会进入 GDB 调试环境：
![img](images/2-200QZ93950241.gif)
<center>图 4 Sublime Text 自行启动 GDB 调试环境</center>

> 感兴趣的读者，可尝试使用第一个选项，其会在该程序文件同目录的位置，生成可供 GDB 调试的可执行文件。

### 4.2. Linux平台配置Sublime Text

Linux 平台配置 Sublime Text 的过程，和 Windows 平台配置 Sublime Text 的过程完全一致。唯一不同的一点在于，gdb-c.sublime-build 配置文件中的内容有所差异。

以 Ubuntu 为例，其配置文件中的内容如下所示：
```
{
  "shell_cmd": "gcc -g \"$file_name\" -o \"$file_base_name\"",
  "file_regex": "^(..[^:]*):([0-9]+):?([0-9]+)?:? (.*)$",
  "selector": "source.c",
  "working_dir": "$file_path",
  "variants":
  [
    { 
    "name": "gdb_c",
    "shell_cmd": "gcc -g \"$file_name\" -o \"$file_base_name\";gnome-terminal -x gdb \"$file_base_name\""
    }
  ]
}
```

> 注意，不同的 Linux 发行版，调用命令行窗口的方式不同。以 Ubuntu 为例，使用 gnome-terminal -x 命令启动命令行窗口。

其它操作和在 Windows 平台上配置 Sublime_text 完全相同，这里不再重复赘述。

### 4.3. Mac OS X配置Sublime Text

Mac OS X 平台配置 Sublime Text 的过程，也和 Windows 平台配置 Sublime Text 的过程完全一致，但需要修改 gdb-c.sublime-build 配置文件中的内容：
```
{
  "shell_cmd":"gcc -g \"$file_name\" -o \"$file_base_name\"",
  "selector":"source.c",
  "working_dir":"$file_path",
  "variants":
     [{
       "name":"gdb_c",
       "shell_cmd":"gcc -g \"$file_name\" -o \"$file_base_name\";open -a terminal.app /usr/local/bin/gdb"
    }]
}
```

读者需根据实际情况，调整 gdb 的存储位置（本机存储为 /usr/local/bin/gdb）。

注意，根据此配置文件，当我们选用 gdb_c 选项调试程序时，其只能自行打开 Terminal 命令行窗口并启动 GDB 调试器，但无法立即调试目标程序，还需手动在 GDB 中借助 file 命令指定要调试的目标程序，例如：

```
(gdb) file /tmp/demo/main.exe
Reading symbols from /tmp/demo/main.exe...
(gdb)
```

> 再次强调，启动 GDB 并不意味着可以立即调试程序，至于为什么，读者可阅读[7. gdb run（r）命令：启动程序](#7.%20gdb%20run（r）命令：启动程序)一节做详细了解。

## 5. GDB调试C/C++程序

通过前面的学习，读者已经了解了什么是 GDB，以及如何下载并安装它。从本节开始，我们将正式学习使用 GDB 调试 C、C++ 程序。

如下是一段能够正常编译运行的 C 语言程序：
```
#include <stdio.h>
int main ()
{
    unsigned long long int n, sum;
    n = 1;
    sum = 0;
    while (n <= 100)
    {
        sum = sum + n;
        n = n + 1;
    }
    return 0;
}
```

> 此源码的完整存储路径为 /tmp/demo/main.c。

本节就以此程序为例，给大家演示 GDB 调试器的基本用法。

### 5.1. 使用GDB的前期准备

通过前面的学习我们知道，GDB 的主要功能就是监控程序的执行流程。这也就意味着，只有当源程序文件编译为可执行文件并执行时，GDB 才会派上用场。

Linux 发行版中，经常使用 GCC 编译 C、C++ 程序（有关 GCC 编译器，读者可猛击《[GCC编译器](http://c.biancheng.net/gcc/)》系统学习）。但需要注意的是，仅使用 gcc（或 g++）命令编译生成的可执行文件，是无法借助 GDB 进行调试的。

以 main.c 源文件为例，正常情况下，使用 GCC 编译该源代码的指令如下：
```
[root@bogon demo]# ls
main.c
[root@bogon demo]# gcc main.c -o main.exe
[root@bogon demo]# ls
main.c main.exe
```

可以看到，这里已经生成了 main.c 对应的执行文件 main.exe，但值得一提的是，此文件不支持使用 GDB 进行调试。原因很简单，使用 GDB 调试某个可执行文件，该文件中必须包含必要的调试信息（比如各行代码所在的行号、包含程序中所有变量名称的列表（又称为符号表）等），而上面生成的 main.exe 则没有。

那么，如何生成符合 GDB 调试要求的可执行文件呢？很简单，只需要使用 gcc -g 选项编译源文件，即可生成满足 GDB 要求的可执行文件。仍以 main.c 源程序文件为例：
```
[root@bogon demo]# ls
main.c
[root@bogon demo]# gcc main.c -o main.exe -g
[root@bogon demo]# ls
main.c main.exe
```

由此生成的 main.exe，即可使用 GDB 进行调试。

> 较早以前的 C 语言编译器也允许使用 -gg 选项来产生调试信息，但是现在版本的 GDB 不再支持这种格式产生的调试信息，所以不建议使用 -gg 选项。

值得一提的是，GCC 编译器支持 -O（等于同 -O1，优化生成的目标文件）和 -g 一起参与编译。GCC 编译过程对进行优化的程度可分为 5 个等级，分别为 O0~O4，O0 表示不优化（默认选项），从 O1 ~ O4 优化级别越来越高，O4 最高。

> 所谓优化，例如省略掉代码中从未使用过的变量、直接将常量表达式用结果值代替等等，这些操作会缩减目标文件所包含的代码量，提高最终生成的可执行文件的运行效率。

而相对于 -O -g 选项，对 GDB 调试器更友好的是 -Og 选项，-Og 对代码所做的优化程序介于 O0 ~ O1 之间，真正可做到“在保持快速编译和良好调试体验的同时，提供较为合理的优化级别”。

解决了如何生成满足 GDB 调试器要求的可执行文件，接下来正式学习 GDB 调试器的使用。

### 5.2. 启动GDB调试器

在生成包含调试信息的 main.exe 可执行文件的基础上，启动 GDB 调试器的指令如下：
```
[root@bogon demo]# gdb main.exe
GNU gdb (GDB) 8.0.1
Copyright (C) 2017 Free Software Foundation, Inc.
......
(gdb) 
```

注意，该指令在启动 GDB 的同时，会打印出一堆免责条款。通过添加 --silent（或者 -q、--quiet）选项，可将比部分信息屏蔽掉：
```
[root@bogon demo]# gdb main.exe --silent
Reading symbols from main.exe...(no debugging symbols found)...done.
(gdb) 
```

无论使用以上哪种方式，最终都可以启动 GDB 调试器，启动成功的标志就是最终输出的 (gdb)。通过在 (gdb) 后面输入指令，即可调用 GDB 调试进行对应的调试工作。

GDB 调试器提供有大量的调试选项，可满足大部分场景中调试代码的需要。如表 1 所示，罗列了几个最常用的调试指令及各自的作用：
<center>表 1 GDB常用的调试指令</center>

| 调试指令                    | 作 用                                                        |
| --------------------------- | ------------------------------------------------------------ |
| (gdb) break xxx (gdb) b xxx | 在源代码指定的某一行设置断点，其中 xxx 用于指定具体打断点的位置。 |
| (gdb) run (gdb) r           | 执行被调试的程序，其会自动在第一个断点处暂停执行。           |
| (gdb) continue (gdb) c      | 当程序在某一断点处停止运行后，使用该指令可以继续执行，直至遇到下一个断点或者程序结束。 |
| (gdb) next (gdb) n          | 令程序一行代码一行代码的执行。                               |
| (gdb) print xxx (gdb) p xxx | 打印指定变量的值，其中 xxx 指的就是某一变量名。              |
| (gdb) list (gdb) l          | 显示源程序代码的内容，包括各行代码所在的行号。               |
| (gdb) quit (gdb) q          | 终止调试。                                                   |

> 如上所示，每一个指令既可以使用全拼，也可以使用其首字母表示。另外，表 1 中罗列的指令仅是冰山一角，GDB 还提供有大量的选项，可以通过 help 选项来查看。有关 help 选项的具体用法，读者可阅读《[GDB查看命令](http://c.biancheng.net/view/7418.html)》一节，这里不再做具体赘述。

仍以 main.exe 可执行程序为例，接下来为读者演示表 1 中部分选项的功能和用法：
```
(gdb) l           <-- 显示带行号的源代码
1 #include <stdio.h>
2 int main ()
3 {
4   unsigned long long int n, sum;
5   n = 1;
6   sum = 0;
7   while (n <= 100)
8   {
9     sum = sum + n;
10     n = n + 1;
(gdb)           <-- 默认情况下，l 选项只显示 10 行源代码，如果查看后续代码，安装 Enter 回车即可                                
11   }
12   return 0;
13 }
(gdb) b 7        <-- 在第 7 行源代码处打断点
Breakpoint 1 at 0x400504: file main.c, line 7.
(gdb) r          <-- 运行程序，遇到断点停止
Starting program: /home/mozhiyan/demo1/main.exe

Breakpoint 1, main () at main.c:7
7   while (n <= 100)
Missing separate debuginfos, use: debuginfo-install glibc-2.17-55.el7.x86_64
(gdb) p n        <-- 查看代码中变量 n 的值
$1 = 1          <-- 当前 n 的值为 1，$1 表示该变量所在存储区的名称
(gdb) b 12       <-- 在程序第 12 行处打断点
Breakpoint 2 at 0x40051a: file main.c, line 12.
(gdb) c         <-- 继续执行程序
Continuing.

Breakpoint 2, main () at main.c:12
12   return 0;
(gdb) p n        <-- 查看当前 n 变量的值
$2 = 101        <-- 当前 n 的值为 101
(gdb) q         <-- 退出调试
A debugging session is active.

Inferior 1 [process 3080] will be killed.

Quit anyway? (y or n) y         <-- 确实是否退出调试，y 为退出，n 为不退出
[root@bogon demo]#
```

> 后续章节会对以上指令做详细的讲解，这里简单了解即可，不必深究。

## 6. 调用GDB调试器的4种方式

[5. GDB调试C/C++程序](#5.%20GDB调试C/C++程序) GDB 调试 C（或者 C++）程序的整个过程，其中对 main.exe 文件启动 GDB 调试，执行的指令为：
```
[root@bogon demo]# gdb main.exe
GNU gdb (GDB) 8.0.1
Copyright (C) 2017 Free Software Foundation, Inc.
......
(gdb) 
```

要知道，这仅是调用 GDB 调试器最常用的一种方式，GDB 调试器还有其它的启动方式。并且，为了满足不同场景的需要，启动 GDB 调试器时还可以使用一些参数选项，从而控制它启动哪些服务或者不启动哪些服务。

### 6.1. 调用GDB的方式

总的来说，调用 GDB 调试器的方法有 4 种。
#### 6.1.1. 直接使用 gdb 指令启动 GDB 调试器
```
[root@bogon demo]# gdb
ubuntu64@ubuntu64-virtual-machine:~/demo$ gdb
GNU gdb (GDB) 8.0.1
Copyright (C) 2017 Free Software Foundation, Inc.
...... <-- 省略部分输出信息
Type "apropos word" to search for commands related to "word".
(gdb)
```

此方式启动的 GDB 调试器，由于事先未指定要调试的具体程序，因此需启动后借助 file 或者 exec-file 命令指定（后续章节会做详细讲解）。

#### 6.1.2. 调试尚未执行的程序

对于具备调试信息（使用 -g 选项编译而成）的可执行文件，调用 GDB 调试器的指令格式为：
```
gdb program
```
其中，program 为可执行文件的文件名，例如上节创建好的 main.exe。

#### 6.1.3. 调试正在执行的程序

在某些情况下，我们可能想调试一个当前已经启动的程序，但又不想重启该程序，就可以借助 GDB 调试器实现。

也就是说，GDB 可以调试正在运行的 C、C++ 程序。要知道，每个 C 或者 C++ 程序执行时，操作系统会使用 1 个（甚至多个）进程来运行它，并且为了方便管理当前系统中运行的诸多进程，每个进程都配有唯一的进程号（PID）。

如果需要使用 GDB 调试正在运行的 C、C++ 程序，需要事先找到该程序运行所对应的进程号。查找方式很简单，执行如下命令即可：
```
pidof 文件名
```

比如，我们将上节创建的 main.c 源文件修改为：
```
#include <stdio.h>
int main()
{
    int num = 1;
    while(1)
    {
        num++;
    }
    return 0;
}
```

执行 gcc main.c -o main.exe -g 编译指令，获得该源程序对应的具备调试信息的 main.exe 可执行文件，并在此基础上执行：
```
[root@bogon demo]# ./main.exe
    <--程序一直运行
```


显然，程序中存在死循环（5~8 行），它会一直执行。此时，借助 pidof 指令即可获取它对应的进程号：
```
[root@bogon demo]# pidof main.exe
1830
```

可以看到，当前正在执行的 main.exe 对应的进程号为 1830。在此基础上，可以调用 GDB 对该程序进行调试，调用指令有以下 3 种形式：
```
1) gdb attach PID
2) gdb 文件名 PID
3) gdb -p PID
```

其中，PID 指的就是要调取的程序对应的进程号。

以调试进程号为 1830 的程序为例，执行如下指令：
```
[root@bogon demo]# gdb -p 1830
GNU gdb (GDB) 8.0.1
Copyright (C) 2017 Free Software Foundation, Inc.
...... <-- 省略部分输出信息
0x00005645319c613c in main () at main.c:6
6     num++;
```

注意，当 GDB 调试器成功连接到指定进程上时，程序执行会暂停。如上所示，程序暂停至第 6 行代码 num++ 的位置，此时可以通过断点调试、逐步运行等方式监控程序的执行过程。例如：
```
(gdb) l   <-- 查看源码以及各行行号
1 #include<stdio.h>
2 int main()
3 {
4   int num = 1;
5   while(1){
6     num++;
7   }
8   return 0;
9 }
(gdb) b 6   <--在程序第 6 行代码处打断点
Breakpoint 1 at 0x5645319c6138: file main.c, line 6.
(gdb) c     <--令程序进行执行，其会在下一个断点处停止
Continuing.

Breakpoint 1, main () at main.c:6
6     num++;
(gdb) p num     <-- 查看当前 num 的值
$2 = 47100335
```

注意，当调试完成后，如果想令当前程序进行执行，消除调试操作对它的影响，需手动将 GDB 调试器与程序分离，分离过程分为 2 步：

1. 执行 detach 指令，使 GDB 调试器和程序分离；
2. 执行 quit（或 q）指令，退出 GDB 调试。

#### 6.1.4. 调试执行异常崩溃的程序

除了以上 3 种情况外，C 或者 C++ 程序运行过程中常常会因为各种异常或者 Bug 而崩溃，比如内存访问越界（例如数组下标越界、输出字符串时该字符串没有 \0 结束符等）、非法使用空指针等，此时就需要调试程序。

值得一提的是，在 Linux 操作系统中，当程序执行发生异常崩溃时，系统可以将发生崩溃时的内存数据、调用堆栈情况等信息自动记录下载，并存储到一个文件中，该文件通常称为 core 文件，Linux 系统所具备的这种功能又称为核心转储（core dump）。幸运的是，GDB 对 core 文件的分析和调试提供有非常强大的功能支持，当程序发生异常崩溃时，通过 GDB 调试产生的 core 文件，往往可以更快速的解决问题。

默认情况下，Linux 系统是不开启 core dump 这一功能的，读者可以借助执行`ulimit -c`指令来查看当前系统是否开启此功能：
```
[root@bogon demo]# ulimit -a
core file size     (blocks, -c) 0
data seg size      (kbytes, -d) unlimited
scheduling priority       (-e) 0
file size        (blocks, -f) unlimited
......
```

其中，如果 core file size（core 文件大小）对应的值为 0，表示当前系统未开启 core dump 功能。这种情况下，可以通过执行如下指令改变 core 文件的大小：
```
[root@bogon demo]# ulimit -c unlimited
[root@bogon demo]# ulimit -a
core file size     (blocks, -c) unlimited
data seg size      (kbytes, -d) unlimited
scheduling priority       (-e) 0
file size        (blocks, -f) unlimited
......
```

> 其中，unlimited 表示不限制 core 文件的大小。

由此，当程序执行发生异常崩溃时，系统就可以自动生成相应的 core 文件。

举个例子，修改 main.c 源程序文件中的代码为：
```
#include <stdio.h>
int main()
{
    char *p = NULL;
    *p = 123;
    return 0;
}
```

重新编译，即执行如下指令：

```
[root@bogon demo]# gcc main.c -o main.exe -g
[root@bogon demo]# ./main.exe
Segmentation fault (core dumped)  <--发生段错误，并生成了 core 文件
[root@bogon demo]# ls
core main.c main.exe
```
> 段错误又称为访问权限冲突，指的是当前程序访问了不可访问的存储空间，比如访问的不存在的空间，又或者是受系统保护的内存空间。

观察此程序不难发现，由于 p 指针初始化为 NULL，即不指向任何存储空间，但后续却执行`*p=123`操作，显然是不可行的。因此，该程序执行时会发生崩溃，Linux 系统会记录必要的崩溃信息，并存储到 core 文件中。

> 默认情况下，core 文件的生成位置同该程序所在的目录相同。当然我们也可以指定 core 文件的生成的位置，感兴趣的读者可自行研究，这里不再介绍。

对于 core 文件的调试，其调用 GDB 调试器的指令为：
```
[root@bogon demo]# gdb main.exe core
GNU gdb (GDB) 8.0.1
Copyright (C) 2017 Free Software Foundation, Inc.
......
Reading symbols from main.exe...
[New LWP 4296]

warning: Unexpected size of section `.reg-xstate/4296' in core file.
Core was generated by `./main.exe'.
Program terminated with signal SIGSEGV, Segmentation fault.

warning: Unexpected size of section `.reg-xstate/4296' in core file.
\#0 0x00005583b933013d in main () at main.c:5
5   *p = 123;
```

可以看到，程序发生崩溃的位置是在 main.c 中的第 5 行。甚至于，对于 core 文件中记录的崩溃信息，可以使用 where、print、bt 等指令查看，有关这些指令的功能和用法，由于并非本节重点，这里不再具体赘述，后续章节会做详细讲解。

### 6.2. GDB调试器启动可用参数

表 1 罗列了一些在启动 GDB 调试器时常用的指令参数，以及它们各自的功能。
<center>表 1 启动GDB调试器可用参数</center>

| 参 数                 | 功 能                                                        |
| --------------------- | ------------------------------------------------------------ |
| -pid number -p number | 调试进程 ID 为 number 的程序。                               |
| -symbols file -s file | 仅从指定 file 文件中读取符号表。                             |
| -q -quiet -silent     | 取消启动 GDB 调试器时打印的介绍信息和版权信息                |
| -cd directory         | 以 directory 作为启动 GDB 调试器的工作目录，而非当前所在目录。 |
| --args 参数1 参数2... | 向可执行文件传递执行所需要的参数。                           |

其中有些参数，我们已经在前面的学习给大家做了具体的演示，这里不再重复赘述，读者可自行尝试使用。除此之外，启动 GDB 调试器时还有其它参数指令可以使用，感兴趣的读者可查阅 GDB 官网做系统了解。有关表 1 以及 GDB 调试器支持的其它指令，后续章节用到时会做详细讲解。

## 7. gdb run（r）命令：启动程序

使用 GDB 调试器调试程序的过程，其实就是借助 GDB 调试器来监控程序的执行流程，进而发现程序中导致异常或者 Bug 的代码。通过前面章节的学习，读者已经学会了如何启动 GDB 调试器，在此基础上，本节继续为大家讲解如何在 GDB 调试器中启动（运行）程序，以及启动程序过程中的一些注意事项。

根据不同场景的需要，GDB 调试器提供了多种方式来启动目标程序，其中最常用的就是 run 指令，其次为 start 指令。也就是说，run 和 start 指令都可以用来在 GDB 调试器中启动程序，它们之间的区别是：

- 默认情况下，run 指令会一直执行程序，直到执行结束。如果程序中手动设置有断点，则 run 指令会执行程序至第一个断点处；
- start 指令会执行程序至 main() 主函数的起始位置，即在 main() 函数的第一行语句处停止执行（该行代码尚未执行）。

> 可以这样理解，使用 start 指令启动程序，完全等价于先在 main() 主函数起始位置设置一个断点，然后再使用 run 指令启动程序。另外，程序执行过程中使用 run 或者 start 指令，表示的是重新启动程序。

问一个问题，GDB 调试器启动后是否就可以直接使用 run 或者 start 指令了呢？答案当然是否定的。我们知道，启动 GDB 调试器的方式有多种，其中简单的方法就是直接使用 gdb 指令，例如：
```
[root@bogon demo]# gdb
GNU gdb (GDB) 8.0.1
Copyright (C) 2017 Free Software Foundation, Inc.
...... <-- 省略部分输出信息
Type "apropos word" to search for commands related to "word".
(gdb)
```

注意，使用此方式启动的 GDB 调试器，尚未指定要调试的目标程序，何谈使用 run 或者 start 指令呢？

不仅如此，在进行 run 或者 start 指令启动目标程序之前，还可能需要做一些必要的准备工作，大致包括以下几个方面：

- 如果启动 GDB 调试器时未指定要调试的目标程序，或者由于各种原因 GDB 调试器并为找到所指定的目标程序，这种情况下就需要再次手动指定；
- 有些 C 或者 C++ 程序的执行，需要接收一些参数（程序中用 argc 和 argv[] 接收）；
- 目标程序在执行过程中，可能需要临时设置 PATH 环境变量；
- 默认情况下，GDB 调试器将启动时所在的目录作为工作目录，但很多情况下，该目录并不符合要求，需要在启动程序手动为 GDB 调试器指定工作目录。
- 默认情况下，GDB 调试器启动程序后，会接收键盘临时输入的数据，并将执行结果会打印在屏幕上。但 GDB 调试器允许对执行程序的输入和输出进行重定向，使其从文件或其它终端接收输入，或者将执行结果输出到文件或其它终端。

假设使用 GDB 调试器调试如下程序：
```
//存储路径为 /tmp/demo/main.c
//其生成的可执行文件为 main.exe，位于同一路径下
#include<stdio.h>
int main(int argc,char* argv[])
{
    FILE * fp;
    if((fp = fopen(argv[1],"r")) == NULL){
        printf("file open fail");
    }
    else{
        printf("file open true");
    }
    return 0;
}
```

要知道，命令行窗口打开时默认位于 ~ （表示当前用户的主目录）路径下，假设我们就位于此目录中使用 gdb 命令启动 GDB 调试器，则在执行 main.exe 之前，有以下几项操作要做：

1) 首先，对于已启动的 GDB 调试器，我们可以先通过 l （小写的 L）指令验证其是否已找到指定的目标程序文件：
```
[root@bogon ~]# gdb -q   <-- 使用 -q 选项，可以省略不必要的输出信息
(gdb) l
No symbol table is loaded. Use the "file" command.
```

可以看到，对于找不到目标程序文件的 GDB 调试器，l 指令的执行结果显示“无法加载符号表”。这种情况下，我们就必须手动为其指定要调试的目标程序，例如：
```
(gdb) file /tmp/demo/main.exe
Reading symbols from /tmp/demo/main.exe...
(gdb) l
1 #include<stdio.h>
2 int main(int argc,char* argv[])
3 {
4   FILE * fp;
5   if((fp = fopen(argv[1],"r")) == NULL){
6     printf("file open fail");
7   }
8   else{
9     printf("file open true");
10   }
(gdb)
11   return 0;
12 }
(gdb)
```

可以看到，通过借助 file 命令，则无需重启 GDB 调试器也能指定要调试的目标程序文件。

> 除了 file 指令外，GDB 调试器还提供有其它的指定目标调试文件的指令，感兴趣的读者可千万[ GDB 官网](https://sourceware.org/gdb/current/onlinedocs/gdb/Files.html#Files)做详细了解，后续章节在用到时也会做详细的讲解。

2) 通过分析 main.c 中程序的逻辑不难发现，要想其正确执行，必须在执行程序的同时给它传递一个目标文件的文件名。

总的来说，为 GDB 调试器指定的目标程序传递参数，常用的方法有 3 种：
1、启动 GDB 调试器时，可以在指定目标调试程序的同时，使用 --args 选项指定需要传递给该程序的数据。仍以 main.exe 程序为例：
```
[root@bogon demo]# gdb --args main.exe a.txt
```

整个指令的意思是：启动 GDB 调试器调试 main.exe 程序，并为其传递 "a.txt" 这个字符串（其会被 argv[] 字符串数组接收）。

2、GDB 调试器启动后，可以借助 set args 命令指定目标调试程序启动所需要的数据。仍以 main.exe 为例：
```
(gdb) set args a.txt
```

该命令表示将 "a.txt" 传递给将要调试的目标程序。

3、除此之外，还可以使用 run 或者 start 启动目标程序时，指定其所需要的数据。例如：
```
(gdb) run a.txt
(gdb) start a.txt
```

以上 2 条命令都可以将 "a.txt" 传递给要调试的程序。

3) 要知道，对于调试 /tmp/demo/ 路径下的 main.exe 文件，将其作为 GDB 调试器的工作目录，一定程度上可以提高我们的调试效率。反之，如果 GDB 调试器的工作目录和目标调试文件不在同一目录，则很多时候需要额外指明要操作文件的存储路径（例如第 1) 种情况中用 file 指令指明调试文件时就必须指明其存储位置）。

默认情况下，GDB 调试器的工作目录为启动时所使用的目录。例如在 ~ 路径下启动的 GDB 调试器，其工作目录就为 ~（当前用户的 home 目录）。当然，GDB 调试器提供有修改工作目录的指令，即 cd 指令。例如，将 GDB 调试器的工作目录修改为 /tmp/demo，则执行指令为：
```
(gdb) cd /tmp/demo
```

由此，GDB 调试器的工作目录就变成了 /tmp/demo。

4) 某些场景中，目标调试程序的执行还需要临时修改 PATH 环境变量，此时就可以借助 path 指令，例如：
```
(gdb) path /temp/demo
Executable and object file path: /temp/demo:/usr/local/sbin:/usr/local/bin...
```

> 注意，此修改方式只是临时的，退出 GDB 调试后会失效。

5) 默认情况下，GDB 调试的程序会接收 set args 等方式指定的参数，同时会将输出结果打印到屏幕上。而通过对输入、输出重定向，可以令调试程序接收指定文件或者终端提供的数据，也可以将执行结果输出到文件或者某个终端上。

例如，将 main.exe 文件的执行结果输出到 a.txt 文件中，执行如下命令：
```
(gdb) run > a.txt
```

由此，在 GDB 调试的工作目录下就会生成一个 a.txt 文件，其中存储的即为 main.exe 的执行结果。

总的来说，只有将调试程序所需的运行环境搭建好后，才能使用 run 或者 start 命令开始调试。如下是一个完整的实例，演示了 GDB 调试 mian.exe 之前所做的准备工作：

```
[root@bogon demo]# pwd  <--显示当前工作路径
/tmp/demo
[root@bogon demo]# ls    <-- 显示当前路径下的文件
a.txt main.c main.exe
[root@bogon demo]# cd ~ <-- 进入 home 目录
[root@bogon ~]# gdb -q   <-- 开启 GDB 调试器
(gdb) cd /tmp/demo      <-- 修改 GDB 调试器的工作目录
Working directory /tmp/demo.
(gdb) file main.exe        <-- 指定要调试的目标文件
Reading symbols from main.exe...
(gdb) set args a.txt        <-- 指定传递的数据
(gdb) run                <-- 运行程序
Starting program: /tmp/demo/main.exe a.txt
file open true[Inferior 1 (process 43065) exited normally]
```

## 8. GDB break（b）：设置断点

默认情况下，程序不会进入调试模式，代码会瞬间从开头执行到末尾。要想观察程序运行的内部细节（例如某变量值的变化情况），可以借助 GDB 调试器在程序中的某个地方设置断点，这样当程序执行到这个地方时就会停下来。

所谓<font color='red'>断点（BreakPoint）</font>，读者可以理解为障碍物，人遇到障碍物不能行走，程序遇到断点就暂停执行。

在 GDB 调试器中对 C、C++ 程序打断点，最常用的就是 break 命令，有些场景中还会用到 tbreak 或者 rbreak 命令，本节将对这 3 个命令的功能和用法做详细的讲解。

为了让大家更好地了解给程序打断点的作用，这里以一段完整的 C 语言程序为例：
```
#include<stdio.h>
int main(int argc,char* argv[])
{
    int num = 1;
    while(num<100)
    {
        num *= 2;
    }
    printf("num=%d",num);
    return 0;
}
```

程序存储在`/tmp/demo/main.c`文件中，并已经生成了具备调试信息的 main.exe 可执行文件：
```
[root@bogon demo]# ls
main.c main.exe
[root@bogon demo]# gdb main.exe -q
Reading symbols from main.exe...
(gdb)
```

### 8.1. GDB break命令

break 命令（可以用 b 代替）常用的语法格式有以下 2 种。
```
1、(gdb) break location   // b location
2、(gdb) break ... if cond  // b .. if cond
```

1) 第一种格式中，location 用于指定打断点的具体位置，其表示方式有多种，如表 1 所示。
<center>表 1 location 参数的表示方式</center>

| location 的值     | 含 义                                                        |
| ----------------- | ------------------------------------------------------------ |
| linenum           | linenum 是一个整数，表示要打断点处代码的行号。要知道，程序中各行代码都有对应的行号，可通过执行 l（小写的 L）命令看到。 |
| filename:linenum  | filename 表示源程序文件名；linenum 为整数，表示具体行数。整体的意思是在指令文件 filename 中的第 linenum 行打断点。 |
| + offset - offset | offset 为整数（假设值为 2），+offset 表示以当前程序暂停位置（例如第 4 行）为准，向后数 offset 行处（第 6 行）打断点；-offset 表示以当前程序暂停位置为准，向前数 offset 行处（第 2 行）打断点。 |
| function          | function 表示程序中包含的函数的函数名，即 break 命令会在该函数内部的开头位置打断点，程序会执行到该函数第一行代码处暂停。 |
| filename:function | filename 表示远程文件名；function 表示程序中函数的函数名。整体的意思是在指定文件 filename 中 function 函数的开头位置打断点。 |

2) 第二种格式中，... 可以是表 1 中所有参数的值，用于指定打断点的具体位置；cond 为某个表达式。整体的含义为：每次程序执行到 ... 位置时都计算 cond 的值，如果为 True，则程序在该位置暂停；反之，程序继续执行。

如下演示了以上 2 种打断点方式的具体用法：
```
(gdb) l
1 #include<stdio.h>
2 int main(int argc,char* argv[])
3 {
4   int num = 1;
5   while(num<100)
6   {
7     num *= 2;
8   }
9   printf("num=%d",num);
10  return 0;
(gdb)
11 }
(gdb) b 4     <-- 程序第 4 行打断点
Breakpoint 1 at 0x1138: file main.c, line 4.
(gdb) r       <-- 运行程序，至第 4 行暂停
Starting program: /home/ubuntu64/demo/main.exe

Breakpoint 1, main (argc=1, argv=0x7fffffffe078) at main.c:4
4   int num = 1;
(gdb) b +1    <-- 在第 4 行的基础上，在第 5 行代码处打断点
Breakpoint 2 at 0x55555555513f: file main.c, line 5.
(gdb) c       <-- 继续执行程序，至第 5 行暂停
Continuing.

Breakpoint 2, main (argc=1, argv=0x7fffffffe078) at main.c:5
5   while(num<100)
(gdb) b 7 if num>10   <-- 如果 num>10 在第 7 行打断点
Breakpoint 3 at 0x555555555141: file main.c, line 7.
(gdb) c        <-- 继续执行
Continuing.

Breakpoint 3, main (argc=1, argv=0x7fffffffe078) at main.c:7
7     num *= 2;    <-- 程序在第 7 行暂停
(gdb) p num   <-- p 命令查看 num 当前的值
$1 = 16       <-- num=16
```

> 有关 c 和 p 命令，后续章节会做详细讲解，这里会用即可，不必深究。

### 8.2. GDB tbreak命令

tbreak 命令可以看到是 break 命令的另一个版本，tbreak 和 break 命令的用法和功能都非常相似，唯一的不同在于，使用 tbreak 命令打的断点仅会作用 1 次，即使程序暂停之后，该断点就会自动消失。

tbreak 命令的使用格式和 break 完全相同，有以下 2 种：
```
1、(gdb) tbreak location
2、(gdb) tbreak ... if cond
```

其中，location、... 和 cond 的含义都和 break 命令中的参数含义相同，即表 1 也同样适用于 tbreak 命令。

仍以 main.exe 为例，如下演示了 tbreak 命令的用法：

```
(gdb) tbreak 7 if num>10
Temporary breakpoint 1 at 0x1165: file main.c, line 7.
(gdb) r
Starting program: /home/ubuntu64/demo/main.exe

Temporary breakpoint 1, main (argc=1, argv=0x7fffffffe088) at main.c:7
7     num *= 2;
(gdb) p num
$1 = 16
(gdb) c    <-- 继续执行程序，则原使用 tbreak 在第 7 行打的断点将不再起作用
Continuing.
num=128[Inferior 1 (process 6534) exited normally]
(gdb)
```

可以看到，自`num=16`开始，后续循环过程中 num 的值始终大于 10，则`num>10`表达式的值永远为 True，理应在第 7 行暂停多次。但由于打断点采用的是 tbreak 命令，因此断点的作用只起 1 次。

### 8.3. GDB rbreak 命令

和 break 和 tbreak 命令不同，rbreak 命令的作用对象是 C、C++ 程序中的函数，它会在指定函数的开头位置打断点。

rbreak 命令的使用语法格式为：
```
(gdb) rbreak regex
```

其中 regex 为一个正则表达式，程序中函数的函数名只要满足 regex 条件，rbreak 命令就会其内部的开头位置打断点。值得一提的是，rbreak 命令打的断点和 break 命令打断点的效果是一样的，会一直存在，不会自动消失。

这里我们对 main.c 源文件的程序做如下修改：
```
(gdb) l   <-- 显示源码
1 #include<stdio.h>
2 void rb_one(){
3  printf("rb_one\n");
4 }
5 void rb_second(){
6  printf("rb_second");
7 }
8 int main(int argc,char* argv[])
9 {
10   rb_one();
(gdb)
11   rb_second();
12   return 0;
13 }
(gdb) rbreak rb_*    <--匹配所有以 rb_ 开头的函数
Breakpoint 1 at 0x1169: file main.c, line 2.
void rb_one();
Breakpoint 2 at 0x1180: file main.c, line 5.
void rb_second();
(gdb) r
Starting program: /home/ubuntu64/demo/main.exe

Breakpoint 1, rb_one () at main.c:2
2 void rb_one(){
(gdb) c
Continuing.
rb_one

Breakpoint 2, rb_second () at main.c:5
5 void rb_second(){
(gdb) c
Continuing.
rb_second[Inferior 1 (process 7882) exited normally]
(gdb)
```

可以看到，通过执行`rbreak rb_*`指令，找到了程序中所有以 tb_* 开头的函数，并在这些函数内部的开头位置打上了断点（如上所示，分别为第 2 行和第 5 行）。

## 9. GDB watch命令：监控变量值的变化

[8. GDB break（b）：设置断点](#8.%20GDB%20break（b）：设置断点)一节，给大家介绍了使用 break 命令在程序某一行的位置打断点。但还有一些场景，我们需要监控某个变量或者表达式的值，通过值的变化情况判断程序的执行过程是否存在异常或者 Bug。这种情况下，break 命令显然不再适用，推荐大家使用 watch 命令。

要知道，GDB 调试器支持在程序中打 3 种断点，分别为普通断点、观察断点和捕捉断点。其中 break 命令打的就是普通断点，而 watch 命令打的为观察断点，关于捕捉断点，后续章节会做详细讲解。

使用 GDB 调试程序的过程中，借助观察断点可以监控程序中某个变量或者表达式的值，只要发生改变，程序就会停止执行。相比普通断点，观察断点不需要我们预测变量（表达式）值发生改变的具体位置。

> 所谓表达式，就是包含多个变量的式子，比如 a+b 就是一个表达式，其中 a、b 为变量。

对于监控 C、C++ 程序中某变量或表达式的值是否发生改变，watch 命令的语法非常简单，如下所示：
```
(gdb) watch cond
```

其中，conde 指的就是要监控的变量或表达式。

和 watch 命令功能相似的，还有 rwatch 和 awatch 命令。其中：

- rwatch 命令：只要程序中出现读取目标变量（表达式）的值的操作，程序就会停止运行；
- awatch 命令：只要程序中出现读取目标变量（表达式）的值或者改变值的操作，程序就会停止运行。

> 强调一下，watch 命令的功能是：只有当被监控变量（表达式）的值发生改变，程序才会停止运行。

举个例子：
```
(gdb) l      <--列出要调试的程序源码
1 #include<stdio.h>
2 int main(int argc,char* argv[])
3 {
4   int num = 1;
5   while(num<=100)
6   {
7     num *= 2;
8   }
9   printf("%d",num);
10   return 0;
(gdb)
11 }
(gdb) b 4    <-- 使用 break 命令打断点
Breakpoint 1 at 0x115c: file main.c, line 4.
(gdb) r      <-- 执行程序
Starting program: /home/ubuntu64/demo/main.exe

Breakpoint 1, main (argc=1, argv=0x7fffffffe088) at main.c:4
4   int num = 1;
(gdb) watch num  <-- 监控程序中 num 变量的值
Hardware watchpoint 2: num
(gdb) c      <-- 继续执行，当 num 值发生改变时，程序才停止执行
Continuing.

Hardware watchpoint 2: num

Old value = 0
New value = 2
main (argc=1, argv=0x7fffffffe088) at main.c:5
5   while(num<=100)
(gdb) c      <-- num 值发生了改变，继续执行程序
Continuing.

Hardware watchpoint 2: num

Old value = 2
New value = 4
main (argc=1, argv=0x7fffffffe088) at main.c:5
5   while(num<=100)
(gdb)
```

可以看到在程序运行过程中，通过借助 watch 命令监控 num 的值，后续只要 num 的值发生改变，程序都会停止。感兴趣的读者，可自行尝试使用 awatch 和 rwatch 命令，这里不再给出具体的示例。

如果我们想查看当前建立的观察点的数量，借助如下指令即可：

```
(gdb) info watchpoints
```

值得一提的是，对于使用 watch（rwatch、awatch）命令监控 C、C++ 程序中变量或者表达式的值，有以下几点需要注意：

- 当监控的变量（表达式）为局部变量（表达式）时，一旦局部变量（表达式）失效，则监控操作也随即失效；
- 如果监控的是一个指针变量（例如 *p），则 watch *p 和 watch p 是有区别的，前者监控的是 p 所指数据的变化情况，而后者监控的是 p 指针本身有没有改变指向；
- 这 3 个监控命令还可以用于监控数组中元素值的变化情况，例如对于 a[10]这个数组，watch a 表示只要 a 数组中存储的数据发生改变，程序就会停止执行。

> 如果读者只想学习如何使用 watch 命令，则读者这里即可。反之，如果想了解 watch 命令底层是如何实现的，可以继续往下阅读。

### 9.1. watch命令的实现原理

watch 命令实现监控机制的方式有 2 种，一种是为目标变量（表达式）设置硬件观察点，另一种是为目标变量（表达式）设置软件观察点。

所谓软件观点（software watchpoint），即用 watch 命令监控目标变量（表达式）后，GDB 调试器会以单步执行的方式运行程序，并且每行代码执行完毕后，都会检测该目标变量（表达式）的值是否发生改变，如果改变则程序执行停止。

可想而知，这种“实时”的判别方式，一定程度上会影响程序的执行效率。但从另一个角度看，调试程序的目的并非是为了获得运行结果，而是查找导致程序异常或 Bug 的代码，因此即便软件观察点会影响执行效率，一定程度上也是可以接受的。

所谓硬件观察点（Hardware watchpoint），和前者最大的不同是，它在实现监控机制的同时不影响程序的执行效率。简单的理解，系统会为 GDB 调试器提供少量的寄存器（例如 32 位的 Intel x86 处理器提供有 4 个调试寄存器），每个寄存器都可以作为一个观察点协助 GDB 完成监控任务。

需要注意的是，基于寄存器个数的限制，如果调试环境中设立的硬件观察点太多，则有些可能会失去作用，这种情况下，GDB 调试器会发出如下警告：
```
Hardware watchpoint num: Could not insert watchpoint
```

解决方案也很简单，就是删除或者禁用一部分硬件观察点。

除此之外，受到寄存器数量的限制，可能会出现：无法使用硬件观察点监控数据类型占用字节数较多的变量（表达式）。比如说，某些操作系统中，GDB 调试器最多只能监控 4 个字节长度的数据，这意味着 C、C++ 中 double 类型的数据是无法使用硬件观察点监测的。这种情况下，可以考虑将其换成占用字符串少的 float 类型。

目前，大多数 PowerPC 或者基于 x86 的操作系统，都支持采用硬件观点。并且 GDB 调试器在建立观察断点时，会优先尝试建立硬件观察点，只有当前环境不支持硬件观察点时，才会建立软件观察点。借助如下指令，即可强制 GDB 调试器只建立软件观察点：
```
set can-use-hw-watchpoints 0
```

> 注意，在执行此命令之前建立的硬件观察点，不会受此命令的影响。

注意，awatch 和 rwatch 命令只能设置硬件观察点，如果系统不支持或者借助如上命令禁用，则 GDB 调试器会打印如下信息：
```
Expression cannot be implemented with read/access watchpoint.
```


## 10. GDB catch命令：建立捕捉断点

要知道，GDB 调试器支持在被调试程序中打 3 种断点，分别为普通断点、观察断点和捕捉断点，其中普通断点用 break 命令建立（可阅读[8. GDB break（b）：设置断点](#8.%20GDB%20break（b）：设置断点)一节），观察断点用 watch 命令建立（可阅读[9. GDB watch命令：监控变量值的变化](#9.%20GDB%20watch命令：监控变量值的变化)一节），本节将讲解如何使用 catch 命令建立捕捉断点。

和前 2 种断点不同，普通断点作用于程序中的某一行，当程序运行至此行时停止执行，观察断点作用于某一变量或表达式，当该变量（表达式）的值发生改变时，程序暂停。而捕捉断点的作用是，监控程序中某一事件的发生，例如程序发生某种异常时、某一动态库被加载时等等，一旦目标时间发生，则程序停止执行。

> 用捕捉断点监控某一事件的发生，等同于在程序中该事件发生的位置打普通断点。

建立捕捉断点的方式很简单，就是使用 catch 命令，其基本格式为：
```
(gdb) catch event
```

其中，event 参数表示要监控的具体事件。对于使用 GDB 调试 C、C++ 程序，常用的 event 事件类型如表 1 所示。
<center>表 1 常见的 event 事件</center>

| event 事件                    | 含 义                                                        |
| ----------------------------- | ------------------------------------------------------------ |
| throw [exception]             | 当程序中抛出 exception 指定类型异常时，程序停止执行。如果不指定异常类型（即省略 exception），则表示只要程序发生异常，程序就停止执行。 |
| catch [exception]             | 当程序中捕获到 exception 异常时，程序停止执行。exception 参数也可以省略，表示无论程序中捕获到哪种异常，程序都暂停执行。 |
| load [regexp] unload [regexp] | 其中，regexp 表示目标动态库的名称，load 命令表示当 regexp 动态库加载时程序停止执行；unload 命令表示当 regexp 动态库被卸载时，程序暂停执行。regexp 参数也可以省略，此时只要程序中某一动态库被加载或卸载，程序就会暂停执行。 |

> 除表中罗列的以外，event 参数还有其它一些写法，感兴趣的读者可查看 [GDB官网](https://sourceware.org/gdb/current/onlinedocs/gdb/Set-Catchpoints.html#Set-Catchpoints)进行了解，这里不再过多赘述。

注意，当前 GDB 调试器对监控 C++ 程序中异常的支持还有待完善，使用 catch 命令时，有以下几点需要说明：

1. 对于使用 catch 监控指定的 event 事件，其匹配过程需要借助 libstdc++ 库中的一些 SDT 探针，而这些探针最早出现在 GCC 4.8 版本中。也就是说，想使用 catch 监控指定类型的 event 事件，系统中 GCC 编译器的版本最低为 4.8，但即便如此，catch 命令是否能正常发挥作用，还可能受到系统中其它因素的影响。
2. 当 catch 命令捕获到指定的 event 事件时，程序暂停执行的位置往往位于某个系统库（例如 libstdc++）中。这种情况下，通过执行 up 命令，即可返回发生 event 事件的源代码处。
3. catch 无法捕获以交互方式引发的异常。

如同 break 命令和 tbreak 命令的关系一样（前者的断点是永久的，后者是一次性的），catch 命令也有另一个版本，即 tcatch 命令。tcatch 命令和 catch 命令的用法完全相同，唯一不同之处在于，对于目标事件，catch 命令的监控是永久的，而 tcatch 命令只监控一次，也就是说，只有目标时间第一次触发时，tcath 命令才会捕获并使程序暂停，之后将失效。

接下来就以下面的 C++ 程序为例，给大家演示 catch（tcatch）命令的用法：
```
#include <iostream>
using namespace std;
int main(){
    int num = 1;
    while(num <= 5){
        try{
            throw 100;
        }catch(int e){
            num++;
            cout << "next" << endl;
        }
    }
    cout << "over" << endl;
    return 0;
}
```

此程序存储于 ~/demo/main.cpp 文件中（ ~ 表示当前登陆用户的主目录）。

在此基础上，对 main.cpp 文件进行编译，并启动对该程序的调试：
```
[root@bogon demo]$ ls
main.cpp
[root@bogon demo]# g++ main.cpp -o main.exe -g
[root@bogon demo]$ ls
main.cpp main.exe
[root@bogon demo]# gdb main.exe -q
Reading symbols from main.exe...done.
(gdb)
```


通过观察程序可以看出，当前程序中通过 throw 手动抛出了 int 异常，此异常能够被 catch 成功捕获。假设我们使用 catch 命令监控：只要程序中引发 int 异常，程序就停止执行：
```
(gdb) catch throw int       <-- 指定捕获“throw int”事件
Catchpoint 1 (throw)
(gdb) r                   <-- 执行程序
Starting program: ~/demo/main.exe

Catchpoint 1 (exception thrown), 0x00007ffff7e81762 in __cxa_throw ()
  from /lib/x86_64-linux-gnu/libstdc++.so.6             <-- 程序暂停执行
(gdb) up                                          <-- 回到源码
#1 0x0000555555555287 in main () at main.cpp:8
8       throw 100;
(gdb) c                                           <-- 继续执行程序
Continuing.
next

Catchpoint 1 (exception thrown), 0x00007ffff7e81762 in __cxa_throw ()
  from /lib/x86_64-linux-gnu/libstdc++.so.6
(gdb) up
#1 0x0000555555555287 in main () at main.cpp:8
8       throw 100;
(gdb) 
```

如上所示，借助 catch 命令设置了一个捕获断点，该断点用于监控 throw int 事件，只要发生程序就会暂停执行。由此当程序执行时，其会暂停至 libstdc++ 库中的某个位置，借助 up 指令我们可以得知该异常发生在源代码文件中的位置。

同理，我们也可以监控 main.cpp 程序中发生的 catch event 事件：
```
(gdb) catch catch int
Catchpoint 1 (catch)
(gdb) r
Starting program: ~/demo/main.exe

Catchpoint 1 (exception caught), 0x00007ffff7e804d3 in __cxa_begin_catch ()
  from /lib/x86_64-linux-gnu/libstdc++.so.6
(gdb) up
#1 0x00005555555552d0 in main () at main.cpp:9
9     }catch(int e){
(gdb) c
Continuing.
next

Catchpoint 1 (exception caught), 0x00007ffff7e804d3 in __cxa_begin_catch ()
  from /lib/x86_64-linux-gnu/libstdc++.so.6
(gdb) up
#1 0x00005555555552d0 in main () at main.cpp:9
9     }catch(int e){
(gdb)
```

甚至于，在个别场景中，还可以使用 catch 命令监控 C、C++ 程序动态库的加载和卸载。就以 main.exe 为例，其运行所需加载的动态库可以使用 ldd 命令查看，例如：
```
[root@bogon demo]# ldd main.exe
linux-vdso.so.1 => (0x00007fffbc1ff000)
libstdc++.so.6 => /usr/lib64/libstdc++.so.6 (0x0000003e75000000)
libm.so.6 => /lib64/libm.so.6 (0x00000037eee00000)
libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x0000003e74c00000)
libc.so.6 => /lib64/libc.so.6 (0x00000037ee200000)
/lib64/ld-linux-x86-64.so.2 (0x00000037eda00000)
```

就以监控 libstdc++.so.6 为例，在 GDB 调试器中，通过执行如下指令，即可监控该动态库的加载：
```
(gdb) catch load libstdc++.so.6
Catchpoint 1 (load)
(gdb) r
Starting program: ~/demo/main.exe

Catchpoint 1
 Inferior loaded /lib/x86_64-linux-gnu/libstdc++.so.6
  /lib/x86_64-linux-gnu/libgcc_s.so.1
  /lib/x86_64-linux-gnu/libc.so.6
  /lib/x86_64-linux-gnu/libm.so.6
0x00007ffff7fd37a5 in ?? () from /lib64/ld-linux-x86-64.so.2
```

> 以上实例仅演示了 catch 命令的用法，而 tcatch 命令的语法格式和 catch 完全相同，读者可自行尝试使用 tcatch 命令，观察它的功能。


## 11. GDB条件断点（condition命令）详解

前面章节给大家介绍了 GDB 调试器中普通断点、观察断点以及捕捉断点的功能和用法。其中值得一提的是，对于普通断点的建立，可以使用如下格式的 break 命令：
```
(gdb) break ... if cond
```

... 参数用于指定生成断点的具体位置；cond 参数用于代指某个表达式。通过此方式建立的普通断点，只有当表达式 cond 成立（值为 True）时，才会发挥它的作用；反之，断点并不会使程序停止执行。

类似上面这种，以某个表达式的是否成立作为条件，从而决定自身是否生效的断点，又称为条件断点。除了普通断点外，观察断点和捕捉断点也可以成为条件断点。

需要说明的是，创建普通条件断点的方式，也同样适用于观察条件断点。通过执行如下命令，即可直接生成一个观察条件断点：
```
(gdb) watch expr if cond
```

参数 expr 表示要观察的变量或表达式；参数 cond 用于代指某个表达式。

但是，以上创建条件断点的方法，不适用于捕捉断点。换句话说，捕捉条件断点无法直接生成，需要借助 condition 命令为现有捕捉断点增添一个 cond 表达式，才能使其变成条件断点。

总的来说，借助 condition 命令，我们可以将现有的普通断点、观察断点以及捕捉断点变成条件断点；而普通条件断点和观察条件断点，可以分别通过 break if 命令和 watch if 命令直接生成。

接下来，我将给大家详细地讲解 condition 命令的用法。

### 11.1. GDB condition命令

严格来说，condition 命令的功能是：既可以为现有的普通断点、观察断点以及捕捉断点添加条件表达式，也可以对条件断点的条件表达式进行修改。

condition 命令没有缩写形式，使用方法很简单，语法格式如下：
```
(gdb) condition bnum expression
(gdb) condition bnum
```

参数 bnum 用于代指目标断点的编号；参数 expression 表示为断点添加或修改的条件表达式。

以上 2 种语法格式中，第 1 种用于为 bnum 编号的断点添加或修改 expression 条件表达式；第 2 种用于删除 bnum 编号断点的条件表达式，使其变成普通的无条件断点。

举个例子，这里以调试如下 C++ 程序为例：
```
#include <iostream>
using namespace std;
int main(){
    int num = 1;
    while(num<20){
        try{
            throw num;
        }catch(int &e){
            num++;
        }
    }
    cout << num << endl;
    return 0;
}
```

程序存储位置为`~/demo/main.cpp`，并已经生成了可供 GDB 调试器使用的执行文件：
```
[root@bogon demo]# ls
main.cpp main.exe
[root@bogon demo]# gdb main.exe -q
Reading symbols from ~/demo/main.exe...done.
(gdb) l
1 #include <iostream>
2 using namespace std;
3 int main(){
4   int num = 1;
5   while(num<20){
6     try{
7       throw num;
8     }catch(int &e){
9       num++;
10     }
(gdb)
11   }
12   cout << num << endl;
13   return 0;
14 }
(gdb)
```

接下来，为读者演示 condition 命令的功能和用法：
```
(gdb) b 9   <--添加普通断点
Breakpoint 1 at 0x12d0: file main.cpp, line 9.
(gdb) r
Starting program: /home/test/demo/main.exe

Breakpoint 1, main () at main.cpp:9
9       num++;
(gdb) rwatch num         <-- 添加观察断点
Hardware read watchpoint 2: num
(gdb) catch throw int      <-- 添加捕捉断点
Catchpoint 3 (throw)
(gdb) info break
Num   Type      Disp Enb Address            What
1    breakpoint   keep y   0x00005555555552d0 in main() at main.cpp:9  breakpoint already hit 1 time
2    read watchpoint keep y                    num
3    catchpoint   keep y                      exception throw   matching: int
(gdb) condition 1 num==3       <-- 为普通断点添加条件表达式
(gdb) condition 2 num==5       <-- 为观察断点添加条件表达式
(gdb) condition 3 num==7       <-- 为捕捉断点添加条件表达式
(gdb) c
Continuing.

Breakpoint 1, main () at main.cpp:9      <-- 普通条件断点触发
9       num++;
(gdb) p num
$1 = 3
(gdb) c
Continuing.

Hardware read watchpoint 2: num       <-- 观察条件断点触发

Value = 5
0x000055555555526f in main () at main.cpp:7
7       throw num;
(gdb) c
Continuing.

Catchpoint 3 (exception thrown), 0x00007ffff7e81762 in __cxa_throw ()    <-- 捕捉条件断点触发
  from /lib/x86_64-linux-gnu/libstdc++.so.6
(gdb) up
\#1 0x0000555555555285 in main () at main.cpp:7
7       throw num;
(gdb) p num
$2 = 7
(gdb)
```

可以看到，通过借助 condition 命令为不同类型断点设置条件表达式，只有当条件表达式成立（值为 True）时，相应的断点才会触发从而使程序暂停运行。

### 11.2. GDB ignore命令

ignore 命令也可以使一个断点成为条件断点，但这里的“条件”并非自定义的表达式，而仅为一个整数，它用来表示该断点失效的次数。也就会说，ignore 命令可以使目标断点暂时失去作用，当断点失效的次数超过指定次数时，断点的功能会自动恢复。

ignore 命令也没有缩写形式，其语法格式如下：
```
ignore bnum count
```

参数 bnum 为某个断点的编号；参数 count 用于指定该断点失效的次数。

仍以 main.exe 为例：
```
(gdb) b 9
Breakpoint 1 at 0x400a33: file main.cpp, line 9.
(gdb) r
Starting program: ~/demo/main.exe

Breakpoint 1, main () at main.cpp:9
9       num++;
(gdb) p num
$1 = 1
(gdb) ignore 1 3
Will ignore next 3 crossings of breakpoint 1.
(gdb) c
Continuing.

Breakpoint 1, main () at main.cpp:9
9       num++;
(gdb) p num
$2 = 5
(gdb)
```

可以看到，执行 ignore 命令之前，num 变量的值为 1。借助 ignore 命令使编号为 1（作用于第 9 行）的断点失效 3 次后，继续执行程序，最终程序仍暂停至第 9 行，此时 num 的值变为 5。这这恰恰证明了 num 从 1 递增至 5 的过程中，编号为 1 的断点失效了 3 次。


## 12. GDB单步调试程序

[6. 调用GDB调试器的4种方式](#6.%20调用GDB调试器的4种方式)一节中提到，借助 next 命令可以控制 GDB 单步执行程序。所谓单步调试，就是通过一行一行的执行程序，观察整个程序的执行流程，进而尝试发现一些存在的异常或者 Bug。

根据实际场景的需要，GDB 调试器共提供了 3 种可实现单步调试程序的方法，即使用 next、step 和 until 命令。换句话说，这 3 个命令都可以控制 GDB 调试器每次仅执行 1 行代码，但除此之外，它们各自还有不同的功能。

本节就来一一给大家讲解这 3 个命令的功能和用法。讲解过程中，将以调试如下 C 语言程序为例：
```
#include <stdio.h>
int print(int num){
    int ret = num * num;
    return ret;
}
int myfunc(int num){
    int i = 1;
    int sum = 0;
    while(i <= num){
        sum += print(i);
        i++;
    }
    return sum;
}
int main(){
    int num =0;
    scanf("%d", &num);
    int result = myfunc(num);
    printf("%d", result);
    return 0;
}
```

此程序存储在`~/demo/main.c`源文件中（~ 表示当前用户的主目录），功能是根据用户输入的 num 值，输出 12+22+...+num2 的值。

### 12.1. GDB next 命令

next 是最常用来进行单步调试的命令，其最大的特点是当遇到包含调用函数的语句时，无论函数内部包含多少行代码，next 指令都会一步执行完。也就是说，对于调用的函数来说，next 命令只会将其视作一行代码。

next 命令可以缩写为 n 命令，使用方法也很简单，语法格式如下：
```
(gdb) next count
```

参数 count 表示单步执行多少行代码，默认为 1 行。

举个例子：
```
(gdb) b 16
Breakpoint 2 at 0x40058b: file main.c, line 16.
(gdb) r
Starting program: /root/demo/main.exe

Breakpoint 2, main () at main.c:16
16   int num =0;
(gdb) n 2                <-- 单步执行 2 次
3
18   int result = myfunc(num);
(gdb) n
19   printf("%d", result);
(gdb) n
20   return 0;
(gdb)
```

可以看到，当程序单步执行第 18 行时，继续执行 next 指令，下一次将要执行的是第 19 行代码，而非 myfunc() 函数内部的代码。

### 12.2. GDB step命令

通常情况下，step 命令和 next 命令的功能相同，都是单步执行程序。不同之处在于，当 step 命令所执行的代码行中包含函数时，会进入该函数内部，并在函数第一行代码处停止执行。

step 命令可以缩写为 s 命令，用法和 next 命令相同，语法格式如下：
```
(gdb) step count
```

参数 count 表示一次执行的行数，默认为 1 行。

仍以 main.exe 为例：
```
(gdb) b 18
Breakpoint 1 at 0x4005ab: file main.c, line 18.
(gdb) r
Starting program: ~/demo/main.exe
Breakpoint 1, main () at main.c:18
18   int result = myfunc(num);
(gdb) step                     <-- step 命令进入 myfunc() 函数内部执行
myfunc (num=0) at main.c:7
7   int i = 1;
```

可以看到，当程序暂停到包含 mufunc() 函数的代码行处时（此时该行代码尚未执行），如果使用 step 命令，则 GDB 在执行该函数代码的同时，会进入 mufunc() 函数内部，并暂停在函数内部的第一行代码处。反之如果使用 next 命令，则程序下一次会执行第 19 行代码，而不是第 7 行，这就是它们最大的不同之处。

### 12.3. GDB until命令

until 命令可以简写为 u 命令，有 2 种语法格式，如下所示：
```
1、(gdb) until
2、(gdb) until location
```

其中，参数 location 为某一行代码的行号。

不带参数的 until 命令，可以使 GDB 调试器快速运行完当前的循环体，并运行至循环体外停止。注意，until 命令并非任何情况下都会发挥这个作用，只有当执行至循环体尾部（最后一行代码）时，until 命令才会发生此作用；反之，until 命令和 next 命令的功能一样，只是单步执行程序。

以 main.exe 中 myfunc() 函数的循环为例：
```
(gdb) b 17
Breakpoint 1 at 0x1201: file main.c, line 17.
(gdb) r
Starting program: ~/demo/main.exe

Breakpoint 1, main () at main.c:17
17   scanf("%d", &num);
(gdb) u
3
18   int result = myfunc(num);
(gdb) step
myfunc (num=3) at main.c:7
7   int i = 1;
(gdb) u
8   int sum = 0;
(gdb) u
9   while(i <= num){
(gdb) u
10     sum += print(i);
(gdb) u
11     i++;
(gdb) u                 <-- 执行 i++ 操作
9   while(i <= num){
(gdb) u                 <-- 快速执行完循环体
13   return sum;
(gdb) p sum
$1 = 14
```

可以看到，这里当程序单步执行完第 11 行代码时，借助 until 命令快速执行完了整个循环体，并在第 13 行代码处停止执行。根据 p 命令输出的 num 变量的值可以确认，整个循环过程确定完整地执行完了。

until 命令还可以后跟某行代码的行号，以指示 GDB 调试器直接执行至指定位置后停止。举个例子：
```
(gdb) r
Starting program:~/demo/main.exe

Breakpoint 1, main () at main.c:17
17   scanf("%d", &num);
(gdb) until 19                  <-- 执行至第 19 行停止
3
main () at main.c:19
19   printf("%d", result);
(gdb) p result
$3 = 14
```

可以看到，通过执行 until 19 命令，GDB 调试器直接从第 17 行代码处执行至指定的第 19 行。


## 13. 如何使用GDB进行断点调试？

前面利用 3 节的内容，分别介绍了 GDB 调试器支持在被调试程序中打断点的 3 种方法，即 break、watch 以及 catch 命令。在此基础上，本节给大家讲解：如何借助断点对程序进行调试？

通过在程序的适当位置打断点，观察程序执行至该位置时某些变量（或表达式）的值，进而不断缩小导致程序出现异常或 Bug 的语句的搜索范围，并最终找到，整个过程就称为断点调试。

值得一提的是，整个断点调试的过程，除了要借助 break、watch 或者 catch 命令以外，还要借助其它一些命令，例如在前面章节中，我们已经使用过的 print 命令（查看变量的值）、continue 命令（使程序继续执行）等。

表 1 罗列了断点调试程序过程中，常用的一些命令以及各自的含义。
<center>表 1 GDB断点调试常用命令</center>

| 命令（缩写）                            | 功 能                                                        |
| --------------------------------------- | ------------------------------------------------------------ |
| run（r）                                | 启动或者重启一个程序。                                       |
| list（l）                               | 显示带有行号的源码。                                         |
| continue（c）                           | 让暂停的程序继续运行。                                       |
| next（n）                               | 单步调试程序，即手动控制代码一行一行地执行。                 |
| step（s）                               | 如果有调用函数，进入调用的函数内部；否则，和 next 命令的功能一样。 |
| until（u） until location（u location） | 当你厌倦了在一个循环体内单步跟踪时，单纯使用 until 命令，可以运行程序直到退出循环体。 until n 命令中，n 为某一行代码的行号，该命令会使程序运行至第 n 行代码处停止。 |
| finish（fi）                            | 结束当前正在执行的函数，并在跳出函数后暂停程序的执行。       |
| return（return）                        | 结束当前调用函数并返回指定值，到上一层函数调用处停止程序执行。 |
| jump（j）                               | 使程序从当前要执行的代码处，直接跳转到指定位置处继续执行后续的代码。 |
| print（p）                              | 打印指定变量的值。                                           |
| quit（q）                               | 退出 GDB 调试器。                                            |

> 其中，next、step 以及 until 命令，已经在 [12. GDB单步调试程序](#12.%20GDB单步调试程序)一节中做了详细介绍，本节不再赘述。另外，表 1 中罗列的这些命令读者无需死记硬背，因为实际使用 GDB 调试器时，它们都会常常用到，熟能生巧。

为了搞清楚表 1 中这些命令的功能和用法，这里仍以上节创建的 C 语言程序为例：
```
#include <stdio.h>
int print(int num){
    int ret = num * num;
    return ret;
}
int myfunc(int num){
    int i = 1;
    int sum = 0;
    while(i <= num){
        sum += print(i);
        i++;
    }
    return sum;
}
int main(){
    int num =0;
    scanf("%d", &num);
    int result = myfunc(num);
    printf("%d", result);
    return 0;
}
```

此程序存储在`~/demo/main.c`源文件中（~ 表示当前用户的主目录）。

\1) 首先，表 1 中 run、continue、list、next、print 以及 quit 命令的用法都非常简单，唯一需要注意的一点是，run 命令除了可以启动程序的执行，还可以在任何时候重新启动程序。

例如，以 main.c 为例：
```
[root@bogon demo]# gdb main.exe -q
Reading symbols from main.exe...
(gdb) l           <-- list 命令的缩写，罗列出带有行号的源码
1 #include <stdio.h>
2 int print(int num){
3   int ret = num * num;
4   return ret;
......
15 int main(){
16   int num =0;
17   scanf("%d", &num);
18   int result = myfunc(num);
19   printf("%d", result);
20   return 0;
(gdb)
21 }
(gdb) b 16             <-- break 命令的缩写，在程序第 16 行处打普通断点
Breakpoint 1 at 0x11fa: file main.c, line 16.
(gdb) r                <-- run 命令的缩写，启动程序
Starting program: /home/test/demo/main.exe
Breakpoint 1, main () at main.c:16
16   int num =0;
(gdb) next             <-- 单步执行程序，即执行一行代码
17   scanf("%d", &num);
(gdb) next             <-- 继续单步执行，此时需要输入一个整数传递给 num
3
18   int result = myfunc(num);
(gdb) p num            <-- print 命令的缩写，显示 num 的值
$1 = 3                 <-- $1 表示 num 变量所在存储区的名称，这里指的是 num 的值为 3
(gdb) n                <-- 继续单步执行
19   printf("%d", result);
(gdb) n                <-- 单步执行
20   return 0;
(gdb) q                <-- 退出调试
A debugging session is active.

Inferior 1 [process 4576] will be killed.

Quit anyway? (y or n) y    <-- 由于程序执行尚未结束，GDB 会进行再次确认
[root@bogon demo]#
```

> 事实上，以上很多命令还有其它的语法格式，只是不常用，这里不再过多赘述，感兴趣的读者可自行查阅[ GDB 官网手册](https://sourceware.org/gdb/current/onlinedocs/gdb/index.html#SEC_Contents)。

接下来，重点给大家介绍表 1 中另外几个命令的用法。

### 13.1. GDB finish和return命令

实际调试时，在某个函数中调试一段时间后，可能不需要再一步步执行到函数返回处，希望直接执行完当前函数，这时可以使用 finish 命令。与 finish 命令类似的还有 return 命令，它们都可以结束当前执行的函数。

finish 命令和 return 命令的区别是，finish 命令会执行函数到正常退出；而 return 命令是立即结束执行当前函数并返回，也就是说，如果当前函数还有剩余的代码未执行完毕，也不会执行了。除此之外，return 命令还有一个功能，即可以指定该函数的返回值。

仍以 main.exe 为例，如下演示了 finish 命令的用法：
```
(gdb) b 18
Breakpoint 1 at 0x4005ab: file main.c, line 18.
(gdb) r
Starting program: ~/demo/main.exe
3

Breakpoint 1, main () at main.c:18
18   int result = myfunc(num);
(gdb) step
myfunc (num=3) at main.c:7
7   int i = 1;
(gdb) n
8   int sum = 0;
(gdb) n
9   while(i <= num){
(gdb) finish
Run till exit from #0 myfunc (num=3) at main.c:9
0x00000000004005b5 in main () at main.c:18
18   int result = myfunc(num);
Value returned is $1 = 14
(gdb) 
```

可以看到，当程序运行至第 9 行处使用 finish 命令，GDB 调试器会执行完 myfunc() 函数中的剩余代码，并在执行完函数后停止。接下来重新执行程序，观察 return 命令的功能：
```
(gdb) r
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: ~/demo/main.exe
3

Breakpoint 1, main () at main.c:18
18   int result = myfunc(num);
(gdb) step
myfunc (num=3) at main.c:7
7   int i = 1;
(gdb) n
8   int sum = 0;
(gdb) n
9   while(i <= num){
(gdb) return 5
Make myfunc return now? (y or n) y
\#0 0x00000000004005b5 in main () at main.c:18
18   int result = myfunc(num);
(gdb) n
19   printf("%d", result);
(gdb) p result
$3 = 5
```

可以看到，同样程序执行至第 9 行，借助 return 命令会立即终止执行 myfunc() 函数，同时手动指定该函数的返回值为 5。因此，最终 result 变量的值为 5，而不再是 14。

### 13.2. GDB jump命令

jump 命令的功能是直接跳到指定行继续执行程序，其语法格式为：
```
(gdb) jump location
```

其中，location 通常为某一行代码的行号。

也就是说，jump 命令可以略过某些代码，直接跳到 location 处的代码继续执行程序。这意味着，如果你跳过了某个变量（对象）的初始化代码，直接执行操作该变量（对象）的代码，很可能会导致程序崩溃或出现其它 Bug。另外，如果 jump 跳转到的位置后续没有断点，那么 GDB 会直接执行自跳转处开始的后续代码。

举个例子：
```
(gdb) b 16
Breakpoint 1 at 0x40058b: file main.c, line 16.
(gdb) r
Starting program: ~/demo/main.exe

Breakpoint 1, main () at main.c:16
16   int num = 0;
(gdb) jump 19
Continuing at 0x4005b8.
0
Program exited normally.
```

可以看到，由于借助 jump 指令跳过了 result 变量的初始化过程，因此 result 变量的值为 0（或者垃圾值）。

> 注意，从第 16 行直接跳到第 19 行执行，并不意味着 result 变量不能使用。因为对于可执行文件而言，并不存在 num、result 这些变量名，它们都已经被转化为了地址（确定地说是偏移地址），并且程序在执行时，位于 main() 函数中的所有变量的存储地址都会被确定。也就是说，当我们跳到第 19 行输出 result 的值时，实际上是取其存储地址中的数据，只不过由于 result 没有初始化，所以最终结果值可能为 0，也可能为垃圾值。

## 14. GDB print和display命令：查看变量的值

前面章节中提到，使用 GDB 调试程序，最常用的方法是：单步调试或者断点调试程序，期间通过查看某个变量或者表达式的值，判断当前程序的执行过程是否正确，不断缩小异常或 Bug 位于代码中的范围，最终找到并修复。

对于在调试期间查看某个变量或表达式的值，GDB 调试器提供有 2 种方法，即使用 print 命令或者 display 命令。本节就对这 2 个命令的功能和用法做详细的讲解，整个讲解过程将以调试如下 C 语言程序为例：
```
#include <stdio.h>
int main(){
    int num,result=0,i=0;
    scanf("%d", &num);
    while(i<=num){
        result += i;
        i++;
    }  
    printf("result=%d\n", result);
    return 0;
}
```

此程序存储在`~/demo/main.c`文件中（`~` 代指当前系统登录用户的主目录），并且已经其编译为可供 GDB 调试的 main.exe 可执行文件：
```
[root@bogon demo]# gcc main.c -o main.exe -g
[root@bogon demo]# ls
main.c main.exe
```

### 14.1. GDB print命令

前面章节中，我们已经多次接触并使用了 print 命令，它的功能就是在 GDB 调试程序的过程中，输出或者修改指定变量或者表达式的值。

> 所谓表达式，简单理解就是由多个变量构成的式子。例如 a、b、c 为单个变量，a+b、a+b*c 即为表达式。

print 命令可以缩写为 p，最常用的语法格式如下所示：
```
(gdb) print num
(gdb) p num
```

其中，参数 num 用来代指要查看或者修改的目标变量或者表达式。

以调试 main.exe 为例：
```
[root@bogon demo]# gdb main.exe -q
Reading symbols from ~/demo/main.exe...done.
(gdb) l
1 #include <stdio.h>
2 int main(){
3   int num,result=0,i=0;
4   scanf("%d", &num);
5   while(i<=num){
6     result += i;
7     i++;
8   } 
9   printf("result=%d\n", result);
10   return 0;
(gdb)
11 }
(gdb) b 3
Breakpoint 1 at 0x40053c: file main.c, line 3.
(gdb) r
Starting program: /root/demo/main.exe

Breakpoint 1, main () at main.c:4
4   scanf("%d", &num);
(gdb) n
3
5   while(i<=num){
(gdb) p num                 <--输出 num 的值
$1 = 3
(gdb) p num=4               <-- 修改 num 的值为 4
$2 = 4
(gdb) b 9
Breakpoint 2 at 0x400569: file main.c, line 9.
(gdb) c
Continuing.

Breakpoint 2, main () at main.c:9
9   printf("result=%d\n", result);
(gdb) p result                 <-- 输出 result 的值
$2 = 10
(gdb) p result=20              <-- 修改 result 的值
$2 = 20
(gdb) c
Continuing.
result=20

Program exited normally.
(gdb)
```

可以看到，调试 main.exe 的过程中，我们先后使用 p 命令输出了程序中 num 和 result 变量的值，同时还使用该命令对它们的值做了修改。

> print 命令还有更高级的用法，后续会给读者做详细的讲解。

### 14.2. GDB display命令

和 print 命令一样，display 命令也用于调试阶段查看某个变量或表达式的值，它们的区别是，使用 display 命令查看变量或表达式的值，每当程序暂停执行（例如单步执行）时，GDB 调试器都会自动帮我们打印出来，而 print 命令则不会。

也就是说，使用 1 次 print 命令只能查看 1 次某个变量或表达式的值，而同样使用 1 次 display 命令，每次程序暂停执行时都会自动打印出目标变量或表达式的值。因此，当我们想频繁查看某个变量或表达式的值从而观察它的变化情况时，使用 display 命令可以一劳永逸。

display 命令没有缩写形式，常用的语法格式如下 2 种：
```
(gdb) display expr
(gdb) display/fmt expr
```

其中，expr 表示要查看的目标变量或表达式；参数 fmt 用于指定输出变量或表达式的格式，表 1 罗列了常用的一些 fmt 参数。
<center>表 1 /fmt 常用的值</center>

| /fmt | 功 能                                |
| ---- | ------------------------------------ |
| /x   | 以十六进制的形式打印出整数。         |
| /d   | 以有符号、十进制的形式打印出整数。   |
| /u   | 以无符号、十进制的形式打印出整数。   |
| /o   | 以八进制的形式打印出整数。           |
| /t   | 以二进制的形式打印出整数。           |
| /f   | 以浮点数的形式打印变量或表达式的值。 |
| /c   | 以字符形式打印变量或表达式的值。     |

> 注意，display 命令和 /fmt 之间不要留有空格。以 /x 为例，应写为 (gdb)display/x expr。

仍以调试 main.exe 为例：
```
(gdb) b 4
Breakpoint 1 at 0x40053c: file main.c, line 4.
(gdb) b 9
Breakpoint 2 at 0x400569: file main.c, line 9.
(gdb) r
Starting program: /root/demo/main.exe

Breakpoint 1, main () at main.c:4
4   scanf("%d", &num);
(gdb) display num
1: num = 32767
(gdb) display/t result
2: /t result = 0
(gdb) n
3
5   while(i<=num){
2: /t result = 0
1: num = 3
(gdb) c
Continuing.

Breakpoint 2, main () at main.c:9
9   printf("result=%d\n", result);
2: /t result = 110
1: num = 3
(gdb) c
Continuing.
result=6

Program exited normally.
(gdb)
```

可以看到，使用 display 命令查看 num 和 result 变量值时，不仅在执行该命令的同时会看到目标变量的值，后续每次程序停止执行时，GDB 调试器都会将目标变量的值打印出来。

事实上，对于使用 display 命令查看的目标变量或表达式，都会被记录在一张列表（称为自动显示列表）中。通过执行`info dispaly`命令，可以打印出这张表：
```
(gdb) info display
Auto-display expressions now in effect:
Num Enb Expression
2:   y   /t result
1:   y   num
```

其中，各列的含义为：

- Num 列为各变量或表达式的编号，GDB 调试器为每个变量或表达式都分配有唯一的编号；
- Enb 列表示当前各个变量（表达式）是处于激活状态还是禁用状态，如果处于激活状态（用 y 表示），则每次程序停止执行，该变量的值都会被打印出来；反之，如果处于禁用状态（用 n 表示），则该变量（表达式）的值不会被打印。
- Expression 列：表示查看的变量或表达式。


对于不需要再打印值的变量或表达式，可以将其删除或者禁用。

1) 通过执行如下命令，即可删除自动显示列表中的变量或表达式：
```
(gdb) undisplay num...
(gdb) delete display num...
```

参数 num... 表示目标变量或表达式的编号，编号的个数可以是多个。

举个例子：
```
(gdb) undisplay 1
(gdb) info display
Auto-display expressions now in effect:
Num Enb Expression
2:   y   /t result
(gdb)
```

可以看到，借助 undisplay 命令成功删除了编号为 1 的 num 变量。

2) 通过执行如下命令，可以禁用自动显示列表中处于激活状态下的变量或表达式：
```
(gdb) disable display num...
```

num... 表示要禁用的变量或表达式的编号，编号的个数可以是多个，表示一次性禁用多个变量或表达式

举个例子：
```
(gdb) disable display 2
(gdb) info display
Auto-display expressions now in effect:
Num Enb Expression
2:   n   /t result
(gdb) 
```

可以看到，编号为 2 的 result 变量的 Enb 由 y 变成了 n。处于禁用状态的变量或表达式，程序停止执行时将不再自动打印出它们的值。

当然根据需要，也可以激活当前处于禁用状态的变量或表达式，执行如下命令即可：
```
(gdb) enable display num...
```

参数 num... 表示要激活的变量或表达式的编号，编号的个数可以是多个，表示一次性激活多个变量或表达式。

举个例子：
```
(gdb) enable display 2
(gdb) info display
Auto-display expressions now in effect:
Num Enb Expression
2:  y /t result
(gdb) 
```

总的来说，每次程序停止执行时，GDB 调试器会将自动显示列表中处于激活状态下的变量或表达式的值打印出来，display 命令可以实现在查看目标变量或表达式的值的同时，将其添加到自动显示列表中，而 print 命令则只会打印出目标变量或表达式的值。

## 15. GDB print命令更高级的用法

[14. GDB print和display命令：查看变量的值](#14.%20GDB%20print和display命令：查看变量的值)一节中，就如何在 GDB 调试代码过程中查看某一变量或表达式的值，对 print 和 display 命令的功能和基本用法做了详细的介绍。

除此之外，print 命令还有更高级的功能和用法，例如以指定的格式输出变量或者表达式的值、输出数组中指定区间内的所有元素等等，接下来将给读者一一做详细的讲解。

和 print 命令最基本的用法相比，该命令的完整语法格式如下所示：

`(gdb) print [options --] [/fmt] expr`

> 格式中用 [ ] 括起来的部分是可选的，可以使用也可以省略。

其中，各个参数的含义如下：

1. options：表示该命令所支持的选项，这些选项可以控制 print 命令输出指定内容的变量或者表达式的值；
2. fmt：指定输出变量或表达式值时所采用的格式；
3. expr：指定要查看的变量或表达式。

1、首先介绍 options 参数的用法，表 1 罗列了常用的几个 options 参数值。
<center>表 1 print options参数的取值</center>

| options 参数           | 功 能                                                        |
| ---------------------- | ------------------------------------------------------------ |
| -address on\|off       | 查看某一指针变量的值时，是否同时打印其占用的内存地址，默认值为 on。该选项等同于单独执行 set print address on\|off 命令。 |
| -array on\|off         | 是否以便于阅读的格式输出数组中的元素，默认值为 off。该选项等同于单独执行 set printf array on\|off 命令。 |
| -array-indexes on\|off | 对于非字符类型数组，在打印数组中每个元素值的同时，是否同时显示每个元素对应的数组下标，默认值为 off。该选项等同于单独执行 set print array-indexes on\|off 命令。 |
| -pretty on\|off        | 以便于阅读的格式打印某个结构体变量的值，默认值为 off。该选项等同于单独执行 set print pretty on\|off 命令。 |

> 注意，options 参数和 /fmt 或者 expr 之间，必须用`--`（ 2 个 - 字符）分隔。此外，options 参数还有很多选项可以使用，感兴趣的读者可自行前往 [GDB官网](https://sourceware.org/gdb/current/onlinedocs/gdb/Data.html#Data)查看，这里不再一一罗列。


2、和 display 命令一样，print 命令可允许自定义输出格式，表 2 罗列了几个常用的 /fmt 参数。
<center>表 2 /fmt 常用的值</center>

| /fmt | 功 能                                |
| ---- | ------------------------------------ |
| /x   | 以十六进制的形式打印出整数。         |
| /d   | 以有符号、十进制的形式打印出整数。   |
| /u   | 以无符号、十进制的形式打印出整数。   |
| /o   | 以八进制的形式打印出整数。           |
| /t   | 以二进制的形式打印出整数。           |
| /f   | 以浮点数的形式打印变量或表达式的值。 |
| /c   | 以字符形式打印变量或表达式的值。     |

> 当 print 命令不指定任何 options 参数时，print 和 /fmt 之间不用添加空格，例如以十六进制的形式输出 num 整形变量的值，执行命令为 (gdb) print/x num。


3、我们知道，print 命令可以打印指定变量或表达式的值。值得一提的是，当指定目标表达式时，除了表达式本身外，GDB 调试器还支持使用`@`和`::`运算符。

`@`运算符用于输出数组中指定区域的元素，使用格式如下：
```
(gdb) print first@len
```

其中，参数 first 用于指定数组查看区域内的首个元素的值；参数 len 用于指令自 first 元素开始查看的元素个数。

假设有一个 array 数组，其定义如下：
```
int array[5] = {1,2,3,4};
```

如果我们想查看第 1 个元素和第 2 个元素的值，可以执行如下指令：
```
(gdb) print array[0]@2
$1 = {1, 2}
```

当程序中包含多个作用域不同但名称相同的变量或表达式时，可以借助`::`运算符明确指定要查看的目标变量或表达式。`::`运算符的语法格式如下：
```
(gdb) print file::variable
(gdb) print function::variable
```

其中 file 用于指定具体的文件名，funciton 用于指定具体所在函数的函数名，variable 表示要查看的目标变量或表达式。

举个例子：
```
#include <stdio.h>
int num = 10;
int main(){
    int num = 20;
    return 0;
}
```

假设该程序存储在 main.c 文件中，则使用 GDB 调试至第 5 行（return 0）处暂停后，通过执行如下命令，即可查看 num 全局变量的值：
```
(gdb) print 'main.c'::num
$1 = 10
```

而通过执行如下命令，可以查看 num 局部变量的值：
```
(gdb) p main::num
$1 = 20
```

当然，由于 GDB 调试就暂停在 main() 函数中，因此即便不指明`main::`，这里的 num 默认指代的也是 num 局部变量。

为了让读者彻底搞清楚 print 命令的用法，这里以一段 C 语言程序为例，为大家演示以上所讲 print 命令的用法：
```
(gdb) l
1 #include <stdio.h>
2 typedef struct website{
3   char *url;
4   int time;
5 }web;
6 int num = 10;
7 int main(){
8   int num = 20;
9   int array[10]={1,2,3,4,5,6};
10   web LanguageC = {"http://c.biancheng.net",6};
(gdb)
11   return 0;
12 }
(gdb) b 11
Breakpoint 1 at 0x11cf: file main.c, line 11.
(gdb) r
Starting program: ~/demo/main.exe

Breakpoint 1, main () at main.c:11
11   return 0;
(gdb) print -address on -- LanguageC.url           <-- 打印 url 指针的同时，输出其所在的内存地址
$1 = 0x555555556004 "http://c.biancheng.net"
(gdb) print -address off -- LanguageC.url           <-- 打印 url 指针，但不输出其所在的内存地址
$2 = "http://c.biancheng.net"
(gdb) print -pretty on -- LanguageC                <-- 以便于阅读的方式，输出结构体的值
$3 = {
 url = 0x555555556004 "http://c.biancheng.net",
 time = 6
}
(gdb) print LanguageC                          <-- 以压缩的格式输出结构体的值
$4 = {url = 0x555555556004 "http://c.biancheng.net", time = 6}
(gdb) print/x num                              <-- 以十六进制的形式输出局部变量 num 的值
$5 = 0x14
(gdb) print array[1]@2                           <-- 从 array[1] 处开始，输出 array 数组中后续的 2 个元素的值
$6 = {2, 3}
(gdb) print num                                <-- 输出局部变量 num 的值
$7 = 20
(gdb) print 'main.c'::num                         <-- 输出全局变量 num 的值
$8 = 10
(gdb)

```

## 16. GDB禁用和删除断点

我们知道，GDB 调试器支持 3 种断点，分别为普通断点（用 break 命令创建）、观察断点（用 watch 命令建立）以及捕捉断点（用 catch 命令建立）。并且如果需要的话，我们可以在被调试程序中打多个断点，甚至于 GDB 允许在同一位置打多个断点。

这就产生一个问题，如果之前建立的断点不再需要或者暂时不需要，该如何删除或者禁用呢？常用的方式有 2 种：

1. 使用 quit 命令退出调试，然后重新对目标程序启动调试，此方法会将消除上一次调试操作中建立的所有断点；
2. 使用专门删除或禁用断点的命令，既可以删除某一个断点，也可以删除全部断点。

本节教大家如何使用命令删除或者禁用之前已建好的断点，讲解过程中，将以调试如下 C 语言程序为例：
```
#include <stdio.h>
int main(){
    int num = 0;
    scanf("%d", &num);
    printf("%d", num);
    return 0;
}
```

程序存储在`~/demo/main.c`文件中，并已将其编译为可调试的 main.exe 可执行文件：
```
[root@bogon demo]# gcc main.c -o main.exe -g
[root@bogon demo]# ls
main.c main.exe
```

### 16.1. 查看当前已建好的断点

对于当前调试环境中已经建好且尚未删除的断点，可以通过以下 2 种方式查看它们。

1) 借助如下指令，可以查看当前调试环境中存在的所有断点，包括普通断点、观察断点以及捕捉断点：
```
(gdb) info breakpoint [n]
(gdb) info break [n]
```

参数 n 作为可选参数，为某个断点的编号，表示查看指定断点而非全部断点。

要知道，任何类型的断点在建立时，GDB 调试器都会为其分配一个独一无二的断点编号。以 main.exe 为例，我们尝试建立如下断点：

```
(gdb) b 1
Breakpoint 1 at 0x1189: file main.c, line 2.
(gdb) r
Starting program: ~/demo/main.exe

Breakpoint 1, main () at main.c:2
2 int main(){
(gdb) watch num
Hardware watchpoint 2: num
(gdb) catch throw int
Catchpoint 3 (throw)
```

可以看到，我们通过 break 命令建立了一个普通断点，其编号为 1；通过 watch 命令建立了一个观察断点，其编号为 2；通过 catch 命令建立了一个捕捉断点，其编号为 3。

在此基础上，可以通过执行 info break 或者 info breakpoint 命令，查看所有断点的具体信息：
```
(gdb) info break
Num   Type           Disp Enb Address                               What
1      breakpoint      keep y   0x0000555555555189 in main at main.c:2    breakpoint already hit 1 time
2      hw watchpoint   keep y                                        num
3      catchpoint       keep y   exception throw                        matching: int
(gdb)
```

借助每个断点不同的编号，也可以进行精准查询：
```
(gdb) info break 1
Num   Type       Disp Enb Address                                What
1      breakpoint   keep y   0x000000000040053c in main at main.c:2      breakpoint already hit 1 time
```

> 以上输出信息中各列的含义分别是：断点编号（Num）、断点类型（Type）、是临时断点还是永久断点（Disp）、目前是启用状态还是禁用状态（Enb）、断点的位置（Address）、断点当前的状态（作用的行号、已经命中的次数等，用 What 列表示）。

2) 除此之外，对于调试环境中已经建好且未删除的观察断点，也可以使用 info watchpoint 命令进行查看，语法格式如下：
```
(gdb) info watchpoint [n]
```

n 为可选参数，为某个观察断点的编号，功能是只查看该编号的观察断点的信息，而不是全部的观察断点。

继续在上面的调试环境中，执行如下指令：
```
(gdb) info watchpoint
Num   Type         Disp Enb Address      What
2      hw watchpoint keep y               num
(gdb) info watchpoint 1
No watchpoint number 1.
```

由于当前环境中仅有 1 个观察断点，因此 info watchpoint 命令仅罗列了编号为 2 的观察断点的信息。需要注意的是，该命令仅能用于查看观察断点，普通断点和捕捉断点无法使用该命令。

### 16.2. GDB删除断点

无论是普通断点、观察断点还是捕捉断点，都可以使用 clear 或者 delete 命令进行删除。

1、clear命令
clear 命令可以删除指定位置处的所有断点，常用的语法格式如下所示：
```
(gdb) clear location
```

参数 location 通常为某一行代码的行号或者某个具体的函数名。当 location 参数为某个函数的函数名时，表示删除位于该函数入口处的所有断点。

在上面调试环境中，继续执行如下命令：
```
(gdb) clear 2

(gdb) info break
Deleted breakpoint 1
Num   Type          Disp Enb Address        What
2      hw watchpoint keep y                 num
3      catchpoint     keep y   exception throw matching: int
(gdb)
```

可以看到，断点编号为 1、位于程序第 2 行代码处的普通断点已经被删除了。

2、delete 命令

delete 命令（可以缩写为 d ）通常用来删除所有断点，也可以删除指定编号的各类型断点，语法格式如下：
```
delete [breakpoints] [num]
```

其中，breakpoints 参数可有可无，num 参数为指定断点的编号，其可以是 delete 删除某一个断点，而非全部。

举个例子：
```
(gdb) delete 2
(gdb) info break
Num   Type       Disp Enb Address         What
3      catchpoint   keep y  exception throw   matching: int
```

可以看到，delete 命令删除了编号为 2 的观察断点。

如果不指定 num 参数，则 delete 命令会删除当前程序中存在的所有断点。例如：
```
(gdb) delete
Delete all breakpoints? (y or n) y
(gdb) info break
No breakpoints or watchpoints.
```

### 16.3. GDB禁用断点

所谓禁用，就是使目标断点暂时失去作用，必要时可以再将其激活，恢复断点原有的功能。

禁用断点可以使用 disable 命令，语法格式如下：
```
disable [breakpoints] [num...]
```

breakpoints 参数可有可无；num... 表示可以有多个参数，每个参数都为要禁用断点的编号。如果指定 num...，disable 命令会禁用指定编号的断点；反之若不设定 num...，则 disable 会禁用当前程序中所有的断点。

举个例子：
```
(gdb) info break
Num   Type          Disp Enb  Address                   What
1      breakpoint     keep y    0x0000555555555189 in main at main.c:2 breakpoint already hit 1 time
2      hw watchpoint  keep y                             num
3      catchpoint     keep y    exception throw            matching: int
(gdb) disable 1 2
(gdb) info break
Num   Type         Disp Enb  Address                   What
1      breakpoint    keep n    0x0000555555555189 in main  at main.c:2 breakpoint already hit 1 time
2      hw watchpoint keep n                              num
3      catchpoint     keep y    exception throw             matching: int
(gdb)
```

可以看到，对于用 disable 命令禁用的断点，Enb 列用 n 表示其处于禁用状态，用 y 表示该断点处于激活状态。

对于禁用的断点，可以使用 enable 命令激活，该命令的语法格式有多种，分别对应有不同的功能：
```
enable [breakpoints] [num...]            激活用 num... 参数指定的多个断点，如果不设定 num...，表示激活所有禁用的断点
enable [breakpoints] once num…         临时激活以 num... 为编号的多个断点，但断点只能使用 1 次，之后会自动回到禁用状态
enable [breakpoints] count num...   临时激活以 num... 为编号的多个断点，断点可以使用 count 次，之后进入禁用状态
enable [breakpoints] delete num…        激活 num.. 为编号的多个断点，但断点只能使用 1 次，之后会被永久删除。
```

其中，breakpoints 参数可有可无；num... 表示可以提供多个断点的编号，enable 命令可以同时激活多个断点。

仍以上面的调试环境为例，当下程序停止在第 2 行（main() 函数开头处），此时执行如下命令：
```
(gdb) info break
Num   Type         Disp Enb Address                    What
1      breakpoint    keep n   0x0000555555555189 in main  at main.c:2 breakpoint already hit 1 time
2      hw watchpoint keep n                             num
3      catchpoint     keep y   exception throw             matching: int
(gdb) enable delete 2
(gdb) info break
Num   Type          Disp  Enb Address                    What
1      breakpoint     keep  n   0x0000555555555189 in main  at main.c:2 breakpoint already hit 1 time
2      hw watchpoint  del   y                             num
3      catchpoint     keep y    exception throw              matching: int
(gdb) c
Continuing.

Hardware watchpoint 2: num

Old value = 32767
New value = 0
main () at main.c:4
4   scanf("%d", &num);
(gdb) info break
Num   Type        Disp Enb  Address                   What
1      breakpoint   keep n    0x0000555555555189 in main  at main.c:2 breakpoint already hit 1 time
3      catchpoint   keep y     exception throw             matching: int
(gdb)
```

可以看到，通过借助 enable delete 命令，我们激活了编号为 2 的观察断点，但其只能作用 1 次，因此当继续执行程序时，其会在程序第 4 行暂停，随时该断点会被删除。

## 17. 如何用GDB调试多线程程序？

顾名思义，多线程程序指的就是拥有多个（≥2）线程的程序，各个线程可以独立完成一项任务，更重要的是它们可以并发执行。这也就意味着完成相同的任务，多线程程序执行花费的时间往往会少于单线程程序。

但有得也有失，多线程程序的编写更容易产生异常或 Bug（例如线程之间因竞争同一资源发生了死锁、多个线程同时对同一资源进行读和写等等），调试多线程程序，往往需要耗费更多的精力。

庆幸的是，GDB 调试器不仅仅支持调试单线程程序，还支持调试多线程程序。本质上讲，使用 GDB 调试多线程程序的过程和调试单线程程序类似，不同之处在于，调试多线程程序需要监控多个线程的执行过程，进而找到导致程序出现问题的异常或 Bug，而调试单线程程序只需要监控 1 个线程。

表 1 罗列了 GDB 调试多线程程序时常用的命令以及它们各自的功能。
<center>表 1 GDB调试多线程程序常用命令</center>

| 调试命令                            | 功 能                                                        |
| ----------------------------------- | ------------------------------------------------------------ |
| info threads                        | 查看当前调试环境中包含多少个线程，并打印出各个线程的相关信息，包括线程编号（ID）、线程名称等。 |
| thread id                           | 将线程编号为 id 的线程设置为当前线程。                       |
| thread apply id... command          | id... 表示线程的编号；command 代指 GDB 命令，如 next、continue 等。整个命令的功能是将 command 命令作用于指定编号的线程。当然，如果想将 command 命令作用于所有线程，id... 可以用 all 代替。 |
| break location thread id            | 在 location 指定的位置建立普通断点，并且该断点仅用于暂停编号为 id 的线程。 |
| set scheduler-locking off\|on\|step | 默认情况下，当程序中某一线程暂停执行时，所有执行的线程都会暂停；同样，当执行 continue 命令时，默认所有暂停的程序都会继续执行。该命令可以打破此默认设置，即只继续执行当前线程，其它线程仍停止执行。 |

> 表 1 也仅罗列了 GDB 调试多线程程序的一部分常用命令，有关更多其他命令，读者可前往 [GDB官网](https://sourceware.org/gdb/current/onlinedocs/gdb/Thread-Stops.html#Thread-Stops)了解。


接下来，我将以调试如下 C 语言多线程程序为例，给大家详细讲解表 1 中这些命令的功能和用法：
```
#include <stdio.h>
#include <pthread.h>
void* thread_job(void*name)
{
    char * thread_name = (char*)name;
    printf("this is %s\n",thread_name);
    printf("http://c.biancheng.net\n");
}
int main()
{
    pthread_t tid1,tid2;
    pthread_create(&tid1, NULL, thread_job, "thread1_job");
    pthread_create(&tid2, NULL, thread_job, "thread2_job");
    pthread_join(tid1,NULL);
    pthread_join(tid2,NULL);
    printf("this is main\n");
    return 0;
}
```

此程序的存储位置为 ~/demo/main.c。可以看到，此程序中包含 3 个线程，分别为 main 主线程、tid1 子线程和 tid2 子线程。

需要注意的是，将此程序编译为可供 GDB 调试的可执行程序时，需执行如下命令：
```
gcc main.c -o main.exe -g -lpthread
```

因为 pthread 线程库并不属于 Linux 系统中的默认库，所以编译、链接时就需要为 gcc 命令附加 -lpthread 参数。

### 17.1. GDB查看所有线程

info threads 命令的功能有 2 个，既可以查看当前调试环境下存在的线程数以及各线程的具体信息，也可以通过指定线程的编号查看某个线程的具体信息。

info threads 命令的完整语法格式如下：
```
(gdb) info threads [id...]
```

其中，参数 id... 作为可选参数，表示要查看的线程编号，编号个数可以是多个。

以 main.exe 程序为例：
```
(gdb) b 6
Breakpoint 1 at 0x11d9: file main.c, line 6.
(gdb) r
Starting program: ~/demo/main.exe
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
[New Thread 0x7ffff7d9f700 (LWP 54283)]   <--新线程
[New Thread 0x7ffff759e700 (LWP 54284)]   <--新线程
[Switching to Thread 0x7ffff7d9f700 (LWP 54283)]    <--该线程作为当前线程，因为它最先碰到断点

Thread 2 "main.exe" hit Breakpoint 1, thread_job (name=0x555555556027) at main.c:6
6   char * thread_name = (char*)name;
(gdb) info threads
 Id  Target Id                                 Frame
 1  Thread 0x7ffff7da0740 (LWP 54279) "main.exe"  __pthread_clockjoin_ex (threadid=140737351644928, thread_return=0x0, clockid=<optimized out>, abstime=<optimized out>, block=<optimized out>) at pthread_join_common.c:145
* 2  Thread 0x7ffff7d9f700 (LWP 54283) "main.exe"  thread_job (name=0x555555556027) at main.c:6
 3  Thread 0x7ffff759e700 (LWP 54284) "main.exe"  thread_job (name=0x555555556033) at main.c:6
```

> 其中 Id 列表示各个线程的编号（ID 号）；Target Id 列表示各个线程的标识符；Frame 列打印各个线程执行的有关信息，例如线程名称，线程暂停的具体位置等。

要知道，使用 GDB 调试多线程程序时，同一时刻我们调试的焦点都只能是某个线程，被称为当前线程。整个调试过程中，GDB 调试器总是会从当前线程的角度为我们打印调试信息。如上所示，当执行 r 启动程序后，GDB 编译器自行选择标识号为 LWP 54283（编号为 2）的线程作为当前线程，则随后打印的暂停运行的信息就与该线程有关，而没有打印出编号为 1 和 3 的暂停信息。

GDB 调试器为了方便用户快速识别出当前线程，执行 info thread 命令后，Id 列前标有 * 号的线程即为当前线程。

> 这里要提醒大家的，我们输入的调试命令并不仅仅作用于当前线程，例如 continue、next 等，默认情况下它们作用于所有线程。

### 17.2. GDB调整当前线程

用 GDB 调试多线程程序的过程中，根据需要可以随时对当前线程进行调整，这就需要用到 thead id 命令。

thread id 命令用于将编号为 id 的线程设定为当前线程。举个例子：
```
(gdb) thread 3
[Switching to thread 3 (Thread 0x7ffff759e700 (LWP 54284))]  <--切换当前线程
#0 thread_job (name=0x555555556033) at main.c:6
6   char * thread_name = (char*)name;
(gdb) info threads
 Id  Target Id                                Frame
 1  Thread 0x7ffff7da0740 (LWP 54279) "main.exe"  __pthread_clockjoin_ex (threadid=140737351644928, thread_return=0x0, clockid=<optimized out>, abstime=<optimized out>, block=<optimized out>) at pthread_join_common.c:145
 2  Thread 0x7ffff7d9f700 (LWP 54283) "main.exe"  thread_job (name=0x555555556027) at main.c:6
* 3  Thread 0x7ffff759e700 (LWP 54284) "main.exe"  thread_job (name=0x555555556033) at main.c:6
```

可以看到，改变当前线程的同时，GDB 调试器为我们打印出了该线程暂停执行的具体信息。再次执行 info threads 命令可以看到，编号为 3 的线程确实成为了新的当前线程。

### 17.3. GDB执行特定线程

如果想单独控制某一线程进行指定的操作，可以借助 thread apply id... command 命令实现：
```
(gdb) thread apply id... command
```

参数 id... 表示要控制的目标线程的编号，编号个数可以是多个。如果想控制所有线程，可以用 all 代替书写所有线程的编号；参数 command 表示要目标线程执行的操作，例如 next、continue 等。

举个例子：
```
(gdb) info threads
 Id  Target Id                                 Frame
 1  Thread 0x7ffff7da0740 (LWP 54279) "main.exe"  __pthread_clockjoin_ex (threadid=140737351644928, thread_return=0x0, clockid=<optimized out>, abstime=<optimized out>, block=<optimized out>) at pthread_join_common.c:145
 2  Thread 0x7ffff7d9f700 (LWP 54283) "main.exe"   thread_job (name=0x555555556027) at main.c:6
* 3  Thread 0x7ffff759e700 (LWP 54284) "main.exe"  thread_job (name=0x555555556033) at main.c:6
(gdb) thread apply 2 next

Thread 2 (Thread 0x7ffff759e700 (LWP 54284)):
[Switching to Thread 0x7ffff759e700 (LWP 54284)]  <-- 由于 3 号线程暂停执行，所以切换 3 号线程作为当前线程

Thread 3 "main.exe" hit Breakpoint 1, thread_job (name=0x555555556033) at main.c:6
6   char * thread_name = (char*)name;
(gdb) thread apply 2 next

Thread 2 (Thread 0x7ffff7d9f700 (LWP 54283)):
7   printf("this is %s\n",thread_name);
(gdb) 
```

如上所示，当我们调用 thread apply 2 next 命令对 2 号线程进行逐步调试时，3 号线程也会运行，这是为什么呢？这和 GDB 调试器的调试机制有关。

默认情况下，无论哪个线程暂停执行，其它线程都会随即暂停；反之，一旦某个线程启动（借助 next、step、continue 命令），其它线程也随即启动。GDB 调试默认的这种调试模式（称为全停止模式），一定程序上可以帮助我们更好地监控程序中各个线程的执行。

> 注意，当对某个线程进行单步调试时，其它线程也会随即执行和停止，但执行的往往不只是一行代码，可能是多行代码。

### 17.4. GDB为特定线程设置断点

当调试环境中拥有多个线程时，我们可以选择为特定的线程设置断点，该断点仅对指定线程有效。

为特定的某个线程设置断点，可以使用如下命令：
```
(gdb) break location thread id
(gdb) break location thread id if...
```

location 表示设置断点的具体位置；id 表示断点要作用的线程的编号；if... 参数作用指定断点激活的条件，即只有条件符合时，断点才会发挥作用。

> 默认情况下，当某个线程执行遇到断点时，GDB 调试器会自动将该线程作为当前线程，并提示用户 "[Switching to Thread n]"，其中 n 即为新的当前线程。

举个例子：
```
(gdb) break 7 thread 3
Breakpoint 5 at 0x5555555551e1: file main.c, line 7.
(gdb) info break
Num   Type      Disp Enb Address      What
4    breakpoint   keep y  0x00005555555551d9 in thread_job at main.c:6 breakpoint already hit 1 time
5    breakpoint   keep y  0x00005555555551e1 in thread_job at main.c:7 thread 3 stop only in thread 3
```

可以看到，我们在第 7 行代码处为 3 号线程单独设置了一个普通断点，该断点仅对 3 号线程有效。

> 有关在设置断点的基础为其添加触发条件，我已经在《[GDB条件断点](http://c.biancheng.net/view/8255.html)》一节做了详细的讲解，这里不再重复赘述。

### 17.5. GDB设置线程锁

前面提到，使用 GDB 调试多线程程序时，默认的调试模式为：一个线程暂停运行，其它线程也随即暂停；一个线程启动运行，其它线程也随即启动。要知道，这种调试机制确实能帮我们更好地监控各个线程的“一举一动”，但并非适用于所有场景。

一些场景中，我们可能只想让某一特定线程运行，其它线程仍维持暂停状态。要想达到这样的效果，就需要借助 set scheduler-locking 命令。 此命令可以帮我们将其它线程都“锁起来”，使后续执行的命令只对当前线程或者指定线程有效，而对其它线程无效。

set scheduler-locking 命令的语法格式如下：
```
(gdb) set scheduler-locking mode
```

其中，参数 mode 的值有 3 个，分别为 off、on 和 step，它们的含义分别是：

- off：不锁定线程，任何线程都可以随时执行；
- on：锁定线程，只有当前线程或指定线程可以运行；
- step：当单步执行某一线程时，其它线程不会执行，同时保证在调试过程中当前线程不会发生改变。但如果该模式下执行 continue、until、finish 命令，则其它线程也会执行，并且如果某一线程执行过程遇到断点，则 GDB 调试器会将该线程作为当前线程。

举个例子：
```
(gdb) info threads
 Id  Target Id                                 Frame
 1  Thread 0x7ffff7da0740 (LWP 54279) "main.exe"  __pthread_clockjoin_ex (threadid=140737351644928, thread_return=0x0, clockid=<optimized out>, abstime=<optimized out>, block=<optimized out>) at pthread_join_common.c:145
* 2  Thread 0x7ffff7d9f700 (LWP 54283) "main.exe"   thread_job (name=0x555555556027) at main.c:6
 3  Thread 0x7ffff759e700 (LWP 54284) "main.exe"  thread_job (name=0x555555556033) at main.c:6
(gdb) set scheduler-locking on
(gdb) next
7   printf("this is %s\n",thread_name);
(gdb) info threads
 Id  Target Id                                 Frame
 1  Thread 0x7ffff7da0740 (LWP 54279) "main.exe"  __pthread_clockjoin_ex (threadid=140737351644928, thread_return=0x0, clockid=<optimized out>, abstime=<optimized out>, block=<optimized out>) at pthread_join_common.c:145
* 2  Thread 0x7ffff7d9f700 (LWP 54283) "main.exe"   thread_job (name=0x555555556027) at main.c:7
 3  Thread 0x7ffff759e700 (LWP 54284) "main.exe"  thread_job (name=0x555555556033) at main.c:6
(gdb)
```

可以看到，通过执行 set scheduler-locking on 命令，接下来的 next 命令只对当前线程（2号线程）有效，其它线程仍保持原有的暂停状态。

同时，我们可以通过执行 show scheduler-locking 命令，查看各个线程锁定的状态，例如：
```
(gdb) show scheduler-locking
Mode for locking scheduler during execution is "on".
```

显然，当前 set scheduler-locking 命令的值为 on。

## 18. GDB non-stop模式详解

《[GDB调试多线程程序](http://c.biancheng.net/view/8261.html)》一节提到，对于调试多线程程序，GDB 默认采用的是 all-stop 模式，即只要有一个线程暂停执行，所有线程都随即暂停。这种调试模式可以适用于大部分场景的需要，借助适当数量的断点，我们可以清楚地监控到各个线程的具体执行过程。

但在某些场景中，我们可能需要调试个别的线程，并且不想在调试过程中，影响其它线程的运行。这种情况下，可以将 GDB 的调试模式由 all-stop 模式更改为 non-stop 模式，该模式下调试多线程程序，当某一线程暂停运行时，其它线程仍可以继续执行。

> 注意，只有 7.0 版本以上的 GDB 调试器，才支持 non-stop 模式。如果 GDB 版本不满足需求，可以阅读《[GDB下载和安装教程](http://c.biancheng.net/view/8130.html)》一节安装高版本的 GDB。

也就是说，non-stop 模式下可以进行 all-stop 模式无法做到的调试工作，例如：

- 保持其它线程继续执行的状态下，单独调试某个线程；
- 在所有线程都暂停执行的状态下，单步调试某个线程；
- 单独执行多个线程等等。

另外还有一点和 all-stop 模式不同的是，在 all-stop 模式下，continue、next、step 命令的作用对象并不是当前线程，而是所有的线程；但在 non-stop 模式下，continue、next、step 命令只作用于当前线程。

> 在 non-stop 模式下，如果想要 continue 命令作用于所有线程，可以为 continue 命令添加一个 -a 选项，即执行 continue -a 或者 c -a 命令，即可实现令所有线程继续执行的目的。

那么，GDB 调试多线程程序时，怎样才能由 all-stop 模式转换到 non-stop 模式呢？很简单，未启动程序前执行如下命令即可：

```
(gdb) set non-stop mode
```

其中，mode 参数的值有 2 种，分别是 on 和 off，on 表示启用 non-stop 模式；off 表示禁用 non-stop 模式。

为了向读者展示 non-stop 模式的功能，这里以调试如下的 C 语言程序为例：
```
#include <stdio.h>
#include <pthread.h>
static void *thread1_job()
{
    printf("this is 1\n");
}
static void *thread2_job()
{
    printf("this is 2\n");
}
int main()
{
    pthread_t tid1,tid2;
    pthread_create(&tid1, NULL, thread1_job, NULL);
    pthread_create(&tid2, NULL, thread2_job, NULL);
    pthread_join(tid1,NULL);
    pthread_join(tid2,NULL);
    printf("this is main\n");
    return 0;
}
```

此程序存储于 ~/demo/main.c 文件中。可以看到，程序中包含有 3 个线程，分别为 main 主线程、thread1_job 子线程和 thread2_job 子线程。

注意，将 main.c 文件编译为供 GDB 调试的可执行文件时，需执行如下命令：

```
[root@bogon demo]# gcc main.c -o main.exe -g -lpthread
[root@bogon demo]# ls
main.c  main.exe
[root@bogon demo]# gdb main.exe -q
Reading symbols from main.exe...
(gdb) 
```

因为 pthread 线程库并不属于 Linux 系统中的默认库，所以编译、链接时就需要为 gcc 命令附加 -lpthread 参数。

在此基础上，下面开始为大家演示在 non-stop 模式下调试多线程程序：

```
(gdb) set non-stop on                <-- 开启 non-stop 模式
(gdb) b 6                          <-- 在 thread1_job 线程执行的 thread1_job 函数中设置断点
Breakpoint 1 at 0x11b1: file main.c, line 6.
(gdb) r                             <-- 开始执行程序
Starting program: ~/demo/main.exe
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
[New Thread 0x7ffff7d9f700 (LWP 55442)]

Thread 2 "main.exe" hit Breakpoint 1, thread1_job () at main.c:6
6   printf("this is 1\n");                <-- thread1_job 线程受断点影响暂停
(gdb) [New Thread 0x7ffff759e700 (LWP 55443)]
this is 2
[Thread 0x7ffff759e700 (LWP 55443) exited]
Quit                                <-- thread2_job 一直执行至结束。      
(gdb) info threads
 Id  Target Id                  Frame
\* 1  Thread 0x7ffff7da0740 (LWP 55438) "main.exe" (running)
 2  Thread 0x7ffff7d9f700 (LWP 55442) "main.exe" thread1_job () at main.c:6
```

可以看到，如果在 all-stop 模式下，thread1_job 线程的暂停执行势必会导致 main 主线程和 thread2_job 线程暂停执行；但在 non-stop 模式下却完全相反，thread1_job 线程的暂停，并未影响到 main 主线程和 thread2_job 线程，其中 thread2_job 线程执行完毕后自动退出，而 main 主线程一直在运行（等待 thread1_job 线程执行结束）。

另外通过这个实例，还可以发现一点 non-stop 模式和 all-stop 模式的不同。在 all-stop 模式下，当某一线程暂停执行时，GDB 调试器会自行将其切换为当前线程；而在 non-stop 模式下不会。这也就解释了上面实例中，当 thread1_job 因断点暂停时当前线程仍为 main.exe 主线程。

除此之外，通过执行 show non-stop 命令，可以查看 non-stop 模式是否开启。例如：

```
(gdb) show non-stop
Controlling the inferior in non-stop mode is on.
```


注意，借助 set non-stop 或者 show non-stop 命令，也只能判断出 non-stop 模式是否开启，并不意味着当前执行的程序一定以 non-stop 模式运行。原因很简单，并非所有的调试环境都支持 non-stop 模式，即便借助 set non-step 命令启动了该模式，GDB 调试器也可能会自动切换至 all-stop 模式。

## 19. GDB如何在后台执行调试命令？

前面章节中，我们已经接触了很多调试命令，如 run（r）、continue（c）、next（n）等，借助它们即可操控 GDB 调试目标程序。有些读者可能已经注意到，这些调试命令在执行过程中，是无法使用其它 GDB 调试命令的，换句话说，只有当一个调试命令执行结束后，(gdb) 命令提示符才会出现，我们才能执行下一个调试命令。

事实上，对于某些调试命令，GDB 调试器提供有 2 种执行方式：

- 同步执行：“一个一个”的执行，即必须等待前一个命令执行完毕，才能执行下一个调试命令。
- 后台执行：又称“异步执行”，即当某个调试命令开始执行时，(gdb) 命令提示符会立即出现，我们无需等待前一个命令执行完毕就可以继续执行下一个调试命令。 


以后台（异步）的方式执行一个调试命令，其语法格式如下：

```
(gdb) command&
```

其中，command 表示就是要执行的调试命令。command 和 & 之间不需要添加空格。

显然，通过在目标命令的后面添加一个 '&' 字符，即可使该命令以后台的方式执行。例如，continue 命令的异步执行版本为`continue&` 或者`c&`。

表 1 罗列了支持后台执行的一些常用的调试命令。

<center>表 1 GDB支持后台执行的调试命令</center>

| 调试命令   | 含 义                                                        |
| ---------- | ------------------------------------------------------------ |
| run（r）   | 启动被调试的程序。有关此命令的具体用法，读者可阅读《[GDB run 命令](http://c.biancheng.net/view/8174.html)》一节。 |
| attach     | 调试处于运行着的的程序。                                     |
| step       | 单步调试程序。有关此命令的具体用法，可阅读《[GDB单步调试程序](http://c.biancheng.net/view/8232.html)》一节。 |
| stepi      | 执行一条机器指令。                                           |
| next(n)    | 单步调试程序。有关此命令的具体用法，可阅读《[GDB单步调试程序](http://c.biancheng.net/view/8232.html)》一节。 |
| nexti      | 执行一条机器指令，其与 stepi 命令的区别，类似于 step 和 next 命令的区别。 |
| continue   | 继续执行程序。                                               |
| finish     | 结束当前正在执行的函数。有关此命令的用法，可阅读《[GDB断点调试](http://c.biancheng.net/view/8203.html)》一节。 |
| until（u） | 快速执行完当前的循环体。有关此命令的用法，可阅读《[GDB单步调试程序](http://c.biancheng.net/view/8232.html)》一节。 |

> 注意，并非所有的调试命令都支持异步执行。如果目标调试命令不支持后台执行的形式，当我们尝试使用“命令名+&”的方式执行该命令时，GDB 调试器会提示类似“Function "&" not defined.”的错误信息。

后台执行命令异步调试程序的方法，多用于 non-stop 模式中。虽然 all-stop 模式中也可以使用，但在前一个异步命令未执行完毕前，仍旧不能执行其它命令。

### 19.1. GDB interrupt命令：暂停后台线程执行

对于在后台处于执行状态的线程，可以使用 interrupt 命令将其中断。以调试《[GDB non-stop模式](http://c.biancheng.net/view/8270.html)》一节给出的多线程程序为例：

```
(gdb) l
1 #include <stdio.h>
2 #include <pthread.h>
3
4 static void *thread1_job()
5 {
6   printf("this is 1\n");
7 }
8 static void *thread2_job()
9 {
10   printf("this is 2\n");
(gdb)
11 }
12
13 int main()
14 {
15   pthread_t tid1,tid2;
16   pthread_create(&tid1, NULL, thread1_job, NULL);
17   pthread_create(&tid2, NULL, thread2_job, NULL);
18   pthread_join(tid1,NULL);
19   pthread_join(tid2,NULL);
20   printf("this is main\n");
(gdb)
21   return 0;
22 }
(gdb) b 6
Breakpoint 1 at 0x11b1: file main.c, line 6.
(gdb) set non-stop on                       <-- 以 non-stop 模式调试程序
(gdb) r
Starting program: ~/demo/main.exe
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
[New Thread 0x7ffff7d9f700 (LWP 57816)]
[New Thread 0x7ffff759e700 (LWP 57817)]
this is 2

Thread 2 "main.exe" hit Breakpoint 1, thread1_job () at main.c:6
6   printf("this is 1\n");
(gdb) [Thread 0x7ffff759e700 (LWP 57817) exited]

(gdb) info threads
 Id  Target Id                                Frame
\* 1  Thread 0x7ffff7da0740 (LWP 57812) "main.exe" (running)
  2  Thread 0x7ffff7d9f700 (LWP 57816) "main.exe"  thread1_job () at main.c:6
(gdb)
```

可以看到，当以 non-stop 模式调试程序时，由于我们仅在第 6 行代码处打上了断点，因此启动程序中，仅存在 2 个线程（1 个线程执行完毕后结束了），并且 1 号线程是一直在后台执行着的。

借助 interrupt 命令，我们可以将 1 号线程暂停执行：

```
(gdb) interrupt
(gdb)
Thread 1 "main.exe" stopped.
......
(gdb) info threads
 Id  Target Id                                Frame
\* 1  Thread 0x7ffff7da0740 (LWP 57812) "main.exe" ......at pthread_join_common.c:145
 2  Thread 0x7ffff7d9f700 (LWP 57816) "main.exe"  hread1_job () at main.c:6
(gdb)
```

可以看到，1 号线程停止了运行。

> 注意，在 all-stop 模式下，interrupt 命令作用于所有线程，即该命令可以令整个程序暂停执行；而在 non-stop 模式下，interrupt 命令仅作用于当前线程。 如果想另其作用于所有线程，可以执行 interrupt -a 命令。

## 20. GDB调试多进程程序

GDB调试器不只可以调试多线程程序，还可以调试多进程程序。

对于 C 和 C++ 程序而言，多进程的实现往往借助的是`<unistd.h>`头文件中的 fork() 函数或者 vfork() 函数。举个例子：
```
#include <stdio.h>
#include <unistd.h>
int main()
{
    pid_t pid = fork();
    if(pid == 0)
    {
        printf("this is child,pid = %d\n",getpid());
    }
    else
    {
        printf("this is parent,pid = %d\n",getpid());
    }
    return 0;
}
```

程序的存储路径为`~/demo/myfork.c`。可以看到，程序中包含 2 个进程，分别为父进程（又称主进程）和使用 fork() 函数分离出的子进程。

事实上在多数 Linux 发行版系统中，GDB 并没有对多进程程序提供友好的调试功能。无论程序中调用了多少次 fork() 函数（或者 vfork() 函数），从父进程中分离出多少个子进程，GDB 默认只调试父进程，而不调试子进程。

那么问题就出现了，如何使用 GDB 调试多进程程序中的子进程呢？

### 20.1. GDB attach命令调试进程

首先，无论父进程还是子进程，都可以借助 attach 命令启动 GDB 调试它。attach 命令用于调试正在运行的进程，要知道对于每个运行的进程，操作系统都会为其配备一个独一无二的 ID 号。在得知目标子进程 ID 号的前提下，就可以借助 attach 命令来启动 GDB 对其进行调试。

这里还需要解决一个问题，很多场景中子进程的执行时间都是一瞬而逝的，这意味着，我们可能还未查到它的进程 ID 号，该进程就已经执行完了，何谈借助 attach 命令对其调试呢？对于 C、C++ 多进程程序，解决该问题最简单直接的方法是，在目标进程所执行代码的开头位置，添加一段延时执行的代码。

例如，将上面程序中`if(pid==0)`判断语句整体做如下修改：
```
if(pid == 0)
{
    int num =10;
    while(num==10){
        sleep(10);
    }
    printf("this is child,pid = %d\n",getpid());
}
```

可以看到，通过添加第 3~6 行代码，该进程执行时会直接进入死循环。这样做的好处有 2 个，其一是帮助 attach 命令成功捕捉到要调试的进程；其二是使用 GDB 调试该进程时，进程中真正的代码部分尚未得到执行，使得我们可以从头开始对进程中的代码进行调试。

> 有读者可能会问，进程都已经进行死循环了，后续代码还可以进行调试吗？当然可以，以上面示例中给出的死循环，我们只需用 print 命令临时修改 num 变量的值，即可使程序跳出循环，从而执行后续代码。

就以调试修改后的 myfork.c 程序（已将其编译为 myfork.exe 可执行文件）为例：
```
[root@bogon demo]# gdb myfork.exe -q
Reading symbols from ~/demo/myfork.exe...done.
(gdb) r
Starting program: ~/demo/myfork.exe
Detaching after fork from child process 5316. <-- 子进程的 ID 号为 5316
this is parent,pid = 5313        <-- 父进程执行完毕

Program exited normally.
(gdb) attach 5316             <-- 跳转调试 ID 号为 5316 的子进程
......
(gdb) n                      <-- 程序正在运行，所有直接使用 next 命令就可以进行单步调试
Single stepping until exit from function __nanosleep_nocancel,
which has no line number information.
0x00000037ee2acb50 in sleep () from /lib64/libc.so.6
(gdb) n
Single stepping until exit from function sleep,
which has no line number information.
main () at myfork.c:10
10 while(num==10){
(gdb) p num=1
$1 = 1
(gdb) n                      <-- 跳出循环
13     printf("this is child,pid = %d\n",getpid());
(gdb) c
Continuing.
this is child,pid = 5316

Program exited normally.
(gdb) 
```

> 对于子进程 ID 号的获取，除了依靠 GDB 调试器打印出的信息，也可以使用 pidof 命令手动获取。有关 pidof 命令获取进程 ID 好的具体用法，我已经在《[调用GDB的几种方式](http://c.biancheng.net/view/8166.html)》一节中做了详细的讲解，这里不再重复赘述。

### 20.2. GDB显式指定要调试的进程

前面提到，GDB 调试多进程程序时默认只调试父进程。对于内核版本为 2.5.46 甚至更高的 Linux 发行版系统来说，可以通过修改 follow-fork-mode 或者 detach-on-fork 选项的值来调整这一默认设置。

#### 20.2.1. GDB follow-fork-mode选项

确切地说，对于使用 fork() 或者 vfork() 函数构建的多进程程序，借助 follow-fork-mode 选项可以设定 GDB 调试父进程还是子进程。该选项的使用语法格式为：
```
(gdb) set follow-fork-mode mode
```

参数 mode 的可选值有 2 个：

- parent：选项的默认值，表示 GDB 调试器默认只调试父进程；
- child：和 parent 完全相反，它使的 GDB 只调试子进程。且当程序中包含多个子进程时，我们可以逐一对它们进行调试。

举个例子：
```
(gdb) show follow-fork-mode
Debugger response to a program call of fork or vfork is "parent".
(gdb) set follow-fork-mode child            <-- 调试子进程
(gdb) r 
Starting program: ~/demo/myfork.exe
[New process 5376]
this is parent,pid = 5375         <-- 父进程执行完成

Program received signal SIGTSTP, Stopped (user).
[Switching to process 5376]       <-- 自动进入子进程
0x00000037ee2accc0 in __nanosleep_nocancel () from /lib64/libc.so.6
(gdb) n
Single stepping until exit from function __nanosleep_nocancel,
which has no line number information.
0x00000037ee2acb50 in sleep () from /lib64/libc.so.6
(gdb) n
Single stepping until exit from function sleep,
which has no line number information.
main () at myfork.c:10
10 while(num==10){
(gdb) p num=1
$2 = 1
(gdb) c
Continuing.
this is child,pid = 5376
```

通过执行如下命令，我们可以轻松了解到当前调试环境中 follow-fork-mode 选项的值：

```
(gdb) show follow-fork-mode
Debugger response to a program call of fork or vfork is "child".
```

#### 20.2.2. GDB detach-on-fork选项

注意，借助 follow-fork-mode 选项，我们只能选择调试子进程还是父进程，且一经选定，调试过程中将无法改变。如果既想调试父进程，又想随时切换并调试某个子进程，就需要借助 detach-on-fork 选项。

detach-on-fork 选项的语法格式如下：
```
(gdb) set detach-on-fork mode
```

其中，mode 参数的可选值有 2 个：

- on：默认值，表明 GDB 只调试一个进程，可以是父进程，或者某个子进程；
- off：程序中出现的每个进程都会被 GDB 记录，我们可以随时切换到任意一个进程进行调试。

和 detach-on-fork 搭配使用的，还有如表 1 所示的几个命令。

<center>表 1 GDB多进程调试常用命令</center>

| 命令语法格式             | 功 能                                                        |
| ------------------------ | ------------------------------------------------------------ |
| (gdb)show detach-on-fork | 查看当前调试环境中 detach-on-fork 选项的值。                 |
| (gdb) info inferiors     | 查看当前调试环境中有多少个进程。其中，进程 id 号前带有 * 号的为当前正在调试的进程。 |
| (gdb) inferiors id       | 切换到指定 ID 编号的进程对其进行调试。                       |
| (gdb) detach inferior id | 断开 GDB 与指定 id 编号进程之间的联系，使该进程可以独立运行。不过，该进程仍存在 info inferiors 打印的列表中，其 Describution 列为 \<null>，并且借助 run 仍可以重新启用。 |
| (gdb) kill inferior id   | 断开 GDB 与指定 id 编号进程之间的联系，并中断该进程的执行。不过，该进程仍存在 info inferiors 打印的列表中，其 Describution 列为 \<null>，并且借助 run 仍可以重新启用。 |
| remove-inferior id       | 彻底删除指令 id 编号的进程（从 info inferiors 打印的列表中消除），不过在执行此操作之前，需先使用 detach inferior id 或者 kill inferior id 命令将该进程与 GDB 分离，同时确认其不是当前进程。 |


> 除表 1 罗列的这几个命令，GDB 调试其提供有其它的一些命令，由于不常用，这里不再罗列，读者可前往[ GDB官网](https://sourceware.org/gdb/current/onlinedocs/gdb/Inferiors-Connections-and-Programs.html#Inferiors-Connections-and-Programs)自行查看。

这里仍以调试 myfork.c 程序为例，不过为了让读者清楚地感受 detach-on-fork 选项的功能，这里需要对 else 语句块的代码进行如下修改：

```
else
{
    int mnum=5;
    while(mnum==5){
        sleep(1);
    }
    printf("this is parent,pid = %d\n",getpid());
}
```

> 也就是说，myfork.c 程序中，父进程和子进程中各拥有一个死循环。

在此基础上，进行如下调试： 
```
(gdb) set detach-on-fork off       <-- 令 GDB 可调试多个进程
(gdb) b 6
Breakpoint 1 at 0x11b5: file myfork.c, line 6.
(gdb) r
Starting program: ~/demo/myfork.exe

Breakpoint 1, main () at myfork.c:6
6   pid_t pid = fork();
(gdb) n
[New inferior 2 (process 5163)]      <-- 新增一个子进程，ID 号为 5163
Reading symbols from ~/demo/myfork.exe...
Reading symbols from /usr/lib/debug/lib/x86_64-linux-gnu/libc-2.31.so...
7   if(pid == 0)
(gdb) n                        <-- 由于 GDB 默认调试父进程，因此进入 else 语句
17   int mnum=5;
(gdb) info inferiors   <-- 查看当前调试环境中的进程数，当前有 2 个进程，1 号进程为当前正在调试的进程
 Num Description    Executable    
* 1  process 5159   ~/demo/myfork.exe
 2  process 5163    ~/demo/myfork.exe
(gdb) inferior 2                  <-- 进入 id 号为 2 的子进程
[Switching to inferior 2 [process 5163] (~/demo/myfork.exe)]
[Switching to thread 2.1 (process 5163)]
(gdb) n
53 in ../sysdeps/unix/sysv/linux/arch-fork.h
(gdb) n
__libc_fork () at ../sysdeps/nptl/fork.c:78
78 ../sysdeps/nptl/fork.c: No such file or directory.
(gdb) n
......                            <-- 执行多个 next 命令
(gdb) n
main () at myfork.c:7              <-- 正式单步调试子进程 
7   if(pid == 0)
(gdb) n
9     int num =10;
(gdb)
```

可以看到，通过设置 detach-on-fork 选项值为 off，再配合使用 info inferiors 等命令，即可随意切换到当前环境中的各个进程，并对它们进行调试。

> 感兴趣的读者，可自行以默认模式（即 detach-on-fork 值为 on）调试该程序，对比它们之间的区别。

## 21. GDB如何进行反向调试？

读到本节，我们已经学会了借助 GDB 调试器对代码进行单步调试和断点调试。这 2 种调试方法有一个共同的特点，即调试过程中代码一直都是“正向”执行的（从第一行代码执行到最后一行代码）。这就产生一个问题，如果调试过程中不小心多执行了一次 next、step 或者 continue 命令，又或者想再次查看刚刚程序执行的过程，该怎么办呢？

面对这种情况，很多读者想到的是借助 run 命令重新启动程序，还原之前所做的所有调试工作。的确，这种方式可以解决上面列举的类似问题，只不过比较麻烦。事实上，如果我们使用的是 7.0 及以上版本的 GDB 调试器，还有一种更简单的解决方法，即反向调试。

所谓反向调试，指的是临时改变程序的执行方向，反向执行指定行数的代码，此过程中 GDB 调试器可以消除这些代码所做的工作，将调试环境还原到这些代码未执行前的状态。

> 注意，目前 GDB 调试器对反向调试的功能支持还不完善，反向调试尚不能适用于所有的调试场景，本节后续会做详细讲解。

要想反向调试程序，我们需要学会使用相应的一些命令。表 1 为大家罗列了完成反向调试常用的一些命令，并且展示各个命令的使用格式和对应的功能。
<center>表 1 GDB反向调试的常用命令</center>

| 命 令                            | 功 能                                                        |
| -------------------------------- | ------------------------------------------------------------ |
| (gdb) record (gdb) record btrace | 让程序开始记录反向调试所必要的信息，其中包括保存程序每一步运行的结果等等信息。进行反向调试之前（启动程序之后），需执行此命令，否则是无法进行反向调试的。 |
| (gdb) reverse-continue (gdb) rc  | 反向运行程序，直到遇到使程序中断的事件，比如断点或者已经退回到 record 命令开启时程序执行到的位置。 |
| (gdb) reverse-step               | 反向执行一行代码，并在上一行代码的开头处暂停。和 step 命令类似，当反向遇到函数时，该命令会回退到函数内部，并在函数最后一行代码的开头处（通常为 return 0; ）暂停执行。 |
| (gdb) reverse-next               | 反向执行一行代码，并在上一行代码的开头处暂停。和 reverse-step 命令不同，该命令不会进入函数内部，而仅将被调用函数视为一行代码。 |
| (gdb) reverse-finish             | 当在函数内部进行反向调试时，该命令可以回退到调用当前函数的代码处。 |
| (gdb) set exec-direction mode    | mode 参数值可以为 forward （默认值）和 reverse：forward 表示 GDB 以正常的方式执行所有命令；reverse 表示 GDB 将反向执行所有命令，由此我们直接只用step、next、continue、finish 命令来反向调试程序。注意，return 命令不能在 reverse 模式中使用。 |

> 注意，表 1 中仅罗列了常用的一些命令，并且仅展示了各个命令最常用的语法格式。有关 GDB 调试器提供的更多支持反向调试的命令，以及各个命令不同的语法格式，感兴趣的读者可前往[ GDB官网](https://sourceware.org/gdb/current/onlinedocs/gdb/Reverse-Execution.html#Reverse-Execution)查看。


接下来以调试如下的 C 语言程序为例，给大家演示如何使用表 1 中的命令实现反向调试：
```
#include <stdio.h>
int main ()
{
    int n, sum;
    n = 1;
    sum = 0;
    while (n <= 100)
    {
        sum = sum + n;
        n = n + 1;
    }
    return 0;
}
```

程序存储在`~/demo/main.c`文件中，并以生成可供 GDB 调试的 main.exe 可执行文件。在此基础上，进行如下调试操作：
```
(gdb) b 6
Breakpoint 1 at 0x40047f: file main.c, line 6.
(gdb) r
Starting program: ~/demo/main.exe

Breakpoint 1, main () at main.c:6
6   sum = 0;
(gdb) record                            <-- 开启记录模式
(gdb) b 10 if n==10
Breakpoint 2 at 0x40048e: file main.c, line 10.
(gdb) c
Continuing.

Breakpoint 2, main () at main.c:10
10     n = n + 1;
(gdb) p n                              <-- 执行至 n=10，sum=55
$1 = 10
(gdb) p sum
$2 = 55
(gdb) reverse-next                      <-- 回退一步，暂停在第 9 行代码开头处
9     sum = sum + n;
(gdb) p n                              <-- 此时 n=10，sum=45
$3 = 10
(gdb) p sum
$4 = 45
(gdb) reverse-continue                   <-- 反向执行代码，直至第 6 行，也就是开启记录的起始位置
Continuing.

No more reverse-execution history.
main () at main.c:6
6   sum = 0;
(gdb) p n                               <-- 回到了 n=1，sum=0 的时候
$5 = 1
(gdb) p sum
$6 = 0
(gdb)
```

当然，此示例中仅展示了 GDB 反向调试的部分功能，有关表 1 中的其它命令，读者可自行编写相应的测试程序进行测试，这里不再过多赘述。

#### 21.1.1. GDB 调试功能尚不完善

要知道，GDB 反向调试的功能是 2006 年左右开始研发的，并于 7.0 版本正式发布。到目前为止，GDB 反向调试的功能还不太稳定，尚无法适用于所有的调试场景。

例如，对于打印到屏幕的数据并不会因为打印语句的回退而自动消失。换句话说，对于程序中出现的打印语句（例如 C 语言中的 printf() 输出函数），虽然可以进行反向调试，但已经输出到屏幕上的数据不会因反向调试而撤销。另外，反向调试也不适用于包含 I/O 操作的代码。

总的来说，尽管 GDB 调试功能尚不完善，但瑕不掩瑜，它能满足大部分场景的需要。在需要进行将代码反向执行时，读者可以先尝试使用 GDB 反向调试，其次再使用 run 命令重新开启调试。

## 22. GDB handle命令：信号处理

C、C++ 程序中，信号常常作为进程间通信的一种重要手段。举个例子：
```
#include <stdio.h>
#include <unistd.h>
#include <signal.h>
void display(){
    printf("http://c.biancheng.net/gdb/");
}
int main ()
{
    pid_t cpid;
    pid_t ppid;
    signal(SIGINT,display);
    if((cpid=fork())==0){
        printf("in cpid\n");
        ppid = getppid();
        kill(ppid,SIGINT);
    }else{
        wait(NULL);
    }
    return 0;
}
```

上面程序中存在 2 个进程，分别为 cpid 子进程和 ppid 主进程。cpid 子进程通过 kill() 函数向 ppid 主进程发送了 SIGINT 信号，当主进程接收到此信号时，会由等待状态转而执行 display() 函数。因此程序的执行结果为：
```
in cpid
http://c.biancheng.net/gdb/
```

其中，第一行为 cpid 子进程打印输出的，第二行为 cpid 向 ppid 主进程发出 SIGINT 信号后由 ppid 主进程打印输出的。

值得一提的是，GDB 调试器可以自动捕获 C、C++ 程序中出现的信号，并根据事先约定好的方式处理它（具体如何约定，本节后续会讲）。Linux 系统中已经事先定义好了诸多中信号，我们可以通过执行如下命令查看：
```
[root@bogon demo]# kill -l
1) SIGHUP  2) SIGINT  3) SIGQUIT  4) SIGILL  5) SIGTRAP
6) SIGABRT  7) SIGBUS  8) SIGFPE  9) SIGKILL 10) SIGUSR1
11) SIGSEGV 12) SIGUSR2 13) SIGPIPE 14) SIGALRM 15) SIGTERM
16) SIGSTKFLT 17) SIGCHLD 18) SIGCONT 19) SIGSTOP 20) SIGTSTP
21) SIGTTIN 22) SIGTTOU 23) SIGURG 24) SIGXCPU 25) SIGXFSZ
.......   <-- 省略部分输出
```

其中，每个信号代表着不同的含义，以 SIGINT 信号为例，它表示程序停止执行，该信号可以通过按 `Ctrl+c`组合键发出。换句话说，对于正在执行的程序，通过按`Ctrl+c`键向程序发出 SIGINT 信号，可以使程序停止执行。

与此同时，GDB 调试器提供了`info handles`指令，用于查看 GDB 可以处理的信号种类，以及各个信号的具体处理方式。例如：
```
(gdb) info signals
Signal        Stop Print  Pass to program  Description

SIGHUP        Yes  Yes    Yes              Hangup
SIGINT        Yes  Yes    No               Interrupt
SIGQUIT       Yes  Yes    Yes              Quit
SIGILL        Yes  Yes    Yes              Illegal instruction
SIGTRAP       Yes  Yes    No               Trace/breakpoint trap
SIGABRT       Yes  Yes    Yes              Aborted
SIGEMT        Yes  Yes    Yes              Emulation trap
SIGFPE        Yes  Yes    Yes              Arithmetic exception
SIGKILL       Yes  Yes    Yes              Killed
......
```

其中各列的含义分别为：

- Signal：各个信号的名称；
- Stop：当信号发生时，是否终止程序执行。Yes 表示终止，No 表示当信号发生时程序认可继续执行；
- Print：当信号发生时，是否要求 GDB 打印出一条提示信息。Yes 表示打印，No 表示不打印；
- Pass：当信号发生时，该信号是否对程序可见。Yes 表示程序可以捕捉到该信息，No 表示程序不会捕捉到该信息；
- Description：对信号所表示含义的简单描述。

> 有关以上各个信号所表示的具体含义，由于不是本节重点，这里不做详细赘述。感兴趣的读者可以自行查找资料。

显然，对于现有的所有信号，GDB 调试器会根据 Stop、Print 以及 Pass 列的值进行相应的处理。当然，GDB 调试器提供了 handle 命令，由此我们就可以通过修改目标信号 Stop、Print、Pass 列的值，调试 GDB 调试器对目标信号的处理方式。

### 22.1. GDB handle命令

handle 命令的语法格式如下：
```
(gdb) handle signal mode
```

其中，signal 参数表示要设定的目标信号，它通常为某个信号的全名（SIGINT）或者简称（去除‘SIG’后的部分，如 INT）；如果要指定所有信号，可以用 all 表示。

mode 参数用于明确 GDB 处理该目标信息的方式，其值可以是如下几个：

- nostop：当信号发生时，GDB 不会暂停程序，其可以继续执行，但会打印出一条提示信息，告诉我们信号已经发生；
- stop：当信号发生时，GDB 会暂停程序执行。
- noprint：当信号发生时，GDB 不会打印出任何提示信息；
- print：当信号发生时，GDB 会打印出必要的提示信息；
- nopass（或者 ignore）：GDB 捕获目标信号的同时，不允许程序自行处理该信号；
- pass（或者 noignore）：GDB 调试在捕获目标信号的同时，也允许程序自动处理该信号。

> 注意，当 GDB 捕获到信号并暂停程序执行的那一刻，程序是捕获不到信号的，只有等到程序继续执行时，信号才能被程序捕获。

接下来以一段 C 语言程序为例，为大家演示 GDB 调试器处理信号的过程。
```
#include <stdio.h>
#include <unistd.h>
#include <signal.h>
void display(){
    printf("http://c.biancheng.net/gdb/");
}
int main ()
{
    pid_t cpid;
    pid_t ppid;
    signal(SIGINT,display);
    if((cpid=fork())==0){
        printf("in cpid\n");
        ppid = getppid();
        kill(ppid,SIGINT);
    }else{
        wait(NULL);
    }
    return 0;
}
```

> 该程序存储在 ~/demo/main.c 文件中，并已编译为可供 GDB 调试的 main.exe 可执行程序。

前面已经讲过，SIGINT 信号是可以通过`Ctrl+c`组合键发出的，因此上面程序执行时，除非我们手动发出 SIGINT 信号，程序会马上执行 display() 函数，否则一直输出 "main" 字符串。

下面我们用 GDB 调试器调试 main.exe 程序：
```
(gdb) info signals SIGINT
Signal  Stop  Print Pass to program Description
SIGINT  Yes  Yes  No            Interrupt
(gdb) r
Starting program: ~/demo/main.exe
main
main
^C
Program received signal SIGINT, Interrupt.
0x00000037ee2accc0 in __nanosleep_nocancel () from /lib64/libc.so.6
(gdb) 
```

可以看到，通过执行`info signals SIGINT`命令，我们调取出了当前 GDB 调试器对 SIGINT 信号处理方式的默认设定，即当 SIGINT 信号发生时，GDB 调试器会暂停程序执行，同时打印出必要的提示信息，并且不让程序捕获到该信号。由此，当程序执行过程中按下`Ctrl+c`组合键后，并没有执行 display() 函数，而是立即暂停了程序。

在此基础上，我们继续做如下调试：
```
(gdb) handle SIGINT nostop
SIGINT is used by the debugger.
Are you sure you want to change it? (y or n) y
Signal  Stop  Print Pass to program Description
SIGINT No   Yes  No            Interrupt
(gdb) handle SIGINT pass
SIGINT is used by the debugger.
Are you sure you want to change it? (y or n) y
Signal  Stop  Print Pass to program Description
SIGINT No   Yes  Yes            Interrupt
(gdb) continue
Continuing.
main
main
main
^C
Program received signal SIGINT, Interrupt.
http://c.biancheng.net/gdb/
main
^Z
Program received signal SIGTSTP, Stopped (user).
0x00000037ee2accc0 in __nanosleep_nocancel () from /lib64/libc.so.6
(gdb)
```

可以看到，通过将 SIGINT 信号 Stop 选项改为 No，将 Pass 选项改为 Yes，意味着当程序执行过程中发生 SIGINT 信号时，程序不再中断，并且可以捕获到此信号。因此，当程序执行过程中按下`Ctrl+c`组合键时，看到打印出了 "http://c.baincheng.net/gdb/" 数据。

## 23. GDB frame和backtrace命令：查看栈信息

当程序因某种异常停止运行时，我们要做的就是找到程序停止的具体位置，分析导致程序停止的原因。

对于 C、C++ 程序而言，异常往往出现在某个函数体内，例如 main() 主函数、调用的系统库函数或者自定义的函数等。要知道，程序中每个被调用的函数在执行时，都会生成一些必要的信息，包括：

- 函数调用发生在程序中的具体位置；
- 调用函数时的参数；
- 函数体内部各局部变量的值等等。

这些信息会集中存储在一块称为“栈帧”的内存空间中。也就是说，程序执行时调用了多少个函数，就会相应产生多少个栈帧，其中每个栈帧自函数调用时生成，函数调用结束后自动销毁。

注意，这些栈帧所在的位置也不是随意的，它们集中位于一个大的内存区域里，我们通常将其称为栈区或者栈。

> 每个 C、C++ 程序在执行时，都会占用一整块内存空间。不仅如此，整块内存空间还会进行更细致的划分，例如栈区、堆区、全局数据区、常量区等，以便于将程序中不同的资源存放在不同的的内存区域中。有关各个区域的具体作用，读者可阅读《[Linux下C语言程序的内存布局](http://c.biancheng.net/view/vip_2097.html)》做详细了解。

这也就意味着，当程序因某种异常暂停执行时，如果其发生在某个函数内部，我们可以尝试借助该函数对应栈帧中记录的信息，找到程序发生异常的原因。

庆幸的是，GDB 调试器为了方便用户在调试程序时查看某个栈帧中记录的信息，提供了 frame 和 backtrace 命令。接下来，我将给读者详细讲解一下这 2 个命令的功能和用法。

### 23.1. GDB frame命令

通过阅读上文我们知道，任何一个被调用的函数，执行时都会生成一个存储必要信息的栈帧。对于 C、C++ 程序而言，其至少也要包含一个函数，即 main() 主函数，这意味着程序执行时至少会生成一个栈帧。

> main() 主函数对应的栈帧，又称为初始帧或者最外层的帧。

除此之外，每当程序中多调用一个函数，执行过程中就会生成一个新的栈帧。更甚者，如果该函数是一个递归函数，则会生成多个栈帧。

在程序内部，各个栈帧用地址作为它们的标识符，注意这里的地址并不一定为栈帧的起始地址。我们知道，每个栈帧往往是由连续的多个字节构成，每个字节都有自己的地址，不同操作系统为栈帧选定地址标识符的规则不同，它们会选择其中一个字节的地址作为栈帧的标识符。

然而，GDB 调试器并没有套用地址标识符的方式来管理栈帧。对于当前调试环境中存在的栈帧，GDB 调试器会按照既定规则对它们进行编号：当前正被调用函数对应的栈帧的编号为 0，调用它的函数对应栈帧的编号为 1，以此类推。

frame 命令的常用形式有 2 个：
1、根据栈帧编号或者栈帧地址，选定要查看的栈帧，语法格式如下：
```
(gdb) frame spec
```

该命令可以将 spec 参数指定的栈帧选定为当前栈帧。spec 参数的值，常用的指定方法有 3 种：

1. 通过栈帧的编号指定。0 为当前被调用函数对应的栈帧号，最大编号的栈帧对应的函数通常就是 main() 主函数；
2. 借助栈帧的地址指定。栈帧地址可以通过 info frame 命令（后续会讲）打印出的信息中看到；
3. 通过函数的函数名指定。注意，如果是类似递归函数，其对应多个栈帧的话，通过此方法指定的是编号最小的那个栈帧。

除此之外，对于选定一个栈帧作为当前栈帧，GDB 调试器还提供有 up 和 down 两个命令。其中，up 命令的语法格式为：
```
(gdb) up n
```

其中 n 为整数，默认值为 1。该命令表示在当前栈帧编号（假设为 m）的基础上，选定 m+n 为编号的栈帧作为新的当前栈帧。

相对地，down 命令的语法格式为：
```
(gdb) down n
```

其中 n 为整数，默认值为 1。该命令表示在当前栈帧编号（假设为 m）的基础上，选定 m-n 为编号的栈帧作为新的当前栈帧。

2、借助如下命令，我们可以查看当前栈帧中存储的信息：
```
(gdb) info frame
```

该命令会依次打印出当前栈帧的如下信息：

- 当前栈帧的编号，以及栈帧的地址；
- 当前栈帧对应函数的存储地址，以及该函数被调用时的代码存储的地址
- 当前函数的调用者，对应的栈帧的地址；
- 编写此栈帧所用的编程语言；
- 函数参数的存储地址以及值；
- 函数中局部变量的存储地址；
- 栈帧中存储的寄存器变量，例如指令寄存器（64位环境中用 rip 表示，32为环境中用 eip 表示）、堆栈基指针寄存器（64位环境用 rbp 表示，32位环境用 ebp 表示）等。

除此之外，还可以使用`info args`命令查看当前函数各个参数的值；使用`info locals`命令查看当前函数中各局部变量的值。

### 23.2. GDB backtrace命令

backtrace 命令用于打印当前调试环境中所有栈帧的信息，常用的语法格式如下：
```
(gdb) backtrace [-full] [n]
```

其中，用 [ ] 括起来的参数为可选项，它们的含义分别为：

- n：一个整数值，当为正整数时，表示打印最里层的 n 个栈帧的信息；n 为负整数时，那么表示打印最外层 n 个栈帧的信息；
- -full：打印栈帧信息的同时，打印出局部变量的值。

> 除此之外，backtrace 命令还有其它可选参数，感兴趣的读者可自行前往[ GDB 官网](https://sourceware.org/gdb/current/onlinedocs/gdb/Backtrace.html#Backtrace)查看。

注意，当调试多线程程序时，该命令仅用于打印当前线程中所有栈帧的信息。如果想要打印所有线程的栈帧信息，应执行`thread apply all backtrace`命令。

基于以上对 frame 和 backtrace 命令的介绍，这里以调试如下 C 语言程序为例，给大家演示这 2 个命令的作用。
```
#include <stdio.h>
int func(int num){
    if(num==1){
        return 1;
    }else{
        return num*func(num-1);
    }
}
int main ()
{
    int n = 5;
    int result = func(n);
    printf("%d! = %d",n,result);
    return 0;
}
```

不难发现，func() 是一个递归函数。该程序存储在`~/demo/main.c`文件中，并已编译为可供 GDB 调试的 main.exe 可执行文件。在此基础上，进行如下调试：
```
(gdb) b 3
Breakpoint 1 at 0x4004cf: file main.c, line 3.
(gdb) r
Starting program: ~/demo/main.exe

Breakpoint 1, func (num=5) at main.c:3
3   if(num==1){
(gdb) c
Continuing.

Breakpoint 1, func (num=4) at main.c:3
3   if(num==1){
(gdb) p num
$1 = 4
(gdb) backtrace    <-- 打印所有的栈帧信息
#0 func (num=4) at main.c:3
#1 0x00000000004004e9 in func (num=5) at main.c:6
#2 0x0000000000400508 in main () at main.c:12
(gdb) info frame    <-- 打印当前栈帧的详细信息
Stack level 0, frame at 0x7fffffffe240:      <-- 栈帧编号 0，地址 0x7fffffffe240
rip = 0x4004cf in func (main.c:3); saved rip 0x4004e9  <-- 函数的存储地址 0x4004cf，调用它的函数地址为 0x4004e9
called by frame at 0x7fffffffe260    <-- 当前栈帧的上一级栈帧（编号 1 的栈帧）的地址为 0x7fffffffe260
source language c.
Arglist at 0x7fffffffe230, args: num=4 <-- 函数参数的地址和值
Locals at 0x7fffffffe230, Previous frame's sp is 0x7fffffffe240 <--函数内部局部变量的存储地址
Saved registers:  <-- 栈帧内部存储的寄存器
 rbp at 0x7fffffffe230, rip at 0x7fffffffe238
(gdb) info args     <-- 打印当前函数参数的值
num = 4
(gdb) info locals    <-- 打印当前函数内部局部变量的信息（这里没有）
No locals.
(gdb) up  <-- 查看编号为 1 的栈帧
#1 0x00000000004004e9 in func (num=5) at main.c:6
6     return num*func(num-1);
(gdb) frame 1      <-- 当编号为 1 的栈帧作为当前栈帧
#1 0x00000000004004e9 in func (num=5) at main.c:6
6     return num*func(num-1);
(gdb) info frame   <-- 打印 1 号栈帧的详细信息
Stack level 1, frame at 0x7fffffffe260:
rip = 0x4004e9 in func (main.c:6); saved rip 0x400508
called by frame at 0x7fffffffe280, caller of frame at 0x7fffffffe240 <--上一级栈帧地址为 0x7fffffffe280，下一级栈帧地址为 0x7fffffffe240
source language c.
Arglist at 0x7fffffffe250, args: num=5
Locals at 0x7fffffffe250, Previous frame's sp is 0x7fffffffe260
Saved registers:
 rbp at 0x7fffffffe250, rip at 0x7fffffffe258
(gdb)
```

> 篇幅有限，这里不再做更多的命令演示，读者可自行验证其它命令格式的用法。

## 24. GDB编辑和搜索源码

本节主要讲解的是在 GDB 内对源文件中的代码进行修改和查找，分别对应 GDB 中的 edit 命令和 search 命令，下面是对这两个命令的详细介绍。

### 24.1. GDB edit命令：编辑文件

在 GDB 中编辑源文件中使用 edit 命令，该命令的语法格式如下：
```
(gdb) edit [location]
(gdb) edit [filename] : [location]
```

location 表示程序中的位置。这个命令表示激活文件的指定位置，然后进行编辑。

举个例子：
```
(gdb) edit 16      //表示激活文件中的第16 行的代码（光标定位到第 16 行代码的开头位置）
(gdb) edit func     //表示激活文件中的 func 处的代码（光标定位到 func 函数所在行的开头位置）
(gdb) edit test.c : 16 //表示激活 test.c 文件的第16 行。
```

值得一提的是，GDB edit 命令编辑文件默认使用的是 ex 编译器，使用的时候可能会遇见下面的情况：
```
(gdb) edit
bash: /bin/ex: 没有那个文件或目录
```

遇到这种问题时，我们可以指定任意的编辑器（例如 Vim）去编辑文件。进入 GDB 调试器前，执行如下命令设置 EDITOR 环境变量：
```
export EDITOR=/usr/bin/vim
```

由此，当在 GDB 调试器中执行 edit 命令时，就会自动进入 Vim 编辑器，从而对当前调试的程序进行修改。

注意，上面修改编辑器的方法只是临时生效，当退出 shell 终端后配置就会被还原，下次使用的时候还要再次使用这条命令。想要永久的实现配置，需要去修改配置文件，具体做法是：修改当前 home 目录下的”～/.bashrc”文件，在文件的最后添加上述的命令就可以实现文件的永久配置（修改的是当前用户的配置文件，一般不建议修改系统的配置文件，出现问题不容易恢复）。配置完成重启 shell 终端，就可以完成配置。

### 24.2. GDB search命令：搜索文件

在调试文件时，某些时候可能会去找寻找某一行或者是某一部分的代码。可以使用 list 显示全部的源码，然后进行查看。当源文件的代码量较少时，我们可以使用这种方式搜索。如果源文件的代码量很大，使用这种方式寻找效率会很低。所以 GDB 中提供了相关的源代码搜索的的 search 命令。

search 命令的语法格式为：
```
search <regexp>
reverse-search <regexp>
```

第一项命令格式表示从当前行的开始向前搜索，后一项表示从当前行开始向后搜索。其中 regexp 就是正则表达式，正则表达式描述了一种字符串匹配的模式，可以用来检查一个串中是否含有某种子串、将匹配的子串替换或者从某个串中取出符合某个条件的子串。很多的编程语言都支持使用正则表达式。

使用命令时可能会下面的情况：
```
(gdb) search func
Expression not found
```

表示搜索的范围没有出现要寻找的字符串或者定位到了代码行的末尾。

## 25. GDB help命令：查看目标命令的具体用法

截止到本节，我们接触了大量的 GDB 命令，甚至很多命令还拥有不同的语法格式和参数。这就产生一个问题，如何才能记住它们呢？

实际上，GDB 调试器的开发人员也想到了这个问题。为了降低用户使用 GDB 调试器的学习成本，GDB 提供了 help 命令，它可以帮用户打印出目标命令的功能和具体用法。

首先，为了方便用户能够快速地从众多 GDB 命令中查找到目标命令，help 命令根据不同 GDB 命令的功能对它们做了分类：
```
(gdb) help
List of classes of commands:

aliases -- Aliases of other commands
breakpoints -- Making program stop at certain points
data -- Examining data
files -- Specifying and examining files
internals -- Maintenance commands
obscure -- Obscure features
running -- Running the program
stack -- Examining the stack
status -- Status inquiries
support -- Support facilities
tracepoints -- Tracing of program execution without stopping the program
user-defined -- User-defined commands
```

可以看到，根据各个 GDB 命令的不同功能，help 命令将它们分成了 12 大类，每一类中都包含多个功能类似的 GDB 命令。

以 breakpoints 类为例，该类中包含了 GDB 所有的断点命令。借助 help 命令，我们可以查看某一类中具体包含的 GDB 命令：
```
(gdb) help breakpoints
Making program stop at certain points.

List of commands:

awatch -- Set a watchpoint for an expression
break -- Set breakpoint at specified location
break-range -- Set a breakpoint for an address range
catch -- Set catchpoints to catch events
catch assert -- Catch failed Ada assertions
catch catch -- Catch an exception
……
```

在此基础上，通过 help 命令，我们可以查看指定命令的功能和用法。例如：
```
(gdb) help break
Set breakpoint at specified line or function.
break [LOCATION] [thread THREADNUM] [if CONDITION]
LOCATION may be a line number, function name, or "*" and an address.
If a line number is specified, break at start of code for that line.
If a function is specified, break at start of code for that function.
If an address is specified, break at that exact address.
With no LOCATION, uses current execution address of the selected
stack frame. This is useful for breaking on return to a stack frame.

THREADNUM is the number from "info threads".
CONDITION is a boolean expression.

Multiple breakpoints at one place are permitted, and useful if their
conditions are different.

Do "help breakpoints" for info on other commands dealing with breakpoints.
```

可以看到，我们尝试借助 help 命令查看 break 命令，其打印信息中包括 break 命令的具体功能、完整语法格式以及各个参数的具体含义。由此，只要系统学习过目标命令的读者，借助 help 命令打印出的提示信息，一定可以回忆起来并切实用目标命令开始对程序进行调试。

> 感兴趣的读者，可自行尝试使用 help 命令打印其他命令的使用信息，本节不再过多举例。

### 25.1. GDB自动补全命令

除了 help 命令辅助用户使用 GDB 调试器之外，GDB 还支持命令的自动补全。所谓自动补全，即在 (gdb) 右侧输入 GDB 命令时，对于特别长的命令，我们只需要输入命令的前几个字符，然后按下 Tab 键，GDB 就会自动帮我们补全整个命令。

举个例子：
```
(gdb) info bre<TAB>  
```

如上所示，当我们按下 Tab 键时，GDB 会自行将 bre 补全为 break。

需要注意的是，有些 GDB 命令长得很像，例如 condition 和 continue，此时如果我们只输入 con，GDB 是无法领会我们要使用哪个命令的。这种情况下，我们可以双击 Tab 键，GDB 会将所有可能的命令都罗列出来：
```
(gdb) con <Tab><Tab>
condition continue
```

由此可知在所有的 GDB 命令中，以 con 开头的命令有 2 个。

除此之外，当我们输入完成的 GDB 命令后，通过双击 Tab 键，GDB 会给我们罗列出该命令可用的所有参数。比如：
```
(gdb) catch <Tab><Tab>
assert   exception fork    load    signal   throw   vfork
catch   exec    handlers  rethrow  syscall  unload
```
