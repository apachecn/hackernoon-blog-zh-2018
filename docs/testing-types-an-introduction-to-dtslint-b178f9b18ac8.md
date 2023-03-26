# 测试类型:dtslint 简介

> 原文：<https://medium.com/hackernoon/testing-types-an-introduction-to-dtslint-b178f9b18ac8>

这个单元测试有什么奇怪的地方吗？

```
it('should square 4', () => {
  square(4);
});
```

当然可以。*它没有断言任何东西！*是否正确执行`square`并不重要。只要函数没有抛出异常，这个测试就会通过。

这不太好。如果检查一下`square(4)`的返回值，测试会好很多:

```
it('should square 4' () => {
  expect(square(4)).to.equal(16);
});
```

尽管第一个例子很疯狂，但这正是传统上测试[中的类型声明的方式。只要类型检查器没有发现任何错误，类型是什么并不重要。特别是在有](https://github.com/DefinitelyTyped/DefinitelyTyped)`[any](https://www.typescriptlang.org/docs/handbook/basic-types.html#any)` [类型](https://www.typescriptlang.org/docs/handbook/basic-types.html#any)的情况下，这导致了一些弱测试。弱测试导致不精确和不准确的类型化，并且它们使重构类型声明变得可怕。

微软最近推出了一个新工具， [dtslint](https://github.com/Microsoft/dtslint) ，它使得类型声明测试中的断言成为可能。这篇文章的其余部分解释了如何使用它来将测试的所有好处带给类型声明文件。

# 没有 dtslint 的测试

下面是来自`_.pluck`的[下划线测试](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/3289762cca59308bf092e4b49ea2242ef27fc23e/types/underscore/underscore-tests.ts#L173-L174)的几行，我在之前已经[写过了:](/@danvdk/a-typed-pluck-exploring-typescript-2-1s-mapped-types-c15f72bf4ca8)

请注意，返回类型缺少断言。这实际上是检查是否有一个名为`_.pluck`的函数，并且它接受一个列表和一个字符串作为参数。

返回类型应该是`string[]`，但是是`any[]`。太糟糕了！怎样才能让测试失败？

# 一个测试

`dtslint`来救援了！为了检查对`_.pluck`调用的返回类型，我们可以使用一个`// $ExpectType`断言:

当我们在这个测试上运行`tsc`时，它通过了。但是当我们在它上面运行`dtslint`时，我们得到如下结果:

```
ERROR: 2:1  expect  Expected type to be:
  string[]
got:
  any[]
```

Tada！抓住了！

我们可以使用映射类型使声明更加精确[:](/@danvdk/a-typed-pluck-exploring-typescript-2-1s-mapped-types-c15f72bf4ca8)

现在我们从`dtslint`得到以下输出:

```
Test with 2.8
Test with 2.7
Test with 2.6
Test with 2.5
Test with 2.4
Test with 2.3
Test with 2.2
Test with 2.1
Test with 2.0
Error: /Users/danvk/github/dtslint-post/types/index.d.ts:1:33
ERROR: 1:33  expect  Compile error in typescript@2.0 but not in typescript@2.1.
Fix with a comment '// TypeScript Version: 2.1' just under the header.
Cannot find name 'keyof'.
```

TypeScript 2.1+通过了测试，但 TypeScript 2.0 未通过测试。这是有意义的，因为`keyof`是在 TypeScript 2.1 中引入的[。在 TS 2.1 之前，不可能如此精确地输入`pluck`。所以我们唯一真正的选择是使用建议的注释要求更新的版本:](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-1.html)

这是类型声明难以维护的另一个原因。类型声明中实际上有三个独立的版本:

*   库的版本
*   打字的版本
*   TypeScript 编译器的版本

FlowTyped 选择[显式地对这个](https://github.com/flowtype/flow-typed/tree/614bf49aa8b00b72c41caab1120094bc10fb9476/definitions/npm/underscore_v1.x.x)建模，而 DefinitelyTyped 没有。

# 测试重构

假设我们正在处理 [lodash 的](https://lodash.com/docs#map) `[map](https://lodash.com/docs#map)` [函数](https://lodash.com/docs#map)的类型声明:

```
export function map<U, V>(array: U[], fn: (u: U) => V): V[];
```

你用这个很像`Array.prototype.map`:

```
_.map([1, 2, 3], x => x * x);  // returns [1, 4, 9].
```

Lodash 没有`_.pluck`功能。相反，它增加了一个`_.map`的变体:

我们应该在类型声明中对此建模，但是改变如此重要的函数的类型是很可怕的！这是编写测试的最好理由之一:它们让你自信地重构。`dtslint`让你对类型声明做同样的事情。

这里有一个针对`_.map`的 dtslint 测试，它涵盖了新旧声明:

现在我们可以为`map`的声明添加一个重载:

当`dtslint`过去时，我们可以确信我们已经添加了新的功能*并且*避免了改变现有的行为。

# 测试回调参数

回调在 [JavaScript](https://hackernoon.com/tagged/javascript) 中无处不在，类型声明准确地建模它们的参数非常重要。这里也有帮助:如果我们注意格式，我们可以断言回调参数的类型。

`_.map`实际上向其回调传递了三个参数。这个代码片段测试了它们是否都推断出了正确的类型:

如果我们改变这些`$ExpectType`行中的任何一行，我们都会得到一个错误。(这通常是一个很好的检查！)

# 测试“这”的类型

众所周知，[很难](https://twitter.com/bendhalpern/status/578925947245633536?lang=en)知道`this`在 JavaScript 中指的是什么。但是 [TypeScript](https://hackernoon.com/tagged/typescript) 可以帮忙！如果一个库在其回调中操纵`this`，那么类型声明应该对其建模。

如果你已经做到了这一步，你就不会惊讶地发现`dtslint`也能帮上忙！只需为`this`写一个类型断言:

# 结论

处理不准确或不精确的类型声明可能是使用 TypeScript 最令人沮丧的方面之一。它们会引入虚假的错误，或者通过在你意想不到的地方引入`any`类型，给你一种不必要的自信。

测试是改进现有代码库的关键，而`dtslint`为 TypeScript 的类型语言带来了许多好处。它让您可以确定现有的行为，以便您可以放心地重构类型声明。它甚至可以让你用类型声明文件进行[测试驱动开发](https://en.wikipedia.org/wiki/Test-driven_development)！

`dtslint`已经在今天的[明确类型的](https://github.com/DefinitelyTyped/DefinitelyTyped)回购中使用。所以如果你写类型声明，请写一些类型断言！如果你要改变现有的类型声明，请为现有的行为编写断言。

我希望，从长远来看，`dtslint`将为所有 TypeScript 用户带来质量显著提高的类型声明。这意味着更好的打字体验，即使你不知道`dtslint`的存在！

*查看* [*这个回购*](https://github.com/danvk/dtslint-post/) *看看这篇文章中的所有代码示例。如果你想在 DefinitelyTyped 之外使用 dtslint，可以看看 Paul krbitz 的伟大文章* *。*

如果你喜欢这篇文章，你可能也会喜欢我的书， [*有效的打字稿:62 种改善你打字稿的具体方法*](https://www.amazon.com/Effective-TypeScript-Specific-Ways-Improve/dp/1492053740)(O ' Reilly 2019)。这篇文章是第 52 条的基础:注意测试类型的陷阱。