title: Nginx上安装SSL证书
date: 2015-04-13 17:10:35
categories: Develop
tags: [SSL, Nginx,Web]
---

>长话短说，省去各种申请过程，讲讲扼要的部分

##生成CRS和密钥
方便一点的做法是使用在线的生成器，或者通过openSSL生成：

    openssl req -nodes -newkey rsa:2048 -nodes -keyout your_key_name.key 
    -out your_csr_name.csr -subj "/C=GB/ST=London/L=London/O=your_server
    /OU=IT/CN=your_server"
   
   <!-- more --> 
##将CRS提交给证书提供商
每个提供商步骤不太一样，讲讲COMODO的吧。
通过验证之后，会向你的邮箱发送一个压缩包，压缩包里包含四个文件

 - AddTrustExternalCARoot.crt
 - COMODORSAAddTrustCA.crt
 - COMODORSADomainValidationSecureServerCA.crt
 - your_server.crt

##合并证书

    cat your_server.crt COMODORSADomainValidationSecureServerCA.crt COMODORSAAddTrustCA.crt AddTrustExternalCARoot.crt > your_server.pem 
    
    
##上传证书并配置Nginx
站点服务器上只需要一开始生成的key和之后合并的pem文件，Nginx如下配置：

    server {
        ....
        ssl    on; 
        ssl_certificate         /etc/nginx/ssl/your_server.pem;
        ssl_certificate_key     /etc/nginx/ssl/your_key_name.key;
        ...
    }
一切顺利的话然后重启一下Nginx就能生效了

    sudo nginx -s reload
