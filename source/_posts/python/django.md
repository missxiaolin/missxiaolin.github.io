---
title: django
date: 2018-01-09 15:30:46
categories: ["python"]
tags: 'python'
---

## python3安装

brew install python3

### django安装

~~~
pip3 install django
pip3 install mysqlclient
~~~

### 生成项目

~~~
django-admin.py startproject mysite2
django-admin.py startapp TestModel
python manage.py makemigrations
python manage.py migrate
~~~

## python 3.6集成安装xadmin

### 安装xadmin

通过pip进行安装

~~~
pip install xadmin 
~~~

安装完成后，发现会自动把关联的对应包给一起安装上 

追查发现，通过pip安装的xadmin，目前是只支持2.X版本，不支持3.X，如果需要在python 3.X环境下安装xadmin，需要执行如下命令：

~~~
pip install git+git://github.com/sshwsfc/xadmin.git
~~~

### 源码安装xadmin

下载xadmin

~~~
https://github.com/sshwsfc/xadmin.git
~~~

pip卸载xadmin

~~~
pip uninstall xadmin
~~~

将xadmin添加到settings.py应用列表

~~~
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    # 需要添加的两个app
    'xadmin',
    'crispy_forms',
]
~~~

配置数据库

~~~
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',  # 或者使用 mysql.connector.django
        'NAME': 'django',
        'USER': 'root',
        'PASSWORD': 'root',
        'HOST': 'localhost',
        'PORT': '3306',
    }
}
~~~

使用migrate同步数据表

~~~
python manager.py makemigrations
python manager.py migrate
~~~

创建用户

~~~
python manager.py createsuperuser
~~~




