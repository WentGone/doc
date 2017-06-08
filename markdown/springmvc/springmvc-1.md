Java语言的体系结构
![Java语言的体系结构](http://i.imgur.com/d7zx7ip.png)	

<h1>简介</h1>

Springmvc是一个web层的mvc框架，类似struts2
什么是mvc？[设计思想：解耦合-->任何的重定向都能解耦合]
![MVC设计模式](http://i.imgur.com/O2rJ7Fo.png)

<h1>执行流程</h1>
![SpringMVC流程图](http://i.imgur.com/A9aFg6h.png)

struts2执行流程：
- 前端控制器strutsPrepareAndExcuteFileter拦截请求
- 寻找Action，处理请求数据
- ActionMapping根据请求寻找执行类
- 找到ActionProxy，这个代理类strutsActionProxy extends defaultActionProxy
- ActionProxy根据struts.xml寻找真正的执行类Action


第一个程序案例
- 创建一个web工程
- 导入springmvc的jar包
	![所需jar包](http://i.imgur.com/nhIQWQA.png)

配置web.xml
```
<servlet>
	<servlet-name>springmvc</servlet-name>
	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
    	<param-name>contextConfigLocation</param-name>
		<!--config下文件名-->
        <param-value>classpath:springmvc.xml</param-value>
    </init-param>
</servlet>
<servlet-mapping>
	<servlet-name>springmvc</servlet-name>
    <url-pattern>*.do</url-pattern>
</servlet-mapping>
```
在src同级目录下新建一个config文件夹(如果使用IntellijIDEA，需要制定文件夹为Resources)然后新建一个xml
```
<?xml version="1.0" encoding="utf-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
		http://www.springframework.org/schema/mvc
		http://www.springframework.org/schema/mvc/spring-mvc-3.2.xsd
		http://www.springframework.org/schema/context
		http://www.springframework.org/schema/context/spring-context-3.2.xsd
		http://www.springframework.org/schema/aop
		http://www.springframework.org/schema/aop/spring-aop-3.2.xsd
		http://www.springframework.org/schema/tx
		http://www.springframework.org/schema/tx/spring-tx-3.2.xsd">
    <!--处理器映射器-->
    <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"></bean>
    <!--处理器适配器-->
    <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"></bean>
    <!--后端控制器-->
    <bean id="userController" name="/hello.do" class="com.wg.controller.UserController"></bean>

    <!-- 配置视图解析器,负责解析出真正的物理视图
		后台返回逻辑视图：index
		解析出真正的物理视图：前缀+逻辑视图+后缀=====/WEB-INF/jsps/index.jsp
	 -->
    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsps/"></property>
        <property name="suffix" value=".jsp"></property>
    </bean>
</beans>
```
新建一个Controller继承于Controller
```
public class UserController implements Controller{
    @Override
    public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
        ModelAndView mv = new ModelAndView();
        mv.addObject("hello","879");
        mv.setViewName("index");
        return mv;
    }
}
```
现在基本做完一个简单的springmvc示例了。

<br/>
<h3>三种处理器映射器:</h3>

```
<!--处理器映射器-->
    <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"></bean>
<!--简单的Url处理器映射器，把Url进行集中配置-->
    <bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
        <property name="mappings">
            <props>
				<!--id名称-->
                <prop key="mine.do">userController</prop>
                <prop key="ss.do">userController</prop>
                <prop key="abc.do">userController</prop>
            </props>
        </property>
    </bean>
<!--直接使用类名访问：类名.do(类名首字母小写)-->
    <bean class="org.springframework.web.servlet.mvc.support.ControllerClassNameHandlerMapping"></bean>
```

<h3>两种处理器适配器:</h3>

```
<!--处理器适配器 负责执行Handler(Controller)默认的-->
    <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"></bean>


<!--处理器适配器是用来处理实现了HttpRequestHandler后端控制类的Handler-->
    <bean class="org.springframework.web.servlet.mvc.HttpRequestHandlerAdapter"></bean>
```

<h3>接受页面参数</h3>

SpringMVC使用命令设计模式

乱码问题：
post:在web.xml中
```
<filter>
    <filter-name>chracterEncoding</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>chracterEncoding</filter-name>
        <url-pattern>/*</url-pattern>
</filter-mapping>
```

时间转换问题：
在Controller中有个initBinder方法在此方法中进行日期转换
```
    @Override
    protected void initBinder(HttpServletRequest request, ServletRequestDataBinder binder) throws Exception {
        String birthday = request.getParameter("birthday");
        if (birthday.contains("/")){
            binder.registerCustomEditor(Date.class,new CustomDateEditor(new SimpleDateFormat("yyyy/MM/dd"),true));
        }else {
            binder.registerCustomEditor(Date.class,new CustomDateEditor(new SimpleDateFormat("yyyy-MM-dd"),true));
        }
    }
```

<h3>注解开发</h3>

