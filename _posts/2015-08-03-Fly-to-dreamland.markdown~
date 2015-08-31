---
layout: post
title: webpy
category: 心情
tags: Bazinga
description: nice

---
----------

![](https://raw.githubusercontent.com/Ashtray/Ashtray.github.io/master/imag/honey.png)  


实习题目拿到，是个关于路由配置协议的实现，需要搭建一个web服务器端，当然，没有任何理由从头做起，牛顿他老人家也是站在巨人的后背上呢（摔~）。  


--------


<!-- more -->


>就目前的需求来看，ACS端就是配置给每个攻城狮链接路由器调试的工具，那么需求比较简单，能够胜任处理标准的HTTP请求即可，考虑到复杂的实现并不会给这个项目增加一分可取之处，反而会让人感觉复杂，功能累赘并不是用，还有就是安装方便，这一点上已经很难容以满足了，全司茫茫多人，可不想每个人在就如何安装使用上邮件我，虽然我很闲。  

之所以选择webpy，正因为它够简单，尤其是对于我这种python新人来说。它是一款轻量级的python web开发框架，对于个人开发小应用来说很适合。

------

###webpy install###  

-----


	下载：wget http://webpy.org/static/web.py-0.37.tar.gz  
	安装：python setup.py install  
	webpy 'hello world'  
 
可参考webpy的官方文档：http://webpy.org/docs/0.3/tutorial  

hello, world如下：  

	import web  

	urls = (  
  	  '/', 'index'  
	)  

	class index:  
 	   def GET(self):  
   	     return "Hello, world!"  

	if __name__ == "__main__":  
 	   app = web.application(urls, globals())  
  	   app.run()  

---------------

在webpy中，url请求的映射在urls元组中，如上图中GET ip:port/   会直接调用index类的GET方法，返回字符串'hello, world!'；  

>class index中包含了一个GET方法，用来处理与index相应的url的GET请求的；

>>在主函数中，只需要创建一个application对象，运行就可以开启一个简单的web应用，默认的地址为：127.0.0.1:8080  

-------


###GET && POST### 
 
-----


web包含两种方法：GET和POST  

>对于GET，可以采用：  


	class index:   
  	  def GET(self):  
      		  return "Hello, world!"  

>而，对于POST，采用：  

	class index:  
   	   def POST(self):  
     	   	 data = web.input(name=None)  
       		 return "Hello, " + data.name + "!"  

------

###html模板###

----


在webpy中，一般采用templates来存放html页面文件。大概的访问方式如下：  


	urls = (  
  	        '/img', 'image'  
	         )  

	render = web.template.render('templates')  

	class image:  
    	   def GET(self):  
                return render.image()      
  
urls中定义了url映射，访问ip:port/img会直接条用class image来处理；  

web.template.render(path)是用来指定存放html的目录，上面指定了html的指定存放位置位于当前文件夹下的templates文件下；  

>返回的render.image()表示在render所指定的目录下寻找image.html文件并作为返回结果。  

 
	class show:  
   	      def GET(self):  
      	  		return render.show('hello world!')  
  
	$def with(str)  
	<html>  
  	  <body>  
        	 $for i in range(5):  
            	<h1>$str</h1>  
   	  <body>  
	</html>   

show类是用来展示字符串'hello world!'，下面的html为show.html，webpy支持模板，支持参数以$def with()开始作为函数的开始；  

在html中可以使用python语句，但语句前需要添加$，在上面的html中str会在页面上打印5次。  

------

###静态文件###  

------


在webpy中，提供了默认的静态文件的访问方式  
webpy作为服务器时，在当前目录下建立static目录，webpy会自动在该目录下寻找静态文件  

在 Apache 中可以使用 Alias 指令，在处理 web.py 之前将请求映射到指定的目录。  

---------

###webpy db###

---------


在webpy中提供了数据库访问的API，其实从源码中可以看出来是对MySQLdb的封装，但为了方便起见用起来还是可以的。  


	db = web.database(dbn='mysql', db='test', user='root', pw='123123')  
 
	def new_post(title, content):  
    		db.insert('news', title=title, content=content, posted_on=datetime.datetime.utcnow())  

	def get_post(id):  
    		try:  
       		  return db.select('news', where='id=$id', vars=locals())[0]  
    		except IndexError:  
        		return None  

	def get_posts():  
   	   return db.select('news', order = 'id DESC')  

	def del_post(id):  
   	    db.delete('news', where = 'id = $id', vars = locals())  

	def update_post(id, title, content):  
    		db.update('news', where='id = $id', vars=locals(), title=title, content=content)   

------

###webpy也支持事务：###

---------


	import web  

	db = web.database(dbn="postgres", db="webpy", user="foo", pw="")  
	t = db.transaction()  
	try:  
   		db.insert('person', name='foo')  
   		db.insert('person', name='bar')  
	except:  
   		t.rollback()  
   		raise  
	else:  
   		t.commit()   


--------------------

[个人主页](http://Ashtray.github.io)

----------

	「你还是一个人吗」
	「难道我回变成一条狗吗」  

>「汪汪汪」

-----------------
    
 
