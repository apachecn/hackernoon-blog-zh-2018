# 使用 Neovim 进行 Javascript 开发

> 原文：<https://medium.com/hackernoon/using-neovim-for-javascript-development-4f07c289d862>

![](img/e13c5ec00bc14ab38f697e071b09f51f.png)

Courtesy of Fotis Fotopoulos on Unsplash

对于我们许多人来说，Neovim 已经成为一种绝对的必需品。在过去的几年里，我使用 Neovim 编程、写作，甚至作为一个窗口管理器。最近几周，我觉得有必要编辑大量的 Javascript 文件，并且需要配置 Neovim 来处理 Javascript。

本文详细介绍了我用来为 Javascript 编辑、完成和林挺配置 Neovim 的所有插件和配置。

首先，我们需要从 Python 安装 [Neovim 包。我们还需要 Node 的最新版本和一些 Neovim 的基础知识。](https://github.com/neovim/python-client)

我使用 [Vim-Plug](https://github.com/junegunn/vim-plug) 作为我的插件管理器已经有一段时间了，我将在这里使用它，但是你可以使用其他任何一个(比如 Vundle 或 Pathogen)。

我做的第一件事是导航到我的 Neovim 配置文件。与 Vim 8 之前的版本不同，我们不寻找*。vimrc* 文件。在 Neovim 中，我们正在寻找 ***~/。config/nvim/init.vim*** 文件。

要使用 Vundle 安装新插件，我们需要在配置文件中包含以下代码块:

```
 call plug#begin()
call plug#end() 
```

这两行之间的所有内容都是需要安装的引用插件。

# 自动完成

我想安装的第一件事是某种代码分析器，它可以帮助我自动完成。我决定用 *Tern 换 Vim* 。

```
call plug#begin()
Plug ‘ternjs/tern_for_vim’, { ‘do’: ‘npm install && npm install -g tern’ }
call plug#end()
```

对于那些不熟悉 TernJs 的人来说，它是一个独立的、独立于编辑器的 JavaScript 分析器，可以用来改进现有编辑器的 JavaScript 集成。

此外，正如我们所看到的，我还安装了插件依赖项和一个全局 tern 包。这只是为了让插件与我电脑上安装的全局 ternjs 包保持同步。

每当我们想要真正安装我们添加到列表中的插件时，只需在 Neovim 中运行 ***:PlugInstall*** 即可，他会为我们完成所有工作。

现在，tern 已经安装好了，我们必须实际配置它，这样他才能知道要检查什么以及在哪里检查。于是，在我创造了一个 ***。tern-config*** 文件在我的主目录下，内容如下:

```
 {
  “plugins”: 
  {
    “node”: {}
  }
}
```

我创建了一个全局配置文件，但是这也可以通过将文件添加到项目的根目录中来本地完成。运行 ***之后:PlugInstall*** 我们现在准备开始使用 tern。为此，我们使用全补命令***<C-x><C-o>***来触发补全候选。

这是伟大的，但它只是抓到了表面。Neovim 给我们带来了异步执行。那么，当我们想要自动补全时，为什么我们需要使用 Omnicompletion 命令呢？尼奥维姆应该为我们做到这一点。

这就是[de complete](https://github.com/Shougo/deoplete.nvim)开始发挥作用的地方。

Deoplete 是一个非常酷的插件，它为 neovim/Vim8 提供了一个可扩展的异步完成框架。

```
 call plug#begin()
 Plug ‘Shougo/deoplete.nvim’, { ‘do’: function(‘DoRemote’) }
 Plug ‘ternjs/tern_for_vim’, { ‘do’: ‘npm install && npm install -g tern’ }
 Plug ‘carlitux/deoplete-ternjs’
call plug#end()let g:deoplete#enable_at_startup = 1
let g:deoplete#enable_ignore_case = 1
let g:deoplete#enable_smart_case = 1
let g:deoplete#enable_camel_case = 1
let g:deoplete#enable_refresh_always = 1
let g:deoplete#max_abbr_width = 0
let g:deoplete#max_menu_width = 0
let g:deoplete#omni#input_patterns = get(g:,’deoplete#omni#input_patterns’,{})let g:tern_request_timeout = 1
let g:tern_request_timeout = 6000
let g:tern#command = [“tern”]
let g:tern#arguments = [“ — persistent”]
```

Deoplete 允许其他人连接到它，并在屏幕上为候选人提供完整信息。它预配置了文件路径完成、ctags 完成和出现在每个 vim 缓冲区中的字符串。de complete-ternjs 是另一个插入 de complete 的包，它使用 tern js 服务器提供完成。

我们还为 Deoplete 和 tern 增加了许多配置。这些帮助我们塑造我们的插件行为。

在再次运行***:plug install***l 之后，我们应该有完全工作的异步完成。尝试输入一些 Javascript 相关的东西，自动补全应该会自动触发。

# 林挺

林挺是我想做的下一件事。因为我不是一个 Javascript 天才，林挺真的在我需要的地方。这让我对代码更加放松，因为我知道任何错误都会很快被发现。

![](img/1dad74a95fa6f2aad7fe36eb502da5d8.png)

我决定用 [Neomake](https://github.com/neomake/neomake) ，因为它已经被使用和证明。我补充了以下内容:

```
Plug ‘neomake/neomake’, { ‘on’: ‘Neomake’ }
```

添加到我的插件列表

```
let g:neomake_javascript_enabled_makers = [‘eslint’]
```

到配置。我们使用的是 ***eslint*** ，但是我们可以根据需要在列表中添加或删除 linters。

# 文件跳转

最后，我还想要某种“跳转到声明”的功能。在 Vim 中，我们通常使用 Ctags 来实现这种效果。但是 [vim-gutentags](https://github.com/ludovicchabant/vim-gutentags) 负责管理 vim 中急需的标记文件。它会在你工作的时候(重新)生成标签文件，同时完全不碍事。这将省去我们手动生成和重新生成标记文件的工作。

因此，我在插件列表中添加了:

```
Plug ‘ludovicchabant/vim-gutentags’
```

再次运行 ***:PlugInstall*** 并打开一个项目后，我们应该能够使用 ctags 跳转到定义。只需将鼠标悬停在已定义的变量或导入语句上，然后键入***<【C-】>***。

# 结论

这是我对 Javascript 编程的基本设置。有更多的插件和配置可以用来把这变成一个更好的体验。

你在用 Neovim 进行 Javascript 开发时使用了哪些插件和配置？

![](img/053f3cacbb0c0a4d951a5d9075cfbc3b.png)

想了解更多关于 Vim 的知识？想学习如何将其用作 IDE？看看我的新书[一个叫做 Vim](https://leanpub.com/anidecalledvim/) 的 IDE。从基本的 Vim 使用到文件查找、自动完成、文件管理器等等，它都有。