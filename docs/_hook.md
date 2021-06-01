# 介绍Hook
HOOK是react V16.8的新特性，可以让我们在不使用class的情况下使用state以及其他的react特性
## Hook的设计动机和概览
  * **完全可选的** 100%向后兼容，不具备任何破坏性，也就是说你的项目中既可以保留class组件也可以引入hook
  ### 初始动机
  React Hook是react团队在开发实践中，逐渐意识到的改进点，背后涉及到类组件和函数组件的对比，所以我们首先要知道什么是类组件和函数组件以及两者之间的对比。
  ### 类组件（class component）
  基于ES6 class，通过继承React.Component定义组件
  ```js
class demo extends React.Component {
       state = {
       number: 1,
       }
       // 编写生命周期方法 didMount
         componentDidMount() {
           // 省略业务逻辑
         }
       handleChange = (next) => {
         this.setState({number: next})
       }
       render () {
         return (
               <button onClick={this.handleChange}>点击一下</button>
         )
       }     
    }
  ```
  
   ### 函数组件/无状态组件（Function Component/Stateless Component）
  以函数形式定义的组件，在早期没有react-hooks的时候，函数组件内部无法定义和维护state，所以也叫无状态组件。
  示例：
 ```js
  function DemoFunction(props) {
         const { text } = props
         return (
             <p>{`function 组件所接收到的来自外界的文本内容是：[${text}]`}</p>
         )
       }
```


### 类组件和函数组件的对比
通过上面两个示例，除了在写法上的区分还有一些用法上的区别包括但不限于：
* 类组件需要继承 函数组件不需要
* 类组件可以访问生命周期方法，函数组件不可以
* 类组件有实例化后的this，并可以基于this做各种操作，函数组件不可以。
* 类组件有自己的state，函数组件没有
可以发现在hook出现之前，类组件的能力明显强于函数式组件，但这并不能说类组件强于函数值组件。同理也不能因为函数组件的轻量优雅，想着有一天能完全替代类组件，这两种组件各有各的优劣，我们应该更关注两者的不同，在不同的使用场景中选择合适的组件。

### 深入理解类组件
类组件式典型的面向对象的编程，对于面向对象这个概念，我们总会想到"继承"和"封装"
以下类组件的示例：
```js
// 代码源自深入浅出搞定React  -- 修言
class DemoClass extends React.Component {
  // 初始化类组件的 state
  state = {
    text: ''
  }

  // 编写生命周期方法 didMount
  componentDidMount() {
    // 省略业务逻辑
  }

  // 编写自定义的实例方法
  changeText = (newText) => {
    // 更新 state
    this.setState({
      text: newText
    })
  }

  // 编写生命周期方法 render
  render() {
    return (
      <div className="demoClass">
        <p>{this.state.text}</p>
        <button onClick={this.changeText}>点我修改</button>
      </div>
    )
  }
}
```
React类组件内置了很多属性和方法，只需要继承React.Component既可以获取他们。但多未必是好，更多的属性的方法意味着更高的学习成本。并且类组件太重了，对于一些简单的问题就显得过于复杂。更致命的是，内部逻辑难以复用。

### 深入理解函数组件
再来看下函数组件的示例：
```js
// 代码源自 “深入浅出搞定 React -- 修言”

// 代码源自 “深入浅出搞定 React -- 修言”

function DemoFunction(props) {
  const { text } = props 

  const showAlert = ()=> {
    alert(`我接收到的文本是${text}`)
  } 

  return (
    <div className="demoFunction">
      <p>{`function 组件所接收到的来自外界的文本内容是：[${text}]`}</p>
      <button onClick={showAlert}>点击弹窗</button>
    </div>
  )
}
```
相较于类组件，函数组件轻量灵活易于组织维护，学习成本较低。
更重要的是React的作者Dan Abramov写过一篇关于[函数式组件与类组件有何不同](https://overreacted.io/zh-hans/how-are-function-components-different-from-classes/)的文章，整篇文章论证了一件事：
> 函数值组件捕获了渲染所用的值

React函数式组件和类组件是否有任何根本上的区别？当然有--在心智模型上，面向对象和函数式编程的两套不同的设计思想之间的差异。
> 函数式组件更加契合React框架的设计理念： UI = f(data)

React组件本身的定位就是函数，能够把数据变为视图的函数。
作为开发者，我们所编写的是声明式的代码，而 React 框架的主要工作就是把声明式的代码转换为命令式的 DOM 操作，把数据映射到用户可见的视图。所以，React 的数据应该和渲染绑在一起，而类组件做不到这一点。
为什么类组件做不到？我们来看下文章的示例代码：
类组件：
```js
import React from 'react';

class ProfilePage extends React.Component {
  showMessage = () => {
    alert('Followed ' + this.props.user);
  };

  handleClick = () => {
    setTimeout(this.showMessage, 3000);
  };

  render() {
    return <button onClick={this.handleClick}>Follow</button>;
  }
}

export default ProfilePage;
```
函数组件：
```js
import React from 'react';

function ProfilePage(props) {
  const showMessage = () => {
    alert('Followed ' + props.user);
  };

  const handleClick = () => {
    setTimeout(showMessage, 3000);
  };

  return (
    <button onClick={handleClick}>Follow</button>
  );
}

export default ProfilePage;

```
尝试按照以下顺序来操作两个按钮：
* 点击 其中某一个 Follow 按钮
* 在3秒内 切换 选中的账号
* 查看 弹出的文本
你将会看到：
* 当使用 函数式组件 实现的 ProfilePage, 当前账号是 Dan 时点击 Follow 按钮，然后立马切换当前账号到 Sophie，弹出的文本将依旧是 'Followed Dan'。
* 当使用 类组件 实现的 ProfilePage, 弹出的文本将是 'Followed Sophie'

> 类组件的结果：user 的内容是通过 props 传递的，而 props 是不可变的，为什么弹出的文字变了（从 Dan 变成 Sophie）？
this.props.user 读取数据。在 React 中 props 是不可变的（immutable），但是 this 是永远可变的（mutable）。事实上，这就是类组件 this 存在的意义。React 本身会随着时间的推移而改变，以便可以在 render 方法及生命周期方法中获得最新的实例。
如果我们说UI在概念上是当前应用状态的一个函数，那么事件处理程序则是渲染结果的一部分 —— 就像视觉输出一样。我们的事件处理程序“属于”一个拥有特定 props 和 state 的特定渲染。
然而，调用一个回调函数读取 this.props 的 timeout 会打断这种关联。我们的 showMessage 回调并没有与任何一个特定的渲染“绑定”在一起，所以它“失去”了正确的 props。从 this 中读取数据的这种行为，切断了这种联系。

> 函数式组件的结果：
props 会在 ProfilePage 函数执行的时候就被捕获，而 props 本身是不可变的，所以我们可以充分保证从函数执行开始，到之后的任何时机读取的 props 都是最初捕获的 props
当父组件传入新的 props 重新渲染，本质上是基于新的 props 发起一次全新的函数调用，并不会影响上一次调用的 props也就是说：函数式组件捕获了渲染所用的值。
经过编程实践，React 团队认识到了，函数组件更加符合设计理念、也更有利于逻辑拆分和复用。接下来就是“用脚投票”，用实际行动支持开发者编写函数组件。于是，React-Hooks 便应运而生。
## Hooks 的本质
***一套能够使函数组件更强大，更灵活的"钩子"***。让函数组件拥有类组件的能力，并保留轻量，优雅的特性。
Hooks是使用你已经知道的React特性的一种更直接的方式--比如state，生命周期，context。以及refs。他们并没有从根本上改变React的工作方式，你对组件，porps，以及自顶向下的数据流只是并没有改变。

### Hooks的核心API

### useState()
#### 为函数组件引入状态（state）
有了useState之后，我们就可以直接在函数组件中引入state


