   我们知道unix/linux刚开始的时候是没有图形界面的，随着时代的发展，排版、制图、多媒体应用越来越普遍了，这些需求都需要用到图形界面（Graphical User Interface）。为此，MIT在1984年开发出了X window system，X在字母表中是W（indows）的下一个字母，寓意“下一代GUI”的意思。目前为止，unix/linux上几乎所有的发行版都采用X window system来作为自己的图形界面，它已经成为事实上的unix/linux图形界面标准。

　　X window system，又名X,X11（现在主要的X window system大都基于其第11个版本），是一个能够跨网络和操作系统平台的图形界面。开发者在开发x时就希望，这个窗口界面不要与硬件有强烈的相关性，这是因为如果与硬件的相关性高，那就等于开发一个操作系统了，如此一来其应用性就要受到限制。故此，x是一套软件体系，而不是操作系统中的组成部分，就像浏览器不是操作系统的组成部分一样（当然微软在与网景干仗时可不管这些）。

X的软件架构
![](https://i.imgur.com/Ka9OAvi.png)

　　如上图所示，X使用服务器-客户端模型：每个需要显示图形界面的主机都要运行一个x server，它负责主机中各个与图形界面有关硬件设备的管理，如在显示器中显示输出，从鼠标、键盘接受输入等。同时与不同的客户端程序（x client）通信，x client指那些需要图形界面的应用程序，如浏览器、终端、视频播放器等。这里需要解释一下，这里的server，client术语是从应用的角度而不是以用户的角度来说的：X server利用自己对硬件的掌控为应用提供显示界面以及处理输入输出等服务，所以说它是服务器端，而各种应用需要使用x server提供的这些服务，他们就是客户端了。

　　x server和x client之间所使用的通信协议对网络来说是透明的，所以client和server可以运行在相同机器上，也可以运行在不同机器上，甚至机器本身的硬件架构和操作系统也可以不一样（如windows上的xmanager就是一个x server，它可以远程连接linux上的主机为linux上的x client提供服务，有兴趣的童鞋可以试下）。

 　　说完了x的软件架构，下面我们就来说说x中其他几个概念。

-------------------

### 1. X Display Manager(XDM)

　　display manager，又称login manager，是linux在boot进程完成之后启动的一个用于用户身份认证的图形界面，相当于文本模式下启动时让我们输入用户名和密码的shell。比较常见的有GNOME的gdm，KDE的kdm等。
![](https://i.imgur.com/Ka9OAvi.png)
　　

　　display manager可以在本地也可以在远程主机上启动。如果在本地启动，display manager接下来会把x server也启动起来，这样就可以在开机时显示如上图所示的图形界面了。

　　如果display manager已经在远程启动了，我们在本地需要连接到远程的display manager上去，这时x server就用使用XDMCP(X Display Manager Control Protocol)协议连接到远程的display manager，请求开启一个会话，这样在本地也就出现了如上图所示画面，如果身份认证通过，display manager就退居二线，剩下的就是本地的x server与远程的x client之间互相交流了：我们通过x server（键盘、鼠标等输入设备）把我们的要求发给x client（x client与display manager 运行在同一台机器上），x client运行得到结果并将结果返回给x server，x server再通过自己管理的输出设备如显示屏等把结果显示出来，如此种种可以参看下图。
![](https://i.imgur.com/Ka9OAvi.png)

　　我们可以发现，在第二种情况下，x server就相当于图形化界面的telnet客户端，而display manager 相当于图形化界面的telnet服务端，不是么？：-）

### 2. GNOME,KDE......

　　GNOME和KDE都是运行在unix/linux操作系统之上的一个桌面环境，其中GNOME是基于GTK+ toolkit的（KDE是基于Qt toolkit的），它个性化了自己的界面风格，非常容易辨认，而且由于界面比较nice，已经是最近桌面系统的主流，它主要包括两大类：
  
　　1.GNOME桌面环境，它包括了一些图形用户界面的实现细则，和一些核心图形界面应用如浏览器等。
　　2.GNOME开发平台，它是一个为linux桌面及移动图形应用开发者提供的扩展平台，包括相应库文件以及开发API等。
  
　　上文所说的GDM就是GNOME的一部分，一旦GDM通过了身份认证请求，它就会启动GNOME为用户提供图形化界面了，当然我们也可以从文本模式中通过startx命令来启动GNOME。
  
 ### 3. GNOME与X的区别与联系
 
　　关于GNOME与X之间的关系，这里有一篇文章介绍的非常清楚，现摘抄一部分如下，我就不画蛇添足了:

    X11 is the window system. Gnome is a desktop environment. The difference is that Gnome works through X11. Gnome and X11 run on Linux, though, not Windows. 

    Basically, X11 is what displays everything. It's the graphical shell that connects you to the operating system. None of the applications you run ever send anything directly to the screen - they need to tell X11 what they want to display and let it do the work for them. 

    X11 draws and moves windows and responds to input from the keyboard and mouse. That's pretty much it. Everything else is taken care of by the desktop environment. What that means is that the look and feel of the windows and the way those windows behave are all decided by which desktop environment you're using (Gnome, KDE, XFCE, etc.). 

    The desktop environments all have their own graphics libraries (reusable chunks of code) that they use, along with APIs (Application Programming Interfaces) which allow programmers to use those libraries when they create GUI applications. This way programmers don't have to recreate commonly used functions and elements from scratch, and the whole operating system has a more standardized look. 


参考链接：

         http://en.wikipedia.org/wiki/X_Window_System

　　　　　http://en.wikipedia.org/wiki/X_display_manager_(program_type)

　　　　　https://wiki.archlinux.org/index.php/Display_Manager

 　　　　 https://wiki.debian.org/DisplayManager

　　　　　http://en.wikipedia.org/wiki/GNOME

　　　　　http://stackoverflow.com/questions/17251293/what-is-the-relationship-between-x11-and-gnome 

