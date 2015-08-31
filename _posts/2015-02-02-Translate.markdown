---
layout: post
title: 特别的翻译技巧
category: 翻译
tags: Bazinga
description: nice

---
----------

![](https://raw.githubusercontent.com/Ashtray/Ashtray.github.io/master/imag/%E7%BA%A2%E5%86%9B.jpg)  


	Lolita:   

>Lolita, light of my life, fire of my loins. My sin, my soul. Lo-lee-ta: the tip of the tongue taking a trip of three steps down the palate to tap, at three, on the teeth. Lo. Lee. Ta.   

 <!-- more -->

请读一读Lolita开头三个版本的译文:  

>洛丽塔，我的生命之光，我的欲念之火。我的罪恶，我的灵魂。 洛－丽－塔：分三下，舌尖自上颚往下，轻落到牙齿上。洛。丽。塔。   

>洛丽塔，照亮我生命的光，点燃我情欲的火。我的罪恶，我的灵魂。洛－丽－塔：舌尖顶到上腭做一次三段旅行。洛。丽。塔。  

>洛丽塔是我的生命之光，欲望之火，同时也是我的罪恶，我的灵魂。洛－丽－塔：舌尖得由上腭向下移动三次，到第三次再轻轻贴在牙齿上。洛-丽-塔。   


-------------------------

可我苦苦查找资料 为何酷比的工科男会这样翻译呢：  

>If we change our query to use an index, we’ll see that a BtreeCursor was used, as well as the index used to fulfill the
request:  
	
	db.unicorns.find({name: 'Pilot'}).explain()  

>Replication  

>MongoDB replication works in some ways similarly to how relational database replication works. All production deployments should be replica sets, which consist of ideally three or more servers that hold the same data. Writes are sent to a single server, the primary, from where it’s asynchronously replicated to every secondary. You can control whether you allow reads to happen on secondaries or not, which can help direct some special queries away from the primary, at the risk of reading slightly stale data. If the primary goes down, one of the secondaries will be automatically elected to be the new primary. Again, MongoDB replication is outside the scope of this book.  

>Sharding  

>MongoDB supports auto-sharding. *blablabla*……   

-----------------------------我是分割线--------------------------------------

>如果我们改用索引来查询，我们会看到的查询使用的 `BtreeCursor`，以及用以查询的索引：  

	db.unicorns.find({name: 'Pilot'}).explain()  

>###射后不理 的*写操作*  `(这样翻译真的大丈夫吗)`


>之前我们有提到过，`MongoDB` 中的写操作默认为射后不理。这样做可以获得一定的性能提高，同时也带来了系统奔溃时丢失数据的风险。有意思的是这种类型的写操作在插入/更新破坏了某唯一的约束时，是不返回错误的。若需要得到写失败的通知，就要在插入后调用 `db.getLastError()`。很多驱动都把这一细节封装起来了，取而代之的是安全的写操作——往往会多提供一个参数用来设置。  
可惜的是`shell`并不提供安全的插入，因此我们就无法实验这一特性了。  

>分片（`sharding`）  

>MongoDB支持自动分片。*布拉布拉布拉*……  

--------------------

[个人主页](http://Ashtray.github.io)

----------

	「你还是一个人吗」
	「难道我回变成一条狗吗」  

>「汪汪汪」

-----------------
    
 
