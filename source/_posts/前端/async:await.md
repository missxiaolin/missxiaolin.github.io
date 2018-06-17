---
title: async/await
date: 2018-06-18 15:16:46
categories: "前端之巅"
tags: '前端'
---

## 前言

ES7 引入的 async/await 是 JavaScript 异步编程的一个重大改进，提供了在不阻塞主线程的情况下使用同步代码异步访问资源的能力。在本文中，我们将从不同的角度探索 async/await，并演示如何正确有效地使用它们。

### async/await 的好处

async/await 给我们带来的最重要的好处是同步编程风格。我们来看一个例子。

~~~
// async/await
async getBooksByAuthorWithAwait(authorId) {
  const books = await bookModel.fetchAll();
  return books.filter(b => b.authorId === authorId);
}
// promise
getBooksByAuthorWithPromise(authorId) {
  return bookModel.fetchAll()
    .then(books => books.filter(b => b.authorId === authorId));
}
~~~

很显然，async/await 比 promise 更容易理解。如果忽略掉 await 关键字，代码看起来与其他任意一门同步语言一样（如 Python）。

除了可读性，async/await 还对浏览器提供了原生支持。目前所有的主流浏览器都完全支持异步功能。

原生支持意味着不需要编译代码。更重要的是，它调试起来很方便。在函数入口设置断点并执行跳过 await 行之后，调试器会在 bookModel.fetchAll() 执行时暂停一会儿，然后移动到下一行（也就是.filter）！这比使用 promise 要容易调试得多，因为你必须在.filter 这一行设置另一个断点。

另一个好处是 async 关键字，尽管看起来不是很明显。它声明 getBooksByAuthorWithAwait() 函数的返回值是一个 promise，因此调用者可以安全地调用 getBooksByAuthorWithAwait().then(…) 或 await getBooksByAuthorWithAwait()。比如像下面这段代码：

~~~
getBooksByAuthorWithPromise(authorId) {
  if (!authorId) {
    return null;
  }
  return bookModel.fetchAll()
    .then(books => books.filter(b => b.authorId === authorId));
  }
}
~~~

在上面的代码中，getBooksByAuthorWithPromise 可能返回一个 promise（正常情况）或 null（异常情况），在这种情况下，调用者无法安全地调用.then()。而如果使用 async 声明，则不会出现这种情况。

### async/await 可能会引起误解

有些文章将 async/await 与 promise 进行了比较，并声称它是 JavaScript 异步编程演变的下一代，但我非常不同意这一观点。async/await 是一种改进，但它不过是一种语法糖，它不会完全改变我们的编程风格。

从本质上讲，异步函数仍然是 promise。在正确使用异步函数之前，你必须了解 promise，更糟糕的是，大部分时间需要同时使用 promise 和异步函数。

考虑上例中的 getBooksByAuthorWithAwait() 和 getBooksByAuthorWithPromises() 函数。请注意，它们不仅功能相同，接口也是完全一样的！

这意味着如果直接调用 getBooksByAuthorWithAwait()，它将返回一个 promise。

不过这不一定是件坏事。只是 await 会给人一种感觉：“它可以将异步函数转换为同步函数”。但这实际上是错误的。

### async/await 的陷阱

那么人们在使用 async/await 时可能会犯什么错误？下面列举了一些常见的错误。

#### 太过串行化

虽然 await 可以让你的代码看起来像是同步的，但请记住，它们仍然是异步的，要避免太过串行化。

~~~
async getBooksAndAuthor(authorId) {
  const books = await bookModel.fetchAll();
  const author = await authorModel.fetch(authorId);
  return {
    author,
    books: books.filter(book => book.authorId === authorId),
  };
}
~~~

上面的代码在逻辑上看起来很正确，但这样做其实是不对的。

- await bookModel.fetchAll() 将等到 fetchAll() 返回。
- 然后 await authorModel.fetch(authorId) 将被调用。

注意，authorModel.fetch(authorId) 不依赖 bookModel.fetchAll() 的结果，事实上它们可以并行调用！然而，因为在这里使用了 await，两个调用变成串行的，总的执行时间将比并行版本要长得多。

正确的方法应该是：

~~~
async getBooksAndAuthor(authorId) {
  const bookPromise = bookModel.fetchAll();
  const authorPromise = authorModel.fetch(authorId);
  const book = await bookPromise;
  const author = await authorPromise;
  return {
    author,
    books: books.filter(book => book.authorId === authorId),
  };
}
~~~

或者更糟糕的是，如果你想要逐个获取物品清单，你必须使用 promise：

~~~
async getAuthors(authorIds) {
  // WRONG, this will cause sequential calls
  // const authors = _.map(
  //   authorIds,
  //   id => await authorModel.fetch(id));
// CORRECT
  const promises = _.map(authorIds, id => authorModel.fetch(id));
  const authors = await Promise.all(promises);
}
~~~

总之，你仍然需要将流程视为异步的，然后使用 await 写出同步的代码。在复杂的流程中，直接使用 promise 可能更方便。

### 错误处理

在使用 promise 时，异步函数有两个可能的返回值。对于正常情况，可以使用.then()，而对于异常情况，则使用.catch()。不过在使用 async/await 时，错误处理可能会变得有点蹊跷。

#### try…catch

最标准的（也是我推荐的）方法是使用 try…catch 语句。在调用 await 函数时，如果出现非正常状况就会跑出异常。比如：

~~~
class BookModel {
  fetchAll() {
    return new Promise((resolve, reject) => {
      window.setTimeout(() => { reject({'error': 400}) }, 1000);
    });
  }
}
// async/await
async getBooksByAuthorWithAwait(authorId) {
try {
  const books = await bookModel.fetchAll();
} catch (error) {
  console.log(error);    // { "error": 400 }
}
~~~

在捕捉到异常之后，我们有几种方法来处理它：

- 处理异常，并返回一个正常值。（不在 catch 块中使用任何 return 语句相当于使用 return undefined，undefined 也是一个正常值。）
- 如果你想让调用者来处理它，就将它抛出。你可以直接抛出错误对象，比如 throw error，这样就可以在 promise 链中使用 await getBooksByAuthorWithAwait() 函数（也就是像 getBooksByAuthorWithAwait().then(...).catch(error => …) 这样调用它）。或者你可以将错误包装成 Error 对象，比如 throw new Error(error)，那么在控制台中显示这个错误时它将给出完整的堆栈跟踪信息。
- 拒绝它，比如 return Promise.reject(error)。这相当于 throw error，因此不推荐使用。

这种方法也有一个缺陷。由于 try...catch 会捕获代码块中的每个异常，所以通常不会被 promise 捕获的异常也会被捕获到。比如：

~~~
class BookModel {
  fetchAll() {
    cb();    // note `cb` is undefined and will result an exception
    return fetch('/books');
  }
}
try {
  bookModel.fetchAll();
} catch(error) {
  console.log(error);  // This will print "cb is not defined"
}
~~~

运行此代码，你将会在控制台看到“ReferenceError：cb is not defined”错误，消息的颜色是黑色的。错误消息是通过 console.log() 输出的，而不是 JavaScript 本身。有时候这可能是致命的：如果 BookModel 被包含在一系列函数调用中，并且其中一个调用把错误吞噬掉了，那么找到这样的 undefined 错误将非常困难。

#### 让函数返回两个值

错误处理的另一种方式是受到了 Go 语言启发，它允许异步函数返回错误和结果。

简单地说，我们可以像这样使用异步函数：

~~~
[err, user] = await to(UserModel.findById(1));
~~~

我个人不喜欢这种方法，因为它将 Go 语言的风格带入到了 JavaScript 中，感觉不自然。但在某些情况下，这可能相当有用。

#### 使用.catch

我们要介绍的最后一种方法是继续使用.catch()。

回想一下 await 的功能：它将等待 promise 完成工作。另外，promise.catch() 也会返回一个 promise！所以我们可以这样进行错误处理：

~~~
// books === undefined if error happens,
// since nothing returned in the catch statement
let books = await bookModel.fetchAll()
  .catch((error) => { console.log(error); });

~~~

这种方法有两个小问题：

- 它是 promise 和异步函数的混合体。你仍然需要了解 promise 的工作原理才能看懂这段代码。
- 错误处理出现在普通代码逻辑之前，这样不直观。

ES7 引入的 async/await 关键字绝对是对 JavaScript 异步编程的重大改进。它让代码更易于阅读和调试。然而，要正确使用它们，人们必须了解 promise。它们不过是语法糖，本质上仍然是 promise。







