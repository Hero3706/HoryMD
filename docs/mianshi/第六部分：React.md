# 第六部分：React

## 1、React 事件绑定原理

理解：react中的事件都是合成事件，不是把每一个dom的事件绑定在dom上，而是把事件统一绑定到document中，触发时通过事件冒泡到document进行触发合成事件，因为是合成事件，所以我们无法去使用e.stopPropagation去阻止，而是使用e.preventDefault去阻止。
 1.事件注册：组件更新或者装载时，在给dom增加合成事件时，需要将增加的target传入到document进行判断，给document注册原生事件回调为dispatchEvent(统一的事件分发机制)。

2.事件存储：EventPluginHub负责管理React合成事件的callback,它将callback存储到listennerBank中，另外还存储了负责合成事件的Plugin，Event存储到listennerbank中，每一个元素在listennerBank中会有唯一的key。

3.事件触发执行：点击时冒泡到docunment中，触发注册原生事件的回调dispatchEvent，获取到触发这个事件的最深层元素，事件执行利用react的批处理机制。
 案例



```csharp
<div onClick={this.parentClick} ref={ref => this.parent = ref}>
      <div onClick={this.childClick} ref={ref => this.child = ref}>
          test
      </div>
</div>
点击test后
1.首先获取到this.child
2.遍历此元素的所有父元素，依次对每一级元素进行处理
3.构成合成事件
4.将每一级的合成事件存储在eventQueen事件队列中
5.遍历，是否组织冒泡，是则停止，否则继续
6.释放已经完成的事件
```

4.合成事件：循环所有类型的eventPlugin，对应每个事件类型，生成不同的事件池，如果是空，则生成新的，有则用之前的，根据唯一key获取到指定的回调函数，再返回带有参数的回调函数。

5.总流程：组件装载/更新 -- 新增/删除事件 -- eventplugin添加到ListennerBank中监听事件 -- 触发事件 -- 生成合成事件 -- 通过唯一key获取到指定函数 -- 执行指定回调函数 -- 执行完毕后释放



 

## 2、**什么是React？**

- React 是 Facebook 在 2011 年开发的前端 JavaScript 库。
- 它遵循基于组件的方法，有助于构建可重用的UI组件。
- 它用于开发复杂和交互式的 Web 和移动 UI。
- 尽管它仅在 2015 年开源，但有一个很大的支持社区。

## 3、 **React有什么特点？**

React的主要功能如下：

1. 它使用**虚拟DOM **而不是真正的DOM。
2. 它可以用**服务器端渲染**。
3. 它遵循**单向数据流**或数据绑定。

## 4、类组件和函数组件的区别

定义组件有两个要求：

1. 组件名称必须以大写字母开头
2. 组件的返回值只能有一个根元素

------

函数组件



```js
function Welcome (props) {
  return <h1>Welcome {props.name}</h1>
}
ReactDOM.render(<Welcome name='react' />, document.getElementById('root'));
```

1. 函数组件接收一个单一的 `props` 对象并返回了一个React元素

类组件



```js
class Welcome extends React.Component {
  render() {
    return (
      <h1>Welcome { this.props.name }</h1>
    );
  }
}
ReactDOM.render(<Welcome name='react' />, document.getElementById('root'));
```

------

1. 无论是使用函数或是类来声明一个组件，它决不能修改它自己的 `props`。
2. 所有 React 组件都必须是纯函数，并禁止修改其自身 `props` 。
3. React是单项数据流，父组件改变了属性，那么子组件视图会更新。
4. 属性 `props` 是外界传递过来的，状态 `state` 是组件本身的，状态可以在组件中任意修改
5. 组件的属性和状态改变都会更新视图。

区别

函数组件和类组件当然是有区别的，而且函数组件的性能比类组件的性能要高，因为类组件使用的时候要实例化，而函数组件直接执行函数取返回结果即可。为了提高性能，尽量使用函数组件。

| 区别               | 函数组件 | 类组件 |
| ------------------ | -------- | ------ |
| 是否有 `this`      | 没有     | 有     |
| 是否有生命周期     | 没有     | 有     |
| 是否有状态 `state` | 没有     | 有     |

 。

## 5、请你说说React的路由是什么

1.1、基本用法

单页面得特点：只需要加载一次主页面，通过局部刷新，就可以实现跳转或者切换页面

优点：加载速度快，用户体验比较好

缺点：

\- 第一次加载比传统要慢一点
\- 不利seo
\- 页面相对复杂
\- 返回键

1.2安装react-router-dom

```
cnpm install react-router-dom　　
```

下载到生产环境的依赖中。

在组件中通过对象的解构方式去获取到`react-router-dom`内置组件，在组件中，按需引入内置组件，在页面中进行使用：

\- HashRouter表示一个路由的根容器，将来所有的路由相关的东西，都要包裹在HashRouter里面，而且一个网站中，只需要使用一次HashRouter就好了；
\- Route表示一个路由规则，在Route上，有两个比较重要的属性，path，component
\- Link表示一个路由的链接

代码示例

```
render(){``    ``return` `(``      ``<HashRouter>``        ``<div>``          ``<h1>这是网站的根目录</h1>``          ``<hr />``          ``<Link to=``"/home"``>首页</Link> ``          ``<Link to=``"/movie/"``>电影</Link> ``          ``<Link to=``"/about"``>关于</Link>``          ``<hr />``          ``<Route path=``"/home"` `component={Home} ></Route><hr/>``          ``<Route path=``"/movie"` `component={Movie} exact></Route><hr/>``          ``<Route path=``"/about"` `component={About}></Route><hr/>``        ``</div>``      ``</HashRouter>``    ``);``  ``}
```

当使用HashRouter把APP根组件的元素包裹起来之后，网站就已经启用路由了，在一个HashRouter中，只能有唯一的一个根元素。 在一个网站中，只需要使用唯一的一次`<HashRouter></HashRouter>`就行了。

Route创建的标签，就是路由规则，其中path表示要匹配的路由，component表示要展示的组件。Route具有两种身份：1.它是一个路由匹配规则；2.它是一个占位符，表示将来匹配到的组件都放到这个位置

需要注意的地方

\- Route 组件path地址是以/开头 ，配置component属性，是显示的组件，这个属性不可以大写
\- Route组件可以单双标签使用，单标签需要/结尾，双标签不可以在中间写入别的东西
\- Link to属性的地址也是/开头，Link在页面渲染的是a标签

2.1、路由传值

通过配置路由的地址，在Link跳转时

\- Route path路径后面 /:id （key）
\- Link to 路径后面 /top/10 （value）

接收传值：

\- class类组件，this.props.match.params.属性名
\- 函数组件：形参.match.params.属性名

代码示例

```
render(){``    ``return` `(``      ``<HashRouter>``        ``<div>``          ``<h1>这是网站的根目录</h1>``          ``<hr />``          ``<Link to=``"/movie/top/10"``>电影</Link> ``          ``<hr />``          ``<Route path=``"/movie/:type/:id"` `component={Movie} exact></Route>``        ``</div>``      ``</HashRouter>``    ``);``  ``}
```

在Route内置组件中，配置path地址：

```
<Route path=``"/movie/:type/:id"` `component={Movie} exact></Route>
```

在Link内置组件中，配置to属性，进行跳转：

```
<Link to=``"/movie/top/10"``>电影</Link>
```

类组件中通过生命周期进行接收，this.props携带路由传递过来的数据：

```
render(){``    ``console.log(``this``);``    ``return` `(``      ``<div>``        ``电影--{``this``.props.match.``params``.type}--{``this``.props.match.``params``.id}``      ``</div>``    ``);``  ``}
```

代码优化后：　

```
class` `Movie extends React.Component{` `  ``constructor(props){``    ``super();``    ``this``.state = {``      ``routeParams:props.match.``params``    ``}``  ``}` `  ``render(){``    ``console.log(``this``);``    ``return` `(``      ``<div>``        ``电影--{``this``.state.routeParams.type}--{``this``.state.routeParams.id}``      ``</div>``    ``);``  ``}``}
```

函数组件中通过形参接收传递过来的值，props形参，函数组件作为路由组件，props就是传递过来的对象，里面携带着路由传递过来的数据　

```
import React ``from` `'react'` `export ``default` `function home(props) {``  ``return` `(``    ``<div>``      ``{props.match.``params``.id}``    ``</div>``  ``)``}
```

2.2、嵌套路由　

嵌套路由：在路由组件中，使用Link， Route，配置子路由，实现跳转，切换；

下面为一级路由，在一级路由Home为路由组件

```
<Route path=``"/home"` `component={ Home }></Route>
```

在Home组件中继续使用Link，Route进行路由的嵌套，需要注意的就是路由地址，前部分为一级路由地址，后面接一个二级路由相应的路径　

```
render() {``  ``return` `(``    ``<div>``      ``<ul>``        ``<li><Link to=``"/home/a"``>推荐</Link></li>``        ``<li><Link to=``"/home/b"``>新时代</Link></li>``        ``<li><Link to=``"/home/c"``>动漫</Link></li>``      ``</ul>``      ``<Route path=``"/home/a"` `component={ A }></Route>``      ``<Route path=``"/home/b"` `component={ B }></Route>``      ``<Route path=``"/home/c"` `component={ C }></Route>``    ``</div>``  ``)``}
```

2.3、JS实现路由跳转　

引入BrowserRouter模块

```
import {BrowserRouter,HashRouter,Route,Link} ``from` `'react-router-dom'
```

使用BrowserRouter作为根容器　

```
jump(){``  ``window.location.href = ``"/news"``}` `render(){``  ``return` `(``    ``<BrowserRouter>``      ``<div>``        ``<h1>这是网站的根目录</h1>``        ``<hr />``        ``<button onClick={()=>{``this``.jump()}}>新闻</button>``        ``<hr />``        ``<Route path=``"/news"` `component={News}></Route>``      ``</div>``    ``</BrowserRouter>``  ``);``}
```

在render方法中，写一个按钮，按钮名称为js跳转路由，定义一个onClick方法，箭头函数解决this指向问题，与render同级，定义一个jump方法，在jump方法中执行一句代码进行路由跳转，使用window.location.href = “路由的地址”实现路由跳转。　

3、react-router-dom内置组件

首先按需引入，使用什么内置组件，就需要引入

```
import { BrowserRouter, Link, Route,Redirect,NavLink,Switch } ``from` `'react-router-dom'
```

3.1、在组件中使用NavLink　

NavLink 带有选中activeClassName ,如果路由处于激活状态，显示激活class样式。

在我们之前案例的基础上，找到Link组件，我们已经学到Link组件的作用，可以进行路由的跳转，通过to属性，跳转相应的path地址。

```
​```html``//``<ul>``  ``<li>``    ``<Link to=``"/home"``>首页</Link>``  ``</li>``  ``<li>``    ``<Link to=``"/video"``>好看视频</Link>``  ``</li>``</ul>
```

　将组件中的Link全部换成NavLink组件

```html``//``<li>``  ``<NavLink activeClassName=``"red"` `to=``"/home"``>首页</NavLink>``</li>``<li>``  ``<NavLink activeClassName=``"red"` `to=``"/video"``>好看视频</NavLink>``</li>``````

　　

我们会发现，之前可以正常进行路由跳转，换成NavLink，还依然可以正常跳转，证明组件得跳转使用NavLink也可以实现，那么问题来了，NavLink有什么用，为什么封装了NavLink，将每一个NavLink加入一个activeClassName属性绑定一个class类样式，这时在触发NavLink时，会触发相应得样式，这样有一个切换效果。

3.2、在组件中使用Redirect内置组件

Redirect 重定向 具备to属性，可以直接跳转到指定路由。

在render方法中，使用内置组件，Redirect内置组件使用to属性，当执行到内置标签是，会进行to跳转路由，to后面接的地址是什么，就可以匹配到相应得路由组件。

```
​```html``//``<Redirect to=``"/home/c"``></Redirect>
```

## 6、**列出React的一些主要优点。**

React的一些主要优点是：

1. 它提高了应用的性能
2. 可以方便地在客户端和服务器端使用
3. 由于 JSX，代码的可读性很好
4. React 很容易与 Meteor，Angular 等其他框架集成
5. 使用React，编写UI测试用例变得非常容易

## 7、React hooks用过吗，为什么要用

函数组件比起类组件“少”了很多东西，比如生命周期、对 state 的管理等。这就给函数组件的使用带来了非常多的局限性。



React-Hooks 的出现，就是为了帮助函数组件补齐这些（相对于类组件来说）缺失的能力

## 8虚拟DOM的优劣如何

- 保证性能下限： 框架的虚拟 DOM 需要适配任何上层 API 可能产生的操作，它的一些 DOM 操作的实现必须是普适的，所以它的性能并不是最优的；但是比起粗暴的 DOM 操作性能要好很多，因此框架的虚拟 DOM 至少可以保证在你不需要手动优化的情况下，依然可以提供还不错的性能，即保证性能的下限；
-  
- 无需手动操作 DOM： 我们不再需要手动去操作 DOM，只需要写好 View-Model 的代码逻辑，框架会根据虚拟 DOM 和 数据双向绑定，帮我们以可预期的方式更新视图，极大提高我们的开发效率；
-  
- 跨平台： 虚拟 DOM 本质上是 JavaScript 对象,而 DOM 与平台强相关，相比之下虚拟 DOM 可以进行更方便地跨平台操作，例如服务器渲染、weex 开发等等。

缺点:

- 无法进行极致优化： 虽然虚拟 DOM + 合理的优化，足以应对绝大部分应用的性能需求，但在一些性能要求极高的应用中虚拟 DOM 无法进行针对性的极致优化。
  首次渲染大量DOM时，由于多了一层虚拟DOM的计算，会比innerHTML插入慢。

## 9、**React有哪些限制？**

React的限制如下：

1. React 只是一个库，而不是一个完整的框架
2. 它的库非常庞大，需要时间来理解
3. 新手程序员可能很难理解
4. 编码变得复杂，因为它使用内联模板和 JSX

## 10、**什么是JSX？**

JSX 是J avaScript XML 的简写。是 React 使用的一种文件，它利用 JavaScript 的表现力和类似 HTML 的模板语法。这使得 HTML 文件非常容易理解。此文件能使应用非常可靠，并能够提高其性能。下面是JSX的一个例子：

```javascript
render(){
    return(        
        <div>
            <h1> Hello World from Edureka!!</h1>
        </div>
    );
}
```

## 11、react中不同组件之间如何做到数据交互？

- 父组件向子组件通信：使用 props
- 子组件向父组件通信：使用 props 回调
- 跨级组件间通信：使用 context 对象
- 非嵌套组件间通信：使用事件订阅

事实上，在组件间进行通信时，这些通信方式都可以使用，区别只在于使用相应的通信方式的复杂程度和个人喜好，选择最合适的那一个。比如，通过事件订阅模式通信不止可以应用在非嵌套组件间，还可以用于跨级组件间，非嵌套组件间通信也可以使用 context 等。关键是选择最合适的方式。
 当然，自己实现组件间的通信还是太难以管理了，因此出现了很多状态管理工具，如 flux、redux 等，使用这些工具使得组件间的通信更容易追踪和管理。





## 12、react中refs的作用是什么？

- `Refs` 是 `React` 提供给我们的安全访问 `DOM`元素或者某个组件实例的句柄
- 可以为元素添加`ref`属性然后在回调函数中接受该元素在 `DOM` 树中的句柄，该值会作为回调函数的第一个参数返回

## 13、请列举react生命周期函数。

React 生命周期分为三种状态 1. 初始化 2.更新 3.销毁

![img](https://img2018.cnblogs.com/common/685007/202002/685007-20200224124737710-689872611.jpg)

- **初始化**

**1、getDefaultProps()**

> 设置默认的props，也可以用dufaultProps设置组件的默认属性.

**2、getInitialState()**

> 在使用es6的class语法时是没有这个钩子函数的，可以直接在constructor中定义this.state。此时可以访问this.props

**3、componentWillMount()**

> 组件初始化时只调用，以后组件更新不调用，整个生命周期只调用一次，**此时可以修改state**。
>
> 在渲染前调用,在客户端也在服务端。

**4、 render()**

> react最重要的步骤，创建虚拟dom，进行diff算法，更新dom树都在此进行。此时就不能更改state了。

**5、componentDidMount()**

> 组件渲染之后调用，只调用一次。
>
> 在第一次渲染后调用，只在客户端。之后组件已经生成了对应的DOM结构，可以通过**this****.getDOMNode()**来进行访问。
>
> 如果你想和其他JavaScript框架一起使用，**可以在这个方法中调用****setTimeout,** **setInterval或者发送****AJAX请求等操作(防止异步操作阻塞UI)**。

- **更新**

**6、componentWillReceiveProps(nextProps)**

> 组件初始化时不调用，组件接受新的props时调用。
>
> ***\*使用componentWillReceiveProps的时候，不要去向上分发，调用父组件的相关setState方法，否则会成为死循环\****
>
> ***\*在组件接收到一个新的 prop (更新后)时被调用。这个方法在初始化render时不会被调用。\****

**7、shouldComponentUpdate(nextProps, nextState)**

> react性能优化非常重要的一环。组件接受新的state或者props时调用，我们可以设置在此对比前后两个props和state是否相同，
>
> 如果相同则返回false阻止更新，因为相同的属性状态一定会生成相同的dom树，这样就不需要创造新的dom树和旧的dom树进行diff算法对比，
>
> 节省大量性能，尤其是在dom结构复杂的时候
>
> 返回一个布尔值。在组件接收到新的props或者state时被调用。
>
> 在初始化时或者使用forceUpdate时不被调用。
>
> 可以在你确认不需要更新组件时使用。

**8、componentWillUpdata(nextProps, nextState)**

> 组件初始化时不调用，只有在组件将要更新时才调用，此时可以修改state

**9、render()**

> 组件渲染

**10、componentDidUpdate()**

> 组件初始化时不调用，组件更新完成后调用，此时可以获取dom节点。

- **卸载**

**11、componentWillUnmount()**

> 组件将要卸载时调用，一些事件监听和定时器需要在此时清除。

二、组件生命周期的执行次数是什么样子的

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
只执行一次： constructor、componentWillMount、componentDidMount

执行多次：render 、子组件的componentWillReceiveProps、componentWillUpdate、componentDidUpdate

有条件的执行：componentWillUnmount（页面离开，组件销毁时）

不执行的：根组件（ReactDOM.render在DOM上的组件）的componentWillReceiveProps（因为压根没有父组件给传递props）
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

三、React生命周期执行顺序

![img](https://img2018.cnblogs.com/common/685007/202002/685007-20200224174515157-985278420.png)

**Mounting中为组件的挂载过程**
        componentWillMount组件挂载之前
        render组件的渲染方法
        componentDidMount组件挂载完成执行
**Updation中为组件数据发生变化的过程**
        props独有 
        componentWillReceiveProps 
        触发条件 1. 当一个组件从父组件接收了参数。
                 2.如果这个组件第一次被父组件加载的时候不会被执行。
                 3.这个组件之前已经存在于父组件中，并且接收的数据发生变动这时此方法才会被触发。 
        props和states共有
        shouldComponentUpdata 是否要更新数据？需要一个返回值true继续执行下面的生命周期，false就会终止当前组件数 
        componentWillUpdate 组件将要更新
        render组件的重新渲染
        componentDidUpdata 组件完成更新
**Unmounting组件卸载**
        componentWillUnmount 组件销毁的时候触发

## 14、**你了解 Virtual DOM 吗？解释一下它的工作原理。**

Virtual DOM 是一个轻量级的 JavaScript 对象，它最初只是 real DOM 的副本。它是一个节点树，它将元素、它们的属性和内容作为对象及其属性。 React 的渲染函数从 React 组件中创建一个节点树。然后它响应数据模型中的变化来更新该树，该变化是由用户或系统完成的各种动作引起的。

Virtual DOM 工作过程有三个简单的步骤。

1. 每当底层数据发生改变时，整个 UI 都将在 Virtual DOM 描述中重新渲染。
   ![Virtual DOM 1](https://img-blog.csdnimg.cn/20190325160134708.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2V5ZW9mYW5nZWw=,size_16,color_FFFFFF,t_70)
2. 然后计算之前 DOM 表示与新表示的之间的差异。
   ![Virtual DOM 2](https://img-blog.csdnimg.cn/20190325160145800.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2V5ZW9mYW5nZWw=,size_16,color_FFFFFF,t_70)
3. 完成计算后，将只用实际更改的内容更新 real DOM。
   ![Virtual DOM 3](https://img-blog.csdnimg.cn/20190325160158808.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2V5ZW9mYW5nZWw=,size_16,color_FFFFFF,t_70)

## 15、**为什么浏览器无法读取JSX？**

浏览器只能处理 JavaScript 对象，而不能读取常规 JavaScript 对象中的 JSX。所以为了使浏览器能够读取 JSX，首先，需要用像 Babel 这样的 JSX 转换器将 JSX 文件转换为 JavaScript 对象，然后再将其传给浏览器。

## 16、**与 ES5 相比，React 的 ES6 语法有何不同？**

以下语法是 ES5 与 ES6 中的区别：

1. require 与 import

```react
// ES5
var React = require('react');
 
// ES6
import React from 'react';
12345
```

1. export 与 exports

```react
// ES5
module.exports = Component;
 
// ES6
export default Component;
12345
```

1. component 和 function

```react
// ES5
var MyComponent = React.createClass({
    render: function() {
        return
			<h3>Hello Edureka!</h3>;
    }
});
 
// ES6
class MyComponent extends React.Component {
    render() {
        return
			<h3>Hello Edureka!</h3>;
    }
}
123456789101112131415
```

1. props

```react
// ES5
var App = React.createClass({
    propTypes: { name: React.PropTypes.string },
    render: function() {
        return
			<h3>Hello, {this.props.name}!</h3>;
    }
});

// ES6
class App extends React.Component {
    render() {
        return
			<h3>Hello, {this.props.name}!</h3>;
    }
}
12345678910111213141516
```

1. state

```react
// ES5
var App = React.createClass({
    getInitialState: function() {
        return { name: 'world' };
    },
    render: function() {
        return
	        <h3>Hello, {this.state.name}!</h3>;
    }
});

// ES6
class App extends React.Component {
    constructor() {
        super();
        this.state = { name: 'world' };
    }
    render() {
        return
	        <h3>Hello, {this.state.name}!</h3>;
    }
}
12345678910111213141516171819202122
```

**10. React与Angular有何不同？**

| **主题**      | **React**          | **Angular**   |
| ------------- | ------------------ | ------------- |
| *1. 体系结构* | 只有 MVC 中的 View | 完整的 MVC    |
| *2. 渲染*     | 可以在服务器端渲染 | 客户端渲染    |
| *3. DOM*      | 使用 virtual DOM   | 使用 real DOM |
| *4. 数据绑定* | 单向数据绑定       | 双向数据绑定  |
| *5. 调试*     | 编译时调试         | 运行时调试    |
| *6. 作者*     | Facebook           | Google        |

## 17、**React与Angular有何不同？**

| **主题**      | **React**          | **Angular**   |
| ------------- | ------------------ | ------------- |
| *1. 体系结构* | 只有 MVC 中的 View | 完整的 MVC    |
| *2. 渲染*     | 可以在服务器端渲染 | 客户端渲染    |
| *3. DOM*      | 使用 virtual DOM   | 使用 real DOM |
| *4. 数据绑定* | 单向数据绑定       | 双向数据绑定  |
| *5. 调试*     | 编译时调试         | 运行时调试    |
| *6. 作者*     | Facebook           | Google        |

## 18、**你理解“在React中，一切都是组件”这句话。**

组件是 React 应用 UI 的构建块。这些组件将整个 UI 分成小的独立并可重用的部分。每个组件彼此独立，而不会影响 UI 的其余部分。

## 19、**解释 React 中 render() 的目的**

每个React组件强制要求必须有一个 **render()**。它返回一个 React 元素，是原生 DOM 组件的表示。如果需要渲染多个 HTML 元素，则必须将它们组合在一个封闭标记内，例如 `<form>`、`<group>`、`<div>` 等。此函数必须保持纯净，即必须每次调用时都返回相同的结果。

## 20、**如何将两个或多个组件嵌入到一个组件中？**

可以通过以下方式将组件嵌入到一个组件中：

```react
class MyComponent extends React.Component{
    render(){
        return(          
			<div>
            	<h1>Hello</h1>
                <Header/>
            </div>
        );
    }
}
class Header extends React.Component{
    render(){
        return
			<h1>Header Component</h1>   
   };
}
ReactDOM.render(
    <MyComponent/>, document.getElementById('content')
);
```

## 21、 **React中的状态是什么？它是如何使用的？**？

状态是 React 组件的核心，是数据的来源，必须尽可能简单。基本上状态是确定组件呈现和行为的对象。与props 不同，它们是可变的，并创建动态和交互式组件。可以通过 `this.state()` 访问它们。

## 22、React组件中props是什么？

Props 是 React 中属性的简写。它们是只读组件，必须保持纯，即不可变。它们总是在整个应用中从父组件传递到子组件。子组件永远不能将 prop 送回父组件。这有助于维护单向数据流，通常用于呈现动态生成的数据。

## 23、react中组件分为那俩种

函数式  和类组件

## 24、react中**区分状态和 props**？

| **条件**                | **State** | **Props** |
| ----------------------- | --------- | --------- |
| 1. 从父组件中接收初始值 | Yes       | Yes       |
| 2. 父组件可以改变值     | No        | Yes       |
| 3. 在组件中设置默认值   | Yes       | Yes       |
| 4. 在组件的内部变化     | Yes       | No        |
| 5. 设置子组件的初始值   | Yes       | Yes       |
| 6. 在子组件的内部更改   | No        | Yes       |

## 25、**如何更新组件的状态？**

可以用 `this.setState()`更新组件的状态。

```react
class MyComponent extends React.Component {
    constructor() {
        super();
        this.state = {
            name: 'Maxx',
            id: '101'
        }
    }
    render()
        {
            setTimeout(()=>{this.setState({name:'Jaeha', id:'222'})},2000)
            return (              
				<div>
                	<h1>Hello {this.state.name}</h1>
					<h2>Your Id is {this.state.id}</h2>
                </div>
            );
        }
    }
ReactDOM.render(
    <MyComponent/>, document.getElementById('content')
);
```

## 26、**React 中的箭头函数是什么？怎么用？**

箭头函数（**=>**）是用于编写函数表达式的简短语法。这些函数允许正确绑定组件的上下文，因为在 ES6 中默认下不能使用自动绑定。使用高阶函数时，箭头函数非常有用。

```react
//General way
render() {    
    return(
        <MyInput onChange = {this.handleChange.bind(this) } />
    );
}
//With Arrow Function
render() {  
    return(
        <MyInput onChange = { (e)=>this.handleOnChange(e) } />
    );
}
```

## 27、**区分有状态和无状态组件。**

| **有状态组件**                                               | **无状态组件**                                  |
| ------------------------------------------------------------ | ----------------------------------------------- |
| 1. 在内存中存储有关组件状态变化的信息                        | 1. 计算组件的内部的状态                         |
| 2. 有权改变状态                                              | 2. 无权改变状态                                 |
| 3. 包含过去、现在和未来可能的状态变化情况                    | 3. 不包含过去，现在和未来可能发生的状态变化情况 |
| 4. 接受无状态组件状态变化要求的通知，然后将 props 发送给他们。 | 4.从有状态组件接收 props 并将其视为回调函数。   |

## 28、**React组件生命周期的阶段是什么？**

React 组件的生命周期有三个不同的阶段：

1. *初始渲染阶段：*这是组件即将开始其生命之旅并进入 DOM 的阶段。
2. *更新阶段：*一旦组件被添加到 DOM，它只有在 prop 或状态发生变化时才可能更新和重新渲染。这些只发生在这个阶段。
3. *卸载阶段：*这是组件生命周期的最后阶段，组件被销毁并从 DOM 中删除。

## 29、 **React中的事件是什么？**

 React 中，事件是对鼠标悬停、鼠标单击、按键等特定操作的触发反应。处理这些事件类似于处理 DOM 元素中的事件。但是有一些语法差异，如：

1. 用驼峰命名法对事件命名而不是仅使用小写字母。
2. 事件作为函数而不是字符串传递。

事件参数重包含一组特定于事件的属性。每个事件类型都包含自己的属性和行为，只能通过其事件处理程序访问。

## 30    React中的合成事件是什么？ 

合成事件是围绕浏览器原生事件充当跨浏览器包装器的对象。它们将不同浏览器的行为合并为一个 API。这样做是为了确保事件在不同浏览器中显示一致的属性。

## 31、**你对 React 的 refs 有什么了解？**

Refs 是 React 中引用的简写。它是一个有助于存储对特定的 React 元素或组件的引用的属性，它将由组件渲染配置函数返回。用于对 render() 返回的特定元素或组件的引用。当需要进行 DOM 测量或向组件添加方法时，它们会派上用场。

```react
class ReferenceDemo extends React.Component{
     display() {
         const name = this.inputDemo.value;
         document.getElementById('disp').innerHTML = name;
     }
render() {
    return(        
          <div>
            Name: <input type="text" ref={input => this.inputDemo = input} />
            <button name="Click" onClick={this.display}>Click</button>            
            <h2>Hello <span id="disp"></span> !!!</h2>
          </div>
    );
   }
 }
```

## 32、 **列出一些应该使用 Refs 的情况。**



以下是应该使用 refs 的情况：

- 需要管理焦点、选择文本或媒体播放时
- 触发式动画
- 与第三方 DOM 库集成

##  33、**如何模块化 React 中的代码？**

可以使用 export 和 import 属性来模块化代码。它们有助于在不同的文件中单独编写组件。

```react
//ChildComponent.jsx
export default class ChildComponent extends React.Component {
    render() {
        return(           
              <div>
              	<h1>This is a child component</h1>
              </div>
        );
    }
}
 
//ParentComponent.jsx
import ChildComponent from './childcomponent.js';
class ParentComponent extends React.Component {    
    render() {        
        return(           
             <div>               
                <App />          
             </div>       
        );  
    }
}
```

## 34、**如何在 React 中创建表单**

React 表单类似于 HTML 表单。但是在 React 中，状态包含在组件的 state 属性中，并且只能通过 `setState()` 更新。因此元素不能直接更新它们的状态，它们的提交是由 JavaScript 函数处理的。此函数可以完全访问用户输入到表单的数据。

```react
handleSubmit(event) {
    alert('A name was submitted: ' + this.state.value);
    event.preventDefault();
}
 
render() {
    return (        
        <form onSubmit={this.handleSubmit}>
            <label>
                Name:
                <input type="text" value={this.state.value} onChange={this.handleSubmit} />
            </label>
            <input type="submit" value="Submit" />
        </form>
    );
}
```

## 35、**你对受控组件和非受控组件了解多少？**

| **受控组件**                                   | **非受控组件**           |
| ---------------------------------------------- | ------------------------ |
| 1. 没有维持自己的状态                          | 1. 保持着自己的状态      |
| 2.数据由父组件控制                             | 2.数据由 DOM 控制        |
| 3. 通过 props 获取当前值，然后通过回调通知更改 | 3. Refs 用于获取其当前值 |

## 36、**什么是高阶组件（HOC）？**

高阶组件是重用组件逻辑的高级方法，是一种源于 React 的组件模式。 HOC 是自定义组件，在它之内包含另一个组件。它们可以接受子组件提供的任何动态，但不会修改或复制其输入组件中的任何行为。你可以认为 HOC 是“纯（Pure）”组件。

## 37、**你能用HOC做什么？**

HOC可用于许多任务，例如：

- 代码重用，逻辑和引导抽象
- 渲染劫持
- 状态抽象和控制
- Props 控制

## 38、 **什么是纯组件？**

*纯（Pure）* 组件是可以编写的最简单、最快的组件。它们可以替换任何只有 **render()** 的组件。这些组件增强了代码的简单性和应用的性能。

## 39、 **React 中 key 的重要性是什么？**

key 用于识别唯一的 Virtual DOM 元素及其驱动 UI 的相应数据。它们通过回收 DOM 中当前所有的元素来帮助 React 优化渲染。这些 key 必须是唯一的数字或字符串，React 只是重新排序元素而不是重新渲染它们。这可以提高应用程序的性能。

## 40、**MVC框架的主要问题是什么？**

以下是MVC框架的一些主要问题：

- 对 DOM 操作的代价非常高
- 程序运行缓慢且效率低下
- 内存浪费严重
- 由于循环依赖性，组件模型需要围绕 models 和 views 进行创建

## 41、hooks的使用有什么注意事项

在使用Hooks的过程中，需要注意的两点是：

- 不要在循环，条件或嵌套函数中调用Hook，必须始终在React函数的顶层使用Hook。这是因为React需要利用调用顺序来正确更新相应的状态，以及调用相应的钩子函数。一旦在循环或条件分支语句中调用Hook，就容易导致调用顺序的不一致性，从而产生难以预料到的后果。
- 只能在React函数式组件或自定义Hook中使用Hook。

## 42、纯函数有什么特点 

- 无状态。线程安全。不需要线程同步。
- [纯函数](https://zh.wikipedia.org/wiki/函数副作用#纯函数)相互调用组装起来的函数，还是[纯函数](https://zh.wikipedia.org/wiki/函数副作用#纯函数)。
- 应用程序或者运行环境（Runtime）可以对[纯函数](https://zh.wikipedia.org/wiki/函数副作用#纯函数)的运算结果进行缓存，运算加快速度。

## 43、**解释一下 Flux**

![flux](https://img-blog.csdnimg.cn/20190325160224109.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2V5ZW9mYW5nZWw=,size_16,color_FFFFFF,t_70)
Flux 是一种强制单向数据流的架构模式。它控制派生数据，并使用具有所有数据权限的中心 store 实现多个组件之间的通信。整个应用中的数据更新必须只能在此处进行。 Flux 为应用提供稳定性并减少运行时的错误。

## 44、**什么是Redux？**

Redux 是当今最热门的前端开发库之一。它是 JavaScript 程序的可预测状态容器，用于整个应用的状态管理。使用 Redux 开发的应用易于测试，可以在不同环境中运行，并显示一致的行为。

## 45、**Redux遵循的三个原则是什么？**

1. ***单一事实来源：***整个应用的状态存储在单个 store 中的对象/状态树里。单一状态树可以更容易地跟踪随时间的变化，并调试或检查应用程序。
2. ***状态是只读的：***改变状态的唯一方法是去触发一个动作。动作是描述变化的普通 JS 对象。就像 state 是数据的最小表示一样，该操作是对数据更改的最小表示。
3. ***使用纯函数进行更改：***为了指定状态树如何通过操作进行转换，你需要纯函数。纯函数是那些返回值仅取决于其参数值的函数。

![Store](https://img-blog.csdnimg.cn/20190325160235928.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2V5ZW9mYW5nZWw=,size_16,color_FFFFFF,t_70)

## 46、**你对“单一事实来源”有什么理解？**

Redux 使用 “Store” 将程序的整个状态存储在同一个地方。因此所有组件的状态都存储在 Store 中，并且它们从 Store 本身接收更新。单一状态树可以更容易地跟踪随时间的变化，并调试或检查程序。

## 47、**列出 Redux 的组件。**

Redux 由以下组件组成：

1. **Action** – 这是一个用来描述发生了什么事情的对象。
2. **Reducer** – 这是一个确定状态将如何变化的地方。
3. **Store** – 整个程序的状态/对象树保存在Store中。
4. **View** – 只显示 Store 提供的数据。

## 48、**数据如何通过 Redux 流动？**

###  

![Data Flow in Redux](https://img-blog.csdnimg.cn/20190325160247418.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2V5ZW9mYW5nZWw=,size_16,color_FFFFFF,t_70)

###  

## 49、**如何在 Redux 中定义 Action？**

React 中的 Action 必须具有 type 属性，该属性指示正在执行的 ACTION 的类型。必须将它们定义为字符串常量，并且还可以向其添加更多的属性。在 Redux 中，action 被名为 Action Creators 的函数所创建。以下是 Action 和Action Creator 的示例：

```react
function addTodo(text) {
       return {
                type: ADD_TODO,    
                 text
    }
}
```

## 50、 **解释 Reducer 的作用。**

Reducers 是纯函数，它规定应用程序的状态怎样因响应 ACTION 而改变。Reducers 通过接受先前的状态和 action 来工作，然后它返回一个新的状态。它根据操作的类型确定需要执行哪种更新，然后返回新的值。如果不需要完成任务，它会返回原来的状态。

## 51、**Store 在 Redux 中的意义是什么？**

Store 是一个 JavaScript 对象，它可以保存程序的状态，并提供一些方法来访问状态、调度操作和注册侦听器。应用程序的整个状态/对象树保存在单一存储中。因此，Redux 非常简单且是可预测的。我们可以将中间件传递到 store 来处理数据，并记录改变存储状态的各种操作。所有操作都通过 reducer 返回一个新状态。

## 52、 **Redux与Flux有何不同？**

| **Flux**                           | **Redux**                        |
| ---------------------------------- | -------------------------------- |
| 1. Store 包含状态和更改逻辑        | 1. Store 和更改逻辑是分开的      |
| 2. 有多个 Store                    | 2. 只有一个 Store                |
| 3. 所有 Store 都互不影响且是平级的 | 3. 带有分层 reducer 的单一 Store |
| 4. 有单一调度器                    | 4. 没有调度器的概念              |
| 5. React 组件订阅 store            | 5. 容器组件是有联系的            |
| 6. 状态是可变的                    | 6. 状态是不可改变的              |

## 53、**Redux 有哪些优点？**

Redux 的优点如下：

- **结果的可预测性 -** 由于总是存在一个真实来源，即 store ，因此不存在如何将当前状态与动作和应用的其他部分同步的问题。
- **可维护性 -** 代码变得更容易维护，具有可预测的结果和严格的结构。
- **服务器端渲染 -** 你只需将服务器上创建的 store 传到客户端即可。这对初始渲染非常有用，并且可以优化应用性能，从而提供更好的用户体验。
- **开发人员工具 -** 从操作到状态更改，开发人员可以实时跟踪应用中发生的所有事情。
- **社区和生态系统 -** Redux 背后有一个巨大的社区，这使得它更加迷人。一个由才华横溢的人组成的大型社区为库的改进做出了贡献，并开发了各种应用。
- **易于测试 -** Redux 的代码主要是小巧、纯粹和独立的功能。这使代码可测试且独立。
- **组织 -** Redux 准确地说明了代码的组织方式，这使得代码在团队使用时更加一致和简单。

## 54、**什么是React 路由？**

React 路由是一个构建在 React 之上的强大的路由库，它有助于向应用程序添加新的屏幕和流。这使 URL 与网页上显示的数据保持同步。它负责维护标准化的结构和行为，并用于开发单页 Web 应用。 React 路由有一个简单的API。

## 55、**为什么React Router v4中使用 switch 关键字 ？**

虽然 **`<div>` \** 用于封装 Router 中的多个路由，当你想要仅显示要在多个定义的路线中呈现的单个路线时，可以使用 “switch” 关键字。使用时，**`<switch>`** 标记会按顺序将已定义的 URL 与已定义的路由进行匹配。找到第一个匹配项后，它将渲染指定的路径。从而绕过其它路线。

## 56、**为什么需要 React 中的路由？**

Router 用于定义多个路由，当用户定义特定的 URL 时，如果此 URL 与 Router 内定义的任何 “路由” 的路径匹配，则用户将重定向到该特定路由。所以基本上我们需要在自己的应用中添加一个 Router 库，允许创建多个路由，每个路由都会向我们提供一个独特的视图

```react
<switch>
    <route exact path=’/’ component={Home}/>
    <route path=’/posts/:id’ component={Newpost}/>
    <route path=’/posts’   component={Post}/>
</switch>
```

## 57、**列出 React Router 的优点。**

几个优点是：

1. 就像 React 基于组件一样，在 React Router v4 中，API 是 *‘All About Components’*。可以将 Router 可视化为单个根组件（**`<BrowserRouter>`**），其中我们将特定的子路由（**`<route>`**）包起来。
2. 无需手动设置历史值：在 React Router v4 中，我们要做的就是将路由包装在 **`<BrowserRouter>`** 组件中。
3. 包是分开的：共有三个包，分别用于 Web、Native 和 Core。这使我们应用更加紧凑。基于类似的编码风格很容易进行切换。

## 58、**React Router与常规路由有何不同？**

| **主题**       | **常规路由**                                    | **React 路由**                   |
| -------------- | ----------------------------------------------- | -------------------------------- |
| **参与的页面** | 每个视图对应一个新文件                          | 只涉及单个HTML页面               |
| **URL 更改**   | HTTP 请求被发送到服务器并且接收相应的 HTML 页面 | 仅更改历史记录属性               |
| **体验**       | 用户实际在每个视图的不同页面切换                | 用户认为自己正在不同的页面间切换 |

## 59、**类组件和函数组件之间有什么区别？**

- 类组件（ **Class components** ）

- - 无论是使用函数或是类来声明一个组件，它决不能修改它自己的 `props` 。

  - - 所有 React 组件都必须是纯函数，并禁止修改其自身 `props` 。



- - React是单项数据流，父组件改变了属性，那么子组件视图会更新。

  - - 属性 `props` 是外界传递过来的，状态 `state` 是组件本身的，状态可以在组件中任意修改
    - 组件的属性和状态改变都会更新视图。





```text
class Welcome extends React.Component {
  render() {
    return (
      <h1>Welcome { this.props.name }</h1>
    );
  }
}
ReactDOM.render(<Welcome name='react' />, document.getElementById('root'));
```

- **函数组件（functional component）**

- - 函数组件接收一个单一的 `props` 对象并返回了一个React元素

function **Welcome** (props) { return <h1>Welcome {props.name}</h1> } ReactDOM.render(<Welcome name='react' />, document.getElementById('root'));

### **区别**

函数组件和类组件当然是有区别的，而且函数组件的性能比类组件的性能要高，因为类组件使用的时候要实例化，而函数组件直接执行函数取返回结果即可。为了提高性能，尽量使用函数组件。

**区别函数组件类组件**是否有`this`没有有是否有生命周期没有有是否有状态`state`没有有

## 60、**React中的refs作用是什么？**

Refs 是 React 提供给我们的安全访问 DOM 元素或者某个组件实例的句柄。

我们可以为元素添加 ref 属性然后在回调函数中接受该元素在 DOM 树中的句柄，该值会作为回调函数的第一个参数返回：

```text
class UnControlledForm extends Component {
  handleSubmit = () => {
    console.log("Input Value: ", this.input.value)
  }
  render () {
    return (
      <form onSubmit={this.handleSubmit}>
        <input
          type='text'
          ref={(input) => this.input = input} />
        <button type='submit'>Submit</button>
      </form>
    )
  }
}
```

上述代码中的 input 域包含了一个 ref 属性，该属性声明的回调函数会接收 input 对应的 DOM 元素，我们将其绑定到 this 指针以便在其他的类函数中使用。

另外值得一提的是，refs 并不是类组件的专属，函数式组件同样能够利用闭包暂存其值：

```text
function CustomForm ({handleSubmit}) {
  let inputElement
  return (
    <form onSubmit={() => handleSubmit(inputElement.value)}>
      <input
        type='text'
        ref={(input) => inputElement = input} />
      <button type='submit'>Submit</button>
    </form>
  )
}
```

## 61、**描述React事件处理。**

为了解决跨浏览器兼容性问题，React中的事件处理程序将传递SyntheticEvent实例，该实例是React跨浏览器本机事件的跨浏览器包装器。这些综合事件具有与您惯用的本机事件相同的界面，除了它们在所有浏览器中的工作方式相同。

有点有趣的是，React实际上并未将事件附加到子节点本身。React将使用单个事件侦听器在顶层侦听所有事件。这对性能有好处，也意味着React在更新DOM时无需担心跟踪事件监听器。

## 63、React 中的 `useState()` 是什么？

react hooks的状态钩子

## 64、**state 和 props有什么区别？**

state 和 props都是普通的JavaScript对象。尽管它们两者都具有影响渲染输出的信息，但它们在组件方面的功能不同。即

- `props` 是一个从外部传进组件的参数，主要作为就是从父组件向子组件传递数据，它具有可读性和不变性，只能通过外部组件主动传入新的 `props` 来重新渲染子组件，否则子组件的 `props` 以及展现形式不会改变。
- `state` 的主要作用是用于组件保存、控制以及修改自己的状态，它只能在 `constructor` 中初始化，它算是组件的私有属性，不可通过外部访问和修改，只能通过组件内部的 `this.setState` 来修改，修改 `state` 属性会导致组件的重新渲染。

## 65、**如何创建refs？**

*Refs* 是使用 **React.createRef()** 方法创建的，并通过 `ref` 属性添加到 React 元素上。为了在整个组件中使用 *refs* ，只需将 *ref* 分配给构造函数中的实例属性

```text
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();
  }
  render() {
    return <div ref={this.myRef} />;
  }
}
```

和：

```text
class UserForm extends Component {
  handleSubmit = () => {
    console.log("Input Value is: ", this.input.value)
  }
  render () {
    return (
      <form onSubmit={this.handleSubmit}>
        <input
          type='text'
          ref={(input) => this.input = input} /> // Access DOM input in handle submit
        <button type='submit'>Submit</button>
      </form>
    )
  }
}
```

我们还可以借助闭包在功能组件中使用它。

## 66、**什么是高阶组件？**



## 67、描述 Flux 与 MVC？



## 68、受控组件和非受控组件区别是啥？



## 69、这段代码有什么问题吗？

```text
this.setState((prevState, props) => {
  return {
    streak: prevState.streak + props.count
  }
})
```





## 70、**什么是受控组件？**

在HTML当中，像 `<input>` , `<textarea>` , 和 `<select>` 这类表单元素会维持自身状态，并根据用户输入进行更新。但在React中，可变的状态通常保存在组件的状态属性中，并且只能用 setState() 方法进行更新。

**非受控组件**

非受控组件，即组件的状态不受React控制的组件，例如下边这个

```text
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

class Demo1 extends Component {
    render() {
        return (
            <input />
        )
    }
}

ReactDOM.render(<Demo1/>, document.getElementById('content'))
```

在这个最简单的输入框组件里,我们并没有干涉input中的value展示,即用户输入的内容都会展示在上面。如果我们通过props给组件设置一个初始默认值,defaultValue属性是React内部实现的一个属性,目的类似于input的placeholder属性。

**受控组件**

同样的，受控组件就是组件的状态受React控制。上面提到过，既然通过设置input的value属性, 无法改变输入框值,那么我们把它和state结合在一起,再绑定onChange事件,实时更新value值就行了。

```text
class Demo1 extends Component {
    constructor(props) {
        super(props);
        this.state = {
            value: props.value
        }
    }

    handleChange(e) {
        this.setState({
            value: e.target.value
        })
    }

    render() {
        return (
            <input value={this.state.value} onChange={e => this.handleChange(e)}/>
        )
    }
}
```

## 71、**什么是JSX？**

JSX即JavaScript XML。一种在React组件内部构建标签的类XML语法。JSX为react.js开发的一套语法糖，也是react.js的使用基础。React在不使用JSX的情况下一样可以工作，然而使用JSX可以提高组件的可读性，因此推荐使用JSX。

```text
class MyComponent extends React.Component {
  render() {
    let props = this.props;  
    return (
      <div className="my-component">
      <a href={props.url}>{props.name}</a>
      </div>
    );
  }
}
```

优点：

1.允许使用熟悉的语法来定义 HTML 元素树；

2.提供更加语义化且移动的标签；

3.程序结构更容易被直观化；

4.抽象了 React Element 的创建过程；

5.可以随时掌控 HTML 标签以及生成这些标签的代码；

6.是原生的 JavaScript。

## 72、**为什么不直接更新state状态？**

如果进行如下方式更新状态，那么它将不会重新渲染组件。

```text
//Wrong
    This.state.message =”Hello world”;
```

而是使用 `setState()` 方法。它计划对组件状态对象的更新。状态改变时，组件通过重新渲染做出响应

```text
//Correct
 This.setState({message: ‘Hello World’});
```

注意：可以分配状态的唯一位置是构造函数。

## 73、**React中的这三个点（...）是做什么的？**

...在此React（使用JSX）代码中做什么？它叫什么？

```text
<Modal {...this.props} title='Modal heading' animation={false}>
```

扩展传值符号。它是在ES2018中添加的（数组/可迭代对象的传播较早，ES2015）。

例如，如果this.props包含a：1和b：2，则

```text
<Modal {...this.props} title='Modal heading' animation={false}>
```

与以下内容相同：

```text
<Modal a={this.props.a} b={this.props.b} title='Modal heading' animation={false}>
```

扩展符号不仅适用于该用例，而且对于创建具有现有对象的大多数（或全部）属性的新对象非常方便-在更新状态时会遇到很多问题，因为您无法修改状态直：

```text
this.setState(prevState => {
    return {foo: {...prevState.foo, a: "updated"}};
});
```

## 74、**使用React Hooks有什么优势？**

hooks 是react 16.8 引入的特性，他允许你在不写class的情况下操作state 和react的其他特性。

hooks 只是多了一种写组件的方法，使编写一个组件更简单更方便，同时可以自定义hook把公共的逻辑提取出来，让逻辑在多个组件之间共享。

**Hook 是什么**

Hook 是什么？ Hook 是一个特殊的函数，它可以让你“钩入” React 的特性。例如，useState 是允许你在 React 函数组件中添加 state 的 Hook。稍后我们将学习其他 Hook。

什么时候我会用 Hook？ 如果你在编写函数组件并意识到需要向其添加一些 state，以前的做法是必须将其它转化为 class。现在你可以在现有的函数组件中使用 Hook。

**ReactHooks的优点**

- 无需复杂的DOM结构
- 简洁易懂

## 75、**React中的useState？**

案例：

```text
import { useState } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  )
}
```

**语法:**

```text
function useState<S>(initialState: S | (() => S)): [S, Dispatch<SetStateAction<S>>];
```

其中 state 是他的值, setState 是用来设置值的函数, initialState 是初始值

**useState-initialState**

该初始值可以接受任何参数,但是记得当他接受为一个函数时,就变成了 `Lazy initialization` (延迟初始化)

该函数返回值即为initialState

```text
const [count, setCount] = useState(0);

const [count, setCount] = useState(()=>0);
// 这两种初始化方式 是相等的,但是在函数为初始值时会被执行一次

const [count, setCount] = useState(()=>{
    console.log('这里只会在初始化的时候执行')
    // class 中的 constructor 的操作都可以移植到这里
    return 0
});
// 当第一次执行完毕后 就和另一句的代码是相同的效果了
```

**useState-setState**

也许很多人 在使用 class 的 setState 时候,会经常使用他的回调函数,

但是这里很遗憾,他只接受新的值,如果想要对应的回调,可以使用useEffect,这个问题等会会提供一个跳转链接

## 76、**React中的StrictMode是什么？**

React的StrictMode是一种帮助程序组件，可以帮助您编写更好的react组件，您可以使用包装一些组件， `<StrictMode />` 并且基本上可以：

- 验证内部组件是否遵循某些推荐做法，如果不在控制台中，则会发出警告。
- 验证不赞成使用的方法，如果使用了严格模式，则会在控制台中警告您。
- 通过识别潜在风险来帮助您预防某些副作用。

## 77、**为什么类方法需要绑定？**

在JavaScript中， `this` 的值取决于当前上下文。在React类的组件方法中，开发人员通常希望它引用组件的当前实例，因此有必要 *将* 这些方法 *绑定* 到该实例。通常，这是在构造函数中完成的，例如：

```text
class SubmitButton extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      isFormSubmitted: false
    };
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleSubmit() {
    this.setState({
      isFormSubmitted: true
    });
  }

  render() {
    return (
      <button onClick={this.handleSubmit}>Submit</button>
    )
  }
}
```

## 78、**描述Flux与MVC？**

传统的MVC模式在分离数据（模型），UI（视图）和逻辑（控制器）的关注方面效果很好，但是MVC架构经常遇到两个主要问题：

- **数据流定义不佳：** 跨视图进行的级联更新通常会导致纠结的事件网，难以调试。
- **缺乏数据完整性：** 可以从任何地方对模型数据进行突变，从而在整个UI上产生不可预测的结果。

使用Flux模式，复杂的UI不再受到级联更新的困扰。任何给定的React组件都将能够根据商店提供的数据重建其状态。Flux模式还通过限制对共享数据的直接访问来增强数据完整性。

## 79、**React context是什么？**

React文档官网并未对 `Context` 给出“是什么”的定义，更多是描述使用的 `Context` 的场景，以及如何使用 `Context` 。

官网对于使用 `Context` 的场景是这样描述的：

In Some Cases, you want to pass data through the component tree without having to pass the props down manuallys at every level. you can do this directly in React with the powerful "context" API.

简单说就是，当你不想在组件树中通过逐层传递 `props` 或者 `state` 的方式来传递数据时，可以使用 `Context` 来实现 **跨层级** 的组件数据传递。



![img](https://pic3.zhimg.com/80/v2-aa67b761b6be18ccebe8c7f8a0c98896_720w.jpg)



使用props或者state传递数据，数据自顶下流。



![img](https://pic1.zhimg.com/80/v2-46cec8a69d770264f79ef51940bde46c_720w.jpg)



使用 `Context` ，可以跨越组件进行数据传递。

## 80、**React Fiber是什么？**

React Fiber 并不是所谓的纤程（微线程、协程），而是一种基于浏览器的单线程调度算法，背后的支持 API 是大名鼎鼎的：requestIdleCallback。

Fiberl是一种将 recocilation （递归 diff），拆分成无数个小任务的算法；它随时能够停止，恢复。停止恢复的时机取决于当前的一帧（16ms）内，还有没有足够的时间允许计算。

## 81、diff复杂度原理及具体过程画图

Diff算法的作用是用来计算出 **Virtual DOM** 中被改变的部分，然后针对该部分进行原生DOM操作，而不用重新渲染整个页面。
 Diff算法有三大策略：

> 1. Tree Diff
> 2. Component Diff
> 3. Element Diff

三种策略的执行顺序也是顺序依次执行。
 **Tree Diff** 是对树每一层进行遍历，找出不同，如图1所示。

![img](https:////upload-images.jianshu.io/upload_images/14512370-0dd50bc3cee4237e.png?imageMogr2/auto-orient/strip|imageView2/2/w/533/format/webp)

图1 Tree Diff



**Component Diff** 是数据层面的差异比较

1. 如果都是同一类型的组件(即：两节点是同一个组件类的两个不同实例，比如：<div id="before"></div>与<div id="after"></div>)，按照原策略继续比较Virtual DOM树即可
2. 如果出现不是同一类型的组件，则将该组件判断为dirty component，从而替换整个组件下的所有子节点

**Element Diff**真实DOM渲染，结构差异的比较

首先进行第一层比较，第一层都是R，不发生变化；然后进入第二层Component Diff，发现A组件没有，则删除A及其子组件B、C；最后比较第三层，创建A及其子组件B、C。

当节点处于同一层级时，Diff提供三种DOM操作：**删除**、**移动**、**插入**。

![img](https:////upload-images.jianshu.io/upload_images/14512370-6c9828951b51e151.png?imageMogr2/auto-orient/strip|imageView2/2/w/487/format/webp)

图2 Element Diff



如图2所示，首先将OldVnode 和 NewVnode的首尾位置分别标记为oldS、oldE、newS、newE。

(1)  oldS和newS相同，不发生变化，oldS++，newS++。

> oldS = a，oldE = d
>  newS = a, newE = c

(2)  newS与OldVnode不匹配，oldS前面插入f，newS++。

> oldS = b，oldE = d
>  newS = f， newE = c

(3)  newS与oldE相同，oldE移动到oldS前面，newS++，oldE--。

> oldS = b，oldE = d
>  newS = d， newE = c

(4)  newE与oldE相同，不发生变化，newE--，oldE--。

> oldS = b，oldE = c
>  newS = e， newE = c

(5)  都不相同，oldS前插入newE，删除oldS，oldS++，newS++，newE--，oldE--。

> oldS = b，oldE = b
>  newS = e， newE = e

(6)  oldS > oldE，Diff结束，最后结果为：a、f、d、e、c。

**最后附上核心源码分析：**
 **patch**



```jsx
function patch (oldVnode, vnode) {
    // some code
    if (sameVnode(oldVnode, vnode)) {
        patchVnode(oldVnode, vnode)
    } else {
        const oEl = oldVnode.el // 当前oldVnode对应的真实元素节点
        let parentEle = api.parentNode(oEl)  // 父元素
        createEle(vnode)  // 根据Vnode生成新元素
        if (parentEle !== null) {
            api.insertBefore(parentEle, vnode.el, api.nextSibling(oEl)) // 将新元素添加进父元素
            api.removeChild(parentEle, oldVnode.el)  // 移除以前的旧元素节点
            oldVnode = null
        }
    }
    // some code 
    return vnode
}

patchVnode (oldVnode, vnode) {
    const el = vnode.el = oldVnode.el
    let i, oldCh = oldVnode.children, ch = vnode.children
    if (oldVnode === vnode) return
    if (oldVnode.text !== null && vnode.text !== null && oldVnode.text !== vnode.text) {
        api.setTextContent(el, vnode.text)
    }else {
        updateEle(el, vnode, oldVnode)
        if (oldCh && ch && oldCh !== ch) {
            updateChildren(el, oldCh, ch)
        }else if (ch){
            createEle(vnode) //create el's children dom
        }else if (oldCh){
            api.removeChildren(el)
        }
    }
}
```

这个函数做了以下事情：

- 找到对应的真实dom，称为el
- 判断Vnode和oldVnode是否指向同一个对象，如果是，那么直接return
- 如果他们都有文本节点并且不相等，那么将el的文本节点设置为Vnode的文本节点。
- 如果oldVnode有子节点而Vnode没有，则删除el的子节点
- 如果oldVnode没有子节点而Vnode有，则将Vnode的子节点真实化之后添加到el
- 如果两者都有子节点，则执行updateChildren函数比较子节点

**updateChildren**



```jsx
function updateChildren (parentElm, oldCh, newCh, insertedVnodeQueue, removeOnly) {
    var oldStartIdx = 0;
    var newStartIdx = 0;
    var oldEndIdx = oldCh.length - 1;
    var oldStartVnode = oldCh[0];
    var oldEndVnode = oldCh[oldEndIdx];
    var newEndIdx = newCh.length - 1;
    var newStartVnode = newCh[0];
    var newEndVnode = newCh[newEndIdx];
    var oldKeyToIdx, idxInOld, vnodeToMove, refElm;

    var canMove = !removeOnly;
    {
        checkDuplicateKeys(newCh);
    }
    // oldVnode起始位置小于结束位置并且newVnode起始位置小于结束位置
    while (oldStartIdx <= oldEndIdx && newStartIdx <= newEndIdx) {
        // isUndef 用来判断对象是否等于undefined或者为空，是的话返回true
        if (isUndef(oldStartVnode)) {
            // oldVnode 起始位置oldS++
            oldStartVnode = oldCh[++oldStartIdx]; // Vnode has been moved left
        } else if (isUndef(oldEndVnode)) {
            // oldVnode 结束位置oldE--
            oldEndVnode = oldCh[--oldEndIdx];
        } else if (sameVnode(oldStartVnode, newStartVnode)) {
            // oldS和newS相同，不变化，进行patch，oldS++，newS++
            patchVnode(oldStartVnode, newStartVnode, insertedVnodeQueue, newCh, newStartIdx);
            oldStartVnode = oldCh[++oldStartIdx];
            newStartVnode = newCh[++newStartIdx];
        } else if (sameVnode(oldEndVnode, newEndVnode)) {
            // oldE和newE相同，不变化，进行patch，oldE--，newE--
            patchVnode(oldEndVnode, newEndVnode, insertedVnodeQueue, newCh, newEndIdx);
            oldEndVnode = oldCh[--oldEndIdx];
            newEndVnode = newCh[--newEndIdx];
        } else if (sameVnode(oldStartVnode, newEndVnode)) { // Vnode moved right
            // oldS和newE相同，oldS移动到oldE之后，进行patch，oldS++，newE--
            patchVnode(oldStartVnode, newEndVnode, insertedVnodeQueue, newCh, newEndIdx);
            canMove && nodeOps.insertBefore(parentElm, oldStartVnode.elm, nodeOps.nextSibling(oldEndVnode.elm));
            oldStartVnode = oldCh[++oldStartIdx];
            newEndVnode = newCh[--newEndIdx];
        } else if (sameVnode(oldEndVnode, newStartVnode)) { // Vnode moved left
            // oldE和newS相同，oldE移动到oldS之前，进行patch，oldE--，newS++
            patchVnode(oldEndVnode, newStartVnode, insertedVnodeQueue, newCh, newStartIdx);
            canMove && nodeOps.insertBefore(parentElm, oldEndVnode.elm, oldStartVnode.elm);
            oldEndVnode = oldCh[--oldEndIdx];
            newStartVnode = newCh[++newStartIdx];
        } else {
            // 全都不相同情况下
            // 获取oldVnode->index的key
            if (isUndef(oldKeyToIdx)) {
                oldKeyToIdx = createKeyToOldIdx(oldCh, oldStartIdx, oldEndIdx); 
            }
            idxInOld = isDef(newStartVnode.key)
              ? oldKeyToIdx[newStartVnode.key]
              : findIdxInOld(newStartVnode, oldCh, oldStartIdx, oldEndIdx);
            if (isUndef(idxInOld)) { // New element
                // oldVnode->index为undefined或null，说明没有该元素，创建新的元素
                createElm(newStartVnode, insertedVnodeQueue, parentElm, oldStartVnode.elm, false, newCh, newStartIdx);
            } else {
                  // 获取oldVnode
                  vnodeToMove = oldCh[idxInOld];
                  if (sameVnode(vnodeToMove, newStartVnode)) {
                      // 创建的Vnode和newS相同，插入到oldS之前，进行patch
                      patchVnode(vnodeToMove, newStartVnode, insertedVnodeQueue, newCh, newStartIdx);
                      oldCh[idxInOld] = undefined;
                      canMove && nodeOps.insertBefore(parentElm, vnodeToMove.elm, oldStartVnode.elm);
                  } else {
                      // 相同的key但是不一样的element. 被视为新的element
                      createElm(newStartVnode, insertedVnodeQueue, parentElm, oldStartVnode.elm, false, newCh, newStartIdx);
                  }
            }
            newStartVnode = newCh[++newStartIdx];
        }
    }
    // 当oldS>oldE时，将newS至newE间的全部插入
    if (oldStartIdx > oldEndIdx) {
        refElm = isUndef(newCh[newEndIdx + 1]) ? null : newCh[newEndIdx + 1].elm;
        addVnodes(parentElm, refElm, newCh, newStartIdx, newEndIdx, insertedVnodeQueue);
    } else if (newStartIdx > newEndIdx) {
        // 当newS>newE，将oldS至oldE间的全部删除
        removeVnodes(parentElm, oldCh, oldStartIdx, oldEndIdx);
    }}
```



 

## 82、什么是虚拟DOM？



**虚拟 DOM (VDOM)**是真实 DOM 在内存中的表示。UI 的表示形式保存在内存中，并与实际的 DOM 同步。这是一个发生在渲染函数被调用和元素在屏幕上显示之间的步骤，整个过程被称为**调和**。





## 83 类组件和函数组件之间的区别是啥？



- **类组件**可以使用其他特性，如状态 `state` 和生命周期钩子。
- 当组件只是接收 `props` 渲染到页面时，就是无状态组件，就属于函数组件，也被称为哑组件或展示组件。

函数组件和类组件当然是有区别的，而且函数组件的性能比类组件的性能要高，因为类组件使用的时候要实例化，而函数组件直接执行函数取返回结果即可。为了提高性能，尽量使用函数组件。

区别函数组件类组件是否有 `this`没有有是否有生命周期没有有是否有状态 `state`没有有





## 84 React 中 refs 干嘛用的？



`Refs` 提供了一种访问在`render`方法中创建的 DOM 节点或者 React 元素的方法。在典型的数据流中，`props` 是父子组件交互的唯一方式，想要修改子组件，需要使用新的`pros`重新渲染它。凡事有例外，某些情况下咱们需要在典型数据流外，强制修改子代，这个时候可以使用 `Refs`。

咱们可以在组件添加一个 `ref` 属性来使用，该属性的值是一个回调函数，接收作为其第一个参数的底层 DOM 元素或组件的挂载实例。

```text
class UnControlledForm extends Component {
  handleSubmit = () => {
    console.log("Input Value: ", this.input.value)
  }
  render () {
    return (
      <form onSubmit={this.handleSubmit}>
        <input
          type='text'
          ref={(input) => this.input = input} />
        <button type='submit'>Submit</button>
      </form>
    )
  }
}
```

请注意，`input` 元素有一个`ref`属性，它的值是一个函数。该函数接收输入的实际 DOM 元素，然后将其放在实例上，这样就可以在 `handleSubmit` 函数内部访问它。

经常被误解的只有在类组件中才能使用 `refs`，但是`refs`也可以通过利用 JS 中的闭包与函数组件一起使用。

```text
function CustomForm ({handleSubmit}) {
  let inputElement
  return (
    <form onSubmit={() => handleSubmit(inputElement.value)}>
      <input
        type='text'
        ref={(input) => inputElement = input} />
      <button type='submit'>Submit</button>
    </form>
  )
}
```



## 85 在 React 中如何处理事件



为了解决跨浏览器的兼容性问题，`SyntheticEvent` 实例将被传递给你的事件处理函数，`SyntheticEvent`是 React 跨浏览器的浏览器原生事件包装器，它还拥有和浏览器原生事件相同的接口，包括 `stopPropagation()` 和 `preventDefault()`。

比较有趣的是，React 实际上并不将事件附加到子节点本身。React 使用单个事件侦听器侦听顶层的所有事件。这对性能有好处，也意味着 React 在更新 DOM 时不需要跟踪事件监听器。



## 86 vuex和redux的区别？



vuex是尤雨溪 开发的最佳实践用在VUE框架上的  ，redux并非react独有 ，是一个经典的状态管理设计。



 

## 87 Redux遵循的三个原则是什么？

（1）单一事实来源：

整个应用的状态存储在单个 store 中的对象/状态树里。单一状态树可以更容易地跟踪随时间的变化，并调试或检查应用程序。

（2）状态是只读的：

改变状态的唯一方法是去触发一个动作。动作是描述变化的普通 JS 对象。就像 state 是数据的最小表示一样，该操作是对数据更改的最小表示。

（3）使用纯函数进行更改：

为了指定状态树如何通过操作进行转换，你需要纯函数。纯函数是那些返回值仅取决于其参数值的函数。

![img](https://img2018.cnblogs.com/blog/1090399/201910/1090399-20191020200400326-910364626.png)







## 88 如何创建 refs



Refs 是使用 `React.createRef()` 创建的，并通过 `ref` 属性附加到 React 元素。在构造组件时，通常将 `Refs` 分配给实例属性，以便可以在整个组件中引用它们。

```text
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();
  }
  render() {
    return <div ref={this.myRef} />;
  }
}
```

或者这样用：

```text
class UserForm extends Component {
  handleSubmit = () => {
    console.log("Input Value is: ", this.input.value)
  }
  render () {
    return (
      <form onSubmit={this.handleSubmit}>
        <input
          type='text'
          ref={(input) => this.input = input} /> // Access DOM input in handle submit
        <button type='submit'>Submit</button>
      </form>
    )
  }
}
```



##  



## 89 什么是高阶组件？



**高阶组件(HOC)**是接受一个组件并返回一个新组件的函数。基本上，这是一个模式，是从 React 的组合特性中衍生出来的，称其为**纯组件**，因为它们可以接受任何动态提供的子组件，但不会修改或复制输入组件中的任何行为。

```text
const EnhancedComponent = higherOrderComponent(WrappedComponent);
```

HOC 可以用于以下许多用例

- 代码重用、逻辑和引导抽象
- 渲染劫持
- state 抽象和操作
- props 处理



## 90 在构造函数调用 `super` 并将 `props` 作为参数传入的作用是啥？



在调用 `super()` 方法之前，子类构造函数无法使用`this`引用，ES6 子类也是如此。将 `props` 参数传递给 `super()` 调用的主要原因是在子构造函数中能够通过`this.props`来获取传入的 `props`。

**传递 props**

```text
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    console.log(this.props);  // { name: 'sudheer',age: 30 }
  }
}
```

**没传递 props**

```text
class MyComponent extends React.Component {
  constructor(props) {
    super();
    console.log(this.props); // undefined
    // 但是 Props 参数仍然可用
    console.log(props); // Prints { name: 'sudheer',age: 30 }
  }

  render() {
    // 构造函数外部不受影响
    console.log(this.props) // { name: 'sudheer',age: 30 }
  }
}
```

上面示例揭示了一点。`props` 的行为只有在构造函数中是不同的，在构造函数之外也是一样的。





## 91 什么是控制组件？



在 HTML 中，表单元素如 `<input>`、`<textarea>`和`<select>`通常维护自己的状态，并根据用户输入进行更新。当用户提交表单时，来自上述元素的值将随表单一起发送。

而 React 的工作方式则不同。包含表单的组件将跟踪其状态中的输入值，并在每次回调函数(例如`onChange`)触发时重新渲染组件，因为状态被更新。以这种方式由 React 控制其值的输入表单元素称为**受控组件**。





## 92 如何 React.createElement ？



**问题：**

```text
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
)
```

上述代码如何使用 `React.createElement` 来实现:

```text
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```

## 93 为什么不直接更新 `state` 呢 ?

如果试图直接更新 `state` ，则不会重新渲染组件。

```text
// 错误
 This.state.message = 'Hello world';
```

需要使用`setState()`方法来更新 `state`。它调度对组件`state`对象的更新。当`state`改变时，组件通过重新渲染来响应：

```text
// 正确做法
This.setState({message: ‘Hello World’})
```





## 94 什么是 React Hooks？



**Hooks**是 React 16.8 中的新添加内容。它们允许在不编写类的情况下使用`state`和其他 React 特性。使用 Hooks，可以从组件中提取有状态逻辑，这样就可以独立地测试和重用它。Hooks 允许咱们在不改变组件层次结构的情况下重用有状态逻辑，这样在许多组件之间或与社区共享 Hooks 变得很容易。

## 95 React 中的 `useState()` 是什么？

下面说明`useState(0)`的用途：

```text
...
const [count, setCounter] = useState(0);
const [moreStuff, setMoreStuff] = useState(...);
...

const setCount = () => {
    setCounter(count + 1);
    setMoreStuff(...);
    ...
};
```

`useState` 是一个内置的 React Hook。`useState(0)` 返回一个元组，其中第一个参数`count`是计数器的当前状态，`setCounter` 提供更新计数器状态的方法。

咱们可以在任何地方使用`setCounter`方法更新计数状态-在这种情况下，咱们在`setCount`函数内部使用它可以做更多的事情，使用 Hooks，能够使咱们的代码保持更多功能，还可以避免过多使用基于类的组件。





## 96 React 中的StrictMode(严格模式)是什么？？



React 的`StrictMode`是一种辅助组件，可以帮助咱们编写更好的 react 组件，可以使用`<StrictMode />`包装一组组件，并且可以帮咱们以下检查：

- 验证内部组件是否遵循某些推荐做法，如果没有，会在控制台给出警告。
- 验证是否使用的已经废弃的方法，如果有，会在控制台给出警告。
- 通过识别潜在的风险预防一些副作用。

## 94 为什么类方法需要绑定到类实例？

在 JS 中，`this` 值会根据当前上下文变化。在 React 类组件方法中，开发人员通常希望 `this` 引用组件的当前实例，因此有必要将这些方法绑定到实例。通常这是在构造函数中完成的:

```text
class SubmitButton extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      isFormSubmitted: false
    };
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleSubmit() {
    this.setState({
      isFormSubmitted: true
    });
  }

  render() {
    return (
      <button onClick={this.handleSubmit}>Submit</button>
    )
  }
}
```







## 95 什么是 prop drilling，如何避免？



在构建 React 应用程序时，在多层嵌套组件来使用另一个嵌套组件提供的数据。最简单的方法是将一个 `prop` 从每个组件一层层的传递下去，从源组件传递到深层嵌套组件，这叫做**prop drilling**。

`prop drilling`的主要缺点是原本不需要数据的组件变得不必要地复杂，并且难以维护。

为了避免`prop drilling`，一种常用的方法是使用**React Context**。通过定义提供数据的`Provider`组件，并允许嵌套的组件通过`Consumer`组件或`useContext` Hook 使用上下文数据。



## 96、描述 Flux 与 MVC？



传统的 MVC 模式在分离数据(Model)、UI(View和逻辑(Controller)方面工作得很好，但是 MVC 架构经常遇到两个主要问题:

**数据流不够清晰**:跨视图发生的级联更新常常会导致混乱的事件网络，难于调试。

**缺乏数据完整性**:模型数据可以在任何地方发生突变，从而在整个UI中产生不可预测的结果。

使用 Flux 模式的复杂用户界面不再遭受级联更新，任何给定的React 组件都能够根据 `store` 提供的数据重建其状态。Flux 模式还通过限制对共享数据的直接访问来加强数据完整性。



## 97、受控组件和非受控组件区别是啥？

- **受控组件**是 React 控制中的组件，并且是表单数据真实的唯一来源。
- 非受控组件是由 DOM 处理表单数据的地方，而不是在 React 组件中。

尽管非受控组件通常更易于实现，因为只需使用`refs`即可从 DOM 中获取值，但通常建议优先选择受控制的组件，而不是非受控制的组件。

这样做的主要原因是受控组件支持即时字段验证，允许有条件地禁用/启用按钮，强制输入格式。





## 98、这段代码有什么问题吗？

这段代码有什么问题:

```text
this.setState((prevState, props) => {
  return {
    streak: prevState.streak + props.count
  }
})
```

**答案：**

没有什么问题。这种方式很少被使用，咱们可以将一个函数传递给`setState`，该函数接收上一个 `state`的值和当前的`props`，并返回一个新的状态，如果咱们需要根据以前的状态重新设置状态，推荐使用这种方式。





## 99、什么是 React Context?

`Context` 通过组件树提供了一个传递数据的方法，从而避免了在每一个层级手动的传递 `props` 属性。



## 100、什么是 React Fiber?



**Fiber** 是 React 16 中新的协调引擎或重新实现核心算法。它的主要目标是支持虚拟DOM的增量渲染。**React Fiber** 的目标是提高其在动画、布局、手势、暂停、中止或重用等方面的适用性，并为不同类型的更新分配优先级，以及新的并发原语。

React Fiber 的目标是增强其在动画、布局和手势等领域的适用性。它的主要特性是增量渲染:能够将渲染工作分割成块，并将其分散到多个帧中。

## 101、如何在 ReactJS 的 Props上应用验证？

当应用程序在开发模式下运行时，React 将自动检查咱们在组件上设置的所有 `props`，以确保它们具有正确的数据类型。对于不正确的类型，开发模式下会在控制台中生成警告消息，而在生产模式中由于性能影响而禁用它。强制的 `props` 用 `isRequired`定义的。

下面是一组预定义的 prop 类型:

- React.PropTypes.string
- React.PropTypes.number
- React.PropTypes.func
- React.PropTypes.node
- React.PropTypes.bool

例如，咱们为用户组件定义了如下的`propTypes`

```text
import PropTypes from 'prop-types';

class User extends React.Component {
  render() {
    return (
      <h1>Welcome, {this.props.name}</h1>
      <h2>Age, {this.props.age}
    );
  }
}

User.propTypes = {
  name: PropTypes.string.isRequired,
  age: PropTypes.number.isRequired
};
```





## 102、在 React 中使用构造函数和 getInitialState 有什么区别？

构造函数和`getInitialState`之间的区别就是`ES6`和`ES5`本身的区别。在使用`ES6`类时，应该在构造函数中初始化`state`，并在使用`React.createClass`时定义`getInitialState`方法。

```text
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = { /* initial state */ };
  }
}
```

等价于：

```text
var MyComponent = React.createClass({
  getInitialState() {
    return { /* initial state */ };
  },
});
```



## 103、如何有条件地向 React 组件添加属性？

对于某些属性，React 非常聪明，如果传递给它的值是虚值，可以省略该属性。例如：

```text
var InputComponent = React.createClass({
    render: function() {
      var required = true;
      var disabled = false;

      return (
        <input type="text" disabled={disabled} required={required} />
      );
    }
});
```

渲染结果：

```text
<input type="text" required>
```

另一种可能的方法是：

```text
var condition = true;

var component = (
  <div
    value="foo"
    { ...( condition && { disabled: true } ) } />
);
```





## 104、Hooks会取代 `render props` 和高阶组件吗？

通常，`render props`和高阶组件仅渲染一个子组件。React团队认为，Hooks 是服务此用例的更简单方法。

这两种模式仍然有一席之地(例如，一个虚拟的 `scroller` 组件可能有一个 `renderItem prop`，或者一个可视化的容器组件可能有它自己的 DOM 结构)。但在大多数情况下，Hooks 就足够了，可以帮助减少树中的嵌套。





## 105、如何避免组件的重新渲染？

React 中最常见的问题之一是组件不必要地重新渲染。React 提供了两个方法，在这些情况下非常有用：

- `React.memo()`:这可以防止不必要地重新渲染函数组件
- `PureComponent`:这可以防止不必要地重新渲染类组件

这两种方法都依赖于对传递给组件的`props`的浅比较，如果 `props` 没有改变，那么组件将不会重新渲染。虽然这两种工具都非常有用，但是浅比较会带来额外的性能损失，因此如果使用不当，这两种方法都会对性能产生负面影响。

通过使用 **React Profiler**，可以在使用这些方法前后对性能进行测量，从而确保通过进行给定的更改来实际改进性能。



## 106、什么是纯函数？



纯函数是不依赖并且不会在其作用域之外修改变量状态的函数。本质上，纯函数始终在给定相同参数的情况下返回相同结果。







## 107、当调用`setState`时，React `render` 是如何工作的？

咱们可以将"`render`"分为两个步骤：

1. 虚拟 DOM 渲染:当`render`方法被调用时，它返回一个新的组件的虚拟 DOM 结构。当调用`setState()`时，`render`会被再次调用，因为默认情况下`shouldComponentUpdate`总是返回`true`，所以默认情况下 React 是没有优化的。
2. 原生 DOM 渲染:React 只会在虚拟DOM中修改真实DOM节点，而且修改的次数非常少——这是很棒的React特性，它优化了真实DOM的变化，使React变得更快。



## 108、如何避免在React重新绑定实例？



有几种常用方法可以避免在 React 中绑定方法：

1.将事件处理程序定义为内联箭头函数

```text
class SubmitButton extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      isFormSubmitted: false
    };
  }

  render() {
    return (
      <button onClick={() => {
        this.setState({ isFormSubmitted: true });
      }}>Submit</button>
    )
  }
}
```

2.使用箭头函数来定义方法：

```text
class SubmitButton extends React.Component {
  state = {
    isFormSubmitted: false
  }

  handleSubmit = () => {
    this.setState({
      isFormSubmitted: true
    });
  }

  render() {
    return (
      <button onClick={this.handleSubmit}>Submit</button>
    )
  }
}
```

3.使用带有 Hooks 的函数组件

```text
const SubmitButton = () => {
  const [isFormSubmitted, setIsFormSubmitted] = useState(false);

  return (
    <button onClick={() => {
        setIsFormSubmitted(true);
    }}>Submit</button>
  )
};
```



## 109、你怎样理解“在React中，一切都是组件”这句话



在 react 中，一个页面由往往由多个 component 组成，他们之间彼此独立，互不影响。而一个组件又可以由多个小组件组成。

另一种回答：组件是 React 应用 UI 的构建块的基本单位。这些组件将整个 UI 分成小的独立并可重用的部分。每个组件彼此独立，而不会影响 UI 的其余部分。



## 110、怎样解释 React 中 render() 的目的



用于更新 UI，比如当 state 的值被改变了的时候，render 会自动被调用到。render 中的 jsx 必须被封装到一个最外层元素中，





## 111、如何将两个或多个组件嵌入到一个组件中？



我觉得这道题很奇怪，把两个组件放到一个组件其实就在 render 里面直接用 jsx 排版就可以了。

```js
 render(){
 return(          
 <div>
 <Component1/>
 <Component2 />
 </div>
    );
```







## 112、什么是 Props?



由父组件传过来的参数，是常量，不可修改。且只能父传子，不能子传父。



## 113、React中的状态是什么？它是如何使用的？



由组件自己控制，state 改变时会触发 render。component 中，假如你定义了一个变量，但你不想用它来触发 render，请把它定义成普通类变量。



## 114、区分状态和 props



状态是组件自己维护的，状态的改变会触发 render；props 不可以改变，是父组件传过来的。





## 115、如何更新组件的状态？



使用 this.setState()，**直接修改 state 并不会触发 render()**。







## 116、React 中的箭头函数是什么？怎么用？



箭头函数没有自己的 this，它里面的 this 属于调用它的 component。因此箭头函数中可以获取 component 的 props 和 state。





## 117、 区分有状态和无状态组件。



有状态组件一般是一个 class，无状态组件一般是一个 function



 










