# 二十一 Json解析

## Json语法

JSON语法 是 JavaScript 对象表示法语法的子集

- 数据在 名称/值对 中
- 数据由逗号分隔
- 花括号{}保存对象
- 方括号[]保存数组

## JsonReader的使用

1. 使用JsonReader方法解析Json数据对象，需要创建一个JsonReader对象；
2. 然后使用beginArray()来开始解析 [ 左边的第一个数组；
3. 再使用beginObject()来开始解析数组{中的第一个对象；
4. 对于直接的数据可以直接得到解析到的数据，但对于在json中嵌套了数组的数据，需要再写一个解析方法；
5. 在解析完成后，需要用endArray(),endObject()来关闭解析。

## JSONObject的使用

#### JSONObject使用

1. 创建一个JSONObject对象，构造函数传入json数据

2. 通过该对象的optXXX()方法解析并拿到数据，如：

   ```java
   String name = jsonObj.optString("name");
   int age = jsonObj.optInt("age");
   int weight = jsonObj.optInt("weight");
   ```

#### JSONArray

可结合JSONArray使用以解析json对象数组，如：

```java
JSONArray jsonArray = new JSONArray(json3);
for (int i= 0; i < jsonArray.length(); i++) {
    JSONObject jsonObject = jsonArray.optJSONObject(i);
    String name = jsonObject.optString("name");
    int age = jsonObject.optInt("age");
}
```

## Gson/FastJson开源框架的使用

### GSON

GSON库将一段JSON格式的字符串自动映射成一个对象，从而不需手动去编写代码进行解析了

Gson提供了`fromJson()` 和`toJson()` 两个直接用于解析和生成的方法，前者实现反序列化，后者实现了序列化

##### 基本数据类型的解析

```java
Gson gson = new Gson();
int i = gson.fromJson("100", int.class);              //100
double d = gson.fromJson("\"99.99\"", double.class);  //99.99
boolean b = gson.fromJson("true", boolean.class);     // true
String str = gson.fromJson("String", String.class);   // String
```

##### 基本数据类型的生成

```java
Gson gson = new Gson();
String jsonNumber = gson.toJson(100);       // 100
String jsonBoolean = gson.toJson(false);    // false
String jsonString = gson.toJson("String"); //"String"
```

##### 生成JSON

```java
Gson gson = new Gson();
User user = new User("怪盗kidou",24);
String jsonObject = gson.toJson(user); // {"name":"怪盗kidou","age":24}
```

##### 解析JSON

```java
Gson gson = new Gson();
String jsonString = "{\"name\":\"怪盗kidou\",\"age\":24}";
User user = gson.fromJson(jsonString, User.class);
```

### Fastjson

##### 生成Json：

JavaBean、List\<JavaBean>、List\<String>、List<Map<String,Object>>

```java
String jsonString = JSON.toJSONString(obj);
```

##### 解析为Bean

```java
public static final <T> T parseObject(String text, Class<T> clazz) {
        return parseObject(text, clazz, new Feature[0]);
}
```

参数第一个是json的字符串；第二个是对应Bean的类

##### 解析为List\<Bean>

```java
public static final <T> List<T> parseArray(String text, Class<T> clazz) {...}
```