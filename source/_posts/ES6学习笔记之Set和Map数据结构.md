---
title: ES6学习笔记之Set和Map数据结构
date: 2018-11-08 18:30:49
tags:
---

## 1. Set

ES6 提供的一种新的数据结构，它类似于数组，但是成员的值都是唯一的，没有重复的值。

Set本身是一个构造函数，用来生成Set数据结构。

Set 函数可以接受一个数组（或者具有 iterable 接口的其他数据结构）作为参数，用来初始化。

```javascript
// 例一   接受数组作为参数
const set = new Set([1, 2, 3, 4, 4]);
[...set] // [1, 2, 3, 4]

// 例二   接受类型数组的对象作为参数
const set = new Set(document.querySelectorAll('div'));

```
> 上面的代码也展示了一种去除数组重复成员的方法。
```
// 去除数组的重复成员
[...new Set(array)]

```

###  Set实例的属性和方法

#### 1. 属性

  -`Set.prototype.constructor`: 构造函数，默认就是`Set`函数
  -`Set.prototype.size`: 返回`Set`实例的成员总数

#### 2. 方法
  Set 实例的方法分为两大类： 操作方法（用于操作数据）和遍历方法（用于遍历成员）

  #####  1）操作方法

  -`add(value)`：添加某个值，返回 Set 结构本身。
  -`delete(value)`： 删除某个值，返回一个布尔值，表示删除是否成功。
  -`has(value)`：返回一个布尔值，表示该值是否为`Set`的成员。
  -`clear()`：清除所有成员，没有返回值。

  ```javascript
    const s = new Set();
    s.add(1).add(2).add(2);
    // 注意 2 被加入了两次

    s.size; // 2

    s.has(1); // true
    s.has(2); // true
    s.has(3); // false

    s.delete(2);
    s.has(2); // false

    s.clear();
  ```

  下面是一个对比，看看在判断是否包含一个键上面，`Object`结构和`Set`结构的写法不同。
  ```javascript
  // 对象的写法
  const properties = {
    'width': 1,
    'height': 1
  };

  if (properties[someName]) {
    // do something
  }

  // Set的写法
  const properties = new Set();

  properties.add('width');
  properties.add('height');

  if(properties.has(someName)) {
    // do something
  }

  ```

  `Array.from`方法可以将 Set 结构转为数组。

  ```javascript
    const items = new Set([1, 2, 3, 4, 5, 5]);
    const array = Array.from(items);
  ```

  > 这就提供了去除数组重复成员的另一种方法。
  ```javascript
    function dedupe(array) {
      return Array.from(new Set(array));
    }

    dedupe([a, 1, 2, 3, a]) // [1, 2, 3]
  ```
  ##### 2）遍历方法

  Set 结构的实例有四个遍历方法，可以用于遍历成员。

  -`keys()`： 返回键名的遍历器
  -`values()`：返回键值的遍历器
  -`entries()`：返回键值对的遍历器
  -`forEach()`：使用回调函数遍历每个成员

  需要特别指出的是，`Set`的遍历顺序就是插入顺序。这个特性有时非常有用，比如使用 Set 保存一个回调函数列表，调用时就能保证按照添加顺序调用。

  ###### (1) `keys()`, `values()`, `entries()`

  `keys()`方法、`values()`方法、`entries()`方法返回的都是遍历器对象。由于Set接否没有键名，只有键值（或者说键名和键值是同一个值），所以`keys()`方法和`values()`方法的行为完全一致。

  ```javascript
  let set = new Set(['red', 'green', 'blue']);

  for (let item in set.values()) {
    console.log(item);
  }
  // red
  // green
  // blue

  for (let item of set.values()) {
    console.log(item);
  }
  // red
  // green
  // blue

  for (let item of set.entries()) {
    console.log(item);
  }
  // ["red", "red"]
  // ["green", "green"]
  // ["blue", "blue"]
  ```
  上面代码中，`entries`方法返回的遍历器，同时包括键名和键值，所以咩次输出一个数组，它的两个成员完全相等。

  Set 结构的实例默认可遍历，它的默认遍历器生成函数就是它的`values`方法。

  ```javascript
  Set.prototype[Symbol.iterator] === Set.prototype.values
  // true
  ```

  这意味着，可以省略`values`方法，直接用`for...of`循环遍历Set。

  ```javascript
  let set = new Set(['red', 'green', 'blue']);

  for (let x of set) {
    console.log(x);
  }
  // red
  // green
  // blue
  ```
  ###### (2) `forEach()`

  Set 结构的实例与数组一样，也拥有`forEach`方法，用于对每个成员执行某种操作，没有返回值。

  ```javascript
  set = new Set([1, 4, 9]);
  set.forEach((value, key) => console.log(key + ' ：' + value))
  // 1 ：1
  // 4 ：4
  // 9 ：9
  ```
  上面代码说明，`forEach`方法的参数就是一个处理函数。该函数的参数与数组的`forEach`一致，依次为键值、键名、集合本身（上例省略了该参数）。这里需要注意，Set结构的键名就是键值（两者是同一个值），因此第一个参数与第二个参数的值永远都是一样的。

  另外，`forEach`方法还可以有第二个参数，表示绑定处理函数内部的`this`对象。

  ##### (3) 遍历的应用

  扩展运算符（`...`）内部使用`for...of`循环，所以也可以用于 Set 结构。

  ```javascript
  let arr = [3, 5, 2, 2, 5, 5];
  let unique = [...new Set(arr)];
  // [3, 5, 2]
  ```
  上面的代码也说明，扩展运算符和 Set 结构相结合，就可以去除数组的重复成员。

  而且，数组的`map`和`filter`方法也可以间接用于 Set 了。
  ```javascript
  let set = new Set([1, 2, 3]);
  set = new Set([...set].map(x => x * 2));
  // 返回的Set结构：{2, 4, 6}

  let set = new Set([1, 2, 3, 4, 5]);
  set = new Set([...set].filter(x => (x % 2) === 0));
  // 返回的Set结构：{2, 4}
  ```

  因此使用 Set 可以很容易地实现并集（Union）、交集（Intersect）和差集（Difference）。

  ```javascript
  let a = new Set([1, 2, 3]);
  let b = new Set([4, 3, 2]);

  // 并集
  let union = new Set([...a, ...b]);
  // Set {1, 2, 3, 4}

  // 交集
  let intersect = new Set([...a].filter(x => b.has(x)));
  // set {2, 3}

  // 差集
  let difference = new Set([...a].filter(x => !b.has(x)));
  // Set {1}
  ```

  如果想在遍历操作中，同步改变原来的 Set 结构，目前没有直接的方法，但有两种变通方法。一种是利用原 Set 结构映射出一个新的结构，然后赋值给原来的 Set 结构；另一种是利用`Array.from`方法。

  ```javascript
  // 方法一
  let set = new Set([1, 2, 3]);
  set = new Set([...set].map(val => val * 2));
  // set的值是2, 4, 6

  // 方法二
  let set = new Set([1, 2, 3]);
  set = new Set(Array.from(set, val => val * 2));
  // set的值是2, 4, 6
  ```

  上面代码提供了两种方法，直接在遍历操作中改变原来的 Set 结构。

  -------

  ## 2. Map





