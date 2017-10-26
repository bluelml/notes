- screen滚屏设置  
screen 默认情况下在screen中滚动鼠标滚轮，会回显以前的输入历史，怎么让screen在终端里头能上下滚动，只需要一句话：

```
# 在~/.screenrc（当前用户）  或者/etc/screenrc（全局）添加一句话：
termcapinfo xterm* ti@:te@  
```
