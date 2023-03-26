# [Laravel 提示]当验证失败时，如何将用户重定向到特定的 URL/路由

> 原文：<https://medium.com/hackernoon/laravel-tips-how-to-redirect-users-to-a-specific-url-route-when-the-validation-fails-2551149d1e4c>

![](img/c21a8ebb9608cd62edc64495c9b363f6.png)

如果在数据库中保存表单之前使用[表单请求](https://laravel.com/docs/5.7/validation#form-request-validation)来验证表单，当验证失败时，用户通常会被重定向回发起请求的页面。

例如，如果你在`/new-article`页面，在你提交你正在创建的文章后，如果用户输入的数据没有通过验证，用户将被自动重定向回这个完全相同的 URL。在大多数情况下，这是理想的行为。

最近，我在开发一个表单，需要将用户重定向回另一条路径。

我在开发一个应用程序，其中有用户可以更新的不同设置(从设置页面)，由于不希望一次发送该页面中的所有数据，验证它，然后更新用户设置，所以我创建了多个“小表单”，每个表单都在 accordion 的一个部分中，只更新一组相关的设置。因此，例如，我可以访问`/settings`来查看所有部分/卡片折叠的设置页面，或者我可以访问(`/settings/personal-info`)来打开该特定部分的设置页面。

所以我可能会访问`/settings/personal-info`，然后我会向下滚动到另一个部分来更新它(比如隐私)。在这种情况下，如果验证失败，用户将被重定向到`/settings/personal-info`而不是`/settings/privacy`。

PS:我知道这可能不是处理这种情况的最佳方式，但是请耐心听我说，我们的目标是解释如何将用户重定向到不同的路径

幸运的是，我们可以通过在表单请求类中设置一些属性，将用户重定向到所需的 URL/route。

看一看`[/vendor/laravel/framework/src/Illuminate/Foundation/Http/FormRequest.php](https://laravel.com/api/5.7/Illuminate/Foundation/Http/FormRequest.html)`

你会发现这三个属性:

```
/**
* The URI to redirect to if validation fails.
*
* @var string
*/
protected $redirect;/**
* The route to redirect to if validation fails.
*
* @var string
*/
protected $redirectRoute;

/**
* The controller action to redirect to if validation fails.
*
* @var string
*/
protected $redirectAction;
```

所以我们需要做的就是给这个属性中的一个(仅仅一个)赋予我们想要的值。

`protected $redirectRoute = 'get_all_articles';`

PS:如果你想知道如果你设置了多个会发生什么，看看同一个类上受保护的函数`getRedirectUrl()`方法。

在这种情况下，您可能会遇到的一个问题是，当您希望向 redirectRoute 传递一个值，但又希望传递一个带有类似

`Route::get('articles/{article}','ArticleController@view')→name('view_article');`

解决方案是在构造函数中设置该属性的值，如下所示:

```
public function __construct(){
	$id = ….;
	$this->redirect = route('view_article', $id);
}
```

注意，我在这里使用的是$redirect，而不是`$redirectRoute`。

我希望这个提示对你有用

PS:我正在写一本关于如何给你的 Laravel CRUD 应用程序添加测试的电子书。如果你有兴趣，可以去 https://laraveltesting101.com/的[看看](https://laraveltesting101.com/)

[![](img/52bf9e84126f483254abfde8427668f0.png)](https://laraveltesting101.com/)