# Golang —优雅地处理错误

> 原文：<https://medium.com/hackernoon/golang-handling-errors-gracefully-8e27f1db729f>

![](img/0edef751d2aef868d97a5c933338b23d.png)

虽然 **go** 有一个简单的错误模型，但是乍一看，事情并没有想象中那么简单。在本文中，我想提供一个很好的策略来处理错误并克服您可能会遇到的问题。

首先，我们要分析什么是围棋中的失误。

然后我们将看到错误创建和错误处理之间的流程，并分析可能的缺陷。

最后，我们将探索一种解决方案，它允许我们在不损害应用程序设计的情况下克服这些缺陷。

# 围棋中什么是错误

查看内置错误类型，我们可以得出一些结论:

```
// The error built-in interface type is the conventional interface for
// representing an error condition, with the nil value representing no error.
type error interface {
	Error() string
}
```

我们看到一个错误是一个接口实现了一个简单的方法错误返回一个字符串。

这个定义告诉我们，创建一个错误只需要一个简单的字符串，所以如果我创建以下结构:

```
type MyCustomError stringfunc (err MyCustomError) Error() string {
  return string(err)
}
```

我想出了最简单的错误定义。

注意:这只是举个例子。我们可以使用 go 标准包 *fmt* 和 *errors* 创建一个错误:

```
import (
  "errors"
  "fmt"
)simpleError := errors.New("a simple error")simpleError2 := fmt.Errorf("an error from a %s string", "formatted")
```

一条简单的消息足以优雅地处理错误吗？让我们最后回答这个问题，探索我将提供的解决方案。

# 误差流

因此，我们已经知道什么是错误，下一步是可视化他的生命周期中的流程。

为了简单和不重复自己的原则，在一个地方对一个错误采取行动是可取的。

让我们举下面的例子来看看为什么:

```
// bad example of handling and returning the error at the same time
func someFunc() (Result, error) {
 result, err := repository.Find(id)
 if err != nil {
   log.Errof(err) return Result{}, err
 } return result, nil
}
```

这段代码有什么问题？

我们处理了两次错误，首先记录错误，然后返回给这个函数的调用者。

可能你的一个团队同事会使用这个函数，当错误返回时，他会再次记录这个错误。然后在系统日志中出现一个错误噩梦。

假设我们的应用程序有 3 层，分别是**存储库**、**交互器**和 **web 服务器**:

```
// The repository uses an external depedency orm
func getFromRepository(id int) (Result, error) {
  result := Result{ID: id}
  err := orm.entity(&result)
  if err != nil {
    return Result{}, err
  } return result, nil 
}
```

根据我之前提到的原则，这是通过返回到顶部来处理错误的正确方法。稍后，它将被记录，检索正确的反馈到 web 服务器，所有这一切都在一个地方。

但是前面的代码有一个问题。不幸的是，go builtin 错误不提供堆栈跟踪。除此之外，错误是在一个外部依赖项上生成的，我们需要知道项目内部的哪段代码对这个错误负责。

**github.com/pkg/errors**前来救援。

我将通过添加堆栈跟踪和添加存储库未能获得结果的消息来重做前面的函数。我想在不损害原始错误的情况下这样做:

```
import "github.com/pkg/errors"// The repository uses an external depedency orm
func getFromRepository(id int) (Result, error) {
  result := Result{ID: id}
  err := orm.entity(&result)
  if err != nil {
    return Result{}, **errors.Wrapf(err, "error getting the result with id %d", id);**
  } return result, nil 
}// after the error wraping the result will be 
// err.Error() -> error getting the result with id 10: whatever it comes from the orm
```

这个函数的作用是包装来自 ORM 的错误，在不破坏原始错误的情况下构建堆栈跟踪。

因此，让我们看看其他层将如何处理错误。首先是互动者:

```
func getInteractor(idString string) (Result, error) {
  id, err := strconv.Atoi(idString)
  if err != nil {
    return Result{}, errors.Wrapf(err, "interactor converting id to int")
  } return repository.getFromRepository(id) 
}
```

现在是顶层，web 服务器:

```
r := mux.NewRouter()
r.HandleFunc("/result/{id}", ResultHandler)func ResultHandler(w http.ResponseWriter, r *http.Request) {
  vars := mux.Vars(r)
  result, err := interactor.getInteractor(vars["id"])
  if err != nil { 
    handleError(w, err) 
  } fmt.Fprintf(w, result)
}func handleError(w http.ResponseWriter, err error) { 
   w.WriteHeader(http.StatusIntervalServerError)
   log.Errorf(err)
   fmt.Fprintf(w, err.Error())
}
```

正如你看到的，我们只是在顶层处理了错误。完美？不。如果你注意到我们总是返回 500 作为 HTTP 响应代码。除此之外，我们总是记录错误。一些错误如“没有找到结果”只是给我们的日志增加了噪音。

# 我的解决方案

我们在上一个主题中看到，当处理顶层的错误时，一个字符串不足以做出决策。

我们知道，如果我们在一个错误中引入一些新的东西，我们会在错误产生的地方和错误最终被处理的时候，以某种方式引入一种依赖性。

## 因此，让我们探索一个定义了 3 个目标的解决方案:

*   提供良好的错误堆栈跟踪
*   记录错误(例如 web 基础设施层)
*   必要时向用户提供上下文错误信息。(例如，提供的电子邮件格式不正确)

首先，我们创建一个错误类型:

```
package errorsconst(
  NoType = ErrorType(iota)
  BadRequest
  NotFound 
  //add any type you want
)type ErrorType uinttype customError struct {
  errorType ErrorType 
  originalError error 
  contextInfo map[string]string 
}// Error returns the mssage of a customError
func (error customError) Error() string {
   return error.originalError.Error()
}// New creates a new customError
func (type ErrorType) New(msg string) error {
   return customError{errorType: type, originalError: errors.New(msg)}
}

// New creates a new customError with formatted message
func (type ErrorType) Newf(msg string, args ...interface{}) error {    
   err := fmt.Errof(msg, args...)

   return customError{errorType: type, originalError: err}
}

// Wrap creates a new wrapped error
func (type ErrorType) Wrap(err error, msg string) error {
   return type.Wrapf(err, msg)
}

// Wrap creates a new wrapped error with formatted message
func (type ErrorType) Wrapf(err error, msg string, args ...interface{}) error { 
   newErr := errors.Wrapf(err, msg, args..)   

   return customError{errorType: errorType, originalError: newErr}
}
```

所以你可以看到，我只公开了错误类型和错误类型。我们可以创建新的错误并包装现有的错误。

但是我们遗漏了两件事。

我们如何在不导出 customError 的情况下检查错误类型？

我们如何向错误添加/获取上下文，甚至是从外部依赖中预先存在的错误？

让我们采用**github.com/pkg/errors.**的策略，首先包装这些库方法。

```
// New creates a no type error
func New(msg string) error {
   return customError{errorType: *NoType*, originalError: errors.New(msg)}
}

// Newf creates a no type error with formatted message
func Newf(msg string, args ...interface{}) error {
   return customError{errorType: *NoType*, originalError: errors.New(fmt.Sprintf(msg, args...))}
}

// Wrap wrans an error with a string
func Wrap(err error, msg string) error {
   return Wrapf(err, msg)
}

// Cause gives the original error
func Cause(err error) error {
   return errors.Cause(err)
}

// Wrapf wraps an error with format string
func Wrapf(err error, msg string, args ...interface{}) error {
   wrappedError := errors.Wrapf(err, msg, args...)
   if customErr, ok := err.(customError); ok {
      return customError{
         errorType: customErr.errorType,
         originalError: wrappedError,
         contextInfo: customErr.contextInfo,
      }
   }

   return customError{errorType: *NoType*, originalError: wrappedError}
}
```

现在，让我们构建我们的方法来处理任何一般错误的上下文和类型:

```
 // AddErrorContext adds a context to an error
func AddErrorContext(err error, field, message string) error {
   context := errorContext{Field: field, Message: message}
   if customErr, ok := err.(customError); ok {
      return customError{errorType: customErr.errorType, originalError: customErr.originalError, contextInfo: context}
   }

   return customError{errorType: *NoType*, originalError: err, contextInfo: context}
}

// GetErrorContext returns the error context
func GetErrorContext(err error) map[string]string {
   emptyContext := errorContext{}
   if customErr, ok := err.(customError); ok || customErr.contextInfo != emptyContext  {

      return map[string]string{"field": customErr.context.Field, "message": customErr.context.Message}
   }

   return nil
}

// GetType returns the error type
func GetType(err error) ErrorType {
   if customErr, ok := err.(customError); ok {
      return customErr.errorType
   }

   return *NoType* }
```

现在回到我们的例子，我们将应用这个新的错误包:

```
import "github.com/our_user/our_project/errors"// The repository uses an external depedency orm
func getFromRepository(id int) (Result, error) {
  result := Result{ID: id}
  err := orm.entity(&result)
  if err != nil {    
    msg := fmt.Sprintf("error getting the  result with id %d", id) switch err {
    case orm.NoResult:
        **err = errors.Wrapf(err, msg);** default: **
        err = errors.NotFound(err, msg); ** 
    } return Result{}, err
  } return result, nil 
}// after the error wraping the result will be 
// err.Error() -> error getting the result with id 10: whatever it comes from the orm
```

现在互动者:

```
func getInteractor(idString string) (Result, error) {
  id, err := strconv.Atoi(idString)
  if err != nil { 
    err = **errors.BadRequest.Wrapf(err, "interactor converting id to int")**
    err = **errors.AddContext(err, "id", "wrong id format, should be an integer)**

    return Result{}, err
  } return repository.getFromRepository(id) 
}
```

最后是 web 服务器:

```
r := mux.NewRouter()
r.HandleFunc("/result/{id}", ResultHandler)func ResultHandler(w http.ResponseWriter, r *http.Request) {
  vars := mux.Vars(r)
  result, err := interactor.getInteractor(vars["id"])
  if err != nil { 
    handleError(w, err) 
  } fmt.Fprintf(w, result)
}func handleError(w http.ResponseWriter, err error) { 
   var status int
   errorType := errors.GetType(err)
   switch errorType {
     case BadRequest: 
      status = http.StatusBadRequest
     case NotFound: 
      status = http.StatusNotFound
     default: 
      status = http.StatusInternalServerError
   } w.WriteHeader(status) 

   if errorType == errors.NoType {
     log.Errorf(err)
   } fmt.Fprintf(w,"error %s", err.Error()) 

   errorContext := errors.GetContext(err) 
   if errorContext != nil {
     fmt.Printf(w, "context %v", errorContext)
   }
}
```

正如你所看到的，有了导出的类型和一些导出的值，我们可以更容易地处理错误。我喜欢这个解决方案的一点是，通过设计，当创建一个错误时，我们显式地显示他的类型。

你有什么建议吗？下面评论。

**github 资源库**:【https://github.com/henrmota/errors-handling-example】T4