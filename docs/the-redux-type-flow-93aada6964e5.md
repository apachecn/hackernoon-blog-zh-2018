# Redux 型流程(2018 年重访)

> 原文：<https://medium.com/hackernoon/the-redux-type-flow-93aada6964e5>

## 探索冗余使用流程中的类型安全

[redux](https://hackernoon.com/tagged/redux) 代码中的类型安全有 3 个组成部分:

*   行动
*   动作创建者
*   [减速器](https://hackernoon.com/tagged/reducer)

**动作**只是[普通的老 JS 对象](https://wikipedia.org/wiki/Plain_old_Java_object)。它们是由动作创作者创造的。

**动作创建者**是创建动作对象的功能。

**归约器**是带有两个参数的函数——T0 和 T1。其中，给`action`添加合适的字体是一个真正的挑战。我们一会儿就会明白为什么会这样。

# 我们的工作台

为了表达我的观点，我将在本文中使用一个简单的 reducer 函数。它存储来自输入的字符串消息，默认值为“Hello World”。用户可以**清除**输入，**更新**或**复位**它。

```
const **CLEAR** = 'CLEAR';
const **RESET** = 'RESET';
const **UPDATE** = 'UPDATE';function **reducer**(state: string = '', action) {
  switch (action.type) {
    case **CLEAR**:
      return ''; case **RESET**:
      return action.text; case **UPDATE**:
      return action.text; **default**:
      return state;
  }
}function **clear**() {
  return { type: CLEAR };
}function **reset**() {
  return { type: RESET, text: 'Hello World' };
}function **update**(text: string) {
  return { type: UPDATE, text };
}
```

# 手动类型

“主要想法”是每个动作创建者创建一个具有独特形状的*动作*——一个具有类型`string`值的属性`type`。

我们可以手动将类型添加到 action 对象中，并在 reducer 中将它们用作一个 [union](https://flow.org/en/docs/types/unions/) 类型。

```
type **ClearAction** = { type: 'CLEAR' };
type **ResetAction** = { type: 'RESET', text: 'Hello World' };
type **UpdateAction** = { type: 'UPDATE', text: string };type **Action** =
  | ClearAction
  | ResetAction
  | UpdateAction;function reducer(state: string = '', action: **Action**) {
  ...
}
```

这个简单的方法*就在*起作用，你可以在这里[看到](https://flow.org/try/#0C4TwDgpgBAwgNhAhgJwIIGNgEsD2A7KAXigG8pRIAuKAchgBkBRVAJRqgF8BuAKAuhYQAzhGAZs+IqXLgI1Gi0YBlRgBUaAGnIQAHsHkAJCHDg4oAdRzI4AE3bc+sqAFUwNxMAjjcBYmX7yzgAKACKoqoya2nrUQsDIWHgA5py8jpBQ3pKEPFBQAD6wCChZeLkFUIIiYpg+5YWu7p6laej4cbBMrFJ0XWy8bXgdiiqqPSNqNAPtwC6h4Yw9wWERUzw8AGYArni1ksgQNlvoEMgAFHEeclBxCck9UYh7eNSlAJSk5UIA7ljA6AALKBnJ4SPAAOn4HxI5Ty6EQIk6zBYlFheSgB2AW2QBBoazR8MRE1UqPR6Mx2IIoJ8kN0wDSZMJ0GWC1JZIxokpUGp+FpegZ6JsEA2iC2cH0aLyFJxN2AV14eQ4PCVmx2zyg6GK52h5WlBH8smoDGRqWV622uzBHOqZx1Us5MoNVEqyjUWk8MVoRhMZks1jsppVFvVWzcVzOHv0sruSTtHKxjpkzpZEXddMDPCAA)。以这种方式注释代码的一个额外好处是，我们也可以向动作创建者添加类型(参见[这里的](https://flow.org/try/#0C4TwDgpgBAwgNhAhgJwIIGNgEsD2A7KAXigG8pRIAuKAchgBkBRVAJRqgF8BuAKAuhYQAzhGAZs+IqXLgI1Gi0YBlRgBUaAGnIQAHsHkAJCHDg4oAdRzI4AE3bc+sqAFUwNxMAjjcBYmX7yzgAKACKoqoya2nrUQsDIWHgA5py8jpBQ3pKEPFBQAD6wCChZeLkFUIIiYpg+5YWu7p6laej4cbBMrFJ0XWy8bXgdiiqqPSNqNAPtwC6h4Yw9wWERUzw8AGYArni1ksgQNlvoEMgAFHEeclBxCck9UYh7eNSlAJSk5UIA7ljA6AALKBnJ4SPAAOn4HxI5Ty6EQIk6zBYlFheSgB2AW2QBBoazR8MRE1UqPR6Mx2IIoJ8kN0wDSZMJ0GWC1JZIxokpUGp+FpegZ6JsEA2iC2cH0aLyFJxN2AV14eQ4PCVmx2zyg6GK5ze1HgSDQ6phUs5Mv8sl1fVSyvW212YI51TOOsqwlEpU+xqxppkVEqyjUWk8MVoRhMZks1jsVpVtvVWzcVzOQf0sruSWdjSu7qNHK9BDNvpZEUDdOjPCAA))。

```
...function **clear**(): **ClearAction** {
  return { type: CLEAR };
}function **reset**(): **ResetAction** {
  return { type: RESET, text: 'Hello World' };
}function **update**(text: string): **UpdateAction** {
  return { type: UPDATE, text };
}
```

# 推断的注释类型

在一个小而简单的项目中，这很好。但是当您有多个 reducer，并且每个 reducer 有多个动作，每个动作对象有不同的形状时，手动编写所有内容本身就是一个挑战。我们需要一个更好的系统；一个有某种程度自动化的系统。

我们来看看`ResetAction`:

```
type ResetAction = **{ type: 'RESET', text: 'Hello World' }**;
```

它与`reset()`动作创建者的返回值形状相同。

```
function reset() {
  return **{ type: RESET, text: 'Hello World' }**;
}
```

除了手动编写每个操作对象类型，我们还可以:

1.  调用操作创建者
2.  将返回的对象转换为静态类型
3.  在`Action`类型中使用这些类型值。

幸运的是，我们真的不需要*字面上的*调用动作创建者。我们可以利用 [Flow 的注释类型](https://flow.org/en/docs/types/comments/)，它们将在“编译时”而不是运行时执行代码块(参见这里的)。

```
/*::
  // *"compile time"* execution code
  const **clearAction** = clear();
  const **resetAction** = reset();
  const **updateAction** = update('');
*/type **ClearAction** = typeof clearAction;
type **ResetAction** = typeof resetAction;
type **UpdateAction** = typeof updateAction;type **Action** = 
  | ClearAction
  | ResetAction
  | UpdateAction;function reducer(state: string = '', action: **Action**) {
  ...
}
```

不幸的是，因为我们是通过调用动作创建者来推断动作对象的类型，所以我们**不能**用推断的类型来注释创建者。这有点道理，因为这将在推断的类型和类型注释之间创建一个循环的依赖链。

***注意:*** *在*行:5 *中，我们将一个空字符串传递给* `*update()*` *，因为它需要一个字符串参数，而 Flow 要求我们传递* ***所有的*** *参数来进行适当的函数调用。*

# 提取返回类型

最*合法*的解决方案之一来自[这个 github 线程](https://github.com/facebook/flow/issues/4002)。其中一条评论把我引向了谢恩·奥斯本的这篇文章。

本文讨论了一种“提取”函数返回类型的类型，并将其用于动作创建者。一个[后来在帖子中的评论](https://github.com/facebook/flow/issues/4002#issuecomment-384756198)将这背后的想法浓缩到了它的本质:*动作是动作创造者的返回类型；而不是相反*。为了提取函数的返回类型，我们使用了一个[通用函数类型](https://flow.org/en/docs/types/generics/#toc-function-types-with-generics) `ReturnType`。

```
type **ReturnTypeHelper** = <T>((...Array<any>) => T) => T;
type **ReturnType**<Fn> =  $Call<ReturnTypeHelper, Fn>;
```

这与前一个相同(见这里的[和](https://flow.org/try/#0C4TwDgpgBAJAogD2AJwIYGNgCULAK7IB2APAGKEB8UAvLAMKoA2jxxAKhQBScB0fAgsjQhiqQiAoBKGlTbTqsgDRRyFANwAoDaEhQ6jCKmT9MASwD2hGrEQoM2XARI6I5gGZR0Bo+u3hoOADOuCbAFla08EhomDj4RMQu7lDIEMHAvi5QAKpgACaowBCh4dZRdrGOCUkeePmFEL5+uiWWNBpQUAA+et7GZpYd3VBBIQOEQz25BUWthJoa6JaBwHoAMnD8WNYA5HQbWzuaS4QrI3AAynBsu1iX10eLy6vZAAoAIvxscLtvn9+PDRuPCEcYpCB5PDoCDITgrBoALigK2QpkIAHNdjtlPZwki5tIAN5DQIAd1MwHQAAsoJxcZYeC4iUNOuhUMF1pssAiWZ1wfErDtAXzPOyAvc2DyRZ1UgKoPTCIyIEgFiK2Ry-l84FLpbKnPLxkqVVoRXkIG5UHhGMAdSK9URkcAGppOgBfDTujQAei9osIhHMRXl-sDDQNYTa6FShXMyECUHJwBpaLcMNSeSgLkCQJBYK8hlhzJlVSshMz-iR+y5UFdmk9wNBEasqXSnCL-P1ZZcSLuVzYyiKSCROwAEhBmOYoAB1WOMPI7Gt1rQNsF1GYQTiDm2O1EY9v20vlyBIzXfAfK1a1j0aIA))，也不能给动作创建者添加类型。但是从好的方面来看——更少的语法！

# 拨打$电话

现在我们对添加类型所需的过程有了更好的理解，我们可以通过使用 Flow 的[实用程序类型](https://flow.org/en/docs/types/utilities/#toc-call) `[$Call](https://flow.org/en/docs/types/utilities/#toc-call)`(您已经在上面的操作中看到了)直接调用函数来进一步减少语法。

> `$Call<F>`是表示调用给定的[函数类型](https://flow.org/en/docs/types/functions) `F`的结果的类型。这类似于在运行时调用一个函数，但是在**类型级别**；这意味着函数类型调用是静态发生的，即不是在运行时。

这就是我们得到的(见这里的)。

```
type **ClearAction** = **$Call**<typeof clear>;
type **ResetAction** = **$Call**<typeof reset>;
type **UpdateAction** = **$Call**<typeof update, string>; // argumentstype **Action** = 
  | ClearAction
  | ResetAction
  | UpdateAction;function reducer(state: string = '', action: **Action**) {
  ...
}
```

***注:*** *以我的经验来看，* `*$Call*` *有时候有就是“不工作”的倾向。我还没有试验它来找出这背后的原因。如果你知道什么，一定要告诉我。*

# 定论

最终，一切都取决于你的选择。如果您希望动作创建者具有显式类型，您需要手动添加类型注释。如果没有，使用推断类型来简化过程并减少语法。

我希望从长远来看，你会发现这很有用。如果你用不同的方式输入 redux 代码，可能是很酷的方式，请在评论区告诉我。