title: 通过源安装的tomcat7的安装路径
date: 2014-09-20 20:23:00
categories: Develop
tags: [tomcat7]
---

比较坑爹得拆成了三个路径，所以这个版本只能用来部署，开发的话还是选择编译安装吧。

###/etc/tomcat7 

    ├── Catalina
    
    │   └── localhost
    
    ├── catalina.properties
    
    ├── context.xml
    
    ├── logging.properties
    
    ├── policy.d
    
    │   ├── 01system.policy
    
    │   ├── 02debian.policy
    
    │   ├── 03catalina.policy
    
    │   ├── 04webapps.policy
    
    │   └── 50local.policy
    
    ├── server.xml
    
    ├── tomcat-users.xml
    
    └── web.xml

<!-- more --> 

###/usr/share/tomcat7 

    ├── bin
    
    │   ├── bootstrap.jar
    
    │   ├── catalina.sh
    
    │   ├── catalina.sh.save
    
    │   ├── catalina-tasks.xml
    
    │   ├── configtest.sh
    
    │   ├── daemon.sh
    
    │   ├── digest.sh
    
    │   ├── setclasspath.sh
    
    │   ├── shutdown.sh
    
    │   ├── startup.sh
    
    │   ├── tomcat-juli.jar -> ../../java/tomcat-juli.jar
    
    │   ├── tool-wrapper.sh
    
    │   └── version.sh
    
    ├── defaults.md5sum
    
    ├── defaults.template
    
    ├── lib
    
    │   ├── annotations-api.jar -> ../../java/tomcat-annotations-api-7.0.52.jar
    
    │   ├── catalina-ant.jar -> ../../java/catalina-ant-7.0.52.jar
    
    │   ├── catalina-ha.jar -> ../../java/tomcat-catalina-ha-7.0.52.jar
    
    │   ├── catalina.jar -> ../../java/tomcat-catalina-7.0.52.jar
    
    │   ├── catalina-tribes.jar -> ../../java/catalina-tribes-7.0.52.jar
    
    │   ├── commons-dbcp.jar -> ../../java/commons-dbcp.jar
    
    │   ├── commons-pool.jar -> ../../java/commons-pool.jar
    
    │   ├── el-api.jar -> ../../java/tomcat-el-api-2.2.jar
    
    │   ├── jasper-el.jar -> ../../java/tomcat-jasper-el-7.0.52.jar
    
    │   ├── jasper.jar -> ../../java/tomcat-jasper-7.0.52.jar
    
    │   ├── jsp-api.jar -> ../../java/tomcat-jsp-api-2.2.jar
    
    │   ├── servlet-api.jar -> ../../java/tomcat-servlet-api-3.0.jar
    
    │   ├── tomcat-api.jar -> ../../java/tomcat-api-7.0.52.jar
    
    │   ├── tomcat-coyote.jar -> ../../java/tomcat-coyote-7.0.52.jar
    
    │   ├── tomcat-i18n-es.jar -> ../../java/tomcat-i18n-es-7.0.52.jar
    
    │   ├── tomcat-i18n-fr.jar -> ../../java/tomcat-i18n-fr-7.0.52.jar
    
    │   ├── tomcat-i18n-ja.jar -> ../../java/tomcat-i18n-ja-7.0.52.jar
    
    │   ├── tomcat-jdbc.jar -> ../../java/tomcat-jdbc-7.0.52.jar
    
    │   └── tomcat-util.jar -> ../../java/tomcat-util-7.0.52.jar
    
    ├── logrotate.md5sum
    
    └── logrotate.template


###/var/lib/tomcat7

    ├── common
    
    │   └── classes
    
    ├── conf -> /etc/tomcat7
    
    ├── logs -> ../../log/tomcat7
    
    ├── server
    
    │   └── classes
    
    ├── shared
    
    │   └── classes
    
    ├── webapps
    
    │   ├── ROOT
    
    │   │   ├── index.html
    
    │   │   └── META-INF
    
    │   │       └── context.xml



