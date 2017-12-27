# qt5.6移植到tiny4412 
一：
  下载qt5.60源代码http://download.qt.io/archive/qt/ <br/>
  解压后，cd qt-everywhere-opensource-src-5.6.0 <br/>
  vim qtbase/mkspecs/linux-arm-gnueabi-g++/qmake.conf <br/>
  加入下面这三个代码（march就是架构，arm a9用的是armv7架构）<br/>
  QT_QPA_DEFAULT_PLATFORM = linuxfb #eglfs  <br/>
  QMAKE_CFLAGS_RELEASE   += -O2 -march=armv7-a    <br/>
  QMAKE_CXXFLAGS_RELEASE += -O2 -march=armv7-a <br/>
  
  将以下部分<br/>
  /# modifications to g++.conf<br/>
  QMAKE_CC = arm-linux-gnueabi-gcc<br/>
  QMAKE_CXX = arm-linux-gnueabi-g++<br/>
  QMAKE_LINK = arm-linux-gnueabi-g++<br/>
  QMAKE_LINK_SHLIB = arm-linux-gnueabi-g++<br/>
  
  /# modifications to linux.conf<br/>
  QMAKE_AR = arm-linux-gnueabi-ar cqs<br/>
  QMAKE_OBJCOPY = arm-linux-gnueabi-objcopy<br/>
  QMAKE_NM = arm-linux-gnueabi-nm -P<br/>
  QMAKE_STRIP = arm-linux-gnueabi-strip<br/>
  <br/>
  修改为：-lts 是指在链接时链接 tslib 库<br/>
  /# modifications to g++.conf<br/>
  QMAKE_CC = arm-none-linux-gnueabi-gcc -lts<br/>
  QMAKE_CXX = arm-none-linux-gnueabi-g++ -lts<br/>
  QMAKE_LINK = arm-none-linux-gnueabi-g++ -lts<br/>
  QMAKE_LINK_SHLIB = arm-none-linux-gnueabi-g++ -lts<br/>
  
  /# modifications to linux.conf<br/>
  QMAKE_AR = arm-none-linux-gnueabi-ar cqs <br/>
  QMAKE_OBJCOPY = arm-none-linux-gnueabi-objcopy <br/>
  QMAKE_NM = arm-none-linux-gnueabi-nm -P<br/>
  QMAKE_STRIP = arm-none-linux-gnueabi-strip<br/>
二：<br/>
 删除不用的模块，如果不删除会出现错误，或者配置的时候取消掉这个模块<br/>
 rm qt3d qtcanvas3d qtweb* -rf<br/>
 然后配置<br/>
 ./configure -release -opensource -xplatform linux-arm-gnueabi-g++ -prefix /usr/local/Qt5.6 -no-c++11 -no-opengl -no-dbus -tslib -I /usr/local/tslib/include -L /usr/local/tslib/lib<br/>
 make -j8<br/>
 make install<br/>
三：<br/>
 将移植好的 qt 打包到开发板<br/>
  cd /usr/local<br/><br/>
  tar -cvf Qt5.6.tar.gz Qt5.6<br/>
  cp Qt5.6.tar.gz /work/my_rootfs/usr/local/<br/>
  tar -xvf Qt5.6.tar.gz<br/>
  rm Qt5.6.tar.gz <br/>
  rm -r doc include bin mkspecs qml translations<br/>
  
  设置qt相关的环境变量<br/>
  此部分可以参考qt官方问文档：http://doc.qt.io/qt-5/embedded-linux.html ，这我这仅仅是设置支持了触摸屏，你可以参考官方设置支持键盘，鼠标等等。<br/>
  在文件系统 /etc/profile 里添加<br/>
  export QTEDIR=/usr/local/Qt5.6<br/>
  export LD_LIBRARY_PATH=/usr/local/Qt5.6/lib:$LD_LIBRARY_PATH<br/>
  export QT_QPA_GENERIC_PLUGINS=tslib<br/>
  export QT_QPA_FB_TSLIB=1<br/>
  export QT_QPA_EVDEV_MOUSE_PARAMETERS=/dev/input/event0 #加入鼠标指针在屏幕上，方便调试<br/>
  export QT_QPA_FONTDIR=$QTEDIR/lib/fonts #字体库<br/>
  export QT_QPA_PLATFORM_PLUGIN_PATH=$QTEDIR/plugins <br/>
  export QT_QPA_PLATFORM=linuxfb:fb=/dev/fb0:size=800x1280:mmSize=800x1280:offset=0x0:tty=/dev/tty1<br/>
  <br/>
  QT程序在运行时需要依赖libiconv库。<br/>
  更新libiconv库，到iconv官网下载最新的库源码包，下载地址为:<br/>
  http://ftp.gnu.org/gnu/libiconv/libiconv-1.14.tar.gz<br/>
  编译步骤：<br/>
  在libiconv目录下：<br/>
  ./configure --prefix=$PWD/temp --host=arm-linux<br/>
  make<br/>
  make install<br/>
  上述命令执行完成后会在libiconv目录下生成新的temp目录，该目录下存在4个目录分别是：<br/>
  bin  include  lib  share<br/>
  在lib目录下为生成的库文件，其中一个为preloadable_libiconv.so，把它下载到开发板的lib目录下，然后再设置开发板的系统环境变量即可：<br/>
  export LD_PRELOAD=/lib/preloadable_libiconv.so<br/>
四：<br/>
  拷贝QT依赖库<br/>
  QT运行时需要用到ibjpeg.so.8、libpng14.so.14 等一系列库。这些库在交叉编译器目录下已经编译好，直接拷贝到开发板的lib目录下即可。<br/>
  库存放的路径：<br/>
  /a_linux_station/arm-linux-gcc/opt/FriendlyARM/toolschain/4.5.1/arm-none-linux-gnueabi/sys-root/usr/lib<br/>
<br/>
  如果想让QT显示中文，可以将windows下的中文字体拷贝到QT的字体库目录下<br/>
  字库位置<br/>
  /usr/local/Qt5.6/lib/fonts<br/>
五：<br/>
  编译qt程序<br/>
  进入程序文件夹后，/usr/local/Qt5.6/bin/qmake生成Makefile<br/>
  vim Makefile LIBS = .........后面加-L /usr/local/tslib/lib -lts<br/>
  make<br/>
  将编译好的程序放入挂载的文件系统<br/>
  ./app<br/>
  
ps:如果tslib没反应，尝试在export LD_PRELOAD=/lib/preloadable_libiconv.so后面加上:/usr/local/tslib/lib/libts.so<br/>

  

  

