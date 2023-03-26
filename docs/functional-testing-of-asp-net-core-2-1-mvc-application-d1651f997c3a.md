# ASP.NET 核心 2.1 MVC 应用程序的功能测试

> 原文：<https://medium.com/hackernoon/functional-testing-of-asp-net-core-2-1-mvc-application-d1651f997c3a>

在 ASP.NET 核心 2.1 中，随着微软[的发布，建立](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)[功能](https://hackernoon.com/tagged/functional)测试项目变得更加容易。AspNetCore . MVC . testingnu get 包。在这篇文章中，我们将建立一个功能测试项目。

# 先决条件

要遵循本教程，您应该有-

1.  [。NET Core 2.1 RC1 SDK](https://www.microsoft.com/net/download/dotnet-core/sdk-2.1.300-rc1) 和
2.  [VS 代码](https://code.visualstudio.com/)或[Microsoft Visual Studio 2017](https://www.visualstudio.com/downloads/)v 15.7 预览版 1 或更新版本，安装在您的[系统上](https://hackernoon.com/tagged/system)

# 创建测试项目

创建一个文件夹并命名为`HelloWorld`，因为为什么不呢😜。打开文件夹内的 **PowerShell** 窗口(Shift + `right click`文件夹内任意位置，选择`Open PowerShell window here`，创建一个解决方案:

```
dotnet new sln
```

现在在`src`目录下创建一个基本的 **MVC** 项目，在`tests`目录下创建 **xunit** 项目；

```
dotnet new mvc -o .\src\HelloWorld.Mvcdotnet new xunit -o .\tests\HelloWorld.FunctionalTests
```

将这两个项目添加到**解决方案**:

```
dotnet sln add .\src\HelloWorld.Mvc\HelloWorld.Mvc.csprojdotnet sln add .\tests\HelloWorld.FunctionalTests\HelloWorld.FunctionalTests.csproj
```

从**功能测试**项目中引用 **MVC** 项目；

```
dotnet add .\tests\HelloWorld.FunctionalTests\HelloWorld.FunctionalTests.csproj reference .\src\HelloWorld.Mvc\HelloWorld.Mvc.csproj
```

# 编写功能测试

添加[微软。AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 到功能测试项目:

```
dotnet add .\tests\HelloWorld.FunctionalTests\HelloWorld.FunctionalTests.csproj package Microsoft.AspNetCore.Mvc.Testing -v 2.1.0-rc1-final
```

现在在 **VS 代码**或 **Visual Studio 2017 15.7 预览版 1 或更新版本**中打开项目，在 **HelloWorld 内创建一个新类。功能测试**项目并将其命名为`HomePageShould.cs`

```
using HelloWorld.Mvc;
using Microsoft.AspNetCore.Mvc.Testing;
using System.Net;
using System.Net.Http;
using System.Threading.Tasks;
using Xunit;namespace HelloWorld.FunctionalTests
{
    public class HomePageShould : IClassFixture<WebApplicationFactory<Startup>>
    {
        private readonly HttpClient _client; public HomePageShould(WebApplicationFactory<Startup> factory)
        {
            _client = factory.CreateClient();
        } [Fact]
        public async Task ReturnHttpStatusCodeOk()
        {
            var response = await _client.GetAsync("/"); Assert.Equal(HttpStatusCode.OK, response.StatusCode);
        }
    }
}
```

# 运行测试

现在运行**测试**。它应该会失败，并显示错误消息

> 消息:系统。IO.FileNotFoundException:无法加载文件或程序集“Microsoft。AspNetCore，Version=2.1.0.0，Culture=neutral，public key token = ADB 9793829 ddae 60 '。系统找不到指定的文件。

要解决，加[微软。AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) nuget 包到**测试**项目

```
dotnet add .\tests\HelloWorld.FunctionalTests\HelloWorld.FunctionalTests.csproj package Microsoft.AspNetCore.App -v 2.1.0-rc1-final
```

现在从`Test`>`Run`>`All Tests`(Visual Studio 2017)或从 **PowerShell** 运行测试-

```
dotnet test .\tests\HelloWorld.FunctionalTests\HelloWorld.FunctionalTests.csproj
```

你应该看到幸福的绿色勾号。