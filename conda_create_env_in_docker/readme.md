# 在codna官方docker中创建特定pytho版本的env环境

1. 基于Dockerfile.base 创建基础镜像，docker中的shell环境将启动conda env环境，并对应到想要的python版本

2. Dockfile是基于基础镜像，安装需要的python package. 

## 参考
https://pythonspeed.com/articles/activate-conda-dockerfile/
