# 文件上传

> 本文档描述如何在框架中开发文档上传功能。

* 本例子前端使用[jQuery-File-Upload(v10.8.0)](https://github.com/blueimp/jQuery-File-Upload/tree/v10.8.0)实现文件上传功能。

## 1、SpringBoot文件上传参数配置
| 参数名                      | 必填 | 默认值 | 说明|
| ----------------------------|-----|-------|--------|
|spring.servlet.multipart.enabled|否 |true|是否开启SpringBoot的文件上传功能|
|spring.servlet.multipart.max-file-size|否 |1MB|单个文件大小|
|spring.servlet.multipart.max-request-size|否 |10MB|所有文件总大小|
|spring.servlet.multipart.file-size-threshold|否 |0B|超过这个数值，文件将会被临时写入到磁盘|
|spring.servlet.multipart.location|否 |操作系统的临时目录|序列化文件的目录，不推荐设置这个参数，使用默认值即可|

## 2、前端代码

html部分
```html
<input id="file" name="file" type="file" />
```
javascript部分
```javascript
$(function () {
    $('#file').fileupload({
        url: getContextPath() + '/test/testFileupload',
        formData: {name: "abc", id: "123"},
        done: function (e, data) {
            console.log(e);
            console.log(data);
            console.log(data.result);
        }
    });
});
```

## 3、后端代码
UI Controller
```java
@ResponseBody
@PostMapping("/testFileupload")
public Response<String> testFileupload(Student record,
                              @RequestParam(value="name", required=true) String name,
                              @ApiParam(name = "file", value = "上传文件", required = true)
                              @RequestParam(value="file", required=true) MultipartFile multipartFile) {
    Response<String> response = null;
    try{
        System.out.println(record);
        System.out.println(name);
        //调用service
        response = testFc.testFileupload(name, multipartFile);
    } catch (Exception e) {
        logger.error(e.getMessage(), e);
        response = ResponseHelper.createExceptionResponse(e);
    }
    return response;
}
```
UI FeignClient
```java
/**
 * 使用@RequestPart传送文件
 * @param name
 * @param multipartFile
 * @return
 */
@PostMapping(value = "/testFileupload", consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
Response<String> testFileupload(@RequestParam(value="name", required=true) String name, @RequestPart(value = "file") MultipartFile multipartFile);

```

Service Controller
```java
@ResponseBody
@PostMapping("/testFileupload")
public Response<String> testFileupload(@RequestParam(value="name", required=true) String name,
                                       @ApiParam(name = "file", value = "上传文件", required = true)
                                       @RequestParam(value="file", required=true) MultipartFile multipartFile) throws IOException {
    Response<String> response = null;
    InputStream inputStream = null;
    OutputStream outputStream = null;
    try{
        System.out.println(name);
        String riginalFilename = multipartFile.getOriginalFilename();
        inputStream = multipartFile.getInputStream();
        outputStream = new FileOutputStream(new File("F:\\testFile\\fileUpload\\" +  riginalFilename.replace(".", "_.")));
        IOUtils.copy(inputStream, outputStream);

        //调用service
        response = ResponseHelper.createSuccessResponse("上传成功");
    } catch (Exception e) {
        logger.error(e.getMessage(), e);
        response = ResponseHelper.createExceptionResponse(e);
    }finally {
        IOUtils.closeQuietly(inputStream);
        IOUtils.closeQuietly(outputStream);
    }
    return response;
}
```



