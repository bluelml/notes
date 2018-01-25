* 下载工具
```
git clone https://github.com/raspberrypi/tools
```

* 设置gcc，g++别名
```
cd tools/arm-bcm2708/arm-linux-gnueabihf/bin
ln -s arm-linux-gnueabihf-as as
ln -s arm-linux-gnueabihf-cpp cpp
ln -s arm-linux-gnueabihf-g++ g++
ln -s arm-linux-gnueabihf-gcc gcc
ln -s arm-linux-gnueabihf-ld ld
ln -s arm-linux-gnueabihf-ldd lld
ln -s arm-linux-gnueabihf-nm nm
ln -s arm-linux-gnueabihf-objcopy objcopy
ln -s arm-linux-gnueabihf-objdump objdump
```

* 设置环境变量或者直接指到这个路径就可以用了

* 如果使用cmake，需要修改CMakeLists.txt，添加如下内容
```
set(CMAKE_C_COMPILER /you/path/tools/arm-bcm2708/arm-linux-gnueabihf/bin/gcc)
set(CMAKE_CXX_COMPILER /you/path/tools/arm-bcm2708/arm-linux-gnueabihf/bin/g++)
set(CMAKE_C_FLAGS "-o3 -mfpu=neon")
```
