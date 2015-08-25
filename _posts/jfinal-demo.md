title: 快速上手JFinal
date: 2015-07-22 23:20:00
categories: Develop
tags: [Java,JFinal]
---

JFinal是国产的MVC框架，由 Handler、Interceptor、Controller、Render、Plugin 五大部分组成。本文通过一个例子上手JFinal，旨在熟悉JFinal中各组件的用法。

###目标
 - 用户登录/注册
 - 用户上传图片
 - 登录权限验证

<!-- more --> 

####访问路径
用户登录:IP/user/login
用户注册：IP/user/register
用户上传图片：IP/user/image
登录后能访问： IP/user/show

###依赖
 - druid-1.0.5.jar
 - cos-26Dec2008.jar
 - jfinal-2.0-bin.jar
 - log4j-1.2.16.jar
 

###AppConfig
JFinal需要一个继承 `JFinalConfig` 的子类，我们这里取名叫AppConfig，这个类的名字是随便取的，我这里取AppConfig。

    import com.jfinal.config.*;
    import com.jfinal.plugin.activerecord.ActiveRecordPlugin;
    import com.jfinal.plugin.druid.DruidPlugin;
    import com.jfinal.render.ViewType;
    import controller.IndexController;
    import controller.UserController;
    import model.User;
    
    /**
     * Created by reeco_000 on 2015/7/22.
     */
    public class AppConfig extends JFinalConfig{
    
    @Override
    public void configConstant(Constants constants) {
	    constants.setEncoding("UTF-8");
	    constants.setDevMode(true);
	    constants.setViewType(ViewType.JSP);
    }
    
    @Override
    public void configRoute(Routes routes) {
	    routes.add("/", IndexController.class);
	    routes.add("/user", UserController.class);
    }
    
    @Override
    public void configPlugin(Plugins plugins) {
		//这里启用Jfinal插件

   	 PropKit.use("jdbc.properties");
        final String URL =PropKit.get("jdbcUrl");
        final String USERNAME = PropKit.get("user");
        final String PASSWORD =PropKit.get("password");
        final Integer INITIALSIZE = PropKit.getInt("initialSize");
        final Integer MIDIDLE = PropKit.getInt("minIdle");
        final Integer MAXACTIVEE = PropKit.getInt("maxActivee");

        DruidPlugin druidPlugin = new DruidPlugin(URL,USERNAME,PASSWORD);
        druidPlugin.set(INITIALSIZE,MIDIDLE,MAXACTIVEE);
        druidPlugin.setFilters("stat,wall");
        plugins.add(druidPlugin);

        ActiveRecordPlugin activeRecordPlugin = new ActiveRecordPlugin(druidPlugin);
        activeRecordPlugin.addMapping("user","userid", User.class);
        plugins.add(activeRecordPlugin);
    
    }
    
    @Override
  	  public void configInterceptor(Interceptors interceptors) {
    		//这里用于配置全局的拦截器，对所有请求进行拦截
			
    }
    
    @Override
	    public void configHandler(Handlers handlers) {
    
    }
    }

###配置文件
####Jdbc.properties
在根目录下新建一个Jdbc.properties,用来保存数据库连接信息。我们在AppConfig中需要加载它，使用了JFinal的工具类PropKit。

    driverClass=com.mysql.jdbc.Driver
    jdbcUrl=jdbc:mysql://localhost:3306/school
    user=root
    password=root
    
    initialSize=1
    minIdle=1
    maxActivee=20

####log4j.properties
这样就能使用log4j进行日志统计，在需要记录的地方获取logger对象即可

	log4j.rootLogger=INFO, stdout, file
	log4j.appender.stdout.Target=System.out
	log4j.appender.stdout=org.apache.log4j.ConsoleAppender
	log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
	log4j.appender.stdout.layout.ConversionPattern=%n%-d{yyyy-MM-dd HH:mm:ss}%n[%p]-[Thread: %t]-[%C.%M()]: %m%n
	
	# Output to the File
	log4j.appender.file=org.apache.log4j.FileAppender
	log4j.appender.file.File=./web.log
	log4j.appender.file.layout=org.apache.log4j.PatternLayout
	log4j.appender.file.layout.ConversionPattern=%n%-d{yyyy-MM-dd HH:mm:ss}%n[%p]-[Thread: %t]-[%C.%M()]: %m%n
	
	# Druid
	log4j.logger.druid.sql=warn,stdout
	log4j.logger.druid.sql.DataSource=warn,stdout
	log4j.logger.druid.sql.Connection=warn,stdout
	log4j.logger.druid.sql.Statement=debug,stdout
	log4j.logger.druid.sql.ResultSet=warn,stdout

###web.xml
然后在web.xml里配置JFinal的核心filter和我们写好的AppConfig

	  <filter>
	        <filter-name>jfinal</filter-name>
	        <filter-class>com.jfinal.core.JFinalFilter</filter-class>
	        <init-param>
	            <param-name>configClass</param-name>
	            <param-value>AppConfig</param-value>
	        </init-param>
	    </filter>
	
	    <filter-mapping>
	        <filter-name>jfinal</filter-name>
	        <url-pattern>/*</url-pattern>
	    </filter-mapping>

###Model
定义用户实体，代码非常简单

    package model;
    
    import com.jfinal.plugin.activerecord.Model;
    
    /**
     * Created by reeco_000 on 2015/7/22.
     */
    public class User extends Model<User>{
    }

继承Model就行了，无需 getter、setter 方法，无需Annotation，无需xml

这里数据库中user设计也很简单，就三个字段，userid,username,password,贴下sql:

    DROP TABLE IF EXISTS `user`;
    CREATE TABLE `user` (
      `userid` int(11) NOT NULL AUTO_INCREMENT,
      `username` varchar(255) NOT NULL,
      `password` varchar(255) NOT NULL,
      PRIMARY KEY (`userid`)
    ) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8;


###Service

service层，封装业务逻辑。这里用到了ActiveRecord插件，需要在AppConfig中注册。

	
	   public void configPlugin(Plugins plugins) {
			//这里启用JFinal插件
	
	        DruidPlugin druidPlugin = new DruidPlugin(URL,USERNAME,PASSWORD);
	        druidPlugin.set(INITIALSIZE,MIDIDLE,MAXACTIVEE);
	        druidPlugin.setFilters("stat,wall");
	        plugins.add(druidPlugin);
	
	        ActiveRecordPlugin activeRecordPlugin = new ActiveRecordPlugin(druidPlugin);
			//添加Model类和数据库表的映射。user指的是表名，userid指的是主键
	        activeRecordPlugin.addMapping("user","userid", User.class);
	        plugins.add(activeRecordPlugin);
	    
	    }
数据库连接池使用的是Druid，JFinal还支持C3P0。
JFinal使用的是Db+Record 完成ORM。Db类提供了大量查询的方法，这种模式类似于ruby on rails。

	package service;
	
	import com.jfinal.plugin.activerecord.Db;
	import com.jfinal.plugin.activerecord.Record;
	
	/**
	 * Created by reeco_000 on 2015/7/22.
	 */
	public class UserService {
	
	    public boolean add(String username,String password){
	        String SQL = "SELECT userid FROM user WHERE username =?";
	        Integer result = Db.queryFirst(SQL, username);
	        if(result==null){
	            Record user = new Record().set("username", username).set("password", password);
	            Db.save("user", user);
	            return true;
	        }
	        return false;
	    }
	
	    public boolean login(String username,String password){
	        String SQL = "SELECT userid FROM user WHERE username =? and password=?";
	        Integer result = Db.queryFirst(SQL, username, password);
	        if(result!=null)
	            return true;
	        else return false;
	    }
	}
	  
###Interceptor
JFinal里的拦截器有三个作用域，全局，类和方法。利用JFinal的AOP，使用起来也非常方便。拦截器定义也非常简单，只需继承Interceptor接口重写intercept方法。全局的拦截器是在AppConfig里配置：

	 public void configInterceptor(Interceptors interceptors) {
    		//这里用于配置全局的拦截器，对所有请求进行拦截

			// 添加控制层全局拦截器
			interceptors.addGlobalActionInterceptor(new GlobalActionInterceptor());
			// 添加业务层全局拦截器
			interceptors.addGlobalServiceInterceptor(new GlobalServiceInterceptor());
    }
类和方法使用@Before(Class.class)即能使用，具体可以看下面Controller的例子。在JFinal2还提供了Inject拦截器，不过我们的例子里没这需求，所以没用到，具体各位还是看官网文档吧。

####AuthInterceptor
简易的权限拦截器，这里的作用是只有登录的才能执行。在session里设置了一个变量flag，当是true时就执行。

	package interceptor;
	
	
	import com.jfinal.aop.Interceptor;
	import com.jfinal.aop.Invocation;
	import com.jfinal.core.Controller;
	
	/**
	 * Created by reeco_000 on 2015/7/22.
	 */
	public class AuthInterceptor implements Interceptor {
	
	    @Override
	    public void intercept(Invocation invocation) {
	        Controller controller = invocation.getController();
	        Boolean loginUser = controller.getSessionAttr("flag");
	        if (loginUser ==true )
	            invocation.invoke();
	        else
	            controller.redirect("/");
	    }
	}

####LoginValidator
Validator是JFinal提供的校验组件。其核心是Interceptor，所以用法和Interceptor类似，继承Validator类即可。
 
 - validate
	提供了一系列的validateXXX方法

 - handleError
	处理错误，常见的用法是 
	
        controller.keepPara();
    	controller.render("register.html");
    	
	意思是返回原页面，保持传入的参数。


<br />

	package validator;
	
	import com.jfinal.core.Controller;
	import com.jfinal.validate.Validator;
	
	/**
	 * Created by reeco_000 on 2015/7/22.
	 */
	public class LoginValidator extends Validator {
	    @Override
	    protected void validate(Controller c) {
	        validateRequiredString("username","nameError","username is null");
	        validateRequiredString("password","passError","username is null");
	    }
	
	    @Override
	    protected void handleError(Controller c) {
	
	    }
	}


###Controller
JFinal的Controller需要继承Controller类，类的映射是在AppConfig中配置：

    public void configRoute(Routes routes) {
	    routes.add("/", IndexController.class);
	    routes.add("/user", UserController.class);
    }
我们这里配置意思是/访问到IndexController这个类，/user 访问到UserController。/user/login 默认访问UserController.login()方法，这点类似于struct2,如果之前有基础，上手会非常快。

####UserController

映射 /user,在login()上使用了校验拦截器LoginValidator，show()使用了权限拦截器AuthInterceptor。

	package controller;
	
	import com.jfinal.aop.Before;
	import com.jfinal.core.Controller;
	import interceptor.AuthInterceptor;
	import service.UserService;
	import validator.LoginValidator;
	
	/**
	 * Created by reeco_000 on 2015/7/22.
	 */
	public class UserController extends Controller{
	
	    private UserService userService = new UserService();
	
	    public void index(){}
	
	    @Before(LoginValidator.class)
	    public void login(){
	        String username = getPara("username");
	        String password = getPara("password");
	        boolean loginCheck = userService.login(username,password);
	        if(loginCheck){
	            renderJson("10000");
	            getSession().setAttribute("flag",true);
	        }
	
	        else
	            renderJson("10001");
	    }
	
	    public void register(){
	        String username = getPara("username");
	        String password = getPara("password");
	        boolean result = userService.add(username,password);
	        if(result)
	            renderJson("10010");
	        else
	            renderJson("10011");
	    }
	
	    @Before(AuthInterceptor.class)
	    public void show(){
	        renderJsp("user.jsp");
	    }
	
	    public void image(){
	        try{
	            getFile(getPara("img"),"UTF-8");
	            renderJson("20010");
	        } catch (Exception e){
	            renderJson("20012");
	        }
	    }
	}


 - getPara() 从请求中获取参数
 - getFile()  支持文件上传
 - renderJson() 返回JSON，JFinal提供了一系列的render方法，目前支持的视图类型有：
FreeMarker、JSP、Velocity、JSON、File、Text、Html 等等。


####IndexController

	package controller;
	
	import com.jfinal.core.Controller;
	
	/**
	 * Created by reeco_000 on 2015/7/22.
	 */
	public class IndexController extends Controller{
	
	    public void index(){
	        renderJsp("index.jsp");
	    }
	}

index是Controller默认调用的方法

###总结
![](http://img-storage.qiniudn.com/15-7-28/60814655.jpg)
最后整个工程的文件如上图所示，代码非常简单，我也就不上传献丑了。主要介绍了JFinal的Interceptor、Controller、Render、Plugin，Handler是对Controller和Interceptor的补充。ORM使用的是Db+ActiveRecord。JFinal还有很多组件没有提到，各位请到官网查看详细文档。
