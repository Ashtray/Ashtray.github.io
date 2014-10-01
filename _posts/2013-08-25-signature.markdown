---
layout: post
title: linux下自建CA根证书
category: 笔迹
tags: Tech
description: linux下自建CA 根证书

---

![](https://raw.githubusercontent.com/Ashtray/Ashtray.github.io/master/imag/%E8%AE%A4%E8%AF%81.png)  
  
linux下自建CA 根证书 并给服务器颁发证书过程

<!-- more -->

-------------

###linux下自建CA 根证书 并给服务器颁发证书过程  

建立一个根目录放置所有文件，这里选择`/etc/ssl`  
建立一个`private`文件夹存放CA自己的私钥  
建立`certs`文件夹存放为`server`颁发的证书的副本  
创建一个`index.txt`文件作为证书的数据库文件  
创建一个`serial`文件作为颁布证书的序号，先写入`01`  

--------

	#mkdir private certs  
	#chmod 777 private  
	#touch index.txt  
	#echo ‘01’ > serial  

--------

创建一个CA配置文件caconfig.conf，名称任意，输入参考网站给的内容:  

	[ ca ]
	default_ca= local_ca
	
	[ local_ca ]
	dir= /etc/ssl
	certif icate = $dir/cacert.pem
	database= $dir/index.txt
	new_certs_dir = $dir/certs
	private_key = $dir/private/cakey.pem
	serial= $dir/serial
	default_crl_days= 365
	default_days= 1825
	default_md= sha1
	policy= local_ca_policy
	x509_extensions = local_ca_extensions
	copy_extensions = copy

	[ local_ca_policy ]
	commonName=optional
	stateOrProvinceName = optional
	countryName=optional
	emailAddress=option al
	organizationName=optional
	organizationalUnitName = optional

	[ local_ca_extensions ]
	basicConstraints= CA:false

	[ req ]
	default_bits = 2048
	default_keyfile = /etc/ssl /private/cakey.pem
	default_md= sha1
	prompt= no
	distinguished_name= root_ca_distinguished_name
	x509_extensions= root_ca_extensions

	[ root_ca_distinguished_name ]
	commonName= MyOwn Root Certificate Authority
	stateOrProvinceName = NC
	countryName= US
	emailAddress= root@tradeshowhell.com
	organizationName= Trade Show Hell
	organizationalUnitName = IT Department

	[ root_ca_extensions ]
	basicConstraints= CA:true


[local_ca]下，dir为所有文件根目录，这里改为`/etc/ssl`  
`new_certs_dir`存放为服务器颁发的证书的副本，这里改为`/etc/ssl/certs`   certificate为CA根证书存放目录  
database为证书数据库文件  
private\_key为CA的私钥文件  
serial为证书序列号文件，每生成一个新证书会加1，这里不需更改。  
将[local_ca_policy]的各项都改为optional，创建根证书时可不必输入这些信息  
[ req ] 下的default_keyfile也必须修改，所指文件是生成CA私钥的位置和文件名，这里改为`/etc/ssl/private/cakey.pem`  
配置文件可以通过环境变量`export OPENSSL_CONF=/etc/ssl/caconfig.conf`件传给openssl，这里用参数-config   

####生成ca根证书：  

>openssl req -x509 -newkey rsa:2048 -out cacert.pem -outform PEM -days 365 -config “caconfig.conf”  
`-newkey rsa:bits`生成bits大小rsa新密钥  

(-in输入文件-out输出文件-outform输出格式，DER或PEM)  
提示给CA私钥设置密码，这个密码在生成服务器证书时会要求输入  
会生成CA根证书cacert.pem和在private目录下的CA私钥cakey.pem  

给server颁发证书需要一个请求文件，先生成一个证书请求文件和服务器的私钥，服务器的信息通过一个配置文件server.conf给出：  

	[ req ]
	prompt = no
	distinguished_name = server_distinguished_name
	req_extensions = v3_req
	
	[ server_distinguished_name ]
	commonName = localhost
	stateOrProvinceName = NC
	countryName = US
	emailAddress = root@tradeshowhell.com
	organizationName = My Organization Name
	organizationalUnitName = Subunit of My Large Organization

	[ v3_req ]
	basicConstraints = CA:FALSE
	keyUsage = nonRepudiation, digitalSignature, keyEncipherment
	subjectAltName = @alt_names
	
	[ alt_names ]
	DNS.0 = localhost
	DNS.1 = localhost

为了让访问的域名和证书中域名吻合，需修改:  
[server_distinguished_name]下commonName  
[alt_names]下DNS.0和DNS.1为localhost  
执行命令前设置环境变量  

	export OPENSSL_CONF=/etc/ssl/server.conf   

或用参数传递

>openssl req -newkey rsa:1024 -keyout serverkey.pem -keyform PEM -out tempreq.pem -outform PEM -config “server.conf”  

(-keyout file 发送密钥的文件-keyform 密钥文件格式)  
这一步会提示输入密码，是给`serverkey.pem`文件设置的。  
在设置完default-ssl后重启apache2会让输入这里设置的密码。  
生成两个文件`serverkey.pem`服务器私钥，和证书请求文件`tempreq.pem`。  

####利用tempreq.pem请求文件生成证书  

>openssl ca -in tempreq.pem -out server_crt.pem -config  “caconfig.conf”  

----

`tempreq.pem`文件为输入，`server_crt.pem`为输出的服务器证书，因为是请求CA生成证书，要使用CA的配置文件`caconfig.conf`而不是server的,如果之前把环境变量OPENSSL_CONF设为了`server.conf`,这里要改回来。会要求输入CA私钥的密码。  

当前目录下会生成`server_crt.pem`服务器证书文件，查看certs文件夹里有了一个`01.pem`，是`server_crt.pem`的副本,serial内容变成02，index.txt里有了一则证书信息。  

修改`/etc/apache2/sites-available中default-ssl`  
文件的SSLCertificateFile以及SSLCertificateKeyFile  
前者改为给服务器颁发的证书文件，`server_crt.pem`或01.pem都可  
后者是服务器私钥`serverkey.pem`，不是CA的私钥cakey.pem。  

	SSLCertificateFile /etc/ssl/server_crt.pem
	SSLCertificateKeyFile /etc/ssl/serverkey.pem

执行  `#a2enmod ssl`和`#a2ensite default-ssl`启动SSL模块  

>执行#service apache2 restart重启服务器，会让输入服务器私钥serverkey.pem的密码。   
>在firefox的Edit->Preferences->Advanced->Encryption->View Certificates->Authorities中import CA的根证书/etc/ssl/cacert.pem文件  
>在浏览器中输入https://localhost，前面会显示经过验证。




--------------------

[个人主页](http://Ashtray.github.io)

----------

	「你还是一个人吗」
	「难道我回变成一条狗吗」
	「`汪汪汪`」
-----------------
    
 
