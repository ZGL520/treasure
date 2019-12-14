1.this.props
-
react中this.props对象的属性和组件的属性一 一对应,this.props.children除外,this.props.children表示组件的所有子节点.

this.props.children的值有三种可能性,如果不存在就是undefined,如果有一个就是object,如果有多个就是arra.

2.dom diff
-
组件并不是真实的dom节点,而是存储在内存中的数据结构,叫做虚拟dom,只有当他插入文档之后才变成真实的dom,在react的设计中,所有的dom变动都是先在虚拟dom上发生的,然后再将实际发生变动的部分反映到真实dom上,这种算法叫做,dom diff,它可以极大提高网页的性能表现.但是有时需要从组件获取真实的dom节点,这可以用ref属性来实现.

3.表单
-
文本输入框的值，不能用 this.props.value 读取，而要定义一个 onChange 事件的回调函数，通过 event.target.value 读取用户输入的值。textarea 元素、select元素、radio元素都属于这种情况

4.react设置样式

	style={{opacity: this.state.opacity}}
	//React 组件样式是一个对象，所以第一重大括号表示这是 JavaScript 语法，第二重大括号表示样式对象。