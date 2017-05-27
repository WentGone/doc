ContextMap
1. 动作类生命周期：
  明确：动作类是多例的，每次访问动作方法时，动作类都会实例化，所以是线程安全的。
2. 请求动作的数据存放：
  我们的问题：
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;每次请求时，都会产生一些请求数据，这些数据都存放在什么地方了呢？
  首先我们要明确一点：
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在每次执行动作前，核心控制器StrutsPrepareAndExecuteFilter都会创建一个ActionContext和ValueStack对象。且每次访问动作都会创建。
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**这两个对象存储了整个动作访问期间用到的数据。**并且把数据绑定到了**线程局部变量（ThreadLocal）**上了。所以线程是安全的。

Context Map是OGNL的上下文ActionContext ValueStack
包含以下方面：
- ActionContext 一个Map，这就类似于keyedHandler结构
 - application&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;是一个Map，封装着应用域的属性
 - session&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;是一个Map，封装着会话域的属性
 - request&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;是一个Map，封装着请求域的属性
 - parameters&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;是一个Map，封装着请求正文（参数）的属性
 - attr&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;是一个Map，封装着四个域中的所有属性
- value stack(root)
- action(the current action)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;当前动作的引用