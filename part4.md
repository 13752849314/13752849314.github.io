# 图像运算
## 图像加减乘除
```python
import cv2
import numpy as np

img = cv2.imread(r'../../../image/test.png')

# print(img.shape)

img1 = np.ones((800, 1280, 3), np.uint8) * 50

cv2.imshow('orig', img)
result = cv2.add(img, img1)  # +
result1 = cv2.subtract(img, img1)  # -
result2 = cv2.multiply(img, img1)  # *
result3 = cv2.divide(img, img1)  # /

cv2.imshow('add_result', result)
cv2.imshow('sub_result', result1)
cv2.imshow('mut_result', result2)
cv2.imshow('div_result', result3)
cv2.waitKey(0)

```
## 图像的溶合
```python
# 图像的溶合
import cv2
import numpy as np

"""
api -> addWeighted(A, alpha, B, beta, gamma)
alpha, beta 为权重
gamma 静态权重
"""

img = cv2.imread(r'../../../image/test.png')
# print(img.shape)
back = np.zeros(img.shape, np.uint8)

result = cv2.addWeighted(img, 0.7, back, 0.3, 0)
cv2.imshow('add2', result)
cv2.waitKey(0)

```
## 图像的位运算
```python
# 图像的位运算
import cv2
import numpy as np

"""
1. 非运算
api -> bitwise_not(img)
"""

img = cv2.imread(r'../../../image/test.png')

result1 = cv2.bitwise_not(img)

cv2.imshow('result1', result1)

"""
2. 与运算
api -> bitwise_and(img1, img2)
"""
img2 = np.zeros(img.shape, np.uint8)
img2[50:500, 50:500] = 255
cv2.imshow('img2', img2)
result2 = cv2.bitwise_and(img, img2)
cv2.imshow('result2', result2)

"""
3. 或运算与异或运算
api -> bitwise_or(img1, img2)
api -> bitwise_xor(img1, img2)
"""
result3 = cv2.bitwise_or(img, img2)
result4 = cv2.bitwise_or(img, img2)
cv2.imshow('result3', result3)
cv2.imshow('result4', result4)
cv2.waitKey(0)

```
# 图像变换
```python
# 图像变换
import cv2
import numpy as np

img = cv2.imread(r'../../../image/test.png')
size = img.shape
"""
1. 图像的缩放
api -> resize(src, dsize, dst, fx, fy, interpolation)
@:param dsize: 目标尺寸
@:param fx: x轴的缩放因子
@:param fy: y轴的缩放因子
@:param interpolation: 插值算法
INTER_NEAREST(临近插值，速度快，效果差)
INTER_LINEAR(双线性插值，原图中的4个点)
INTER_CUBIC(三次插值，16个点)
INTER_AREA(效果最好)
"""

result1 = cv2.resize(img, (size[1] // 2, size[0] // 2), interpolation=cv2.INTER_AREA)

cv2.imshow('img', img)
cv2.imshow('result1', result1)

"""
2. 图像翻转
api -> flip(img, flipCode)
@:param flipCode
==0 上下
> 0 左右
< 0 上下+左右
"""
result2 = cv2.flip(img, -1)
cv2.imshow('result2', result2)


"""
3. 图像旋转
api -> rotate(img, rotateCode)
@:param rotateCode
ROTATE_90_CLOCKWISE
ROTATE_180
ROTATE_90_COUNTERCLOCKWISE 270
"""
result3 = cv2.rotate(img, cv2.ROTATE_90_CLOCKWISE)
cv2.imshow('result3', result3)

"""
4. 仿射变换
api -> warpAffine(src, M, dsize, flags, mode, value)
@:param M 变换矩阵
@:param dsize 输出尺寸
@:param flags 与resize中的插值算法一致
@:param mode 边界外推法标志
@:param value 填充边界的值
"""

# 平移矩阵
#                       右           下
M = np.float32([[1, 0, 100], [0, 1, 100]])
result4 = cv2.warpAffine(img, M, (size[1], size[0]))
cv2.imshow('result4', result4)

# 变换矩阵
"""
api -> getRotationMatrix2D(center, angle, scale)
@:param center 中心点
@:param angle 角度 逆时针
@:param scale 缩放比例
"""
M1 = cv2.getRotationMatrix2D((size[1] / 2, size[0] / 2), 15, 0.3)
result5 = cv2.warpAffine(img, M1, (size[1], size[0]))
cv2.imshow('result5', result5)

src = np.float32([[400, 300], [800, 300], [400, 1000]])
dst = np.float32([[200, 400], [600, 500], [150, 1100]])
M2 = cv2.getAffineTransform(src, dst)
result6 = cv2.warpAffine(img, M2, (size[1], size[0]))
cv2.imshow('result6', result6)

cv2.waitKey(0)

```
# 透视变换
```python
# 透视变换

import cv2
import numpy as np

src1 = []
index = 0


def mouse_callback(event, x, y, flags, userdata):
    global index
    if event & cv2.EVENT_LBUTTONDOWN == cv2.EVENT_LBUTTONDOWN:
        src1.append([x, y])
        index += 1
        print('index=', index, (x, y))


"""
api -> warpPerspective(img, M, dsize, ...)
M -> getPerspectiveTransform(src, dst)
"""

img = cv2.imread(r'../../../image/perspective.jpg')
cv2.namedWindow('per')
cv2.setMouseCallback('per', mouse_callback)
while True:
    cv2.imshow('per', img)
    key = cv2.waitKey(5) & 0xFF
    if key == ord('q') or index == 4:
        cv2.destroyWindow('per')
        break
print(src1)
src = np.float32(src1)
dst = np.float32([[0, 0], [400, 0], [0, 400], [400, 400]])
M = cv2.getPerspectiveTransform(src, dst)
result = cv2.warpPerspective(img, M, (400, 400))
cv2.imshow('img', img)
cv2.imshow('result', result)

cv2.waitKey(0)

```