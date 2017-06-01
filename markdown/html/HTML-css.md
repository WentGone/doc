<h1>css简介</h1>

1. 什么是css
		层叠样式表，css是对html进行样式修饰的语言
		层叠:是每一层的覆盖叠加，如果不同的css样式对同一个html标签进行修饰
		    样式有冲突的部分应用优先级高的，不冲突的部分共同作用。
		样式表:是css属性样式的集合
2. css的作用
	- 修饰html，使html样式更加好看
	- 提高样式代码的复用性
	- html的内容与样式相分离，便于后期的维护

3. css的引入方式和书写规范
		(1)内嵌样式
			内嵌样式是把css的代码嵌入到html标签中
			<div style="color:red;font-size:25px">内嵌样式</div>
			语法:
				1>使用style熟悉，将样式嵌入到html标签中
				2>属性的写法:属性:属性值
				3>多个属性之前使用分号;隔开
			不建议使用
		(2)内部样式
			在head标签中使用style标签进行css的引入
			 <style type="text/css">
        		div{
            		color:green;
            		font-size:25px
        		}
    		</style>
			语法:
				1>使用style标签进行引入
					<style type="text/css">
					属性:type  告知浏览器使用css的解析器解析代码
				2>属性的写法:属性:属性值
				3>多个属性之前使用分号;隔开
		(3)外部样式
			将css样式抽取成一个单独的css文件，使用时引用。
			<link rel="stylesheet" type="text/css" href="../../css/css.css">
			语法:
				1>创建一个css文件。将css属性写在css文件中
				2>在head中使用link标签进行引入
					<link rel="stylesheet" type="text/css" href="../../css/css.css">
				3>属性的写法:属性:属性值
				4>多个属性之前使用分号;隔开
		(4)@import方式
			 <style type="text/css">
        		@import url("css地址");
    		 </style>
			link与@import方式的区别:
				a. link所有浏览器都支持，@import部分低版本的IE不支持
				b. import方式是等待html加载完毕之后再加载
				c. import方式不支持js的动态修改

<h1>css选择器</h1>

1. 基本选择器
		(1)元素选择器
			语法：html的标签名{css属性}
			示例:
				<style type="text/css">
					span{color:red;font-size:100px;}
				</style>

				<span>
					元素选择器
				</span>
		(2)id选择器 id唯一性
			语法：#id的值{css属性}
			示例:
			    <style type="text/css">
        		#div1{
            		background-color:red;
        		}
        		#div2{
            		background-color:pink;
        		}
				</style>

				<div id="div1">hello,css1</div>
    			<div id="div2">hello,css2</div>
		(3)class选择器
			语法:.class的值{css属性}
			示例：
				<style type="text/css">
        			.div_class1{
            			color: aqua;
            			font-size: 30px;
        			}
        			.div_class3{
            			background-color: blanchedalmond;
	            		font-size: 20px;
    	    		}
    			</style>

				<div class="div_class1">css1,class</div>
    			<div class="div_class1">css2,class</div>
    			<div class="div_class3">css3,class</div>
		选择器优先级:id > class > 元素

2. 属性选择器
		语法:基本选择器[属性='属性值']{css属性}
		示例:
		<style type="text/css">
        	input[type='text']{
            	background-color: yellow;
        	}
        	input[type='password']{
            	background-color: pink;
        	}
    	</style>

		<form>
        	username:<input type="text"/><br/>
        	password:<input type="password"/><br/>
    	</form>

3. 伪元素选择器
		a标签的伪元素选择器
			语法:
				静止状态 a:link{css属性}
				悬浮状态	a:hover{css属性}
				触发状态 a:active{css属性}
				完成状态 a:visited{css属性}
			示例:
				  <style type="text/css">
        			a:link{color: black}
        			a:hover{color: blue}
        			a:active{color: green}
        			a:visited{color: yellow}
    			</style>

				 <a href="#">伪元素选择器</a>

4. 层级选择器
		语法:父级选择器 子级选择器 .....
		示例:
			<style type="text/css">
        		#d1 .dd2 span{
            		color: red;
        		}
    		</style>

			<div id="d1">
        		<div class="dd1">
            		<span>span1-1</span>
        		</div>
        		<div class="dd2">
            		<span>span1-2</span>
        		</div>
    		</div>
    		<div id="d2">
        		<div class="dd1">
            		<span>span1-1</span>
        		</div>
        		<div class="dd2">
            		<span>span1-2</span>
        		</div>
    		</div>

<h1>css属性</h1>

1. 文字属性	

		font-size:大小
		font-family:字体类型

2. 文本属性

		color:颜色
		text-decoration:下划线
			属性值:none underline
		text-align:对齐方式
			属性值:left center right

3. 背景属性

		background-color:背景颜色
		background-image:背景图片
			属性值:url("图片地址")
		background-repeat:平铺方式
			属性值:默认横纵向平铺
					repeat:横纵向平铺
					no-repeat:不平铺
					repeat-x:横向平铺
					repeat-y:纵向平铺

4. 列表属性

		list-style-type:列表项前的小标志
		list-style-image:列表前的小图片
			属性值:url("图片地址")

5. 尺寸属性
6. 显示属性
7. 浮动属性

<h1>css盒子模型</h1>
