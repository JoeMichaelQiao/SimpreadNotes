> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/fengqiuzhihua/article/details/120741632?spm=1000.2115.3001.5927)

自从 2010 年第一份工作接触了前后端半分离的开发方式之后，在后面的这些年里，对前端的组件化开发有了更全面一点的认识，组件化在我们的前端开发中，对提高开发效率、代码的可维护性和可复用性有很大帮助，甚至对跟设计师沟通的效率和企业的品牌形象都有着深刻的影响。这篇文章就把我在开发中总结的一些组件化开发经验分享一下。示例中的所有代码都是伪代码，你可以按照实际情况应用到 React 或 Vue 的项目中。

前端组件化发展历史
---------

在讨论组件化开发之前，我们先看看前端组件化开发的发展历史。网页开发刚起步时，并没有『前端』这个概念，更不用提组件化了。当时，网页只是作为可以在浏览器中浏览的富文本文件，开发网页就是使用一些标签让浏览器解析并显示。受制于 HTML 只是描述式的语言，网页中的代码没有办法复用，即使是类似的页面，都需要复制粘贴大量的重复代码：

```
<!-- index.html -->
<nav>
  <!-- 导航 -->
</nav>
<main>
  <!-- 内容 -->
</main>
<footer>
  <!-- 页脚 -->
</footer>

<!-- blogPost.html -->
<nav>
  <!-- 相同的导航 -->
</nav>
<main>
  <!-- 不同的内容 -->
</main>
<footer>
  <!-- 相同的页脚 -->
</footer>

```

后来随着模板引擎的出现，可以把网页的代码分割成片段（Fragments）或模板（Templates），例如导航，内容，页脚等等，之后在需要的地方，使用引入（Include）语法，把这些片段引入进来，从而避免重复代码，这样形成了组件化的雏形，常见的动态脚本语言都有自己的模板引擎，例如 PHP、ASP 和 JSP：

```
<!-- nav.jsp -->
<nav>
  <!-- 导航 -->
</nav>

<!-- index.jsp -->
<jsp:include page="nav.jsp" />

```

只是这些片段在数据管理方面仍然会比较麻烦，还是需要使用客户端 JavaScript 脚本，手动控制数据和 HTML 视图的同步。而对于样式，也还是存在全局污染的问题。  
再后来，有些高级的开发语言，例如 Java， 推出了基于服务器的组件化开发技术，例如 JSF （JSP 的后继），基于 MVC 设计模式，通过 Java Class (POJO) 定义数据模型（Model），为 JSF 页面模板提供数据。JSF 的数据模型中有事件和生命周期相关的方法，而模板和数据模型通信的方式是 Ajax，通过使用 JSF facelets 组件的方式，可以直接发送 Ajax 请求，调用模型中的方法：

```
<!-- index.xhtml -->
<html
  xmlns="http://www.w3.org/1999/xhtml"
  xmlns:h="http://xmlns.jcp.org/jsf/html"
  xmlns:f="http://xmlns.jcp.org/jsf/core"
>
  <h:commandButton id="submit" value="Submit">
    <f:ajax event="click" />
  </h:commandButton>
  <h:outputText id="result" value="#{userNumberBean.response}" />
</html>

```

```
// UserNumberBean.java
@Named
@RequestScoped
public class UserNumberBean implements Serializable {
	/* 其它代码省略 */

    public String getResponse() {
        if ((userNumber != null)
                && (userNumber.compareTo(dukesNumberBean.getRandomInt()) == 0)) {
            return "Yay! You got it!";
        }
        if (userNumber == null) {
            return null;
        } else {
            return "Sorry, " + userNumber + " is incorrect.";
        }
    }
}


```

代码来源：[Jarkarta EE 官方示例](https://github.com/eclipse-ee4j/jakartaee-tutorial-examples/tree/master/web/jsf/ajaxguessnumber)  
不过这种方式严格限定了编程语言，例如要用 JSF 技术就必须要使用 Java，并且样式污染的问题和客户端数据管理的问题仍然没有解决。  
随着 Node.js 的普及，JavaScript 可以脱离浏览器运行了，并且带来了 npm 包管理器，基于 npm 庞大的工具链，可以 Node.js 的代码打包成浏览器中能够运行的代码。而这期间，产生了像 React、Vue 和 Angular 这样的、适合大型前端项目开发的库和框架。

![](https://img-blog.csdnimg.cn/29dd6b9f21d6406682bcd43028bab99f.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bOw5Y2O5YmN56uv5bel56iL5biI,size_20,color_FFFFFF,t_70,g_se,x_16)

React 和 Vue 目前的流行程度比较高一些，它们都是纯客户端的、组件化的 JS 库，不依赖任何后端编程语言，让程序员都能够快速上手。不过，随着项目规模的扩大，如何利用好这些库进行组件化开发，成了前端工程师必须要掌握的课题。

什么是组件
-----

那么到底什么是组件呢？你想一下日常生活中，电脑上的硬件，例如显卡、内存、CPU，或者汽车的零部件：轮胎、发动机、方向盘等，这些都属于组件，它们组合起来能形成一个完整可用的产品，对于遵循了设计规范的组件，只要型号匹配，无论品牌、样式，都可以互相兼容。  
我们前端工程师其实就当于是一个工厂，我们需要按照一定的规范，产生出合格的网页组件，利用它们组装成完整的页面。组件一般包含 HTML 模板、CSS 样式和 JavaScript 数据逻辑，自成一体，可以直接在其它组件中使用，组件本身的模板、样式和数据不会影响到其它组件。组件还包含一系列可配置的属性，动态的产生内容。

> 常见的基础组件有按钮、导航、提示框、表单输入控件、对话框、表格、列表等，我们在它们的基础上又能组合出更复杂的组件，那么对于前端中的组件的定义就非常广泛了，小到一个按钮，大到一个页面都可以形成一个组件，例如两个相似的页面，可以复用一个页面组件，只需要通过修改组件的属性，来形成一个新的页面。

![](https://img-blog.csdnimg.cn/af98160c8e8d4230a32af9407c81ab5d.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bOw5Y2O5YmN56uv5bel56iL5biI,size_16,color_FFFFFF,t_70,g_se,x_16)

为什么要组件化开发
---------

你可能也知道，React 和 Vue 其实也可以完全按照传统的网页开发方式进行开发，最多是把网页大体分成几个部分，放到单独的几个文件里就好了，对于简单的网站来说没什么问题，但是如果开发的是大型的应用，例如网页版的 QQ 音乐、微信、邮箱等，它们有大量的、细小的组件，并且重复出现，这时如果针对每个页面编写 HTML 和样式，那么就会造成太多冗余代码了。

```
<!-- playlist.html -->
<div class="card">
  <div class="card__title"></div>
  <div class="card_content"></div>
</div>

<!-- homepage.html -->
<div class="card">
  <div class="card__title"></div>
  <div class="card_content"></div>
</div>

<!-- user.html -->
<div class="card">
  <div class="card__title"></div>
  <div class="card_content"></div>
</div>

```

如果使用组件化的方式，我们可以把重复出现的页面内容定义成组件，这样就不用复制粘贴同样的代码了，减少代码的体积：

```
<!-- 伪组件 -->
<!-- Card.comp -->
<div class="card">
  <div class="card__title"></div>
  <div class="card_content"></div>
</div>

<!-- playlist.html -->
<Card />

<!-- homepage.html -->
<Card />

<!-- user.html -->
<Card />

```

当某个页面内容是复合组件时，那么可以直接把基础组件直接拿过来应用：

```
<Card>
  <Title></Title>
  <Button></Button>
</Card>

```

利用这种方式，甚至可以达到 1 分钟产生一个新页面的恐怖速度，这对于你来说，节省时间去摸鱼，岂不美哉？

```
<Page title="页面1" data="{...}" /> <Page title="页面2" data="{...}" />

```

对于大型的公司来说，公司的网站、APP、桌面应用、Web 端应用等的设计风格都是一样的，同样的组件会在不同平台中使用，这个时候团队之间可以共享一套组件库，复用到各端平台上，减少重复开发的成本。React 和 Vue 都支持创建跨平台的应用，这时组件化开发就显得更重要了：

![](https://img-blog.csdnimg.cn/ed14f02bd8114ca2959a13747470099e.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bOw5Y2O5YmN56uv5bel56iL5biI,size_20,color_FFFFFF,t_70,g_se,x_16)

使用组件化开发之后，代码也容易维护了，如果页面某个部分出了问题，那么我们可以针对出现问题的组件进行修复，多次用到这个组件的地方也都会一起修复了。  
最后，设计师在设计产品界面的时候，会把重复出现的 UI 也做成『组件』，这个组件的概念几乎和前端中的组件一模一样，这时可以跟设计师交流想法，看看他 / 她是怎么规划组件的，这样也减少了在开发时，设计规划组件的时间。  
​

接下来分享一下我在组件化开发中总结的经验：

一、组件的规划
-------

在正式进行前端应用开发之前，需要有充分的时间来分析，看看页面上的哪些内容需要做成组件。这一步只需要大致规划一下：

*   如果公司设计师提供了**详细的设计规范**，那么直接按照规范中的组件来开发就可以了。

![](https://img-blog.csdnimg.cn/b66f642166a546da9d9b5e98dca957b4.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bOw5Y2O5YmN56uv5bel56iL5biI,size_20,color_FFFFFF,t_70,g_se,x_16)

*   如果**只有设计稿**，那么就看看哪些内容有 2 次或更多次在其它页面中用到了，这些大概率需要设计为组件。
*   如果**连设计稿都没有**，那么可以用市面上现成的组件库，例如 ant design。
*   如果**没有设计稿，还要自己从零开发**，那么可以根据通用的套路，把基础组件，例如按钮、菜单等，规划出来，并把可能会多次用到的页面内容，也规划出来，例如导航，底部信息、联系方式区域，这样可以只改动需要变化的部分，不影响其它部分。

这一步**不用太过详细，浪费太多时间**，后续开发的时候如果遇到不确定是否要写成组件的部分，可以直接把这部分代码写到大的组件里，如果其它组件又用到了，再把它抽离成组件。

二、组件代码管理
--------

组件的代码应该遵循就近原则，也就是说：

*   和组件有关的 HTML、CSS 、JS 代码和图片等静态资源应该放在同一个目录下，方便引用。
*   组件里的代码应该只包括跟本组件相关的 HTML 模板、CSS 样式和 JS 数据逻辑。
*   所有的组件应放到一个统一的『组件文件夹中』。

如果组件之间有可以复用的 **HTML 和 CSS**，那么这个复用的部分可以直接定义成一个新的组件。

如果组件之间有可以复用的 **JS 数据逻辑**，那么可以把公用的数据逻辑抽离出来，放到公共的业务逻辑目录下，使用到该逻辑的组件，统一从这个目录中导入。

如果项目中使用到了**全局状态管理**，那么状态管理的数据应放在独立的目录里，这个目录还会存放分割好的状态片段 reducer，之后统一在 store 中合并。

对于项目中的 ** API 处理 **，可以把它们单独放到一个文件夹里，对于同一个数据类型的操作，可以放到同一个 js 文件里，例如对 user 用户数据的增删改查，这样能尽最大可能进行复用，之后在组件里可以直接引入相关 api 进行调用。如果直接写在组件里，那么使用相同 API 的组件就会造成代码重复。  
例如下面是一个组件化开发的目录结构示例（框架无关）：

```
project
|-- components                # 所有组件
      |-- Card                # Card 组件
          |-- index.js        # Card 组件 js 代码
          |-- Card.html       # Card 组件 html 模板
          |-- Card.css        # Card 组件 css 样式
          |-- icon.svg        # Card 组件用到的 icon
|-- logics                    # 公共业务逻辑
      |-- getUserInfo.js      # 例如获取用户信息
|-- data                      # 全局状态
      |-- store.js            # 全局状态管理 store
      |-- user.reducer.js     # user reducer
      |-- blogPost.reducer.js # blogPost reducer
|-- apis                      # 远程请求 API 业务逻辑
      |-- user.js             # user API
      |-- blogPost.js         # blogPost API

```

三、组件样式管理
--------

在编写组件样式的时候，应**只设置组件 CSS 盒子内部的样式**，影响外部布局的样式要尽可能的避免，而应该由使用该组件的父组件去设置。例如，如果有一个组件设置了外边距，但是这个组件经常会用于 grid 或 flex 布局中，那么这个额外的边距会对布局造成影响，只能通过重置外边距的方式取消边距，这就不如组件不设置外边距，由父组件的布局决定它的位置，或者外边距。

![](https://img-blog.csdnimg.cn/791fea91ae594fed9bb292e12d7b68ac.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bOw5Y2O5YmN56uv5bel56iL5biI,size_20,color_FFFFFF,t_70,g_se,x_16)

**类似的还有定位相关的样式**，绝对定位、固定定位等对文档流有影响，应交由父组件决定，除非这个组件只有绝对定位这一种情况，例如对话框。  
**组件中的 CSS 要局部化**，以避免影响全局样式。传统的 CSS 样式是全局的，如果有两个不同的组件，使用了相同的 class 名字，那么后定义的样式会覆盖之前定义的。一般前端库中都有定义局部样式的功能，例如通过 CSS Modules。

```
<!-- Vue -->
<style scoped></style>

<!-- React，通过文件名 -->
Button.module.css

```

**修改子组件的样式时，优先使用选择器特异性（CSS Specificity）策略选定特定元素**，而行内样式（inline-style）在设置简单样式的时候使用，尽一切可能避免 `!important`，因为如果再有上一层组件（爷爷组件）需要修改这个组件的样式时，就会很困难了。

四、组件属性管理
--------

组件属性的命名要与它实际展示的内容相符。例如一个博客文章组件，title 属性表示标题，content 代表内容，showFullArticle 表示是否显示全文。

```
<!-- 不推荐，full 表示什么？ -->
<BlogPost title="" content="" full="" />
<!-- 推荐 -->
<BlogPost title="" content="" showFullArticle="" />

```

组件的属性应有必要的类型检查，避免在使用属性时出现异常，例如在需要数组的地方传递了布尔类型，那么如果代码有遍历数组的逻辑，就会出错。

```
props: {
	title: String,
  content: String,
  showFullArticle: Boolean
}

```

代表事件的属性，应该和现有的 HTML 事件名称规范保持一致，以 on 开头，后面用英文表示会触发的事件，例如 onEdit 表示会触发编辑事件。

```
<BlogPost onEdit="" />

```

组件的属性不能直接和状态进行捆绑，而是应该只作为状态的初始值。如果把属性值作为状态值，那么就破坏了单一数据流向机制，此时该组件可以通过自身修改状态，也能通过父组件的属性变化修改状态，很容易造成数据的不一致。推荐的做法是，设置一个初始值属性，可以通过父组件传递进来，当作状态的初始值，然后丢弃，后续只通过该组件修改状态值。

```
const { initialTitle } = props;
const title = state(initialTitle);

// 修改
updateTitle() {
 title = "...";
}

```

五、组件状态管理
--------

组件的状态分为全局状态和局部状态两种。  
局部状态是定义在组件本身的状态，应由组件本身内部管理，当子组件需要该组件的状态值时，通过属性传递给子组件，此时状态变化时，子组件也会自动刷新。

```
// 父组件
const someState = state("");

<ChildComponent prop1="someState"></ChildComponent>;

```

当子组件需要给父组件传递状态的值时，要通过事件的方式传递给父组件，**尽最大可能避免使用 ref**。

```
// 父组件
function getStateVal(val) {
  console.log(val);
}
<ChildComponent onChange="getStateVal" />

// 子组件
<input onChange="e => getStateVal(e.target.value)" />

```

状态的变化还应只通过事件或生命周期来进行，不能在其它同步执行的代码中进行，这样不会引起组件的刷新。

```
const someState = state();

// 错误
const state = "newState";

// 正确
handleButtonClick() {
  someState = "newState";
}

```

全局状态是多个组件共享的，如果有多个组件共享某个状态，那么应该把状态定义在这些组件统一的、最接近的父组件中，或者使用全局状态管理库。

![](https://img-blog.csdnimg.cn/8b430d805d044ebe950b8f0c84da3ecd.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bOw5Y2O5YmN56uv5bel56iL5biI,size_20,color_FFFFFF,t_70,g_se,x_16)

全局状态的修改，应该由类似于 actions 的行为触发，然后使用 reducer 修改状态，这样能追溯状态的变化路径，方便调试和打印日志。

```
// 组件
function updateState() {
  emitAction("changeState");
}

// reducer
function someState(state, action) {
	switch(action) {
    "changeState": someState => {
      	log(someState);
    		return newState
    }
  }
}

```

全局状态推荐按领域（Domain）来拆分 reducer，而不是按页面。例如按 user 用户、文章 posts、评论 comments 来拆分，而不是以 HomePage 主页、BlogPostListPage 博客列表页，BlogPostPage 博客详情页这样来拆分。这样做的好处是，能够最大限度的复用 reducer 中的逻辑。

```
// userReducer

{
	"updateUser": ...
  "deleteUser": ...
  /* ... */
}

```

六、组件的组合
-------

前端开发就是组合不同的组件。  
在组合组件的时候，尽量使用『插槽』的形式（例如 React 的 children/render props，Vue 的 slot），这样可以减少组件的嵌套，避免多层传递属性或事件监听。  
使用 slot：

```
// Layout 组件
<div>
  <!-- nav slot -->
  <!-- content slot -->
  <!-- footer slot -->
</div>

// 首页 function handleNavChange() {}

<Layout>
  <nav onChange="handleNavChange"></nav>
  <main></main>
  <footer></footer>
</Layout>

```

不使用 slot：

```
// Layout props: { onNavChange: function }
<Layout>
  <!-- 还需再传递一层 -->
  <nav onChange="onNavChange"></nav>
  <main></main>
  <footer></footer>
</Layout>

// 首页 function handleNavChange() {}
<Layout onNavChange="handleNavChange" />

```

如果有循环展示列表的地方，需要对循环中最外层的组件设置 key，这样在列表发生变化时，能帮助前端库判断是否可以通过排序的方式，重复利用现有的组件，来更新视图，而不是销毁重建。

```
let todos = [{id: 1, content: "todo1"}, {id:2, content: "todo2"}, {id:3,
content: "todo3"}];

<List>
  for todo in todos:
  <item content="todo.content" key="todo.id" />
</List>

// todos 顺序变化，列表也只是根据 id 调整顺序，不会销毁重建 todos = [{id: 3,
content: "todo3"}, {id:2, content: "todo2"}, {id:1, content: "todo1"}];

```

如果有按条件展示组件的地方，且切换频率高，或有动画需求，要使用设置 css display 的方式，例如 vue 中的 v-show，如果切换频率低，可以使用加载、销毁的方式，例如 vue 中的 v-if，React 中使用 && 逻辑判断。

七、组件的复用
-------

在复用组件的时候，可以通过改变组件的属性来修改一些简单的组件内容。

```
<Card title="" content="<ContentComp />" />

```

如果组件结构类似，但是有些内部的组件不一样，可以考虑通过『插槽』来复用。

```
<!-- Comp -->
<div>
  <h1></h1>
  <!-- slot -->
</div>

<!-- 其它组件 -->
<Comp>
  <p>替换 slot</p>
</Comp>

```

如果有业务逻辑需要复用，尤其是涉及到状态变化的，那么可以把它抽离为公共的业务逻辑，利用 Hooks（React）或 Composables （Vue）来复用。

```
// 公共逻辑 (/logic/useSomeLogic.js)
function useSomeLogic() {
  const someState = state();
  const updateState = (v) => (someState = v);
  return {
    someState,
    updateState,
  };
}

// 组件1复用
import userSomeLoginc from "/logic/useSomeLogic.js";
const { someState, updateState } = useSomeLogic();

// 组件2复用
import userSomeLoginc from "/logic/useSomeLogic.js";
const { someState, updateState } = useSomeLogic();

```

如果有视图样式需要复用，那么可以直接把这部分再抽离成一个新的组件。

小结
--

这篇文章从组件的代码、样式、属性、状态、组合和复用的这几个场景上，总结了一些我在前端开发中的一些经验和个人看法，可能并不适用所有项目，如果你有更好的最佳实践和经验，欢迎分享！如果觉得本文有帮助，请分享给其它人，感谢！

> 原文地址：[https://zxuqian.cn/7-ways-to-organize-frontend-components](https://zxuqian.cn/7-ways-to-organize-frontend-components)，欢迎访问查看更多前端开发教程！  
> Bilibili：峰华前端工程师  
> 公众号：峰华前端工程师