---
title: 'React hooks 概要'
date: 2021-08-23 16:21:49
tags: []
published: true
hideInList: false
feature: 
isTop: false
---
### React 基础知识回顾
React(响应)的设计理念是，当数据发生变化时，UI能自动把变化反映出来。它的诞生颠覆了传统的web UI开发模式，它把UI的开发从复杂的DOM操作中解脱出来，让开发者专注于数据、逻辑和UI组件本身。

<!-- more -->


![](https://sanmuny.github.io/post-images/1629707806132.webp)

#### 组件 (component)
React 是通过组件的方式来组织和描述UI的。组件可以分为两种类型：
1. 内置组件。内置组件其实就是映射到 HTML 节点的组件，例如 div、input、table 等等，作为一种约定，它们都是小写字母。
2. 自定义组件。自定义组件其实就是自己创建的组件，使用时必须以大写字母开头，例如 TopicList、TopicDetail。

![](https://sanmuny.github.io/post-images/1629708149299.webp)

```
function CommentBox() {
  return (
    <div>
      <CommentHeader />
      <CommentList />
      <CommentForm />
    </div>
  );
}
```

#### 状态 (state)和属性(props)
组件的状态用于维护组件用到的数据，而属性则用于父组件向子组件传递数据。

```
import React from 'react';
import {
  Checkbox, Typography, List, ListItem, ListItemText, CircularProgress,
} from '@material-ui/core';

class ToDoList extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      loading: true,
      items: [],
    };
    this.handleCheck = this.handleCheckEvent.bind(this);
  }

  componentDidMount() {
    this.getToDoList();
    let nr = 0;
    this.state.items.forEach((item) => {
      if (item.done === false) {
        nr += 1;
      }
    });
    document.title = `${nr} itmes left`;
  }

  componentDidUpdate() {
    let nr = 0;
    this.state.items.forEach((item) => {
      if (item.done === false) {
        nr += 1;
      }
    });
    document.title = `${nr} itmes left`;
  }

  handleCheckEvent(id) {
    const tmpState = this.state;
    tmpState.items.forEach((task, index, array) => {
      if (task.id === id) {
        array[index].done = !array[index].done;
      }
    });
    this.setState(tmpState);
  }

  getToDoList() {
    setTimeout(() => {
      this.setState({
        loading: false,
        items: [
          { id: 0, text: 'Learn JavaScript', done: false },
          { id: 1, text: 'Learn React', done: false },
          { id: 2, text: 'Play around in JSFiddle', done: true },
          { id: 3, text: 'Build something awesome', done: true },
        ],
      });
    }, 2000);
  }

  render() {
    return (
      <div className="todo-list-container">
        <Typography variant="h6">
          Todos
        </Typography>
        {this.state.loading ? <div className="loading-container"><CircularProgress /></div>
          : (
            <List dense className="todo-list">
              {this.state.items.map((item) => (
                <ListItem key={item.id} className="todo-item">
                  <Checkbox
                    edge="start"
                    checked={item.done}
                    tabIndex={-1}
                    disableRipple
                    inputProps={{ 'aria-labelledby': item.id }}
                    onChange={() => { this.handleCheck(item.id); }}
                  />
                  <ListItemText className={item.done ? 'done' : ''} id={item.id} primary={item.text} />
                </ListItem>
              ))}
            </List>
          )}
      </div>
    );
  }
}

export default ToDoList;
```

#### JSX
JSX 并不是一个新的模板语言，而可以认为是一个语法糖。

```
React.createElement(
  "div",
  null,
  React.createElement(
    "Typography",
    ...
  ),
  React.createElement(
    "List",
    ...
  );
);
```

React.createElement API的作用就是创建一个组件的实例。此外，这个 API 会接收一组参数：第一个参数表示组件的类型；第二个参数是传给组件的属性，也就是 props；第三个以及后续所有的参数则是子组件。

### React 引入Hooks的原因

React 组件的模型其实很直观，就是从 Model 到 View 的映射，这里的 Model 对应到 React 中就是 state 和 props。

![](https://sanmuny.github.io/post-images/1629798317799.webp)

虽然之前的react也支持函数作为组件，但因为函数组件只能是纯函数，没法使用state，所以更多的情形是用class来实现UI组件。但我们可以从上图可以看到，state/props 到view的本质就是一种函数关系。react用到的class并没有真正使用到面向对象的优势，比如说子组件和父组件并不是一种继承关系，组件之间也不会调用对方的方法。

于是Hooks被引入到react中，Hooks能够把一个外部的数据绑定到函数的执行。当数据变化时，函数能够自动重新执行。这样的话，任何会影响 UI 展现的外部数据，都可以通过这个机制绑定到 React 的函数组件。

前面我们说了，react 引入hooks的原因是其本质是函数映射，那么把react组件函数化最大的优势是什么？答案就是数据和逻辑复用。class组件之间是没法共享state的，父组件的state只能通过子组件的props传递给子组件。没有父子关系的组件之间要共享数据只能通过高阶组件。

### React常用的Hook

#### useState
useState可以让函数组件具有维护状态的能力。参考前面Counter的例子，`const [count, setCount] = React.useState(0);`定义了名为count的状态，使得函数组件Counter的多次渲染可以共享它。0是状态的默认值，而setCount则是用来改变state的函数，调用setCount会让react刷新组件。useState 这个 Hook 的用法总结出来就是这样的：
1. useState(initialState) 的参数 initialState 是创建 state 的初始值，它可以是任意类型，比如数字、对象、数组等等。
2. useState() 的返回值是一个有着两个元素的数组。第一个数组元素用来读取 state 的值，第二个则是用来设置这个 state 的值。
3. 如果要创建多个 state，那么我们就需要多次调用 useState。例如

```
// 定义一个年龄的 state，初始值是 42
const [age, setAge] = useState(42);
// 定义一个水果的 state，初始值是 banana
const [fruit, setFruit] = useState('banana');
// 定一个一个数组 state，初始值是包含一个 todo 的数组
const [todos, setTodos] = useState([{ text: 'Learn Hooks' }]);
```

#### useEffect
副作用(Side Effect)指的是与UI渲染没有直接关系的操作，例如从服务器端获取数据等。React使用useEffect来替代class中的生命周期函数。useEffect接受两个参数，一个是callback函数，另外一个是执行callback函数的条件。
```
useEffect(callback, dependencies)
```
1. 当只传递callback，没有dependencies时，callback会在组件每次渲染的时候执行一次。
2. 当dependencies为空数组`[]`时，callback会在组件第一次渲染的时候执行，相当于componentDidMount
3. 当callback返回一个函数时，这个函数会在组件卸载的时候执行一次，相当于componentWillUnmount

React hooks的使用规则：
1. 在useEffect回调函数中使用的变量，都必须在依赖项中声明
2. Hooks不能出现在条件语句和循环中，也不能出现在return之后
3. Hooks只能在函数组件或者自定义Hook中使用

使用eslint可以检查这些规则：
1. 安装eslint插件：`npm install --save-dev eslint-plugin-react-hooks`
2. 在eslint配置文件中添加规则：`react-hooks/rules-of-hooks` 以及`react-hooks/exhaustive-deps`

#### useCallback
每次state的变化都会导致组件函数重新执行一遍，事件处理函数就会被定义多遍，而且事件处理函数通常是闭包，不会被垃圾回收清理掉。事件处理函数会作为props传递给组件，重新定义事件处理函数也会导致组件的频繁更新。为了提升性能，useCallback被引入到React Hooks之中。useCallback的定义如下：
```
useCallback(fn, [deps])
```
fn是定义的函数，deps是依赖变量的数组。只有deps中的某个变量发生变化时，fn才会被重新声明。

#### useMemo
类似的，由于每次渲染都会重新执行组件函数，那些耗时的计算也会重复进行。useMemo则用于避免重复的耗时计算。
```
const result = useMemo(fn, [deps])
```
同样，只有deps中的变量发生变化时，result才会用fn重新计算。

#### useRef
useRef可以使函数组件的多次渲染之间共享数据。它相当于在函数组件之外创建了一个存储对象，其current属性值可以在多次渲染之间共享。

```
const container = useRef(initialValue)
```

#### useContext
React组件之间传递数据的方式通常是父子组件之间使用props来进行。React context API可以使得各个组件可以共享上下文数据。主要用于language, theme 等上下文的共享。大规模的数据共享还是应该使用redux这类的状态管理框架来进行。

![](https://sanmuny.github.io/post-images/1631515426473.png)

要使用context数据，首先需要在顶层组件中定义context.

```
const ThemeContext = React.createContext('light');

<ThemeContext.Provider value='light'>
    <App />
</ThemeContest.Provider>
```

然后子组件中就可以使用useContext来获取context的值了。

```
const theme = useContext(ThemeContext);

<Button className={theme}>Submit</Button>
```

下面的例子展示了如何使用React Hooks将上面的ToDoList class组件改造为函数组件。

```
const ToDoItems = () => {
  const [loading, setLoading] = useState(true);
  const [todos, setTodos] = useState([]);
  const theme = useContext(ThemeContext);
  const nrTasksLeft = useMemo(() => {
    let nr = 0;
    todos.forEach((item) => {
      if (item.done === false) {
        nr += 1;
      }
    });
    return nr;
  }, [todos]);

  useEffect(() => {
    document.title = `${nrTasksLeft} itmes left`;
  });

  useEffect(() => {
    const getToDoList = () => {
      setTimeout(() => {
        setLoading(false);
        setTodos([
          { id: 0, text: 'Learn JavaScript', done: false },
          { id: 1, text: 'Learn React', done: false },
          { id: 2, text: 'Play around in JSFiddle', done: true },
          { id: 3, text: 'Build something awesome', done: true },
        ]);
      }, 2000);
    };
    getToDoList();
  }, []);

  const handleCheck = useCallback((id) => {
    const tmpTodos = [...todos];
    tmpTodos.forEach((todo, index) => {
      if (todo.id === id) {
        tmpTodos[index].done = !tmpTodos[index].done;
      }
    });
    setTodos(tmpTodos);
  }, [todos]);

  return (
    <div className="todo-list-container">
      <Typography variant="h6">
        Todos
      </Typography>
      {loading ? <div className="loading-container"><CircularProgress /></div>
        : (
          <List dense className="todo-list">
            {todos.map((item) => (
              <ListItem key={item.id} className={`todo-item ${theme}`}>
                <Checkbox
                  className={theme}
                  edge="start"
                  checked={item.done}
                  tabIndex={-1}
                  disableRipple
                  inputProps={{ 'aria-labelledby': item.id }}
                  onChange={() => {
                    handleCheck(item.id);
                  }}
                />
                <ListItemText className={item.done ? `${theme} done` : `${theme}`} id={item.id} primary={item.text} color={theme === 'light' ? 'black' : 'white'} />
              </ListItem>
            ))}
          </List>
        )}
    </div>
  );
};
```
### 自定义Hooks
除了上述react内置的hooks之外，用户可以根据自己的需求利用上述hooks来创建自定义hooks。自定义hooks主要有如下4种使用情况：
1. 抽取业务逻辑，让组件之间能代码复用。
2. 封装通用逻辑，让类似的逻辑只实现一遍。
3. 监听浏览器状态，让组件能够使用浏览器的状态数据。
4. 拆分复杂组件，让组件的逻辑更加清晰明了。

例如，定义如下的自定义hook可以让组件更简单的使用浏览器宽度。
```
import { useState, useEffect } from 'react';

export const useWindowSize = () => {
  const [width, setWidth] = useState(window.innerWidth);
  useEffect(() => {
    const handleWindowSize = () => { setWidth(window.innerWidth); };
    window.addEventListener('resize', handleWindowSize);
    return () => {
      window.removeEventListener('resize', handleWindowSize);
    };
  });
  return width;
};
```
组件使用自定义hook时只需要引入即可。
```
import { useWindowSize } from '../hooks';
...
const width = useWindowSize();
...
return <div>window size: {width}</div>
```

### 小结
| Hook | 用途 |
|---|---|
| const [width, setWidth] = useState(window.innerWidth) | 定义组件的状态 |
| useEffect(fn, [deps]) | 替代class组件中的声明周期函数 |
| useCallback(fn, [deps]) | 避免fn函数的重复定义和组件的重新渲染，只有当deps中的变量变化时才会重新定义 |
| const result = useMemo(fn, [deps]) | 避免数据的重复计算 |
| const container = useRef(initialValue) |  在函数组件的多次渲染之间共享数据 |
| useContext | 用于组件使用页面的上下文 |
| 自定义Hook | 逻辑复用，监听浏览器状态， 拆分复杂组件 |






