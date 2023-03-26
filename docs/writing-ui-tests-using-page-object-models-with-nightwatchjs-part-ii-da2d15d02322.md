# 使用页面对象模型编写 UI 测试

> 原文：<https://medium.com/hackernoon/writing-ui-tests-using-page-object-models-with-nightwatchjs-part-ii-da2d15d02322>

我的博客记录了我作为一名技术人员进入硅谷的经历。我是一个有色人种的怪人，性别不合群，移民，曾被收养的年轻人。我来自一个非传统的[编码](https://hackernoon.com/tagged/coding)背景。在成为城市公立学校系统的一名教师之前，我在大学里学习了一些 CS 课程，最终主修了人文学科。向青少年教授 web 开发让我对编码产生了兴趣。从编程学校毕业后，我开始在旧金山的初创公司工作。我的博客一半是关于技术主题，另一半是关于平等和技术访问(我的旅程)。

在我们使用页面[对象模型](https://hackernoon.com/tagged/object-model)重写代码之前，让我们让这些测试更有趣一点。让我们`validate`为链接导航。

我们将测试我们可以点击一个链接，它将导航到新的页面。在这个新页面上，我们将通过在该页面上查找一个项目来验证导航工作。不过这一次，不是用[手动测试](http://toolsqa.com/software-testing/manual-testing/)这个过程，而是让我们变得更棒，

# [让我们实现自动化吧！](https://smartbear.com/learn/automated-testing/what-is-automated-testing/)

首先，让我们弄清楚用户需要采取什么步骤来验证我们的测试。不要跳过这一步。这一步非常重要。它允许我们思考我们希望浏览器如何与 UI 交互。这些步骤将帮助我们在我们的框架中为浏览器编写命令。

手动检查将确保我们的代码工作得更好，更少出错。因此，让我们概述一下浏览器需要接受的命令。

浏览器命令—酱实验室页面

1.  去酱油实验室的网站/导航到它的网址
2.  找到链接
3.  单击该链接

浏览器命令——酱实验室第二页

1.  转到酱油实验室的第二页/导航到新的网址
2.  在页面上查找项目

让我们将命令写入一个页面对象。创建一个名为`page_objects`的新目录和一个新的 Sauce Labs 页面对象。

```
mkdir -p /test/page_objects && touch sauceLabs.js
```

根据 [Nightwatch 的语法，](http://nightwatchjs.org/guide)让我们使用一个标准对象。让我们在文件的顶部创建一个命令列表。

```
const sauceLabCommands = {
  clickLink: function(){
    this.waitForElementVisible('@link', 1000)
      .waitForElementVisible('@link', 1000)
      .click('@link')
      .api.pause(1000)
    return this;
  }
};

module.exports = {
  commands: [sauceLabCommands],
  url: function() {
    return this.api.launchUrl + '/test/guinea-pig';
  },
  elements: {
    link: {
      selector: 'a[href="/test-guinea-pig2.html"]'
    }
  }
};
```

让我们告诉 NightwatchJS 我们的页面对象在哪里，以及我们希望它打开的 url。

```
"src_folders": [
  "test/ui_tests"
],
"page_objects_path": "test/page_objects",//the google url
"test_settings": {
  "default": {
    "launch_url": "https://saucelabs.com",
```

这是我们目前的测试。

```
module.exports = {
  "Validate Sauce Labs' Page Title": function(browser) {
     browser
       .url('https://saucelabs.com')
       .waitForElementVisible('body')
       .assert.title('I am a page title - Sauce Labs')
       .saveScreenshot('saucelabs.png')
       .end();  
   }
};
```

让我们创建两个测试，一个验证 Sauce Labs 的页面已经加载，另一个验证 Sauce Labs 的链接。让我们重写它来使用我们的页面对象。

```
require('../../nightwatch.conf.js');

module.exports = {
  "Validate Sauce Labs' Page Has Loaded": function(browser) {
    const sauceLabs = browser.page.sauceLabs();

    sauceLabs.navigate()
      .waitForElementVisible('body', 1000)
      .assert.title('I am a page title - Sauce Labs')
  },

  "Validate Sauce Labs' Link Navigation": function(browser) {
    const sauceLabs = browser.page.sauceLabs();

    sauceLabs.navigate()
      .waitForElementVisible('@link', 1000)
      .clickLink();

//navigate to the new page
//search for an element on that new page }
};
```

我们还没完。我们必须确保能够导航到搜索结果页面对象。让我们为搜索结果创建一个页面对象。

```
cd page_objects
touch sauceLabsTwo.js
```

让我们编写新的`sauceLabsTwo`页面对象。

```
module.exports = {
  url: function() {
    return this.api.launchUrl + '/test-guinea-pig2.html';
  },
  elements: {
    div: {
      selector:
        '#i_am_an_id'
    }
  }
};
```

让我们完成 2 个测试的代码编写。

```
//navigate to the new page
const sauceLabs2 = browser.page.sauceLabsTwo();//search for an element on that new page
sauceLabs2
  .waitForElementVisible('@div', 1000)
  .assert.visible('@div');browser.end();
```

好了，让我们开始测试吧

```
npm test
```

您应该看到一个自动浏览器打开，单击一个链接并导航到一个新页面。如果一切顺利，您应该会看到通过测试的绿色检查！