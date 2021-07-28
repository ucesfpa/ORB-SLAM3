# ORB-SLAM3

The original repo is [here](https://github.com/UZ-SLAMLab/ORB_SLAM3).
This repo reports the building process on the Jetson Nano 4gb. The main errors will be highlighted and the respective debugs will be provided.

## Jetson Memory Limitation
ORB-SLAM3 is memory-hungry. To overcome this limitation implement paged memory with **swapfile** ([source](https://github.com/JetsonHacksNano/installSwapfile))
```
$ https://github.com/JetsonHacksNano/installSwapfile
$ cd installSwapfile
$ ./installSwapfile
```

## ORB-SLAM installation
### Environment
Linux Ubuntu 18.04
g++ 7.5.0-3
Python 3.6.9
OpenCV 4.4.1 (Required at least 2.4.3)
Eigen 3.3.4-4 (Required at least 3.1.0)

###Dependencies

**Eigen3**
```
sudo apt-get install libeigen3-dev
```
**Pangolin**
```
sudo apt install libgl1-mesa-dev
sudo apt install pkg-config
sudo apt install libegl1-mesa-dev libwayland-dev libxkbcommon-dev wayland-protocolsall libegl1-mesa-dev libwayland-dev libxkbcommon-dev wayland-protocols
sudo apt install ffmpeg libavcodec-dev libavutil-dev libavformat-dev libswscale-dev libavdevice-dev
sudo apt install libdc1394-22-dev libraw1394-dev
git clone https://github.com/stevenlovegrove/Pangolin.git
cd Pangolin
mkdir build
cd build
cmake ..
cmake --build .
```

for python bindings:
```
sudo pytsudo python -mpip install numpy pyopengl Pillow pybind11
git submodule init && git submodule update
```
**DBoW2**
```
git clone https://github.com/dorian3d/DBoW2.git
cd DBoW2
mkdir build
cd build
cmake .. -DCMAKE_BUILD_TYPE=Release
make
```
**g2o**

```
git clone https://github.com/RainerKuemmerle/g2o.git

mkdir build
cd build
cmake ../
make
```
## Download and build ORB-SLAM3
```
git clone https://github.com/UZ-SLAMLab/ORB_SLAM3.git ORB_SLAM3
cd ORB_SLAM3
chmod +x build.sh
./build.sh
```
The building process might result in the following error:
```
/ORB_SLAM3/src/LocalMapping.cc:628:49: error: no match for ‘operator/’ (operand types are ‘cv::Matx<float, 3, 1>’ and ‘float’) x3D = x3D_h.get_minor<3,1>(0,0) / x3D_h(3);
```
if this happen modify the file **LocalMapping.cc** in the src directory using 
```nano -c src/LocalMapping.cc``` and scroll to the line 628 and replace
```
x3D = x3D_h.get_minor<3,1>(0,0) / x3D_h(3);
```
with
```
x3D = x3D_h.get_minor<3,1>(0,0) / x3D_h(3));
```
Also, modify the file **KannalaBrandt8.cpp** using ```nano -c src/CameraModels/*KannalaBrandt8.cpp```

and replace line 534
```
x3D = x3D_h.get_minor<3,1>(0,0) / x3D_h(3);
```
with
```
x3D = x3D_h.get_minor<3,1>(0,0) (1/x3D_h(3));
```
Once done, try to build again.
