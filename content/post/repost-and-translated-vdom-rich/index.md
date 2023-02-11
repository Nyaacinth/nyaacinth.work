# 虚拟 DOM 是完全没必要的开销

*来将“虚拟 DOM 很快”这一神话拉回凡间吧*

by [RICH HARRIS](https://twitter.com/Rich_Harris) - 2018 年 12 月 27 日

----

​		如果你近几年使用过 JavaScript 框架，你很可能听过这样的一句话——“虚拟 DOM 很快”，它常常意味着“虚拟 DOM 比*真实* DOM 更快”。这是一个令人惊讶的、关于它有多么强大的误解，比如有的人就会问：“为什么 Svelte 不使用虚拟 DOM 的同时还能如此之快？”

​		看来，是时候仔细研究研究了。

### 什么是“虚拟 DOM”

​		在许多框架中，要构建一个 App 你通常需要创建 `render()` 函数，就像这个简单的 [React](https://reactjs.org/) 组件：

```jsx
function HelloMessage(props) {
    return (
        <div className="greeting">
            Hello {props.name}
        </div>
    );
}
```

当然，你也可以不使用 JSX：

```jsx
function HelloMessage(props) {
    return React.createElement(
        'div',
        { className: 'greeting' },
        'Hello ',
        props.name
    );
}
```

​		但结果是一样的：一个对象，它表明了这个页面应有的外观。这个对象就是所谓的“虚拟 DOM”，一旦你的 App 有了状态更新（例如收到的 `props` 中 `name` 字段有变化），你就会创建一个新的虚拟 DOM。框架的任务就是将这个新的虚拟 DOM 与旧的*比较*，来弄清楚哪些变更是必要的，然后将它们应用到真实 DOM。

### 那么，这种误解从何而来？

​		要弄清对虚拟 DOM 性能的误解从何而来，我们就要把时间拨回到 React 项目启动的那一年。在 [重新思考最佳实践（Rethinking Best Practices）](https://www.youtube.com/watch?v=x7cQ3mrcKaY) 这一个 2013 年的开创性的演讲中（由 React 核心团队的成员 Pete Hunt 带来），我们可以得知如下事项：

> 这实际上非常的快，主要是因为多数 DOM 操作太慢。有大量耗费性能的任务被放在 DOM 中，但如此 DOM 操作就带来了掉帧。

![rethinking-best-practices.jpg](./rethinking-best-practices.jpg)

*截图来自 [Rethinking Best Practices](https://www.youtube.com/watch?v=x7cQ3mrcKaY)在 JSConfEU 2013*

​		但请稍等！虚拟 DOM 操作是被附加在最终的真实 DOM 操作之上的。这意味着如果它要更快，唯一的方法就是把它与那些更加低效的框架做对比（这样的框架在 2013 年非常之多），或者与一个假靶子对比——做一些实际上没人会做的事：

```js
onEveryStateChange(() => {
    document.body.innerHTML = renderMyApp();
});
```

而 Pete 很快澄清道…

> React 不是魔法。就像你可以把汇编和 C 联用，然后打败 C 编译器。如果你想的话，你也可以使用原始 DOM 操作和 API 调用，然后打败 React。但无论如何，向下使用 C、Java 或 JavaScript 能带来数量级的性能提升，因为你不需要去担心... 关于平台特殊性的那些事。而使用 React，你就可以只关注构建应用程序本身，而不需要担心性能问题，默认状态已经很快了。

…但那不是真正需要澄清的部分。

### 所以… 虚拟 DOM 很慢吗？

​		这种说法也并不准确。它其实更像是“虚拟 DOM 通常足够快”，但是有一些坑。

​		React 最初的承诺是你可以在每一个细小的状态改变时重新渲染你的整个 App 而无需担心性能。而在实践中，我并不认为它准确到可被检验。如果它确实是，那就没有必要做类似 `shouldComponentUpdate` 这样的优化了（这是一种告知 React 它什么时候可以安全地跳过一个组件的更新的方法）

​		即便我们有了 `shouldComponentUpdate`，一次性更新你整个 App 的虚拟 DOM 也需要大量的工作。不久前，React 团队带来了一个东西，它叫做 React Fiber，这使得更新可以被切分成许多小块。意味着（在其它东西里）更新不会长时间阻塞主线程（但并没有降低更新的工作量或耗时）。

### 那开销是从哪来的？

​		很明显，[比较不是无成本的（diffing isn't free）](https://twitter.com/pcwalton/status/1015694528857047040)。你不能在新的虚拟 DOM 与上一个快照的比较没做时就将变化应用到真实 DOM 中。以前面的 `HelloMessage` 为例，假设 `props` 的 `name` 字段从 `"world"` 变成了 `"everybody"`。

1. 两个快照都只包含一个元素，都是 `<div>`，这意味着我们可以保留相同的 DOM 节点
2. 我们遍历了旧的 `<div>` 和新的 `<div>` 上的所有属性，来了解是否需要有改变、更新或删除的动作。我们可以得知，两者都只包含一个属性 `className`，值为 `"greeting"`
3. 接着看元素，我们可以得知文本有了变化，所以我们需要更新真实 DOM

在这个案例的三个步骤中，只有第三个步骤是有价值的，因为：正如绝大多数更新的情况，App 的基本结构没有发生改变。所以更有效率的做法是直接跳到第三步：

```js
if (changed.name) {
    text.data = name;
}
```

（这基本就是 Svelte 生成的更新代码。不像传统的 UI 框架，Svelte 是一个在*编译时*就知悉你 App 中这些东西将如何改变的编译器，不会等到运行时才做这些事）

### 不过，这也不只是比较的问题

​		React 和其他虚拟 DOM 框架做比较的逻辑确实很快。可以说更大的开销来自这些组件本身。你当然不会写像这样的代码…

```jsx
function StrawManComponent(props) {
    const value = expensivelyCalculateValue(props.foo);

    return (
        <p>the value is {value}</p>
    );
}
```

…因为你会在每次更新时不小心重新计算 `value`，而不管 `props.foo` 有没有改变。但非常常见地，人们会做不必要的计算和分配，这种方式显得更为良性：

```jsx
function MoreRealisticComponent(props) {
    const [selected, setSelected] = useState(null);

    return (
        <div>
            <p>Selected {selected ? selected.name : 'nothing'}</p>

            <ul>
                {props.items.map(item =>
                    <li>
                        <button onClick={() => setSelected(item)}>
                            {item.name}
                        </button>
                    </li>
                )}
            </ul>
        </div>
    );
}
```

这里，我们生成了一个承载那些虚拟 `<li>` 组件的新数组——其中每一项都有它们自己的内联事件处理器——在每一次状态变更时，而不管 `props.items` 有没有改变。除非你对性能有着非一般的迷恋，否则一般来说你不会优化它们。这没有意义，它已经足够快了。但你知道怎样会更快么？就是*不这么干*。

这种“默认做一些不必要的工作，即使这些工作微不足道”的危险在于你的应用程序会屈从于这种形式的“千刀万剐”，而到了优化的时候，却没有明确的瓶颈可供瞄准。

Svelte 是明确设计了的，来防止你最终陷入这种情况的框架。

### 然后，为什么框架们还在用着虚拟 DOM 呢？

你要明白很重要的一点，虚拟 DOM 并不是一个功能或特性，它是达到这种声明式的、状态驱动的 UI 开发的目的的一种手段。虚拟 DOM 很有价值，因为它使你在编写程序时不需要思考状态转变，而且能够得到一个*通常足够好*的性能。这意味着更少的 bug 代码，并且我们能够将更多的时间花在创造性，而不是单调乏味的工作上。

但事实证明，我们可以在不使用虚拟 DOM 的情况下实现类似的编程模式——这就是 Svelte 的意义。

