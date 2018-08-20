# 十二 Service

## 服务的启动方式

#### 通过StartService启动Service

采用start的方式开启服务

使用Service的步骤：

1. 定义一个类继承`Service`

2. 在`Manifest.xml`文件中配置该`Service`
3. 使用Context的`startService(Intent)`方法启动该Service
4. 不再使用时，调用`stopService(Intent)`方法停止该服务

使用这种start方式启动的Service的**生命周期**如下：
 `onCreate()`--->`onStartCommand()`（`onStart()`方法已过时）---> `onDestory()`

**说明**：如果服务已经开启，不会重复的执行`onCreate()`， 而是会调用`onStart()`和`onStartCommand()`。
 服务停止的时候调用 `onDestory()`。服务只会被停止一次。

**特点**：一旦服务开启跟调用者(开启者)就没有任何关系了。开启者退出了，开启者挂了，服务还在后台长期的运行。 开启者**不能调用**服务里面的方法。

#### 通过bindService启动Service

采用bind的方式开启服务

使用Service的步骤：

1.定义一个类继承`Service`
2.在`Manifest.xml`文件中配置该`Service`
3.使用`Context`的`bindService(Intent, ServiceConnection, int)`方法启动该`Service`
4.不再使用时，调用`unbindService(ServiceConnection)`方法停止该服务

使用这种start方式启动的Service的**生命周期**如下：
 `onCreate()` --->`onBind()`--->`onUnbind()`--->`onDestory()`

**注意**：绑定服务不会调用`onstart()`或者`onstartcommand()`方法

**特点**：bind的方式开启服务，绑定服务，调用者挂了，服务也会跟着挂掉。绑定者可以调用服务里面的方法。任何一个服务可以和任何一个活动绑定

## 服务的销毁

- 调用`startService(Intent)`方法后调用`stopService(Intent)`方法，此时服务中的`onDestory()`方法就会执行。
- 调用`bindService(Intent, ServiceConnection, int)`方法后调用`unbindService(ServiceConnection)`方法，执行服务中的`onDestory()`方法。
- 既调用了`startService(Intent)`方法，又调用了`bindService(Intent, ServiceConnection, int)`方法，必须要让两种条件同时不满足，也即同时调用`stopService(Intent)`方法与`unbindService(ServiceConnection)`方法后，`onDestroy()`方法才会执行。