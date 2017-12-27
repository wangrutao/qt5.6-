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


