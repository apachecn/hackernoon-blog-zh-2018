# Android recycle view onItemClickListener & getAdapterPosition():更好的方法

> 原文：<https://medium.com/hackernoon/android-recyclerview-onitemclicklistener-getadapterposition-a-better-way-3c789baab4db>

## 一个更简单的方法来顺利完成那件事。

![](img/36ccfd422ce17964fa372b4ee2c31fcd.png)

Photo by [Émile Perron](https://unsplash.com/photos/xrVDYZRGdw4?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

**更新 1** :包含 lambda 函数作为替代。
**更新 2** :包含了 GitHub 上 [**实现实例 App 的链接。**](https://github.com/RohitSurwase/RvClickListenerExample)

> **TL；DR
> 最好通过回收审查。 **ViewHolder** 使用视图。在你的视图中设置标签()**，然后从视图中设置**getAdapterPosition()**(…和许多其他的东西)。 **getTag()** 即 **ViewHolder 的**对象在你的活动/片段/视图里面。
> [**在 GitHub 上实现示例 App**](https://github.com/RohitSurwase/RvClickListenerExample)

# **最流行的方式；**问题

我知道您一定已经找到了为 RecyclerView 适配器获取 onItemClickListener()的方法。但有时我们思考的方式并不总是正确的或更好的方式。每当有人试图在他们的活动/片段/视图中找到 getAdapterPosition()的方法时。他或她遇到了众所周知的以 ***视图*** 和 ***位置*** 为参数创建自定义界面的模式。

```
public **interface ItemClickListener** {
    void onItemClick(View view, int position);
}
```

稍后，我们使用公共 setter 初始化 ItemClickListener 实例。

```
private **ItemClickListener** onItemClickListener;...public void setItemClickListener(**ItemClickListener** clickListener) {
    onItemClickListener = clickListener;
}
```

或者…通过构造函数传递 ItemClickListener 实例。

Constructor of RecyclerViewAdapter

最后我们把我们的 ***视图*** 和 ***适配器位置*** 传递给监听器。

我们对剩下的部分了如指掌。大多数文章都描述了同样的事情。但是有一个问题。

当我们说 *itemView* ***的时候。setOnClickListener(新视图。onclick listener()……***

> 它创建了一个匿名的内部类，实现了我们的 ItemClickListener 监听器。

```
**class** AnonymousItemClickListener **implements ItemClickListener**{
    @Override
    public void onItemClick(View view, int position) {

    }
}
```

我们最终为一个点击监听器创建并初始化了多个匿名内部类。

```
new AnonymousItemClickListener(){
        @Override
        public void onItemClick(View view, int position) {

        }
    };
```

我以前也是这样。因为这是我们在网上看到的。

[](https://psiloveyou.xyz/introverts-decoded-why-am-i-still-single-a046d1596d1e) [## 内向者解码:恋爱中未说出口的困境

### 他们从不告诉任何人的事情。

psiloveyou.xyz](https://psiloveyou.xyz/introverts-decoded-why-am-i-still-single-a046d1596d1e) 

> **更新:**
> 正如 [Sachin Chandil](https://medium.com/u/f02e354ffc0c?source=post_page-----3c789baab4db--------------------------------) 在评论部分所建议的，我们可以使用 Java8 的 **Lambda 函数**来摆脱编译时的匿名类创建。Lambda 函数与 SAM 类型(具有单一抽象方法的接口)一起工作，它不创建匿名内部类的原因是 Java 7 中引入的 InvokeDynamic 指令。因此，使用 Lambdas，我们可以将上述函数转换如下:

```
itemView.setOnClickListener(view -> onItemClickListener.onItemClick(view, getAdapterPosition());
```

## 由于某些原因，你不能使用 Java8 的 Lambda 函数，那么继续下去，因为下面的方法对你来说仍然是一个更好的选择。

# 更好的方法；解决方案

但是，最近我从一位 [**同事**](/@aalap_shah) 那里得到一个建议，用一种更好更轻便的方式来实现它。让我们看看*我们如何在我们的活动/片段/视图中获取* ***适配器位置、*** ***项目 id***(…以及其他许多东西)*。*

因此，我们将使用**视图，而不是创建新的自定义界面。OnClickListener** 并按照我们以前的方式初始化它。

```
private **View.OnClickListener** onItemClickListener;...public void setItemClickListener(**View.OnClickListener** clickListener) {
    onItemClickListener = clickListener;
}
```

并且我们将使用 **setTag()** 通过 **itemView** 将 ***ViewHolder*** 的*实例*传递给监听器。

```
private class MyViewHolder extends RecyclerView.ViewHolder {
    public MyViewHolder(View itemView) {
        super(itemView);
        **itemView.setTag(this);**
    }
}
```

这样，我们不需要创建匿名内部类，因为我们可以直接给出 **onItemClickListener** 的引用

```
private class MyViewHolder extends RecyclerView.ViewHolder {
    public MyViewHolder(View itemView) {
        super(itemView);
        itemView.setTag(this);
        **itemView.setOnClickListener(onItemClickListener);**
    }
}
```

最后我们可以从****中得到 ***适配器位置* ***和****项目 id*(…以及其他许多东西)*在我们的活动/片段/视图****ViewHolder****实例中。*******

```
RecyclerView.ViewHolder **viewHolder** = (RecyclerView.ViewHolder) **view.getTag()**;int **position** = viewHolder.**getAdapterPosition()**;
// viewHolder.**getItemId()**;
// viewHolder.**getItemViewType()**;
// viewHolder.**itemView**;
```

**简单！**不是吗？请在评论区告诉我你对此的看法和建议。

如果你还有些疑惑，下面是 GitHub **上 [**实现示例 App 的源代码。**](https://github.com/RohitSurwase/RvClickListenerExample)**

感谢阅读！
**Rohit Surwase**

如果你喜欢这篇文章，拍手拍手拍手👏👏👏尽可能多的次数。

太喜欢了！介质允许高达 **50 拍**。
也在 [**Twitter**](https://twitter.com/Rohit5rss) 上发布一条**的推文。**

让我们成为 [***LinkedIn***](http://in.linkedin.com/in/rohitss5) ，[***Medium***](/@rohitss)***，***[**Twitter**](https://twitter.com/Rohit5rss)**和[***GitHub***](http://github.com/RohitSurwase/)***。*****

> **我的最新故事**

**[](https://proandroiddev.com/best-architecture-for-android-mvi-livedata-viewmodel-71a3a5ac7ee3) [## Android 最佳架构:MVI + LiveData + ViewModel = ❤️

### MVVM 和 MVI 架构模式的精华合二为一，成为任何 Android 项目的完美架构。

proandroiddev.com](https://proandroiddev.com/best-architecture-for-android-mvi-livedata-viewmodel-71a3a5ac7ee3)**