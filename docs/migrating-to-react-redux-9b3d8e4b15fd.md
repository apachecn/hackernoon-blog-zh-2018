# 迁移到 React-Redux

> 原文：<https://medium.com/hackernoon/migrating-to-react-redux-9b3d8e4b15fd>

![](img/470272365847aa95abe5c430127bf664.png)

跟我重复“我不需要 [Redux](https://hackernoon.com/tagged/redux) ”。说真的，你可能不知道。在我们开始添加带有复杂验证的更复杂的表单之前，我们在 SPA 中并不需要 Redux。你可以很容易地不用 Redux 编写一个 [React](https://hackernoon.com/tagged/react) SPA。可以在页面级别(或者更高级别，如果您愿意)管理状态。不用 Redux 也能轻松创建 React app。然而，当复杂的验证出现时，我们决定需要 Redux。我们认为最好是从页面中提取出这种逻辑，将其转化为动作和缩减器。

![](img/3040bc10e0e740246a104535426a506f.png)

# 该过程

当我们第一次开始使用 Redux 时，我们不知道自己在做什么。我们已经用 React 编程 7-8 个月了。这是一个全新的想法。这是一场全新的比赛。怎么从 React 到 Redux？对我们来说，你要一个部件一个部件地做。我们还有很多页面不在 Redux 中，因为我们不需要接触这些页面。为什么要改变运行良好的东西呢？显然，如果我们确实需要重用其他页面，我们也会使它们冗余。这对我们来说已经成了家常便饭。

我们是如何做到 Redux 的，就是我们让我们的页面变成了无状态的。这很容易做到，因为我们的大多数组件都是无状态的。然后我们用我们称之为容器的东西把这些纸包起来。这个容器与我们的减速器和动作有联系。这意味着我们已经能够保持组件的可视化，没有复杂的状态。容器看起来像什么？

假设您有一个如下所示的 React 组件。

```
import * as React from 'react';
import { Button } from 'react-bootstrap';interface MyProps {
    onButtonClick: () => void;
    name: string;
}export const MyComponent: React.StatelessComponent<MyProps> = (props) => {
    return (
        <div>
           <span>{props.name}</span>
           <Button onClick={props.onButtonClick} />
        </div>
    );
};
```

它必须从某个地方得到状态，对吗？这就是容器出现的地方。

```
import * as React from 'react';
import { MyComponent } from './MyComponent';
import { State } from './reducer';
import { Dispatch, connect } from 'react-redux';
import { onButtonClick } from './actions';type StateToProps = {
    name: string;
};const mapStateToProps = (state: State): StateToProps => {
    return {
        name: state.name,
    };
};type DispatchToProps = {
    onButtonClick(): void;
};const mapDispatchToProps = (dispatch: Dispatch<State>): DispatchToProps => ({
    onButtonClick() {
        dispatch(onButtonClick())
    },
});export type MyComponentContainerProps = StateToProps & DispatchToProps;export class MyComponentContainer extends React.Component<MyComponentContainerProps, {}>{render() {
        return (
            <MyComponent
                name={this.props.name}
                onButtonClick={this.props.onButtonClick}
            />
        );
    }       
};export default connect(mapStateToProps, mapDispatchToProps)(MyComponentContainerContainer);
```

容器连接到你的减速器，减速器是你的状态守护者。任何改变你状态的东西都必须来自你的减压器。减速器是如何获得变化的？

```
import { handleActions, Action } from 'redux-actions';export const CHANGE_NAME = 'MYCOMPONENT.CHANGE_NAME';export interface State {
    name: string;
}export const initialState: State = {
    name: 'John',
}export const myComponentReducer = handleActions<State, string>({
    [CHANGE_NAME]: (state, action: Action<string>) => {
        return {
            ...state,
            name: action.payload,
        };
    },
}, initialState);
```

这就是行动的由来。动作向你的缩减器发送消息说“嘿，我有这个事件。它在这里有这个变化。做你想做的。”我们可以看到该事件与上面的 reducer 中的字符串相同，所以当调用该动作时，它将触发 reducer 上的状态变化。

```
import { CHANGE_NAME } from './State';
import { Dispatch } from 'react-redux';
import { createAction } from 'redux-actions';const onButtonClickAction = createAction(CHANGE_NAME, (name: string) => name);export const onButtonClick = () => {
    return (dispatch: Dispatch<string>) => {
        dispatch(onButtonClickAction('new name'));
    };
};
```

您还需要做其他事情，如添加提供商和创建商店等。但是你可以在 [Redux](https://redux.js.org/) 上查到。

# 摘要

从上面的例子可以看出，这确实给你的解决方案增加了许多样板代码。如果你认为你不需要 Redux，那么你很可能不需要。在我的下一篇文章中，我将讨论 React-Redux-Forms。React 中的验证工作相当多，但是 React-Redux-Forms 确实帮助我们最小化了组件中的噪声，这样我们就有了漂亮、干净的代码，任何人都可以很快阅读并获得要点。

让我知道你们是如何使用 Redux 的。我很想听听，看看我们能否有所改进。

*原载于 2018 年 2 月 19 日*[*www . alexaitken . NZ*](https://www.alexaitken.nz/blog/migrating-to-react-redux/)*。*