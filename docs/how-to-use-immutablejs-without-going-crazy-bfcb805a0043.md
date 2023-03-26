# 如何在不发疯的情况下使用 ImmutableJS

> 原文：<https://medium.com/hackernoon/how-to-use-immutablejs-without-going-crazy-bfcb805a0043>

![](img/636096279eb1780402a3c21f296c0ca5.png)

如果你在 [Javascript](https://hackernoon.com/tagged/javascript) 生态系统中，你可能知道有许多新的库和良好的实践仍在开发中，尤其是在 ReactJS 生态系统中。

如果您正在使用 ReactJS 开发单页面应用程序，并且使用 Redux 进行状态管理[和](https://hackernoon.com/tagged/management)，那么您可能听说过 ImmutableJS。

这是一个帮助开发人员保持状态不变以避免难以发现的错误的库。

即使不是这样，但它也有助于在正确的时间重新渲染组件。你大概知道 React 是用浅层比较来知道部分道具是否有改动。

当您将结构化数据传递到组件(数组、对象)时，这可能会带来问题。React 不要执行深度比较(因为代价很高)，这样可能会遇到意想不到的行为。

```
// In reducer.js
const state = {
  usersPage: {
    loading: false,
    isFetched: false,
    list: [],
  },
  companiesPage: {
    loading: false,
    isFetched: false,
    list: [],
  },
};// In UsersPage.js
class UsersPage extends React.Component {
  render() {
    const { loading, list } = this.props.usersPage; return (
      <div>
        { loading && <LoadingIndicator /> }
        { !loading && <UsersTable list={list} /> }
      </div>
    );
  }
}function mapStateToProps(state) {
  return {
    usersPage: **selectors.selectUsersPage(state)**,
  };
}// In selectors.js
const selectUsersPage = (state) => state.usersPage;
```

考虑到上面的代码，一切似乎都很好。然而，如果你以错误的方式实现了状态更新，比如你没有创建一个新的对象，那么 React 不会更新你的组件。例如:

```
// Incorrect implementation
function reducer(state, action) {
  switch(action.type) {
    case 'USERS_LOADED':
      state.usersPage.list = action.payload;
      state.usersPage.loading = false;
      state.usersPage.isFetched = true;
      return state;
    default:
      return state;
  }
}
// Correct implementation
function reducer(state, action) {
  switch(action.type) {
    case 'USERS_LOADED':
      return {
        ...state,
        usersPage: Object.assign({}, state.usersPage, {
          list: Object.payload,
          loading: false,
          isFetched: true,
        }),
      };
    default:
      return state;
  }
}
```

正如你在上面的代码中看到的，事情很容易变得棘手。你必须确保，你在返回新的对象的同时，还保留了状态的其他分支，并正确地更新了当前分支中的变量。

现在，让我们看看不可变 JS 中的实现:

```
import { fromJS } from 'immutable';
const state = fromJS({
  usersPage: {
    loading: false,
    isFetched: false,
    list: [],
  },
  companiesPage: {
    loading: false,
    isFetched: false,
    list: [],
  },
});function reducer(state, action) {
  switch(action.type) {
    case 'USERS_LOADED':
      return state
 **.setIn(['usersPage', 'list'], fromJS(action.payload))
        .setIn(['usersPage', 'isFetched'], true)
        .setIn(['usersPage', 'loading'], false)**        ;
    default:
      return state;
  }
}
```

这种语法使得阅读和维护更加简单。并且 ImmutableJS 在您每次改变状态时都会返回一个新的对象。

然而，您将不得不使用 ImmutableJS **getters** 来获取这些值:

```
// In UsersPage.js
class UsersPage extends React.Component {
  render() {
    const { usersPage } = this.props;
 **const loading = usersPage.get('loading');
    const list = usersPage.get('list').toJS();** return (
      <div>
        { loading && <LoadingIndicator /> }
        { !loading && <UsersTable list={list} /> }
      </div>
    );
  }
}function mapStateToProps(state) {
  return {
    usersPage: selectors.selectUsersPage(state),
  };
}// In selectors.js
**const selectUsersPage = (state) => state.get('usersPage');**
```

更糟糕的是，有时不可变的对象会更深地渗入到你的组件中，甚至可能渗入到你的在线组件中(请看`toJS()`)。这通常是人们反对使用不变量的原因之一。

当您计划发布您的包时，情况会变得更糟。你不想让开发人员使用某个库来束缚他们的手脚。

那么如何结合两个世界的优点呢？

# **重新选择救援！**

Reselect 是一个很棒的库，可以帮助你解决这个问题。

让我们首先了解一下 Reselect 是为什么而构建的:

【Redux 的简单“选择器”库。

*   *选择器可以计算派生数据，允许 Redux 存储最小可能状态。*
*   *选择器效率高。除非选择器的一个参数发生更改，否则不会重新计算选择器。*
*   *选择器是可组合的。它们可以用作其他选择器的输入。*

跟我裸一会儿。考虑以下代码:

```
import { createSelector } from 'reselect';const selectUsersPageDomain = (state) => state.get('usersPage');const makeSelectUsersPage = createSelector(
  selectUsersPageDomain,
  (substate) => substate.toJS(),
);
```

然后，您可以再次使用纯 JS 语法:

```
import { **createStructuredSelector** } from 'reselect';class UsersPage extends React.Component {
  render() {
 **const { loading, list } = this.props.usersPage;**return (
      <div>
        { loading && <LoadingIndicator /> }
        { !loading && <UsersTable list={list} /> }
      </div>
    );
  }
}const mapStateToProps = **createStructuredSelector**({
  usersPage: selectors.makeSelectUsersPage(),
});
```

如果`userPage`分支没有变化，选择器**不会重新计算**的值，反应**不会触发**组件渲染。

正如你所看到的，使用这个策略，你可以结合两个世界的优点。

**但是有一个重要的不明显的好处我还没有谈到:**

当您使用 ImmutableJS 更新某个分支时，所有未受影响的分支保持不变:

```
const { fromJS } = require('immutable');
const state = fromJS({
  usersPage: {
    loading: false,
    isFetched: false,
    list: [],
  },
  companiesPage: {
    loading: false,
    isFetched: false,
    list: [],
  },
});console.log('Updating usersPage branch');
const state1 = state.setIn(['usersPage', 'loading'], true);
state.get('usersPage') === state1.get('usersPage'); // false
**state.get('companiesPage') === state1.get('companiesPage'); // true** state === state1; // falseconsole.log('Updating companiesPage branch');
const state2 = state1.setIn(['companiesPage', 'loading'], true);
**state1.get('usersPage') === state2.get('usersPage'); // true** state1.get('companiesPage') === state2.get('companiesPage'); // falsestate1 === state2; // false
```

*注意:我删除了* `*console.log*` *语句以使行更短。*

如果你看看代码，你会发现没有更新的分支将保持不变->所以`reselect`选择器不会触发更新。**只有更新分支的选择器才会被更新**。

但是状态的根也被更新了！因此，在编写选择器时，您必须小心一点:

```
// In reducer.js
const { fromJS } = require('immutable');
const state = fromJS({
 **pages: { // We put our pages 1 more level deeper**    usersPage: {
      loading: false,
      isFetched: false,
      list: [],
    },
    companiesPage: {
      loading: false,
      isFetched: false,
      list: [],
    },
  }
});// In selectors.js
**// Incorrect implementation** import { createSelector } from 'reselect';
**const selectPagesDomain = (state) => state.get('pages');** const selectUsersPageDomain = (state) => state.get('usersPage');
const selectCompaniesPageDomain = (state) => state.get('companiesPage');const makeSelectUsersPage = createSelector(
  **selectPagesDomain**, // source of problem
  selectUsersPageDomain,
  (substate) => substate.toJS(),
);const makeSelectCompaniesPage = createSelector(
  **selectPagesDomain**, // source of problem
  selectCompaniesPageDomain,
  (substate) => substate.toJS(),
);
```

如果你像这样实现选择器，当你只改变其中一个的值时，`makeSelectCompaniesPage`和`makeSelectUsersPage`都会被触发。这可能是你不想要的。

为什么会被触发？回头看看这条规则:

*   选择器是高效的。除非选择器的一个参数发生更改，否则不会重新计算选择器。

因为如果**的任何一个**分支发生变化，`selectPagesDomain`将返回不同的值，所以`reselect`每次都会重新计算两个选择器。

这是正确的实现方式:

```
// Correct implementation
import { createSelector } from 'reselect';
const selectUsersPageDomain = (state) => **state.getIn(['pages', 'usersPage'])**;
const selectCompaniesPageDomain = (state) => **state.getIn(['pages', 'companiesPage'])**;const makeSelectUsersPage = createSelector(
  **selectUsersPageDomain**,
  (substate) => substate.toJS(),
);const makeSelectCompaniesPage = createSelector(
  **selectCompaniesPageDomain**,
  (substate) => substate.toJS(),
);
```

您希望选择与选择器中要使用的数据最接近的路径，以防止在其他分支发生变化时重新计算。

> 如果你坚持到了这里，恭喜你！

让我们把这些放在一起:

```
**// In reducer.js** const { fromJS } = require('immutable');
const state = fromJS({
  pages: {
    usersPage: {
      loading: false,
      isFetched: false,
      list: [],
    },
    companiesPage: {
      loading: false,
      isFetched: false,
      list: [],
    },
  }
});function reducer(state, action) {
  switch(action.type) {
    case 'USERS_LOADED':
      return state
        .setIn(['usersPage', 'list'], fromJS(action.payload))
        .setIn(['usersPage', 'isFetched'], true)
        .setIn(['usersPage', 'loading'], false)
        ;
    case 'COMPANIES_LOADED':
      return state
        .setIn(['companiesPage', 'list'], fromJS(action.payload))
        .setIn(['companiesPage', 'isFetched'], true)
        .setIn(['companiesPage', 'loading'], false)
        ;
    default:
      return state;
  }
}export default reducer;**// In selectors.js** import { createSelector } from 'reselect';
const selectUsersPageDomain = (state) => state.getIn(['pages', 'usersPage']);
const selectCompaniesPageDomain = (state) => state.getIn(['pages', 'companiesPage']);export const makeSelectUsersPage = createSelector(
  selectUsersPageDomain,
  (substate) => substate.toJS(),
);export const makeSelectCompaniesPage = createSelector(
  selectCompaniesPageDomain,
  (substate) => substate.toJS(),
);**// In UsersPage.js** import { createStructuredSelector } from 'reselect';
import * as selectors from './selectors';class UsersPage extends React.Component {
  render() {
    const { loading, list } = this.props.usersPage; return (
      <div>
        { loading && <LoadingIndicator /> }
        { !loading && <UsersTable list={list} /> }
      </div>
    );
  }
}const mapStateToProps = createStructuredSelector({
  usersPage: selectors.makeSelectUsersPage(),
});export default connect(mapStateToProps)(UsersPage);**// In CompaniesPage.js** import { createStructuredSelector } from 'reselect';
import * as selectors from './selectors';class CompaniesPage extends React.Component {
  render() {
    const { loading, list } = this.props.companiesPage; return (
      <div>
        { loading && <LoadingIndicator /> }
        { !loading && <CompaniesTable list={list} /> }
      </div>
    );
  }
}const mapStateToProps = createStructuredSelector({
  companiesPage: selectors.makeSelectCompaniesPage(),
});export default connect(mapStateToProps)(CompaniesPage);
```

就是这样！

现在，您可以使用两个**不可变组件**的所有优点，而不需要在您的容器或呈现组件中使用它的 getters。

最重要的是，你获得了**重新选择**库的优势，它可以帮助你使你的选择器更有效。

让我知道你的想法！

参考资料:

[](https://redux.js.org/) [## Redux

### 如果不使用模块捆绑器，也没问题。npm 包包括预编译的生产和开发…

redux.js.org](https://redux.js.org/) [](https://github.com/reactjs/reselect) [## 反应/重新选择

### Redux 的重选选择器库

github.com](https://github.com/reactjs/reselect)