# 二十六 LayoutInflater

## LayoutInflater原理

不管你是使用的哪个inflate()方法的重载，最终都会辗转调用到LayoutInflater的如下方法中：

```java
public View inflate(XmlPullParser parser, ViewGroup root, boolean attachToRoot) {}
```

LayoutInflater其实是使用pull解析方式来解析布局文件的。其中调用了`createViewFromTag()`这个方法，并把节点名和参数传了进去。它是用于根据节点名来创建View对象的。在`createViewFromTag()`方法的内部又会去调用`createView()`方法，然后使用反射的方式创建出View的实例并返回

到这里只是创建出了一个根布局的实例而已，接下来会在调用`rInflate()`方法来循环遍历这个根布局下的子元素，代码如下所示：

```java

private void rInflate(XmlPullParser parser, View parent, final AttributeSet attrs)
        throws XmlPullParserException, IOException {
    final int depth = parser.getDepth();
    int type;
    while (((type = parser.next()) != XmlPullParser.END_TAG ||
            parser.getDepth() > depth) && type != XmlPullParser.END_DOCUMENT) {
        if (type != XmlPullParser.START_TAG) {
            continue;
        }
        final String name = parser.getName();
        if (TAG_REQUEST_FOCUS.equals(name)) {
            parseRequestFocus(parser, parent);
        } else if (TAG_INCLUDE.equals(name)) {
            if (parser.getDepth() == 0) {
                throw new InflateException("<include /> cannot be the root element");
            }
            parseInclude(parser, parent, attrs);
        } else if (TAG_MERGE.equals(name)) {
            throw new InflateException("<merge /> must be the root element");
        } else {
            final View view = createViewFromTag(name, attrs);
            final ViewGroup viewGroup = (ViewGroup) parent;
            final ViewGroup.LayoutParams params = viewGroup.generateLayoutParams(attrs);
            rInflate(parser, view, attrs);
            viewGroup.addView(view, params);
        }
    }
    parent.onFinishInflate();
}
```

可以看到同样是`createViewFromTag()`方法来创建View的实例，然后还会递归调用`rInflate()`方法来查找这个View下的子元素，每次递归完成后则将这个View添加到父布局当中

这样的话，把整个布局文件都解析完成后就形成了一个完整的DOM结构，最终会把最顶层的根布局返回，至此inflate()过程全部结束

## LayoutInflater实例获取

首先要注意`LayoutInflater`本身是一个抽象类，我们不可以直接通过`new`的方式去获得它的实例，通常有下面三种方式：

**第一种：**

```java
LayoutInflater inflater = (LayoutInflater) context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
```

**第二种：**

```java
LayoutInflater inflater = LayoutInflater.from(context); 
```

**第三种：**

```java
在Activity内部调用getLayoutInflater()方法
```

看看后面两种方法的实现：

```java
public static LayoutInflater from(Context context) {
    LayoutInflater LayoutInflater =
            (LayoutInflater) context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
    if (LayoutInflater == null) {
        throw new AssertionError("LayoutInflater not found.");
    }
    return LayoutInflater;
}
```

在Activity内部调用getLayoutInflater方法其实调用的是PhoneWindow的mLayoutInflater:

```java
public PhoneWindow(Context context) {
    super(context);
    mLayoutInflater = LayoutInflater.from(context);
}
```

所以，这几个方法实际上殊途同归，都是通过调用Context的`getSystemService()`方法去获取。获取到的是`PhoneLayoutInflater`这个实现类

```java
public class Policy implements IPolicy {
    ...
    public LayoutInflater makeNewLayoutInflater(Context context) {
        return new PhoneLayoutInflater(context);
    }
}
```

## LayoutInflater加载布局

#### 动态加载布局用inflater来实现

其步骤一般为：动态加载布局 –> 向布局中动态添加组件

一、动态加载布局：

- 在Activity、Fragment等中可获取到getLayoutInflater()

```java
LayoutInflater _inflater = getLayoutInflater();
View view = _inflater.inflate(R.layout.activity_main, null);
setContentView(_view);
```

- 在Adapter中不能获取到getLayoutInflater()，通过上下文获取

```java
LayoutInflater _inflater = LayoutInflater.from(this);
View view = _inflater.inflate(R.layout.activity_main, null);
setContentView(_view);
```

二、在布局中动态添加组件：

```java
    // 1、获取根布局属性
    FrameLayout frameLayout = (FrameLayout)   findViewById(R.id.flyt_main);

    // 2、获取组件，设置组件属性
    final Button button = new Button(this);
    button.setText("跳转到SecondActivity");

    // 3、在布局中添加组件，设置组件属性
    frameLayout.addView(_button,LayoutParams.FILL_PARENT,LayoutParams.FILL_PARENT);
```

当然，除了一般方法，当然也有高等级的使用方式，那就是静态XML和动态加载XML混合使用。

#### 混合使用

- 静态主Layout动态加载静态子Layout 
  操作要点： 
  在一个静态Layout的基础上，将其作为子Layout，构建一个主Layout。 
  要在主Layout中`设置一个容纳子Layout的容器`。 
  在程序中获取到容器，并将子Layout以view的形式加入主Layout中。
- 静态主Layout动态加载动态的Layout 
  操作要点： 
  同样在一个静态Layout的基础上，将其作为子Layout，构建一个主Layout。并在主Layout中`设置一个容纳子Layout的容器`。 
  构建一个自定义的Layout类，在类中加载子Layout（当然，你可以封装很多的方法实现你需要的功能）。 
  因为子Layout的加载已经包含在自定义的Layout类中，所以需要实例化该Layout类，并将其加入到主Layout中。

## LayoutInflater中inflate()的区别

区别主要在于root和attachToRoot

root和attachToRoot参数的影响：

- `root != null， attachToRoot == true：`
   传进来的布局会被加载成为一个View并作为子View添加到root中，最终返回root；
   而且这个布局根节点的android:layout_参数会被解析用来设置View的大小。
- `root == null， attachToRoot无用：`
   当root为空时，attachToRoot是什么都没有意义，此时传进来的布局会被加载成为一个View并直接返回；
   布局根View的android:layout_xxx属性会被忽略。
- `root != null， attachToRoot == false：`
   传进来的布局会被加载成为一个View并直接返回。
   布局根View的android:layout_xxx属性会被解析成LayoutParams并保留。(root只用来参与生成布局根View的LayoutParams)