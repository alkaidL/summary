# 十五 BroadcastReceiver

广播（Broadcast）是在组件之间传播数据的一种机制，这些组件可以位于不同的进程中，起到**进程间通信**的作用 

## 广播的类别

#### 标准广播

标准广播是一种完全异步执行的广播，在广播发出后**所有**的广播接收器会在同一时间接收到这条广播，之间没有先后顺序，效率比较高，且无法被截断

#### 有序广播

有序广播是一种同步执行的广播，在广播发出后同一时刻只有**一个**广播接收器能够接收到， 优先级高的广播接收器会优先接收，当优先级高的广播接收器的 onReceiver() 方法运行结束后，广播才会继续传递，且前面的广播接收器可以选择**截断**广播，这样后面的广播接收器就无法接收到这条广播了

## 广播的注册方式及其使用差异

#### 静态注册

静态注册即在**清单文件**中为 **BroadcastReceiver** 进行注册，使用`< receiver >`标签声明，并在标签内用 `< intent-filter >` 标签设置过滤器

这种形式的 BroadcastReceiver 的生命周期伴随着整个应用，如果这种方式处理的是系统广播，那么应用未启动时，该广播接收器也能接收到该广播

#### 动态注册

动态注册 BroadcastReceiver 是在代码中定义并设置好一个 **IntentFilter** 对象，然后在需要注册的地方调用 `Context.registerReceiver()` 方法，调用 `Context.unregisterReceiver()` 方法取消注册，此时就不需要在清单文件中注册 Receiver了

由于注册逻辑无法在onCreate()方法调用之前执行，所以无法在程序未启动时接收广播

## 自定义广播

1. 定义广播接收器
2. 在清单文件中的`<receiver>`里注册action
3. 在Intent初始化时给其对应的action表明该自定义广播，还可以在Intent中携带数据传递给广播接收器

## LocalBroadcastManager

#### 场景

之前发送和接收到的广播全都是属于系统全局广播，即发出的广播可以被其他应用接收到，而且也可以接收到其他应用发送出的广播，这样可能会有不安全因素。因此，在某些情况下可以采用本地广播机制，使用这个机制发出的广播只能在应用内部进行传递，而且广播接收器也只能接收本应用内自身发出的广播

本地广播是使用 **LocalBroadcastManager** 来对广播进行管理

| 函数                                                         | 作用         |
| ------------------------------------------------------------ | ------------ |
| LocalBroadcastManager.getInstance(this).registerReceiver(BroadcastReceiver, IntentFilter) | 注册Receiver |
| LocalBroadcastManager.getInstance(this).unregisterReceiver(BroadcastReceiver) | 注销Receiver |
| LocalBroadcastManager.getInstance(this).sendBroadcast(Intent) | 发送异步广播 |
| LocalBroadcastManager.getInstance(this).sendBroadcastSync(Intent) | 发送同步广播 |

需要注意的是，本地广播是无法通过静态注册的方式来接收的，因为静态注册广播主要是为了在程序未启动的情况下也能接收广播，而本地广播是应用自己发送的，此时应用肯定是启动的了

#### 优点

1. 不必担心机密数据泄露
2. 其它程序无法将广播发送到我们的程序内部
3. 发送本地广播比系统全局广播更高效

## 常用的系统广播总结

| 系统广播意图                            | 广播作用                                                     |
| --------------------------------------- | ------------------------------------------------------------ |
| Intent.ACTION_AIRPLANE_MODE_CHANGED     | 关闭或打开飞行模式时的广播                                   |
| Intent.ACTION_BATTERY_CHANGED           | 充电状态，或者电池的电量发生变化                             |
| Intent.ACTION_BATTERY_LOW               | 表示电池电量低                                               |
| Intent.ACTION_BATTERY_OKAY              | 表示电池电量充足，即从电池电量低变化到饱满时会发出广播       |
| Intent.**ACTION_BOOT_COMPLETED**        | 在系统启动完成后，这个动作被广播一次（只有一次）             |
| Intent.**ACTION_CAMERA_BUTTON**         | 按下照相时的拍照按键(硬件按键)时发出的广播                   |
| Intent.**ACTION_CLOSE_SYSTEM_DIALOGS**  | 当屏幕超时进行锁屏时,当用户按下电源按钮，长按或短按(不管有没跳出话框)，进行锁屏时，android系统都会广播此Action消息 |
| Intent.**ACTION_CONFIGURATION_CHANGED** | 设备当前设置被改变时发出的广播（包括的改变：界面语言、设备方向等，请参考Configuration.java） |
| Intent.ACTION_DATE_CHANGED              | 设备日期发生改变时会发出此广播                               |
| Intent.ACTION_DEVICE_STORAGE_LOW        | 设备内存不足时发出的广播，此广播只能由系统使用，其它APP不可用 |
| Intent.ACTION_DEVICE_STORAGE_OK         | 设备内存从不足到充足时发出的广播，此广播只能由系统使用，其它APP不可用 |
| Intent.ACTION_HEADSET_PLUG              | 在耳机口上插入耳机时发出的广播                               |
| Intent.ACTION_INPUT_METHOD_CHANGED      | 改变输入法时发出的广播                                       |
| Intent.ACTION_LOCALE_CHANGED            | 设备当前区域设置已更改时发出的广播                           |
| Intent.ACTION_MEDIA_BAD_REMOVAL         | 未正确移除SD卡（正确移除SD卡的方法:设置–SD卡和设备内存–卸载SD卡），但已把SD卡取出来时发出的广播 |
| Intent.ACTION_MEDIA_BUTTON              | 按下”Media Button” 按键时发出的广播，假如有”Media Button”按键的话(硬件按键) |
| Intent.ACTION_MEDIA_CHECKING            | 插入外部储存装置，比如SD卡时，系统会检验SD卡，此时发出的广播 |
| Intent.ACTION_MEDIA_EJECT               | 已拔掉外部大容量储存设备发出的广播（比如SD卡，或移动硬盘）,不管有没有正确卸载都会发出此广播 |
| Intent.ACTION_MEDIA_MOUNTED             | 插入SD卡并且已正确安装（识别）时发出的广播                   |
| Intent.ACTION_MEDIA_REMOVED             | 外部储存设备已被移除，不管有没正确卸载,都会发出此广播        |
| Intent.ACTION_MEDIA_SCANNER_FINISHED    | 已经扫描完介质的一个目录                                     |
| Intent.**ACTION_PACKAGE_ADDED**         | 成功的安装APK之后                                            |
| Intent.ACTION_PACKAGE_CHANGED           | 一个已存在的应用程序包已经改变，包括包名                     |
| Intent.ACTION_PACKAGE_DATA_CLEARED      | 清除一个应用程序的数据时发出的广播（在设置－应用管理－选中某个应用，之后点清除数据时） |
| Intent.**ACTION_PACKAGE_INSTALL**       | 触发一个下载并且完成安装时发出的广播，比如在电子市场里下载应用 |
| Intent.**ACTION_PACKAGE_REMOVED**       | 成功的删除某个APK之后发出的广播                              |
| Intent.**ACTION_PACKAGE_REPLACED**      | 替换一个现有的安装包时发出的广播（不管现在安装的APP比之前的新还是旧，都会发出此广播） |
| Intent.ACTION_PACKAGE_RESTARTED         | 用户重新开始一个包，包的所有进程将被杀死，所有与其联系的运行时间状态应该被移除，包括包名（重新开始包程序不能接收到这个广播） |
| Intent.ACTION_POWER_CONNECTED           | 插上外部电源时发出的广播                                     |
| Intent.ACTION_POWER_DISCONNECTED        | 已断开外部电源连接时发出的广播                               |
| Intent.ACTION_REBOOT                    | 重启设备时的广播                                             |
| Intent.ACTION_SCREEN_OFF                | 屏幕被关闭之后的广播                                         |
| Intent.**ACTION_SCREEN_ON**             | 屏幕被打开之后的广播                                         |
| Intent.ACTION_SHUTDOWN                  | 关闭系统时发出的广播                                         |
| Intent.ACTION_TIMEZONE_CHANGED          | 时区发生改变时发出的广播                                     |
| Intent.ACTION_TIME_CHANGED              | 时间被设置时发出的广播                                       |
| Intent.ACTION_TIME_TICK                 | 当前时间改变，每分钟都发送，不能通过组件声明来接收，只有通过`Context.registerReceiver()`方法来注册 |
| Intent.ACTION_UID_REMOVED               | 一个用户ID已经从系统中移除发出的广播                         |
| Intent.ACTION_UMS_CONNECTED             | 设备已进入USB大容量储存状态时发出的广播                      |
| Intent.ACTION_UMS_DISCONNECTED          | 设备已从USB大容量储存状态转为正常状态时发出的广播            |
| Intent.ACTION_WALLPAPER_CHANGED         | 设备墙纸已改变时发出的广播                                   |

## 广播的使用注意事项

- 静态注册时，如果是系统广播，则子类Receiver实现中不能重写非默认构造函数，也不能主动发送广播，例如：

  ```java
  Intent intent = new Intent("android.net.conn.CONNECTIVITY_CHANGE"); 
  sendBroadcast(intent);
  ```
  自定义广播需要如上主动发送广播

- 动态注册时，若是系统广播，可以在子类Receiver实现非默认构造函数，但不能主动发送广播。需要在代码中注册：

  ```java
  @Override
  protected void onResume() {
      IntentFilter filter = new IntentFilter();
      filter.addAction("android.net.conn.CONNECTIVITY_CHANGE");
      registerReceiver(mMybroadcastReceiver, filter);
      super.onResume();
  }
  ```

  自定义广播需要注册，并且需要主动`sendBroadcast()`

- 本地广播是无法通过静态注册的方式来接收的

- 广播中想要启动Activity，需要为Intent加入**FLAG_ACTIVITY_NEW_TASK**的标记，因为需要栈存放新活动

- 广播中想弹出AlertDialog的话需要设置对话框的类型为**TYPE_SYSTEM_ALERT**，否则无法弹出
