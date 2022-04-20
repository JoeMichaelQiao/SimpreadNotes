> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/Nocky/article/details/6056695)

转自：[http://hi.baidu.com/psy1011/blog/item/975ebab7954f53f430add1ae.html/cmtid/1fdcb2a11c2d5a8146106447](http://hi.baidu.com/psy1011/blog/item/975ebab7954f53f430add1ae.html/cmtid/1fdcb2a11c2d5a8146106447)

2006-11-29 20:09

知道 CON 是 DOS 下的特殊设备名  
如下由系统保留的设备名字不能用作文件名：  
CON,PRN,AUX,CLOCK$,NUL,COM1,COM2,COM3,COM4,COM5,COM6,COM7,COM8,COM9,LPT1

Windows 9X 的 /con/con 设备名称解析漏洞允许用户远程攻击, 导致 windows98 系统崩溃  
详细：  
请求访问系统上一些包含设备名的非法路径，这将导致 windows9x 崩溃。当 windows 解析这些路径时，内核的溢出导致整个系统出错。这时你只有重新启动系统才能恢复正常，没有其它的选择。  
发现有五个设备或设备驱动程序可以使系统崩溃。它们是:  
CON、NUL、AUX、CLOCK$、CONFIG$，其它的设备如 LPTx 和 COMx 则不行。  
把这些组合起来形成一个路径，如: CON/NUL, NUL/CON, AUX/NUL，只要请求成功，windows 系统将崩溃。  
下面是在 IO.SYS 里面关于这些设备的定义：  
DEVICE DRIVERS  
--------------  
These are specified in IO.SYS and date back from the early Ms Dos  
days. Here is what I have found. Here is a brief list;  
CLOCK$ - System clock  
CON - Console; combination of keyboard and screen to  
handle input and output  
AUX or COM1 - First serial communicationport  
NUL - Dummy port, or the "null device" which we all  
know under Linux as /dev/null.  
CONFIG$ - Unknown  
只要能对这些路径进行请求，不管是远程，还是本地，都可以令系统崩溃，下面是几种方式:  
(1) 如果目标机器上存在共享，不管该共享资源有没有设置密码，都可以用这个办法使系统当机:  
比如机器 192.168.0.2 上有一个名称为 d 的共享，则在 "运行" 中输入下面的命令可以使目标 windows9x 系统崩溃:  
//192.168.0.2/d/nul/nul  
唯一的要求是兰色字的必须是正确的路径，否则无效  
在网上有大量的 windows95/98 的机器，其打印机共享是打开的。这个共享是入侵 windows95/98 的入口，通常它对应 c:/windows/system  
属于只读共享。  
这种攻击方式对这类机器是很有效的:  
//192.168.0.2/printer$/nul/nul  
(2) 制作包含下列 HTML 代码的一个网页，通过邮件方式 (outlook) 或者浏览器设法让攻击目标打开  
  
  
crashing IE  
  
  
  
  
(3) 如果攻击目标上安装了 web server，如 Microsoft Person web server，或者 Frontpage Extention Server，用:  
"Get /con/con" 或者 "Get nul/nul" 的请求，就可以远程当掉 windows 系统，比如:  
[http://xxx.xxx.xxx.xxx/nul/nul](http://xxx.xxx.xxx.xxx/nul/nul)  
如果目标上安装了 FTP server，也可以使用上述 GET 命令来达到目的。  
(4) 在注册表的: Inserting HKEY_LOCAL_MACHINE/Software/CLASSES/exefile/shell/_ 项，用  
c:/con/con "%1" %* or c:/nul/nul "%1" %* 的值，也可以使系统当机。这是宏病毒的一种常用攻击方式。  
(5) 下面的 HTML 代码也可以达到目的:  
  
  
  
  
  
  
  
以上的情况只适用于 IE 浏览器。

Windows 下不能够以下面这些字样来命名文件 / 文件夹，包括：“aux”“com1”“com2”“prn”“con”和 “nul” 等，因为这些名字都属于设备名称，等价于一个 DOS 设备，如果我们把文件命名为这些名字，Windows 就会误以为发生重名，所以会提示 “不能创建同名的文件” 等等。  
当然，有一些特殊的方法可以偷机取巧，建立以这些设备名为名的文件夹，比如我们在命令提示符下执行 “md C:/aux//”，就在 C 盘建了一个名叫 aux 的文件夹。此文件夹虽然可以访问，也可以建立子文件夹，但却无法删除，因为 Windows 不允许以这种方式删除设备。在系统看来，这个 aux 文件夹就是设备。  
那么，如何删除这样的文件 / 文件夹呢？我们只要按照完整的 UNC 路径格式，就是网上邻居的路径格式，正确输入文件路径及文件名即可。比如要删除 C 盘下的 aux 文件夹，可在命令提示符下执行：rd /s //./C:/aux，rd 是命令提示符删除文件夹的命令，/s 参数表示从所有子目录删除指定文件。再比如要删除 C 盘 temp 文件夹下的 nul.exe 文件，在命令提示符下执行：del //./C:/temp/nul.exe 即可。

建立特殊文件名的方法：

1. 建立文件夹。运行 CMD 进入命令提示符窗口。键入以下命令即可 md lpt1/（注意保留字后面一定要多一个 “/”，这是关键！）

2. 访问该文件夹。在 DOS 下新建的，却甭想用 cd lpt1 / 之类的命令进入！其实要访问完全没必要在 DOS 下。呵呵，这个文件夹可以直接在资源管理器中打开，只是无法向其中复制或移动文件，也无法直接删除该文件夹。但却可以直接在里面新建文件和文件夹。不信你可以试试。

2. 删除该文件夹。在命令提示符窗口使用命令：rd //?/d:/lpt1 /s /q （其中 d: 表示盘符）。

小提示

使用这个内置的命令后，Windows 就会绕过典型的 Win32 保留名称检查，从而直接删除文件夹，同理以下命令：DEL //./driveletter:/path/filename，可以删除类似以保留字作为文件名的文件。