# RSpec 轨道控制器测试

> 原文：<https://medium.com/hackernoon/rspec-rails-controller-test-158f142e11ce>

Rails 是一个 web 开发框架，其中**模型**、**视图**和**控制器**是应用程序的重要方面。控制器，就像模型和查看器一样，需要用 Ruby 社区最喜欢的工具 **RSpec** 进行测试。

Rails 中的**控制器**接受 **HTTP 请求**作为它们的输入，并将 **HTTP 响应**作为输出。

# 组织测试

**描述**和**上下文块**对于根据控制器的动作和我们正在测试的上下文，将测试组织成一个清晰的层次结构是至关重要的。Betterspecs.org 提供了编写测试的基础知识，它将帮助你使你的测试更具表现力。

> “描述”的目的是针对一个功能包装一组测试，而“上下文”是针对同一状态下的一个功能包装一组测试。刘明的 RSpec 中的描述与语境。

您希望为每个有意义的输入创建一个上下文，并将其包装到一个 describe 块中。

我们将用不同的 describe 块来表示每个 HTTP 会话。

```
describe "Stories" do
  describe "GET stories#index" do
    context "when the user is an admin" do
      it "should list titles of all stories"
    end context "when the user is not an admin" do
      it "should list titles of users own stories" do
    end
```

当您想要控制**授权访问**时，您可以为每个用户角色创建一个新的上下文。同样，您可以通过为登录和注销的用户创建一个新的上下文来管理**认证访问**。

```
 context "when the user is logged in" do
      it "should render stories#index"
    end context "when the user is logged out" do
      it "should redirect to the login page"
    end
  end
```

默认情况下， [RSpec-Rails 配置](https://kolosek.com/rails-rspec-setup/)禁用控制器规格模板的渲染。您可以通过添加`render_views`来启用它:

1.  全局地，通过将其添加到`rails_helper.rb`文件中的`RSpec.configure`块
2.  按个人分组

```
 describe "GET stories#show" do
     it "should render stories#show template" do
     end
   end describe "GET stories#new" do
     it "should render stories#new template" do
   end
 end
```

在将属性保存到数据库之前，通常会检查您使用的属性是有效的还是无效的。

```
describe "POST stories#create" do context "with valid attributes" do it "should save the new story in the database" it "should redirect to the stories#index page" end context "with invalid attributes" do it "should not save the new story in the database" it "should render stories#new template" end end end
```

# 如何准备好您的数据？

我们使用**工厂**为我们的控制器规格准备好数据。工厂的工作方式可以通过一个[工厂来改善。](https://kolosek.com/factory-girl-build-vs-build_stub/)

通过下面的工厂，我们将使用不同标题和内容的`sequence`生成多个故事:

```
FactoryBot.define do
  factory :story do
    user
    sequence(:title) { |n| "Title#{n}" }
    sequence(:content) { |n| "Content#{n}" }
  end
end
```

# 让我们来测试一下！

是时候创建我们自己的控制器测试了。使用 [RSpec](https://kolosek.com/rails-rspec-setup/) 和 [Capybara](https://kolosek.com/rails-capybara-setup/) 编写测试。我们将在`stories_controller.rb`中介绍这些方法的测试:

# #索引

首先，我们想看看我们的控制器`stories_controller.rb`。索引动作**根据当前用户是否是管理员来授权对故事的访问**:

```
def index
  @stories = Story.view_premissions(current_user).
end
```

在模型`story.rb`中，我们**检查**当前用户是否是管理员:

```
def self.view_premissions(current_user)
  current_user.role.admin? ? Story.all : current_user.stories
end
```

利用我们刚刚收集的信息，我们可以创建下面的 **GET stories#index** 测试:

```
describe "GET stories#index" do
  context "when the user is an admin" do
    it "should list titles of all stories" do
      admin = create(:admin)
      stories = create_list(:story, 10, user: admin)
      login_as(admin, scope: :user)
      visit stories_path stories.each do |story|
        page.should have_content(story.title)
      end
   end
end context "when the user is not an admin" do
    it "should list titles of users own stories" do
      user = create(:user)
      stories = create_list(:story, 10, user: user)
      login_as(user, scope: :user)
      visit stories_path stories.each do |story|
      page.should have_content(story.title)
      end
    end
  end
end
```

如您所见，我们为每个用户角色(admin 和 not admin)创建了两个不同的上下文。管理员用户将能够看到所有的故事标题，另一方面，标准用户只能看到他们自己的。

使用选项`create(:user)`和`create_list(:story, 10, user: user)`你可以创建用户和十个不同的用户故事。新创建的用户将登录`login_as(user, scope: :user)`并访问`stories_path`页面，在这里他可以根据他当前的角色`page.should have_content(story.title)`看到所有的故事标题。

另一个创建新用户的好方法是使用 [let 或 before blocks](https://kolosek.com/rspec-let-vs-before/) ，这是编写干测试的两种不同方法。

# #显示

您可以用类似的方式编写#show 方法测试。唯一的区别是你想访问的页面显示了你想读的故事。

```
describe "GET stories#show" do
  it "should render stories#show template" do
    user = create(:user)
    story = create(:story, user: user) login_as(user, scope: :user)
    visit story_path(story.id) page.should have_content(story.title)
    page.should have_content(story.content)
  end
end
```

我们再次想要创建用户`create(:user)`和故事`create(:story, user: user)`。创建的用户将登录并访问包含基于 story.id `visit story_path(story.id)`的故事的页面。

# #新建和#创建

与其他方法不同，这种方法创建了一个新的故事。让我们来看看`stories_controller.rb`中的以下动作

```
# GET stories#new
def new
  @story = Story.new
end# POST stories#create
def create
  @story = Story.new(story_params)
  if @story.save redirect_to story_path(@story), success: "Story is successfully created."
  else
    render action: :new, error: "Error while creating new story"
  end
endprivatedef story_params
  params.require(:story).permit(:title, :content)
end
```

`new`动作呈现一个 stories #新模板，这是一个您在使用`create`动作创建新故事之前填写的表单。成功创建后，故事将保存在数据库中。

```
describe "POST stories#create" do
  it "should create a new story" do
    user = create(:user)
    login_as(user, scope: :user)
    visit new_stories_path fill_in "story_title", with: "Ruby on Rails"
    fill_in "story_content", with: "Text about Ruby on Rails" expect { click_button "Save" }.to change(Story, :count).by(1)
  end
end
```

这一次，一个已创建并登录的用户将访问该页面，在那里可以创建一个新故事`visit new_stories_path`。下一步是用标题和内容`fill_in "...", with: "..."`填充表单。一旦我们点击保存按钮`click_button "Save"`，总故事数将增加一个`change(Story, :count).by(1)`，意味着该故事已成功创建。

每个人都希望能够更新他们的故事。这可以通过以下方式轻松实现:

```
def update
  if @story.update(story_params)
    flash[:success] = "Story #{@story.title} is successfully updated."
    redirect_to story_path(@story)
  else
    flash[:error] = "Error while updating story"
    redirect_to story_path(@story)
  end
endprivatedef story_params
  params.require(:story).permit(:title, :content)
end
```

当一个新的故事被创建时，我们可以通过访问故事编辑页面来更新它。

```
describe "PUT stories#update" do
  it "should update an existing story" do
    user = create(:user)
    login_as(user, scope: :user)
    story = create(:story)
    visit edit_story_path(story) fill_in "story_title", with: "React"
    fill_in "story_content", with: "Text about React" click_button "Save"
    expect(story.reload.title).to eq "React"
    expect(story.content).to eq "Text about React"
  end
end
```

就像前面的方法一样，一个新创建的登录用户将创建一个故事并访问编辑故事页面`edit_story_path(story)`。一旦我们更新了故事的标题和内容，它就会按照我们的要求发生变化`expect(story.reload.title).to eq "React"`。

# #删除

最后，我们希望能够删除我们不喜欢的故事。

```
def destroy
  authorize @story
  if @story.destroy
    flash[:success] = "Story #{@story.title} removed successfully"
    redirect_to stories_path
  else
    flash[:error] = "Error while removing story!"
    redirect_to story_path(@story)
  end
end
```

你想通过安装`[gem 'pundit'](https://kolosek.com/rails-bundle-install-and-gemfile/)`来确保只有故事的管理员和所有者可以删除它。

```
class StoryPolicy < ApplicationPolicy
  def destroy?
    @user.role.admin?
  end
end
```

让我们也来测试一下。

```
describe "DELETE stories#destroy" do
  it "should delete a story" do
    user = create(:admin)
    story = create(:story, user: user)
    login_as(user, scope: :user)
    visit story_path(story.id)
    page.should have_link("Delete")
    expect { click_link "Delete" }.to change(Story, :count).by(-1)
  end
end
```

该测试的编写方式与 stories#create 类似，主要区别在于。我们没有创建故事，而是删除了它，这样总计数就减少了一个`change(Story, :count).by(-1)`。

我们又一次到达了终点！但是还有很多文章等着你，现在就订阅吧！

*原载于 2018 年 2 月 22 日 kolosek.com*[](https://kolosek.com/rspec-controller-test/)**。**