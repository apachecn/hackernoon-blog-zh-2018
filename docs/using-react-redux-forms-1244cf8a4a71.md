# 使用 React-Redux 形式

> 原文：<https://medium.com/hackernoon/using-react-redux-forms-1244cf8a4a71>

![](img/498c6861cb5ceb89d369527625e4b976.png)

你可以说我们转向 Redux 的主要原因是验证。我们不想在页面组件中添加大量代码来验证每个子组件。在一些组件中，我们有多达十个字段，并且所有的字段都必须经过验证。你能想象验证所有这些的组件的大小吗？几百行长。对你们中的一些人来说，这没什么。我是说，为什么一个组件不能很长？对我们来说，这是关于可维护性，拥有一个大的组件是很难导航和看到它看起来像什么。

# 出现了[反应还原形式](https://github.com/davidkpiano/react-redux-form)

从之前的帖子中，你可能已经看到我们在[反应](https://hackernoon.com/tagged/react)中使用了[引导](https://hackernoon.com/tagged/agilehttps://hackernoon.com/tagged/agilehttps://hackernoon.com/tagged/agilehttps://hackernoon.com/tagged/agilehttps://hackernoon.com/tagged/agilehttps://hackernoon.com/tagged/agilehttps://hackernoon.com/tagged/agilehttps://hackernoon.com/tagged/agilehttps://hackernoon.com/tagged/agilehttps://hackernoon.com/tagged/agilehttps://hackernoon.com/tagged/agilehttps://hackernoon.com/tagged/agilehttps://hackernoon.com/tagged/agilehttps://hackernoon.com/tagged/agilehttps://hackernoon.com/tagged/bootstrap)。这是事实。我们需要一种方法，能够在不增加太多代码的情况下验证引导组件。React-Redux-Form 使这变得非常容易。它只是你的组件(在这种情况下是引导组件)的包装器，封装了你在验证时需要的所有东西。它可以处理所有的东西，比如脏的，碰过的等等。给你的。

我在下面为你创建了一个非常基本的例子。你必须总是用一个表单标签包围你的表单。这很重要，因为这是你提交的地方。从。首先，我创建了一个包含三个按钮的组件。两个创建有效值，我们希望第三个触发无效值。

```
import * as React from 'react';
import { Button } from 'react-bootstrap';

interface MyProps {
    onChange: (name: string) => void;
    value: string;
}

export const MyComponent: React.StatelessComponent<MyProps> = (props) => {

    const onButtonClickJohn = () => {
        props.onChange('John');
    };

    const onButtonClickJacob = () => {
        props.onChange('Jacob');
    };

    const onButtonClickMichael = () => {
        props.onChange('Michael');
    };

    return (
        <div>
           <span>{props.value}</span>
           <Button onClick={onButtonClickJohn}>John</Button>
           <Button onClick={onButtonClickJacob}>Jacob</Button>
           <Button onClick={onButtonClickMichael}>Michael</Button>
        </div>
    );
};
```

这是表单中的组件。

```
import * as React from 'react';
import MyComponent from './MyComponent';
import { Control, Form } from 'react-redux-form';export interface MyComponentFormProps {
    viewModel: {
        myComponent: string;
    };
}export default class MyComponentForm extends React.Component<MyComponentFormProps, {}>{
    constructor(props: MyComponentFormProps) {
        super(props);
    }

    handleSubmit() {
        console.log('submitted');
    }

    render() {
        const mapProps = {
            value: (props: MapPropsProps) => props.viewValue,
        };
        const validators = () => ({
            isNotMichael: (val: any): boolean => {
                if (val === 'Michael') {
                    return false;
                }
                return true;
            },
        });

        return (
            <Form className="myComponentForm" model="myComponentForm" onSubmit={this.handleSubmit}>
                <Control.text
                    component={MyComponent}
                    model={'myComponentForm.myComponent'}
                    mapProps={mapProps}
                    validators={validators}
                    updateOn="change"
                />
                <Errors
                  model="myComponentForm.myComponent"
                  messages={{
                    isNotMichael: 'Please don't choose michael.',
                  }}
                />
            </Form>
        );
    }
}
```

你需要给你的减速器加点东西。现在有一个表单部分—如果您需要访问有效/无效等。

```
import React from 'react';
import { createStore } from 'redux';
import { combineForms } from 'react-redux-form';const initialMyComponentForm = { myComponent: '' };const store = createStore(combineForms({
  myComponentForm: initialMyComponentForm,
}));
```

# 你的不会那么容易

上面的例子是一个相对良性的例子，说明了如何在 React-Redux 代码中引入 React-Redux 表单。我也没有测试它是否能编译，所以让我知道它是否能工作。可惜，生活没那么容易。你会发现你的表格会变得更加复杂。您将拥有需要重用的组件。您将拥有需要重用的验证器。一切都应该被测试，并且是可测试的。而且，最重要的是，您可能想要干净的代码，不要让验证逻辑或所有这些表单之类的东西污染您的组件。

*原载于 2018 年 2 月 26 日*[*www . alexaitken . NZ*](https://www.alexaitken.nz/blog/using-react-redux-forms/)*。*