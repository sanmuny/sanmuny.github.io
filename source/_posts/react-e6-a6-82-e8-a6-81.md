---
title: 'React 概要'
date: 2017-09-12 15:54:47
tags: [javascript,react]
published: true
hideInList: false
feature: 
isTop: false
---

React 简介
========

React 是一个开源的javascript库，用来构建用户接口(UI)。下图是React的一些基本信息：

React 的特点
=========

1.  单向数据流
    *   数据自上而下
        *   Props 不可变
        *   States可变
        *   任何数据、函数都可以作为属性(props)传    递给子组件(Props,　States,　Handlers,　Styles)
    *   事件冒泡
        *   子组件触发的事件会传递到父组件
2.  虚拟DOM
    *   Javascript内存中的DOM数据缓存
    *   组件发生变化时渲染虚拟DOM
    *   React将虚拟DOM与DOM的差异转化为一系列的DOM操作
    *   将这些操作同步应用到真实的DOM中
3.  JSX
    *   可以使用HTML语法创建Javascript 对象
    *   代码更少
    *   会被转化为Javascript执行
        *   Offline - react-tools
        *   In-Browser - JSXTransformer
    *   不改变Javascript语义
4.  其他特点
    *   元素嵌套: 每个组件只允许有一个顶层元素(div, table ...)
    *   自定义属性: 除了HTML标签自带属性之外，React也支持自定义属性，自定义属性需要加上data- 前缀
    *   JavaScript表达式: 可以通过{}在JSX中使用Javascript表达式
    *   三目运算符: JSX中不能使用if-else但可以使用三目运算

React元素
=======

    const element = <h1>Hello, world</h1>;

*   React 元素 != DOM 元素
*   将元素渲染到 DOM 中  
      
    ReactDOM.render(element, document.getElementById('root'));  
     
*   更新元素 \- 重新 Render
*   更新元素只会更新变化的部分

```
    function tick() {
        const element = (
            <div>
                <h1>Hello, world!</h1>
                <h2>It is {new Date().toLocaleTimeString()}.</h2>
            </div>
        );
        ReactDOM.render(
        element,
        document.getElementById('root')
        );
    }
    setInterval(tick, 1000);
```

React组件
=======

组件可以将UI切分成一些的独立的、可复用的部件，这样你就只需专注于构建每一个单独的部件。

*   如何定义一个组件
    *   函数
        
```
function Welcome(props) {
    return <h1>Hello, {props.name}</h1>;
}
```
        
    *   class
 ```       
    class Welcome extends React.Component {
        render() {
        return <h1>Hello, {this.props.name}</h1>;
        }
    }
```        
*   渲染组件
    *   `const element = <Welcome name="Sara" />;`
*   组合组件
```
function App() {
    return (
    <div>
        <Welcome name="Sara" />
        <Welcome name="Cahal" />
        <Welcome name="Edite" />
    </div>
    );
}
```

状态与生命周期
=======

因为react中props为只读，如果需要更新数据，可以使用react中的状态。在使用状态之前，需要把组件函数转变为类。

*   创建一个名称扩展为 React.Component 的ES6 类
*   创建一个叫做render()的空方法
*   将函数体移动到 render() 方法中
*   在 render() 方法中，使用 this.props 替换 props
*   删除剩余的空函数声明

将组件函数转化为类之后就可以添加状态了：

*   在 render() 方法中使用 this.state.date 替代 this.props.date
*   添加一个类构造函数来初始化状态 this.state
*   使用 this.setState() 来更新组件局部状
```
class Clock extends React.Component {
    constructor(props) {
    super(props);
    this.state = {date: new Date()};
    }
    componentDidMount() {
    this.timerID = setInterval(
        () => this.tick(),
        1000
    );
    }
    componentWillUnmount() {
    clearInterval(this.timerID);
    }
    tick() {
    this.setState({
        date: new Date()
    });
    }
    render() {
    return (
        <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
        </div>
    );
    }
}
ReactDOM.render(
    <Clock />,
    document.getElementById('root')
);
```
事件处理
====

*   React事件绑定属性的命名采用驼峰式写法，而不是小写。
*   如果采用 JSX 的语法你需要传入一个函数作为事件处理函数，而不是一个字符串(DOM元素的写法)
*   React不能使用返回 false 的方式阻止默认行为

条件渲染
====

*   使用 JavaScript 操作符 if 或条件运算符来创建表示当前状态的元素,然后让 React 根据它们来更新 UI
*   使用变量来储存元素
```
    function Greeting(props) {
        const isLoggedIn = props.isLoggedIn;
        if (isLoggedIn) {
        return <UserGreeting />;
        }
        return <GuestGreeting />;
    }
    ReactDOM.render(
        // Try changing to isLoggedIn={true}:
        <Greeting isLoggedIn={false} />,
        document.getElementById('root')
    );
```
*   &&运算符
 ```   
    function Mailbox(props) {
        const unreadMessages = props.unreadMessages;
        return (
        <div>
            <h1>Hello!</h1>
            {unreadMessages.length > 0 &&
            <h2>
                You have {unreadMessages.length} unread messages.
            </h2>
            }
        </div>
        );
    }
```
*   三目运算符
  ```  
    render() {
        const isLoggedIn = this.state.isLoggedIn;
        return (
        <div>
            The user is <b>{isLoggedIn ? 'currently' : 'not'}</b> logged in.
        </div>
        );
    }
 ```   
列表渲染
====

*   React 可以直接渲染列表
*   Keys可以在DOM中的某些元素被增加或删除的时候帮助React识别哪些元素发生了变化。因此应当给数组中的每一个元素赋予一个确定的标识。
*   元素的key只有在它和它的兄弟节点对比时才有意义
*   jsx中可以使用map
```
    function ListItem(props) {
      return <li>{props.value}</li>;
    }
    function NumberList(props) {
      const numbers = props.numbers;
      const listItems = numbers.map((number) =>
        <ListItem key={number.toString()}
                  value={number} />
      );
      return (
        <ul>
          {listItems}
        </ul>
      );
    }
```
表单
==

*   HTML表单元素与React中的其他DOM元素有所不同,因为表单元素生来就保留一些内部状态
*   在HTML当中，像`<input>`,`<textarea>`, 和 `<select>`这类表单元素会维持自身状态，并根据用户输入进行更新
*   在React中，可变的状态通常保存在组件的状态属性中，并且只能用 setState(). 方法进行更新
```
    class Reservation extends React.Component {
      constructor(props) {
        super(props);
        this.state = {
          isGoing: true,
          numberOfGuests: 2
        };
        this.handleInputChange = this.handleInputChange.bind(this);
      }
      handleInputChange(event) {
        const target = event.target;
        const value = target.type === 'checkbox' ? target.checked : target.value;
        const name = target.name;
        this.setState({
          [name]: value
        });
      }
      render() {
        return (
          <form>
            <label>
              Is going:
              <input
                name="isGoing"
                type="checkbox"
                checked={this.state.isGoing}
                onChange={this.handleInputChange} />
            </label>
            <br />
            <label>
              Number of guests:
              <input
                name="numberOfGuests"
                type="number"
                value={this.state.numberOfGuests}
                onChange={this.handleInputChange} />
            </label>
          </form>
        );
      }
    }
```