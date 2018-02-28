# 视频转图像
```
ffmpeg -i "你是我的小呀小苹果儿.mp4" -r 1 -q:v 2 -f image2 image-3%d.jpeg
```
- -i选项用来获取输入文件，在这里是视频文件名你是我的小呀小苹果儿.mp4，  
- -r选项设置每秒提取图片的帧数。我想要每秒提取一帧。  
- -q:v 设置提取到的图片质量。设置2来从视频中获取高质量图片。

# rtsp to image
```
ffmpeg -i rtsp://IP_ADDRESS/live.sdp -f image2 -r 1 img%01d.jpg
ffmpeg -i rtsp://192.168.123.13:554/onvif1 -r 1/5 -f image2 -updatefirst 1 img1.jpg
```

# 关键帧提取
```
 ffmpeg -i rtsp://192.168.123.13:554/onvif1 -r 1 -s 160x90 -q:v 2 -vf select='eq(pict_type\,I)' -f image2 image-3%d.jpeg
```

- -vf: 表示过滤图形的描述选择过滤器select会选择帧进行输出：包括过滤器常量pict_type和对应的类型:PICT_TYPE_I 表示是I帧，即关键帧。
- -vsync 2:阻止每个关键帧产生多余的拷贝
- -s 160x90:分辨率

or 

```
ffmpeg -ss <start_time> -i video.mp4 -t <duration> -q:v 2 -vf select="eq(pict_type\,PICT_TYPE_I)" -vsync 0 frame%03d.jpg
```

# 添加水印

左下
```
ffmpeg -i demo-jt-ped.mp4 -strict -2 -vf "movie=logo111.png [watermark]; [in][watermark] overlay=10:main_h-overlay_h-10 [out]" outputvideo.mp4
```
左上
```
ffmpeg -i demo-jt-ped.mp4 -strict -2 -vf "movie=logo111.png [watermark]; [in][watermark] overlay=10:10 [out]" outputvideo.mp4
```
右上
```
ffmpeg -i demo-jt-ped.mp4 -strict -2 -vf "movie=logo111.png [watermark]; [in][watermark] overlay=main_w-overlay_w-10:10 [out]" outputvideo.mp4
```
右下
```
ffmpeg -i demo-jt-ped.mp4 -strict -2 -vf "movie=logo111.png [watermark]; [in][watermark] overlay=main_w-overlay_w-10:main_w-overlay_w-10 [out]" outputvideo.mp4
```
修改水印大小：
```
ffmpeg -y -i movie.mkv -ignore_loop 0 -i movieGif.gif -ss 0 -t 100 -filter_complex '[0:0]scale=iw:ih[firstInput]; [1:0]scale=iw/4:ih/4[secondInput]; [firstInput][secondInput]overlay=0:0:1' movieScaleGif.mp4

```

