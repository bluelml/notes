# 17年的老版本不添加额外的配置编译出来会出现只使用CPU的问题，不确定确定新版本是否有改进

- 修改1:
```
Serving/tensorflow 文件夹下的./configure

并在其中设置CUDA=y，还有cudnn位置版本等等
```

- 修改2:
```
在bazel build -c opt tensorflow_serving/… 这一步的过程中，一定要增加--config=cuda，否则安装的就是CPU版本的serving

bazel build -c opt --config=cuda tensorflow_serving/…
```

## Note: 不支持python 3.x
```
总结： python 2.7  +  ./configure设置CUDA  +  --config=cuda
```
