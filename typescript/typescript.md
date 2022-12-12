# Typescript面试题

## 高频题目

1. typescript和javascript有什么区别，有什么优点
2. 泛型是什么？
3. interface和type的区别
4. implements和extends的区别
5. interface和class的区别
6. any和unknown的区别



## 非高频题目

1. keyof和in的作用
2. typeof的作用
3. extends的作用
4. 常用工具类型有哪些



## 高频题目答案

### typescript和javascript有什么区别，有什么优点

**优点**

1. 结合ide，有各种提示，提高代码开发效率
2. 有了对应的数据类型，类型写错时，会直接报错，提升了代码的健壮性，使代码更加规范
3. 对于重构更友好
4. 由于后端一般来说都是强类型语言进行开发的，所以当我们使用js开发，联调时很容易出现由于类型错误导致的联调问题，而ts恰恰可以规避这个问题

**缺点:**

1. 类型检查会增加编译时长

### 泛型是什么？

在定义时表明不知道是什么类型，只有在使用时才去定义对应的类型，更多的是可以当作类型占位

### interface和type的区别

**定义：**

**接口interface**：

是对一个行为的抽象，对对象整体进行描述。去描述这个接口有什么功能有哪些属性。在ts中又可以称作给值所具有的结构进行检查，类检查器会去检查用到接口的地方接口结构是否符合接口的定义

**类型别名type：**

顾名思义，是类型的另一种称号，相当于新的名字。和接口作用是一样的，也是去对对象的形状进行描述，在用到的地方对其结构进行类型检查。

### implements和extends的区别

**区别：**

1. 接口和类都能实现extends，但只有类可以进行implements
2. extends只能重写方法，不能重写属性，但implements方法、属性都可以进行重写

### interface和class的区别

在执行编译后, interface会消失, 而class依然会存在

既然interface在编译后会消失, 那么我们为什么还要使用interface?

答: interface能够为我们提供类型提示，类型检查. 比如角色信息, 可以通过定义interface Role {student: 1}, 以达到条件判断时不直接写1, 而是写Role.student, 以提高可维护性.



### any和unknown的区别

any表示任意类型，不会进行类型检查

unknown(未知类型，需要进行类型检查,使用时进行类型断言)



## 非高频题目答案

### keyof和in的作用

keyof：获取T类型中所有属性键，这些属性键组合成一个联合类型

in：迭代一个联合类型，常用来和keyof一起使用

### typeof的作用

主要用来做类型推断

```typescript
const add = (a: number, b: number): number => {
  return a + b
}

const obj = {
  name: 'AAA',
  age: 23
}

const t1 = typeof add;  //function
const t2 = typeof obj;  //object
type t3 = typeof add;//(a: number, b:number)=>number
```

### extends的作用

**作用**

1. 类型约束
2. 条件类型
3. 类型继承

**类型约束**

// 类型约束

U extends keyof T

keyof T是一个整体，它表示一个联合类型。U extends Union这一整段表示U的类型被收缩在一个联合类型的范围内。

经常和泛型一起使用

**条件类型**

判断类型

常见的条件类型表现形式如下：

T extends U ? 'Y' : 'N'

我们发现条件类型有点像JavaScript中的三元表达式，事实上它们的工作原理是类似的

type res1 = true extends boolean ? true : false         // true

type res2 = 'name' extends 'name'|'age' ? true : false      // true



### 常用工具类型有哪些

**Pick:**Pick挑选对象中的属性组成新的类型

**Omit:**移除的意思，它用来在T类型中移除指定的属性

**Exclude:**从T类型中排除属于U类型的子集，可以理解成取T对于U的差集

**Required:**将属性设置为必填项

**Partial:**将属性设置为可选项

**Extract：**Extract<T, U>用来取联合类型T和U的交集，

**Readonly：**将类型内部键都变为只读
