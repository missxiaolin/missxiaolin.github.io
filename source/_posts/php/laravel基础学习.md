---
title: laravel基础学习
date: 2017-04-03 15:16:46
categories: ["PHP","laravel"]
tags: 'php'
---

### lareval安装

1.composer global require "laravel/installer"

2.加入环境变量

~~~
export PATH=$PATH:~/.composer/vendor/bin/（立刻生效）
vi ~/.bash_profile
~~~

3.laravel new blog

### 数据迁移

- 'unix_socket'  => '/Applications/MAMP/tmp/mysql/mysql.sock',

1.php artisan migrate迁移

2.生成迁移文件

~~~
php artisan make:migration create_students_table --create=students
~~~

3.填充文件

~~~
php artisan make:seeder StudentTableSeeder
~~~

4.执行单个填充文件

~~~
php artisan db:seed --class=StudentTableSeeder
~~~

4.单元测试

全部测试

~~~
vendor/bin/phpunit
~~~

指定类单元测试

~~~
vendor/bin/phpunit tests/Feature/ExampleTest.php 
~~~

指定方法进行单元测试

~~~
vendor/bin/phpunit --filter=testBasicTest 
~~~




