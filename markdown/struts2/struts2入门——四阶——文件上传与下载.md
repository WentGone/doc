啊，少爷，灵儿小姐送来的灵泉酒年份很足，酒香四溢啊，我都舍不得让她走了。少爷要不要娶了灵儿小姐啊，很般配的。
“福伯，你说什么呢，灵儿对我来说就是妹妹，我不允许她在这个世界被人欺负，再说我还没有成为父亲那样的代码英雄，怎么可能娶亲。”
好啦好啦，是老奴说错话了，那我们今天开始修炼文件的上传与下载吧，之前使用servlet的时候学过，但是这次使用struts2的工具进行开发，会更方便快捷。
#1.文件上传
首先我们要建立一个jsp页面，里边有上传的文件选择器。
```
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="/struts-tags" prefix="s"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>struts2文件上传</title>
</head>
<body>
	<%--
		文件上传的必要前提：
			1.请求方式必须是post
			2.enctype属性的取值必须是multipart/form-data
			3.提供一个文件选择域
	 --%>
	 <s:actionerror/>
	 <s:form action="upload" enctype="multipart/form-data">
	 	<s:textfield name="username" label="用户名"/>
	 	<s:file name="photo" label="文件"/>
	 	<s:submit value="上传"/>
	 </s:form>
</body>
</html>
```
![jsp显示](http://i.imgur.com/utUoVZ4.png)
“福伯，我已经写了好jsp了，我建立一个动作方法。”（struts2在文件上传的时候，会赋予两个属性**上传字段名称+FileName**，**上传字段名称+ContentType**）
```
/**
 * 照片实体类
 * @author asmin
 *
 */
public class PhotoBean implements Serializable {
	private String username;
	private File photo;
	//struts2在文件上传时提供的属性
	private String photoFileName;//上传的文件名 上传字段名称+FileName 注意大小写
	private String photoContentType;//上传文件的MIME类型 上传字段名称+ContentType 注意大小写

	public String getPhotoFileName() {
		return photoFileName;
	}
	public void setPhotoFileName(String photoFileName) {
		this.photoFileName = photoFileName;
	}
	public String getPhotoContentType() {
		return photoContentType;
	}
	public void setPhotoContentType(String photoContentType) {
		this.photoContentType = photoContentType;
	}
	public String getUsername() {
		return username;
	}
	public void setUsername(String username) {
		this.username = username;
	}
	public File getPhoto() {
		return photo;
	}
	public void setPhoto(File photo) {
		this.photo = photo;
	}
}

/**
*动作方法
*/
public class UpLoadAction extends ActionSupport implements ModelDriven<PhotoBean> {
	private PhotoBean bean = new PhotoBean();
	
	public String upload(){
		return null;
	}

	@Override
	public PhotoBean getModel() {
		return bean;
	}
}
```
少爷，你的动作类只是写了最基本的，那我们改如何在动作方法中接受我们上传过来的文件呢？
“记得以前是通过获得ServletContext来获得虚拟路径，然后获取真实路径来进行文件的存储的。”
那么在动作方法upload中
>1.拿到ServletContext对象
>2.通过ServletContext对象调用realPath方法，根据一个虚拟目录得到真实目录
>3.我们要进行判断真实目录是否存在，如果不存在则需要创建。
>4.文件的存储是有缓存的。
	* 当文件超过10KB时，使用磁盘临时文件作为缓存。
	* 当文件不足10KB时，使用内存作为缓存。
>5.将获得的文件从缓存中剪切到指定的真实目录中。  

```
public String upload(){
		//1.拿到一个ServletContext对象
		ServletContext application = ServletActionContext.getServletContext();
		//2.调用realPath方法，根据一个虚拟目录得到的真实目录
		String filePath = application.getRealPath("/WEB-INF/files");
		//3.如果真实目录不存在，需要创建
		File file = new File(filePath);
		if(!file.exists()){
			file.mkdirs();
		}
		//4.把File存储（超过10KB，使用磁盘临时文件作为缓存，不足10KB，使用内存作为缓存）
		//拷贝：把上传的file的临时文件复制到指定位置，临时文件还是会在
/*		try {
			FileUtils.copyFile(bean.getPhoto(), new File(file,bean.getPhotoFileName()));
		} catch (IOException e) {
			e.printStackTrace();
		}*/
		//剪切：把上传的file的临时文件剪切到指定位置，并给其重命名。临时文件消失
		bean.getPhoto().renameTo(new File(file,bean.getPhotoFileName()));
		//去发布目录中寻找
		return null;
	}
```
那么结果是怎样的？
![上传文件的演示](http://i.imgur.com/9M4icr9.gif)
少爷，你仅仅是传了一张小图片而已，你传一个大一点的文件试试。
“咦，找不到网页···”
呵呵，那是因为struts2有个默认的上传大小的限制。在default.properties中有个**struts.multipart.maxSize=2097152**，这句话的意思是，struts最大的文件上传支持2M。
“(⊙o⊙)…，那福伯，我知道该怎么办了，重写default.properties中的这个属性”
```
<constant name="struts.multipart.maxSize" value="5242880"></constant>
```
恩恩，孺子可教啊~

那在正常的开发过程中，不一定全是一个文件的上传，可能是多个文件的上传，此时应该如何处理呢？
很简单，参考checkbox，多个参数的时候使用数组分割开。
#2.多文件上传
在实体类中
```
public class PhotoBeanArr implements Serializable {
	private String username;
	private File[] photo;
	//struts2在文件上传时提供的属性
	private String[] photoFileName;//上传的文件名 上传字段名称+FileName 注意大小写
	private String[] photoContentType;//上传文件的MIME类型 上传字段名称+ContentType 注意大小写
	public String getUsername() {
		return username;
	}
	public void setUsername(String username) {
		this.username = username;
	}
	public File[] getPhoto() {
		return photo;
	}
	public void setPhoto(File[] photo) {
		this.photo = photo;
	}
	public String[] getPhotoFileName() {
		return photoFileName;
	}
	public void setPhotoFileName(String[] photoFileName) {
		this.photoFileName = photoFileName;
	}
	public String[] getPhotoContentType() {
		return photoContentType;
	}
	public void setPhotoContentType(String[] photoContentType) {
		this.photoContentType = photoContentType;
	}
}
```
在动作方法中，本来是剪切一个文件，现在多个文件，那我们就使用数组for循环进行剪切文件：
```
for (int i = 0; i < bean.getPhoto().length; i++) {
	bean.getPhoto()[i].renameTo(new File(file,bean.getPhotoFileName()[i]));			
}
```
多文件的上传：
![多文件上传](http://i.imgur.com/VVzS2GG.gif)
好了，struts2的文件上传的问题基础讲解完毕，少爷可懂多少？
“不算多，也很简单，所以练习几下就毁的差不多了。”
好，那我们开始接下来的，有文件上传那么肯定就有文件下载。我们接下来开始修炼文件下载。
#3.文件下载
文件下载有这么几步：
文件存储的路径--->inputStream--->返回成功--->struts2中使用stream结果视图为我们做剩下的工作
怎么让stream结果视图知道我们用的哪个输入流呢
我们就需要写一个属性InputStream类型的，但是名称不能使用in。
```
/**
 * 文件下载的动作类
 * @author asmin
 *
 */
public class DownLoadAction extends ActionSupport {
	//注意:再给InputStream指定名称时，不能使用in
	private InputStream inputStream;
	
	public String download() throws Exception{
		//1.知道文件在哪  找到文件的存储路径
		String filePath = ServletActionContext.getServletContext().getRealPath("WEB-INF/files/5.png");
		//2.把文件读到一个InputStream中
		inputStream = new FileInputStream(filePath);
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
而在配置文件中：我们要使用struts2给我们提供的stream的结果视图。使用注入参数的方式将Content-Type、Disposition注入。还有我们如何指定我们动作方法中使用的文件流呢，我们使用inputName参数进行注入：
取值是动作类中的set方法名称，首字母小写。
```xml
<action name="download" class="com.wg.javaee.action.DownLoadAction" method="download">
	<result name="success" type="stream">
		<!-- 给Stream的结果类型注入参数：Content-Type -->
		<param name="contentType">application/octet-stream</param>
		<!-- 告知客户浏览器以下载的方式打开 -->
		<param name="contentDisposition">attachment;filename=photo.png</param>
		<!-- 注入字节输入流:取值要写动作类中的set方法名称，首字母小写 -->
		<param name="inputName">inputStream</param>
	</result>
</action>
```
![文件下载的结果gif图~](http://i.imgur.com/YleMwOc.gif)
少爷，至此我们就修炼完了文件的上传与下载了。平时要做练习啊~