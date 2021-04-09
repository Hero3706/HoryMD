# 第五部分：Vue

##  1、你知道Vue响应式数据原理吗？Proxy 与 Object.defineProperty 优劣对比

**一、Proxy的优势如下:**

1.Proxy可以直接监听对象⽽⾮属性 。

2.Proxy可以直接监听数组的变化 。

3.Proxy有多达13种拦截⽅法,不限于apply、ownKeys、deleteProperty、has等等是 Object.defineProperty 不具备的 。

4.Proxy返回的是⼀个新对象,我们可以只操作新的对象达到⽬的,⽽ Object.defineProperty 只能遍历对象属性直接修改。

5.Proxy作为新标准将受到浏览器⼚商重点持续的性能优化，也就是传说中的新标准的性能红利 。

**二、Object.defineProperty的优势如下:**

1.兼容性好,⽀持IE9。

## 2、Vue2.x组件通信有哪些方式

常见使用场景可以分为三类：

- 父子通信： 父向子传递数据是通过 props，子向父是通过 events（ `$emit`）；通过父链 / 子链也可以通信（ `$parent` / `$children`）；ref 也可以访问组件实例；provide / inject API； `$attrs/$listeners`
- 兄弟通信： Bus；Vuex
- 跨级通信： Bus；Vuex；provide / inject API、 `$attrs/$listeners`

## 3、Vue 中的 computed 和 watch 的区别在哪里

**计算属性computed :** 

\1. 支持缓存，只有依赖数据发生改变，才会重新进行计算

\2. 不支持异步，当computed内有异步操作时无效，无法监听数据的变化

3.computed 属性值会默认走缓存，计算属性是基于它们的响应式依赖进行缓存的，也就是基于data中声明过或者父组件传递的props中的数据通过计算得到的值

\4. 如果一个属性是由其他属性计算而来的，这个属性依赖其他属性，是一个多对一或者一对一，一般用computed

5.如果computed属性属性值是函数，那么默认会走get方法；函数的返回值就是属性的属性值；在computed中的，属性都有一个get和一个set方法，当数据变化时，调用set方法。

![img](https://img2018.cnblogs.com/blog/1402448/201908/1402448-20190809154932198-1444047098.png)

**侦听属性watch：**

\1. 不支持缓存，数据变，直接会触发相应的操作；

2.watch支持异步；

3.监听的函数接收两个参数，第一个参数是最新的值；第二个参数是输入之前的值；

\4. 当一个属性发生变化时，需要执行对应的操作；一对多；

\5. 监听数据必须是data中声明过或者父组件传递过来的props中的数据，当数据变化时，触发其他操作，函数有两个参数，

　　immediate：组件加载立即触发回调函数执行，

　　deep: 深度监听，为了发现**对象内部值**的变化，复杂类型的数据时使用，例如数组中的对象内容的改变，注意监听数组的变动不需要这么做。注意：deep无法监听到数组的变动和对象的新增，参考vue数组变异,只有以响应式的方式触发才会被监听到。

![img](https://img2018.cnblogs.com/blog/1402448/201908/1402448-20190809160441362-1201017336.png)

监听的对象也可以写成字符串的形式

![img](https://img2018.cnblogs.com/blog/1402448/201908/1402448-20190809160648619-505189772.png)

## 4、组件中的data为什么是一个函数

　如果两个实例引用同一个对象，当其中一个实例的属性发生改变时，另一个实例属性也随之改变，只有当两个实例拥有自己的作用域时，才不会相互干扰。

　　这是因为JavaScript的特性所导致，在component中，data必须以函数的形式存在，不可以是对象。

　　组建中的data写成一个函数，数据以函数返回值的形式定义，这样每次复用组件的时候，都会返回一份新的data，相当于每个组件实例都有自己私有的数据空间，它们只负责各自维护的数据，不会造成混乱。而单纯的写成对象形式，就是所有的组件实例共用了一个data，这样改一个全都改了。

## 5、nextTick的实现原理是什么

官方文档对 `nextTick` 的功能如是说明：

在下次 `DOM` 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 `DOM`。

```
// 修改数据
vm.msg = 'Hello'
// DOM 还没有更新
Vue.nextTick(function () {
  // DOM 更新了
})

// 作为一个 Promise 使用 (2.1.0 起新增，详见接下来的提示)
Vue.nextTick()
  .then(function () {
    // DOM 更新了
  })
```

> 2.1.0 起新增：如果没有提供回调且在支持 Promise 的环境中，则返回一个 Promise。请注意 Vue 不自带 Promise 的 polyfill，所以如果你的目标浏览器不原生支持 Promise (IE：你们都看我干嘛)，你得自己提供 polyfill。

从上面的官方介绍中可以看到，`nextTick` 的主要功能就是更新数据后让回调函数作用于更新后的`DOM` 。看到这句话，你可能第一反应是：呸！说了等于没说，还是不理解。那么请看下面这个例子：

```
<template>
<div id="example">{{message}}</div>
</template>
<script>
var vm = new Vue({
  el: '#example',
  data: {
    message: '123'
  }
})
vm.message = 'new message' // 更改数据
console.log(vm.$el.innerHTML) // '123'
Vue.nextTick(function () {
  console.log(vm.$el.innerHTML) // 'new message'
})
</script>
```

在上面例子中，当我们更新了`message`的数据后，立即获取`vm.$el.innerHTML`，发现此时获取到的还是更新之前的数据：123。但是当我们使用`nextTick`来获取`vm.$el.innerHTML`时，此时就可以获取到更新后的数据了。这是为什么呢？

这里就涉及到`Vue`中对`DOM`的更新策略了，`Vue` 在更新 `DOM` 时是**异步**执行的。只要侦听到数据变化，`Vue` 将开启一个事件队列，并缓冲在同一事件循环中发生的所有数据变更。如果同一个 `watcher` 被多次触发，只会被推入到事件队列中一次。这种在缓冲时去除重复数据对于避免不必要的计算和 `DOM` 操作是非常重要的。然后，在下一个的事件循环“tick”中，`Vue` 刷新事件队列并执行实际 (已去重的) 工作。

在上面这个例子中，当我们通过 `vm.message = ‘new message‘`更新数据时，此时该组件不会立即重新渲染。当刷新事件队列时，组件会在下一个事件循环“tick”中重新渲染。所以当我们更新完数据后，此时又想基于更新后的 `DOM` 状态来做点什么，此时我们就需要使用`Vue.nextTick(callback)`，把基于更新后的`DOM` 状态所需要的操作放入回调函数`callback`中，这样回调函数将在 `DOM` 更新完成后被调用。

OK，现在大家应该对`nextTick`是什么、为什么要有`nextTick`以及怎么使用`nextTick`有个大概的了解了。那么问题又来了，`Vue`为什么要这么设计？为什么要异步更新`DOM`？这就涉及到另外一个知识：`JS`的运行机制。

3. 前置知识：JS的运行机制

我们知道 `JS` 执行是单线程的，它是基于事件循环的。事件循环大致分为以下几个步骤：

1. 所有同步任务都在主线程上执行，形成一个执行栈（`execution context stack`）。
2. 主线程之外，还存在一个"任务队列"（`task queue`）。只要异步任务有了运行结果，就在"任务队列"之中放置一个事件。
3. 一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"，看看里面有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。
4. 主线程不断重复上面的第三步。

![技术分享图片](http://image.bubuko.com/info/201909/20190929233900677000.png)

主线程的执行过程就是一个 `tick`，而所有的异步结果都是通过 “任务队列” 来调度。 消息队列中存放的是一个个的任务（`task`）。 规范中规定 `task` 分为两大类，分别是宏任务(`macro task`) 和微任务(`micro task`），并且每执行完一个个宏任务(`macro task`)后，都要去清空该宏任务所对应的微任务队列中所有的微任务(`micro task`），他们的执行顺序如下所示：

```
for (macroTask of macroTaskQueue) {
    // 1. 处理当前的宏任务
    handleMacroTask();
      
    // 2. 处理对应的所有微任务
    for (microTask of microTaskQueue) {
        handleMicroTask(microTask);
    }
}
```

在浏览器环境中，常见的

- 宏任务(`macro task`) 有 `setTimeout`、`MessageChannel`、`postMessage`、`setImmediate`；
- 微任务(`micro task`）有`MutationObsever` 和 `Promise.then`。

OK，有了这个概念之后，接下来我们就进入本篇文章的正菜：从`Vue`源码角度来分析`nextTick`的实现原理。

4. nextTick源码分析

`nextTick` 的源码位于`src/core/util/next-tick.js`，总计118行。

`nextTick`源码主要分为两块：

1. 能力检测
2. 根据能力检测以不同方式执行回调队列

4.1 能力检测

`Vue` 在内部对异步队列尝试使用原生的 `Promise.then`、`MutationObserver` 和 `setImmediate`，如果执行环境不支持，则会采用 `setTimeout(fn, 0)` 代替。

宏任务耗费的时间是大于微任务的，所以在浏览器支持的情况下，优先使用微任务。如果浏览器不支持微任务，使用宏任务；但是，各种宏任务之间也有效率的不同，需要根据浏览器的支持情况，使用不同的宏任务。

这一部分的源码如下：

```
let microTimerFunc
let macroTimerFunc
let useMacroTask = false

/* 对于宏任务(macro task) */
// 检测是否支持原生 setImmediate(高版本 IE 和 Edge 支持)
if (typeof setImmediate !== 'undefined' && isNative(setImmediate)) {
  macroTimerFunc = () => {
    setImmediate(flushCallbacks)
  }
} 
// 检测是否支持原生的 MessageChannel
else if (typeof MessageChannel !== 'undefined' && (
  isNative(MessageChannel) ||
  // PhantomJS
  MessageChannel.toString() === '[object MessageChannelConstructor]'
)) {
  const channel = new MessageChannel()
  const port = channel.port2
  channel.port1.onmessage = flushCallbacks
  macroTimerFunc = () => {
    port.postMessage(1)
  }
} 
// 都不支持的情况下，使用setTimeout
else {
  macroTimerFunc = () => {
    setTimeout(flushCallbacks, 0)
  }
}

/* 对于微任务(micro task) */
// 检测浏览器是否原生支持 Promise
if (typeof Promise !== 'undefined' && isNative(Promise)) {
  const p = Promise.resolve()
  microTimerFunc = () => {
    p.then(flushCallbacks)
  }
} 
// 不支持的话直接指向 macro task 的实现。
else {
  // fallback to macro
  microTimerFunc = macroTimerFunc
}
```

首先声明了两个变量： `microTimerFunc` 和 `macroTimerFunc` ，它们分别对应的是 `micro task` 的函数和 `macro task` 的函数。对于 `macro task` 的实现，优先检测是否支持原生 `setImmediate`，这是一个高版本 `IE` 和`Edge` 才支持的特性，不支持的话再去检测是否支持原生的 `MessageChannel`，如果也不支持的话就会降级为 `setTimeout 0`；而对于 `micro task` 的实现，则检测浏览器是否原生支持 `Promise`，不支持的话直接指向 `macro task` 的实现。

4.2 执行回调队列

接下来就进入了核心函数`nextTick`中，如下：

```
const callbacks = []   // 回调队列
let pending = false    // 异步锁

// 执行队列中的每一个回调
function flushCallbacks () {
  pending = false     // 重置异步锁
  // 防止出现nextTick中包含nextTick时出现问题，在执行回调函数队列前，提前复制备份并清空回调函数队列
  const copies = callbacks.slice(0)
  callbacks.length = 0
  // 执行回调函数队列
  for (let i = 0; i < copies.length; i++) {
    copies[i]()
  }
}

export function nextTick (cb?: Function, ctx?: Object) {
  let _resolve
  // 将回调函数推入回调队列
  callbacks.push(() => {
    if (cb) {
      try {
        cb.call(ctx)
      } catch (e) {
        handleError(e, ctx, 'nextTick')
      }
    } else if (_resolve) {
      _resolve(ctx)
    }
  })
  // 如果异步锁未锁上，锁上异步锁，调用异步函数，准备等同步函数执行完后，就开始执行回调函数队列
  if (!pending) {
    pending = true
    if (useMacroTask) {
      macroTimerFunc()
    } else {
      microTimerFunc()
    }
  }
  // 如果没有提供回调，并且支持Promise，返回一个Promise
  if (!cb && typeof Promise !== 'undefined') {
    return new Promise(resolve => {
      _resolve = resolve
    })
  }
}
```

首先，先来看 `nextTick`函数，该函数的主要逻辑是：先把传入的回调函数 `cb` 推入 回调队列`callbacks` 数组，同时在接收第一个回调函数时，执行能力检测中对应的异步方法（异步方法中调用了回调函数队列）。最后一次性地根据 `useMacroTask` 条件执行 `macroTimerFunc` 或者是 `microTimerFunc`，而它们都会在下一个 tick 执行 `flushCallbacks`，`flushCallbacks` 的逻辑非常简单，对 `callbacks` 遍历，然后执行相应的回调函数。

`nextTick` 函数最后还有一段逻辑：

```
 if (!cb && typeof Promise !== 'undefined') {
  return new Promise(resolve => {
    _resolve = resolve
  })
}
```

这是当 `nextTick` 不传 `cb` 参数的时候，提供一个 Promise 化的调用，比如：

```
nextTick().then(() => {})
```

当 `_resolve` 函数执行，就会跳到 `then` 的逻辑中。

这里有两个问题需要注意：

1. 如何保证只在接收第一个回调函数时执行异步方法？

   `nextTick`源码中使用了一个异步锁的概念，即接收第一个回调函数时，先关上锁，执行异步方法。此时，浏览器处于等待执行完同步代码就执行异步代码的情况。

2. 执行 `flushCallbacks` 函数时为什么需要备份回调函数队列？执行的也是备份的回调函数队列？

   因为，会出现这么一种情况：`nextTick` 的回调函数中还使用 `nextTick`。如果 `flushCallbacks` 不做特殊处理，直接循环执行回调函数，会导致里面`nextTick` 中的回调函数会进入回调队列。

5. 总结

以上就是对 `nextTick` 的源码分析，我们了解到数据的变化到 `DOM` 的重新渲染是一个异步过程，发生在下一个 tick。当我们在实际开发中，比如从服务端接口去获取数据的时候，数据做了修改，如果我们的某些方法去依赖了数据修改后的 DOM 变化，我们就必须在 `nextTick` 后执行。如下：



```
getData(res).then(()=>{
  this.xxx = res.data
  this.$nextTick(() => {
    // 这里我们可以获取变化后的 DOM
  })
})
```

## 6、说说你对keep-alive组件的了解

一、Keep-alive 是什么

keep-alive是vue中的内置组件，能在组件切换过程中将状态保留在内存中，防止重复渲染DOM

keep-alive 包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们

keep-alive可以设置以下props属性：

- include - 字符串或正则表达式。只有名称匹配的组件会被缓存
- exclude - 字符串或正则表达式。任何名称匹配的组件都不会被缓存
- max - 数字。最多可以缓存多少组件实例

**关于keep-alive的基本用法：**

```javascript
<keep-alive>
  <component :is="view"></component>
</keep-alive>
123
```

**使用includes和exclude：**

```javascript
<keep-alive include="a,b">
  <component :is="view"></component>
</keep-alive>

<!-- 正则表达式 (使用 `v-bind`) -->
<keep-alive :include="/a|b/">
  <component :is="view"></component>
</keep-alive>

<!-- 数组 (使用 `v-bind`) -->
<keep-alive :include="['a', 'b']">
  <component :is="view"></component>
</keep-alive>
12345678910111213
```

匹配首先检查组件自身的 name 选项，如果 name 选项不可用，则匹配它的局部注册名称 (父组件 components 选项的键值)，匿名组件不能被匹配

设置了 keep-alive 缓存的组件，会多出两个生命周期钩子（activated与deactivated）：

- 首次进入组件时：beforeRouteEnter > beforeCreate > created> mounted > activated > … … > beforeRouteLeave > deactivated
- 再次进入组件时：beforeRouteEnter >activated > … … > beforeRouteLeave > deactivated

二、使用场景

使用原则：当我们在某些场景下不需要让页面重新加载时我们可以使用keepalive

举个栗子:

当我们从首页–>列表页–>商详页–>再返回，这时候列表页应该是需要keep-alive

从首页–>列表页–>商详页–>返回到列表页(需要缓存)–>返回到首页(需要缓存)–>再次进入列表页(不需要缓存)，这时候可以按需来控制页面的keep-alive

**在路由中设置keepAlive属性判断是否需要缓存**

```javascript
{
  path: 'list',
  name: 'itemList', // 列表页
  component (resolve) {
    require(['@/pages/item/list'], resolve)
 },
 meta: {
  keepAlive: true,
  title: '列表页'
 }
}
1234567891011
```

使用<keep-alive>

```javascript
<div id="app" class='wrapper'>
    <keep-alive>
        <!-- 需要缓存的视图组件 --> 
        <router-view v-if="$route.meta.keepAlive"></router-view>
     </keep-alive>
      <!-- 不需要缓存的视图组件 -->
     <router-view v-if="!$route.meta.keepAlive"></router-view>
</div>
12345678
```

三、原理分析

keep-alive是vue中内置的一个组件

源码位置：src/core/components/keep-alive.js

```javascript
export default {
  name: 'keep-alive',
  abstract: true,

  props: {
    include: [String, RegExp, Array],
    exclude: [String, RegExp, Array],
    max: [String, Number]
  },

  created () {
    this.cache = Object.create(null)
    this.keys = []
  },

  destroyed () {
    for (const key in this.cache) {
      pruneCacheEntry(this.cache, key, this.keys)
    }
  },

  mounted () {
    this.$watch('include', val => {
      pruneCache(this, name => matches(val, name))
    })
    this.$watch('exclude', val => {
      pruneCache(this, name => !matches(val, name))
    })
  },

  render() {
    /* 获取默认插槽中的第一个组件节点 */
    const slot = this.$slots.default
    const vnode = getFirstComponentChild(slot)
    /* 获取该组件节点的componentOptions */
    const componentOptions = vnode && vnode.componentOptions

    if (componentOptions) {
      /* 获取该组件节点的名称，优先获取组件的name字段，如果name不存在则获取组件的tag */
      const name = getComponentName(componentOptions)

      const { include, exclude } = this
      /* 如果name不在inlcude中或者存在于exlude中则表示不缓存，直接返回vnode */
      if (
        (include && (!name || !matches(include, name))) ||
        // excluded
        (exclude && name && matches(exclude, name))
      ) {
        return vnode
      }

      const { cache, keys } = this
      /* 获取组件的key值 */
      const key = vnode.key == null
        // same constructor may get registered as different local components
        // so cid alone is not enough (#3269)
        ? componentOptions.Ctor.cid + (componentOptions.tag ? `::${componentOptions.tag}` : '')
        : vnode.key
     /*  拿到key值后去this.cache对象中去寻找是否有该值，如果有则表示该组件有缓存，即命中缓存 */
      if (cache[key]) {
        vnode.componentInstance = cache[key].componentInstance
        // make current key freshest
        remove(keys, key)
        keys.push(key)
      }
        /* 如果没有命中缓存，则将其设置进缓存 */
        else {
        cache[key] = vnode
        keys.push(key)
        // prune oldest entry
        /* 如果配置了max并且缓存的长度超过了this.max，则从缓存中删除第一个 */
        if (this.max && keys.length > parseInt(this.max)) {
          pruneCacheEntry(cache, keys[0], keys, this._vnode)
        }
      }

      vnode.data.keepAlive = true
    }
    return vnode || (slot && slot[0])
  }
}
123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657585960616263646566676869707172737475767778798081
```

可以看到该组件没有template，而是用了render，在组件渲染的时候会自动执行render函数

this.cache是一个对象，用来存储需要缓存的组件，它将以如下形式存储：

```javascript
this.cache = {
    'key1':'组件1',
    'key2':'组件2',
    // ...
}
12345
```

在组件销毁的时候执行pruneCacheEntry函数

```javascript
function pruneCacheEntry (
  cache: VNodeCache,
  key: string,
  keys: Array<string>,
  current?: VNode
) {
  const cached = cache[key]
  /* 判断当前没有处于被渲染状态的组件，将其销毁*/
  if (cached && (!current || cached.tag !== current.tag)) {
    cached.componentInstance.$destroy()
  }
  cache[key] = null
  remove(keys, key)
}
1234567891011121314
```

在mounted钩子函数中观测 include 和 exclude 的变化，如下：

```javascript
mounted () {
    this.$watch('include', val => {
        pruneCache(this, name => matches(val, name))
    })
    this.$watch('exclude', val => {
        pruneCache(this, name => !matches(val, name))
    })
}
12345678
```

如果include 或exclude 发生了变化，即表示定义需要缓存的组件的规则或者不需要缓存的组件的规则发生了变化，那么就执行pruneCache函数，函数如下：

```javascript
function pruneCache (keepAliveInstance, filter) {
  const { cache, keys, _vnode } = keepAliveInstance
  for (const key in cache) {
    const cachedNode = cache[key]
    if (cachedNode) {
      const name = getComponentName(cachedNode.componentOptions)
      if (name && !filter(name)) {
        pruneCacheEntry(cache, key, keys, _vnode)
      }
    }
  }
}
123456789101112
```

在该函数内对this.cache对象进行遍历，取出每一项的name值，用其与新的缓存规则进行匹配，如果匹配不上，则表示在新的缓存规则下该组件已经不需要被缓存，则调用pruneCacheEntry函数将其从this.cache对象剔除即可

关于keep-alive的最强大缓存功能是在render函数中实现

首先获取组件的key值：

```javascript
const key = vnode.key == null? 
componentOptions.Ctor.cid + (componentOptions.tag ? `::${componentOptions.tag}` : '')
: vnode.key
123
```

拿到key值后去this.cache对象中去寻找是否有该值，如果有则表示该组件有缓存，即命中缓存，如下：

```javascript
/* 如果命中缓存，则直接从缓存中拿 vnode 的组件实例 */
if (cache[key]) {
    vnode.componentInstance = cache[key].componentInstance
    /* 调整该组件key的顺序，将其从原来的地方删掉并重新放在最后一个 */
    remove(keys, key)
    keys.push(key)
} 
1234567
直接从缓存中拿 vnode 的组件实例，此时重新调整该组件key的顺序，将其从原来的地方删掉并重新放在this.keys中最后一个

this.cache对象中没有该key值的情况，如下：
123
/* 如果没有命中缓存，则将其设置进缓存 */
else {
    cache[key] = vnode
    keys.push(key)
    /* 如果配置了max并且缓存的长度超过了this.max，则从缓存中删除第一个 */
    if (this.max && keys.length > parseInt(this.max)) {
        pruneCacheEntry(cache, keys[0], keys, this._vnode)
    }
}
123456789
```

表明该组件还没有被缓存过，则以该组件的key为键，组件vnode为值，将其存入this.cache中，并且把key存入this.keys中

此时再判断this.keys中缓存组件的数量是否超过了设置的最大缓存数量值this.max，如果超过了，则把第一个缓存组件删掉

四、思考题：缓存后如何获取数据

解决方案可以有以下两种：

- beforeRouteEnter
- actived

**beforeRouteEnter**
每次组件渲染的时候，都会执行beforeRouteEnter

```javascript
beforeRouteEnter(to, from, next){
    next(vm=>{
        console.log(vm)
        // 每次进入路由执行
        vm.getData()  // 获取数据
    })
}
1234567
```

**actived**
在keep-alive缓存的组件被激活的时候，都会执行actived钩子

```javascript
activated(){
   this.getData() // 获取数据
},
```

## 7、你都做过哪些Vue的性能优化

- 尽量减少`data`中的数据，`data`中的数据都会增加`getter`和`setter`，会收集对应的`watcher`
- `v-if`和`v-for`不能连用
- 如果需要使用`v-for`给每项元素绑定事件时使用事件代理
- `SPA` 页面采用`keep-alive`缓存组件
- 在更多的情况下，使用`v-if`替代`v-show`
- `key`保证唯一
- 使用路由懒加载、异步组件
- 防抖、节流
- 第三方模块按需导入
- 长列表滚动到可视区域动态加载
- 图片懒加载

`SEO`优化

- 预渲染
- 服务端渲染`SSR`

打包优化

- 压缩代码
- `Tree Shaking/Scope Hoisting`
- 使用`cdn`加载第三方模块
- 多线程打包`happypack`
- `splitChunks`抽离公共文件
- `sourceMap`优化

用户体验

- 骨架屏
- `PWA`

## 8、vue3.0介绍

2020年9月19日凌晨，尤雨溪大大正式发布了 Vue.js 3.0 版本，代号：One Piece。此框架新的主要版本提供了更好的性能、更小的捆绑包体积、更好的 TypeScript 集成、用于处理大规模用例的新 API，并为框架未来的长期迭代奠定了坚实的基础。

3.0 版本的开发周期长达两年多，期间产生了 30+ RFCs、2600+ commits、628 pull requests，以及核心仓库之外的大量开发和文档工作。

Vue 3.0 的发布标志着此框架整体上已处于可用状态。尽管框架的某些子项目可能仍需要进一步的开发才能达到稳定状态（特别是 devtools 中的路由和 Vuex 集成），不过现在仍然是开始使用 Vue 3 启动新项目的合适时机。官方还鼓励库作者现在可以开始升级项目以支持 Vue 3。



 

## 9、Composition API 的出现带来哪些新的开发体验，为啥需要这个？

Composition API是Vue的下一个主要版本中最常用的讨论和特色语法。这是一种全新的逻辑重用和代码组织方法

当前，我们使用所谓的Options API构建组件。为了向Vue组件添加逻辑，我们填充（选项）属性，例如数据，方法，计算的等。这种方法的最大缺点是，这本身并不是有效的JavaScript代码。您需要确切了解模板中可以访问哪些属性，以及此关键字的行为。在后台，Vue编译器需要将此属性转换为工作代码。因此，我们无法从自动建议或类型检查中受益。

Composition API的目的是通过将当前可用组件属性作为JavaScript函数暴露出来的机制来解决这个问题。Vue核心团队将Composition API描述为“一组基于功能的附加API，可以灵活地组合组件逻辑”。使用 Composition API 编写的代码更易读，而且没有任何幕后的魔力，更容易阅读和学习。

## 10、什么情况下使用 Vuex

Vuex 可以帮助我们管理共享状态，并附带了更多的概念和框架。这需要对短期和长期效益进行权衡。

如果您不打算开发大型单页应用，使用 Vuex 可能是繁琐冗余的。确实是如此——如果您的应用够简单，您最好不要使用 Vuex。一个简单的 [store 模式](https://cn.vuejs.org/v2/guide/state-management.html#简单状态管理起步使用)就足够您所需了。但是，如果您需要构建一个中大型单页应用，您很可能会考虑如何更好地在组件外部管理状态，Vuex 将会成为自然而然的选择。 

## 11、可以直接修改state的值吗？

不可以

## 12、为什么Vuex的mutation不能做异步操作 

Vuex中所有的状态更新的唯一途径都是mutation，异步操作通过 Action 来提交 mutation实现，这样使得我们可以方便地跟踪每一个状态的变化，从而让我们能够实现一些工具帮助我们更好地了解我们的应用。

每个mutation执行完成后都会对应到一个新的状态变更，这样devtools就可以打个快照存下来，然后就可以实现 time-travel 了。如果mutation支持异步操作，就没有办法知道状态是何时更新的，无法很好的进行状态的追踪，给调试带来困难。

## 13、v-model和vuex有冲突吗？

第一种（暴力流）

```
    <div>
        <input type="text" v-model="$store.state.Root.value" />
        <p>{{ $store.state.Root.value }}</p>
        // 这里为什么是state.Root.value 是我这里用到了vuex里的modules
        // 关于modules我会用新一篇文章来介绍，这里大家看看就行
    </div>
复制代码
```

- 我们都知道v-model是一种语法糖：

  ```
    <input type="text" v-model="val" />
  复制代码
  ```

  等价于

  ```
    <input type="text" :value="value" @input="value = $event.tagret.value" />
  复制代码
  ```

- 其实第一种方法就是利用了v-model的语法糖，至于为什么不需要mutations我猜是因为对象的引用关系

- 第二种（优雅型，通过computed）

- 这种方式一直是我在团队里比较建议使用的，因为它遵从了Vuex的核心理念：使用mutations来改变state

- ```
   <input v-model="getVal" />
    
  computed: {
      getVal: {
          get() {
               // 这里也是用了Vuex里的 modules 大家可以当成普通的变量来看
              return this.$store.state.Root.value
          },
           set(newVal) {
               this.$store.commit('handleVal', newVal)
           }
      }
  }
  复制代码
  ```

- computed其实可以接受两个参数：

  - get：当获取值时会触发
  - set：当修改值时会触发并有新值作为参数返回

- 所以我在get里获取Vuex

- 在set里调用 mutations

- ```
   // store.js
   mutations: {
       handleVal(state, payload) {
           state.value = payload
       }
   }
  ```




## 14、路由懒加载是什么意思？如何实现路由懒加载？

　　像vue这种单页面应用，如果没有应用懒加载，运用webpack打包后的文件将会异常的大，造成进入首页时，需要加载的内容过多，时间过长，会出啊先长时间的白屏，即使做了loading也是不利于用户体验，而运用懒加载则可以将页面进行划分，需要的时候加载页面，可以有效的分担首页所承担的加载压力，减少首页加载用时

## 15 Vue-router导航守卫有哪些？

1、全局守卫： `router.beforeEach`

2、全局解析守卫： `router.beforeResolve`

3、全局后置钩子： `router.afterEach`

4、路由独享的守卫： `beforeEnter`

5、组件内的守卫： `beforeRouteEnter、beforeRouteUpdate (2.2 新增)、beforeRouteLeave`

`导航`表示路由正在发生改变，`vue-router 提供的导航守卫主要用来:`通过跳转或取消的方式守卫导航。有多种机会植入路由导航过程中：`全局的`, `单个路由独享的`, 或者`组件级的`。

`注意：`参数或查询的改变并不会触发进入/离开的导航守卫。 你可以通过 [观察 $route 对象](https://www.kancloud.cn/hanxuming/vue-iq/733850) 来应对这些变化，或使用 `beforeRouteUpdate`的组件内守卫。

------

1、全局守卫：

使用 `router.beforeEach` 注册一个全局前置守卫：

```
const router = new VueRouter({ ... })
  router.beforeEach((to, from, next) => {
  // ...
})
```

当一个导航触发时，`全局前置守卫`按照创建顺序调用。守卫是异步解析执行，此时导航在所有守卫 `resolve 完之前`一直处于`等待中`。

每个守卫方法接收三个参数：

`to: Route`: 即将要进入的目标 路由对象

`from: Route`: 当前导航正要离开的路由

`next: Function`: 一定要调用该方法来`resolve`这个钩子。执行效果`依赖 next 方法的调用`参数。

- `next()`: 进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是`confirmed` (确认的)。
- `next(false)`: 中断当前的导航。如果浏览器的 URL 改变了 (可能是用户手动或者浏览器后退按钮)，那么 URL 地址`会重置到 from 路由`对应的地址。
- `next('/') 或者 next({ path: '/' })`: 跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。你可以向 `next 传递任意位置对象`，且`允许设置诸如` replace: true、name: 'home' 之类的选项以及任何用在`router-link`的 `to prop`或 `router.push`中的选项。
- `next(error)`: (2.4.0+) 如果传入 next 的参数是一个 Error 实例，则导航会`被终止`且该错误会被传递给 `router.onError()`注册过的回调。

确保要调用 `next`方法，`否则`钩子就`不会被 resolved`。

------

2、全局解析守卫：

> 2.5.0 新增

在 `2.5.0+` 你可以用 `router.beforeResolve` 注册一个`全局守卫`。这和 `router.beforeEach` 类似，`区别是：`在导航`被确认之前`，同时在所有`组件内守卫`和`异步路由组件`被解析`之后`，解析守卫就`被调用`。

------

3、全局后置钩子

你也可以注册`全局后置钩子`，然而和`守卫不同的是`，这些钩子`不会接受 next 函数`也`不会改变导航本身`：

```
router.afterEach((to, from) => {
  // ...
})
```

------

4、路由独享的守卫

你可以在路由配置上直接定义 `beforeEnter` 守卫：

```
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      beforeEnter: (to, from, next) => {
        // ...
      }
    }
  ]
})
```

这些守卫`与全局前置守卫`的`方法参数是一样的`。

------

5、组件内的守卫

最后，你可以在`路由组件内`直接定义以下`路由导航守卫`：

> ```
> beforeRouteEnter
> ```

> `beforeRouteUpdate` (2.2 新增)

> ```
> beforeRouteLeave
> ```

```
const Foo = {
  template: `...`,
  beforeRouteEnter (to, from, next) {
    // 在渲染该组件的对应路由被 confirm 前调用
    // 不！能！获取组件实例 `this`
    // 因为当守卫执行前，组件实例还没被创建
  },
  //不过，你可以通过传一个回调给 next来访问组件实例。
  //在导航被确认的时候执行回调，并且把组件实例作为回调方法的参数。
  beforeRouteEnter (to, from, next) {
    next(vm => {
      // 通过 `vm` 访问组件实例
    })
  },
  beforeRouteUpdate (to, from, next) {
    // 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
    // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
    // 可以访问组件实例 `this`
  },
  beforeRouteLeave (to, from, next) {
    // 导航离开该组件的对应路由时调用
    // 可以访问组件实例 `this`
  }
}
```

注意：`beforeRouteEnter` 是支持给`next 传递回调`的唯一守卫。对于`beforeRouteUpdate` 和 `beforeRouteLeave` 来说，`this` 已经可用了，所以`不支持传递回调`，因为没有必要了:

```
beforeRouteUpdate (to, from, next) {
  // just use `this`
  this.name = to.params.name
  next()
}
```

`离开守卫beforeRouteLeave：`通常用来禁止用户在还未保存修改前突然离开。该导航可以通过 `next(false)` 来取消：

```
beforeRouteLeave (to, from , next) {
  const answer = window.confirm('Do you really want to leave? you have unsaved changes!')
  if (answer) {
    next()
  } else {
    next(false)
  }
}
```

## 16、vue路由hash模式和histroy模式实现原理分别是什么 区别是什么？

hash 模式：

\#后面 hash 值的变化，不会导致浏览器向服务器发出请求，浏览器不发出请求，就不会刷新页面

通过监听 hashchange 事件可以知道 hash 发生了哪些变化，然后根据 hash 变化来实现更新页面部分内容的操作。

history 模式：

history 模式的实现，主要是 HTML5 标准发布的两个 API，pushState 和 replaceState，这两个 API 可以在改变 url，但是不会发送请求。这样就可以监听 url 变化来实现更新页面部分内容的操作

区别

url 展示上，hash 模式有“#”，history 模式没有

刷新页面时，hash 模式可以正常加载到 hash 值对应的页面，而 history 没有处理的话，会返回 404，一般需要后端将所有页面都配置重定向到首页路由

兼容性，hash 可以支持低版本浏览器和 IE。

## 17、说一下Vue的双向绑定数据的原理

vue.js 则是采用数据劫持结合发布者-订阅者模式的方式，通过`Object.defineProperty()`来劫持各个属性的`setter`，`getter`，在数据变动时发布消息给订阅者，触发相应的监听回调。我们先来看Object.defineProperty()这个方法：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
var obj  = {};
Object.defineProperty(obj, 'name', {
        get: function() {
            console.log('我被获取了')
            return val;
        },
        set: function (newVal) {
            console.log('我被设置了')
        }
})
obj.name = 'fei';//在给obj设置name属性的时候，触发了set这个方法
var val = obj.name;//在得到obj的name属性，会触发get方法
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

已经了解到vue是通过数据劫持的方式来做数据绑定的，其中最核心的方法便是通过`Object.defineProperty()`来实现对属性的劫持，那么在设置或者获取的时候我们就可以在get或者set方法里假如其他的触发函数，达到监听数据变动的目的，无疑这个方法是本文中最重要、最基础的内容之一。

**2.实现最简单的双向绑定**

我们知道通过Object.defineProperty()可以实现数据劫持，是的属性在赋值的时候触发set方法，

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <div id="demo"></div>
    <input type="text" id="inp">
    <script>
        var obj  = {};
        var demo = document.querySelector('#demo')
        var inp = document.querySelector('#inp')
        Object.defineProperty(obj, 'name', {
            get: function() {
                return val;
            },
            set: function (newVal) {//当该属性被赋值的时候触发
                inp.value = newVal;
                demo.innerHTML = newVal;
            }
        })
        inp.addEventListener('input', function(e) {
            // 给obj的name属性赋值，进而触发该属性的set方法
            obj.name = e.target.value;
        });
        obj.name = 'fei';//在给obj设置name属性的时候，触发了set这个方法
    </script>
</body>
</html>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

当然要是这么粗暴，肯定不行，性能会出很多的问题。

**3.讲解vue如何实现**

先看原理图

![img](https://images2017.cnblogs.com/blog/1162184/201709/1162184-20170918135341618-553576179.png)

3.1 observer用来实现对每个vue中的data中定义的属性循环用Object.defineProperty()实现数据劫持，以便利用其中的setter和getter，然后通知订阅者，订阅者会触发它的update方法，对视图进行更新。

3.2 我们介绍为什么要订阅者，在vue中v-model，v-name，{{}}等都可以对数据进行显示，也就是说假如一个属性都通过这三个指令了，那么每当这个属性改变的时候，相应的这个三个指令的html视图也必须改变，于是vue中就是每当有这样的可能用到双向绑定的指令，就在一个Dep中增加一个订阅者，其订阅者只是更新自己的指令对应的数据，也就是v-model='name'和{{name}}有两个对应的订阅者，各自管理自己的地方。每当属性的set方法触发，就循环更新Dep中的订阅者。

**4.vue代码实现**

4.1 observer实现，主要是给每个vue的属性用Object.defineProperty()，代码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
function defineReactive (obj, key, val) {
    var dep = new Dep();
        Object.defineProperty(obj, key, {
             get: function() {
                    //添加订阅者watcher到主题对象Dep
                    if(Dep.target) {
                        // JS的浏览器单线程特性，保证这个全局变量在同一时间内，只会有同一个监听器使用
                        dep.addSub(Dep.target);
                    }
                    return val;
             },
             set: function (newVal) {
                    if(newVal === val) return;
                    val = newVal;
                    console.log(val);
                    // 作为发布者发出通知
                    dep.notify();//通知后dep会循环调用各自的update方法更新视图
             }
       })
}
        function observe(obj, vm) {
            Object.keys(obj).forEach(function(key) {
                defineReactive(vm, key, obj[key]);
            })
        }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

4.2实现compile：

compile的目的就是解析各种指令称真正的html。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
function Compile(node, vm) {
    if(node) {
        this.$frag = this.nodeToFragment(node, vm);
        return this.$frag;
    }
}
Compile.prototype = {
    nodeToFragment: function(node, vm) {
        var self = this;
        var frag = document.createDocumentFragment();
        var child;
        while(child = node.firstChild) {
            console.log([child])
            self.compileElement(child, vm);
            frag.append(child); // 将所有子节点添加到fragment中
        }
        return frag;
    },
    compileElement: function(node, vm) {
        var reg = /\{\{(.*)\}\}/;
        //节点类型为元素(input元素这里)
        if(node.nodeType === 1) {
            var attr = node.attributes;
            // 解析属性
            for(var i = 0; i < attr.length; i++ ) {
                if(attr[i].nodeName == 'v-model') {//遍历属性节点找到v-model的属性
                    var name = attr[i].nodeValue; // 获取v-model绑定的属性名
                    node.addEventListener('input', function(e) {
                        // 给相应的data属性赋值，进而触发该属性的set方法
                        vm[name]= e.target.value;
                    });
                    new Watcher(vm, node, name, 'value');//创建新的watcher，会触发函数向对应属性的dep数组中添加订阅者，
                }
            };
        }
        //节点类型为text
        if(node.nodeType === 3) {
            if(reg.test(node.nodeValue)) {
                var name = RegExp.$1; // 获取匹配到的字符串
                name = name.trim();
                new Watcher(vm, node, name, 'nodeValue');
            }
        }
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

4.3 watcher实现

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
function Watcher(vm, node, name, type) {
    Dep.target = this;
    this.name = name;
    this.node = node;
    this.vm = vm;
    this.type = type;
    this.update();
    Dep.target = null;
}

Watcher.prototype = {
    update: function() {
        this.get();
        this.node[this.type] = this.value; // 订阅者执行相应操作
    },
    // 获取data的属性值
    get: function() {
        console.log(1)
        this.value = this.vm[this.name]; //触发相应属性的get
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

4.4 实现Dep来为每个属性添加订阅者

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
function Dep() {
    this.subs = [];
}
Dep.prototype = {
    addSub: function(sub) {
        this.subs.push(sub);
    },
    notify: function() {
        this.subs.forEach(function(sub) {
        sub.update();
        })
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

这样一来整个数据的双向绑定就完成了。

**5.梳理**

首先我们为每个vue属性用Object.defineProperty()实现数据劫持，为每个属性分配一个订阅者集合的管理数组dep；然后在编译的时候在该属性的数组dep中添加订阅者，v-model会添加一个订阅者，{{}}也会，v-bind也会，只要用到该属性的指令理论上都会，接着为input会添加监听事件，修改值就会为该属性赋值，触发该属性的set方法，在set方法内通知订阅者数组dep，订阅者数组循环调用各订阅者的update方法更新视图。

## 18、解释单向数据流和双向数据绑定

一：单向数据流

单向数据流的意思是指数据的改变只能从一个方向修改。

举个栗子：如一个父组件有两个子组件，分别为1和2。父组件向子组件传递数据，两个组件都接收到了父组件传递过来的数据，在组件1中修改父组件传递过来的数据，子组件2和父组件的值不会发生变化。这就是单向的数据流，子组件不能直接改变父组件的状态。但是如果父组件改变相应的数据，两个子组件的数据也会发生相应的改变。

二：双向数据绑定

由MVVM框架实现，MVVM的组成：View，ViewModel，Model。其中View 和 Model不能直接通信，要通过ViewModel来进行通信。

举个栗子：例如，当Model部分数据发生改变时，由于vue中Data Binding将底层数据和Dom层进行了绑定，ViewModel通知View层更新视图；当在视图 View数据发生变化也会同步到Model中。View和Model之间的同步完全是自动的，不需要人手动的操作DOM。


## 19、Vue 如何去除url中的 `#`

```
vue-router` 默认使用 `hash` 模式，所以在路由加载的时候，项目中的 `url` 会自带 `#`。如果不想使用 `#`， 可以使用 `vue-router` 的另一种模式 `history
new Router({
  mode: 'history',
  base: process.env.BASE_URL,
  routes
})
```

​    需要注意的是，当我们启用 history 模式的时候，由于我们的项目是一个单页面应用，所以在路由跳转的时候，就会出现访问不到静态资源而出现 404 的情况，这时候就需要服务端增加一个覆盖所有情况的候选资源：如果 URL 匹配不到任何静态资源，则应该返回同一个 index.html 页面。 

## 20、对 MVC、MVVM的理解

一、早期的mvc

早期的页面通常由多个PHP，jsp文件构成，这种代码难以维护，于是延伸了mvc开发模式和框架，前端展示以模板的形式出现，典型的框架如spring等，这种分成结构职责清晰，代码易于维护，但这里的mvc仅仅限于后端，前后端形成了一定的分离，前端只完成了开发成中的view层

![img](https://img2020.cnblogs.com/blog/1010710/202003/1010710-20200320123921969-1650585354.png)

缺点

1前端页面开发效率不高

2前后端职责不清晰

二、前端mvc

随着ajax的发展，前端得到进一步发展，前端的类库也开始发展最著名的就是jq，因此前端mvc随之而来

与后端类似，具有view,controller,model，mvc模式是单项绑定，即model绑定到view，当我们用js代码更新model时，view就会自动更新

model负责数据保存，与后端数据进行同步

controller负责业务逻辑，根据用户行为对model数据进行修改

view负责视图展示，将model中的数据可视化出来

![img](https://img2020.cnblogs.com/blog/1010710/202003/1010710-20200320124753730-1577822292.png)

缺点：强行分层，一个小小的功能都必须经过这么一个流程，并不灵活

![img](https://img2020.cnblogs.com/blog/1010710/202003/1010710-20200320125814612-350696279.png)

mvvm的出现

![img](https://img2020.cnblogs.com/blog/1010710/202003/1010710-20200320130030121-1943412182.png)

![img](https://img2020.cnblogs.com/blog/1010710/202003/1010710-20200320130222956-67547092.png)

![img](https://img2020.cnblogs.com/blog/1010710/202003/1010710-20200320130403385-1489665672.png)

## 21、介绍虚拟DOM

先介绍浏览器加载一个HTML文件需要做哪些事，帮助我们理解为什么我们需要虚拟DOM。webkit引擎的处理流程，一图胜千言：

![img](https:////upload-images.jianshu.io/upload_images/1959053-7c24fdb60936bd96.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/636/format/webp)

所有浏览器的引擎工作流程都差不多，如上图大致分5步：创建DOM tree –> 创建Style Rules -> 构建Render tree -> 布局Layout –> 绘制Painting

第一步，用HTML分析器，分析HTML元素，构建一颗DOM树。

第二步：用CSS分析器，分析CSS文件和元素上的inline样式，生成页面的样式表。

第三步：将上面的DOM树和样式表，关联起来，构建一颗Render树。这一过程又称为Attachment。每个DOM节点都有attach方法，接受样式信息，返回一个render对象（又名renderer）。这些render对象最终会被构建成一颗Render树。

第四步：有了Render树后，浏览器开始布局，会为每个Render树上的节点确定一个在显示屏上出现的精确坐标值。

第五步：Render数有了，节点显示的位置坐标也有了，最后就是调用每个节点的paint方法，让它们显示出来。

当你用传统的源生api或jQuery去操作DOM时，浏览器会从构建DOM树开始从头到尾执行一遍流程。比如当你在一次操作时，需要更新10个DOM节点，理想状态是一次性构建完DOM树，再执行后续操作。但浏览器没这么智能，收到第一个更新DOM请求后，并不知道后续还有9次更新操作，因此会马上执行流程，最终执行10次流程。显然例如计算DOM节点的坐标值等都是白白浪费性能，可能这次计算完，紧接着的下一个DOM更新请求，这个节点的坐标值就变了，前面的一次计算是无用功。

即使计算机硬件一直在更新迭代，操作DOM的代价仍旧是昂贵的，频繁操作还是会出现页面卡顿，影响用户的体验。真实的DOM节点，哪怕一个最简单的div也包含着很多属性，可以打印出来直观感受一下：

![img](https:////upload-images.jianshu.io/upload_images/1959053-409c2c86d78baa71.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

虚拟DOM就是为了解决这个浏览器性能问题而被设计出来的。例如前面的例子，假如一次操作中有10次更新DOM的动作，虚拟DOM不会立即操作DOM，而是将这10次更新的diff内容保存到本地的一个js对象中，最终将这个js对象一次性attach到DOM树上，通知浏览器去执行绘制工作，这样可以避免大量的无谓的计算量。

实现虚拟DOM

我们来实现一个虚拟DOM。例如一个真实的DOM节点：代码见[仓库里的src/firstStep](https://link.jianshu.com?t=https://github.com/JackZhangXL/virtual-dom-demo)



```javascript
<div id="real-container">
    <p>Real DOM</p>
    <div>cannot update</div>
    <ul>
        <li className="item">Item 1</li>
        <li className="item">Item 2</li>
        <li className="item">Item 3</li>
    </ul>
</div>
```

用js对象来模拟DOM节点如下：



```javascript
const tree = Element('div', { id: 'virtual-container' }, [
    Element('p', {}, ['Virtual DOM']),
    Element('div', {}, ['before update']),
    Element('ul', {}, [
        Element('li', { class: 'item' }, ['Item 1']),
        Element('li', { class: 'item' }, ['Item 2']),
        Element('li', { class: 'item' }, ['Item 3']),
    ]),
]);

const root = tree.render();
document.getElementById('virtualDom').appendChild(root);
```

用js对象模拟DOM节点的好处是，页面的更新可以先全部反映在js对象上，操作内存中的js对象的速度显然要快多了。等更新完后，再将最终的js对象映射成真实的DOM，交由浏览器去绘制。

那具体怎么实现呢？看一下Element方法的具体实现：



```javascript
function Element(tagName, props, children) {
    if (!(this instanceof Element)) {
        return new Element(tagName, props, children);
    }

    this.tagName = tagName;
    this.props = props || {};
    this.children = children || [];
    this.key = props ? props.key : undefined;

    let count = 0;
    this.children.forEach((child) => {
        if (child instanceof Element) {
            count += child.count;
        }
        count++;
    });
    this.count = count;
}
```

第一个参数是节点名（如div），第二个参数是节点的属性（如class），第三个参数是子节点（如ul的li）。除了这三个参数会被保存在对象上外，还保存了key和count。



![img](https:////upload-images.jianshu.io/upload_images/1959053-b834a19182079afd.png?imageMogr2/auto-orient/strip|imageView2/2/w/744/format/webp)

有了js对象后，最终还需要将其映射成真实的DOM：



```javascript
Element.prototype.render = function() {
    const el = document.createElement(this.tagName);
    const props = this.props;

    for (const propName in props) {
        setAttr(el, propName, props[propName]);
    }

    this.children.forEach((child) => {
        const childEl = (child instanceof Element) ? child.render() : document.createTextNode(child);
        el.appendChild(childEl);
    });

    return el;
};
```

上面都是自解释代码，根据DOM名调用源生的createElement创建真实DOM，将DOM的属性全都加到这个DOM元素上，如果有子元素继续递归调用创建子元素，并appendChild挂到该DOM元素上。这样就完成了从创建虚拟DOM到将其映射成真实DOM的全部工作。



 

## 22、vue生命周期的理解

当面试官问：“谈谈你对vue的生命周期的理解”，听到这句话你是不是心里暗自窃喜：这也太容易了吧，不就是beforeCreate、created、beforeMount、mounted、beforeUpdate、updated、beforeDestroy、destroyed 这几个钩子函数么，创建=>挂载=>更新=>销毁，So easy ！！！

非也非也。如果你只是简单罗列出这几个钩子函数的名称，不具体深入阐述的话，你这样的回答很难令面试官满意。如何才能以点带面深入阐述自己对vue的生命周期理解，从而让面试官对你留下好印象呢？

别急，闰土大叔来告诉你，下次再碰到这个问题，你可以直接甩给面试官下面这张Image就OK了~

![clipboard.png](https://segmentfault.com/img/bVbo4Vg?w=505&h=1280)

当然，甩张Image给面试官这句话肯定是开玩笑的（适度幽默，缓解紧张气氛）。不过这张流程图还是有用的，因为它是我从Vue官网上拷贝下来的，只要你能理解了这张图，也就对Vue的生命周期有了一个大致的了解。那么接下来，闰土大叔将手摸手教你如何深入浅出地说出令面试官满意的、有亮点的回答。

在谈到Vue的生命周期的时候，我们首先需要创建一个实例，也就是在 new Vue ( ) 的对象过程当中，首先执行了init（init是vue组件里面默认去执行的），在init的过程当中首先调用了beforeCreate，然后在injections（注射）和reactivity（反应性）的时候，它会再去调用created。所以在init的时候，事件已经调用了，我们在beforeCreate的时候千万不要去修改data里面赋值的数据，最早也要放在created里面去做（添加一些行为）。

当created完成之后，它会去判断instance（实例）里面是否含有“el”option（选项），如果没有的话，它会调用vm.$mount(el)这个方法，然后执行下一步；如果有的话，直接执行下一步。紧接着会判断是否含有“template”这个选项，如果有的话，它会把template解析成一个render function ，这是一个template编译的过程，结果是解析成了render函数：

```
render (h) {
  return h('div', {}, this.text)
}
```

解释一下，render函数里面的传参h就是Vue里面的createElement方法，return返回一个createElement方法，其中要传3个参数，第一个参数就是创建的div标签；第二个参数传了一个对象，对象里面可以是我们组件上面的props，或者是事件之类的东西；第三个参数就是div标签里面的内容，这里我们指向了data里面的text。

使用render函数的结果和我们之前使用template解析出来的结果是一样的。render函数是发生在beforeMount和mounted之间的，这也从侧面说明了，在beforeMount的时候，$el还只是我们在HTML里面写的节点，然后到mounted的时候，它就把渲染出来的内容挂载到了DOM节点上。这中间的过程其实是执行了render function的内容。

在使用.vue文件开发的过程当中，我们在里面写了template模板，在经过了vue-loader的处理之后，就变成了render function，最终放到了vue-loader解析过的文件里面。这样做有什么好处呢？原因是由于在解析template变成render function的过程，是一个非常耗时的过程，vue-loader帮我们处理了这些内容之后，当我们在页面上执行vue代码的时候，效率会变得更高。

beforeMount在有了render function的时候才会执行，当执行完render function之后，就会调用mounted这个钩子，在mounted挂载完毕之后，这个实例就算是走完流程了。

后续的钩子函数执行的过程都是需要外部的触发才会执行。比如说有数据的变化，会调用beforeUpdate，然后经过Virtual DOM，最后updated更新完毕。当组件被销毁的时候，它会调用beforeDestory，以及destoryed。

这就是vue实例从新建到销毁的一个完整流程，以及在这个过程中它会触发哪些生命周期的钩子函数。那说到这儿，可能很多童鞋会问，钩子函数是什么意思？

钩子函数，其实和回调是一个概念，当系统执行到某处时，检查是否有hook，有则回调。说的更直白一点，每个组件都有属性，方法和事件。所有的生命周期都归于事件，在某个时刻自动执行。

其实，当你跟面试官阐述到这儿的时候，面试官基本上已经满意你的回答了，隐约看到了你的技术功底。当然，如果你还想更进一步，让面试官对你刮目相看，达到加分的效果，你还可以这样说：

在这个过程当中，Vue为我们提供了renderError方法，这个方法只有在开发的时候它才会被调用，在正式打包上线的过程当中，它是不会被调用的。它主要是帮助我们调试render里面的一些错误。

```
renderError (h, err) {
  return h('div', {}, err.stack)
}
```

有且只有当render方法里面报错了，才会执行renderError方法。

所以我们主动让render函数报个错：

```
render (h) {
  throw new TypeError('render error')
}
```

![clipboard.png](https://segmentfault.com/img/bVbo4Vz?w=1280&h=416)

如图所示，渲染出来的就是Error信息了。还有一点，renderError只有在本组件的render方法报错的情况下它才会被调用。

## 23、**vue父组件向子组件传递数据？**

通过props

## 24、vue-router 路由实现原理

更新视图但不重新请求页面，是前端路由原理的核心之一，目前在浏览器环境中这一功能的实现主要有`2`种方式：

　　　　1.**Hash** --- 利用 URL 中的hash("#");

　　　　2.利用 **History interface** 在HTML5中新增的方法。

 

　　Vue 中，它是通过 **mode** 这一参数控制路由的实现模式：

 

```
const router=new VueRouter({
    mode:'history',
    routes:[...]
})
```

 

　　创建 VueRouter 的实例对象时，mode 以构造参数的形式传入，如下代码：

　　

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 源码

 

　　

**mode** 参数：

　　　　1.默认 hash

　　　　2. history。如果浏览器不支持 history 新特性，则采用 hash

　　　　3. 如果不在浏览器环境下，就采用 abstract（Node环境下）

　　　　![img](https://upload-images.jianshu.io/upload_images/4099767-4e2101346868ac4e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/442/format/webp)

 

mode 区别：

　　　　1. mode:"hash" 多了 “#”

```
http://localhost:8080/#/login
```

　　　　

　　　　2.mode:"history" 

```
http://localhost:8080/recommend
```

　　

HashHistory:

　　　　hash("#") 的作用是加载 URL 中指示网页中的位置。

本身以及它后面的字符称职位 hash，可通过 window.location.hash 获取

特点：

　　　　　　1. hash 虽然出现在 url 中，但不会被包括在 http 请求中，它是用来指导浏览器动作的，对服务器端完全无用，因此，改变 hash 不会重新加载页面。

　　　　　　2. 可以为 hash 的改变添加监听事件：

```
　　　　　　window.addEventListener("hashchange",funcRef,false)
```

　　　　　　3. 每一次改变 hash(window.localtion.hash)，都会在浏览器访问历史中增加一个记录。

　　　　利用 hash 的以上特点，就可以来实现前端路由"更新视图但不重新请求页面"的功能了。

 

　　　　**HashHistory** 拥有两个方法，一个是 **push**， 一个是 **replace**

```
两个方法：HashHistory.push() 和 HashHistory.replace()
```

　　

　　　　**HashHistory.push()** 将新路由添加到浏览器访问历史的栈顶

　　

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) HashHisttory.push()

 

　　　　![img](https://upload-images.jianshu.io/upload_images/4099767-1398ade7d9db935e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/599/format/webp)

　　　　从设置路由改变到视图更新的流程：　　

```
$router.push() --> HashHistory.push() --> History.transitionTo() --> History.updateRoute() --> {app._route = route} --> vm.render()
```

　　

　　　　解析：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
1 $router.push() //调用方法

2 HashHistory.push() //根据hash模式调用,设置hash并添加到浏览器历史记录（添加到栈顶）（window.location.hash= XXX）

3 History.transitionTo() //监测更新，更新则调用History.updateRoute()

4 History.updateRoute() //更新路由

5 {app._route= route} //替换当前app路由

6 vm.render() //更新视图
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

　　　　**transitionTo()** 方法是父类中定义的是用来处理路由变化中的基础逻辑的，push() 方法最主要的是对 window 的 hash 进行了直接赋值：

　　

```
window.location.hash=route.fullPath
```

 

　　　　hash 的改变会自动添加到浏览器的访问历史记录中。
　　　　那么视图的更新是怎么实现的呢，我们来看看父类 History 中的 transitionTo() 方法：

　　　　

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
transitionTo (location: RawLocation, onComplete?: Function, onAbort?: Function) {
  const route = this.router.match(location, this.current)
  this.confirmTransition(route, () => {
    this.updateRoute(route)
    ...
  })
}

updateRoute (route: Route) {
  
  this.cb && this.cb(route)
  
}

listen (cb: Function) {
  this.cb = cb
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

　　　　可以看到，当路由变化时，调用了`Hitory`中的`this.cb`方法，而`this.cb`方法是通过`History.listen(cb)`进行设置的，回到`VueRouter`类定义中，找到了在`init()`中对其进行了设置：

　　　　

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
init (app: any /* Vue component instance */) {
    
  this.apps.push(app)

  history.listen(route => {
    this.apps.forEach((app) => {
      app._route = route
    })
  })
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

 

　　　　**HashHistory.replace()**

　　　　　　replace()方法与push()方法不同之处在于，它并不是将新路由添加到浏览器访问历史的栈顶，而是替换掉当前的路由

 

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) HashHisttory.replace()

　　　　　　![img](https://upload-images.jianshu.io/upload_images/4099767-13ffd0864295ca69.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/629/format/webp)

 

 

HTML5History

　　　　**History interface** 是浏览器历史记录栈提供的接口，通过back()、forward()、go()等方法，我们可以读取浏览器历史记录栈的信息，进行各种跳转操作。``

　　　　从 HTML5开始，**History interface** 提供了2个新的方法：pushState()、replaceState() 使得我们可以对浏览器历史记录栈进行修改：

```
　　window.history.pushState(stateObject,title,url)
　　window.history,replaceState(stateObject,title,url)
```

　　　　

　　　　stateObject：当浏览器跳转到新的状态时，将触发 Popstate 事件，该事件将携带这个 stateObject 参数的副本

　　　　title：所添加记录的标题

　　　　url：所添加记录的 url``

　　　　

　　　　这`2`个方法有个共同的特点：当调用他们修改浏览器历史栈后，虽然当前`url`改变了，但浏览器不会立即发送请求该`url`，这就为单页应用前端路由，更新视图但不重新请求页面提供了基础

　　　　

　　　　1.push

　　　　　　与hash模式类似，只是将window.hash改为history.pushState

　　　　2.replace

　　　　　　与hash模式类似，只是将window.replace改为history.replaceState

　　　　3.监听地址变化

　　　　　　在HTML5History的构造函数中监听popState（window.onpopstate）

## 25、v-if 和 v-show 区别

1.共同点

v-if 和 v-show 都可以动态地显示DOM元素

2.区别

（1）手段：

v-if 是动态的向DOM树内添加或者删除DOM元素；
v-show 是通过设置DOM元素的display样式属性控制显隐；

（2）编译过程：

v-if 切换有一个局部编译/卸载的过程，切换过程中合适地销毁和重建内部的事件监听和子组件；
v-show只是简单的基于css切换；

（3）编译条件：

v-if 是惰性的，如果初始条件为假，则什么也不做；只有在条件第一次变为真时才开始局部编译（编译被缓存？编译被缓存后，然后再切换的时候进行局部卸载);
v-show 是在任何条件下（首次条件是否为真）都被编译，然后被缓存，而且DOM元素保留；

（4）性能消耗：

v-if 有更高的切换消耗；
v-show 有更高的初始渲染消耗；

（5）使用场景：

v-if 适合运营条件不大可能改变；
v-show 适合频繁切换。

> 

## 26、`$route`和`$router`的区别

![router是VueRouter的实例，在script标签中想要导航到不同的URL,使用](https://math.jianshu.com/math?formula=router%E6%98%AFVueRouter%E7%9A%84%E5%AE%9E%E4%BE%8B%EF%BC%8C%E5%9C%A8script%E6%A0%87%E7%AD%BE%E4%B8%AD%E6%83%B3%E8%A6%81%E5%AF%BC%E8%88%AA%E5%88%B0%E4%B8%8D%E5%90%8C%E7%9A%84URL%2C%E4%BD%BF%E7%94%A8)router.push方法，路由跳转，传递参数等。

返回上一个历史history用$router.to(-1)

$route为当前router跳转对象。里面可以获取当前路由的name,path,query,parmas等 可以拿里边的值来做各种判断。



 





 

## 27、对比 jQuery ，Vue 有什么不同

1.jquery介绍：想必大家都用过jquery吧，这个曾经也是现在依然最流行的web前端js库，可是现在无论是国内还是国外他的使用率正在渐渐被其他的js库所代替，随着浏览器厂商对HTML5规范统一遵循以及ECMA6在浏览器端的实现，jquery的使用率将会越来越低

 

2.vue介绍：vue是一个兴起的前端js库，是一个精简的MVVM。从技术角度讲，Vue.js 专注于 MVVM 模型的 ViewModel 层。它通过双向数据绑定把 View 层和 Model 层连接了起来，通过对数据的操作就可以完成对页面视图的渲染。当然还有很多其他的mvmm框架如Angular，React都是大同小异，本质上都是基于MVVM的理念。 然而vue以他独特的优势简单，快速，组合，紧凑，强大而迅速崛起 

 

 

3.vue和jquey对比 

jQuery是使用选择器（$）选取DOM对象，对其进行赋值、取值、事件绑定等操作，其实和原生的HTML的区别只在于可以更方便的选取和操作DOM对象，而数据和界面是在一起的。比如需要获取label标签的内容：`$("lable").val();`,它还是依赖DOM元素的值。 

Vue则是通过Vue对象将数据和View完全分离开来了。对数据进行操作不再需要引用相应的DOM对象，可以说数据和View是分离的，他们通过Vue对象这个vm实现相互的绑定。这就是传说中的MVVM。

 

## 28、Vue 中怎么自定义指令

指令的概念

指令是指可以控制操作 DOM 的一些小命令，通常以 `v-` 前缀出现的特殊特性。

例如我们经常使用的`v-if`、`v-show`、`v-bind`、`v-on`、`v-html`等。

使用指令时，你可以传递值，字符串，也可以给指令添加参数，修饰符等等。比如：

1.传递值

```
<p v-if="isShow">你好，我是六哥</p>
let vm = new Vue({
  el: "#app",
  data: {
    isShow: true
  }
});
```

2.字符串

```
<p v-text="'hello world'"></p>
```

3.添加参数

```
// class,style 就是传给指令的参数

<div v-bind:class="classObj"></div>
<div v-bind:style="classObj"></div>
```

4.修饰符

```
// prevent 指令的修饰符

<button v-on:submit.prevent="onSubmit"></button>
```

以上是你经常使用指令的方式，了解这些之后，它可以帮助我们更好的认识自定义指令的 一些参数问题。下面就来看看自定义指令。

自定义指令

指令的注册方式和「过滤器」、「混入」、「组件」注册的方式一样都分为两种：一是全局注册，二是局部注册。

1.全局注册

```
Vue.directive('name', {})
```

2.局部注册

```
directives: {
    name: {}
}
```

然后在模版中直接使用即可。

```
<p v-name>你好，六哥在这</p>
```

我个人更倾向于使用全局注册的方式，因为既然已经使用了自定义指令，应该是通用，可复用的。

所以提供整个项目使用的指令才更有价值，而不仅仅只限于某个组件内部。如果单一地方使用直接把功能搂出就行了，何必费这力气。

继续来看具体的实现方式。

Vue 提供了自定义指令的几个钩子函数：

- bind：指令第一次绑定到元素时调用，只执行一次。
- inserted：被绑定的元素，插入到父节点的 DOM 中时调用。
- update：组件更新时调用。
- componentUpdated：组件与子组件更新时调用。
- unbind：指令与元素解绑时调用，只执行一次。

除update 与 componentUpdated 钩子函数之外，每个钩子函数都含有 `el`、`binding`、`vnode` 这三个参数。

`oldVnode` 只有在 update 与 componentUpdated 钩子中生效。

参数`el` 就是指令绑定的 DOM 元素，而`binding`是一个对象，它包含一下属性：`name`、`value`、`oldValue`、`expression`、`arg`、`modifiers`。

另外值得注意的一点是，除了 `el` 之外，`binding`、`vnode` 属性都是只读的。

熟悉指令的创建方式与参数之后，我们就用它来创建一个案例。

创建自定义指令

Loading 是项目中最常见的一个小功能，别看它功能小，但是起到的作用却很大，手动创建一个 Loading 指令。

```
Vue.directive("loading", {
  bind(el, binding) {
    if (binding.value) {
      let div = document.createElement("div");
      div.className = "loading-parent";
      div.innerHTML = `
        <div class="loading-spinner"><i class='el-icon-loading'></i></div>
      `;
      el.appendChild(div);
    }
  },
  update(el, binding) {
    if (binding.value) {
      let div = document.createElement("div");
      div.className = "loading-parent";
      div.innerHTML = `
        <div class="loading-spinner"><i class='el-icon-loading'></i></div>
      `;
      el.appendChild(div);
      el.load = div;
    } else {
      el.load && el.load.parentNode && el.load.parentNode.removeChild(el.load);
    }
  }
});
```

然后我们直接就可以在模版中使用了。

```
<div v-loading="loading" class="box"></div>
```

你也看出来了很多代码是重复的，怎么办呢？

Vue 中给我们提供了简写方式。当只有这两个钩子函数时`bind` 与 `update`，我们可以简写如下。

```
Vue.directive("loading", function(el, binding) {
  if (binding.value) {
    let div = document.createElement("div");
    div.className = "loading-parent";
    div.innerHTML = `
        <div class="loading-spinner"><i class='el-icon-loading'></i></div>
      `;
    el.appendChild(div);
    el.load = div;
  } else {
    el.load && el.load.parentNode && el.load.parentNode.removeChild(el.load);
  }
});
```

我们实现了一个非常简单的指令，但还不够灵活，比如我想添加 loading 的背景色，修改图标的颜色怎么办呢？

这时候就需要给指令传入多个值，改造下实现背景与图标颜色。

```
Vue.directive("loading", function(el, binding) {
  if (binding.value) {
    let div = document.createElement("div");
    div.className = "loading-parent";
    div.style.backgroundColor = binding.value.background;
    div.style.color = binding.value.color;
    div.innerHTML = `
        <div class="loading-spinner"><i class='el-icon-loading'></i></div>
      `;
    el.appendChild(div);
    el.load = div;
  } else {
    el.load && el.load.parentNode && el.load.parentNode.removeChild(el.load);
  }
});
```

直接使用

```
<div v-loading="{color: 'white', background: '#000'}">我可以拥有更多属性</div>
```

在文章开始我们介绍指令时，还说到指令的「参数」与「修饰符」这里我就不多介绍了，大家不妨自己去尝试一下吧。体验下自定义指令的魅力。




## 29、Vue 中怎么自定义过滤器

注意事项:

(1)全局方法 Vue.filter() 注册一个自定义过滤器,必须放在Vue实例化前面

(2) 过滤器函数始终以表达式的值作为第一个参数。带引号的参数视为字符串，而不带引号的参数按表达式计算

(3)可以设置两个过滤器参数,前提是这两个过滤器处理的不冲突

(4)用户从input输入的数据在回传到model之前也可以先处理

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title>vue自定义过滤器</title>
        <script src="../js/vue.js" type="text/javascript" charset="utf-8"></script>
        <meta name="viewport" content="width=device-width,initial-scale=1,minimum-scale=1,maximum-scale=1,user-scalable=no" />
    </head>
    <body>
        <div class="test">
            <p>{{message | sum}}</p>
            <p>{{message | cal 10 20}}</p>  <!--过滤器函数始终以表达式的值作为第一个参数。带引号的参数视为字符串，而不带引号的参数按表达式计算。-->
            <p>{{message | sum | currency }}</p> <!--添加两个过滤器,注意不要冲突-->
            
            <input type="text" v-model="message | change"> <!--用户从input输入的数据在回传到model之前也可以先处理-->
            
        </div>
        <script type="text/javascript">
        
//        -----------------------------------------华丽分割线(从model->view)---------------------------------------
            Vue.filter("sum", function(value) {   //全局方法 Vue.filter() 注册一个自定义过滤器,必须放在Vue实例化前面
                return value + 4;
            });
            
            Vue.filter('cal', function (value, begin, xing) {   //全局方法 Vue.filter() 注册一个自定义过滤器,必须放在Vue实例化前面
                return value + begin + xing;
            });

//        -----------------------------------------华丽分割线(从view->model)---------------------------------------
            Vue.filter("change", {
                read: function (value) { // model -> view 在更新 `<input>` 元素之前格式化值
                    return value;
                },
                write: function (newVal,oldVal) { // view -> model  在写回数据之前格式化值
                    console.log("newVal:"+newVal); 
                    console.log("oldVal:"+oldVal);
                    return newVal;
                }
            });

            var myVue = new Vue({
                el: ".test",
                data: {
                    message:12
                }
            });
            
        </script>
    </body>
</html>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

二 自定义过滤器(注册在实例化内部)

上面的例子直接注册在Vue全局上面,其他不用这个过滤器的实例也会被迫接受,其实过滤器可以注册在实例内部,仅在使用它的实例里面注册

上面的程序改写为:

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title>vue自定义过滤器</title>
        <script src="../js/vue.js" type="text/javascript" charset="utf-8"></script>
        <meta name="viewport" content="width=device-width,initial-scale=1,minimum-scale=1,maximum-scale=1,user-scalable=no" />
    </head>
    <body>
        <div class="test">
            <p>{{message | sum}}</p>
            <p>{{message | cal 10 20}}</p>  <!--过滤器函数始终以表达式的值作为第一个参数。带引号的参数视为字符串，而不带引号的参数按表达式计算。-->
            <p>{{message | sum | currency }}</p> <!--添加两个过滤器,注意不要冲突-->
            
            <input type="text" v-model="message | change"> <!--用户从input输入的数据在回传到model之前也可以先处理-->
            
        </div>
        <script type="text/javascript">
            Vue.filter("change", {
                read: function (value) { // model -> view 在更新 `<input>` 元素之前格式化值
                    return value;
                },
                write: function (newVal,oldVal) { // view -> model  在写回数据之前格式化值
                    console.log("newVal:"+newVal); 
                    console.log("oldVal:"+oldVal);
                    return newVal;
                }
            });

            var myVue = new Vue({
                el: ".test",
                data: {
                    message:12
                },
                filters: {
                    sum: function (value) {
                        return value + 4;
                    },
                    cal: function (value, begin, xing) {
                        return value + begin + xing;
                    }
                }
            });
            
        </script>
    </body>
</html>
```



## 30、Vue 中 key 的作用

1.v-if中用key管理可复用的元素

　 Vue 会尽可能高效地渲染元素，通常会复用已有元素而不是从头开始渲染。这么做，除了使 Vue 变得非常快之外，还有一些有用的好处。例如，如果你允许用户在不同的登录方式之间切换：

 ![img](https://img2018.cnblogs.com/common/1896185/201912/1896185-20191212154707616-1309420918.png)

那么在上面的代码中切换 loginType 将不会清除用户已经输入的内容。因为两个模版使用了相同的元素，<input> 不会被替换掉——仅仅是替换了它的 placeholder。这样也不总是符合实际需求，所以 Vue 为你提供了一种方式来声明“这两个元素是完全独立的——不要复用它们”。只需添加一个具有唯一值的 key 属性即可：

![img](https://img2018.cnblogs.com/common/1896185/201912/1896185-20191212154811647-1689217957.png)

**2、v-for中的key**

　　当 Vue.js 用 v-for 正在更新已渲染过的元素列表时，它默认用 “就地复用” 策略。如果数据项的顺序被改变，Vue将不是移动 DOM 元素来匹配数据项的顺序， 而是简单复用此处每个元素，并且确保它在特定索引下显示已被渲染过的每个元素。这个类似 Vue 1.x 的 track-by="$index" 。

　　这个默认的模式是有效的，但是只适用于不依赖子组件状态或临时 DOM 状态（例如：表单输入值）的列表渲染输出。

　　*为了给 Vue 一个提示，以便它能跟踪每个节点的身份，从而重用和重新排序现有元素，你需要为每项提供一个唯一 key 属性。理想的 key 值是每项都有唯一 id。这个特殊的属性相当于 Vue 1.x 的 track-by ，但它的工作方式类似于一个属性，所以你需要用 v-bind 来绑定动态值（在这里使用简写）：*

![img](https://img2018.cnblogs.com/common/1896185/201912/1896185-20191212154908217-775387165.png)

## 31、Vue 的核心思想是什么

**Vue核心思想：数据驱动、组件化**

一,数据驱动

传统的前端数据交互是用Ajax从服务端获取数据，然后操作DOM来改变视图；或者前端交互要改变数据时，又要再来一次上述步骤，而手动操作DOM是一个繁琐的过程且易出错。
Vue.js 是一个提供了 MVVM 风格的双向数据绑定的 Javascript 库，专注于View 层。它让开发者省去了操作DOM的过程，只需要改变数据。Vue会通过Dircetives指令，对DOM做一层封装，当数据发生改变会通知指令去修改对应的DOM，数据驱动DOM变化，DOM是数据的一种自然映射。
Vue还会对操作进行监听，当视图发生改变时，vue监听到这些变化，从而改变数据，这样就形成了数据的双向绑定。
Vue是一种MVVM框架。而DOM是数据的一个种自然映射。传统的模式是通过Ajax请求从model请求数据，然后手动的触发DOM传入数据修改页面。Vue中，Directives对view进行了封装，当model里的数据发生变化是，Vue就会通过Directives指令去修改DOM。同时也通过DOM Listener实现对视图view的监听，当DOM改变时，就会被监听到，实现model的改变，实现数据的双向绑定。

二,组件响应原理
数据（model）改变驱动视图（view）自动更新
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190530160840207.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTEyNzk4MQ==,size_16,color_FFFFFF,t_70)
当你把一个普通的 JavaScript 对象传给 Vue 实例的 data选项，Vue 将遍历此对象所有的属性，并使用 Object.defineProperty 把这些属性全部转为 getter/setter。Object.defineProperty 是 ES5 中一个无法 shim 的特性，这也就是为什么 Vue 不支持 IE8 以及更低版本浏览器的原因。
用户看不到 getter/setter，但是在内部它们让 Vue 追踪依赖，在属性被访问和修改时通知变化。这里需要注意的问题是浏览器控制台在打印数据对象时 getter/setter 的格式化并不同，所以你可能需要安装 vue-devtools 来获取更加友好的检查接口。
每个组件实例都有相应的 watcher 实例对象，它会在组件渲染的过程中把属性记录为依赖，之后当依赖项的setter被调用时，会通知watcher重新计算，从而致使它关联的组件得以更新。

三,组件化

扩展HTML元素，封装可重用的代码。每一个组件都对应一个ViewModel。页面上每个独立的可视/可交互区域都可以视为一个组件。每个组件对应一个工程目录，组件所需要的各种资源在这个目录下就进维护。页面是组件的容器，组件可以嵌套自由组合形成完整的页面。

组件化实现了扩展HTML元素，封装可用的代码。页面上每个独立的可视/可交互区域视为一个组件；每个组件对应一个工程目录，组件所需要的各种资源在这个目录下就近维护；页面不过是组件的容器，组件可以嵌套自由组合形成完整的页面。

## 32、Vue 等单页面应用的优缺点

先来说说什么是单页面应用和多页面应用：

​    **单页面应用（SPA）**，通俗一点说就是指只有一个主页面的应用，浏览器一开始要加载所有必须的 html, js, css。所有的页面内容都包含在这个所谓的主页面中。

​    **多页面（MPA）**，就是指一个应用中有多个页面，页面跳转时是整页刷新。

**单页面的优点和缺点：**

   **优点：**

​     1、用户体验好，快，内容的改变不需要重新加载整个页面，对服务器压力较小。

​     2、前后端分离，比如vue项目

​     3、完全的前端组件化，前端开发不再以页面为单位，更多地采用组件化的思想，代码结构和组织方式更加规范化，便于修改         和调整；

   **缺点：**

​    1、首次加载页面的时候需要加载大量的静态资源，这个加载时间相对比较长。

​    2、不利于 SEO优化，单页页面，数据在前端渲染，就意味着没有 SEO。

​    3、页面导航不可用，如果一定要导航需要自行实现前进、后退。（由于是单页面不能用浏览器的前进后退功能，所以需要自        己建立堆栈管理）

## 33、Vue-router 使用params与query传参有什么区别

首先简单来说明一下`$router`和`$route`的区别

```
//$router : 是路由操作对象，只写对象
//$route : 路由信息对象，只读对象

//操作 路由跳转
this.$router.push({
      name:'hello',
      params:{
          name:'word',
          age:'11'
     }
})

//读取 路由参数接收
this.name = this.$route.params.name;
this.age = this.$route.params.age;123456789101112131415
```

1·query传递参数

我看了很多人都说query传参要用path来引入，params传参要用name来引入，只是我测试了一下，query使用name来引入也可以传参，使用path也可以。如果有人知道原因可以告诉我一下，谢谢！

```
//query传参，使用name跳转
this.$router.push({
    name:'second',
    query: {
        queryId:'20180822',
        queryName: 'query'
    }
})

//query传参，使用path跳转
this.$router.push({
    path:'second',
    query: {
        queryId:'20180822',
        queryName: 'query'
    }
})

//query传参接收
this.queryName = this.$route.query.queryName;
this.queryId = this.$route.query.queryId;123456789101112131415161718192021
```

![这里写图片描述](https://img-blog.csdn.net/20180822153015319?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21mXzcxNzcxNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

最终不管是path引入还是name引入效果都一样如下图
![这里写图片描述](https://img-blog.csdn.net/20180822153035300?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21mXzcxNzcxNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

2·params传递参数

注：使用params传参只能使用name进行引入

- 使用params传参

```
//params传参 使用name
this.$router.push({
  name:'second',
  params: {
    id:'20180822',
     name: 'query'
  }
})

//params接收参数
this.id = this.$route.params.id ;
this.name = this.$route.params.name ;

//路由

{
path: '/second/:id/:name',
name: 'second',
component: () => import('@/view/second')
}1234567891011121314151617181920
```

![这里写图片描述](https://img-blog.csdn.net/20180822153133299?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21mXzcxNzcxNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

效果如下图
![这里写图片描述](https://img-blog.csdn.net/20180822153155350?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21mXzcxNzcxNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

需要注意的是：

1. params是路由的一部分,必须要在路由后面添加参数名。query是拼接在url后面的参数，没有也没关系。
2. params一旦设置在路由，params就是路由的一部分，如果这个路由有params传参，但是在跳转的时候没有传这个参数，会导致跳转失败或者页面会没有内容。

如果路由后面没有 /:id/:name效果如下图，地址栏没有参数
![这里写图片描述](https://img-blog.csdn.net/20180822153257738?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21mXzcxNzcxNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

但是如果你刷新一下，就会发现页面失败，效果如下图
![这里写图片描述](https://img-blog.csdn.net/20180822153330256?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21mXzcxNzcxNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

因此我们不可能让用户不要刷新，所以我们必须在路由后面加上 /:id/:name

- 如果使用path进行传参

```
//params传参 使用path
this.$router.push({
  path:'second',
   params: {
    id:'20180822',
     name: 'query'
  }
})

//params接收参数
this.id = this.$route.params.id ;
this.name = this.$route.params.name ;123456789101112
```

效果如下图

使用path传参什么效果都没有。

![这里写图片描述](https://img-blog.csdn.net/20180822153539928?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21mXzcxNzcxNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

3.总结

1. 传参可以使用params和query两种方式。
2. 使用params传参只能用name来引入路由，即push里面只能是name:’xxxx’,不能是path:’/xxx’,因为params只能用name来引入路由，如果这里写成了path，接收参数页面会是undefined！！！。
3. 使用query传参使用path来引入路由。
4. params是路由的一部分,必须要在路由后面添加参数名。query是拼接在url后面的参数，没有也没关系。
5. 二者还有点区别，直白的来说query相当于get请求，页面跳转的时候，可以在地址栏看到请求参数，而params相当于post请求，参数不会再地址栏中显示。

## 34、**子组件像父组件传递事件？**

$emit方法

## 35、Vue中 `<keep-alive>` 的作用

**1.keep-alive的作用以及好处**

在做电商有关的项目中，当我们第一次进入列表页需要请求一下数据，当我从列表页进入详情页，详情页不缓存也需要请求下数据，然后返回列表页，这时候我们使用keep-alive来缓存组件，防止二次渲染，这样会大大的节省性能。

**2.keep-alive的基本用法**

![img](https:////upload-images.jianshu.io/upload_images/19330661-5096303c5403084e.png?imageMogr2/auto-orient/strip|imageView2/2/w/850/format/webp)

需要缓存的组件内容直接在router中添加：

![img](https:////upload-images.jianshu.io/upload_images/19330661-adb1951e31ed4ff0.png?imageMogr2/auto-orient/strip|imageView2/2/w/812/format/webp)

**3.keep-alive的生命周期**

当引入keep-alive的时候，页面第一次进入，钩子的触发顺序created-> mounted-> activated，退出时触发deactivated。当再次进入（前进或者后退）时，只触发activated。

 


## 36、**如何让CSS只在当前组件中起作用？**

在组件中的style前面加上scoped

## 37、**如何获取dom?**

ref="domName" 用法：this.$refs.domName

## 38、**说出几种vue当中的指令和它的用法？**
：v-model双向数据绑定；
v-for循环；
v-if v-show 显示与隐藏；
v-on事件；v-once: 只绑定一次。



##  39、Vue Router的钩子函数有哪些？他们有哪些参数？

全局的：beforeEach、beforeResolve、afterEach
路由的：beforeEnter
组件的：beforeRouteEnter、beforeRouteUpdate、beforeRouteLeave
参数：to、from、next；正对不同的钩子函数参数有所差异。

## 40、说明一下封装vue组件的原则和方法

页面尽量不要封装为组件，只将一些复用率高的部分封装为组件就行。例如新增或修改信息的弹框，七八个页面都要用，自然有理由封装为组件。

并且要高内聚低耦合，说白了万万不可依赖于某一个或某几个页面的逻辑

## 41、简述一下$nextTick的用法

1. dom更新：在vue中，你修改了data的某一个值，并不会立即反应到该ele中。vue将你对data的更 改放到watcher的一个对列中（异步），只有在当前任务空闲时才会去执行watcher队列任务。这就有一个延迟时间了。
2. 当执行到$nextTick的时候，这是一个异步事件，他也会把这个事件放到一个队列当中，异步事件是 不会立即执行的代码，会被js处理器放到一个队列里，按照队列的顺序优先级等一个个按次序执行， 新添加的事件都会放在队列末尾。所以，当第一个也就是data的修改执行渲染在页面之后，这个时候执行$nextTick，就肯定能获取dom的东西la。
3. 同理也是，创建一个setTimeout，他也会放到队列中，当上一个事件执行完之后，才会这个他这个事件，才会执行他里面的回调，也就能成功获取啦。

## 42、 **vue-loader是什么？使用它的用途有哪些？**

vue文件的一个加载器，将template/js/style转换成js模块。
用途：js可以写es6、style样式可以scss或less、template可以加jade等

## 43、**为什么使用key?**

需要使用key来给每个节点做一个唯一标识，Diff算法就可以正确的识别此节点。
作用主要是为了高效的更新虚拟DOM。

## 44、**axios及安装?**

请求后台资源的模块。npm install axios --save装好，
js中使用import进来，然后.get或.post。返回在.then函数中如果成功，失败则是在.catch函数中。

## 45、**分别简述computed和watch的使用场景**

omputed:
　　　　当一个属性受多个属性影响的时候就需要用到computed
　　　　最典型的栗子： 购物车商品结算的时候
watch:
　　　　当一条数据影响多条数据的时候就需要用watch
　　　　栗子：搜索数据

## 46、**v-on可以监听多个方法吗？**

可以， <input type="text" v-on="{ input:onInput,focus:onFocus,blur:onBlur, }">。

## 47、Vue-loader解释一下

```
1.vue-loader是webpack的加载器，允许以单文件组件（SFC）的格式创作Vue组件
2.允许对Vue组件的每个部分使用其他webpack加载器
3.允许.vue文件中的自定义块可以应用自定义加载程序链，简单来说就是可以解析.vue文件
4.处理在模块依赖项中引用的静态资源
5.模拟每个组件的范围CSS
6.在开发过程中保持热加载
```

## 48、**$nextTick的使用**

当你修改了data的值然后马上获取这个dom元素的值，是不能获取到更新后的值，
你需要使用$nextTick这个回调，让修改后的data值渲染更新到dom元素之后在获取，才能成功。

## 49、用过插槽吗？用的是具名插槽还是匿名插槽

一个通俗点的回答就是 父组件的数据和子组件的模板配合起来使用就是内容分发 

内容分发其实和一个叫做插槽的概念有关 slot关键字

这里有三种插槽 我们就来一一讲解 

1.匿名插槽 又叫做默认插槽 就是没有名字

我还是直接上代码 更直接点

1.1.在父组件中  子组件引用的中间也有内容 有代码 父组件可以决定里面html代码的样式 还有数据内容

<h2>这里是父组件</h2>

<child>

         <div>

​        <h2>hello</h2>

​        <h2vue.js</h2>

​     </div>

</child>

1.1. 子组件  使用 slot 插槽标签 可以上父组件传来的内容进行展示 至于在哪里展示 由子组件决定

<template>

    <div>

​    <h2>这里是子组件</h2>

​    <slot ></slot>

  </div>

</template>

![img](https://img-blog.csdnimg.cn/20200210195614592.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l1bmNob25nX3poYW8=,size_16,color_FFFFFF,t_70)

这是在页面上的显示效果

2.具名插槽  这个和匿名插槽 区别就是这个是由名字 在标签上 加一个slot 属性 起一个自定义的名字

2.1 在父组件

<h2>这里是父组件</h2>

<child>

         <div>

​        <h2>hello</h2>

​        <h2vue.js</h2>

​       <h2 slot="react" style="color:#f00">react.js</h2>   //我们还给他改变了颜色

​     </div>

</child>

2.2. 在子组件中  引用的时候 我们 需要在 slot标签上加上 一个 name属性 名字 就是我们在父组件中自定义的那个名字

<template>

    <div>

​    <h2>这里是子组件</h2>

​    <slot ></slot>  // 没有名字的都会被收到这个匿名插槽中间

   <slot name="react"></slot>

  </div>

</template>

![img](https://img-blog.csdnimg.cn/20200210200128487.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l1bmNob25nX3poYW8=,size_16,color_FFFFFF,t_70)

最后也确实 显示 这就是 具名插槽的作用 注意 没有其名字的插槽 都会被 匿名插槽所接收 

3.作用域插槽 这下和具名插槽有俩点不同  功能快和具名插槽作用相反了

3.1 是父组件引用子组件中的数据 

3.2 使用slot-scope 进行数据的传递

子组件中 将数据传递给父组件

<template>

    <div>

​    <h2>这里是子组件</h2>

​     <slot :data="data"></slot>  // 有点和 props 传递参数有点像是吧

  </div>

</template>

data(){

​      return {

​        data:['tom','jack','rose','tony','stark']

​      }

}

在父组件中

<child>

​     <template slot-scope="user">  // 这里自定义一个变量 我们传的数据会到这个对象下面 使用的时候是 user.data                                       // data就是好我们传过来的数据

                <div class="tmp">

​          <span v-for="item in user.data" :key="item">{{item}}</span>

​        </div>

​      </template>

</child>

同时我们在给设置一个弹性布局

.tmp{

​    display: flex;

​    justify-content: space-around;

  }

最后页面显示效果如下

![img](https://img-blog.csdnimg.cn/20200210201301520.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l1bmNob25nX3poYW8=,size_16,color_FFFFFF,t_70)

<child>  // 如果我们不用 slot-scope进行接受的话 子组件的作用域插槽 就会变成了 普通的 匿名插槽了

​     hello world

</child> 

![img](https://img-blog.csdnimg.cn/2020021020143322.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l1bmNob25nX3poYW8=,size_16,color_FFFFFF,t_70)

## 50、**渐进式框架的理解**

主张最少；可以根据不同的需求选择不同的层级；

## 51、**Vue中双向数据绑定是如何实现的？**

vue 双向数据绑定是通过 数据劫持 结合 发布订阅模式的方式来实现的， 也就是说数据和视图同步，数据发生变化，视图跟着变化，视图变化，数据也随之发生改变；
核心：关于VUE双向数据绑定，其核心是 Object.defineProperty()方法。

## 52、**单页面应用和多页面应用区别及优缺点**

单页面应用（SPA），通俗一点说就是指只有一个主页面的应用，浏览器一开始要加载所有必须的 html, js, css。所有的页面内容都包含在这个所谓的主页面中。但在写的时候，还是会分开写（页面片段），然后在交互的时候由路由程序动态载入，单页面的页面跳转，仅刷新局部资源。多应用于pc端。
多页面（MPA），就是指一个应用中有多个页面，页面跳转时是整页刷新
单页面的优点：
用户体验好，快，内容的改变不需要重新加载整个页面，基于这一点spa对服务器压力较小；前后端分离；页面效果会比较炫酷（比如切换页面内容时的专场动画）。
单页面缺点：
不利于seo；导航不可用，如果一定要导航需要自行实现前进、后退。（由于是单页面不能用浏览器的前进后退功能，所以需要自己建立堆栈管理）；初次加载时耗时多；页面复杂度提高很多。

## 53、**v-if和v-for的优先级**

当 v-if 与 v-for 一起使用时，v-for 具有比 v-if 更高的优先级，这意味着 v-if 将分别重复运行于每个 v-for 循环中。所以，不推荐v-if和v-for同时使用。
如果v-if和v-for一起用的话，vue中的的会自动提示v-if应该放到外层去。

## 54、**assets和static的区别**

相同点：assets和static两个都是存放静态资源文件。项目中所需要的资源文件图片，字体图标，样式文件等都可以放在这两个文件下，这是相同点
不相同点：assets中存放的静态资源文件在项目打包时，也就是运行npm run build时会将assets中放置的静态资源文件进行打包上传，所谓打包简单点可以理解为压缩体积，代码格式化。而压缩后的静态资源文件最终也都会放置在static文件中跟着index.html一同上传至服务器。static中放置的静态资源文件就不会要走打包压缩格式化等流程，而是直接进入打包好的目录，直接上传至服务器。因为避免了压缩直接进行上传，在打包时会提高一定的效率，但是static中的资源文件由于没有进行压缩等操作，所以文件的体积也就相对于assets中打包后的文件提交较大点。在服务器中就会占据更大的空间。
建议：将项目中template需要的样式文件js文件等都可以放置在assets中，走打包这一流程。减少体积。而项目中引入的第三方的资源文件如iconfoont.css等文件可以放置在static中，因为这些引入的第三方文件已经经过处理，我们不再需要处理，直接上传。

## 55、**vue常用的修饰符**

.stop：等同于JavaScript中的event.stopPropagation()，防止事件冒泡；
.prevent：等同于JavaScript中的event.preventDefault()，防止执行预设的行为（如果事件可取消，则取消该事件，而不停止事件的进一步传播）；
.capture：与事件冒泡的方向相反，事件捕获由外到内；
.self：只会触发自己范围内的事件，不包含子元素；
.once：只会触发一次。

## 56、请你说一下vue中create和mount的区别



| 生命周期     | 是否获取dom节点 | 是否可以获取data | 是否获取methods |
| ------------ | --------------- | ---------------- | --------------- |
| beforeCreate | 否              | 否               | 否              |
| created      | 否              | 是               | 是              |
| beforeMount  | 否              | 是               | 是              |
| mounted      | 是              | 是               | 是              |

以我的个人理解，**vue生命周期**实际上和**浏览器渲染过程**是挂钩的，

在beforecreate阶段，对浏览器来说，整个渲染流程尚未开始或者说准备开始，对vue来说，实例尚未被初始化，data observer和 event/watcher也还未被调用，在此阶段，对data、methods或文档节点的调用现在无法得到正确的数据。

在created阶段，对浏览器来说，渲染整个HTML文档时,dom节点、css规则树与js文件被解析后，但是没有进入被浏览器render过程，上述资源是尚未挂载在页面上，也就是在vue生命周期中对应的created
阶段，实例已经被初始化，但是还没有挂载至**$el**上，所以我们无法获取到对应的节点，但是此时我们是可以获取到vue中data与methods中的数据的

在beforeMount阶段，实际上与created阶段类似，节点尚未挂载，但是依旧可以获取到data与methods中的数据。

在mounted阶段，对浏览器来说，已经完成了dom与css规则树的render，并完成对render tree进行了布局，而浏览器收到这一指令，调用渲染器的paint（）在屏幕上显示，而对于vue来说，在mounted阶段，vue的**template成功挂载在$el中**，此时一个完整的页面已经能够显示在浏览器中，所以在这个阶段，即可以调用节点了（关于这一点，在笔者测试中，在mounted方法中打断点然后run，依旧能够在浏览器中看

## 57、子组件中是否可以修改props，如果想修改的话如何修改 

.sync是vue中用于实现简单的“双向绑定”的语法糖，在平时的开发中是非常使用的。

vue的prop是单向下行绑定：父级的prop的更新会向下流动到子组件中，但是反过来不行。可是有些情况，我们需要对prop进行“双向绑定”。这个时候，就可以用.sync来解决

**.sync用法**

```
<text-document :title.sync="doc.title"></text-document>

当子组件需要更新 title 的值时，它需要显式地触发一个更新事件：
this.$emit('update:title', newValue)
```

这样title的属性在子组件内部更新，父组件也能感知的到，实现了“双向绑定”。

**.sync应运实例**

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<template>
    <div class="details">
        <myComponent :show.sync='valueChild' style="padding: 30px 20px 30px 5px;border:1px solid #ddd;margin-bottom: 10px;"></myComponent>
        <button @click="changeValue">toggle</button>
    </div>
</template>
<script>
import Vue from 'vue'
Vue.component('myComponent', {
      template: `<div v-if="show">
                    <p>默认初始值是{{show}}，所以是显示的</p>
                    <button @click.stop="closeDiv">关闭</button>
                 </div>`,
      props:['show'],
      methods: {
        closeDiv() {
          this.$emit('update:show', false); //触发 input 事件，并传入新值
        }
      }
})
export default{
    data(){
        return{
            valueChild:true,
        }
    },
    methods:{
        changeValue(){
            this.valueChild = !this.valueChild
        }
    }
}
</script>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

如果未触发事件 this.$emit('update:show', false); 则外部感知不到子组件内部对show的改变，依然认为此事的值是true，导致弹框点击打开一次之后，后面再不会打开。

## 58、Object.defineProperty有什么缺点

1. Object.defineProperty只能劫持对象的属性，从而需要对每个对象，每个属性进行遍历，如果，属性值是对象，还需要深度遍历。Proxy可以劫持整个对象，并返回一个新的对象。
2. Proxy不仅可以代理对象，还可以代理数组。还可以代理动态增加的属性。

Object.defineProperty本身有一定的监控到数组下标变化的能力：
Object.defineProperty本身是可以监控到数组下标的变化的，但是在 Vue 中，从性能/体验的性价比考虑，尤大大就弃用了这个特性。具体我们可

## 59、**引进组件的步骤**

 在template中引入组件；
在script的第一行用import引入路径；
用component中写上组件名称。

## 60、**delete和Vue.delete删除数组的区别**

delete只是被删除的元素变成了 empty/undefined 其他的元素的键值还是不变。Vue.delete 直接删除了数组 改变了数组的键值。

## 61、**SPA首屏加载慢如何解决**

安装动态懒加载所需插件；使用CDN资源。

## 62、**Vue-router跳转和location.href有什么区别**

使用location.href='/url'来跳转，简单方便，但是刷新了页面；
使用history.pushState('/url')，无刷新页面，静态跳转；
引进router，然后使用router.push('/url')来跳转，使用了diff算法，实现了按需加载，减少了dom的消耗。
其实使用router跳转和使用history.pushState()没什么差别的，因为vue-router就是用了history.pushState()，尤其是在history模式下。

## 63、**vue slot**

简单来说，假如父组件需要在子组件内放一些DOM，那么这些DOM是显示、不显示、在哪个地方显示、如何显示，就是slot分发负责的活。

## 64、如果你是leader，做管理系统项目 Vue和React 怎么选择

Angular的极限面向对象处理复杂业务非常舒适，但是对于轮子兄弟极其不友好，没有CDK你就只能抓瞎，一句话：高楼大厦，攀登不易。

React的Hooks有很强的Hacky精神，零星几个API，绝大部分需求皆可实现，一句话：步步为营，跬步千里。

Vue的API数量适中，基础设施完善，文档友好，概念与Angular和React都互通，一句话：百家之长，指哪打哪。

 

## 65、Vuex的缺点

如果您不打算开发大型单页应用，使用 Vuex 可能是繁琐冗余的。确实是如此——如果您的应用够简单，您最好不要使用 Vuex。一个简单的 store 模式就足够您所需了。

## 66、Vue和React区别

react和vue都是做组件化的，整体的功能都类似，但是他们的设计思路是有很多不同的。使用react和vue，主要是理解他们的设计思路的不同。

1.数据是不是可变的

react整体是函数式的思想，把组件设计成纯组件，状态和逻辑通过参数传入，所以在react中，是单向数据流，推崇结合immutable来实现数据不可变。react在setState之后会重新走渲染的流程，如果shouldComponentUpdate返回的是true，就继续渲染，如果返回了false，就不会重新渲染，PureComponent就是重写了shouldComponentUpdate，然后在里面作了props和state的浅层对比。



![img](https:////upload-images.jianshu.io/upload_images/5077517-34c55a3e98d33edf.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

image.png



而vue的思想是响应式的，也就是基于是数据可变的，通过对每一个属性建立Watcher来监听，当属性变化的时候，响应式的更新对应的虚拟dom。



![img](https:////upload-images.jianshu.io/upload_images/5077517-1a1ca8cf8e576bcd.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

image.png

总之，react的性能优化需要手动去做，而vue的性能优化是自动的，但是vue的响应式机制也有问题，就是当state特别多的时候，Watcher也会很多，会导致卡顿，所以大型应用（状态特别多的）一般用react，更加可控。

2.通过js来操作一切，还是用各自的处理方式

react的思路是all in js，通过js来生成html，所以设计了jsx，还有通过js来操作css，社区的styled-component、jss等，



![img](https:////upload-images.jianshu.io/upload_images/5077517-b7caaac227d1f172.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

image.png

vue是把html，css，js组合到一起，用各自的处理方式，vue有单文件组件，可以把html、css、js写到一个文件中，html提供了模板引擎来处理。



![img](https:////upload-images.jianshu.io/upload_images/5077517-543afa129266ecc6.png?imageMogr2/auto-orient/strip|imageView2/2/w/832/format/webp)

image.png

3.类式的组件写法，还是声明式的写法

react是类式的写法，api很少，



![img](https:////upload-images.jianshu.io/upload_images/5077517-be610fb17260fa7d.png?imageMogr2/auto-orient/strip|imageView2/2/w/928/format/webp)

image.png



而vue是声明式的写法，通过传入各种options，api和参数都很多。所以react结合typescript更容易一起写，vue稍微复杂。



![img](https:////upload-images.jianshu.io/upload_images/5077517-cf0c138886c56b57.png?imageMogr2/auto-orient/strip|imageView2/2/w/624/format/webp)

image.png


 vue结合vue-class-component也可以实现类式的写法，但是还是需要通过decorator来添加声明，并不纯粹。

![img](https:////upload-images.jianshu.io/upload_images/5077517-c6ea036a3896c03c.png?imageMogr2/auto-orient/strip|imageView2/2/w/1006/format/webp)

image.png

react可以通过高阶组件（Higher Order Components--HOC）来扩展，而vue需要通过mixins来扩展
 一个react高阶组件的例子：

![img](https:////upload-images.jianshu.io/upload_images/5077517-c6022362e91df571.png?imageMogr2/auto-orient/strip|imageView2/2/w/1128/format/webp)

image.png



![img](https:////upload-images.jianshu.io/upload_images/5077517-ea08ddfbb3fff521.png?imageMogr2/auto-orient/strip|imageView2/2/w/1150/format/webp)

image.png


 vue的mixin的例子：

![img](https:////upload-images.jianshu.io/upload_images/5077517-b5847b591a52f74f.png?imageMogr2/auto-orient/strip|imageView2/2/w/1084/format/webp)

image.png


 React刚开始也有mixin的写法，通过React.createClass的api，不过现在很少用了。
 Vue也不是不能实现高阶组件，只是特别麻烦，因为Vue对与组件的option做了各种处理，想实现高阶组件就要知道每一个option是怎么处理的，然后正确的设置。具体有多复杂，可以参考下面的文章。
[http://hcysun.me/2018/01/05/%E6%8E%A2%E7%B4%A2Vue%E9%AB%98%E9%98%B6%E7%BB%84%E4%BB%B6/](http://hcysun.me/2018/01/05/探索Vue高阶组件/)



4.什么功能内置，什么交给社区去做

react做的事情很少，很多都交给社区去做，vue很多东西都是内置的，写起来确实方便一些，
 比如 redux的combineReducer就对应vuex的modules，
 比如reselect就对应vuex的getter和vue组件的computed，
 vuex的mutation是直接改变的原始数据，而redux的reducer是返回一个全新的state，所以redux结合immutable来优化性能，vue不需要。

总结

上面主要梳理了react和vue的4点不同：

1. 数据是不是可变的
2. 通过js操作一切还是各自的处理方式
3. 类式的组件写法还是声明式的写法
4. 什么功能内置，什么交给社区去做

（其中第3点在vue3.0支持类式写法之后就可以去掉了）

react整体的思路就是函数式，所以推崇纯组件，数据不可变，单向数据流，当然需要双向的地方也可以做到，比如结合redux-form，而vue是基于可变数据的，支持双向绑定。react组件的扩展一般是通过高阶组件，而vue组件会使用mixin。vue内置了很多功能，而react做的很少，很多都是由社区来完成的，vue追求的是开发的简单，而react更在乎方式是否正确。

![img](https:////upload-images.jianshu.io/upload_images/5077517-0d4da429fe17906b.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

image.png



![img](https:////upload-images.jianshu.io/upload_images/5077517-0e27af48aa00f491.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

 

## 67、**你们vue项目是打包了一个js文件，一个css文件，还是有多个文件？**

根据vue-cli脚手架规范，一个js文件，一个CSS文件。

## 68、**Vue里面router-link在电脑上有用，在安卓上没反应怎么解决？**

Vue路由在Android机上有问题，babel问题，安装babel polypill插件解决。

## 69、**Vue2中注册在router-link上事件无效解决方法**

使用@click.native。原因：router-link会阻止click事件，.native指直接监听一个原生事件。

## 70、**RouterLink在IE和Firefox中不起作用（路由不跳转）的问题**

方法一：只用a标签，不适用button标签；方法二：使用button标签和Router.navigate方法

## 71、**axios的特点有哪些**

浏览器中创建XMLHttpRequests；
node.js创建http请求；
支持Promise API；
拦截请求和响应；
转换请求数据和响应数据；
取消请求；
自动换成json。
axios中的发送字段的参数是data跟params两个，两者的区别在于params是跟请求地址一起发送的，data的作为一个请求体进行发送
params一般适用于get请求，data一般适用于post put 请求。

## 72、Vue深层次嵌套传值方法

vuex

## 73、**请说下封装 vue 组件的过程？**

\1. 建立组件的模板，先把架子搭起来，写写样式，考虑好组件的基本逻辑。(os：思考1小时，码码10分钟，程序猿的准则。)
　　2. 准备好组件的数据输入。即分析好逻辑，定好 props 里面的数据、类型。
　　3. 准备好组件的数据输出。即根据组件逻辑，做好要暴露出来的方法。
　　4. 封装完毕了，直接调用即可

## 74、**params和query的区别**

用法：query要用path来引入，params要用name来引入，接收参数都是类似的，分别是this.$route.query.name和this.$route.params.name。
url地址显示：query更加类似于我们ajax中get传参，params则类似于post，说的再简单一点，前者在浏览器地址栏中显示参数，后者则不显示
注意点：query刷新不会丢失query里面的数据
params刷新 会 丢失 params里面的数据。

## 75、**vue初始化页面闪动问题**

使用vue开发时，在vue初始化之前，由于div是不归vue管的，所以我们写的代码在还没有解析的情况下会容易出现花屏现象，看到类似于{{message}}的字样，虽然一般情况下这个时间很短暂，但是我们还是有必要让解决这个问题的。
首先：在css里加上[v-cloak] {
display: none;
}。
如果没有彻底解决问题，则在根元素加上style="display: none;" :style="{display: 'block'}"

## 76、**vue更新数组时触发视图更新的方法**

push()；pop()；shift()；unshift()；splice()； sort()；reverse()

## 77、**vue常用的UI组件库**

Mint UI，element，VUX

## 78、**vue修改打包后静态资源路径的修改**

cli2版本：将 config/index.js 里的 assetsPublicPath 的值改为 './' 。
build: {
...
assetsPublicPath: './',
...
}
cli3版本：在根目录下新建vue.config.js 文件，然后加上以下内容：（如果已经有此文件就直接修改）
module.exports = {
publicPath: '', // 相对于 HTML 页面（目录相同） }
生命周期函数面试题

## 79、**什么是 vue 生命周期？有什么作用？**

每个 Vue 实例在被创建时都要经过一系列的初始化过程——例如，需要设置数据监听、编译模板、将实例挂载到 DOM 并在数据变化时更新 DOM 等。同时在这个过程中也会运行一些叫做 生命周期钩子 的函数，这给了用户在不同阶段添加自己的代码的机会。（ps：生命周期钩子就是生命周期函数）例如，如果要通过某些插件操作DOM节点，如想在页面渲染完后弹出广告窗， 那我们最早可在mounted 中进行。

## 80、**第一次页面加载会触发哪几个钩子？**

beforeCreate， created， beforeMount， mounted

## 81 **简述每个周期具体适合哪些场景**

beforeCreate：在new一个vue实例后，只有一些默认的生命周期钩子和默认事件，其他的东西都还没创建。在beforeCreate生命周期执行的时候，data和methods中的数据都还没有初始化。不能在这个阶段使用data中的数据和methods中的方法
create：data 和 methods都已经被初始化好了，如果要调用 methods 中的方法，或者操作 data 中的数据，最早可以在这个阶段中操作
beforeMount：执行到这个钩子的时候，在内存中已经编译好了模板了，但是还没有挂载到页面中，此时，页面还是旧的
mounted：执行到这个钩子的时候，就表示Vue实例已经初始化完成了。此时组件脱离了创建阶段，进入到了运行阶段。 如果我们想要通过插件操作页面上的DOM节点，最早可以在和这个阶段中进行
beforeUpdate： 当执行这个钩子时，页面中的显示的数据还是旧的，data中的数据是更新后的， 页面还没有和最新的数据保持同步
updated：页面显示的数据和data中的数据已经保持同步了，都是最新的
beforeDestory：Vue实例从运行阶段进入到了销毁阶段，这个时候上所有的 data 和 methods ， 指令， 过滤器 ……都是处于可用状态。还没有真正被销毁
destroyed： 这个时候上所有的 data 和 methods ， 指令， 过滤器 ……都是处于不可用状态。组件已经被销毁了。

## 82、Vue $forceUpdate的原理 

调用强制更新方法this.$forceUpdate()会更新视图和数据，触发updated生命周期。

```
<button @click="reload()">强制更新</button>
updated(){
      console.log("更新了");
  },
  methods:{
      reload(){
          this.$forceUpdate();
      }
  }
```

\-----------------------------------------------------------------

一个对象数组，我们尝试直接给某个item增加一个属性，发现页面上没有效果；直接将length变成0来清空数组，也没有效果

最好用$set方法，这样vue就可以知道发生了变化，同时vue也不建议直接修改length，可以给一个空数组来置空。

this.$set(this.list[index],'sex','男');

this.list=[];

可是如果我们不想利用$set去设置，非要按照我们第一种方式去写，可以实现么？答案是可以的，就是利用$forceUpdate了

1. this.list[index].sex = '男';
2.   this.$forceUpdate();  

\-----------------------------------------------------------------------

$children表示当前页面的组件个数

$children表示当前页面的组件个数，但是如果直接在页面上引用{{$children}}，往往得不到正确的结果：

这个和vue内部的实现机制有关，（vue不同版本可能不一定有此问题），可以看到，当我们用$forceUpdate之后，就会得到正确的结果。当然，一般我们也不会这么去使用，我们可以在data里面定义一个变量，在mounted之后给变量赋值this.$children.length；或者直接在mounted之后使用$forceUpdate强制刷新。

\-------------------------------------------------------------------------

网友推荐用途：

VUE项目中使用this.$forceUpdate();解决页面v-for中修改item属性值后页面v-if不改变的问题

## 83、**vue获取数据在哪个周期函数**

一般 created/beforeMount/mounted 皆可.
比如如果你要操作 DOM , 那肯定 mounted 时候才能操作.

## 84、**请详细说下你对vue生命周期的理解？**

总共分为8个阶段创建前/后，载入前/后，更新前/后，销毁前/后。
创建前/后： 在beforeCreated阶段，vue实例的挂载元素$el和**数据对象**data都为undefined，还未初始化。在created阶段，vue实例的数据对象data有了，$el还没有。
载入前/后：在beforeMount阶段，vue实例的$el和data都初始化了，但还是挂载之前为虚拟的dom节点，data.message还未替换。在mounted阶段，vue实例挂载完成，data.message成功渲染。
更新前/后：当data变化时，会触发beforeUpdate和updated方法。
销毁前/后：在执行destroy方法后，对data的改变不会再触发周期函数，说明此时vue实例已经解除了事件监听以及和dom的绑定，但是dom结构依然存在。

## 85、**mvvm 框架是什么？**

vue是实现了双向数据绑定的mvvm框架，当视图改变更新模型层，当模型层改变更新视图层。在vue中，使用了双向绑定技术，就是View的变化能实时让Model发生变化，而Model的变化也能实时更新到View。

## 86、**vue-router 是什么?它有哪些组件**

vue用来写路由一个插件。router-link、router-view

## 87、**active-class 是哪个组件的属性？**

vue-router模块的router-link组件。children数组来定义子路由

## 88、**Vue.js中ajax请求代码应该写在组件的methods中还是vuex的actions中？**

如果请求来的数据是不是要被其他组件公用，仅仅在请求的组件内使用，就不需要放入vuex 的state里。
如果被其他地方复用，这个很大几率上是需要的，如果需要，请将请求放入action里，方便复用



##  89、**vuex有哪几种属性？**

有五种，分别是 State、 Getter、Mutation 、Action、 Module
state => 基本数据(数据源存放地)
getters => 从基本数据派生出来的数据
mutations => 提交更改数据的方法，同步！
actions => 像一个装饰器，包裹mutations，使之可以异步。
modules => 模块化Vuex

## 90、**vue-router实现路由懒加载（ 动态加载路由 ）**

三种方式
第一种：vue异步组件技术 ==== 异步加载，vue-router配置路由 , 使用vue的异步组件技术 , 可以实现按需加载 .但是,这种情况下一个组件生成一个js文件。
第二种：路由懒加载(使用import)。
第三种：webpack提供的require.ensure()，vue-router配置路由，使用webpack的require.ensure技术，也可以实现按需加载。这种情况下，多个路由指定相同的chunkName，会合并打包成一个js文件。
vuex常见面试题

## 91、**vuex是什么？怎么使用？哪种功能场景使用它？**

vue框架中状态管理。在main.js引入store，注入。
新建了一个目录store.js，….. export 。
场景有：单页应用中，组件之间的状态。音乐播放、登录状态、加入购物车

## 92、**vue优点？**

轻量级框架：只关注视图层，是一个构建数据的视图集合，大小只有几十kb；
简单易学：国人开发，中文文档，不存在语言障碍 ，易于理解和学习；
双向数据绑定：保留了angular的特点，在数据操作方面更为简单；
组件化：保留了react的优点，实现了html的封装和重用，在构建单页面应用方面有着独特的优势；
视图，数据，结构分离：使数据的更改更为简单，不需要进行逻辑代码的修改，只需要操作数据就能完成相关操作；
虚拟DOM：dom操作是非常耗费性能的， 不再使用原生的dom操作节点，极大解放dom操作，但具体操作的还是dom不过是换了另一种方式；
运行速度更快:相比较与react而言，同样是操作虚拟dom，就性能而言，vue存在很大的优势。

## 93、NextTick 是做什么的

```
$nextTick` 是在下次 `DOM` 更新循环结束之后执行延迟回调，在修改数据之后使用 `$nextTick`，则可以在回调中获取更新后的 `DOM
```

## 94、Class 与 Style 如何动态绑定？

Class 可以通过对象语法和数组语法进行动态绑定：

- 对象语法：

```text
 <div v-bind:class="{ active: isActive, 'text-danger': hasError }"></div>  
data: {   
  isActive: true, 
  hasError: false 
} 
```

- 数组语法：

```text
 <div v-bind:class="[isActive ? activeClass : '', errorClass]"></div> 
 data: {   
    activeClass: 'active', 
    errorClass: 'text-danger'
 } 
```


Style 也可以通过对象语法和数组语法进行动态绑定：

- 对象语法：

```text
 <div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>  
data: {
   activeColor: 'red',
   fontSize: 30 } 
```

- 数组语法：

```text
 <div v-bind:style="[styleColor, styleSize]"></div>
  data: {
   styleColor: {
      color: 'red' 
   }, 
  styleSize:{ 
     fontSize:'23px'
   }
 } 
```

## 95、直接给一个数组项赋值，Vue 能检测到变化吗？

由于 JavaScript 的限制，Vue 不能检测到以下数组的变动：

- 当你利用索引直接设置一个数组项时，例如：`vm.items[indexOfItem] = newValue`
- 当你修改数组的长度时，例如：`vm.items.length = newLength`

为了解决第一个问题，Vue 提供了以下操作方法：

```text
 // Vue.set 
Vue.set(vm.items, indexOfItem, newValue)
 // vm.$set，Vue.set的一个别名 
vm.$set(vm.items, indexOfItem, newValue)
 // Array.prototype.splice 
vm.items.splice(indexOfItem, 1, newValue) 
```


为了解决第二个问题，Vue 提供了以下操作方法：

```text
// Array.prototype.splice 
vm.items.splice(newLength) 
```


## 96、Vue 的父组件和子组件生命周期钩子函数执行顺序？

Vue 的父组件和子组件生命周期钩子函数执行顺序可以归类为以下 4 部分：

- 加载渲染过程
  父 beforeCreate -> 父 created -> 父 beforeMount -> 子 beforeCreate -> 子 created -> 子 beforeMount -> 子 mounted -> 父 mounted
- 子组件更新过程
  父 beforeUpdate -> 子 beforeUpdate -> 子 updated -> 父 updated
- 父组件更新过程
  父 beforeUpdate -> 父 updated
- 销毁过程
  父 beforeDestroy -> 子 beforeDestroy -> 子 destroyed -> 父 destroyed

## 97、在哪个生命周期内调用异步请求？

可以在钩子函数 created、beforeMount、mounted 中进行调用，因为在这三个钩子函数中，data 已经创建，可以将服务端端返回的数据进行赋值。但是本人推荐在 created 钩子函数中调用异步请求，因为在 created 钩子函数中调用异步请求有以下优点：

- 能更快获取到服务端数据，减少页面 loading 时间；
- ssr 不支持 beforeMount 、mounted 钩子函数，所以放在 created 中有助于一致性；

## 98、谈谈你对 keep-alive 的了解？

keep-alive 是 Vue 内置的一个组件，可以使被包含的组件保留状态，避免重新渲染 ，其有以下特性：

- 一般结合路由和动态组件一起使用，用于缓存组件；
- 提供 include 和 exclude 属性，两者都支持字符串或正则表达式， include 表示只有名称匹配的组件会被缓存，exclude 表示任何名称匹配的组件都不会被缓存 ，其中 exclude 的优先级比 include 高；
- 对应两个钩子函数 activated 和 deactivated ，当组件被激活时，触发钩子函数 activated，当组件被移除时，触发钩子函数 deactivated。





## 99、你使用过 Vuex 吗？



Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。每一个 Vuex 应用的核心就是 store（仓库）。“store” 基本上就是一个容器，它包含着你的应用中大部分的状态 ( state )。

（1）Vuex 的状态存储是响应式的。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。

（2）改变 store 中的状态的唯一途径就是显式地提交 (commit) mutation。这样使得我们可以方便地跟踪每一个状态的变化。
主要包括以下几个模块：

- State：定义了应用状态的数据结构，可以在这里设置默认的初始状态。
- Getter：允许组件从 Store 中获取数据，mapGetters 辅助函数仅仅是将 store 中的 getter 映射到局部计算属性。
- Mutation：是唯一更改 store 中状态的方法，且必须是同步函数。
- Action：用于提交 mutation，而不是直接变更状态，可以包含任意异步操作。
- Module：允许将单一的 Store 拆分为多个 store 且同时保存在单一的状态树中。

## 100、使用过 Vue SSR 吗？说说 SSR？



> Vue.js 是构建客户端应用程序的框架。默认情况下，可以在浏览器中输出 Vue 组件，进行生成 DOM 和操作 DOM。然而，也可以将同一个组件渲染为服务端的 HTML 字符串，将它们直接发送到浏览器，最后将这些静态标记"激活"为客户端上完全可交互的应用程序。
> 即：SSR大致的意思就是vue在客户端将标签渲染成的整个 html 片段的工作在服务端完成，服务端形成的html 片段直接返回给客户端这个过程就叫做服务端渲染。


**服务端渲染 SSR 的优缺点如下：**
**（1）服务端渲染的优点：**

- 更好的 SEO： 因为 SPA 页面的内容是通过 Ajax 获取，而搜索引擎爬取工具并不会等待 Ajax 异步完成后再抓取页面内容，所以在 SPA 中是抓取不到页面通过 Ajax 获取到的内容；而 SSR 是直接由服务端返回已经渲染好的页面（数据已经包含在页面中），所以搜索引擎爬取工具可以抓取渲染好的页面；
- 更快的内容到达时间（首屏加载更快）： SPA 会等待所有 Vue 编译后的 js 文件都下载完成后，才开始进行页面的渲染，文件下载等需要一定的时间等，所以首屏渲染需要一定的时间；SSR 直接由服务端渲染好页面直接返回显示，无需等待下载 js 文件及再去渲染等，所以 SSR 有更快的内容到达时间；

**（2) 服务端渲染的缺点：**

- 更多的开发条件限制： 例如服务端渲染只支持 beforCreate 和 created 两个钩子函数，这会导致一些外部扩展库需要特殊处理，才能在服务端渲染应用程序中运行；并且与可以部署在任何静态文件服务器上的完全静态单页面应用程序 SPA 不同，服务端渲染应用程序，需要处于 Node.js server 运行环境；
- 更多的服务器负载：在 Node.js 中渲染完整的应用程序，显然会比仅仅提供静态文件的 server 更加大量占用CPU 资源 (CPU-intensive - CPU 密集)，因此如果你预料在高流量环境 ( high traffic ) 下使用，请准备相应的服务器负载，并明智地采用缓存策略。



## 101、Vue 框架怎么实现对象和数组的监听？

如果被问到 Vue 怎么实现数据双向绑定，大家肯定都会回答 通过 Object.defineProperty() 对数据进行劫持，但是 Object.defineProperty() 只能对属性进行数据劫持，不能对整个对象进行劫持，同理无法对数组进行劫持，但是我们在使用 Vue 框架中都知道，Vue 能检测到对象和数组（部分方法的操作）的变化，那它是怎么实现的呢？我们查看相关代码如下：

```text
  /**
   * Observe a list of Array items.
   */
  observeArray (items: Array<any>) {
    for (let i = 0, l = items.length; i < l; i++) {
      observe(items[i])  // observe 功能为监测数据的变化
    }
  }

  /**
   * 对属性进行递归遍历
   */
  let childOb = !shallow && observe(val) // observe 功能为监测数据的变化
```


通过以上 Vue 源码部分查看，我们就能知道 Vue 框架是通过遍历数组 和递归遍历对象，从而达到利用 Object.defineProperty() 也能对对象和数组（部分方法的操作）进行监听。





## 102、Proxy 与 Object.defineProperty 优劣对比



**Proxy 的优势如下:**

- Proxy 可以直接监听对象而非属性；
- Proxy 可以直接监听数组的变化；
- Proxy 有多达 13 种拦截方法,不限于 apply、ownKeys、deleteProperty、has 等等是 Object.defineProperty 不具备的；
- Proxy 返回的是一个新对象,我们可以只操作新的对象达到目的,而 Object.defineProperty 只能遍历对象属性直接修改；
- Proxy 作为新标准将受到浏览器厂商重点持续的性能优化，也就是传说中的新标准的性能红利；

**Object.defineProperty 的优势如下:**

- 兼容性好，支持 IE9，而 Proxy 的存在浏览器兼容性问题,而且无法用 polyfill 磨平，因此 Vue 的作者才声明需要等到下个大版本( 3.0 )才能用 Proxy 重写。





## 103、介绍下vue单页面和多页面区别

单页面首次加载缓慢，但是后面的加载优势就会超过多页面了。而多页面则是首次加载快，但是后面每次都要进行加载。
vue这种还是比较适合做单页面的，你把webpack配置一下做多页面应该也可以，但是这样就丧失了vue的优势了。
建议如果不需要考虑太多的兼容性就还是用vue来做单页面吧。比如自己用的后台管理系统什么的。
如果要兼容到ie8什么的。。。还是再考虑下。



## 104、Vue 怎么用 vm.$set() 解决对象新增属性不能响应的问题 ？

受现代 JavaScript 的限制 ，Vue **无法检测到对象属性的添加或删除**。由于 Vue 会在初始化实例时对属性执行 getter/setter 转化，所以属性必须在 data 对象上存在才能让 Vue 将它转换为响应式的。但是 Vue 提供了 `Vue.set (object, propertyName, value) / vm.$set (object, propertyName, value)` 来实现为对象添加响应式属性，那框架本身是如何实现的呢？


我们查看对应的 Vue 源码：`vue/src/core/instance/index.js`

```text
export function set (target: Array<any> | Object, key: any, val: any): any {
  // target 为数组  
  if (Array.isArray(target) && isValidArrayIndex(key)) {
    // 修改数组的长度, 避免索引>数组长度导致splcie()执行有误
    target.length = Math.max(target.length, key)
    // 利用数组的splice变异方法触发响应式  
    target.splice(key, 1, val)
    return val
  }
  // key 已经存在，直接修改属性值  
  if (key in target && !(key in Object.prototype)) {
    target[key] = val
    return val
  }
  const ob = (target: any).__ob__
  // target 本身就不是响应式数据, 直接赋值
  if (!ob) {
    target[key] = val
    return val
  }
  // 对属性进行响应式处理
  defineReactive(ob.value, key, val)
  ob.dep.notify()
  return val
}
```


我们阅读以上源码可知，vm.$set 的实现原理是：

- 如果目标是数组，直接使用数组的 splice 方法触发相应式；
- 如果目标是对象，会先判读属性是否存在、对象是否是响应式，最终如果要对属性进行响应式处理，则是通过调用 defineReactive 方法进行响应式处理（ defineReactive 方法就是 Vue 在初始化对象时，给对象属性采用 Object.defineProperty 动态添加 getter 和 setter 的功能所调用的方法）



## 105、虚拟 DOM 的优缺点？



**优点：**

- **保证性能下限：** 框架的虚拟 DOM 需要适配任何上层 API 可能产生的操作，它的一些 DOM 操作的实现必须是普适的，所以它的性能并不是最优的；但是比起粗暴的 DOM 操作性能要好很多，因此框架的虚拟 DOM 至少可以保证在你不需要手动优化的情况下，依然可以提供还不错的性能，即保证性能的下限；
- **无需手动操作 DOM：** 我们不再需要手动去操作 DOM，只需要写好 View-Model 的代码逻辑，框架会根据虚拟 DOM 和 数据双向绑定，帮我们以可预期的方式更新视图，极大提高我们的开发效率；
- **跨平台：** 虚拟 DOM 本质上是 JavaScript 对象,而 DOM 与平台强相关，相比之下虚拟 DOM 可以进行更方便地跨平台操作，例如服务器渲染、weex 开发等等。

**缺点:**

- **无法进行极致优化：** 虽然虚拟 DOM + 合理的优化，足以应对绝大部分应用的性能需求，但在一些性能要求极高的应用中虚拟 DOM 无法进行针对性的极致优化。

## 106、虚拟 DOM 实现原理？

虚拟 DOM 的实现原理主要包括以下 3 部分：

- 用 JavaScript 对象模拟真实 DOM 树，对真实 DOM 进行抽象；
- diff 算法 — 比较两棵虚拟 DOM 树的差异；
- pach 算法 — 将两个虚拟 DOM 对象的差异应用到真正的 DOM 树。



## 107、Vue中key的作用  不加会怎么样



key 是为 Vue 中 vnode 的唯一标记，通过这个 key，我们的 diff 操作可以更准确、更快速。Vue 的 diff 过程可以概括为：oldCh 和 newCh 各有两个头尾的变量 oldStartIndex、oldEndIndex 和 newStartIndex、newEndIndex，它们会新节点和旧节点会进行两两对比，即一共有4种比较方式：newStartIndex 和oldStartIndex 、newEndIndex 和 oldEndIndex 、newStartIndex 和 oldEndIndex 、newEndIndex 和 oldStartIndex，如果以上 4 种比较都没匹配，如果设置了key，就会用 key 再进行比较，在比较的过程中，遍历会往中间靠，一旦 StartIdx > EndIdx 表明 oldCh 和 newCh 至少有一个已经遍历完了，就会结束比较。具体有无 key 的 diff 过程， 


所以 Vue 中 key 的作用是：key 是为 Vue 中 vnode 的唯一标记，通过这个 key，我们的 diff 操作可以更准确、更快速


**更准确**：因为带 key 就不是就地复用了，在 sameNode 函数 `a.key === b.key` 对比中可以避免就地复用的情况。所以会更加准确。


**更快速**：利用 key 的唯一性生成 map 对象来获取对应节点，比遍历方式更快，源码如下：

```text
 function createKeyToOldIdx (children, beginIdx, endIdx) {
  let i, key
  const map = {}
  for (i = beginIdx; i <= endIdx; ++i) {
    key = children[i].key
    if (isDef(key)) map[key] = i
  }
  return map
}
```

## 108、Vue的项目优化

 **（1）代码层面的优化**

- v-if 和 v-show 区分使用场景
- computed 和 watch 区分使用场景
- v-for 遍历必须为 item 添加 key，且避免同时使用 v-if
- 长列表性能优化
- 事件的销毁
- 图片资源懒加载
- 路由懒加载
- 第三方插件的按需引入
- 优化无限列表性能
- 服务端渲染 SSR or 预渲染

**（2）Webpack 层面的优化**

- Webpack 对图片进行压缩
- 减少 ES6 转为 ES5 的冗余代码
- 提取公共代码
- 模板预编译
- 提取组件的 CSS
- 优化 SourceMap
- 构建结果输出分析
- Vue 项目的编译优化

**（3）基础的 Web 技术的优化**

- 开启 gzip 压缩
- 浏览器缓存
- CDN 的使用
- 使用 Chrome Performance 查找性能瓶颈

 





## 109、说说你使用 Vue 框架踩过最大的坑是什么？怎么解决的？



本题为开放题目  把老师项目中 给大家讲的坑说出来即可  



## 110、**vue如何优化页面加载：**



使用cdn,不要打包一些公共的文件和组件库

## 111、**什么是路由懒加载：**





路由懒加载是通过异步的方式来加载对应的路由组件，提高页面相应速度

## 112、**vue中有哪些内置组件：**



component slot transtion fliters



## 113、**ssr是什么**



服务端渲染

## 114、vue中router-link和传统a链接的区别



**官方中给出的解释是这样的：**

<router-link> 比起写死的 <a href="..." rel="external nofollow" rel="external nofollow" > 会好一些，理由如下：

无论是 HTML5 history 模式还是 hash 模式，它的表现行为一致，所以，当你要切换路由模式，或者在 IE9 降级使用 hash 模式，无须作任何变动。

在 HTML5 history 模式下，router-link 会守卫点击事件，让浏览器不再重新加载页面。

当你在 HTML5 history 模式下使用 base 选项之后，所有的 to 属性都不需要写 (基路径) 了。

呃呃，只能说博主刚学时第一次并没有看懂(应该是之前的基础知识不牢固吧，看来之后得补补啦)，于是去自行查阅啦：

> <a href="..." rel="external nofollow" rel="external nofollow" >

**W3C中是这样解释a标签的：**

<a> 标签定义超链接，用于从一张页面链接到另一张页面。

从一张页面跳转到另一张页面，但从这里来说就违背了多视图的单页Web应用这个概念

通过a标签进行跳转，页面会被重新渲染，即相当于重新打开一个新的网页，体现为视觉上的“闪烁”（如果是本地的项目基本看不出来）

`router-link`

<router-link> 组件支持用户在具有路由功能的应用中 (点击) 导航。 通过 to 属性指定目标地址，默认渲染成带有正确链接的 <a> 标签，可以通过配置 tag 属性生成别的标签.。

通过router-link进行跳转不会跳转到新的页面，也不会重新渲染，它会选择路由所指的组件进行渲染，避免了重复渲染的“无用功”。

**总结：**对比<a>，router-link组件避免了不必要的重渲染,它只更新变化的部分从而减少DOM性能消耗

Vue的创新之处在于，它利用虚拟DOM的概念和diff算法实现了对页面的"按需更新"，

Vue-router很好地继承了这一点，重渲染是我们不希望看到的，因为无论跳转到哪个页面，只需要渲染一次就够了。<Link>组件帮助我们实现了这个愿望

反观<a>标签，每次跳转都得重渲染一次，在一个浩大的项目里，这多么可怕！我们的"渲染"做了许多"无用功"，而且消耗了大量弥足珍贵的DOM性能！



## 115、vue插槽是什么   



插槽就是子组件中的提供给父组件使用的一个占位符，用<slot></slot> 表示，父组件可以在这个占位符中填充任何模板代码，如 HTML、组件等，填充的内容会替换子组件的<slot></slot>标签。





## 116、methods和computed的区别？

都是存放方法的地方；

methods里面存放的方法每次调用都会重新计算（不会缓存）

computed里面的方法调用计算后，当其参数不发生改变时再次调用，直接返回上次计算的结果，不重新计算（会响应数据缓存）

watch主要用来解决 beforeUpdate, updated两个钩子里修改状态导致死循环的问题，采用watch以及 监测可能异步改变值得时候



 



## 117、Vue的传参方法？



属性传值、$refs、$parent、通知传值(广播传值)、本地传值、路由传值

父向子传值：v-bind属性绑定（props)

子向父传值：v-on 事件绑定

兄弟组件之间共享数据：EventBus（$on数据接收方，$emit发送数据的内个组件）



作者：SultanST




## 118、vue-router路由守卫？



全局守卫、路由独享守卫、组件内守卫





## 119、vue组件封装？



首先，使用Vue.extend()创建一个组件

然后，使用Vue.component()方法注册组件

接着，如果子组件需要数据，可以在props中接受定义

最后，子组件修改好数据之后，想把数据传递给父组件，可以使用emit()方法



 







## 120、vuex刷新数据页面会丢失吗？咋解决的？



会  

用本地存储

## 121、虚拟DOM和DIFF算法？

将DOM抽象为虚拟DOM, 然后通过新旧虚拟DOM 这两个对象的差异(Diff算法),最终只把变化的部分重新渲染,提高渲染效率的过程; 

diff 是通过JS层面的计算，返回一个patch对象，即补丁对象，在通过特定的操作解析patch对象，完成页面的重新渲染



 



## 122、vue2和vue3原理？



**1. vue2和vue3双向数据绑定原理发生了改变**

**vue2** 的双向数据绑定是利用ES5 的一个 API Object.definePropert()对数据进行劫持 结合 发布订阅模式的方式来实现的。

**vue3** 中使用了 es6 的 ProxyAPI 对数据代理。

相比于vue2.x，使用proxy的优势如下

defineProperty只能监听某个属性，不能对全对象监听

可以省去for in、闭包等内容来提升效率（直接绑定整个对象即可）

可以监听数组，不用再去单独的对数组做特异性操作 vue3.x可以检测到数组内部数据的变化

**2、默认进行懒观察（lazy observation）**

在 2.x 版本里，不管数据多大，都会在一开始就为其创建观察者。当数据很大时，这可能会在页面载入时造成明显的性能压力。3.x 版本，只会对「被用于渲染初始可见部分的数据」创建观察者，而且 3.x 的观察者更高效。

**3、更精准的变更通知**

比例来说：2.x 版本中，使用 Vue.set 来给对象新增一个属性时，这个对象的所有 watcher 都会重新运行；3.x 版本中，只有依赖那个属性的 watcher 才会重新运行。

**4、vue3新加入了TypeScript以及PWA的支持**

**5、vue2和vue3组件发送改变**



 

## 123、scss是什么？





预处理css，把css当前函数编写，定义变量,嵌套.



## 124、 active-class是哪个组件的属性？





vue-router模块的router-link组件。





## 125、说出至少4种vue当中的指令和它的用法？



v-if：判断是否隐藏；v-for：数据循环出来；v-bind:class：绑定一个属性；v-model：实现双向绑定



## 126、请说出vue.cli项目中src目录每个文件夹和文件的用法？



assets文件夹是放静态资源；
components是放组件；
router是定义路由相关的配置;
view视图；
app.vue是一个应用主组件；
main.js是入口文件







## 127、对于即将要来的vue3.0你有什么了解



Vue 3.0 正走在发布的路上，Vue 3.0 的目标是让 Vue 核心变得更小、更快、更强大，因此 Vue 3.0 增加以下这些新特性：


**（1）监测机制的改变**


3.0 将带来基于代理 Proxy 的 observer 实现，提供全语言覆盖的反应性跟踪。这消除了 Vue 2 当中基于 Object.defineProperty 的实现所存在的很多限制：

- 只能监测属性，不能监测对象
- 检测属性的添加和删除；
- 检测数组索引和长度的变更；
- 支持 Map、Set、WeakMap 和 WeakSet。

新的 observer 还提供了以下特性：

- 用于创建 observable 的公开 API。这为中小规模场景提供了简单轻量级的跨组件状态管理解决方案。
- 默认采用惰性观察。在 2.x 中，不管反应式数据有多大，都会在启动时被观察到。如果你的数据集很大，这可能会在应用启动时带来明显的开销。在 3.x 中，只观察用于渲染应用程序最初可见部分的数据。
- 更精确的变更通知。在 2.x 中，通过 Vue.set 强制添加新属性将导致依赖于该对象的 watcher 收到变更通知。在 3.x 中，只有依赖于特定属性的 watcher 才会收到通知。
- 不可变的 observable：我们可以创建值的“不可变”版本（即使是嵌套属性），除非系统在内部暂时将其“解禁”。这个机制可用于冻结 prop 传递或 Vuex 状态树以外的变化。
- 更好的调试功能：我们可以使用新的 renderTracked 和 renderTriggered 钩子精确地跟踪组件在什么时候以及为什么重新渲染。

**（2）模板**


模板方面没有大的变更，只改了作用域插槽，2.x 的机制导致作用域插槽变了，父组件会重新渲染，而 3.0 把作用域插槽改成了函数的方式，这样只会影响子组件的重新渲染，提升了渲染的性能。
同时，对于 render 函数的方面，vue3.0 也会进行一系列更改来方便习惯直接使用 api 来生成 vdom 。


**（3）对象式的组件声明方式**


vue2.x 中的组件是通过声明的方式传入一系列 option，和 TypeScript 的结合需要通过一些装饰器的方式来做，虽然能实现功能，但是比较麻烦。3.0 修改了组件的声明方式，改成了类式的写法，这样使得和 TypeScript 的结合变得很容易。
此外，vue 的源码也改用了 TypeScript 来写。其实当代码的功能复杂之后，必须有一个静态类型系统来做一些辅助管理。现在 vue3.0 也全面改用 TypeScript 来重写了，更是使得对外暴露的 api 更容易结合 TypeScript。静态类型系统对于复杂代码的维护确实很有必要。

**（4）其它方面的更改**


vue3.0 的改变是全面的，上面只涉及到主要的 3 个方面，还有一些其他的更改：

- 支持自定义渲染器，从而使得 weex 可以通过自定义渲染器的方式来扩展，而不是直接 fork 源码来改的方式。
- 支持 Fragment（多个根节点）和 Protal（在 dom 其他部分渲染组建内容）组件，针对一些特殊的场景做了处理。
- 基于 treeshaking 优化，提供了更多的内置功能。





## 128、prop 验证，和默认值





> 我们在父组件给子组件传值得时候，为了避免不必要的错误，可以给prop的值进行类型设定，让父组件给子组件传值得时候，更加准确，prop可以传一个数字，一个布尔值，一个数组，一个对象，以及一个对象的所有属性。组件可以为 props 指定验证要求。如果未指定验证要求，Vue 会发出警告比如传一个number类型的数据，用defalt设置它的默认值，如果验证失败的话就会发出警告。

```
props: {
    visible: {
        default: true,
        type: Boolean,
        required: true
    },
},
```






##  







## 129、Vuex和redux有什么区别 他们的共同思想



- Vuex改进了Redux中的Action和Reducer函数，以mutations变化函数取代Reducer，无需switch，只需在对应的mutation函数里改变state值就可以
- Vuex由于Vue自动重新渲染的特性，无需订阅重新渲染函数，只要生成新的state就可以
- Vuex数据流的顺序是:View调用store.commit提交对应的请求到Store中对应的mutation函数 -- store改变(vue检测到数据变化自动渲染)

> 通俗理解就是:Vuex弱化dispatch，通过commit进行store状态的一次变更；取消了action概念，不必传入特定的action形式进行指定变更；弱化reducer，基于commit参数直接对数据进行转变，使得框架更加建议

### 共同思想

- 单一的数据源
- 变化可以预测
- `本质上:`Redux和Vuex都是对MVVM思想的服务，将数据从视图中抽离的一种方案
- `形式上:`Vuex借鉴了Redux，将store作为全局的数据中心，进行数据管理








