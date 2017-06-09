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

注解入门：
创建一个web工程，导入jar文件

配置入口文件web.xml

配置springmvc.xml
```
 <bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping"></bean>
 <bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter"></bean>

<context:component-scan base-package="com.wg">
    <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller" />
</context:component-scan>
```
或者
```
<context:component-scan base-package="com.wg">
    <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller" />
</context:component-scan>
<mvc:annotation-driven />
```

定义一个Controller(不需要继承Controller),加入注解**@Controller**
写一个公共方法，用于返回的逻辑视图，加入注解**@RequestMapping("/地址")**请求映射
几种写法：
@RequestMapping("/user")
@RequestMapping(value = "/user")
@RequestMapping(value = "/user",method=RequestMethod.GET)
@RequestMapping(value = "/user",method={RequestMethod.Get,RequestMethod.POST})
```
@Controller
public class UserController {
    @RequestMapping("/user")
    public String getUser(){
        return "user";
    }
}
```
<h3>SpringMVC封装参数</h3>
注意：springmvc没有成员变量，需要传递的参数对象放到方法中，当请求方法时，方法里的对象会自动被创建，需要封装的参数会自动被封装方法对象（传递时跟参数名相同）

基本数据类型
```
/**
 *访问时传递的参数要和方法中的参数名一致
 *
**/
@RequestMapping("user")
public String getUser(int id){
    System.out.println(id);
    return "user";
}
```

String类型(同Int)

传递对象POJO

对于接受集合类型参数
方法里边不能直接传递list集合，和map集合类型参数，需要将list、map封装到包装类中


接受Map类型参数
前端 value = maps['username']   &nbsp;&nbsp;&nbsp;{属性名['key名']}


struts2与springmvc的区别:
##实现机制
struts2底层是过滤器，是基于过滤器实现
springmvc是基于servlet实现。


##执行速度
struts2是多例的
springmvc是单例的

##参数封装
struts2参数封装是基于属性封装
springmvc是基于方法封装。颗粒更细

<h3>页面参数回显</h3>
Springmvc使用Model对象，Model对象相当于域application。Application对象中的数据可以使用el表达式进行获取