少爷，我们之前学会了servlet使用，但是想要在以后代码之路上行走，只会这最最基础的是根本不够的，想一下你做一个项目里边全是使用servlet进行分析各种参数，那样一是会累死，二一个别人接手你代码的时候看到这么多参数也会疯的。
“那福伯，我们应该怎么去开发javaweb项目呢，难道不用servlet？”
放心吧，java发展到现在，已经有很多成熟的框架供我们使用了，像struts2，springMVC，hibernate等。那么现在就让我们开始修炼struts2吧。
“好的，福伯。”

首先，我们来了解一下什么是框架及struts2。
##1. 框架概述
>把繁琐重复性的代码封装起来，使编码过程中把更多的精力放在业务需求的分析和理解上。

##2. 三层架构与Struts2
	1. 表现层：M model  V view  C control
	2. 业务层：service
	3. 持久层：dao(data access object)
        
![01Struts2.png](http://upload-images.jianshu.io/upload_images/2776669-74a7629c88799a01.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

	Struts2在三层架构中处于表现层即MVC，它只是一个表现层框架。

##3. MVC与Struts2
	控制器：Servlet
	过滤器：Filter 它也可以作为控制器
		init(FilterConfig)
		destory()
		doFilter(ServletRequest,ServletResponse,FilterChain)
	Servlet能做的Filter也都可以做，并且比Servlet功能更强大，它多了一个放行的功能。

##4. 搭建开发环境
	1. 下载开发包
	2. 搭建环境
		a. 导包
		b. 创建一个配置文件：
			在顶层类路径下，创建一个struts.xml文件
		c. 在web.xml中配置struts2的控制器

###5. Struts2的原理###

![05strtus2原理图.png](http://upload-images.jianshu.io/upload_images/2776669-56c69afbc356cdf3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###6. Struts2配置文件加载顺序
default.properties-->struts-default.xml-->plugin配置-->struts.xml-->struts.properties-->web.xml
如果遇到重名，后一个覆盖前一个。如图：
![Struts2中配置问价加载顺序](http://upload-images.jianshu.io/upload_images/2776669-e6c6a8bc29b7bb91.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

你能看到struts2.xml中有很多的元素吗？我们现在开始修行每个元素的各种属性啦，少爷，准备好了么？
“准备好啦，来吧，哈哈哈~”
###7. Struts2中配置文件中的各个元素

```
<!--package元素：是定义一个struts的包，把配置文件按照面向对象编程的思想来管理
	name属性：指定包的名称。注意： 包的名称在配置文件中唯一。
	extends属性：指定当前包的父包。 它是按照面向对象的思想管理的体现
	abstract属性：把包声明为一个抽象包，抽象包就是用来被继承的。只有没有action属性的包才能被定义为抽象包 
	namespace属性：名称空间。当指定了名称空间之后，访问路径就变成了：
						访问路径=名称空间+动作名称
				   当不指定该属性时，该属性有默认值，默认为""。注意，不是"/"
				   名称空间的搜索顺序：
					   	第一步：先去找对应的名称空间（由大到小）
				   				在指定的名称空间找到了：执行第二步
				   				在指定的名称空间没找到：按照名称空间结构往上追溯，一直到根名称空间，只要在任何一级找到了就执行第二步
					   	第二步：找对应的动作名称
				   				现在指定名称空间下搜索对应的动作名称：找到了执行动作类的方法
				   				在指定的名称空间下没找到对应名称空间：前往默认的名称空间下找对应的动作名称。注意：只搜索动作名称。
	 -->
	<package name="p1" extends="struts-default">
		<!-- 
			action元素：适用于定义动作名称、动作类和动作方法的映射。以及当出现不同情况时前往指定的结果视图
				name属性：动作名称。和客户端请求的url地址中的名称对应起来。这里不能写.action
				class属性：指定动作类。
				method属性：指定要执行的动作方法。
		 -->
		<action name="action1" class="com.wg.web.action.Demo1Action" method="doDemo">
			<!--result元素：结果视图
				    name属性：如果不写name时，默认值是success
					type属性：以何种方式前往指定的视图或动作，默认是dispatch		
            -->
			<!-- 可以进行自定义结果视图：
					1.创建类 集成StrutsResultSupport 重写doExecute方法
                    2.在struts.xml中配置-->
			<result name="success">/success.jsp</result>
		</action>
	</package>
```

对于动作类的创建方法：

```
/**
 * 创建动作类的第一种方式(简单  但是开发几乎不使用)
 * 		创建一个普通的java类
 * 		此类就是一个POJO（原始的 老的 Java 对象）
 * 		Plain Old Java Object
 */
public class Demo1Action {
	public String doDemo(){
		return "success";
	}
}

import com.opensymphony.xwork2.Action;
/**
 * 创建动作类的第二种方式：
 * 		创建一个普通类实现Action接口，实现接口中的方法
 * @author Administrator
 *
 */
public class Demo2Action implements Action {

	@Override
	public String execute() throws Exception {
		return null;
	}

}


import com.opensymphony.xwork2.ActionSupport;
/***
 * 第三种创建动作类
 * 		新建一个类继承ActionSupport
 * 在开发中我们采取这样的方式创建我们的动作类
 * @author Administrator
 *
 */
public class Demo3Action extends ActionSupport {
	//在这个类中我们什么都不写，一个动作方法都不提供时，有一个默认的动作方法：execute()
}
```
8.分文件编写配置文件
  建立多个struts.xml(不同名称) 在每个struts.xml中进行编写相对应模块的代码，在struts.xml中使用**include**进行添加每个模块的struts.xml；例如：
```
<include file="struts_login.xml"></include>
```
恭喜少爷，已经修炼出了Web基础阶段一啦~，再接再厉哟~。
“我的天，好多属性，好多东西啊，这要记到什么时候a，累死了”
呵呵，少爷，这些不要死记硬背，要会看源码，这些会在以后的修炼中不断磨练的。
“恩，知道了福伯。不行了，我要去后山的温泉泡一会去了，祛除疲劳”
说完就飞一般跑开了。
福伯：“慢点啊，少爷。”