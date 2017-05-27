少爷，之前在讲struts2的jar包的时候还记不记得有个ognl-xxx.jar的jar包啊。
“恩，记得，但是福伯你当时只是说是OGNL表达式，没有说这个表达式的jar包是做什么的。”
好吧，今天我们就开始学习修炼OGNL。
什么是OGNL呢？
>OGNL是Object Graphic Navigation Language(对象图导航语言)的缩写，他是一个单独的开源项目。Struts框架使用OGNL作为默认的表达式语言。

使用的前提：
**OGNL是struts2整合的一个开源项目，所以在struts2中，要想使用OGNL表达式，必须使用Struts2标签库。**

我们在开始OGNL之前先了解下EL表达式 **(EL表达式只能支持静态方法调用)**
EL表达式的具体步骤：
1. 编写一个普通类，提供一个实现功能的静态方法
```java
	public class MyFunction {
		public static String toUpperCase(String str){
			return str.toUpperCase();
		}
	}
```
2. 在WEB-INF目录中创建一个扩展名为.tld的xml文件 注意文件不能放在classes和lib目录
![.tld文件位置](http://i.imgur.com/hZkHznh.png)
首先copy。修改需要修改的。
short-name:是在jsp中使用的对应的taglib的prefix
uri:是将当前库绑定在一个uri地址上
function-->name方法名称，在jsp页面上使用的名称
function-->function-class方法名称，指定执行的自定义类名
function-->function-signature指定执行的方法。方法名称必须和类中的方法名称保持一致(当方法有参数和返回值时，参数值和返回值必须写类全名，除了基本数据类型之外)
```
<?xml version="1.0" encoding="UTF-8"?>
<taglib xmlns="http://java.sun.com/xml/ns/j2ee"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-jsptaglibrary_2_0.xsd"
    version="2.0">
      <description>A tag library exercising SimpleTag handlers.</description>
    <tlib-version>1.0</tlib-version><!-- 指定标签库或版本库的版本号 -->
    <short-name>myfn</short-name><!-- 使用的短名称，对应的是taglib中的prefix -->
    <!-- 把当前的方法库绑定到一个uri地址上，在该网址上不一定存在方法库 -->
    <uri>http://www.wg.com/myfunction</uri>
    <function>
        <name>toUpperCase</name><!-- 方法名称。在jsp页面上使用的名称 -->
        <!-- 指定执行的自定义方法类 -->
        <function-class>com.wg.javaee.web.MyFunction</function-class>
        <!-- 指定执行的方法。方法名称必须和类中的方法名称保持一致 
        	当方法有参数和返回值时，参数值和返回值必须写类全名，除了基本数据类型之外
        -->
        <function-signature>java.lang.String toUpperCase(java.lang.String )</function-signature>
    </function>
</taglib>
```
3. 在jsp页面中使用taglib指令引入外部的标签库或者方法库
```
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://www.wg.com/myfunction" prefix="myfn"%>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>ONGL-Expression</title>
</head>
<body>
	<%--el表达式 ${}
		jsp表达式 <%=%>
	 --%>
	<%-- EL表达式：只能调用静态方法 --%>
	  abcdefg--->ABCDEFG
	  <br/>
	  ${myfn:toUpperCase("abcdefg")}
</body>
</html>
```
运行结果：
![使用EL表达式进行大小写转换](http://i.imgur.com/JryvJIq.png)
**注意：EL表达式只能是静态方法**

***

接下来我们开始我们的ONGL表达式啦，少爷，准备好了没，我们开始发车咯~
ONGL不同于EL表达式，OGNL表达式可以访问普通方法。这点要记住啊，少爷。
首先我们要根据uri去找对应的struts2标签库。

```
<%@ taglib uri="/struts-tags" prefix="s"%>
```
接下来呢，少爷应该怎么做？
“唔，既然引用了struts2标签库，接下来就是使用了~”
```
<%--OGNL表达式：它可以访问普通方法 
	OGNL表达式必须写在struts2的标签中
	s:property它就类似于jsp的表达式,把值输出到浏览器上
	value属性中的内容不再是我们看到的字符串，它是一个ognl表达式。必须知道	  
--%>
<s:property value="OGNL-Expression"/>这是一个OGNL表达式<br/>
```
![OGNL-Expression表达式](http://i.imgur.com/mB0zLvk.png)
那我们如何表示一个字符串呢，也很简单，只要在要显示的字符串外嵌套一层单引号**''**
```jsp
 <s:property value="'OGNL-Expression'"/>这是一个普通的字符串<br/>
```
![OGNL表示一个字符串](http://i.imgur.com/gMRbvMq.png)
接下来我们开始访问方法，上边我们提到OGNL可以访问普通方法，那我们访问一个普通方法，计算字符串长度length()
```
<s:property value="'OGNL-Expression'.length()"/>使用普通字符串调用了获取长度的方法<br/>
```
![OGNL调用普通方法](http://i.imgur.com/RM4lTys.png)
少爷，现在我们静下心来思考个问题，OGNL能调用普通方法，那他能调用静态方法么？
···
答案是肯定的。不过我们在调用静态方法之前，我们可以调用下静态属性，从中找出规律
```
<%-- OGNL访问静态属性 ：
     访问静态属性的方式：@全类名@静态属性名称
--%>
<s:property value="@java.lang.Integer@MAX_VALUE"/><br/>
```
![访问静态属性获取int最大值](http://i.imgur.com/Oya4P0v.png)
通过访问静态属性我们可以知道如何访问静态方法：由于struts2默认**禁用**静态方法调用，我们**需要提前开启**
在struts.xml中添加
```
<constant name="struts.ognl.allowStaticMethodAccess" value="true"></constant>
```
在jsp中使用：
```
 <%-- OGNL访问静态方法 ：
	  访问静态方法的方式：@全类名@静态方法名称
	  struts2的框架默认禁用静态方法调用，我们可以通过配置开启
	  struts.ognl.allowStaticMethodAccess=false把它改为true
--%>
<s:property value="@java.lang.Math@random()"/>
```

***

接下来我们开始修炼OGNL的应用了~
之前我们接触过radio，在struts2中给我们封装好了radio，使用list进行添加数据，这个就是OGNL表达式的应用。
OGNL可以操作map和list的：
```
<%--
	使用的是s:radio的标签创建list集合
	{}就相当于创建了一个list集合
	list属性中的取值：是一个ognl表达式
--%>
<s:radio name="gender" list="{'男','女'}"></s:radio><br/>
```
![OGNL操作list](http://i.imgur.com/RBgzS0Z.png)
通过浏览器显示源码：
![](http://i.imgur.com/5sLWMCq.png)
但是使用list进行post时，传递过去的数据的value是'男'，'女'这样的字符串。
但是在开发中我们几乎不会使用这样的方式的，我们要传递的value和展示的可能不会一样，比如传1表示男，0表示女。那么男、女就相当于key，1、0相当于value了，我们可以想一下什么样的数据类型才会有这样的key，value的对应关系呢？
“我知道了，是map。”
哈哈，是的，少爷有进步啊。就是map，我们可以使用OGNL进行操作map。
那么上边的男女的radio可以进行修改。
```
<%--使用s：radio创建一个map 
	#{}表示创建一个map
	1和0作为value给radio标签的value属性赋值
--%>
<s:radio name="genderMap" list="#{'1':'男','0':'女'}"></s:radio>
```
通过浏览器显示源码：
![](http://i.imgur.com/0yBgoFq.png)
通过源码我们可以看到list与map的value值是不一样的。
**{}表示list，#{}表示map**

***

现在我们要解决一个之前留下的小问题了，少爷，你还记得前几天学习的文件上传与下载么？
“记得啊，怎么了？有什么问题么？”
呵呵，当时写文件下载的时候我们固定写死了下载下的文件名：
```
<param name="contentDisposition">attachment;filename=photo.png</param>
```
“想起来了，那个时候确实是写死的。”
恩，我们现在学习了OGNL表达式之后就可以进行动态设置了。
首先我们在文件下载动作类中添加一个filename，set、get方法，我们在动作方法返回SUCCESS结果视图之前，给filename赋值"皂片.png"
```java
/**
 * 文件下载的动作类
 * @author asmin
 *
 */
public class DownLoadAction extends ActionSupport {
	//注意:再给InputStream指定名称时，不能使用in
	private InputStream inputStream;
	//创建一个文件名的属性
	private String filename;
	public String getFilename() {
		return filename;
	}
	public void setFilename(String filename) {
		this.filename = filename;
	}
	public String download() throws Exception{
		//1.知道文件在哪  找到文件的存储路径
		String filePath = ServletActionContext.getServletContext().getRealPath("WEB-INF/files/5.png");
		//2.把文件读到一个InputStream中
		inputStream = new FileInputStream(filePath);
		//再返回之前。在filename赋值
		filename="皂片.png";
		//3.返回成功
		return SUCCESS;
		//4.由一个叫stream的结果类型为我们把剩下的做完
	}

	public InputStream getInputStream() {
		return inputStream;
	}
	public void setInputStream(InputStream inputStream) {
		this.inputStream = inputStream;
	}
}
```
接下来我们就要使用OGNL表达式了。将原本的filename="photo.png"写成filename=${@java.net.URLEncoder@encode(filename,"UTF-8")}我们调用了静态方法，所以我们要在struts.xml中开启调用静态方法。
```
<action name="download" class="com.wg.javaee.action.DownLoadAction" method="download">
	<result name="success" type="stream">
		<!-- 给Stream的结果类型注入参数：Content-Type -->
		<param name="contentType">application/octet-stream</param>
		<!-- 使用OGNL表达式，动态获取文件名
			${@java.net.URLEncoder@encode(filename,"UTF-8")}把${}中间的作为OGNL表达式
		 -->
		<param name="contentDisposition">attachment;filename=${@java.net.URLEncoder@encode(filename,"UTF-8")}</param>
		<!-- 注入字节输入流:取值要写动作类中的set方法名称，首字母小写 -->
		<param name="inputName">inputStream</param>
	</result>
</action>
```
![使用ONGL表达式进行动态设置下载的文件名](http://i.imgur.com/H78bAHl.gif)