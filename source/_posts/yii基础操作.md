---
title: yii基础操作
date: 2017-03-23 15:16:46
categories: "php"
tags: 'php'
---

### yii友好时间表示方法
Yii::$app->formatter->asRelativeTime('1447565922'); 
### Yii2 获取接口传过来的 JSON 数据：
Yii::$app->request->rawBody;
### 座机和手机号码必须填写一个：

~~~
public function rules(){

    return [
        [['telephone', 'mobile'], function ($attribute, $param) {//至少要一个
            if (empty($this->telephone) && empty($this->mobile)) {
                $this->addError($attribute, 'telephone/mobile至少要填一个');
            }
        }, 'skipOnEmpty' => false],
    ];
}
~~~

### where多条件查询示例：

~~~
//and复杂示例：
$time = time();

Member::find()->where(['and', ['userid' => 1, 'company' =>'测试公司'], ['>', 'addtime', $time]]);

//SELECT * FROM `member` WHERE ((`userid`=1) AND (`company`='测试公司')) AND (`addtime` > 1447587486)

//and和or组合示例：

$query = Member::find()->where(['and', ['>','userid',2], ['or', ['company' => '深圳市新民家具有限公司'], ['address' => '深圳']]]);
//SELECT * FROM `member` WHERE (`userid` > 2) AND ((`company`='深圳市新民家具有限公司') OR (`address`='深圳'))
### 关于事务：
优雅的写法

Yii::$app->db->transaction(function() {

    $order = new Order($customer);
    $order->save();
});
~~~

这相当于下列冗长的代码：

~~~
$transaction = Yii::$app->db->beginTransaction();

try {

    $order = new Order($customer);
    $order->save();
    $transaction->commit();
} catch (\Exception $e) {

    $transaction->rollBack();
    throw $e;
}
~~~

### rest风格API获取客户端提交的get和post的数组
// post

Yii::$app->request->bodyParams

// get

Yii::$app->request->queryParams;
### 验证某个ID值是否存在

~~~
//之前一直用$model->findOne($id);exists()方法，资源节约，有没有？！

public function validateAttribute($model, $attribute)

{

   $value = $model->$attribute;
   
   if (!Status::find()->where(['id' => $value])->exists()) {
   
       $model->addError($attribute, $this->message);
   }
}
~~~

### 防止 SQL 和 Script 注入

~~~
use yii\helpers\Html;

use yii\helpers\HtmlPurifier;

echo Html::encode($view_hello_str) //可以原样显示<script></script>代码  

echo HtmlPurifier::process($view_hello_str)  //可以过滤掉<script></script>代码
~~~

### 批量查询

~~~
如查询并循环10000条数据。一次性拿1万条内存会有压力，通过批量查询，每次拿1000条，那么内存始终只有1000条的占有量。

foreach(Member::find()->batch(1000) as $value){

    //do something
    
    //print_r(count($value));
    
}
~~~

### yii2中关闭debug后return $this->redirect($url)；不能跳转，服务器报500错误。

~~~
1.在正常情况下，使用 return $this->redirect($url);

2.在解决方案1不生效时，用$this->redirect($url);Yii::$app->response->send();

3.在解决方案2不生效时，用$this->redirect($url);Yii::$app->end();
~~~

### YII命令行生成数据库文件

~~~
自动列出可用的migrate文件

php yii migrate
 从vendor/callmez/wechat/migrations目录下生成数据表

php yii migrate --migrationPath=@callmez/wechat/migrations
从当前应用/migrations/db1下初始化数据到db1表

php yii migrate --migrationPath=@app/migrations/db1 --db=db1
~~~

### 关联查询

~~~
//客户表Model：CustomerModel 
//订单表Model：OrdersModel
//国家表Model：CountrysModel
//首先要建立表与表之间的关系 
//在CustomerModel中添加与订单的关系
      
Class CustomerModel extends \yii\db\ActiveRecord
{
    ...
    
    public function getOrders()
    {
        //客户和订单是一对多的关系所以用hasMany
        //此处OrdersModel在CustomerModel顶部别忘了加对应的命名空间
        //id对应的是OrdersModel的id字段，order_id对应CustomerModel的order_id字段
        return $this->hasMany(OrdersModel::className(), ['id'=>'order_id']);
    }
     
    public function getCountry()
    {
        //客户和国家是一对一的关系所以用hasOne
        return $this->hasOne(CountrysModel::className(), ['id'=>'Country_id']);
    }
    ....
}
~~~
      
// 查询客户与他们的订单和国家

~~~
CustomerModel::find()->with('orders', 'country')->all();

// 查询客户与他们的订单和订单的发货地址

CustomerModel::find()->with('orders.address')->all();

// 查询客户与他们的国家和状态为1的订单

CustomerModel::find()->with([

    'orders' => function ($query) {
        $query->andWhere('status = 1');
        },
        'country',
])->all();
~~~

### 头部引入js

~~~
$this->registerJsFile("@web/static/js/jquery-2.0.0.min.js",['depends' => ['frontend\assets\AppAsset'], 'position' => $this::POS_HEAD]);
~~~

### mac yii2.0运行生成

~~~
/Ap表plications/MAMP/bin/php/php5.5.38/bin/php yiis
 
migrate
~~~

### 数组助手类Array Helper

~~~
$array = [

	['id'=1,'name'=>'a','class'=>'x],
	
	['id'=2,'name'=>'b','class'=>'y]

]

$result = ArrayHelper::map($array,'id','name');

结果：[

	'1'=>a,
	
	'2'=>b

]
~~~

### where的使用(where() orWhere andWhere)

~~~
and ['and' 'id=1','id=2'] sql语句id=1 and id=2

or  ['or' 'id=1','id=2'] sql语句id=1 or id=2]

in  ['in' 'id',[1,2,3]] sql语句in(1,2,3)

between ['between' 'id',1,10] sql语句id between 1 and 10

like ['like','name',['test',sample]] sql语句name like '%test%' and name like '%sample%'

比较  ['>=',id,10]     sql语句 id>=10
~~~

### 登录判断

~~~
public function behaviors(){
    return [
        'access' => [
            'class' => AccessControl::className(),
            'only' => ['index', 'create', 'update'],
            'rules' => [
                // 允许认证用户
                [
                    'allow' => true,
                    'roles' => ['@'],
                ],
                // 默认禁止其他用户
            ],       
        ],
        'verbs' => [
            'class' => VerbFilter:: className(),
            'actions' => [
                 'index'  => [ 'get'],            //只允许get方式访问
                 'create' => [ 'post'],          //只允许用post方式访问
                 'update' => [ 'post']
             ],
        ],
    ];
}
~~~

### yii2.0控制器过滤(增加权限)
~~~
return [
            'access' => [
                'class' => AccessControl::className(),
                'rules' => [
                    [
                        'actions' => [
                            'identify',//默认认证页
                            'bussiness-identify',
                            'developer-identify',
                        ],
                        'allow'   => true,
                        'roles'   => ['@'], //表示登录的用户
                    ],
                    [
                        'actions' => [
                            'competitiveness',
                            'competitiveness-detail',
                            'look-me',
                        ],
                        'allow' => true,
                        'roles'   => ['@'], //表示登录的用户
                        'matchCallback' => function ($rule, $action) {
                            $user = $this->getUser();
                            return $user->role_type == UserType::UNKNOWN ? false : true;
                        },
                    ]
                ],
            ],
        ];
~~~  