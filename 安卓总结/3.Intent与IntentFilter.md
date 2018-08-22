# 三 Intent

Intent 是一个消息传递对象 ，但其基本用例主要包括以下三个： 

- 启动Activity
- 启动服务
- 传递广播

分为显式Intent和隐式Intent

## Intent 解析

当系统收到隐式 Intent 以启动 Activity 时，它根据以下三个方面将该 Intent 与 Intent 过滤器进行比较，搜索该 Intent 的最佳 Activity：

- Intent 操作（action）
- Intent 数据（URI 和数据类型）
- Intent 类别（category）

