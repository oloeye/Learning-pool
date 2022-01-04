## 创建手机摄像头
网上查找，发现一款比较好的app.  支持搭建。  那就是 ip-webcam，能访问谷歌商店的用户可以直接在商店内搜索 “ip-webcam” 下载即可，对于无法访问的用户可以到 https://www.appsapk.com/ip-webcam/ 进行下载。

打开后里面大概这样子：

![](https://www.gcssloop.com/assets/gebug/07-internet-ip-webcam/webcam-show.png)

如果是再公网，可以设置一个账号密码，不然很不安全。但是如果只是需要在局域网使用的话，账号密码可以留空
![](https://www.gcssloop.com/assets/gebug/07-internet-ip-webcam/webcam-config1.png)
![](https://www.gcssloop.com/assets/gebug/07-internet-ip-webcam/webcam-config2.png)


在设置完成后，翻到最后面一条，直接点击开启服务器即可

![](https://www.gcssloop.com/assets/gebug/07-internet-ip-webcam/webcam-start.png)

在服务开启后，会在界面上显示视频的连接地址，如下
![](imgs/202201050111102.jpg)  
![](imgs/20220105011110.jpg)  
![](imgs/202201050111101.jpg)  


python 代码获取直播画面
```python
import cv2

url = 'rtsp://admin:cz303771604@192.168.1.8:8080/h264_ulaw.sdp'
cap = cv2.VideoCapture(url)  # 带有摄像头的笔记本用户将url替换为 0 即可

while(cap.isOpened()):
    ret, frame = cap.read()  # frame为一帧图像，当frame为空时，ret返回false，否则为true
    cv2.imshow('frame',frame)
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

cap.release()  # release the capture  
cv2.destroyAllWindows()
```