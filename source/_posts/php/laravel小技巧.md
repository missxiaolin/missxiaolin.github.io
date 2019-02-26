---
title: laravel 小技巧
date: 2019-02-25 15:16:46
categories: ["PHP","laravel"]
tags: 'php'
---

## laravel小技巧

Laravel 是一个功能丰富的框架。但是，你无法从官方文档中找到所有可用的功能。以下是一些你可能不知道的功能。

### 获取原始属性

当修改一条 Eloquent 模型记录的时候你可以通过调用 getOriginal() 方法获取记录的原始属性

~~~
$user = App\User::first();
$user->name;                   //John

$user->name = "Peter";         //Peter

$user->getOriginal('name');    //John
$user->getOriginal();          //原始 $user 记录
~~~

### 检查模型是否被修改

使用 isDirty() 方法确定模型或给定属性是否已被修改

~~~
$user = App\User::first();
$user->isDirty();          //false

$user->name = "Peter";
$user->isDirty();          //true
~~~

也可以检查指定属性是否被修改。

~~~
$user->isDirty('name');    //true
$user->isDirty('age');     //false
~~~

### 获取更改的属性

使用 getChanges() 获取更改的属性

~~~
$user->getChanges()

//[
     "name" => "Peter",
  ]
~~~

注：仅当您使用 syncChanges() 保存模型或同步更新时，才生效

### 定义 deleted_at 字段

默认情况下，Laravel使用deleted_at字段处理软删除。 您可以通过定义DELETED_AT属性来更改它。

~~~
class User extends Model
{
    use SoftDeletes;

     * The name of the "deleted at" column.
     *
     * @var string
     */
    const DELETED_AT = 'is_deleted';
}
~~~

或者定义访问

~~~
class User extends Model
{
    use SoftDeletes;

    public function getDeletedAtColumn()
    {
        return 'is_deleted';
    }
}
~~~

### 保存模型和关系

您可以使用push()方法保存模型及其关联。

~~~
class User extends Model
{
    public function phone()
    {
        return $this->hasOne('App\Phone');
    }
}

$user = User::first();
$user->name = "Peter";

$user->phone->number = '1234567890';

$user->push(); // 这将更新数据库中的用户和电话

~~~

### 重新加载模型

使用 fresh() 重新从数据库加载一个模型。

~~~
$user = App\User::first();
$user->name;               // John

// user 表被其他进程修改。 例：数据库又插入一条 “name” 为 “Peter” 的数据。

$updatedUser = $user->fresh();
$updatedUser->name;       // Peter

$user->name;              // John
~~~

### 重新加载现有模型

你可以使用 refresh() 方法从数据库重新加载具有新值的现有模型。

~~~
$user = App\User::first();
$user->name;               // John

// user 表被其他进程修改。例: “name” 被修改为 “Peter” 。

$user->refresh();
$user->name;              // Peter
~~~

注: refresh() 也会更新模型的关联模型数据。

### 检查模型是否为同一个

使用 is() 方法确定两个模型是否拥有相同主键并且属于同一张表。

~~~
$user = App\User::find(1);
$sameUser = App\User::find(1);
$diffUser = App\User::find(2);

$user->is($sameUser);       // true
$user->is($diffUser);       // false
~~~

### 克隆一个模型

你可以使用 replicate() 方法来复制一个模型到一个新的对象中。

~~~
$user = App\User::find(1);
$newUser = $user->replicate();

$newUser->save();
~~~

### 在 find() 方法中指定查找的属性

当使用 find() 或 findOrFail() 方法时，传入第二个参数可以指定需要查找的属性。

~~~
$user = App\User::find(1, ['name', 'age']);

$user = App\User::findOrFail(1, ['name', 'age']);
~~~
