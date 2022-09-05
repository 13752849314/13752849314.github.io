# 图像滤波
```python
# 图像滤波
import cv2
import numpy as np

img = cv2.imread(r'../../../image/test.png')
cv2.imshow('img', img)
"""
低通滤波可以去除噪声或平滑图像
高通滤波可以帮助查找图像的边缘

图像卷积
api -> filter2D(src, ddepth, kernel, anchor, delta, borderType)
@:param ddepth 位深
@:param kernel 卷积核
@:param anchor 锚点
@:param delta 
@:param borderType 边界类型
"""
kernel = np.ones((5, 5), np.float32) / 25
result = cv2.filter2D(img, ddepth=-1, kernel=kernel)

cv2.imshow('result', result)

"""
1. 方盒滤波 boxFilter(src, ddepth, ksize, anchor, normalize, borderType)
@:param ksize 卷积核大小
K = a * ones(?)
normalize = True, a = 1 / (W * H)  平均滤波 blur(src, ksize, anchor, borderType)
normalize = False, a = 1
"""
result2 = cv2.blur(img, ksize=(5, 5))
cv2.imshow('result2', result2)

"""
2. 高斯滤波
api -> GaussianBlur(img, ksize, sigmaX ,sigmaY, ...)
@:param sigmaX X的延展宽度
@:param sigmaY Y的延展宽度
"""
result3 = cv2.GaussianBlur(img, ksize=(5, 5), sigmaX=1.0)
cv2.imshow('result3', result3)

"""
3. 中值滤波
中值滤波对胡椒噪声效果明显
api -> medianBlur(img, ksize)
"""
result4 = cv2.medianBlur(img, ksize=5)
cv2.imshow('result4', result4)

"""
4. 双边滤波
可以保留边缘，同时可以对边缘的区域进行平滑处理
作用：进行美颜
api -> bilateralFilter(img, d, sigmaColor, sigmaSpace, ...)
@:param d 美化核的大小
@:param sigmaColor 颜色空间方差
@:param sigmaSpace 坐标空间方差
"""
result5 = cv2.bilateralFilter(img, d=7, sigmaColor=20, sigmaSpace=50)
cv2.imshow('result5', result5)

cv2.waitKey(0)

```
# 高通滤波
```python
# 高通滤波
"""
高通滤波最主要的作用是用于检测边缘
Sobel(索贝尔)（高斯），对噪声适应性强，很多算法均以索贝尔卷积核为基础
Scharr(沙尔)， 卷积核不会改变，3*3大小，如果Sobel(索贝尔)的size设为-1，则自动使用的则为沙尔滤波，所以一般情况下均使用索贝尔算法。
对于3*3的卷积核，Sobel(索贝尔)没有Scharr(沙尔)好，因为Scharr(沙尔)可以检测出更细的边缘线。Sobel(索贝尔)比较粗糙

缺点：计算边缘时，只能求一个方向，要么是横轴，要么是纵轴，最后再相加的出最终结果

Laplacian（拉普拉斯），优点不需要单独求x或y的边缘，可以直接将横轴和纵轴的边缘全部检测出来。
缺点：对噪声比较敏感，在其内部没有降噪的功能，因此在使用前，需要进行降噪处理。
"""

import cv2
import numpy as np

img = cv2.imread(r'../../../image/test.png')

"""
1. Sobel算子
api -> Sobel(src, ddepth, dx, dy, ksize=3, scale=1, delte=0, borderType=BORDER_DEFAULT)
"""

sobel1 = cv2.Sobel(img, cv2.CV_64F, 1, 0, ksize=3)
sobel2 = cv2.Sobel(img, cv2.CV_64F, 0, 1, ksize=3)

sobel = cv2.add(sobel1, sobel2)

cv2.imshow('img', img)
# cv2.imshow('sobel1', sobel1)
# cv2.imshow('sobel2', sobel2)
# cv2.imshow('sobel', sobel)

"""
2. Scharr算子
api -> Scharr(src, ddepth, dx, dy, scale=1, delte=0, borderType=BORDER_DEFAULT)
"""

scharr1 = cv2.Scharr(img, cv2.CV_64F, 1, 0)
scharr2 = cv2.Scharr(img, cv2.CV_64F, 0, 1)

# scharr1 = cv2.Scharr(img, -1, 1, 0)
# scharr2 = cv2.Scharr(img, -1, 0, 1)

scharr = cv2.add(scharr1, scharr2)

# cv2.imshow('scharr1', scharr1)
# cv2.imshow('scharr2', scharr2)
# cv2.imshow('scharr', scharr)

"""
3. 拉普拉斯算子
api -> Laplacian(img, ddepth, ksize=1, scal =1, borderType=BORDER_DEFAULT)
"""

lap = cv2.Laplacian(img, -1, ksize=3)
cv2.imshow('lap', lap)
cv2.waitKey(0)

```
# Canny
```python
# 边缘检测 Canny
"""
使用5*5高斯滤波消除噪声
计算图像梯度的方向（0°/45°/90°/135°）共四个方向
在四个方向上取局部最大值
取出最大值后进行阈值计算

api -> Canny(img, threshold1, threshold2, ...)
@:param threshold1 最小阈值
@:param threshold2 最大阈值
"""
import cv2

img = cv2.imread(r'../../../image/test.png')

dst = cv2.Canny(img, 50, 150)

cv2.imshow('img', img)
cv2.imshow('dst', dst)

cv2.waitKey(0)

```