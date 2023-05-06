# 一文理解JavaScript中的函数式编程的概念

> 最近在看 [《JavaScript 函数式编程实践指南》](https://juejin.cn/book/7173591403639865377)，里面把函数式编程讲的很详细，本文算是学习笔记，也算是转换成自己的想法后的输出。

我理解的 `函数式编程` 是一种 `编程规范`，也是一种对语言程序（比如JavaScript）本身能力的 `运用方式`。

就 `编程规范` 来说，对开发者来说可以说是一种约束，但在这种约束下，会让我们编写出可控且稳定的程序。

就 `运用方式` 来说，函数式编程本身有很多经典的运用方式，对于 `JavaScript` 语言本身而言，天然支持了 `函数式编程`，因此，我们可以直接它的一些运用方式，来扩展我们的语言运用能力。

本篇文章将从 `编程规范` 来介绍函数式编程的到底是一种什么样的规范，它能带给我们作用有什么。

## 分析

函数式编程核心规范就是：拥抱 `纯函数` ，隔离 `副作用` 。

因此要弄清楚 `函数式编程` 的规范，就需要弄懂 `纯函数` 和 `副作用` 概念和作用。

我们先来看一下各自的概念：

`纯函数`：就是一个函数，但它需要满足以下两个特征：

1. 相同的输入参数，总会有相同的输出
2. 在执行过程中不会产生语义上可观察的 `副作用`

`副作用`：函数 `副作用` 指当调用函数时，除了返回函数值之外，还对主调用函数产生附加的影响。

纯函数的理解依赖 `副作用`，因此不会分开去理解，本篇具体内容结构：

- 理解纯函数
- 纯函数的作用
- 如何对待副作用

## 理解纯函数

下面我们从 `纯函数` 的两个特征来理解。

### 相同的输入参数，总会有相同的输出

比如：

```js
function add(a, b) {
    return a + b
}

add(1, 2); // 3
```

执行 `add` 函数，不管执行多少次，不管在 JavaScript 上下文哪里执行，相同的参数传递进去后，总会返回相同的输出，且不会对外界造成影响，是一个 `纯函数` 。

如果改成这样：

```js
let c = 1;

function add(a, b) {
    return a + b + c;
}

add(1, 2); // 4
c = 5;
add(1, 2); // 8
```

这时候 `add` 调用了两次，传递进去的参数没有变化，但返回结果却不相同，它受到了外界变量的干扰，不属于纯函数。

因此，可以推断出要想 `相同的输入参数，总会有相同的输出` ，函数内部不能使用函数外部的 `变量` ，但不可变的 `常量` 是可以的，比如在使用 `redux`时，定义 `reducer` 的 action 常常就会使用常量来定义。

`相同的输入参数，总会有相同的输出` 可以保证我们程序的稳定性，返回结果不受外界影响。

### 在执行过程中不会产生语义上可观察的 `副作用`

比如：

```js
function add(a, b) {
    const result = a + b;
    console.log(result);
    return result;
}
add(1, 2); // 3
```

上面的 `add` 函数添加了 `console.log` 把结果打印到了浏览器的控制台，属于对外界造成了影响，产生了 `副作用`。

再比如一个请求接口的函数：

```js
async function getList(url) {
    const result = await fetch(url);
    return result;
}
```

内部调用了请求函数 `fetch` ，`fetch` 函数执行后返回的内容并不能保证每一次都一样，受到了网络和服务器等原因的影响。因此，`fetch` 函数本身就不是一个纯函数，`getList` 受到其影响，也变得不纯了。

因此，如果一个主函数内部调用了不纯函数，不纯的原因还是对主函数外部产生了副作用或者收到了影响，那么这个函数就不是纯函数。

## 纯函数 的作用

`纯函数` 的两大特征，合起来可以这样看：不受外界影响，不影响外界。也就是它可以解决程序中 `不确定性` 的问题。

### 不确定性的危害

以测试过程为例：单元测试的主要判断的依据就是函数的输入和输出。如果对于同样的输入，函数不能够给到确定的输出，那就很难进行测试了。

不确定性还会导致我们的程序中出现各种风险，比如：

- 代码难以被调试
- 数据变化难以被追溯
- 计算结果难以被复用（代码难以封装）
- 程序运行中容易出现各种突发性事件

### 确定性的好处

确定性的函数让我们的程序更加有保障，也能解决很多实际性的问题，比如：

- 代码逻辑更加清晰。
- 更容易进行测试，结果只依赖输入，测试时可以确保输出稳定。
- 更容易维护和重构，我们可以写出质量更高的代码。
- 更容易调用，我们不用担心函数会有什么副作用。
- 结果可以缓存，因为相同的输入总是会得到相同的输出。
- 代码复用性强，提升开发者的编码效率。

## 如何对待副作用

解决 `副作用` 等于解决了影响外界的问题和部分不受外界影响的问题。因此消除副作用，足以解决函数中大多数的不确定性问题。

但对于一个完整的程序来说，副作用却至关重要，没有副作用，程序就不能和外界沟通，就不能产生对外界的作用，这样程序本身就没有了意义和价值。

因此我们要做的不是消除副作用，而是拥抱 `纯函数` ，隔离 `副作用` 。将计算逻辑与副作用做合理的分层解耦，从而提升我们的编码质量和执行效率。

## 总结

函数式编程带来了一种编程规范，而核心就是拥抱 `纯函数`，让我们更加注重代码本身的质量和运用能力。

本篇主要用于理解函数式编程的意义，并没有去讲解它的一些故事和运用方式，有问题的地方欢迎大家提出，感谢阅读🙏。

参考：

- [JavaScript 函数式编程实践指南](https://juejin.cn/book/7173591403639865377)
- [纯函数是什么？怎么合理运用纯函数？](https://segmentfault.com/a/1190000039807327)



