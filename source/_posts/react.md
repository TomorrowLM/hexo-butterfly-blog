---
slug: react技术栈
title: react技术栈
description: react学习
keywords: react,redux,router,hooks,生命周期,next
category: react技术栈
tags: [react,redux,router,hooks,生命周期,next]
author: liming
date: 25-September-2020
sticky: 1
swiper_index: 1
swiper_desc: 关于一些react,redux,router,hooks,生命周期,next的总结！
cover: https://i0.hippopx.com/photos/457/88/1021/microphone-boy-studio-screaming-preview.jpg
---

# react原理

## 虚拟DOM

https://www.jianshu.com/p/af0b398602bc

### **背景**

前端主流框架 vue 和 react 中都使用了虚拟DOM（virtual DOM）技术，因为渲染真实DOM的开销是很大的，性能代价昂贵，比如有时候我们修改了某个数据，如果直接渲染到真实dom上会引起整个dom树的重绘和重排，而我们只需要更新修改过的那一小块dom而不要更新整个dom，这时使用diff算法能够帮助我们

#### **DOM`文档对象模型`**

浏览器渲染引擎工作流程都差不多，大致分为5步，**创建DOM树——创建StyleRules——创建Render树——布局Layout——绘制Painting**

-   第一步，用HTML分析器，分析HTML元素，**构建一颗DOM树**(标记化和树构建)。
-  第二步，用CSS分析器，分析CSS文件和元素上的inline样式，生成页面的样式表。
-   第三步，将DOM树和样式表，关联起来，构建一颗Render树(这一过程又称为Attachment)。每个DOM节点都有**attach方法，接受样式信息**，返回一个render对象(又名renderer)。这些render对象最终会被构建成一颗Render树。
-   第四步，有了Render树，浏览器开始布局，为每个Render树上的节点确定一个在显示屏上出现的精确坐标。
-   第五步，Render树和节点显示坐标都有了，就调用每个节点**paint方法，把它们绘制**出来。

**缺点**

- 修改了某个数据，会直接渲染到真实dom上引起整个dom树的重绘和重排
- 原生JS或JQ操作DOM时，浏览器会从构建DOM树开始从头到尾执行一遍流程。在一次操作中，我需要更新10个DOM节点，浏览器收到第一个DOM请求后并不知道还有9次更新操作，因此会马上执行流程，最终执行10次。

#### **VDOM**

虚拟dom(JS模拟DOM中的真实节点对象)， 通过VDom和真实DOM的比对，再通过特定的render方法将其渲染成真实的DOM节点。

 ![img](https://img2020.cnblogs.com/blog/1569466/202101/1569466-20210129140258056-1083022081.png) 

**优点：**

- 只会更新对应的节点。**diff算法**
- 更新10个DOM节点，只会执行最后一次。**例如批处理**

### diff

传统 diff 算法通过循环递归对节点进行依次对比，效率低下，算法复杂度达到 O(n^3)，其中 n 是树中节点的总数。O(n^3) 到底有多可怕，这意味着如果要展示1000个节点，就要依次执行上十亿次的比较。

**React 通过制定大胆的策略，将 O(n^3) 复杂度的问题转换成 O(n) 复杂度的问题**。 

**diff 策略**

- **tree diff**：新旧DOM树，逐层对比的方式
  
> DOM节点跨层级的移动操作特别少，可以忽略不计。

![img](https://images2018.cnblogs.com/blog/1455367/201808/1455367-20180808083547179-1944470540.jpg)

只会对相同颜色方框内的 DOM 节点进行比较，即同一个父节点下的所有子节点。

当发现节点已经不存在，则该节点及其子节点会被完全删除掉，不会用于进一步的比较。

这样只需要对树进行一次遍历，便能完成整个 DOM 树的比较。

- **component diff**：
   React 是基于组件构建应用的，对于组件间的比较所采取的策略也是简洁高效。 

  - 如果是同一类型的组件，按照原策略继续比较 virtual DOM tree。
  - 如果不是，则将该组件判断为 dirty component，从而替换整个组件下的所有子节点
  - 对于同一类型的组件，有可能其 Virtual DOM 没有任何变化，如果能够确切的知道这点那可以节省大量的 diff 运算时间，因此 React 允许用户通过 shouldComponentUpdate() 来判断该组件是否需要进行 diff。
  
- **element diff**：
  
   当节点处于同一层级时，React diff 提供了三种节点操作
  
  INSERT_MARKUP:新的组件类型不在旧集合中，即全新的节点，需要对新节点进行插入操作。
  MOVE_EXISTING:旧集合中有新组件类型，且element是可更新的类型，这时候就需要做移动操作，可以复用以前的DOM节点。
  REMOVE_NODE:旧组件类型，在新集合里也有，但对应的element不同则不能直接复用和更新，需要执行删除操作，或者旧组件不在新集合里的，也需要执行删除操作。
  ————————————————
  
  对于同一层级的一组子节点，通过唯一id区分。

## fiber

**fiber就是链表结构的虚拟Dom**

1. 为什么需要fiber

   对于大型项目，组件树会很大，这个时候**递归遍历**的成本就会很高，会造成主线程被持续占用，结果就是主线程上的布局，动画等周期性任务无法立即得到处理，造成视觉上的卡顿，影响用户体验。

2. 在react 16之后发布的⼀种react 核⼼算法，React Fiber是对核⼼算法的⼀次重新实现(官⽹说法)。之前⽤的是diff算法。在之前React中，**更新过程是同步的**，这可能会导致性能问题。当React决定要加载或者更新组件树时，会做很多事，⽐如调⽤各个组件的⽣命周期函数，计算和⽐对Virtual DOM，最后更新DOM树，这整个过程是同步进⾏的，**也就是说只要⼀个加载或者更新过程开始，中途不会中断。因为JavaScript单线程的特点，如果组件树很⼤的时候，每个同步任务耗时太⻓，就会出现卡顿。**
   React Fiber的⽅法其实很简单——分⽚。把⼀个耗时⻓的任务分成很多⼩⽚，每⼀个⼩⽚的运⾏时间很短，**虽然总时间依然很⻓，但是在每个⼩⽚执⾏完之后，都给其他任务⼀个执⾏的机会，**这样唯⼀的线程就不会被独占，其他任务依然有运⾏的机会。

# React的特点和优势

1. 虚拟DOM
之前操作dom的⽅式是通过document.getElementById()的⽅式，这样的过程实际上是先去读取html的dom结构，将结构转换成变量，再进⾏操作
⽽reactjs定义了⼀套变量形式的dom模型，⼀切操作和换算直接在变量中，这样减少了操作真实dom，性能真实相当的⾼，和主流MVC框架有本质的区别，并不和dom打交道
2. 组件系统
react最核⼼的思想是将⻚⾯中任何⼀个区域或者元素都可以看做⼀个组件 component
那么什么是组件呢？
组件指的就是同时包含了html、css、js、image元素的聚合体
使⽤react开发的核⼼就是将⻚⾯拆分成若⼲个组件，并且react⼀个组件中同时耦合了css、js、
image，这种模式整个颠覆了过去的传统的⽅式
3. 单向数据流
其实reactjs的核⼼内容就是数据绑定，所谓数据绑定指的是只要将⼀些服务端的数据和前端⻚⾯绑定
好，开发者只关注实现业务就⾏了
4. JSX 语法
在vue中，我们使⽤render函数来构建组件的dom结构性能较⾼，因为省去了查找和编译模板的过程，
但是在render中利⽤createElement创建结构的时候代码可读性较低，较为复杂，此时可以利⽤jsx语法
来在render中创建dom，解决这个问题，但是前提是需要使⽤⼯具来编译jsx

# react项目搭建

## 脚⼿架create-react-app

全局安装create-react-app

```bash
$ npm install -g create-react-app
```

创建⼀个项⽬

```bash
$ create-react-app your-app 注意命名⽅式
cd  your-app
npm i
```

## 插件

### 底层编译类

#### react 

 react 这个包，是专门用来创建React组件、组件生命周期等这些东西的；
 react-dom 里面主要封装了和 DOM 操作相关的包，要把组件渲染到页面上

```js
import React from 'react'
import ReactDOM from 'react-dom'
```

要使用 JSX 语法，必须先运行 `cnpm i babel-preset-react -D`，然后再 `.babelrc` 中添加 语法配置；

### 框架功能类

- pubsub利用JavaScript进行发布/订阅的库
- React 中保存页面状态/在react中实现vue的**keep-alive**/**React Activation**

### 样式动画类

#### CSS-in-JS

CSS-in-JS就是**将应用的CSS样式写在JavaScript文件里面**，而不是独立为一些`.css`，`.scss`或者`less`之类的文件，这样你就可以在CSS中使用一些属于JS的诸如模块声明，变量定义，函数调用和条件判断等语言特性来提供灵活的可扩展的样式定义。

```sh
// 用 npm 安装
npm install @material-ui/styles
```

```jsx
import React from 'react';
import { makeStyles } from '@material-ui/core/styles';
import Button from '@material-ui/core/Button';

const useStyles = makeStyles({
  root: {
    background: 'linear-gradient(45deg, #FE6B8B 30%, #FF8E53 90%)',
    border: 0,
    borderRadius: 3,
    boxShadow: '0 3px 5px 2px rgba(255, 105, 135, .3)',
    color: 'white',
    height: 48,
    padding: '0 30px',
  },
});

export default function Hook() {
  const classes = useStyles();
  return <Button className={classes.root}>Hook</Button>;
}
```

```jsx
import React from 'react';
import { styled } from '@material-ui/core/styles';
import Button from '@material-ui/core/Button';

const MyButton = styled(Button)({
  background: 'linear-gradient(45deg, #FE6B8B 30%, #FF8E53 90%)',
  border: 0,
  borderRadius: 3,
  boxShadow: '0 3px 5px 2px rgba(255, 105, 135, .3)',
  color: 'white',
  height: 48,
  padding: '0 30px',
});

export default function StyledComponents() {
  return <MyButton>Styled Components</MyButton>;
}
```

```jsx
const useStyles = makeStyles({
  // style rule
  foo: props => ({
    backgroundColor: props.backgroundColor,
  }),
  bar: {
    // CSS property
    color: props => props.color,
  },
});

function MyComponent() {
  // 为了示例，我们模拟了这个属性
  const props = { backgroundColor: 'black', color: 'white' };
  // 将 props 作为 useStyles() 的第一个属性传入
  const classes = useStyles(props);

  return <div className={`${classes.foo} ${classes.bar}`} />
}
```

#### 样式动画库

https://zhuanlan.zhihu.com/p/361065034

- https://react-spring.io/basics
- https://reactcommunity.org/react-transition-group/
- https://motion.ant.design/components/tween-one-cn
- http://textillate.js.org/
- http://react-animations.herokuapp.com/
- https://www.framer.com/docs/

### 工具类

#### 拖拽react-sortable-hoc

高阶函数（Higher Order Function）=> 参数或返回值为函数组件

`react-sortable-hoc` 提供了两个特别重要的 API ，分别是`SortableContainer 和 SortableElement` 看英文的意思也知道，`SortableElement`用来装饰每个要拖拽的组件，相对于`SortableContainer` 要装饰的组件，`SortableElement` 装饰的组件是子组件。

```jsx
const SortableItem = SortableElement((props: any) => <div>{props.children}</div>);
const SortableList1 = SortableContainer(({ items }: any) => {
    return (
      <div>
        {items.map((item: any, index: number) => (
          <SortableItem key={`item-${item}-${index}`} index={index} value={item} >
            <RuleOne
            ></RuleOne>
          </SortableItem>
        ))}
      </div>
    );
  });
  
<SortableList1 items={submitData['rule1'].ruleGroup} useDragHandle disableAutoscroll
                onSortEnd={({ oldIndex, newIndex }: any) => {
                  if (oldIndex !== newIndex) {
                    saveRules(submitData, () => {
                      submitData['rule1'].ruleGroup = 				 arrayMoveImmutable(submitData['rule1'].ruleGroup, oldIndex, newIndex)
                    })
                  }
                }}
/>
```

`array-move` 就一个 API，它的主要作用就是**用来交换数组中元素的位置**。我们用 node 进行调试 

```jsx
// 引包
const arrayMove = require('array-move');
// 创建一个数组
const input = ['a', 'b', 'c'];
 
// 以下三个排序实例
console.log(arrayMove(input, 1, 2));
//=> ['a', 'c', 'b']
 
console.log(arrayMove(input, -1, 0));
//=> ['c', 'a', 'b']
 
console.log(arrayMove(input, -2, -3));
//=> ['b', 'a', 'c']
```



## main.js

全局定义将组件App放置在`id='App'`的容器里

```react
import React from 'react'
import ReactDom from 'react-dom'
import App from './app.jsx'
// ReactDOM.render('要渲染的虚拟DOM元素', '要渲染到页面上的哪个位置中')
// 注意： ReactDOM.render() 方法的第二个参数，和vue不一样，不接受 "#app" 这样的字符串，而是需要传递一个 原生的 DOM 对象
ReactDom.render(
    <App/>,
    document.getElementById('App')
)
```

## APP.jsx   

```react
// 在 react 中，如要要创建 DOM 元素了，只能使用 React 提供的 JS API 来创建，不能【直接】像 Vue 中那样，手写 HTML 元素
// React.createElement() 方法，用于创建 虚拟DOM 对象，它接收 3个及以上的参数
// 参数1： 是个字符串类型的参数，表示要创建的元素类型
// 参数2： 是一个属性对象，表示 创建的这个元素上，有哪些属性
// 参数3： 从第三个参数的位置开始，后面可以放好多的虚拟DOM对象，这写参数，表示当前元素的子节点
 var myDiv = React.createElement('div', { title: 'this is a div', id: 'mydiv' }, '这是一个div', myH1)
//<div title="this is a div" id="mydiv">这是一个div</div>
 
// 由于，React官方，发现，如果直接让用户手写 JS 代码创建元素，用户会疯掉的，然后，用户就开始寻找新的前端框架了，于是，React 官方，就提出了一套 JSX 语法规范，能够让我们在 JS 文件中，书写类似于 HTML 那样的代码，快速定义虚拟DOM结构；
// 问题： JSX（符合 XML 规范的 JS 语法）的原理是什么？
// JSX内部在运行的时候，也是先把 类似于HTML 这样的标签代码，转换为了 React.createElement 的形式；（JSX是一个对程序员友好的语法糖）
//在JSX创建DOM的时候，所有的节点，必须有唯一的根元素进行包裹；
```

```react
 // 在React中，构造函数，就是一个最基本的组件
// 如果想要把组件放到页面中，可以把 构造函数的名称，当作 组件的名称，以 HTML标签形式引入页面中即可
// 注意：React在解析所有的标签的时候，是以标签的首字母来区分的，如果标签的首字母是小写，那么就按照 普通的 HTML 标签来解析，如果 首字母是大写，则按照 组件的形式去解析渲染
// 结论：组件的首字母必须是大写
export default function Hello(props) {
  // 在组件中，如果想要使用外部传递过来的数据，必须，显示的在 构造函数参数列表中，定义 props 属性来接收；
  // 通过 props 得到的任何数据都是只读的，不能从新赋值
  return (
  <div>
    <h1>这是在Hello组件中定义的元素 --- {props.name}</h1>
  </div>)
}
```



# 组件的生命周期

https://zh-hans.reactjs.org/docs/react-component.html#static-getderivedstatefromprops

 + ##### 组件生命周期分为三部分：
   
	+ **组件初始化阶段**：组件实例创建阶段的生命周期函数，在组件的一辈子中，**只执行一次**；
	  
	  - constructor(props)
	    
	    仅用于以下两种情况：
	    
	    - 通过给 `this.state` 赋值对象来初始化[内部 state](https://zh-hans.reactjs.org/docs/state-and-lifecycle.html)。
	    - 为[事件处理函数](https://zh-hans.reactjs.org/docs/handling-events.html)绑定实例
	    
	    如果不初始化 state 或不进行方法绑定，则不需要为 React 组件实现构造函数。 **(所以只适用class)**
	    
	    React组件的构造函数在挂载之前被调⽤。在实现 React.Component 构造函数时，需要先在添加其他内容前，调⽤ super(props) ，⽤来将⽗组件传来的 props 绑定到这个类中，使⽤ this.props 将会得到。
	    
	  - componentWillMount（**17已经过时）: 组件将要被挂载，此时还没有开始渲染虚拟DOM。无法获取到页面上的 任何元素，因为虚拟DOM 和 页面 都还没有开始渲染呢。**通常在这⾥进⾏ajax请求**
	  
	  - render： `render()` 方法是 class 组件中唯一必须实现的方法。 第一次开始渲染真正的虚拟DOM，当render执行完，内存中就有了完整的虚拟DOM了，但是，页面上尚未真正显示DOM元素
	  
	  - componentDidMount:  `componentDidMount()` 会在组件挂载后（插入 DOM 树中）立即调用。依赖于 DOM 节点的初始化应该放在这里。**如需通过网络请求获取数据，此处是实例化请求的好地方**。 这个方法是比较适合添加订阅的地方。如果添加了订阅，请不要忘记在 `componentWillUnmount()` 里取消订阅  
	
	- **组件更新阶段**：更新根据组件的state和props的改变，有选择性的触发0次或多次；
	
	  - componentWillReceiveProps（**17已经过时**）: 
	    组件将要接收新属性props，此时，只要这个方法被触发，就证明父组件为当前子组件传递了新的属性值；
	    如果我们使用 this.props 来获取属性值，这个属性值，不是最新的，是上一次的旧属性值
	    // 如果想要获取最新的属性值，需要通过 componentWillReceiveProps 的参数列表来获取
	    
	    ```jsx
	    componentWillReceiveProps(nextProps){    
	        console.log(this.props.pmsg + ' ---- ' + nextProps.pmsg
	    );}
	    ```
	    
	  - shouldComponentUpdate: 组件是否需要被更新，此时，组件尚未被更新，但是，state 和 props 肯定是最新的。 首次渲染或使用 `forceUpdate()` 时不会调用该方法。 
	
	  - componentWillUpdate（**17已经过时**）: 组件将要被更新，此时，尚未开始更新，内存中的虚拟DOM树还是旧的，页面上的 DOM 元素 也是旧的
	
	  - render: 此时，又要重新根据最新的 state 和 props 重新渲染一棵内存中的 虚拟DOM树，当 render 调用完毕，内存中的旧DOM树，已经被新DOM树替换了！此时页面还是旧的
	
	  - componentDidUpdate: 此时，页面又被重新渲染了，state 和 虚拟DOM 和 页面已经完全保持同步
	
	- **组件销毁阶段**：也有一个显著的特点，一辈子只执行一次
	
	```
	componentWillUnmount: 组件将要被卸载，此时组件还可以正常使用；
	```
	
	![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0fb3cd2923f04e4c8dc58243522ff666~tplv-k3u1fbpfcp-zoom-1.image)

# 核心概念

## 组件

### 组件渲染机制

`Component`在 `state`改变,`props`改变,调用`this.setState({...})`，的时候都会进行渲染

- 强制React组件重新渲染

  **使用React的`forceUpdate`函数**

  这是一个最明显的方式。在React类组件中，你可以通过调用这个方法，强制重渲染一个组件：

  ```javascript
  this.forceUpdate();
  ```

  **在React hooks中强制更新组件**

  在React hooks中，`forceUpdate`函数是无法使用的。你可以使用如下方式强制更新组件，并且不更改组件的state：

  ```javascript
  const [state, updateState] = React.useState();
  const forceUpdate = React.useCallback(() => updateState({}), []);
  ```

- `shouldComponentUpdate`

  ```jsx
  shouldComponentUpdate(nextProps,nextState) {    
      if(this.state.name === nextState.name) {        
          return false    
      }else {        
          return true    
      }
  }
  ```

  

- 通过memo来判断指定的参数变化更新组件

- **componentWillReciveProps**

### 状态组件

#### 状态组件对比

使用 function 创建的组件，叫做【无状态组件】；使用 class 创建的组件，叫做【有状态组件】

- 使用 function 构造函数创建的组件，内部没有 state 私有数据，只有一个props来接收外界传递过来的数据
- 使用 class创建的组件，内部，除了有 this.props 这个只读属性之外，还有一个 专门用于 存放自己私有数据的this.state 属性，这个 state 是可读可写的！

有状态组件和无状态组件，最本质的区别：

- 有无 state 属性；

- class 创建的组件，有自己的生命周期函数，但是，function 创建的 组件，没有自己的生命周期函数；

问题来了：什么时候使用 有状态组件，什么时候使用无状态组件呢？？？

  1. 如果一个组件需要存放自己的私有数据，或者需要在组件的不同阶段执行不同的业务逻辑，此时，非常适合用 class 创建出来的有状态组件；
 2. 如果一个组件，只需要根据外界传递过来的 props，渲染固定的 页面结构就完事儿了，此时，非常适合使用 function 创建出来的 无状态组件；（使用无状态组件的小小好处： 由于剔除了组件的生命周期，所以，运行速度会相对快一丢丢）

#### class组件

**用class关键字创建出来的组件：“有状态组件”**

```js
// 使用 class 创建的类，通过 extends 关键字，继承了 React.Component 之后，这个类，就是一个组件的模板了
// 如果想要引用这个组件，可以把 类的名称， 以标签形式，导入到 JSX 中使用
export default class Hello2 extends React.Component {
  constructor(props) {
    // 注意： 如果使用 extends 实现了继承，那么在 constructor 的第一行，一定要显示调用一下 super()
    //  super() 表示父类的构造函数
    super(props)
    // 在 constructor 中，如果想要访问 props 属性，不能直接使用 this.props， 而是需要在 constructor 的构造器参数列表中，显示的定义 props 参数来接收，才能正常使用；
    // 注意： 这是固定写法，this.state 表示 当前组件实例的私有数据对象，就好比 vue 中，组件实例身上的 data(){ return {} } 函数  
    this.state = {
      msg: '这是 Hello2 组件的私有msg数据',
      info: '瓦塔西***'
    }
  }
  // 保存信息1： No `render` method found on the returned component instance: you may have forgotten to define `render`.
  // 通过分析以上报错，发现，提示我们说，在 class 实现的组件内部，必须定义一个 render 函数
  render() {
    // 报错信息2： Nothing was returned from render. This usually means a return statement is missing. Or, to render nothing, return null.
    // 通过分析以上报错，发现，在 render 函数中，还必须 return 一个东西，如果没有什么需要被return 的，则需要 return null

    // 虽然在 React dev tools 中，并没有显示说 class 组件中的 props 是只读的，但是，经过测试得知，其实 只要是 组件的 props，都是只读的；
    // this.props.address = '123'
    return <div>
      <h1>这是 使用 class 类创建的组件</h1>
      <h3>外界传递过来的数据是： {this.props.address} --- {this.props.info}</h3>
      <h5>{this.state.msg}</h5>

      //React中，提供的事件绑定机制，使用的 都是驼峰命名
      //     在为 React 事件绑定 处理函数的时候，需要通过 this.函数名， 来把 函数的引用交给 事件 
      <input type="button" value="修改 msg" id="btnChangeMsg" onClick={this.changeMsg} />
      <br />
    </div>
  }

  changeMsg = () => {
    // 注意： 这里不是传统网页，所以 React 已经帮我们规定死了，在 方法中，默认this 指向 undefined，并不是指向方法的调用者

    // 直接使用 this.state.msg = '123' 为 state 上的数据重新赋值，可以修改 state 中的数据值，但是，页面不会被更新；
    // 所以这种方式，React 不推荐，以后尽量少用；
    // 如果要为 this.state 上的数据重新赋值，那么，React 推荐使用 this.setState({配置对象}) 来重新为 state 赋值
    // 注意： this.setState 方法，只会重新覆盖那些 显示定义的属性值，如果没有提供最全的属性，则没有提供的属性值，不会被覆盖；
    /* this.setState({
      msg: '123'
    }) */

    // this.setState 方法，也支持传递一个 function，如果传递的是 function，则在 function 内部，必须return 一个 对象；
    // 在 function 的参数中，支持传递两个参数，其中，第一个参数是 prevState，表示为修改之前的 老的 state 数据
    // 第二个参数，是 外界传递给当前组件的 props 数据
    this.setState(function (prevState, props) {
      return {
        msg: '123'
      }
    }, function () {
      // 由于 this.setState 是异步执行的，所以，如果想要立即拿到最新的修改结果，最保险的方式， 在回调函数中去操作最新的数据
      console.log(this.state.msg)
    })
  }
}
```

#### 函数组件

函数/无状态组件是一个纯函数，它可接受接受参数，并返回react元素。这些都是没有任何副作用的纯函数。这些组件没有状态或生命周期方法

```js
// 组件的首字母必须是大写
function Hello(props) {
  // 在组件中，如果想要使用外部传递过来的数据，必须，显示的在 构造函数参数列表中，定义 props 属性来接收；
  // 通过 props 得到的任何数据都是只读的，不能从新赋值
  return <div>
    <h1>这是在Hello组件中定义的元素 --- {props.name}</h1>
  </div>
}
```

### 内置组件

#### PureComponent

https://blog.csdn.net/deng1456694385/article/details/88746797

```jsx
class demo extent Component {
    state = {
        name: ''
    }
    componentDidMount() {
        this.setState({name: ''})
    }
    render() {
        console.log('render')
        return <div>haha</div>
    }
}
```

上面的组件会在`this.setState`调用后就会重新传染一次,但是我们可以看出`name`状态并没有没被我们用到,也没有改变,这种渲染就是无效渲染,所以为了优化我们通常会使用钩子函数`shouldComponentUpdate`来做一些逻辑判断,来确定是否要重新`render`一次

```jsx
class demo extent Component {    
    state = {        
        name: ''    
    }
    componentDidMount() {	
        this.setState({name: ''})
    }
    shouldComponentUpdate(nextProps,nextState) {    
        if(this.state.name === nextState.name) {        
            return false    
        }else {        
            return true    
        }
    }
	render <div>haha</div>
}
```

这样就可以避免无效渲染,优化性能,但是如果这种判断逻辑多到一定程度,光判断逻辑就很复杂,而且每次都要判断也会影响性能,所以才有了 `PureComponent`

**`PureComponent`的区别在于相当于自己写了一个`shouldComponentUpdate`钩子函数处理, 对`props`和`state`进行浅比较,所谓浅比较就是之比较内部第一层的各个属性的值是否相同,像对象和数组这种数据类型,如果只改变内部的元素,就不会造成渲染**

**Component vs PureComponent 总结**
PureComponent相较于Component区别就是,对props和state默认进行判断来确定是否渲染,从而减少无效渲染次数. 大部分情况下直接用PureComponent比较好可以提高性能,但是如果遇到需要频繁修改值重新渲染的组件,用Component比较好,因为PureComponent频繁的判断也会影响性能.

#### memo

针对函数组件的,减少组件的不必要更新。 `React.memo` 仅检查 props 变更。如果函数组件被 `React.memo` 包裹，且其实现中拥有 [`useState`](https://zh-hans.reactjs.org/docs/hooks-state.html)，[`useReducer`](https://zh-hans.reactjs.org/docs/hooks-reference.html#usereducer) 或 [`useContext`](https://zh-hans.reactjs.org/docs/hooks-reference.html#usecontext) 的 Hook，当 state 或 context 发生变化时，它仍会重新渲染。 

```js
const TextCell = memo(function(props:any) {
  console.log('我重新渲染了')
  return (
    <p onClick={props.click}>ffff</p>
  )
})

//父组件
const fatherComponent = () => {
const [number,setNumber] = useState(0);
 return(
    <div>
      模块{number}
      <TextCell/>
      <Button onClick={()=>setNumber(number => number + 1)}>加加加</Button>
    </div>
  )
}
```

在这里如果没有用到memo 每次父组件重新setNumber,**子组件都会重新渲染一次**,加上了后**只会在初始化的时候渲染(useMemo会在页面初始化的时候执行一次,并把执行的结果缓存一份)**,减少了子组件渲染的次数

默认情况下其只会对复杂对象做**浅层对比**，如果你想要控制对比过程，那么请将自定义的比较函数通过第二个参数传入来实现。 

```jsx
function MyComponent(props) {
  /* 使用 props 渲染 */
}
function areEqual(prevProps, nextProps) {
  /*
  如果把 nextProps 传入 render 方法的返回结果与
  将 prevProps 传入 render 方法的返回结果一致则返回 true，
  否则返回 false
  */
}
export default React.memo(MyComponent, areEqual);
```

#### Fragment

无论是函数组件还是类组件，return 的 React 元素的语法必须是由一个标签包裹起来的所有虚拟 DOM 内容

一种是使用一个 div 标签将其包裹起来，另外一种方式就是使用 React 提供的 `<React.Fragment>` 将其包裹起来。但是我们不期望，增加额外的`dom`节点，所以`react`提供`Fragment`碎片概念，能够让一个组件返回多个元素。

```
render() {
  return (
    <React.Fragment>
      Some text.
      <h2>A heading</h2>
    </React.Fragment>
  );
}
```

### 组件通信

#### props

适用于父子组件通信

##### 父组件->子组件

父组件将需要传递的参数通过`key={xxx}`方式传递至子组件，子组件通过`this.props.key`获取参数.

##### 子组件->父组件

利用 props callback 通信，父组件传递一个 callback 到子组件，当事件触发时将参数放置到 callback 带回给父组件.

```react
// 父组件
import React from 'react'
import Son from './son'
class Father extends React.Component {
  constructor(props) {
    super(props)
  }
  state = {
    info: '',
  }
  callback = (value) => {
    // 此处的value便是子组件带回
    this.setState({
      info: value,
    })
  }
  render() {
    return (
      <div>
        <p>{this.state.info}</p>
        <Son callback={this.callback} />
      </div>
    )
  }
}
export default Father

// 子组件
import React from 'react'
interface IProps {
  callback: (string) => void
}
class Son extends React.Component<IProps> {
  constructor(props) {
    super(props)
    this.handleChange = this.handleChange.bind(this)
  }
  handleChange = (e) => {
    // 在此处将参数带回
    this.props.callback(e.target.value)
  }
  render() {
    return (
      <div>
        <input type='text' onChange={this.handleChange} />
      </div>
    )
  }
}
export default Son
```

#### Context

https://zh-hans.reactjs.org/docs/context.html

数据是通过 props 属性自上而下（由父及子）进行传递的 ，需要显式地通过组件树的逐层传递 props。Context 设计目的是为了共享那些对于一个组件树而言是“全局”的数据 

```jsx
// context.js
import React from 'react'
//创建一个 Context 对象，并暴露Consumer和Provide
const { Consumer, Provider } = React.createContext(null) 
export { Consumer, Provider }
//Father
import { Provider } from './context'
import React from 'react'
import Son from './son'
<Provider value={this.state.info}>
    <div>
        <p>{this.state.info}</p>
        <Son />
    </div>
</Provider>
    
//Son
import React from 'react'
import GrandSon from './grandson'
import { Consumer } from './context'
<Consumer>
{(info) => (
// 通过Consumer直接获取父组件的值
    <div>
    <p>父组件的值:{info}</p>
    <GrandSon />
    </div>
    )}
</Consumer>
```

当 Provider 的 `value` 值发生变化时，它内部的所有消费组件都会重新渲染。从 Provider 到其内部 consumer 组件（包括 [.contextType](https://zh-hans.reactjs.org/docs/context.html#classcontexttype) 和 [useContext](https://zh-hans.reactjs.org/docs/hooks-reference.html#usecontext)）的传播不受制于  `shouldComponentUpdate` 函数，因此当 consumer 组件在其祖先组件跳过更新的情况下也能更新。 

## DOM

react的核心思想是虚拟DOM。react包含了生成虚拟DOM的函数react.createElement，及Component类。而react-dom包的核心功能就是把这些虚拟DOM渲染到文档中变成实际DOM。

### 通过原生JS获取Dom去操作

```jsx
import {Component} from "react";
class App extends Component {
    //定义获取Dom的函数
    handleGetDom(){
        let title = document.querySelector('#title');
        console.log(title);
        title.style.background = 'skyblue'
    }
    render() {
        return (
            <>
                <h1 id="title">测试节点</h1>
                <button onClick={this.handleGetDom}>点击操作Dom</button>
            </>
        )
    }
}
export default App;
```

### **Refs**

#### 回调 Refs

> 支持在函数组件和类组件内部使用

`React` 支持 `回调 refs` 的方式设置 Refs。这种方式可以帮助我们更精细的控制何时 Refs 被设置和解除。

使用 `回调 refs` 需要将回调函数传递给 `React元素` 的 `ref` 属性。这个函数接受 React 组件实例 或 HTML DOM 元素作为参数，将其挂载到实例属性上，如下所示：

```jsx
import React from 'react';
export default class MyInput extends React.Component {
    constructor(props) {
        super(props);
        this.inputRef = null;
        this.setTextInputRef = (ele) => {
            this.inputRef = ele;
        }
        this.setSonRef = (ele) => {
            this.sonInfo = ele;
        }
    }

    componentDidMount() {
        this.inputRef && this.inputRef.focus();//获取input DOM
        this.sonInfo.getSonInfo()//父组件执行子组件函数
    }
    render() {
        return (
            <input type="text" ref={this.setTextInputRef}/>
            <MyInput ref={this.setSonRef}/>
        )
    }
}
//
function MyInput(props) {
  state = {
      info: {}
  }
  getSonInfo(){
      console.log(this,state.info)
  }
  return <input type="text" ref={props.inputRef} />;
}
```

React 会在组件挂载时，调用 `ref` 回调函数并传入 DOM元素(或React实例)，当卸载时调用它并传入 `null`。在 `componentDidMount` 或 `componentDidUpdate` 触发前，React 会保证 Refs 一定是最新的。

> 可以在组件间传递回调形式的 refs.

```js
import React from "react";
export default function Form() {
  let ref = null;
  React.useEffect(() => {
    //ref 即是 MyInput 中的 input 节点
    ref.focus();
  }, [ref]);
  return (
    <>
      <MyInput inputRef={(ele) => (ref = ele)} />
      {/** other code */}
    </>
  );
}
function MyInput(props) {
  return <input type="text" ref={props.inputRef} />;
}
```

#### createRef

**支持在函数组件和类组件内部使用**

##### **使用场景**

1.管理焦点，文本选择或媒体播放。
2.触发强制动画。
3.集成第三方 DOM 库。

##### Refs API使用

```jsx
import React from 'react';
export default class MyInput extends React.Component {
    constructor(props) {
        super(props);
        //分配给实例属性
        this.inputRef = React.createRef(null);
    }
    componentDidMount() {
        //通过 this.inputRef.current 获取对该节点的引用
        this.inputRef && this.inputRef.current.focus();
    }
    render() {
        //把 <input> ref 关联到构造函数中创建的 `inputRef` 上
        return (
            <input type="text" ref={this.inputRef}/>
        )
    }
}
```

**ref 的值根据节点的类型而有所不同：**

一、当 ref 属性用于 HTML 元素时，构造函数中使用 React.createRef() 创建的 ref 接收**底层 DOM 元素**作为其 current 属性。

二、当 ref 属性用于自定义 class 组件时，ref 对象接收组件的**挂载实例**作为其 current 属性。

三、**你不能将ref属性用于函数式组件上，因为他们并没有实例（instance）！**

但是，你可以在函数式组件中使用ref属性，就像你引用DOM元素和类组件一样。

#### useRef

**只能在函数组件中使用**

> 区别：https://zhuanlan.zhihu.com/p/105276393
>
> useRef 用法类似于React.createRef()，区别：
>
> **createRef 每次渲染都会返回一个新的引用，而 useRef 每次都会返回相同的引用。**`useRef` 返回的 ref 对象在组件的**整个生命周期内保持不变**。useRef 不仅仅是用来管理 DOM ref 的，它还相当于 this , 可以存放任何变量。useRef 可以很好的解决闭包带来的不方便性

```jsx
import React, { useRef } from "react";
export default function UseRefHookExample() {
  let inputRef = useRef(null);
  const handleClick = () => {
    inputRef.current.focus();
  };
  return (
    <div>
      使用 useRef() hook:
      <br />
      <input type="text" ref={inputRef} />
      <button onClick={handleClick}>
        Click
      </button>
    </div>
  );
}
```

##### useImperativeHandle

```javascript
useImperativeHandle(ref, createHandle, [deps])
```

- 通过useImperativeHandle可以**只暴露特定的操作**
  - 通过useImperativeHandle的Hook, 将父组件传入的ref和useImperativeHandle第二个参数返回的对象绑定到了一起
  - 所以在父组件中, 调用inputRef.current时, 实际上是返**回的对象**
- useImperativeHandle使用简单总结:
  - 作用: 减少暴露给父组件获取的DOM元素属性, 只暴露给父组件需要用到的DOM方法
  - 参数1: 父组件传递的ref属性
  - 参数2: 返回一个对象, 以供给父组件中通过ref.current调用该对象中的方法

```jsx
import React, { useRef, forwardRef, useImperativeHandle } from 'react'

const JMInput = forwardRef((props, ref) => {
  const inputRef = useRef()
  // 作用: 减少父组件获取的DOM元素属性,只暴露给父组件需要用到的DOM方法
  // 参数1: 父组件传递的ref属性
  // 参数2: 返回一个对象,父组件通过ref.current调用对象中方法
  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current.focus()
    },
  }))
  return <input type="text" ref={inputRef} />
})

export default function ImperativeHandleDemo() {
  // useImperativeHandle 主要作用:用于减少父组件中通过forward+useRef获取子组件DOM元素暴露的属性过多
  // 为什么使用: 因为使用forward+useRef获取子函数式组件DOM时,获取到的dom属性暴露的太多了
  // 解决: 使用uesImperativeHandle解决,在子函数式组件中定义父组件需要进行DOM操作,减少获取DOM暴露的属性过多
  const inputRef = useRef()

  return (
    <div>
      <button onClick={() => inputRef.current.focus()}>聚焦</button>
      <JMInput ref={inputRef} />
    </div>
  )
}
```



#### forwardRef 转发/传递

 `React.forwardRef` 会创建一个React组件，这个组件能够将其接受的 [ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html) 属性转发到其组件树下的另一个组件中。 

以下两种场景中特别有用：

- [转发 refs 到 DOM 组件](https://zh-hans.reactjs.org/docs/forwarding-refs.html#forwarding-refs-to-dom-components)

  ```js
  import React from 'react';
  
  const MyInput = React.forwardRef((props, ref) => {
      return (
          <input type="text" ref={ref} {...props} />
  	)
  });
  function Form() {
      const inputRef = React.useRef(null);//class组件用createRef
      React.useEffect(() => {
          console.log(inputRef.current);//input节点
      })
      return (
          <MyInput ref={inputRef} />
      )
  }
  ```

  1. 调用 `React.useRef` 创建了一个 `React ref` 并将其赋值给 `ref` 变量。
  2. 指定 `ref` 为JSX属性，并向下传递 `<MyInput ref={inputRef}>`
  3. React 传递 `ref` 给 `forwardRef` 内函数 `(props, ref) => ...` 作为其第二个参数。
  4. 向下转发该 `ref` 参数到 `<button ref={ref}>`，将其指定为JSX属性
  5. 当 `ref` 挂载完成，`inputRef.current` 指向 `input` DOM节点

- [在高阶组件中转发 refs](https://zh-hans.reactjs.org/docs/forwarding-refs.html#forwarding-refs-in-higher-order-components)

### findDOMNode()

当组件加载到页面上之后（mounted），你都可以通过 `react-dom` 提供的 `findDOMNode()` 方法拿到组件对应的 DOM 元素。

```javascript
import { findDOMNode } from 'react-dom';

// Inside Component class
componentDidMound() {
  const el = findDOMNode(this);
}
```

`findDOMNode()` 不能用在无状态组件上。

## jsx

### 条件渲染

```ts
function Greeting(props) {
  const isLoggedIn = props.isLoggedIn;
  if (isLoggedIn) {
    return <UserGreeting />;
  }
  return <GuestGreeting />;
}
//或者
function Greeting(props) {
  const isLoggedIn = props.isLoggedIn;
  return (
  	{isLoggedIn?<UserGreeting />:<GuestGreeting />}
  )
}
ReactDOM.render(
  // Try changing to isLoggedIn={true}:
  <Greeting isLoggedIn={false} />,
  document.getElementById('root')
);
```

### 列表 & Key

  ```js
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li key={number.toString()}>
      {number}
    </li>
  );
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
  ```

 **在 JSX 中嵌入 map()**

  ```js
function NumberList(props) {
  const numbers = props.numbers;
  return (
    <ul>
      {numbers.map((number) =>
        <ListItem key={number.toString()}
                  value={number} />
      )}
    </ul>
  );
}
  ```

key 帮助 React 识别哪些元素改变了，比如被添加或删除。因此你应当给数组中的每一个元素赋予一个确定的标识。一个元素的 key 最好是这个元素在列表中拥有的一个独一无二的字符串。通常，我们使用数据中的 id 来作为元素的 key;当元素没有确定 id 的时候，万不得已你可以使用元素索引 index 作为 key 

**key 只是在兄弟节点之间必须唯一**

 数组元素中使用的 key 在其兄弟节点之间应该是独一无二的。然而，它们不需要是全局唯一的。当我们生成两个不同的数组时，我们可以使用相同的 key 值：

  ```js
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li key={number.toString()}>
    {number}
  </li>
);
  ```

### 添加样式的方式

**第一种：行内样式**

想给虚拟dom添加行内样式，需要使用表达式传入样式对象的方式来实现：

```javascript
// 注意这里的两个括号，第一个表示我们在要JSX里插入JS了，第二个是对象的括号
 <p style={{color:'red', fontSize:'14px'}}>Hello world</p>
```

**动态添加样式**

```
<div style={{display: (index===this.state.currentIndex) ? "block" : "none"}}>此标签是否隐藏</div>
```

```
<div className={index===this.state.currentIndex?"active":null}>此标签是否选中</div>
```

**第二种：内嵌样式**

```
<style>{`.operafor4{margin-top:42px !important}`}</style>
```

**第二种：css modules**

https://www.ruanyifeng.com/blog/2016/06/css_modules.html

CSS的规则都是全局的，任何一个组件的样式规则，都对整个页面有效。例如：父组件和子组件使用相同的class，父组件的class会覆盖子组件的样式

产生局部作用域的唯一方法，就是使用一个独一无二的`class`的名字，不会与其他选择器重名。这就是 CSS Modules 的做法。

css Modules 添加多个className

```css
<div className={`${styles.sAll} ${styles.s1}`}>aaaaaa</div>
```

CSS Modules 允许使用`:global(.className)`的语法，声明一个全局规则。

```css
.title {
  color: red;
}

:global(.title) {
  color: green;
}
```

**第三种：样式组件（styled-components）**

styled-components是针对React写的一套css-in-js框架，简单来讲就是在js中写css。
styled-components是一个第三方包，要安装。**Material框架**中的样式也是如此

```javascript
const Container = styled.div`
    width: 100px;
    height: 100px;
    background: pink;
    color: white;
`
```

## 表单和受控组件

在 HTML 中，表单元素（如`<input>`、 `<textarea>` 和 `<select>`）之类的表单元素通常自己维护 state，并根据用户输入进行更新。而在 React 中，可变状态（mutable state）通常保存在组件的 state 属性中，并且只能通过使用 [`setState()`](https://react.docschina.org/docs/react-component.html#setstate)来更新。

我们可以把两者结合起来，使 React 的 state 成为“唯一数据源”。渲染表单的 React 组件还控制着用户输入过程中表单发生的操作。被 React 以这种方式控制取值的表单输入元素就叫做“**受控组件**”。

```tsx
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: ''};
    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {    this.setState({value: event.target.value});  }
  handleSubmit(event) {
    alert('提交的名字: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          名字:
          <input type="text" value={this.state.value} onChange={this.handleChange} />        </label>
        <input type="submit" value="提交" />
      </form>
    );
  }
}
```

由于在表单元素上设置了 `value` 属性，因此显示的值将始终为 `this.state.value`，这使得 React 的 state 成为唯一数据源。由于 `handlechange` 在每次按键时都会执行并更新 React 的 state，因此显示的值将随着用户输入而更新。

**对于受控组件来说，输入的值始终由 React 的 state 驱动**

## props

```jsx
function Welcome(props) {  return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;ReactDOM.render(
  element,
  document.getElementById('root')
);
```

### defaultProps

无论是函数组件还是 class 组件，都拥有 `defaultProps` 属性。可以通过配置特定的 `defaultProps` 属性来定义 `props` 的默认值

```jsx
class Greeting extends React.Component {
  render() {
    return (
      <h1>Hello, {this.props.name}</h1>
    );
  }
}

// 指定 props 的默认值：
Greeting.defaultProps = {
  name: 'Stranger'
};

// 渲染出 "Hello, Stranger"：
ReactDOM.render(
  <Greeting />,
  document.getElementById('example')
);
```

### PropTypes类型检查

PropTypes 进行类型检查,可用于确保组件接收到的props数据类型是有效的

导入包

```jsx
import PropTypes from 'prop-types';
```

编写组件

```jsx
class Greeting extends React.Component {
    render() {
        return <h1>Hello, {this.props.name}</h1>
    }
}
```

新增类型检查

```cpp
Greeting.propTypes = {
// 你可以将属性声明为 JS 原生类型，默认情况下
  // 这些属性都是可选的。
  optionalArray: PropTypes.array,
  optionalBool: PropTypes.bool,
  optionalFunc: PropTypes.func,
  optionalNumber: PropTypes.number,
  optionalObject: PropTypes.object,
  optionalString: PropTypes.string,
  optionalSymbol: PropTypes.symbol,

  // 任何可被渲染的元素（包括数字、字符串、元素或数组）
  // (或 Fragment) 也包含这些类型。
  optionalNode: PropTypes.node,

  // 一个 React 元素。
  optionalElement: PropTypes.element,

  // 一个 React 元素类型（即，MyComponent）。
  optionalElementType: PropTypes.elementType,

  // 你也可以声明 prop 为类的实例，这里使用
  // JS 的 instanceof 操作符。
  optionalMessage: PropTypes.instanceOf(Message),

  // 你可以让你的 prop 只能是特定的值，指定它为
  // 枚举类型。
  optionalEnum: PropTypes.oneOf(['News', 'Photos']),

  // 一个对象可以是几种类型中的任意一个类型
  optionalUnion: PropTypes.oneOfType([
    PropTypes.string,
    PropTypes.number,
    PropTypes.instanceOf(Message)
  ]),

  // 可以指定一个数组由某一类型的元素组成
  optionalArrayOf: PropTypes.arrayOf(PropTypes.number),

  // 可以指定一个对象由某一类型的值组成
  optionalObjectOf: PropTypes.objectOf(PropTypes.number),

  // 可以指定一个对象由特定的类型值组成
  optionalObjectWithShape: PropTypes.shape({
    color: PropTypes.string,
    fontSize: PropTypes.number
  }),
  
  // An object with warnings on extra properties
  optionalObjectWithStrictShape: PropTypes.exact({
    name: PropTypes.string,
    quantity: PropTypes.number
  }),   

  // 你可以在任何 PropTypes 属性后面加上 `isRequired` ，确保
  // 这个 prop 没有被提供时，会打印警告信息。
  requiredFunc: PropTypes.func.isRequired,

  // 任意类型的数据
  requiredAny: PropTypes.any.isRequired,

  // 你可以指定一个自定义验证器。它在验证失败时应返回一个 Error 对象。
  // 请不要使用 `console.warn` 或抛出异常，因为这在 `onOfType` 中不会起作用。
  customProp: function(props, propName, componentName) {
    if (!/matchme/.test(props[propName])) {
      return new Error(
        'Invalid prop `' + propName + '` supplied to' +
        ' `' + componentName + '`. Validation failed.'
      );
    }
  },

  // 你也可以提供一个自定义的 `arrayOf` 或 `objectOf` 验证器。
  // 它应该在验证失败时返回一个 Error 对象。
  // 验证器将验证数组或对象中的每个值。验证器的前两个参数
  // 第一个是数组或对象本身
  // 第二个是他们当前的键。
  customArrayProp: PropTypes.arrayOf(function(propValue, key, componentName, location, propFullName) {
    if (!/matchme/.test(propValue[key])) {
      return new Error(
        'Invalid prop `' + propFullName + '` supplied to' +
        ' `' + componentName + '`. Validation failed.'
      );
    }
  })
}
```

### **this.props.children** 

将一个组件写在另一个组件的内容中，然后在外层组件中通过 this.props.children来接收内容中的组件

如果当前组件没有子节点，它就是 undefined ;
如果有一个子节点，数据类型是 Object；
如果有多个子节点，数据类型就是 Array。

## setState

https://juejin.cn/post/6850418109636050958

https://juejin.cn/post/6959885030063603743#heading-0

```
state = {
    number:1
};
componentDidMount(){
    this.setState({number:3})
    console.log(this.state.number)
}
```

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/7/11/1733ca3cdf9d950d~tplv-t2oaga2asx-watermark.awebp)

看完这个例子，也许很多小伙伴会下意识的以为setState是一个异步方法，但是其实**setState并没有异步的说法，之所以会有一种异步方法的表现形式，归根结底还是因为react框架本身的性能机制所导致的**。因为每次调用setState都会触发更新，异步操作是为了提高性能，将多个状态更新合并一起进行**批量更新**，减少re-render调用。 **将 `setState()` 视为*请求*而不是立即更新组件的命令。** 

```
for ( let i = 0; i < 100; i++ ) {
    this.setState( { num: this.state.num + 1 } );
}
```

如果setState是一个同步执行的机制，那么这个状态会被重新渲染100次，这对性能是一个相当大的消耗。

> React会将多个setState的调用合并为一个来执行，也就是说，当执行setState的时候，state中的数据并不会马上更新

**回调函数**

setState提供了一个回调函数供开发者使用，在回调函数中，我们可以实时的获取到更新之后的数据。还是以刚才的例子做示范：

```
state = {
    number:1
};
componentDidMount(){
    this.setState({number:3},()=>{
        console.log(this.state.number)
    })
}
复制代码
```

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/7/11/1733ca3cdfc5be38~tplv-t2oaga2asx-watermark.awebp)

**总结：**

- setState本身并不是异步（不会立即更新state的结果），只是因为react的性能优化机制体现为异步。在react的生命周期函数或者作用域下为异步，在原生的环境下为同步。

- React18之前，react 无法对 setTimeout 的代码前后加上事务逻辑（除非 react 重写 setTimeout）。

  所以当遇到 `setTimeout/setInterval/Promise.then(fn)/fetch 回调/xhr 网络回调`时，react 都是无法控制的**（可以使用手动批处理）**。

  - 在setTimeout，Promise.then等异步事件中。setState和useState是同步执行的（立即更新state的结果）

## 事件处理

- react 事件的命名采用小驼峰式（camelCase），而不是纯小写。

- 使用 JSX 语法时你需要传入一个函数作为事件处理函数，而不是一个字符串。

- 不能通过返回 `false` 的方式阻止默认行为。你必须显式的使用 `preventDefault`

  ```jsx
  class Toggle extends React.Component {
    constructor(props) {
      super(props);
      this.state = {isToggleOn: true};
  
      // 为了在回调中使用 `this`，这个绑定是必不可少的
      this.handleClick = this.handleClick.bind(this);
    }
  
    handleClick() {
      this.setState(state => ({
        isToggleOn: !state.isToggleOn
      }));
    }
  
    render() {
      return (
        <button onClick={this.handleClick}>
          {this.state.isToggleOn ? 'ON' : 'OFF'}
        </button>
      );
    }
  }
  
  ReactDOM.render(
    <Toggle />,
    document.getElementById('root')
  );
  ```

在 JavaScript 中，class 的方法默认不会[绑定](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_objects/Function/bind) `this`。如果你忘记绑定 `this.handleClick` 并把它传入了 `onClick`，当你调用这个函数的时候 `this` 的值为 `undefined`。

**绑定this：**

- 在constructor中用bind绑定

- 箭头函数

- 回调中使用箭头函数

  ```
  class LoggingButton extends React.Component {
    handleClick() {
      console.log('this is:', this);
    }
  
    render() {
      // 此语法确保 `handleClick` 内的 `this` 已被绑定。
      return (
        <button onClick={() => this.handleClick()}>
          Click me
        </button>
      );
    }
  }
  ```

## 高阶组件

高阶组件（HOC）是 React 中用于复用组件逻辑的一种高级技巧。HOC 自身不是 React API 的一部分，它是一种基于 React 的组合特性而形成的设计模式。具体而言，**高阶组件是参数为组件，返回值为新组件的函数。**

```jsx
class BlogPost extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.state = {
      blogPost: DataSource.getBlogPost(props.id)
    };
  }

  componentDidMount() {
    DataSource.addChangeListener(this.handleChange);
  }

  componentWillUnmount() {
    DataSource.removeChangeListener(this.handleChange);
  }

  handleChange() {
    this.setState({
      blogPost: DataSource.getBlogPost(this.props.id)
    });
  }

  render() {
    return <TextBlock text={this.state.blogPost} />;
  }
}
```

- 在挂载时，向 `DataSource` 添加一个更改侦听器。
- 在侦听器内部，当数据源发生变化时，调用 `setState`。
- 在卸载时，删除侦听器。

你可以想象，在一个大型应用程序中，这种订阅 `DataSource` 和调用 `setState` 的模式将一次又一次地发生。我们需要一个抽象，允许我们在一个地方定义这个逻辑，并在许多组件之间共享它。这正是高阶组件擅长的地方。

## Tip

- 列表渲染为什么需要key:

  为每个元素加上唯一的标识，这样在执行diff的时候会加快位置的确定

# Hooks函数

http://www.ruanyifeng.com/blog/2019/09/react-hooks.html

- 纯函数组件**没有状态**
- 纯函数组件**没有生命周期**
- 纯函数组件没有`this`

这就注定，我们所推崇的函数组件，只能做UI展示的功能，涉及到状态的管理与切换，我们不得不用类组件或者redux，但我们知道类组件的也是有缺点的，比如，遇到简单的页面，你的代码会显得很重，并且每创建一个类组件，都要去继承一个React实例，至于Redux,更不用多说，很久之前Redux的作者就说过，“能用React解决的问题就不用Redux”,等等一系列的话。关于**React类组件r**edux的作者又有话说

> - 大型组件很难拆分和重构，也很难测试。
> - 业务逻辑分散在组件的各个方法之中，导致重复逻辑或关联逻辑。
> - 组件类引入了复杂的编程模式，比如 render props 和高阶组件。

**Hooks 优势**

1. 能优化类组件的三大问题
2. 能在无需修改组件结构的情况下复用状态逻辑（自定义 Hooks ）
3. 能将组件中相互关联的部分拆分成更小的函数（比如设置订阅或请求数据）

**React Hooks 的意思是，组件尽量写成纯函数，如果需要外部功能和副作用，就用钩子把外部代码"钩"进来。**而React Hooks 就是我们所说的“钩子”。

## useState():状态钩子

用于为函数组件引入状态（state）。纯函数不能有状态，所以把状态放在钩子里面。

### 原理

```jsx
//useState模拟1.0
//因为每次调用myUseState时会重置state的值。经过改进，必须将state写在函数的外面。
let _state;
function myUseState(initialValue) {
  _state = _state===undefined? initialValue:_state;
  const setState = (newValue) => {
    _state = newValue; //更新state值，
    render(); //触发重新渲染
  };
  return [_state, setState];
}
/* 粗糙的渲染 */
const render = () => {
  ReactDOM.render(<App />, document.getElementById("root"));
};
// 使用myUseState
const App = () => {
  const [n, setN] = myUseState(0);
  return (
	  <div classNam="App">
		 <p>n:{n}</p>
		 <button onClick={()=>{setN(n+1)}}>n+1</button> 
	  </div>
	  );
};
ReactDOM.render(<App />, document.getElementById("root"));

```

```jsx
//一个组件用了两个useState怎么办？useState模拟2.0
let _state=[];
let index=0;
function myUseState(initialValue) {
  int currentIndex=index;	//引入中间变量currentIndex就是为了保存当前操作的下标index。
  _state[currentIndex] = _state[currentIndex]===undefined? initialValue:_state[currentIndex];
  const setState = (newValue) => {
    _state[currentIndex] = newValue; 
    render(); 
  };
  index+=1;// 每次更新完state值后，index值+1
  return [_state[currentIndex], setState];
}
const render = () => {
  index=0;	//重要的一步，必须在渲染前后将index值重置为0，不然index会一种增加1
  ReactDOM.render(<App />, document.getElementById("root"));
};
// 使用myUseState
const App = () => {
  const [n, setN] = myUseState(0);
  const [m, setM] = myUseState(0);
  return (
	  <div classNam="App">
		 <p>n:{n}</p>
		 <button onClick={()=>{setN(n+1)}}>n+1</button> 
		 <p>m:{m}</p>
		 <button onClick={()=>{setM(m+1)}}>n+1</button> 
	  </div>
	  );
};
ReactDOM.render(<App />, document.getElementById("root"));
```

- 在正常的react的事件流里（如onClick等）

  - setState和useState是**异步执行**的（不会立即更新state的结果，所以console数据没有更新）
    - setState的“异步”并不是说内部由异步代码实现，其实本身执行的过程和代码都是同步的，只是合成事件和钩子函数的调用顺序在更新之前，导致在合成事件和钩子函数中没法立马拿到更新后的值，形式了所谓的“异步”

  - 多次执行setState和useState，只会调用一次重新渲染render

  - 不同的是，setState会进行state的合并，而useState会进行state的覆盖

- 在setTimeout，Promise.then等异步事件中

  - setState和useState是**同步执行**的（立即更新state的结果，**react17之后还是会批处理**）

  - 多次执行setState和useState，每一次的执行setState和useState，都会调用一次render

### **批处理**

batch批量处理：在每次执行 useState 的时候，组件都要重新 render 一次，会造成无效渲染，浪费时间（因为最后一次渲染会覆盖掉前面所有的渲染效果）。 所以 react 会把一些可以一起更新的 useState/setState 放在一起，只渲染一次。

在React16版本及以前，React 会对所有React内部触发的事件监听函数中的更新（比如onClick函数）做批处理，如果是绕过react组件，如addEventListenr，或者异步调用如异步请求或者setTimeout等，不会进行批处理。在React17版本及之后，React会对所有的更新做批处理。

**unstable_batchedUpdates手动批处理**

```jsx
  function handleClick3() {
    // 手动批处理
    setTimeout(() => {
      unstable_batchedUpdates(() => {
        setCount1(count1 + 1);
        console.log(count1);
        setFlag((f) => !f);
      });
    }, 10);
    // React 只会在最后重新渲染一次（这是批处理！）
  }
```

### Tip

- react中useState更新了组件，但是页面上的组件没有刷新

  原因：useState更新的数据，是一个多层次的数据，[react](https://so.csdn.net/so/search?q=react)监听的时候，是浅层监听(默认开启 类 Object.is 的浅层比较，所以指向的地址不变)，所以不一定及时刷新页面

  解决办法:深拷贝，把需要更新的数据深拷贝一份，再使用useState 存储，就能实现每次都及时更新页面

## useContext():共享状态钩子

如果需要在层层组件之间共享状态，可以使用`useContext()`。

第一步就是使用 React Context API，在组件外部建立一个 Context。

> ```javascript
> const AppContext = React.createContext({});
> ```

组件封装代码如下。

> ```js
>  // 使用一个 Provider 来将当前的 theme 传递给以下的组件树。
> // 无论多深，任何组件都能读取这个值。
> <AppContext.Provider value={{
> username: 'superawesome'
> }}>
> <div className="App">
>  <Navbar/>
>  <Messages/>
> </div>
> </AppContext.Provider>
> ```

上面代码中，`AppContext.Provider`提供了一个 Context 对象，这个对象可以被子组件共享。

Navbar 组件的代码如下。

> ```javascript
> const Navbar = () => {
>   const { username } = useContext(AppContext);
>   return (
>     <div className="navbar">
>       <p>AwesomeSite</p>
>       <p>{username}</p>
>     </div>
>   );
> }
> ```

## useReducer()钩子

**useReducer适用于引用类型，而useState适合值类型**

```
const [state, dispatch] = useReducer(reducer, initialArg, init)
```

- useReducer 接收三个参数，**第一个参数为一个 reducer 函数，第二个参数是reducer的初始值，第三个参数为可选参数，值为一个函数，可以用来惰性提供初始状态。**

  reducer 接受两个参数一个是 state 另一个是 action ，用法原理和 redux 中的 reducer 一致

- useReducer 返回一个数组，数组中包含一个 state 和 dispath，state 是返回状态中的值，而 dispatch 是一个可以发布事件来更新 state 的函数。

### **原理**

**useReucer 也是 useState 的内部实现**

```jsx
let memoizedState
function useReducer(reducer, initialArg, init) {
    let initState = void 0
    if (typeof init === 'function') {
        initState = init(initialArg)
    } else {
        initState = initialArg
    }
    function dispatch(action) {
        memoizedState = reducer(memoizedState, action)
        // React的渲染
        // render()
    }
    memoizedState = memoizedState || initState
    return [memoizedState, dispatch]
}

function useState(initState) {
    return useReducer((oldState, newState) => {
        if (typeof newState === 'function') {
            return newState(oldState)
        }
        return newState
    }, initState)
}

```

### 实例

```js
const initialState = {count: 0};

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
    </>
  );
}

```

## useEffect()：副作用钩子

纯函数只能进行数据计算，那些不涉及计算的操作（比如ajax 请求、访问原生dom 元素、本地持久化缓存、绑定/解绑事件、添加订阅、设置定时器、记录日志）应该写在哪里呢？

函数式编程将那些跟数据计算无关的操作，都称为 "**副效应**" **（side effect）** 。

`useEffect()`用来引入具有副作用的操作，最常见的就是向服务器请求数据。可以把 `useEffect` Hook 看做 `componentDidMount`，`componentDidUpdate` 和 `componentWillUnmount` 这三个函数的组合。

**`useEffect()`的用法如下：**

> ```javascript
> useEffect(()  =>  {
>     // Async Action
>     //return 则是在页面被卸载时调用.返回一个函数来指定如何“清除”副作用
>     return fn;
> }, [dependencies])
> ```

上面用法中，`useEffect()`接受两个参数。第一个参数是一个函数，异步操作的代码放在里面。第二个参数是一个数组，用于给出 Effect 的依赖项，只要这个数组发生变化，`useEffect()`就会执行。第二个参数可以省略，这时每次组件渲染时，就会执行`useEffect()`。

**它的常见用途有下面几种：**

- 获取数据（data fetching）
- 事件监听或订阅（setting up a subscription）
- 改变 DOM（changing the DOM）
- 输出日志（logging）

**tips**

- **它在第一次渲染之后*和*每次更新之后都会执行**

- 使用`useEffect()`时，有一点需要注意。如果有多个副效应，应该调用多个`useEffect()`，而不应该合并写在一起。

- 在useEffect中，不仅会请求后端的数据，还会通过调用setData来更新本地的状态，这样会触发view的更新。

  但是，运行这个程序的时候，会出现无限循环的情况。useEffect在组件**mount**时执行，但也会在组件**更新**时执行。因为我们在每次请求数据之后都会设置本地的状态，所以组件会更新，因此useEffect会再次执行，因此出现了无限循环的情况。**我们只想在组件mount时请求数据。**我们可以传递一个空数组作为useEffect的第二个参数，这样就能避免在组件更新执行useEffect，只会在组件mount时执行。

  ```js
  import React, { useState, useEffect } from 'react';
  import axios from 'axios';
  
  function App() {
    const [data, setData] = useState({ hits: [] });
  
    useEffect(async () => {
      const result = await axios(
        'http://localhost/api/v1/search?query=redux',
      );
  
      setData(result.data);
    }, []);
  
    return (
      <ul>
        {data.hits.map(item => (
          <li key={item.objectID}>
            <a href={item.url}>{item.title}</a>
          </li>
        ))}
      </ul>
    );
  }
  
  export default App;
  ```

  升级加载loading 

  ```js
  import React, { Fragment, useState, useEffect } from 'react';
  import axios from 'axios';
  
  function App() {
    const [data, setData] = useState({ hits: [] });
    const [query, setQuery] = useState('redux');
    const [url, setUrl] = useState(
      'http://hn.algolia.com/api/v1/search?query=redux',
    );
    const [isLoading, setIsLoading] = useState(false);
  
    useEffect(() => {
      const fetchData = async () => {
        setIsLoading(true);
  
        const result = await axios(url);
  
        setData(result.data);
        setIsLoading(false);
      };
  
      fetchData();
    }, [url]);
    return (
      <Fragment>
        <input
          type="text"
          value={query}
          onChange={event => setQuery(event.target.value)}
        />
        <button
          type="button"
          onClick={() =>
            setUrl(`http://localhost/api/v1/search?query=${query}`)
          }
        >
          Search
        </button>
  
        {isLoading ? (
          <div>Loading ...</div>
        ) : (
          <ul>
            {data.hits.map(item => (
              <li key={item.objectID}>
                <a href={item.url}>{item.title}</a>
              </li>
            ))}
          </ul>
        )}
      </Fragment>
    );
  }
  
  export default App;
  ```

## useCallback和useMemo

https://www.xiaye0.com/?p=113

https://www.jianshu.com/p/014ee0ebe959

**useCallback和useMemo**都会在组件**第一次**渲染的时候执行，之后会在其**依赖的变量**发生改变时再次执行；并且这两个hooks都返回**缓存**，**useMemo返回缓存的变量(用来记住变量)，useCallback返回缓存的函数(用来记住函数)。**

```js
type DependencyList = ReadonlyArray<any>;

function useCallback<T extends (...args: any[]) => any>(callback: T, deps: DependencyList): T;

function useMemo<T>(factory: () => T, deps: DependencyList | undefined): T;
```

> **React 中当组件的 props 或 state 变化时，会重新渲染视图**

### useCallback

父组件给子组件传递属性（**函数**），父组件重新渲染，会重新创建函数，对应函数地址改变，即传给子组件的属性发生了变化，导致子组件渲染。

```js
const TextCell = memo(function(props:any) {
  console.log('我重新渲染了')
  return (
    <p onClick={props.click}>ffff</p>
  )
})

//父组件
const fatherComponent = () => {
const [number,setNumber] = useState(0);

const handleClick = useCallback(()=>{
   console.log(33)
},[])
 return(
    <div>
      模块{number}
      <TextCell click={handleClick}/>

      <Button onClick={()=>setNumber(number => number + 1)}>加加加</Button>
    </div>
  )
}
```

这里如果不使用useCallback,哪怕子组件用memo包裹了 也还是会更新子组件,因为子组件的绑定的函数click在父组件更新的时候也会更新**引用地址**,导致子组件的更新,但是这个其实是没必要的更新,绑定的函数并不需要子组件更新,useCallback就是阻止这类没必要的更新而存在的

这里需要注意的是 如果是有参数需要传递,则需要这样写

```js
<TextCell click={useCallback(()=>handleClick(‘传递的参数’),[])}/>
```

**作用**

- 防止死循环

  ```jsx
  // 用于记录 getData 调用次数
  let count = 0;
  
  function App() {
    const [val, setVal] = useState("");
  
    function getData() {
      setTimeout(() => {
        setVal("new data " + count);
        count++;
      }, 500);
    }
    return <Child val={val} getData={getData} />;
  }
  
  function Child({val, getData}) {
    useEffect(() => {
      getData();
    }, [getData]);
  
    return <div>{val}</div>;
  }
  ```

  执行过程：

  1. `App`渲染`Child`，将`val`和`getData`传进去
  2. `Child`使用`useEffect`获取数据。因为对`getData`有依赖，于是将其加入依赖列表
  3. `getData`执行时，调用`setVal`，导致`App`重新渲染
  4. `App`重新渲染时生成新的`getData`方法，传给`Child`
  5. `Child`发现`getData`的引用变了，又会执行`getData`
  6. 3 -> 5 是一个死循环

### useMemo

# 路由

路由是一种向用户显示不同页面的能力。 这意味着用户可以通过输入 URL 或单击页面元素在 WEB 应用的不同部分之间切换

这里需要说明一下 React Router 库中几个不同的 npm 依赖包，每个包都有不同的用途

| 相关组件            | 功能                                                         |
| ------------------- | ------------------------------------------------------------ |
| react-router        | 实现了路由的核心功能，用作下面几个包的运行时依赖项(peer dependency)。 |
| react-router-dom    | 基于 `react-router` 添加了浏览器运行环境的一些组件和功能。   |
| react-router-native | 适用于 `React Native`                                        |
| react-router-redux  | React Router 和 Redux 的集成。                               |
| eact-router-config  | 提供可配置化的路由                                           |

## React Routers三类组件

### 路由器Router

`<BrowserRouter>`和`<HashRouter>`，两者之间的主要区别是它们存储URL和与Web服务器通信的方式。

#### BrowserRouter

`<BrowserRouter>`使用常规的URL路径。这些通常是外观最好的URL，但是它们要求正确配置服务器。具体来说，您的Web服务器需要在所有由React Router客户端管理的URL上提供相同的页面。Create React App在开发中即开即用地支持此功能，并[附带](https://create-react-app.dev/docs/deployment#serving-apps-with-client-side-routing)有关如何配置生产服务器的说明。

- BrowserRouter提供了如下属性
  - `basename (string)` 当前位置的基准 URL
  - `forceRefresh (boolean)`，在导航的过程中整个页面是否刷新
  - `getUserConfirmation (func)`，当导航需要确认时执行的函数。默认是：window.confirm
  - `keyLength (number)`  location.key 的长度。默认是 6
  - `children (node)` 要渲染的子节点

#### HashRouter

`<HashRouter>`将当前位置存储在[URL](https://developer.mozilla.org/en-US/docs/Web/API/HTMLHyperlinkElementUtils/hash)[的`hash`一部分中](https://developer.mozilla.org/en-US/docs/Web/API/HTMLHyperlinkElementUtils/hash)，因此URL看起来像`http://example.com/#/your/page`。由于哈希从不发送到服务器，因此这意味着不需要特殊的服务器配置(**在任意的路由进行页面的刷新都不会是 404**)。

```jsx
<Router>
    <Route path="/" component={App}>
      <Route path="about" component={About} />
      <Route path="inbox" component={Inbox}>
        <Route path="messages/:id" component={Message} />
      </Route>
    </Route>
</Router>
```

#### 其他

其他还有 `<MemoryRouter>  内存路由组件`、`<NativeRouter>  Native的路由组件`、`<StaticRouter> 静态路由组件`这些路由组件，其中 MemoryRouter 主要用在 ReactNative 这种非浏览器的环境中，因此直接将 URL 的 history 保存在了内容中。StaticRouter 主要用于服务器端渲染

#### 参数

- **basename: string**

  原因是：ngix服务器上面要放不止一个网站 根目录下面已经有一个网站，这个网站需单独建一个文件夹。

  作用：为所有位置添加一个基准URL
  使用场景：假如你需要把页面部署到服务器的二级目录，你可以使用 `basename` 设置到此目录。

#### History 的小知识

每个 `<Router>` 组件都会创建一个 `history` 对象，它记录了当前的位置（`history.location`），还记录了堆栈中以前的位置。在当前位置发生变化时，页面会被重新渲染，于是你就有一种导航跳转的感觉。

那么如何改变当前的位置呢？也就是说如何做到**导航跳转**呢？这时候 `history` 的作用就来了，这个对象暴露了一些方法，比如 `history.push` 和 `history.replace` ，它们就可以拿来处理上面的问题。

当你点击一个 `<Link>` 组件时，`history.push` 就会被调用，而当你使用一个 `<Redirect>` 组件时，`history.replace` 就会被调用。其它的方法比如 `history.goBack` 和 `history.goForward` 可以用来在历史堆栈中回溯或前进。

### 路线匹配器Route

`<Route>`和`<Switch>`

```jsx
 <Switch>
    <Route path="/about">
        <About />
    </Route>
    //  会把没有匹配的路径直接重定向到 /login
    <Redirect to="/login" />
    {/* 从 /inbox/messages/:id 跳转到 /messages/:id */}
    <Redirect from="messages/:id" to="/messages/:id" />
 </Switch>
```

#### Route

当 location 与 Route 的 path 匹配时渲染 Route 中的 Component

##### **三种渲染方式**

- `<Route component>`

- `<Route render>`
  
  `render` function 类型，Route 会渲染这个 function 的返回值，可以在函数中附加一些额外的逻辑，所以你可以在render中添加一些逻辑判断，再返回一个要渲染的 component

- `<Route children>`

  `children` function 类型，比 `render` 多了 `match参数`，可以根据 match参数来决定匹配的时候渲染什么，不匹配的时候渲染什么

##### **参数**

[说明文字](#jump)

- <a id="exact">exact</a> 是否进行精确匹配，路由 `/a` 可以和 `/a/、/a` 匹配

  > 当exact为false时，根据路由匹配所有组件，例如/a/b/c 能匹配到/、/a、/a/b、/a/b/c 且匹配还是按顺序的
  >
  > 例如路由设置的前后顺序为:
  > 1./ ；
  > 2./a；
  > 3./a/b ; 
  > 4./a/b/c
  > 且前3个路径都没有设置 exact，这样前3个组件**都会被渲染**并且默认将2当作1的子页面，3当作2的子页面

- `strict` 是否进行严格匹配，指明路径只匹配以斜线结尾的路径，路由`/a`可以和`/a`匹配，不能和`/a/`匹配，相比 `exact` 会更严格些

- `path (string)` 标识路由的路径,`path`属性可以使用通配符。

  ```
  <Route path="/hello/:name">
  ```

    通配符的规则如下:

> **（1）`:paramName`**
>
> `:paramName`匹配URL的一个部分，直到遇到下一个`/`、`?`、`#`为止。这个路径参数可以通过`this.props.params.paramName`取出。
>
> **（2）`()`**
>
> `()`表示URL的这个部分是可选的。
>
> **（3）`\*`**
>
> `*`匹配任意字符，直到模式里面的下一个字符为止。匹配方式是非贪婪模式。
>
> **（4） `\**`**
>
> `**` 匹配任意字符，直到下一个`/`、`?`、`#`为止。匹配方式是贪婪模式。

- `component` 表示路径对应显示的组件
- `location (object)` 除了通过 path 传递路由路径，也可以通过传递 location 对象可以匹配
- `sensitive (boolean)` 匹配路径时，是否区分大小写

##### **属性的隐式传递**

```
location、history、match
```

- 三个 props 比较常用的是 match，通过 match.params 可以取到动态参数的值

| 所属     | 属性                   | 类型     | 含义                                              |
| -------- | ---------------------- | -------- | ------------------------------------------------- |
| history  | length                 | number   | 表示history堆栈的数量                             |
|          | action                 | string   | 表示当前的动作。比如pop、replace或push            |
|          | location               | object   | 表示当前的位置                                    |
|          | push(path, [state])    | function | 在history堆栈顶加入一个新的条目                   |
|          | replace(path, [state]) | function | 替换在history堆栈中的当前条目                     |
|          | go(n)                  | function | 将history堆栈中的指针向前移动                     |
|          | goBack()               | function | 等同于go(-1)                                      |
|          | goForward()            | function | 等同于go(1)                                       |
|          | block(promt)           | function | 阻止跳转                                          |
|          |                        |          |                                                   |
| match    | params                 | object   | 表示路径参数，通过解析URL中动态的部分获得的键值对 |
|          | isExact                | boolean  | 为true时，表示精确匹配                            |
|          | path                   | string   | 用来做匹配的路径格式                              |
|          | url                    | string   | URL匹配的部分                                     |
|          |                        |          |                                                   |
| location | pathname               | string   | URL路径                                           |
|          | search                 | string   | URl中查询字符串                                   |
|          | hash                   | string   | URL的hash分段                                     |
|          | state                  | string   | 表示location中的状态                              |

#### Swtich

`Swtich` 就近匹配路由，仅渲染一个路由，路由的默认行为是匹配了就直接渲染

```jsx
/// 假设你访问的URL为 /dog
<Route path='/dog' component={Dog}></Route> // 虽然这里匹配了，但不会停止查找
<Route path="/:dog" component={Husky}></Route> // 这个路由依然会被匹配，这样两个组件都会被渲染
...
<Switch>
  <Route path='/dog' component={Dog}></Route> // Switch 匹配一个路由后就不会再去查找下一个路由，那么下面的路由就不会被匹配
  <Route path="/:dog" component={Husky}></Route>
</Switch>
```

### Link

#### 导航Link

`<Link>` 组件被用来在**页面之间**进行导航，它其实就是 HTML 中的 `<a>` 标签的上层封装，不过在其源码中使用 `event.preventDefault` 禁止了其默认行为，然后使用 [history API](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FAPI%2FHistory_API) 自己实现了跳转。我们都知道，如果使用 `<a>` 标签去进行导航的话，整个页面都会被刷新，这是我们不希望看到的，当然，跳转到首页这种行为我倒是蛮喜欢用 `<a>` 标签的

所以我们使用 `<Link>` 组件来导航到一个目标 URL，可以在不刷新页面的情况下重新渲染页面

```js
//无论您在何处呈现<Link>，<a>都会在HTML文档中呈现锚点。
<Link to="/">Home</Link>
// <a href="/">Home</a>
```

`Link` 声明路由要跳转的地方

- ```
  to（string | object | function）
  ```

   需要跳转到的路径(pathname) 或地址（location）

  - 为 string 时 就是一个明确的路径地址
  - 为 object 时有如下属性（就是一个location对象）
    - pathname：URL路径。
    - search：URl中查询字符串。
    - hash：URL的hash分段，例如#a-hash。
    - state：表示location中的状态
  - 为 function 时，就是一个函数接收当前 location 为参数，然后以字符串或对象的形式返回位置形式

- `replace (boolean)` 为 true 是替换历史记录，false 是新增历史记录

```jsx
  <Link to="/course" />
  ...
  <Link to={{
    pathname: '/course',
    search: '?sort=name',
    hash: '#the-hash',
    state: { fromDashboard: true }
  }} />
  ...
  <Link to={location => ({ ...location, pathname: "/courses" })} />
  
	<Link to={location => `${location.pathname}?sort=name`} />

```

#### `NavLink` 

`NavLink` 功能与 `Link` 类似不过参数更多，并且可以设置被选中时的样式或者类

- `exact (boolean)` 是否进行精确匹配
- `strict (boolean)` 是否进行严格匹配
- `to（string | object）` 需要跳转到的路径(pathname)或地址（location）
- `activeClassName (string)` 是选中状态的类名，我们可以为其添加样式
- `activeStyle (Object)` 元素处于选中状态时，应用于元素的样式
- `isActive(function)` 添加额外逻辑以确定链接是否处于活动状态

#### Redirect

重定向，新位置将覆盖历史堆栈中的当前位置

- `from (string)` 需要重定向的路径，可以包括动态参数

  `push (boolean)` 为 true 时，重定向会将新条目推入历史记录，而不是替换当前条目

  `to (string | object)` 重定向到的路径

  `exact (boolean)` 是否要对 from 进行精确匹配

  `strict (boolean)` 是否要对 from 进行严格匹配

  `sensitive (boolean)` 匹配 from 时是否区分大小写

### IndexRoute和IndexRedirect

#### Index Routes

通常情况下，我们会建立如下情况的路由：

```jsx
<Router>
  <Route path="/" component={App}>
    <Route path="accounts" component={Accounts}/>
    <Route path="statements" component={Statements}/>
  </Route>
</Router>
```

当用户访问 `/` 时, App 组件被渲染，但组件内的子元素却没有， `App` 内部的 `this.props.children` 为 undefined 。 你可以简单地使用 `{this.props.children ||}` 来渲染一些默认的 UI 组件。

```jsx
<Router>
  <Route path="/" component={App}>
    <IndexRoute component={Home}/>
    <Route path="accounts" component={Accounts}/>
    <Route path="statements" component={Statements}/>
  </Route>
</Router>
```

如此配置后，我们再次访问 `/` 路由，你会发现页面渲染了 Home 组件的内容。这就是 IndexRoute 的功能，指定一个路由的默认页。

#### Index Redirects

上面这种情况比较常见，还有一种非常常见的方式就是当我们尝试访问 `/` 这个路由时，我们想让其直接跳转到 ‘/Accounts’，直接免去了默认页 Home，这样来的更加直接。由此我们就需要 `IndexRedirect` 功能。考虑如下路由：

```
<Router>
  <Route path="/" component={App}>
    <IndexRedirect to="/accounts"/>
    <Route path="accounts" component={Accounts}/>
    <Route path="statements" component={Statements}/>
  </Route>
</Router>
```

这样设计路由后，我们再次访问 `/` 时，系统默认会跳转到 `/accounts` 路由。

#### 总结

以上就是 IndexRoute 和 IndexRedirect 的功能介绍，让我们来总结一下他们两个的区别。

- IndexRoute 一般情况下用于设计一个默认页且不改变 URL 地址，而 IndexRedirect 则是跳转默认地址且地址会发生改变。
- IndexRoute 指定一个组件作为默认页，而 IndexRedirect 指定一个路由地址作为跳转地址。

## Hooks

### useHistory

- 用以获取history对象，进行编程式的导航

```
const Husky = props => {
  console.log(useHistory()); // 与 props.history 结果一致
  console.log(props.history);
  return <div>哈士奇</div>;
};
...
/// 使用 useHistory 的好处是，引入组件会更自由些
<Route path="/dog" component={Dog}></Route> // 必须这么写，props 才能拿到相关值
...
<Route path="/husky">
	<Husky />
</Route> // 这样写的话 useHistory 可以正常取值，但是 props 不行
复制代码
```

### useLocation

- 用以获取location对象，可以查看当前路由信息

```
const Husky = props => {
  console.log(useLocation()); // 与 props.location 结果一致
  console.log(props.location);
  return <div>哈士奇</div>;
};
复制代码
```

### useParams

- 可以用来获取 match.params

```
const Husky = props => {
    console.log(useParams()) // 与 props.match.params 结果一致，但明显更简洁
    console.log(props.match.params)
    const {eat} = props.match.params;
    return (
    	<div>哈士奇 吃 {eat}</div>
    );
}
复制代码
```

### useRouteMatch

- 可以接受一个 path 字符串作为参数。当参数的path与当前的路径相匹配时，useRouteMatch会返回 match 对象，否则返回 null。

```
// 接收一个字符串作为参数
const Husky = props => {
		const match = useParams('/husky'); // 假定当前匹配路由就是 /husky，如果访问的路径不是 /husky ，那么 match 就为 null
    const {eat} = match ? match.params : '';
    return (
    	<div>哈士奇 吃 {eat}</div>
    );
}
```

## 实战

### 路由嵌套

可以通过嵌套 `route` 来实现路由嵌套，注意[exact](#exact)

```jsx
//根路由
<Switch>
    <Route path="router" component={Router}></Route>
</Switch>
//Router.jsx
<span>router</span>
<ul>
    <li>
        <Link to="/router/second/1">1</Link>
    </li>
    <li>
        <Link to="/router/second/12">2</Link>
    </li>
</ul>
//子路由的配置分散到各组件中
<Switch>
    <Route exact path="/router/second/:id" component={Second}></Route>
</Switch>
```

注意：如果在父路由中开启 exact 匹配，就会导致子组件加载不出来

### 路由懒加载

https://zh-hans.reactjs.org/docs/code-splitting.html

**Suspense和lazy**

如果我们项目有三个模块，用户管理（UserManage）、资产管理（AssetManage）、考勤管理（AttendanceManage）。当我们进入首页的时候由于没有进入任何一个模块，为了提高响应效率是不需要进行模块资源加载的，同时当我们进入用户管理的时候只需要加载用户管理路由对应的模块资源，进入其他模块亦然。这时候我们就需要对代码进行拆分，React.lazy可以结合Router来对模块进行懒加载。

```jsx
import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';
import React, { Suspense, lazy } from 'react';

// 懒加载引入组件 在用到路由组件时才发送请求
// 通过React的lazy函数配合import()函数动态加载路由组件 ===> 路由组件代码会被分开打包 

const Home = lazy(() => import('./routes/Home'));
const UserManage = lazy(() => import('./routes/UserManage'));
const AssetManage = lazy(() => import('./routes/AssetManage'));
const AttendanceManage = lazy(() => import('./routes/AttendanceManage'));

const App = () => (
  <Router>
     {/* 用Suspense包含所有需要注册的路由 fallback为响应未回来时显示的内容 */}
    <Suspense fallback={<div>Loading...</div>}>
      <Switch>
        <Route exact path="/" component={Home}/>
        <Route path="/userManage" component={UserManage}/>
        <Route path="/assetManage" component={AssetManage}/>
        <Route path="/attendanceManage" component={AttendanceManage}/>
      </Switch>
    </Suspense>
  </Router>
)
```

### withRouter

本质: 高阶组件

作用: 可以在非路由组件中注入路由对象

在没有路由指向(就是没有Route对象)的组件默认this.props当中没有路由所需要的参数，使用withRouter可以添加

# 状态管理器

## **Mobx**

作为了解的内容，在项⽬中使⽤redux的情况更多。

Mobx是⼀个功能强⼤，上⼿⾮常容易的状态管理⼯具。redux的作者也曾经向⼤家推荐过它，在不少情况下可以使⽤Mobx来替代掉redux。

## hox

**定义 Model**： 用 `createModel` 包装后，就变成了持久化，且全局共享的数据。 

```javascript
import { createModel } from 'hox';

/* 任意一个 custom Hook */
function useCounter() {
  const [count, setCount] = useState(0);
  const decrement = () => setCount(count - 1);
  const increment = () => setCount(count + 1);
  return {
    count,
    decrement,
    increment
  };
}

export default createModel(useCounter)
```

**使用 Model**：`createModel` 返回值是个 Hook，你可以按 React Hooks 的用法正常使用它。

```javascript
import { useCounterModel } from "../models/useCounterModel";

function App(props) {
  const counter = useCounterModel();
  return (
    <div>
      <p>{counter.count}</p>
      <button onClick={counter.increment}>Increment</button>
    </div>
  );
}
```

## Redux

Redux是将整个应用状态存储到一个地方，称为store。里面保存一棵状态树(state tree)。组件可以派发(dispatch)行为(action)给store,action发出命令后将state放入reucer加工函数中，返回新的state。其它组件可以通过订阅store中的状态(state)来刷新自己的视图

<img src="https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/12/15/16f09a0b5196a2dd~tplv-t2oaga2asx-watermark.awebp" alt="img" style="zoom:50%;" />



### 三大原则

#### 单一数据源

**整个应用的state被储存在一棵 object tree 中，并且这个 object tree 只存在于唯一一个store 中。**

#### State 是只读的

**唯一改变 state 的方法就是触发 action，action 是一个用于描述已发生事件的普通对象。**

这样确保了视图和网络请求都不能直接修改 state，相反它们只能表达想要修改的意图。**action就是改变state的指令，有多少操作state的动作就会有多少action。**

```js
//添加todo任务的 action 是这样的：
const ADD_TODO = 'ADD_TODO'

//action创建函数，返回一个action对象 
function addTodo(text) {
  return{
  type: ADD_TODO,//执行的动作
  text: 'Build my first Redux app'，
  index：5，//用户完成任务的动作序列号
}
}

//Redux 中只需把 action 创建函数的结果传给 dispatch() 方法即可发起一次dispatch 过程。
dispatch(addTodo(text))
//或者创建一个被绑定的 action 创建函数来自动 dispatch：
const boundAddTodo = text => dispatch(addTodo(text))
boundAddTodo(text);
//store 里能直接通过 store.dispatch() 调用 dispatch() 方法，但是多数情况下你会使用 react-redux 提供的 connect() 帮助器来调用。
```

#### 使用纯函数来执行修改

**reducer 就是一个纯函数，接收旧的 state 和 action，返回新的 state。**

```js
(previousState, action) => newState
```

之所以将这样的函数称之为reducer，是因为这种函数与被传入 [`Array.prototype.reduce(reducer, ?initialValue)`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce) 里的回调函数属于相同的类型。保持 reducer 纯净非常重要。**永远不要**在 reducer 里做这些操作：

- 修改传入参数；
- 执行有副作用的操作，如 API 请求和路由跳转；
- 调用非纯函数，如 `Date.now()` 或 `Math.random()`。

**这是一个redux的经典案例**

- 定义reducer函数根据action的类型改变state
- actions 定义指令
- 通过createStore创建store
- 调用store.dispatch()发出修改state的命令

```js
import { createStore } from 'redux';
//这里一个技巧是使用 ES6 参数默认值语法 来精简代码。
const reducer = (state = {count: 0}, action) => {
  switch (action.type){
    case 'INCREASE': return {count: state.count + 1};
    case 'DECREASE': return {count: state.count - 1};
    default: return state;
  }
}
const actions = {
  increase: () => ({type: 'INCREASE'}),
  decrease: () => ({type: 'DECREASE'})
}
// 创建 Redux store 来存放应用的状态。
// API 是 { subscribe, dispatch, getState }。
let store = createStore(counter);

// 可以手动订阅更新，也可以事件绑定到视图层。
store.subscribe(() =>
  console.log(store.getState())
);

// 改变内部 state 惟一方法是 dispatch 一个 action。
// action 可以被序列化，用日记记录和储存下来，后期还可以以回放的方式执行
store.dispatch(actions.increase()) // {count: 1}
store.dispatch(actions.increase()) // {count: 2}
store.dispatch(actions.increase()) // {count: 3}
```

### store构建

#### **目录结构**

[![屏幕截图](https://z3.ax1x.com/2021/01/19/sgpjbR.png)](https://imgchr.com/i/sgpjbR)

#### action

**存放描述行为的数据结构(本质上是 JavaScript 普通对象),一般来说你会通过 store.dispatch() 将 action 传到 store。**

我们约定，action 内必须使用一个字符串类型的 `type` 字段来表示将要执行的动作。多数情况下，`type` 会被定义成字符串常量。当应用规模越来越大时，建议使用单独的模块或文件来存放 action。

```js
//	./actions/counter.js
export const INCREMENT = 'INCREMENT';
export const DECREMENT = 'DECREMENT';

export const increment = ()=>{
  {type:INCREMENT}
}
export const decrement = ()=>{
  {type:DECREMENT}
}
```

注意：当我们表示用户完成任务的动作序列号时，我们还需要再添加一个 action index 来，所以我们通过下标 `index` 来引用特定的任务。而实际项目中一般会在新建数据的时候生成唯一的 ID 作为数据的引用标识。

#### **Reducer**

**Reducers** 指定了应用状态的变化如何响应 [actions](https://www.redux.org.cn/docs/basics/Actions.html) 并发送到 store 的。

```js
//	./reducers/counter.js
import {INCREMENT, DECREMENT} from "../actions/counter"
export default function(state = 0, action){
    switch (action.type) {
        case INCREMENT:
          return state + 1;
        case DECREMENT:
          return state - 1;
        default:
          return state;
        }
}
```

```js
//	./reducers/index.js
import { combineReducers } from 'redux'
import counter from './counter'

export default combineReducers({
	counter
})

```

#### store

**注意：Redux 应用只有一个单一的 store**

我们学会了使用 action 来描述“发生了什么”，和使用 reducers 来根据 action 更新 state 的用法。

**Store** 就是把它们联系到一起的对象。Store 有以下职责：

- 维持应用的 state；
- 提供 [`getState()`](https://www.redux.org.cn/docs/api/Store.html#getState) 方法获取 state；
- 提供 [`dispatch(action)`](https://www.redux.org.cn/docs/api/Store.html#dispatch) 方法更新 state；
- 通过 [`subscribe(listener)`](https://www.redux.org.cn/docs/api/Store.html#subscribe) 注册监听器;
- 通过 [`subscribe(listener)`](https://www.redux.org.cn/docs/api/Store.html#subscribe) 返回的函数注销监听器。

https://zhuanlan.zhihu.com/p/258017257

```js
import { createStore, applyMiddleware, compose } from 'redux'
import { createLogger } from 'redux-logger'
import thunk from 'redux-thunk'
import reducers from './reducers'

function configureStore() {
  const logger = createLogger({})

  const middlewares = [thunk]

  if (process.env.NODE_ENV !== 'production') {
    middlewares.push(logger)
  }

  const composeEnhancers =
    typeof window === 'object' && window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__
      ? window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({})
      : compose
  const enhancer = composeEnhancers(applyMiddleware(...middlewares))
//createStore() 的第二个参数是可选的, 用于设置 state 初始状态。这对开发同构应用时非常有用，服务器端 redux 应用的 state 结构可以与客户端保持一致, 那么客户端可以将从网络接收到的服务端 state 直接用于本地数据初始化。
  return createStore(reducers, enhancer)
}

export default configureStore()

```

### redux 异步请求

https://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_two_async_operations.html

![img](https://upload-images.jianshu.io/upload_images/18616547-35a9f5f3f9956a6b.png?imageMogr2/auto-orient/strip|imageView2/2/w/800/format/webp)

Action 发出以后，Reducer 立即算出 State，这叫做同步；Action 发出以后，过一段时间再执行 Reducer，这就是异步。
在实际的开发中，redux中管理的很多数据可能来自服务器，我们需要进行异步的请求，再将数据保存到redux中。就是说在异步的网络请求中通过dispatch action来更新state中的数据。这时候就需要用到Redux中间件**(指这个框架允许我们在某个流程的执行中间插入我们自定义的一段代码)**。

[Thunk middleware](https://github.com/gaearon/redux-thunk) 并不是 Redux 处理异步 action 的唯一方式：

- 你可以使用 [redux-promise](https://github.com/acdlite/redux-promise) 或者 [redux-promise-middleware](https://github.com/pburtchaell/redux-promise-middleware) 来 dispatch Promise 来替代函数。
- 你可以使用 [redux-observable](https://github.com/redux-observable/redux-observable) 来 dispatch Observable。
- 你可以使用 [redux-saga](https://github.com/yelouafi/redux-saga/) 中间件来创建更加复杂的异步 action。
- 你可以使用 [redux-pack](https://github.com/lelandrichardson/redux-pack) 中间件 dispatch 基于 Promise 的异步 Action。

### API

#### Provider 组件

`<Provider store>` 使组件层级中的 `connect()` 方法都能够获得 Redux store。正常情况下，你的根组件应该嵌套在 `<Provider>` 中才能使用 `connect()` 方法。

React-Redux 提供`Provider`组件，可以让容器组件拿到`state`。

> ```javascript
> import { Provider } from 'react-redux'
> import { createStore } from 'redux'
> import todoApp from './reducers'
> import App from './components/App'
> 
> let store = createStore(todoApp);
> 
> render(
> <Provider store={store}>
>  <App />
> </Provider>,
> document.getElementById('root')
> )
> ```

上面代码中，`Provider`在根组件外面包了一层，这样一来，`App`的所有子组件就默认都可以拿到`state`了。

**它的原理是`React`组件的[`context`](https://facebook.github.io/react/docs/context.html)属性**，请看源码。

> ```javascript
> class Provider extends Component {
> getChildContext() {
>  return {
>    store: this.props.store
>  };
> }
> render() {
>  return this.props.children;
> }
> }
> 
> Provider.childContextTypes = {
> store: React.PropTypes.object
> }
> ```

上面代码中，`store`放在了上下文对象`context`上面。然后，子组件就可以从`context`拿到`store`，代码大致如下。

> ```js
> class VisibleTodoList extends Component {
> componentDidMount() {
>  const { store } = this.context;
>  this.unsubscribe = store.subscribe(() =>
>    this.forceUpdate()
>  );
> }
> 
> render() {
>  const props = this.props;
>  const { store } = this.context;
>  const state = store.getState();
>  // ...
> }
> }
> 
> VisibleTodoList.contextTypes = {
> store: React.PropTypes.object
> }
> ```

`React-Redux`自动生成的容器组件的代码，就类似上面这样，从而拿到`store`。

#### connect

React-Redux 提供`connect`方法，用于从 UI 组件生成容器组件。`connect`的意思，就是将这两种组件连起来。

> ```javascript
> import { connect } from 'react-redux'
> const VisibleTodoList = connect()(TodoList);
> ```

上面代码中，`TodoList`是 UI 组件，`VisibleTodoList`就是由 React-Redux 通过`connect`方法自动生成的容器组件。

但是，因为没有定义业务逻辑，上面这个容器组件毫无意义，只是 UI 组件的一个单纯的包装层。为了定义业务逻辑，需要给出下面两方面的信息。

> （1）输入逻辑：外部的数据（即`state`对象）如何转换为 UI 组件的参数
>
> （2）输出逻辑：用户发出的动作如何变为 Action 对象，从 UI 组件传出去。

因此，`connect`方法的完整 API 如下。

> ```javascript
> import { connect } from 'react-redux'
> 
> const VisibleTodoList = connect(
> mapStateToProps,
> mapDispatchToProps
> )(TodoList)
> ```

上面代码中，`connect`方法接受两个参数：`mapStateToProps`和`mapDispatchToProps`。它们定义了 UI 组件的业务逻辑。前者负责输入逻辑，即将`state`映射到 UI 组件的参数（`props`），后者负责输出逻辑，即将用户对 UI 组件的操作映射成 Action。

##### mapStateToProps()

`mapStateToProps`是一个函数。它的作用就是像它的名字那样，建立一个从（外部的）`state`对象到（UI 组件的）`props`对象的映射关系。也就是说， **把state映射到props中去** 

作为函数，`mapStateToProps`执行后应该返回一个对象，里面的每一个键值对就是一个映射。请看下面的例子。

> ```javascript
> const mapStateToProps = (state) => {
>  return {
>     todos: getVisibleTodos(state.todos, state.visibilityFilter)
>  }
> }
> ```

上面代码中，`mapStateToProps`是一个函数，它接受`state`作为参数，返回一个对象。这个对象有一个`todos`属性，代表 UI 组件的同名参数，后面的`getVisibleTodos`也是一个函数，可以从`state`算出 `todos` 的值。

下面就是`getVisibleTodos`的一个例子，用来算出`todos`。

> ```javascript
> const getVisibleTodos = (todos, filter) => {
> switch (filter) {
>  case 'SHOW_ALL':
>    return todos
>  case 'SHOW_COMPLETED':
>    return todos.filter(t => t.completed)
>  case 'SHOW_ACTIVE':
>    return todos.filter(t => !t.completed)
>  default:
>    throw new Error('Unknown filter: ' + filter)
> }
> }
> ```

`mapStateToProps`会订阅 Store，每当`state`更新的时候，就会自动执行，重新计算 UI 组件的参数，从而触发 UI 组件的重新渲染。

`mapStateToProps`的第一个参数总是`state`对象，还可以使用第二个参数，代表容器组件的`props`对象。

> ```javascript
> // 容器组件的代码
> //    <FilterLink filter="SHOW_ALL">
> //      All
> //    </FilterLink>
> 
> const mapStateToProps = (state, ownProps) => {
> return {
>  active: ownProps.filter === state.visibilityFilter
> }
> }
> ```

使用`ownProps`作为参数后，如果容器组件的参数发生变化，也会引发 UI 组件重新渲染。

`connect`方法可以省略`mapStateToProps`参数，那样的话，UI 组件就不会订阅Store，就是说 Store 的更新不会引起 UI 组件的更新。

##### mapDispatchToProps()

`mapDispatchToProps`是`connect`函数的第二个参数，**用来建立各种dispatch变成props，让你可以直接使用  UI 组件的参数到`store.dispatch`方法的映射**。也就是说，**把各种dispatch变成了props让你可以直接使用** 

如果`mapDispatchToProps`是一个函数，会得到`dispatch`和`ownProps`（容器组件的`props`对象）两个参数。

> ```javascript
> const mapDispatchToProps = (
> dispatch,
> ownProps
> ) => {
> return {
>  onClick: () => {
>    dispatch({
>      type: 'SET_VISIBILITY_FILTER',
>      filter: ownProps.filter
>    });
>  }
> };
> }
> ```

从上面代码可以看到，`mapDispatchToProps`作为函数，应该返回一个对象，该对象的每个键值对都是一个映射，定义了 UI 组件的参数怎样发出 Action。

如果`mapDispatchToProps`是一个对象，它的每个键名也是对应 UI 组件的同名参数，键值应该是一个函数，会被当作 Action creator ，返回的 Action 会由 Redux 自动发出。举例来说，上面的`mapDispatchToProps`写成对象就是下面这样。

> ```javascript
> const mapDispatchToProps = {
> onClick: (filter) => {
>  type: 'SET_VISIBILITY_FILTER',
>  filter: filter
> };
> }
> ```



##### 实例：计数器

我们来看一个实例。下面是一个计数器组件，它是一个纯的 UI 组件。

> ```js
> import React from "react";
> import { connect } from "react-redux";
> import { increment, decrement } from "../../store/actions/counter";
> 
> const Home = function (props) {
>  //生成props
> const { count, onincrement, ondecrement} = props;
> // console.log(props);
> return (
>    <div>
>      <Button
>        variant="contained"
>        color="primary"
>        onClick={onincrement}
>      >
>        increment
>      </Button>
>      <Button
>        variant="contained"
>        color="primary"
>        onClick={ondecrement}
>        style={{marginLeft:'30px'}}
>      >
>        decrement
>      </Button>
>      <p style={{fontSize:'30px'}}>{count}</p>
>    </div>
> );
> };
> ```

上面代码中，这个 UI 组件有三个参数：count和 onincrement, ondecrement。前者需要从`state`计算得到，后者需要向外发出 Action。

接着，定义`count`到`state`的映射，以及`onincrement, ondecrement`到`dispatch`的映射。

> ```javascript
> function mapStateToProps(state) {
>  console.log(state)
>   return {
>    count: state.counter.count,
> };
> }
> function mapDispatchToProps(dispatch) {
>  return {
>     onincrement: () => dispatch(increment()),
>    ondecrement: () => dispatch(decrement())
> };
> }
> 
> ```

然后，使用`connect`方法生成容器组件。

> ```javascript
> export default connect(mapStateToProps, mapDispatchToProps)(Home);
> ```

然后，定义这个组件的 Reducer。

> ```javascript
> // Reducer
> import {INCREMENT, DECREMENT} from "../actions/counter"
> export default function(state = { count: 0}, action){
> const count = state.count
> switch (action.type) {
>      case INCREMENT:
>        return {count:count + 1};
>      case DECREMENT:
>        return {count:count - 1};
>      default:
>        return {count:count};
>      }
> }
> ```

最后，生成`store`对象，并使用`Provider`在根组件外面包一层。

> ```js
> import React from "react";
> import route from "../route/index.js";
> import { Provider } from "react-redux";
> import store from "../store";
> export default function Menu() {
> const classes = useStyles();
> return (
>  <div className={classes.root}>
>    <Provider store={store}>
>    </Provider>
>  </div>
> );
> }
> 
> ```

#### createStore

`createStore(reducer, [preloadedState], enhancer)`

创建一个 Redux [store](https://www.redux.org.cn/docs/api/Store.html) 来以存放应用中所有的 state。
应用中应有且仅有一个 store。

**参数**

1. `reducer` *(Function)*: 接收两个参数，分别是当前的 state 树和要处理的 [action](https://www.redux.org.cn/docs/Glossary.html#action)，返回新的 [state 树](https://www.redux.org.cn/docs/Glossary.html#state)。
2. [`preloadedState`] *(any)*: 初始时的 state。 在同构应用中，你可以决定是否把服务端传来的 state 水合（hydrate）后传给它，或者从之前保存的用户会话中恢复一个传给它。如果你使用 [`combineReducers`](https://www.redux.org.cn/docs/api/combineReducers.html) 创建 `reducer`，它必须是一个普通对象，与传入的 keys 保持同样的结构。否则，你可以自由传入任何 `reducer` 可理解的内容。
3. `enhancer` *(Function)*: Store enhancer 是一个组合 store creator 的**高阶函数**，返回一个新的强化过的 store creator。这与 middleware 相似，它也允许你通过复合函数改变 store 接口。

**返回值**

([*`Store`*](https://www.redux.org.cn/docs/api/Store.html)): 保存了应用所有 state 的对象。改变 state 的惟一方法是 [dispatch](https://www.redux.org.cn/docs/api/Store.html#dispatch) action。你也可以 [subscribe 监听](https://www.redux.org.cn/docs/api/Store.html#subscribe) state 的变化，然后更新 UI。

```js
import { createStore } from 'redux'

function todos(state = [], action) {
  switch (action.type) {
    case 'ADD_TODO':
      return state.concat([action.text])
    default:
      return state
  }
}

let store = createStore(todos, ['Use Redux'])

store.dispatch({
  type: 'ADD_TODO',
  text: 'Read the docs'
})

console.log(store.getState())
// [ 'Use Redux', 'Read the docs' ]
```

- 应用中不要创建多个 store！相反，使用 [`combineReducers`](https://www.redux.org.cn/docs/api/combineReducers.html) 来把多个 reducer 创建成一个根 reducer。
- 要使用多个 store 增强器的时候，你可能需要使用 [compose](https://www.redux.org.cn/docs/api/compose.html)

#### Store 方法

Store 就是用来维持应用所有的 state 树 的一个对象。 改变 store 内 state 的惟一途径是对它 dispatch 一个 action。

- getState()
- dispatch(action)
- subscribe(listener)
- replaceReducer(nextReducer)

#### combineReducers

随着应用变得越来越复杂，可以考虑将 [reducer 函数](https://www.redux.org.cn/docs/Glossary.html#reducer) 拆分成多个单独的函数，拆分后的每个函数负责独立管理 [state](https://www.redux.org.cn/docs/Glossary.html#state) 的一部分。

```
import { combineReducers } from 'redux'
import counter from './counter'

export default combineReducers({
	counter
})
```

combineReducers把一个由多个不同 reducer 函数作为 value 的 object，合并成一个最终的 reducer 函数，然后就可以对这个 reducer 调用 createStore 方法。

合并后的 reducer 可以调用各个子 reducer，并把它们返回的结果合并成一个 state 对象。

#### applyMiddleware

https://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_two_async_operations.html

**applyMiddleware(...middlewares)**

使用包含自定义功能的 middleware 来扩展 Redux 是一种推荐的方式。Middleware 可以让你包装 store 的 dispatch 方法来达到你想要的目的。同时， middleware 还拥有“可组合”这一关键特性。多个 middleware 可以被组合到一起使用，形成 middleware 链。其中，每个 middleware 都不需要关心链中它前后的 middleware 的任何信息。

Middleware 最常见的使用场景是实现异步 actions。这种方式可以让你像 dispatch 一般的 actions 那样 **dispatch 异步 actions**。

**示例: 自定义 Logger Middleware**

```js
import { createStore, applyMiddleware } from 'redux'
import todos from './reducers'

function logger({ getState }) {
  return (next) => (action) => {
    console.log('will dispatch', action)

    // 调用 middleware 链中下一个 middleware 的 dispatch。
    let returnValue = next(action)

    console.log('state after dispatch', getState())

    // 一般会是 action 本身，除非
    // 后面的 middleware 修改了它。
    return returnValue
  }
}

let store = createStore(
  todos,
  [ 'Use Redux' ],
  applyMiddleware(logger)
)

store.dispatch({
  type: 'ADD_TODO',
  text: 'Understand the middleware'
})
// (将打印如下信息:)
// will dispatch: { type: 'ADD_TODO', text: 'Understand the middleware' }
// state after dispatch: [ 'Use Redux', 'Understand the middleware' ]
```

####   ` compose(...functions)`

从右到左来组合多个函数。

这是函数式编程中的方法，为了方便，被放到了 Redux 里。
当需要把多个 [store 增强器](https://www.redux.org.cn/docs/Glossary.html#store-enhancer) 依次执行的时候，需要用到它。

**参数**

1. (*arguments*): 需要合成的多个函数。预计每个函数都接收一个参数。它的返回值将作为一个参数提供给它左边的函数，以此类推。例外是最右边的参数可以接受多个参数，因为它将为由此产生的函数提供签名。（译者注：`compose(funcA, funcB, funcC)` 形象为 `compose(funcA(funcB(funcC())))`）

**返回值**

(*Function*): 从右到左把接收到的函数合成后的最终函数。

```js
import { createStore, combineReducers, applyMiddleware, compose } from 'redux'
import thunk from 'redux-thunk'
import DevTools from './containers/DevTools'
import reducer from '../reducers/index'

const store = createStore(
  reducer,
  compose(
    applyMiddleware(thunk),
    DevTools.instrument()
  )
)
```





# TS与React

1. 减少编写冗余的类型定义、类型标注，充分利用ts的自动类型推断，以及外部提供的类型声明。
2. 类型安全：提供足够的类型信息来避免运行时错误，让错误暴露在开发期。这些类型信息同时能够提供代码补全、跳转到定义等功能。

## React.FC

`React.FC`是函数式组件，是在TypeScript使用的一个**泛型**。FC是FunctionComponent的缩写，`React.FC`可以写成`React.FunctionComponent`。这个类型定义了默认的 props(如 children)以及一些静态属性(如 defaultProps)

```
import React, { FC } from 'react';

/**
 * 声明Props类型
 */
export interface MyComponentProps {
  className?: string;
  style?: React.CSSProperties;
}

export const MyComponent: FC<MyComponentProps> = props => {
  return <div>hello react</div>;
};
```

# NEXT

https://www.nextjs.cn/

## 背景

要从头开始使用 React 构建一个完整的 Web 应用程序，需要考虑许多重要的细节：

- 必须使用打包程序（例如 webpack）打包代码，并使用 Babel 等编译器进行代码转换。
- 你需要针对生产环境进行优化，例如代码拆分。
- 你可能需要对一些页面进行预先渲染以提高页面性能和 SEO。你可能还希望使用服务器端渲染或客户端渲染。
- 你可能必须编写一些服务器端代码才能将 React 应用程序连接到数据存储。

**Next.js：React 开发框架**

- 直观的、 [基于页面](https://www.nextjs.cn/docs/basic-features/pages) 的路由系统（并支持 [动态路由](https://www.nextjs.cn/docs/routing/dynamic-routes)）
- [预渲染](https://www.nextjs.cn/docs/basic-features/pages#pre-rendering)。支持在页面级的 [静态生成](https://www.nextjs.cn/docs/basic-features/pages#static-generation-recommended) (SSG) 和 [服务器端渲染](https://www.nextjs.cn/docs/basic-features/pages#server-side-rendering) (SSR)
- 自动代码拆分，提升页面加载速度
- 具有经过优化的预取功能的 [客户端路由](https://www.nextjs.cn/docs/routing/introduction#linking-between-pages)
- [内置 CSS](https://www.nextjs.cn/docs/basic-features/built-in-css-support) 和 [Sass 的支持](https://www.nextjs.cn/docs/basic-features/built-in-css-support#sass-support)，并支持任何 [CSS-in-JS](https://www.nextjs.cn/docs/basic-features/built-in-css-support#css-in-js) 库
- 开发环境支持 [快速刷新](https://www.nextjs.cn/docs/basic-features/fast-refresh)
- 利用 Serverless Functions 及 [API 路由](https://www.nextjs.cn/docs/api-routes/introduction) 构建 API 功能
- 完全可扩展

## 创建

```
npx create-next-app nextjs-blog --use-npm --example "https://github.com/vercel/next-learn-starter/tree/master/learn-starter"
```

```
cd nextjs-blog
```

```
npm run dev
```

在浏览器中打开 [http://localhost:3000](http://localhost:3000/) 。

## 页面

### 客户端导航

在 Next.js 中，页面是从[`pages`目录中](https://www.nextjs.cn/docs/basic-features/pages)的文件导出的 React 组件。

页面与基于其**文件名**的路由相关联。例如，在开发中：

- `pages/index.js`与`/`路由相关联。
- `pages/posts/first-post.js`与`/posts/first-post`路由相关联。

**在页面之间导航**

```react
import Link from 'next/link'
```

```react
Read <Link href="/posts/first-post"><a>this page!</a></Link>
```

该[`Link`](https://www.nextjs.cn/docs/api-reference/next/link)组件支持在同一个 Next.js 应用程序中的两个页面之间进行**客户端导航**。

客户端导航意味着页面转换*使用 JavaScript 进行*，这比浏览器执行的默认导航更快。



该[`Link`](https://www.nextjs.cn/docs/api-reference/next/link)组件支持在同一个 Next.js 应用程序中的两个页面之间进行**客户端导航**。

客户端导航意味着页面转换*使用 JavaScript 进行*，这比浏览器执行的默认导航更快。

这是您可以验证的简单方法：

- 使用浏览器的开发人员工具将`background`CSS 属性更改`<html>`为`yellow`。
- 单击链接可在两个页面之间来回切换。
- 您会看到黄色背景在页面转换之间持续存在。

这表明浏览器*未*加载完整页面并且客户端导航正在工作。

<img src="https://www.nextjs.cn/static/images/learn/navigate-between-pages/client-side.gif" alt="Links" style="zoom:50%;" />

如果您使用了`<a href="…">`代替`<Link href="…">`并执行了此操作，则链接点击时背景颜色将被清除，因为浏览器会完全刷新。

### 动态路由

Next.js 支持具有动态路由的 pages（页面）。例如，如果你创建了一个命名为 `pages/posts/[id].js` 的文件，那么就可以通过 `posts/1`、`posts/2` 等类似的路径进行访问。

- `pages/blog/[slug].js` → `/blog/:slug` (`/blog/hello-world`)
- `pages/[username]/settings.js` → `/:username/settings` (`/foo/settings`)
- `pages/post/[...all].js` → `/post/*` (`/post/2020/id/title`)

### 代码拆分和预取

Next.js 会自动进行代码拆分，因此每个页面只加载该页面所需的内容。这意味着在呈现主页时，最初不会提供其他页面的代码。

这可确保即使您添加数百个页面，主页也能快速加载。

仅加载您请求的页面的代码也意味着页面变得孤立。如果某个页面抛出错误，应用程序的其余部分仍然可以工作。

此外，在 Next.js 的生产版本中，每当[`Link`](https://www.nextjs.cn/docs/api-reference/next/link)组件出现在浏览器的视口中时，Next.js 都会在后台自动**预取**链接页面的代码。当您单击链接时，目标页面的代码已在后台加载，页面转换将近乎即时！

### HTML

#### html

`<Head>`使用 代替小写字母`<head>`。`<Head>`是一个内置于 Next.js 的 React 组件。它允许您修改`<head>`页面的名称。

```
import Head from 'next/head'
```

```html
<Head>
        <meta name="viewport" content="width=device-width, initial-scale=1" />
        <meta charSet="utf-8" />
        <meta name="Description" content={props.description}></meta>
        <title>{props.title}</title>
</Head>
```

#### img

```
img统一放在public中，引用直接引用img，不需要添加图像路径
src="/head.jpg"
```

#### css

```
<style jsx>{`
  …
`}</style>
```

这是使用一个名为[styled-jsx](https://github.com/vercel/styled-jsx)的库。它是一个“CSS-in-JS”库——它允许你在 React 组件中编写 CSS，并且 CSS 样式将被*限定*（其他组件不会受到影响）。

Next.js 内置了对[styled-jsx 的](https://github.com/vercel/styled-jsx)支持，但您也可以使用其他流行的 CSS-in-JS 库。我用的是materialUI框架中的css-in-js

- 全局样式

  如果你希望**每个页面**都加载一些 CSS，添加pages/_app.js文件

  ```
  import '../styles/global.css'
  export default function App({ Component, pageProps }) {
    return <Component {...pageProps} />
  }
  ```

  创建一个顶级styles目录并global.css在里面创建。将其导入pages/_app.js

## 内置API

某些页面需要获取外部数据以进行预渲染。有两种情况，在每种情况下，您都可以使用 Next.js 提供的特殊功能：

1. 您的页面 **内容** 取决于外部数据：使用 `getStaticProps`。
2. 你的页面 **paths（路径）** 取决于外部数据：使用 `getStaticPaths` （通常还要同时使用 `getStaticProps`）。

**getStaticProps**函数在**构建时**被调用，并允许你在预渲染时将获取的数据作为 `props` 参数传递给页面。**getStaticProps不会在页面组件中生效**

Next.js 允许你创建具有 **动态路由** 的页面。例如，你可以创建一个名为 `pages/posts/[id].js` 的文件用以展示以 `id` 标识的单篇博客文章。当你访问 `posts/1` 路径时将展示 `id: 1` 的博客文章。但是，在构建 `id` 所对应的内容时可能需要从外部获取数据。**getStaticPaths**函数在构建时被调用，并允许你指定要预渲染的路径。

```jsx
// 此函数在构建时被调用
export async function getStaticPaths() {
  // 调用外部 API 获取博文列表
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // 据博文列表生成所有需要预渲染的路径
  const paths = posts.map((post) => ({
    params: { id: post.id },
  }))

  // We'll pre-render only these paths at build time.
  // { fallback: false } means other routes should 404.
  return { paths, fallback: false }
}
```

为了让页面使用服务端渲染，你需要导出 getServerSideProps 异步函数。这个函数将在**每次请求**时在服务端被调用。例如，假设你的页面需要用最新的数据预渲染（通过外部的 api 获取数据）。你应该写下 getServerSideProps 来获取数据传递给 Page。

getServerSideProps 和 getStaticProps 很像，但是区别的是，getServerSideProps 是每个请求都会调用而不是在构建时。

## mardown解析

### 插件

https://dev.to/imranib/build-a-next-js-markdown-blog-5777

- [react-markdown](https://www.npmjs.com/package/react-markdown)将帮助我们解析和渲染 Markdown 文件

- 代码格式化：`react-syntax-highlighter`包

- gray-matter](https://www.npmjs.com/package/react-markdown) 将解析我们博客的*顶部内容*。（文件顶部的部分`---` ）

  我们需要这样的元数据`title`，`data` 并`description`和`slug`。您可以在此处添加任何您喜欢的内容

| 参数        | 意义         |
| ----------- | ------------ |
| slug        | 导航的参数   |
| title       | 文章名称     |
| data        | 最新时间     |
| updated     | 文章更新日期 |
| tags        | 文章標籤     |
| category    | 文章分類     |
| description | 文章描述     |

- [raw-loader](https://www.npmjs.com/package/raw-loader)将帮助我们导入我们的markdown文件。 

### 流程

https://dev.to/imranib/build-a-next-js-markdown-blog-5777

https://thetombomb.com/posts/adding-code-snippets-to-static-markdown-in-Next%20js

## Tips

- material,classname报错，每次刷新，material失去效果。添加_app.js和__document.js文件



