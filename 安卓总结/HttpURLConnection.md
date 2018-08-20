# 二十 HttpURLConnection

## HttpURLConnection的使用步骤

使用HttpURLConnection的步骤如下：

 - 创建一个URL对象： **URL url = new URL(http://www.baidu.com);**
 - 调用URL对象的`openConnection()`来获取HttpURLConnection对象实例： 
     **HttpURLConnection conn = (HttpURLConnection) url.openConnection();**
 - 设置HTTP请求使用的方法:GET或者POST，或者其他请求方式比如：PUT **conn.setRequestMethod("GET");**
 - 设置连接超时，读取超时的毫秒数，以及服务器希望得到的一些消息头 **conn.setConnectTimeout(6\*1000);** **conn.setReadTimeout(6 \* 1000);**
 - 调用`getInputStream()`方法获得服务器返回的输入流，然后输入流进行读取
    **InputStream in = conn.getInputStream();**
 - 最后调用disconnect()方法将HTTP连接关掉 **conn.disconnect();**

有时我们还可能需要对**响应码**进行判断，比如：`if(conn.getResponseCode() != 200)`。并且可能有时我们只需要直接访问一个页面，可以用： `final InputStream in = new URL("url").openStream();` ，然后直接读流。

## HttpURLConnection的常用方法

```java
// 设置连接超时时间
connection.setConnectTimeOut(15000);

// 设置读取超时时间
connection.setReadTimeOut(15000);

// 设置请求参数，即具体的 HTTP 方法
connection.setRequestMethod("POST");

// 添加 HTTP HEAD 中的一些参数，可参考《Java 核心技术 卷II》
connection.setRequestProperty("Connection", "Keep-Alive");

// 设置是否向 httpUrlConnection 输出，
// 对于post请求，参数要放在 http 正文内，因此需要设为true。
// 默认情况下是false;
connection.setDoOutput(true);

// 设置是否从 httpUrlConnection 读入，默认情况下是true;
connection.setDoInput(true);

// 这两个方法结合，可以查询所有消息头字段
public String getHeaderFieldKey (int n)
public String getHeaderField(int n)

// 返回一个包含消息头所有字段的标准map对象
public Map<String,List<String>> getHeaderFields()

// 为了方便使用，以下方法可以查询各标准字段
public String getContentType()
public int getContentLength()
public String getContentEncoding()
public long getDate()
public long getExpiration()
public long getLastModified()
```

#### 利用 getOutputStream() 传输 POST 消息

```java
  BufferedWriter writer = new BufferedWriter(new OutputStreamWriter(connection.getOutputStream(), "UTF-8"));
  writer.write("ip=xxx.xxx.xxx.xxx");
  writer.flush();
  writer.close();
```

#### 利用 getInputStream() 访问资源数据 

#### 利用 getResponseCode() 获取状态码

#### 调用 connection.disconnect() 关闭 HttpURLConnection