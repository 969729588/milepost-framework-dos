# JWT服务部署

> 本文档介绍JWT服务的部署。

> 本框架使用org.springframework.security.oauth:spring-security-oauth2:2.3.4.RELEASE
和org.springframework.security:spring-security-jwt:1.0.9.RELEASE实现认证功能，
认证信息经过加密后保存在token中，客户端请求服务端资源时需要携带token，框架从token中解密出认证信息，
避免每次请求都调用JWT服务。

## 1、软件环境
* JDK1.8+
* Oracle或MySQL
* 已经启动了EurekaServer

## 2、所需资料

| 文件名                     | 说明       |
| -------------------------- | ---------- |
| milepost-eureka-1.0.0.100.jar | 程序jar包 |
| run.sh                     | 启停脚本   |

## 3、服务启停

* 启动

```bash
run.sh start
```

* 查看

```bash
run.sh status
```

* 停止

```bash
run.sh stop
```

* 重启

```bash
run.sh restart
```

##  4、启动脚本中的参数
* Java参数

| 参数名 | 必填 | 默认值 | 说明 |
| -------| ----| ------| ---- |
| ssl   | 否   |false  |是否用https，true启用，false不启用|


* 命令参数

| 参数名                      | 必填 | 默认值 | 说明                                                         |
| ---------------------------| ---- | ------ | ------------------------------------------------------------ |
|spring.profiles.active|是|  |配置文件环境，<br>dev：开发环境；test：测试环境；prod：生产环境|
|server.port|是| |服务端口，建议设置为9999|
|spring.datasource.druid.db-type|是| |数据库类型，mysql、oracle|
|spring.datasource.druid.driver-class-name|是|   |数据库驱动类，<br>com.mysql.cj.jdbc.Driver、oracle.jdbc.driver.OracleDriver|
|spring.datasource.druid.url|是| |连接数据库url|
|spring.datasource.druid.username|是 |   |用户名|
|spring.datasource.druid.password|是|    |密码|
|eureka.client.service-url.defaultZone|是|   |EurekaServer地址|
|eureka.instance.ip-address|是|   |本机ip|
|multiple-tenant.tenant|否|default|实例租户，不区分大小写，不支持逗号分割。|
|multiple-tenant.weight|否|1|实例权重，0和正整数。|
|multiple-tenant.label-and|否|   |与标签，格式：aa,bb,cc，多个标签支持逗号分割。|
|multiple-tenant.label-or|否|    |或标签，格式：aa,bb,cc，多个标签支持逗号分割。|
|auth.client-detail.access-token-validity|否|7200|token的有效时间，单位秒。|
|eureka.instance.ip-address|是|  |服务绑定ip，配置为服务器ip即可|



> 表格中关于租户、标签、权重的配置，见[租户、标签、权重](../../3guideForDevelopment/2distributedDevelopment/2tenant.md)。

## 5、验证


* 查看日志

服务启动成功后，会在jar包所在的目录下生成logs文件夹，里面存放着日志文件，使用下面的命令查看日志。
```bash
tail -f logs/milepost-auth.log -n 300
```
日志中有
```html
...[INFO ] [com.milepost.core.MilepostApplication             : 844 ] - 服务启动完毕。
```
字样表示服务启动成功。

* 获取token

初始用户：admin/123456

请求如下接口
```html
curl "http://${eureka.instance.ip-address}:${server.port}/milepost-auth/oauth/token?username=admin&password=123456&grant_type=password" \
-H "Authorization:Basic ${basic}" \
-X POST
```

返回如下json数据表示部署成功
```html
{
    "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJ...",
    "token_type": "bearer",
    "refresh_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJ...",
    "expires_in": 7199,
    "scope": "test",
    "jti": "a3f1913f-e726-4fb7-ad6a-515182cee75f"
}
```

* 验证token

请求如下接口
```html
curl "http://${eureka.instance.ip-address}:${server.port}/milepost-auth/oauth/check_token?token=eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...." \
-X POST
```

返回如下json数据表示部署成功
```html
{
	"active": true,
	"exp": 1584285565,
	"user_name": "admin",
	"jti": "f7953d3d-9611-446e-880f-2090ea5a2bb2",
	"client_id": "client_id_tenant1",
	"scope": ["all"]
}
```

* 关于接口中${basic}的描述

${basic}是Basic认证的username+":"+password的base64编码值，<br>
username取"client_id_" + 租户；password取"client_secret_" + 租户，<br>
如当前JWT服务的租户配置为“zhangsan”，则username="client_id_zhangsan"；password="client_secret__zhangsan"，<br>
${basic}=EncryptionUtil.encodeWithBase64(username+":"+password)="Y2xpZW50X2lkX3poYW5nc2FuOmNsaWVudF9zZWNyZXRfX3poYW5nc2Fu"<br>

## 6、Docker支持

TBD