# Vue.js 和 Django Rest 框架中的 JWT 认证—第 2 部分

> 原文：<https://medium.com/hackernoon/jwt-authentication-in-vue-js-and-django-rest-framework-part-2-788f0ad53ad5>

*原载于*[*www . melvinkoh . me*](http://www.melvinkoh.me/#/post/a2d7326a-0d56-4f4a-bdab-fa1abd74e72d)*。*

在第 1 部分中，我们用**djangorestframework _ jwt**设置了我们的后端。现在，我们将设置我们的前端，以便与我们的 DRF 一起工作。(我们将使用 Vue.js，如果您要在 React.js 中使用它，解决方案是类似的)

# 设置 Vue.js

确保您包括以下模块: *axios - >基于承诺的 HTTP 客户端*Vue-Axios->Axios 到 Vue 的绑定器。$http *jwt_decode - >解码 jwt 有效负载，以获得到期时间戳和时间戳的原始发布时间* …