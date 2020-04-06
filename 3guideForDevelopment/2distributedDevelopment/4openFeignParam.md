# OpenFeign参数传递

> 本文档OpenFeign参数传递的几种方式。

> 本框架的OpenFeign依赖为org.springframework.cloud:spring-cloud-starter-openfeign:2.1.0.RELEASE。

本框架中一个请求的流程如下：<br>
* UI类服务前端发送ajax请求UI类服务后端Controller
* UI类服务后端Controller调用OpenFeign
* OpenFeign调用Service类服务Controller
* Service类服务Controller调用Service
* Service类服务Service调用Dao完成操作

下面给出UI类服务前端的js代码、UI类服务的Controller、UI类服务的FeignClient、Service类服务Controller。

* UI类服务前端的js代码
```javascript
/**
 * Created by Huarf on 2020/3/15.
 */

function list() {
    $.ajax({
        type: "POST",
        data: {
            paramList: ["aa", "bb", "cc"]
        },
        url: getContextPath() + '/testParam/list',
        success: function (data) {
            if (data.code == Constant.returnSuccess) {
                console.log(data.payload);
            } else {
                alert(data.msg);
            }
        }
    });
}

function array() {
    $.ajax({
        type: "POST",
        data: {
            paramArray: ["aa", "bb", "cc"]
        },
        url: getContextPath() + '/testParam/array',
        success: function (data) {
            if (data.code == Constant.returnSuccess) {
                console.log(data.payload);
            } else {
                alert(data.msg);
            }
        }
    });
}

function listMap() {
    $.ajax({
        type: "POST",
        contentType: "application/json;charset=UTF-8",
        data: JSON.stringify([person1, person2]),
        url: getContextPath() + '/testParam/listMap',
        success: function (data) {
            if (data.code == Constant.returnSuccess) {
                console.log(data.payload);
            } else {
                alert(data.msg);
            }
        }
    });
}

function listPojo() {
    $.ajax({
        type: "POST",
        contentType: "application/json;charset=UTF-8",
        data: JSON.stringify([person1, person2]),
        url: getContextPath() + '/testParam/listPojo',
        success: function (data) {
            if (data.code == Constant.returnSuccess) {
                console.log(data.payload);
            } else {
                alert(data.msg);
            }
        }
    });
}

function complexPojo2() {
    $.ajax({
        type: "POST",
        contentType: "application/json;charset=UTF-8",
        data:JSON.stringify({
            id: "1",
            firstName : "张三",
            birth : "2020-03-16 01:46:00",
            score : 12.11,
            user: {
                id: "2",
                username: "李四",
                mobile: "12345678901"
            }
        }),
        url: getContextPath() + '/testParam/complexPojo2',
        success: function (data) {
            if (data.code == Constant.returnSuccess) {
                console.log(data.payload);
            } else {
                alert(data.msg);
            }
        }
    });
}

function complexPojo1() {
    $.ajax({
        type: "POST",
        data: {
            "id": "1",
            "firstName" : "张三",
            "birth" : "2020-03-16 01:46:00",
            "score" : 12.11,
            "user.id": "2",
            "user.username": "李四",
            "user.mobile": "12345678901"
        },
        url: getContextPath() + '/testParam/complexPojo1',
        success: function (data) {
            if (data.code == Constant.returnSuccess) {
                console.log(data.payload);
            } else {
                alert(data.msg);
            }
        }
    });
}

function map2() {
    $.ajax({
        type: "POST",
        contentType: "application/json;charset=UTF-8",
        data:JSON.stringify(person),
        url: getContextPath() + '/testParam/map2',
        success: function (data) {
            if (data.code == Constant.returnSuccess) {
                console.log(data.payload);
            } else {
                alert(data.msg);
            }
        }
    });
}

function map1() {
    $.ajax({
        type: "POST",
        data: person,
        url: getContextPath() + '/testParam/map1',
        success: function (data) {
            if (data.code == Constant.returnSuccess) {
                console.log(data.payload);
            } else {
                alert(data.msg);
            }
        }
    });
}

function pojo2() {
    $.ajax({
        type: "POST",
        contentType: "application/json;charset=UTF-8",
        data:JSON.stringify(person),
        url: getContextPath() + '/testParam/pojo2',
        success: function (data) {
            if (data.code == Constant.returnSuccess) {
                console.log(data.payload);
            } else {
                alert(data.msg);
            }
        }
    });
}

function pojo1() {
    $.ajax({
        type: "POST",
        data: person,
        url: getContextPath() + '/testParam/pojo1',
        success: function (data) {
            if (data.code == Constant.returnSuccess) {
                console.log(data.payload);
            } else {
                alert(data.msg);
            }
        }
    });
}

function pathVariable() {
    $.ajax({
        type: "GET",
        url: getContextPath() + '/testParam/pathVariable/11',
        success: function (data) {
            if (data.code == Constant.returnSuccess) {
                console.log(data.payload);
            } else {
                alert(data.msg);
            }
        }
    });
}

function basicTypes() {
    $.ajax({
        type: "POST",
        data: {
            name : "张三",
            count : 12,
            age : 1323
        },
        url: getContextPath() + '/testParam/basicTypes',
        success: function (data) {
            if (data.code == Constant.returnSuccess) {
                console.log(data.payload);
            } else {
                alert(data.msg);
            }
        }
    });
}

function forward() {
    $.ajax({
        type: "GET",
        url: getContextPath() + '/testParam/forward',
        success: function (data) {
            if (data.code == Constant.returnSuccess) {
                console.log(data.payload);
            } else {
                alert(data.msg);
            }
        }
    });
}

function redirect() {
    $.ajax({
        type: "POST",
        url: getContextPath() + '/testParam/redirect',
        success: function (data) {
            if (data.code == Constant.returnSuccess) {
                console.log(data.payload);
            } else {
                alert(data.msg);
            }
        }
    });
}

function cookieValue() {
    $.ajax({
        type: "POST",
        url: getContextPath() + '/testParam/cookieValue',
        success: function (data) {
            if (data.code == Constant.returnSuccess) {
                console.log(data.payload);
            } else {
                alert(data.msg);
            }
        }
    });
}

function requestHeader() {
    $.ajax({
        type: "POST",
        url: getContextPath() + '/testParam/requestHeader',
        success: function (data) {
            if (data.code == Constant.returnSuccess) {
                console.log(data.payload);
            } else {
                alert(data.msg);
            }
        }
    });
}

function servlet() {
    $.ajax({
        type: "POST",
        url: getContextPath() + '/testParam/servlet',
        success: function (data) {
            if (data.code == Constant.returnSuccess) {
                console.log(data.payload);
            } else {
                alert(data.msg);
            }
        }
    });
}

var person2 = {
    id : "2",
    firstName : "张三2",
    birth : "2020-03-16 22:22:22",
    score : 22.22,
    user: {
        id: "2",
        username: "李四2",
        mobile: "12345678901"
    }
};

var person1 = {
    id : "1",
    firstName : "张三1",
    birth : "2020-03-16 11:11:11",
    score : 11.11,
    user: {
        id: "1",
        username: "李四1",
        mobile: "12345678901"
    }
};

var person = {
    id : "1",
    firstName : "张三",
    birth : "2020-03-16 01:46:00",
    score : 12.11
};
```

* UI类服务的Controller
```java
package com.milepost.authenticationUi.test.controller;

import com.milepost.api.vo.response.Response;
import com.milepost.api.vo.response.ResponseHelper;
import com.milepost.authenticationApi.entity.person.Person;
import com.milepost.authenticationUi.test.feignClient.TestParamFc;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.io.IOException;
import java.util.*;

/**
 * Created by Ruifu Hua on 2020/3/15.
 */
@Controller
@RequestMapping("/testParam")
public class TestParamController {

    @Autowired
    private TestParamFc testParamFc;

    private Logger logger = LoggerFactory.getLogger(TestParamController.class);

    /**
     * @param paramList
     * @return
     */
    @ResponseBody
    @PostMapping("/list")
    public Response<List<String>> list(@RequestParam("paramList[]")  List<String> paramList) {
        Response<List<String>> response = null;
        try {
            for(String param : paramList){
                System.out.println(param);
            }
            response = ResponseHelper.createSuccessResponse(paramList);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }
        return response;
    }

    /**
     * @param paramArray
     * @return
     */
    @ResponseBody
    @PostMapping("/array")
    public Response<String[]> array(@RequestParam("paramArray[]") String[] paramArray) {
        Response<String[]> response = null;
        try {
            for(String param : paramArray){
                System.out.println(param);
            }
            response = ResponseHelper.createSuccessResponse(paramArray);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }
        return response;
    }

    /**
     * @param mapList
     * @return
     */
    @ResponseBody
    @PostMapping("/listMap")
    public Response<List<Map<String, Object>>> listMap(@RequestBody List<Map<String, Object>> mapList) {
        Response<List<Map<String, Object>>> response = null;
        try {
            for(Map map : mapList){
                System.out.println(map);
            }
            response = testParamFc.listMap(mapList);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }
        return response;
    }

    /**
     * @param personList
     * @return
     */
    @ResponseBody
    @PostMapping("/listPojo")
    public Response<List<Person>> listPojo(@RequestBody List<Person> personList) {
        Response<List<Person>> response = null;
        try {
            for(Person person : personList){
                System.out.println(person);
            }
            response = testParamFc.listPojo(personList);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }
        return response;
    }

    /**
     * @param person
     * @return
     */
    @ResponseBody
    @PostMapping("/complexPojo2")
    public Response<Person> complexPojo2(@RequestBody Person person) {
        Response<Person> response = null;
        try {
            System.out.println("person: " + person);
            response = testParamFc.complexPojo(person);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }
        return response;
    }

    /**
     * @param person
     * @return
     */
    @ResponseBody
    @PostMapping("/complexPojo1")
    public Response<Person> complexPojo(Person person) {
        Response<Person> response = null;
        try {
            System.out.println("person: " + person);
            response = testParamFc.complexPojo(person);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }
        return response;
    }

    /**
     * @param map
     * @return
     */
    @ResponseBody
    @PostMapping("/map2")
    public Response<Map<String, Object>> map2(@RequestBody Map<String, Object> map) {
        Response<Map<String, Object>> response = null;
        try {
            System.out.println("map: " + map);
            response = testParamFc.map(map);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }
        return response;
    }

    /**
     * map前面要加@RequestParam注解
     * @param map
     * @return
     */
    @ResponseBody
    @PostMapping("/map1")
    public Response<Map<String, Object>> map1(@RequestParam Map<String, Object> map) {
        Response<Map<String, Object>> response = null;
        try {
            System.out.println("map: " + map);
            response = testParamFc.map(map);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }
        return response;
    }

    /**
     * 入参当中，不能有多余一个参数被标@RequestBody注解，因为只有一个请求体
     * @param person
     * @return
     */
    @ResponseBody
    @PostMapping("/pojo2")
    public Response<Person> pojo2(@RequestBody Person person) {
        Response<Person> response = null;
        try {
            System.out.println("person: " + person);
            response = testParamFc.pojo(person);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }
        return response;
    }

    /**
     * pojo前面不加@RequestParam注解
     * @param person
     * @return
     */
    @ResponseBody
    @PostMapping("/pojo1")
    public Response<Person> pojo1(Person person) {
        Response<Person> response = null;
        try {
            System.out.println("person: " + person);
            response = testParamFc.pojo(person);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }
        return response;
    }

    @ResponseBody
    @PostMapping("/basicTypes")
    public Response<String> basicTypes(@RequestParam("name") String name, @RequestParam("count") int count, @RequestParam Integer age) {
        Response<String> response = null;
        try {
            System.out.println("name: " + name);
            System.out.println("count: " + count);
            System.out.println("age: " + age);
            response = testParamFc.basicTypes(name, count, age);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }
        return response;
    }


    @ResponseBody
    @GetMapping("/pathVariable/{id}")
    public Response<String> pathVariable(@PathVariable("id") Integer id){
        Response<String> response = null;
        try {
            System.out.println("id: " + id);
            response = testParamFc.pathVariable(id);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }
        return response;
    }

    @GetMapping("/forward")
    public String forward(){
        System.out.println("forward");
        return "forward:/testParam/testRedirect";
    }

    @PostMapping("/redirect")
    public String redirect(){
        System.out.println("redirect");
        return "redirect:/testParam/testRedirect";
    }

    @ResponseBody
    @GetMapping("/testRedirect")
    public Response<String> testRedirect(){
        System.out.println("testRedirect");
        Response<String> response = ResponseHelper.createSuccessResponse("testRedirect");
        return response;
    }

    @ResponseBody
    @PostMapping("/cookieValue")
    public Response<String> cookieValue(@CookieValue("JSESSIONID") String sessionId){
        Response<String> response = null;
        try {
            System.out.println("sessionId: " + sessionId);
            response = testParamFc.cookieValue(sessionId);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }
        return response;
    }


    @ResponseBody
    @PostMapping("/requestHeader")
    public Response<String> requestHeader(@RequestHeader(value="accept") String accept){
        Response<String> response = null;
        try {
            System.out.println("accept: " + accept);
            response = testParamFc.requestHeader();
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }
        return response;
    }

    @ResponseBody
    @PostMapping("/servlet")
    public Response<String> servlet(HttpServletRequest request, HttpServletResponse response, HttpSession session) throws IOException {
        Response<String> response1 = null;
        try {
            Enumeration<String> headerNames = request.getHeaderNames();
            while (headerNames.hasMoreElements()){
                String headerName = headerNames.nextElement();
                System.out.println(headerName + ":" + request.getHeader(headerName));
            }

            System.out.println(response.toString());

            System.out.println(session.getId());
            response1 = testParamFc.servlet();
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response1 = ResponseHelper.createExceptionResponse(e);
        }
        return response1;
    }
}

```

* UI类服务的FeignClient
```java
package com.milepost.authenticationUi.test.feignClient;

import com.milepost.api.vo.response.Response;
import com.milepost.authenticationApi.entity.person.Person;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.*;

import java.util.List;
import java.util.Map;

/**
 * Created by Ruifu Hua on 2020/3/15.
 */
@FeignClient(value = "${info.app.service.name}")
@RequestMapping("${info.app.service.prefix}/testParam")
public interface TestParamFc {

    @PostMapping("/servlet")
    Response<String> servlet();

    @PostMapping("/requestHeader")
    Response<String> requestHeader();

    @PostMapping("/cookieValue")
    Response<String> cookieValue(@RequestParam("JSESSIONID") String sessionId);

    @GetMapping("/pathVariable/{id}")
    Response<String> pathVariable(@PathVariable("id") Integer id);

    @PostMapping("/basicTypes")
    Response<String> basicTypes(@RequestParam("name") String name, @RequestParam("count") int count, @RequestParam("age") Integer age);

    /**
     * Feign传递pojo时，只能使用@RequestBody注解，不能像SpringMVC那样不使用任何注解传递pojo。
     * @param person
     * @return
     */
    @PostMapping("/pojo")
    Response<Person> pojo(@RequestBody Person person);

    @PostMapping("/map")
    Response<Map<String,Object>> map(@RequestBody Map<String, Object> map);

    @PostMapping("/complexPojo")
    Response<Person> complexPojo(@RequestBody Person person);

    @PostMapping("/listPojo")
    Response<List<Person>> listPojo(@RequestBody List<Person> personList);

    @PostMapping("/listMap")
    Response<List<Map<String,Object>>> listMap(@RequestBody List<Map<String, Object>> mapList);

}

```

* Service类服务Controller
```java
package com.milepost.authenticationService.test.controller;

/**
 * Created by Ruifu Hua on 2020/3/15.
 */

import com.milepost.api.vo.response.Response;
import com.milepost.api.vo.response.ResponseHelper;
import com.milepost.authenticationApi.entity.person.Person;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.io.IOException;
import java.util.Enumeration;
import java.util.List;
import java.util.Map;

/**
 * Created by Ruifu Hua on 2020/3/15.
 */
@Controller
@RequestMapping("/testParam")
public class TestParamController {

    private Logger logger = LoggerFactory.getLogger(TestParamController.class);

    private static final String CALL_SUCCESS = "调用成功";

    @ResponseBody
    @PostMapping("/listMap")
    public Response<List<Map<String, Object>>> listMap(@RequestBody List<Map<String, Object>> mapList) {
        Response<List<Map<String, Object>>> response = null;
        try {
            for(Map map : mapList){
                System.out.println(map);
            }
            response = ResponseHelper.createSuccessResponse(mapList);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }
        return response;
    }

    @ResponseBody
    @PostMapping("/listPojo")
    public Response<List<Person>> listPojo(@RequestBody List<Person> personList) {
        Response<List<Person>> response = null;
        try {
            for(Person person : personList){
                System.out.println(person);
            }
            response = ResponseHelper.createSuccessResponse(personList);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }
        return response;
    }

    @ResponseBody
    @PostMapping("/complexPojo")
    public Response<Person> complexPojo(@RequestBody Person person) {
        Response<Person> response = null;
        try {
            System.out.println("person: " + person);
            response = ResponseHelper.createSuccessResponse(person);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }
        return response;
    }

    @ResponseBody
    @PostMapping("/map")
    public Response<Map<String, Object>> map(@RequestBody Map<String, Object> map) {
        Response<Map<String, Object>> response = null;
        try {
            System.out.println("map: " + map);
            response = ResponseHelper.createSuccessResponse(map);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }
        return response;
    }

    /**
     * pojo前面不加@RequestParam注解
     * @param person
     * @return
     */
    @ResponseBody
    @PostMapping("/pojo")
    public Response<Person> pojo(@RequestBody Person person) {
        Response<Person> response = null;
        try {
            System.out.println("person: " + person);
            response = ResponseHelper.createSuccessResponse(person);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }
        return response;
    }


    @ResponseBody
    @PostMapping("/basicTypes")
    public Response<String> basicTypes(@RequestParam("name") String name, @RequestParam("count") int count, @RequestParam Integer age) {
        Response<String> response = null;
        try {
            System.out.println("name: " + name);
            System.out.println("count: " + count);
            System.out.println("age: " + age);
            response = ResponseHelper.createSuccessResponse(CALL_SUCCESS);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }
        return response;
    }

    @ResponseBody
    @GetMapping("/pathVariable/{id}")
    public Response<String> pathVariable(@PathVariable("id") Integer id){
        Response<String> response = null;
        try {
            System.out.println("id: " + id);
            response = ResponseHelper.createSuccessResponse(CALL_SUCCESS);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }
        return response;
    }

    @ResponseBody
    @PostMapping("/cookieValue")
    public Response<String> cookieValue(@RequestParam("JSESSIONID") String sessionId){
        Response<String> response = null;
        try {
            System.out.println("sessionId: " + sessionId);
            response = ResponseHelper.createSuccessResponse(CALL_SUCCESS);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }
        return response;
    }

    @ResponseBody
    @PostMapping("/requestHeader")
    public Response<String> requestHeader(@RequestHeader(value="accept") String accept){
        Response<String> response = null;
        try {
            System.out.println("accept: " + accept);
            response = ResponseHelper.createSuccessResponse(CALL_SUCCESS);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }
        return response;
    }


    @ResponseBody
    @PostMapping("/servlet")
    public Response<String> servlet(HttpServletRequest request, HttpServletResponse response, HttpSession session) throws IOException {
        Response<String> response1 = null;
        try {
            Enumeration<String> headerNames = request.getHeaderNames();
            while (headerNames.hasMoreElements()){
                String headerName = headerNames.nextElement();
                System.out.println(headerName + ":" + request.getHeader(headerName));
            }

            System.out.println(response.toString());


            System.out.println(session.getId());
            response1 = ResponseHelper.createSuccessResponse(CALL_SUCCESS);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response1 = ResponseHelper.createExceptionResponse(e);
        }
        return response1;
    }
}

```
