# 开始用水豚测试

> 原文：<https://medium.com/hackernoon/start-testing-with-capybara-f3ce82b44c4a>

Capybara 是一个基于网络的自动化框架工具，用于测试 Rails 应用。它帮助你创建**功能测试**，模拟用户如何通过浏览器与你的应用程序交互。

这是一个通过浏览器测试应用程序的极好工具！

**水豚**不与网站互动，它只是你和网络驱动之间的一层。为此，您可以使用 **Selenium** web 驱动程序或水豚支持的任何其他驱动程序。

# 配置水豚

**水豚**可以很容易地与 Rails 中使用的通用测试框架集成。它通常与 [RSpec](https://kolosek.com/rails-rspec-setup/) 一起用于写特性，但不常用于[控制器测试](https://kolosek.com/rspec-controller-test/)。

在开始之前，您需要将以下 gem 添加到您的[gem 文件](https://kolosek.com/rails-bundle-install-and-gemfile/)中的:test 组，并运行`bundle install`:

```
group :test do
  gem 'capybara'
  gem 'selenium-webdriver'
end
```

# 用水豚测试

水豚配备了一种用户友好的 DSL 语言，它提供了提取信息、输入数据、测试或点击的帮助方法。

> 默认情况下，水豚只会定位可见元素。这是因为真正的用户无法与不可见的元素进行交互。你可以在 [rubydoc.info](http://www.rubydoc.info/github/teamcapybara/capybara/master#the-dsl) *了解更多。*

# 常见 DSL 选项

这里我们将介绍最常用的与 web 应用程序中的元素交互的 DSL 动作:

*   **找到**特定元素并操纵它们:

```
find_field('field_name').value
find_field(id: 'field_id').value
find_link('link_name', visible: all).visible? find_button('button_name').click
```

如果你想操作你的元素，这是每个优秀的程序员都应该知道的最重要的功能之一。你可以用它来找到你的链接、按钮和字段，检查它们是否对用户可见，或者点击它们。在引发错误之前，总是等待页面上出现一个元素。

*   通过**以下链接和按钮**与网络应用程序交互:

```
click_link('link_id')
click_link('link_text')
click_button('button_name')
click_on('link_text') # clicks on either link or button
```

当您想要访问链接或按钮时，请始终使用这些选项之一。您可以通过使用 id、标题、标签中的文本或元素的值来实现这一点。

*   与**表单元素**交互:

```
fill_in('Title', :with => 'Example')
choose('radio_button')
check('checkbox') uncheck('checkbox')
attach_file('image', 'image_path') # upload a file
select('option', from: 'select_box') # select from dropdown
```

每当你想填写你的表格时，你应该使用这些可能性之一。根据元素的类型，当前值为:id、名称或相关的标签元素。唯一的例外是 attach_file，它只能接受 id 和名称。

*   **浏览槽页**,使用:

```
visit('url_name')
```

您可以使用这个简单的命令轻松地浏览页面，或者仅使用以下命令访问当前页面:

```
expect(page).to have_current_path(path_name)
```

*   **查询**页面中是否存在某些元素:

```
expect(page).to have_selector('table tr')
expect(page).to have_content('content_name')
```

*   使用**范围**将某些操作限制在页面的特定区域内:

```
# within(:xpath, 'actual_xpath') do
  fill_in 'Title', with: 'Example'
end
```

当您想只在特定区域使用动作或使用这些特殊方法时，请使用`within`方法:`within_fieldset`用于特定字段集，`within_table`用于特定表格。

*   **用以下方法调试**:

```
save_and_open_page # current snapshot of the page save_sceenshot('screenshot_name.png')
save_and_open_screenshot # saves and automatically opens the screenshot
```

当您想要保存页面的快照时，只需将其中一行添加到您的代码中，或者您也可以使用以下命令来检索元素的当前状态:

```
print page_name.html
```

# 火柴人

当试图使用 DSL 或 XPath 查找元素时，通常会有两个或更多的**匹配**。您可以使用以下方式自定义水豚查找元素的方式:

*   **Capybara.exact** —查找元素的精确匹配。
*   **Capybara.match** —控制多个元素匹配时 Capybara 的行为。

为了避免在找到多个匹配时出现**模糊匹配**错误，您应该考虑使用水豚支持的以下匹配策略:

*   **:第一个** —这将简单地选择第一个匹配的元素。
*   **:一个** —如果发现一个以上的元素，这将引发一个错误。
*   **:prefer_exact** —查找所有匹配的元素，但只返回第一个完全匹配的元素，其余的被丢弃。
*   **:智能**——如果 **Capybara.exact** 设置为**真**，它将表现为**:一个**。否则，它将试图找到确切的元素。如果找到多个元素，将再次引发错误。如果没有找到元素，则执行新的搜索。

# JavaScript 和异步调用

**异步 JavaScript** 允许你与页面上还不存在的元素进行交互。当使用 JavaScript 在页面上创建新元素时，可能会发生这种情况:

```
click_link('test')
click_link('foo')
```

一旦你点击“测试”链接，它将触发一个异步过程，这将显示“foo”链接。单击“foo”很可能会引发一个错误，因为该链接还不存在。

水豚可以通过等待一段时间来解决这个问题。您可以通过`Capybara.default_max_wait_time`调整默认等待时间(默认为 2 秒)。

# 水豚和 RSpec

将**水豚与 RSpec** 一起使用，就像在`rspec_helper.rb`文件中添加`require 'capybara/rails'`一样简单。要做到这一点，你需要预先安装[RSpec](https://kolosek.com/rails-rspec-setup/)。

```
describe 'users' do
  let! :each do
    @user = FactoryGirl.create(:user)
    login_as(@user)
  end describe GET user#edit' do
    it 'should update user' do
      visit edit_user_path(@user)
      within('#edit_user_path') do
        fill_in 'email', with: '[[email protected]](https://kolosek.com/cdn-cgi/l/email-protection)'
      end
      click_button 'Save'
      find_button('Save').click
      expect(page).to have_content 'Success'
    end
 end
end
```

这是一个简单的例子，展示了如何通过编辑用户来同时使用 Capybara 和 RSpec。

希望这篇文章能帮助你理解啮齿动物水豚会对你的测试环境造成什么影响！

**订阅，敬请关注即将到来的文章！**

*原载于 2018 年 2 月 19 日*[*kolosek.com*](https://kolosek.com/rails-capybara-setup/)*。*