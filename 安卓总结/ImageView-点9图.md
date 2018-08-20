# 五 ImageView/点9图

## src 属性和 background 属性的区别

ImageView 有两个可以设置图片的属性，分别是：src 和 background**。**background 通常指的都是**背景**, 而 src 指的是**内容**

当使用 **src** 填入图片时, 是按照图片大小**直接填充**, 并**不会进行拉伸**而使用 background 填入图片, 则是会根据 ImageView 给定的宽度来进行**拉伸**

## scaleType 设置缩放类型

android:scaleType 用于设置显示的图片如何缩放或者移动以适应 ImageView 的大小

Java 代码中可以通过 `imageView.setScaleType(ImageView.ScaleType.CENTER);` 来设置

可选值如下：

- **fitXY**: 对图像的横向与纵向进行独立缩放, 使得该图片完全适应 ImageView, 但是图片的横纵比可能会发生改变
- **fitStart**: 保持纵横比缩放图片, 知道较长的边与 Image 的编程相等, 缩放完成后将图片放在 ImageView 的左上角
- **fitCenter**: 同上, 缩放后放于中间;
- **fitEnd**: 同上, 缩放后放于右下角;
- **center**: 保持原图的大小，显示在 ImageView 的中心。当原图的 size 大于 ImageView 的 size，超过部分裁剪处理。
- **centerCrop**: 保持横纵比缩放图片, 知道完全覆盖 ImageView, 可能会出现图片的显示不完全
- **centerInside**: 保持横纵比缩放图片, 直到 ImageView 能够完全地显示图片
- **matrix**: 默认值，不改变原图的大小，从 ImageView 的左上角开始绘制原图， 原图超过 ImageView 的部分作裁剪处理