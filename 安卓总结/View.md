# 二十七 View

## View与ViewGroup的概念及关系

Android 应用中的所有用户界面元素都是使用 `View` 和 `ViewGroup` 对象构建而成。`View` 对象用于在屏幕上绘制可供用户交互的内容。`ViewGroup` 对象用于储存其他 `View`（和 `ViewGroup`）对象，以便定义界面的布局。

每个应用组件的用户界面都是使用 `View` 和 `ViewGroup` 对象的层次结构定义的。每个视图组都是一个用于组织子视图的不可见容器，而子视图可以是输入控件或其他可绘制某一 UI 部分的小部件。 

![img](https://developer.android.com/images/viewgroup.png) 

## View的绘制流程

#### View的布局过程

1. 测量阶段，`measure()` 方法被父 View 调用，在 `measure()` 中做一些准备和优化工作后，调用 `onMeasure()` 来进行实际的自我测量。 `onMeasure()` 做的事，`View` 和 `ViewGroup` 不一样：
   1. **View**：`View` 在 `onMeasure()` 中会计算出自己的尺寸然后保存
   2. **ViewGroup**：`ViewGroup` 在 `onMeasure()` 中会调用所有子 View 的 `measure()` 让它们进行自我测量，并根据子 View 计算出的期望尺寸来计算出它们的实际尺寸和位置（实际上 99.99% 的父 View 都会使用子 View 给出的期望尺寸来作为实际尺寸）然后保存。同时，它也会根据子 View 的尺寸和位置来计算出自己的尺寸然后保存
2. 布局阶段，`layout()` 方法被父 View 调用，在 `layout()` 中它会保存父 View 传进来的自己的位置和尺寸，并且调用 `onLayout()` 来进行实际的内部布局。`onLayout()` 做的事， `View` 和 `ViewGroup` 也不一样：
   1. **View**：由于没有子 View，所以 `View` 的 `onLayout()` 什么也不做
   2. **ViewGroup**：`ViewGroup` 在 `onLayout()` 中会调用自己的所有子 View 的 `layout()` 方法，把它们的尺寸和位置传给它们，让它们完成自我的内部布局

#### View的绘制流程

一个完整的绘制过程会依次绘制以下几个内容：

1. 背景
2. 主体（`onDraw()`）
3. 子 View（`dispatchDraw()`）
4. 滑动边缘渐变和滑动条
5. 前景

一般来说，一个 View（或 ViewGroup）的绘制不会将这几项全都包含，但必然逃不出这几项，并且一定会严格遵守这个顺序。例如通常一个 `LinearLayout` 只有背景和子 View，那么它会先绘制背景再绘制子 View；一个 `ImageView` 有主体，有可能会再加上一层半透明的前景作为遮罩，那么它的前景也会在主体之后进行绘制。需要注意，前景的支持是在 Android 6.0（也就是 API 23）才加入的；之前其实也有，不过只支持 `FrameLayout`，而直到 6.0 才把这个支持放进了 `View` 类里。

这其中的第 2、3 两步，前面已经讲过了；第 1 步——背景，它的绘制发生在一个叫 `drawBackground()` 的方法里，但这个方法是 `private` 的，不能重写，你如果要设置背景，只能用自带的 API 去设置（xml 布局文件的 `android:background` 属性以及 Java 代码的 `View.setBackgroundXxx()` 方法），而不能自定义绘制；而第 4、5 两步——滑动边缘渐变和滑动条以及前景，这两部分被合在一起放在了 `onDrawForeground()` 方法里，这个方法是可以重写的。

![img](https://ws4.sinaimg.cn/large/006tKfTcly1fiiwb2nr63j30ga0bddgg.jpg)

滑动边缘渐变和滑动条可以通过 xml 的 `android:scrollbarXXX` 系列属性或 Java 代码的 `View.setXXXScrollbarXXX()` 系列方法来设置；前景可以通过 xml 的 `android:foreground` 属性或 Java 代码的 `View.setForeground()` 方法来设置。而重写 `onDrawForeground()` 方法，并在它的 `super.onDrawForeground()`方法的上面或下面插入绘制代码，则可以控制绘制内容和滑动边缘渐变、滑动条以及前景的遮盖关系。

## 事件分发与传递机制

![img](https://upload-images.jianshu.io/upload_images/966283-d01a5845f7426097.png) 

ViewGroup和View的onTouchEvent方法是做事件处理的，那么这个事件只能有两个处理方式：

1. 自己消费掉，事件终结，不再传给谁----->return true;

2. 继续从下往上传，不消费事件，让父View也能收到到这个事件----->return false;View的默认实现是不消费的。所以super==false。

ViewGroup的onInterceptTouchEvent方法对于事件有两种情况：

1. 拦截下来，给自己的onTouchEvent处理--->return true;
2. 不拦截，把事件往下传给子View---->return false,ViewGroup默认是不拦截的，所以super==false；

#### 总结

1. 对于 dispatchTouchEvent，onTouchEvent，return true是终结事件传递。return false 是回溯到父View的onTouchEvent方法
2. ViewGroup 想把自己分发给自己的onTouchEvent，需要拦截器onInterceptTouchEvent方法return true 把事件拦截下来
3. ViewGroup 的拦截器onInterceptTouchEvent 默认是不拦截的，所以return super.onInterceptTouchEvent()=return false
4. View 没有拦截器，为了让View可以把事件分发给自己的onTouchEvent，View的dispatchTouchEvent默认实现（super）就是把事件分发给自己的onTouchEvent

## 自定义view时的注意事项

### 自定义View的流程

##### 1.继承View或者继承View的一个实现类

- 至少重写父类的一个构造方法
   它包含一个Context与一个AttributeSet对象作为参数。这个constructor允许layout editor创建并编辑你的view的实例。

  ```java
  public RoundImageView(Context context, AttributeSet attrs) {
      super(context, attrs);
      getAttrs(context,attrs);
  }
  ```

##### 2.定义自定义属性

- 如何定义
   在values文件夹下新建attrs.xml文件，定义自定义View需要的属性，可以通过构造方法的 attrs参数获取

 ```xml
<resources>
<declare-styleable name="RoundImageView">
    <attr name="XfermodeImageView_src" format="reference"/>
    <attr name="XfermodeImageView_type" format="integer"/>
    <attr name="XfermodeImageView_borderRadius" format="dimension"/>
</declare-styleable>
</resources>
  
 ```

- 如何使用
   在使用自定义的属性时要声明相应的命名空间：

  ```xml
<com.vivo.RoundImageView 
	xmlns:custom="http://schemas.android.com/apk/res-auto"   
	android:layout_width="match_parent"             
	android:layout_height="wrap_content"   
	custom:XfermodeImageView_src="@mipmap/test2" 
/>
  ```

**请注意**：如果你的view是一个inner class，你必须指定这个view的outer class。同样的,如果PieChart有一个inner class叫做PieView。为了使用这个类中自设的属性，你应该使用com.example.customviews.charting.PieChart$PieView。

- 如何获取属性并且应用

  对res目录里的每一个\<declare-styleable>资源，自动生成的R.java文件定义了存放属性ID的数组和常量，常量用来索引数组中每个属性。因此我们可以使用TypedArray这个类来读取属性：

  ```java
  public CustomView(Context context, AttributeSet attrs) {
      super(context, attrs);
      TypedArray a = context.getTheme().obtainStyledAttributes(attrs, R.styleable.CustomView, 0, 0);
      try {
          mShowText =a.getBoolean(R.styleable.CustomView_showText, false);
          mTextPos = a.getInteger(R.styleable.CustomView_labelPosition, 0);
      } finally {
          a.recycle();
      }
  }
  ```

TypedArray对象是一个共享资源，必须被在使用后进行回收。

- 添加属性和事件

  ```
  public void setShowText(boolean showText) {
      mShowText = showText;
      invalidate();
      requestLayout();
  }
  ```

**注意点**：在setShowText方法里面有调用invalidate()和requestLayout()，这两个调用是确保稳定运行的关键。当view的某些内容发生变化的时候，需要调invalidate来通知系统对这个view进行redraw，当某些元素变化会引起组件大小变化时，需要调用requestLayout方法。调用时若忘了这两个方法，将会导致hard-to-find bugs。

- 响应事件的监听器
   如果需要额外的事件，可以通过接口回调的方式来设置事件监听，一般可以定义一个内部接口.

##### 3.需要warp_content属性时

在onMeasure()方法中判断测量模式为AT_MOST时，设定一个值。

##### 4.实现onDraw()方法