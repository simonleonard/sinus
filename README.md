#!/bin/bash

# create the directories
export WS=$HOME/src/sinus
mkdir -p $WS
cd $WS
mkdir -p src build
cd src

# clone 
git clone https://github.com/simonleonard/sinus.git
cd sinus
git submodule init
git submodule update


# Build open cv 3 with cuda
mkdir -p opencv/build
cd opencv/build
cmake .. -DCMAKE_INSTALL_PREFIX=$WS -DCMAKE_BUILD_TYPE=Release -DBUILD_PERF_TESTS=OFF -DBUILD_TESTS=OFF -DOPENCV_EXTRA_MODULES_PATH=$WS/src/sinus/opencv_contrib/modules
make -j20 install

# Build open MVG
cd ../../openMVG
git submodule update -i
mkdir build
cd build
cmake -DBUILD_SHARED_LIBS=TRUE -DCMAKE_BUILD_TYPE=RELEASE -DEIGEN_INCLUDE_DIR_HINTS=TRUE -DCMAKE_INSTALL_PREFIX=$WS ../src/
make -j20 install

# Build PCL
cd ../../pcl
mkdir build
cd build
cmake -DBUILD_SHARED_LIBS=TRUE -DCMAKE_BUILD_TYPE=RELEASE -DCMAKE_INSTALL_PREFIX=$WS ..
make -j20 install

# Build vlfeat
cd ../../vlfeat
make
find vl -name "*h" -exec cp --parents {} $WS/include \;
cp bin/glnxa64/libvl.so $WS/lib

# build SFM
cd $WS
catkin init
catkin build
