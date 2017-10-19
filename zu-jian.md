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

> 三元判断是表达式，if...else...条件语句不是表达式。

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

### 组件通信

### 组件实例引用与真实DOM节点

### 组件注入服务



