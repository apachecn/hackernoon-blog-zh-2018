# Vue 中的属性与数据

> 原文：<https://medium.com/hackernoon/props-vs-data-in-vue-b619b06ae526>

![](img/d91a70bf9aaba7f95653c1358f1d2f3d.png)

Photo by [Tim Swaan](https://unsplash.com/@timswaanphotography?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Vue 有两种不同的存储变量的方式， **props** 和 **data** 。

起初，这些可能会令人困惑，因为它们看起来像是在做类似的事情，并且不清楚什么时候使用其中一个，什么时候使用另一个。

那么道具和数据有什么[区别](https://hackernoon.com/tagged/difference)？

`**Data**` **是每个组件的私有内存，你可以在这里存储任何你需要的变量。Props 是将数据从父组件传递到子组件的方式。**

在本文中，您将了解到:

*   道具是什么，为什么**这些数据只向下**流动，不向上流动
*   `data`选项的用途
*   什么是**反应性**
*   如何避免**道具和`data`之间的**命名碰撞
*   如何将**道具和数据一起**用于娱乐和盈利💰

# 什么是道具？

在 Vue 中， [props](https://vuejs.org/v2/guide/components-props.html) (或属性)，是我们将数据从父[组件](https://hackernoon.com/tagged/component)向下传递到其子组件的方式。

当我们用组件构建我们的应用程序时，我们最终会构建一个叫做树的数据结构。类似于家谱，你有:

*   双亲
*   儿童
*   祖先
*   和后代

数据从最顶端的根组件开始沿着树向下流动。有点像基因是如何从一代传到下一代的，**父组件将道具传递给他们的孩子**。

在 Vue 中，我们在代码的`<template>`部分向组件添加道具:

```
<template>
  <my-component cool-prop="hello world"></my-component>
</template>
```

在这个例子中，我们传递给道具`cool-prop`一个值`"hello world"`。我们将能够从`my-component`内部访问这个值。

然而，当我们从组件内部访问道具时，我们并不拥有它们，**所以我们不能改变它们**(就像你不能改变你父母给你的基因一样)。

> 注意:虽然可以改变组件的属性，但这不是一个好主意。您最终也改变了父节点正在使用的值，这可能会导致很多混乱。

但是如果我们不能改变变量，我们就有点卡住了。

这就是`data`的用武之地！

# 什么是数据？

[数据](https://vuejs.org/v2/guide/instance.html#Data-and-Methods)是各部件的*内存*。这是您存储数据(因此得名)和您想要跟踪的任何其他变量的地方。

如果我们正在构建一个计数器应用程序，我们将需要跟踪计数，因此我们将向我们的`data`添加一个`count`:

```
<template>
  <div>
    {{ count }}
    <button @click="increment">+</button>
    <button @click="decrement">-</button>
  </div>
</template><script>
export default {
  name: 'Counter',
  data() {
    return {
      count: 0,
    };
  },
  methods: {
    increment() {
      this.count += 1;
    },
    decrement() {
      this.count -= 1;
    }
  }
}
</script>
```

这些数据是私有的，并且**仅供组件本身**使用。其他组件无法访问它。

> *注意:同样，其他组件也可能访问这些数据，但是出于同样的原因，这样做真的不是个好主意！*

如果需要向组件传递数据，可以使用 props 沿树向下传递数据(传递给子组件)，或者使用 events 沿树向上传递数据(传递给父组件)。

# 道具和数据都是反应性的

有了 Vue，你不需要考虑太多组件什么时候会自我更新，并在屏幕上呈现新的变化。

**这是因为 Vue 是无功的。**

不是每次你想改变什么的时候都打电话给`setState`，你只是改变事情！只要你在更新一个**反应属性**(道具，计算道具，以及`data`中的任何东西)，Vue 知道当它改变时要注意。

回到我们的柜台应用程序，让我们仔细看看我们的方法:

```
methods: {
  increment() {
    this.count += 1;
  },
  decrement() {
    this.count -= 1;
  }
}
```

我们所要做的就是更新`count`，Vue 会检测到这个变化。然后，它用新值重新呈现我们的应用程序！

Vue 的反应系统有更多的细微差别，我相信如果你想用 Vue 高效工作，很好地理解它是非常重要的。如果你想更深入地了解 Vue 的反应系统，这里还有一些需要学习的东西。

# 避免命名冲突

Vue 做的另一件大事是让开发变得更好。

让我们在组件上定义一些属性和数据:

```
export default {
  props: ['propA', 'propB'],
  data() {
    return {
      dataA: 'hello',
      dataB: 'world',
    };
  }
};
```

如果我们想在一个方法中访问它们，我们不必做`this.props.propA`或`this.data.dataA`。Vue 让我们完全省略`props`和`data`，留给我们更干净的代码。

我们可以使用`this.propA`或`this.dataA`来访问它们:

```
methods: {
  coolMethod() {
    console.log(this.propA);
    console.log(this.dataA);
  }
}
```

因此，如果我们不小心在我们的`props`和`data`中使用了相同的名字，我们就会遇到问题。

如果发生这种情况，Vue 会给你一个警告，因为它不知道你想访问哪一个！

```
export default {
  props: ['secret'],
  data() {
    return {
      secret: '1234',
    };
  },
  methods: {
    printSecret() {
      console.log(this.secret);
    }
  }
};
```

使用 Vue 的真正魔力发生在你开始一起使用道具和`data`的时候。

# 一起使用道具和数据

现在我们已经看到了道具和数据是如何不同的，让我们通过构建一个基本的应用来看看为什么**我们需要它们两者**。

比方说，我们正在建立一个社交网络，我们正在处理个人资料页面。我们已经构建了一些东西，但现在我们必须添加用户的联系信息。

我们将使用一个名为`ContactInfo`的组件来显示这些信息:

```
// ContactInfo
<template>
  <div class="container">
    <div class="row">
      Email: {{ emailAddress }}
      Twitter: {{ twitterHandle }}
      Instagram: {{ instagram }}
    </div>
  </div>
</template><script>
export default {
  name: 'ContactInfo',
  props: ['emailAddress', 'twitterHandle', 'instagram']
};
</script>
```

`ContactInfo`组件获取道具`emailAddress`、`twitterHandle`和`instagram`，并显示在页面上。

我们的个人资料页面组件`ProfilePage`如下所示:

```
// ProfilePage
<template>
  <div class="profile-page">
    <div class="avatar">
      <img src="user.profilePicture" />
      {{ user.name }}
    </div>
  </div>
</template><script>
export default {
  name: 'ProfilePage',
  data() {
    return {
      user: {
        name: 'John Smith',
        profilePicture: './profile-pic.jpg',
        emailAddress: 'john@smith.com',
        twitterHandle: 'johnsmith',
        instagram: 'johnsmith345',
      },
    }
  }
};
</script>
```

我们的`ProfilePage`组件当前显示用户的个人资料图片以及他们的名字。它也有用户数据对象。

我们如何将数据从父组件(`ProfilePage`)传递到子组件(`ContactInfo`)？

我们必须使用道具来传递这些数据。

首先我们需要将我们的`ContactInfo`组件导入到`ProfilePage`组件中:

```
<script>
import ContactInfo from './ContactInfo.vue';export default {
  name: 'ProfilePage',
  components: {
    ContactInfo,
  },
  data() {
    return {
      user: {
        name: 'John Smith',
        profilePicture: './profile-pic.jpg',
        emailAddress: 'john@smith.com',
        twitterHandle: 'johnsmith',
        instagram: 'johnsmith345',
      },
    }
  }
};
</script>
```

其次，我们必须将组件添加到我们的`<template>`部分:

```
// ProfilePage
<template>
  <div class="profile-page">
    <div class="avatar">
      <img src="user.profilePicture" />
      {{ user.name }}
    </div>
    <contact-info
      :email-address="user.emailAddress"
      :twitter-handle="user.twitterHandle"
      :instagram="user.instagram"
    />
  </div>
</template>
```

现在`ContactInfo`需要的所有用户数据将从`ProfilePage`流向组件树并进入`ContactInfo`！

我们将数据保存在`ProfilePage`而不是`ContactInfo`中的原因是简档页面的其他部分需要访问用户对象。

由于**数据只向下流动**，这意味着我们必须将数据放在组件树中足够高的位置，这样它才能向下流动到它需要去的所有地方。

*如果你喜欢这篇文章或者有任何意见，请回复我* [*这篇推文*](https://twitter.com/MichaelThiessen/status/1050425430963052546) *！*

*原载于*[*michaelnthiessen.com*](http://michaelnthiessen.com/vue-props-vs-data/)*。*