# 使用 ImageMagick 的 Elixir 中的图像缩略图

> 原文：<https://medium.com/hackernoon/image-thumbnail-in-elixir-using-imagemagick-215b8ab5f23f>

![](img/da4d043eda4039676c35984ba0e5d617.png)

当你处理图像时，你离不开缩略图。对于大图像，缩略图为我们提供了一种浏览图像的方式，而不用担心图像的实际大小，也不用等待它完成下载，这样我们就可以看到图像的全部内容。

我们的整个附件功能已经准备就绪，我们已经找到了 Elixir/Phoenix world 中唯一可用的选项— [Thumbnex](https://github.com/talklittle/thumbnex) ，用于生成缩略图。除了生成缩略图之外，一切都很好。Thumbnex 出现以下错误:

```
 ** (ErlangError) Erlang error: :enoent
 (elixir) lib/system.ex:622: System.cmd(“ffprobe”, [“-show_format”, “/var/folders/k2/37ht7xpx4g1fswv0cn003gp80000gp/T/plug-1531/multipart-739416–202247–1”], [stderr_to_stdout: true])
 lib/ffprobe.ex:47: FFprobe.format/1
 lib/thumbnex/animations.ex:6: Thumbnex.Animations.duration/1
 lib/thumbnex.ex:35: Thumbnex.create_thumbnail/3
```

只是给你一些背景`Thumbnex` hex 包使用 ImageMagick 和`FFmpeg`来操纵图像和媒体文件生成缩略图。

ImageMagick 是一个命令行工具，用于调整大小、翻转、镜像、旋转、扭曲、剪切和变换图像。我们可以用`sudo apt-get imagemagick`把它安装在我们的机器上。

FFmpeg 也是一个命令行工具，可以处理视频、音频和其他多媒体文件。

由于我们最感兴趣的是图像缩略图生成，ImageMagick 可以帮助我们在内部做到这一点。但是这篇博客将帮助你从 Elixir 开始使用上述工具。

回到上面的错误，这个错误与`ffprobe`有关，它是 FFmpeg 提供的一个工具，用于打印与媒体文件相关的信息。最初，我认为这可能与我的系统有关，所以我在另一台机器上尝试了一下，它抛出了同样的错误。此外，我不能相信这个 prod。

所以我开始寻找其他方法来完成同样的任务。不幸的是，我找不到任何其他生成缩略图的十六进制包。

于是我继续探索，自己动手。所以第一步是使用 ImageMagick()从命令行生成缩略图。所以我找到了[这个方便的命令](http://www.imagemagick.org/Usage/thumbnails)，它的作用是:

```
 convert -define jpeg:size=500x180 trackive-staging/attachments/scope.id/sample.jpeg -auto-orient -thumbnail 250x90 -unsharp 0x.5 sample_thumb.jpeg

Note: Make sure you have ImageMagick installed on your machine.
```

`-thumbnail` -它会调整图片大小，删除所有的个人资料和评论数据，使其变小。

`-define jpeg:size=` —用于设置原始图像的初始尺寸，该尺寸已被设置为最终图像尺寸的两倍，以使其清晰可见且看起来不模糊。它用于避免对大型输入图像进行操作。

`-auto-orient` —用于将图像设置为正确的方向。如果图像来自相机，这很有帮助。

`- unsharp`——因此，在`-thumbnail`调整大小操作后，你可以通过稍微锐化图像(使用`-unsharp`)来改善上述结果。

在`-thumbnail`后指定所需的缩略图尺寸。

上面的命令应该给我们所需的图像缩略图。一旦完成，我们将有兴趣使用 Elixir 以编程方式完成它。

在 Elixir 中， [System.cmd](https://hexdocs.pm/elixir/System.html#cmd/3) 可以直接与主机系统交互，这意味着我们传递给这个函数的任何命令都在本地命令行上执行。现在我们知道了这个命令，并且知道了如何以编程方式运行它来生成动态图像缩略图。

因此，可以直接用于从原始图像获取缩略图的最终灵丹妙药代码如下:

```
 defmodule Image.Transformer do
 def transform(original_file, operation \\ “convert”) do
    #1 get thumbnail file name
    thumb_path = generate_thumb_file(original_file)

    #2 generate thumbnail by passing appropriate parameters
    System.cmd(operation, operation_commands(original_file_path, thumb_path))

    #3 return the generated thumbnail
    thumb_path
 end defp generate_thumb_file(original_file) do
    original_file
    |> String.replace(“.”, “_thumb.”)
 end defp operation_commands(original_file_path, thumb_path, size \\ “250x90”) do
    [
     “-define”,
     “jpeg:size=500x180”,
     original_file_path,
     “-auto-orient”,
     “-thumbnail”,
     size,
     “-unsharp”,
     “0x.5”,
     thumb_path
    ]
 end
end
```

首先，我们获取原始图像文件，并为其缩略图生成一个文件名。因此，如果文件名是`sample.jpeg`，那么缩略图文件名将是`sample_thumb.jpeg`。

然后，我们使用`System.cmd`在主机上运行 ImageMagick 命令。

由于我们将缩略图文件名传递给了`convert`命令，那就是我们的文件新生成的文件。检查原始图像文件目录以确认是否生成了缩略图。

我把这个加入了我的 GitHub 要点。
[https://gist . github . com/trojanh/319 D8 c 01 B3 C4 c 139 BD 1 df 930 ea 1c 5d 48](https://gist.github.com/trojanh/319d8c01b3c4c139bd1df930ea1c5d48)

您知道 ImageMagick，您知道 Elixir，使用 ImageMagick(或任何其他工具，如 FFmpeg)的任何操作都可以轻松地从这个文件进行任何转换。

编辑:似乎这个错误是因为我的机器上安装了损坏的`FFmpeg`。因此，从命令行重新安装 FFmpeg 将解决 Thumbnex 的问题。