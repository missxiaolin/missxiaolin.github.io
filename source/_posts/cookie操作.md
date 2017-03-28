---
title: yii cookie的使用
date: 2017-03-21 15:16:46
categories: "php"
tags: 'php'
---

### yii cookie

~~~
$get_cookie = Yii::$app->request->cookies;//获取cookie
$store_cookies = Yii::$app->response->cookies;//存入cookie
~~~

### cookie 不加密

~~~
Yii::$app->request->enableCookieValidation = false;
~~~

### 存入cookie

~~~
$store_cookies->add(new Cookie([
    'name'     => '_provider_content',
    'value'    => json_encode($ids),
    'httpOnly' => false,//为了能让js取到cookie要设置
    'domain' => '.xl.com',//设置域
    'path' => '/',路径
]));
~~~

### 取出cookie

~~~
$cookie_provider_ids = $get_cookie->get('_provider_content', []);
~~~

### 删除cookie

~~~
$cookies = Yii::$app->request->cookies;
 
$cookies->remove('user');
~~~

### js设置cookie

~~~
/**
 * 设置cookie
 * @param name
 * @param value
 * @param days
 */
Cookie.prototype.setCookie = function (name, value,domain, days) {
    if (days) {
        var date = new Date();
        date.setTime(date.getTime() + (days * 24 * 60 * 60 * 1000));
        var expires = "; expires=" + date.toGMTString();
    } else {
        var expires = "";
    }
    document.cookie = name + "=" + value + expires + "; path=/"+";domain="+domain;
};
~~~

### js获取cookie

~~~
/**
* 获取cookie
* @param name
* @returns {*}
*/
Cookie.prototype.getCookie = function (name) {
	var nameEQ = name + "=";
	var ca = document.cookie.split(';');
	for (var i = 0; i < ca.length; i++) {
	    var c = ca[i];
	    while (c.charAt(0) == ' ') c = c.substring(1, c.length);
	    if (c.indexOf(nameEQ) == 0) return c.substring(nameEQ.length, c.length);
	}
	return null;
};
~~~

### js删除cookie

~~~
/**
 * 删除cookie
 * @param name
 */
Cookie.prototype.deleteCookie = function (name) {
    setCookie(name, "", -1);
};
~~~

### decodeURI和decodeURIComponent

函数编码的 URI 进行解码。

encodeURI（）：用于编码完整的URI，它不对URI中的特殊字符进行解码：例如冒号、前斜杠、问号、英镑符号  

decodeURIComponent（）：用于编码完整的URI，它不对URI中的特殊字符进行解码：例如冒号、前斜杠、问号、英镑符号  
 
### encodeURI和encodeURIComponent

可把字符串作为 URI 进行编码。encodeURI()和encodeURIComponent()的用法基本相同，区别在于encodeURIComponent()也对"?"等特殊字符进行编码。

### JSON.stringify

js：数组转json

### JSON.parse

js：json转数组

### cookie组件

~~~
define(['jquery'], function ($) {

    function Cookie(options) {}


    /**
     * 设置cookie
     * @param name
     * @param value
     * @param days
     */
    Cookie.prototype.setCookie = function (name, value, days) {
        if (days) {
            var date = new Date();
            date.setTime(date.getTime() + (days * 24 * 60 * 60 * 1000));
            var expires = "; expires=" + date.toGMTString();
        } else {
            var expires = "";
        }
        document.cookie = name + "=" + value + expires + "; path=/";
    };

    /**
     * 获取cookie
     * @param name
     * @returns {*}
     */
    Cookie.prototype.getCookie = function (name) {
        var nameEQ = name + "=";
        var ca = document.cookie.split(';');
        for (var i = 0; i < ca.length; i++) {
            var c = ca[i];
            while (c.charAt(0) == ' ') c = c.substring(1, c.length);
            if (c.indexOf(nameEQ) == 0) return c.substring(nameEQ.length, c.length);
        }
        return null;
    };

    /**
     * 删除cookie
     * @param name
     */
    Cookie.prototype.deleteCookie = function (name) {
        setCookie(name, "", -1);
    };

    return Cookie;
});
~~~