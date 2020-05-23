# OpenVSLAM 설치부터 카메라출력까지

## INDEX

---

1. Installation
   - Source Code
   - Linux
   - macOS
   - Build instruc
   - Server Setup for Socket Viewer
2. Simple Tutorial
   - Tutorial
   - Sample Datasets
   - Tracking and Mapping
   - Locallization
3. Example
   - SLAM with Video Files
   - SLAM with Image Sequences
   - SLAM with Standard Datasets
   - SLAM with UVC cmaera

---



### Installation

Git Source Code 다운로드 [홈페이지](https://github.com/xdspacelab/openvslam)

```Ubuntu
git clone https://github.com/xdspacelab/openvslam
```

다운받은 OpenVSLAM - master 폴더명을 openvslam으로 변경해준다.



### OpenVSLAM요구사항

```
Eigen : version 3.3.0 or later. #선형대수 계산할때 사용하는 라이브러리
g2o : Please use the latest release. Tested on commit ID 9b41a4e. # 그래프 최적화 라이브러리
SuiteSparse : Required by g2o.
DBoW2 : Please use the custom version of DBoW2 released in https://github.com/shinsumicco/DBoW2. # 이미지 인덱싱
yaml-cpp : version 0.6.0 or later. # 데이터 직렬화 언어
OpenCV : version 3.3.1 or later. # 기본적인 영상처리
```



### Viewer는 2가지로 원하는걸 사용하면 된다.

1. Pangolin Viewer
   - Pangolin : 최신버전 released ID ad8b5f8
   - GLEW 
2. Sockt Viewer
   - [Socket.io-client-cpp](https://github.com/shinsumicco/socket.io-client-cpp)
   - Protobuf : version 3 or later



### 지원하는 OS는 Linux, macOS



## Installing for Linux

##### Tested for Ubuntu 16.04.

##### (18.04.LTS버전 구동 확인)

```
apt update -y
apt upgrade -y --no-install-recommends
# basic dependencies
apt install -y build-essential pkg-config cmake git wget curl unzip
# g2o dependencies
apt install -y libatlas-base-dev libsuitesparse-dev
# OpenCV dependencies
apt install -y libgtk-3-dev
apt install -y ffmpeg
apt install -y libavcodec-dev libavformat-dev libavutil-dev libswscale-dev libavresample-dev
# eigen dependencies
apt install -y gfortran
# other dependencies
apt install -y libyaml-cpp-dev libgoogle-glog-dev libgflags-dev

# (if you plan on using PangolinViewer)
# Pangolin dependencies
apt install -y libglew-dev

# (if you plan on using SocketViewer)
# Protobuf dependencies
apt install -y autogen autoconf libtool
# Node.js
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
apt install -y nodejs
```



##### Download and install Eigen form source.

```
cd /path/to/working/dir
wget -q http://bitbucket.org/eigen/eigen/get/3.3.4.tar.bz2
tar xf 3.3.4.tar.bz2
rm -rf 3.3.4.tar.bz2
cd eigen-eigen-5a0156e40feb
mkdir -p build && cd build
cmake \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_INSTALL_PREFIX=/usr/local \
    ..
make -j4
make install
```



##### Download, bulid and install OpenCV form source.

##### (_OpenCV3.4.0버전은 CUDA와 충돌이 있어 설치 할때 끄거나, 최신버전을 설치해야 한다 OpenCV 4.3.0 추천_)

```
cd /path/to/working/dir
wget -q https://github.com/opencv/opencv/archive/4.3.0.zip
unzip -q 4.3.0.zip
rm -rf 4.3.0.zip
cd opencv-4.3.0
mkdir -p build && cd build
cmake \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_INSTALL_PREFIX=/usr/local \
    -DENABLE_CXX11=ON \
    -DBUILD_DOCS=OFF \
    -DBUILD_EXAMPLES=OFF \
    -DBUILD_JASPER=OFF \
    -DBUILD_OPENEXR=OFF \
    -DBUILD_PERF_TESTS=OFF \
    -DBUILD_TESTS=OFF \
    -DWITH_EIGEN=ON \
    -DWITH_FFMPEG=ON \
    -DWITH_OPENMP=ON \
    ..
make -j4
make install
```

설치가 끝났으면 Common Installation Instructions 이동

---

## Installing for macOS

##### Tested for macOS High Sierra.

##### (macOS Catalina 10.15.4 구동 확인)

Ubuntu 패키지 설치 툴인 apt-get install 대신 mac에서는 Home Brew의 패키지 설치 툴을 사용한다.

##### Install [Home Brew](https://brew.sh/index_ko)

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```

터미널에 붙여넣기 하면 설치 완료

하나씩 설치

```
brew update
# basic dependencies
brew install pkg-config cmake git
# g2o dependencies
brew install suite-sparse
# OpenCV dependencies and OpenCV
brew install eigen
brew install ffmpeg
brew install opencv
# other dependencies
brew install yaml-cpp glog gflags

# (if you plan on using PangolinViewer)
# Pangolin dependencies
brew install glew

# (if you plan on using SocketViewer)
# Protobuf dependencies
brew install automake autoconf libtool
# Node.js
brew install node
```

---



## Common Installation Instrctions

##### Download, build and install the custom DBoW2.

```
cd /path/to/working/dir
git clone https://github.com/shinsumicco/DBoW2.git
cd DBoW2
mkdir build && cd build
cmake \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_INSTALL_PREFIX=/usr/local \
    ..
make -j4
make install
```

##### Download, build and install g2o.

```
cd /path/to/working/dir
git clone https://github.com/RainerKuemmerle/g2o.git
cd g2o
git checkout 9b41a4ea5ade8e1250b9c1b279f3a9c098811b5a
mkdir build && cd build
cmake \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_INSTALL_PREFIX=/usr/local \
    -DCMAKE_CXX_FLAGS=-std=c++11 \
    -DBUILD_SHARED_LIBS=ON \
    -DBUILD_UNITTESTS=OFF \
    -DBUILD_WITH_MARCH_NATIVE=ON \
    -DG2O_USE_CHOLMOD=OFF \
    -DG2O_USE_CSPARSE=ON \
    -DG2O_USE_OPENGL=OFF \
    -DG2O_USE_OPENMP=ON \
    ..
make -j4
make install
```

---

## 본인이 선택한 뷰어에맞게 설치

#### if you plan on using PangolinViewer

##### Download, build and install Pangolin form source.

```
cd /path/to/working/dir
git clone https://github.com/stevenlovegrove/Pangolin.git
cd Pangolin
git checkout ad8b5f83222291c51b4800d5a5873b0e90a0cf81
mkdir build && cd build
cmake \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_INSTALL_PREFIX=/usr/local \
    ..
make -j4
make install
```

#### if you plan on using SocketViewer

##### Download, build and install socket.io-client-cpp form source.

```
cd /path/to/working/dir
git clone https://github.com/shinsumicco/socket.io-client-cpp
cd socket.io-client-cpp
git submodule init
git submodule update
mkdir build && cd build
cmake \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_INSTALL_PREFIX=/usr/local \
    -DBUILD_UNIT_TESTS=OFF \
    ..
make -j4
make install
```

##### Install Protobuf

##### (사용하는 os에 맞게 설치)

```
# for Ubuntu 18.04 (or later)
apt install -y libprotobuf-dev protobuf-compiler
# for macOS
brew install protobuf
```

---

## Build Instruction

#### PangolineViewer

```
cd /path/to/openvslam
mkdir build && cd build
cmake \
    -DBUILD_WITH_MARCH_NATIVE=ON \
    -DUSE_PANGOLIN_VIEWER=ON \
    -DUSE_SOCKET_PUBLISHER=OFF \
    -DUSE_STACK_TRACE_LOGGER=ON \
    -DBOW_FRAMEWORK=DBoW2 \
    -DBUILD_TESTS=ON \
    ..
make -j4
```

### SocketViewer

```
cd /path/to/openvslam
mkdir build && cd build
cmake \
    -DBUILD_WITH_MARCH_NATIVE=ON \
    -DUSE_PANGOLIN_VIEWER=OFF \
    -DUSE_SOCKET_PUBLISHER=ON \
    -DUSE_STACK_TRACE_LOGGER=ON \
    -DBOW_FRAMEWORK=DBoW2 \
    -DBUILD_TESTS=ON \
    ..
make -j4
```

---

## 기본적인 설치가 끝났다.

### 성공적인 설치를 확인하기 위해서 ./run_kitti_salm -h를 실행시켜보자

```
$ ./run_kitti_slam -h
Allowed options:
-h, --help             produce help message
-v, --vocab arg        vocabulary file path
-d, --data-dir arg     directory path which contains dataset
-c, --config arg       config file path
--frame-skip arg (=1)  interval of frame skip
--no-sleep             not wait for next frame in real time
--auto-term            automatically terminate the viewer
--debug                debug mode
--eval-log             store trajectory and tracking times for evaluation
-p, --map-db arg       store a map database at this path after SLAM
```

다음과 같이 출력되면 성공.



## Server Setup for SocketViewer

```
$ cd /path/to/openvslam/viewer
$ ls
Dockerfile  app.js  package.json  public  views
$ npm install
added 88 packages from 60 contributors and audited 204 packages in 2.105s
found 0 vulnerabilities
$ ls
Dockerfile  app.js  node_modules  package-lock.json  package.json  public  views
$ node app.js
WebSocket: listening on *:3000
HTTP server: listening on *:3001
```

npm을 설치하고 app.js를 실행시키면 SocketViewer가 실행된다.

[http://localhost:3001/](http://localhost:3001/) <- 접속해보면 SocketViewer가  다음과 같이 정상적으로 출력되면 성공 👏🏼

![](https://github.com/khg11102/OpenVSLAM/blob/master/%E1%84%80%E1%85%B3%E1%84%85%E1%85%B5%E1%86%B73.png)

---

## Simple Tutorial

### OpenVSLAM에서 제공헤주는 Sample Datasets을 이용해서 프로그램을 실행히켜봅시다.

#### 하나씩 다운받아주세요 

- orb_vocab
- aist_living_lab1
- aist_living_lab2

```
$ pwd #경로확인
/path/to/openvslam/build/ 
$ ls
run_video_slam   run_video_localization   lib/   ...

# download an ORB vocabulary from Google Drive
FILE_ID="1wUPb328th8bUqhOk-i8xllt5mgRW4n84"
curl -sc /tmp/cookie "https://drive.google.com/uc?export=download&id=${FILE_ID}" > /dev/null
CODE="$(awk '/_warning_/ {print $NF}' /tmp/cookie)"
curl -sLb /tmp/cookie "https://drive.google.com/uc?export=download&confirm=${CODE}&id=${FILE_ID}" -o orb_vocab.zip
unzip orb_vocab.zip

# download a sample dataset from Google Drive
FILE_ID="1d8kADKWBptEqTF7jEVhKatBEdN7g0ikY"
curl -sc /tmp/cookie "https://drive.google.com/uc?export=download&id=${FILE_ID}" > /dev/null
CODE="$(awk '/_warning_/ {print $NF}' /tmp/cookie)"
curl -sLb /tmp/cookie "https://drive.google.com/uc?export=download&confirm=${CODE}&id=${FILE_ID}" -o aist_living_lab_1.zip
unzip aist_living_lab_1.zip

# download a sample dataset from Google Drive
FILE_ID="1TVf2D2QvMZPHsFoTb7HNxbXclPoFMGLX"
curl -sc /tmp/cookie "https://drive.google.com/uc?export=download&id=${FILE_ID}" > /dev/null
CODE="$(awk '/_warning_/ {print $NF}' /tmp/cookie)"
curl -sLb /tmp/cookie "https://drive.google.com/uc?export=download&confirm=${CODE}&id=${FILE_ID}" -o aist_living_lab_2.zip
unzip aist_living_lab_2.zip

# run tracking and mapping
./run_video_slam -v ./orb_vocab/orb_vocab.dbow2 -m ./aist_living_lab_1/video.mp4 -c ./aist_living_lab_1/config.yaml --frame-skip 3 --no-sleep --map-db map.msg
# click the [Terminate] button to close the viewer
# you can find map.msg in the current directory

# run localization
./run_video_localization -v ./orb_vocab/orb_vocab.dbow2 -m ./aist_living_lab_2/video.mp4 -c ./aist_living_lab_2/config.yaml --frame-skip 3 --no-sleep --map-db map.msg
```

### 3개의 파일을 다운을받고 압축을 풀었다면

복사해서 붙여넣고 엔터

```
./run_video_slam -v ./orb_vocab/orb_vocab.dbow2 -m ./aist_living_lab_1/video.mp4 -c ./aist_living_lab_1/config.yaml --frame-skip 3 --no-sleep --map-db map.msg
```

_Installation에서 ./run_kitti_slam -h 를 출력하면 스니펫을 확인 할 수 있었죠 방금 실행시킨 명령어는 스니펫을 이용해서 Sample Datasets을 입력해서 맵핑을 한다는 명령어로 해석이 됩니다._

### Sample Datasets의 영상을 맵핑까지 확인해보았습니다.

![](https://github.com/khg11102/OpenVSLAM/blob/master/%E1%84%80%E1%85%B3%E1%84%85%E1%85%B5%E1%86%B72.png)

---

## Example

### SLAM with UVC camera

가지고 있는 USB Webcam 또는 FaceTime HD Camera를 활용해봅시다.

```
$ ./run_camera_slam  -h
Allowed options:
-h, --help            produce help message
-v, --vocab arg       vocabulary file path
-n, --number arg      camera number
-c, --config arg      config file path
--mask arg            mask image path
-s, --scale arg (=1)  scaling ratio of images
-p, --map-db arg      store a map database at this path after SLAM
--debug               debug mode
```

스니펫은 다음과 같이 제공되어 있고, -n 부분에 카메라 숫자를 입력해주면 되겠네요.

이제 카메라 디바이스 숫자를 확인해줘야합니다. Linux와 macOS 디바이스 확인 하는 방법이 다릅니다.

### Ubuntu 18.04 LTS

```
$ ls -ltrh /dev/video*
```

### macOS

```
$ ffmpeg -f avfoundation -list_devices true -i ""
```



### 숫자도 확인했으니 스니펫을 이용해서 출력해봅시다.

```
$ ./run_camera_slam –v ./orb_vocab/orb_vocab.dbow2 –n 1 –c ./aist_living_lab_1/config.yaml –map-db map.msg
```

_-n 뒤에 1은 카메라 숫자입니다 본인 카메라 번호를 입력해주세요._

![](https://github.com/khg11102/OpenVSLAM/blob/master/%E1%84%80%E1%85%B3%E1%84%85%E1%85%B5%E1%86%B71.png)

MacBook Pro - FaceTime HD Camera를 이용해서 출력해 보았습니다.

한가지 아쉬운점은 Mac에는 그래픽카드가 없어 제대로 테스트를 진행하지 못했습니다.
