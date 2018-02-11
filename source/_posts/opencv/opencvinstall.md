---
title: opencv安装(debian)
tag: opencv
---
opencv流行已久，下面来讲一波debian上安装说明
## 从debian仓库安装依赖
```
sudo apt-get install libopencv-dev python-opencv
```
## 通过以下脚本批量操作
```
# 1. KEEP UBUNTU OR DEBIAN UP TO DATE

sudo apt-get -y update
sudo apt-get -y upgrade
sudo apt-get -y dist-upgrade
sudo apt-get -y autoremove


# 2. INSTALL THE DEPENDENCIES

# Build tools:
sudo apt-get install -y build-essential cmake

# GUI (if you want to use GTK instead of Qt, replace 'qt5-default' with 'libgtkglext1-dev' and remove '-DWITH_QT=ON' option in CMake):
sudo apt-get install -y qt5-default libvtk6-dev

# Media I/O:
sudo apt-get install -y zlib1g-dev libjpeg-dev libwebp-dev libpng-dev libtiff5-dev libjasper-dev libopenexr-dev libgdal-dev

# Video I/O:
sudo apt-get install -y libdc1394-22-dev libavcodec-dev libavformat-dev libswscale-dev libtheora-dev libvorbis-dev libxvidcore-dev libx264-dev yasm libopencore-amrnb-dev libopencore-amrwb-dev libv4l-dev libxine2-dev

# Parallelism and linear algebra libraries:
sudo apt-get install -y libtbb-dev libeigen3-dev

# Python:
sudo apt-get install -y python-dev python-tk python-numpy python3-dev python3-tk python3-numpy

# Java:
# sudo apt-get install -y ant default-jdk

# Documentation:
sudo apt-get install -y doxygen


# 3. INSTALL THE LIBRARY (YOU CAN CHANGE '3.2.0' FOR THE LAST STABLE VERSION)

sudo apt-get install -y unzip wget
wget https://github.com/opencv/opencv/archive/3.2.0.zip
unzip 3.2.0.zip
rm 3.2.0.zip
mv opencv-3.2.0 OpenCV
cd OpenCV
mkdir build
cd build
cmake -DWITH_QT=ON -DWITH_OPENGL=ON -DFORCE_VTK=ON -DWITH_TBB=ON -DWITH_GDAL=ON -DWITH_XINE=ON -DBUILD_EXAMPLES=ON -DENABLE_PRECOMPILED_HEADERS=OFF ..
make -j4
sudo make install
sudo ldconfig

```
1. 如已安装上面部分内容，可直接注释，如本地安装了jdk，就直接#
2. 安装的opencv版本可自行修改
3. 下载安装内容比较多，时间很长

---
安装过程可能提示：Add the installation prefix of "Qt5Concurrent" to CMAKE_PREFIX_PATH or set   "Qt5Concurrent_DIR" to a directory containing one of the above files.  If   "Qt5Concurrent" provides a separate development package or SDK, be sure it   has been installed

可以参见这篇文件。https://stackoverflow.com/questions/24378473/ubuntu-opencv-install-and-setup-qt5/27406016#27406016

## 检查安装是否成功
参考：
http://wiki.opencv.org.cn/index.php/Debian%E4%B8%8B%E5%AE%89%E8%A3%85

其它说明：https://www.pyimagesearch.com/opencv-tutorials-resources-guides/
