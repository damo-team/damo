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

1. props属性对组件来说只读的，在组件内部改动不会影响到外部，React不建议直接改动props。
2. state状态，React提供的一种用渲染需要的可改变的数值，这样和props配合一起来支持组件模板的渲染。
3. 当props（父级传入新的值）或者state发生变更时，组件模板会重新的渲染。挂载到父级DOM的HTML结构随之会更新。

完整的组件渲染步骤![](/assets/component-3.png)一般来说组件state用来维护子组件通信的状态数据以及组件自身交互逻辑更新的状态。

1. state创建有2种方式
   1. 当组件是以class类来创建，state可以是class的属性值，也可以在初始化函数通过this.state赋值来创建。
   2. 当组件是以函数来创建，state需要通过函数的getIntialState函数的返回值来创建。
2. 更新state必须通过this.setState方法来更新，更新时新旧state会做合并，产生新的state。
3. setState并不会立即生效，它是异步的，只有触发了钩子函数处理完重新渲染组件时，你才能拿到最新改变的值。

组件从初始化到渲染到组件销毁的过程，我们称之为组件生命周期，过程中每个步骤都会触发钩子函数，钩子函数代表每个步骤处理的结果。

把钩子函数标注出来，组件的渲染图步骤![](/assets/component-4.png)

### 条件与表达式

### 组件遍历与标志位

### 表单元素

### 组件通信

### 组件实例引用与真实DOM节点

### 组件注入服务



