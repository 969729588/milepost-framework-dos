# 租户、标签、权重

> 租户、标签、权重提供了一种人为干预FeignClient和Ribbon负载均衡策略的方式。
* JWT服务、Service类服务、UI类服务都可以设置租户、标签、权重，EurekaServer不需要设置这些。
* 三个参数的优先级是：租户 > 标签 > 权重。


## 1、租户
用来隔离一个服务的多个实例，租户A的所有实例对租户B不可见，A租户的实例不可能被B租户的调用，即多租户之间是绝对隔离的。<br>
调用者实例未设置租户，则他可以调用设置和未设置租户的所有被调用者实例，<br>
调用者实例设置了租户，则只调用与他设置了相同租户的被调用者实例。

## 2、标签
标签分为或标签(label-or)和与标签(label-and)，多个标签使用英文逗号分割。<br>
同时设置了或标签和与标签，以或标签为准，忽略与标签。<br>

或标签：调用者实例和被调用者实例的标签交集不为空，调用者实例就可以调用被调用者实例。例如：
```html
aa可以调用aa,bb
aa,bb可以调用aa
aa,bb可以调用aa,cc
aa,bb不可以调用cc,dd
```
与标签：调用者实例和被调用者实例的标签集合需要完全相同，调用者实例才可以调用被调用者实例。例如：
```html
aa可以调用aa
aa,bb可以调用aa,bb
aa,bb可以调用bb,aa
aa,bb不可以调用aa,bb,cc
```

## 3、权重
被调用者实例被调用的概率是
```html
被调用者实例权重值 / 当前服务的所有实例权重值之和
```
权重值为0的实例永远不会被调用。


## 4、待完善的
实现动态刷新多租户相关的属性(不依赖配置中心，不依赖消息总线)。


修改环境属性
curl http://192.168.223.1:9981/example-service/milepost-actuator/env \
-H "Content-Type:application/json" \
-H "Authorization:Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1ODYwMTY1NzUsInVzZXJfbmFtZSI6ImFkbWluIiwianRpIjoiNDc0YjUyZTctYzAxMi00MDc0LTg2OTEtZjk1MmVmN2RjZTUwIiwiY2xpZW50X2lkIjoiY2xpZW50X2lkX3RlbmFudDEiLCJzY29wZSI6WyJhbGwiXX0.m6GdVoR-2H5vtb06fmCaHTi1wfgCBBil6aMUu-yn5kpOxXro0YBXyNoPNgkq6MBqJfnJUXNB8rXK4qhnRzN-jmdR8u--IVgHyATj1_EHaw-BaD9ptWrgw5ptw94_ZztmAfUuxRhI8tHihmKjAOPlRoOzstntHQZlADIaKxYw_hl7XXG62Z5kBKWMucNnvtYFn60eVFDJJAe4NW_mQcKbMPJ5r_ew0h7mCHEn5LaWlawyjftFYCOblo7K-BzW8vLgEQCyivii-98k40ebA1xko9eKmDM7BTGHJWhRhL9kdxBeZLJ8ygxxMHphJ9s8BQGChUgUxxdkFokPqs6t5SuFQg" \
-X POST \
-d '{"name":"eureka.instance.metadata-map.tenant","value":"tenant1"}'

刷新属性
curl http://192.168.223.1:9981/example-service/milepost-actuator/refresh \
-H "Authorization:Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1ODYwMTY1NzUsInVzZXJfbmFtZSI6ImFkbWluIiwianRpIjoiNDc0YjUyZTctYzAxMi00MDc0LTg2OTEtZjk1MmVmN2RjZTUwIiwiY2xpZW50X2lkIjoiY2xpZW50X2lkX3RlbmFudDEiLCJzY29wZSI6WyJhbGwiXX0.m6GdVoR-2H5vtb06fmCaHTi1wfgCBBil6aMUu-yn5kpOxXro0YBXyNoPNgkq6MBqJfnJUXNB8rXK4qhnRzN-jmdR8u--IVgHyATj1_EHaw-BaD9ptWrgw5ptw94_ZztmAfUuxRhI8tHihmKjAOPlRoOzstntHQZlADIaKxYw_hl7XXG62Z5kBKWMucNnvtYFn60eVFDJJAe4NW_mQcKbMPJ5r_ew0h7mCHEn5LaWlawyjftFYCOblo7K-BzW8vLgEQCyivii-98k40ebA1xko9eKmDM7BTGHJWhRhL9kdxBeZLJ8ygxxMHphJ9s8BQGChUgUxxdkFokPqs6t5SuFQg" \
-X POST





修改环境属性
curl http://192.168.223.1:9981/example-service/milepost-actuator/bus-env \
-H "Content-Type:application/json" \
-H "Authorization:Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1ODYwMTY1NzUsInVzZXJfbmFtZSI6ImFkbWluIiwianRpIjoiNDc0YjUyZTctYzAxMi00MDc0LTg2OTEtZjk1MmVmN2RjZTUwIiwiY2xpZW50X2lkIjoiY2xpZW50X2lkX3RlbmFudDEiLCJzY29wZSI6WyJhbGwiXX0.m6GdVoR-2H5vtb06fmCaHTi1wfgCBBil6aMUu-yn5kpOxXro0YBXyNoPNgkq6MBqJfnJUXNB8rXK4qhnRzN-jmdR8u--IVgHyATj1_EHaw-BaD9ptWrgw5ptw94_ZztmAfUuxRhI8tHihmKjAOPlRoOzstntHQZlADIaKxYw_hl7XXG62Z5kBKWMucNnvtYFn60eVFDJJAe4NW_mQcKbMPJ5r_ew0h7mCHEn5LaWlawyjftFYCOblo7K-BzW8vLgEQCyivii-98k40ebA1xko9eKmDM7BTGHJWhRhL9kdxBeZLJ8ygxxMHphJ9s8BQGChUgUxxdkFokPqs6t5SuFQg" \
-X POST \
-d '{"name":"eureka.instance.metadata-map.tenant","value":"tenant1"}'

刷新属性
curl http://192.168.223.1:9981/example-service/milepost-actuator/bus-refresh \
-H "Authorization:Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1ODYwMTY1NzUsInVzZXJfbmFtZSI6ImFkbWluIiwianRpIjoiNDc0YjUyZTctYzAxMi00MDc0LTg2OTEtZjk1MmVmN2RjZTUwIiwiY2xpZW50X2lkIjoiY2xpZW50X2lkX3RlbmFudDEiLCJzY29wZSI6WyJhbGwiXX0.m6GdVoR-2H5vtb06fmCaHTi1wfgCBBil6aMUu-yn5kpOxXro0YBXyNoPNgkq6MBqJfnJUXNB8rXK4qhnRzN-jmdR8u--IVgHyATj1_EHaw-BaD9ptWrgw5ptw94_ZztmAfUuxRhI8tHihmKjAOPlRoOzstntHQZlADIaKxYw_hl7XXG62Z5kBKWMucNnvtYFn60eVFDJJAe4NW_mQcKbMPJ5r_ew0h7mCHEn5LaWlawyjftFYCOblo7K-BzW8vLgEQCyivii-98k40ebA1xko9eKmDM7BTGHJWhRhL9kdxBeZLJ8ygxxMHphJ9s8BQGChUgUxxdkFokPqs6t5SuFQg" \
-X POST