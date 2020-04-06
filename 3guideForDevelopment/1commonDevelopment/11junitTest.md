# 单元测试

> 本文档描述如何使用框架封装的单元测试模块。

> 本框架基于junit:junit:4.12封装了自己的单元测试类模块。

* UI类服务和Service类服务都可以使用单元测试类模块。


运行单元测试类中方法的过程如下：

* 调用主启动类的main方法，启动服务。
* 调用@Before注解标注的方法，从Spring IOC容器中获取bean，初始化成员变量。
* 运行@Test注解标注的方法，即执行测试。
* 关闭服务，释放资源。

由以上过程可知，这与启动服务没有任何区别，所以要注意项目中的定时任务，连接EurekaServer等。

下面是一个单元测试类的例子。

```java
/**
* 所有单元测试类都要集成框架中的BaseTest类，并传入泛型参数，这个参数就是当前项目的主启动类。
*/
public class RestTemplateTest extends BaseTest<AuthenticationUiApplication>{

    /**
    * 定义成员变量，这个变量就是要测试的对象，一般是项目中的某个service或者controller。
    */
    private RestTemplate restTemplate;

    private String BASE_URL = "http://192.168.1.104:8080/restServer";

    /**
    * 在@Before注解标注的方法中初始化要测试的对象，调用getBean方法即可。
    */
    @Before
    public void init(){
        restTemplate = getBean(RestTemplate.class);
    }
   
    /**
    * 真正要运行的单元测试类方法， 
    */
    @Test
    public void test1(){
        //这仅仅打印了restTemplate.toString()，可见其不是null，证明获取到了对象。
        System.out.println(restTemplate.toString());
    }
}

```