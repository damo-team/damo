# 组件

简介章节介绍了React组件从简单到复杂的组件的开发，以及组件之间的嵌套使用，组件通信（依靠父组件的state来维护组件之前的数据传递）问题。

### 组件模板与属性

组件模板即JSX，编写JSX需要注意几点：

1. JSX组件和HTML标签很相似，使用有2中方式：`<Custom />` 和 `<Custom>...</Custom>` 组件属性值传入同HTML标签属性一样`key=value` 格式。
2. 组件模板JSX必须且只能包含在一个容器组件下（这不是绝对的，下面会有一种特殊形式），通过以下代码区分下：
   1. wrong: `const Custom = (<h1>Hello</h1><span>World</span>)`;
   2. right: `const Custom = (<div><h1>Hello</h1><span>World</span></div>)`;
3. 几乎所有的HTML标签在JSX中都有对应的内置的组件，比如`h1` 、`span` 等。但是有部分特性属性含义不同，后续篇章有说明。
4. 属性值和组件内容都可以使用{}表达式，看一下3中形式：
   1. `const Custom = (<h1>{'Hello World'}</h1>)`; `h1`的内容是通过{}输出的`Hello World`
   2. `const Custom = (<h1 title={1}/>);` `h1`的属性`title`传值为`1`
   3. `const Custom = (<h1 content={<span>Hello World!</span>}/>` `h1`的属性`content`传了一个`span`组件。

> `{xxx}`填写的表达式只能为一个，结束不能包含`;`, 表达式的返回结果必须为JS原始类型（`null`, `undefined`, `string`, `number`, `boolean`）或者 React组件实例。  
> 有一种特殊情况，表达式可以返回数组，在 遍历与标识位 段落有详细说明。

对于HTML标签对应的React组件，其特性属性，比如`title`、`width`等属性可以被组件正常使用，基本和HTML标签展示结果无异。但是仍有部分特殊的属性和HTML的概念不同。

总结起来分为3类：

1. 同名的组件在调用时，其真实结构对应就是HTML标签，上面提到的概念相同的属性会体现在真实结构的HTML标签上，除此之外，`data-*`形式的属性也可以被专递下来，其他的属性将会丢失。
2. 不同的属性分3种：
   1. 事件属性，`onclick`、`onmouseenter`等，不再被接收，取而代之的是`onClick`、`onMouseenter`等驼峰形式的属性书写，从而来实现事件触发（使用上有一点不同，就是原生事件`return false`就终止默认的HTML标签行为，比如a链接点击会跳转到href的地址，`return false`后不会跳转。统一是通过`preventDefault`方法来控制）。
   2. 所有标签的`class`和`style`属性失效，React组件使用`className`和`style`来替代（json的数据格式）。
   3. 表单元素的部分属性，包括`value`、`checked`、`selected`属性，属性不会丢失，但是使用概念却完全不同，以下表单元素段落有说明（特殊的Textarea标签的innerText也已value来代替）。

所有自定义的组件，其属性传递的值，只能在组件内部可以使用，其含义也只能在组件内部才能够了解。

```
class Custom extends Component{
    static propTypes = {
        label: Props.string.isRequired
    }
    static defaultProps = {
        label: 'nobody:'
    }
    render(){
        return (<div><span>{this.props.label}</span>{this.props.children}</div>);
    }
}
```

组件调用为`<Custom label=(<b>It said: </b>)>Hello World</Custom>` , 从调用时我们并不清楚`label`属性有何用，只有在`Custom`的代码中才知道是作为`span`标签的内容输出。

关于组件属性props的调用，前篇[简介](/简介.md)已有介绍，这里再强调下：

1. `this.props`可访问到所有传入的组件属性和值。
2. 静态属性`propsTypes`是声明对传入的属性，需要做类型检查，符合类型的属性值将抛错。
3. 静态属性`defaultProps`是设置当不传入对应属性时，可以使用默认值来注入到`this.props`。
4. `this.props.children`是特殊属性值，这是组件内容的引用，上面`this.props.children`指向的是"Hello World"

### 组件状态与生命周期

`<Custom/>`组件调用返回组件实例，这其中经历了哪些过程，一起来看：

![](/assets/component-1.png)

则是比较直观的步骤，有2个点概念要掌握：

1. `props`属性对组件来说只读的，在组件内部改动不会影响到外部，React不建议直接改动`props`。
2. `state`状态，React提供的一种用渲染需要的可改变的数值，这样和props配合一起来支持组件模板的渲染。
3. 当`props`（父级传入新的值）或者`state`发生变更时，组件模板会重新的渲染。挂载到父级DOM的HTML结构随之会更新。

完整的组件渲染步骤![](/assets/component-3.png)一般来说组件state用来维护子组件通信的状态数据以及组件自身交互逻辑更新的状态。

1. state创建有2种方式
   1. 当组件是以`class`类来创建，`state`可以是`class`的属性值，也可以在初始化函数通过`this.state`赋值来创建。
   2. 当组件是以函数来创建，`state`需要通过函数的`getIntialState`函数的返回值来创建。
2. 更新`state`必须通过`this.setState`方法来更新，更新时新旧`state`会做合并，产生新的`state`。
3. `setState`并不会立即生效，它是异步的，只有触发了钩子函数处理完重新渲染组件时，你才能拿到最新改变的值。

组件从初始化到渲染到组件销毁的过程，我们称之为组件生命周期，过程中每个步骤都会触发钩子函数，钩子函数代表每个步骤处理的结果。

把钩子函数标注出来，组件的渲染图步骤![](/assets/component-4.png)React组件的钩子函数

1. `componentWillReceiveProps(nextProps)` 即将接受新改变的props值，props的值需要转变为state的过程可以此进行。
2. `shouldComponentUpdate(nextProps, nextState)` 新props和新state变更完成后，是否要更新组件渲染，`return false`表示不更新
3. `componentWillUpdate(nextProps, nextState)` 组件即将调用`render`方法更新组件。
4. `componentDidUpdate(prevProps, prevState)` 组件`render`调用完成后触发。
5. 以上钩子函数是会重复触发，有5个钩子函数只会触发一次
   1. `constructor(props, context)` 组件初始化，`state`创建一般在这里进行。
   2. `componentWillMoount()` 组件第一次渲染完成后，需要挂载到父级的真实DOM结构下。
   3. `componentDidMount()` 组件挂载成功后触发
   4. `componentWillUnmount` 组件销毁之前需要从父级DOM断开。
   5. `destroy` 组件销毁后触发。

> 组件销毁是因为父组件被销毁 或者 父组件的模板中移除了该组件。

### 遍历与标志位key

这里会着重介绍表达式的用法，JSX模板已`{}`来嵌入表达式语句，其表达式返回结果只能为JS原始类型或者React组件实例

试想下，表达式可以返回React组件实例，表达式如何执行并不做限定，以下代码能正常运行。

```
const ComA = (<h1>Hello World</h1>);
const ComB = (<h6>Hello World</h6>);
const count = 1;
const Custom = (<div>{count > 1? <ComA/> : <ComB/>}</div>);
```

> 三元判断是表达式，if...else...条件语句不是表达式， for...也不是表达式。

一种特殊的情况是，表达式可以返回数组，如果数组项的值为JS原始类型仍可以正常执行，但是数据项为React组件实例时则会出现一些状况（每次模板更新时，组件实例都会重新的生成），这是因为JSX模板没法跟踪组件实例，除非组件带有唯一的标志位key，这样模板更新时React能够识别组件实例是要重新创建还是只做更新。

```
const Li = function(props){ return (<li>{this.props.text}</li>); }

const Custom = (<ul>{
                [Li, Li].map(function(Item, index){
                  return <Item key={index} text={'count: ' + index} />;
                })
                }</ul>);
```

1. 数组中组件实例必须带有key属性，其key值必须为数组内唯一，即2个`Item`的key不能相同。
2. key是在组件调用时传入，而不是组件内部，即如果只在`Li`的JSX模板中写key是没用的。

### 表单元素

React内置了丰富的组件，基本覆盖了HTML原生标签，包括表单元素组件，为了这些组件能按照React的机制来运行，表单元素组件的属性赋有额外的含义。

1. `placeholder`属性正常使用
2. `value`属性, 表单元素的值通过`value`属性赋值。`textarea`元素的输入值也通过value属性赋值。
3. `disabled`、`selected`、`checked`属性需要赋值true和false来激活。
4. `onChange`事件，取代了`onchange`原生事件，通过`event.target.value`可获取到变更的值。
5. 其他
   1. 没有`readonly`属性，根据`value`属性值做判断，初始化时`value`值不为`undefined`或`null`时表示组件为只读的，组件从只读变为可读写只需要把`value`值改为`undefined`或`null`
   2. 新增`defaultValue`属性，即想组件初始化时有value值，同时组件也可读写，则通过`defaultValue`赋值来实现，此时`value`值不传值。

### 组件通信

组件通信是指同级组件之间通信和父子组件的通信，在React中组件通信都属于父子组件的通信，为什么这么讲:

1. React组件的props是只读的，组件内无法改变外部的数据。但是可以通知父组件来改，然后再拿到新的props.
2. React组件的state可以维护组件内可变的数据，子组件可变的props通常是存在父组件的state中。

那么接下来我们关注的问题是如何管理父组件的state，来达到控制子组件通信的效果。

```
class Custom extends Component{
    constructor(props){
        super(props);
        this.state = {
            color: 'red'
        }
    }
    render(){
        return (<div>
            <input value={this.state.color} onChange={(e) => this.setState({color: e.target.value})} />
            <p style={{color: this.state.color}}>Hello World</p>
        </div>);
    }
}
```

组件展示了一个输入框和一个段落 2个组件，输入颜色值，段落的文本就编程对应的颜色，默认是红色。

1. 应用了上面提到的表单元素和style属性。
2. 2个组件交互的状态数据，存放在父级组件的state，在初始化时创建state，当变更时通过setState方法来变更。

### 组件真实实例与真实DOM节点

React组件调用返回组件实例，通过console.log把组件实例打印出来，实际上只是一个描述组件接收值的一个对象，并不是组件new出来的实例

在一些特殊的逻辑中，我们需要获取到组件new出来的实例，从而绕过父子组件通信的约束，直接操作组件new出来的实例，那么我们需要在组件调用时传入ref属性，值为function类型，ref触发时会调用该函数，并传入组件new的实例。

```
let h1_inst;
const h1 = (<h1 ref={inst => h1_inst = inst}>test</h1>);
```

以上代码写法没问题，但是并不能获取到h1的实例。因为只有自定义组件ref属性才有效，同时自定义组件必须要通过class来定义。

按照提示我们改下代码

```
let h1_inst;
class H1 extends Component{
    render(){
        return (<h1>test</h1>);
    }
}
const h1 = (<H1 ref={inst => h1_inst = inst}/>);
```

H1是自定义组件，在调用时传入ref属性。当组件调用成功后（即渲染完模板且挂在到DOM上）h1\_inst就拿到了H1 new的实例。

`console.log(h1_inst.props)`可以打印出H1组件传入的props属性。所以说ref可以获取真实的组件实例。

组件调用成功后，组件在HTML文档表现的真实DOM结构，如果非要获取的话，通过`ReactDOM.findDOMNode(组件真实实例)`可返回真实的DOM结构。

### 组件注入服务

React组件有一个静态属性context，很少有人用到，React官方也不推荐使用。

Damo框架中却推荐大家使用context，动机是因为：

1. React本身只是组件框架，不涉及到提供组件跨容器甚至跨页面之间的通信，此时按照父子组件通信的思路不能解决问题（因为shu与不同的父组件），React官方推荐使用社区的Redux状态管理类库来配合使用，无疑是把构建应用复杂度交给了用户做决定（好坏只能看开发者的掌握程度而定）。
2. Damo专注于解决构建大型应用，组件通信，组件和接口通信等复杂数据状态的管理和使用的问题。同时也会提供优秀案例的前端解决方案，这些基本都属于独立解决意见特定事情的class类，最终在应用中作为单例运行，方便所有页面可以共享特性。所以Damo推崇服务概念的使用。
3. 但是本身Damo依赖于React组件。服务要植入到组件中，需要借助于React的context来实现（哪天React组件去除了context，wo们仍有办法往组件中植入服务）。

最后看一个context的例子，更深入的了解服务查看 [服务](/fu-wu.md) 章节。

```
class Custom extends Component{
     static contextTypes = {
          shareObj: PropTypes.object.isRequired
     }
     return (){
          return (<h1>{this.context.shareObj.title}</h1>);
     }   
}

class Parent extends Component{
     getChildContext(){
          return {
               shareObj: {
                    title: 'hello world!'
               }
          }
     }
     render (){
          return (<Custom />);
     }
}

ReactDOM.render(<Parent/>, document.body)
```

解释一下实现过程

1. 子组件使用的服务context，只能由父级组件提供，父级组件通过`getChildContext`方法返回创建好的服务实例。
2. 子组件必须声明`contextTypes`依赖哪个服务，在运行时，父级的对应服务实例才会注入进来。
3. `this.context`来访问服务实例。



