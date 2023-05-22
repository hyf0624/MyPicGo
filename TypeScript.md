# 1.基本类型

TypeScript 支持与 JavaScript 几乎相同的数据类型，此外还提供了实用的枚举类型方便我们使用。



- 类型声明

  - 类型声明是TS非常重要的一个特点

  - 通过类型声明可以指定TS中变量 （参数、形参）的类型

  - 指定类型后，当为变量赋值时，TS编译会自动检查值是否符合类型声明，符合则赋值，否则报错

  - 简而言之，类型声明给变量设置了类型，使得变量只能存储某种类型的值。

  - 语法：

    ```js
    let 变量: 类型 = 值;
    function fn(参数: 类型, 参数: 类型): 类型{
        ...
    }
    ```

- 自动判断类型：

  - TS拥有自动的类型判断机制
  - 当对变量的声明和赋值是同时进行的，TS编译器会自动判断变量的类型
  - 所有如果你的变量的声明和赋值时同时进行时，可以省略掉类型的声明

- 类型：

  |  类型   |      例子       |
  | :-----: | :-------------: |
  | number  |   1, -33, 2.5   |
  | string  |      'abc'      |
  | boolean |   true, false   |
  |   any   |        *        |
  | unknown |        *        |
  |  void   | 空值(undefined) |
  |  never  |     没有值      |
  | object  |    对象类型     |
  |  array  |     [1,2,3]     |
  |  tuple  |      [4,5]      |
  |  enum   |    enum{A,B}    |
  |         |                 |

  

# 2.ts当中的类

在ts中的类和js中的类几乎一样，没什么区别

```js
class Person {
  /*
  这里的name和age 属性都是只能在实例对象上的属性
  skill属性是属于类身上的静态属性
  */
  name: string = '孙悟空'
  age: number = 16
  static skill: string = 'killing'
  constructor(name: string) {
    this.name = name
  }
  fn() {
    console.log(this.name)
  }
}

const person = new Person('方明旺')
console.log(person)
console.log(Person.skill)
person.fn()

```



# 3.ts中的接口

```js
/**
 *  接口用来定义一个类结构，用来定义一个类（对象）中应该包含哪些属性和方法，
 * 同时接口也可以当成类型声明去使用, 接口中的所有属性都不能有实际值，
 */
interface myInterFace {
  name: string
  age: number
}

const obj: myInterFace = {
  name: '孙悟空',
  age: 500
}

interface myInter {
  name: string
  sayHello(): void
}
/**
 * 定义类时，可以使类去实现一个接口，
 *  实现接口就是使类满足接口的要求。
 */
class MyClass implements myInter {
  name: string
  constructor(name: string) {
    this.name = name
  }
  sayHello(): void {
    console.log('方明旺傻逼')
  }
}
```





# 4.属性的封装

```js
;(function () {
  // 定义一个表示人的类
  class Person {
    // plubic修饰的属性可以在任意位置修改(默认值)
    public _name: string // 公共的

    /**
     * private 私有属性只能在类的内部进行访问修改
     *  - 通过在类中添加方法使得私有属性可以被外部访问
     */
    private _age: number // 私有属性
    constructor(name: string, age: number) {
      this._name = name
      this._age = age
    }
    get age() {
      return this._age
    }

    set age(value: number) {
      this._age = value
    }
  }

  const person = new Person('孙悟空', 18)
  console.log(person)

  /**
   * 现在属性是直接在对象中设置，属性可以任意的被修改
   *  属性可以被任意修改将会导致对象中的数据非常不安全。
   */
  person._name = '猪八戒'
  person.age = 18
  console.log(person)

  class A {
    /**
     *  protected 受保护的属性，只能在当前类和当前类的子类中使用(修改)
     */
    protected num: number
    constructor(num: number) {
      this.num = num
    }
  }
  class B extends A {
    test() {
      console.log(this.num)
    }
  }
  const b = new B(132)
  // b.num = 122 // 这种写法是错误的, num 只能在类的内部修改

  class C {
    // 可以直接将属性写在构造函数中。
    constructor(public name: string, public age: number) {}
  }

  const c = new C('123', 121)
  console.log(c)
})()

```



# 5.泛型

```js
/**
 *
 * 在定义函数或是类时，如果遇到类型不明确就可以使用泛型
 */
function fn<T>(a: T): T {
  return a
}

// 可以直接调用具有泛型的函数
fn(10) // 不指定泛型，TS可以自动对类型进行推断

//
fn<string>('hello') // 指定泛型

function fn2<T, K>(a: T, b: K): T {
  console.log(b)
  return a
}

fn2<number, string>(123, 'hello')

interface Inter {
  length: number
}

// 表示 泛型T 必须是Inter实现类,必须有length属性
function fn3<T extends Inter>(a: T): number {
  return a.length
}
fn3({ length: 123123 })
fn3('123')

class MyClass<T> {
  name: T
  constructor(name: T) {
    this.name = name
  }
} 

const mc = new MyClass<string>('孙悟空')

```



# 6.更多详细文档

https://24kcs.github.io/vue3_study







