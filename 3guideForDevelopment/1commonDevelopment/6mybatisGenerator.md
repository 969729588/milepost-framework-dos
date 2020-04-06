# Mybatis Generator

> 本文档描述在框架中如何使用Mybatis Generator。

> 本框架基于org.mybatis.generator:mybatis-generator-core:1.3.2封装了自己的Generator插件，
原生的Generator插件有一个弊端，就是在生成代码之后，对数据库表、实体类、mapper配置文件进行了更改，
再次生成代码时，老代码会被覆盖掉或者在老代码基础上追加，这显然不是我们想要的，
框架提供的milepost-mybatis-generator插件解决了这个弊端。


需要注意的是，Generator插件只能在Service类服务中使用，因为UI类服务不链接数据库。

关于插件的使用方法，见[使用本框架构建一个项目并打包发布到Linux上](1projectExample.md)。
