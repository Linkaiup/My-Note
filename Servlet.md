## 	7/23、应用程序事件、监听器

#### 一、序

*关闭浏览器时会马上失效的是浏览器上的Cookie，而不是HttpSession。*

可以执行HttpSession的setMaxInactiveInterval()方法，设置浏览器在多久没有请求应用程序的情况下，HttpSession就会自动失效，设置的单位是”秒”。也可以在web.xml中设置HttpSession默认的失效时间，但要注意的时，这里设置的时间单位是”分钟”。例如：

```xml
<web-app ...>
    <session-config>
        <session-timeout>30</session-timeout>
    </session-config>
</web-app>
```

*保存Session ID的Cookie被设置为关闭浏览器就失效。关闭浏览器后若希望保存信息，必须通过自行操作Cookie来达成，例如完成自动登录机制。*



**session存放在哪里：服务器端的内存中。不过session可以通过特殊的方式做持久化管理。session的创建和使用总在服务端，而浏览器从来都没得到过session对象。但浏览器可以请求Servlet（jsp也是Servlet）来获取session的信息。客户端浏览器真正紧紧拿到的是session ID，而这个对于浏览器操作的人来说，是不可见的，并且用户也无需关心自己处于哪个会话过程中。**



#### 二、Servlet、ServletConfig、GenericServlet

#### 三、监听器

##### A、ServletContext事件、监听器

1. ServletContextListener
2. ServletContextAttributeListener

##### B、HttpSession事件、监听器

与其相关的监听器有四个：HttpSessionListener/HttpSessionAttributeListener/HttpSessionBindingListener/HttpSessionActivationListener

1. HttpSessionListener

   如果想在 HttpSession 对象创建或结束时做相应动作，可实现 HttpSessionListener

2. HttpSessionAttributeListener

   “属性改变监听器”，当在会话对象中加入属性，移除属性或交换属性时，相对应的方法就会被调用，并分别传入HttpSessionBindingEvevt。

3. HttpSessionBindingListener

   对象绑定监听器

##### C、HttpServletRequest事件、监听器

1. ServletRequestListener

   在 HttpServletRequest 对象生成或结束时做相应动作，可以实现 ServletRequestListener。

2. ServletRequestAttributeListener

   属性改变监听器

##### 四、过滤器种类

1. 用户授权
2. 日志 Filter
3. 负责解码的 Filter
4. 改变 XML 内容的 XSLT Filter 

```java
@WebFilter(filterName="log", urlPatterns={"/*"}, initParams={ //这里使用注解的方式配置Filter
　　@WebInitParam(name="encoding" value="UTF-8"),
　　@WebInitParam(name="loginPage"value="/login.jsp")
}) 
public class LogFilter implements Filter {
　　private FilterConfig config;//FilterConfig可用于访问Filter的配置信息
　　public void init(FilterConfig config) 
  { this.config = config; }
　　public void destroy(){this.config = null;}
　　public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain) throws IOException, ServletException{
　　　　//下面的代码用于对用户请求执行预处理
ServletContext context = this.config.getServletContext();//获取ServletContext对象，用于记录日志
　　　　long before = System.currentTimeMillis();
　　　　System.out.println("开始过滤...");
　　　　HttpServletRequest request = (HttpServletRequest)req;
　　　　System.out.println("已经拦截到用户的请求地址：" + request.getServletPath());
　　　　chain.doFilter(req, resp);//Filter只是链式处理，请求依然放行到目的地址
 
　　　　//下面的代码用于对服务器响应执行后处理
　　　　long after = System.currentTimeMillis();
　　　　System.out.println("过滤结束");
　　　　System.out.println("请求被定位到：" + request.getRequestURI() + " 耗时：" + (after - before));//这里只是输出提示信息
　　}
}
```

##### 五、文件上传下载

1. JSP/Servlet 实现文件上传

```java
upload.setProgressListener(listener);// 注册监听器
// 解决上传文件名 乱码问题
upload.setHeaderEncoding("utf-8");
// 限制上传文件大小
upload.setFileSizeMax(1024 * 1024 * 40);

try{
  List<FileItem> list=new ServletFileUpload(new DiskFileItemFactory()).parseRequest(request);
  //遍历 FileItem 集合
  for(FileItem fileItem : list){
    //判断每个FileItem 是不是文件上传项
    if(fileItem.isFormField()){
      //不是文件上传项
      String name = fileItem.getFileName();
      String value = fileItem.getString("utf-8");
      System.out.println("普通form项：" + name + "----" + value);} 
    else {// 是上传文件
    String filename = fileItem.getName(); // 文件名
     // 解决老版本浏览器IE6 文件路径存在问题
   if (filename.contains("\\")) {
    filename = filename.substring(filename    		.lastIndexOf("\\") + 1);
   } InputStream in = new      						BufferedInputStream(fileItem.getInputStream());     // 文件内容
  System.out.println("文件上传项：" + filename);
// 保证上传文件名唯一
 filename = UUID.randomUUID().toString() + filename;

  // 生成随机目录
 String randomPath =  				 				UploadUtils.generateRandomPath(filename);
 // 生成目录不一定存在 ---创建
File path= new File(getServletContext().getRealPath(
 "/WEB-INF/upload" + randomPath));
   path.mkdirs();

//第一种方法
fileItem.write(new File(path,filename));
      
fileItem.delete();
   }
 }
}
```

文件上传监听器：

```java
ProgressListener Listener = new ProgressListener(){
  @Override
  public void update(long PbYTESread, long pContentLength, int pItems){
    .....
  }
};
```

2. 模拟Post 请求/Servlet 实现文件上传

假如客户端不是webapp项目呢。可以通过HttpClient 来模拟浏览器发送上传文件的请求。

```java
import java.io.BufferedReader;
import java.io.File;
import java.io.InputStream;
import java.io.InputStreamReader;

import org.apache.commons.httpclient.HttpClient;
import org.apache.commons.httpclient.HttpStatus;
import org.apache.commons.httpclient.methods.PostMethod;
import org.apache.commons.httpclient.methods.multipart.FilePart;
import org.apache.commons.httpclient.methods.multipart.MultipartRequestEntity;
import org.apache.commons.httpclient.methods.multipart.Part;
import org.apache.log4j.Logger;

public class FileLoadClient {
	
	private static Logger logger = Logger.getLogger(FileLoadClient.class);
	
	public static String fileload(String url, File file) {
		String body = "{}";
		
		if (url == null || url.equals("")) {
			return "参数不合法";
		}
		if (!file.exists()) {
			return "要上传的文件名不存在";
		}
		
		PostMethod postMethod = new PostMethod(url);
		
        try {
        	
            // FilePart：用来上传文件的类,file即要上传的文件
            FilePart fp = new FilePart("file", file);
            Part[] parts = { fp };

            // 对于MIME类型的请求，httpclient建议全用MulitPartRequestEntity进行包装
            MultipartRequestEntity mre = new MultipartRequestEntity(parts, postMethod.getParams());
            postMethod.setRequestEntity(mre);
            
            HttpClient client = new HttpClient();
            // 由于要上传的文件可能比较大 , 因此在此设置最大的连接超时时间
            client.getHttpConnectionManager().getParams() .setConnectionTimeout(50000);
            
            int status = client.executeMethod(postMethod);
            if (status == HttpStatus.SC_OK) {
                InputStream inputStream = postMethod.getResponseBodyAsStream();
                BufferedReader br = new BufferedReader(new InputStreamReader(inputStream));
                
                StringBuffer stringBuffer = new StringBuffer();
                String str = "";
                while ((str = br.readLine()) != null) {
                    stringBuffer.append(str);
                }
                
                body = stringBuffer.toString();
                
            } else {
            	body = "fail";
            }
        } catch (Exception e) {
            logger.warn("上传文件异常", e);
        } finally {
            // 释放连接
            postMethod.releaseConnection();
        }
		
		return body;
	}
	
	
	public static void main(String[] args) throws Exception {
		String body = fileload("http://localhost:8080/jsp_upload-servlet/fileload", new File("C:/1111.txt"));
		System.out.println(body);
	}
	
}
```

3. Java 实现多线程下载

```java
import java.io.File;
import java.io.IOException;
import java.io.InputStream;
import java.io.RandomAccessFile;
import java.net.HttpURLConnection;
import java.net.MalformedURLException;
import java.net.URL;


public class MultiThread {
	public static void main(String[] args) {
		//文件下载路径
String filePath ="http://dldir1.qq.com/qqfile/qq/QQ2013/QQ2013Beta2.exe";
//文件保存路径
	String destination = "D:";
	//打算开启的线程数
	int threadNum = 5;
    new MultiThread().download(filePath, destination, threadNum);
    }

	/**
	 * 下载文件
	 */
private void download(String filePath, String destination, int threadNum) {
		try {
			//通过下载路径获取连接
			URL url = new URL(filePath);
HttpURLConnection conn = (HttpURLConnection) url.openConnection();
			//设置连接的相关属性
			conn.setRequestMethod("GET");
			conn.setReadTimeout(5000);
			//判断连接是否正确。
	if (conn.getResponseCode() == 200) {
				// 获取文件大小。
		int fileSize = conn.getContentLength();
				//得到文件名
		String fileName = getFileName(filePath);
				//根据文件大小及文件名，创建一个同样大小，同样文件名的文件
File file = new File(destination + File.separator + fileName);
RandomAccessFile raf = new RandomAccessFile(file, "rw");
	raf.setLength(fileSize);
	raf.close();
				// 将文件分成threadNum = 5份。
int block = fileSize % threadNum == 0 ? fileSize / threadNum
						: fileSize / threadNum + 1;
for (int threadId = 0; threadId < threadNum; threadId++) {
					//传入线程编号，并开始下载。
		new DownloadThread(threadId,block, file, url).start();
				}

			}
		} catch (MalformedURLException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}

	//由路径获取文件名。
private String getFileName(String filePath) {
		return filePath.substring(filePath.lastIndexOf('/') + 1);
	}

}

//文件下载线程
class DownloadThread extends Thread {
int start, end,threadId;
	File file = null;
	URL url = null;

public DownloadThread(int threadId,int block, File file, URL url) {
		this.threadId = threadId;
		start = block * threadId;
		end = block * (threadId + 1) - 1;
		this.file = file;
		this.url = url;
	}

public void run() {
try {
//获取连接并设置相关属性。
HttpURLConnection conn = (HttpURLConnection) url.openConnection();
	conn.setRequestMethod("GET");
	conn.setReadTimeout(5000);
			//此步骤是关键。
conn.setRequestProperty("Range", "bytes=" + start + "-" + end);
if (conn.getResponseCode() == 206) {
RandomAccessFile raf = new RandomAccessFile(file, "rw");
	//移动指针至该线程负责写入数据的位置。
	raf.seek(start);
	//读取数据并写入
	InputStream inStream = conn.getInputStream();
				byte[] b = new byte[1024];
				int len = 0;
	while ((len = inStream.read(b)) != -1) {
					raf.write(b, 0, len);
	}
		System.out.println("线程"+threadId+"下载完毕");
	}
} catch (IOException e) {
			e.printStackTrace();
		}
	}
}
```

##### 六、Servlet 的注意点

1. 在java文件中，使用out.print();将输出文件流给用户，即浏览器，而使用system.out.print(); 将输出文件流给服务器端，即运行tomcat的命令提示符窗口。这个在查错是很有用。
2. 在文件上传下载时需要注意编码问题

```java
response.setContentType(getServletContext().getMimeType(filename));
//根据文件扩展名获得 MIME 类型
response.setHeader("Content-Dispostion","attachment;filename=" + filename);//以附件下载
```