---
layout: post
title: TR069 协议栈
category: 技术
tags: Bazinga
description: nice

---
----------

![](https://raw.githubusercontent.com/Ashtray/Ashtray.github.io/master/imag/JUSTICE.png)  

ACS为自动配置服务器，负责对终端设备CPE进行管理。  

ACS与CPE间的接口为南向接口，ACS与管理系统间的接口为北向接口。TR069协议主要定义了南向接口。  


<!-- more -->

![](http://p.blog.csdn.net/images/p_blog_csdn_net/Dreamcode/EntryImages/20090205/tr069framework.jpg)

---------

TR069的实现（协议栈）
  
		（1）TR069协议基于TCP层；  
		（2）ACS与CPE间的消息传输使用HTTP1.1，ACS为HTTP服务器，CPE为HTTP客户端；  
		（3）消息的具体内容使用SOAP包进行封装，SOAP包是一个包含SOAP Head（SOAP头）和 SOAP Body （SOAP体）组成的XML文档   


![](http://p.blog.csdn.net/images/p_blog_csdn_net/Dreamcode/EntryImages/20090205/SOAPMessage.jpg)

![](http://p.blog.csdn.net/images/p_blog_csdn_net/Dreamcode/EntryImages/20090205/SOAPBindingWithHttp.jpg)

-----


ACS的管理方法就是对CPE上的管理函数进行远程调用，因此需要向CPE传输要调用的函数名及参数，这些内容包含在SOAP体中。ACS并不直接对设备本身的接口进行调用 ，ACS所使用的函数为TR069的标准函数（称作TR-069 RPC Methods），CPE需要通过一个设备上的中间层解析出RPC方法，再由这个中间层调用设备自身的接口，这个中间层就是TR069 Agent。  

使用SSL/TLS并不强制要求，确保CPE和ACS之间基于证书的鉴权
 
		——————————————  
		CPE/ACS Management App  
		——————————————  
		RPC Method  
		——————————————  
		SOAP
		——————————————  
		HTTP 1.1
		——————————————  
		SSL3.0 orTLS1.0
		——————————————  
		TCP/IP
		——————————————  


>TR069协议的RPC函数（或称RPC方法）  
>>如上所述ACS与CPE之间通过TR069协议特有的RPC方法进行互操作。  
  CPE函数由ACS调用，用来对CPE进行管理如：设置CPE参数、获取CPE参数、硬件升级、重启设备等；  
  ACS函数由CPE调用，用来向ACS上报状态信息，请求硬件镜像文件下载（用来升级硬件）等等。  
  这些方法中有些是必须的有些是可选的（Required/Optional）  

![](http://p.blog.csdn.net/images/p_blog_csdn_net/Dreamcode/EntryImages/20090206/RPCMethods.jpg)

----------

>网络架构上包含两种设备类型，因此包含两套数据模型：  
   （1）网络网关设备(Internet Gateway Device)，如路由器，缩写为IGD DM(Data Model)  
   （2）网关下的普通局域网设备(LAN Device)，如可视电话，缩写为Device DM(Data Model)   


TR069协议定义了其中网关设备的数据模型  

>>参数名  ：使用由类似树型的点分层关系组织起来。树干为需要配置的对象，树叶为具体的配置参数，所有配置参数都具有是否可读写属性。  
              如：InternetGatewayDevice.IPPingDiagnostics.Interface  
>>参数类型：基于SOAP的数据类型  

函数具体功能（TR069协议的功能）  
 （1）自动配置和动态业务服务（Auto-configuration and dynamic service provisioning）  
      TR069协议规定了ACS可以在CPE第一次上线或上线后的任何时候对CPE进行配置如：ACS使用CPE函数中的set方法对CPE进行配置。  
 （2）软件和硬件镜像管理（Software/Firmware image management）  
      TR069协议规定了软件和硬件版本号鉴权机制，和下载触发机制（CPE和ACS都可以主动发起下载），以及下载失败后的通知机制  

        如：ACS使用CPE函数中的download和upload方法，下载新的版本以供升级设备或上传当前版本软件镜像文件  

 （3）状态和性能监控（Status and performance monitoring）  
      TR069协议规定了一种CPE随时（有些地方翻译为“动态”）上报给（有些地方翻译为“通知”）ACS它自身状态的机制  

        如：CPE使用ACS函数的inform方法：CPE通过定时调用ACS的inform方法上报硬件状态信息  

                           ［Inform SOAP包信息］  

		DeviceID    标识CPE
		Event    此次Inform发起的原因 如：2 PERIODIC CPE周期性的发起
		Inform调用，上报状态
		MaxEnvelopes    HTTP Response 可携带的SOAP包（信封）      
		CurrentTime    CPE 当前时间
		RetryCount    这次Session最大的重复连接数
		ParameterList    这次Inform 函数所代参数

-----------

TR069协议的安全性  

>TR069协议支持使用“SSL/TLS”的证书鉴权机制，CPE必须使用ACS提供的证书来鉴权ACS。  
>>如果CPE没有经过SSL/TLS认证，ACS就必须使用HTTP对CPE进行认证；如果已经通过“SSL/TLS”认证，ACS可以使用“HTTP basic”认证或“HTTP digest”认证.  


--------






--------------------

[个人主页](http://Ashtray.github.io)

----------

	「你还是一个人吗」
	「难道我回变成一条狗吗」  

>「汪汪汪」

-----------------
    
 
