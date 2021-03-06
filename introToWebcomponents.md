# Intro to web components

## 什么是 Web components
Web components是一系列由web平台提供的api，通过这些api你能够创建自定义，可重用，可封装的HTML标签来应用到网页中。 

Web components由4大部分组成：

- Custom Elements
- Shadow DOM
- HTML imports
- HTML Template

这四部分有机地组合在一起，才是 Web Components。

Web components目前还处于草案阶段，这里我们基于最新的版本来对其做一个简单的解读：

## Custom Elements
Custom Elements顾名思义就是自定义元素，这是web components的基础。在web components没有出现之前，浏览器是无法识别我们自定义的web元素的。在新的标准里面，我们可以有两种方式来创建自定义的元素：

### 自定义元素(autonomous custom element)

示例：
	
	class AutonomousButton extends HTMLElement {
          // 创建或升级元素的一个实例。用于初始化状态、设置事件侦听器或创建 Shadow DOM。
          constructor() {};
          // 元素每次插入到 DOM 时都会调用。用于运行安装代码，例如获取资源或渲染。一般来说，您应将工作延迟至合适时机执行。
          connectedCallback() {};
          // 元素每次从 DOM 中移除时都会调用。用于运行清理代码（例如移除事件侦听器等）。
          disconnectedCallback() {};
          // 自定义元素被移入新的 document（例如，有人调用了 document.adoptNode(el)）。
          adoptedCallback() {};
          // 属性添加、移除、更新或替换。解析器创建元素时，或者升级时，也会调用它来获取初始值。
          // 注：仅 observedAttributes 属性中列出的特性才会收到此回调。
          attributeChangedCallback(attr, oldVal, newVal) {};
	}
	customElements.define("autonomous-button", AutonomousButton);
	
如何使用：

	<autonomous-button>Click Me :)</autonomous-button>


这里我们创建了`AutonomousButton `, 继承自`HTMLElement`。

在`AutonomousButton `里面提供了一些了的生命周期方法：

- connectedCallback
- disconnectedCallback
- adoptedCallback
- attributeChangedCallback

可以利用这些方法来实现组件的具体逻辑。


### 扩展原生HTML元素(customized built-in element)

自定义内置元素是用于扩展某个浏览器内置 HTML 标记的自定义元素。 扩展现有元素的主要好处是能获得其所有功能（DOM 属性、方法）。

示例：

	// See https://html.spec.whatwg.org/multipage/indices.html#element-interfaces for the list of other DOM interfaces.
	class CustomizedButton extends HTMLButtonElement {
	  ...
	}
	customElements.define("customized-button", CustomizedButton, { extends: "button" });
	
如何使用：

	<button is="customized-button">Click Me :)</button>

这里我们创建了`CustomizedButton `, 继承自`HTMLButtonElement`。 在声明时注意最后一个参数`{ extends: "button" }`。在使用是通过`is`属性绑定到`button`标签上。

通过这种方，我们可以扩展任意的原生html标签，比如：`HTMLDivElement`, `HTMLFormElement`, `HTMLLinkElement` ..


## Shadow DOM
所谓Shadow DOM指的是，浏览器将模板、样式表、属性、JavaScript代码等，封装成一个独立的DOM元素。外部的设置无法影响到其内部，而内部的设置也不会影响到外部. **Shadow DOM 修复了 CSS 和 DOM**。它在网络平台中引入**作用域样式**。

Shadow DOM可为网络开发中的常见问题提供解决方案:

- **隔离 DOM**: 组件的 DOM 是独立的（例如，document.querySelector() 不会返回组件 shadow DOM 中的节点）。
- **作用域 CSS**：shadow DOM 内部定义的 CSS 在其作用域内。样式规则不会泄漏，页面样式也不会渗入。
- **简化 CSS**: 作用域 DOM 意味着您可以使用简单的 CSS 选择器，更通用的 id/类名称，而无需担心命名冲突。

Shadow DOM需要挂载到一个宿主元素上，使用方式如下：
	
	const header = document.createElement('header');
	const shadowRoot = header.attachShadow({mode: 'open'});
	shadowRoot.innerHTML = '<h1>Hello Shadow DOM</h1>';

Shadow DOM另外还提供两个特性，分别是：

	
### Sytling
在shadow tree里面的声明的样式不会影响到shadow tree外面的元素。反之，shadow tree外面的css选择器也不会影响到shadow里面。

### slot

slot 提供了在使用自定义标签的时候可以传递子模板给到内部使用的能力.

直接看例子：

我们在`<my-header>`里面的节点树如下：

	<header>
	   <h1><slot name="header"></slot></h1>
	   <h1><slot name="sub-header"></slot></h1>
	   <button>Menu</button>
	</header>
	
在页面上使用方式如下：

	<my-header>
	    <span slot="header">This is header</span>		
	    <span slot="sub-header">This is header</span>
	</my-header>	
	
最终浏览器解析结果如下：

	<my-header>
	  <header>
	     <h1>This is header</h1>
	     <h2>This is header</h2>
	     <button>Menu</button>
	  </header>
	</my-header>

##  HTML imports
HTML Import用于将外部的HTML文档加载进当前文档。我们可以将组件的HTML、CSS、JavaScript封装在一个文件里，然后使用下面的代码插入需要使用该组件的网页。

	<link rel="import" href="../emoji-rain/emoji-rain.html">
	...
	<emoji-rain active></emoji-rain>
	
## HTML Template
声明一个template

	<template id="mytemplate">
	  <img src="" alt="great image">
	  <div class="comment"></div>
	</template>

使用这个template

	var t = document.querySelector('#mytemplate');
	// Populate the src at runtime.
	t.content.querySelector('img').src = 'logo.png';

	var clone = document.importNode(t.content, true);
	document.body.appendChild(clone);

简单的说，就是在`template`里面的代码在dom渲染时不会执行，需要我们自己通过js去后去，然后插入到dom中。

## 有哪些库可以用来构建Web components
### Polymer 
[Polymer](https://www.polymer-project.org/)是由**Google出品**，提供基于最新的web components api标准来创建web components的轻量库。
### X-Tag
> [X-Tag](https://x-tag.github.io/) is a **Microsoft supported**, open source, JavaScript library that wraps the W3C standard Web Components family of APIs to provide a compact, feature-rich interface for rapid component development.

## 参考资料

### 中文
[http://blog.csdn.net/fengyinchao/article/details/52382913](http://blog.csdn.net/fengyinchao/article/details/52382913)

[http://javascript.ruanyifeng.com/htmlapi/webcomponents.html](http://javascript.ruanyifeng.com/htmlapi/webcomponents.html)

[http://mobile.51cto.com/web-440551.htm](http://mobile.51cto.com/web-440551.htm)

[https://developers.google.com/web/fundamentals/web-components/customelements?hl=zh-cn](https://developers.google.com/web/fundamentals/web-components/customelements?hl=zh-cn)
### 英文
[https://www.webcomponents.org/](https://www.webcomponents.org/)


## Demo Examples

[https://developers.google.com/web/fundamentals/web-components/examples/howto-checkbox?hl=zh-cn](https://developers.google.com/web/fundamentals/web-components/examples/howto-checkbox?hl=zh-cn)

[https://github.com/mdn/web-components-examples](https://github.com/mdn/web-components-examples)


