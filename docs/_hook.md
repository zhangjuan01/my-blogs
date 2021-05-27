# 介绍Hook
HOOK是react V16.8的新特性，可以让我们在不使用class的情况下使用state以及其他的react特性
## Hook的设计动机和概览
  * **完全可选的** 100%向后兼容，不具备任何破坏性，也就是说你的项目中既可以保留class组件也可以引入hook
  ### 初始动机
  React Hook是react团队在开发实践中，逐渐意识到的改进点，背后涉及到类组件和函数组件的对比，所以我们首先要知道什么是类组件和函数组件以及两者之间的对比。
  ### 类组件（class component）
  基于ES6 class，通过继承React.Component定义组件
  <code>
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
  </code>
   ### 函数组件/无状态组件（Function Component/Stateless Component）
  以函数形式定义的组件，在早期没有react-hooks的时候，函数组件内部无法定义和维护state，所以也叫无状态组件。
  示例：
  <pre>
  function DemoFunction(props) {
         const { text } = props
         return (
             <p>{`function 组件所接收到的来自外界的文本内容是：[${text}]`}</p>
         )
       }
  </pre>

### 类组件和函数组件的对比
通过上面两个示例，除了在写法上的区分还有一些用法上的区别包括但不限于：
* 类组件需要继承 函数组件不需要
* 类组件可以访问生命周期方法，函数组件不可以
* 类组件有实例化后的this，并可以基于this做各种操作，函数组件不可以。
* 类组件有自己的state，函数组件没有

## Hook的设计动机？