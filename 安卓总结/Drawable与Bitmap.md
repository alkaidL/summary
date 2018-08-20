# 二十四 Drawable与Bitmap

## Drawable的分类、属性及使用方式

### ColorDrawable

##### 自带colors.xml文件

```xml
<resources>
    <color name="colorPrimary">#3F51B5</color
</resources>
```

##### 静态方法argb来设置颜色

Android使用一个int类型的数据表示颜色值,通常是十六进制,即0x开头， 颜色值的定义是由透明度alpha和RGB(红绿蓝)三原色来定义的,以"#"开始,后面依次为:  **透明度-红-绿-蓝**;eg:#RGB #ARGB #RRGGBB #AARRGGBB  每个要素都由一个字节(8 bit)来表示,所以取值范围为0~255,在xml中设置颜色可以忽略透明度, 但是如果是在Java代码中的话就需要明确指出透明度的值了,省略的话表示完全透明

示例:(参数依次为:透明度,红色值,绿色值,蓝色值) **txtShow.setBackgroundColor(Color.argb(0xff, 0x00, 0x00, 0x00));**

##### 在Java代码中定义ColorDrawable

```java
ColorDrawable drawable = new ColorDrawable(0xffff2200);  
txtShow.setBackground(drawable);  
```

##### 在xml中定义ColorDrawable

```xml
<?xml version="1.0" encoding="utf-8"?>  
<color  
    xmlns:android="http://schemas.android.com/apk/res/android"  
    android:color="#FF0000"/>
```

### NiewPatchDrawable

点9图不能放在mipmap目录下，而需要放在drawable目录下

##### xml定义NinePatchDrawable

```xml
<!--参数依次为:引用的.9图片,是否对位图进行抖动处理-->  
<?xml version="1.0" encoding="utf-8"?>  
<nine-patch  
    xmlns:android="http://schemas.android.com/apk/res/android"  
    android:src="@drawable/dule_pic"  
    android:dither="true"/>  
```

##### 使用Bitmap包装.9图片

`<nine-patch` 换为`<bitmap`即可

### ShapeDrawable

定义基本的几何图形,如(矩形,圆形,线条等)，属性如下：

-  <**shape**>:

  ~ **visible**:设置是否可见

  ~ **shape**:形状,可选:rectangle(矩形,包括正方形),oval(椭圆,包括圆),line(线段),ring(环形)

  ~ **innerRadiusRatio**:当shape为ring才有效,表示环内半径所占半径的比率,如果设置了innerRadius, 会被忽略

  ~ **innerRadius**:当shape为ring才有效,表示环的内半径的尺寸

  ~ **thicknessRatio**:当shape为ring才有效,表示环的厚度占半径的比率

  ~ **thickness**:当shape为ring才有效,表示环的厚度,即外半径与内半径的差

  ~ **useLevel**:当shape为ring才有效,表示是否允许根据level来显示环的一部分

- <**size**>:

  ~ **width**:图形形状宽度

  ~ **height**:图形形状高度

  ~ **color**:背景填充色,设置solid后会覆盖gradient设置的所有效果

- <**stroke**>

  ~ **width**:边框的宽度

  ~ **color**:边框的颜色

  ~ **dashWidth**:边框虚线段的长度

  ~ **dashGap**:边框的虚线段的间距

- <**conner**>

  ~ **radius**:圆角半径,适用于上下左右四个角

  ~ **topLeftRadius**,**topRightRadius**,**BottomLeftRadius**,**tBottomRightRadius**: 依次是左上,右上,左下,右下的圆角值,按自己需要设置!

- <**padding**>

  left,top,right,bottm:依次是左上右下方向上的边距

### GradientDrawable

一个具有渐变区域的Drawable，可以实现线性渐变,发散渐变和平铺渐变效果，属性如下：

- **startColor**:渐变的起始颜色
- **centerColor**:渐变的中间颜色
- **endColor**:渐变的结束颜色
- **type**:渐变类型,可选(**linear**,**radial**,**sweep**), **线性渐变**(可设置渐变角度),发散渐变(中间向四周发散),平铺渐变
- **centerX**:渐变中间颜色的x坐标,取值范围为:0~1
- **centerY**:渐变中间颜色的Y坐标,取值范围为:0~1
- **angle**:只有linear类型的渐变才有效,表示渐变角度,必须为45的倍数
- **gradientRadius**:只有radial和sweep类型的渐变才有效,radial必须设置,表示渐变效果的半径
- **useLevel**:判断是否根据level绘制渐变效果

### BitmapDrawable

对Bitmap的一种封装,可以设置它包装的bitmap在BitmapDrawable区域中的绘制方式,有: 平铺填充,拉伸填充或保持图片原始大小。根节点<**bitmap**> 。属性如下：

- **android:src**：这个比较简单就是图片的资源 ID（必填）
- **android:antialias**：是否开启抗锯齿功能，（建议开启）
- **android:dither**：是否允许开启抖动效果（建议开启）。当位图与屏幕的像素配置不同时，开启这个选项可以让高质量的图片在低质量的屏幕上还能较好的显示效果。（例如：一个位图的像素设置是 ARGB 8888，但屏幕的设置是RGB 565，如果不开启抖动效果，图片显示就会失真）
- **android:filter**表示：是否允许对位图进行滤波（建议开启）。对位图进行收缩或者延展使用滤波可以获得平滑的外观效果
- **android:gravity**表示：如果位图小于其容器时，设置位图显示的位置（此选项可以同“ | ”来组合使用）
- **android:tileMode**表示：平铺模式。开启该功能以后 android:gravity 属性会失效。
  - disabled ：表示平铺模式不可用 ( 默认值 )
  - clamp ：表示位图周围的像素会扩展到周围区域
  - repeat ：表示水平和竖直方向上的铺平效果
  - mirror ：表示水平和竖直方向上的镜面投影效果

### InsetDrawable

InsetDrawable对应标签\<inset>，它可以将其他Drawable内嵌到自己当中，并可以在四周预留出一定的间距。 当我们希望View的背景比实际区域小时，就可以采用InsetDrawable来实现。 layerDrawable也是可以达到相同的预期效果的。 

- `android:drawable`Drawable 资源 。必须的。引用一个drawable资源
- `android:insetTop`与顶部的距离。可以是一个尺寸值，或者一个尺寸的资源。
- `android:insetRight`与右边的距离。可以是一个尺寸值，或者一个尺寸的资源。
- `android:insetBottom`与底部的距离。可以是一个尺寸值，或者一个尺寸的资源。
- `android:insetLeft`与左边的距离。可以是一个尺寸值，或者一个尺寸的资源。

### ClipDrawable

可以把ClipDrawable理解为从位图上剪下一个部分。Android中的进度条就是使用ClipDrawable来实现的,根据设置level的值来决定剪切区域的大小,根节点是<**clip**>。属性如下：

- **clipOrietntion**:设置剪切的方向,可以设置水平和竖直2个方向
- **gravity**:从那个位置开始裁剪
- **drawable**:引用的drawable资源,为空的话需要有一个Drawable类型的子节点

```xml
<clip xmlns:android="http://schemas.android.com/apk/res/android"
    android:clipOrientation="horizontal"
    android:drawable="@mipmap/ic_bg_meizi"
    android:gravity="left" /> 
```

### RotateDrawable

用来对Drawable进行旋转,也是通过setLevel来控制旋转的,最大值是10000，属性如下：

- **fromDegrees**:起始的角度,对应最低的level值,默认为0
- **toDegrees**:结束角度,对应最高的level值,默认360
- **pivotX**:设置参照点的x坐标,取值为0~1,默认是50%,即0.5
- **pivotY**:设置参照点的Y坐标,取值为0~1,默认是50%,即0.5。如果出现旋转图片显示不完全的话可以修改上述两个值解决
- **drawable**:设置位图资源
- **visible**:设置drawable是否可见

角度图如下：

![img](http://www.runoob.com/wp-content/uploads/2015/10/70168328.jpg) 

```xml
<rotate xmlns:android="http://schemas.android.com/apk/res/android"
    android:drawable="@mipmap/ic_launcher"
    android:fromDegrees="-180"
    android:pivotX="50%"
    android:pivotY="50%" />  
```

### AnimationDrawable

AnimationDrawable是用来实现Android中帧动画的，根节点<**animation-list**>，属性如下：

- **oneshot**:设置是否循环播放,false为循环播放
- **duration**:帧间隔时间,通常我们会设置为300毫秒

我们获得AniamtionDrawable实例后，需要调用它的start()方法播放动画，另外要注意在OnCreate()方法中调用的话,是没有任何效果的,因为View还没完成初始化,可以用简单的handler来延迟播放动画

### LayerDrawable

层图形对象，包含一个Drawable数组，然后按照数组对应的顺序来绘制他们，索引值最大的Drawable会被绘制在最上层。虽然这些Drawable会有交叉或者重叠的区域，但他们位于不同的层，所以并不会相互影响，以<**layer-list**>作为根节点。属性如下：

- **drawable**:引用的位图资源,如果为空需有一个Drawable类型的子节点
- **left**:层相对于容器的左边距
- **right**:层相对于容器的右边距
- **top**:层相对于容器的上边距
- **bottom**:层相对于容器的下边距
- **id**:层的id

```xml
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <item>
        <bitmap
            android:gravity="center"
            android:src="@mipmap/ic_bg_ciwei" />
    </item>
    <item
        android:left="25dp"
        android:top="25dp">
        <bitmap
            android:gravity="center"
            android:src="@mipmap/ic_bg_ciwei" />
    </item>
    <item
        android:left="50dp"
        android:top="50dp">
        <bitmap
            android:gravity="center"
            android:src="@mipmap/ic_bg_ciwei" />
    </item>
</layer-list> 
```

### TransitionDrawable

LayerDrawable的一个子类，TransitionDrawable只管理两层Drawable，并且提供了透明度变化的动画，可以控制一层Drawable过渡到另一层Drawable的动画效果。 根节点为<**transition**>

 调用`startTransition`方法才能启动两层间的切换动画； 也可以调用`reverseTransition()`方法反过来播放

```xml
<transition xmlns:android="http://schemas.android.com/apk/res/android" >
    <item android:drawable="@mipmap/ic_bg_meizi1"/>
    <item android:drawable="@mipmap/ic_bg_meizi2"/>
</transition>
```

可实现多张图片循环的淡入淡出的效果。核心原理：handler定时修改Transition中两个图片

### LevelListDrawable

用来管理一组Drawable的,我们可以为里面的drawable设置不同的level， 当绘制的时候，会根据level属性值获取对应的drawable绘制到画布上，根节点:<**level-list**>。只能设置每个<**item**> 的属性。属性如下：

- **drawable**:引用的位图资源,如果为空徐璈有一个Drawable类型的子节点
- **minlevel:level**对应的最小值
- **maxlevel:level**对应的最大值

```xml
<level-list xmlns:android="http://schemas.android.com/apk/res/android" >
    <item android:drawable="@drawable/shape_cir1" android:maxLevel="2000"/>
    <item android:drawable="@drawable/shape_cir2" android:maxLevel="4000"/>
    <item android:drawable="@drawable/shape_cir3" android:maxLevel="6000"/>
    <item android:drawable="@drawable/shape_cir4" android:maxLevel="8000"/>
    <item android:drawable="@drawable/shape_cir5" android:maxLevel="10000"/>
</level-list> 
```

### StateListDrawable

为按钮设置不同状态的drawable的<**selctor**>，selector的本质是一个drawable对象，用到的就是**StateListDrawable**。属性如下：

- **drawable**:引用的Drawable位图,我们可以把他放到最前面,就表示组件的正常状态
- **state_focused**:是否获得焦点
- **state_window_focused**:是否获得窗口焦点
- **state_enabled**:控件是否可用
- **state_checkable**:控件可否被勾选,eg:checkbox
- **state_checked**:控件是否被勾选
- **state_selected**:控件是否被选择,针对有滚轮的情况
- **state_pressed**:控件是否被按下
- **state_active**:控件是否处于活动状态,eg:slidingTab
- **state_single**:控件包含多个子控件时,确定是否只显示一个子控件
- **state_first**:控件包含多个子控件时,确定第一个子控件是否处于显示状态
- **state_middle**:控件包含多个子控件时,确定中间一个子控件是否处于显示状态
- **state_last**:控件包含多个子控件时,确定最后一个子控件是否处于显示状态

**shape_btn_normal.xml**：

```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">
    <solid android:color="#DD788A"/>
    <corners android:radius="5dp"/>
    <padding android:top="2dp" android:bottom="2dp"/>
</shape>
```

接着我们来写个selctor：**selctor_btn.xml**：

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_pressed="true" android:drawable="@drawable/shape_btn_pressed"/>
    <item android:drawable="@drawable/shape_btn_normal"/>
</selector>
```

然后按钮设置android:background="@drawable/selctor_btn"就可以了

## Bitmap、BitmapFactory、BitmapFacotry.Options的关系及常用方法

### 关系

Bitmap的构造方法是私有的，外面不能实例化，只能通过JNI实例化。BitmapFactory通过各种`decodeXXX()`方法创建Bitmap，**BitmapFacotry.Options**是BitmapFactory的静态内部类，用来设置decode时的选项的

### Bitmap常用方法

```java
//BitMap类
public void recycle()——回收位图占用的内存空间，把位图标记为Dead 
public final boolean isRecycled() ——判断位图内存是否已释放 
public final int getWidth()——获取位图的宽度
public final int getHeight()——获取位图的高度
public final boolean isMutable()——图片是否可修改
public int getScaledWidth(Canvas canvas)——获取指定密度转换后的图像的宽度 
public int getScaledHeight(Canvas canvas)——获取指定密度转换后的图像的高度 
public boolean compress(CompressFormat format, int quality, OutputStream stream)——按指定的图片格式以及画质，将图片转换为输出流。 
format：Bitmap.CompressFormat.PNG或Bitmap.CompressFormat.JPEG 
quality：画质，0-100.0表示最低画质压缩，100以最高画质压缩。对于PNG等无损格式的图片，会忽略此项设置。 

常用的静态方法： 
public static Bitmap createBitmap(Bitmap src) ——以src为原图生成不可变得新图像 
public static Bitmap createScaledBitmap(Bitmap src, int dstWidth, 
            int dstHeight, boolean filter)——以src为原图，创建新的图像，指定新图像的高宽以及是否可变。 
public static Bitmap createBitmap(int width, int height, Config config)——创建指定格式、大小的位图 
public static Bitmap createBitmap(Bitmap source, int x, int y, int width, int height)以source为原图，创建新的图片，指定起始坐标以及新图像的高宽。 
```

### BitmapFactory常用方法

```java
//BitmapFactory工厂类：

//使用BitmapFactory  可从资源files, streams, and byte-arrays中解码生成Bitmap对象。
读取一个文件路径得到一个位图。如果指定文件为空或者不能解码成文件，则返回NULL。 
public static Bitmap decodeFile(String pathName, Options opts) 
public static Bitmap decodeFile(String pathName) 
读取一个资源文件得到一个位图。如果位图数据不能被解码，或者opts参数只请求大小信息时，则返回Null。 
（即当Options.inJustDecodeBounds=true,只请求图片的大小信息。） 
public static Bitmap decodeResource(Resources res, int id) 
public static Bitmap decodeResource(Resources res, int id, Options opts) 
从输入流中解码位图 
public static Bitmap decodeStream(InputStream is) 
从字节数组中解码生成不可变的位图 
public static Bitmap decodeByteArray(byte[] data, int offset, int length) 
```

### BitmapFactory.Option可设置参数

- boolean **inJustDecodeBounds**——如果设置为true，不获取图片，不分配内存，但会返回图片的高宽度信息。
- int **inSampleSize**——图片缩放的倍数。如果设为4，则宽和高都为原来的1/4，则图是原来的1/16。
- int **outWidth**——获取图片的宽度值
- int **outHeight**——获取图片的高度值
- int **inDensity**——用于位图的像素压缩比
- int **inTargetDensity**——用于目标位图的像素压缩比（要生成的位图）
- boolean **inScaled**——设置为true时进行图片压缩，从inDensity到inTargetDensity。

## Bitmap与Drawable的区别

Drawable 是一个抽象的概念, 而 Bitmap 是其存在的实体之一 

## Bitmap与Drawable相互转换

### Drawable 转换成 Bitmap

##### 方法一

通过 BitmapFactory 中的 decodeResource 方法，将资源文件中的 R.drawable.ic_drawable 转化成Bitmap

```java
Resources res = getResources();
Bitmap bmp = BitmapFactory.decodeResource(res, R.drawable.ic_drawable);
```

##### 方法二

将 Drawable 对象先转化成 BitmapDrawable ，然后调用 getBitmap 方法获取

```java
Resource res = gerResource();
Drawable drawable = res.getDrawable(R.drawable.ic_drawable);//获取drawable
BitmapDrawable bd = (BitmapDrawable) drawable;
Bitmap bm  = bd.getBitmap();
```

##### 方法三

根据已有的Drawable创建一个新的Bitmap

```java
public static Bitmap drawableToBitmap(Drawable drawable) {
    int w = drawable.getIntrinsicWidth();
    int h = drawable.getIntrinsicHeight();
    Bitmap.Configconfig = drawable.getOpacity() != PixelFormat.OPAQUE? Bitmap.Config.ARGB_8888  : Bitmap.Config.RGB_565;  
    Bitmap bitmap = Bitmap.createBitmap(w, h, config);
    //注意，下面三行代码要用到，否则在View或者SurfaceView里的canvas.drawBitmap会看不到图          
    Canvas canvas = new Canvas(bitmap); 
    drawable.setBounds(0,0, w, h);
    drawable.draw(canvas);
    return bitmap;
}
```

### Bitmap 转换成 Drawable

##### 方法一

```java
Drawable drawable = newBitmapDrawable(bitmap);
```

##### 方法二

```java
BitmapDrawable bd = newBitmapDrawable(getResource(), bm);
//BitmapDrawable是Drawable的子类，可以直接使用
```

## 避免Bitmap引起的OOM技巧

### 采用低内存占用量的编码方式

可以设置**BitmapFactory.Options**其中的**inPreferredConfig**属性， 默认是**Bitmap.Config.ARGB_8888**，我们可以修改成**Bitmap.Config.ARGB_4444**

- Bitmap.Config ARGB_4444：每个像素占四位，即A=4，R=4，G=4，B=4，那么一个像素点占4+4+4+4=16位

- Bitmap.Config ARGB_8888：每个像素占八位，即A=8，R=8，G=8，B=8，那么一个像素点占8+8+8+8=32位

默认使用ARGB_8888，即一个像素占4个字节，修改后大小可减小一倍

### 图片压缩

同样是**BitmapFactory.Options**，我们通过**inSampleSize**参数设置缩放倍数，比如写2，即长宽变为原来的1/2，图片就是原来的1/4，如果不进行缩放的话设置为1即可。但是值太小的话，图片会很模糊，而且要避免图片的拉伸变形，所以需要在程序中动态的计算，inSampleSize的合适值，而Options中又有这样一个方法：**inJustDecodeBounds**，将该参数设置为 true后，decodeFiel并不会分配内存空间，但是可以计算出原始图片的长宽，调用 options.**outWidth**/**outHeight**获取出图片的宽高，然后通过一定的算法，即可得到适合的 inSampleSize

### 及时回收图像

如果引用了大量的Bitmap对象，而应用又不需要同时显示所有图片，可以将暂时不用到的Bitmap对象及时回收掉。对于一些明确知道图片使用情况的场景可以主动recycle回收，比如引导页的图片，使用完就recycle，帧动画，加载一张，画一张，释放一张。使用时加载，不显示时直接置null或recycle。比如`imageView.setImageResource(0);` 