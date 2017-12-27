# qt5.6移植到tiny4412 
一：
  下载qt5.60源代码http://download.qt.io/archive/qt/
  解压后，cd qt-everywhere-opensource-src-5.6.0
  vim qtbase/mkspecs/linux-arm-gnueabi-g++/qmake.conf 
  加入下面这三个代码（march就是架构，arm a9用的是armv7架构）
  QT_QPA_DEFAULT_PLATFORM = linuxfb #eglfs  
  QMAKE_CFLAGS_RELEASE   += -O2 -march=armv7-a    
  QMAKE_CXXFLAGS_RELEASE += -O2 -march=armv7-a
  将以下部分
  # modifications to g++.conf
  QMAKE_CC = arm-linux-gnueabi-gcc
  QMAKE_CXX = arm-linux-gnueabi-g++
  QMAKE_LINK = arm-linux-gnueabi-g++
  QMAKE_LINK_SHLIB = arm-linux-gnueabi-g++

  # modifications to linux.conf
  QMAKE_AR = arm-linux-gnueabi-ar cqs
  QMAKE_OBJCOPY = arm-linux-gnueabi-objcopy
  QMAKE_NM = arm-linux-gnueabi-nm -P
  QMAKE_STRIP = arm-linux-gnueabi-strip
  修改为：-lts 是指在链接时链接 tslib 库
  # modifications to g++.conf
  QMAKE_CC = arm-none-linux-gnueabi-gcc -lts
  QMAKE_CXX = arm-none-linux-gnueabi-g++ -lts
  QMAKE_LINK = arm-none-linux-gnueabi-g++ -lts
  QMAKE_LINK_SHLIB = arm-none-linux-gnueabi-g++ -lts

  # modifications to linux.conf
  QMAKE_AR = arm-none-linux-gnueabi-ar cqs 
  QMAKE_OBJCOPY = arm-none-linux-gnueabi-objcopy 
  QMAKE_NM = arm-none-linux-gnueabi-nm -P
  QMAKE_STRIP = arm-none-linux-gnueabi-strip


