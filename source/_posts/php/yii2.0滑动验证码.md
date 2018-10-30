---
title: yii2.0滑动验证码
date: 2017-04-09 15:16:46
categories: ["PHP","yii"]
tags: 'php'
---

之前我们用Geetest api做过一个[滑动验证码](https://missxiaolin.github.io/2017/04/08/yii2.0%E6%95%B4%E5%90%88Geetest%E6%BB%91%E5%8A%A8%E9%AA%8C%E8%AF%81%E7%A0%81/)
之后大家都说这个api其实免费版1小时只有500次

<img src="http://www.missxiaolin.com/%E6%BB%91%E5%8A%A8%E9%AA%8C%E8%AF%81%E7%A0%81.png" >
怎么办呢，好吧只能自己写一个了……

大家先下载需要准备的[文件](http://www.missxiaolin.com/%E6%BB%91%E5%8A%A8%E9%AA%8C%E8%AF%81%E7%A0%81.zip)吧!

怎么使用呢
1.先看看后端我们需要做些什么吧
当然需要引入VerifyCode类文件

~~~
public function actionCode()
{
    $data = [];
    $verify_code = new VerifyCode();
    $code_data = $verify_code->getOkPng();
    $temp = array_chunk($code_data['data'], 20);
    $data = [
        'left_pic'  => $temp[0],
        'right_pic' => $temp[1],
        'pg_bg'     => $code_data['bg_pic'],
        'ico_pic'   => $code_data['ico_pic'],
        'y_point'   => $code_data['y_point'],
    ];
    Yii::$app->session['XSVer_VAL_SUM'] = 1;
    return $this->renderPartial('code2', $data);
}
~~~

2.写个ajax验证的方法

~~~
public function actionCheck()
{
    $data = [];
    Yii::$app->response->format = Response::FORMAT_JSON;
    static $v_num = 1;
    $ret = VerifyCode::checkData(Yii::$app->request->post('point'), Yii::$app->session['XSVer']);
    $v_num += Yii::$app->session['XSVer_VAL_SUM'];
    if ($v_num > 6) {
        Yii::$app->session['XSVer_VAL_SUM'] = null;
        Yii::$app->response->statusCode = 400;
        $data = [
            'state' => 4603,
            'data'  => '验证码失效',
        ];
        return $data;
    } else {
        Yii::$app->session['XSVer_VAL_SUM'] = $v_num;
    }
    if ($ret['state'] == 0) {
        Yii::$app->session['XSVer_VAL_SUM'] = 0x111;
        return [
            'state' => 0,
            'data'  => Yii::$app->session['XSVer'],
        ];
    } else {
        Yii::$app->session['XSVer_VAL_SUM'] = null;
        return [
            'state' => 603,
            'data'  => '错误' . $v_num,
        ];
    }

}
~~~

3.下面就是前端了（大家直接可以在上面下载的压缩包可以找到html部分：code1.php）

~~~
//文件在Verify/js/js/drag.js（发送ajax的时候需要改成你们自己的api地址哦）
$.ajax({
    type: "POST",
    url: "/code/check",
    dataType: "JSON",
    async: false,
    data: {point: _x},
    success: function (result) {
        result['state'] == 4603 ? window.location.reload() : '';
        if (result['state'] == 0) {
            for (var i = 1; 4 >= i; i++) {
                $xy_img.animate({left: _x - (30 - 10 * i)}, 50);
                $xy_img.animate({left: _x + 2 * (30 - 10 * i)}, 50, function () {
                    $xy_img.css({'left': result['data']});
                });
            }
            handler.css({'left': maxWidth});
            drag_bg.css({'width': maxWidth});
            $xy_img.removeClass('xy_img_bord');
            dragOk();
        } else {
            drag.css({'color': '#FF0000'});
            text.text('验证失败');
            $xy_img.css({'left': 0});
            handler.css({'left': 0});
            drag_bg.css({'width': 0});
        }
    },
    beforeSend: function () {
        //$(".text-c").children('td').html('<i style="color: lightseagreen">加载中...</i>');
    }
});
~~~

到这里其实已经可以看到效果了

<img src="http://www.missxiaolin.com/%E6%BB%91%E5%8A%A8%E9%AA%8C%E8%AF%81%E7%A0%81%E6%95%88%E6%9E%9C2.png">
