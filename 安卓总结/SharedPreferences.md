# 十七 SharedPreferences

当有一个相对较小的key-value集合需要保存时，可以使用SharedPreferences APIs。 SharedPreferences 对象指向一个保存key-value pairs的文件，并为读写他们提供了简单的方法。每个 SharedPreferences 文件均由framework管理，其既可以是私有的，也可以是共享的。 

## 获取SharedPreference

我们可以通过以下两种方法之一创建或者访问shared preference 文件:

- getSharedPreferences() — 如果需要多个通过名称参数来区分的shared preference文件, 名称可以通过第一个参数来指定。可在app中通过任何一个Context 执行该方法。
- getPreferences() — 当activity仅需要一个shared preference文件时。因为该方法会检索activity下默认的shared preference文件，并不需要提供文件名称。

例：下面的示例在一个 Fragment 中被执行，它以private模式访问名为 `R.string.preference_file_key` 的shared preference文件。这种情况下，该文件仅能被我们的app访问。

```
Context context = getActivity();
SharedPreferences sharedPref = context.getSharedPreferences(
        getString(R.string.preference_file_key), Context.MODE_PRIVATE);
```

应以与app相关的方式为shared preference文件命名，该名称应唯一。如本例中可将其命名为 `"com.example.myapp.PREFERENCE_FILE_KEY"` 。

当然，当activity仅需要一个shared preference文件时，我们可以使用getPreferences()方法：

```
SharedPreferences sharedPref = getActivity().getPreferences(Context.MODE_PRIVATE);
```

## SharedPreferences读写

#### 写Shared Preference

为了写`shared preferences`文件，需要通过执行edit()创建一个 SharedPreferences.Editor。

通过类似putInt()与putString()等方法传递keys与values，接着通过commit()提交改变.

```java
SharedPreferences sharedPref = getActivity().getPreferences(Context.MODE_PRIVATE);
SharedPreferences.Editor editor = sharedPref.edit();
editor.putInt(getString(R.string.saved_high_score), newHighScore);
editor.commit();
```

#### 读Shared Preference

为了从shared preference中读取数据，可以通过类似于 getInt() 及 getString()等方法来读取。在那些方法里面传递我们想要获取的value对应的key，并提供一个默认的value作为查找的key不存在时函数的返回值。如下：

```java
SharedPreferences sharedPref = getActivity().getPreferences(Context.MODE_PRIVATE);
int defaultValue = getResources().getInteger(R.string.saved_high_score_default);
long highScore = sharedPref.getInt(getString(R.string.saved_high_score), default);
```

## SharedPreferences使用注意事项

#### 多进程问题

多进程间通过SharedPreferences来共享数据是**不安全**的 ，MODE_MULTI_PROCESS可能会导致数据丢失，这种模式下每次获取SharedPreferences都会检测文件是否改变，只要读的时候另一进程在写，就会导致写丢失。这样失败概率就会大幅度提高。反之，若不设置这个模式，则只在第一次创建SharedPreferences的时候读取，导致写失败的概率就会大幅度降低，当然，仍然存在失败的可能。 

#### 提交数据

`SharedPreferences.Editor` 有 `apply` 和 `commit` 两个方法用于提交数据编辑，这两个方法的区别在于：

1. `apply`没有返回值而`commit`返回`boolean`表明修改是否提交成功
2. `apply`是将修改数据原子提交到内存, 而后异步真正提交到硬件磁盘, 而`commit`是同步的提交到硬件磁盘，因此，在多个并发的提交`commit`的时候，他们会等待正在处理的`commit`保存到磁盘后在操作，从而降低了效率。而`apply`只是原子的提交到内容，后面有调用`apply`的函数的将会直接覆盖前面的内存数据，这样从一定程度上提高了很多效率，不过即使是异步的`apply`操作也是有可能**阻塞**主线程的，所以不要存储过大文件
3. `apply`方法没有任何失败的提示

由于在一个进程中，SharedPreference是单实例，一般不会出现并发冲突，如果对提交的结果不关心的话，建议使用`apply`，当然需要确保提交成功且有后续操作的话，还是需要用`commit`的。