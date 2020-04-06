# 使用本框架构建一个项目并打包发布到Linux上





这里是暂时的，之后要详细规划怎么写，

## 安全配置
框架已经配置了一些不需要携带token就可以访问的资源，具体如下：
* UI类服务

|   antPatterns|    说明|
|--------------|--------|
|   /milepost-actuator/\*\*   |SpringBoot的actuator监控 | 
|   /\*\*/\*.css、 /\*\*/\*.js、 /\*\*/\*.jpg、 /\*\*/\*.png、 /\*\*/\*.gif、 /\*\*/\*.ico、 /\*\*/\*.woff、 /\*\*/\*.ttf  |静态资源| 

* Service类服务

|   antPatterns|    说明|
|--------------|--------|
|   /milepost-actuator/\*\*   |SpringBoot的actuator监控 | 
|   /swagger-resources/\*\*、 /v2/\*\*、 /swagger-ui.html/\*\*、 /webjars/\*\*  |swagger页面相关| 
|   /enc   |配置文件加密工具 | 

* 默认情况下，除以上资源以外的所有资源都需要携带token才能访问，
开发者可以使用下面的配置类来配置自己服务中不需要携带token就能访问的资源。
```java
package com.milepost.authenticationUi.config.security;

import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.WebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

/**
 * Created by Ruifu Hua on 2020/3/5.
 * 配置那些不被认证保护资源，即可以不携带token直接访问的资源。
 */
@Configuration
public class WebSecurityConfigurer extends WebSecurityConfigurerAdapter {

    @Override
    public void configure(WebSecurity web) throws Exception {
        web.ignoring().antMatchers( "/login", "/login/", //登录页面
                "/login/doLogin", //登录操作
                "/login/checkCode"//验证码
        );
    }
}
```

## UI前端请求UI后端携带token的方式
* 参数

在Post请求和Get请求中，以参数形式传入token，参数名是“access_token”，值是token值，这里不推荐Get请求，因为token会被暴露在浏览器地址栏中。

* 请求头

通过请求头传入token，头名称是“Authorization”，值是“Bearer ${token}”值。注意，Bearer后面有一个空格字符，如token值是“123”，
那么请求头Authorization的值应该是“Bearer 123”。这里推荐使用请求头的方式。


## 业务系统服务UI与认证服务对接

本节介绍业务系统服务的UI如何与认证服务对接，
即用户点击认证服务页面上的业务系统服务的UI链接时，
业务系统服务的UI如何接收认证服务传入的实例元数据和认证数据。

* 传入实例元数据的原因

由于UI类服务是前后端分离的架构模式，前端无法像后端的SpringBoot那样支持命令行参数、操作系统环境变量、yml配置等多种方式，
要想使前端与SpringBoot一样支持多种方式的配置，有一个办法就是前端从后端的SpringBoot中得到这些数据，实例元数据就是后端给前端注入的数据。

* 实例元数据结构
```json
{
  "contextPath": "/authentication-ui" //应用的根目录，
}
```

* 认证数据结构
```json
{
  "jwt": {  //java web token
    "access_token": "xxx",  //token，
    "expires_in": 7199, //有效时长，单位秒
    "born_time_millis": 1583586750536 //token产生时间
  },
  "user": { //用户数据
    "id": "1",  //id
    "username": "admin",  //登录名
    "truename": "Huarf",  //真实姓名
    "mobile": "18310891237",  //手机号码		
    "email": "969729588@qq.com" //电子邮箱
  }
}
```

* 接收数据

点击认证服务页面上的各业务系统服务UI时，浏览器打开新窗口，渲染类路径下的static/index.html文件，
开发者要在这个html文件中编写如下JavaScript代码，
```html
<script type="text/javascript">
    var metadata = '${metadata}';//实例元数据
    var authData = '${authData}';//认证数据
    
    //在这两个变量定义的后面，都可以访问框架传入的数据
    console.log(metadata);
    console.log(authData);
</script>
```
框架会将实例元数据和认证数据注入到metadata变量和authData变量中，
两个变量都是json对象类型，结构如上面所属，
UI前端向UI后端发出请求时可能用到实例元数据，而且要携带token。



