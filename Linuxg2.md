 <div align="center">
    <img src="https://www.easyicon.net/api/resizeApi.php?id=546986&size=72" width="72px">
    <br>
</div>

在进行Linux下的图形系统编程时，我们常常会遇到以下这些概念：

Framebuffer, X11, SDL，DFB, miniGUI, OpenGL，QT, GTK，KDE, GNOME等等。

### 一、Linux 图形领域的基础设施 

1.1 X Window 

X Window从逻辑上分为三层：X Server、X Client和X协议。

最底层的X Server（X服务器）主要处理输入/输出信息并维护相关资源，它接受来自键盘、鼠标的操作并将它交给X Client（X客户端）作出反馈，而由X Client传来的输出信息也由它来负责输出；

最上层的X Client则提供一个完整的GUI界面，负责与用户的直接交互（KDE、GNOME都是一个X Client）。

X协议则是衔接X Server与X Client的通讯协议，它的任务是充当这两者的沟通管道。尽管UNIX厂商采用相同的X Window，但终端的X Client并不相同。

XFree86是X Window系统的一个开源的实现。它主要运行于Unix以及类Unix操作系统上。XFree86在显示硬件（鼠标、键盘以及显卡）与桌面环境（也就是窗口管理器）之间提供了一个Client/Server接口。

1.2 SVGALib 
SVGALib是Linux下的底层图形库，也是Linux系统中最早出现的非X图形支持库，它支持标准的VGA图形模式和一些其他的模式，SVGALib的缺点是程序必须以root权限登录，并且它是基于图形卡的，所以不是所有的硬件都支持它。
自从framebuffer这个孪生姐妹诞生后，许多软件由只支持SVGALib变为同时支持两者，甚至一些流行的高层函数库如QT 和GTK只支持Framebuffer，作为一个老的图形支持库，SVGALib目前的应用范围越来越小，尤其是在 Linux 内核增加了FrameBuffer驱动支持之后。


1.3 FrameBuffer 
FrameBuffer是出现在Linux 2.2.xx内核当中的一种驱动程序接口。这种接口将显示设备抽象为帧缓冲区。用户可以将它看成是显示内存的一个映像，将其映射到进程地址空间之后，就可以直接对显存进行读写操作，而写操作可以立即反映在屏幕上。该驱动程序的设备文件一般是/dev/fb0、/dev/fb1 等等。
在应用程序中，一般通过将FrameBuffer设备映射到进程地址空间的方式
来使用，比如下面的程序就打开/dev/fb0设备，并通过mmap系统调用进行地址映射，随后用memset将屏幕清空（这里假设显示模式是1024x766-8位色模式线性内存模式）：

FrameBuffer设备还提供了若干ioctl命令，通过这些命令，可以获得显示
设备的一些固定信息（比如显示内存大小）、与显示模式相关的可变信息（比如分辨率、象素结构、每扫描线的字节宽度），以及伪彩色模式下的调色板信息等等。
FrameBuffer 实际上只是一个提供显示内存和显示芯片寄存器从物理内存映
射到进程地址空间中的设备。所以，对于应用程序而言，如果希望在 FrameBuf
之上进行图形编程，还需要完成其他许多工作。FrameBuffer 就像一张画布，用什么样子的画笔，如何画画，还需要你自己动手完成。


1.4 LibGGI 
LibGGI试图建立一个一般性的图形接口，而这个抽象接口连同相关的输入（鼠标、键盘、游戏杆等）抽象接口一起，可以方便地运行在X Window、SVGALib、FrameBuffer等等之上。建立在LibGGI之上的应用程序，不需重新编译，就可以在上述这些底层图形接口上运行。但不知何故，LibGGI的发展几乎停滞。 


### 二、Linux 图形领域的高级函数库 

2.1 Xlib及其他相关函数库 
　　在X Window系统中进行图形编程时，可以选择直接使用Xlib。Xlib实际上是对底层X协议的封装，可通过该函数库进行一般的图形输出。如果你的X Server支持DGA，则可以通过DGA扩展直接访问显示设备，从而获得加速支持。对一般用户而言，由于Xlib的接口太原始而且复杂，因此一般的图形程序选择其他高级一些的图形库作为基础。比如GTK、QT 等等。这两个函数库同时还是一些高级的图形用户界面的支持函数库。由于种种原因，GTK、QT等函数库存在庞大、占用系统资源多的问题，不太适合在嵌入式系统中使用。这时，你可以选择使用 FLTK，这是一个轻量级的图形函数库，但它的主要功能集中在用户界面上，提供了较为丰富的控件集。 

2.2 SDL 
　　SDL（Simple DirectMedia Layer）是一个跨平台的多媒体游戏支持库。其中包含了对图形、声音、游戏杆、线程等等的支持，目前可以运行在许多平台上，其中包括 X Window、X Window with DGA、Linux FrameBuffer控制台、Linux SVGALib，以及Windows DirectX、BeOS 等等。 
　　因为SDL是专门为游戏和多媒体应用而设计开发，所以它对图形的支持非常优秀，尤其是高级图形能力，比如Alpha混和、透明处理、YUV覆盖、Gamma 校正等等。而且在SDL环境中能够非常方便地加载支持OpenGL的Mesa库，从而提供对二维和三维图形的支持。 
　　可以说，SDL是编写跨平台游戏和多媒体应用的最佳平台，也的确得到了广泛应用。相关信息，可参阅 http://www.libsdl.org/。 

2.3 Allegro
　　Allegro是一个专门为x86平台设计的游戏图形库。最初的Allegro运行在 DOS环境下，而目前可运行在Linux FrameBuffer控制台、Linux SVGALib、X Window等系统上。Allegro提供了一些丰富的图形功能，包括矩形填充和样条曲线生成等等，而且具有较好的三维图形显示能力。由于Allegro的许多关键代码是采用汇编编写的，所以该函数库具有运行速度快、资源占用少的特点。然而，Allegro也存在如下缺点： 
1）对线程的支持较差。Allegro的许多函数是非线程安全的，不能同时在两个以上的线程中使用。 
2）对硬件加速能力的支持不足，在设计上没有为硬件加速提供接口。 
有关 Allegro 的进一步信息，可参阅http://www.allegro.cc/。 

2.4 Mesa3D
　　Mesa3D是一个兼容OpenGL规范的开放源码函数库，是目前Linux上提供专业三维图形支持的惟一选择。Mesa3D同时也是一个跨平台的函数库，能够运行在X Window、X Window with DGA、BeOS、Linux SVGALib 等平台上。 
有关 Mesa3D 的进一步信息，可参阅 http://www.mesa3d.org/。 

2.5 DirectFB
　　DirectFB是专注于Linux FrameBuffer硬件加速的一个图形库，并试图建立一个兼容GTK的嵌入式GUI系统。它以可装载函数库的形式提供对加速 FrameBuffer驱动程序的支持。目前，该函数库正在开发之中（最新版本 0.9.97），详情可见 http://www.directfb.org/。

### 三、面向嵌入式Linux系统的图形用户界面 

3.1 MicoroWindows/NanoX
　　[MicroWindows](http://microwindows.censoft.com/) 是一个开放源码的项目，目前由美国Century Software公司主持开发。该项目的开发一度非常活跃，国内也有人参与了其中的开发，并编写了GB2312等字符集的支持。但在 Qt/Embedded发布以来，该项目变得不太活跃，并长时间停留在0.89Pre7版本。可以说，以开放源码形势发展的MicroWindows项目，已基本停滞。 
  
　　MicroWindows是一个典型的基于客户/服务器体系结构的GUI系统，基本分为三层。最底层是面向图形输出和键盘、鼠标或触摸屏的驱动程序；中间层提供底层硬件的抽象接口，并进行窗口管理；最高层分别提供兼容于X Window和 Windows CE（Win32 子集）的API。 
　　该项目的主要特色在于提供了类似X的客户/服务器体系结构，并提供了相对完善的图形功能，包括一些高级的功能，比如Alpha混合，三维支持，TrueType 字体支持等。但需要注意的是，MicroWindows的图形引擎存在许多问题，可以归纳如下： 
  
1）无任何硬件加速能力。 

2）图形引擎中存在许多低效算法，同时未经任何优化。比如在直线或者圆弧绘图函数中，存在低效的逐点判断剪切的问题。

3）代码质量较差。由于该项目缺少一个强有力的核心代码维护人员，因此代码质量参差不齐，影响整体系统稳定性。这也是MicroWindows长时间停留在 0.89Pre7 版本上的原因。 

　　MicroWindows 采用MPL条款发布（该条款基本类似 LGPL 条款）。 

3.2 OpenGUI
　　[OpenGUI](http://www.tutok.sk/fastgl/) 在Linux系统上存在已经很长时间了。最初的名字叫FastGL，只支持256色的线性显存模式，但目前也支持其他显示模式，并且支持多种操作系统平台，比如 MS-DOS、QNX 和Linux等等，不过目前只支持x86硬件平台。OpenGUI也分为三层。最低层是由汇编编写的快速图形引擎；中间层提供了图形绘制API，包括线条、矩形、圆弧等，并且兼容于 Borland的BGI API。第三层用C++编写，提供了完整的GUI对象集。
  
　　OpenGUI采用LGPL条款发布。OpenGUI比较适合于基于x86平台的实时系统，可移植性稍差，目前的发展也基本停滞。 

3.3 Qt/Embedded
　　Qt/Embedded是著名的Qt库开发商[TrollTech](http://www.trolltech.com/) 发布的面向嵌入式系统的Qt版本。因为Qt是KDE等项目使用的GUI支持库，所以有许多基于Qt 的X Window程序可以非常方便地移植到Qt/Embedded版本上。因此，自从Qt/Embedded以GPL条款形势发布以来，就有大量的嵌入式Linux开发商转到了Qt/Embedded系统上。比如韩国的Miz 公司，台湾省的某些嵌入式Linux应用开发商等等。
  
　　不过，在笔者看来，Qt/Embedded还有一些问题值得开发者注意： 
  
1）目前，该系统采用两种条款发布，其中包括GPL条款。对函数库使用GPL条款，意味着其上的应用需要遵循GPL条款。当然了，如果要开发商业程序，TrollTech也允许你采用另外一个授权条款，这时，就必须向TrollTech交纳授权费用了。 
2）Qt/Embedded是一个C++函数库，尽管Qt/Embedded声称可以裁剪到最少 630K，但这时的Qt/Embedded库已经基本上失去了使用价值。低的程序效率、大的资源消耗也对运行Qt/Embedded的硬件提出了更高的要求。 
3）Qt/Embedded库目前主要针对手持式信息终端，因为对硬件加速支持的匮乏，很难应用到对图形速度、功能和效率要求较高的嵌入式系统当中，比如机顶盒、游戏终端等等。 
4）Qt/Embedded提供的控件集风格沿用了PC风格，并不太适合许多手持设备的操作要求。 
5）Qt/Embedded的结构过于复杂，很难进行底层的扩充、定制和移植，尤其是那个用来实现signal/slot机制的著名的moc文件。 
　　因为上述这些原因，目前所见到的Qt/Embedded 的运行环境，几乎是清一色基于StrongARM的iPAQ。 
注：目前，Qt/Embedded已经增加了对DirectFB驱动的支持，因此具有了图形加速能力，其性能也大大地得到提高。

3.4 MiniGUI
　　[MiniGUI](http://www.minigui.org/) 是由许多自由软件开发人员支持的一个自由软件项目（遵循 LGPL 条款发布），其目标是为基于Linux 的实时嵌入式系统提供一个轻量级的图形用户界面支持系统。该项目自 1998 年底开始到现在，已历经3年多的开发过程。到目前为止，已经非常成熟和稳定。目前，已经正式发布了稳定版本 1.0.9，并且开始了新版本系列的开发，即 MiniGUI Version 1.1.x，该系列的正式版也即将发布。 

　　在MiniGUI几年的发展过程中，有许多值得一提的技术创新点，正是由于这些技术上的创新，才使得 MiniGUI 更加适合实时嵌入式系统；而且 MiniGUI 的灵活性非常好，可以应用在包括手持设备、机顶盒、游戏终端等等在内的各种高端或者低端的嵌入式系统当中。这些技术创新包括： 

1）图形抽象层。图形抽象层对顶层 API 基本没有影响，但大大方便了 MiniGUI 应用程序的移植、调试等工作。目前包含三个图形引擎，SVGALib、LibGGI 以及直接基于 Linux FrameBuffer 的 Native Engine，利用 LibGGI 时，可在 X Window 上运行 MiniGUI 应用程序，并可非常方便地进行调试。与图形抽象层相关的还有输入事件的抽象层。MiniGUI 现在已经被证明能够在基于 ARM、MIPS、StrongARM 以及 PowerPC 等的嵌入式系统上流畅运行。 

2）多字体和多字符集支持。这部分通过设备上下文（DC）的逻辑字体（LOGFONT）实现，不管是字体类型还是字符集，都可以非常方便地进行扩充。应用程序在启动时，可切换系统字符集，比如 GB、BIG5、EUCKR、UJIS。利用 DrawText 等函数时，可通过指定字体而获得其他字符集支持。对于一个窗口来说，同时显示不同语种的文字是可能的。MiniGUI 的这种字符集支持不同于传统通过 UNICODE 实现的多字符集支持，这种实现更加适合于嵌入式系统。

3）两个不同架构的版本。最初的 MiniGUI 运行在 PThread 库之上，这个版本适合于功能单一的嵌入式系统，但存在系统健壮性不够的缺点。在 0.9.98 版本中，我们引入了 MiniGUI-Lite 版本，这个版本在提高系统健壮性的同时，通过一系列创新途径，避免了传统 C/S 结构的弱点，为功能复杂的嵌入式系统提供了一个高效、稳定的 GUI 系统。 
　　在 MiniGUI 1.1.0 版本的开发中，我们参照 SDL 和 Allegro 的图形部分，重新设计了图形抽象层，并增强了图形功能，同时增强了 MiniGUI-Lite 版本的某些特性。这些特性包括： 
1）MiniGUI-Lite 支持层的概念。同一层可容纳多个能够同时显示的客户程序，并平铺在屏幕上显示。 
2）新的 GAL 能够支持硬件加速能力，并能够充分使用显示内存；新 GAL 之上的新 GDI 接口得到进一步增强。新的 GDI 接口可以支持 Alpha 混和、透明位块传输、光栅操作、YUV覆盖、Gamma 校正，以及高级图形功能（椭圆、多边形、样条曲线）等等。 

　　MiniGUI 新版本在图形方面的增强和提高，将大大扩展它的应用领域，希望能够对嵌入式 Linux 上的多媒体应用、游戏开发提供支持。 
　　纵观嵌入式 Linux 系统上的各种图形系统方案，我们发现，许多图形系统（如 Qt/Embedded 和 MicoroWindows），只注重手持设备上的需求，却不太注重其他应用领域的需求，而其他许多需要图形支持的嵌入式 Linux 系统却需要许多独特的、高级的图形功能，而不仅仅是图形用户界面。为此，在接下来的开发中，我们还将在如下领域继续开发 MiniGUI： 

1）提供运行在 MiniGUI上的 JAVA 虚拟机 AWT 组件的实现。 
2）提供 MiniGUI 上的OpenGL 实现。 
3）提供类QT 控件集的C++ 封装。 
3）提供窗口/控件风格主题支持。 
4）在 MiniGUI-Lite 当中增加对矢量字体的支持。

### 四、Linux/Unix系统图形界面原理简单介绍及GTK、QT、GNOME和KDE的关系

最近IT新闻出现较多的就是诺基亚的新版手机操作系统Symbian 3。今天看到在cnbeta上看大家为这个系统争论不休，其焦点也逐渐转移到到塞班新系统的核心技术：Qt 。评论区大家唇枪舌战，不过很多人连基本概念都没搞明白，今天正好无事可做，稍微整理下X11,GTK,QT,GNOME，KDE的区别与联系。

一、在这之前你必须要了解： 

1.linux是基于Unix的

2.塞班Symbian、苹果max os等系统的最底层也是unix

3.linux本身没有图形界面,linux现在的图形界面的实现只是linux下的应用程序  

 实现的

4.Xwindow、Xfree中的X是协议,不是具体的某个软件

5.linux图形界面层次关系：linux本身-->X服务器<-[通过X协议交谈]->窗口管

 理器(综合桌面环境)-->X应用程序

二、linux和windows下界面系统的区别： 

图形界面并不是linux的一部分 ,linux只是一个基于命令行的操作系统,linux和Xfree的关系就相当于当年的DOS和 WINDOWS3.0一样,windows3.0不是独立的操作系统,它只是DOS的扩充,是DOS下的应用程序级别的系统,不是独立的操作系统,同样 XFree只是linux下的一个应用程序而已.不是系统的一部分,但是X的存在可以方便用户使用电脑.WINDOWS95及以后的版本就不一样了,他们 的图形界面是操作系统的一部分,图形界面在系统内核中就实现了,没有了图形界面windows就不成为windows了,但linux却不一样,没有图形界面linux还是linux,很多装linux的WEB服务器就根本不装X服务器。这也WINDOWS和linux的重要区别之一。

三、关于linux两大图形界面KDE和Gnome 

KDE早于Gnome出现，但是KDE基于的Qt是不遵循GPL开源协议的，Qt是一个跨平台的C++图形用户界面库 ，它是挪威TrollTech公司的产品(2008年底被NOKIA收购)。 Qt具有优良的跨平台特性（支持Windows、Linux、各种UNIX、OS390和QNX等）、面向对象机制以及丰富的API，同时也可支持2D/3D渲染和OpenGL API。在当时的同类图形用户界面库产品中，Qt的功能最为强大.但底层的基础 Qt却是一个不遵循GPL的商业软件，这就给KDE上了一道无形的枷锁并带来可能的法律风险。一大批自由程序员对KDE项目的决定深为不满，它们认为利用非自由软件开发违背了GPL的精神。于是这些GNU的狂热信徒兵分两路：其中一部分人去制作Harmonny，试图重写出一套兼容Qt的替代品，这个项目虽然技术上相对简单，但却没有获得KDE项目的支持；另一路人马则决定重新开发一套名为“GNOME（GNU Network Object Environment）”的图形环境来替代KDE。

GNOME选择完全遵循GPL的GTK图形界面库为基础，因此我们也一般将GNOME和KDE两大阵营称为GNOME/GTK和 KDE/Qt。与Qt基于C++语言不同，GTK采用较传统的C语言 ，虽然C语言不支持面向对象设计，看起来比较落后，但当时熟悉C语言的开发者远远多于熟悉C++的开发者。加之GNOME/GTK完全遵循GPL版权公约，吸引了更多的自由程序员参与。

四、linux/unix基于X window的图形显示处理原理 

X Window从逻辑上分为三层：最底层的X Server（X服务器）主要处理输入/输出信息并维护相关资源，它接受来自键盘、鼠标的操作并将它交给X Client（X客户端）作出反馈，而由X Client传来的输出信息也由它来负责输出；最外层的X Client则提供一个完整的GUI界面，负责与用户的直接交互（KDE、Gnome都是一个X Client），而衔接X Server与X Client的就是“X Protocol(X通讯协议)”、它的任务是充当这两者的沟通管道。尽管UNIX厂商采用相同的X Window，但终端的X Client并不相同。

五、Qt、GTK 和KDE、GNOME的关系 

简单来说：为了方便开发人员编写X clients，就有了Xlib来封装X协议；Xlib还不够方便，于是就有了qt和gtk，它们提供了很多窗口控件（widgets）。
为了方便用户 ，就出现了gnome和kde等桌面管理系统。一般来说，linux用户看到的界面就是其中之一了。gnome用的是gtk库，kde用的是qt库。

参考链接

[默默淡然](https://www.cnblogs.com/liangxiaofeng/p/5744729.html)
