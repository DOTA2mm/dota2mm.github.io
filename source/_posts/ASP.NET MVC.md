---
title: ASP.NET MVC摸索
date: 2016/5/9
categories:
- 笔记整理
tags:
- ASP.NET MVC
---

## MVC-ViewBag ##

一个控制器可以使用ViewBag对象来将数据或对象传递到视图模板中。ViewBag是一个**动态对象**，它提供了一种便利的，后期绑定的方法来**将信息从控制器传递到视图**中。你可以为它添加任何属性并赋上属性值。在未赋值之前该属性是不生效的，直到你赋值为止。例如：  

```cs
public ActionResult Welcome(string name, int numTimes = 1)
        {
            ViewBag.Message = "Hello " + name;
            ViewBag.NumTimes = numTimes;
            return View();
        }
```
ViewBag对象中已经包含了数据，它将被自动传递给视图。

<!--more-->

## MVC 控制器向View传值的三种方法 ##
1. **提供视图模型对象**  
	* 你能把一个对象作为View方法的参数传递给视图.  
```cs
public ViewResult Index()  
{  
DateTime date = DateTime.Now;  
return View(date);  
}  
```
	* 然后我们在视图中使用Razor的Model关键字来访问这个对象   
```cs 
@{  
ViewBag.Title = "Index";  
}  
<h2>Index</h2>  
The day is: @(((DateTime)Model).DayOfWeek)   
``` 
或者是  
```cs
@model DateTime    
@{  
ViewBag.Title = "Index";  
}    
<h2>Index</h2>  
The day is: @Model.DayOfWeek 
``` 

2. **使用ViewBag(视图包)传递数据**  
	* View  Bag 允许在一个动态的对象上定义任意属性,并在视图中访问它.这个动态的对象可以通过Controller.ViewBag属性访问它.  
```cs
public ViewResult Index()    
{    
    ViewBag.Message = "Hello";    
    ViewBag.Date = DateTime.Now;    
    return View();    
}    
  
@{
	ViewBag.Title = "Index";    
}    
<h>Index</h>    
The day is: @ViewBag.Date.DayOfWeek    
<p />  
The message is: @ViewBag.Message    
```
3. **使用View Data传递数据**  
	* 在MVC3.0之前,主要是通过这种方式传递数据,它是通过用 ViewDataDictionary类实现的,而不是动态的对象.ViewDataDictionary类是类似标准"键/值"集合,并通过Controller类的ViewData属性进行访问的.这个方法,在视图中需要对对象进行转换.  
	*控制器中:* 
	```cs 
		public ViewResult Index()    
	 {    
	    ViewData["Message"] = "Hello";    
	    ViewData["Date"] = DateTime.Now;    
	    return View();    
	 }    

    ```  
	*视图中:*  

	```cs
		 @{   
		 ViewBag.Title = "Index";  
		 }    
		 <h2>Index</h2>    
		 The day is: @(((DateTime)ViewData["Date"]).DayOfWeek)    
		 <p />    
		 The message is: @ViewData["Message"]   
 	```
## ASP.NET MVC中form提交和控制器接受form提交过来的数据 ##  
  
1. **cshtml页面form提交**  
![](http://i.imgur.com/GIv73hk.png)  
2. **控制器处理表单提交数据4种方式**  

方法1:使用传统的Request请求取值 
```cs 
		[HttpPost]
		public ActionResult AddNews()
		{
		string a=Request["text1"];
		string b=Request["text2"];
		}  
```
方法2:Action参数名与表单元素name值一一对应  
```cs
		[HttpPost]
		public ActionResult AddNews(string text1,string text2)
		{
		    string a=text1;
		    string b=text2;
		}  
```
方法3:从MVC封装的FormCollection容器中读取  
```cs
		[HttpPost]
		public ActionResult AddNews(FormCollection form)
		{
		    string a=form["text1"];
		    string b=form["text2"];
		}  
```
方法4:使用实体作为Action参数传入，前提是提交的表单元素名称与实体属性名称一一对应  
```cs
		[HttpPost]
		public ActionResult AddNews(userModel user)
		{
		    string a=user.text1;
		    string b=user.text2;
		}  
```

## ASP.NET MVC 中的ViewData与ViewBag ##	  
  
>Asp.net MVC默认使用 Web Form来作为View，新建的aspx页面均是继承于ViewPage。而ViewData和ViewBag都是ViewPage的属性。ViewPage.ViewData表示获取或设置一个字典，其中包含在控制器和视图时间传递的数据，也就是说ViewPage.ViewData是控制器Controller和视图View之间沟通传递数据的桥梁。而ViewPage.ViewBag则表示获取一个视图包。

| i|类型和作用|引入时间|版本	|查询速度|是否需要类型转换|可读性|
|---|---|---|---|---|---|
|ViewData|字典类型（Key和Value的合）是 Controller和View之间传递数据的桥梁|ASP.NETMVC 1.0引入|基于.Net Framework 3.5|ViewData查询速度比ViewBag快|在ViewPage中查询数据时需要进行类型转换|可读性相对要差点|
|ViewBag|dynamic类型|ASP.NET MVC 3.0引入|基于.Net Framework 4.0|查询速度比ViewData慢|在ViewPage中查询数据时不需要进行类型转换|可读性更好|  

## MVC3中PartialView()与View()的区别 ##  

当我们使用razor作为页面引擎时，它的视图文件扩展名为cshtml或者vbshtml，而之前作为分部视图的ascx文件，进行razor之后，也是cshtml，这与非razor引擎有些不同，在这方面，官方并没有显式把分部视图与标准视图分开，有时，我们在开发时，可能会出现一些混乱了，今天主要来说一下，如何正确的使用分部视图！

**分部视图**在action中返回一定要用`PartialView()`，而不要偷懒使用`View()`，因为，如果你使用View()渲染视图，系统会认为你是一个标准视图，会为你加个默认的母板页（Layout)，除非你显式的设置了Layout这个属性。

![layout=null](http://i.imgur.com/66PeL4y.png)

之前的程序代码：

```cs
1  　　　 public ActionResult PartialLogon()
2         {
3             return View();//会认识它的标准视图，所以会加上默认的Layout
4         }
```
当返回视图后，你的分部视图会被加上默认的母板页，这不是我们希望看到的，当然有些同学会不先麻烦的在页面上显式的加上Layout=null

事实上，如果你正确的返回分部视图，这行当然是不用加的。

正确的写法：
```cs
public ActionResult PartialLogon()
   {
       return PartialView();//会将页面的Layout自动设为null
   }
```

我想这后我们把这两个东西换个名称：
* PartialView()=>渲染视图=>不带Layout
* View()=>渲染分部视图=>自动加上Layout

