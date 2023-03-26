# Go 教程中的高级测试

> 原文：<https://medium.com/hackernoon/advanced-testing-in-go-tutorial-28b89d3a813>

欢迎各位编码员！在这个[教程](https://hackernoon.com/tagged/tutorial)中，我们将看看 Go 语言开发者在官方 [Golang](https://hackernoon.com/tagged/golang) /go repo 中使用的一些更高级的测试实践。

我觉得这种实际研究生产系统中所做的事情的方法，将有望给你一些关于测试你自己的生产级围棋程序的最佳方法的见解。

> *如果你刚刚开始测试你的基于围棋的程序，那么我建议你看看我的另一个教程:* [*围棋测试介绍*](https://tutorialedge.net/golang/intro-testing-in-go/)

# 视频教程

本教程有视频格式。如果你希望支持我和我的内容，那么请喜欢和订阅！:D

# 用表格驱动测试实现良好的覆盖率

让我们从`strings`包开始我们的旅程。如果您看一下`src/strings/`中的`strings_test.go`文件的顶部，您应该会看到许多已定义和填充的数组。

例如，看看`lastIndexTests`，它是一个`IndexTest`类型的数组:

```
var lastIndexTests = []IndexTest{
	{"", "", 0},
	{"", "a", -1},
	{"", "foo", -1},
	{"fo", "foo", -1},
	{"foo", "foo", 0},
	{"foo", "f", 0},
	{"oofofoofooo", "f", 7},
	{"oofofoofooo", "foo", 7},
	{"barfoobarfoo", "foo", 9},
	{"foo", "", 3},
	{"foo", "o", 2},
	{"abcABCabc", "A", 3},
	{"abcABCabc", "a", 6},
}
```

该数组用于测试`strings.go`文件中的`LastIndex`函数，其中包含大量的正负情况。这些`IndexTest`元素中的每一个都有一个标准的`string`、一个分隔符和一个`out`整数值，并有一个如下所示的`struct`:

```
type IndexTest struct {
	s   string
	sep string
	out int
}
```

然后这些测试由`TestLastIndex()`函数触发，该函数运行所有这些测试用例，并检查从`lastIndex`函数返回的结果是否与数组中列出的预期结果相匹配。

对于许多不同的函数，这种相同的做法被重复了许多次，这有助于保证当对这些函数进行任何代码更改时，预期的功能不会改变。

# 使用 testdata 目录

在某些情况下，您不能像上面的例子那样将预期的输入和输出指定为一个元素数组。您可能正在尝试测试如何在文件系统上读写文件，或者如何解析专有数据格式等等。

如果是这种情况，那么一个选择是创建一个`testdata`目录，并在该目录中存储测试可能需要的任何文件。

在`src/archive/tar/`下的标准库中可以再次找到一个很好的例子，其中定义了一个`testdata/`目录，并包含许多随后用于测试的`.tar`文件。

使用这些文件的一些相当复杂的测试例子可以在`reader_test.go`文件中找到。

```
func TestReader(t *testing.T) {
	vectors := []struct {
		file    string    // Test input file
		headers []*Header // Expected output headers
		chksums []string  // MD5 checksum of files, leave as nil if not checked
		err     error     // Expected error to occur
	}{{
		file: "testdata/gnu.tar",
		headers: []*Header{{
			Name:     "small.txt",
			Mode:     0640,
			Uid:      73025,
			Gid:      5000,
			Size:     5,
			ModTime:  time.Unix(1244428340, 0),
			Typeflag: '0',
			Uname:    "dsymonds",
			Gname:    "eng",
			Format:   FormatGNU,
		}, {
			Name:     "small2.txt",
			Mode:     0640,
			Uid:      73025,
			Gid:      5000,
			Size:     11,
			ModTime:  time.Unix(1244436044, 0),
			Typeflag: '0',
			Uname:    "dsymonds",
			Gname:    "eng",
			Format:   FormatGNU,
		}},
		chksums: []string{
			"e38b27eaccb4391bdec553a7f3ae6b2f",
			"c65bd2e50a56a2138bf1716f2fd56fe9",
		},
  }, 
  // more test cases
```

在上面的函数中，您将看到核心开发人员将我们在这里介绍的第一种技术与来自`testdata/`目录的文件相结合，以确保当一个示例`.tar`文件被打开时，这些文件及其校验和符合他们的期望。

# 模仿 HTTP 请求

一旦您开始编写生产级 API 和服务，您很可能会开始与其他服务交互，并且能够测试您与这些服务交互的方式与测试您代码库的其他部分一样重要。

然而，您可能会与在数据库上执行 CRUD 操作的 REST APIs 进行交互，因此，当您只是试图测试工作正常时，您不希望这些更改实际提交到您的数据库中。

因此，为了解决这个问题，我们可以使用`net/http/httptest`包来模拟 HTTP 响应，这是我们在这些情况下最好的朋友。

```
package main_test

import (
	"fmt"
	"io"
	"io/ioutil"
	"net/http"
	"net/http/httptest"
	"testing"
)

func TestHttp(t *testing.T) {
  // 
	handler := func(w http.ResponseWriter, r *http.Request) {
    // here we write our expected response, in this case, we return a
    // JSON string which is typical when dealing with REST APIs
		io.WriteString(w, "{ \"status\": \"expected service response\"}")
	}

	req := httptest.NewRequest("GET", "https://tutorialedge.net", nil)
	w := httptest.NewRecorder()
	handler(w, req)

	resp := w.Result()
	body, _ := ioutil.ReadAll(resp.Body)
haha
	fmt.Println(resp.StatusCode)
	fmt.Println(resp.Header.Get("Content-Type"))
	fmt.Println(string(body))
}
```

在上面的测试案例中，我们基本上覆盖了我们期望的来自 URL 的响应，然后继续测试依赖于该响应的系统的其他部分。

# 独立包装的使用

如果我们看一下`strings_test.go`文件并检查顶部的包，你应该注意到它不在`strings.go`文件所在的包中。

这其中的原因？它帮助您避免循环导入。在某些场景中，您需要在您的`*_test.go`文件中导入一个包来充分地编写您的测试。如果您在中导入的包已经引用了您正在尝试测试的包，您可能会看到循环依赖关系的问题。

# 区分您的单元测试和集成测试

> *我最初是从*[*Go Advanced Tips Tricks*](/@povilasve/go-advanced-tips-tricks-a872503ac859)找到这个技巧的

如果你正在为大型企业 Go 系统编写测试，那么你将很有可能拥有一组既有`integration`又有`unit`的测试来确保你的系统的有效性。

然而，通常情况下，你会发现你的集成测试比你的单元测试运行的时间要长得多，因为它们可能会影响到其他系统。

在这种情况下，将您的集成测试放到`*_integration_test.go`文件中，并将`// +build integration`添加到测试文件的顶部是有意义的:

```
// +build integration

package main_test

import (
	"fmt"
	"testing"
)

func TestMainIntegration(t *testing.T) {
	fmt.Println("My Integration Test")
}
```

为了运行这套集成测试，您可以像这样调用`go test`:

```
➜  advanced-go-testing-tutorial git:(master) ✗ go test -tags=integration
My Integration Test
PASS
ok      _/Users/elliot/Documents/Projects/tutorials/golang/advanced-go-testing-tutorial 0.006s
```

# 结论

因此，在本教程中，我们看了一些由 Go 语言维护者使用的更高级的测试技术和技巧。

希望您发现这很有用，它给了您继续改进您自己的 go 测试所需的洞察力。如果你觉得它有用，或者有任何进一步的问题，请不要犹豫，在下面的评论区告诉我！

*最初发表于*[](https://tutorialedge.net/golang/advanced-go-testing-tutorial/)**。**