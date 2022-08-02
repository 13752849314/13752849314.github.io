# 创建和显示窗口
```cpp
namedWindow() // 命名窗口
resizeWindow() // 设置窗口大小
imshow() // 展示图片
destroyAllWindows() // 释放所有窗口
```
# 加载和保存图片
```cpp
/*
读取图片
@path: 图片路径 
@flags: 读取方式 
*/
imread(path, flags)

/*
保存图片
@name: image name
@img -> Mat: image
*/
imwrite(name, img)
```

# 视频采集和保存
从当前设备摄像头读取
```cpp
namedWindow("video", WINDOW_NORMAL);

// 获取视频设备
VideoCapture cap = VideoCapture(0);
Mat image;
while (true) {

    bool success = cap.read(image); // cap >> image

    imshow("video", image);

    int key = waitKey(1);

    if (key == (int) ('q')) {
        cout << key << endl;
        break;
    }
}

cap.release();
destroyAllWindows();
```
从视频中读取
```cpp
namedWindow("video1", WINDOW_AUTOSIZE);

VideoCapture cap;
cap.open(R"(D:\github\opencv\video\test.mp4)");
cout << cap.isOpened() << endl;

double fps = cap.get(CAP_PROP_FPS);
cout << "fps=" << fps << endl;
Mat image;

while (true) {
    cap >> image;
    if (image.empty()) {
        break;
    }
    imshow("video1", image);
    int key = waitKey(41);
    if (key == (int) ('q')) {
        break;
    }
}

cap.release();
destroyAllWindows();
```

视频录制
```python
# api: class -> VideoWriter
# methods: write() release()
def save_video(filename, frames, fps, resolution, fourcc='mp4v'):
    """
    保存视频
    :param filename: 文件名
    :param frames: 视频帧
    :param fps: fps
    :param resolution: 分辨率 
    :param fourcc: 
    :return: 
    """
    fcc = cv2.VideoWriter_fourcc(*fourcc)
    vw = cv2.VideoWriter(filename, fcc, fps, resolution)

    for frame in frames:
        vw.write(frame)
    vw.release()
    print("success!")
```
鼠标控制
```python
# setMouseCallback(winname, callback, userdata)
# callback(event, x, y, flags, userdata)

import cv2
import numpy as np


def mouse_callback(event, x, y, flags, userdata):
    """
    鼠标回调函数
    :param event: 事件
    :param x:
    :param y:
    :param flags:
    :param userdata: 
    :return:
    """
    print(event, x, y, flags, userdata)


if __name__ == '__main__':
    cv2.namedWindow('mouse', cv2.WINDOW_NORMAL)
    cv2.resizeWindow('mouse', 640, 360)

    cv2.setMouseCallback('mouse', mouse_callback, '123')
    img = np.zeros((360, 640, 3), dtype=np.uint8)
    while True:
        cv2.imshow('mouse', img)
        key = cv2.waitKey(1)
        if key & 0xFF == ord('q'):
            break

    cv2.destroyAllWindows()

```
TrackBar控件
```python
# createTrackbar
# getTrackbarPos
import cv2
import numpy as np


def callback():
    pass


if __name__ == '__main__':
    cv2.namedWindow('TrackBar', cv2.WINDOW_NORMAL)
    cv2.resizeWindow('TrackBar', 640, 360)

    cv2.createTrackbar('R', 'TrackBar', 0, 255, callback)
    cv2.createTrackbar('G', 'TrackBar', 0, 255, callback)
    cv2.createTrackbar('B', 'TrackBar', 0, 255, callback)

    img = np.zeros((360, 640, 3), dtype=np.uint8)
    while True:
        r = cv2.getTrackbarPos('R', 'TrackBar')
        g = cv2.getTrackbarPos('G', 'TrackBar')
        b = cv2.getTrackbarPos('B', 'TrackBar')

        img[:] = [b, g, r]
        cv2.imshow('TrackBar', img)

        key = cv2.waitKey(1)
        if key & 0xFF == ord('q'):
            break

    cv2.destroyAllWindows()
```
