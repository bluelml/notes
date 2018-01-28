* 针对内存不足，可以通过创建swap内存解决：
```
sudo dd if=/dev/zero of=/4G.swap bs=4M count=1024
sudo mkswap /4G.swap
sudo swapon /4G.swap
```
* 编译完不再使用此swap可以关闭和删除
```
sudo swapoff /4G.swap
sudo rm /4G.swap
```

* raspberry pi 3 上面编译指导：
```
https://github.com/macchina-io/macchina.io/wiki/Getting-Started-With-a-Raspberry-Pi-3-and-the-Bosch-XDK
```
* 在raspberry上面编译运行出现问题解答参考：
```
https://github.com/macchina-io/macchina.io/issues/77
https://github.com/macchina-io/macchina.io/issues/55
```
