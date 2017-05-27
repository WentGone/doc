<h1>什么是HTML</h1>
HTML是用来描述网页的一种语言
- HTML指的是超文本标记语言
- HTML不是一种编程语言，而是一种标记语言
- 标记语言是一套标记标签
- HTML使用标记标签来描述页面

HTML通过标签的形式将信息展示给用户。

<h1>HTML基本标签</h1>
1. 文件标签(结构标签)
		<html>根标签
		<head>
			<title>页面的标题
		<body>内容
			属性
				text:文本的颜色
				bgcolor:背景色
				background:背景图片
2. 排版标签
		<!-- -->注释标签
		<br/>换行标签
		<p></p>段落标签 段与段之间的空行
			属性:
				align:对齐方式	left center right
		<hr/>水平线标签
			属性:
				width:长度
				size:粗度
				color:颜色
				align:对齐方式
			尺寸的写法:
				(1). 像素:10px
				(2). 百分比:占据父标签的百分比
3. 块标签
		<div>:行级块标签
		<span>:行内块标签
		作用:div:div+css布局。span:进行友好提示
4. 文字标签
		基本文字标签:
		<font>
			属性:
				color:颜色
				size:文字大小 最小值:1,最大值:7,默认值:3
				face:字体类型(黑体、宋体)
		标题标签:
		<h1>--<h6>
		是依次减小的，默认字体加粗，内置字号
5. 清单标签
		无序列表:<ul>
				属性
				type:disc square circle
					<li>:列表项
		有序列表:<ol>
				属性
				type:1 a A I i
				start:从第几个开始(数字)
6. 图形标签
		<img>
			src:图片地址
			width:宽度
			height:高度
			border:边框
			align:对齐方式 代表图片与相邻的文本的相对位置 top middle bottom
			alt:图片的文字说明，
7. 链接标签
		<a>
			属性:
				href:跳转的页面地址
				name:页面名称， 锚点
				target:_self _blank 默认_self
			作用:
				(1)页面跳转
					注意：访问互联网上的资源，前边必须加协议http://
				(2)锚点访问
					href在访问锚点的时候书写格式#name的值
8. 表格标签
		<table>
		属性:
		border:表格线
		width:表格的宽度
		align:表格的对齐方式
		bgcolor:背景颜色
			<caption>:表格的标题
			<tr>行
			属性:
			align:对齐方式  单元格内数据的对齐方式
				<td>单元格
				属性:
				colspan:列合并
				rowspan:行合并
			表头的话讲<td>换成<th>
		表格的作用:
			(1)简单的实现一个表格样式
			(2)进行布局
		<thead>
		<tbody>
		<tfoot>
			作用:分块加载，优化用户体验

<h1>HTML表单标签</h1>
1. form标签
		<form>
		属性:
			name:表单名称
			action:提交的路径地址
			method:提交方式 post get
				*get与post区别:
					(1)get提交将数据加在地址栏后边,而post提交将数据封装在请求体中。
					(2)get提交相对不安全 post提交相对安全。
					(3)get提交有大小限制,根据浏览器的不同而不同。post不限制大小。
2. input标签
		属性:
			type:根据type值的不同，会显示不同功能呢表单项
				text:普通的文本输入框
				password:密码输入框，特点显示掩码
				radio:单选按钮 name值相同
					checked:代表默认被选中
				checkbox:复选框 name值相同
				file:上传文件
				button:普通按钮，没有任何内置功能
				submit:提交按钮，点击表单按照action地址进行提交
				reset:重置按钮，点击会将表单清空
				image:图片按钮，点击表单按照action地址进行提交
					属性:
						src:图片地址
						alt:图片的文字信息
				hidden:隐藏标签 服务端需要，不需要用户看到。
			name:
			value:默认显示值
3. select标签
		name:表单项名称 
		option:代表一个选择项
			value
			selected:默认被选中的项
4. textArea文本域标签
		属性:
			cols:列数
			rows:行数
			默认的文本值是在标签体中

<h1>HTML框架标签及其他</h1>
1. 框架标签
		frameset:
			属性:
				cols:按照列划分
				rows:按照行划分
				划分的格式:rows="120,*,120" *代表剩余部分
			frame:
				属性:
				name:名称，方便target根据name值进行定位。
				src:加载页面的路径
2. 其他标签
		<meta>
		<link>
			<link re="stylesheet" type="text/css" href="./styles.css">
				href:引入css文件的地址
		<script>
			<script type="text/javascript" src=""></script>
				src:js的文件地址
3. 特殊字符
		&nbsp; 空格
		&gt; 大于号 >
		&lt; 小于号 <
		&copy; 版权符 ©