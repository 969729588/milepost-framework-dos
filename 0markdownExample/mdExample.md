# markdown的使用

## 1、标题
“#”表示标题，“#”个数表示标题级别。

## 2、代码块
在代码块起始和结束的地方增加```符号即可（英文输入法下的波浪号，键盘esc下面那个按键），然后根据提示选择代码语言。

```
package com.milepost.milepostFrameworkDos;

/**
 * Created by Ruifu Hua on 2020/2/19.
 */
public class User {

    private String username;

    private String pwd;

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPwd() {
        return pwd;
    }

    public void setPwd(String pwd) {
        this.pwd = pwd;
    }
}

```

## 3、块
在块的地方增加>符号即可；几层块就加几个>。
>一级块
>>二级块
>>>三级块

## 4、链接
直接在添加的链接前面加上空格号，取别名时用方括号括起来，链接放在后面的圆括号后面。
 
 [baidu](https://www.baidu.com/)

## 5、图片
![测试图片](images/test/1.png)

## 6、表格
一列|二列|三列|
---|---|---
一栏| |
二栏| |

## 7、标题点
- 第一点(横线后有一个空格)
- 第二点
- 第三点

* 第一点(横线后有一个空格)
* 第二点
    * 第三点
    
## 8、代码换行
文本后面一个空格，然后按回车。

milepost-framework
是 
一 
个 
好框架。

## 9、加粗字体

**加粗**字体1

## 10、字体颜色

<font color=#0099ff size=7 face="黑体">color=#0099ff size=72 face="黑体"</font>
<font color=#00ffff size=72>color=#00ffff</font>
<font color=gray size=72>color=gray</font>


