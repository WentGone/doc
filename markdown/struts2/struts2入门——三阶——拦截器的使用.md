“福伯，我来了，昨晚上回顾了一下前端时间的修炼，还有又自己看了一下国际化方向的资料，嘿嘿，又有学习修炼的冲动了。”
呵呵，好啊，那少爷，我们今天来学习拦截器的使用吧。在学习之前，我们先温习下之前讲到struts2的框架的时候的原理图，里边是不是有个Interceptor啊。是的，这就是struts2给我们封装了一些拦截器。当然我们也可以进行自定义的。
首先我们来看一下拦截器的运行图：
![Interceptor](http://i.imgur.com/1ckTw91.png)
从图中我们可以看到，Interceptor的执行是在Action方法之前以及结果视图返回后执行，两次执行顺序是相反的。
从struts2的源码中我们可以了解到：
![](http://i.imgur.com/qcxTAZx.png)
我们看到的所有的struts2为我们做好的拦截器，几乎都是继承与抽象类AbstractInterceptor，实现intercept方法。
那我们心里有数了，我们也按照里边走，继承抽象类实现方法，看看能不能做出一个自定义的拦截器，少爷，动手试试。
“好来，我来试试。”
首先创建一个类继承抽象类(继承AbstractInterceptor实现抽象方法intercept)：
```
public class Demo1Interceptor extends AbstractInterceptor {

	@Override
	public String intercept(ActionInvocation invocation) throws Exception {
		System.out.println("Demo1Interceptor拦截器执行ing");
		//放行
		String rtValue = invocation.invoke();//结果视图的名称
		System.out.println("Demo1Interceptor拦截器执行结束");
		System.out.println(rtValue);
		return rtValue;
	}

}
```
恩少爷，记住一定要invoke()进行放行，要不然无法向下传递了。
“之后呢，唔，我记得之前说配置框架及各种属性的时候说了一句要在struts.xml中进行配置，唔...”
```xml
	<!-- 声明自定义拦截器 -->
	<interceptors>
		<interceptor name="demo1Interceptor" class="com.wg.javaee.interceptor.Demo1Interceptor"></interceptor>
	</interceptors>
```
“不对啊，为什么还是没有结果，没有打印呢，福伯，你是不是说错啦~”
哈哈，少爷，你在仔细想想，你声明了拦截器，你有没有使用它啊，你要在哪使用它？
“嗯···，不知道了，福伯，你说吧，还差哪？”
嘿嘿，少爷也有不懂的时候啊，还差：
```
<action name="action1" class="com.wg.javaee.action.Demo1Action" method="save">
	<!-- 使用自定义拦截器,当配置了任意一个自定义拦截器，默认的拦截器栈就不会再工作了 
		当有多个拦截器时，执行顺序是interceptor-ref配置顺序
	-->
	<interceptor-ref name="demo1Interceptor"></interceptor-ref>
	<result name="success">/demo1.jsp</result>
</action>
```
好啦，运行一下，看看有什么变化没。
![interceptor的执行顺序](http://i.imgur.com/GWmdf9k.png)
从图中我们可以看到先执行了自定义拦截器，然后执行了动作方法，再到结果视图，再重新返回到自定义拦截器。
拦截器中的invoke()方法其实返回的就是结果视图的结果。


少爷，你现在会拦截器了，那我们来做一个小demo测试一下，要求：
1.有登陆页面。
2.有主页，但是只有在登陆之后才能进入。内部有个进入其他页面的链接。
3.有其他页，前提条件也是只有登陆之后才能查看。

操作ing···

“按照上边的步骤走，好了”
```
	<package name="p2" extends="struts-default">
		<!--声明拦截器-->
		<interceptors>
			<interceptor name="checkLoginInterceptor" class="com.wg.javaee.interceptor.CheckLoginInterceptor"/>
		</interceptors>
		<!--全局结果视图-->
		<global-results>
			<!--数据回显的结果视图-->
			<result name="input">/login.jsp</result>
		</global-results>
		<action name="login" class="com.wg.javaee.action.LoginAction" method="login">
			<result type="redirectAction">showMain</result>
		</action>
		<action name="showMain" class="com.wg.javaee.action.ShowMainAction">
			<interceptor-ref name="checkLoginInterceptor"/>
			<result>/main.jsp</result>
		</action>
		<action name="showOther" class="com.wg.javaee.action.ShowOtherAction">
			<interceptor-ref name="checkLoginInterceptor"/>
			<result>/other.jsp</result>
		</action>
	</package>
```
恩，少爷，果然聪明，可是你难道忘了么，当你设置了自己的拦截器的时候，默认的所有拦截器都会不起作用的。这样该怎么办？
“啊？真没有想那么多”
少爷，这要进行多次调整的：
>###1.使用正常的普通的设置拦截器
```
	<!-- a.使用自定义拦截，检查登录，最基本的配置 
		 存在的问题：
		 	当我们使用了自定义拦截器之后，默认的拦截器栈不再工作了
	-->
	<package name="p2" extends="struts-default">
		<!--声明拦截器-->
		<interceptors>
			<interceptor name="checkLoginInterceptor" class="com.wg.javaee.interceptor.CheckLoginInterceptor"/>
		</interceptors>
		<!--全局结果视图-->
		<global-results>
			<!--数据回显的结果视图-->
			<result name="input">/login.jsp</result>
		</global-results>
		<action name="login" class="com.wg.javaee.action.LoginAction" method="login">
			<result type="redirectAction">showMain</result>
		</action>
		<action name="showMain" class="com.wg.javaee.action.ShowMainAction">
			<interceptor-ref name="checkLoginInterceptor"/>
			<result>/main.jsp</result>
		</action>
		<action name="showOther" class="com.wg.javaee.action.ShowOtherAction">
			<interceptor-ref name="checkLoginInterceptor"/>
			<result>/other.jsp</result>
		</action>
	</package>
```
**此方式存在着弊端：我们无法使用默认的拦截器。**

>###2.在1的基础上进行改进，我们引入默认的拦截器栈这样不就好了么
```
<!-- b.a中存在的弊端，当需要拦截的动作很多时，写起来繁琐 
	针对上边a的问题，我们的解决办法是，把默认的拦截器栈配置进来 <interceptor-ref name="defaultStack"></interceptor-ref> -->
	<package name="p2" extends="struts-default">
		<interceptors>
			<interceptor name="checkLoginInterceptor" class="com.wg.javaee.interceptor.CheckLoginInterceptor"/>
		</interceptors>
		<global-results>
			<result name="input">/login.jsp</result>
		</global-results>
		<action name="login" class="com.wg.javaee.action.LoginAction" method="login">
			<result type="redirectAction">showMain</result>
		</action>
		<action name="showMain" class="com.wg.javaee.action.ShowMainAction">
			<interceptor-ref name="defaultStack"/>
			<interceptor-ref name="checkLoginInterceptor"/>
			<result>/main.jsp</result>
		</action>
		<action name="showOther" class="com.wg.javaee.action.ShowOtherAction">
			<interceptor-ref name="defaultStack"/>
			<interceptor-ref name="checkLoginInterceptor"/>
			<result>/other.jsp</result>
		</action>
	</package>
```
**此方式还是存在着弊端：我们需要在拦截的方法中进行不断的引用我们配置的所有拦截器**

>###3.我们在2的基础上直接覆盖struts-default.xml配置文件的默认拦截栈，将原本的默认拦截栈和我们自己的拦截栈组合设置成新的默认拦截栈default-interceptor-ref
```
	<!-- c.b中存在的问题，还是需要在要用的地方使用拦截器引用
			解决办法：使用覆盖struts-default.xml配置文件中的默认拦截栈，让我们的这个成为默认的。
		在声明拦截器中定义一个interceptor-stack
	-->
	<package name="p2" extends="struts-default">
		<interceptors>
			<interceptor name="checkLoginInterceptor" class="com.wg.javaee.interceptor.CheckLoginInterceptor"/>
			<interceptor-stack name="myDefaultStack">
				<interceptor-ref name="defaultStack"></interceptor-ref>
				<interceptor-ref name="checkLoginInterceptor"></interceptor-ref>
			</interceptor-stack>
		</interceptors>
		<default-interceptor-ref name="myDefaultStack"></default-interceptor-ref>
		<global-results>
			<result name="input">/login.jsp</result>
		</global-results>
		<action name="login" class="com.wg.javaee.action.LoginAction" method="login">
			<result type="redirectAction">showMain</result>
		</action>
		<action name="showMain" class="com.wg.javaee.action.ShowMainAction">
			<result>/main.jsp</result>
		</action>
		<action name="showOther" class="com.wg.javaee.action.ShowOtherAction">
			<result>/other.jsp</result>
		</action>
	</package>
```
**然而这个方法还是有弊端...  当我们设置了新的默认拦截栈后发现，登陆的时候不想拦截都被拦截了，那我们就得看看有没有那种可以进行自己判断哪些方法需要拦截，哪些不需要拦截的。**

>###4.我们通过观察源码，发现在AbstractInterceptor的子类中有个MethodFilterInterceptor，发现里边有俩参数有意思excludeMethods、includeMethods字面意思就是不包括的方法、包括的方法。那我们就可以使用参数注入的方式进行选择了
```
<!-- d.c中存在的问题，当我们配置了默认的拦截器栈后，不想拦截的也被拦截了
			解决办法：在AbstractInterceptor的子类中还有个抽象类MethodFilterInterceptor
			里边有两个属性  
			excludeMethods：哪些方法不需要拦截
			includeMethods：哪些方法需要拦截
	 -->
	<package name="p2" extends="struts-default">
		<interceptors>
			<interceptor name="checkLoginInterceptor2" class="com.wg.javaee.interceptor.CheckLoginInterceptor2"/>
			<interceptor-stack name="myDefaultStack">
				<interceptor-ref name="defaultStack"></interceptor-ref>
				<interceptor-ref name="checkLoginInterceptor2">
					注入的方式进行设置动作方法是否需要拦截 
					<param name="excludeMethods">login</param>
				</interceptor-ref>
			</interceptor-stack>
		</interceptors>
		<default-interceptor-ref name="myDefaultStack"></default-interceptor-ref>
		<global-results>
			<result name="input">/login.jsp</result>
		</global-results>
		<action name="login" class="com.wg.javaee.action.LoginAction" method="login">
			<result type="redirectAction">showMain</result>
		</action>
		<action name="showMain" class="com.wg.javaee.action.ShowMainAction">
			<result>/main.jsp</result>
		</action>
		<action name="showOther" class="com.wg.javaee.action.ShowOtherAction">
			<result>/other.jsp</result>
		</action>
	</package>
```
**但是，这种方法如果在我们先写拦截器的情况下，我们根部不知道哪些方法被拦截，哪些不被拦截，所以还是不方便**

>###5千辛万苦揪出了最终格式，我们直接继承MethodFilterInterceptor，在不需要拦截的方法中使用params进行注入。
```
<!-- e.d中存在的问题，我们在声明拦截器和定义拦截器栈时，可能根本不知道哪些方法需要拦截哪些不需要
		解决办法，在使用拦截器的时候注入参数，告诉拦截器哪些需要拦截，哪些不需要
	 -->
	<package name="p2" extends="struts-default">
		<interceptors>
			<interceptor name="checkLoginInterceptor2" class="com.wg.javaee.interceptor.CheckLoginInterceptor2"/>
			<interceptor-stack name="myDefaultStack">
				<interceptor-ref name="defaultStack"></interceptor-ref>
				<interceptor-ref name="checkLoginInterceptor2">
				</interceptor-ref>
			</interceptor-stack>
		</interceptors>
		<default-interceptor-ref name="myDefaultStack"></default-interceptor-ref>
		<global-results>
			<result name="input">/login.jsp</result>
		</global-results>
		<action name="login" class="com.wg.javaee.action.LoginAction" method="login">
			<interceptor-ref name="myDefaultStack">
				<param name="checkLoginInterceptor2.excludeMethods">
				login
				</param>
			</interceptor-ref>
			<result type="redirectAction">showMain</result>
		</action>
		<action name="showMain" class="com.wg.javaee.action.ShowMainAction">
			<result>/main.jsp</result>
		</action>		
		<action name="showOther" class="com.wg.javaee.action.ShowOtherAction">
			<result>/other.jsp</result>
		</action>
	</package>
```

来吧少爷，运行一下项目试试。
![demo演示](http://i.imgur.com/JVU0htr.gif)
“哈哈哈，好有成就感，额，就是一步一步的推，好复杂啊。福伯，为什么你不一次性让我学最后一种啊，其他的也没用”
少爷，可不能这么说啊，你现在的一点一点的积累都是为你以后的强大做的基础的。如果不一步一步的推导，你会知道还有个拦截器抽象类叫MethodFilterInterceptor么。一步步的来你才会印象深刻啊。

“若林哥哥，过来陪灵儿玩好不好，无聊死了。啊，福伯也在啊，福伯，能不能让若林哥哥歇会陪我玩半天呀，嘿嘿。”
灵儿小姐，少爷才学了一点点，不好吧，struts2基础还没一半呢。
“好福伯啦，下次给你带灵泉酒好不好，反正若林哥哥也累了。嘿嘿”
怕了你了，就属你鬼灵精，去吧，早去早回啊。

未完待续哟···