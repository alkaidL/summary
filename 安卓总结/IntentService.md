# 十三 IntentService

## IntentService特点

1. 其回调函数onHandleIntent中可以直接进行耗时操作，不必再开线程。其原理是IntentService的成员变量 Handler在初始化时已属于工作线程，之后handleMessage，包括onHandleIntent等函数都运行在工作线程中。
2. 多次调用onHandleIntent函数（也就是有多个耗时任务要执行），多个耗时任务会按顺序依次执行。原理是其内置的Handler关联了任务队列，Handler通过looper取任务执行是顺序执行的。 

## IntentService与Service区别

- Service中的代码运行在主线程中，处理耗时任务时容易ANR（Application Not Responding），IntentService是继承于Service并处理异步请求的一个类，在IntentService中有一个工作线程（HandlerThread）来处理耗时操作，请求的Intent记录会加入队列，故不会引发ANR
- Service需要写`stopSelf()`，IntentService在运行结束后自动停止（内置该方法）

## 注意事项

#### 多次启动 IntentService 会顺序执行事件，为什么停止服务后，后续的事件得不到执行？

IntentService 中使用的 Handler、Looper、MessageQueue 机制把消息发送到线程中去执行的，所以多次启动 IntentService 不会重新创建新的服务和新的线程，只是把消息加入消息队列中等待执行，而如果服务停止，会清除消息队列中的消息，后续的事件得不到执行。

#### 为什么不建议通过 bindService() 启动 IntentService？

IntentService 源码中的 onBind() 默认返回 null；不适合 bindService() 启动服务，如果你执意要 bindService() 来启动 IntentService，可能因为你想通过 Binder 或 Messenger 使得 IntentService 和 Activity 可以通信，这样那么 onHandleIntent() 不会被回调，相当于在你使用 Service 而不是 IntentService。