## RPM

RPM(RPM Package Manager)是一个本地包管理系统，可以运行在Red Hat Enterprise Linux, CentOS, 和Fedora上，主要提供以下能力：

- Install, reinstall, remove, upgrade and verify packages
- Use a database of installed packages to query and verify packages
- Use metadata to describe packages, their installation instructions, and so on
- Package pristine software sources into source and binary packages
- Add packages to Yum repositories

YUM(Yellowdog Updater, Modified)是一个rpm包管理工具，底层基于PRM，可以进行包的安装，更新，卸载等。可以配置远程仓库，自动化下载，安装，自动下载依赖包，解决依赖问题。

> An RPM package is simply a file containing other files and information about them needed by the system. Specifically, an RPM package consists of the cpio archive, which contains the files, and the RPM header, which contains metadata about the package. The rpm package manager uses this metadata to determine dependencies, where to install files, and other information.

RPM包就是一个文件，其中包含**cpio**归档文件和RPM头，归档文件中包含了系统所需的各项文件，RPM头描述了包的元数据，比如依赖，安装路径等。

有两种类型的RPM包：

- source RPM (SRPM)
- binary RPM

两者格式相同，但是文件内容不同，SRPM里有源代码和一个SPEC文件，SEPC文件描述了如何将源代码构建成一个binary RPM，而binary RPM中包含了由源代码构建的二进制文件。
https://rpm-packaging-guide.github.io/

## 一个程序的一生

### 编码

### 构建

将源代码转换为可执行程序的过程

- Natively Compiled Code（本地编译型代码）
  源代码直接编译成机器码，得到一个二进制可执行文件，能够独立运行。
  编译后的程序是特定于体系结构的。

- Interpreted Code（解释型代码）
  程序可以在解释器或者语言虚拟机上一步步执行，与体系结构无关，依赖于解释器
  - Raw-interpreted
    直接由解释器执行
  - Byte_compiled
    需要编译成字节码，由虚拟机执行

### 打包

### 分发

### 安装

安装是将软件从一种分发形式部署到计算机上，使其能够运行，简单来说就是**把文件放到合适的位置**，安装过程会因为编程语言，分发包形式等有所不同，具体来说会有：

- 将可执行文件，配置文件复制到对应目录下
- 注册动态链接库文件
- 更新系统配置，环境变量等
- 执行安装脚本，完成一些初始化任务等

## Systemd



## 历史

1969年AT&T公司的贝尔实验室开发出**UNIX操作系统**，此后10年UNIX被广泛应用，期间诞生了许多基于UNIX的变种，称为“**类UNIX系统**”，和基于UNIX的衍生组件。十年后AT&T公司意识到了UNIX的商业价值，对代码闭源，并对之前基于UNIX的系统和组件声明著作权，对许多用户造成了负面影响。

1983年，理查德·斯托曼发起了**GNU计划**,旨在创建一个兼容UNIX但完全自由的操作系统。

1991年，林纳斯·托瓦兹不满于使用的MINIX系统，着手开发自己的操作系统，也就是“**Linux内核**”。

GNU（GNU's Not Unix）：GNU计划开发出了所有组件除了一个操作系统内核，例如gcc，gzip等软件。GNU计划的组件和Linux内核组合就是一个完整的操作系统，称为“**GNU/Linux系统**”，如今许多linux发行版都是GNU/Linux系统。

GNU通用公共许可证（GPL）

Linux内核：

### Linux发行版

一个典型的 Linux 发行版除了 Linux 内核以外，通常还会包括一系列 GNU 工具和库、一些附带的软件、说明文档、一个桌面系统、一个窗口管理器和一个桌面环境。

- **Debian 分支：**
   Debian是一个完全由自由软件构成的类UNIX操作系统，生态环境优良，Linux发行版被称为Debian GNU/Linux，Ubuntu由Debian派生出，主打桌面应用。
- **Red Hat 分支：**
   Red Hat Linux 是美国的Red Hat公司发行的一个发行版，在2003年red hat公司停止了维护，转而投身于其收费的企业版Red Hat Enterprise Linux（RHEL）。
   Red Hat停止维护后，社区启动Fedora项目接管源代码并继续维护，演变成Fedora发行版，后被red hat赞助，成为RHEL的上游，在其中做一些新技术的测试。  
   RHEL遵循GNU通用公共许可证，会开放源代码，编译源码可以得到一个可用的操作系统CentOS(Community Enterprise Operating System)。2020年12月CentOS社区将重点转向CentOS Stream，他成为RHEL和Fedora之间的版本。
- **Arch Linux 分支：**

### Android与Linux

Android是Linux的一个发行版，不同于GNU/Linux支，它没有采用GNU组件作为工具，而是谷歌自行研发的另一套体系。基于这套体系构成的组合则被称为Android/Linux，常用于智能手机和嵌入式设备。
由谷歌推出的Android叫做Android原生系统，基于原生系统诞生出的各类独特操作系统就是Android/Linux下的子发行版。


