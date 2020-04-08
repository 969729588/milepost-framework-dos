# 求被oauth保护的接口并在接口中获取用户信息

> 本文档描述如何请求被oauth保护的接口和如何在接口中获取用户信息。

> 本框架使用org.springframework.security.oauth:spring-security-oauth2:2.3.4.RELEASE
和org.springframework.security:spring-security-jwt:1.0.9.RELEASE实现认证功能，
认证信息经过加密后保存在token中，客户端请求服务端资源时需要携带token，框架从token中解密出认证信息，
避免每次请求都调用JWT服务。

## 1、获取token
请求被oauth保护的接口时需要携带token，获取token的方法见[获取有效的token](8getToken.md)

##2、携带token请求接口
```html
curl -X GET "http(s)://${eureka.instance.ip-address}:${server.port}/${server.servlet.context-path}/person" -H "Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...."
```

## 3、在controller中获取token中的用户
在controller方法的入参中增加java.security.Principal类型的入参，即可获取到用户信息，见下面的例子：
```java
@ResponseBody
@RequestMapping("/getPrincipal")
public String getPrincipal(Principal principal) throws InterruptedException {
    System.out.println(principal.getName());//打印当前用户。
    return principal.getName();
}
```



