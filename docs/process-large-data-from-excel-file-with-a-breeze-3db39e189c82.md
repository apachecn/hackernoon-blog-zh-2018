# 轻松处理 Excel 文件中的大量数据

> 原文：<https://medium.com/hackernoon/process-large-data-from-excel-file-with-a-breeze-3db39e189c82>

当你需要的只是文件中的数据时，为什么还要上传文件呢？更干净，更快避免连接超时，损坏的数据只需添加 excel_uploader js

![](img/ef7b590642ce44cb13d57a6686930402.png)

# 1.概观

从 excel 文件中可靠地批量上传数据有时可能是一件棘手的工作。这就是如何用一个简单的 [JavaScript](https://hackernoon.com/tagged/javascript) 库驯服野兽。

这个库不会先将整个文件上传到服务器，然后再进行处理，而是在本地机器上处理文件，然后将提取的数据(这是您实际需要的)批量上传到服务器，从而为您节省一些带宽。

因为它是成批上传的，所以连接超时之类的问题得到了解决。除此之外，它还报告在服务器上导致异常的数据，并以 Excel 文件的形式提供下载

# 2.问题是

我需要从 3000 到 16000 行的 excel 文件中批量上传用户记录。每个 excel 记录(一行)将用于在系统中创建一个用户帐户。这包括访问多个数据库表、发送电子邮件、数据验证以及一个或多个循环。

首先想到的技术是寻找 excel 库，将 excel 文件上传到服务器，处理 excel 文件，最后返回成功或错误消息——所有这些都在一个 HTTP 请求中完成！

这种方法的缺点是:

- HTTP 请求超时，特别是使用非多线程的[编程](https://hackernoon.com/tagged/programming)语言

-不可靠的错误报告

诸如此类。

# 3.解决方案

我做的是在客户端浏览器用 JavaScript 提取数据，将这些数据批量推送到服务器。

在服务器端，接收到的数据在一个`try…catch`块中进行处理，引发异常的数据存储在一个错误数组中。一旦服务器处理完当前批处理，它就向客户端发送 OK 响应，同时发送错误数据(如果有的话)。

**这个过程的另一个关键技术是数据库事务管理**。特定记录的所有数据库交互都是在服务器端的 DB 事务范围内完成的。如果在数据库交互过程中出现错误，事务将回滚，否则将被提交。

在编程中，通常有不止一种方法来实现一项壮举，每种方法都有自己的缺点。然而，这种技术在错误报告和 HTTP 连接管理方面是可靠的。

# 4.使用 excel_uploader.js JavaScript 库

我在一个名为 excel_uploader.js 的简单 JavaScript 文件中实现了上述解决方案。要在项目中使用 JavaScript 文件:我们需要从其 [Github repo](https://github.com/SeunMatt/excel_uploader) 中获取库，并将其与 jQuery、xlsx.min.js、FileSaver.js 以及可选的 bootstrap 4 和 sweetalert.js 一起包含在项目中:

```
<!-- required -->
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.2.1/jquery.min.js"></script><!--- optional -->
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js"></script>
<script src="https://unpkg.com/sweetalert/dist/sweetalert.min.js"></script><!-- needed by older browsers -->
<script src="https://github.com/eligrey/Blob.js"></script><!-- Required -->
<script src="https://rawgit.com/eligrey/FileSaver.js/src/FileSaver.js"></script>
<script src="https://unpkg.com/xlsx/dist/xlsx.full.min.js"></script>
<script src="https://cdn.rawgit.com/SeunMatt/excel_uploader/4f4ebd93/src/excel_uploader.js"></script>
```

**注意，在较旧的浏览器上需要** [**Blob.js**](https://github.com/eligrey/Blob.js) **才能让 FileSaver.js 工作。**

接下来，我们将把它添加到希望 excel 上载发生的 HTML 页面中:

```
<script>
    $(document).ready( function () {
        new ExcelUploader({
            maxInAGroup: 1000,
            serverColumnNames: ["Name", "Email", "Phone Number"],
            importTypeSelector: "#dataType",
            fileChooserSelector: "#fileUploader",
            outputSelector: "#tableOutput",
            extraData: {_token: "23333323323223323232"}
        });
    });
</script>
```

从上面的片段中，我们用配置参数实例化了 *ExcelUploader* 对象。以下是对每个参数及其作用的解释。

**maxInAGroup:** 该参数控制每次发送到服务器的记录数量。默认值为 1000，因此如果源 excel 文件中有 5000 条记录，数据将分 5 批上传到服务器。它可以根据可用资源根据我们的口味进行微调。

**serverColumnNames** :这是服务器期望的列的数组。每次 *excel_uploader* 向服务器推送数据时，也会发送一个 *column_map* 。

使用 *serverColumnNames* 中元素的小写形式作为键，使用相应 excel 列的索引作为值，生成列映射。

例如，如果服务器希望上传的 excel 数据包含 3 列—姓名、电子邮件和电话号码。

用户将首先上传一个 excel 文件，然后在 excel 中映射哪个列代表什么数据。然后 *excel_uploader* 将使用小写的 *serverColumnNames、*用下划线(_)字符替换空格来组成服务器列名。

因此，最后的服务器将收到一个 JSON 有效负载，如下所示:

```
{
  "column_map":{"name":0,"email":2,"phone_number":1}, 
  "data":[ 
        ["name1", "08010xxx", "email1@g.com"],
        ["name2", "08020xxx", "email2@g.com"],
        ["name3", "08030xxx", "email3@g.com"],
        ...
     ]
}
```

这可以解释为列*名称*在数据的索引零(0)，电子邮件在索引二(2)，电话号码*在索引 1。*

**importTypeSelector** :这是 HTML *select* 字段的 jQuery 选择器。**选择选项的值是 excel 数据发送到的实际 URL(如果选择了该选项)。**这样就可以使用同一个页面进行多次上传。只需将目标 URL 作为选项添加到导入类型选择器中。

**文件选择选择器:**这是 HTML 文件输入字段的 jQuery 选择器。用于用户从本地存储中实际选择 excel 文件。

**outputSelector** :这是 HTML div 的 jQuery 选择器，用于显示进度、状态和错误消息。

**extraData** :这是与上传的数据和列映射一起发送到服务器的数据。例如，CSRF·托肯。这是一个可选参数。

既然我们已经查看了选项，我们一定已经注意到我们需要在我们的 web 页面上添加一些标记，这样才能工作。

因此需要下面的 HTML:

```
<!--This is the import type selector-->
<div class="form-group">
    <select class="form-control" id="dataType">
        <option value="-1" disabled selected>Select Data to Import</option>
        <option selected value="http://localhost/excel_uploader/demo/php/demo.php">Import Posts Data</option>
    </select>
</div><!-- This is the file chooser input field-->
<div class="form-group">
    <input type="file" id="fileUploader" class="btn btn-fill btn-primary btn-large" />
</div><!-- This is the Blank output/progress div-->
<div id="tableOutput"></div>
```

完整的 HTML 可以在 demo.html 的[页面上找到。](https://github.com/SeunMatt/excel_uploader/tree/master/demo)

注意:在上面的 JavaScript 代码片段中，HTML 元素各自的 id 用于配置 *ExcelUploader* 对象。这就是我们在前端需要做的一切。简单不是吗？

# 5.一点点服务器端处理

***excel_uploader.js* 库与服务器端无关！**它几乎可以与任何服务器端技术一起工作。它只需要知道通过 *importTypeSelector* 推送数据的 URL，如此而已。

它将使用 jQuery ajax 在 POST 请求中将数据推送到服务器。POST 请求有效载荷将包含两个强制条目:*数据*和*列 _ 映射*。

**数据**:这是数组中的数组——代表用户上传的 excel 文件中的数据。它是 JSON 格式的，应该被解码成一个键值数据结构，比如 PHP 中的关联数组或者 Java 中的 *Map* 或者其他服务器端编程语言中的等效物。

外部数组表示整体，而内部数组表示 excel 文件中每一行的数据。因此，*数据[0]* 的长度数将是上传期间映射的列数，而*数据*数组本身的长度是上传文件中可用的行/记录总数。

这是在 post 请求中收到的数据示例:

```
{
  "column_map":{"name":0,"email":2,"phone_number":1}, 
  "data":[ 
        ["name1", "08010xxx", "email1@g.com"],
        ["name2", "08020xxx", "email2@g.com"],
        ["name3", "08030xxx", "email3@g.com"],
        ...
     ]
}
```

## 5.1.访问数据

从上一节中我们了解到， *column_map* 将列名与包含在*数据中的数组中的相应索引相关联。*

因此，我们可以通过将 JSON 有效负载解析为键值数据结构(如关联数组(PHP)或映射(Java))来访问数据。

在 PHP CodeIgniter 中，我们可以这样做:

```
$columnMap = json_decode($this->input->post("column_map"), true);//to get the name of the first row
$name = $data[0][$columnMap["name"]];//to get the email of the first row
$email = $data[0][$columnMap["email"]];//to get the phone_number in first row
$phoneNumber = $data[0][$columnMap["phone_number"]];//we can definitely use a loop to process all the data
```

如果你注意到了，从上面的片段中，我们不需要记住*数据*数组中每一列的对应索引。我们简单地使用 *column_map* ，这要容易得多。

如果服务器端在像 Spring Boot 这样的框架中使用 Java 编程语言，同样的事情也是适用的:

```
//extract the index of the columns we're expecting from the map
int nameIndex = Integer.parseInt(columnMap.get("name").toString());
int emailIndex = Integer.parseInt(columnMap.get("email").toString());
int phoneIndex = Integer.parseInt(columnMap.get("phone_number").toString());//save a user
User user = new User();
user.setName(data.get(nameIndex));
user.setEmail(data.get(emailIndex));
user.setPhone(data.get(phoneIndex));
user.setPassword("newHashedPassword");
User savedUser = userRepository.save(user);
```

[PHP](https://github.com/SeunMatt/excel_uploader/tree/master/demo/php) 和 [Java](https://github.com/SeunMatt/excel_uploader/tree/master/demo/java/exceluploaderdemo) 的完整演示代码可以在[这里](https://github.com/SeunMatt/excel_uploader/tree/master/demo)找到。

## 5.2.处理数据

处理传入 post 数据的最好方法之一是有两个方法(或函数)。

其中 POST 有效载荷将被解码，数据数组将被循环，我们称之为 *uploadUserData()* 。

在这个函数中，会有一个 *$errorArray* 变量，它本身必须是一个数组的数组。从 POST 有效负载解码的*数据*数组将被循环，该数据的每个元素(对应于 excel 文件中的一行)将由另一个方法 *doUserUpload()* 处理。

方法 *doUserUpload* 将在封装在数据库事务中的`*try … catch*`块中执行数据库交互。如果任何数据库交互失败，它将回滚数据库更改并返回 false，否则返回 true。

如果 *doUserUpload* 返回 *false* ，正在处理的特定记录/行将被添加到 *$errorArray*

PHP 中 *uploadUserData()* 的实现示例:

```
$errorArray = [];//this is where the data will be handled and process
foreach ($data as $datum) {
   if(!empty($datum) && !$this->doUserUpload($datum,  $columnMap)) {
     //that means there was an error processing the file
     //so we will just add this $datum to the errorArray
     array_push($errorArray, $datum);
   }
}//....//after all the data has been processed, let's respond to the client
if(!empty($errorArray)) {
    //there was an error somewhere. Let's send the client the affected data
    $this->respond(json_encode(["data" => $errorArray]));
} else{
    $this->respond(json_encode(["success" => "OK"]));
}
```

**完整的服务器端处理示例可以在**[](https://github.com/SeunMatt/excel_uploader/tree/master/demo)****中找到。相应的 Java 演示也可以在这里** **找到** [**。**](https://github.com/SeunMatt/excel_uploader/tree/master/demo/java/exceluploaderdemo)**

## ****5.3。服务器响应****

**这个流程的关键步骤是服务器响应。服务器应该用 JSON 响应，内容类型应该是*应用程序/json* 。**

**如果任何上传的数据在处理时导致错误，并被添加到 *errorArray* 中，则产生的错误数组应被发送到 excel_uploader.js，否则，应发送成功消息:**

```
if(!empty($errorArray)) {
    //there was an error somewhere. Let's send the user some data that are affected.
    $this->respond(json_encode(["data" => $errorArray]));
} else{
    $this->respond(json_encode(["success" => "OK"]));
}
```

**或者在 Java 中:**

```
if(errors.isEmpty()) {
    //all is well during the process
    responseMap.put("success", "OK");
    return ResponseEntity.ok(responseMap);
}//there are errors and some data are not processed.
// Let's send those data back to the client
//note that we're sending the OK response 200
//it's just that we're appending some error data that might have been present
responseMap.put("data", errors);
return ResponseEntity.ok(mapper.writeValueAsString(responseMap));
```

**如果我们注意到正在发送一个无效数据，并且我们想要通知用户，我们可以发送一个 JSON 响应，其中包含一个错误条目和一个 200 状态代码——因为我们实际上自己处理了这个错误:**

```
if(count($data[0]) < 3 || count($columnMap) < 3) {
    $this->respond(json_encode([
      "error" => "The Data Seems not to be complete! " . count($columnMap) . " columns were mapped instead of 3"]), 200);
}
```

**注意:JSON 响应应该有一个状态代码 200，以便 excel_uploader.js 能够处理它。**

**如果有任何其他服务器生成的错误，它们将在 ajax 方法的 fails()回调中处理。**

# **6.汤好了！**

**只需一点点 JavaScript，一点点 config 和 HTML，我们就可以开始了。好的一面是，我们可以使用同一个页面将数据上传到许多端点，即使有额外的负载。只需将 URL 作为另一个选项添加到 *importTypeSelector* 中，就可以了。**

**我想听听你将这个简单的工具集成到你的项目中的经历。如果您发现任何 bug，请向 Github 报告。**

**github:【https://github.com/SeunMatt/excel_uploader **

> **你听说过[吗？这是我正在开发的一个新的社交媒体应用。如果你喜欢我的作品，你一定会喜欢这个应用程序。现在就从谷歌 PlayStore 下载吧，让我知道你的想法](https://palcrib.app.link/medium)**