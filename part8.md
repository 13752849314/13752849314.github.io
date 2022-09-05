# 车辆检测
```python
# 车辆统计

import cv2
import numpy as np


def center(x, y, w, h):
    x1 = int(w / 2)
    y1 = int(h / 2)
    cx = x + x1
    cy = y + y1
    return cx, cy


cap = cv2.VideoCapture(r'../../../video/car.mp4')
bgsubmog = cv2.createBackgroundSubtractorMOG2()
kernel = cv2.getStructuringElement(cv2.MORPH_RECT, (5, 5))
min_w = 90
min_h = 90
cars = []
line_high = 600  # 检测线高度
offset = 3  # 线的偏移量
car_num = 0

while True:
    ret, frame = cap.read()

    if ret:
        # 灰度
        cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
        # print(frame.shape)
        # exit()

        # 去噪(高斯)
        blur = cv2.GaussianBlur(frame, (3, 3), 5)

        # 去背景
        mask = bgsubmog.apply(blur)

        # 腐蚀  去除图中小斑块
        erode = cv2.erode(mask, kernel=kernel)

        # 膨胀  还原放大
        dilate = cv2.dilate(erode, kernel=kernel, iterations=3)

        # 闭运算  去掉物体内部的小块
        close = cv2.morphologyEx(dilate, cv2.MORPH_CLOSE, kernel=kernel, iterations=2)

        # 查找轮廓
        contours, hierarchy = cv2.findContours(close, cv2.RETR_TREE, cv2.CHAIN_APPROX_SIMPLE)

        cv2.line(frame, (10, line_high), (1260, line_high), (255, 255, 0), 3)
        for (i, c) in enumerate(contours):
            (x, y, w, h) = cv2.boundingRect(c)
            isValid = ((w >= min_w) and (h >= min_h))

            if not isValid:
                continue

            cv2.rectangle(frame, (x, y), (x + w, y + h), color=(0, 0, 255), thickness=2)
            # 有效的车
            cp = center(x, y, w, h)
            cars.append(cp)
            cv2.circle(frame, cp, 5, (0, 0, 255), -1)

            for (x, y) in cars:
                # 要有一条线
                # 有范围
                # 从数组中减去
                if (y > line_high - offset) and (y < line_high + offset):
                    car_num += 1
                    cars.remove((x, y))
                    print(car_num)

        cv2.putText(frame, "Cars Count:" + str(car_num), (500, 60), cv2.FONT_HERSHEY_SIMPLEX, 2, (255, 0, 0), 5)
        cv2.imshow('video', frame)
        # cv2.imshow('erode', close)

    key = cv2.waitKey(1)

    if key == 27:
        break

cap.release()
cv2.destroyAllWindows()

```
p85