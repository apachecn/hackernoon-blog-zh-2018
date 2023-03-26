# 如何将 React 应用部署到 Heroku

> 原文：<https://medium.com/hackernoon/properly-deploy-your-react-app-to-heroku-c1a13f5f978c>

TLDR:使用[创建-反应-应用-构建包](https://github.com/mars/create-react-app-buildpack):

```
$ heroku buildpacks:set mars/create-react-app
```

## 我还没有部署到赫罗库！😣

```
$ heroku create MY-APP --buildpack mars/create-react-app
$ git add .
$ git commit -m "create-react-app"
$ heroku git:remote --app MY-APP
$ git push heroku master
```

## 我已经部署到 Heroku 了…😎

```
$ heroku git:remote --app MY-APP
$ heroku buildpacks:set mars/create-react-app
$ git commit --allow-empty -m 'Use create-react-app-buildpack'
$ git push heroku master
```

更多信息: [Buildpack 回购](https://github.com/mars/create-react-app-buildpack)， [Heroku 博客](https://blog.heroku.com/deploying-react-with-zero-configuration)