# 十六 ContentProvider

ContentProvider是Android系统中提供的专门用于不同应用间进行数据共享的组件，提供了一套标准的接口用来获取以及操作数据，准许开发者把自己的应用数据根据需求开放给其他应用进行增删改查，而无须担心直接开放数据库权限而带来的安全问题。系统预置了许多ContentProvider用于获取用户数据，比如消息、联系人、日程表等。

## 如何读取联系人

在ContentProvider的使用过程中，需要借用**ContentResolver**来控制ContentProvider所暴露处理的接口，作为代理来间接操作ContentProvider以获取数据。 可以在所有继承Context的类中通过 `getContentResovler()` 方法获取ContentResolver。读取联系人可以通过拿到系统中联系人对应的Uri后用Cursor实现遍历查询：

```java
ContentResolver resolver = getContentResolver();
Uri uri = ContactsContract.CommonDataKinds.Phone.CONTENT_URI;
Cursor cursor = resolver.query(uri, null, null, null, null);
while (cursor.moveToNext()) {
    String cName = cursor.getString(cursor.getColumnIndex(ContactsContract.CommonDataKinds.Phone.DISPLAY_NAME));
    String phoneNum = cursor.getString(cursor.getColumnIndex(ContactsContract.CommonDataKinds.Phone.NUMBER));
}
cursor.close();
```

## ContentProvider与SQLiteOpenHelper的关系

- ContentProvider可以通过SQLiteOpenHelper中的数据来提供数据。创建一个内容提供器必须建立一个保存数据的系统。大多数内容提供器使用Android的文件储存方法或SQLite数据库来存放它们的数据，但是你可以用任何你想要的方式来存放数据。Android提供SQLiteOpenHelper类来帮助你创建一个数据库以及SQLiteDatabase类来管理它
- ContentProvider用于不同应用间的数据共享，而SQLiteOpenHelper只能操作当前应用的数据库

## ContentProvider的写法

创建一个自定义ContentProvider的方式是**继承**ContentProvider类并实现其六个抽象方法：

- onCreate()：代表ContentProvider的创建，可以用来进行一些初始化操作
- getType(Uri uri)：用来返回一个Uri请求所对应的MIME类型
- insert(Uri uri, ContentValues values)：插入数据
- query(Uri uri, String[] projection, String selection, String[] selectionArgs, String sortOrder)：查询数据
- update(Uri uri, ContentValues values, String selection, String[] selectionArgs)：更新数据
- delete(Uri uri, String selection, String[] selectionArgs)：删除数据

之后，需要在AdnroidManifest.xml中对ContentProvider进行**注册**

## 数据库的监听

通过ContentObserver监听ContentProvider的数据变化，在继承自ContentObserver的类中重写onChange()方法实现监听

## 数据库的升降级

#### 保留数据的升级

在继承自SQLiteOpenHelper的自定义类的构造函数中修改**版本号**，后续传入的版本参数值如果比系统当前记录的高则执行onUpgrade()方法进行升级

##### 增加一张表

增加新表（或者删除没有键的表）的操作代价很小，只需要在onUpgrade()中写好建表操作和插入初始数据就好了

##### 修改表定义

SQLite数库对ALTER TABLE命令支持非常有限，只能在表末尾添加列，不能修改列定义，不能删除已有的列。如果在末尾添加列不能满足需求，进行如下操作即可：

1. 将现有表重命名为临时表；
2. 创建新表；
3. 将临时表的数据导入新表（注意处理修改的列）；
4. 删除临时表。

#### 降级

重写`onDowngrade(SQLiteDatabase db, int oldVersion, int newVersion)` 方法，重写的逻辑步骤同上面的四步，只是第二步需要变为：创建低版本的表

## Cursor的使用注意事项

在Android查询数据是通过Cursor类来实现的。当我们使用`SQLiteDatabase.query()`方法时，就会得到Cursor对象，Cursor所指向的就是每一条数据。注意事项：

1. 由于android中数据库处理使用cursor时，游标不是放在为0的下标，而是放在为-1的下标处开始的，所以想访问第一个元素时应执行moveToFirst()方法
2. 用finally子句确保Cursor执行close()方法防止内存泄露