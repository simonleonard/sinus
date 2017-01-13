#!/bin/bash

# create the directories
export WS=$HOME/src/sinus </br>
mkdir -p $WS </br>
cd $WS </br>
mkdir -p src build</br>
cd src</br>

# clone 
git clone https://github.com/simonleonard/sinus.git</br>
cd sinus</br>
git submodule init</br>
git submodule update</br>


# Build open cv 3 with cuda
mkdir -p opencv/build</br>
cd opencv/build</br>
cmake .. -DCMAKE_INSTALL_PREFIX=$WS -DCMAKE_BUILD_TYPE=Release -DOPENCL_FOUND=OFF -DBUILD_PERF_TESTS=OFF -DBUILD_TESTS=OFF -DOPENCV_EXTRA_MODULES_PATH=$WS/src/sinus/opencv_contrib/modules</br>
make -j20 install</br>

# Build open MVG
cd ../../openMVG</br>
git submodule update -i</br>
mkdir build</br>
cd build</br>
cmake -DBUILD_SHARED_LIBS=TRUE -DCMAKE_BUILD_TYPE=RELEASE -DEIGEN_INCLUDE_DIR_HINTS=TRUE -DCMAKE_INSTALL_PREFIX=$WS ../src/</br>
make -j20 install</br>

# Build PCL
cd ../../pcl</br>
mkdir build</br>
cd build</br>
cmake -DBUILD_SHARED_LIBS=TRUE -DCMAKE_BUILD_TYPE=RELEASE -DCMAKE_INSTALL_PREFIX=$WS ..</br>
make -j20 install</br>

# Build vlfeat
cd ../../vlfeat</br>
make</br>
find vl -name "*h" -exec cp --parents {} $WS/include \;</br>
cp bin/glnxa64/libvl.so $WS/lib</br>

# build SFM
cd $WS/src/sinus/sfm</br>
git checkout master</br>
cd $WS</br>
catkin init</br>
catkin build -DOpenCV_DIR=$WS/share/OpenCV</br>
