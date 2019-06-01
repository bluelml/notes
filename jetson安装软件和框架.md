### install pytorch  
```
https://devtalk.nvidia.com/default/topic/1049071/jetson-nano/pytorch-for-jetson-nano/

install by wheel:  
https://devtalk.nvidia.com/default/topic/1041716/jetson-agx-xavier/pytorch-install-problem-solved-/
````  

### install tensorflow
`
https://docs.nvidia.com/deeplearning/frameworks/install-tf-jetsontx2/index.html
`


### install deepstream  
1. 先在Linux host上安装sdkManger(只能linux)  
`
sdkManager教程地址：
https://docs.nvidia.com/sdk-manager/download-run-sdkm/index.html

jetpack下载地址：  
https://developer.nvidia.com/embedded/jetpack
`  

2. 使用sdkmanger安装 JetPack 4.2，包括cuda, tensorrt, cudnn, opencv, gcc等一系列工具
- Note: 可能由于网络问题，会出现安装不成功的情况， 重试几次。 如果重试不成功，那么。。。  

3. 安装deepstram
目前deepstream 3.0版本只支持Jetson AGX Xavier
tx2只能deepstream 1.5版本

`
https://developer.nvidia.com/embedded/deepstream-on-jetson-downloads
`


