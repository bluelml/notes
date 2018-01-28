* 创建交叉编译环境
```
https://github.com/bluelml/notes/blob/master/raspberry%E4%BA%A4%E5%8F%89%E7%BC%96%E8%AF%91%E7%8E%AF%E5%A2%83.md
```

* 修改CMakeLists.txt(dlib目录下，和dlib/example下都有), 指定编译器路径和编译选项
```
设置环境变量：
export CC=/usr/local/bin/gcc
export CXX=/usr/local/bin/g++
export CFLAGS="-o3 -mfpu=neon -fprofile-use -DENABLE_NEON -DARM_NEON_IS_AVAILABLE=ON"
export CXXFLAGS="-o3 -mfpu=neon -fprofile-use -DENABLE_NEON -DARM_NEON_IS_AVAILABLE=ON"


这种方式不起作用：
set(CMAKE_C_COMPILER /home/dev/tools/arm-bcm2708/arm-linux-gnueabihf/bin/gcc)
set(CMAKE_CXX_COMPILER /home/dev/tools/arm-bcm2708/arm-linux-gnueabihf/bin/g++)
set(CMAKE_C_FLAGS "-o3 -mfpu=neon -fprofile-use -DENABLE_NEON -DARM_NEON_IS_AVAILABLE=ON")
```

* dlib目录下运行下面命令进行编译
### Note: 查看dlib/dlib/CMakeLists.txt的402行，发现有个NEON的开关，cmake时加入“-DARM_NEON_IS_AVAILABLE=ON”来打开编译!!

```
cd example
mkdir build
cd build
cmake -DARM_NEON_IS_AVAILABLE=ON ..
cmake --build .
```

* 拷贝目标文件到树莓派上运行就可以了
