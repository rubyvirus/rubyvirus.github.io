---
title: 记录一次求图像相似度求解过程
tag: tensorflow
---
本人测试一枚，对于APP 浏览器兼容性行业或现有平台都是对页面资源优化、页面加载时间等，对于不同手机、不同屏幕尺寸、不同app browser样式的兼容，没有给出明确的说明，说到这里我们就来求解这个问题。
1. 图像相似度的概念定义涉及内容很多，可以参考如下：http://www.jsjkx.com/jsjkx/ch/reader/create_pdf.aspx?file_no=20160615&year_id=2016&quarter_id=6&falg=1
2. 图像相似度算法（一） 直方图
http://blog.csdn.net/gzlaiyonghao/article/details/2325027
3. 图像相似度算法（二）比如： PSNR、IW-PSNR 可以参考如下：
 - 各种算法定义（英文） https://ece.uwaterloo.ca/~z70wang/research/iwssim/
 - 各种算法定义 (中文) http://www.cnblogs.com/vincent2012/archive/2012/10/13/2723152.html
http://blog.csdn.net/ecnu18918079120/article/details/60149864
 - msssim 实现 https://github.com/tensorflow/models/blob/master/research/compression/image_encoder/msssim.py
 如果是python3运行，请添加tf.gfile.FastGFile打开文件方式，如：tf.gfile.FastGFile(FLAGS.original_image, 'rb')

tensorflow msssim 是目前相似度识别比较高的 脚本如下：
```
#!/usr/bin/python
#
# Copyright 2016 The TensorFlow Authors. All Rights Reserved.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
# ==============================================================================

"""Python implementation of MS-SSIM.
Usage:
python msssim.py --original_image=original.png --compared_image=distorted.png
"""
import numpy as np
from scipy import signal
from scipy.ndimage.filters import convolve
import tensorflow as tf

tf.flags.DEFINE_string('original_image', None, 'Path to PNG image.')
tf.flags.DEFINE_string('compared_image', None, 'Path to PNG image.')
FLAGS = tf.flags.FLAGS


def _FSpecialGauss(size, sigma):
    """Function to mimic the 'fspecial' gaussian MATLAB function."""
    radius = size // 2
    offset = 0.0
    start, stop = -radius, radius + 1
    if size % 2 == 0:
        offset = 0.5
        stop -= 1
    x, y = np.mgrid[offset + start:stop, offset + start:stop]
    assert len(x) == size
    g = np.exp(-((x ** 2 + y ** 2) / (2.0 * sigma ** 2)))
    return g / g.sum()


def _SSIMForMultiScale(img1, img2, max_val=255, filter_size=11,
                       filter_sigma=1.5, k1=0.01, k2=0.03):
    """Return the Structural Similarity Map between `img1` and `img2`.
    This function attempts to match the functionality of ssim_index_new.m by
    Zhou Wang: http://www.cns.nyu.edu/~lcv/ssim/msssim.zip
    Arguments:
      img1: Numpy array holding the first RGB image batch.
      img2: Numpy array holding the second RGB image batch.
      max_val: the dynamic range of the images (i.e., the difference between the
        maximum the and minimum allowed values).
      filter_size: Size of blur kernel to use (will be reduced for small images).
      filter_sigma: Standard deviation for Gaussian blur kernel (will be reduced
        for small images).
      k1: Constant used to maintain stability in the SSIM calculation (0.01 in
        the original paper).
      k2: Constant used to maintain stability in the SSIM calculation (0.03 in
        the original paper).
    Returns:
      Pair containing the mean SSIM and contrast sensitivity between `img1` and
      `img2`.
    Raises:
      RuntimeError: If input images don't have the same shape or don't have four
        dimensions: [batch_size, height, width, depth].
    """
    if img1.shape != img2.shape:
        raise RuntimeError('Input images must have the same shape (%s vs. %s).',
                           img1.shape, img2.shape)
    if img1.ndim != 4:
        raise RuntimeError('Input images must have four dimensions, not %d',
                           img1.ndim)

    img1 = img1.astype(np.float64)
    img2 = img2.astype(np.float64)
    _, height, width, _ = img1.shape

    # Filter size can't be larger than height or width of images.
    size = min(filter_size, height, width)

    # Scale down sigma if a smaller filter size is used.
    sigma = size * filter_sigma / filter_size if filter_size else 0

    if filter_size:
        window = np.reshape(_FSpecialGauss(size, sigma), (1, size, size, 1))
        mu1 = signal.fftconvolve(img1, window, mode='valid')
        mu2 = signal.fftconvolve(img2, window, mode='valid')
        sigma11 = signal.fftconvolve(img1 * img1, window, mode='valid')
        sigma22 = signal.fftconvolve(img2 * img2, window, mode='valid')
        sigma12 = signal.fftconvolve(img1 * img2, window, mode='valid')
    else:
        # Empty blur kernel so no need to convolve.
        mu1, mu2 = img1, img2
        sigma11 = img1 * img1
        sigma22 = img2 * img2
        sigma12 = img1 * img2

    mu11 = mu1 * mu1
    mu22 = mu2 * mu2
    mu12 = mu1 * mu2
    sigma11 -= mu11
    sigma22 -= mu22
    sigma12 -= mu12

    # Calculate intermediate values used by both ssim and cs_map.
    c1 = (k1 * max_val) ** 2
    c2 = (k2 * max_val) ** 2
    v1 = 2.0 * sigma12 + c2
    v2 = sigma11 + sigma22 + c2
    ssim = np.mean((((2.0 * mu12 + c1) * v1) / ((mu11 + mu22 + c1) * v2)))
    cs = np.mean(v1 / v2)
    return ssim, cs


def MultiScaleSSIM(img1, img2, max_val=255, filter_size=11, filter_sigma=1.5,
                   k1=0.01, k2=0.03, weights=None):
    """Return the MS-SSIM score between `img1` and `img2`.
    This function implements Multi-Scale Structural Similarity (MS-SSIM) Image
    Quality Assessment according to Zhou Wang's paper, "Multi-scale structural
    similarity for image quality assessment" (2003).
    Link: https://ece.uwaterloo.ca/~z70wang/publications/msssim.pdf
    Author's MATLAB implementation:
    http://www.cns.nyu.edu/~lcv/ssim/msssim.zip
    Arguments:
      img1: Numpy array holding the first RGB image batch.
      img2: Numpy array holding the second RGB image batch.
      max_val: the dynamic range of the images (i.e., the difference between the
        maximum the and minimum allowed values).
      filter_size: Size of blur kernel to use (will be reduced for small images).
      filter_sigma: Standard deviation for Gaussian blur kernel (will be reduced
        for small images).
      k1: Constant used to maintain stability in the SSIM calculation (0.01 in
        the original paper).
      k2: Constant used to maintain stability in the SSIM calculation (0.03 in
        the original paper).
      weights: List of weights for each level; if none, use five levels and the
        weights from the original paper.
    Returns:
      MS-SSIM score between `img1` and `img2`.
    Raises:
      RuntimeError: If input images don't have the same shape or don't have four
        dimensions: [batch_size, height, width, depth].
    """
    if img1.shape != img2.shape:
        raise RuntimeError('Input images must have the same shape (%s vs. %s).',
                           img1.shape, img2.shape)
    if img1.ndim != 4:
        raise RuntimeError('Input images must have four dimensions, not %d',
                           img1.ndim)

    # Note: default weights don't sum to 1.0 but do match the paper / matlab code.
    weights = np.array(weights if weights else
                       [0.0448, 0.2856, 0.3001, 0.2363, 0.1333])
    levels = weights.size
    downsample_filter = np.ones((1, 2, 2, 1)) / 4.0
    im1, im2 = [x.astype(np.float64) for x in [img1, img2]]
    mssim = np.array([])
    mcs = np.array([])
    for _ in range(levels):
        ssim, cs = _SSIMForMultiScale(
            im1, im2, max_val=max_val, filter_size=filter_size,
            filter_sigma=filter_sigma, k1=k1, k2=k2)
        mssim = np.append(mssim, ssim)
        mcs = np.append(mcs, cs)
        filtered = [convolve(im, downsample_filter, mode='reflect')
                    for im in [im1, im2]]
        im1, im2 = [x[:, ::2, ::2, :] for x in filtered]
    return (np.prod(mcs[0:levels - 1] ** weights[0:levels - 1]) *
            (mssim[levels - 1] ** weights[levels - 1]))


def main(_):
    if FLAGS.original_image is None or FLAGS.compared_image is None:
        print('\nUsage: python msssim.py --original_image=original.png '
              '--compared_image=distorted.png\n\n')
        return

    if not tf.gfile.Exists(FLAGS.original_image):
        print('\nCannot find --original_image.\n')
        return

    if not tf.gfile.Exists(FLAGS.compared_image):
        print('\nCannot find --compared_image.\n')
        return

    with tf.gfile.FastGFile(FLAGS.original_image, 'rb') as image_file:
        img1_str = image_file.read()
    with tf.gfile.FastGFile(FLAGS.compared_image, 'rb') as image_file:
        img2_str = image_file.read()

    input_img = tf.placeholder(tf.string)
    decoded_image = tf.expand_dims(tf.image.decode_png(input_img, channels=3), 0)

    with tf.Session() as sess:
        img1 = sess.run(decoded_image, feed_dict={input_img: img1_str})
        img2 = sess.run(decoded_image, feed_dict={input_img: img2_str})

    print((MultiScaleSSIM(img1, img2, max_val=255)))


if __name__ == '__main__':
    tf.app.run()
```
命令行运行：
python msssim.py --original_image=original.png --compared_image=distorted.png

结构相似对比的基础需要shape相同，如果不同shape图像对比，可以将两张图像转换为同一个尺寸,python 操作库
1. skimage(https://www.jianshu.com/p/f2e88197e81d) transform方法
2. cv2(opencv-python) resize方法

ssim 经典案例： https://www.pyimagesearch.com/2017/06/19/image-difference-with-opencv-and-python/