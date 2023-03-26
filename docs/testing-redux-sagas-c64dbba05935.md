# 测试 Redux 传奇

> 原文：<https://medium.com/hackernoon/testing-redux-sagas-c64dbba05935>

尽管我很想从这篇文章中获得荣誉，但这篇文章大部分是由约翰·皮普金写的。它真的很好，对我帮助很大，我想和全世界分享它。

[点击这里阅读他的完整文章](/@johnpipkin_79120/conceptualizing-unit-tests-with-redux-sagas-71c49fd8b08f?source=linkShare-1ef30430402c-1533944212)

所以测试生成器时，最好把它们想象成一个循环，当你让它们执行时，它们就会执行。

# 第一步

```
it(‘should return 6’, () => {
 // we’ve set up the generator, but we haven’t called next yet so we’re not at a yield
 const gen = count()expect(gen.next().value).toEqual(call(addNumber, number, 1))expect(gen.next(1).value).toEqual(call(addNumber, number, 2))expect(gen.next(3).value).toEqual(call(addNumber, number, 3))expect(gen.next(6).value).toEqual(put(something(6)))
})function *count() {
 -> // We haven’t told the generator to exicute anything yet so we’re not at a yield
 let number = 0number = yield call(addNumber, number, 1)
 number = yield call(addNumber, number, 2) //3
 number = yield call(addNumber, number, 3) //6
 yield.put(something(number))
}
```

在这一步中，我们还没有告诉生成器做任何事情，所以我们没有要测试的东西。

# 第二步

```
it(‘should return 6’, () => {
 const gen = count()// This next is called, we’re seeing what the yield will be
 expect(gen.next().value).toEqual(call(addNumber, number, 1))expect(gen.next(1).value).toEqual(call(addNumber, number,2))expect(gen.next(3).value).toEqual(call(addNumber, number, 3))expect(gen.next(6).value).toEqual(put(something(6)))
})function *count() {
 let number = 0// Generator is paused here, waiting. The yield call(addNumber, number, 1) hasn’t executed
 -> number = yield call(addNumber, number, 1)
 number = yield call(addNumber, number, 2) //3
 number = yield call(addNumber, number, 3) //6
 yield.put(something(number))
}
```

这里我们已经执行了`gen.next()`。我们已经告诉生成器使用收益声明并等待。所以现在我们在`->`暂停了，但是我们并没有真正运行代码。

当我们在这里得到`gen.next().value`时，我们只是得到收益率会做什么。

# 第三步

```
it(‘should return 6’, () => {
 const gen = count()expect(gen.next().value).toEqual(call(addNumber, number, 1))// called next here, this evaluated the previous line. We pass in the return value from the last line
 expect(gen.next(1).value).toEqual(call(addNumber, number, 2))expect(gen.next(3).value).toEqual(call(addNumber, number, 3))expect(gen.next(6).value).toEqual(put(something(6)))
})function *count() {
 let number = 0number = yield call(addNumber, number, 1)
 // We evaluated the previous line, now were waiting at the this yield
 -> number = yield call(addNumber, number, 2) 
 number = yield call(addNumber, number, 3) //6
 yield.put(something(number))
}
```

这里我们又调用了`gen.next()`。生成器看到该命令，并执行其暂停处的 yield，然后继续下一个“yield”语句。它将在这里暂停`->`。`gen.next()`告诉 yield 语句它正在执行，我们可以把我们想要的从那里返回的内容传递给调用
我们在`yield call(addNumber, number, 1)`暂停，我们想要它返回`1`，所以当我们实际执行那一行时，我们把它传递进去。

# 第四步

```
it(‘should return 6’, () => {
 const gen = count()expect(gen.next().value).toEqual(call(addNumber, number,1))expect(gen.next(1).value).toEqual(call(addNumber, number, 2))// Called next again, we evaluated the call to (number, 2) and specifiy its return value in this next
 expect(gen.next(3).value).toEqual(call(addNumber, number, 3))expect(gen.next(6).value).toEqual(put(something(6)))
})function *count() {
 let number = 0number = yield call(addNumber, number, 1)
 number = yield call(addNumber, number, 2) //3
 -> number = yield call(addNumber, number,3) //6
 yield.put(something(number))
}
```

这一步和上一步一样，只是为了说明我在说什么。对`call(addNumber, number, 2)`的让步应该返回`3`，所以我们把它传递给对`gen.next(3)`的调用，因为我们已经告诉生成器执行那行代码，我们希望它返回`3`。

# 第五步

```
it(‘should return 6’, () => {
 const gen = count()expect(gen.next().value).toEqual(call(addNumber, number, 1))expect(gen.next(1).value).toEqual(call(addNumber, number, 2))expect(gen.next(3).value).toEqual(call(addNumber, number, 3))// Called next again, we evaluated the call to (number, 3) and specifiy its return value in this next.
 // I’ve intentionally returned the wrong value from the call to (addNumber, number, 3) to illustrate how the return in next works
 expect(gen.next(8).value).toEqual(put(something(8)))
})function *count() {
 let number = 0number = yield call(addNumber, number, 1)
 number = yield call(addNumber, number, 2) //3
 number = yield call(addNumber, number, 3) //6
 -> yield.put(something(number))
}
```

我们再次调用了`gen.next()`,所以现在我们在等待卖出。在这个例子中，你可以看到我故意在`gen.next()`中返回了错误的值‘8’。这是为了说明 next 接受前一次执行的返回值。这种情况下基本上是模拟数据。现在我们看到我们暂停的收益率是`put(number)`。因为本例中的`number`是`8`(因为那是我们告诉`call(addNumber, number, 3)`要返回的，那就是我们要找的。

# 第六步

```
it(‘should return 6’, () => {
 const gen = count()expect(gen.next().value).toEqual(call(addNumber, number, 1))expect(gen.next(1).value).toEqual(call(addNumber, number, 2))expect(gen.next(3).value).toEqual(call(addNumber, number, 3))expect(gen.next(8).value).toEqual(put(something(8)))// Finally call next again to see there are no more yields
 expect(gen.next().done).toBeTruthy()
})function *count() {
 let number = 0number = yield call(addNumber, number, 1)
 number = yield call(addNumber, number, 2) //3
 number = yield call(addNumber, number, 3) //6
 yield.put(something(number))
 ->
}
```

最后，我们再次调用`gen.next()`。既然没有更多的收益需要等待，我们可以看到传奇已经结束。