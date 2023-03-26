# React 的第一步

> 原文：<https://medium.com/hackernoon/your-first-steps-in-react-dce29b400bde>

[**ReactJS**](https://reactjs.org/) 是一个用于构建 ui 的 JavaScript 库。尽管没有严格的定义，React 的思想是遵循基于组件的思想。它的声明性视图使得**代码更加可预测**并且**更容易调试**。

我所在的 Kolosek 团队使用 ReactJS 结合 [**Webpack**](https://webpack.js.org/) 作为捆绑包管理器，使用 [**Redux**](https://redux.js.org/) 结合 [**不可变 JS**](https://facebook.github.io/immutable-js/) 进行应用状态管理，使用其他一些让开发人员生活更轻松的库，如[](https://gulpjs.com/)**处理构建和部署(以及其他一些)任务。**

**既可以作为简单的静态网页使用，也可以在服务器端渲染，例如 [**节点 JS**](https://nodejs.org/en/) 。在服务器上呈现的 React 应用通常被称为 [**通用应用**](https://scotch.io/tutorials/react-on-the-server-for-beginners-build-a-universal-react-and-node-app) ，但有时也被称为同构。**

**在本文中，重点只放在 React 上，因为 Redux 和不可变 JS 将被分别记录。**

# **和 JSX 反应过来**

**在 React 中，您会广泛地看到如下代码部分:**

```
const element = <h1>Hello, world!</h1>;
```

**这既不是 JavaScript 字符串，也不是 HTML，但它被称为 **JSX** ，JavaScript 的一种语法扩展。它在 React 的 render 方法中被广泛使用，用来呈现网页的动态部分。JSX 产生 React“元素”，例如，当[基于条件](https://kolosek.com/react-jsx-conditions/)渲染某些东西时，它被广泛使用。**

**要使用 JSX 内部渲染方法，需要将其包裹在 **{}** 内部:**

```
<img src={user.avatarUrl}></img>
```

**使用 JSX 是安全的，因为它可以防止注入攻击，所以不用担心会有代码注入。**

**在开发时，您肯定还想呈现一个项目列表，或者在某个点上遍历某个对象。这也是使用 JSX 完成的，和条件一样，我有[一篇单独的文章，只关注列表的渲染](https://kolosek.com/react-jsx-loops/)。
永远记住使用列表键！**

****键**帮助 React 识别哪些项目被改变、添加或删除。键必须是唯一的，并且不应该为了列表中 React 元素的正确呈现而改变。**

# **基于组件的组织和道具**

**React 推荐**基于组件的代码组织**。代码的所有逻辑上可分离的部分，如输入字段、按钮、表单、列表等。应该在单独的组件中编写。**

**代码的所有简单部分都应该写成**虚拟**组件(通常也被称为**纯**)，这意味着它们只会根据传递给它们的参数进行渲染，而不会对任何应用程序逻辑有任何想法。**

**以下是虚拟组件的两个示例:**

```
 function Button({ onClick, text }) {
       return (
           <button
               onClick={onClick}
           >
               {text}
           </button>
       );
    }
```

**您可以使用{…objectName}语法将整个对象作为 [props](https://kolosek.com/react-props-basic/) 传递；这将把 objectName 中的每个键值作为单独的属性传递。**

```
 function InputField(props) {
       // props is an object containing keys like onChange, placeholder, etc...
       //
       return (
           <input
               {...props}
           />
       );
     }
```

**如果你想只提取一些道具，你可以这样做:**

```
 function InputField(props) {
        const {
            onChange,
            placeholder,
            value,
        } = props;
        return (
            <input
                onChange={onChange}
                placeholder={placeholder}
                value={value}
            />
        );
      }
```

****现在，如何使用这些虚拟组件？**基本上，你可以通过将它们导入文件(使用文件的相对路径)然后在 render 方法中调用它们来使用它们。这里有一个例子:**

```
 import React, { Component } from 'react';
    import InputField from '../components/InputField.js';
    import Button from '../components/Button.js';
    class Login extends Component {
         ...
         render() {
             return (
                 <InputField
                     onChange={this.handleChange('email')}
                     placeholder={"Email"}
                     type="text"
                     value={this.state.email}
                 />
                 <InputField
                     onChange={this.handleChange('password')}
                     placeholder={"Password"}
                     type="password"
                     value={this.state.password}
                 />
                 <Button
                     onClick={this.login}
                 >
                     {'Login'}
                 </Button>
               );
           }
           ...
      }
```

**你注意到了吗，我写这个组件的方式和以前的不同。那是因为它不是一个虚拟的，而是包含了更多的逻辑。以这种方式编写的组件被称为智能组件或**类**，除了 render 方法之外，还可以绑定其他方法。点击可了解更多关于虚拟和智能组件的信息[。](https://jaketrent.com/post/smart-dumb-components-react/)**

**那么，**的道具**到底是什么？**

**你可以把一个组件想象成一个功能(还记得那些虚拟的例子吗？他们甚至使用标准的函数定义！).基本上，props 是调用时传递给该函数的**参数。任何东西都可以是道具:字符串、布尔值、数字、一些其他的 React 元素、未定义的…****

**关于道具，要记住的最重要的事情是它们是**只读的**，这意味着它们不能在接收它们的组件中被改变，也不应该被变异。你可以在我们关于如何在 React 中正确使用道具的系列文章[中阅读更多关于如何传递道具的内容。](https://kolosek.com/react-props-basic/)**

# **反应状态**

**在前一章中，你已经知道了 props 是传递给组件的参数。另一方面，**状态**是私有的，它的主要目的是在组件内操纵它。状态只对类可用*,用于处理一些内部变化(比如更新一些基于 onChange 事件存储的文本):***

```
 *import React, { Component } from 'react';
    import InputField from '../components/InputField.js';
    ...
    class Login extends Component {
        // We define initial component state in constructor function
        constructor() {
            super();
            this.state = {
                email: '',
            }
        }
        // Handling state change is done by using this.setState function
        handleChange = (event) => {
             this.setState(() => {
                 return {
                     email: event.target.value,
                 };
             });
        } ...
        render() {
            return (
                <InputField
                    onChange={this.handleChange}
                    placeholder={"Email"}
                    type="text"
                    value={this.state.email}
                />
                {
                    ...
                }
              );
         }
         ...
    }*
```

***状态在**构造函数**中初始化，这是一个在初始组件渲染之前调用的特殊方法。所有的自定义方法(比如上一个例子中的 handleChange)都应该使用 arrow 函数编写，这样它们就会自动绑定到一个组件。***

***您可能会注意到其他一些人像这样使用 this.setState:***

```
 *this.setState({
        email: event.target.value,
    });*
```

***这不是更新状态的好方法！状态是异步更新的，如果需要在一行中多次更新状态，它会以这种方式批量处理所有的设置状态。使用[功能方式更新状态](https://medium.freecodecamp.org/functional-setstate-is-the-future-of-react-374f30401b6b)，如其他示例所述。***

***现在，让我们看另一个例子:***

```
 *import React, { Component } from 'react';
    import InputField from '../components/InputField.js';
    ...
    class Login extends Component {
        constructor() {
            super();
            this.state = {
                email: '',
                password: '',
            }
         } handleChange = (type) => (event) => {
             this.setState(() => {
                 const stateUpdates = {};
                 stateUpdates[type] = event.target.value;
                 return stateUpdates;
              });
         } ...
         render() {
             return (
                 <InputField
                     onChange={this.handleChange("email")}
                     placeholder={"Email"} type="text"
                     value={this.state.email}
                 />
                 <InputField
                     onChange={this.handleChange("password")}
                     placeholder={"Password"}
                     type="password"
                     value={this.state.password}
                 />
                 {
                      ...
                 }
               );
          }
          ...
    }*
```

***在定义自定义方法时有一条规则:**永远不要在呈现方法**中绑定它。上面的例子展示了一种正确的方法。如果该方法绑定在 render 方法中，则每次重新呈现组件时都会创建新函数。我已经在[一篇关于道具功能的文章](https://kolosek.com/react-props-basic/)中提到了这一点。***

# ***组件生命周期***

***处理组件生命周期的方法继承自 component(是的，您在文件顶部导入的组件)。生命周期方法的最详细描述可以在[这里](http://busypeoples.github.io/post/react-component-lifecycle/)找到。***

***要记住的一件重要的事情是，在通用应用程序(服务器上呈现的应用程序)的情况下，浏览器相关的全局变量如窗口、本地存储等。仅可在组件安装后使用(即仅在**componentid mount**中可用)。***

# ***样式反应组件***

***虽然 React 本身不支持直接使用 CSS 进行样式化，但更面向内联 JSXstyles(将样式对象 prop 传递给 HTML 标记)，这种方法并不常用。***

***在你将会看到的许多(如果不是全部)真实项目中，已经设置了一些 [CSS/SCSS 加载器](https://github.com/webpack-contrib/css-loader)，所以你可以轻松地使用像 [CSS 列](https://kolosek.com/css-columns/)这样的样式。这是通过 Webpack 配置的。***

# ***React 中的路由***

***对于路由，使用[React 路由器 v4。它的文档非常简单明了，也非常简短，所以是必读的。基本上，我们使用路由器和路由来定义路由，使用链接组件来链接页面，使用重定向组件来重定向到某个动作上某个其他页面。](https://github.com/ReactTraining/react-router)***

# ***实践想法***

*   ***使用 [**创建 React 应用**](https://github.com/facebookincubator/create-react-app) 启动一个新的 React 应用。***
*   ***尝试创建几个非常简单的组件:带标签的输入标签包装器，一个灰色的按钮，禁用后文本会改变，一个头像。***
*   ***尝试创建一个稍微复杂一点的组件:一个包含电子邮件、密码、名字和姓氏字段以及一个按钮的表单(重用您在上一步中编写的虚拟组件！).***
*   ***验证表单:在启用按钮之前，应该填写电子邮件、密码、名字和姓氏字段。电子邮件也应该是有效的格式。***
*   ***一旦表单有效并按下登录按钮，弹出一个用户填写值的警告。***
*   ***恭喜你！您已经编写了您的第一个 React 组件！***

****原载于 2018 年 4 月 2 日 kolosek.com*[](https://kolosek.com/react-first-steps/)**。*****