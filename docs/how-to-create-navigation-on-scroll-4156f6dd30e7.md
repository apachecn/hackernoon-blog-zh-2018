# 如何在滚动条上创建导航

> 原文：<https://medium.com/hackernoon/how-to-create-navigation-on-scroll-4156f6dd30e7>

Kolosek 团队最近面临的一个挑战是，我们需要创建一个只在向上滚动时才显示的导航。

**我们是怎么解决的？**

首先，创建了表单的 HTML。请记住，在创建表单时，使用了 Bootstrap。这里我们已经使用 CSS 和 jQuery 解决了这个问题。

应该学习的一些有用的 CSS 属性是[相对字体大小](https://kolosek.com/css-relative-font-size/)、[列](https://kolosek.com/css-columns/)、[相对和绝对定位](https://kolosek.com/css-position-relative-vs-position-absolute/)。现在，让我们继续。

然后，我们创建了静态导航和应该在向上滚动时出现的导航。

```
<nav class="main-navigation navbar navbar-expand-lg">
  <a class="navbar-brand" href="/"></a>
  <button class="navbar-toggler custom-toggler" type="button" data-toggle="collapse" data-target="#navbarNavDropdown" aria-controls="navbarNavDropdown" aria-expanded="false" aria-label="Toggle navigation">
    <span class="navbar-toggler-icon"></span>
  </button>
  <div class="collapse navbar-collapse" id="navbarNavDropdown">
    <ul class="navbar-nav ml-auto">
      <li class="nav-item">
        <a class="nav-link" href="#">Home <span class="sr-only">(current) </span></a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#">First link</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#">Second link</a>
      </li>
    </ul>
   </div>
</nav>
```

我们创建了两个相同的导航，一个在另一个的下面。区别在于:一个应该在向上滚动时出现，并有不同的背景，而另一个应该保持静止在页面顶部。

为了做到这一点，这些导航必须有不同的标识符。

卷轴上出现的导航有了类”。导航条滚动条”，而静态导航有一个不同名称的类，这样风格就不会混淆。

我们使用以下 SCSS 来导航卷轴上显示的内容:

```
.navigation-bar-scroll {
  position: fixed;
  left: 0;
  right: 0;
  top: 0;
  z-index: 1000; &.is-hidden {
    opacity: 0;
    -webkit-transform: translate(0,-60px);
    transition: .5s ease;
  } &.is-visible {
    opacity: 1;
    -webkit-transform: translate(0,0);
    transition: .5s ease;
  }
}
```

然后，我们使用下面的 JQuery 代码来实现“向上滚动时显示”功能。

```
var previousScroll = 0;$(window).scroll(function(){ var currentScroll = $(this).scrollTop(); if (currentScroll > 0 && currentScroll < $(document).height() - $(window).height()){ if (currentScroll > previousScroll){
      window.setTimeout(hideNav, 300); } else if (currentScroll == previousScroll) {
      window.setTimeout(visibleNav, 300);
    }
      else {
       window.setTimeout(showNav, 300);
    } previousScroll = currentScroll;
   } /* make the scroll navigation disappear when it is scrolled on top */ if ($(window).scrollTop() <= 150) {
        $('#navigation-scroll').css('display', 'none');
    } else {
      $('#navigation-scroll').css('display', 'flex');
    } }); function hideNav() {
      $(".main-navigation-scroll").removeClass("is-visible").addClass("is-hidden");
    }
    function showNav() {
      $(".main-navigation-scroll").removeClass("is-hidden").addClass("is-visible");
      $(".main-navigation-scroll").addClass("shadow");
    }});
```

我们实现了**向上滚动**时出现导航的效果。当滚动到顶部时，这个导航栏将消失，而第二个静态导航栏将位于页面的顶部。看起来很酷，不是吗？

这里是我们最后的提示:导航**应该是相同的**，这样用户就不会混淆。您可以添加的唯一区别是向上滚动时导航的背景颜色不同，因此导航的链接在包含大量内容的页面上清晰可见。

这只是你可以改进网站设计的众多方法之一。

希望这篇简短的博文对你有所帮助！

**感谢您的阅读！:)**

*原载于 2018 年 7 月 24 日 kolosek.com**的* [*。*](https://kolosek.com/navigation-on-scroll/?utm_source=me)