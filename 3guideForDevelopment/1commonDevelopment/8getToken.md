# 获取有效的token

> 本文档说明如何手动获取oauth的token。

* 请求认证UI的登录接口
```html
curl "http(s)://${eureka.instance.ip-address}:${server.port}/${server.servlet.context-path}/login/doLogin?username=${用户名}&password=${密码}"
```
可以获取有效的token。

* 接口返回数据如下：
```json
{
    "code": 0,
    "msg": "",
    "payload": {
        "jwt": {
            "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1ODM1OTkxNjksInVzZXJfbmFtZSI6ImFkbWluIiwianRpIjoiZDMxZDBlZDYtNzY1Yi00MmZhLTgyZmYtNDE5MTNjOTRmODNmIiwiY2xpZW50X2lkIjoiY2xpZW50X2lkX3RlbmFudDEiLCJzY29wZSI6WyJhbGwiXX0.g4nwIQ_RV_JJriwNExW4udj2re0Hx8yDJ7AVAwzocrV3krOdZLst9bDihDiammNeIk-Z6c4dbDVmUZJea0O26IqVrr2P_NdwVp-9MuNiAVXOJt5y5rUBGaArXAUnoUH1tV4mg28wBYyJ3mNISGb6vOnVMol38KDUsnxquK8YgUUDb6z9Q9rdyNGHzOR2x5PqE2GOz99ePia4kcWXjomsGMfsdiHl-4qjxohViKd5jzaSjupYJowLaGl61jMYIdhPumjJUR8IWTpLQnrFVOG_5rzxO5ma92BtttHSr0byqCJo5-oAROQFNB7IGz0yn9Jd_MMhY7GBOcuIp_7xDnJjlA",
            "token_type": "bearer",
            "refresh_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1ODM1OTkxNjksInVzZXJfbmFtZSI6ImFkbWluIiwianRpIjoiZmM5YmVjZTItYzVlYy00N2NmLWFiYjQtNmQzYTEzMGE0YzAyIiwiY2xpZW50X2lkIjoiY2xpZW50X2lkX3RlbmFudDEiLCJzY29wZSI6WyJhbGwiXSwiYXRpIjoiZDMxZDBlZDYtNzY1Yi00MmZhLTgyZmYtNDE5MTNjOTRmODNmIn0.dGatUJ6I7RqX17q3kD4nJpk2EKTVYHU7OTdtzJjWy3o7m2TZ71Qj52Li_W6e7KP2tpwmhPF1ySTCHCW7FL42vmLIZtL90UcCzyNwJ7lLJ5NYflR4-YZFiaWrj7TvfbCfWZx7clKkedXDTUxnyQirmBl01v5WNblf34BxZfc8MaHvOEFJCKm7KhTe8XJVqb1X8rFZ_RyseGudOmqzA8izAyhzk1bh6bo6sPlepySFpLceKz8s169vC9RJBZbGfQRbqjXnJ01TCa43neCpfhdwAT3x3sd58uG75sSBlxm-KrA8Ju9F_-LDOZil-Z_IvnynE7s643CTVTccbvD_zLz4QA",
            "expires_in": 7199,
            "born_time_millis": 1583591971650,
            "scope": "all",
            "jti": "d31d0ed6-765b-42fa-82ff-41913c94f83f"
        },
        "user": {
            "id": "1",
            "username": "admin",
            "truename": "Huarf",
            "mobile": "18310891237",
            "email": "969729588@qq.com",
            "password": "******",
            "activated": true
        }
    }
}
```
其中payload.jwt.access_token就是我们要的token。

* token是有失效时间的，默认是7200s，关于token的失效时间见[JWT服务部署](../../2userManual/1baseServerDeploy/2jwtServer.md)中 第4节启动脚本中的参数。