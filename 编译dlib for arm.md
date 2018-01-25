* 创建交叉编译环境
```
https://github.com/bluelml/notes/blob/master/raspberry%E4%BA%A4%E5%8F%89%E7%BC%96%E8%AF%91%E7%8E%AF%E5%A2%83.md
```

* 修改CMakeLists.txt, 指定编译器路径和编译选项
```
set(CMAKE_C_COMPILER /home/dev/tools/arm-bcm2708/arm-linux-gnueabihf/bin/gcc)
set(CMAKE_CXX_COMPILER /home/dev/tools/arm-bcm2708/arm-linux-gnueabihf/bin/g++)
set(CMAKE_C_FLAGS "-o3 -mfpu=neon")
```

* dlib目录下运行下面命令进行编译
```
cd example
mkdir build
cd build
cmake ..
cmake --build .
```

* 拷贝目标文件到树莓派上运行就可以了
