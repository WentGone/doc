少爷，今天我们开始修炼struts2的参数封装。首先要知道参数有静态参数和动态参数之分。
那么我们就先修炼静态参数的封装吧。
“好的福伯，我一定好好去学的，长大也想成为父亲那样伟大代码英雄”
好，好。少爷，你要努力啦，我们开始修炼。

#1.静态参数的封装
在struts.xml中使用依赖注入的方式对动作类参数赋值（动作类中set.get方法）
```
<param name="username">Went_Gone</param>
<param name="age">18</param>
```

#2.动态参数的封装
少爷，动态参数是JavaWeb开发中必须要用到的。那就让我们先了解一下动态参数，什么是动态参数呢，就是可以进行动态变化的不像上边那样在struts中写死的数据。而动态参数的封装有三种方式，哪三种呢~
##2.1 数据模型和动作类写在一起（即动作类中有数据模型）
例如：
```
public class Demo2Action extends ActionSupport {	
	//数据模型
	private String username;
	private int age;
	
	public String addUser(){
		System.out.println(username+","+age);
		return null;
	}
	public String getUsername() {
		return username;
	}
	public void setUsername(String username) {
		this.username = username;
	}
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}
}
```
使用此种封装方式，那么你在传递参数的时候需要表单中的name属性的取值必须和动作模型中set方法后边的名称一致。
如图：
![动态参数封装方式之一](http://i.imgur.com/ptCn3EP.png)

此方式简单易懂，但是在实际开发中，用的不是很多，局限性大，并且数据模型和动作类写在一个类中，不利于解耦管理。由此出现了动态参数封装的第二种方式。
##2.2 数据模型和动作类分别写在不同类中（即数据模型建立javabean）
例如：
```
动作类：
public class Demo3Action extends ActionSupport {
	//动作类中只有一个javabean对象。通过set.get方法进行管理
	private User user;
	
	public String addUser(){
		System.out.println(user.getUsername()+","+user.getAge());
		return null;
	}

	public User getUser() {
		return user;
	}

	public void setUser(User user) {
		this.user = user;
	}
}
所需要的数据模型：
public class User implements Serializable{
	private String username;
	private int age;
	public String getUsername() {
		return username;
	}
	public void setUsername(String username) {
		this.username = username;
	}
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}	
}
```
但是使用这个方法要***注意***：
表单中的name属性值必须是javabean的对象的属性
* 不是简单的username了，变成了user.username
还有动作类中set.get方法的执行顺序：
>get方法：struts2先调用get方法，判断对象是否存在，如果不存在，使用反射创建一个对象。
 set方法：不存在的时候，创建一个对象，把它set进去
 get方法：再调用get方法得到对象，调用set方法得到对象的各种属性，为属性赋值

##2.3使用数据模型驱动进行参数封装。
使用模型驱动进行参数封装时，数据模型必须和动作类方法不在同一个类中。
使用步骤如下：
	1. 动作类实现ModelDriven的接口
	2. 实现接口中的抽象方法getModel
	3. 使用模型驱动时，数据模型必须由我们自己实例化。即
	   private User user = new User();
此时在form表单中可以直接使用username进行参数传递了~
如图：
![动态参数封装方式之三](http://i.imgur.com/2OQ6faD.png)

少爷，这一块能消化的了么。
“福伯，还好啦，我再做个demo练习一下就好啦~”

完成参数封装后，再做项目的时候经常会遇到用户不按套路出牌的情况，比如填写时间的时候**2017-04-20**，用户非要写成**04/20/2017**形式，如果我们不进行转化，就会出现数据库无法保存甚至程序崩溃的情况。那么接下来我们就开始修炼数据类型的转化~
#3.数据类型转换
>struts2提供常用的类型转换
> 1.基本数据类型的自动转换  比如int
> 2.日期类型，默认按照本地日期格式转换（yyyy-MM-dd）
> 3.字符串数组类型，默认用逗号+空格(", ")，连接成一个字符串

比如上边提到的年月日的格式情况，这就需要少爷进行自定义类型转换器。那么如果进行自定义呢,哈哈，像struts2这么强大的工具怎么能没有数据转换的工具类呢。那么我们就以日期类型进行小型修炼。准备好了么，来喽

struts2中有一个StrutsTypeConverter的抽象类，我们通过继承此类，实现其中的convertFromString，convertToString这两个方法进行数据类型的转换。
```
/**
 * 需求：
 * 		把表单中MM/dd/yyyy的数据转成日期类型
 * 		把数据库中的本地日期格式转成MM/dd/yyyy形式输出
 * 
 * 自定义类型转化器
 * 		继承StrutsTypeConverter，实现方法
 * @author asmin
 *
 */
public class DateTypeConvertor extends StrutsTypeConverter {
	//定义一个类型转换器
	private DateFormat format = new SimpleDateFormat("MM/dd/yyyy");

	/**
	 * 把字符串数组数据中的数据转成日期类型
	 * 		参数：
	 * 			Map context:是OGNL的上下文对象，暂时不用
	 * 			String[] values:要转换的数据
	 * 			Class toclass:目标类型
	 */
	public Object convertFromString(Map context, String[] values, Class toClass) {
		//1.检查有无数据
		if(values==null || values.length == 0){
			return null;
		}
		//2.取出数组中的第一个元素
		String date = values[0];
		//3.判断目标类型的字节码是不是日期类型
		if(toClass == java.util.Date.class){
			//4.使用DateFormat进行转化，并且返回转换后的结果			
			try {
				return format.parse(date);
			} catch (ParseException e) {
				e.printStackTrace();
				return null;
			}
		}
		return null;
	}
	
	/**
	 * 把日期类型的数据转成字符串
	 *		 参数：
	 * 			Map context:是OGNL的上下文对象，暂时不用
	 * 			Object o:要转换的数据
	 */
	public String convertToString(Map context, Object o) {
		//1.判断object是不是日期类型
		if(o instanceof Date){
			Date date = (Date)o;
			//2.如果是日期类型，使用转换器转换成指定格式的字符串，并返回
			return format.format(date);
		}
		return null;
	}
}
```

“哈哈，我也会自定义数据转换类型了，太好了~”
少爷，不要高兴的太早，这仅仅是写好转换类了，真正起作用的是你要在哪使用，怎么去使用。
“啊，还有啊，好吧，我以为这就可以了呢”
接下来就是重点难点了，少爷你可看清楚了。

配置分为**局部类型转换器**与**全局类型转换器**，我们先说局部类型转换器
##3.1局部类型转换器
要在所要使用的实体类(即javaBean)同包下建一个properties。命名规则：**javabean的名称-conversion.properties** ，它的内容呢就是键值对的形式存在的，你所使用的属性名作为key，而转换器的全类名作为value，如图：
![局部类型转换器](http://i.imgur.com/prZ9yHW.png)

##3.2全局类型转换器
少爷，考你一下，局部类型转换器配置文件放置在所使用的的javabean的包下，那全局的应该放在哪呢？
“我想想，恩...，局部放在里边，那全局就放在外边呗，放在类路径的根路径。”

是的，很聪明啊，全局类型转换器就是放在**类路径的根路径**，但是注意，他的命名规则是固定的，写死的“**xwork-conversion.properties**”，它同样是键值对的形式存在的，这时候你不能使用属性名了，因为你不可能在项目中将所有的要使用该转换器的属性都叫成一个名字，所以这里要用**使用的数据类型（全类名）**作为key，而转换器的全类名作为value，如图：
![全局类型转换器](http://i.imgur.com/Jma50pA.png)

少爷，还要记住一点，我们在开发过程中，推荐全局类型转换器。
“好的，福伯，我记住了！”

最终运行结果：
![数据类型转换器——结果](http://i.imgur.com/1CuP6XL.png)

少爷，你觉得有了类型转换器之后是不是开发方便了很多啊。
“是的福伯，简便快捷了，这样我就不用为了数据类型发愁了~”
错了少爷，我们不可能在一个项目中将所有的数据类型转换器写的那么完善的，所以我们还要解决当数据类型转换出异常时我们的解决方法。记住，我们给用户的永远不能是Struts Problem Report...
##3.4数据类型转换失败后的处理
这时候我们要定义一个input的结果视图进行回到原来的视图结果。
使用struts2的标签库：
>1. 首先导入struts2的标签库。
```jsp
<%@taglib uri="/struts-tags" prefix="s" %>
```
>2. 使用struts2的标签进行标记错误字段及动作类错误。
```jsp
<s:actionerror/><%-- 动作错误 --%>
<s:fielderror/><%-- 字段错误 --%>
```
>3. 使用struts2的标签进行文本框及form表单的替换
```jsp
<%-- struts2的form标签，它提供了和原始html表单标签几乎一致的属性 
		action:请求地址，可以直接写动作名称，不用写前边的contextPath
		method:请求方式，在这里不需要写，struts2的form表单默认是post
		enctype:表单编码的MIME类型
	--%>
	<s:form action="register">
		<s:textfield name="username" label="用户名"/>
		<s:password name="password" label="密码"/>
		<s:textfield name="birthday" label="生日"/>
		<s:checkboxlist name="hobby" label="爱好" list="{'吃饭','篮球','代码'}"/>
		<s:radio list="#{true:'已婚',false:'未婚'}" name="married" value="true" lable="婚否"/>
		<s:submit value="注册"/> 
	</s:form>
```

#4.数据验证
数据的校验在开发中是由服务器+客户端共同完成的，这样可以减少客户端访问服务器的次数。但是不论怎样，服务器的校验是绝对不能少的，这个必须要铭记在心啊少爷。
“福伯。我知道了客户端可以通过检验是否是null进行判断，那服务器怎么判断呢，难道也是根据null或者""来判断么？”
![](https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=3472912723,2972765062&fm=23&gp=0.jpg)
哈哈,自有方法。

我们可以去看一下struts2的源码，会发现里边提供了一个Map用来建立错误提示。那么让我们看看该怎样用呢~

两种用法哦~：
##4.1 编程式验证
什么叫编程式验证呢，就是重新struts2的验证方法validate。通过struts2的long包，进行判断是否是null和""，然后通过addFiledError("字段名","错误信息");来进行存储某个字段的额错误信息。

```java
/**
	 * 在struts2的框架中，也提供了一个Map<表单的字段名，错误提示>
	 * 我们要做的：
	 * 		往Map中存放错误信息提示
	 * 
	 * 编程式验证：
	 * 		1.动作类必须继承ActionSupport
	 * 		2.重写validate方法
	 * 
	 * validate方法在动作方法执行之前进行验证
	 * 
	 * 当重写validate方法，他会对动作类中的所有的动作方法进行验证。
	 * @return
	 */
	public void validate(){
		if(StringUtils.isBlank(user.getUsername())){
			//存入错误信息,直接调用父类的addFieldError方法，存入错误信息，第一个参数是表单name属性的值，第二个参数是错误提示。
			addFieldError("username","请输入用户名");
		}
	}
```
代码中也有提到，这种编程式验证会对所有的动作方法进行验证，那当我们不想对某个方法进行验证，但是其他动作方法都需要进行验证该怎么办呢，少爷，可以使用以下两种跳过验证的方法：
```
第一种方式：在想要跳过的动作方法前使用@SkipValidation
	@SkipValidation
	public String findAll(){
		return SUCCESS;
	}
第二种方式：定义验证方法的名称：validate+动作名称注意大小写问题  此时去掉validate方法
	public String validateAddUser(){
	}
```
但是编程式验证的弊端也显现出来了，硬编码，不灵活。

##4.2 声明式验证
少爷，这个声明式验证是在开发中经常被用到的验证方式，因为需要验证谁就写谁，简单方便。
通过编写验证规则的xml文件，需要验证时编写xml文件，不需要时不编写。
解决了编程式编码的弊端。
	1.针对动作类中所有动作进行验证，在动作类所在的包下，建立一个**ActionClassName-validation.xml**文件：
```
			<?xml version="1.0" encoding="UTF-8"?>
				<!DOCTYPE validators PUBLIC 
					"-//Apache Struts//XWork Validator 1.0.3//EN"
					"http://struts.apache.org/dtds/xwork-validator-1.0.3.dtd">
				<!-- 当使用ActionClassName-validation.xml命名文件时，它是一个动作类（所有动作方法）的验证器。 -->

			<validators>
			<!-- 基于字段的声明式验证 -->
			<!-- field中name属性指定的是表单中name属性的取值 -->
				<field name="username">
				<!-- struts2的框架集成了很多内置验证器  requiredstring会验证输入内容是否为空、空字符串，去左右空格 -->
					<field-validator type="requiredstring">
						<message>用户名不能是空白~</message>
					</field-validator>
				</field>
			</validators>
```	
少爷，我在上边写了一个注释，基于字段的声明验证，你知道是为什么吗？
“唔···,还有其他的验证方式，但是我不知道还有什么样的验证方式”
呵呵，有长进。
还有一种是叫做基于验证器的验证方式
```
	<!-- 基于验证器验证 -->
	<validator type="requiredstring">
		<!-- 以注入的方式，提供要验证的字段的信息
			setFieldName("password");
		-->
		<param name="fieldName">password</param>
		<message>密码不能为空~</message>
	</validator>
```

同样可使用SkipValidation进行跳过验证，这可是通用的。
	2.针对动作类中的某个动作进行验证：在动作类所在的包下，建立一个**ActionClassName-ActionName-validation.xml**,这里的ActionName**不是**动作方法名称，**是**struts.xml中的对应的方法名

少爷，容属下展示下两种方式的示意图：
![声明式数据验证](http://i.imgur.com/MQHxZM9.png)
少爷，这段时间您修炼成功了动态参数封装和验证器的使用手册，可喜可贺啊。
“是啊，不过这才刚开始呢，不说了，灵儿还等着我一块逛街去呢，走啦，福伯~，明天我会准时来修炼的。”