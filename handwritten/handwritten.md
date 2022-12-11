# 手写代码

## 高频题目

1. 手写instanceof
2. 手写new
3. 手写promise
4. 手写深拷贝
5. 手写防抖节流
6. 手写call/apply/bind函数
7. 数组扁平化
8. 手写柯里化
9. 手写LRU
10. 手写一个add方法
11. 类数组转数组
12. 类数组使用如何使用forof
13. 列表转树，树转列表
14. 冒泡排序
15. 选择排序
16. 快速排序

## 高频题目答案

### 手写instanceof

```javascript
function myInstanceOf(left, right){
     //获取对象的原型链
    let proto = Object.getPrototypeOf(left);
    //判断构造函数的prototype是否在对象的原型链上
    while(true){
        if(!proto)return false;
        if(proto === right.prototype)return true;
        
        proto = Object.getPrototypeOf(proto);
    }
}
```



### 手写new

```javascript
function myNew(fn, ...args){
  //判断参数是否是一个函数
  if(typeof fn !== "function"){
      return console.error("type error");
  }
  //创建一个对象，并将对象的原型绑定到构造函数的原型上
  const obj = Object.create(fn.prototype);
  //调用构造函数，将this绑定到obj上，给obj添加属性
  const value = fn.apply(obj, args); 
  // 假如构造函数返回的是对象，就返回引用类型value,否则返回obj
  return value instanceof Object ? value : obj;
}
```



### 手写promise

```javascript
function myPromise(constructor){
  let self = this;
  self.status = "pending"; //定义状态改变前的初始状态
  self.value = undefined; //定义状态为resolved的时候的状态
  self.reason = undefined; //定义状态为reject的时候的状态
  function resolve(value){
    if(self.status === "pending"){
      self.value = value;
      self.status = "resolved";
    }
  }
  function reject(reason){
    if(self.status === "pending"){
      self.value = value;
      self.status = "rejected";
    }
  }
  
  //捕获构造异常
  try{
    constructor(resolve, reject);
  }catch(e){
    reject(e);
  }
}
myPromise.prototype.then = function(onFullfilled,onRejected){
  let self = this;
  switch(self.status){
    case "resolved": onFullfilled(self.value); break;
    case "rejected": onRejected(self.reanson); break;
    default;
  }
}
//测试
var p = new myPromise(function(resolve,reject){resolve(1)});
p.then(function(x){console.log(x)});
```



### 手写深拷贝

```javascript
function deepClone(target, weakMap = new WeakMap()){
    if(target === null)return target;
    if(target instanceof Date)return new Date(target);
    if(target instanceof RegExp)return new RegExp(target);
    if(target instanceof HTMLElement)return target;
    // 处理原始类型和函数
    if(typeof target !== 'object')return target;
    /* 引用类型进行深拷贝*/
    // 需要拷贝当前对象时，先去存储空间中找，有的话直接返回
    if(weakMap.has(target))return weakMap.get(target);
    const cloneTarget = new target.constructor();
    weakMap.set(target, cloneTarget);
    /* 引入Reflect.ownKeys处理Symbol作为键名的情况，且进行赋值 */
    Reflect.ownKeys(target).forEach(key => {
        cloneTarget[key] = deepClone(target[key], qfHash);
    });
    return cloneTarget;
}
```



### 手写防抖节流

```javascript
// 防抖
function debounce(func, wait){
    let timer;
    return function(){
        clearTimeout(timer);
        timer = setTimeout(() => {
             func.apply(this, arguments);
         }, wait)
        }
}

//节流
function throttle(func, wait){
    let t1 = 0;
    return function(){
         let t2 = +new Date();
	         if(t2 - t1 > wait){
	             func.apply(this,arguments);
	             t1 = t2;
	         }
        }
}
```



### 手写call/apply/bind函数

```javascript
Function.prototype.myCall = function (context, ...args) {
  if (!context || context === null) {
    context = window;
  }
  // 创造唯一的key值  作为我们构造的context内部方法名
  let fn = Symbol();
  context[fn] = this; //this指向调用call的函数
  // 执行函数并返回结果 相当于把自身作为传入的context的方法进行调用了
  return context[fn](...args);
};

// apply原理一致  只是第二个参数是传入的数组
Function.prototype.myApply = function (context, args) {
  if (!context || context === null) {
    context = window;
  }
  // 创造唯一的key值  作为我们构造的context内部方法名
  let fn = Symbol();
  context[fn] = this;
  // 执行函数并返回结果
  return context[fn](...args);
};

//bind实现要复杂一点  因为他考虑的情况比较多 还要涉及到参数合并(类似函数柯里化)
Function.prototype.myBind = function (context, ...args) {
  if (!context || context === null) {
    context = window;
  }
  // 创造唯一的key值  作为我们构造的context内部方法名
  let fn = Symbol();
  context[fn] = this;
  let _this = this;
  //  bind情况要复杂一点
  const result = function (...innerArgs) {
    // 第一种情况 :若是将 bind 绑定之后的函数当作构造函数，通过 new 操作符使用，则不绑定传入的 this，而是将 this 指向实例化出来的对象
    // 此时由于new操作符作用  this指向result实例对象  而result又继承自传入的_this 根据原型链知识可得出以下结论
    // this.__proto__ === result.prototype   //this instanceof result =>true
    // this.__proto__.__proto__ === result.prototype.__proto__ === _this.prototype; //this instanceof _this =>true
    if (this instanceof _this === true) {
      // 此时this指向指向result的实例  这时候不需要改变this指向
      this[fn] = _this;
      this[fn](...[...args, ...innerArgs]); //这里使用es6的方法让bind支持参数合并
    } else {
      // 如果只是作为普通函数调用  那就很简单了 直接改变this指向为传入的context
      context[fn](...[...args, ...innerArgs]);
    }
  };
  // 如果绑定的是构造函数 那么需要继承构造函数原型属性和方法
  // 实现继承的方式: 使用Object.create
  result.prototype = Object.create(this.prototype);
  return result;
};

//用法如下

// function Person(name, age) {
//   console.log(name); //'我是参数传进来的name'
//   console.log(age); //'我是参数传进来的age'
//   console.log(this); //构造函数this指向实例对象
// }
// // 构造函数原型的方法
// Person.prototype.say = function() {
//   console.log(123);
// }
// let obj = {
//   objName: '我是obj传进来的name',
//   objAge: '我是obj传进来的age'
// }
// // 普通函数
// function normalFun(name, age) {
//   console.log(name);   //'我是参数传进来的name'
//   console.log(age);   //'我是参数传进来的age'
//   console.log(this); //普通函数this指向绑定bind的第一个参数 也就是例子中的obj
//   console.log(this.objName); //'我是obj传进来的name'
//   console.log(this.objAge); //'我是obj传进来的age'
// }

// 先测试作为构造函数调用
// let bindFun = Person.myBind(obj, '我是参数传进来的name')
// let a = new bindFun('我是参数传进来的age')
// a.say() //123

// 再测试作为普通函数调用
// let bindFun = normalFun.myBind(obj, '我是参数传进来的name')
//  bindFun('我是参数传进来的age')
```



### 数组扁平化

```javascript
//递归版本
function flatter(arr) {
  if (!arr.length) return;
  return arr.reduce(
    (pre, cur) =>
      Array.isArray(cur) ? [...pre, ...flatter(cur)] : [...pre, cur],
    []
  );
}
// console.log(flatter([1, 2, [1, [2, 3, [4, 5, [6]]]]]));

//迭代版本
function flatter(arr) {
  if (!arr.length) return;
  while (arr.some((item) => Array.isArray(item))) {
    arr = [].concat(...arr);
  }
  return arr;
}
// console.log(flatter([1, 2, [1, [2, 3, [4, 5, [6]]]]]));
```



### 手写柯里化

```javascript
function currying(fn, ...args) {
  const length = fn.length;
  let allArgs = [...args];
  const res = (...newArgs) => {
    allArgs = [...allArgs, ...newArgs];
    if (allArgs.length === length) {
      return fn(...allArgs);
    } else {
      return res;
    }
  };
  return res;
}

// 用法如下：
// const add = (a, b, c) => a + b + c;
// const a = currying(add, 1);
// console.log(a(2,3))
```



### 手写LRU

最近最少使用

```javascript
//  一个Map对象在迭代时会根据对象中元素的插入顺序来进行
// 新添加的元素会被插入到map的末尾，整个栈倒序查看
class LRUCache {
  constructor(capacity) {
    this.secretKey = new Map();
    this.capacity = capacity;
  }
  get(key) {
    if (this.secretKey.has(key)) {
      let tempValue = this.secretKey.get(key);
      this.secretKey.delete(key);
      this.secretKey.set(key, tempValue);
      return tempValue;
    } else return -1;
  }
  put(key, value) {
    // key存在，仅修改值
    if (this.secretKey.has(key)) {
      this.secretKey.delete(key);
      this.secretKey.set(key, value);
    }
    // key不存在，cache未满
    else if (this.secretKey.size < this.capacity) {
      this.secretKey.set(key, value);
    }
    // 添加新key，删除旧key
    else {
      this.secretKey.set(key, value);
      // 删除map的第一个元素，即为最长未使用的
      this.secretKey.delete(this.secretKey.keys().next().value);
    }
  }
}
// let cache = new LRUCache(2);
// cache.put(1, 1);
// cache.put(2, 2);
// console.log("cache.get(1)", cache.get(1))// 返回  1
// cache.put(3, 3);// 该操作会使得密钥 2 作废
// console.log("cache.get(2)", cache.get(2))// 返回 -1 (未找到)
// cache.put(4, 4);// 该操作会使得密钥 1 作废
// console.log("cache.get(1)", cache.get(1))// 返回 -1 (未找到)
// console.log("cache.get(3)", cache.get(3))// 返回  3
// console.log("cache.get(4)", cache.get(4))// 返回  4
```



### 手写一个add方法

题目描述:实现一个 add 方法 使计算结果能够满足如下预期： add(1)(2)(3)()=6 add(1,2,3)(4)()=10

```javascript
function add(...args) {
  let allArgs = [...args];
  function fn(...newArgs) {
    allArgs = [...allArgs, ...newArgs];
    return fn;
  }
  fn.toString = function () {
    if (!allArgs.length) {
      return;
    }
    return allArgs.reduce((sum, cur) => sum + cur);
  };
  return fn;
}
```



### 类数组转数组

```javascript
const arrayLike=document.querySelectorAll('div')

// 1.扩展运算符
[...arrayLike]
// 2.Array.from
Array.from(arrayLike)
// 3.Array.prototype.slice
Array.prototype.slice.call(arrayLike)
// 4.Array.apply
Array.apply(null, arrayLike)
// 5.Array.prototype.concat
Array.prototype.concat.apply([], arrayLike)
```



### 类数组使用如何使用forof

题目描述:类数组拥有 length 属性 可以使用下标来访问元素 但是不能使用数组的方法 如何把类数组转化为数组?

```javascript
const arrayLike=document.querySelectorAll('div')

// 1.扩展运算符
[...arrayLike]
// 2.Array.from
Array.from(arrayLike)
// 3.Array.prototype.slice
Array.prototype.slice.call(arrayLike)
// 4.Array.apply
Array.apply(null, arrayLike)
// 5.Array.prototype.concat
Array.prototype.concat.apply([], arrayLike)
```

### 列表转树，树转列表

```javascript
[
    {
        id: 1,
        text: '节点1',
        parentId: 0 //这里用0表示为顶级节点
    },
    {
        id: 2,
        text: '节点1_1',
        parentId: 1 //通过这个字段来确定子父级
    }
    ...
]

转成
[
    {
        id: 1,
        text: '节点1',
        parentId: 0,
        children: [
            {
                id:2,
                text: '节点1_1',
                parentId:1
            }
        ]
    }
]

//列表转树
function listToTree(data) {
  let temp = {};
  let treeData = [];
  for (let i = 0; i < data.length; i++) {
    temp[data[i].id] = data[i];
  }
  for (let i in temp) {
    if (+temp[i].parentId != 0) {
      if (!temp[temp[i].parentId].children) {
        temp[temp[i].parentId].children = [];
      }
      temp[temp[i].parentId].children.push(temp[i]);
    } else {
      treeData.push(temp[i]);
    }
  }
  return treeData;
}

//树转列表
function treeToList(data) {
  let res = [];
  const dfs = (tree) => {
    tree.forEach((item) => {
      if (item.children) {
        dfs(item.children);
        delete item.children;
      }
      res.push(item);
    });
  };
  dfs(data);
  return res;
}
```

### 冒泡排序
```javascript
//[1,3,4,2]->[1,3,2,4]->[1,2,3,4]->[1,2,3,4]
let n = 0
function bubbleSort(arr){
    for(let i = 1;i < arr.length;i++){
        for(let j = i;j > 0;j--){
            n++ // 1+2+3+...+arr.length-1
            if(arr[j] < arr[j-1]){
                [arr[j],arr[j-1]] = [arr[j-1],arr[j]];
            }
        }
    }
    return arr;
}

```

### 选择排序
```javascript
//[4,1,3,2]->[1,4,3,2]->[1,2,4,3]->[1,2,3,4]
function selectSort(arr){
    for(let i = 0;i < arr.length;i++){
        let min = Math.min(...arr.slice(i));
        let index
        for (let j = i; j < arr.length; j++) {
          if (arr[j] === min) {
            index = j
            break
          }
        }
        [arr[i],arr[index]] = [arr[index],arr[i]];
    }
    return arr;
}
```

### 快速排序
```javascript
function quickSort(arr){
    if(arr.length <= 1) return arr;
    let right = [],left = [],keys = arr.shift();
    for(let value of arr){
        if(value > keys){
            right.push(value)
        }else{
            left.push(value);
        }
    }
    return quickSort(left).concat(keys,quickSort(right));
}

//[4,1,3,2]-->quickSort([1,3,2]).concat(4,quickSort([]))
//         -->quickSort([]).concant(1,quickSort([3,2])).concat(4,quickSort([]))
//         -->quickSort([]).concant(1,quickSort([2]).concant(3)).concat(4,quickSort([]))
//         -->[1,2,3,4]
//keys=4 R[] L[1,3,2]  
-------quickSort(left)
//keys=1 R[3,2] L[]
//keys=3 R[] L[2]
//quickSort(left)=[1,2,3]

```
