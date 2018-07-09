---
title: JavaScript中的深拷贝和浅拷贝
date: 2018-07-09 15:16:46
categories: "前端之巅"
tags: '前端'
---

### 在说深拷贝与浅拷贝前，我们先看两个简单的案例：

~~~
//案例1
var num1 = 1, num2 = num1;
console.log(num1) //1
console.log(num2) //1

num2 = 2; //修改num2
console.log(num1) //1
console.log(num2) //2

//案例2
var obj1 = {x: 1, y: 2}, obj2 = obj1;
console.log(obj1) //{x: 1, y: 2}
console.log(obj2) //{x: 1, y: 2}

obj2.x = 2; //修改obj2.x
console.log(obj1) //{x: 2, y: 2}
console.log(obj2) //{x: 2, y: 2}
~~~

按照常规思维，obj1应该和num1一样，不会因为另外一个值的改变而改变，而这里的obj1 却随着obj2的改变而改变了。同样是变量，为什么表现不一样呢？这就要引入JS中基本类型和引用类型的概念了。

### 基本类型和引用类型

```
ECMAScript变量可能包含两种不同数据类型的值：基本类型值和引用类型值。基本类型值指的是那些保存在栈内存中的简单数据段，即这种值完全保存在内存中的一个位置。而引用类型值是指那些保存堆内存中的对象，意思是变量中保存的实际上只是一个指针，这个指针指向内存中的另一个位置，该位置保存对象。
```

打个比方，基本类型和引用类型在赋值上的区别可以按“连锁店”和“单店”来理解：基本类型赋值等于在一个新的地方安装连锁店的规范标准新开一个分店，新开的店与其他旧店互不相关，各自运营；而引用类型赋值相当于一个店有两把钥匙，交给两个老板同时管理，两个老板的行为都有可能对一间店的运营造成影响。

上面清晰明了的介绍了基本类型和引用类型的定义和区别。目前基本类型有：Boolean、Null、Undefined、Number、String、Symbol，引用类型有：Object、Array、Function。之所以说“目前”，因为Symbol就是ES6才出来的，之后也可能会有新的类型出来。

再回到前面的案例，案例1中的值为基本类型，案例2中的值为引用类型。案例2中的赋值就是典型的浅拷贝，并且深拷贝与浅拷贝的概念只存在于引用类型。

### 深拷贝与浅拷贝

既然已经知道了深拷贝与浅拷贝的来由，那么该如何实现深拷贝？我们先分别看看Array和Object自有方法是否支持：

Array

~~~
var arr1 = [1, 2], arr2 = arr1.slice();
console.log(arr1); //[1, 2]
console.log(arr2); //[1, 2]

arr2[0] = 3; //修改arr2
console.log(arr1); //[1, 2]
console.log(arr2); //[3, 2]
~~~

### 此时，arr2的修改并没有影响到arr1，看来深拷贝的实现并没有那么难嘛。我们把arr1改成二维数组再来看看：

~~~
var arr1 = [1, 2, [3, 4]], arr2 = arr1.slice();
console.log(arr1); //[1, 2, [3, 4]]
console.log(arr2); //[1, 2, [3, 4]]

arr2[2][1] = 5; 
console.log(arr1); //[1, 2, [3, 5]]
console.log(arr2); //[1, 2, [3, 5]]
~~~

咦，arr2又改变了arr1，看来slice()只能实现一维数组的深拷贝。

具备同等特性的还有：concat、Array.from() 。

### Object

1.Object.assign()

~~~
var obj1 = {x: 1, y: 2}, obj2 = Object.assign({}, obj1);
console.log(obj1) //{x: 1, y: 2}
console.log(obj2) //{x: 1, y: 2}

obj2.x = 2; //修改obj2.x
console.log(obj1) //{x: 1, y: 2}
console.log(obj2) //{x: 2, y: 2}
~~~

~~~
var obj1 = {
    x: 1, 
    y: {
        m: 1
    }
};
var obj2 = Object.assign({}, obj1);
console.log(obj1) //{x: 1, y: {m: 1}}
console.log(obj2) //{x: 1, y: {m: 1}}

obj2.y.m = 2; //修改obj2.y.m
console.log(obj1) //{x: 1, y: {m: 2}}
console.log(obj2) //{x: 2, y: {m: 2}}
~~~

经测试，Object.assign()也只能实现一维对象的深拷贝。

2.JSON.parse(JSON.stringify(obj))

~~~
var obj1 = {
    x: 1, 
    y: {
        m: 1
    }
};
var obj2 = JSON.parse(JSON.stringify(obj1));
console.log(obj1) //{x: 1, y: {m: 1}}
console.log(obj2) //{x: 1, y: {m: 1}}

obj2.y.m = 2; //修改obj2.y.m
console.log(obj1) //{x: 1, y: {m: 1}}
console.log(obj2) //{x: 2, y: {m: 2}}
~~~

JSON.parse(JSON.stringify(obj)) 看起来很不错，不过MDN文档 的描述有句话写的很清楚：

```
undefined、任意的函数以及 symbol 值，在序列化过程中会被忽略（出现在非数组对象的属性值中时）或者被转换成 null（出现在数组中时）。
```

我们再来把obj1改造下：

~~~
var obj1 = {
    x: 1,
    y: undefined,
    z: function add(z1, z2) {
        return z1 + z2
    },
    a: Symbol("foo")
};
var obj2 = JSON.parse(JSON.stringify(obj1));
console.log(obj1) //{x: 1, y: undefined, z: ƒ, a: Symbol(foo)}
console.log(JSON.stringify(obj1)); //{"x":1}
console.log(obj2) //{x: 1}
~~~

发现，在将obj1进行JSON.stringify()序列化的过程中，y、z、a都被忽略了，也就验证了MDN文档的描述。既然这样，那JSON.parse(JSON.stringify(obj))的使用也是有局限性的，不能深拷贝含有undefined、function、symbol值的对象，不过JSON.parse(JSON.stringify(obj))简单粗暴，已经满足90%的使用场景了。

经过验证，我们发现JS 提供的自有方法并不能彻底解决Array、Object的深拷贝问题。只能祭出大杀器：递归

~~~
function deepCopy(obj) {
    // 创建一个新对象
    let result = {}
    let keys = Object.keys(obj),
        key = null,
        temp = null;

    for (let i = 0; i < keys.length; i++) {
        key = keys[i];    
        temp = obj[key];
        // 如果字段的值也是一个对象则递归操作
        if (temp && typeof temp === 'object') {
            result[key] = deepCopy(temp);
        } else {
        // 否则直接赋值给新对象
            result[key] = temp;
        }
    }
    return result;
}

var obj1 = {
    x: {
        m: 1
    },
    y: undefined,
    z: function add(z1, z2) {
        return z1 + z2
    },
    a: Symbol("foo")
};

var obj2 = deepCopy(obj1);
obj2.x.m = 2;

console.log(obj1); //{x: {m: 1}, y: undefined, z: ƒ, a: Symbol(foo)}
console.log(obj2); //{x: {m: 2}, y: undefined, z: ƒ, a: Symbol(foo)}
~~~	

可以看到，递归完美的解决了前面遗留的所有问题，我们也可以用第三方库：jquery的$.extend和lodash的_.cloneDeep来解决深拷贝。上面虽然是用Object验证，但对于Array也同样适用，因为Array也是特殊的Object。

到这里，深拷贝问题基本可以告一段落了。但是，还有一个非常特殊的场景：

#### 循环引用拷贝

~~~
var obj1 = {
    x: 1, 
    y: 2
};
obj1.z = obj1;

var obj2 = deepCopy(obj1);
~~~

此时如果调用刚才的deepCopy函数的话，会陷入一个循环的递归过程，从而导致爆栈。jquery的$.extend也没有解决。解决这个问题也非常简单，只需要判断一个对象的字段是否引用了这个对象或这个对象的任意父级即可，修改一下代码：

~~~
function deepCopy(obj, parent = null) {
    // 创建一个新对象
    let result = {};
    let keys = Object.keys(obj),
        key = null,
        temp= null,
        _parent = parent;
    // 该字段有父级则需要追溯该字段的父级
    while (_parent) {
        // 如果该字段引用了它的父级则为循环引用
        if (_parent.originalParent === obj) {
            // 循环引用直接返回同级的新对象
            return _parent.currentParent;
        }
        _parent = _parent.parent;
    }
    for (let i = 0; i < keys.length; i++) {
        key = keys[i];
        temp= obj[key];
        // 如果字段的值也是一个对象
        if (temp && typeof temp=== 'object') {
            // 递归执行深拷贝 将同级的待拷贝对象与新对象传递给 parent 方便追溯循环引用
            result[key] = DeepCopy(temp, {
                originalParent: obj,
                currentParent: result,
                parent: parent
            });

        } else {
            result[key] = temp;
        }
    }
    return result;
}

var obj1 = {
    x: 1, 
    y: 2
};
obj1.z = obj1;

var obj2 = deepCopy(obj1);
console.log(obj1); //太长了去浏览器试一下吧～ 
console.log(obj2); //太长了去浏览器试一下吧～ 
~~~

至此，已完成一个支持循环引用的深拷贝函数。当然，也可以使用lodash的_.cloneDeep噢～。


