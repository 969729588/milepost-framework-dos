# 发送邮件

> 本文档介绍在框架中如何发送邮件。

> 本框架使用org.springframework.boot:spring-boot-starter-mail:2.1.0.RELEASE实现发送邮件功能。

* UI类和服和Service类服务都可以使用邮件发送功能。

## 1、申请授权码
各大邮箱服务商都提供发送邮件的服务，本文档使用的是163邮箱，所以需要去163邮箱申请授权码，
即第三方程序登录邮箱服务器的密码，并不是邮箱的登录密码。

## 2、配置yml
```yaml
spring: 
  mail:
    username: 个人邮箱，如 m18310891237@163.com
    password: 授权码
    host: smtp.163.com
```

## 3、注入JavaMailSender，发送邮件
```java
@Autowired
private JavaMailSender mailSender;

public void sendMail() {
    SimpleMailMessage message = new SimpleMailMessage();
    message.setSubject("通知-今晚开会");
    message.setText("今晚7:31开会");
    //发送给谁
    message.setTo("969729588@qq.com");
    //来自谁，是申请授权码的那个邮箱
    message.setFrom(mailUsername);
    mailSender.send(message);
}
```

