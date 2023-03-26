# React 中的表单处理！别担心，让它保持反应状态。

> 原文：<https://medium.com/hackernoon/better-forms-with-react-reactive-form-431571e40a0f>

> 很酷的是，如果你把所有的数据控制在一个对象中，你可以在任何时候不受任何限制地操作控件的状态。

![](img/e5d7e8ba22d7d794d211c1671d215315.png)

当我发布这个库`[react-reactive-form](https://github.com/bietkul/react-reactive-form)`时，我得到了来自社区的不同反应，在&尝试 react 之前与 angular 一起工作的人，只是喜欢它，但是来自 React 背景的人并没有感到很舒服。

这个想法是在反应式表单 api 和反应式组件之间架起一座桥梁，这样我们就能以一种简单高效的方式控制大型复杂的表单。

在 RRF 的最新版本中，我添加了一些 react [api 的](https://hackernoon.com/tagged/apis)来创建&操纵控件。因此，对于 react 开发人员来说，现在创建表单变得非常容易，而不需要了解太多的库。

# **最新版本有什么新内容？🎉**

新版本的 [RRF](https://hackernoon.com/tagged/rrf) 包含了这些很酷的特性，这使得 RRF 成为在 react 中构建表单的完美库。

**字段组件**
你可以使用这些组件创建一个新的控件或者绑定一个现有的控件。

*   [**field group**](https://github.com/bietkul/react-reactive-form/blob/master/docs/api/FieldGroup.md)react 组件，创建新的或可用于现有 [FormGroup](https://github.com/bietkul/react-reactive-form/blob/master/docs/api/FormGroup.md) 控件。
*   [**field array**](https://github.com/bietkul/react-reactive-form/blob/master/docs/api/FieldArray.md)一个 react 组件，它创建一个新的或者可以与现有的 [FormArray](https://github.com/bietkul/react-reactive-form/blob/master/docs/api/FormArray.md) 控件一起使用。
*   [**field control**](https://github.com/bietkul/react-reactive-form/blob/master/docs/api/FieldControl.md)一个 react 组件，它创建一个新的或可与现有的 [FormControl](https://github.com/bietkul/react-reactive-form/blob/master/docs/api/FormControl.md) 控件一起使用。

查看下面的示例，了解这些组件的基本用途。

React Reactive Form: Simple form example

# [**表格生成器**](https://github.com/bietkul/react-reactive-form/blob/master/docs/api/FormGenerator.md)

最新版本的 RRF 引入了`[FormGenerator](https://github.com/bietkul/react-reactive-form/blob/master/docs/api/FormGenerator.md)` api，你可以通过一个字段配置生成你的表单。当你有可重用的输入组件或使用第三方库如 bootstrap 时，这个 api 是最合适的，你只需要将组件分配给你的控件。

有了这个 api，用不了两分钟就可以完成一个大表单，但是唯一的条件是你必须准备好你的组件。😃

```
const fieldConfig = {  
  controls: {
    username: {
      render: TextInput, // A React component which renders an input
      meta: {
        label: 'Username'
      },
      options: {
        validators: Validators.required
      }
    },
    password: {
      render: TextInput,
      meta: {
        label: 'Password',
        type: 'password'
      },
      options: {
        validators: Validators.required
      }
    }
  }
}
```

React Reactive Form: Simple form with Form Generator

# **新听众**

RRF 的新版本引入了两个新的监听器，为表单提供了更多的控制。您可以使用这些侦听器来订阅事件更改&执行一些操作，如向服务器更新数据或处理其他控件的状态。

*   **onValueChange** 每当控件上的`onChange`事件触发时，将调用注册的观察器。
*   每当控件上触发`onBlur`事件时，就会调用注册的观察者。

除了这些新的侦听器之外，RRF 还提供了更多的(valueChanges，statusChanges 等)侦听器，这些侦听器可用于执行表单状态的动态更改。

例如:

```
componentDidMount() {
     this.form.get('gender').valueChanges((value) => {
        if(value === 'FEMALE') {
          this.form.get('age').disable();
        }     
     })
}
```

这些监听器的常见用例有:
——根据其他字段的变化禁用/启用一个输入字段。
-从控制树中添加/移除控制。
-设置/移除验证器

# 相关链接:

要知道为什么这个库比其他解决方案更好，请查看下面的文章。
[**引入 React 反应形式**](https://hackernoon.com/introducing-reactive-forms-in-react-e6df0b84a1ea)

[](https://github.com/bietkul/react-reactive-form) [## bietkul/react-reactive-form

### 反应-反应-形式-角状反应形式。

github.com](https://github.com/bietkul/react-reactive-form) 

让我们让 React 反应形式更好！如果你有兴趣帮忙，欢迎并感谢所有的贡献。❤️

感谢阅读！请试一试👏如果你喜欢这篇文章，不要忘记开始回购，我会保证更频繁的更新！🙏