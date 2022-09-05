# 图像轮廓
```python
# 轮廓查找

"""
为了检测的准确性，需要先对图像进行二值化或Canny操作
画轮廓时会修改输入的图像
"""
import cv2

img = cv2.imread(r'../../../image/lk.png', cv2.IMREAD_GRAYSCALE)
img1 = cv2.imread(r'../../../image/lk.png')
cv2.imshow('img', img)
print(img.shape)

# 二值化
ret, binary = cv2.threshold(img, 150, 255, cv2.THRESH_BINARY)
cv2.imshow('binary', binary)
binary1 = binary.copy()

"""
api -> findContours(img, mode, method) -> (contours, hierarchy)
@:param mode 
enum {
    RETR_EXTERNAL  = 0, // 表示只检测外轮廓
    RETR_LIST      = 1, // 检测的轮廓不建立等级关系
    RETR_CCOMP     = 2, // 每层最多两级
    RETR_TREE      = 3, // 按树形存储轮廓
}
@:param method
enum {
    CHAIN_APPROX_NONE      = 1, // 保存所有轮廓上的点
    CHAIN_APPROX_SIMPLE    = 2, // 只保存角点
}
return:
@:param contours 轮廓
@:param hierarchy 层级
"""

contours, hierarchy = cv2.findContours(binary, cv2.RETR_TREE, cv2.CHAIN_APPROX_SIMPLE)
print(len(contours))
print(hierarchy)

# 绘制轮廓
"""
api -> drawContours(img, contours, contourIdx, color, thickness=1, ...)
@:param contourIdx -1表示绘制所有轮廓
@:param thickness 线宽，-1是全部填充
"""
cv2.drawContours(img1, contours, contourIdx=1, color=(0, 0, 255), thickness=2)
cv2.imshow('contours', img1)

# 计算轮廓的面积和周长
# 1. 轮廓的面积
area0 = cv2.contourArea(contours[1])

# 2. 轮廓的周长
length0 = cv2.arcLength(contours[1], closed=True)  # closed 是否是闭合的轮廓

print('area=', area0, 'length=', length0)
cv2.waitKey(0)

```
# 多边形逼近与凸包
```python
# 多边形逼近与凸包

import cv2


def draw_shape(src, points):
    for i in range(len(points)):
        if i == len(points) - 1:
            x, y = points[i][0]
            x1, y1 = points[0][0]
            cv2.line(src, (x, y), (x1, y1), (0, 0, 255), 2)
        else:
            x, y = points[i][0]
            x1, y1 = points[i + 1][0]
            cv2.line(src, (x, y), (x1, y1), (0, 0, 255), 2)


img = cv2.imread(r'../../../image/dbx.png')
cv2.imshow('img', img)

# 灰度图
grey = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)

# 二值化
ret, binary = cv2.threshold(grey, 150, 255, cv2.THRESH_BINARY)
cv2.imshow('binary', binary)

# 轮廓查找
contours, hierarchy = cv2.findContours(binary, cv2.RETR_TREE, cv2.CHAIN_APPROX_SIMPLE)

# 绘制轮廓
img1 = img.copy()
cv2.drawContours(img1, contours, -1, (0, 0, 255), 2)

cv2.imshow('contours', img1)

"""
1. 多边形逼近
api -> approxPolyDP(curve, epsilon, closed)
@:param curve 轮廓
@:param epsilon 精度
@:param closed 是否是闭合轮廓
"""
e = 5
approx = cv2.approxPolyDP(contours[0], e, True)
draw_shape(img, approx)

cv2.imshow('approx', img)

"""
2. 凸包
api -> convexHull(points, clockwise=False, returnPoints=True)
@:param points 轮廓
@:param clockwise=True 顺时针
"""
hull = cv2.convexHull(contours[0])
draw_shape(img, hull)
cv2.imshow('hull', img)

cv2.waitKey(0)

```
# 外接矩形
```python
# circumscribed rectangle 外接矩形

import cv2
import numpy as np

img = cv2.imread(r'../../../image/flash.png')
cv2.imshow('img', img)

# 灰度图
grey = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)

# 二值化
ret, binary = cv2.threshold(grey, 150, 255, cv2.THRESH_BINARY)
cv2.imshow('binary', binary)

# 轮廓查找
contours, hierarchy = cv2.findContours(binary, cv2.RETR_TREE, cv2.CHAIN_APPROX_SIMPLE)

# 1. 最小外接矩阵
min_rect = cv2.minAreaRect(contours[0])
print(min_rect)
box = cv2.boxPoints(min_rect)
box = np.int0(box)
cv2.drawContours(img, [box], 0, (0, 0, 255), 2)

# 2. 最大外接矩阵
max_rect = cv2.boundingRect(contours[0])
print(max_rect)
x, y, w, h = max_rect
cv2.rectangle(img, (x, y), (x + w, y + h), (255, 0, 2), 2)

cv2.imshow('min', img)
cv2.waitKey(0)

```