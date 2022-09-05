# 形态学
## 图像二值化和自适应阈值
```python
# 形态学
import cv2

img = cv2.imread(r'../../../image/test.png')
img1 = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
cv2.imshow('img', img)
cv2.imshow('img1', img1)

"""
1. 图像二值化
api -> threshold(img, thresh, maxval, type)
@:param img 最好为灰度图
@:param thresh 阈值
@:param maxval 超过阈值，替换为 maxval
@:param type enum {
    THRESH_BINARY     = 0, bit
    THRESH_BINARY_INV = 1, bit
    THRESH_TRUNC      = 2,
    THRESH_TOZERO     = 3,
    THRESH_TOZERO_INV = 4,
    THRESH_MASK       = 7,
    THRESH_OTSU       = 8,
    THRESH_TRIANGLE   = 16
}
"""

ret, result1 = cv2.threshold(img1, 180, 255, cv2.THRESH_BINARY)
print(ret)
cv2.imshow('result1', result1)

"""
2. 自适应阈值
api -> adaptiveThreshold(img, maxValue, adaptiveMethod, thresholdType, blockSize, C)
@:param adaptiveMethod 计算阈值的方法
enum {
    ADAPTIVE_THRESH_MEAN_C     = 0, // 计算邻近区域的平均值
    ADAPTIVE_THRESH_GAUSSIAN_C = 1 // 高斯窗口加权平均值
}
@:param blockSize 邻近区域的大小
@:param C 常量，应从计算出的平均值或加权平均值中减去
"""

result2 = cv2.adaptiveThreshold(img1, 255, cv2.ADAPTIVE_THRESH_GAUSSIAN_C, cv2.THRESH_BINARY,
                                3, 0)
cv2.imshow('result2', result2)

cv2.waitKey(0)

```
## 腐蚀
```python
# corrosion 腐蚀

import cv2
import numpy as np

img = cv2.imread(r'../../../image/test.png', cv2.IMREAD_GRAYSCALE)
cv2.imshow('img', img)

"""
api -> erode(img, kernel, iterations=1)
@:param iterations=1 腐蚀的次数
"""

kernel = np.ones((5, 5), dtype=np.uint8)
erode = cv2.erode(img, kernel=kernel, iterations=1)

cv2.imshow('erode', erode)

"""
获取卷积核
api -> getStructuringElement(shape, ksize)
@:param shape 卷积核类型
enum {
    MORPH_RECT    = 0, // 全1
    MORPH_CROSS   = 1, // 交叉
    MORPH_ELLIPSE = 2  // 椭圆
}
"""

kernel1 = cv2.getStructuringElement(cv2.MORPH_CROSS, ksize=(5, 5))
print(kernel1)

erode1 = cv2.erode(img, kernel=kernel1, iterations=1)

cv2.imshow('erode1', erode1)

cv2.waitKey(0)

```
## 膨胀
```python
# swell 膨胀

import cv2
import numpy as np

img = cv2.imread(r'../../../image/test.png', cv2.IMREAD_GRAYSCALE)
cv2.imshow('img', img)

"""
api -> dilate(img, kernel, iterations=1)
"""

kernel = cv2.getStructuringElement(cv2.MORPH_RECT, (3, 3))
swell = cv2.dilate(img, kernel=kernel)

cv2.imshow('swell', swell)

cv2.waitKey(0)

```
## morphologyEx
```python
# open operation 开运算
import cv2
import numpy as np

img = cv2.imread(r'../../../image/h.png', cv2.IMREAD_GRAYSCALE)
cv2.imshow('img', img)

"""
开运算 = 腐蚀 + 膨胀  去除大图形外的小图形
闭运算 = 膨胀 + 腐蚀  去除大图形内的小图形
形态学梯度 = 膨胀 - 腐蚀  求图形的边缘
顶帽运算 = 原图 - 开运算  得到大图形外的小图形
黑帽运算 = 原图 - 闭运算  得到大图形内的小图形
api -> morphologyEx(img, op, kernel)
@:param op 操作类型
enum {
    MORPH_ERODE    = 0, // 腐蚀
    MORPH_DILATE   = 1, // 膨胀
    MORPH_OPEN     = 2, // 开运算
    MORPH_CLOSE    = 3, // 闭运算
    MORPH_GRADIENT = 4, // 形态学梯度
    MORPH_TOPHAT   = 5, // 顶帽运算
    MORPH_BLACKHAT = 6  // 黑帽运算
}
"""

kernel = cv2.getStructuringElement(cv2.MORPH_RECT, (3, 3))
op = cv2.morphologyEx(img, cv2.MORPH_OPEN, kernel=kernel)
cv2.imshow('op', op)

cl = cv2.morphologyEx(img, cv2.MORPH_CLOSE, kernel=kernel)
cv2.imshow('cl', cl)

grad = cv2.morphologyEx(cl, cv2.MORPH_GRADIENT, kernel=kernel)
cv2.imshow('grad', grad)

th = cv2.morphologyEx(img, cv2.MORPH_TOPHAT, kernel=kernel)
cv2.imshow('th', th)

bh = cv2.morphologyEx(img, cv2.MORPH_BLACKHAT, kernel=kernel)
cv2.imshow('bh', bh)

cv2.waitKey()

```