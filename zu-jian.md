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

1. 同名的组件在调用时，其真实结构对应就是HTML标签，上面提到的概念相同的属性会体现在真实结构的HTML标签上，除此之外，data-\*形式的属性也可以被专递下来，其他的属性将会丢失。
2. 不同的属性分3种：
   1. 事件属性，`onclick`、`onmouseenter`等，不再被接收，取而代之的是`onClick`、`onMouseenter`等驼峰形式的属性书写，从而来实现事件触发（使用上有一点不同，就是原生事件`return false`就终止默认的HTML标签行为，比如a链接点击会跳转到href的地址，`return false`后不会跳转。统一是通过`preventDefault`方法来控制）。
   2. 所有标签的`class`和`style`属性失效，React组件通过`className`和`style`来替代。而style属性只能是CSS的json形式，比如`style={{backgroundColor: 'red'}}`，其中`backgroundColor`而不是`background-color`,规则名已驼峰形式。
   3. 表单元素的部分属性，包括value、checked、selected属性，属性不会丢失，但是使用概念却完全不同，以下表单元素段落有说明（特殊的Textarea标签的innerText也已value来代替）。

所有自定义的组件，其属性传递的值，只能在组件内部可以使用，其含义也只能在组件内部才能够了解。

```
class Custom extends Component{
    static propTypes = {
        label: Props.string.isRequired
    }
    render(){
        return (<div><span>{this.props.label}</span>{this.props.children}</div>);
    }
}
```

组件调用为`<Custom label=(<b>It said: </b>)>Hello World</Custom>` , 从调用时我们并不清楚`label`属性有何用，只有在`Custom`的代码中才知道是作为`span`标签的内容输出。

关于组件属性props的调用，前篇[简介](/简介.md)已有介绍，这里再强调下：



### 组件状态与生命周期

### 条件与表达式

### 组件遍历与标志位

### 表单元素

### 组件通信

### 组件实例引用与真实DOM节点

### 组件注入服务



