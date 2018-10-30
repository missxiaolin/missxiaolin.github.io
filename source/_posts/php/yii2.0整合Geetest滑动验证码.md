---
title: yii2.0整合Geetest滑动验证码
date: 2017-04-08 15:16:46
categories: ["PHP","yii"]
tags: 'php'
---

今天我们就来讲讲滑动验证码，这是一个体验好安全高的方案；
普通的数字、字母组成的验证码大多数很多平台都有去识别这个图片上的文字、数字、字母……
官方：[http://www.geetest.com/](http://www.geetest.com/)

做好之后效果图
<img src="http://www.missxiaolin.com/8.png">
好了不多说了，进入我们今天的主题吧：
1.注册获取到id和key
<img src="http://www.missxiaolin.com/10.png">

2.引入sdk

[官方下载地址]()

[可以直接在我这边复制](http://www.missxiaolin.com/Geetest.php)

3.先看看前端我们需要引入什么吧

~~~
<form id="geetest" action="" method="post" style="margin-top: 50px;" style="margin:0 0 0 200px;">
    <div id="captcha"></div>
    <input type="submit" value="post提交登录">
</form>
<script src="/statics/js/jquery-1.10.2.min.js"></script>

<script src="http://static.geetest.com/static/tools/gt.js"></script>//引入官方的js

<script>
    var handler = function (captchaObj) {
        // 将验证码加到id为captcha的元素里
        captchaObj.appendTo("#captcha");
    };
    console.log(handler);
    // 使用initGeetest接口
    // 参数1：配置参数，与创建Geetest实例时接受的参数一致
    // 参数2：回调，回调的第一个参数验证码对象，之后可以使用它做appendTo之类的事件
    initGeetest({
        gt: "<?= $get_response_str['gt'] ?? '' ?>",
        challenge: "<?= $get_response_str['challenge'] ?? '' ?>",
        product: "float", // 产品形式
        offline: !<?= $get_response_str['success'] ?? '' ?>
    }, handler);

</script>
~~~

4.先来说说yii存取储session

~~~
//存
Yii::$app->session['geetest'] = [
    'gtserver' => $status,
    'user_id'  => $user_id
];
//取
print_r($session = Yii::$app->session['geetest']);
~~~


5.前端到这里就已经完成了，接下来我们看看php需要做什么

~~~
//配置项
<?php
return [
    //验证码
    'CODE_ID'             => '63230acdc3b8dfaf53645a27967c72d1',
    'CODE_KEY'            => '088e11955b70908551f64b9aeac4f68f',
];
?>

//控制器
public function actionCode()
{
    $data = [];
    $code_id = Yii::$app->params['CODE_ID'];
    $code_key = Yii::$app->params['CODE_KEY'];
    $geetest = new Geetest($code_id, $code_key);
    $user_id = "test";
    $status = $geetest->pre_process($user_id);
    //这里是验证是否通过验证
    if (Yii::$app->request->isPost) {
            $geetest_data = Yii::$app->session['geetest'];
            $user_id = $geetest_data['user_id'];
            $code_data = Yii::$app->request->post();
            if ($geetest_data['gtserver'] == 1) {
                $result = $geetest->success_validate($code_data['geetest_challenge'], $code_data['geetest_validate'], $code_data['geetest_seccode'], $user_id);
                if ($result) {
                    echo '验证成功';
                } else {
                    echo '验证失败';
                }
            } else {
                if ($geetest->fail_validate($code_data['geetest_challenge'], $code_data['geetest_validate'], $code_data['geetest_seccode'])) {
                    echo '验证成功';
                } else {
                    echo '验证失败';
                }
            }
        }
    //存入session
    Yii::$app->session['geetest'] = [
        'gtserver' => $status,
        'user_id'  => $user_id
    ];
    $data['get_response_str'] = json_decode($geetest->get_response_str(), true);
    return $this->renderPartial('code1', $data);
}
~~~

做到这里大家其实已经可以看到验证码的效果了

