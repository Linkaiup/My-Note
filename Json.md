# Json

#### 1、解析方式

- 数组、集合、类等方式的解析方法。包装进工具类方便以后使用。

#### 2、Gson的解析方式

```java
//获得所传进来的流
BufferedReader br=new BufferedReader(new InputStreamReader(request.getInputStream()));
String line="";
String line2="";
//StringBuffer line2="";
while((line=br.readLine())!=null){
  line2+=line;
  //line2.append(line);
}
Gson gson = new Gson();
//解析类
User user = gson.fromJson(line2,User.class);
//将对象转换为 json 对象传回
String json = gson.toJson(user);
PrintWriter pw = response.getWriter();
pw.print(json);
```

#### 3、数组、List、Map 等解析方法参见 jsptest 的 JsonUtil

#### 4、JSON注意事项

廖雪峰文章：Google 浏览器工具文件夹下

#### 5、JSON Schema

- 数据交换中的一种虚拟 “合同” 。
- 负责提供一种一致性验证，是数据接收方的第一道防线，也是数据发送方节约时间保证数据正确的好工具。
- 可以验证数据类型，范围，那些数据是必须的。

#### 6、JSON的安全问题

1. 跨站请求伪造 ( CSRF )

   - 如果使用顶层JSON数组，则是合法的JavaScript，黑客可以在你登录银行网站之后，向你发送各种假冒银行链接的信息，当你点开时，你的账户信息就会以`<script>`的方式被黑客拿取到。所以需要将数组放到对象之中，使之成为非法的JavaScript，这样就不会被`<script>`标签加载了。然后，银行再仅允许POST请求获取数据，禁止GET请求，这样黑客就无法使用他自己的URL中的链接了。

   - >因为如果服务器允许GET请求，就可以直接通过浏览器或`<script>`标签链接到它。但POST不会。

   - 所以要养成不要使用顶级数组，不要贪图用GET代替POST的便利。

2. 注入攻击

   - ​





